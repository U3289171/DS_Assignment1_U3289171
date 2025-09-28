
# UC Assignment — Part C: Reproducibility & Workflow

This repository is a **ready-to-run template** satisfying Part C (10 marks): Git, Git LFS, and DVC with a reproducible pipeline.
It is tailored to your files:
- `data/raw/zomato_df_final_data.csv` (copied from your upload)
- `data/external/sydney.geojson` (copied from your upload)
- `notebooks/U3289171_Assignment1.ipynb` (copied from your upload)

> Generated 2025-09-28 10:36 (local time)

## 1) Environment

```bash
# Create and activate a virtual environment (Windows PowerShell)
python -m venv .venv
. .venv/Scripts/Activate.ps1

# or macOS/Linux
python3 -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

## 2) Git & GitHub

```bash
git init
git add .gitattributes
git lfs install

# LFS tracks heavy visualisations & notebooks by default
git add .
git commit -m "Init: Repo + LFS + DVC pipeline skeleton"
git branch -M main
git remote add origin <YOUR_GITHUB_REPO_URL>
git push -u origin main
```

Notes:
- We initialise **Git LFS** for figures (`reports/figures/**`, common image types) and `.ipynb`.
- Datasets/models are managed by **DVC** here. If your rubric insists on LFS for datasets/models,
  you may uncomment patterns in `.gitattributes`, but avoid double-tracking with DVC.

## 3) DVC

```bash
# Initialise DVC and set a remote (choose one)
dvc init
# Local directory remote
dvc remote add -d localstore ./dvcstore
# OR: S3 remote (example)
# dvc remote add -d s3store s3://<bucket>/<path>
# dvc remote modify s3store access_key_id <AWS_KEY>
# dvc remote modify s3store secret_access_key <AWS_SECRET>
# dvc remote modify s3store region <AWS_REGION>

# Track raw data and external resources
dvc add data/raw/zomato_df_final_data.csv
dvc add data/external/sydney.geojson

git add data/raw/zomato_df_final_data.csv.dvc data/external/sydney.geojson.dvc .gitignore .dvc .dvcignore dvc.yaml params.yaml
git commit -m "DVC: track raw data & pipeline"
```

Reproduce the **full pipeline** end-to-end:

```bash
dvc repro
```

Outputs:
- Processed data: `data/processed/zomato_processed.csv`
- Model: `models/model.pkl`
- Metrics: `reports/metrics.json`
- Figure: `reports/figures/cost_hist.png`

Push data/model artifacts to your DVC remote & code to GitHub:

```bash
dvc push
git push
```

## 4) How to Run the Project

```bash
# 1) Activate environment
source .venv/bin/activate  # or . .venv/Scripts/Activate.ps1 on Windows

# 2) Install deps
pip install -r requirements.txt

# 3) Reproduce pipeline
dvc repro

# 4) Inspect results
type reports/metrics.json   # Windows (use `cat` on macOS/Linux)
# open reports/figures/cost_hist.png
```

## 5) Expected Results (Template)

- A baseline binary classification distinguishing **high-cost** restaurants (`cost >= 60 AUD`) vs others.
- `reports/metrics.json` with baseline classification accuracy.
- A cost distribution histogram at `reports/figures/cost_hist.png`.

Extend the pipeline by:
- Richer feature engineering (e.g., cuisines, location grids using `sydney.geojson`)
- Regression experiments (predicting `cost`) alongside classification
- Alternative models (SVM, RandomForest, GradientBoosting) with tracked metrics

## 6) Project Layout

```
.
├── .dvcignore
├── .gitattributes
├── .gitignore
├── dvc.yaml
├── params.yaml
├── requirements.txt
├── data
│   ├── external
│   │   └── sydney.geojson
│   ├── processed
│   └── raw
│       └── zomato_df_final_data.csv
├── models
├── notebooks
│   └── U3289171_Assignment1.ipynb
├── reports
│   └── figures
└── src
    ├── evaluate.py
    ├── featurize.py
    ├── preprocess.py
    └── train.py
```

## 7) Reproducibility Checklist

- Git repo initialised; commits pushed to GitHub
- Git LFS initialised; heavy figures & notebooks tracked
- DVC initialised; raw data tracked and pipeline defined
- `dvc repro` rebuilds all stages from code/param changes
- `params.yaml` centralises parameters; changing them triggers the right stages
