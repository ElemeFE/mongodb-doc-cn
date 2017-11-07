# BSON Types

On this page

* [ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid)
* [String](https://docs.mongodb.com/manual/reference/bson-types/#string)
* [Timestamps](https://docs.mongodb.com/manual/reference/bson-types/#timestamps)
* [Date](https://docs.mongodb.com/manual/reference/bson-types/#date)

[BSON](https://docs.mongodb.com/manual/reference/glossary/#term-bson)is a binary serialization format used to store documents and make remote procedure calls in MongoDB. The BSON specification is located at[bsonspec.org](http://bsonspec.org/).

Each BSON type has both integer and string identifiers as listed in the following table:

| Type | Number | Alias | Notes |
| :--- | :--- | :--- | :--- |
| Double | 1 | “double” |  |
| String | 2 | “string” |  |
| Object | 3 | “object” |  |
| Array | 4 | “array” |  |
| Binary data | 5 | “binData” |  |
| Undefined | 6 | “undefined” | Deprecated. |
| ObjectId | 7 | “objectId” |  |
| Boolean | 8 | “bool” |  |
| Date | 9 | “date” |  |
| Null | 10 | “null” |  |
| Regular Expression | 11 | “regex” |  |
| DBPointer | 12 | “dbPointer” | Deprecated. |
| JavaScript | 13 | “javascript” |  |
| Symbol | 14 | “symbol” | Deprecated. |
| JavaScript \(with scope\) | 15 | “javascriptWithScope” |  |
| 32-bit integer | 16 | “int” |  |
| Timestamp | 17 | “timestamp” |  |
| 64-bit integer | 18 | “long” |  |
| Decimal128 | 19 | “decimal” | New in version 3.4. |
| Min key | -1 | “minKey” |  |
| Max key | 127 | “maxKey” |  |

You can use these values with the[`$type`](https://docs.mongodb.com/manual/reference/operator/query/type/#op._S_type)operator to query documents by their BSON type. The[`$type`](https://docs.mongodb.com/manual/reference/operator/aggregation/type/#exp._S_type)aggregation operator returns the type of an[operator expression](https://docs.mongodb.com/manual/meta/aggregation-quick-reference/#agg-quick-ref-operator-expressions)using one of the listed BSON type strings.

To determine a field’s type, see[Check Types in the mongo Shell](https://docs.mongodb.com/manual/core/shell-types/#check-types-in-shell).

If you convert BSON to JSON, see the[Extended JSON](https://docs.mongodb.com/manual/reference/mongodb-extended-json/)reference.

The following sections describe special considerations for particular BSON types.

## ObjectId

ObjectIds are small, likely unique, fast to generate, and ordered. ObjectId values consist of 12 bytes, where the first four bytes are a timestamp that reflect the ObjectId’s creation. Specifically:

* a 4-byte value representing the seconds since the Unix epoch,
* a 3-byte machine identifier,
* a 2-byte process id, and
* a 3-byte counter, starting with a random value.

In MongoDB, each document stored in a collection requires a unique[\_id](https://docs.mongodb.com/manual/reference/glossary/#term-id)field that acts as a[primary key](https://docs.mongodb.com/manual/reference/glossary/#term-primary-key). If an inserted document omits the`_id`field, the MongoDB driver automatically generates an[ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid)for the`_id`field.

This also applies to documents inserted through update operations with[upsert: true](https://docs.mongodb.com/manual/reference/method/db.collection.update/#upsert-parameter).

MongoDB clients should add an`_id`field with a unique ObjectId. Using ObjectIds for the`_id`field provides the following additional benefits:

* in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell, you can access the creation time of the`ObjectId`, using the[`ObjectId.getTimestamp()`](https://docs.mongodb.com/manual/reference/method/ObjectId.getTimestamp/#ObjectId.getTimestamp)method.

* sorting on an`_id`field that stores`ObjectId`values is roughly equivalent to sorting by creation time.

  IMPORTANT

  The relationship between the order of`ObjectId`values and generation time is not strict within a single second. If multiple systems, or multiple processes or threads on a single system generate values, within a single second;`ObjectId`values do not represent a strict insertion order. Clock skew between clients can also result in non-strict ordering even for values because client drivers generate`ObjectId`values.

SEE ALSO

[`ObjectId()`](https://docs.mongodb.com/manual/reference/method/ObjectId/#ObjectId)

## String

BSON strings are UTF-8. In general, drivers for each programming language convert from the language’s string format to UTF-8 when serializing and deserializing BSON. This makes it possible to store most international characters in BSON strings with ease.[\[1\]](https://docs.mongodb.com/manual/reference/bson-types/#sort-string-internationalization)In addition, MongoDB[`$regex`](https://docs.mongodb.com/manual/reference/operator/query/regex/#op._S_regex)queries support UTF-8 in the regex string.

| [\[1\]](https://docs.mongodb.com/manual/reference/bson-types/#id3) | Given strings using UTF-8 character sets, using[`sort()`](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)on strings will be reasonably correct. However, because internally[`sort()`](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)uses the C++`strcmp`api, the sort order may handle some characters incorrectly. |
| :--- | :--- |


## Timestamps

BSON has a special timestamp type for_internal_MongoDB use and is**not**associated with the regular[Date](https://docs.mongodb.com/manual/reference/bson-types/#document-bson-type-date)type. Timestamp values are a 64 bit value where:

* the first 32 bits are a
  `time_t`
  value \(seconds since the Unix epoch\)
* the second 32 bits are an incrementing
  `ordinal`
  for operations within a given second.

Within a single[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instance, timestamp values are always unique.

In replication, the[oplog](https://docs.mongodb.com/manual/reference/glossary/#term-oplog)has a`ts`field. The values in this field reflect the operation time, which uses a BSON timestamp value.

NOTE

The BSON timestamp type is for_internal_MongoDB use. For most cases, in application development, you will want to use the BSON date type. See[Date](https://docs.mongodb.com/manual/reference/bson-types/#document-bson-type-date)for more information.

If you insert a document containing an empty BSON timestamp in a top-level field, the MongoDB server will replace that empty timestamp with the current timestamp value. For example, if you create an insert a document with a timestamp value, as in the following operation:

```
var
a
=
new
Timestamp
();
db
.
test
.
insertOne
(
{
ts
:
a
}
);
```

Then, the[`db.test.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)operation will return a document that resembles the following:

```
{
"_id"
:
ObjectId
(
"542c2b97bac0595474108b48"
),
"ts"
:
Timestamp
(
1412180887
,
1
)
}
```

If`ts`were a field in an embedded document, the server would have left it as an empty timestamp value.

Changed in version 2.6:Previously, the server would only replace empty timestamp values in the first two fields, including`_id`, of an inserted document. Now MongoDB will replace any top-level field.

## Date

BSON Date is a 64-bit integer that represents the number of milliseconds since the Unix epoch \(Jan 1, 1970\). This results in a representable date range of about 290 million years into the past and future.

The[official BSON specification](http://bsonspec.org/#/specification)refers to the BSON Date type as the_UTC datetime_.

BSON Date type is signed.[\[2\]](https://docs.mongodb.com/manual/reference/bson-types/#unsigned-date)Negative values represent dates before 1970.

EXAMPLE

Construct a Date using the`newDate()`constructor in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell:

```
var
mydate1
=
new
Date
()
```

EXAMPLE

Construct a Date using the`ISODate()`constructor in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell:

```
var
mydate2
=
ISODate
()
```

EXAMPLE

Return the`Date`value as string:

```
mydate1
.
toString
()
```

EXAMPLE

Return the month portion of the Date value; months are zero-indexed, so that January is month`0`:

```
mydate1
.
getMonth
()
```

| [\[2\]](https://docs.mongodb.com/manual/reference/bson-types/#id4) | Prior to version 2.0,`Date`values were incorrectly interpreted as_unsigned_integers, which affected sorts, range queries, and indexes on`Date`fields. Because indexes are not recreated when upgrading, please re-index if you created an index on`Date`values with an earlier version, and dates before 1970 are relevant to your application. |
| :--- | :--- |




