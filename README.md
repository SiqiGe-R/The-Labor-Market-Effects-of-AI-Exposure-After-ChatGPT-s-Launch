# Replication Package — AI Exposure and U.S. Occupational Labour-Market Outcomes

This repository contains the data and code used to produce the empirical results in the
dissertation. All analysis was run in Python (Jupyter notebooks) and R (R Markdown).

## 1. Required software

- Python 3.x with: `pandas`, `numpy`, `matplotlib`, `linearmodels`, `scipy`, `openpyxl`, `xlrd`
- R with: `gsynth`, `dplyr`, `ggplot2`, `HonestDiD`

## 2. Data sources

| File | Source |
|---|---|
| `nat3d_M20XX_dl.xls(x)` | BLS Occupational Employment and Wage Statistics (OEWS), annual national 3-digit NAICS files, 2012–2025 |
| `soc_2010_to_2018_crosswalk.xlsx` | BLS SOC 2010→2018 crosswalk (bls.gov/soc/2018) |
| `Task_Statements.xlsx` | O*NET database, Task Statements file (onetcenter.org/database.html) |
| `onet_merged_tasks.csv` | Derived from `Task_Statements.xlsx` by `explore_onet.ipynb` — task descriptions merged per occupation. Already included, so this notebook does not need to be re-run unless you want to rebuild it from scratch. |
| `Language_Modeling_AIOE_and_AIIE.xlsx` | Felten, Raj and Seamans (2023) — LM_AIOE index |
| `ai_applicability_scores.csv` | Tomlinson et al. (2025) — Microsoft AI Applicability Score (MS_SCORE) |

## 3. Pipeline — how to reproduce the harmonised panel from scratch

**Note:** `bls_onet_felten_ms_panel_harmonised.csv` (the final panel used by every
downstream script) is not tracked in this repository because of its size — see
`.gitignore`. Run the step below to regenerate it locally before running any of the
analysis scripts in Section 4.

1. `merge_bls_onet.ipynb`
   Stacks all `nat3d_*` files into a single panel, keeps detailed occupations only,
   applies the SOC 2010→2018 crosswalk to pre-2019 rows, collapses duplicate
   occupation-industry-year keys created by the crosswalk (see note below), merges
   in O*NET task descriptions, LM_AIOE, and MS_SCORE.
   **Output:** `bls_onet_felten_ms_panel_harmonised.csv`

**Methodological note on Step 1:** where the SOC crosswalk maps multiple SOC 2010
codes onto a single SOC 2018 code, employment is summed and wages are combined
using an employment-weighted average of the medians. This is an approximation —
a weighted mean of medians is not itself a median — and is noted here for
transparency (see also the corresponding comment in `merge_bls_onet.ipynb`, Step 4).

## 4. Analysis scripts

| Script | Produces |
|---|---|
| `descriptive_analysis_combined.ipynb`, `descriptive_charts.ipynb` | Descriptive employment/wage trend figures (Section 3.4) |
| `did_unified.ipynb` | Baseline TWFE pooled + event-study estimates (Section 5.1) |
| `did_covid_inclusive_nocontrol.ipynb` | COVID-inclusive TWFE robustness check (Section 5.4.3) |
| `synthetic_control_unified.ipynb` | Baseline Standard Synthetic Control (Section 5.2) |
| `synthetic_control_unified_covid.ipynb` | COVID-inclusive Standard SC robustness check |
| `gsc_analysis.Rmd` | Baseline GSC — LM_AIOE, both outcomes, plus trend-adjusted wage results (Sections 5.3, 5.4.1) |
| `gsc_ms_score.Rmd` | Baseline GSC — MS_SCORE, both outcomes |
| `gsc_analysis_covid.Rmd`, `gsc_ms_score_covid.Rmd` | COVID-inclusive GSC robustness checks (Section 5.4.3) |
| `rambachan_roth_sensitivity.Rmd` | Rambachan–Roth sensitivity analysis across all four specifications (Section 5.4.2) |

## 5. Output files

- `did_pooled_summary.csv`, `sc_unified_summary.csv`, `sc_unified_summary_covid.csv`,
  `did_covid_inclusive_nocontrol_summary.csv`, `rambachan_roth_summary.csv` — summary
  tables of coefficients/ATTs used to build the results tables in the dissertation.
- `gsc_results_summary.txt`, `gsc_results_summary_covid.txt`,
  `gsc_ms_results_summary.txt`, `gsc_ms_results_summary_covid.txt` — full `gsynth`
  console output (ATT, SE, CI, p-values by period) for each GSC specification.
  These are the direct source of the GSC numbers reported in Section 5.3 and Table 4.

## 6. Notes

- Estimation samples differ slightly across LM_AIOE and MS_SCORE due to differing
  occupational coverage of the two exposure indices (see Section 3.1).
- The Job Zone diagnostic exercise and the CPS-based robustness checks referenced
  during the analysis process are not included in the final results and are not
  part of this replication package.
