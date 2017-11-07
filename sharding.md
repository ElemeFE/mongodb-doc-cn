# Sharding

On this page

* [Sharded Cluster](https://docs.mongodb.com/manual/sharding/#sharded-cluster)
* [Shard Keys](https://docs.mongodb.com/manual/sharding/#shard-keys)
* [Chunks](https://docs.mongodb.com/manual/sharding/#chunks)
* [Advantages of Sharding](https://docs.mongodb.com/manual/sharding/#advantages-of-sharding)
* [Considerations Before Sharding](https://docs.mongodb.com/manual/sharding/#considerations-before-sharding)
* [Sharded and Non-Sharded Collections](https://docs.mongodb.com/manual/sharding/#sharded-and-non-sharded-collections)
* [Connecting to a Sharded Cluster](https://docs.mongodb.com/manual/sharding/#connecting-to-a-sharded-cluster)
* [Sharding Strategy](https://docs.mongodb.com/manual/sharding/#sharding-strategy)
* [Zones in Sharded Clusters](https://docs.mongodb.com/manual/sharding/#zones-in-sharded-clusters)
* [Collations in Sharding](https://docs.mongodb.com/manual/sharding/#collations-in-sharding)
* [Additional Resources](https://docs.mongodb.com/manual/sharding/#additional-resources)

[Sharding](https://docs.mongodb.com/manual/reference/glossary/#term-sharding)is a method for distributing data across multiple machines. MongoDB uses sharding to support deployments with very large data sets and high throughput operations.

Database systems with large data sets or high throughput applications can challenge the capacity of a single server. For example, high query rates can exhaust the CPU capacity of the server. Working set sizes larger than the system’s RAM stress the I/O capacity of disk drives.

There are two methods for addressing system growth: vertical and horizontal scaling.

_Vertical Scaling_involves increasing the capacity of a single server, such as using a more powerful CPU, adding more RAM, or increasing the amount of storage space. Limitations in available technology may restrict a single machine from being sufficiently powerful for a given workload. Additionally, Cloud-based providers have hard ceilings based on available hardware configurations. As a result, there is a practical maximum for vertical scaling.

_Horizontal Scaling_involves dividing the system dataset and load over multiple servers, adding additional servers to increase capacity as required. While the overall speed or capacity of a single machine may not be high, each machine handles a subset of the overall workload, potentially providing better efficiency than a single high-speed high-capacity server. Expanding the capacity of the deployment only requires adding additional servers as needed, which can be a lower overall cost than high-end hardware for a single machine. The trade off is increased complexity in infrastructure and maintenance for the deployment.

MongoDB supports_horizontal scaling_through[sharding](https://docs.mongodb.com/manual/reference/glossary/#term-sharding).

## Sharded Cluster

A MongoDB[sharded cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster)consists of the following components:

* [shard](https://docs.mongodb.com/manual/core/sharded-cluster-shards/)
  : Each shard contains a subset of the sharded data. Each shard can be deployed as a
  [replica set](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set)
  .
* [mongos](https://docs.mongodb.com/manual/core/sharded-cluster-query-router/)
  : The
  `mongos`
  acts as a query router, providing an interface between client applications and the sharded cluster.
* [config servers](https://docs.mongodb.com/manual/core/sharded-cluster-config-servers/)
  : Config servers store metadata and configuration settings for the cluster. As of MongoDB 3.4, config servers must be deployed as a replica set \(CSRS\).

The following graphic describes the interaction of components within a sharded cluster:

![](https://docs.mongodb.com/manual/_images/sharded-cluster-production-architecture.bakedsvg.svg "Diagram of a sample sharded cluster for production purposes.  Contains exactly 3 config servers, 2 or more \`\`mongos\`\` query routers, and at least 2 shards. The shards are replica sets.")

MongoDB shards data at the[collection](https://docs.mongodb.com/manual/reference/glossary/#term-collection)level, distributing the collection data across the shards in the cluster.

## Shard Keys

To distribute the documents in a collection, MongoDB[partitions](https://docs.mongodb.com/manual/reference/glossary/#term-data-partition)the collection using the[shard key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key). The[shard key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key)consists of an immutable field or fields that exist in every document in the target collection.

You choose the shard key when sharding a collection. The choice of shard key cannot be changed after sharding. A sharded collection can have only_one_shard key. See[Shard Key Specification](https://docs.mongodb.com/manual/core/sharding-shard-key/#sharding-shard-key-creation).

To shard a non-empty collection, the collection must have an[index](https://docs.mongodb.com/manual/reference/glossary/#term-index)that starts with the shard key. For empty collections, MongoDB creates the index if the collection does not already have an appropriate index for the specified shard key. See[Shard Key Indexes](https://docs.mongodb.com/manual/core/sharding-shard-key/#sharding-shard-key-indexes).

The choice of shard key affects the performance, efficiency, and scalability of a sharded cluster. A cluster with the best possible hardware and infrastructure can be bottlenecked by the choice of shard key. The choice of shard key and its backing index can also affect the[sharding strategy](https://docs.mongodb.com/manual/sharding/#sharding-strategy)that your cluster can use.

See the[shard key](https://docs.mongodb.com/manual/core/sharding-shard-key/)documentation for more information.

## Chunks

MongoDB partitions sharded data into[chunks](https://docs.mongodb.com/manual/reference/glossary/#term-chunk). Each chunk has an inclusive lower and exclusive upper range based on the[shard key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key).

MongoDB migrates[chunks](https://docs.mongodb.com/manual/reference/glossary/#term-chunk)across the[shards](https://docs.mongodb.com/manual/reference/glossary/#term-shard)in the[sharded cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster)using the[sharded cluster balancer](https://docs.mongodb.com/manual/core/sharding-balancer-administration/). The balancer attempts to achieve an even balance of chunks across all shards in the cluster.

See[Data Partitioning with Chunks](https://docs.mongodb.com/manual/core/sharding-data-partitioning/)for more information.

## Advantages of Sharding

### Reads / Writes

MongoDB distributes the read and write workload across the[shards](https://docs.mongodb.com/manual/reference/glossary/#term-shard)in the[sharded cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster), allowing each shard to process a subset of cluster operations. Both read and write workloads can be scaled horizontally across the cluster by adding more shards.

For queries that include the shard key or the prefix of a[compound](https://docs.mongodb.com/manual/reference/glossary/#term-compound-index)shard key,[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)can target the query at a specific shard or set of shards. These[targeted operations](https://docs.mongodb.com/manual/core/sharded-cluster-query-router/#sharding-mongos-targeted)are generally more efficient than[broadcasting](https://docs.mongodb.com/manual/core/sharded-cluster-query-router/#sharding-mongos-broadcast)to every shard in the cluster.

### Storage Capacity

[Sharding](https://docs.mongodb.com/manual/reference/glossary/#term-sharding)distributes data across the[shards](https://docs.mongodb.com/manual/reference/glossary/#term-shard)in the cluster, allowing each shard to contain a subset of the total cluster data. As the data set grows, additional shards increase the storage capacity of the cluster.

### High Availability

A[sharded cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster)can continue to perform partial read / write operations even if one or more shards are unavailable. While the subset of data on the unavailable shards cannot be accessed during the downtime, reads or writes directed at the available shards can still succeed.

Starting in MongoDB 3.2, you can deploy[config servers](https://docs.mongodb.com/manual/reference/glossary/#term-config-server)as[replica sets](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set). A sharded cluster with a Config Server Replica Set \(CSRS\) can continue to process reads and writes as long as a majority of the replica set is available. In version 3.4, MongoDB removes support for SCCC config servers. To upgrade your config servers from SCCC to CSRS, see[Upgrade Config Servers to Replica Set](https://docs.mongodb.com/manual/tutorial/upgrade-config-servers-to-replica-set/).

In production environments, individual shards should be deployed as[replica sets](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set), providing increased redundancy and availability.

## Considerations Before Sharding

Sharded cluster infrastructure requirements and complexity require careful planning, execution, and maintenance.

Careful consideration in choosing the shard key is necessary for ensuring cluster performance and efficiency. You cannot change the shard key after sharding, nor can you unshard a sharded collection. See[Choosing a Shard Key](https://docs.mongodb.com/manual/core/sharding-shard-key/#sharding-internals-operations-and-reliability).

Sharding has certain[operational requirements and restrictions](https://docs.mongodb.com/manual/core/sharded-cluster-requirements/#sharding-operational-restrictions). See[Operational Restrictions in Sharded Clusters](https://docs.mongodb.com/manual/core/sharded-cluster-requirements/)for more information.

If queries do_not_include the shard key or the prefix of a[compound](https://docs.mongodb.com/manual/reference/glossary/#term-compound-index)shard key,[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)performs a[broadcast operation](https://docs.mongodb.com/manual/core/sharded-cluster-query-router/#sharding-mongos-broadcast), querying_all_shards in the sharded cluster. These scatter/gather queries can be long running operations.

NOTE

If you have an active support contract with MongoDB, consider contacting your account representative for assistance with sharded cluster planning and deployment.

## Sharded and Non-Sharded Collections

A database can have a mixture of sharded and unsharded collections. Sharded collections are[partitioned](https://docs.mongodb.com/manual/reference/glossary/#term-data-partition)and distributed across the[shards](https://docs.mongodb.com/manual/reference/glossary/#term-shard)in the cluster. Unsharded collections are stored on a[primary shard](https://docs.mongodb.com/manual/reference/glossary/#term-primary-shard). Each database has its own primary shard.

![](https://docs.mongodb.com/manual/_images/sharded-cluster-primary-shard.bakedsvg.svg "Diagram of a primary shard. A primary shard contains non-sharded collections as well as chunks of documents from sharded collections. Shard A is the primary shard.")

## Connecting to a Sharded Cluster

You must connect to a[mongos](https://docs.mongodb.com/manual/reference/glossary/#term-mongos)router to interact with any collection in the[sharded cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster). This includes sharded_and_unsharded collections. Clients should_never_connect to a single shard in order to perform read or write operations.

![](https://docs.mongodb.com/manual/_images/sharded-cluster-mixed.bakedsvg.svg "Diagram of applications/drivers issuing queries to mongos for unsharded collection as well as sharded collection. Config servers not shown.")

You can connect to a[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)the same way you connect to a[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod), such as via the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell or a MongoDB[driver](https://docs.mongodb.com/ecosystem/drivers?jump=docs).

## Sharding Strategy

MongoDB supports two sharding strategies for distributing data across[sharded clusters](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster).

### Hashed Sharding

Hashed Sharding involves computing a hash of the shard key field’s value. Each[chunk](https://docs.mongodb.com/manual/reference/glossary/#term-chunk)is then assigned a range based on the hashed shard key values.

TIP

MongoDB automatically computes the hashes when resolving queries using hashed indexes. Applications do**not**need to compute hashes.

![](https://docs.mongodb.com/manual/_images/sharding-hash-based.bakedsvg.svg "Diagram of the hashed based segmentation.")

While a range of shard keys may be “close”, their hashed values are unlikely to be on the same[chunk](https://docs.mongodb.com/manual/reference/glossary/#term-chunk). Data distribution based on hashed values facilitates more even data distribution, especially in data sets where the shard key changes[monotonically](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-monotonic).

However, hashed distribution means that ranged-based queries on the shard key are less likely to target a single shard, resulting in more cluster wide[broadcast operations](https://docs.mongodb.com/manual/core/sharded-cluster-query-router/#sharding-mongos-broadcast)

See[Hashed Sharding](https://docs.mongodb.com/manual/core/hashed-sharding/)for more information.

### Ranged Sharding

Ranged sharding involves dividing data into ranges based on the shard key values. Each[chunk](https://docs.mongodb.com/manual/reference/glossary/#term-chunk)is then assigned a range based on the shard key values.

![](https://docs.mongodb.com/manual/_images/sharding-range-based.bakedsvg.svg "Diagram of the shard key value space segmented into smaller ranges or chunks.")

A range of shard keys whose values are “close” are more likely to reside on the same[chunk](https://docs.mongodb.com/manual/reference/glossary/#term-chunk). This allows for[targeted operations](https://docs.mongodb.com/manual/core/sharded-cluster-query-router/#sharding-mongos-targeted)as a[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)can route the operations to only the shards that contain the required data.

The efficiency of ranged sharding depends on the shard key chosen. Poorly considered shard keys can result in uneven distribution of data, which can negate some benefits of sharding or can cause performance bottlenecks. See[shard key selection](https://docs.mongodb.com/manual/core/ranged-sharding/#sharding-ranged-shard-key)for ranged sharding.

See[Ranged Sharding](https://docs.mongodb.com/manual/core/ranged-sharding/)for more information.

## Zones in Sharded Clusters

In sharded clusters, you can create[zones](https://docs.mongodb.com/manual/reference/glossary/#term-zone)of sharded data based on the[shard key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key). You can associate each zone with one or more shards in the cluster. A shard can associate with any number of non-conflicting zones. In a balanced cluster, MongoDB migrates[chunks](https://docs.mongodb.com/manual/reference/glossary/#term-chunk)covered by a zone only to those shards associated with the zone.

Each zone covers one or more ranges of[shard key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key)values. Each range a zone covers is always inclusive of its lower boundary and exclusive of its upper boundary.

![](https://docs.mongodb.com/manual/_images/sharded-cluster-zones.bakedsvg.svg "Diagram of data distribution based on zones in a sharded cluster")

You must use fields contained in the[shard key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key)when defining a new range for a zone to cover. If using a[compound](https://docs.mongodb.com/manual/reference/glossary/#term-compound-index)shard key, the range must include the prefix of the shard key. See[shard keys in zones](https://docs.mongodb.com/manual/core/zone-sharding/#zone-sharding-shard-key)for more information.

When choosing a shard key, carefully consider the possibility of using zone sharding in the future, as you cannot change the shard key after sharding the collection.

Most commonly, zones serve to improve the locality of data for sharded clusters that span multiple data centers.

See[zones](https://docs.mongodb.com/manual/core/zone-sharding/#zone-sharding)for more information.

## Collations in Sharding

Use the[`shardCollection`](https://docs.mongodb.com/manual/reference/command/shardCollection/#dbcmd.shardCollection)command with the`collation:{locale:"simple"}`option to shard a collection which has a[default collation](https://docs.mongodb.com/manual/reference/collation/). Successful sharding requires that:

* The collection must have an index whose prefix is the shard key
* The index must have the collation
  `{`
  `locale:`
  `"simple"`
  `}`

When creating new collections with a collation, ensure these conditions are met prior to sharding the collection.

NOTE

Queries on the sharded collection continue to use the default collation configured for the collection. To use the shard key index’s`simple`collation, specify`{locale:"simple"}`in the query’s[collation document](https://docs.mongodb.com/manual/reference/collation/).

See[`shardCollection`](https://docs.mongodb.com/manual/reference/command/shardCollection/#dbcmd.shardCollection)for more information about sharding and collation.

## Additional Resources

* [Sharding Methods for MongoDB \(Presentation\)](http://www.mongodb.com/presentations/webinar-sharding-methods-mongodb?jmp=docs)
* [Everything You Need to Know About Sharding \(Presentation\)](http://www.mongodb.com/presentations/webinar-everything-you-need-know-about-sharding?jmp=docs)
* [MongoDB for Time Series Data: Sharding](http://www.mongodb.com/presentations/mongodb-time-series-data-part-3-sharding?jmp=docs)
* [MongoDB Operations Best Practices White Paper](http://www.mongodb.com/lp/white-paper/ops-best-practices?jmp=docs)
* [Talk to a MongoDB Expert About Scaling](http://www.mongodb.com/lp/contact/planning-for-scale?jmp=docs)
* [MongoDB Consulting Package](https://www.mongodb.com/products/consulting?jmp=docs)
* [Quick Reference Cards](https://www.mongodb.com/lp/misc/quick-reference-cards?jmp=docs)



