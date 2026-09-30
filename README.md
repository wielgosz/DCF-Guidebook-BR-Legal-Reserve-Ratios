# DCF Guidebook: Brazil state-level Legal Reserve ratios

> **Status: placeholder.** The data table has not been published yet. This repository holds
> the location for it.

A machine-readable version of **Table F-1** from *Navigating data for deforestation and conversion
free (DCF) supply chain analyses: applied learnings from soy in Brazil*, Appendix F. The table gives
Brazil's minimum Legal Reserve (Reserva Legal) ratios by GADM level-1 administrative unit (state)
and native vegetation type, as set by Art. 12 of Law No. 12,651/2012.

It is intended for geospatial developers, including users of the project's STAC catalogue
of DCF-relevant datasets (Commodity Monitoring Catalogues).

## Description

*From the guidebook, Appendix F: State level Legal Reserve Ratios*

A geospatial DCF analysis in Brazil may verify the compliance of a CAR property boundary with national forest code requirements relating to the ratio of native vegetation within a CAR that must be maintained a reserve areas.

The Brazil National Forest code or “native vegetation protection law” (Brazil federal law number 12651) defines minimum protection ratios for native vegetation areas within CAR boundaries. These ratios vary according to the state, the native vegetation type, and whether the location falls within the legal amazon boundaries (Brazil 2012).

Table F-1 can inform readers of the proportion of a CAR area required to remain as native vegetation according to the national forest code and the state environmental regularization process for a CAR to be given a verified status.

## Planned contents

| Path | Format | Purpose |
|---|---|---|
| `data/br_admin1_legal_reserve_ratios.csv` | CSV, UTF-8, comma, LF, header row | Canonical table |
| `data/br_admin1_legal_reserve_ratios.parquet` | Apache Parquet | Typed copy for dataframe and GIS pipelines |
| `datapackage.json` | Frictionless Data Package / Table Schema | Column types, units, constraints, primary key |
| `stac/collection.json` | STAC 1.1.0 Collection (table extension) | Discovery via the project STAC catalogue |

Planned conventions:

- **Join keys:** GADM **4.1** `GID_1` (for example `BRA.1_1`) as the primary join key. GADM 4.1 is
  the version the guidebook references, and it is pinned in the metadata. The IBGE state code (`CD_UF`)
  will be included as an alternative key. All codes are stored as text, never as numbers.
- **Vegetation class:** the Art. 12 classes (Legal Amazon forest / cerrado / campos gerais; rest of
  Brazil), each with its legal basis (for example `Art. 12, I, a`).
- **Ratios** are stored as decimal fractions (0-1) of rural property area, with units documented in
  the schema. Percent-formatted copies will not be published.
- **Long ("tidy") format:** one row per state × vegetation class.

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
