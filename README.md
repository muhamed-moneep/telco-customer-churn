# Telco Customer Churn Analysis

A data analysis project (originally for **CS 307 — Data Analysis and Algorithms**) that explores, cleans, and models a telecom customer dataset to understand **why customers churn** and which factors most strongly predict it — paired with two standalone interactive HTML tools for presenting the findings.

## Project Contents

| File | Description |
|---|---|
| `Project_TelcoCustomerChurn.ipynb` | The main analysis notebook — data cleaning, EDA, feature engineering, feature selection, probability/hypothesis testing, and clustering (see below). |
| `Customer_Churn_Report.pdf` | The written team report ("Customer Churn Analysis — Identifying Churn Factors and Recommending Retention Strategies"), summarizing the methodology, findings, and retention recommendations. |
| `Churn_Analytics_Dashboard.html` | A standalone, self-contained HTML dashboard visualizing churn KPIs and trends. |
| `Churn_Risk_Retention_GUI.html` | A standalone, self-contained HTML "Churn Risk & Retention Advisor" tool. |

> **Note:** The dataset file itself (`TelcoCustomerChurn.csv`) is not included in this archive — the notebook was originally run in Google Colab with the CSV uploaded at runtime (`from google.colab import files`). To rerun the notebook, place the standard [Telco Customer Churn dataset](https://www.kaggle.com/datasets/blastchar/telco-customer-churn) as `TelcoCustomerChurn.csv` alongside the notebook, or adapt the loading cell.

## Notebook Walkthrough

The notebook is organized as a full analysis pipeline:

### 1. Data Cleaning & Validation
- Inspects shape, dtypes, and summary statistics of the raw dataset.
- Checks for missing values (none found) and duplicate rows/customer IDs (none found).
- Validates that `TotalCharges ≈ tenure × MonthlyCharges` to catch data-entry inconsistencies.
- Detects outliers in `tenure`, `MonthlyCharges`, and `TotalCharges` using three methods: **IQR**, **boxplots**, and **Z-score** (no significant outliers found).

### 2. Exploratory Data Analysis (EDA)
- Overall churn rate (~26.6% churned vs ~73.4% retained).
- Distributions of numeric features (tenure, senior citizen, monthly/total charges) and all categorical features.
- Bivariate analysis of churn vs. tenure, monthly charges, and total charges.
- Correlation heatmap across numeric features and churn.

### 3. Feature Engineering
- `tenure_group` — buckets customers into subscription-length groups (0–12, 12–24, 24–48, 48–72 months).
- `avg_monthly_spend` — actual average spend derived from total charges and tenure.
- `num_extra_services` — count of add-on services (Online Security, Backup, Device Protection, Tech Support, Streaming TV/Movies) a customer subscribes to.
- Additional binary flags (e.g. `has_internet`, `is_month_to_month`).
- One-hot encoding of remaining categorical columns to prepare the modeling matrix.

### 4. Feature Selection
Compares multiple feature selection approaches against a Logistic Regression cross-validation baseline:
- **Filter methods:** Variance Threshold, Mutual Information, SelectKBest (ANOVA F-test)
- **Wrapper method:** Recursive Feature Elimination (RFE)
- **Embedded methods:** Lasso (L1) and Logistic Regression with L1 penalty
- **Extra comparison:** Random Forest feature importance

**Consistently important features across methods:** `is_month_to_month`, `tenure`, `Contract_Two year`, `TotalCharges`, `InternetService_Fiber optic`, `PaymentMethod_Electronic check`.

### 5. Probability & Hypothesis Testing
- Computes P(Churn) overall and conditional on contract type — month-to-month customers churn far more than average.
- Applies **Bayes' Theorem** to show that, given a customer churned, they were very likely on a month-to-month contract.
- Runs **Chi-square tests of independence** (α = 0.05) confirming statistically significant associations between churn and both **Payment Method** and **Contract Type** (electronic check payers and month-to-month customers churn disproportionately).

### 6. Dimensionality Reduction & Clustering
- Projects customers into 2D space with **PCA** and visualizes churn separation.
- Uses the **Elbow Method** to choose an optimal number of clusters.
- Runs **K-Means** (k=3) on the PCA-reduced data and evaluates cluster quality with a **Silhouette Score**.
- Shows that unsupervised clusters (formed without using the churn label) still separate into distinct high-risk vs. loyal customer segments — useful for targeting retention efforts.

## Key Findings

- **Contract type** is the strongest churn driver: month-to-month customers churn at a much higher rate than one- or two-year contract holders.
- **Payment method** matters: customers paying by electronic check churn more than those using automatic bank transfer, automatic credit card, or mailed check.
- **Tenure and total charges** are inversely related to churn — newer/shorter-tenure customers are more likely to leave.
- Customer segments formed via clustering (independent of the churn label) still show clearly different churn rates, supporting a segmented retention strategy — prioritizing the high-risk segment (short tenure, month-to-month, fiber optic, electronic check) for targeted offers.

## Interactive HTML Tools

Both HTML files are self-contained (no server or build step needed) — just open them directly in a browser:

- **`Churn_Analytics_Dashboard.html`** — a KPI-style dashboard summarizing churn metrics and trends visually.
- **`Churn_Risk_Retention_GUI.html`** — an interactive advisor-style tool for exploring churn risk and retention scenarios.

## Requirements (to rerun the notebook)

- Python 3
- `pandas`, `numpy`, `matplotlib`, `seaborn`, `scipy`
- `scikit-learn` (for `train_test_split`, `LogisticRegression`, `RandomForestClassifier`, `VarianceThreshold`, `mutual_info_classif`, `SelectKBest`, `f_classif`, `RFE`, `Lasso`, `StandardScaler`, `PCA`, `KMeans`, `silhouette_score`)

Install with:
```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn
```

## Usage

1. Place `TelcoCustomerChurn.csv` in the same directory as the notebook (or update the loading cell to point to your local dataset path).
2. Open `Project_TelcoCustomerChurn.ipynb` in Jupyter Notebook/Lab (or Google Colab) and run all cells top to bottom.
3. Open either `.html` file directly in a web browser to view the standalone dashboard/advisor tool.
4. Refer to `Customer_Churn_Report.pdf` for the full written write-up and recommendations.
