# Comparison/Sort Order

On this page

* [Numeric Types](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#numeric-types)
* [Strings](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#strings)
* [Arrays](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#arrays)
* [Dates and Timestamps](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#dates-and-timestamps)
* [Non-existent Fields](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#non-existent-fields)
* [BinData](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#bindata)

When comparing values of different[BSON types](https://docs.mongodb.com/manual/reference/bson-types/#bson-types), MongoDB uses the following comparison order, from lowest to highest:

1. MinKey \(internal type\)
2. Null
3. Numbers \(ints, longs, doubles, decimals\)
4. Symbol, String
5. Object
6. Array
7. BinData
8. ObjectId
9. Boolean
10. Date
11. Timestamp
12. Regular Expression
13. MaxKey \(internal type\)

## Numeric Types

MongoDB treats some types as equivalent for comparison purposes. For instance, numeric types undergo conversion before comparison.

## Strings

### Binary Comparison

By default, MongoDB uses the simple binary comparison to compare strings.

### Collation

New in version 3.4.

[Collation](https://docs.mongodb.com/manual/reference/collation/)allows users to specify language-specific rules for string comparison, such as rules for lettercase and accent marks.

Collation specification has the following syntax:

```
{
locale
:
<
string
>
,
caseLevel
:
<
boolean
>
,
caseFirst
:
<
string
>
,
strength
:
<
int
>
,
numericOrdering
:
<
boolean
>
,
alternate
:
<
string
>
,
maxVariable
:
<
string
>
,
backwards
:
<
boolean
>
}
```

When specifying collation, the`locale`field is mandatory; all other collation fields are optional. For descriptions of the fields, see[Collation Document](https://docs.mongodb.com/manual/reference/collation/#collation-document-fields).

If no collation is specified for the collection or for the operations, MongoDB uses the simple binary comparison used in prior versions for string comparisons.

## Arrays

With arrays, a less-than comparison or an ascending sort compares the smallest element of arrays, and a greater-than comparison or a descending sort compares the largest element of the arrays. As such, when comparing a field whose value is a single-element array \(e.g.`[1]`\) with non-array fields \(e.g.`2`\), the comparison is between`1`and`2`. A comparison of an empty array \(e.g.`[]`\) treats the empty array as less than`null`or a missing field.

## Dates and Timestamps

Changed in version 3.0.0:Date objects sort before Timestamp objects. Previously Date and Timestamp objects sorted together.

## Non-existent Fields

The comparison treats a non-existent field as it would an empty BSON Object. As such, a sort on the`a`field in documents`{}`and`{a:null}`would treat the documents as equivalent in sort order.

## BinData

MongoDB sorts`BinData`in the following order:

1. First, the length or size of the data.
2. Then, by the BSON one-byte subtype.
3. Finally, by the data, performing a byte-by-byte comparison.



