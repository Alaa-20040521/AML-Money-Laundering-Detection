# Anti-Money Laundering (AML) Transaction Classification

## Project Overview

Money laundering detection is a challenging task for financial institutions due to the complexity of financial transactions and the difficulty of distinguishing suspicious activities from legitimate ones.

This project focuses on developing a machine learning-based approach to classify financial transactions as either legitimate or potentially related to money laundering.

Using a subset derived from the **Synthetic Anti-Money Laundering Dataset (SAML-D)**, the project follows a structured workflow covering data understanding, data cleaning, feature engineering, preprocessing, model training, threshold optimization, and evaluation.

Three machine learning models were implemented and compared:

- Random Forest
- XGBoost
- CatBoost

The models were evaluated using metrics suitable for imbalanced classification, including Precision, Recall, F1-score, PR-AUC, and ROC-AUC.

The project also investigates decision threshold optimization to balance the detection of laundering transactions against false-positive predictions.

## Project Objectives

- Understand and explore synthetic financial transaction data.
- Identify and handle missing values and duplicate records.
- Investigate potential target leakage before model training.
- Apply feature engineering and feature selection techniques.
- Build preprocessing pipelines for numerical and categorical features.
- Train and compare multiple machine learning classifiers.
- Optimize classification thresholds using validation data.
- Evaluate model performance using a separate test split.

## Dataset

### Dataset Source

The project uses the **Synthetic Anti-Money Laundering Dataset (SAML-D)**.

- **Kaggle:** [Synthetic Transaction Monitoring Dataset (AML)](https://www.kaggle.com/datasets/berkanoztas/synthetic-transaction-monitoring-dataset-aml)
- **Research Paper:** Oztas et al., *Enhancing Anti-Money Laundering: Development of a Synthetic Transaction Monitoring Dataset*, IEEE ICEBE 2023.

### Dataset Preparation

The project dataset was prepared through multiple stages:

**1. Original Dataset**

The original SAML-D dataset contains approximately **9,504,852 synthetic financial transactions** across 12 columns.

**2. Selected Dataset**

A subset of approximately 100K transactions was selected from the original dataset.

The data understanding and cleaning notebook reports an initial dataset size of **100,500 rows × 12 columns**.

**3. Data Cleaning**

The initial dataset underwent data cleaning, including:

- Detection and removal of 2,846 exact duplicate rows.
- Correction of data types.
- Handling missing categorical values.
- Removal of records with missing critical fields, including account identifiers, transaction amount, and date.
- Investigation of transaction amount outliers, which were retained during this cleaning stage.

After this stage, the cleaned dataset contained **97,066 rows × 12 columns**.

**4. Feature Engineering and Model Preparation**

Additional feature engineering and preparation steps produced a dataset with 14 columns.

| Property | Value |
|---|---:|
| Original dataset | 9,504,852 transactions |
| Selected dataset | 100,500 rows |
| Cleaned dataset | 97,066 rows |
| Feature-engineered dataset before final cleaning | 97,066 rows × 14 columns |
| Final modeling dataset | 97,065 rows × 14 columns |

The final modeling dataset was used for model development and evaluation.

### Target Variable

The target variable is `Is_laundering`, which represents a binary classification task:

- `0`: Legitimate transaction.
- `1`: Money laundering transaction.

### Target Distribution

The final modeling dataset contains the following class distribution:

| Class | Transactions | Percentage |
|---|---:|---:|
| Legitimate (0) | 74,585 | 76.84% |
| Laundering (1) | 22,480 | 23.16% |
| **Total** | **97,065** | **100%** |

The dataset is imbalanced, making Precision, Recall, F1-score, and Precision-Recall AUC important evaluation metrics.

## Methodology

The project follows a structured machine learning workflow.

### 1. Data Understanding and Cleaning

The initial data inspection included:

- Examining dataset dimensions and data types.
- Identifying missing values and their percentages.
- Detecting exact duplicate records.
- Converting account identifiers into string data types.
- Parsing transaction dates and times.
- Replacing missing `Payment_type` and `Laundering_type` values with explicit categories.
- Removing records with missing critical fields.
- Investigating outliers in the `Amount` feature.

A total of 2,846 exact duplicate rows were identified and removed.

Missing values in the `Time` column remained after this cleaning stage and were handled through subsequent feature preparation and preprocessing where applicable.

### 2. Leakage Investigation

Potential target leakage was investigated before model training.

The following features were excluded from model inputs:

- `Laundering_type`: Excluded because it contains typological information strongly associated with the target label.
- `Sender_frequancy`
- `Reciever_frequancy`
- `hour`: Excluded after generating cyclical time features.

The target variable `Is_laundering` was also excluded from the input features.

### 3. Feature Engineering

Feature engineering was applied to improve the representation of transaction data.

**Cyclical Time Encoding**

The transaction hour was transformed using sine and cosine functions:

- `hour_sin`
- `hour_cos`

This transformation represents the cyclical nature of time, allowing the model to capture relationships between hours near the beginning and end of the day.

**Amount Processing**

The `Amount` feature was converted to numeric format, and negative values were clipped at zero during model preparation.

**Boolean Feature Processing**

Boolean-like features were converted into numerical representations:

- `is_payment_same`
- `is_bank_same_location`

### 4. Feature Selection

The final model input consists of **11 features**.

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

The dataset was divided into three subsets using stratified splitting:

| Dataset | Percentage |
|---|---:|
| Training | 60% |
| Validation | 20% |
| Testing | 20% |

A fixed random state of `42` was used to support reproducibility.

| Split | Number of Records |
|---|---:|
| Training | 58,239 |
| Validation | 19,413 |
| Testing | 19,413 |

### 6. Data Preprocessing

A Scikit-learn preprocessing pipeline was implemented using `ColumnTransformer` and `Pipeline`.

The preprocessing steps included:

- **Numerical features:** Missing values handled using median imputation.
- **Categorical features:** Missing values handled using most-frequent imputation.
- **Categorical encoding:** One-Hot Encoding with `handle_unknown="ignore"`.

The preprocessing pipeline was integrated with the classification models.

## Machine Learning Models

Three classification algorithms were trained and evaluated.

| Model | Main Configuration |
|---|---|
| Random Forest | 200 estimators, balanced class weights |
| XGBoost | 300 estimators, max depth 5, learning rate 0.05 |
| CatBoost | 800 iterations, depth 6, learning rate 0.03, balanced class weights |

CatBoost was trained using One-Hot Encoded categorical features within the preprocessing pipeline.

### Validation Performance

The following results were obtained on the validation set:

| Model | PR-AUC | ROC-AUC |
|---|---:|---:|
| Random Forest | 0.6817 | 0.8820 |
| XGBoost | 0.7228 | 0.8957 |
| CatBoost | 0.7447 | 0.9206 |

CatBoost achieved the highest PR-AUC and ROC-AUC among the three evaluated models.

## Model Selection and Threshold Optimization

The default classification threshold of `0.50` was not assumed to be optimal for this imbalanced classification task.

Instead, threshold optimization was performed using the validation set.

The selection strategy prioritized:

1. A minimum Recall of 0.75.
2. Maximum Precision among thresholds satisfying the Recall requirement.
3. F1-score as a secondary comparison metric.

### Threshold Comparison

| Model | Selected Threshold | Precision | Recall | F1-score |
|---|---:|---:|---:|---:|
| CatBoost | 0.57 | 0.6611 | 0.7551 | 0.7050 |
| XGBoost | 0.26 | 0.5911 | 0.7636 | 0.6663 |
| Random Forest | 0.28 | 0.5588 | 0.7738 | 0.6489 |

CatBoost achieved the highest Precision and F1-score among the evaluated models while satisfying the minimum Recall requirement.

Therefore, CatBoost was selected for final test evaluation using a threshold of `0.57`.

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

The model correctly classified 13,165 legitimate transactions and detected 3,508 laundering transactions in the test set.

However, 988 laundering transactions were classified as legitimate, while 1,752 legitimate transactions were classified as laundering.

These results highlight the trade-off between detecting suspicious transactions and generating false-positive predictions.

## Technologies and Libraries

- Python
- Pandas
- NumPy
- Scikit-learn
- Random Forest
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
│   ├── Data_Understanding_and_Cleaning.ipynb
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

*Note: The directory and notebook names should match the files actually committed to the repository.*

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

Obtain the required dataset files from their documented source or the project team.

Place each file in the path expected by its corresponding notebook.

The original and processed datasets are not necessarily included in the repository.

### 4. Run the Notebooks

Open the notebooks using Jupyter Notebook or Google Colab.

Run the data understanding and cleaning workflow first, followed by the feature engineering and model training workflow.

## Limitations and Future Improvements

- The dataset is synthetic and may not fully represent real-world financial transaction patterns.
- Further evaluation on real-world or independently generated transaction datasets is required.
- Additional feature engineering may improve classification performance.
- Native categorical feature handling in CatBoost can be investigated.
- Hyperparameter optimization can be explored.
- SHAP can be applied to improve model interpretability and understand feature contributions.
- Further threshold analysis can help balance detection sensitivity and false-positive alerts.
- Future experiments should preserve an untouched test set for final evaluation after all model selection decisions.

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

## Disclaimer

This project was developed for educational and research purposes using synthetic transaction data. It is not intended to replace established financial institution compliance systems, professional investigations, or regulatory procedures.
