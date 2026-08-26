# MLOps MLflow Pipeline

An end-to-end machine learning pipeline using Python, DVC and DVCLive. The workflow covers data ingestion, preprocessing, feature engineering, model training and model evaluation. DVC defines and reproduces the pipeline, while DVCLive records experiment metrics and parameters.

## Pipeline

```text
Data Ingestion
      ↓
Data Preprocessing
      ↓
Feature Engineering
      ↓
Model Building
      ↓
Model Evaluation
```

## Repository structure

```text
.
├── src/
│   ├── data_ingestion.py
│   ├── data_preprocessing.py
│   ├── feature_engineering.py
│   ├── model_building.py
│   └── model_evaluation.py
├── dvclive/
│   ├── metrics.json
│   ├── params.yaml
│   └── plots/metrics/
├── dvc.yaml
├── dvc.lock
├── params.yaml
├── projectflow.txt
└── README.md
```

## Prerequisites

- Python 3.9+
- Git
- DVC
- AWS CLI only when using an S3 DVC remote

## 1. Clone the repository

```bash
git clone https://github.com/tushar0678/5.-MLOps-MLFLOW.git
cd 5.-MLOps-MLFLOW
```

## 2. Create a virtual environment

### Windows

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### Linux / macOS

```bash
python3 -m venv .venv
source .venv/bin/activate
```

## 3. Install dependencies

```bash
pip install --upgrade pip
pip install dvc dvclive pandas scikit-learn numpy pyyaml nltk
python --version
dvc --version
```

## 4. Run the complete pipeline

```bash
dvc repro
```

DVC reads `dvc.yaml`, checks dependencies and parameters, and runs the stages that need rebuilding.

The ingestion stage downloads the spam dataset from the configured public dataset URL. Generated `data/`, `models/` and `reports/` directories are intentionally ignored by Git.

## 5. View the pipeline graph

```bash
dvc dag
```

## 6. Run individual stages

```bash
dvc repro data_ingestion
dvc repro data_preprocessing
dvc repro feature_engineering
dvc repro model_building
dvc repro model_evaluation
```

## 7. Change model parameters

Current values in `params.yaml`:

```yaml
data_ingestion:
  test_size: 0.20

feature_engineering:
  max_features: 35

model_building:
  n_estimators: 22
  random_state: 2
```

For example, change `n_estimators` to 50 and run:

```bash
dvc repro
dvc metrics show
```

DVC detects the parameter change and rebuilds the affected stages.

## 8. Experiment tracking with DVCLive

The evaluation stage records metrics with DVCLive. Results are stored under:

```text
dvclive/metrics.json
dvclive/params.yaml
dvclive/plots/metrics/
```

View tracked metrics with:

```bash
dvc metrics show
```

For experiment-oriented runs, DVC also supports:

```bash
dvc exp run
dvc exp show
dvc exp apply <experiment-name>
```

## 9. DVC data and model versioning

DVC tracks pipeline outputs and keeps the workflow reproducible. If an S3 remote is configured, use:

```bash
dvc remote add -d storage s3://YOUR_BUCKET/YOUR_PATH
dvc push
```

On another machine:

```bash
dvc pull
dvc repro
```

Never commit AWS access keys or other secrets.

## 10. Git + DVC workflow

```bash
git checkout -b experiment/new-model
# edit source code or params.yaml
dvc repro
dvc metrics show
git status
git add params.yaml dvc.yaml dvc.lock src/ dvclive/ projectflow.txt README.md
git commit -m "Run MLOps experiment"
git push -u origin experiment/new-model
```

## 11. Reproduce on another machine

```bash
git clone https://github.com/tushar0678/5.-MLOps-MLFLOW.git
cd 5.-MLOps-MLFLOW
python -m venv .venv
# activate the environment
pip install dvc dvclive pandas scikit-learn numpy pyyaml nltk
dvc pull
dvc repro
```

## 12. Common issues

### `dvc` command not found

```bash
pip install dvc
```

### NLTK resource errors

The preprocessing stage downloads the required NLTK resources. If your environment blocks downloads, install the required NLTK resources manually before running the stage.

### `dvc repro` says nothing changed

DVC believes the current dependencies and parameters match the lock file. Change source code or a parameter and run `dvc repro` again.

### S3 / DVC remote errors

Check the DVC remote URL and AWS credentials. Test the AWS CLI separately before troubleshooting DVC.

## Learning objectives

- Build a reproducible ML pipeline.
- Track parameters and pipeline dependencies with DVC.
- Track evaluation metrics with DVCLive.
- Run and compare experiments.
- Version ML pipeline code and metadata with Git.

## License

See the original project license information where applicable.
