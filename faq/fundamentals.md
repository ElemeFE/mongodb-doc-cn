# FAQ: MongoDB Fundamentals

On this page

* [What platforms does MongoDB support?](https://docs.mongodb.com/manual/faq/fundamentals/#what-platforms-does-mongodb-support)
* [Is MongoDB offered as a hosted service?](https://docs.mongodb.com/manual/faq/fundamentals/#is-mongodb-offered-as-a-hosted-service)
* [How does a collection differ from a table?](https://docs.mongodb.com/manual/faq/fundamentals/#how-does-a-collection-differ-from-a-table)
* [How do I create a database and a collection?](https://docs.mongodb.com/manual/faq/fundamentals/#how-do-i-create-a-database-and-a-collection)
* [How do I define or alter the collection schema?](https://docs.mongodb.com/manual/faq/fundamentals/#how-do-i-define-or-alter-the-collection-schema)
* [Does MongoDB support SQL?](https://docs.mongodb.com/manual/faq/fundamentals/#does-mongodb-support-sql)
* [Does MongoDB support transactions?](https://docs.mongodb.com/manual/faq/fundamentals/#does-mongodb-support-transactions)
* [Does MongoDB handle caching?](https://docs.mongodb.com/manual/faq/fundamentals/#does-mongodb-handle-caching)
* [How does MongoDB address SQL or Query injection?](https://docs.mongodb.com/manual/faq/fundamentals/#how-does-mongodb-address-sql-or-query-injection)

This document answers some common questions about MongoDB.

## What platforms does MongoDB support?

For the list of supported platforms, see[Supported Platforms](https://docs.mongodb.com/manual/administration/production-notes/#prod-notes-supported-platforms).

## Is MongoDB offered as a hosted service?

Yes.[MongoDB Atlas](https://www.mongodb.com/cloud/atlas?jmp=docs)is a cloud-hosted database-as-a-service. For more information, please visit the[MongoDB Atlas docs](https://www.mongodb.com/cloud/atlas?jmp=docs).

## How does a collection differ from a table?

Instead of tables, a MongoDB database stores its data in[collections](https://docs.mongodb.com/manual/reference/glossary/#term-collection). A collection holds one or more[BSON documents](https://docs.mongodb.com/manual/core/document/#bson-document-format). Documents are analogous to records or rows in a relational database table. Each document has[one or more fields](https://docs.mongodb.com/manual/core/document/#document-structure); fields are similar to the columns in a relational database table.

SEE ALSO

[SQL to MongoDB Mapping Chart](https://docs.mongodb.com/manual/reference/sql-comparison/),[Introduction to MongoDB](https://docs.mongodb.com/manual/introduction/)

## How do I create a database and a collection?

If a database does not exist, MongoDB creates the database when you first store data for that database.

If a collection does not exist, MongoDB creates the collection when you first store data for that collection.[\[1\]](https://docs.mongodb.com/manual/faq/fundamentals/#explicit-creation)

As such, you can switch to a non-existent database \(`use<dbname>`\) and perform the following operation:

```
use myNewDB

db.myNewCollection1.insertOne( { x: 1 } )
db.myNewCollection2.createIndex( { a: 1 } )
```

The`insert`operation creates both the database`myNewDB`and the collection`myNewCollection1`if they do not already exist.

The`createIndex`operation, which occurs after the`myNewDB`has been created, creates the index and the collection`myNewCollection2`if the collection does not exist. If`myNewDb`did not exist, the`createIndex`operation would have also created the`myNewDB`.

| [\[1\]](https://docs.mongodb.com/manual/faq/fundamentals/#id1) | You can also create a collection explicitly using[`db.createCollection`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)if you want to specify specific options, such as maximum size or document validation rules. |
| :--- | :--- |


## How do I define or alter the collection schema?

You do not need to specify a schema for a collection in MongoDB. Although it is common for the documents in a collection to have a largely homogeneous structure, it is not a requirement; i.e. documents in a single collection do not need to have the same set of fields. The data type for a field can differ across documents in a collection as well.

To change the structure of the documents in a collection, update the documents to the new structure. For instance, add new fields, remove existing ones, or update the value of a field to a new type.

Changed in version 3.2:Starting in MongoDB 3.2, however, you can enforce[document validation rules](https://docs.mongodb.com/manual/core/document-validation/)for a collection during update and insert operations.

Some collection properties, such as specifying a maximum size, can be specified during the explicit creation of a collection and be modified. See[`db.createCollection`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)and[`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod). If you are not specifying these properties, you do not need to explicitly create the collection since MongoDB creates new collections when you first store data for the collections.

## Does MongoDB support SQL?

No. However, MongoDB does support a rich query language of its own. For examples on using MongoDB’s query language, see[MongoDB CRUD Operations](https://docs.mongodb.com/manual/crud/)

SEE ALSO

[SQL to MongoDB Mapping Chart](https://docs.mongodb.com/manual/reference/sql-comparison/)

## Does MongoDB support transactions?

MongoDB does not support multi-document transactions. However, MongoDB does provide atomic operations on a single document.

For more details on MongoDB’s isolation guarantees and behavior under concurrency, see[FAQ: Concurrency](https://docs.mongodb.com/manual/faq/concurrency/).

## Does MongoDB handle caching?

Yes. MongoDB keeps most recently used data in RAM. If you have created indexes for your queries and your working data set fits in RAM, MongoDB serves all queries from memory.

MongoDB does not cache the query results in order to return the cached results for identical queries.

For more information on MongoDB and memory use, see[WiredTiger and Memory Use](https://docs.mongodb.com/manual/core/wiredtiger/#wiredtiger-ram)and[MMAPv1 and Memory Use](https://docs.mongodb.com/manual/core/mmapv1/#mmapv1-ram).

## How does MongoDB address SQL or Query injection?

### BSON

As a client program assembles a query in MongoDB, it builds a BSON object, not a string. Thus traditional SQL injection attacks are not a problem. More details and some nuances are covered below.

MongoDB represents queries as[BSON](https://docs.mongodb.com/manual/reference/glossary/#term-bson)objects. Typically[client libraries](https://docs.mongodb.com/manual/applications/drivers/)provide a convenient, injection free, process to build these objects. Consider the following C++ example:

```
BSONObj my_query = BSON( "name" << a_name );
auto_ptr<DBClientCursor> cursor = c.query("tutorial.persons", my_query);
```

Here,`my_query`then will have a value such as`{name:"Joe"}`. If`my_query`contained special characters, for example`,`,`:`, and`{`, the query simply wouldn’t match any documents. For example, users cannot hijack a query and convert it to a delete.

### JavaScript

NOTE

You can disable all server-side execution of JavaScript, by passing the`--noscripting`option on the command line or setting[`security.javascriptEnabled`](https://docs.mongodb.com/manual/reference/configuration-options/#security.javascriptEnabled)in a configuration file.

All of the following MongoDB operations permit you to run arbitrary JavaScript expressions directly on the server:

* [`$where`](https://docs.mongodb.com/manual/reference/operator/query/where/#op._S_where)
* [`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce)
* [`group`](https://docs.mongodb.com/manual/reference/command/group/#dbcmd.group)

You must exercise care in these cases to prevent users from submitting malicious JavaScript.

Fortunately, you can express most queries in MongoDB without JavaScript and for queries that require JavaScript, you can mix JavaScript and non-JavaScript in a single query. Place all the user-supplied fields directly in a[BSON](https://docs.mongodb.com/manual/reference/glossary/#term-bson)field and pass JavaScript code to the[`$where`](https://docs.mongodb.com/manual/reference/operator/query/where/#op._S_where)field.

If you need to pass user-supplied values in a[`$where`](https://docs.mongodb.com/manual/reference/operator/query/where/#op._S_where)clause, you may escape these values with the`CodeWScope`mechanism. When you set user-submitted values as variables in the scope document, you can avoid evaluating them on the database server.

