# Anti-Money Laundering (AML) Transaction Classification

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-Machine%20Learning-orange)
![XGBoost](https://img.shields.io/badge/Model-XGBoost-blue)
![CatBoost](https://img.shields.io/badge/Model-CatBoost-yellow)
![Status](https://img.shields.io/badge/Status-Model%20Evaluation-success)

## Project Overview

Money laundering detection is a major challenge for financial institutions due to the complexity and diversity of suspicious financial transactions.

This project aims to develop a machine learning-based classification approach to identify potentially suspicious transactions and distinguish them from legitimate financial activities.

Using the Synthetic Anti-Money Laundering Dataset (SAML-D), the project explores data preprocessing, feature engineering, and machine learning classification techniques.

Three machine learning models were developed and evaluated:

- Random Forest
- XGBoost
- CatBoost

The models were compared using evaluation metrics suitable for imbalanced classification, with particular attention to Precision, Recall, F1-score, PR-AUC, and ROC-AUC.

## Project Objectives

- Analyze and preprocess financial transaction data.
- Investigate potential target leakage and select relevant features.
- Apply feature engineering techniques to improve model input representation.
- Train and compare multiple machine learning classifiers.
- Optimize decision thresholds based on validation performance.
- Evaluate the selected model on a separate test set.

## Dataset Information

This project uses the **Synthetic Anti-Money Laundering Dataset (SAML-D)**, following a multi-stage data preparation process.

**1. Original Dataset**

The original SAML-D dataset contains approximately **9,504,852 synthetic financial transactions** and was used as the primary data source for this project.

**2. Selected Dataset (100K Transactions)**

A subset of **100,000 transactions** was selected from the original dataset to be used in the project.

**3. Feature-Engineered Dataset**

The selected data underwent data preparation and feature engineering, resulting in the following dataset:

| Property | Value |
|---|---:|
| Selected dataset | 100,000 transactions |
| Processed dataset (before final cleaning) | 97,066 rows × 14 columns |
| Final dataset (after cleaning) | 97,065 rows × 14 columns |
| Target variable | `Is_laundering` |
| Task | Binary Classification |

**Final Target Distribution**

| Class | Transactions | Percentage |
|---|---:|---:|
| Legitimate (0) | 74,585 | 76.84% |
| Laundering (1) | 22,480 | 23.16% |
| **Total** | **97,065** | **100%** |

The final processed dataset was used for model training, validation, and testing.

**Dataset Source**

- Kaggle: [Synthetic Transaction Monitoring Dataset (AML)](https://www.kaggle.com/datasets/berkanoztas/synthetic-transaction-monitoring-dataset-aml)
- Research Paper: Oztas et al., *Enhancing Anti-Money Laundering: Development of a Synthetic Transaction Monitoring Dataset*, IEEE ICEBE 2023.

## Methodology

The project follows a structured machine learning workflow.

### 1. Data Cleaning

- Removed duplicate records.
- Handled missing target values.
- Validated binary target labels.
- Converted Boolean-like features into numerical values.
- Converted transaction amounts into numeric format and handled negative values.

### 2. Leakage Investigation

Potential target leakage was investigated before model training.

The following features were excluded:

- `Laundering_type`: Excluded because it contains typological information strongly associated with the target label.
- `Sender_frequancy`
- `Reciever_frequancy`
- `hour`: Excluded after generating cyclical time features.

### 3. Feature Engineering

Cyclical encoding was applied to the transaction hour using sine and cosine transformations:

- `hour_sin`
- `hour_cos`

The `Amount` feature was also converted to numeric format and clipped at zero to prevent negative values.

### 4. Feature Selection

The final model input contains 11 features:

**Numerical Features**
- `Amount`
- `is_payment_same`
- `is_bank_same_location`
- `hour_sin`
- `hour_cos`

**Categorical Features**
- `Day`
- `Payment_currency`
- `Received_currency`
- `Sender_bank_location`
- `Receiver_bank_location`
- `Payment_type`

The target variable and excluded leakage-related features were not included in the model inputs.

### 5. Data Splitting

The dataset was divided into three subsets using stratified sampling:

| Dataset | Percentage |
|---|---:|
| Training | 60% |
| Validation | 20% |
| Testing | 20% |

A fixed random state (`42`) was used to ensure reproducibility.

### 6. Preprocessing

A Scikit-learn preprocessing pipeline was implemented:

- Numerical features: Missing values handled using median imputation.
- Categorical features: Missing values handled using most-frequent imputation.
- Categorical encoding: One-Hot Encoding.
- `ColumnTransformer` and `Pipeline` were used to integrate preprocessing with model training.

## Machine Learning Models

Three classification algorithms were trained and evaluated:

| Model | Configuration |
|---|---|
| Random Forest | 200 estimators, balanced class weights |
| XGBoost | 300 estimators, maximum depth 5, learning rate 0.05 |
| CatBoost | 800 iterations, depth 6, learning rate 0.03, balanced class weights |

CatBoost was trained using One-Hot Encoded categorical features within the preprocessing pipeline.

## Model Selection & Threshold Optimization

Instead of relying only on the default classification threshold of 0.50, a threshold optimization process was conducted using the validation set.

The selection strategy prioritized:

- A minimum Recall of 0.75.
- Maximum Precision among thresholds satisfying the Recall requirement.
- F1-score as a secondary comparison metric.

### Validation Results

| Model | Selected Threshold | Precision | Recall | F1-score |
|---|---:|---:|---:|---:|
| CatBoost | **0.57** | **0.6611** | 0.7551 | **0.7050** |
| XGBoost | 0.26 | 0.5911 | 0.7636 | 0.6663 |
| Random Forest | 0.28 | 0.5588 | **0.7738** | 0.6489 |

CatBoost achieved the highest Precision and F1-score among the evaluated models while satisfying the minimum Recall requirement.

Therefore, CatBoost was selected for final test evaluation.

## Final Model Evaluation

The selected CatBoost model was evaluated on the test set using the selected threshold of `0.57`.

### Test Results

| Metric | Score |
|---|---:|
| Accuracy | 85.89% |
| Precision | 66.69% |
| Recall | 78.02% |
| F1-score | 71.91% |
| PR-AUC (Average Precision) | 75.83% |
| ROC-AUC | 92.54% |

### Confusion Matrix

| Actual / Predicted | Legitimate | Laundering |
|---|---:|---:|
| Legitimate | 13,165 | 1,752 |
| Laundering | 988 | 3,508 |

The selected model detected 3,508 laundering transactions in the test set, while 988 laundering transactions were classified as legitimate.

## Technologies & Libraries

- Python
- Pandas
- NumPy
- Scikit-learn
- XGBoost
- CatBoost
- Matplotlib
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
│   └── AML_Model_Training_and_Evaluation.ipynb
│
├── src/
│   └── README.md
│
├── results/
│   ├── figures/
│   └── README.md
│
└── docs/
    └── README.md
```

## Installation & Usage

Clone the repository:

```bash
git clone https://github.com/money-laundering-detector/AML-Money-Laundering-Detection.git

cd AML-Money-Laundering-Detection
```

Install the required libraries:

```bash
pip install -r requirements.txt
```

Open the notebook using Jupyter:

```bash
jupyter notebook
```

The processed dataset should be obtained from the project team or its documented source and placed in the appropriate location before running the notebook.

## Limitations & Future Improvements

- The dataset is synthetic and may not fully represent real-world financial transaction patterns.
- Further evaluation on real-world or independently generated transaction datasets is required.
- Additional feature engineering may improve detection performance.
- Native categorical feature handling in CatBoost can be investigated.
- Hyperparameter optimization can be explored.
- Model explainability techniques such as SHAP can be applied to understand feature contributions.
- Further threshold analysis can help balance fraud detection sensitivity and false-positive alerts.

## Contributors

| Name | Major | GitHub |
|---|---|---|
| Ala'a Yahia Almousa | Data Science and Artificial Intelligence | [Alaa-20040521](https://github.com/Alaa-20040521) |
| Abdellrahman Abdallah Alhanaqtah | Intelligent Systems Engineering | [abdellrahmanHq](https://github.com/abdellrahmanHq) |
| Rana Kamel Khaleel Jumah | Computer Science | [rana03jumah](https://github.com/rana03jumah) |
| Abdullah Anas Zahran | Computer Information Systems | [abdullahzahran36](https://github.com/abdullahzahran36) |
| Laith Sami Ismail Alhawajreh | Artificial Intelligence and Data Science | [Laith-Alhawajreh](https://github.com/Laith-Alhawajreh) |
| Obaeda Majde Abu Baker | Computer Science | [Obaedamjde](https://github.com/Obaedamjde) |
| Oula Saleem Hanandeh | Data Science and Artificial Intelligence | [olasaleemhanandeh-ux](https://github.com/olasaleemhanandeh-ux) |

---

**Disclaimer:** This project is developed for educational and research purposes using synthetic transaction data. It is not intended to replace established financial institution compliance systems or regulatory procedures.
