# Getting Started

[MongoDB Atlas](https://cloud.mongodb.com/?jmp=docs) 是一个定制, 运行, 监控以及维护 MongoDB 的云端服务. 它是一个快速, 简单并且免费的使用 MongoDB 的途径. 如果想在本地安装运行 MongoDB, 参见[安装 MongoDB](https://docs.mongodb.com/manual/installation/#tutorial-installation).

接下来的教程使用 [MongoDB Node.js 驱动](http://mongodb.github.io/node-mongodb-native/2.2/) 来连接一个免费的 Atlas 集群, 然后执行插入和查询操作.

想学习更多关于 MongoDB 查询语句一起其他 MongoDB 的基础支持, 可以注册 [M001: MongoDB Basics](https://university.mongodb.com/courses/M001/about) 课程.

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

2. Click the Select button for Instance Size`M0`for Atlas Free Tier.

3. Enter aUsernameandPassword.

   ![](https://docs.mongodb.com/manual/_images/atlas-create-cluster-add-user.png "Screenshot of admin username and password fields that appear only when admin user doesn&apos;t exist.")  
   These fields appear only if no MongoDB user exists for your Atlas group. If you created users in the group beforehand these fields will not appear.

4. ClickConfirm & Deploy.

## Setup security.

Atlas only allows client connections to the cluster from entries in the group’s whitelist. To add an entry to the whitelist:

![](https://docs.mongodb.com/manual/_images/atlas-setup-cluster-security.png "Screeenshot of cluster security tab on Atlas.")

1. Click the Security tab from the Clusters view.
2. Click IP Whitelist, then Add IP Address. You may either enter the IP address manually in the
   Whitelist Entry field or click the Add Current IP Address button.
3. Click Confirm and wait for Atlas to update the firewall.

## Download the connecting client.

First create your project using`npminit`then install the[MongoDB Node.js Driver](http://mongodb.github.io/node-mongodb-native/2.2/)via`npm`:

Copy

```
npm install mongodb --save
```

For more information, see the Node.js driver[Quick Start](http://mongodb.github.io/node-mongodb-native/2.2/installation-guide/installation-guide/)documentation.

## Get the URI connection string.

From the MongoDB AtlasClustersview:

1. Click the Connect button to view the [URI Connection String](https://docs.mongodb.com/manual/reference/connection-string/#mongodb-uri).
2. Copy the connection string to the clipboard.
3. Paste the connection string somewhere and replace `<PASSWORD>` with the password for the user created earlier, and
   `<DATABASE>` with the name of the database to which you wish to connect. In the following examples, the `test` database is used.
4. Copy the modified URI to the clipboard.

## Connect to the cluster.

Using the**URI string**from the previous step, connect to the Atlas cluster:

```js
var MongoClient = require('mongodb').MongoClient;

var uri = "mongodb://user123:p455w0rd@gettingstarted-shard-00-00-hyjsm.mongodb.net:27017,gettingstarted-shard-00-01-hyjsm.mongodb.net:27017,gettingstarted-shard-00-02-hyjsm.mongodb.net:27017/test?ssl=true&replicaSet=GettingStarted-shard-0&authSource=admin";
MongoClient.connect(uri, function(err, db) {
  // Paste the following examples here

  db.close();
});
```

## Documents and Collections

MongoDB stores data as[BSON documents](https://docs.mongodb.com/manual/core/document/#bson-document-format)\(binary represenatation of JSON\) in[collections](https://docs.mongodb.com/manual/reference/glossary/#term-collection). MongoDB databases hold collections of documents.

## Insert Documents

[Collection.insertMany\(\)](http://mongodb.github.io/node-mongodb-native/2.2/api/Collection.html#insertMany)can insert_multiple_[documents](https://docs.mongodb.com/manual/core/document/#bson-document-format)into a collection. Pass an array of documents to the method.

The following example inserts new documents into the`inventory`collection:

Copy

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

[insertMany\(\)](http://mongodb.github.io/node-mongodb-native/2.2/api/Collection.html#insertMany)returns a document that includes the newly inserted documents`_id`field values. See the[reference](https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/#insertmany-examples)for an example.

Use[Collection.insertOne\(\)](http://mongodb.github.io/node-mongodb-native/2.2/api/Collection.html#insertOne)to insert a single document.

For more information and examples, see[Insert Documents](https://docs.mongodb.com/manual/tutorial/insert-documents/#write-op-insert)in the[CRUD](https://docs.mongodb.com/manual/crud/#crud)section.

## Query Documents

### Select All Documents

To select all documents in the collection, pass an empty document as the[query filter document](https://docs.mongodb.com/manual/core/document/#document-query-filter)to the[Collection.find\(\)](http://mongodb.github.io/node-mongodb-native/2.2/api/Collection.html#find)method:

Copy

```js
var cursor = db.collection('inventory').find({});
```

To query for documents that match specific equality conditions, pass the[find\(\)](http://mongodb.github.io/node-mongodb-native/2.2/api/Collection.html#find)method a[query filter document](https://docs.mongodb.com/manual/core/document/#document-query-filter)with the`<field>:<value>`of the desired documents. The following example selects from the`inventory`collection all documents where the`status`equals`"D"`:

Copy

```js
var cursor = db.collection('inventory').find({ status: "D" });
```

### Match an Embedded Document

Equality matches on the whole embedded document require an\_exact\_match of the specified`<value>`document, including the field order. For example, the following query selects all documents where the field`size`equals the document`{h:14,w:21,uom:"cm"}`:

Copy

```js
var cursor = db.collection('inventory').find({ 
  size: { h: 14, w: 21, uom: "cm" }
});
```

### Match a Field in an Embedded Document

The following example selects all documents where the field`uom`nested in the`size`field equals the string value`"in"`:

Copy

```js
var cursor = db.collection('inventory').find({ 
  "size.uom": "in"
});
```

### Match an Element in an Array

The following example queries for all documents where`tags`is an array that contains the string`"red"`as one of its elements:

Copy

```js
var cursor = db.collection('inventory').find({ 
  tags: "red"
});
```

### Match an Array Exactly

The following example queries for all documents where the field`tags`value is an array with exactly two elements,`"red"`and`"blank"`, in the specified order:

Copy

```js
var cursor = db.collection('inventory').find({ 
  tags: [ "red", "blank" ]
});
```

For more information and query examples, see[Query Documents](https://docs.mongodb.com/manual/tutorial/query-documents/#read-operations-queries)in the[CRUD](https://docs.mongodb.com/manual/crud/#crud)section.

To update or delete documents, see[Update Documents](https://docs.mongodb.com/manual/tutorial/update-documents/#write-op-update)and[Delete Documents](https://docs.mongodb.com/manual/tutorial/remove-documents/#write-op-delete).

## Next Steps

Once you complete the Getting Started Guide, you may find the following course and topics useful:

To learn more about the MongoDB query language and other MongoDB fundamentals, sign up for[M001: MongoDB Basics](https://university.mongodb.com/courses/M001/about).

| Introduction | Developers | Administrators | Reference |
| :--- | :--- | :--- | :--- |
| [Introduction to MongoDB](https://docs.mongodb.com/manual/introduction/)[Installation Guides](https://docs.mongodb.com/manual/installation/)[Databases and Collections](https://docs.mongodb.com/manual/core/databases-and-collections/)[Documents](https://docs.mongodb.com/manual/core/document/) | [CRUD Operations](https://docs.mongodb.com/manual/crud/)[Aggregation](https://docs.mongodb.com/manual/aggregation/)[SQL to MongoDB](https://docs.mongodb.com/manual/reference/sql-comparison/)[Indexes](https://docs.mongodb.com/manual/indexes/) | [Production Notes](https://docs.mongodb.com/manual/administration/production-notes/)[Replica Sets](https://docs.mongodb.com/manual/replication/)[Sharded Clusters](https://docs.mongodb.com/manual/sharding/)[MongoDB Security](https://docs.mongodb.com/manual/security/) | [Shell Methods](https://docs.mongodb.com/manual/reference/method/)[Query Operators](https://docs.mongodb.com/manual/reference/operator/)[Reference](https://docs.mongodb.com/manual/reference/)[Glossary](https://docs.mongodb.com/manual/reference/glossary/) |



