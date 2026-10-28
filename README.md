# Infrastructure accessibility and inequality at the commune/ward level in Vietnam

Data and results supporting the article:

The study measures population-weighted accessibility to **economic**, **social** and **environmental** infrastructure on a 1 km × 1 km grid covering Vietnam (256,752 populated cells, 101.30 million people, 3,318 commune-level units, 34 provinces, 6 socio-economic regions), classifies communes/wards by overlapping deprivation, and measures inequality with population-weighted Gini coefficients and a nested Theil decomposition. Uncertainty is quantified with a spatial block bootstrap (B = 1,000) and 18 robustness scenarios.

## Repository structure

```
data/
  cells_1km.parquet        Cell-level indicators and composite accessibility index (CAI), 256,752 rows
  cells_1km.csv.gz         Same content as cells_1km.parquet, gzip-compressed CSV
  communes.csv             Commune/ward-level accessibility, H/M/L levels and classes, 3,321 rows
  provinces.csv            Province-level accessibility, Gini and classes, 34 rows
  accessibility_by_infrastructure.xlsx            Supplementary Data 1: accessibility by infrastructure type (communes, provinces, regions/groups)
  accessibility_by_infrastructure_communes.csv    Same content, commune/ward sheet, 3,318 rows
  accessibility_by_infrastructure_provinces.csv   Same content, province sheet (34 provinces × 10 indicators + 3 totals)
  accessibility_by_infrastructure_groups.csv      Same content, regions, urban/rural/special zones and national
  data_dictionary.csv      Description and unit of every column in the data files above
results/                   Result tables reported in the article and Supplementary Material
figures/                   Figures of the article and Supplementary Material (PNG, 300 dpi)
```

## Key definitions

| Term | Definition |
|---|---|
| Dimensions | I = economic (road density, telecommunications, night-time light, airports/seaports); II = social (healthcare, schools, shopping malls/supermarkets); III = environmental (green and blue space, water supply, waste treatment) |
| `xn_*` | Normalised indicator in [0, 1] after ln(1+x) (road density, night-time light) or ln(max(d, 500 m)) (distances) and min–max scaling; 1 = best cell in the country |
| CAI | Composite accessibility index of a dimension in a cell: entropy-weighted mean of the normalised indicators (weights computed at commune level, see `results/entropy_weights.csv`) |
| PWE | Population-weighted exposure: population-weighted mean CAI of a unit |
| r | PWE of a unit divided by the national PWE (r > 1: above the national average) |
| H/M/L | High (PWE ≥ 75th percentile), low (≤ 25th percentile), medium otherwise; unweighted percentiles over 3,318 communes/wards (or 34 provinces) |
| Profile | H/M/L levels in the order I-II-III, e.g. `M-L-L` |
| Class | I = low in all three dimensions; II = low in one or two dimensions; III = no low dimension |
| Area type | `urban` = wards; `rural` = communes; `special_zone` = island special administrative zones, reported separately for descriptive purposes only |
| Contribution | Contribution of an infrastructure type to the PWE of its dimension = entropy weight × PWE of the normalised indicator; contributions of a dimension sum to its PWE |
| `_lo`, `_hi` | Bounds of the 95% confidence interval (spatial block bootstrap, B = 1,000; 25 km blocks for national, regional and urban/rural groups, 10 km blocks stratified by province for provinces) |

Region codes: R1 Northern Midlands and Mountains; R2 Red River Delta; R3 North Central Coast; R4 South Central Coast and Central Highlands; R5 Southeast; R6 Mekong River Delta.

Administrative units follow the reorganisation effective 1 July 2025. Of the 3,321 commune-level units, three have no computation cell and are left blank in `communes.csv`: Binh Tay ward (Ho Chi Minh City), which contains no 1 km cell centroid, and the Hoang Sa and Truong Sa special zones, which are excluded owing to a lack of data.

Coordinates `x`, `y` (cell centre) and `x_pop`, `y_pop` (population-weighted centroid used as the measurement point) are in VN-2000 / UTM zone 48N (EPSG:3405); `lon`, `lat` are WGS 84 (EPSG:4326). Decimal separator is the point. CSV files are UTF-8 encoded.

In the cell-level files, CAI and normalised indicators are rounded to 4 decimals and distances to the nearest metre; PWE values recomputed from these files may therefore differ from the published values in the fourth decimal.

## Results tables (`results/`)

| File | Content | Manuscript |
|---|---|---|
| `accessibility_inequality_by_group.csv` | PWE, r and Gini [95% CI] by region, urban/rural/special zones and nationally | Fig. 1; Tables S5, S11, S15 |
| `pwe_differences_main.csv` | Urban − rural and highest − lowest region PWE differences | Section 3.1 |
| `pwe_differences_region_pairs.csv` | Paired PWE differences for all 15 region pairs × 3 dimensions | Table S6 |
| `lowest_access_region_group.csv` | Lowest-access group of regions per dimension | Section 3.1 |
| `class_distribution_by_group.csv` | Communes/wards by class (number, % communes, % population) | Tables S7, S16 |
| `profile_counts.csv` | Number of communes/wards and population by H/M/L profile | Section 3.2 |
| `hml_thresholds.csv` | 25th/75th percentile thresholds (commune and province level) | Section 2.2 |
| `gini_differences.csv` | Paired Gini differences [95% CI] | Table S12 |
| `theil_decomposition.csv` | Nested Theil T decomposition region → province → commune → cell | Table 2 |
| `entropy_weights.csv` | Entropy and weights (commune-level baseline, equal weights, cell-level) | Table S2 |
| `spearman_correlations.csv` | Population-weighted Spearman correlations between indicators at cell, commune and province level | Not shown in the article (indicator screening, Fig. S1) |
| `indicator_transformation.csv` | Transformation, min/max and normalisation diagnostics per indicator | Section 2.1 |
| `cai_summary.csv` | Distribution of cell-level CAI and agreement with weighting variants | Section 3.5; Table S14a |
| `robustness_matrix.csv` | Status of the five main conclusions under 18 scenarios | Table S13 |
| `robustness_details.csv` | Detailed values behind each robustness status | Table S13 |
| `robustness_key_numbers.csv` | National PWE, Gini, sub-provincial Theil share and class shares per scenario | Section 3.5; Table S14b |
| `robustness_weights.csv` | Indicator weights used in each scenario | Section 2.4; Table S4 |

Conclusions tested in the robustness check: C1 lowest-access group of regions; C2 rural accessibility lower than urban; C3 Gini ranking across dimensions; C4 spatial level of inequality (sub-provincial Theil share > 50%, < 50% or undetermined); C5 majority class by region. In `robustness_details.csv`, R1–R6 inside the text refer to region codes, not scenario codes.

Accessibility by infrastructure type (Supplementary Data 1, Figures S2–S3) is provided in `data/accessibility_by_infrastructure.xlsx` and the three matching CSV files, because the commune-level values are derived from OpenStreetMap data (ODbL).

## Figures (`figures/`)

| File | Manuscript |
|---|---|
| `fig1_ratio_r_by_region_urban_rural.png` | Figure 1 |
| `fig2_pwe_by_province.png` | Figure 2 |
| `fig3_commune_accessibility_classes.png` | Figure 3 |
| `fig4_lorenz_curves.png` | Figure 4 |
| `fig5_gini_by_province.png` | Figure 5 |
| `figS1_research_design.png` | Figure S1 |
| `figS2_accessibility_by_infrastructure_region.png` | Figure S2 |
| `figS3_accessibility_by_infrastructure_province.png` | Figure S3 |
| `figS4_province_hml_levels_classes.png` | Figure S4 |
| `figS5_class_shares_by_province.png` | Figure S5 |
| `extra_spearman_correlations.png` | Spearman correlations between indicators (not shown in the article) |
| `extra_cai_cell_maps.png` | Cell-level CAI maps (not shown in the article) |

## Source data

The derived data were produced from the following openly available sources, which are not redistributed here:

| Data | Source |
|---|---|
| Population 2025 (100 m) | WorldPop, https://www.worldpop.org (CC BY 4.0) |
| Roads, healthcare, schools, airports/seaports, malls/supermarkets, green and blue space, water supply, waste treatment | OpenStreetMap contributors via Geofabrik, https://download.geofabrik.de/asia/vietnam.html (ODbL 1.0) |
| Healthcare facilities and schools | Overture Maps Foundation, https://overturemaps.org |
| Telecommunications base stations | OpenCelliD (Unwired Labs), https://opencellid.org (CC BY-SA 4.0) |
| Night-time light 2025 | NOAA/EOG VIIRS DNB Annual V2.2 via Google Earth Engine |

## Licence

See [LICENSE.md](LICENSE.md): result tables and figures are released under CC BY 4.0; the derived cell-, commune- and province-level data in `data/` are released under the Open Database License (ODbL 1.0) because they are derived from OpenStreetMap data.

## Citation

Please cite the article above and this dataset:

> Ngo, T. H., & Vu, T. M. (2026). *Infrastructure accessibility and inequality at the commune/ward level in Vietnam: data and results* (Version 1.1.0) [Data set]. GitHub. https://github.com/vuthemanh444-cmd/VN-infrastructure-access

See also `CITATION.cff` (GitHub shows a “Cite this repository” button). A Zenodo DOI will be added after the first release.

## Version history

- **1.1.0** (2026-10-27): added accessibility by infrastructure type (`data/accessibility_by_infrastructure*`, Supplementary Data 1) and Figures S2–S3; renumbered figures and table references to match the current manuscript (Lorenz curves → Figure 4, provincial Gini → Figure 5, Spearman correlations moved to `extra_`); all figures regenerated from the final English versions (updated colour palette, 300 dpi).
- **1.0.0** (2026-10-21): initial release.

## Authors and contact

- **Ngo Thuy Hao** (corresponding author) — nthao@vnu.edu.vn
- **Vu The Manh**

Faculty of Development Economics, VNU University of Economics and Business, Vietnam National University, Hanoi, 100000, Vietnam.
