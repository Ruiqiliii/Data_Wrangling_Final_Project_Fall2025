# Data_Wrangling_Final_Project_Fall2025

> **Topic.** Maternal vaccination (Flu & Tdap) at the **state–year** level, and how **uninsurance** and **urbanicity (NCHS)** relate to coverage.  

## Team
- **Team name:** Wrangle Avengers  
- **Members:** Mengying Xia, Ruining Zhou, Xinyi Yu, Ruiqi Li  
- **Course:** QBS 181 / Data Wrangling (Fall 2025)  
- **Instructor:** Carly Bobak

---

## Repository structure

```
.
├── Aim2.2&2.3/                           # Code & figs for Aim 2.2–2.3 (TWFE, interactions)
├── Aim 1.2 Code.Rmd                      # Code for Aim 1.2
├── Aim1.1_Final_Project_GIS_MXIA.Rmd     # GIS overview (choropleths/maps)
├── Data Approval.pdf                      # Data approval (project requirement)
├── DataCleaning&Aim2.1.Rmd                # Data cleaning + Aim 2.1 raw analyses
├── DataCleaning-Aim2.1.pdf                # Knit output of the above
├── MergeClarification.csv                 # Merge keys / sanity checks (doc table)
├── MergedVersion.csv                      # Processed + merged analytic table (state–year)
├── RawData.zip                            # Original raw files (zipped for size)
├── Vaccination_Coverage_among_Pregnant_W....csv  # Processed vaccination input
├── insurance_summary_by_state_2012_2022.csv      # Processed insurance input
├── state_urban_index_2013_2023.csv               # Processed NCHS (state-level)
├── vaccination.csv                        # Final processed/merged data (used by models)
├── README.md
└── .gitignore / .Rhistory
```

---

## Quick start (reproducibility)

1. **Environment**
   - R ≥ 4.3 (RStudio recommended)
   - Packages (install as needed):  
     `tidyverse`, `readr`, `dplyr`, `tidyr`, `janitor`, `ggplot2`,  
     `fixest` (TWFE), `marginaleffects` (marginal effects & CI),  
     `modelsummary`, `broom` (tables),  
     `sf`, `tigris`, `tmap` (for GIS, optional).

2. **Run order (minimal path to replicate)**
   - Run **`DataCleaning&Aim2.1.Rmd`** → produces cleaned intermediates and **`vaccination.csv`** (analysis table) and **`DataCleaning-Aim2.1.pdf`**.
   - Run **`Aim 1.2 Code.Rmd`** (if you need Aim 1.2 outputs).
   - Run **`Aim1.1_Final_Project_GIS_MXIA.Rmd`** (if you need maps).
   - Enter **`Aim2.2&2.3/`** and Knit the Rmd there to generate **TWFE** and **interaction** figures/tables.
   - Figures/tables are saved to the paths specified in each Rmd (typically a `fig/` or `tables/` directory).

3. **Inputs & outputs**
   - **Inputs:**  
     - `Vaccination_Coverage_among_Pregnant_W....csv` (processed vaccination)  
     - `insurance_summary_by_state_2012_2022.csv` (processed insurance)  
     - `state_urban_index_2013_2023.csv` (processed NCHS)  
   - **Analytic table:** `vaccination.csv` (state–year–vaccine unit)  
   - **Key merge doc:** `MergeClarification.csv` (keys, alignment and missingness notes)  
   - **Final figs/tables:** written by each Rmd

---

## Data dictionary (core columns)

| Column                    | Type    | Description |
|---------------------------|---------|-------------|
| `state`                   | factor  | US state (postal) |
| `year`                    | integer | calendar year |
| `vaccine`                 | factor  | `flu` or `tdap` |
| `vacc_pct`                | numeric | vaccination coverage (%) among pregnant persons |
| `n_vacc`                  | integer | sample size used as precision weight |
| `uninsurance_pct`         | numeric | uninsured / (insured + uninsured), women 19–64 |
| `nchs_index`              | integer | urbanicity index (**higher = more rural**, baseline per state) |
| `uninsurance_z`, `nchs_z` | numeric | standardized (z-score) predictors for modeling |
| `...`                     |         | other cleaning flags / QC fields created in Rmds |

> Consistency: `uninsurance_pct = 100 − insurance_coverage_pct` (KFF).
> NCHS range: project-wide **unified** to **1–5**.

---

## Aims & methods

- **Aim 1.1 (GIS overview / maps)**  
  - Build state polygons with `sf`/`tigris`;  
  - Join vaccination coverage to geometry (latest available year, and/or faceted by year);  
  - Produce state choropleths for **Flu** and **Tdap** with consistent color scales, legend, and missingness encoding; optional AK/HI insets.  
- **Aim 1.2 (wrangling code / pipeline)**  
  - Parse raw inputs; clean state codes/names and years; de-duplicate; enforce types;  
  - Compute sample-size–weighted coverage at the **state–year–vaccine** unit;   
- **Aim 2.1 (raw associations)**  
  - Scatter/fits for `uninsurance_pct` vs `vacc_pct` (by vaccine / NCHS strata).
- **Aim 2.2 (between-state)**  
  - State-mean (sample-size weighted) regression: `vacc_pct ~ nchs_z` (Flu & Tdap separately).
- **Aim 2.3 (within-state, TWFE)**  
  - Two-way FE: `vacc_pct_it = α_i + τ_t + β1*uninsurance_z_it + β3*(uninsurance_z_it × nchs_z_i) + ε_it`  
  - Weights: `n_vacc`; SEs clustered by `state`.  
  - Visuals: predicted lines by NCHS; **marginal effects** across NCHS (shaded band = **95% CI**).

---

## Data sources

- **CDC:** Maternal vaccination coverage among pregnant persons (state–year; Flu & Tdap).  
- **KFF:** State-level insurance coverage (women 19–64).  
- **NCHS:** State urban–rural classification (baseline index).
