# DCF Guidebook: Brazil APP ratios by admin-1 and biome

> **Status: placeholder.** The data table has not been published yet. This repository holds
> the location for it.

A machine-readable version of Table F1 in *Navigating DCF Data Guidebook*. The table
gives Brazil's legal Área de Preservação Permanente (APP) ratios, broken down by GADM level-1 administrative unit (state) and biome.

It is intended for geospatial developers, including users of the project's STAC catalogue
of DCF-relevant datasets (Commodity Monitoring Catalogues).

## Planned contents

| Path | Format | Purpose |
|---|---|---|
| `data/br_admin1_biome_app_ratios.csv` | CSV, UTF-8, comma, LF, header row | Canonical table |
| `data/br_admin1_biome_app_ratios.parquet` | Apache Parquet | Typed copy for dataframe and GIS pipelines |
| `datapackage.json` | Frictionless Data Package / Table Schema | Column types, units, constraints, primary key |
| `stac/collection.json` | STAC 1.1.0 Collection (table extension) | Discovery via the project STAC catalogue |

Planned conventions:

- **Join keys:** GADM `GID_1` (for example `BRA.1_1`) as the primary join key, with the GADM
  version pinned in the metadata. The IBGE state code (`CD_UF`) and IBGE biome name will be
  included as alternative keys. All codes are stored as text, never as numbers.
- **Ratios** are stored as decimal fractions (0-1), with units documented in the schema.
  Percent-formatted copies will not be published.
- **Long ("tidy") format:** one row per admin-1 × biome combination.
- Legal basis (Lei 12.651/2012, the Código Florestal) and the guidebook citation will be recorded in
  the metadata.

## Citation

To be added on publication.

## Licence

To be confirmed before publication.
