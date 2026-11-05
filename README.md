# Anti-Money Laundering (AML) Transaction Classification

## Project Overview

Money laundering detection is a challenging task for financial institutions due to the complexity of financial transactions and the difficulty of distinguishing suspicious activities from legitimate ones.

This project focuses on developing a machine learning-based approach to classify financial transactions as either legitimate or potentially related to money laundering.

Using a subset derived from the Synthetic Anti-Money Laundering Dataset (SAML-D), the project follows a structured workflow covering data understanding, exploratory data analysis, feature engineering, preprocessing, model training, threshold optimization, and evaluation.

Three machine learning models are implemented and compared:

- Random Forest
- XGBoost
- CatBoost

The models are evaluated using metrics suitable for imbalanced classification, including Precision, Recall, F1-score, Average Precision (PR-AUC), and ROC-AUC.

The project also investigates classification threshold optimization to balance the detection of laundering transactions against false-positive predictions.

## Project Objectives

- Understand and explore synthetic financial transaction data.
- Assess data quality, including missing values and duplicate records.
- Investigate potential target leakage before model training.
- Apply feature engineering and feature selection techniques.
- Build preprocessing pipelines for numerical and categorical features.
- Train and compare multiple machine learning classifiers.
- Optimize classification thresholds using validation data.
- Evaluate final model performance using an independent test set.

## Dataset

### Dataset Source

The project uses the Synthetic Anti-Money Laundering Dataset (SAML-D).

- **Source:** [Kaggle – Synthetic Transaction Monitoring Dataset (AML)](https://www.kaggle.com/datasets/berkanoztas/synthetic-transaction-monitoring-dataset-aml)
- **Research Paper:** Oztas et al., *Enhancing Anti-Money Laundering: Development of a Synthetic Transaction Monitoring Dataset*, IEEE ICEBE 2023.
- **Original Dataset Size:** Approximately 9,504,852 transactions.
- **Original Features:** 12 columns.

### Dataset Preparation

The project dataset was prepared through multiple stages.

#### 1. Original Dataset

The original SAML-D dataset was used as the primary data source.

#### 2. Data Selection

A subset of approximately 100K transactions was selected from the original dataset.

The initial selected dataset contained 100,500 rows and 12 columns.

#### 3. Data Cleaning

The cleaning process included:

- Detecting and removing exact duplicate records.
- Correcting data types.
- Handling missing categorical values.
- Removing records with missing critical fields.
- Investigating transaction amount outliers and retaining potential outliers.

The resulting cleaned dataset contains **97,066 rows and 12 columns**.

#### 4. Feature Engineering and Preprocessing

The cleaned dataset was transformed through feature engineering and preprocessing.

The operations included:

- Extracting the transaction hour and day of the week.
- Applying cyclical encoding to the transaction hour using sine and cosine transformations.
- Creating Boolean features to identify matching payment and received currencies.
- Creating Boolean features to identify matching sender and receiver bank locations.
- Investigating potential target leakage.
- Selecting the final model input features.

Potential transaction frequency features were excluded from the final model inputs.

#### 5. Final Modeling Dataset

The final model input consists of **11 selected features**.

**Categorical Features (6):**
- Day
- Payment_currency
- Received_currency
- Sender_bank_location
- Receiver_bank_location
- Payment_type

**Numerical Features (5):**
- Amount
- is_payment_same
- is_bank_same_location
- hour_sin
- hour_cos

The target variable is `Is_laundering`.

The cleaned dataset contains 97,066 records, divided using stratified splitting:

| Dataset | Records | Percentage |
|---|---:|---:|
| Training | 58,239 | 60% |
| Validation | 19,413 | 20% |
| Testing | 19,414 | 20% |
| **Total** | **97,066** | **100%** |

### Target Variable

The target variable is `Is_laundering`:

- `0`: Legitimate transaction.
- `1`: Money laundering transaction.

**Target Distribution:**

| Class | Transactions | Percentage |
|---|---:|---:|
| Legitimate (0) | 74,585 | 76.84% |
| Laundering (1) | 22,481 | 23.16% |
| **Total** | **97,066** | **100%** |

The target distribution indicates class imbalance, making Precision, Recall, F1-score, and Precision-Recall AUC important evaluation metrics.

## Methodology

The project follows a structured machine learning workflow.

### 1. Data Understanding and Exploratory Data Analysis

The initial analysis included:

- Examining dataset dimensions and data types.
- Identifying missing values.
- Detecting exact duplicate records.
- Investigating numerical and categorical features.
- Analyzing transaction amount distributions and potential outliers.
- Examining target class distribution.
- Exploring transaction patterns across payment types, currencies, days, and hours.

### 2. Feature Engineering

Feature engineering was applied to improve the representation of transaction data.

**Cyclical Time Encoding**

The transaction hour was transformed using sine and cosine functions:

- `hour_sin`
- `hour_cos`

This transformation represents the cyclical nature of time and preserves the relationship between hours near the beginning and end of the day.

**Amount Validation**

The `Amount` feature was converted to numeric format and checked for invalid and negative values.

**Boolean Feature Engineering**

Two Boolean features were created:

- `is_payment_same`
- `is_bank_same_location`

These features represent whether the payment and received currencies match, and whether the sender and receiver bank locations match.

### 3. Target Leakage Investigation

Potential target leakage was investigated before model training.

The following columns were excluded from model inputs:

- `Is_laundering`: Target variable.
- `Laundering_type`: Excluded because its typological information may reveal information related to the target.
- `hour`: Replaced by cyclical time features.
- `Sender_frequancy` and `Reciever_frequancy`: Excluded from the final model input features.

### 4. Feature Selection

The final model input consists of 11 features:

**Numerical Features:**
- Amount
- is_payment_same
- is_bank_same_location
- hour_sin
- hour_cos

**Categorical Features:**
- Day
- Payment_currency
- Received_currency
- Sender_bank_location
- Receiver_bank_location
- Payment_type

### 5. Data Splitting

The dataset was divided into three subsets using stratified splitting:

| Dataset | Percentage |
|---|---:|
| Training | 60% |
| Validation | 20% |
| Testing | 20% |

A fixed random state of 42 was used to support reproducibility.

### 6. Data Preprocessing

A Scikit-learn preprocessing pipeline was implemented using `ColumnTransformer` and `Pipeline`.

The preprocessing steps included:

- **Numerical features:** Median imputation.
- **Categorical features:** Most-frequent value imputation.
- **Categorical encoding:** One-Hot Encoding with `handle_unknown="ignore"`.

The preprocessing pipeline is fitted within each model pipeline using training data only.

### 7. Model Training and Evaluation

Three classification algorithms were trained and compared:

| Model | Main Configuration |
|---|---|
| Random Forest | 200 estimators, balanced class weights |
| XGBoost | 300 estimators, max depth 5, learning rate 0.05 |
| CatBoost | 800 iterations, depth 6, learning rate 0.03, balanced class weights |

CatBoost was trained using One-Hot Encoded categorical features rather than its native categorical feature handling.

### 8. Model Comparison and Threshold Optimization

Model ranking performance was evaluated on the validation set using:

- Average Precision (PR-AUC).
- ROC-AUC.

Classification threshold optimization was also performed using validation data.

The selection strategy prioritized:

1. A minimum Recall of 0.75.
2. Maximum Precision among thresholds satisfying the Recall requirement.
3. F1-score as a tie-breaker.

The model with the highest F1-score among eligible models was selected, using Precision as a tie-breaker.

### 9. Final Test Evaluation

The selected model was evaluated once on the held-out test set using the threshold selected from validation data.

The final evaluation includes:

- Accuracy
- Precision
- Recall
- F1-score
- Average Precision (PR-AUC)
- ROC-AUC
- Confusion Matrix
- ROC Curve
- Precision-Recall Curve

The test set was not used for model selection or threshold optimization.

## Technologies and Libraries

- Python
- Pandas
- NumPy
- Scikit-learn
- Random Forest
- XGBoost
- CatBoost
- Matplotlib
- Joblib
- Google Colab
- Jupyter Notebook

## Project Structure

```text
AML-Money-Laundering-Detection/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── data/
│   └── README.md
│
├── notebooks/
│   ├── Data_Understanding_and_Exploratory_Data_Analysis.ipynb
│   ├── Feature_Engineering_and_Preprocessing.ipynb
│   └── AML_Model_Training_and_Evaluation_Organized.ipynb
|   └── README.md
│
│
├── results/
│   └── README.md
│
└── docs/
    └── README.md
```

Note: Directory and notebook names should match the files actually committed to the repository.

## Installation and Usage

### 1. Clone the Repository

```bash
git clone https://github.com/money-laundering-detector/AML-Money-Laundering-Detection.git

cd AML-Money-Laundering-Detection
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### 3. Prepare the Dataset

Obtain the original dataset from its documented Kaggle source.

Place the required dataset file where the first notebook can access it.

The raw and processed datasets are not included in this repository.

### 4. Run the Notebooks

Open the notebooks using Jupyter Notebook or Google Colab.

Run the notebooks in the following order:

1. `Data_Understanding_and_Exploratory_Data_Analysis.ipynb`
2. `Feature_Engineering_and_Preprocessing.ipynb`
3. `AML_Model_Training_and_Evaluation_Organized.ipynb`

The Feature Engineering notebook generates the prepared datasets and preprocessing artifacts required by the Model Training notebook.

## Limitations and Future Improvements

- The dataset is synthetic and may not fully represent real-world financial transaction patterns.
- Further evaluation on real-world or independently generated transaction datasets is required.
- Additional feature engineering may improve classification performance.
- Native categorical feature handling in CatBoost can be investigated.
- Hyperparameter optimization can be explored.
- SHAP can be applied to improve model interpretability and understand feature contributions.
- Further threshold analysis can help balance detection sensitivity and false-positive alerts.
- Future experiments should preserve an untouched test set for final evaluation.

## Contributors

| Name | Major | GitHub |
|---|---|---|
| Ala'a Yahia Almousa | Data Science and Artificial Intelligence | [Alaa-20040521](https://github.com/Alaa-20040521) |
| Oula Saleem Hanandeh | Data Science and Artificial Intelligence | [olasaleemhanandeh-ux](https://github.com/olasaleemhanandeh-ux) |
| Abdullah Anas Zahran | Computer Information Systems | [abdullahzahran36](https://github.com/abdullahzahran36) |
| Laith Sami Ismail Alhawajreh | Artificial Intelligence and Data Science | [Laith-Alhawajreh](https://github.com/Laith-Alhawajreh) |
| Rana Kamel Khaleel Jumah | Computer Science | [rana03jumah](https://github.com/rana03jumah) |

## Disclaimer

This project was developed for educational and research purposes using synthetic transaction data. It is not intended to replace established financial institution compliance systems, professional investigations, or regulatory procedures.
