# MongoDB 简介

本页索引：

* [文档数据库](#文档数据库)
* [关键特性](#关键特性)

MongoDB 是一个提供了高性能、高可用以及自动伸缩（auto scaling）功能的开源的文档数据库 (Document Database).

## 文档数据库

在 MongoDB 中一条记录 (record) 就是一个文档 (document), 文档本身类似 JSON 对象, 其数据结构是组合的键值对. 值可以是其他文档, 数组或者一个文档的数组等.

![](https://docs.mongodb.com/manual/_images/crud-annotated-document.bakedsvg.svg "A MongoDB document.")

文档的优势在于:

* 文档 \(如 Object\) 可以对应到许多编程语言内置的数据类型.
* 内嵌的文档和数组结构减少了昂贵的跨表 (join) 查询.
* 自由的表结构 (Dynamic schema) 支持流畅的多态.

## 关键特性

### 高性能

MongoDB 提供了高性能的数据持久化. 特别是,

* 通过嵌入式的数据模型来减少 I/O 操作.
* 索引可以支持快速的查询, 还可以通过通过深层结构的 (embedded) 文档和数组作为 key.

### 丰富的查询语句

MongoDB 拥有丰富的查询语句来进行 [读写操作 \(CRUD\)](https://docs.mongodb.com/manual/crud/) 以及:

* [数据聚合 (Aggregation)](https://docs.mongodb.com/manual/core/aggregation-pipeline/)
* [文本搜索](https://docs.mongodb.com/manual/text-search/)
  和
  [地理位置查询](https://docs.mongodb.com/manual/tutorial/geospatial-tutorial/)
  .

### 高可用

MongoDB 的备份功能, 名为[复制集 (replica set)](https://docs.mongodb.com/manual/replication/), 提供:

* _自动_ 熔断 (failover)
* 数据冗余

一个[复制集](https://docs.mongodb.com/manual/replication/)是一组维护相同数据集的 MongoDB 实例, 提供了冗余和新增数据的可用性.

### 水平拓展

MongoDB 将水平拓展性作为它的 _核心_ 功能:

* [分片 (Sharding)](https://docs.mongodb.com/manual/sharding/#sharding-introduction)
  将数据分布在一个集群的机器上.
* MongoDB 3.4 支持根据 [shard key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key) 创建
  数据[区域 (zones)](https://docs.mongodb.com/manual/core/zone-sharding/#zone-sharding). 在一个均衡的集群中, MongoDB 根据分区直接读写区域中的节点. 阅读
  [区域](https://docs.mongodb.com/manual/core/zone-sharding/#zone-sharding)
  手册了解更多.

### 支持多种存储引擎

MongoDB 支持[多个存储引擎](https://docs.mongodb.com/manual/core/storage-engines/), 如:

* [WiredTiger 存储引擎](https://docs.mongodb.com/manual/core/wiredtiger/)
  and
* [MMAPv1 存储引擎](https://docs.mongodb.com/manual/core/mmapv1/)
  .

此外, MongoDB 还提供了允许第三方的人员为 MongoDB 开发的插件 API.

