# Predictive Maintenance ML Project

Production-style end-to-end Predictive Maintenance project based on the Azure PdM dataset family shown in the supplied image:

- `PdM_telemetry`
- `PdM_errors`
- `PdM_failures`
- `PdM_machines`
- `PdM_maint`

## Syllabus coverage

### Module I — Project Environment Setup
- End-to-end Industry ML project
- Environment and pipeline setup
- `venv` and Conda
- Dependency management
- Data loading and validation
- Feature engineering
- Model training and evaluation
- Standard ML project folder structure
- GitHub and version control

### Module II — Logging, Exception Handling & Git Essentials
- `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`
- File logging
- Custom exceptions
- Structured `try/except`
- `git init`, `clone`, `commit`, `push`, `pull`
- `.gitignore`
- Branching and merging
- GitHub Actions CI

## 1. Dataset setup

Put the real dataset files in:

```text
data/raw/
```

Recommended names:

```text
PdM_telemetry.csv
PdM_errors.csv
PdM_failures.csv
PdM_machines.csv
PdM_maint.csv
```

The loader also accepts common variations such as `telemetry.csv`, `errors.csv`, `failures.csv`, `machines.csv`, and `maintenance.csv`, and supports CSV, Excel and Parquet.

**Important:** the supplied image showed the dataset filenames, not the actual data rows. The ZIP therefore does not contain fake or invented data. Add your real datasets to `data/raw/`.

## 2. Python venv

### Windows

```powershell
python -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### Linux/macOS

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

## 3. Conda

```bash
conda env create -f environment.yml
conda activate pdm-maintenance
```

## 4. Run the full ML pipeline

After copying the five datasets into `data/raw/`:

```bash
python scripts/run_pipeline.py
```

The pipeline performs:

1. Load datasets
2. Validate schema
3. Build time-aware features
4. Create the 24-hour failure target
5. Chronological train/validation/test split
6. Train Random Forest model
7. Evaluate the model
8. Save model and metrics
9. Save test predictions

Generated files:

```text
data/processed/features.csv
data/processed/test_features.csv
artifacts/model.joblib
artifacts/feature_columns.json
artifacts/metrics.json
artifacts/test_predictions.csv
logs/pipeline.log
```

## 5. Individual commands

```bash
python scripts/validate_data.py
python scripts/build_features.py
python scripts/train.py
python scripts/evaluate.py
python scripts/predict.py
```

## 6. API

Start:

```bash
uvicorn src.api.app:app --reload
```

Open:

```text
http://127.0.0.1:8000/docs
```

The API exposes:

- `GET /health`
- `POST /predict`

## 7. ML problem

The model predicts whether a machine will fail within the next 24 hours.

Features include:

- voltage
- rotation
- pressure
- vibration
- rolling telemetry statistics
- error frequency
- maintenance frequency
- hours since maintenance
- machine age
- machine model
- hour/day/month features

The split is chronological rather than random because maintenance data is time-dependent.

## 8. Logging

Logs are written to:

```text
logs/pipeline.log
```

Levels:

- `DEBUG` — detailed diagnostic information
- `INFO` — normal progress
- `WARNING` — suspicious but recoverable condition
- `ERROR` — operation failure
- `CRITICAL` — serious application failure

## 9. GitHub

```bash
git init
git add .
git commit -m "Initial predictive maintenance project"
git branch -M main
git remote add origin YOUR_GITHUB_REPOSITORY_URL
git push -u origin main
```

Feature branch:

```bash
git checkout -b feature/model-improvement
git add .
git commit -m "Improve predictive maintenance model"
git push -u origin feature/model-improvement
```

Then create a Pull Request and merge it into `main`.

Update:

```bash
git pull origin main
```

History:

```bash
git log --oneline --graph --decorate --all
```

## 10. What should NOT be pushed

Do not commit:

- raw/private datasets
- model binaries
- logs
- `.env`
- virtual environments
- temporary files

`.gitignore` is already configured for these.

## 11. Architecture

```text
pdm_predictive_maintenance/
├── data/
│   ├── raw/
│   ├── interim/
│   └── processed/
├── artifacts/
├── logs/
├── notebooks/
├── reports/
├── scripts/
├── src/
│   ├── api/
│   ├── config/
│   ├── data/
│   ├── exceptions/
│   ├── features/
│   ├── models/
│   └── utils/
├── tests/
├── .github/workflows/
├── .env.example
├── .gitignore
├── environment.yml
├── requirements.txt
└── README.md
```

## 12. Production note

The 0.50 classification threshold is only a baseline. In a real maintenance system, choose the threshold using the business cost of false alarms versus missed failures. Recall is often more important than raw accuracy because missing an actual machine failure can be expensive.
