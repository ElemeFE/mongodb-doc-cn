# Getting Started

[MongoDB Atlas](https://cloud.mongodb.com/?jmp=docs) 是一个定制, 运行, 监控以及维护 MongoDB 的云端服务. 它是一个快速, 简单并且免费的使用 MongoDB 的途径. 如果想在本地安装运行 MongoDB, 参见[安装 MongoDB](https://docs.mongodb.com/manual/installation/#tutorial-installation).

接下来的教程使用 [MongoDB Node.js 驱动](http://mongodb.github.io/node-mongodb-native/2.2/) 来连接一个免费的 Atlas 集群, 然后执行插入和查询操作.

## 1.创建一个 Atlas 账户.

想要使用 [MongoDB Atlas](https://cloud.mongodb.com/?jmp=docs), 请先创建你的账户并登陆到 Atlas.

## 2.创建一个 Atlas Group.

为你的 group 取个名字.

![](https://docs.mongodb.com/manual/_images/atlas-create-group.png "Screenshot of group creation prompt.")

你可以通过 gourp 来管理相关联的 MongoDB 实例. 要创建另外的 Atlas group, 请点击左上角的用户名然后选择 **My Groups**. 点击 Add Group 按钮即可.

## 3.创建一个集群.

在 Atlas 中部署一个 MongoDB 实例或者 “clusters”, 可以是一个 [replica set](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set) 或者 [sharded cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster).

要创建集群, 进入 **Clusters** 界面并点击 **Add New Cluster** 或者 **Build a New Cluster** 按钮.

![](https://docs.mongodb.com/manual/_images/atlas-create-cluster.png "Screenshot of cluster config options during creation.")

1. 输入 Cluster Name.

2. 找到 M0 级免费教学实例, 并点击对应的 Select 按钮.

3. 输入 Username (用户名) 和 Password (密码).

   ![](https://docs.mongodb.com/manual/_images/atlas-create-cluster-add-user.png "Screenshot of admin username and password fields that appear only when admin user doesn&apos;t exist.")  
   These fields appear only if no MongoDB user exists for your Atlas group. If you created users in the group beforehand these fields will not appear.

4. 点击 Confirm & Deploy (确认部署).

## 4.安全设置

Atlas 只允许 group 入口白名单中的客户端连接集群. 在白名单中添加一个入口:

![](https://docs.mongodb.com/manual/_images/atlas-setup-cluster-security.png "Screeenshot of cluster security tab on Atlas.")

1. 在 Clusters 页面点击 Security 选项.
2. 点击 IP Whitelist, 然后添加 IP Address. 你可以在 Whitelist Entry 中手动输入 IP 地址, 也可以点击 Add Current IP Address 按钮.
3. 点击 Confirm 然后等待 Atlas 更新防火墙.

## 5.下载驱动客户端.

推荐通过 `npm init` 初始化一个项目的配置文件, 然后用 `npm` 安装 [MongoDB Node.js 驱动](http://mongodb.github.io/node-mongodb-native/2.2/):

```
npm install mongodb --save
```

需要更多信息, 请查看 Node.js 驱动[快速开始](http://mongodb.github.io/node-mongodb-native/2.2/installation-guide/installation-guide/)文档.

## 6.获取 URI 连接串.

通过 MongoDB Atlas Clusters 页面:

1. 点击 Connect 按钮来查看 [URI 连接字符串](https://docs.mongodb.com/manual/reference/connection-string/#mongodb-uri).
2. 复制连接字符串.
3. 将连接串中的 `<PASSWORD>` 替换成之前创建时设置的密码, 并且替换
   `<DATABASE>` 为你要连接/简历的数据库名. 下文中使用 `test` 数据库.
4. 复制修改好的 URI.

## 7.连接到集群

使用上一步获取的 **URI 字符串**, 连接 Atlas 集群:

```js
var MongoClient = require('mongodb').MongoClient;

var uri = "mongodb://user123:p455w0rd@gettingstarted-shard-00-00-hyjsm.mongodb.net:27017,gettingstarted-shard-00-01-hyjsm.mongodb.net:27017,gettingstarted-shard-00-02-hyjsm.mongodb.net:27017/test?ssl=true&replicaSet=GettingStarted-shard-0&authSource=admin";
MongoClient.connect(uri, function(err, db) {
  // Paste the following examples here

  db.close();
});
```

## 文档 & 集合

MongoDB 在 [collections](https://docs.mongodb.com/manual/reference/glossary/#term-collection) 中使用 [BSON 结构](https://docs.mongodb.com/manual/core/document/#bson-document-format)\(二进制格式的 JSON\) 来存储数据. MongoDB 中一个数据库对应多个集合, 一个集合对应多个文档.

## 插入文档

[Collection.insertMany\(\)](http://mongodb.github.io/node-mongodb-native/2.2/api/Collection.html#insertMany) 可以用来插入 _多个_ [文档](https://docs.mongodb.com/manual/core/document/#bson-document-format). 传递一个包含文档的给这个方法.

如下代码插入了新的文档到 `inventory` 集合中:


```js
db.collection('inventory').insertMany([
   // MongoDB adds the _id field with an ObjectId if _id is not present
   { item: "journal", qty: 25, status: "A",
       size: { h: 14, w: 21, uom: "cm" }, tags: [ "blank", "red" ] },
   { item: "notebook", qty: 50, status: "A",
       size: { h: 8.5, w: 11, uom: "in" }, tags: [ "red", "blank" ] },
   { item: "paper", qty: 100, status: "D",
       size: { h: 8.5, w: 11, uom: "in" }, tags: [ "red", "blank", "plain" ] },
   { item: "planner", qty: 75, status: "D",
       size: { h: 22.85, w: 30, uom: "cm" }, tags: [ "blank", "red" ] },
   { item: "postcard", qty: 45, status: "A",
       size: { h: 10, w: 15.25, uom: "cm" }, tags: [ "blue" ] }
])
.then(function(result) {
  // process result
})
```

[insertMany\(\)](http://mongodb.github.io/node-mongodb-native/2.2/api/Collection.html#insertMany) 返回一个包含被插入文档的 `_id` 的数据. 可以查看[这里](https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/#insertmany-examples)的例子.

使用 [Collection.insertOne\(\)](http://mongodb.github.io/node-mongodb-native/2.2/api/Collection.html#insertOne) 来插入单个文档.

想要更多的文档和例子, 可以参见[增删改查](/crud/)章节的[插入](/crud/insert.html).

## Query

### 查询所有

要查询 (select) 所有集合中的文档, 给 [Collection.find\(\)](http://mongodb.github.io/node-mongodb-native/2.2/api/Collection.html#find) 方法传一个空的文档作为[查询过滤](https://docs.mongodb.com/manual/core/document/#document-query-filter):


```js
var cursor = db.collection('inventory').find({});
```

要查询匹配指定条件的文档, 可以使用 [find\(\)](http://mongodb.github.io/node-mongodb-native/2.2/api/Collection.html#find) 方法, 并传递 `<字段>:<值>` 格式的[过滤](https://docs.mongodb.com/manual/core/document/#document-query-filter) 条件. 下例为查询 `inventory` 集合中所有的 `status` 为 `"D"` 的文档:


```js
var cursor = db.collection('inventory').find({ status: "D" });
```

### 匹配嵌套内容

相等的匹配在匹配整个嵌套结构的文档时需要 _确切_ 地指定 `<value>`, 包括字段的顺序. 举个栗子, 下列语句可以查询所有 `size` 字段等于 `{h:14,w:21,uom:"cm"}` 的文档:


```js
var cursor = db.collection('inventory').find({ 
  size: { h: 14, w: 21, uom: "cm" }
});
```

### 匹配嵌套文档中的一个字段

查询所有嵌套在 `size` 字段下的 `uom` 字段等于 `"in"` 的文档:

```js
var cursor = db.collection('inventory').find({ 
  "size.uom": "in"
});
```

### 匹配数组中的一个元素

查询 `tags` 是一个数组并且其中包含 `"red"` 的所有文档:

```js
var cursor = db.collection('inventory').find({ 
  tags: "red"
});
```

### 精确匹配一个数组

查询 `tags` 字段的值为一个包含 `"red"` 和 `"blank"` 并且顺序确定的数组的所有文档:


```js
var cursor = db.collection('inventory').find({ 
  tags: [ "red", "blank" ]
});
```

更多信息以及查询例子, 参见[增删改查](https://docs.mongodb.com/manual/crud/#crud)章节的[查询](https://docs.mongodb.com/manual/tutorial/query-documents/#read-operations-queries).

关于更新和删除文档, 参见[Update Documents](https://docs.mongodb.com/manual/tutorial/update-documents/#write-op-update)和[Delete Documents](https://docs.mongodb.com/manual/tutorial/remove-documents/#write-op-delete).

## 下一步

当你阅读完 Getting Started 概览之后, 你可以通过下列课程和主题获得更多帮助:

想学习更多关于 MongoDB 查询语句和其他 MongoDB 基础知识, 可以注册 [M001: MongoDB Basics](https://university.mongodb.com/courses/M001/about) 课程.

| Introduction | Developers | Administrators | Reference |
| :--- | :--- | :--- | :--- |
| [MongoDB 简介](/introduction/)<br>[安装指南](https://docs.mongodb.com/manual/installation/)<br>[数据库 & 集合](/introduction/db-and-collections.html)<br>[文档](/introduction/document.html) | [增删改查](http://localhost:4000/crud/)<br>[聚合](http://localhost:4000/aggregation/)<br>[SQL 转 MongoDB](https://docs.mongodb.com/manual/reference/sql-comparison/)<br>[索引](https://docs.mongodb.com/manual/indexes/) | [Production Notes](https://docs.mongodb.com/manual/administration/production-notes/)<br>[复制集](https://docs.mongodb.com/manual/replication/)<br>[Sharded 集群](https://docs.mongodb.com/manual/sharding/)<br>[MongoDB 安全](https://docs.mongodb.com/manual/security/) | [Shell 方法](https://docs.mongodb.com/manual/reference/method/)<br>[查询操作符](https://docs.mongodb.com/manual/reference/operator/)<br>[Reference](https://docs.mongodb.com/manual/reference/)<br>[术语表](/reference/glossary.html) |



