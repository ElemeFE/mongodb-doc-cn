# Documents

本页索引

* [文档结构](#文档结构)
* [点符号](#点符号)
* [文档限制](#文档限制)
* [文档结构的其他用途](#文档结构的其他用途)
* [其他资源](#其他资源)

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

字段的值可以是任意的 BSON [数据类型](https://docs.mongodb.com/manual/reference/bson-types/), 包括其他的文档, 数组, 或者含文档的数组. 例如, 以下文档包含的不同类型的值:

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

[文档](#) 对字段名称有以下一些限制:

* 字段 `_id` 保留用于主键的; 该字段必须在集合中唯一, 不可改变, 并且可以是数组外的任意类型.
* 字段名称**不能**以 \(`$`\) 符号开头.
* 字段名称**不能**包含 \(`.`\) 字符.
* 字段名称**不能**包含 `null` 字符.

BSON documents may have more than one field with the same name. Most[MongoDB interfaces](https://docs.mongodb.com/manual/applications/drivers/), however, represent MongoDB with a structure \(e.g. a hash table\) that does not support duplicate 字段名称. If you need to manipulate documents that have more than one field with the same name, see the[driver documentation](https://docs.mongodb.com/manual/applications/drivers/)for your driver.

Some documents created by internal MongoDB processes may have duplicate fields, but_no_MongoDB process will_ever_add duplicate fields to an existing user document.

### 字段值限制

对于[有索引的集合](https://docs.mongodb.com/manual/indexes/), 其索引字段有一个 [最大索引 key 长度 (`MaximumIndexKeyLength`)](https://docs.mongodb.com/manual/reference/limits/#Index-Key-Limit) 限制. 详细参见 [`MaximumIndexKeyLength`](https://docs.mongodb.com/manual/reference/limits/#Index-Key-Limit).

## 点符号

MongoDB 使用 _._ 来访问数组中的元素以及内嵌文档中的字段.

### 数组

要通过基于零的数组位置来指定或访问数组的元素，请使用数组名与 \(`.`\) 和从零开始的数组位置，并用中括号括起来：

```
" < array > . < index > "
```

例如, 给定如下文档的字段:

```
{
    ...
    contribs : ["Turing machine", "Turing test", "Turingery"],
    ...
}
```

要指定数组 `contribs` 中的第三个元素, 可以使用 `"contribs.2"`.

关于查询数组的, 参见:

* [Query an Array](https://docs.mongodb.com/manual/tutorial/query-arrays/)
* [Query an Array of Embedded Documents](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/)

也参见

* [`$`](https://docs.mongodb.com/manual/reference/operator/update/positional/#up._S_) positional operator for update operations,
* [`$`](https://docs.mongodb.com/manual/reference/operator/projection/positional/#proj._S_) projection operator when array index position is unknown
* [Query an Array](https://docs.mongodb.com/manual/tutorial/query-arrays/#read-operations-arrays) for 点符号 examples with arrays.

### 内嵌文档

要指定或访问内嵌文档的字段，请将内嵌文档名称与 \(`.`\) 和字段名称连接起来，并用中括号括起来：

```
" < embedded document > . < field > "
```

例如, 给定如下文档中的字段:

```
{
  ...
  name : { first : "Alan", last : "Turing"},
  contact : { phone : {type : "cell", number : "111-222-3333"} },
  ...
}
```

* 要指定 `name` 字段中的 `last` 字段, 可以使用 `"name.last"`.
* 要指定 `contact` 下 `phone` 中的 `number` 字段, 可以使用 `"contact.phone.number"`.

关于查询内嵌文档, 参见:

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

在 MongoDB 中, 存在集合中的每个文档都需要一个唯一的 [\_id](https://docs.mongodb.com/manual/reference/glossary/#term-id) 字段来作为 [主键](https://docs.mongodb.com/manual/reference/glossary/#term-primary-key). 如果一个插入的文档省略了 `_id` 字段, 那么 MongoDB 驱动会自动生成一个 [ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid) 来作为 `_id`.

这也适用于通过 [upsert: true](https://docs.mongodb.com/manual/reference/method/db.collection.update/#upsert-parameter) 方式插入的文档.

`_id` 字段有一下的表现和约束:

* By default, MongoDB creates a unique index on the`_id`field during the creation of a collection.

* The`_id`field is always the first field in the documents. If the server receives a document that does not have the`_id`field first, then the server will move the field to the beginning.

* The`_id`field may contain values of any[BSON data type](https://docs.mongodb.com/manual/reference/bson-types/), other than an array.

>  警告
>
>  To ensure functioning replication, do not store values that are of the BSON regular expression type in the`_id`field.
>

一下是 `_id` 的常见情况:

* 使用 [ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#objectid).

* Use a natural unique identifier, if available. This saves space and avoids an additional index.

* 生成一个自增长的数字. (译注：类似 mysql 的 auto increase)

* 在你的应用中生成一个 UUID. For a more efficient storage of the UUID values in the collection and in the`_id`index, store the UUID as a value of the BSON `BinData` type.

  Index keys that are of the`BinData` type are more efficiently stored in the index if:

  * the binary subtype value is in the range of 0-7 or 128-135, and
  * the length of the byte array is: 0, 1, 2, 3, 4, 5, 6, 7, 8, 10, 12, 14, 16, 20, 24, or 32.

* Use your driver’s BSON UUID facility to generate UUIDs. Be aware that driver implementations may implement UUID serialization and deserialization logic differently, which may not be fully compatible with other drivers. See your[driver documentation](https://api.mongodb.com/)for information concerning UUID interoperability.

> NOTE
>
> Most MongoDB driver clients will include the`_id`field and generate an`ObjectId`before sending the insert operation to MongoDB; however, if the client sends a document without an`_id`field, the[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)will add the`_id`field and generate the`ObjectId`.

## 文档结构的其他用途

除了定义数据记录之外, MongoDB 还可以使用文档结构来做一些, 包括但不限于: [文档查询过滤](#document-query-filter),[文档指定更新](#document-update-specification), 以及[文档索引规范](#document-index-specification)

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



