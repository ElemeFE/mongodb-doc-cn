# MongoDB CRUD Operations

On this page

* [Create Operations](https://docs.mongodb.com/manual/crud/#create-operations)
* [Read Operations](https://docs.mongodb.com/manual/crud/#read-operations)
* [Update Operations](https://docs.mongodb.com/manual/crud/#update-operations)
* [Delete Operations](https://docs.mongodb.com/manual/crud/#delete-operations)
* [Bulk Write](https://docs.mongodb.com/manual/crud/#bulk-write)

CRUD operations_create_,_read_,_update_, and_delete_[documents](https://docs.mongodb.com/manual/core/document/#bson-document-format).

## Create Operations

Create or insert operations add new[documents](https://docs.mongodb.com/manual/core/document/#bson-document-format)to a[collection](https://docs.mongodb.com/manual/core/databases-and-collections/#collections). If the collection does not currently exist, insert operations will create the collection.

MongoDB provides the following methods to insert documents into a collection:

* [`db.collection.insertOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne)
  _New in version 3.2_
* [`db.collection.insertMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/#db.collection.insertMany)
  _New in version 3.2_

In MongoDB, insert operations target a single[collection](https://docs.mongodb.com/manual/reference/glossary/#term-collection). All write operations in MongoDB are[atomic](https://docs.mongodb.com/manual/core/write-operations-atomicity/)on the level of a single[document](https://docs.mongodb.com/manual/core/document/).

![](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-insertOne.bakedsvg.svg "The components of a MongoDB insertOne operations.")

For examples, see[Insert Documents](https://docs.mongodb.com/manual/tutorial/insert-documents/).

## Read Operations

Read operations retrieves[documents](https://docs.mongodb.com/manual/core/document/#bson-document-format)from a[collection](https://docs.mongodb.com/manual/core/databases-and-collections/#collections); i.e. queries a collection for documents. MongoDB provides the following methods to read documents from a collection:

* [`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)

You can specify[query filters or criteria](https://docs.mongodb.com/manual/tutorial/query-documents/#read-operations-query-argument)that identify the documents to return.

![](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-find.bakedsvg.svg "The components of a MongoDB find operation.")

For examples, see:

* [Query Documents](https://docs.mongodb.com/manual/tutorial/query-documents/)
* [Query on Embedded/Nested Documents](https://docs.mongodb.com/manual/tutorial/query-embedded-documents/)
* [Query an Array](https://docs.mongodb.com/manual/tutorial/query-arrays/)
* [Query an Array of Embedded Documents](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)

## Update Operations

Update operations modify existing[documents](https://docs.mongodb.com/manual/core/document/#bson-document-format)in a[collection](https://docs.mongodb.com/manual/core/databases-and-collections/#collections). MongoDB provides the following methods to update documents of a collection:

* [`db.collection.updateOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.updateOne/#db.collection.updateOne)
  _New in version 3.2_
* [`db.collection.updateMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.updateMany/#db.collection.updateMany)
  _New in version 3.2_
* [`db.collection.replaceOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.replaceOne/#db.collection.replaceOne)
  _New in version 3.2_

In MongoDB, update operations target a single collection. All write operations in MongoDB are[atomic](https://docs.mongodb.com/manual/core/write-operations-atomicity/)on the level of a single document.

You can specify criteria, or filters, that identify the documents to update. These[filters](https://docs.mongodb.com/manual/core/document/#document-query-filter)use the same syntax as read operations.

![](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-updateMany.bakedsvg.svg "The components of a MongoDB updateMany operation.")

For examples, see[Update Documents](https://docs.mongodb.com/manual/tutorial/update-documents/).

## Delete Operations

Delete operations remove documents from a collection. MongoDB provides the following methods to delete documents of a collection:

* [`db.collection.deleteOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.deleteOne/#db.collection.deleteOne)
  _New in version 3.2_
* [`db.collection.deleteMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.deleteMany/#db.collection.deleteMany)
  _New in version 3.2_

In MongoDB, delete operations target a single[collection](https://docs.mongodb.com/manual/reference/glossary/#term-collection). All write operations in MongoDB are[atomic](https://docs.mongodb.com/manual/core/write-operations-atomicity/)on the level of a single document.

You can specify criteria, or filters, that identify the documents to remove. These[filters](https://docs.mongodb.com/manual/core/document/#document-query-filter)use the same syntax as read operations.

![](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-deleteMany.bakedsvg.svg "The components of a MongoDB deleteMany operation.")

For examples, see[Delete Documents](https://docs.mongodb.com/manual/tutorial/remove-documents/).

## Bulk Write

MongoDB provides the ability to perform write operations in bulk. For details, see[Bulk Write Operations](https://docs.mongodb.com/manual/core/bulk-write-operations/).

