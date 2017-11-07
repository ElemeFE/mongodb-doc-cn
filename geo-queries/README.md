# Geospatial Queries

On this page

* [Geospatial Data](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-data)
* [Geospatial Indexes](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-indexes)
* [Geospatial Queries](https://docs.mongodb.com/manual/geospatial-queries/#id1)
* [Geospatial Models](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-models)
* [Example](https://docs.mongodb.com/manual/geospatial-queries/#example)

MongoDB supports query operations on geospatial data. This section introduces MongoDB’s geospatial features.

## Geospatial Data

In MongoDB, you can store geospatial data as[GeoJSON](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geojson)objects or as[legacy coordinate pairs](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-legacy).

### GeoJSON Objects

To calculate geometry over an Earth-like sphere, store your location data as[GeoJSON objects](https://docs.mongodb.com/manual/reference/geojson/).

To specify GeoJSON data, use an embedded document with:

* a field named`type`that specifies the[GeoJSON object type](https://docs.mongodb.com/manual/reference/geojson/)and

* a field named`coordinates`that specifies the object’s coordinates.

  If specifying latitude and longitude coordinates, list the**longitude**first and then**latitude**:

  * Valid longitude values are between
    `-180`
    and
    `180`
    , both inclusive.
  * Valid latitude values are between
    `-90`
    and
    `90`
    \(both inclusive\).

```
<
field
>
:
{
type
:
<
GeoJSON
type
>
,
coordinates
:
<
coordinates
>
}
```

For example, to specify a[GeoJSON Point](https://docs.mongodb.com/manual/reference/geojson/#geojson-point):

```
location
:
{
type
:
"Point"
,
coordinates
:
[
-
73.856077
,
40.848447
]
}
```

For a list of the GeoJSON objects supported in MongoDB as well as examples, see[GeoJSON objects](https://docs.mongodb.com/manual/reference/geojson/).

MongoDB geospatial queries on GeoJSON objects calculate on a sphere; MongoDB uses the[WGS84](https://docs.mongodb.com/manual/reference/glossary/#term-wgs84)reference system for geospatial queries on GeoJSON objects.

### Legacy Coordinate Pairs

To calculate distances on a Euclidean plane, store your location data as legacy coordinate pairs and use a[2d](https://docs.mongodb.com/manual/geospatial-queries/#geo-2d)index. MongoDB supports spherical surface calculations on legacy coordinate pairs via a[2dsphere](https://docs.mongodb.com/manual/geospatial-queries/#geo-2dsphere)index by converting the data to the GeoJSON Point type.

To specify data as legacy coordinate pairs, you can use either an array \(_preferred_\) or an embedded document.

Specify via an array \(

_Preferred_

\):

```
<
field
>
:
[
<
x
>
,
<
y
>
]
```

If specifying latitude and longitude coordinates, list the**longitude**first and then**latitude**; i.e.

```
<
field
>
:
[
<
longitude
>
,
<
latitude
>
]
```

If specifying latitude and longitude coordinates, list the**longitude**first and then**latitude**:

* Valid longitude values are between
  `-180`
  and
  `180`
  , both inclusive.
* Valid latitude values are between
  `-90`
  and
  `90`
  \(both inclusive\).

Specify via an embedded document:

```
<
field
>
:
{
<
field1
>
:
<
x
>
,
<
field2
>
:
<
y
>
}
```

If specifying latitude and longitude coordinates, the first field, regardless of the field name, must contains the**longitude**value and the second field, the**latitude**value ; i.e.

```
<
field
>
:
{
<
field1
>
:
<
longitude
>
,
<
field2
>
:
<
latitude
>
}
```

* Valid longitude values are between
  `-180`
  and
  `180`
  , both inclusive.
* Valid latitude values are between
  `-90`
  and
  `90`
  \(both inclusive\).

To specify legacy coordinate pairs, arrays are preferred over an embedded document as some languages do not guarantee associative map ordering.

## Geospatial Indexes

MongoDB provides the following geospatial index types to support the geospatial queries.

### `2dsphere`

[2dsphere](https://docs.mongodb.com/manual/core/2dsphere/)indexes support queries that calculate[geometries on an earth-like sphere](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geometry).

To create a`2dsphere`index, use the[`db.collection.createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex)method and specify the string literal`"2dsphere"`as the index type:

```
db
.
collection
.
createIndex
(
{
<
location
field
>
:
"2dsphere"
}
)
```

where the`<locationfield>`is a field whose value is either a[GeoJSON object](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geojson)or a[legacy coordinates pair](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-legacy).

For more information on the`2dsphere`index, see[2dsphere Indexes](https://docs.mongodb.com/manual/core/2dsphere/).

### `2d`

[2d](https://docs.mongodb.com/manual/core/2d/)indexes support queries that calculate[geometries on a two-dimensional plane](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geometry). Although the index can support[`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere)queries that calculate on a sphere, if possible, use the[2dsphere](https://docs.mongodb.com/manual/geospatial-queries/#geo-2dsphere)index for spherical queries.

To create a`2d`index, use the[`db.collection.createIndex()`](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex)method, specifying the location field as the key and the string literal`"2d"`as the index type:

```
db
.
collection
.
createIndex
(
{
<
location
field
>
:
"2d"
}
)
```

where the`<locationfield>`is a field whose value is a[legacy coordinates pair](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-legacy).

For more information on the`2d`index, see[2d Indexes](https://docs.mongodb.com/manual/core/2d/).

### Geospatial Indexes and Sharded Collections

You cannot use a geospatial index as a[shard key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key)when sharding a collection. However, you can create a geospatial index on a sharded collection by using a different field as the shard key.

For sharded collections, queries using[`$near`](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near)and[`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere)are not supported. You can instead use either the[`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear)command or the[`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear)aggregation stage.

You can also query for geospatial data for a sharded cluster using[`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin)and`$geoIntersect`.

### Covered Queries

A[geospatial indexes](https://docs.mongodb.com/manual/geospatial-queries/#index-feature-geospatial)cannot[cover a query](https://docs.mongodb.com/manual/core/query-optimization/#covered-queries).

## Geospatial Queries

NOTE

For spherical queries, use the`2dsphere`index result.

The use of`2d`index for spherical queries may lead to incorrect results, such as the use of the`2d`index for spherical queries that wrap around the poles.

### Geospatial Query Operators

MongoDB provides the following geospatial query operators:

| Name | Description |
| :--- | :--- |
| [`$geoIntersects`](https://docs.mongodb.com/manual/reference/operator/query/geoIntersects/#op._S_geoIntersects) | Selects geometries that intersect with a[GeoJSON](https://docs.mongodb.com/manual/reference/glossary/#term-geojson)geometry. The[2dsphere](https://docs.mongodb.com/manual/core/2dsphere/)index supports[`$geoIntersects`](https://docs.mongodb.com/manual/reference/operator/query/geoIntersects/#op._S_geoIntersects). |
| [`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin) | Selects geometries within a bounding[GeoJSON geometry](https://docs.mongodb.com/manual/reference/geojson/#geospatial-indexes-store-geojson). The[2dsphere](https://docs.mongodb.com/manual/core/2dsphere/)and[2d](https://docs.mongodb.com/manual/core/2d/)indexes support[`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin). |
| [`$near`](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near) | Returns geospatial objects in proximity to a point. Requires a geospatial index. The[2dsphere](https://docs.mongodb.com/manual/core/2dsphere/)and[2d](https://docs.mongodb.com/manual/core/2d/)indexes support[`$near`](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near). |
| [`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere) | Returns geospatial objects in proximity to a point on a sphere. Requires a geospatial index. The[2dsphere](https://docs.mongodb.com/manual/core/2dsphere/)and[2d](https://docs.mongodb.com/manual/core/2d/)indexes support[`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere). |

For more details, including examples, see the individual reference page.

### Geospatial Command

MongoDB provides the following geospatial command:

| Command | Description |
| :--- | :--- |
| [`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear) | Performs a geospatial query that returns the documents closest to a given point.[`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear)requires a[geospatial index](https://docs.mongodb.com/manual/core/geospatial-indexes/). |

For more details, including examples, see[`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear)reference page.

### Geospatial Aggregation Stage

MongoDB provides the following geospatial[aggregation pipeline stage](https://docs.mongodb.com/manual/core/aggregation-pipeline/):

| Stage | Description |
| :--- | :--- |
| [`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear) | Returns an ordered stream of documents based on the proximity to a geospatial point. Incorporates the functionality of[`$match`](https://docs.mongodb.com/manual/reference/operator/aggregation/match/#pipe._S_match),[`$sort`](https://docs.mongodb.com/manual/reference/operator/aggregation/sort/#pipe._S_sort), and[`$limit`](https://docs.mongodb.com/manual/reference/operator/aggregation/limit/#pipe._S_limit)for geospatial data. The output documents include an additional distance field and can include a location identifier field.[`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear)requires a[geospatial index](https://docs.mongodb.com/manual/core/geospatial-indexes/). |

For more details, including examples, see[`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear)reference page.

## Geospatial Models

MongoDB geospatial queries can interpret geometry on a flat surface or a sphere.

`2dsphere`indexes support only spherical queries \(i.e. queries that interpret geometries on a spherical surface\).

`2d`indexes support flat queries \(i.e. queries that interpret geometries on a flat surface\) and some spherical queries. While`2d`indexes support some spherical queries, the use of`2d`indexes for these spherical queries can result in error. If possible, use`2dsphere`indexes for spherical queries.

The following table lists the geospatial query operators, supported query, used by each geospatial operations:

| Operation | Spherical/Flat Query | Notes |
| :--- | :--- | :--- |
| [`$near`](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near)\([GeoJSON](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geojson)centroid point in this line and the following line,[2dsphere](https://docs.mongodb.com/manual/geospatial-queries/#geo-2dsphere)index\) | Spherical | See also the[`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere)operator, which provides the same functionality when used with[GeoJSON](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geojson)and a[2dsphere](https://docs.mongodb.com/manual/geospatial-queries/#geo-2dsphere)index. |
| [`$near`](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near)\([legacy coordinates](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-legacy),[2d](https://docs.mongodb.com/manual/geospatial-queries/#geo-2d)index\) | Flat |  |
| [`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere)\([GeoJSON](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geojson)point,[2dsphere](https://docs.mongodb.com/manual/geospatial-queries/#geo-2dsphere)index\) | Spherical | Provides the same functionality as[`$near`](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near)operation that uses[GeoJSON](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geojson)point and a[2dsphere](https://docs.mongodb.com/manual/geospatial-queries/#geo-2dsphere)index.For spherical queries, it may be preferable to use[`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere)which explicitly specifies the spherical queries in the name rather than[`$near`](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near)operator. |
| [`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere)\([legacy coordinates](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-legacy),[2d](https://docs.mongodb.com/manual/geospatial-queries/#geo-2d)index\) | Spherical | Use[GeoJSON](https://docs.mongodb.com/manual/reference/glossary/#term-geojson)points instead. |
| [`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin): {[`$geometry`](https://docs.mongodb.com/manual/reference/operator/query/geometry/#op._S_geometry): … } | Spherical |  |
| [`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin): {[`$box`](https://docs.mongodb.com/manual/reference/operator/query/box/#op._S_box): … } | Flat |  |
| [`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin): {[`$polygon`](https://docs.mongodb.com/manual/reference/operator/query/polygon/#op._S_polygon): … } | Flat |  |
| [`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin): {[`$center`](https://docs.mongodb.com/manual/reference/operator/query/center/#op._S_center): … } | Flat |  |
| [`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin): {[`$centerSphere`](https://docs.mongodb.com/manual/reference/operator/query/centerSphere/#op._S_centerSphere): … } | Spherical |  |
| [`$geoIntersects`](https://docs.mongodb.com/manual/reference/operator/query/geoIntersects/#op._S_geoIntersects) | Spherical |  |
| [`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear)\([2dsphere](https://docs.mongodb.com/manual/geospatial-queries/#geo-2dsphere)index\) | Spherical |  |
| [`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear)\([2d](https://docs.mongodb.com/manual/geospatial-queries/#geo-2d)index\) | Flat |  |
| [`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear)\([2dsphere](https://docs.mongodb.com/manual/geospatial-queries/#geo-2dsphere)index\) | Spherical |  |
| [`$geoNear`](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/#pipe._S_geoNear)\([2d](https://docs.mongodb.com/manual/geospatial-queries/#geo-2d)index\) | Flat |  |

## Example

Create a collection`places`with the following documents:

```
db
.
places
.
insert
(
{
name
:
"Central Park"
,
location
:
{
type
:
"Point"
,
coordinates
:
[
-
73.97
,
40.77
]
},
category
:
"Parks"
}
);
db
.
places
.
insert
(
{
name
:
"Sara D. Roosevelt Park"
,
location
:
{
type
:
"Point"
,
coordinates
:
[
-
73.9928
,
40.7193
]
},
category
:
"Parks"
}
);
db
.
places
.
insert
(
{
name
:
"Polo Grounds"
,
location
:
{
type
:
"Point"
,
coordinates
:
[
-
73.9375
,
40.8303
]
},
category
:
"Stadiums"
}
);
```

The following operation creates a`2dsphere`index on the`location`field:

```
db
.
places
.
createIndex
(
{
location
:
"2dsphere"
}
)
```

The following query uses the[`$near`](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near)operator to return documents that are at least 1000 meters from and at most 5000 meters from the specified GeoJSON point, sorted in order from nearest to farthest:

```
db
.
places
.
find
(
{
location
:
{
$near
:
{
$geometry
:
{
type
:
"Point"
,
coordinates
:
[
-
73.9667
,
40.78
]
},
$minDistance
:
1000
,
$maxDistance
:
5000
}
}
}
)
```

The following operation uses the[`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear)command to return documents that match the query filter`{category:"Parks"}`, sorted in order of nearest to farthest to the specified GeoJSON point:

```
db
.
runCommand
(
{
geoNear
:
"places"
,
near
:
{
type
:
"Point"
,
coordinates
:
[
-
73.9667
,
40.78
]
},
spherical
:
true
,
query
:
{
category
:
"Parks"
}
}
)
```

  


