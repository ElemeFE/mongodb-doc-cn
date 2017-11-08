# 封顶集合

本文索引

* [概述](#概述)
* [表现](#表现)
* [限制与推荐](#限制与推荐)
* [使用](#使用)

## 概述

封顶集合 [capped collection](https://docs.mongodb.com/manual/reference/glossary/#term-capped-collection) 是固定大小的集合, 支持高吞吐的插入操作和根据插入顺序的查询操作. 封顶集合的工作方式与循环缓冲区 (circular buffers) 类似: 当一个集合填满了被分配的空间, 则通过覆盖最早的文档来为新的文档腾出空间.

参阅 [`createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection) 或 [`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create) 获取更多创建封顶集合的信息.

## 表现

### 顺序插入

封顶集合保证了插入的顺序. 因此, 历史查询不需要索引排序. 没有这种索引开销, 封顶集合可以支持更高的插入吞吐量.

### 自动删除最早的文档

为了给新的文档腾出空间, 封顶集合会自动删除集合中最早的文档, 不需要定时脚本或者显示的删除操作.

例如, 在 [oplog.rs](https://docs.mongodb.com/manual/reference/glossary/#term-oplog) 集合中存储了 [replica set](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set) 的操作的日志, 该集合就使用的是封顶集合. 除此之外, 还可以考虑以下潜在的用例:

* 存储由高容量 (high-volume) 系统生成的日志信息. 在封顶集合中不用索引插入文档的速度接近直接输出日志到文件系统. 而且, 内置的 _先进先出_ 的属性维护了事件的顺序, 这在管理存储时用得到 (译注: 有些日志存储系统的顺序可能会乱, 如 Elasticsearch).
* 在封顶集合中记性数据缓存 (少量的). 由于缓存是高频读很少写, 因此你需要确保集合 _始终_ 保留在工作区间 \(即 RAM 中\) _或者_ 接受一些使用索引带来的写入的成本 (or accept some write penalty for the required index or indexes
).

### `_id` 索引

封顶集合有 `_id` 字段并且有一个基于 `_id` 字段的默认索引.

## 限制与推荐

### 更新

如果您计划更新封顶集合中的文档, 请创建一个索引, 来避免更新操进行集合扫描.

### 文档大小

在 3.2 版本中修改.

更新或替换文档大小的操作会失败. (注: 之前的 MMAPv1 可以修改)

### 文档删除

你不能删除封顶集合中的文档. 要删除集合中的所有文档, 请使用 [`drop()`](https://docs.mongodb.com/manual/reference/method/db.collection.drop/#db.collection.drop) 方法删除集合, 并重新创建封顶的集合.

### 分片

你不能对封顶集合进行分片操作.

### 查询效率

使用自然顺序 (natural ordering) 来有效地检索集合最近插入的元素. 这 \(有点\) 类似 `tail` 一个日志文件 (查看他的尾部).

### 聚合 `$out`

聚合管道操作符 [`$out`](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out)不能将结果写入封顶集合.

## 使用

### 创建封顶集合

When creating a capped collection you must specify the maximum size of the collection in bytes, which MongoDB will pre-allocate for the collection. The size of the capped collection includes a small amount of space for internal overhead.

您必须使用 [`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection) 方法显式地创建封顶集合, 该过程可以通过 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell 来帮忙执行 [`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create) 命令. 在创建封顶集合时, 您必须预先指定集合的最大容量 (以字节为单位). 其中包括少量的内部空间. 

```
db.createCollection( "log", { capped: true, size: 100000 } )
```

如果 `size` 字段小于或等于 4096, 则该集合将具有 4096 字节的容量. 此外, MongoDB 会提升用户所提供给的 size 大小直到其满足 256 的倍数为止.

Additionally, you may also specify a maximum number of documents for the collection using the`max`field as in the following document:

```
db.createCollection("log", { capped : true, size : 5242880, max : 5000 } )
```

> IMPORTANT
>
> The`size`argument is_always_required, even when you specify`max`number of documents. MongoDB will remove older documents if a collection reaches the maximum size limit before it reaches the maximum document count.
>

SEE

[`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)and[`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create).

### 封顶集合查询

If you perform a[`find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)on a capped collection with no ordering specified, MongoDB guarantees that the ordering of results is the same as the insertion order.

To retrieve documents in reverse insertion order, issue[`find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)along with the[`sort()`](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)method with the[`$natural`](https://docs.mongodb.com/manual/reference/operator/meta/natural/#metaOp._S_natural)parameter set to`-1`, as shown in the following example:

```
db.cappedCollection.find().sort( { $natural: -1 } )
```

### 检查集合是否封顶

Use the[`isCapped()`](https://docs.mongodb.com/manual/reference/method/db.collection.isCapped/#db.collection.isCapped)method to determine if a collection is capped, as follows:

```
db.collection.isCapped()
```

### 集合转换为封顶集合

You can convert a non-capped collection to a capped collection with the[`convertToCapped`](https://docs.mongodb.com/manual/reference/command/convertToCapped/#dbcmd.convertToCapped)command:

```
db.runCommand({"convertToCapped": "mycoll", size: 100000});
```

The`size`parameter specifies the size of the capped collection in bytes.

> WARNING
>
> This command obtains a global write lock and will block other operations until it has completed.
>

### Automatically Remove Data After a Specified Period of Time

As an alternative to 封顶集合, consider MongoDB’s[TTL](https://docs.mongodb.com/manual/reference/glossary/#term-ttl)\(“_time to live_”\) indexes. As described in[Expire Data from Collections by Setting TTL](https://docs.mongodb.com/manual/tutorial/expire-data/), these indexes allow you to expire and remove data from normal collections based on the value of a date-typed field and a TTL value for the index.

> IMPORTANT
>
> [TTL indexes](https://docs.mongodb.com/manual/tutorial/expire-data/)are not compatible with 封顶集合.
>

### Tailable Cursor

You can use a[tailable cursor](https://docs.mongodb.com/manual/reference/glossary/#term-tailable-cursor)with 封顶集合. Similar to the Unix`tail-f`command, the tailable cursor “tails” the end of a capped collection. As new documents are inserted into the capped collection, you can use the tailable cursor to continue retrieving documents.

See[Tailable Cursors](https://docs.mongodb.com/manual/core/tailable-cursors/)for information on creating a tailable cursor.

