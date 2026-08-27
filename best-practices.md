# stac-geoparquet best practices

These best practices are built on the
[best practices for distributing geoparquet](https://github.com/opengeospatial/geoparquet/blob/main/format-specs/distributing-geoparquet.md)
with specific guidance for writing **stac-geoparquet**.

## Use spatio-temporal ordering

The
[geoparquet best practices](https://github.com/opengeospatial/geoparquet/blob/main/format-specs/distributing-geoparquet.md#spatial-ordering)
document provides the following guidance around ordering:

> It is essential to make sure that the data is spatially ordered in some way
> within the file, in order for the row group statistics to be used effectively.

STAC is spatial _and_ temporal, so it follows that **stac-geoparquet** should be
spatially _and_ temporally ordered to help users make the most efficient queries possible. There are many methods for spatio-temporal
ordering; one implementation uses
[stac-hash](https://github.com/gadomski/stac-hash/) to compute a 63-bit hash
value from a STAC item's bounding box centroid and its datetime[^1]. Items
should then be sorted by this hash value.

## Prefix item ids with the spatio-temporal ordering key

A search for an item by `id` against a naively-written **stac-geoparquet** will
often perform poorly because your client needs to scan through all the data to
fulfill the query. If **stac-geoparquet** data are ordered by space and time, we
can leverage row-group statistics and predicate push-down to enable faster
search-by-id by prefixing each item id with its ordering key. Because Parquet
stores the minimum and maximum values of the `id` column, clients can use those
statistics to skip row groups that cannot contain the id.

For example, a Sentinel-2 item with a **stac-hash** id prefix might look like
`0002082da2804d36-S2B_MSIL2A_20250101T153809_R082_T01CDJ_20250101T201827`.

## Partitioning

It can be useful to write stac-geoparquet as a 
[hive-partitioned dataset](https://duckdb.org/docs/lts/data/partitioning/hive_partitioning) 
where one or multiple fields are used to separate the records into 
separate files. This can help query engines be more efficient by 
quickly scanning _file-level metadata_ that describes the range of 
values of each column in each file before loading any actual data. 

A common pattern when writing stac-geoparquet is to apply a 
monthly or yearly hive-partitioning scheme based on the `datetime` 
property then apply a spatial- or spatio-temporal sorting scheme to 
the records within each file to add structure to the row-groups.

Hive-partitioning can also simplify the process of maintaining a 
continuously updated dataset because appending new records only 
requires you to update the files/partitions that intersect the domain
of the new records. e.g. load the last month's records into a new 
monthly partition once they are available instead of re-writing the 
entire dataset.
[^1]:
    In the absence of a datetime, the midpoint of the start and end datetime are
    used
