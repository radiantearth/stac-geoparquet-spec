# stac-geoparquet

<img src="./img/stac-geoparquet.png" alt="The stac-geoparquet logo" width=200 />

A specification for storing
[SpatioTemporal Asset Catalog (STAC)](https://stacspec.org) items in
[GeoParquet](https://geoparquet.org/). The specification lives at
<https://radiantearth.github.io/stac-geoparquet-spec>.

<!-- prettier-ignore -->
> [!WARNING]
> The **stac-geoparquet** specification is under development, and has
> not yet been released as a stable v1. See
> [this milestone](https://github.com/radiantearth/stac-geoparquet-spec/milestone/1)
> to track progress towards a stable release.

## Motivation

The STAC spec is defined in terms of JSON, but it can be hard to manage and
search through many millions of STAC items in JSON format. JSON is large on
disk, and you need to parse the entire JSON data into memory to extract just a
small piece of information, e.g. the `datetime` and one `asset` of an Item.

GeoParquet is a good complement to JSON for many bulk-access and analytic use
cases. While STAC Items are commonly distributed as individual JSON files on
object storage or through a
[STAC API](https://github.com/radiantearth/stac-api-spec), **STAC GeoParquet**
(also styled **stac-geoparquet**) allows users to access a large number of STAC
items in bulk without making repeated HTTP requests.

For analytic questions like "find the items in the Sentinel-2 collection in June
2024 over New York City with cloud cover of less than 20%" it can be much, much
faster to find the relevant data from a STAC GeoParquet source than from JSON,
because GeoParquet needs to load only the relevant columns for that query, not
the full data.

## Libraries and tools

Several tools exist to read and write **stac-geoparquet**:

- [stac-geoparquet](https://github.com/stac-utils/stac-geoparquet): The original
  reference Python implementation, **stac-geoparquet** converts STAC items
  between JSON, STAC GeoParquet, [pgstac](https://github.com/stac-utils/pgstac),
  and [Delta Lake](https://delta.io/)
- [rustac](https://github.com/stac-utils/rustac-py/): Another Python
  implementation that uses [Rust](https://github.com/stac-utils/rustac) under
  the hood and provides read+write support through
  [obstore](https://developmentseed.org/obstore/latest/), and supports API-style
  queries against STAC GeoParquet files
- [duckdb](https://duckdb.org/): Though not STAC-specific, DuckDB has excellent
  [spatial](https://duckdb.org/docs/lts/core_extensions/spatial/overview)
  support and is often used for analytics and queries against STAC GeoParquet
- [QGIS](https://qgis.org/): The state-of-the-art open-source GIS, modern QGIS
  versions ship with GeoParquet support via
  [GDAL's Parquet driver](https://gdal.org/en/stable/drivers/vector/parquet.html)

Any GeoParquet tool should work with **stac-geoparquet**; see
<https://geoparquet.org/> for a more complete listing.

## Contributing

To contribute to this specification, get
[uv](https://docs.astral.sh/uv/getting-started/installation/), then:

```sh
uv sync
npm install
```

To lint the markdown files:

```sh
npm run lint
# or, to fix errors
npm run format
```

To serve the documentation site:

```sh
uv run mkdocs serve
```

To validate the example collection metadata against the jsonschema:

```shell
uv run check-jsonschema --schemafile json-schema/metadata.json example-metadata.json
```

## History

The **stac-geoparquet** specification was split from the
[stac-utils repository](https://github.com/stac-utils/stac-geoparquet) in
October 2025. The **git** history was preserved via the following command:

```sh
git filter-repo --subdirectory-filter=spec --path LICENSE --path README.md --path docs/drawbacks.md
```
