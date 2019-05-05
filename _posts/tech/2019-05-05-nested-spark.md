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

In this post I will offer some tips and best practices for operating with real-world data in the context of [Apache Spark][1].
Spark provides high-level APIs for operating over datasets that are distributed in memory over a compute cluster.
I will assume that the reader is already generally familiar with these APIs, but I shouldn't need to go too deep.

## Data preparation

Let us work with the following example dataset:

```python
{"name": "arya", "weapon": "needle"}
{"name": "sansa", "family": {"spouses": ["tyrion", "ramsay"]}}
{"name": "cersei", "family": {"children": ["joffrey", "myrcella", "tommen"], "spouses": ["robert"]}}
{"name": "jamie", "weapon": "oathkeeper", "family": {"children": ["joffrey", "myrcella", "tommen"]}}
```

This is obviously not a lot of data and it structurally isn't too complicated, but it already illustrates some of the challenges which one faces at scale.
The fields `weapon` and `family` are not present for all objects, and even among objects with a `family` field the subfields `spouses` and `children` are also not consistent.

How should we go about reading in the data and parsing it into an appropriate data structure?
Spark gives you basically basically two choices, depending on which of the Spark API's you wish to use.

### Preferred: DataFrame API

The preferred answer is to read the data using Spark's highly optimized `DataFrameReader`.
The starting point for this is a `SparkSession` object, provided for you automatically in a variable called `spark` if you are using the REPL.
The code is simple:

```python
df = spark.read.json(path_to_data)
df.show()
```
```
+---------------------------------------+------+----------+
|family                                 |name  |weapon    |
+---------------------------------------+------+----------+
|null                                   |arya  |needle    |
|[, [tyrion, ramsay]]                   |sansa |null      |
|[[joffrey, myrcella, tommen], [robert]]|cersei|null      |
|[[joffrey, myrcella, tommen],]         |jamie |oathkeeper|
+---------------------------------------+------+----------+
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
      'type': {'type': 'array', 'elementType': 'string', 'containsNull': True},
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

### If necessary: RDD API

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

Nevertheless this is a good technique for when you first get started with a complex dataset: it is guaranteed to work (well, with some exception handling for corrupt JSON entries), so hopefully it gives you a hook for transforming your data into something more usable.

### Ideally: Parquet

The absolute best way to operate over large amounts of JSON is to make it stop being JSON.
