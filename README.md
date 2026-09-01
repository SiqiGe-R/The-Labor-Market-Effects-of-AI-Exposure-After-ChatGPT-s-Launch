# Replication Package — AI Exposure and U.S. Occupational Labour-Market Outcomes

This repository contains the data and code used to reproduce the empirical analysis in the dissertation:

**Augmentation or Substitution? The Labour Market Effects of AI Exposure After ChatGPT’s Launch: Evidence from a Generalized Synthetic Control Approach**

The analysis combines U.S. Occupational Employment and Wage Statistics (OEWS) data from 2012–2025 with two occupation-level measures of AI exposure: LM_AIOE and the Microsoft AI Applicability Score (MS_SCORE).

The empirical analysis includes:

- continuous-exposure two-way fixed effects (TWFE);
- dynamic event-study specifications;
- standard synthetic control;
- generalized synthetic control (GSC);
- trend-adjusted GSC wage specifications;
- Rambachan–Roth sensitivity analysis;
- COVID-inclusive robustness checks;
- alternative occupation-level clustering for TWFE inference.

All analysis was conducted in Python and R.

## 1. Required software

### Python

Python 3.x with the following packages:

- `pandas`
- `numpy`
- `matplotlib`
- `linearmodels`
- `scipy`
- `openpyxl`
- `xlrd`

### R

R with the following packages:

- `gsynth`
- `dplyr`
- `ggplot2`
- `fixest`
- `HonestDiD`
- `purrr`

The Rambachan–Roth script installs `HonestDiD` from GitHub if it is not already available.

## 2. Data files

| File | Description / Source |
|---|---|
| `nat3d_M2012_dl.xls` to `nat3d_M2025_dl.xlsx` | U.S. Bureau of Labor Statistics Occupational Employment and Wage Statistics (OEWS), annual national occupation-by-3-digit-NAICS files, 2012–2025 |
| `soc_2010_to_2018_crosswalk.xlsx` | Official BLS SOC 2010-to-2018 occupational crosswalk |
| `Task_Statements.xlsx` | O*NET Task Statements |
| `onet_merged_tasks.csv` | Occupation-level task descriptions derived from `Task_Statements.xlsx` |
| `Language_Modeling_AIOE_and_AIIE.xlsx` | Felten, Raj and Seamans (2023), including the LM_AIOE exposure measure |
| `ai_applicability_scores.csv` | Tomlinson et al. (2025), Microsoft AI Applicability Score |
| `bls_onet_felten_ms_panel_harmonised.7z` | Compressed copy of the final harmonised occupation-industry-year analysis panel |

## 3. Preparing the final harmonised panel

All downstream analysis scripts expect the following file:

`bls_onet_felten_ms_panel_harmonised.csv`

There are two ways to obtain it.

### Option A: Extract the included compressed panel

Extract:

`bls_onet_felten_ms_panel_harmonised.7z`

The extracted file should be named:

`bls_onet_felten_ms_panel_harmonised.csv`

Place it in the repository root before running the downstream analysis scripts.

### Option B: Reconstruct the panel from the raw source files

Run:

`merge_bls_onet.ipynb`

This notebook:

1. loads and harmonises the annual OEWS files from 2012–2025;
2. retains detailed occupations;
3. harmonises occupational codes to the SOC 2018 classification using the official BLS crosswalk;
4. collapses duplicate occupation-industry-year observations created by many-to-one SOC mappings;
5. merges O*NET task information;
6. merges the LM_AIOE measure;
7. merges the Microsoft AI Applicability Score;
8. exports the final harmonised panel.

Output:

`bls_onet_felten_ms_panel_harmonised.csv`

### Note on SOC harmonisation

Where multiple SOC 2010 occupations map to a single SOC 2018 occupation, employment is summed.

Median wages are combined using an employment-weighted average. This is an approximation because a weighted average of medians is not itself a median. The procedure is used to construct a consistent harmonised occupational panel across classification changes.

## 4. Supporting data-preparation scripts

| Script | Purpose |
|---|---|
| `explore_onet.ipynb` | Processes and explores the O*NET Task Statements data and produces `onet_merged_tasks.csv` |
| `merge_felten.ipynb` | Supporting script for merging the Felten LM_AIOE data |
| `merge_bls_onet.ipynb` | Main harmonisation pipeline producing the final analysis panel |

For replication of the final dissertation analysis, `merge_bls_onet.ipynb` is the main data-construction script.

## 5. Main analysis scripts

### Descriptive analysis

| Script | Produces |
|---|---|
| `descriptive_analysis_combined.ipynb` | Descriptive analysis of employment, wages and AI exposure |
| `descriptive_charts.ipynb` | Employment and wage trend figures used in Section 3.4 |
| `summary_statistics_table.ipynb` | Summary statistics reported in the Data Appendix |

### TWFE and event study

| Script | Produces |
|---|---|
| `did_unified.ipynb` | Baseline continuous-exposure pooled TWFE estimates and dynamic event-study estimates |
| `did_covid_inclusive_nocontrol.ipynb` | COVID-inclusive TWFE robustness analysis |
| `did_occupation_clustered.ipynb` | TWFE robustness check using occupation-level rather than occupation-industry-level clustering |

The baseline TWFE specifications exclude 2020 and 2021. The COVID-inclusive specification retains these years.

### Standard synthetic control

| Script | Produces |
|---|---|
| `synthetic_control_unified.ipynb` | Baseline standard synthetic-control analysis |
| `synthetic_control_unified_covid.ipynb` | COVID-inclusive standard synthetic-control robustness analysis |

For each AI exposure measure, occupations above the median are classified as relatively high exposure and lower-exposure occupations form the donor pool.

The preferred donor-pool size is selected using leave-one-pre-treatment-period-out cross-validation.

### Generalized synthetic control

| Script | Produces |
|---|---|
| `gsc_analysis.Rmd` | Baseline LM_AIOE GSC analysis for employment and wages, including trend-adjusted wage specifications |
| `gsc_ms_score.Rmd` | Baseline MS_SCORE GSC analysis |
| `gsc_analysis_covid.Rmd` | COVID-inclusive LM_AIOE GSC robustness analysis |
| `gsc_ms_score_covid.Rmd` | COVID-inclusive MS_SCORE GSC robustness analysis |

The GSC models are estimated at the occupation-year level.

Employment is aggregated by summing employment across industries.

The occupation-year wage outcome is constructed as the employment-weighted mean of log occupation-industry median wages.

The number of latent factors is selected by cross-validation. In the principal dissertation specifications, cross-validation selects `r* = 0`.

Inference uses the parametric bootstrap implemented in `gsynth`, with 200 bootstrap replications.

### Rambachan–Roth sensitivity analysis

| Script | Produces |
|---|---|
| `rambachan_roth_sensitivity.Rmd` | Relative-magnitude sensitivity analysis for all four TWFE event-study specifications |

The script uses coefficient vectors and covariance matrices generated from the baseline event-study specification in `did_unified.ipynb`.

It calculates robust confidence intervals over a grid of relative-magnitude restrictions.

The reported breakdown value is the smallest value of the relative-magnitude parameter at which the robust confidence interval includes zero.

Because the baseline sample excludes 2020 and 2021, the pre-treatment event-time index is not evenly spaced in calendar time between 2019 and 2022. The analysis follows the event-time indexing used in the TWFE specification and is therefore interpreted as a sensitivity exercise rather than as a separate source of identification.

## 6. Order of replication

A complete replication can be run in the following order:

1. Extract `bls_onet_felten_ms_panel_harmonised.7z`

   or reconstruct the panel using:

   `merge_bls_onet.ipynb`

2. Run descriptive analysis:

   `descriptive_analysis_combined.ipynb`

   `descriptive_charts.ipynb`

   `summary_statistics_table.ipynb`

3. Run baseline TWFE and event-study analysis:

   `did_unified.ipynb`

4. Run alternative clustering robustness:

   `did_occupation_clustered.ipynb`

5. Run COVID-inclusive TWFE analysis:

   `did_covid_inclusive_nocontrol.ipynb`

6. Run baseline standard synthetic control:

   `synthetic_control_unified.ipynb`

7. Run COVID-inclusive standard synthetic control:

   `synthetic_control_unified_covid.ipynb`

8. Run baseline GSC:

   `gsc_analysis.Rmd`

   `gsc_ms_score.Rmd`

9. Run COVID-inclusive GSC:

   `gsc_analysis_covid.Rmd`

   `gsc_ms_score_covid.Rmd`

10. Run Rambachan–Roth sensitivity analysis:

   `rambachan_roth_sensitivity.Rmd`

## 7. Baseline sample definition

The baseline analysis excludes 2020 and 2021 because of the exceptional labour-market disruption associated with the COVID-19 pandemic.

The baseline panel therefore contains:

2012–2019 and 2022–2025.

The intervention is defined by the public release of ChatGPT in November 2022.

Because OEWS annual estimates refer to May, the May 2022 observation is treated as the final pre-intervention observation and 2023 as the first post-intervention year.

## 8. AI exposure measures and treatment definitions

Two occupation-level AI exposure measures are used.

### LM_AIOE

LM_AIOE is taken from Felten, Raj and Seamans (2023) and captures occupational exposure to language-model capabilities through the relationship between AI capabilities and O*NET occupational abilities.

LM_AIOE is the principal exposure measure in the dissertation.

### MS_SCORE

MS_SCORE is based on the Microsoft AI Applicability Score developed by Tomlinson et al. (2025), using observed Bing Copilot interactions mapped to O*NET work activities.

It is used as an alternative measure of realised generative-AI applicability.

In the TWFE specifications, each exposure measure is standardised within its estimation sample.

In the standard synthetic-control and GSC analyses, occupations are divided into relatively high- and low-exposure groups using the median of the relevant exposure measure.

These groups are relative classifications. Low-exposure occupations are not assumed to be literally untreated by generative AI.

## 9. Data coverage

The harmonised baseline panel contains:

- 219,783 occupation-industry-year observations;
- 889 occupations;
- 96 three-digit industries.

Exposure coverage differs across the two indices:

- LM_AIOE is matched to 178,979 baseline observations;
- MS_SCORE is matched to 203,165 baseline observations.

A total of 670 occupations are matched to both exposure measures.

The estimation samples therefore differ slightly because the two AI exposure measures do not cover exactly the same occupations.

## 10. Methodological notes

The three main empirical approaches estimate related but distinct quantities.

- TWFE estimates differential post-treatment changes across the continuous AI-exposure distribution.
- Standard synthetic control constructs occupation-specific counterfactuals and examines whether post-treatment synthetic gaps vary systematically with exposure.
- GSC estimates the average post-treatment deviation of relatively high-exposure occupations from a jointly modelled counterfactual.

Coefficient magnitudes should therefore not be compared mechanically across estimators.

The analysis focuses primarily on direction, qualitative consistency and sensitivity to alternative identifying assumptions.

The wage results exhibit meaningful differential pre-treatment trends. The dissertation therefore interprets the post-2022 wage estimates cautiously and supplements the baseline results with trend-adjusted GSC and Rambachan–Roth sensitivity analysis.

## 11. Reproducibility notes

The repository includes:

- all annual OEWS files from 2012 through 2025;
- the official SOC crosswalk used for occupational harmonisation;
- both AI exposure datasets;
- O*NET task data;
- the code required to reconstruct the harmonised analysis panel;
- the scripts used for the main and robustness analyses reported in the dissertation.

Random seeds are set where relevant in the GSC analysis.

Small numerical differences may occur across software or package versions, particularly for bootstrap-based inference.

The Job Zone diagnostic analysis and CPS-based exercises considered during earlier stages of the project are not part of the final dissertation results and are therefore not included in the main replication pipeline.
