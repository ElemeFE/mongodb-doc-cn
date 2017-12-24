# Collation

On this page

* [Collation Document](https://docs.mongodb.com/manual/reference/collation/#collation-document)
* [Operations that Support Collation](https://docs.mongodb.com/manual/reference/collation/#operations-that-support-collation)
* [Behavior](https://docs.mongodb.com/manual/reference/collation/#behavior)

New in version 3.4.

Collation allows users to specify language-specific rules for string comparison, such as rules for lettercase and accent marks.

You can specify collation for a collection or a view, an index, or specific operations that support collation.

## Collation Document

A collation document has the following fields:

```
{
   locale: <string>,
   caseLevel: <boolean>,
   caseFirst: <string>,
   strength: <int>,
   numericOrdering: <boolean>,
   alternate: <string>,
   maxVariable: <string>,
   backwards: <boolean>
}
```

When specifying collation, the`locale`field is mandatory; all other collation fields are optional. For descriptions of the fields, see[Collation Document](https://docs.mongodb.com/manual/reference/collation/#collation-document-fields).

Default collation parameter values vary depending on which locale you specify. For a complete list of default collation parameters and the locales they are associated with, see[Collation Default Parameters](https://docs.mongodb.com/manual/reference/collation-locales-defaults/#collation-default-params).

| Field | Type | Description |
| :--- | :--- | :--- |
| `locale` | string | The ICU locale. See[Supported Languages and Locales](https://docs.mongodb.com/manual/reference/collation-locales-defaults/#collation-languages-locales)for a list of supported locales.To specify simple binary comparison, specify`locale`value of`"simple"`. |
| `strength` | integer | Optional. The level of comparison to perform. Corresponds to[ICU Comparison Levels](http://userguide.icu-project.org/collation/concepts#TOC-Comparison-Levels). Possible values are:ValueDescription1Primary level of comparison. Collation performs comparisons of the base characters only, ignoring other differences such as diacritics and case.2Secondary level of comparison. Collation performs comparisons up to secondary differences, such as diacritics. That is, collation performs comparisons of base characters \(primary differences\) and diacritics \(secondary differences\). Differences between base characters takes precedence over secondary differences.3Tertiary level of comparison. Collation performs comparisons up to tertiary differences, such as case and letter variants. That is, collation performs comparisons of base characters \(primary differences\), diacritics \(secondary differences\), and case and variants \(tertiary differences\). Differences between base characters takes precedence over secondary differences, which takes precedence over tertiary differences.This is the default level.4Quaternary Level. Limited for specific use case to consider punctuation when levels 1-3 ignore punctuation or for processing Japanese text.5Identical Level. Limited for specific use case of tie breaker.See[ICU Collation: Comparison Levels](http://userguide.icu-project.org/collation/concepts#TOC-Comparison-Levels)for details. |
| `caseLevel` | boolean | Optional. Flag that determines whether to include case comparison at`strength`level`1`or`2`.If`true`, include case comparison; i.e.When used with`strength:1`, collation compares base characters and case.When used with`strength:2`, collation compares base characters, diacritics \(and possible other secondary differences\) and case.If`false`, do not include case comparison at level`1`or`2`. The default is`false`.For more information, see[ICU Collation: Case Level](http://userguide.icu-project.org/collation/concepts#TOC-CaseLevel). |
| `caseFirst` | string | Optional. A flag that determines sort order of case differences during tertiary level comparisons.Possible values are:ValueDescription“upper”Uppercase sorts before lowercase.“lower”Lowercase sorts before uppercase.“off”Default value. Similar to`"lower"`with slight differences. See[http://userguide.icu-project.org/collation/customization](http://userguide.icu-project.org/collation/customization)for details of differences. |
| `numericOrdering` | boolean | Optional. Flag that determines whether to compare numeric strings as numbers or as strings.If`true`, compare as numbers; i.e.`"10"`is greater than`"2"`.If`false`, compare as strings; i.e.`"10"`is less than`"2"`.Default is`false`. |
| `alternate` | string | Optional. Field that determines whether collation should consider whitespace and punctuation as base characters for purposes of comparison.Possible values are:ValueDescription`"non-ignorable"`Whitespace and punctuation are considered base characters.`"shifted"`Whitespace and punctuation are not considered base characters and are only distinguished at strength levels greater than 3.See[ICU Collation: Comparison Levels](http://userguide.icu-project.org/collation/concepts#TOC-Comparison-Levels)for more information.Default is`"non-ignorable"`. |
| `maxVariable` | string | Optional. Field that determines up to which characters are considered ignorable when`alternate:"shifted"`. Has no effect if`alternate:"non-ignorable"`Possible values are:ValueDescription`"punct"`Both whitespaces and punctuation are “ignorable”, i.e. not considered base characters.`"space"`Whitespace are “ignorable”, i.e. not considered base characters. |
| `backwards` | boolean | Optional. Flag that determines whether strings with diacritics sort from back of the string, such as with some French dictionary ordering.If`true`, compare from back to front.If`false`, compare from front to back.The default value is`false`. |
| `normalization` | boolean | Optional. Flag that determines whether to check if text require normalization and to perform normalization. Generally, majority of text does not require this normalization processing.If`true`, check if fully normalized and perform normaliztion to compare text.If`false`, does not check.The default value is`false`.See[http://userguide.icu-project.org/collation/concepts\#TOC-Normalization](http://userguide.icu-project.org/collation/concepts#TOC-Normalization)for details. |

## Operations that Support Collation

You can specify collation for the following operations:

NOTE

You cannot specify multiple collations for an operation. For example, you cannot specify different collations per field, or if performing a find with a sort, you cannot use one collation for the find and another for the sort.

| Commands | `mongo` | Shell Methods |
| :--- | :--- | :--- |
|  | [`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create) | [`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)[`db.createView()`](https://docs.mongodb.com/manual/reference/method/db.createView/#db.createView) |
|  | [`createIndexes`](https://docs.mongodb.com/manual/reference/command/createIndexes/#dbcmd.createIndexes) | [`db.collection.createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex) |
|  | [`aggregate`](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate) | [`db.collection.aggregate()`](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate) |
|  | [`distinct`](https://docs.mongodb.com/manual/reference/command/distinct/#dbcmd.distinct) | [`db.collection.distinct()`](https://docs.mongodb.com/manual/reference/method/db.collection.distinct/#db.collection.distinct) |
|  | [`findAndModify`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify) | [`db.collection.findAndModify()`](https://docs.mongodb.com/manual/reference/method/db.collection.findAndModify/#db.collection.findAndModify)[`db.collection.findOneAndDelete()`](https://docs.mongodb.com/manual/reference/method/db.collection.findOneAndDelete/#db.collection.findOneAndDelete)[`db.collection.findOneAndReplace()`](https://docs.mongodb.com/manual/reference/method/db.collection.findOneAndReplace/#db.collection.findOneAndReplace)[`db.collection.findOneAndUpdate()`](https://docs.mongodb.com/manual/reference/method/db.collection.findOneAndUpdate/#db.collection.findOneAndUpdate) |
|  | [`find`](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find) | [`cursor.collation()`](https://docs.mongodb.com/manual/reference/method/cursor.collation/#cursor.collation)to specify collation for[`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find) |
|  | [`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) | [`db.collection.mapReduce()`](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce) |
|  | [`delete`](https://docs.mongodb.com/manual/reference/command/delete/#dbcmd.delete) | [`db.collection.deleteOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.deleteOne/#db.collection.deleteOne)[`db.collection.deleteMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.deleteMany/#db.collection.deleteMany)[`db.collection.remove()`](https://docs.mongodb.com/manual/reference/method/db.collection.remove/#db.collection.remove) |
|  | [`update`](https://docs.mongodb.com/manual/reference/command/update/#dbcmd.update) | [`db.collection.update()`](https://docs.mongodb.com/manual/reference/method/db.collection.update/#db.collection.update)[`db.collection.updateOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.updateOne/#db.collection.updateOne),[`db.collection.updateMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.updateMany/#db.collection.updateMany),[`db.collection.replaceOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.replaceOne/#db.collection.replaceOne) |
|  | [`shardCollection`](https://docs.mongodb.com/manual/reference/command/shardCollection/#dbcmd.shardCollection) | [`sh.shardCollection()`](https://docs.mongodb.com/manual/reference/method/sh.shardCollection/#sh.shardCollection) |
|  |  | Individual update, replace, and delete operations in[`db.collection.bulkWrite()`](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#db.collection.bulkWrite). |

## Behavior

### Local Variants

Some collation locales have variants, which employ special language-specific rules. To specify a locale variant, use the following syntax:

```
{ "locale" : "<locale code>@collation=<variant>" }
```

For example, to use the`pinyin`variant of the Chinese collation:

```
{ "locale" : "zh@collation=pinyin" }
```

For a complete list of all collation locales and their variants, see[Collation Locales](https://docs.mongodb.com/manual/reference/collation-locales-defaults/#collation-languages-locales).

### Collation and Views

* You can specify a default
  [collation](https://docs.mongodb.com/manual/reference/collation/#)
  for a view at creation time. If no collation is specified, the view’s default collation is the “simple” binary comparison collator. That is, the view does not inherit the collection’s default collation.
* String comparisons on the view use the view’s default collation. An operation that attempts to change or override a view’s default collation will fail with an error.
* If creating a view from another view, you cannot specify a collation that differs from the source view’s collation.
* If performing an aggregation that involves multiple views, such as with
  [`$lookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#pipe._S_lookup)
  or
  [`$graphLookup`](https://docs.mongodb.com/manual/reference/operator/aggregation/graphLookup/#pipe._S_graphLookup)
  , the views must have the same
  [collation](https://docs.mongodb.com/manual/reference/collation/#)
  .

### Collation and Index Use

To use an index for string comparisons, an operation must also specify the same collation. That is, an index with a collation cannot support an operation that performs string comparisons on the indexed fields if the operation specifies a different collation.

For example, the collection`myColl`has an index on a string field`category`with the collation locale`"fr"`.

```
db.myColl.createIndex( { category: 1 }, { collation: { locale: "fr" } } )
```

The following query operation, which specifies the same collation as the index, can use the index:

```
db.myColl.find( { category: "cafe" } ).collation( { locale: "fr" } )
```

However, the following query operation, which by default uses the “simple” binary collator, cannot use the index:

```
db.myColl.find( { category: "cafe" } )
```

For a compound index where the index prefix keys are not strings, arrays, and embedded documents, an operation that specifies a different collation can still use the index to support comparisons on the index prefix keys.

For example, the collection`myColl`has a compound index on the numeric fields`score`and`price`and the string field`category`; the index is created with the collation locale`"fr"`for string comparisons:

```
db.myColl.createIndex(
   { score: 1, price: 1, category: 1 },
   { collation: { locale: "fr" } } )
```

The following operations, which use`"simple"`binary collation for string comparisons, can use the index:

```
db.myColl.find( { score: 5 } ).sort( { price: 1 } )
db.myColl.find( { score: 5, price: { $gt: NumberDecimal( "10" ) } } ).sort( { price: 1 } )
```

The following operation, which uses`"simple"`binary collation for string comparisons on the indexed`category`field, can use the index to fulfill only the`score:5`portion of the query:

```
db.myColl.find( { score: 5, category: "cafe" } )
```



