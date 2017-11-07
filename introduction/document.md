# Documents

On this page

* [Document Structure](https://docs.mongodb.com/manual/core/document/#document-structure)
* [Dot Notation](https://docs.mongodb.com/manual/core/document/#dot-notation)
* [Document Limitations](https://docs.mongodb.com/manual/core/document/#document-limitations)
* [Other Uses of the Document Structure](https://docs.mongodb.com/manual/core/document/#other-uses-of-the-document-structure)
* [Additional Resources](https://docs.mongodb.com/manual/core/document/#additional-resources)

MongoDB stores data records as BSON documents. BSON is a binary representation of[JSON](https://docs.mongodb.com/manual/reference/glossary/#term-json)documents, though it contains more data types than JSON. For the BSON spec, see[bsonspec.org](http://bsonspec.org/). See also[BSON Types](https://docs.mongodb.com/manual/reference/bson-types/).

![](https://docs.mongodb.com/manual/_images/crud-annotated-document.bakedsvg.svg "A MongoDB document.")

## Document Structure

MongoDB documents are composed of field-and-value pairs and have the following structure:

```
{
field1
:
value1
,
field2
:
value2
,
field3
:
value3
,
...
fieldN
:
valueN
}
```

The value of a field can be any of the BSON[data types](https://docs.mongodb.com/manual/reference/bson-types/), including other documents, arrays, and arrays of documents. For example, the following document contains values of varying types:

```
var
mydoc
=
{
_id
:
ObjectId
(
"5099803df3f4948bd2f98391"
),
name
:
{
first
:
"Alan"
,
last
:
"Turing"
},
birth
:
new
Date
(
'Jun 23, 1912'
),
death
:
new
Date
(
'Jun 07, 1954'
),
contribs
:
[
"Turing machine"
,
"Turing test"
,
"Turingery"
],
views
:
NumberLong
(
1250000
)
}
```

The above fields have the following data types:

* `_id`
  holds an
  [ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid)
  .
* `name`
  holds an
  _embedded document_
  that contains the fields
  `first`
  and
  `last`
  .
* `birth`
  and
  `death`
  hold values of the
  _Date_
  type.
* `contribs`
  holds an
  _array of strings_
  .
* `views`
  holds a value of the
  _NumberLong_
  type.

### Field Names

Field names are strings.

[Documents](https://docs.mongodb.com/manual/core/document/#)have the following restrictions on field names:

* The field name
  `_id`
  is reserved for use as a primary key; its value must be unique in the collection, is immutable, and may be of any type other than an array.
* The field names
  **cannot**
  start with the dollar sign \(
  `$`
  \) character.
* The field names
  **cannot**
  contain the dot \(
  `.`
  \) character.
* The field names
  **cannot**
  contain the
  `null`
  character.

BSON documents may have more than one field with the same name. Most[MongoDB interfaces](https://docs.mongodb.com/manual/applications/drivers/), however, represent MongoDB with a structure \(e.g. a hash table\) that does not support duplicate field names. If you need to manipulate documents that have more than one field with the same name, see the[driver documentation](https://docs.mongodb.com/manual/applications/drivers/)for your driver.

Some documents created by internal MongoDB processes may have duplicate fields, but_no_MongoDB process will_ever_add duplicate fields to an existing user document.

### Field Value Limit

For[indexed collections](https://docs.mongodb.com/manual/indexes/), the values for the indexed fields have a[`MaximumIndexKeyLength`](https://docs.mongodb.com/manual/reference/limits/#Index-Key-Limit)limit. See[`MaximumIndexKeyLength`](https://docs.mongodb.com/manual/reference/limits/#Index-Key-Limit)for details.

## Dot Notation

MongoDB uses the_dot notation_to access the elements of an array and to access the fields of an embedded document.

### Arrays

To specify or access an element of an array by the zero-based index position, concatenate the array name with the dot \(`.`\) and zero-based index position, and enclose in quotes:

```
"
<
array
>
.
<
index
>
"
```

For example, given the following field in a document:

```
{
...
contribs
:
[
"Turing machine"
,
"Turing test"
,
"Turingery"
],
...
}
```

To specify the third element in the`contribs`array, use the dot notation`"contribs.2"`.

For examples querying arrays, see:

* [Query an Array](https://docs.mongodb.com/manual/tutorial/query-arrays/)
* [Query an Array of Embedded Documents](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)

SEE ALSO

* [`$`](https://docs.mongodb.com/manual/reference/operator/update/positional/#up._S_)
  positional operator for update operations,
* [`$`](https://docs.mongodb.com/manual/reference/operator/projection/positional/#proj._S_)
  projection operator when array index position is unknown
* [Query an Array](https://docs.mongodb.com/manual/tutorial/query-arrays/#read-operations-arrays)
  for dot notation examples with arrays.

### Embedded Documents

To specify or access a field of an embedded document with dot notation, concatenate the embedded document name with the dot \(`.`\) and the field name, and enclose in quotes:

```
"
<
embedded document
>
.
<
field
>
"
```

For example, given the following field in a document:

```
{
...
name
:
{
first
:
"Alan"
,
last
:
"Turing"
},
contact
:
{
phone
:
{
type
:
"cell"
,
number
:
"111-222-3333"
}
},
...
}
```

* To specify the field named
  `last`
  in the
  `name`
  field, use the dot notation
  `"name.last"`
  .
* To specify the
  `number`
  in the
  `phone`
  document in the
  `contact`
  field, use the dot notation
  `"contact.phone.number"`
  .

For examples querying embedded documents, see:

* [Query on Embedded/Nested Documents](https://docs.mongodb.com/manual/tutorial/query-embedded-documents/)
* [Query an Array of Embedded Documents](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)

## Document Limitations

Documents have the following attributes:

### Document Size Limit

The maximum BSON document size is 16 megabytes.

The maximum document size helps ensure that a single document cannot use excessive amount of RAM or, during transmission, excessive amount of bandwidth. To store documents larger than the maximum size, MongoDB provides the GridFS API. See[`mongofiles`](https://docs.mongodb.com/manual/reference/program/mongofiles/#bin.mongofiles)and the documentation for your[driver](https://docs.mongodb.com/manual/applications/drivers/)for more information about GridFS.

### Document Field Order

MongoDB preserves the order of the document fields following write operations_except_for the following cases:

* The
  `_id`
  field is always the first field in the document.
* Updates that include
  [`renaming`](https://docs.mongodb.com/manual/reference/operator/update/rename/#up._S_rename)
  of field names may result in the reordering of fields in the document.

Changed in version 2.6:Starting in version 2.6, MongoDB actively attempts to preserve the field order in a document. Before version 2.6, MongoDB did not actively preserve the order of the fields in a document.

### The`_id`Field

In MongoDB, each document stored in a collection requires a unique[\_id](https://docs.mongodb.com/manual/reference/glossary/#term-id)field that acts as a[primary key](https://docs.mongodb.com/manual/reference/glossary/#term-primary-key). If an inserted document omits the`_id`field, the MongoDB driver automatically generates an[ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid)for the`_id`field.

This also applies to documents inserted through update operations with[upsert: true](https://docs.mongodb.com/manual/reference/method/db.collection.update/#upsert-parameter).

The`_id`field has the following behavior and constraints:

* By default, MongoDB creates a unique index on the`_id`field during the creation of a collection.

* The`_id`field is always the first field in the documents. If the server receives a document that does not have the`_id`field first, then the server will move the field to the beginning.

* The`_id`field may contain values of any[BSON data type](https://docs.mongodb.com/manual/reference/bson-types/), other than an array.

  WARNING

  To ensure functioning replication, do not store values that are of the BSON regular expression type in the`_id`field.

The following are common options for storing values for`_id`:

* Use an[ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid).

* Use a natural unique identifier, if available. This saves space and avoids an additional index.

* Generate an auto-incrementing number.

* Generate a UUID in your application code. For a more efficient storage of the UUID values in the collection and in the`_id`index, store the UUID as a value of the BSON`BinData`type.

  Index keys that are of the`BinData`type are more efficiently stored in the index if:

  * the binary subtype value is in the range of 0-7 or 128-135, and
  * the length of the byte array is: 0, 1, 2, 3, 4, 5, 6, 7, 8, 10, 12, 14, 16, 20, 24, or 32.

* Use your driver’s BSON UUID facility to generate UUIDs. Be aware that driver implementations may implement UUID serialization and deserialization logic differently, which may not be fully compatible with other drivers. See your[driver documentation](https://api.mongodb.com/)for information concerning UUID interoperability.

NOTE

Most MongoDB driver clients will include the`_id`field and generate an`ObjectId`before sending the insert operation to MongoDB; however, if the client sends a document without an`_id`field, the[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)will add the`_id`field and generate the`ObjectId`.

## Other Uses of the Document Structure

In addition to defining data records, MongoDB uses the document structure throughout, including but not limited to:[query filters](https://docs.mongodb.com/manual/core/document/#document-query-filter),[update specifications documents](https://docs.mongodb.com/manual/core/document/#document-update-specification), and[index specification documents](https://docs.mongodb.com/manual/core/document/#document-index-specification)

### Query Filter Documents

Query filter documents specify the conditions that determine which records to select for read, update, and delete operations.

You can use`<field>:<value>`expressions to specify the equality condition and[query operator](https://docs.mongodb.com/manual/reference/operator/query/)expressions.

```
{
<
field1
>
:
<
value1
>
,
<
field2
>
:
{
<
operator
>
:
<
value
>
},
...
}
```

For examples, see:

* [Query Documents](https://docs.mongodb.com/manual/tutorial/query-documents/)
* [Query on Embedded/Nested Documents](https://docs.mongodb.com/manual/tutorial/query-embedded-documents/)
* [Query an Array](https://docs.mongodb.com/manual/tutorial/query-arrays/)
* [Query an Array of Embedded Documents](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)

### Update Specification Documents

Update specification documents use[update operators](https://docs.mongodb.com/manual/reference/operator/update/#id1)to specify the data modifications to perform on specific fields during an[`db.collection.update()`](https://docs.mongodb.com/manual/reference/method/db.collection.update/#db.collection.update)operation.

```
{
<
operator1
>
:
{
<
field1
>
:
<
value1
>
,
...
},
<
operator2
>
:
{
<
field2
>
:
<
value2
>
,
...
},
...
}
```

For examples, see[Update specifications](https://docs.mongodb.com/manual/tutorial/update-documents/#update-documents-modifiers).

### Index Specification Documents

Index specifications document define the field to index and the index type:

```
{
<
field1
>
:
<
type1
>
,
<
field2
>
:
<
type2
>
,
...
}
```

## Additional Resources

* [Thinking in Documents Part 1 \(Blog Post\)](https://www.mongodb.com/blog/post/thinking-documents-part-1?jmp=docs)



