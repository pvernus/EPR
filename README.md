# GeoEPR ADM-2 Panel

This folder contains the scripts and outputs for a balanced ADM-2 × year panel of ethnic group political status, covering 2000–2022, plus a cross-section dataset of time-invariant unit summaries.

## Purpose

`script_epr_adm2_panel.qmd` links two datasets — EPR Core (which ethnic group holds which political status and when) and GeoEPR (the geographic polygons of each group's territorial base) — and intersects them against GADM 4.10 ADM-2 boundaries. For each administrative unit and year, it computes ten variables capturing whether low-power (discriminated/powerless) or high-power (monopoly/dominant/senior partner) ethnic groups are present, how much of the unit's area and population falls within their territories, and what share of the country's total area and population that represents.

## Workflow

Run the three scripts in order:

```bash
# Step 1 — Build the panel (~20–30 min first run; <1 min thereafter)
quarto render script/script_epr_adm2_panel.qmd

# Step 2 — Time variation diagnostics (<1 min)
quarto render script/analysis_adm2_variation.qmd

# Step 3 — Cross-section dataset (<1 min)
quarto render script/script_epr_adm2_crosssection.qmd
```

**Step 1** caches three intermediate files (spatial links, intersection polygons, population overlaps). Subsequent renders skip the expensive spatial computation and complete in under a minute. To force a full recompute from scratch, set `FORCE_RECOMPUTE <- TRUE` in the `constants` chunk.

## Output Files

| File | Description |
|------|-------------|
| `output/adm2_epr_panel.rds` | Balanced panel: 48,010 ADM-2 units × 23 years (2000–2022), 10 variables |
| `output/adm2_epr_crosssection.rds` | Cross-section: one row per ADM-2 unit, time-invariant EPR summaries |
| `output/cache_adm2_spell_links.rds` | Cached ADM-2 × GeoEPR spell spatial links |
| `output/cache_spell_intersections_sf.rds` | Cached intersection polygons with overlap areas (Mollweide) |
| `output/cache_pop_overlap.rds` | Cached population-weighted overlap fractions (WorldPop year 2000) |
| `output/cache_adm2_pop.rds` | Cached total population per ADM-2 unit (for country-relative variables) |
| `output/epr_groups_no_geometry.csv` | Coverage audit: EPR groups without a GeoEPR polygon |

## Panel Variables (`adm2_epr_panel.rds`)

### ADM-relative variables

| Variable | Type | Definition |
|----------|------|------------|
| `EPR_low_bin` | 0/1 | Any DISCRIMINATED or POWERLESS group overlaps this ADM-2 unit |
| `EPR_high_bin` | 0/1 | Any MONOPOLY, DOMINANT, or SENIOR PARTNER group overlaps |
| `EPR_low_pct` | [0, 1] | Share of ADM-2 area covered by low-power group territory |
| `EPR_high_pct` | [0, 1] | Share of ADM-2 area covered by high-power group territory |
| `EPR_low_pop_pct` | [0, 1] | Share of ADM-2 population living inside low-power territory |
| `EPR_high_pop_pct` | [0, 1] | Share of ADM-2 population living inside high-power territory |

### Country-relative variables

| Variable | Type | Definition |
|----------|------|------------|
| `EPR_low_country_area_pct` | [0, 1] | Share of country total area contributed by this unit's low-power territory |
| `EPR_high_country_area_pct` | [0, 1] | Share of country total area contributed by this unit's high-power territory |
| `EPR_low_country_pop_pct` | [0, 1] | Share of country total population contributed by this unit's low-power territory |
| `EPR_high_country_pop_pct` | [0, 1] | Share of country total population contributed by this unit's high-power territory |

Country-relative variables are additive: summing over all ADM-2 units within a country and year recovers the country-level share of territory/population under low- or high-power group control.

## Cross-Section Variables (`adm2_epr_crosssection.rds`)

One row per ADM-2 unit. All continuous variables are time-averages over 2000–2022.

| Variable | Type | Definition |
|----------|------|------------|
| `always_low` | 0/1 | Low-power group present in all 23 years |
| `always_high` | 0/1 | High-power group present in all 23 years |
| `never_low` | 0/1 | No low-power group in any year |
| `never_high` | 0/1 | No high-power group in any year |
| `ever_low` | 0/1 | Low-power group present in at least one year |
| `ever_high` | 0/1 | High-power group present in at least one year |
| `mean_low_area_pct` | [0, 1] | Mean of `EPR_low_pct` over 2000–2022 |
| `mean_high_area_pct` | [0, 1] | Mean of `EPR_high_pct` over 2000–2022 |
| `mean_low_pop_pct` | [0, 1] | Mean of `EPR_low_pop_pct` over 2000–2022 |
| `mean_high_pop_pct` | [0, 1] | Mean of `EPR_high_pop_pct` over 2000–2022 |
| `mean_low_country_area_pct` | [0, 1] | Mean of `EPR_low_country_area_pct` over 2000–2022 |
| `mean_high_country_area_pct` | [0, 1] | Mean of `EPR_high_country_area_pct` over 2000–2022 |
| `mean_low_country_pop_pct` | [0, 1] | Mean of `EPR_low_country_pop_pct` over 2000–2022 |
| `mean_high_country_pop_pct` | [0, 1] | Mean of `EPR_high_country_pop_pct` over 2000–2022 |

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

The rendered reports include a full `sessionInfo()` at the end for reproducibility auditing.

## Data Sources

| Dataset | Version | Path (local) | Link |
|---------|---------|--------------|------|
| GADM | 4.10, ADM-2, cleaned | `source/gadm_410_adm2_clean.gpkg` | [gadm.org](https://gadm.org/data.html) |
| EPR Core | 2023 | `source/EPR/EPR-2023.csv` | [icr.ethz.ch](https://icr.ethz.ch/data/epr/core/beta.html) |
| GeoEPR | 2023 | `source/EPR/GeoEPR-2023.geojson` | [icr.ethz.ch](https://icr.ethz.ch/data/epr/geoepr/beta.html) |
| WorldPop | 2000, 1 km, aggregated | `source/WorldPop/ppp_2000_1km_Aggregated.tif` | [worldpop.org](https://hub.worldpop.org/geodata/summary?id=24757) |
