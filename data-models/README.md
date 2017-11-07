# Data Modeling Introduction

On this page

* [Document Structure](https://docs.mongodb.com/manual/core/data-modeling-introduction/#document-structure)
* [Atomicity of Write Operations](https://docs.mongodb.com/manual/core/data-modeling-introduction/#atomicity-of-write-operations)
* [Document Growth](https://docs.mongodb.com/manual/core/data-modeling-introduction/#document-growth)
* [Data Use and Performance](https://docs.mongodb.com/manual/core/data-modeling-introduction/#data-use-and-performance)
* [Additional Resources](https://docs.mongodb.com/manual/core/data-modeling-introduction/#additional-resources)

Data in MongoDB has a_flexible schema_. Unlike SQL databases, where you must determine and declare a table’s schema before inserting data, MongoDB’s[collections](https://docs.mongodb.com/manual/reference/glossary/#term-collection)do not enforce[document](https://docs.mongodb.com/manual/reference/glossary/#term-document)structure. This flexibility facilitates the mapping of documents to an entity or an object. Each document can match the data fields of the represented entity, even if the data has substantial variation. In practice, however, the documents in a collection share a similar structure.

The key challenge in data modeling is balancing the needs of the application, the performance characteristics of the database engine, and the data retrieval patterns. When designing data models, always consider the application usage of the data \(i.e. queries, updates, and processing of the data\) as well as the inherent structure of the data itself.

## Document Structure

The key decision in designing data models for MongoDB applications revolves around the structure of documents and how the application represents relationships between data. There are two tools that allow applications to represent these relationships:_references_and_embedded documents_.

### References

References store the relationships between data by including links or_references_from one document to another. Applications can resolve these[references](https://docs.mongodb.com/manual/reference/database-references/)to access the related data. Broadly, these are_normalized_data models.

![](https://docs.mongodb.com/manual/_images/data-model-normalized.bakedsvg.svg "Data model using references to link documents. Both the \`\`contact\`\` document and the \`\`access\`\` document contain a reference to the \`\`user\`\` document.")

See[Normalized Data Models](https://docs.mongodb.com/manual/core/data-model-design/#data-modeling-referencing)for the strengths and weaknesses of using references.

### Embedded Data

Embedded documents capture relationships between data by storing related data in a single document structure. MongoDB documents make it possible to embed document structures in a field or array within a document. These_denormalized_data models allow applications to retrieve and manipulate related data in a single database operation.

![](https://docs.mongodb.com/manual/_images/data-model-denormalized.bakedsvg.svg "Data model with embedded fields that contain all related information.")

See[Embedded Data Models](https://docs.mongodb.com/manual/core/data-model-design/#data-modeling-embedding)for the strengths and weaknesses of embedding documents.

## Atomicity of Write Operations

In MongoDB, write operations are atomic at the[document](https://docs.mongodb.com/manual/reference/glossary/#term-document)level, and no single write operation can atomically affect more than one document or more than one collection. A denormalized data model with embedded data combines all related data for a represented entity in a single document. This facilitates atomic write operations since a single write operation can insert or update the data for an entity. Normalizing the data would split the data across multiple collections and would require multiple write operations that are not atomic collectively.

However, schemas that facilitate atomic writes may limit ways that applications can use the data or may limit ways to modify applications. The[Atomicity Considerations](https://docs.mongodb.com/manual/core/data-model-operations/#data-model-atomicity)documentation describes the challenge of designing a schema that balances flexibility and atomicity.

## Document Growth

Some updates, such as pushing elements to an array or adding new fields, increase a[document’s](https://docs.mongodb.com/manual/reference/glossary/#term-document)size.

For the MMAPv1 storage engine, if the document size exceeds the allocated space for that document, MongoDB relocates the document on disk. When using the MMAPv1 storage engine, growth consideration can affect the decision to normalize or denormalize data. See[Document Growth Considerations](https://docs.mongodb.com/manual/core/data-model-operations/#data-model-document-growth)for more about planning for and managing document growth for MMAPv1.

## Data Use and Performance

When designing a data model, consider how applications will use your database. For instance, if your application only uses recently inserted documents, consider using[Capped Collections](https://docs.mongodb.com/manual/core/capped-collections/). Or if your application needs are mainly read operations to a collection, adding indexes to support common queries can improve performance.

See[Operational Factors and Data Models](https://docs.mongodb.com/manual/core/data-model-operations/)for more information on these and other operational considerations that affect data model designs.

## Additional Resources

* [Thinking in Documents Part 1 \(Blog Post\)](https://www.mongodb.com/blog/post/thinking-documents-part-1?jmp=docs)



