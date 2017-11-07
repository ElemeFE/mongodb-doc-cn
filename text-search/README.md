# Text Search

On this page

* [Overview](https://docs.mongodb.com/manual/text-search/#overview)
* [Example](https://docs.mongodb.com/manual/text-search/#example)
* [Language Support](https://docs.mongodb.com/manual/text-search/#language-support)

## Overview

MongoDB supports query operations that perform a text search of string content. To perform text search, MongoDB uses a[text index](https://docs.mongodb.com/manual/core/index-text/#index-feature-text)and the[`$text`](https://docs.mongodb.com/manual/reference/operator/query/text/#op._S_text)operator.

NOTE

[Views](https://docs.mongodb.com/manual/core/views/)do not support text search.

## Example

This example demonstrates how to build a text index and use it to find coffee shops, given only text fields.

Create a collection`stores`with the following documents:

```js
db.stores.insert(
   [
     { _id: 1, name: "Java Hut", description: "Coffee and cakes" },
     { _id: 2, name: "Burger Buns", description: "Gourmet hamburgers" },
     { _id: 3, name: "Coffee Shop", description: "Just coffee" },
     { _id: 4, name: "Clothes Clothes Clothes", description: "Discount clothing" },
     { _id: 5, name: "Java Shopping", description: "Indonesian goods" }
   ]
)
```

### Text Index

MongoDB provides[text indexes](https://docs.mongodb.com/manual/core/index-text/#index-feature-text)to support text search queries on string content.`text`indexes can include any field whose value is a string or an array of string elements.

To perform text search queries, you must have a`text`index on your collection. A collection can only have**one**text search index, but that index can cover multiple fields.

For example you can run the following in a[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell to allow text search over the`name`and`description`fields:db

```js
db.stores.createIndex( { name: "text", description: "text" } )
```

### `$text`Operator

Use the[`$text`](https://docs.mongodb.com/manual/reference/operator/query/text/#op._S_text)query operator to perform text searches on a collection with a[text index](https://docs.mongodb.com/manual/core/index-text/#index-feature-text).

[`$text`](https://docs.mongodb.com/manual/reference/operator/query/text/#op._S_text)will tokenize the search string using whitespace and most punctuation as delimiters, and perform a logical`OR`of all such tokens in the search string.

For example, you could use the following query to find all stores containing any terms from the list “coffee”, “shop”, and “java”:

```js
db.stores.find( { $text: { $search: "java coffee shop" } } )
```

#### Exact Phrase

You can also search for exact phrases by wrapping them in double-quotes. For example, the following will find all documents containing “java” or “coffee shop”:

```js
db.stores.find( { $text: { $search: "java \"coffee shop\"" } } )
```

#### Term Exclusion

To exclude a word, you can prepend a “`-`” character. For example, to find all stores containing “java” or “shop” but not “coffee”, use the following:db

```js
db.stores.find( { $text: { $search: "java shop -coffee" } } )
```

#### Sorting

MongoDB will return its results in unsorted order by default. However, text search queries will compute a relevance score for each document that specifies how well a document matches the query.

To sort the results in order of relevance score, you must explicitly project the[`$meta`](https://docs.mongodb.com/manual/reference/operator/projection/meta/#proj._S_meta)`textScore`field and sort on it:

```js
db.stores.find(
   { $text: { $search: "java coffee shop" } },
   { score: { $meta: "textScore" } }
).sort( { score: { $meta: "textScore" } } )
```

Text search is also available in the aggregation pipeline.

## Language Support

MongoDB supports text search for various languages. See[Text Search Languages](https://docs.mongodb.com/manual/reference/text-search-languages/)for a list of supported languages.

