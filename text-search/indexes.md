# Text Indexes

MongoDB provides[text indexes](https://docs.mongodb.com/manual/core/index-text/#index-feature-text)to support text search queries on string content.`text`indexes can include any field whose value is a string or an array of string elements.

To perform text search queries, you must have a`text`index on your collection. A collection can only have**one**text search index, but that index can cover multiple fields.

For example you can run the following in a[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell to allow text search over the`name`and`description`fields:

```js
db.stores.createIndex( { name: "text", description: "text" } )

```

See the[Text Indexes](https://docs.mongodb.com/manual/core/index-text/)section for a full reference on text indexes, including behavior, tokenization, and properties.

  


