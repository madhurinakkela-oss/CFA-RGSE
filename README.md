CFA-RGSE

Reliability-Gated Multi-Source Geospatial Imputation and Decision Screening for India’s Cement–Fly-Ash System






CFA-RGSE is a reproducible, reliability-guided multi-view learning framework for harmonising heterogeneous Indian cement-plant, thermal-power, and fly-ash records. It supports geographically robust missing-capacity estimation, source-aware uncertainty diagnostics, and transparent industrial-symbiosis screening without presenting screening outputs as verified transport or investment decisions.

Validation status: The frozen package passes all 22 dataset, result, allocation, and figure-integrity checks in VALIDATION_REPORT.json.

Study overview

Public industrial records are distributed across incompatible spatial and administrative levels. Cement plants are represented as geospatial facilities, thermal-power records are facility-level, state fly-ash statistics are aggregates, and the indicative major-cement list contains free-text names and locations. CFA-RGSE integrates these sources into an audited 380 × 53 modelling table while preserving provenance, missingness, and claim boundaries.

The framework addresses three questions:

Can missing cement-plant capacity be estimated under whole-state held-out evaluation, rather than an optimistic random row split?

How much evidence is contributed by plant attributes, thermal-power proximity, state fly-ash context, and the CEA major-cement list?

Can model outputs support a transparent linkage-priority screen while keeping prediction, material suitability, and operational feasibility conceptually separate?

Main contributions

Audited harmonisation of four India-focused sources into a provenance-preserving facility table.

State-held-out GroupKFold evaluation designed to reduce geographic leakage.

Four source-aware Extra-Trees views combined using observation-specific reliability weights.

Reliability gating based on inner cross-validation error, tree disagreement, and source availability.

Baselines, source-view ablations, paired state-level tests, and cluster-bootstrap intervals.

Missing-capacity estimates with active-view weights and disagreement diagnostics.

A separate, declared-weight screening layer for 341 plants with available state fly-ash context.

Optional nested graph spatial-lag and mass-balance allocation extensions, clearly separated from the core manuscript evidence.

System architecture



The trained imputation model and the downstream decision-screening layer are deliberately separated. State fly-ash quantities are contextual evidence and are not treated as plant-level ash measurements or causal determinants of cement capacity.

Datasets

Dataset

Provider

Spatial level

Rows

Primary role

Cement Plants in India 2024

Esri India Living Atlas

Plant/polygon

380

Plant geometry, capacity and production attributes

Thermal Power Plants 2024–25

Central Electricity Authority

Station

189

Proximity and regional thermal-infrastructure features

Statewise Fly Ash 2024–25

Central Electricity Authority

State + India total

18

State-context generation and utilisation variables

Major Cement Plants 2025

Central Electricity Authority

Indicative plant list

141

Conservatively matched major-plant evidence

Processed modelling table

Shape: 380 rows × 53 columns

Valid installed-capacity labels: 272

Positive production labels: 115

Missing installed-capacity values completed: 108

Plants with transparent priority scores: 341

The Esri India layer is India-specific but is not a Government of India registry. The 18-row fly-ash file contains 17 states and one India total; it does not provide plant-level ash quality, availability, or contractual access.

Verified results

Core state-held-out evaluation

Task/model

Evaluation

RMSE

Interpretation

Capacity — CFA-RGSE full model

5-fold state-grouped CV

1.303 MTPA

Core proposed model

Capacity — Random Forest

5-fold state-grouped CV

1.320 MTPA

Main nonlinear baseline

Capacity — without fly-ash view

5-fold state-grouped CV

1.298 MTPA

Negative ablation result; slightly lower RMSE

Production — CFA-RGSE full model

5-fold state-grouped CV

0.749 Mt

Exploratory production task

Production — Random Forest

5-fold state-grouped CV

0.788 Mt

Production baseline

The full capacity model improved mean fold RMSE over Random Forest by approximately 1.3%, but the cluster-bootstrap 95% interval for the paired RMSE difference crossed zero. The result therefore does not establish statistically decisive superiority. The fly-ash-view ablation also performed slightly better than the full model, showing that aggregate state context did not improve plant-capacity prediction in this dataset.

Optional extensions

Extension

Verified frozen result

Evidence boundary

Nested graph spatial-lag model

RMSE 1.295 MTPA

State-grouped nested scale selection; not a routing network

Baseline mass-balance scenario

24.260 Mt allocated

Scenario output, not observed material movement

Positive allocation links

113

Straight-line-distance screening links

These extensions must not be described as real-time logistics, road/rail routing, causal inference, verified ash chemistry, or investment feasibility.

Repository structure

.
├── cfa_pkg/
│   ├── data/
│   │   ├── raw/                         # Four source-oriented CSV files
│   │   └── processed/                   # 380 × 53 table and QA outputs
│   ├── notebooks/
│   │   └── CFA_RGSE_Real_Experiment_Colab.ipynb
│   ├── scripts/                         # Core data/model/analysis pipeline
│   ├── results/                         # Metrics, OOF predictions and plots
│   ├── README.md
│   └── requirements.txt
├── manuscripts/                         # Core and extended manuscripts
├── output/
│   ├── core_figures/
│   └── q1_extension_figures/            # PNG, TIFF and SVG versions
├── run_cfa_rgse_complete.py              # One-command workflow
├── run_graph_spatial_lag_experiment.py
├── run_nested_graph_scale_experiment.py
├── build_q1_extension_artifacts.py
├── validate_cfa_rgse_package.py
├── VALIDATION_REPORT.json
├── FILE_MANIFEST.csv
└── SHA256SUMS.txt

Installation

Python 3.10 or later is recommended.

git clone <YOUR-REPOSITORY-URL>
cd <YOUR-REPOSITORY-DIRECTORY>

python -m venv .venv
source .venv/bin/activate
# Windows PowerShell: .venv\Scripts\Activate.ps1

python -m pip install --upgrade pip
python -m pip install -r cfa_pkg/requirements.txt

Frozen dependencies:

numpy==2.3.5
pandas==2.2.3
scipy==1.17.0
scikit-learn==1.8.0
matplotlib==3.10.8

Reproducing the study

Core manuscript pipeline

python run_cfa_rgse_complete.py --core-only

This rebuilds the audited table, runs state-held-out models and ablations, creates missing-value estimates and priority outputs, regenerates base plots, and executes validation.

Complete pipeline, including extensions

python run_cfa_rgse_complete.py

Validate frozen outputs without retraining

python validate_cfa_rgse_package.py

Expected final status:

validation_status: PASS
checks_passed: 22
checks_total: 22

All model seeds are fixed at 42. Small floating-point differences can occur across operating systems or dependency versions.

Google Colab

Upload the repository or extracted reproducibility package to Google Drive, then open:

cfa_pkg/notebooks/CFA_RGSE_Real_Experiment_Colab.ipynb

The notebook provides an entry point to the same source files and scripts rather than duplicating a separate implementation.

Important output files

File

Purpose

capacity_group_cv_summary.csv

Capacity baselines and ablations

capacity_group_cv_oof_predictions.csv

Plant-level out-of-fold predictions

capacity_paired_statistics.json

State-cluster bootstrap and paired test results

capacity_missing_predictions.csv

Estimates for 108 missing-capacity plants

production_group_cv_summary.csv

Exploratory production results

cement_flyash_linkage_priority.csv

Full 380-row screening file; 341 rows scored

nested_graph_summary.csv

Confirmatory nested graph-extension result

baseline_mass_balance_links.csv

Positive scenario allocation links

VALIDATION_REPORT.json

Machine-readable 22-check validation report

SHA256SUMS.txt

Per-file integrity hashes

Reproducibility and claim boundaries

Capacity and production completions are model estimates, not statutory records.

Haversine distance is a geographic screening measure, not road or rail travel distance.

The priority index uses declared analyst weights and is not a learned feasibility label.

State fly-ash totals must not be interpreted as plant-level supply.

Results do not establish ash chemistry, BIS compliance, contracted access, delivered cost, or commercial viability.

Operational decisions require updated source records, material testing, network-routing data, and field verification.

Data provenance and reuse

The Central Electricity Authority files originate from Government of India reporting. The cement-plant geometry layer originates from Esri India Living Atlas. Before redistributing the raw files or using them commercially, verify the current source terms and attribution requirements.

See:

DATA_SOURCE_AND_LICENSE_NOTES.md

cfa_pkg/README.md

CEA fly-ash reporting page

Esri India Cement Plants 2024 service

No universal downstream licence for third-party data is implied by this repository.

Citation

If this repository supports your work, cite the associated manuscript:

@article{sakamudi2026cfargse,
  title   = {CFA-RGSE: Reliability-Gated Multi-Source Geospatial Imputation and Decision Screening for India's Cement--Fly-Ash System},
  author  = {Sakamudi, Srilakshmi Ramya and Kothapalli, Pavan Kumar Varma and Rajani, Koduri Naga and P, Anuradha and Vikruthi, Sriharsha and Nakella, Madhuri},
  year    = {2026},
  note    = {Research manuscript and reproducibility package}
}

Update the journal name, volume, pages, and DOI after publication.

Authors

Srilakshmi Ramya Sakamudi

Pavan Kumar Varma Kothapalli

Koduri Naga Rajani

Anuradha P

Sriharsha Vikruthi

Madhuri Nakella

Research integrity statement

This repository intentionally retains negative and statistically inconclusive findings. Its contribution is auditable multi-source integration, leakage-aware geographic evaluation, missing-view diagnostics, and reproducible decision screening—not a claim of universal predictive superiority.
