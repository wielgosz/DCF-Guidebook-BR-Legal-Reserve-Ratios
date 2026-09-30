# DCF Guidebook: Brazil state-level Legal Reserve ratios

> **Status: draft (v1.0.0-draft).** The content matches the Table F-1 draft under peer review.

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
  GADM 4.1 level-1 boundaries. GADM 4.1 is the version the guidebook references. `ibge_cd_uf` (the IBGE
  state code) is an alternative key. All codes are stored as text.
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
