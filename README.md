# mslearn-mlops

A hands-on learning repository for Microsoft Learn MLOps concepts and exercises. This repository contains a small example training script (MLflow autologging), job definitions, tests and supporting documentation used to demonstrate reproducible training, experiment tracking, and example job submission definitions.

This README is tailored to the repository layout and scripts currently in this repo.

## Quick links

- Training script: [src/model/train.py](https://github.com/pcarnelli/mslearn-mlops/blob/main/src/model/train.py)
- Job definitions: [src/job.yml](https://github.com/pcarnelli/mslearn-mlops/blob/main/src/job.yml), [src/job-dev.yml](https://github.com/pcarnelli/mslearn-mlops/blob/main/src/job-dev.yml), [src/job-prod.yml](https://github.com/pcarnelli/mslearn-mlops/blob/main/src/job-prod.yml)
- Tests: [tests/test_train.py](https://github.com/pcarnelli/mslearn-mlops/blob/main/tests/test_train.py)
- Documentation folder: [documentation/](https://github.com/pcarnelli/mslearn-mlops/tree/main/documentation)
- Experimentation examples: [experimentation/](https://github.com/pcarnelli/mslearn-mlops/tree/main/experimentation)
- Production examples: [production/](https://github.com/pcarnelli/mslearn-mlops/tree/main/production)

## Repository layout (relevant files)

- src/
  - model/train.py         — training script using scikit-learn and MLflow autolog
  - job.yml, job-dev.yml, job-prod.yml — job definition YAMLs (for job runners / Azure ML)
- tests/
  - datasets/              — small test dataset used by unit tests
  - test_train.py          — tests for the CSV ingestion helper
- requirements.txt         — Python dependencies
- pytest.ini               — pytest configuration
- documentation/, experimentation/, production/ — supporting docs and examples

## Prerequisites

- Python 3.8+
- Git
- Recommended: virtual environment (venv or conda)
- Optional: Docker (if you add packaging), Azure CLI + Azure ML extension if you plan to submit jobs using Azure ML
- Install dependencies:

  pip install -r requirements.txt

## Running locally

Note: the tests import modules as `model.train`. To run tests and import the `model` package correctly, add the `src` folder to your Python path.

Unix / macOS (bash/zsh):

  export PYTHONPATH=src
  pip install -r requirements.txt

Windows (PowerShell):

  $env:PYTHONPATH = "src"
  pip install -r requirements.txt

Run the training script (example using the small test dataset included in repo):

  python src/model/train.py --training_data tests/datasets --reg_rate 0.01

What this does:
- src/model/train.py reads CSV files from the provided directory, splits data, and calls a simple LogisticRegression training routine.
- The script enables MLflow autologging (`mlflow.autolog()`), so runs and metrics will be recorded to the MLflow tracking server specified by your environment (default: local file-based tracking if MLFLOW_TRACKING_URI not set).

Run the unit tests:

  export PYTHONPATH=src
  pytest -q

The repository includes tests/test_train.py which validates the CSV ingestion helper (`get_csvs_df`) using the small datasets under tests/datasets.

## Job definitions

There are three YAML files at the repo root of `src/`:

- `src/job.yml`
- `src/job-dev.yml`
- `src/job-prod.yml`

These are job definition templates (intended for Azure ML jobs or other YAML-driven job runners). If you use Azure ML CLI, you can submit a job with an Azure ML command like:

  az ml job create -f src/job-dev.yml

(Adjust the command to your environment and Azure ML configuration. If you use another runner, adjust accordingly.)

## MLflow notes

- The training script calls `mlflow.autolog()`. Configure MLflow with the `MLFLOW_TRACKING_URI` environment variable to point to a tracking server if you don't want the default local file store.
- Example:

  export MLFLOW_TRACKING_URI=http://localhost:5000

## CI / CD and repository automation

- The repository contains a `.github` directory — you can add GitHub Actions workflows there to run linting, tests, build artifacts, and optionally submit jobs.
- There are no Dockerfiles currently included; if you plan to deploy as a container, add a `Dockerfile` and a `scripts/` or `Makefile` workflow.

## Development tips

- Keep the training script idempotent and driven by CLI args (the current `src/model/train.py` uses `--training_data` and `--reg_rate`).
- Save run metadata (params, metrics, artifact paths, git commit SHA) so trained models are traceable.
- When adding code, update tests under `tests/` and run `pytest` in CI.

## Troubleshooting

- If `pytest` cannot import `model.train`, ensure `PYTHONPATH=src` is set or install the package in editable mode (e.g., create a package and pip install -e .).
- If MLflow logs don't appear where you expect, set `MLFLOW_TRACKING_URI` to a reachable tracking server.

## License & acknowledgements

This repository is intended for learning and demonstration purposes. Check with the repository owner for licensing details before re-using content.

Acknowledgements: inspired by Microsoft Learn MLOps materials.
