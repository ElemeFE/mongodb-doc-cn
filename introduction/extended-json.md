# MongoDB Extended JSON

On this page

* [Parsers and Supported Format](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#parsers-and-supported-format)
* [BSON Data Types and Associated Representations](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#bson-data-types-and-associated-representations)

[JSON](https://docs.mongodb.com/manual/reference/glossary/#term-json)can only represent a subset of the types supported by[BSON](https://docs.mongodb.com/manual/reference/glossary/#term-bson). To preserve type information, MongoDB adds the following extensions to the JSON format:

* _Strict mode_
  . Strict mode representations of BSON types conform to the
  [JSON RFC](http://www.json.org/)
  . Any JSON parser can parse these strict mode representations as key/value pairs; however, only the MongoDB internal JSON parser recognizes the type information conveyed by the format.
* `mongo`
  _Shell mode_
  . The MongoDB internal JSON parser and the
  [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)
  shell can parse this mode.

The representation used for the various data types depends on the context in which the JSON is parsed.

## Parsers and Supported Format

### Input in Strict Mode

The following can parse representations in strict mode_with_recognition of the type information.

* [REST Interfaces](https://docs.mongodb.com/ecosystem/tools/http-interfaces)
* [`mongoimport`](https://docs.mongodb.com/manual/reference/program/mongoimport/#bin.mongoimport)
* `--query`
  option of various MongoDB tools
* [MongoDB Compass](https://www.mongodb.com/products/compass)

Other JSON parsers, including[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell and[`db.eval()`](https://docs.mongodb.com/manual/reference/method/db.eval/#db.eval), can parse strict mode representations as key/value pairs, but_without_recognition of the type information.

### Input in`mongo`Shell Mode

The following can parse representations in`mongo`shell mode_with_recognition of the type information.

* [REST Interfaces](https://docs.mongodb.com/ecosystem/tools/http-interfaces)
* [`mongoimport`](https://docs.mongodb.com/manual/reference/program/mongoimport/#bin.mongoimport)
* `--query`
  option of various MongoDB tools
* [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)
  shell

### Output in Strict mode

[`mongoexport`](https://docs.mongodb.com/manual/reference/program/mongoexport/#bin.mongoexport)and[REST and HTTP Interfaces](https://docs.mongodb.com/ecosystem/tools/http-interfaces)output data in_Strict mode_.

### Output in`mongo`Shell Mode

[`bsondump`](https://docs.mongodb.com/manual/reference/program/bsondump/#bin.bsondump)outputs in`mongo`_Shell mode_.

## BSON Data Types and Associated Representations

The following presents the BSON data types and the associated representations in_Strict mode_and`mongo`_Shell mode_.

### Binary

`data_binary`



| Strict Mode | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) | Shell Mode |
| :--- | :--- | :--- |
| { "$binary": "&lt;bindata&gt;", "$type": "&lt;t&gt;" } |  | BinData \( &lt;t&gt;, &lt;bindata&gt; \) |

* `<bindata>`is the base64 representation of a binary string.
* `<t>`is a representation of a single byte indicating the data type. In
  _Strict mode_
  it is a hexadecimal string, and in
  _Shell mode_
  it is an integer. See the extended bson documentation.
  [http://bsonspec.org/spec.html](http://bsonspec.org/spec.html)

### Date

`data_date`



| Strict Mode | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) | Shell Mode |
| :--- | :--- | :--- |
| { "$date": "&lt;date&gt;" } |  | new Date \( &lt;date&gt; \) |

In_Strict mode_,`<date>`is an ISO-8601 date format with a mandatory time zone field following the template`YYYY-MM-DDTHH:mm:ss.mmm<+/-Offset>`.

The MongoDB JSON parser currently does not support loading ISO-8601 strings representing dates prior to the[Unix epoch](https://docs.mongodb.com/manual/reference/glossary/#term-unix-epoch). When formatting pre-epoch dates and dates past what your system’s`time_t`type can hold, the following format is used:

```
{ "$date" : { "$numberLong" : "
<
dateAsMilliseconds
>
" } }

```

In_Shell mode_,`<date>`is the JSON representation of a 64-bit signed integer giving the number of milliseconds since epoch UTC.

### Timestamp

`data_timestamp`



| Strict Mode | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) | Shell Mode |
| :--- | :--- | :--- |
| { "$timestamp": { "t": &lt;t&gt;, "i": &lt;i&gt; } } |  | Timestamp\( &lt;t&gt;, &lt;i&gt; \) |

* `<`
  `t`
  `>`
  is the JSON representation of a 32-bit unsigned integer for seconds since epoch.
* `<`
  `i`
  `>`
  is a 32-bit unsigned integer for the increment.

### Regular Expression

`data_regex`



| Strict Mode | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) | Shell Mode |
| :--- | :--- | :--- |
| { "$regex": "&lt;sRegex&gt;", "$options": "&lt;sOptions&gt;" } |  | /&lt;jRegex&gt;/&lt;jOptions&gt; |

* `<`
  `sRegex`
  `>`
  is a string of valid JSON characters.
* `<`
  `jRegex`
  `>`
  is a string that may contain valid JSON characters and unescaped double quote \(
  `"`
  \) characters, but may not contain unescaped forward slash \(
  `/`
  \) characters.
* `<`
  `sOptions`
  `>`
  is a string containing the regex options represented by the letters of the alphabet.
* `<`
  `jOptions`
  `>`
  is a string that may contain only the characters ‘g’, ‘i’, ‘m’ and ‘s’ \(added in v1.9\). Because the
  `JavaScript`
  and
  `mongo`
  `Shell`
  representations support a limited range of options, any nonconforming options will be dropped when converting to this representation.

### OID

`data_oid`



| Strict Mode | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) | Shell Mode |
| :--- | :--- | :--- |
| { "$oid": "&lt;id&gt;" } |  | ObjectId\( "&lt;id&gt;" \) |

`<id>`is a 24-character hexadecimal string.

### DB Reference

`data_ref`



| Strict Mode | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) | Shell Mode |
| :--- | :--- | :--- |
| { "$ref": "&lt;name&gt;", "$id": "&lt;id&gt;" } |  | DBRef\("&lt;name&gt;", "&lt;id&gt;"\) |

* `<`
  `name`
  `>`
  is a string of valid JSON characters.
* `<`
  `id`
  `>`
  is any valid extended JSON type.

### Undefined Type

`data_undefined`



| Strict Mode | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) | Shell Mode |
| :--- | :--- | :--- |
| { "$undefined": true } |  | undefined |

The representation for the JavaScript/BSON undefined type.

You_cannot_use`undefined`in query documents. Consider the following document inserted into the`people`collection:

```js
db.people.insert( { name : "Sally", age : undefined } )
```

The following queries return an error:

```js
db.people.find( { age : undefined } )
db.people.find( { age : { $gte : undefined } } )
```

However, you can query for undefined values using[`$type`](https://docs.mongodb.com/manual/reference/operator/query/type/#op._S_type), as in:

```js
db.people.find( { age : { $type : 6 } } )
```

This query returns all documents for which the`age`field has value`undefined`.

### MinKey

`data_minkey`



| Strict Mode | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) | Shell Mode |
| :--- | :--- | :--- |
| { "$minKey": 1 } |  | MinKey |

The representation of the MinKey BSON data type that compares lower than all other types. See[Comparison/Sort Order](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#faq-dev-compare-order-for-bson-types)for more information on comparison order for BSON types.

### MaxKey

`data_maxkey`



| Strict Mode | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) | Shell Mode |
| :--- | :--- | :--- |
| { "$maxKey": 1 } |  | MaxKey |

The representation of the MaxKey BSON data type that compares higher than all other types. See[Comparison/Sort Order](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#faq-dev-compare-order-for-bson-types)for more information on comparison order for BSON types.

### NumberLong

New in version 2.6.

`data_numberlong`



| Strict Mode | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) | Shell Mode |
| :--- | :--- | :--- |
| { "$numberLong": "&lt;number&gt;" } |  | NumberLong\( "&lt;number&gt;" \) |

`NumberLong`is a 64 bit signed integer. You must include quotation marks or it will be interpreted as a floating point number, resulting in a loss of accuracy.

For example, the following commands insert`9223372036854775807`as a`NumberLong`with and without quotation marks around the integer value:

```js
db.json.insert( { longQuoted : NumberLong("9223372036854775807") } )
db.json.insert( { longUnQuoted : NumberLong(9223372036854775807) } )
```

When you retrieve the documents, the value of`longUnQuoted`has changed, while`longQuoted`retains its accuracy:

```js
db.json.find()
{ "_id" : ObjectId("54ee1f2d33335326d70987df"), "longQuoted" : NumberLong("9223372036854775807") }
{ "_id" : ObjectId("54ee1f7433335326d70987e0"), "longUnQuoted" : NumberLong("-9223372036854775808") }
```

### NumberDecimal

New in version 3.4.

`data_numberdecimal`



| Strict Mode | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) | Shell Mode |
| :--- | :--- | :--- |
| { "$numberDecimal": "&lt;number&gt;" } |  | NumberDecimal\( "&lt;number&gt;" \) |

`NumberDecimal`is a[high-precision decimal](https://github.com/mongodb/specifications/blob/master/source/bson-decimal128/decimal128.rst). You must include quotation marks, or the input number will be treated as a double, resulting in data loss.

For example, the following commands insert`123.40`as a`NumberDecimal`with and without quotation marks around the value:

```js
db.json.insert( { decimalQuoted : NumberDecimal("123.40") } )
db.json.insert( { decimalUnQuoted : NumberDecimal(123.40) } )
```

When you retrieve the documents, the value of`decimalUnQuoted`has changed, while`decimalQuoted`retains its specified precision:

```js
db.json.find()
{ "_id" : ObjectId("596f88b7b613bb04f80a1ea9"), "decimalQuoted" : NumberDecimal("123.40") }
{ "_id" : ObjectId("596f88c9b613bb04f80a1eaa"), "decimalUnQuoted" : NumberDecimal("123.400000000000") }

```



