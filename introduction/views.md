# Views

New in version 3.4.

On this page

* [Create View](https://docs.mongodb.com/manual/core/views/#create-view)
* [Behavior](https://docs.mongodb.com/manual/core/views/#behavior)
* [Drop a View](https://docs.mongodb.com/manual/core/views/#drop-a-view)
* [Modify a View](https://docs.mongodb.com/manual/core/views/#modify-a-view)
* [Supported Operations](https://docs.mongodb.com/manual/core/views/#supported-operations)

Starting in version 3.4, MongoDB adds support for creating read-only views from existing collections or other views.

## Create View

To create or define a view, MongoDB 3.4 introduces:

* the`viewOn`and`pipeline`options to the existing[`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create)command \(and[`db.createCollection`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)helper\):

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

## Behavior

Views exhibit the following behavior:

### Read Only

Views are read-only; write operations on views will error.

The following read operations can support views:

* [`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)
* [`db.collection.findOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.findOne/#db.collection.findOne)
* [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate)
* [`db.collection.count()`](https://docs.mongodb.com/manual/reference/method/db.collection.count/#db.collection.count)
* [`db.collection.distinct()`](https://docs.mongodb.com/manual/reference/method/db.collection.distinct/#db.collection.distinct)

### Index Use and Sort Operations

* Views use the indexes of the underlying collection.

* As the indexes are on the underlying collection, you cannot create, drop or re-build indexes on the view directly nor get a list of indexes on the view.

* You cannot specify a[`$natural`](https://docs.mongodb.com/manual/reference/operator/meta/natural/#metaOp._S_natural)sort on a view.

  For example, the following operation is_invalid_:

  ```
  db.view.find().sort({$natural: 1})
  ```

### Projection Restrictions

[`find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)operations on views do not support the following[projection](https://docs.mongodb.com/manual/reference/operator/projection/)operators:

* [`$`](https://docs.mongodb.com/manual/reference/operator/projection/positional/#proj._S_)
* [`$elemMatch`](https://docs.mongodb.com/manual/reference/operator/projection/elemMatch/#proj._S_elemMatch)
* [`$slice`](https://docs.mongodb.com/manual/reference/operator/projection/slice/#proj._S_slice)
* [`$meta`](https://docs.mongodb.com/manual/reference/operator/projection/meta/#proj._S_meta)

### Immutable Name

You cannot rename[views](https://docs.mongodb.com/manual/core/views/#).

### View Creation

* Views are computed on demand during read operations, and MongoDB executes read operations on views as part of the underlying aggregation pipeline. As such, views do not support operations such as:
  * [`db.collection.mapReduce()`](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce)
    ,
  * [`$text`](https://docs.mongodb.com/manual/reference/operator/query/text/#op._S_text)
    operator, since
    `$text`
    operation in aggregation is valid only for the first stage,
  * [`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear)
    command and
    [`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear)
    pipeline stage.
* If the aggregation pipeline used to create the view suppresses the
  `_id`
  field, documents in the view do not have the
  `_id`
  field.

### Sharded View

Views are considered sharded if their underlying collection is sharded. As such, you cannot specify a sharded view for the`from`field in[`$lookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#pipe._S_lookup)and[`$graphLookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/graphLookup/#pipe._S_graphLookup)operations.

### Views and Collation

* You can specify a default
  [collation](https://docs.mongodb.com/manual/reference/collation/)
  for a view at creation time. If no collation is specified, the view’s default collation is the “simple” binary comparison collator. That is, the view does not inherit the collection’s default collation.
* String comparisons on the view use the view’s default collation. An operation that attempts to change or override a view’s default collation will fail with an error.
* If creating a view from another view, you cannot specify a collation that differs from the source view’s collation.
* If performing an aggregation that involves multiple views, such as with
  [`$lookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#pipe._S_lookup)
  or
  [`$graphLookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/graphLookup/#pipe._S_graphLookup)
  , the views must have the same
  [collation](https://docs.mongodb.com/manual/reference/collation/)
  .

### Public View Definition

Operations that lists collections, such as[`db.getCollectionInfos()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionInfos/#db.getCollectionInfos)and[`db.getCollectionNames()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionNames/#db.getCollectionNames), include views in their outputs.

IMPORTANT

The view definition is public; i.e.[`db.getCollectionInfos()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionInfos/#db.getCollectionInfos)and`explain`operations on the view will include the pipeline that defines the view. As such, avoid referring directly to sensitive fields and values in view definitions.

## Drop a View

To remove a view, use the[`db.collection.drop()`](https://docs.mongodb.com/manual/reference/method/db.collection.drop/#db.collection.drop)method on the view.

## Modify a View

You can modify a view either by dropping and recreating the view or using the[`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod)command.

## Supported Operations

The following operations provide support for views, except for the restrictions mentioned in this page:

| Commands | Methods |
| :--- | :--- |
| [`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create) | [`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)[`db.createView()`](https://docs.mongodb.com/manual/reference/method/db.createView/#db.createView) |
| [`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod) |  |
|  | [`db.getCollection()`](https://docs.mongodb.com/manual/reference/method/db.getCollection/#db.getCollection)[`db.getCollectionInfos()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionInfos/#db.getCollectionInfos)[`db.getCollectionNames()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionNames/#db.getCollectionNames) |
| [`find`](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find)[`distinct`](https://docs.mongodb.com/manual/reference/command/distinct/#dbcmd.distinct)[`count`](https://docs.mongodb.com/manual/reference/command/count/#dbcmd.count) | [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate)[`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)[`db.collection.findOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.findOne/#db.collection.findOne)[`db.collection.count()`](https://docs.mongodb.com/manual/reference/method/db.collection.count/#db.collection.count)[`db.collection.distinct()`](https://docs.mongodb.com/manual/reference/method/db.collection.distinct/#db.collection.distinct) |



