# Dataset Documentation

## Dataset Source

This project uses the Synthetic Anti-Money Laundering Dataset (SAML-D).

- **Source:** [Kaggle – Synthetic Transaction Monitoring Dataset (AML)](https://www.kaggle.com/datasets/berkanoztas/synthetic-transaction-monitoring-dataset-aml)
- **Original Dataset Size:** Approximately 9,504,852 transactions.
- **Original Features:** 12 columns.

## Data Preparation Workflow

The dataset underwent several preparation stages before being used for machine learning model development.

### 1. Original Dataset

The original SAML-D dataset was used as the primary data source.

### 2. Data Selection

A subset of approximately 100K transactions was selected from the original dataset.

The initial selected dataset contained 100,500 rows and 12 columns.

### 3. Data Cleaning

The following operations were performed:

- Detected and removed 2,846 duplicate records.
- Corrected data types.
- Handled missing categorical values.
- Removed records with missing critical fields.
- Investigated transaction amount outliers and retained potential outliers.

The resulting cleaned dataset contained **97,066 rows and 12 columns**, with no remaining missing values or exact duplicate records.

### 4. Feature Engineering and Preprocessing

Additional features were derived from the cleaned dataset to improve its representation for machine learning.

The operations included:

- Extracting the transaction hour and day of the week.
- Applying cyclical encoding to the transaction hour using sine and cosine transformations.
- Creating Boolean features to identify matching payment and received currencies.
- Creating Boolean features to identify matching sender and receiver bank locations.
- Investigating potential target leakage.
- Selecting the final model input features.

The complete feature-engineered dataset preserves the original information and includes the derived features.

### 5. Final Modeling Dataset

The final model input consists of **11 selected features**:

- **6 Categorical Features:** Day, Payment_currency, Received_currency, Sender_bank_location, Receiver_bank_location, and Payment_type.
- **5 Numerical Features:** Amount, is_payment_same, is_bank_same_location, hour_sin, and hour_cos.

The target variable is `Is_laundering`.

The 97,066 records were divided using stratified splitting:

| Dataset | Records | Percentage |
|---|---:|---:|
| Training | 58,239 | 60% |
| Validation | 19,413 | 20% |
| Testing | 19,414 | 20% |
| **Total** | **97,066** | **100%** |

The preprocessing pipeline applies median imputation to numerical features and most-frequent imputation followed by One-Hot Encoding to categorical features.

## Target Variable

The target variable is `Is_laundering`:

- `0`: Legitimate transaction.
- `1`: Money laundering transaction.

## Data Availability

The raw and processed dataset files are not included in this repository.

Users should obtain the original dataset from the Kaggle source and reproduce the preparation workflow using the project notebooks.

The Feature Engineering and Preprocessing notebook generates the prepared datasets and preprocessing artifacts required by the Model Training and Evaluation notebook.

## Important Notes

- The dataset is synthetic and intended for educational and research purposes.
- `Laundering_type` is retained for reference and analysis but excluded from model input features due to potential target leakage.
- Potential transaction amount outliers were investigated and retained rather than automatically removed.
- Model performance on this synthetic dataset should not be interpreted as evidence of equivalent performance on real-world financial transactions.
