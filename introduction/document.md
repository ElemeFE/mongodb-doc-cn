# Documents

本页索引

* [文档结构](https://docs.mongodb.com/manual/core/document/#document-structure)
* [点符号](https://docs.mongodb.com/manual/core/document/#dot-notation)
* [文档限制](https://docs.mongodb.com/manual/core/document/#document-limitations)
* [文档结构的其他用途](https://docs.mongodb.com/manual/core/document/#other-uses-of-the-document-structure)
* [其他资源](https://docs.mongodb.com/manual/core/document/#additional-resources)

MongoDB 将数据记录存储为 BSON 文档. BSON 是一个 [JSON 的二进制形式](https://docs.mongodb.com/manual/reference/glossary/#term-json) 文档, 虽然它比起 JSON 包含更多的数据类型. 关于 BSON 的规范, 参见 [bsonspec.org](http://bsonspec.org/). 也可以查看本手册的 [BSON 类型](https://docs.mongodb.com/manual/reference/bson-types/).

![](https://docs.mongodb.com/manual/_images/crud-annotated-document.bakedsvg.svg "A MongoDB document.")

## 文档结构

MongoDB 文档由 字段-值 成对组成，结构如下:

```js
{ 
   field1 : value1,
   field2 : value2,
   field3 : value3,
   ... 
   fieldN : valueN 
}
```

字段的值可以是任意的 BSON[数据类型](https://docs.mongodb.com/manual/reference/bson-types/), 包括其他的文档, 数组, 或者含文档的数组. 例如, 以下文档包含的不同类型的值:

```js
var  mydoc  =  { 
               _id ： ObjectId （“5099803df3f4948bd2f98391” ），
               name ： {  first ： “Alan” ， last ： “Turing”  }，
               birth ： new  Date （'Jun 23，1912 ' ），
               death ： new  Date （'Jun 07 ，1954年），
               贡献： [  “图灵机” ， “图灵测试” ， “Turingery”  ] 
               观点 ： NumberLong （125万）
            }
```

上述字段具有以下数据类型：

* `_id` 保存了 [ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid).
* `name` 拥有包含 `first`, `last` 字段的 _内嵌文档_.
* `birth` 和 `death` 保存 _Date_ 类型的值.
* `contribs` 拥有 _字符串数组_.
* `views` 保存 _NumberLong_ 类型的值.

### 字段名称

字段名称是 string 类型.

[文档](https://docs.mongodb.com/manual/core/document/#) 对字段名称有以下一些限制:

* The field name `_id` is reserved for use as a primary key; its value must be unique in the collection, is immutable, and may be of any type other than an array.
* The 字段名称 **cannot** start with the dollar sign \(`$`\) character.
* The 字段名称 **cannot** contain the dot \(`.`\) character.
* The 字段名称 **cannot** contain the `null` character.

BSON documents may have more than one field with the same name. Most[MongoDB interfaces](https://docs.mongodb.com/manual/applications/drivers/), however, represent MongoDB with a structure \(e.g. a hash table\) that does not support duplicate 字段名称. If you need to manipulate documents that have more than one field with the same name, see the[driver documentation](https://docs.mongodb.com/manual/applications/drivers/)for your driver.

Some documents created by internal MongoDB processes may have duplicate fields, but_no_MongoDB process will_ever_add duplicate fields to an existing user document.

### 字段值限制

对于[有索引的集合](https://docs.mongodb.com/manual/indexes/), 其索引字段有一个 [最大索引 key 长度 (`MaximumIndexKeyLength`)](https://docs.mongodb.com/manual/reference/limits/#Index-Key-Limit) 限制. 详细参见 [`MaximumIndexKeyLength`](https://docs.mongodb.com/manual/reference/limits/#Index-Key-Limit).

## 点符号

MongoDB 使用 _._ 来访问数组中的元素以及内嵌文档中的字段.

### 数组

To specify or access an element of an array by the zero-based index position, concatenate the array name with the dot \(`.`\) and zero-based index position, and enclose in quotes:

```
" < array > . < index > "
```

For example, given the following field in a document:

```
{
    ...
    contribs : ["Turing machine", "Turing test", "Turingery"],
    ...
}
```

To specify the third element in the`contribs`array, use the 点符号`"contribs.2"`.

For examples querying arrays, see:

* [Query an Array](https://docs.mongodb.com/manual/tutorial/query-arrays/)
* [Query an Array of Embedded Documents](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)

SEE ALSO

* [`$`](https://docs.mongodb.com/manual/reference/operator/update/positional/#up._S_)
  positional operator for update operations,
* [`$`](https://docs.mongodb.com/manual/reference/operator/projection/positional/#proj._S_)
  projection operator when array index position is unknown
* [Query an Array](https://docs.mongodb.com/manual/tutorial/query-arrays/#read-operations-arrays)
  for 点符号 examples with arrays.

### 内嵌文档

To specify or access a field of an embedded document with 点符号, concatenate the embedded document name with the dot \(`.`\) and the field name, and enclose in quotes:

```
" < embedded document > . < field > "
```

For example, given the following field in a document:

```
{
  ...
  name : { first : "Alan", last : "Turing"},
  contact : { phone : {type : "cell", number : "111-222-3333"} },
  ...
}
```

* To specify the field named `last` in the `name` field, use the 点符号 `"name.last"`.
* To specify the `number` in the `phone` document in the `contact` field, use the 点符号 `"contact.phone.number"`.

For examples querying embedded documents, see:

* [Query on Embedded/Nested Documents](https://docs.mongodb.com/manual/tutorial/query-embedded-documents/)
* [Query an Array of Embedded Documents](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)

## 文档限制

### 文档大小限制

BSON 文档的最大 size 为 16 MB.

The maximum document size helps ensure that a single document cannot use excessive amount of RAM or, during transmission, excessive amount of bandwidth. To store documents larger than the maximum size, MongoDB provides the GridFS API. See[`mongofiles`](https://docs.mongodb.com/manual/reference/program/mongofiles/#bin.mongofiles)and the documentation for your[driver](https://docs.mongodb.com/manual/applications/drivers/)for more information about GridFS.

### 文档字段排序

MongoDB preserves the order of the document fields following write operations_except_for the following cases:

* The `_id` field is always the first field in the document.
* Updates that include [`renaming`](https://docs.mongodb.com/manual/reference/operator/update/rename/#up._S_rename) of 字段名称 may result in the reordering of fields in the document.

Changed in version 2.6:Starting in version 2.6, MongoDB actively attempts to preserve the field order in a document. Before version 2.6, MongoDB did not actively preserve the order of the fields in a document.

### `_id` 字段

In MongoDB, each document stored in a collection requires a unique[\_id](https://docs.mongodb.com/manual/reference/glossary/#term-id)field that acts as a[primary key](https://docs.mongodb.com/manual/reference/glossary/#term-primary-key). If an inserted document omits the`_id`field, the MongoDB driver automatically generates an[ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid)for the`_id`field.

This also applies to documents inserted through update operations with[upsert: true](https://docs.mongodb.com/manual/reference/method/db.collection.update/#upsert-parameter).

The`_id`field has the following behavior and constraints:

* By default, MongoDB creates a unique index on the`_id`field during the creation of a collection.

* The`_id`field is always the first field in the documents. If the server receives a document that does not have the`_id`field first, then the server will move the field to the beginning.

* The`_id`field may contain values of any[BSON data type](https://docs.mongodb.com/manual/reference/bson-types/), other than an array.

>  WARNING
>
>  To ensure functioning replication, do not store values that are of the BSON regular expression type in the`_id`field.
>

The following are common options for storing values for`_id`:

* Use an[ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid).

* Use a natural unique identifier, if available. This saves space and avoids an additional index.

* Generate an auto-incrementing number.

* Generate a UUID in your application code. For a more efficient storage of the UUID values in the collection and in the`_id`index, store the UUID as a value of the BSON`BinData`type.

  Index keys that are of the`BinData`type are more efficiently stored in the index if:

  * the binary subtype value is in the range of 0-7 or 128-135, and
  * the length of the byte array is: 0, 1, 2, 3, 4, 5, 6, 7, 8, 10, 12, 14, 16, 20, 24, or 32.

* Use your driver’s BSON UUID facility to generate UUIDs. Be aware that driver implementations may implement UUID serialization and deserialization logic differently, which may not be fully compatible with other drivers. See your[driver documentation](https://api.mongodb.com/)for information concerning UUID interoperability.

> NOTE
>
> Most MongoDB driver clients will include the`_id`field and generate an`ObjectId`before sending the insert operation to MongoDB; however, if the client sends a document without an`_id`field, the[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)will add the`_id`field and generate the`ObjectId`.

## 文档结构的其他用途

除了定义数据记录之外, MongoDB 还可以使用文档结构来做一些, 包括但不限于: [文档查询过滤](https://docs.mongodb.com/manual/core/document/#document-query-filter),[文档指定更新](https://docs.mongodb.com/manual/core/document/#document-update-specification), 以及[文档索引规范](https://docs.mongodb.com/manual/core/document/#document-index-specification)

### 文档查询过滤

查询过滤条件可以用来确定哪些记录被查询、更新、修改删除等.

你可以使用 `<field>:<value>` 表达式来指定匹配条件并在[查询操作](https://docs.mongodb.com/manual/reference/operator/query/)中使用.

```
{ 
  < field1 >： < value1 > ，
  < field2 >： {  <  operator > : < value >  }，
  ... 
}
```

例如, 参见:

* [Query Documents](https://docs.mongodb.com/manual/tutorial/query-documents/)
* [Query on Embedded/Nested Documents](https://docs.mongodb.com/manual/tutorial/query-embedded-documents/)
* [Query an Array](https://docs.mongodb.com/manual/tutorial/query-arrays/)
* [Query an Array of Embedded Documents](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)

### 文档指定更新

可以使用 [更新操作](https://docs.mongodb.com/manual/reference/operator/update/#id1) 来指定 [`db.collection.update()`](https://docs.mongodb.com/manual/reference/method/db.collection.update/#db.collection.update) 修改文档的具体字段.

```
{ 
  < operator1 >： {  < field1 >： < value1 > ， ...  }，
  < operator2 >： {  < field2 >： < value2 > ， ...  }，
  ... 
}
```

For examples, see[Update specifications](https://docs.mongodb.com/manual/tutorial/update-documents/#update-documents-modifiers).

### 文档索引规范

如果有设置索引的话，那么索引会定义字段的类型:

```
{  < field1 >： < type1 > ， < field2 >： < type2 > ， ...   }
```

## 其他资源

* [Thinking in Documents Part 1 \(Blog Post\)](https://www.mongodb.com/blog/post/thinking-documents-part-1?jmp=docs)



