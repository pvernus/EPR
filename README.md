# GeoEPR ADM-2 Panel

This folder contains the script and outputs for a balanced ADM-2 × year panel of ethnic group political status, covering 2000–2023.

## Purpose

`script_epr_adm2_panel.qmd` links two datasets — EPR Core (which ethnic group holds which political status and when) and GeoEPR (the geographic polygons of each group's territorial base) — and intersects them against GADM 4.10 ADM-2 boundaries. For each administrative unit and year, it computes six variables capturing whether low-power (discriminated/powerless) or high-power (monopoly/dominant/senior partner) ethnic groups are present, and how much of the unit's area and population falls within their territories.

## Output

| File | Description |
|------|-------------|
| `adm2_epr_panel.rds` | Main output: balanced panel, ~1.15M rows (48,010 ADM-2 units × 24 years), 8 columns |
| `script_epr_adm2_panel.html` | Rendered report with diagnostics, plots, and robustness checks |
| `cache_adm2_spell_links.rds` | Cached ADM-2 × GeoEPR spell spatial links (skip re-render recomputation) |
| `cache_spell_intersections_sf.rds` | Cached intersection polygons with overlap areas (Mollweide) |
| `cache_pop_overlap.rds` | Cached population-weighted overlap fractions (WorldPop year 2000) |
| `epr_groups_no_geometry.csv` | Coverage audit: EPR groups without a GeoEPR polygon |

### Panel variables

| Variable | Type | Definition |
|----------|------|------------|
| `EPR_low_bin` | 0/1 | Any DISCRIMINATED or POWERLESS group overlaps this ADM-2 unit |
| `EPR_high_bin` | 0/1 | Any MONOPOLY, DOMINANT, or SENIOR PARTNER group overlaps |
| `EPR_low_pct` | [0, 1] | Share of ADM-2 area covered by low-power group territory |
| `EPR_high_pct` | [0, 1] | Share of ADM-2 area covered by high-power group territory |
| `EPR_low_pop_pct` | [0, 1] | Share of ADM-2 population living inside low-power territory |
| `EPR_high_pop_pct` | [0, 1] | Share of ADM-2 population living inside high-power territory |

## How to run

```bash
quarto render script/script_epr_adm2_panel.qmd
```

**First run:** ~20–30 minutes (builds three cache files via spatial intersection and raster extraction). Subsequent renders complete in under a minute.

To force a full recompute from scratch, set `FORCE_RECOMPUTE <- TRUE` in the `constants` chunk.

## Environment

Developed and tested with:

| Software | Version |
|----------|---------|
| R | 4.5.2 |
| Quarto | — |
| sf | 1.1.0 |
| terra | 1.8.93 |
| exactextractr | 0.10.1 |
| tidyverse | 2.0.0 |
| future / future.apply | 1.69.0 / 1.20.2 |

The rendered report includes a full `sessionInfo()` at the end for exact reproducibility auditing.

## Data sources

| Dataset | Version | Path (local) | Link |
|---------|---------|--------------|------|
| GADM | 4.10, ADM-2, cleaned | `source/gadm_410_adm2_clean.gpkg` | [gadm.org](https://gadm.org/data.html) |
| EPR Core | 2023 | `source/EPR/EPR-2023.csv` | [icr.ethz.ch](https://icr.ethz.ch/data/epr/core/beta.html) |
| GeoEPR | 2023 | `source/EPR/GeoEPR-2023.geojson` | [icr.ethz.ch](https://icr.ethz.ch/data/epr/geoepr/beta.html) |
| WorldPop | 2000, 1 km, aggregated | `source/WorldPop/ppp_2000_1km_Aggregated.tif` | [worldpop.org](https://hub.worldpop.org/geodata/summary?id=24757) |
