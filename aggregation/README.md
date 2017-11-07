# Aggregation

On this page

* [Aggregation Pipeline](https://docs.mongodb.com/manual/aggregation/#aggregation-pipeline)
* [Map-Reduce](https://docs.mongodb.com/manual/aggregation/#map-reduce)
* [Single Purpose Aggregation Operations](https://docs.mongodb.com/manual/aggregation/#single-purpose-aggregation-operations)
* [Additional Features and Behaviors](https://docs.mongodb.com/manual/aggregation/#additional-features-and-behaviors)
* [Additional Resources](https://docs.mongodb.com/manual/aggregation/#additional-resources)

Aggregation operations process data records and return computed results. Aggregation operations group values from multiple documents together, and can perform a variety of operations on the grouped data to return a single result. MongoDB provides three ways to perform aggregation: the[aggregation pipeline](https://docs.mongodb.com/manual/aggregation/#aggregation-framework), the[map-reduce function](https://docs.mongodb.com/manual/aggregation/#aggregation-map-reduce), and[single purpose aggregation methods](https://docs.mongodb.com/manual/aggregation/#single-purpose-agg-operations).

## Aggregation Pipeline

MongoDB’s[aggregation framework](https://docs.mongodb.com/manual/core/aggregation-pipeline/)is modeled on the concept of data processing pipelines. Documents enter a multi-stage pipeline that transforms the documents into an aggregated result.

The most basic pipeline stages provide_filters_that operate like queries and_document transformations_that modify the form of the output document.

Other pipeline operations provide tools for grouping and sorting documents by specific field or fields as well as tools for aggregating the contents of arrays, including arrays of documents. In addition, pipeline stages can use[operators](https://docs.mongodb.com/manual/reference/operator/aggregation/#aggregation-expression-operators)for tasks such as calculating the average or concatenating a string.

The pipeline provides efficient data aggregation using native operations within MongoDB, and is the preferred method for data aggregation in MongoDB.

The aggregation pipeline can operate on a[sharded collection](https://docs.mongodb.com/manual/sharding/).

The aggregation pipeline can use indexes to improve its performance during some of its stages. In addition, the aggregation pipeline has an internal optimization phase. See[Pipeline Operators and Indexes](https://docs.mongodb.com/manual/core/aggregation-pipeline/#aggregation-pipeline-operators-and-performance)and[Aggregation Pipeline Optimization](https://docs.mongodb.com/manual/core/aggregation-pipeline-optimization/)for details.

![](https://docs.mongodb.com/manual/_images/aggregation-pipeline.bakedsvg.svg "Diagram of the annotated aggregation pipeline operation. The aggregation pipeline has two stages: \`\`$match\`\` and \`\`$group\`\`.")

## Map-Reduce

MongoDB also provides[map-reduce](https://docs.mongodb.com/manual/core/map-reduce/)operations to perform aggregation. In general, map-reduce operations have two phases: a_map_stage that processes each document and_emits_one or more objects for each input document, and_reduce_phase that combines the output of the map operation. Optionally, map-reduce can have a_finalize_stage to make final modifications to the result. Like other aggregation operations, map-reduce can specify a query condition to select the input documents as well as sort and limit the results.

Map-reduce uses custom JavaScript functions to perform the map and reduce operations, as well as the optional_finalize_operation. While the custom JavaScript provide great flexibility compared to the aggregation pipeline, in general, map-reduce is less efficient and more complex than the aggregation pipeline.

Map-reduce can operate on a[sharded collection](https://docs.mongodb.com/manual/sharding/). Map-reduce operations can also output to a sharded collection. See[Aggregation Pipeline and Sharded Collections](https://docs.mongodb.com/manual/core/aggregation-pipeline-sharded-collections/)and[Map-Reduce and Sharded Collections](https://docs.mongodb.com/manual/core/map-reduce-sharded-collections/)for details.

NOTE

Starting in MongoDB 2.4, certain[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell functions and properties are inaccessible in map-reduce operations. MongoDB 2.4 also provides support for multiple JavaScript operations to run at the same time. Before MongoDB 2.4, JavaScript code executed in a single thread, raising concurrency issues for map-reduce.

![](https://docs.mongodb.com/manual/_images/map-reduce.bakedsvg.svg "Diagram of the annotated map-reduce operation.")

## Single Purpose Aggregation Operations

MongoDB also provides[`db.collection.count()`](https://docs.mongodb.com/manual/reference/method/db.collection.count/#db.collection.count)and[`db.collection.distinct()`](https://docs.mongodb.com/manual/reference/method/db.collection.distinct/#db.collection.distinct).

All of these operations aggregate documents from a single collection. While these operations provide simple access to common aggregation processes, they lack the flexibility and capabilities of the aggregation pipeline and map-reduce.

![](https://docs.mongodb.com/manual/_images/distinct.bakedsvg.svg "Diagram of the annotated distinct operation.")

## Additional Features and Behaviors

For a feature comparison of the aggregation pipeline, map-reduce, and the special group functionality, see[Aggregation Commands Comparison](https://docs.mongodb.com/manual/reference/aggregation-commands-comparison/).

## Additional Resources

* [MongoDB Analytics: Learn Aggregation by Example: Exploratory Analytics and Visualization Using Flight Data](http://www.mongodb.com/presentations/mongodb-analytics-learn-aggregation-example-exploratory-analytics-and-visualization?jmp=docs)
* [MongoDB for Time Series Data: Analyzing Time Series Data Using the Aggregation Framework and Hadoop](http://www.mongodb.com/presentations/mongodb-time-series-data-part-2-analyzing-time-series-data-using-aggregation-framework?jmp=docs)
* [The Aggregation Framework](https://www.mongodb.com/presentations/aggregation-framework-0?jmp=docs)
* [Webinar: Exploring the Aggregation Framework](https://www.mongodb.com/webinar/exploring-the-aggregation-framework?jmp=docs)
* [Quick Reference Cards](https://www.mongodb.com/lp/misc/quick-reference-cards?jmp=docs)



