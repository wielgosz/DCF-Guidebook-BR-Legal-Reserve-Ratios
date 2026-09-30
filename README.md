# DCF Guidebook: Brazil state-level Legal Reserve ratios

> **Status: draft (v1.1.0-draft).** The content matches the Table F-1 draft under peer review.

A machine-readable version of **Table F-1** from *Navigating data for deforestation and conversion
free (DCF) supply chain analyses: applied learnings from soy in Brazil*, Appendix F. The table gives
Brazil's minimum Legal Reserve (Reserva Legal) ratios by GADM level-1 administrative unit (state)
and native vegetation type, as set by Art. 12 of Law No. 12,651/2012.

It is intended for geospatial developers, including users of the project's STAC catalogue
of DCF-relevant datasets (Commodity Monitoring Catalogues).

## Description

*From the guidebook, Appendix F: State level Legal Reserve Ratios*

A geospatial DCF analysis in Brazil may verify the compliance of a CAR property boundary with national forest code requirements relating to the ratio of native vegetation within a CAR that must be maintained as reserve areas.

The Brazil National Forest code or “native vegetation protection law” (Brazil federal law number 12651) defines minimum protection ratios for native vegetation areas within CAR boundaries. These ratios vary according to the state, the native vegetation type, and whether the location falls within the legal amazon boundaries (Brazil 2012).

Table F-1 can inform readers of the proportion of a CAR property area required to remain as native vegetation according to the national forest code and the state environmental regularization process for a CAR to be given a verified status.

## Contents

| Path | Format | Purpose |
|---|---|---|
| `data/br_admin1_legal_reserve_ratios.csv` | CSV, UTF-8, comma, LF, header row | Canonical table |
| `data/br_admin1_legal_reserve_ratios.parquet` | Apache Parquet | Typed copy for dataframe and GIS pipelines |
| `datapackage.json` | Frictionless Data Package / Table Schema | Column types, units, constraints, primary key |
| `stac/collection.json` | STAC 1.1.0 Collection (table extension) | Discovery via the project STAC catalogue |

Conventions:

- **Join keys:** `gid_1` holds GADM **4.1** `GID_1` codes (for example `BRA.1_1`) and is the primary join key to
  GADM 4.1 level-1 boundaries. GADM 4.1 is the version the guidebook references. `hasc_1` (GADM `HASC_1`,
  for example `BR.AC`) is a secondary GADM key. `ibge_cd_uf` (the IBGE state code) is the key for IBGE
  datasets. All codes are stored as text.
- **Primary key:** `gid_1` + `art12_region` + `vegetation_class`. The table is long ("tidy"), with one row
  per state × Art. 12 vegetation class (50 rows).
- **Ratios:** `legal_reserve_min_share` and `max_alt_land_use_share` are decimal fractions (0-1) of
  rural property area.
- **Field definitions:** see `datapackage.json` (Frictionless Table Schema) or `table:columns` in
  `stac/collection.json`.

```python
import pandas as pd
f1 = pd.read_csv("data/br_admin1_legal_reserve_ratios.csv", dtype={"ibge_cd_uf": str})
```

## Notes

a. Values are statutory minima under Art. 12 of Law No. 12,651/2012 and are Legal Reserve (Reserva
Legal) requirements, not Permanent Preservation Area (APP) requirements.

b. APP requirements (Arts. 4-6) apply in addition and may be counted towards the Reserva Legal under
Art. 15.

c. Legal Amazon rows follow the Forest Code's own definition (Law No. 12,651/2012, Art. 3, I), which
uses the 13°S parallel for Tocantins and Goiás. This differs from IBGE's official Legal Amazon
boundary (Lei Complementar 124/2007; IBGE 2024), which includes all of Tocantins and none of Goiás.

`max_alt_land_use_share` is the maximum share of rural property area eligible for alternative land
use *before* APP and other restrictions. It is not a clearing entitlement.

## How to join

Tested on 2026-09-30 against GADM 4.1 (GeoPackage and GeoJSON downloads), IBGE Biomas 1:250 000
(2025) and IBGE Amazônia Legal (2024).

1. **Join on codes, never on names.** `gid_1` = GADM `GID_1` (27/27 states match in both GADM
   downloads). GADM's own `NAME_1` differs between its GeoJSON (spaces removed, e.g. `MatoGrossodoSul`)
   and GeoPackage downloads for 10 of 27 states. GADM names the field `GID_1`, while this table uses `gid_1`.
2. **Use `ibge_cd_uf` only for IBGE data.** GADM carries no IBGE code for Brazil (`CC_1` is empty for
   all 27 states, and `ISO_1` is missing for 11).
3. **Expect 1-4 rows per state.** 17 states have 1 row, 7 have 3, and Goiás, Maranhão and Tocantins have 4.
   Filter on `art12_region` and `vegetation_class` *before* a spatial join, or the join will duplicate
   each state polygon once per row.
4. **Goiás, Tocantins and Maranhão need a spatial test.** Whether a property is in the Legal Amazon
   (Art. 3, I) depends on its position, not just its state. Build the Art. 3, I Legal Amazon as: all of AC, AM, AP,
   MT, PA, RO and RR, plus Goiás and Tocantins **north of 13°S**, plus Maranhão **west of 44°W**. Compared
   with IBGE's Legal Amazon (2024), this adds 2,915 km² of Goiás and excludes 5,415 km² of Tocantins
   (Maranhão is identical).

```python
import geopandas as gpd, pandas as pd
from shapely.geometry import box

f1 = pd.read_csv("data/br_admin1_legal_reserve_ratios.csv", dtype=str)
states = gpd.read_file("gadm41_BRA.gpkg", layer="ADM_ADM_1")      # obtain from gadm.org (see licence below)
states["uf"] = states["HASC_1"].str[3:]
full = states[states.uf.isin(["AC", "AM", "AP", "MT", "PA", "RO", "RR"])]
north13 = states[states.uf.isin(["GO", "TO"])].clip(box(-80, -13, -30, 10))
west44 = states[states.uf == "MA"].clip(box(-80, -40, -44, 10))
art3_legal_amazon = pd.concat([full, north13, west44]).dissolve()
```

## Usage notes

- **Art. 12 vegetation class is not the IBGE biome.** The Reserva Legal percentage depends on the
  vegetation physiognomy on the property (forest / cerrado / campos gerais). Take it from a vegetation
  map or from the state environmental agency's determination in CAR analysis, not from IBGE biomes.
  In testing, an IBGE-biome crosswalk could never select 15 of the 50 rows (all 10 `campos_gerais`
  rows, `cerrado` for AC, AM, AP and RR, and `forest` for GO). It also left 53,494 km² of Mato Grosso
  Pantanal inside the Legal Amazon without a class.
- **Areas and geometry.** Compute areas in an equal-area CRS (South America Albers `ESRI:102033`, or
  `EPSG:6933`). Brazil Polyconic `EPSG:5880` is not equal-area. Geodesic area is a cross-check only.
  Re-validate geometry after reprojection: IBGE Biomas 2025 is valid as published, but its Amazônia
  feature self-intersects after reprojection to `ESRI:102033` or `EPSG:5880`. `make_valid` repairs it with
  no material area change.
- **GADM licence.** This repository publishes GADM *identifiers* only. GADM geometry is "freely
  available for academic use and other non-commercial use"; redistribution or commercial use needs
  GADM's prior permission ([gadm.org/license](https://gadm.org/license.html)). Download the
  boundaries from GADM directly, and do not redistribute GADM-derived polygons such as the Art. 3, I
  Legal Amazon above.

## Source

Brazil. (2012). *Law No. 12,651 of May 25, 2012 (Native Vegetation Protection Law).* Presidency of
the Republic, Casa Civil. Retrieved January 23, 2026, from
https://www.planalto.gov.br/ccivil_03/_ato2011-2014/2012/lei/l12651.htm

## Citation

To be added on publication.

## Licence

Data and documentation are licensed under
[Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).
See [LICENSE](LICENSE).
