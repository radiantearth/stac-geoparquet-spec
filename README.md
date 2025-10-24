# stac-geoparquet

A specification for storing [SpatioTemporal Asset Catalog (STAC)](https://stacspec.org) items in [GeoParquet](https://geoparquet.org/).
The specification lives at <https://github.com/radiantearth/stac-geoparquet-spec/blob/main/stac-geoparquet-spec.md>.

> [!WARNING]
> The **stac-geoparquet** specification is under development, and has not yet been released as a stable v1.
> See [this milestone](https://github.com/radiantearth/stac-geoparquet-spec/milestone/1) to track progress towards a stable release.

## Motivation

The STAC spec defines a JSON-based schema.
But it can be hard to manage and search through many millions of STAC items in JSON format.
For one, JSON is very large on disk.
And you need to parse the entire JSON data into memory to extract just a small piece of information, say the `datetime` and one `asset` of an Item.

GeoParquet can be a good complement to JSON for many bulk-access and analytic use cases.
While STAC Items are commonly distributed as individual JSON files on object storage or through a [STAC API](https://github.com/radiantearth/stac-api-spec), STAC GeoParquet allows users to access a large number of STAC items in bulk without making repeated HTTP requests.

For analytic questions like "find the items in the Sentinel-2 collection in June 2024 over New York City with cloud cover of less than 20%" it can be much, much faster to find the relevant data from a GeoParquet source than from JSON, because GeoParquet needs to load only the relevant columns for that query, not the full data.

## Development

Get [uv](https://docs.astral.sh/uv/getting-started/installation/), then:

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
