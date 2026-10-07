# Sales Data Validation and Versioning Pipeline

This project demonstrates how to build a reproducible data pipeline for model training using DVC (Data Version Control) and Great Expectations.

The workflow is designed to:

- ingest raw sales data,
- split it into training and test sets,
- preprocess the data for downstream modeling,
- validate critical dataset quality checks before using the data in training.

The result is a simple but practical example of combining data versioning with data quality validation in a machine learning workflow.

## Why this project matters

In production ML pipelines, it is not enough to just version code and model artifacts. Data quality matters just as much. This project shows how to:

- track data changes with DVC,
- keep raw and processed datasets versioned,
- detect schema drift and invalid values,
- fail the pipeline early if required data quality checks do not pass.

## Pipeline overview

The workflow contains three stages:

1. Ingest
   - Reads the source sales dataset
   - Splits it into train/test data
   - Saves the raw data files to the `data/raw/` folder

2. Preprocess
   - Removes unnecessary columns
   - Cleans string values
   - Converts date fields into usable features
   - Encodes categorical variables
   - Drops duplicates and missing rows

3. Validate
   - Uses Great Expectations to check that the processed training data has the expected columns,
   - verifies key fields are not null,
   - ensures numeric values remain in expected ranges.

## Project structure

```bash
module-03-dvc-gx/
└── exercise-starter/
    ├── data/
    │   ├── raw/
    │   ├── processed/
    │   └── source/
    ├── reports/
    ├── src/
    │   ├── ingest.py
    │   ├── preprocess.py
    │   └── validate.py
    ├── dvc.yaml
    ├── params.yaml
    ├── requirements.txt
    └── README.md
```

## Key files

- `dvc.yaml` - Defines the DVC pipeline stages and outputs.
- `params.yaml` - Stores configuration values for ingestion, preprocessing, and validation.
- `src/ingest.py` - Loads the source data and creates train/test splits.
- `src/preprocess.py` - Cleans and prepares the datasets for modeling.
- `src/validate.py` - Runs Great Expectations validation checks and saves the report.

## Setup

1. Create and activate a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
```

2. Install dependencies:

```bash
pip install -r requirements.txt
```

3. Initialize DVC if needed:

```bash
dvc init
```

## Run the pipeline

To reproduce the full pipeline:

```bash
dvc repro
```

This runs the stages in the order defined in `dvc.yaml`:

```bash
dvc repro
```

Expected behavior:

- raw train and test data are generated,
- processed datasets are created,
- validation is executed against the training dataset,
- a report is stored in `reports/validation_result.json`.

## Validation rules

The Great Expectations suite checks the following:

- expected columns are present,
- key columns do not contain null values,
- numeric values for `Price` and `Quantity` remain within configured ranges.

If validation fails, the script exits with a non-zero status so the pipeline can stop before bad data reaches model training.

## Output artifacts

After a successful pipeline run, these artifacts are generated:

- `data/raw/train.csv`
- `data/raw/test.csv`
- `data/processed/train_clean.csv`
- `data/processed/test_clean.csv`
- `reports/validation_result.json`

These files represent the versioned inputs and validation output used during the ML workflow.

## Notes

This project is an example of a data-quality-first ML pipeline. It helps ensure that the training data is clean, consistent, and versioned before model development begins.

A typical next step would be to train a model using the validated processed dataset and track training metrics with a separate experiment-tracking tool.

## Useful commands

```bash
# Re-run the pipeline
dvc repro

# Check DVC status
dvc status

# View pipeline graph
dvc dag
```

## Summary

This repository is a practical example of using DVC and Great Expectations together to manage data versioning and validation for model training workflows. It is a strong foundation for more advanced ML pipelines that require reproducibility, traceability, and reliable data quality checks.
