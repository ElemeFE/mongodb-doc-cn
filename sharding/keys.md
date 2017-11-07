# Shard Keys

On this page

* [Shard Key Specification](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-specification)
* [Shard Key Indexes](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-indexes)
* [Choosing a Shard Key](https://docs.mongodb.com/manual/core/sharding-shard-key/#choosing-a-shard-key)

The shard key determines the distribution of the collection’s[documents](https://docs.mongodb.com/manual/reference/glossary/#term-document)among the cluster’s[shards](https://docs.mongodb.com/manual/reference/glossary/#term-shard). The shard key is either an indexed[field](https://docs.mongodb.com/manual/reference/glossary/#term-field)or indexed[compound](https://docs.mongodb.com/manual/reference/glossary/#term-compound-index)fields that exists in every document in the collection.

MongoDB[partitions](https://docs.mongodb.com/manual/reference/glossary/#term-data-partition)data in the collection using ranges of shard key values. Each range defines a non-overlapping range of shard key values and is associated with a[chunk](https://docs.mongodb.com/manual/reference/glossary/#term-chunk).

MongoDB attempts to distribute chunks evenly among the shards in the cluster. The shard key has a direct relationship to the effectiveness of chunk distribution. See[Choosing a Shard Key](https://docs.mongodb.com/manual/core/sharding-shard-key/#sharding-shard-key-selection).

![](https://docs.mongodb.com/manual/_images/sharding-range-based.bakedsvg.svg "Diagram of the shard key value space segmented into smaller ranges or chunks.")

IMPORTANT

Once you shard a collection, the shard key and the shard key values are immutable; i.e.

* You cannot select a different shard key for that collection.
* You cannot update the values of the shard key fields.

## Shard Key Specification

To shard a collection, you must specify the target collection and the shard key to the[`sh.shardCollection()`](https://docs.mongodb.com/manual/reference/method/sh.shardCollection/#sh.shardCollection)method:

```
sh
.
shardCollection
(
namespace
,
key
)
```

* The
  `namespace`
  parameter consists of a string
  `<`
  `database`
  `>`
  `.`
  `<`
  `collection`
  `>`
  specifying the full
  [namespace](https://docs.mongodb.com/manual/reference/glossary/#term-namespace)
  of the target collection.
* The
  `key`
  parameter consists of a document containing a field and the index traversal direction for that field.

For instructions specific to sharding a collection using the[hashed sharding](https://docs.mongodb.com/manual/core/hashed-sharding/#sharding-hashed)strategy, see[Shard a Collection using Hashed Sharding](https://docs.mongodb.com/manual/tutorial/deploy-sharded-cluster-hashed-sharding/#deploy-hashed-sharded-cluster-shard-collection)

For instructions specific to sharding a collection using the[ranged sharding](https://docs.mongodb.com/manual/core/ranged-sharding/#sharding-ranged)strategy, see[Shard a Collection using Ranged Sharding](https://docs.mongodb.com/manual/tutorial/deploy-sharded-cluster-ranged-sharding/#deploy-ranged-sharded-cluster-shard-collection).

## Shard Key Indexes

All sharded collections**must**have an index that supports the[shard key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key); i.e. the index can be an index on the shard key or a[compound index](https://docs.mongodb.com/manual/reference/glossary/#term-compound-index)where the shard key is a[prefix](https://docs.mongodb.com/manual/core/index-compound/#compound-index-prefix)of the index.

* If the collection is empty,
  [`sh.shardCollection()`](https://docs.mongodb.com/manual/reference/method/sh.shardCollection/#sh.shardCollection)
  creates the index on the shard key if such an index does not already exists.
* If the collection is not empty, you must create the index first before using
  [`sh.shardCollection()`](https://docs.mongodb.com/manual/reference/method/sh.shardCollection/#sh.shardCollection)
  .

If you drop the last valid index for the shard key, recover by recreating an index on just the shard key.

### Unique Indexes

For a sharded collection, only the`_id`field index and the index on the shard key or a[compound index](https://docs.mongodb.com/manual/reference/glossary/#term-compound-index)where the shard key is a[prefix](https://docs.mongodb.com/manual/core/index-compound/#compound-index-prefix)can be[unique](https://docs.mongodb.com/manual/core/index-unique/):

* You cannot shard a collection that has unique indexes on other fields.
* You cannot create unique indexes on other fields for a sharded collection.

Through the use of the unique index on the shard key, MongoDB_can_enforce uniqueness on the shard key values. MongoDB enforces uniqueness on the_entire_key combination, and not individual components of the shard key. To enforce uniqueness on the shard key values, pass the`unique`parameter as`true`to the[`sh.shardCollection()`](https://docs.mongodb.com/manual/reference/method/sh.shardCollection/#sh.shardCollection)method:

* If the collection is empty,
  [`sh.shardCollection()`](https://docs.mongodb.com/manual/reference/method/sh.shardCollection/#sh.shardCollection)
  creates the unique index on the shard key if such an index does not already exists.
* If the collection is not empty, you must create the index first before using
  [`sh.shardCollection()`](https://docs.mongodb.com/manual/reference/method/sh.shardCollection/#sh.shardCollection)
  .

Although you can have a unique[compound index](https://docs.mongodb.com/manual/reference/glossary/#term-compound-index)where the shard key is a[prefix](https://docs.mongodb.com/manual/core/index-compound/#compound-index-prefix), if using`unique`parameter, the collection must have a unique index that is on the shard key.

You cannot specify a unique constraint on a[hashed index](https://docs.mongodb.com/manual/core/index-hashed/#index-type-hashed).

## Choosing a Shard Key

The choice of shard key affects how the[sharded cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster)[balancer](https://docs.mongodb.com/manual/reference/glossary/#term-balancer)creates and distributes[chunks](https://docs.mongodb.com/manual/reference/glossary/#term-chunk)across the available[shards](https://docs.mongodb.com/manual/reference/glossary/#term-shard). This affects the overall efficiency and performance of operations within the sharded cluster.

The shard key affects the performance and efficiency of the[sharding strategy](https://docs.mongodb.com/manual/sharding/#sharding-strategy)used by the sharded cluster.

The ideal shard key allows MongoDB to distribute documents evenly throughout the cluster.

![](https://docs.mongodb.com/manual/_images/sharded-cluster-ranged-distribution-good.bakedsvg.svg "Diagram of good shard key distribution")

At minimum, consider the consequences of the[cardinality](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-range),[frequency](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-frequency), and rate of[change](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-monotonic)of a potential shard key.

### Restrictions

For restrictions on shard key, see[Shard Key Limitations](https://docs.mongodb.com/manual/reference/limits/#limits-shard-keys).

### Collection Size

When sharding a collection that is not empty, the shard key can constrain the maximum supported collection size for the initial sharding operation only. See`ShardingExistingCollectionDataSize`.

IMPORTANT

A sharded collection can grow to any size after successful sharding.

### Shard Key Cardinality

The[cardinality](https://docs.mongodb.com/manual/reference/glossary/#term-cardinality)of a shard key determines the maximum number of chunks the balancer can create. This can reduce or remove the effectiveness of horizontal scaling in the cluster.

A unique shard key value can exist on no more than a single chunk at any given time. If a shard key has a cardinality of`4`, then there can be no more than`4`chunks within the sharded cluster, each storing one unique shard key value. This constrains the number of effective shards in the cluster to`4`as well - adding additional shards would not provide any benefit.

The following image illustrates a sharded cluster using the field`X`as the shard key. If`X`has low cardinality, the distribution of inserts may look similar to the following:

![](https://docs.mongodb.com/manual/_images/sharded-cluster-ranged-distribution-low-cardinal.bakedsvg.svg "Diagram of poor shard key distribution due to low cardinality")

The cluster in this example would_not_scale horizontally, as incoming writes would only route to a subset of shards.

A shard key with high cardinality does not guarantee even distribution of data across the sharded cluster, though it does better facilitate horizontal scaling. The[frequency](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-frequency)and[rate of change](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-monotonic)of the shard key also contributes to data distribution. Consider each factor when choosing a shard key.

If your data model requires sharding on a key that has low cardinality, consider using a[compound index](https://docs.mongodb.com/manual/reference/glossary/#term-compound-index)using a field that has higher relative cardinality.

### Shard Key Frequency

Consider a set representing the range of shard key values - the`frequency`of the shard key represents how often a given value occurs in the data. If the majority of documents contain only a subset of those values, then the chunks storing those documents become a bottleneck within the cluster. Furthermore, as those chunks grow, they may become[indivisible chunks](https://docs.mongodb.com/manual/core/sharding-data-partitioning/#jumbo-chunks)as they cannot be split any further. This reduces or removes the effectiveness of horizontal scaling within the cluster.

The following image illustrates a sharded cluster using the field`X`as the shard key. If a subset of values for`X`occur with high frequency, the distribution of inserts may look similar to the following:

![](https://docs.mongodb.com/manual/_images/sharded-cluster-ranged-distribution-frequency.bakedsvg.svg "Diagram of poor shard key distribution due to high frequency")

A shard key with low frequency does not guarantee even distribution of data across the sharded cluster. The[cardinality](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-cardinality)and[rate of change](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-monotonic)of the shard key also contributes to data distribution. Consider each factor when choosing a shard key.

If your data model requires sharding on a key that has high frequency values, consider using a[compound index](https://docs.mongodb.com/manual/reference/glossary/#term-compound-index)using a unique or low frequency value.

### Monotonically Changing Shard Keys

A shard key on a value that increases or decreases monotonically is more likely to distribute inserts to a single shard within the cluster.

This occurs because every cluster has a chunk that captures a range with an upper bound of[`maxKey`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#data_maxkey).`maxKey`always compares as higher than all other values. Similarly, there is a chunk that captures a range with a lower bound of[`minKey`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#data_minkey).`minKey`always compares as lower than all other values.

If the shard key value is always increasing, all new inserts are routed to the chunk with`maxKey`as the upper bound. If the shard key value is always decreasing, all new inserts are routed to the chunk with`minKey`as the lower bound. The shard containing that chunk becomes the bottleneck for write operations.

The following image illustrates a sharded cluster using the field`X`as the shard key. If the values for`X`are monotonically increasing, the distribution of inserts may look similar to the following:

![](https://docs.mongodb.com/manual/_images/sharded-cluster-monotonic-distribution.bakedsvg.svg "Diagram of poor shard key distribution due to monotonically increasing or decreasing shard key")

If the shard key value was monotonically decreasing, then all inserts would route to`ChunkA`instead.

A shard key that does not change monotonically does not guarantee even distribution of data across the sharded cluster. The[cardinality](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-cardinality)and[frequency](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-frequency)of the shard key also contributes to data distribution. Consider each factor when choosing a shard key.

If your data model requires sharding on a key that changes monotonically, consider using[Hashed Sharding](https://docs.mongodb.com/manual/core/hashed-sharding/).

