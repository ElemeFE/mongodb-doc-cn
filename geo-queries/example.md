# Find Restaurants with Geospatial Queries

On this page

* [Overview](https://docs.mongodb.com/manual/tutorial/geospatial-tutorial/#overview)
* [Distortion](https://docs.mongodb.com/manual/tutorial/geospatial-tutorial/#distortion)
* [Searching for Restaurants](https://docs.mongodb.com/manual/tutorial/geospatial-tutorial/#searching-for-restaurants)

## Overview

MongoDB’s[geospatial](https://docs.mongodb.com/manual/reference/glossary/#term-geospatial)indexing allows you to efficiently execute spatial queries on a collection that contains geospatial shapes and points. This tutorial will briefly introduce the concepts of geospatial indexes, and then demonstrate their use with[`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin),[`$geoIntersects`](https://docs.mongodb.com/manual/reference/operator/query/geoIntersects/#op._S_geoIntersects), and[`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear).

To showcase the capabilities of geospatial features and compare different approaches, this tutorial will guide you through the process of writing queries for a simple geospatial application.

Suppose you are designing a mobile application to help users find restaurants in New York City. The application must:

* Determine the user’s current neighborhood using
  [`$geoIntersects`](https://docs.mongodb.com/manual/reference/operator/query/geoIntersects/#op._S_geoIntersects)
  ,
* Show the number of restaurants in that neighborhood using
  [`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin)
  , and
* Find restaurants within a specified distance of the user using
  [`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere)
  .

This tutorial will use a`2dsphere`index to query for this data on spherical geometry.

For more information on spherical and flat geometries, see[Geospatial Models](https://docs.mongodb.com/manual/geospatial-queries/#geospatial-geometry).

## Distortion

Spherical geometry will appear distorted when visualized on a map due to the nature of projecting a three dimensional sphere, such as the earth, onto a flat plane.

For example, take the specification of the spherical square defined by the longitude latitude points`(0,0)`,`(80,0)`,`(80,80)`, and`(0,80)`. The following figure depicts the area covered by this region:

![](https://docs.mongodb.com/manual/_images/geospatial-spherical-square.png "Diagram of a square projected onto a sphere.")

## Searching for Restaurants

### Prerequisites

Download the example datasets from[https://raw.githubusercontent.com/mongodb/docs-assets/geospatial/neighborhoods.json](https://raw.githubusercontent.com/mongodb/docs-assets/geospatial/neighborhoods.json)and[https://raw.githubusercontent.com/mongodb/docs-assets/geospatial/restaurants.json](https://raw.githubusercontent.com/mongodb/docs-assets/geospatial/restaurants.json). These contain the collections`restaurants`and`neighborhoods`respectively.

After downloading the datasets, import them into the database:

```
mongoimport
<
path
to
restaurants
.
json
>
-
c
restaurants
mongoimport
<
path
to
neighborhoods
.
json
>
-
c
neighborhoods
```

The[`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear)command requires a geospatial index, and almost always improves performance of[`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin)and[`$geoIntersects`](https://docs.mongodb.com/manual/reference/operator/query/geoIntersects/#op._S_geoIntersects)queries.

Because this data is geographical, create a`2dsphere`index on each collection using the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell:

```
db
.
restaurants
.
createIndex
({
location
:
"2dsphere"
})
db
.
neighborhoods
.
createIndex
({
geometry
:
"2dsphere"
})
```

### Exploring the Data

Inspect an entry in the newly-created`restaurants`collection from within the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell:

```
db
.
restaurants
.
findOne
()
```

This query returns a document like the following:

```
{
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
},
name
:
"Morris Park Bake Shop"
}
```

This restaurant document corresponds to the location shown in the following figure:

![](https://docs.mongodb.com/manual/_images/geospatial-single-point.png "Map of a single geospatial point.")

Because the tutorial uses a`2dsphere`index, the geometry data in the`location`field must follow the[GeoJSON format](https://docs.mongodb.com/manual/reference/geojson/).

Now inspect an entry in the`neighborhoods`collection:

```
db
.
neighborhoods
.
findOne
()
```

This query will return a document like the following:

```
{
geometry
:
{
type
:
"Polygon"
,
coordinates
:
[[
[
-
73.99
,
40.75
],
...
[
-
73.98
,
40.76
],
[
-
73.99
,
40.75
]
]]
},
name
:
"Hell's Kitchen"
}
```

This geometry corresponds to the region depicted in the following figure:

![](https://docs.mongodb.com/manual/_images/geospatial-polygon-hells-kitchen.png "Map of a geospatial polygon.")

### Find the Current Neighborhood

Assuming the user’s mobile device can give a reasonably accurate location for the user, it is simple to find the user’s current neighborhood with[`$geoIntersects`](https://docs.mongodb.com/manual/reference/operator/query/geoIntersects/#op._S_geoIntersects).

Suppose the user is located at -73.93414657 longitude and 40.82302903 latitude. To find the current neighborhood, you will specify a point using the special[`$geometry`](https://docs.mongodb.com/manual/reference/operator/query/geometry/#op._S_geometry)field in[GeoJSON](https://docs.mongodb.com/manual/reference/glossary/#term-geojson)format:

```
db
.
neighborhoods
.
findOne
({
geometry
:
{
$geoIntersects
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
73.93414657
,
40.82302903
]
}
}
}
})
```

This query will return the following result:

```
{
"_id"
:
ObjectId
(
"55cb9c666c522cafdb053a68"
),
"geometry"
:
{
"type"
:
"Polygon"
,
"coordinates"
:
[
[
[
-
73.93383000695911
,
40.81949109558767
],
...
]
]
},
"name"
:
"Central Harlem North-Polo Grounds"
}
```

### Find all Restaurants in the Neighborhood

You can also query to find all restaurants contained in a given neighborhood. Run the following in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell to find the neighborhood containing the user, and then count the restaurants within that neighborhood:

```
var
neighborhood
=
db
.
neighborhoods
.
findOne
(
{
geometry
:
{
$geoIntersects
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
73.93414657
,
40.82302903
]
}
}
}
}
)
db
.
restaurants
.
find
(
{
location
:
{
$geoWithin
:
{
$geometry
:
neighborhood
.
geometry
}
}
}
).
count
()
```

This query will tell you that there are 127 restaurants in the requested neighborhood, visualized in the following figure:

![](https://docs.mongodb.com/manual/_images/geospatial-all-restaurants.png "Map of all restaurants in a geospatial polygon.")

### Find Restaurants within a Distance

To find restaurants within a specified distance of a point, you can use either[`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin)with[`$centerSphere`](https://docs.mongodb.com/manual/reference/operator/query/centerSphere/#op._S_centerSphere)to return results in unsorted order, or`nearSphere`with[`$maxDistance`](https://docs.mongodb.com/manual/reference/operator/query/maxDistance/#op._S_maxDistance)if you need results sorted by distance.

### Unsorted with`$geoWithin`

To find restaurants within a circular region, use[`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin)with[`$centerSphere`](https://docs.mongodb.com/manual/reference/operator/query/centerSphere/#op._S_centerSphere).[`$centerSphere`](https://docs.mongodb.com/manual/reference/operator/query/centerSphere/#op._S_centerSphere)is a MongoDB-specific syntax to denote a circular region by specifying the center and the radius in radians.

[`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin)does not return the documents in any specific order, so it may show the user the furthest documents first.

The following will find all restaurants within five miles of the user:

```
db
.
restaurants
.
find
({
location
:
{
$geoWithin
:
{
$centerSphere
:
[
[
-
73.93414657
,
40.82302903
],
5
/
3963.2
]
}
}
})
```

[`$centerSphere`](https://docs.mongodb.com/manual/reference/operator/query/centerSphere/#op._S_centerSphere)’s second argument accepts the radius in radians, so you must divide it by the radius of the earth in miles. See[Calculate Distance Using Spherical Geometry](https://docs.mongodb.com/manual/tutorial/calculate-distances-using-spherical-geometry-with-2d-geospatial-indexes/)for more information on converting between distance units.

### Sorted with`$nearSphere`

You may also use[`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere)and specify a[`$maxDistance`](https://docs.mongodb.com/manual/reference/operator/query/maxDistance/#op._S_maxDistance)term in meters. This will return all restaurants within five miles of the user in sorted order from nearest to farthest:

```
var
METERS_PER_MILE
=
1609.34
db
.
restaurants
.
find
({
location
:
{
$nearSphere
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
73.93414657
,
40.82302903
]
},
$maxDistance
:
5
*
METERS_PER_MILE
}
}
})
```



