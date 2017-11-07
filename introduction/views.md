# 视图

_3.4 版本新功能_

本页索引

* [创建视图](https://docs.mongodb.com/manual/core/views/#创建视图)
* [表现](https://docs.mongodb.com/manual/core/views/#表现)
* [删除视图](https://docs.mongodb.com/manual/core/views/#删除视图)
* [修改视图](https://docs.mongodb.com/manual/core/views/#修改视图)
* [支持操作](https://docs.mongodb.com/manual/core/views/#支持操作)

从 3.4 开始, MongoDB 添加了基于已存在的集合或者 View (视图) 创建只读的 View 支持.

## 创建视图

创建或者定义一个视图, MongoDB 3.4 的介绍是:

* the `viewOn` and `pipeline`options to the existing[`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create)command \(and[`db.createCollection`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)helper\):

  ```
  db.runCommand( { create: <view>, viewOn: <source>, pipeline: <pipeline> } )
  ```

  or if specifying a default[collation](https://docs.mongodb.com/manual/release-notes/3.4/#relnotes-collation)for the view:

  ```
  db.runCommand( { create: <view>, viewOn: <source>, pipeline: <pipeline>, collation: <collation> } )
  ```

* a new[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell helper[`db.createView()`](https://docs.mongodb.com/manual/reference/method/db.createView/#db.createView):

  ```
  db.createView(<view>, <source>, <pipeline>, <collation> )
  ```

## 表现

视图具备以下几种表现:

### 只读

视图是只读的; 通过视图进行写操作会报错.

以下为支持视图的读操作:

* [`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)
* [`db.collection.findOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.findOne/#db.collection.findOne)
* [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate)
* [`db.collection.count()`](https://docs.mongodb.com/manual/reference/method/db.collection.count/#db.collection.count)
* [`db.collection.distinct()`](https://docs.mongodb.com/manual/reference/method/db.collection.distinct/#db.collection.distinct)

### 索引使用 & 排序操作

* 视图使用其上游集合的索引.

* 由于索引是基于集合的, 所以你不能基于视图创建, 删除或重建索引, 也不能获取视图的索引列表.

* 你不能指定 [`$natural`](https://docs.mongodb.com/manual/reference/operator/meta/natural/#metaOp._S_natural) 排序.

  例如, 下列操作是 _错误的_:

  ```
  db.view.find().sort({$natural: 1})
  ```

### Project 限制

视图上的 [`find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) 方法不支持如下[projection](https://docs.mongodb.com/manual/reference/operator/projection/) 操作:

* [`$`](https://docs.mongodb.com/manual/reference/operator/projection/positional/#proj._S_)
* [`$elemMatch`](https://docs.mongodb.com/manual/reference/operator/projection/elemMatch/#proj._S_elemMatch)
* [`$slice`](https://docs.mongodb.com/manual/reference/operator/projection/slice/#proj._S_slice)
* [`$meta`](https://docs.mongodb.com/manual/reference/operator/projection/meta/#proj._S_meta)

### 不能改变名称

你不能重命名[视图](#).

### 视图创建

* 视图是在读操邹期间根据需要实时计算的, 同时 MongoDB 基于视图的读操作是底层聚合管道 (aggregation pipeline) 的一部分. 因此, 视图不支持一下操作:
  * [`db.collection.mapReduce()`](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce),
  * [`$text`](https://docs.mongodb.com/manual/reference/operator/query/text/#op._S_text) 操作, 因为 `$text` 只在聚合的第一阶段有效,
  * [`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear) 命令和 [`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear)
    管道阶段.
* 如果用于创建视图的聚合管道屏蔽了 `_id` 字段, 那么视图中的文档也会没有 `_id` 字段.

### 分片视图

如果视图依赖的集合是分片的, 那么视图也视为分片的. 因此, 你不能指定分片视图中 [`$lookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#pipe._S_lookup) 的 `from`字段与 [`$graphLookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/graphLookup/#pipe._S_graphLookup) 操作.

### Views 与 Collation

* You can specify a default [collation](https://docs.mongodb.com/manual/reference/collation/) for a view at creation time. If no collation is specified, the view’s default collation is the “simple” binary comparison collator. That is, the view does not inherit the collection’s default collation.
* String comparisons on the view use the view’s default collation. An operation that attempts to change or override a view’s default collation will fail with an error.
* If creating a view from another view, you cannot specify a collation that differs from the source view’s collation.
* If performing an aggregation that involves multiple views, such as with [`$lookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#pipe._S_lookup) or [`$graphLookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/graphLookup/#pipe._S_graphLookup) , the views must have the same [collation](https://docs.mongodb.com/manual/reference/collation/).

### 公共视图定义

列出集合的操作, 如 [`db.getCollectionInfos()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionInfos/#db.getCollectionInfos) 和 [`db.getCollectionNames()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionNames/#db.getCollectionNames), 的结果中会包括它们的视图.

> IMPORTANT
>
> 视图定义是公开的; 即在视图上的 [`db.getCollectionInfos()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionInfos/#db.getCollectionInfos) 和 `explain` 操作将会包括定义视图的管道. 因此, 请避免直接引用视图定义中敏感的字段和值.
>

## 删除视图

要删除视图, 请使用视图上的 [`db.collection.drop()`](https://docs.mongodb.com/manual/reference/method/db.collection.drop/#db.collection.drop) 方法.

## 修改视图

你可以通过删除或者重建的方式修改视图, 也可以使用 [`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod) 命令.

## 支持操作

以下操作支持视图, 除了本文中提到的限制除外:

| 命令 | 方法 |
| :--- | :--- |
| [`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create) | [`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection) <br> [`db.createView()`](https://docs.mongodb.com/manual/reference/method/db.createView/#db.createView) |
| [`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod) |  |
|  | [`db.getCollection()`](https://docs.mongodb.com/manual/reference/method/db.getCollection/#db.getCollection) <br> [`db.getCollectionInfos()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionInfos/#db.getCollectionInfos) <br> [`db.getCollectionNames()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionNames/#db.getCollectionNames) |
| [`find`](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find) <br> [`distinct`](https://docs.mongodb.com/manual/reference/command/distinct/#dbcmd.distinct) <br> [`count`](https://docs.mongodb.com/manual/reference/command/count/#dbcmd.count) | [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate) <br> [`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) <br> [`db.collection.findOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.findOne/#db.collection.findOne) <br> [`db.collection.count()`](https://docs.mongodb.com/manual/reference/method/db.collection.count/#db.collection.count) <br> [`db.collection.distinct()`](https://docs.mongodb.com/manual/reference/method/db.collection.distinct/#db.collection.distinct) |



