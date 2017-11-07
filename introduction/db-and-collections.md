# Databases and Collections

On this page

* [Databases](https://docs.mongodb.com/manual/core/databases-and-collections/#databases)
* [Collections](https://docs.mongodb.com/manual/core/databases-and-collections/#collections)

MongoDB stores[BSON documents](https://docs.mongodb.com/manual/core/document/#bson-document-format), i.e. data records, in[collections](https://docs.mongodb.com/manual/reference/glossary/#term-collection); the collections in databases.

![](https://docs.mongodb.com/manual/_images/crud-annotated-collection.bakedsvg.svg "A collection of MongoDB documents.")

## Databases

In MongoDB, databases hold collections of documents.

To select a database to use, in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell, issue the`use<db>`statement, as in the following example:

```bash
use myDB
```

### Create a Database

If a database does not exist, MongoDB creates the database when you first store data for that database. As such, you can switch to a non-existent database and perform the following operation in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell:

```js
use myNewDB

db.myNewCollection1.insertOne( { x: 1 } )
```

The[`insertOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne)operation creates both the database`myNewDB`and the collection`myNewCollection1`if they do not already exist.

For a list of restrictions on database names, see[Naming Restrictions](https://docs.mongodb.com/manual/reference/limits/#restrictions-on-db-names).

## Collections

MongoDB stores documents in collections. Collections are analogous to tables in relational databases.

### Create a Collection

If a collection does not exist, MongoDB creates the collection when you first store data for that collection.

```js
db.myNewCollection2.insertOne( { x: 1 } )
db.myNewCollection3.createIndex( { y: 1 } )
```

Both the[`insertOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne)and the[`createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex)operations create their respective collection if they do not already exist.

For a list of restrictions on collection names, see[Naming Restrictions](https://docs.mongodb.com/manual/reference/limits/#restrictions-on-collection-names).

### Explicit Creation

MongoDB provides the[`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)method to explicitly create a collection with various options, such as setting the maximum size or the documentation validation rules. If you are not specifying these options, you do not need to explicitly create the collection since MongoDB creates new collections when you first store data for the collections.

To modify these collection options, see[`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod).

### Document Validation

New in version 3.2.

By default, a collection does not require its documents to have the same schema; i.e. the documents in a single collection do not need to have the same set of fields and the data type for a field can differ across documents within a collection.

Starting in MongoDB 3.2, however, you can enforce[document validation rules](https://docs.mongodb.com/manual/core/document-validation/)for a collection during update and insert operations. See[Document Validation](https://docs.mongodb.com/manual/core/document-validation/)for details.

### Modifying Document Structure[¶](https://docs.mongodb.com/manual/core/databases-and-collections/#modifying-document-structure)

To change the structure of the documents in a collection, such as add new fields, remove existing fields, or change the field values to a new type, update the documents to the new structure.

