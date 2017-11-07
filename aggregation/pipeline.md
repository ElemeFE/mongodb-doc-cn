# Aggregation Pipeline

On this page

* [Pipeline](https://docs.mongodb.com/manual/core/aggregation-pipeline/#pipeline)
* [Pipeline Expressions](https://docs.mongodb.com/manual/core/aggregation-pipeline/#pipeline-expressions)
* [Aggregation Pipeline Behavior](https://docs.mongodb.com/manual/core/aggregation-pipeline/#aggregation-pipeline-behavior)
* [Additional Resources](https://docs.mongodb.com/manual/core/aggregation-pipeline/#additional-resources)

The aggregation pipeline is a framework for data aggregation modeled on the concept of data processing pipelines. Documents enter a multi-stage pipeline that transforms the documents into aggregated results.

![](https://docs.mongodb.com/manual/_images/aggregation-pipeline.bakedsvg.svg "Diagram of the annotated aggregation pipeline operation. The aggregation pipeline has two stages: \`\`$match\`\` and \`\`$group\`\`.")

The aggregation pipeline provides an alternative to[map-reduce](https://docs.mongodb.com/manual/reference/glossary/#term-map-reduce)and may be the preferred solution for aggregation tasks where the complexity of map-reduce may be unwarranted.

Aggregation pipeline have some limitations on value types and result size. See[Aggregation Pipeline Limits](https://docs.mongodb.com/manual/core/aggregation-pipeline-limits/)for details on limits and restrictions on the aggregation pipeline.

## Pipeline

The MongoDB aggregation pipeline consists of[stages](https://docs.mongodb.com/manual/reference/operator/aggregation/#aggregation-pipeline-operator-reference). Each stage transforms the documents as they pass through the pipeline. Pipeline stages do not need to produce one output document for every input document; e.g., some stages may generate new documents or filter out documents. Pipeline stages can appear multiple times in the pipeline.

MongoDB provides the[`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate)method in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell and the[`aggregate`](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate)command for aggregation pipeline. See[Stage Operators](https://docs.mongodb.com/manual/reference/operator/aggregation/#aggregation-pipeline-operator-reference)for the available stages.

For example usage of the aggregation pipeline, consider[Aggregation with User Preference Data](https://docs.mongodb.com/manual/tutorial/aggregation-with-user-preference-data/)and[Aggregation with the Zip Code Data Set](https://docs.mongodb.com/manual/tutorial/aggregation-zip-code-data-set/).

## Pipeline Expressions

Some pipeline stages take a pipeline expression as the operand. Pipeline expressions specify the transformation to apply to the input documents. Expressions have a[document](https://docs.mongodb.com/manual/core/document/)structure and can contain other[expression](https://docs.mongodb.com/manual/meta/aggregation-quick-reference/#aggregation-expressions).

Pipeline expressions can only operate on the current document in the pipeline and cannot refer to data from other documents: expression operations provide in-memory transformation of documents.

Generally, expressions are stateless and are only evaluated when seen by the aggregation process with one exception:[accumulator](https://docs.mongodb.com/manual/reference/operator/aggregation/#aggregation-accumulator-operators)expressions.

The accumulators, used in the[`$group`](https://docs.mongodb.com/manual/reference/operator/aggregation/group/#pipe._S_group)stage, maintain their state \(e.g. totals, maximums, minimums, and related data\) as documents progress through the pipeline.

Changed in version 3.2:Some accumulators are available in the[`$project`](https://docs.mongodb.com/manual/reference/operator/aggregation/project/#pipe._S_project)stage; however, when used in the[`$project`](https://docs.mongodb.com/manual/reference/operator/aggregation/project/#pipe._S_project)stage, the accumulators do not maintain their state across documents.

For more information on expressions, see[Expressions](https://docs.mongodb.com/manual/meta/aggregation-quick-reference/#aggregation-expressions).

## Aggregation Pipeline Behavior

In MongoDB, the[`aggregate`](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate)command operates on a single collection, logically passing the_entire_collection into the aggregation pipeline. To optimize the operation, wherever possible, use the following strategies to avoid scanning the entire collection.

### Pipeline Operators and Indexes

The[`$match`](https://docs.mongodb.com/manual/reference/operator/aggregation/match/#pipe._S_match)and[`$sort`](https://docs.mongodb.com/manual/reference/operator/aggregation/sort/#pipe._S_sort)pipeline operators can take advantage of an index when they occur at the**beginning**of the pipeline.

New in version 2.4:The[`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear)pipeline operator takes advantage of a geospatial index. When using[`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear), the[`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear)pipeline operation must appear as the first stage in an aggregation pipeline.

Changed in version 3.2:Starting in MongoDB 3.2, indexes can[cover](https://docs.mongodb.com/manual/core/query-optimization/#read-operations-covered-query)an aggregation pipeline. In MongoDB 2.6 and 3.0, indexes could not cover an aggregation pipeline since even when the pipeline uses an index, aggregation still requires access to the actual documents.

### Early Filtering

If your aggregation operation requires only a subset of the data in a collection, use the[`$match`](https://docs.mongodb.com/manual/reference/operator/aggregation/match/#pipe._S_match),[`$limit`](https://docs.mongodb.com/manual/reference/operator/aggregation/limit/#pipe._S_limit), and[`$skip`](https://docs.mongodb.com/manual/reference/operator/aggregation/skip/#pipe._S_skip)stages to restrict the documents that enter at the beginning of the pipeline. When placed at the beginning of a pipeline,[`$match`](https://docs.mongodb.com/manual/reference/operator/aggregation/match/#pipe._S_match)operations use suitable indexes to scan only the matching documents in a collection.

Placing a[`$match`](https://docs.mongodb.com/manual/reference/operator/aggregation/match/#pipe._S_match)pipeline stage followed by a[`$sort`](https://docs.mongodb.com/manual/reference/operator/aggregation/sort/#pipe._S_sort)stage at the start of the pipeline is logically equivalent to a single query with a sort and can use an index. When possible, place[`$match`](https://docs.mongodb.com/manual/reference/operator/aggregation/match/#pipe._S_match)operators at the beginning of the pipeline.

### Additional Features

The aggregation pipeline has an internal optimization phase that provides improved performance for certain sequences of operators. For details, see[Aggregation Pipeline Optimization](https://docs.mongodb.com/manual/core/aggregation-pipeline-optimization/).

The aggregation pipeline supports operations on sharded collections. See[Aggregation Pipeline and Sharded Collections](https://docs.mongodb.com/manual/core/aggregation-pipeline-sharded-collections/#aggregation-pipeline-sharded-collection).

## Additional Resources

* [MongoDB Analytics: Learn Aggregation by Example: Exploratory Analytics and Visualization Using Flight Data](http://www.mongodb.com/presentations/mongodb-analytics-learn-aggregation-example-exploratory-analytics-and-visualization?jmp=docs)
* [MongoDB for Time Series Data: Analyzing Time Series Data Using the Aggregation Framework and Hadoop](http://www.mongodb.com/presentations/mongodb-time-series-data-part-2-analyzing-time-series-data-using-aggregation-framework?jmp=docs)
* [The Aggregation Framework](https://www.mongodb.com/presentations/aggregation-framework-0?jmp=docs)
* [Webinar: Exploring the Aggregation Framework](https://www.mongodb.com/webinar/exploring-the-aggregation-framework?jmp=docs)
* [Quick Reference Cards](https://www.mongodb.com/lp/misc/quick-reference-cards?jmp=docs)



