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

There are two ways to deal with this.
One is to reduce the sampling ratio:

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

An even better option is to combine the previous two.
Often you know more than Spark does about your data, and so you can intelligently prepare a small sample that is representative of the whole dataset.
For instance, maybe your dataset is split into thousands of separate files, but you are confident that one or two of the files has enough structural variability to provide an adequate schema.
You can then have Spark infer a schema from your intelligent sample and then apply that schema to the rest of the data.
It looks like this:

```python
sample_schema = spark.read.json(path_to_sample)
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

## Tip: switch to parquet

The absolute best way to operate over large amounts of JSON is to make it stop being JSON.
This obviously isn't practical in all cases, but if you have control over your data ingestion pipeline or if there is a fixed dataset over which you know you will need to do many computations, then it is worth investing some time in optimizing how your data is stored.
And the most straightforward way to make this investment is to convert all of your data to the parquet format.

To be completely honest I can't explain what exactly the parquet format is, except that it was created precisely for the purpose of scaling up computations over large structured datasets.
What I know for sure is that I have reduced the running time of certain computations from several hours to several minutes simply by converting the underlying data from JSON to parquet.
Of course the difference is made up in the conversion process, which can take quite a long time, but it's a cost that you only have to pay once rather than every time you try to do even a simple computation.

How does this work?
Again, I don't know the nuts and bolts, but I can describe some of the key features:

1. **Static schema.** Every parquet file has a global schema which can be retrieved easily without parsing the rest of the file.  This completely eliminates the need for the schema inference step, which can consume much of the running time of a Spark job over JSON data.
2. **Columnar structure.** To extract the `name` field from every object in JSON-formatted data, Spark must read every row and parse enough of it to reach the `name` key.  The parquet format stores the data by column, allowing Spark to grab the fields it needs and ignore the rest of the data.
3. **Predicate pushdown.** Typical processing pipelines include filters as well as various transformations and joins.  With parquet data it is possible to execute certain kinds of filtered reads, meaning Spark can read specifically the rows which pass through the filter without reading the whole document.

These strengths are compounded by the Catalyst optimization engine that powers the Spark DataFrame API.
For instance, Spark can analyze your computation and discover that some of the filtering steps that occur at the end of your computation can be moved to the beginning, thus taking advantage of predicate pushdown without you even realizing it.

More good news: Spark makes it fairly trivial to convert whatever format you're currently using into parquet:

```python
spark.read.json(path_to_data).write.parquet(path_to_parquet)
```

Warning: if your data is organized according to a specific directory / file naming structure, this will butcher it.
But often the purpose of such a structure is to provide a sort of manual way to execute filtered reads, and spark can handle that for you!
Parquet will easily compensate you for any additional overhead.
And if it really is crucial to group your data according to a particular value in your file system, you can always repartition along the appropriate column before writing.

Of course, reading parquets is just as easy:

```python
spark.read.parquet(path_to_parquet)
```

So what do you lose by switching to parquet?
Honestly, not much.

You lose human readability: if you open a parquet file in a text editor it will look like nonsense.
This means `grep` and `sed` won't work either.
But most languages have a parquet parser, and there are even command-line utilities that help you work with parquet data.
(And let's be honest: how often do you use `sed` with JSON data?)

I think the only case where JSON is the better choice is when the data is truly, irredeemably schemaless.
If your data consists of a million objects with a hundred thousand distinct fields and each object only uses a dozen of those fields, then individually parsing each JSON object might very well be more efficient than incurring the overhead of a sparse columnar representation.
I haven't personally had to work with data like this (yet!) but it wouldn't surprise me if it does come up.

## Processing nested data

Let us assume that you have successfully read in your data as a Spark DataFrame.
A lot of the hard work is over: Spark has applied a global schema to your data, and the Catalyst engine is ready to go.
How can we manipulate the data and extract what we need?

Spark DataFrames provide an SQL-like API for querying, grouping, filtering, and transforming data, and I'm not going to try to summarize all of it.
Instead I will specifically focus on how nested selections work and what you can do with them.

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

Note that Spark fills missing fields with `null`.
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

### Higher order SQL functions

The basic selection syntax together with the rest of standard Spark functionality is enough to do most typical data processing tasks.
But Spark provides some additional built-in tools for manipulating nested data, introduced in Spark 2.4.
These are the higher order functions for working with array and struct fields.

I will leave the reader to consult the documentation for a full list of higher order functions, but to give a sense of what is possible I will use them to work through a simple example.
Suppose we wish to replace the array field `family.spouses` in our example dataset with a struct field which assigns each spouse to their status (alive or dead).
So for Sansa we want the value of `family.spouses` to be `{"tyrion": true, "ramsay": false}`.


