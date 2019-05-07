---
layout: post
permalink: /tech/nested-spark/
title: Working with badly nested data in Spark
abstract: Apache Spark is a distributed computing platform which can handle almost any kind of data you throw at it. But it has lots of optimizations that require SQL-like tables with consistent schemas.  This post includes some of what I have learned about taking advantage of these optimizations when the data has an inconsistent, deeply nested schema.
date: 2019-05-05
categories: data-science
comments: true
---

In ten years our laptops - or whatever device we're using to do scientific computing - will have no trouble computing a regression on a terabyte of data.
But in 2019 it takes a bit of engineering savvy to do it efficiently even with datasets on the order of a dozen gigabytes or so.
There are a number of different tools and approaches depending on the goals and constraints, but one thing they mostly have in common is that they prefer datasets with simple, consistent schemas.

Of course, many important real-world datasets make a mockery of that preference.
Real-world data tends to describe varied interactions between complex actors or objects, and expectations of simplicity or consistency are just too optimistic.
Imagine trying to create a consistent schema for objects in [Wikidata][1] to get a sense of how bad it can get.

For now, let us work with the following example dataset:

```python
{"name": "arya", "weapon": "needle"}
{"name": "sansa", "family": {"spouses": [{"name": "tyrion", "alive": true}, {"name": "ramsay", "alive": false}]}}
{"name": "cersei", "family": {"children": ["joffrey", "myrcella", "tommen"], "spouses": [{"name": "robert", "alive": false}]}}
{"name": "jamie", "weapon": "oathkeeper", "family": {"children": ["joffrey", "myrcella", "tommen"]}}
```

This is obviously not a lot of data and it structurally isn't too complicated, but it already illustrates some of the challenges which one faces at scale.
The fields `weapon` and `family` are not present for all objects, and even among objects with a `family` field the subfields `spouses` and `children` are also not consistent.
How can we efficiently read, parse, and operate on this dataset?

## Spark's native JSON parser

The standard, preferred answer is to read the data using Spark's highly optimized `DataFrameReader`.
The starting point for this is a `SparkSession` object, provided for you automatically in a variable called `spark` if you are using the REPL.
The code is simple:

```python
df = spark.read.json(path_to_data)
df.show(truncate=False)
```
```
+------------------------------------------------+------+----------+
|family                                          |name  |weapon    |
+------------------------------------------------+------+----------+
|null                                            |arya  |needle    |
|[, [[true, tyrion], [false, ramsay]]]           |sansa |null      |
|[[joffrey, myrcella, tommen], [[false, robert]]]|cersei|null      |
|[[joffrey, myrcella, tommen],]                  |jamie |oathkeeper|
+------------------------------------------------+------+----------+
```

As you can see Spark did a lot of work behind the scenes: it read each line from the file, deserialized the JSON, inferred a schema, and merged the schemas together into one global schema for the whole dataset, filling missing values with `null` when necessary.
All of this work is great, but it can slow things down quite a lot, particularly in the schema inference step: Spark achieves this by reading in a sample of the data and inferring a schema from the sample before reading in the full dataset, and this can add quite a lot of overhead.

There are several ways to deal with this.
The best way is to transform all of your data from JSON to parquet.
There are many reasons why this is a great idea - I'll write another blog post about it - but one of them is that parquet files know their own schema and hence the schema inference step is eliminated.
But reformatting large datasets isn't always a viable option, so it's worth exploring JSON-specific options.

One is to reduce the schema sampling ratio:

```python
spark.read.option('samplingRatio', 0.1).json(path_to_data)
```

This makes sense in theory, but in practice Spark often has to read in the entire dataset in order to extract even a relatively small sampling ratio.
And it is error-prone for datasets that have rare fields.
I haven't had much luck using this approach.

A better option is to prepare a schema yourself so that Spark can skip the schema inference step entirely.
The problem is that writing Spark schemas yourself by hand is a rather laborious process; here is what the schema looks like for our simple example dataset:

```python
{'type': 'struct',
 'fields': [{'name': 'family',
   'type': {'type': 'struct',
    'fields': [{'name': 'children',
      'type': {'type': 'array', 'elementType': 'string', 'containsNull': True},
      'nullable': True,
      'metadata': {}},
     {'name': 'spouses',
      'type': {'type': 'array',
       'elementType': {'type': 'struct',
        'fields': [{'name': 'alive',
          'type': 'boolean',
          'nullable': True,
          'metadata': {}},
         {'name': 'name',
          'type': 'string',
          'nullable': True,
          'metadata': {}}]},
       'containsNull': True},
      'nullable': True,
      'metadata': {}}]},
   'nullable': True,
   'metadata': {}},
  {'name': 'name', 'type': 'string', 'nullable': True, 'metadata': {}},
  {'name': 'weapon', 'type': 'string', 'nullable': True, 'metadata': {}}]}
```

The best option, in my experience, is a hybrid approach: let Spark infer a schema on a small but well-chosen sample and then apply that schema to the rest of the data.
Often you know more than Spark does about your data, and so you can intelligently prepare a small sample that is representative of the whole dataset.
For instance, maybe your dataset is split into thousands of separate files, but you are confident that one or two of the files has enough structural variability to provide an adequate schema.
Here's what it would look like:

```python
sample_schema = spark.read.json(path_to_sample).schema
df = spark.read.schema(sample_schema).json(path_to_data)
```

## Parse it yourself

All told the best way I have found for reading in large amounts of JSON data is to use the `DataFrameReader` with a provided schema.
But it doesn't always work: there are datasets which are so complicated that Spark errors out before it can infer a schema, and it is too hard to build one manually.
The Wikidata database dump alluded to above is an example of such a dataset; I'm sure there are others.

In these cases you may have little choice but to dynamically deserialize the JSON yourself, using the `json` module or some other tool.
The code is fairly straightforward:

```python
rdd = sc.textFile(path_to_data).map(json.loads)
```

Note that we use `sc`, the default name for the `SparkContext` variable provided by the Spark REPL, rather than `spark`.
The function `sc.textFile()` reads the data in line-by-line and stores the lines as strings, and then the `.map(json.loads)` step deserializes those strings into Python dictionaries.
If the dataset is very large and the JSON is very complicated then the deserialization process will take a long time, so this should really be treated as a last resort.
Additionally the RDD API does not have nearly as many optimizations under the hood as the DataFrame API, so whatever you do next will likely be slower than it should be.

Another option in this direction is to use the DataFrame function `from_json`, introduced in Spark 2.1.
This approach would look like:

```python
spark.read.text(path_to_data).select(from_json('value', schema))
```

The `schema` variable can either be a Spark schema (as in the last section), a DDL string, or a JSON format string.
I'm not sure what advantage, if any, this approach has over invoking the native `DataFrameReader` with a prescribed schema, though certainly it would come in handy for, say, CSV data with a column whose entries are JSON strings.

## Processing nested data

Let us assume that you have successfully read in your data as a Spark DataFrame.
A lot of the hard work is over: Spark has applied a global schema to your data, and the Catalyst engine is ready to go.
I won't try to give an overview of the whole Spark API - this post is long enough as it is - but I will try to explain some of the tools that Spark provides specifically for working with nested data.

### Nested selections

Just like in SQL, nested fields are addressed using periods.
Here is how to build a DataFrame consisting of each person's name and a list of their children from our example dataset:

```python
df.select('name', 'family.children').show(truncate=False)
```
```
+------+---------------------------+
|name  |children                   |
+------+---------------------------+
|arya  |null                       |
|sansa |null                       |
|cersei|[joffrey, myrcella, tommen]|
|jamie |[joffrey, myrcella, tommen]|
+------+---------------------------+
```

For array columns the `explode` function (available in the module `pyspark.sql.functions`) is handy; here is how to build a DataFrame of all parent / child relationships in the data:

```python
df.select('name', explode('family.children')).dropna().show(truncate=False)
```
```
+------+--------+
|name  |col     |
+------+--------+
|cersei|joffrey |
|cersei|myrcella|
|cersei|tommen  |
|jamie |joffrey |
|jamie |myrcella|
|jamie |tommen  |
+------+--------+
```

The column `family.spouses` is also an array column, but the elements of this array are structs.
The same nested selection synax still works for these structs, and it has the effect of generating an array column whose elements are the values of the field selected from the original array column.
The following code produces each person's name and a list of their spouses' names:

```python
df.select('name', 'family.spouses.name')
```
```
+------+----------------+
|name  |name            |
+------+----------------+
|arya  |null            |
|sansa |[tyrion, ramsay]|
|cersei|[robert]        |
|jamie |null            |
+------+----------------+
```

Observe that spark uses the nested field name - in this case `name` - as the name for the selected column in the new DataFrame.
It is not uncommon for this to create duplicated column names as we see above, and further operations with the duplicated name will cause Spark to throw an `AnalysisException`.
The easiest way to deal with this is to alias the column while selecting:

```python
df.select('name', col('family.spouses.name').alias('spouse_name')).show(truncate=False)
```
```
+------+----------------+
|name  |spouse_name     |
+------+----------------+
|arya  |null            |
|sansa |[tyrion, ramsay]|
|cersei|[robert]        |
|jamie |null            |
+------+----------------+
```

### Parsing the schema

The nested selection syntax is very handy, but it requires the user to have a precise understanding of the schema.
Spark DataFrames know their own schema and are happy to show it to you via `df.printSchema()`, but as indicated previously the schema can be very complicated even for relatively manageable datasets, particularly if the data is highly nested.
So I wrote some helper code which parses the schema into ready-to-go selection strings; it looks like this:

```python
def spark_schema_to_string(schema_json, progress=''):
    if schema['type'] == 'struct':
        for field in schema['fields']:
            key = field['name']
            yield from spark_schema_to_string(field, f'{progress}.{key}')
    elif schema['type'] == 'array':
        if type(schema['elementType']) == dict:
            yield from spark_schema_to_string(schema['elementType'], progress)
        else:
            yield progress.strip('.')
    elif type(schema['type']) == dict:
        yield from spark_schema_to_string(schema['type'], progress)
    else:
        yield progress.strip('.')
```

This function produces a generator which iterates through all possible nested selections in a DataFrame; it should be invoked on the JSON representation of the DataFrame's schema as follows:

```python
list(spark_schema_to_string(df.schema.jsonValue()))
```
```
['family.children',
 'family.spouses.alive',
 'family.spouses.name',
 'name',
 'weapon']
```

You still have to look at the full schema to determine which fields are arrays, but I have found that this representation of the schema to be a much more convenient hook when first getting to know a dataset.
The algorithm above is fairly Python-specific in that it makes crucial use of the `yield from` syntax, but it can be adapted relatively easily to Scala or Java using `flatMap` instead.

### Higher order SQL functions

I will conclude this post by providing a few tips and examples for manipulating nested data.
When in doubt it is possible to do most things using a combination of `select`, `explode`, `groupBy`, and structured aggregations like `collect_list` and `collect_set`.
And Spark 2.3 added the nuclear option of `pandas_udf` which allows you to apply pandas transformations to grouped data.
But `groupBy` and `pandas_udf` aggregations can be quite expensive, particularly for unevenly distributed data, because they tend to trigger shuffles.
Fortunately, Spark 2.4 introduced some handy higher order column functions which do some basic manipulations with arrays and structs, and they are worth a look.

Suppose, for instance, we want to transform our example dataset so that the `family.spouses` column becomes a `struct` column whose keys come from the `name` column and whose values come from the `alive` column.
This is exactly what the higher order function `map_from_arrays` is for:

```python
df.select('name', map_from_arrays('family.spouses.name', 'family.spouses.alive')).show(truncate=False)
```
```
+------+----------------------------------------------------------+
|name  |map_from_arrays(family.spouses.name, family.spouses.alive)|
+------+----------------------------------------------------------+
|arya  |null                                                      |
|sansa |[tyrion -> true, ramsay -> false]                         |
|cersei|[robert -> false]                                         |
|jamie |null                                                      |
+------+----------------------------------------------------------+
```

Another example: suppose we want to construct a new column called `all_family` which contains the names of all available family members, including both spouses and children.
For this we will use the `array_union` higher order function:

```python
(
    df.select(
        'name',
        when(isnull('family.spouses.name'), array()).otherwise(col('family.spouses.name')).alias('spouse_name'),
        when(isnull('family.children'), array()).otherwise(col('family.children')).alias('child_name')
    )
    .select('name', array_union('spouse_name', 'child_name').alias('all_family'))
    .show(truncate=False)
)
```
```
+------+-----------------------------------+
|name  |all_family                         |
+------+-----------------------------------+
|arya  |[]                                 |
|sansa |[tyrion, ramsay]                   |
|cersei|[robert, joffrey, myrcella, tommen]|
|jamie |[joffrey, myrcella, tommen]        |
+------+-----------------------------------+
```

Note the use of the `when` and `otherwise` column functions in the initial selection to coerce null columns into empty arrays; these sorts of manipulations are quite common in the presence of messy nested data because Spark records missing JSON fields as nulls.

There are a variety of other higher order functions which can help manipulate standard data structures - the reader is invited to consult the `pyspark.sql.functions` [documentation][1] for more.

[1]: https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.functions
