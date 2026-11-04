# Dataset Documentation

## Dataset Source

This project uses the Synthetic Anti-Money Laundering Dataset (SAML-D).

- **Source:** [Kaggle – Synthetic Transaction Monitoring Dataset (AML)](https://www.kaggle.com/datasets/berkanoztas/synthetic-transaction-monitoring-dataset-aml)
- **Original Dataset Size:** Approximately 9,504,852 transactions.
- **Original Features:** 12 columns.

## Data Preparation Workflow

The dataset went through the following stages:

### 1. Original Dataset
The original SAML-D dataset was used as the primary data source.

### 2. Data Selection
A subset of approximately 100K transactions was selected from the original dataset.

The initial dataset used in the cleaning notebook contained 100,500 rows and 12 columns.

### 3. Data Cleaning
The following operations were performed:

- Detected and removed 2,846 duplicate records.
- Corrected data types.
- Handled missing categorical values.
- Removed records with missing critical fields.
- Investigated transaction amount outliers and retained them.

The resulting cleaned dataset contained 97,066 rows and 12 columns.

### 4. Feature Engineering
Additional data preparation and feature engineering operations were applied, resulting in a dataset containing 14 columns.

### 5. Final Modeling Dataset
The final dataset used for model development contained 97,065 rows and 14 columns.

## Target Variable

The target variable is `Is_laundering`:

- `0`: Legitimate transaction.
- `1`: Money laundering transaction.

## Data Availability

The raw and processed dataset files are not included in this repository.

Users should obtain the original dataset from the Kaggle source and reproduce the preparation workflow using the project notebooks.

## Important Note

The dataset is synthetic and intended for educational and research purposes. Its results should not be interpreted as evidence of performance on real-world financial transactions.
