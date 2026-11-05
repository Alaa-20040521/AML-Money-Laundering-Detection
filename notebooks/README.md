# Notebooks Documentation

This directory contains the Jupyter Notebooks used throughout the AML (Anti-Money Laundering) project.

## Notebook Workflow

### 1. Data Understanding and Exploratory Data Analysis

**Notebook:** `Data_Understanding_and_Exploratory_Data_Analysis.ipynb`

**Purpose:** Understand the cleaned dataset, assess its quality, and explore transaction patterns before feature engineering.

Main tasks:

- Dataset inspection and structure validation.
- Missing values and duplicate records analysis.
- Data type and value assessment.
- Numerical and categorical feature analysis.
- Outlier investigation.
- Target variable distribution analysis.
- Exploratory transaction pattern analysis.

### 2. Feature Engineering and Preprocessing

**Notebook:** `Feature_Engineering_and_Preprocessing.ipynb`

**Purpose:** Transform the cleaned dataset into model-ready features and prepare the datasets required for model training.

Main tasks:

- Feature engineering and cyclical time encoding.
- Boolean feature creation and numerical validation.
- Target leakage investigation.
- Feature selection.
- Stratified training, validation, and test splitting.
- Numerical and categorical preprocessing pipeline construction.
- Exporting the feature-engineered dataset and preprocessing artifacts.

### 3. Model Training and Evaluation

**Notebook:** `AML_Model_Training_and_Evaluation_Organized.ipynb`

**Purpose:** Train, compare, and evaluate machine learning models using the prepared datasets and preprocessing pipeline.

Main tasks:

- Loading prepared datasets and preprocessing artifacts.
- Random Forest, XGBoost, and CatBoost training.
- Validation-based model comparison.
- Classification threshold optimization.
- Final model selection.
- Test set evaluation.
- Performance visualization using Confusion Matrix, ROC Curve, and Precision-Recall Curve.

## Execution Order

Run the notebooks in the following order:

1. `Data_Understanding_and_Exploratory_Data_Analysis.ipynb`
2. `Feature_Engineering_and_Preprocessing.ipynb`
3. `AML_Model_Training_and_Evaluation_Organized.ipynb`

**Note:** Ensure that the required input dataset is available before running the first notebook. The Feature Engineering notebook generates the prepared datasets and preprocessing artifacts required by the Model Training notebook. Run the notebooks in sequence to ensure all required files are generated and available.
