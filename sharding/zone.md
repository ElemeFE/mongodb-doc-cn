- [分片](http://www.mongoing.com/docs/sharding.html) > 区域

# 区域

本页内容

- [行为和操作](http://www.mongoing.com/docs/core/zone-sharding.html#behavior-and-operations)
- [其它资源](http://www.mongoing.com/docs/core/zone-sharding.html#additional-resource)

在分片集群中，您可以基于 [*shard key*](http://www.mongoing.com/docs/reference/glossary.html#term-shard-key) 创建分片数据的 [*zones*](http://www.mongoing.com/docs/reference/glossary.html#term-zone) 。您可以将每个区域与集群中的一个或者更多分片关联起来。一个分片可以和任意数目的非冲突区域关联。在一个均衡的集群中，MongoDB只会将一个区域包含的 [*chunks*](http://www.mongoing.com/docs/reference/glossary.html#term-chunk) 迁移到与该区域相关联的分片。

可以运用区域的一些常见开发模式如下：

- 在一个特定的分片集合中分隔一个特定的数据子集。
- 保证最相关的数据存储于与应用服务器地理上最相近的分片上。
- 基于硬件/分片硬件的性能将数据路由到分片。

下图展示了一个拥有三个分片和两个区域的分片集群。`A` 区域表示下限为 `1` 、上限为 `10` 的范围。`B` 区域则表示下限为 `10` 、上限为 `20` 的范围。 分片 `Alpha` 和 `Beta` 有 `A` 区域。分片 `Beta` 还拥有 `B` 区域。 分片 `Charlie` 没有区域与之相关、该集群在一个稳定的状态，没有数据块违背任何区域。

![Diagram of data distribution based on zones in a sharded cluster](http://www.mongoing.com/docs/_images/sharded-cluster-zones.png)

## 行为和操作

### 范围

每个区域设计一个或多个 [*shard key*](http://www.mongoing.com/docs/reference/glossary.html#term-shard-key) 值范围。每个区域覆盖的每个范围一般包含其下界，不包含其上界。

区域不能共享范围，它们也不能有交叉的范围。

### 均衡器

The [*balancer*](http://www.mongoing.com/docs/reference/glossary.html#term-balancer) attempts to evenly distribute a sharded collection’s chunks across all shards in the cluster.

对于每一个标记为要迁移的 [*chunk*](http://www.mongoing.com/docs/reference/glossary.html#term-chunk) ，均衡器检查所有配置区域内的每一个可能的目标分片。如果数据块范围属于某一个区域，那么均衡器就会将该数据块迁移到该区域上的一个分片。不属于任何区域的数据块可以存在于集群中的 *任何* 分片，并且正常迁移。

在均衡过程中，如果均衡器检测到任何违背已配置区域上给定的分片，均衡器将会把这些数据块迁移到不存在冲突的分片上。

在使用一个片键范围配置区域，并且将它与一个或多个分片关联起来之后，集群可能会花费一些时间来迁移影响的数据。这依赖于数据块的划分以及目前集群中数据的分布。当均衡完成之后，在某一给定区域的文档读取和写入将只会路由到该区域内的一个分片或几个分片。

一旦配置完成后，均衡器将在 [*balancing rounds*](http://www.mongoing.com/docs/core/sharding-balancer-administration.html#sharding-internals-balancing) 中重新检查区域。

### 片键

在定义一个区域覆盖的新范围时，您必须使用包含在 [*shard key*](http://www.mongoing.com/docs/reference/glossary.html#term-shard-key) 中的字段。如果使用一个 [*compound*](http://www.mongoing.com/docs/reference/glossary.html#term-compound-index) 片键，则该范围必须包含该片键的前缀。

例如，给定一个片键 `{ a : 1, b : 2, c : 3 }` ，创建或更新一个区域来覆盖 `b` 的值要求包括 `a` 作为前缀。创建或更新一个区域来覆盖 `c` 的值要求包括 `a` 和 `b` 作为前缀。

您不能使用片键中部包含的字段创建区域。例如，如果您先使用区域来对数据进行地理位置进行分区，片键中需要至少包括一个包含地理数据的字段。

当选择集合的片键时，考虑您可能需要用来配置区域的字段。在分片之后，您不能修改片键。查阅 [*通常,一个经过计算的片键会有一定的”随机性”,比如一个包含了其他字段加密哈希(例如 MD5或者SHA1)的片键,会使集群具有较好的写扩展性能.不过,随机的片键通常不会提供 查询隔离 的特性,而查询隔离同样是片键一个很重要的特性.*](http://www.mongoing.com/docs/core/sharding-shard-key.html#sharding-internals-choose-shard-key) 了解选择片键的考量。

### 哈希片键和区域

当在哈希片键上使用区域时，每个区域覆盖 *哈希* 片键值。给定一个片键值 `{ a : 1 }` 和区域一个下界为 `1` 上界为 `5` 的区域 `alpha` ，边界表示 `a` 的 *哈希* 值，而不是真实值。因此，并不能保证 MongoDB将 `a`值为 `1` 到 `5` 之间的文档路由到 `alpha` 区域。MongoDB将任何 *哈希* 片键值落入到 `1` 到 `5` 范围内的文档路由到区域 `alpha` 内的分片上。

一般说来，一个覆盖哈希片键值顺序范围的区域可能会出现一些预计不到的行为。

可以通过使用 `minkey` 和 `maxkey` 创建覆盖片键值整个范围的区域，以保证MongoDB将某个特定集合的所有数据控制存储在该区域的一个分片或几个分片上。

### 分片区域边界

区域范围一般包含下界，不包含上界。

参见

[*Manage Shard Zones*](http://www.mongoing.com/docs/tutorial/manage-shard-zone.html)

## 其它资源

- ` 白皮书： MongoDB 多数据中心部署 <[http://www.mongodb.com/lp/white-paper/multi-dc?jmp=docs](http://www.mongodb.com/lp/white-paper/multi-dc?jmp=docs)>`_
- ` 在线讲座：多数据中心部署 <[https://www.mongodb.com/presentations/webinar-multi-data-center-deployment?jmp=docs](https://www.mongodb.com/presentations/webinar-multi-data-center-deployment?jmp=docs)>`_

←  [Deploy Sharded Cluster using Ranged Sharding](http://www.mongoing.com/docs/tutorial/deploy-sharded-cluster-ranged-sharding.html)[Manage Shard Zones](http://www.mongoing.com/docs/tutorial/manage-shard-zone.html) →