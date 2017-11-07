# 数据库 & 集合

本页索引

* [数据库](#数据库)
* [集合](#集合)

MongoDB 在 [集合](https://docs.mongodb.com/manual/reference/glossary/#term-collection) 中存储 [BSON 文档](https://docs.mongodb.com/manual/core/document/#bson-document-format)作为数据记录. 数据库中的集合:

![](https://docs.mongodb.com/manual/_images/crud-annotated-collection.bakedsvg.svg "A collection of MongoDB documents.")

## 数据库

在 MongoDB 中一个数据库对应多个集合, 一个集合对应多个文档.

要找一个数据库来用, 可以在 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell 中使用 `use <db>` 语句, 如下例所示:

```bash
use myDB
```

### 创建数据库 

如果数据库不存在, MongoDB 在你第一次存储数据的时候默认帮你创建数据库. 例如, 你可以切换到一个不存在的数据库然后执行下例操作:

```js
use myNewDB

db.myNewCollection1.insertOne( { x: 1 } )
```

其中 [`insertOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne) 操作同时创建了名为 `myNewDB` 的数据库以及名为 `myNewCollection1` 的集合 (如果它们之前没有的话).

关于数据库名称的限制, 参阅 [Naming Restrictions](https://docs.mongodb.com/manual/reference/limits/#restrictions-on-db-names).

## 集合

MongoDB 将文档存储在集合中. 集合类似关系型数据库中表的概念.

### 创建集合

如果集合不存在, MongoDB 会在你第一次存储数据到集合时默认创建.

```js
db.myNewCollection2.insertOne( { x: 1 } )
db.myNewCollection3.createIndex( { y: 1 } )
```

不论是 [`insertOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne) 还是 [`createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex) 操作都可以创建它们各自的集合 (如果集合不存在的话).

关于集合名称的限制, 参阅 [Naming Restrictions](https://docs.mongodb.com/manual/reference/limits/#restrictions-on-collection-names).

### 显示创建

MongoDB 还提供了 [`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection) 方法来通过选项, 例如设置文档的最大大小或者验证规则, 显示地创建一个集合. 如果你不指定这些选项的花, 你其实不需要显示的创建集合, 毕竟 MongoDB 会在你第一次存储数据到集合时默认创建该集合.

如果要修改集合的选项, 参阅 [`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod).

### 文档验证

3.2 版本中的新功能.

默认情况下, 一个集合不需要它的文档都是统一的结构 (schema); 即单个集合中的文档不需要包含同样的字段集以及指定字段的数据类型.

从 MongoDB 3.2 开始, 你可以开启[文档验证规则](https://docs.mongodb.com/manual/core/document-validation/) 来确保集合的更新和插入操作. 更多参阅[文档验证](https://docs.mongodb.com/manual/core/document-validation/).

### 修改文档结构[¶](#修改文档结构)

修改一个集合中文档的结构, 例如添加新的字段, 删除存在的字段, 或者将字段的值修改为新的类型, 更新文档为新的结构等.

