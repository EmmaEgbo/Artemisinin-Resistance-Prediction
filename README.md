# Predicting Artemisinin Resistance in *Plasmodium falciparum*

## Project Overview

This project aims to predict Artemisinin resistance in *Plasmodium falciparum*, the parasite responsible for the deadliest form of malaria. Artemisinin-based combination therapies (ACTs) are the frontline treatment for uncomplicated malaria, but the emergence of resistance threatens global malaria control and elimination efforts.

The data comes from the [MalariaGEN *Plasmodium falciparum* Community Project](https://www.malariagen.net/), which provides open-access genetic and phenotypic datasets. In this project, we use:

* **Genotype data** (mutations in key drug resistance genes)
* **Inferred resistance data** (samples classified as Sensitive, Resistant, or Undetermined)

The goal is to build predictive models that identify resistance based on genetic markers.

---

## Data Loading and Processing

### Data Loading

Two datasets were used:

1. **Genotype Data** – mutations in resistance-associated genes.
2. **Resistance Status Data** – inferred drug resistance classifications.

Both were loaded into pandas DataFrames (`genotypes`, `resistance`) using `pd.read_csv` with tab separators.

### Initial Exploration

* `.head()` for a quick preview of structure and values
* `.info()` for summary statistics and missing values

Key findings:

* `genotypes`: 40 columns, 24,409 entries.
* `resistance`: 11 columns, 24,409 entries.
* The `kelch13_349-726_ns_changes` column had many missing values.

### Data Processing

1. **Genotypes** – raw calls (e.g., "N", "S,N", "-", "!") were converted into binary format:

   * `0` = wildtype
   * `1` = mutation present
   * `NaN` = missing/ambiguous

2. **Resistance Data** – categorical values mapped to binary:

   * Resistant → `1`
   * Sensitive → `0`
   * Undetermined → `NaN`

---

## Data Merging and Preparation

* Merged `geno_proc` and `res_proc` on sample identifiers.
* Target variable (`y`) = Artemisinin resistance (0/1).
* Feature set (`X`) = all other columns (excluding identifiers).

### Target Distribution

* Sensitive: 70.25%
* Resistant: 16.00%
* Undetermined: 13.75%

Undetermined samples were removed, leaving:

* Sensitive: 81.45%
* Resistant: 18.55%

### Cleaning

* Dropped `kelch13_349-726_ns_changes` (too many missing values).
* Dropped duplicate `sample` column.
* Imputed missing values with `SimpleImputer(strategy="mean")`.

### Train/Test Split

* 80/20 split (`train_test_split`)
* Stratified by resistance status
* Final sets: `X_train`, `X_test`, `y_train`, `y_test`

---

## Model Training and Evaluation

Three models were trained:

1. **Logistic Regression** – simple, interpretable baseline.
2. **Random Forest** – ensemble of decision trees, captures non-linear patterns.
3. **XGBoost** – gradient boosting, highly effective for structured data.

### Evaluation Metrics

* **Classification Report** (precision, recall, F1)
* **Confusion Matrix** (TP, FP, TN, FN)
* **ROC AUC** (discrimination ability)
* **Precision-Recall AUC** (important for imbalanced datasets)

Metrics were compiled into a results DataFrame (`results_df`).

---

## Results and Discussion

* **Logistic Regression** – reliable baseline with balanced performance.
* **Random Forest** – better at capturing complex interactions.
* **XGBoost** – best-performing model with highest recall, F1, and ROC AUC.

High recall for resistant samples is especially important for public health, as it ensures most resistant cases are identified, even if some false positives occur.

---

## Conclusion

This project demonstrates that Artemisinin resistance can be predicted from genotype data with high accuracy. XGBoost outperformed the other models, highlighting the predictive power of genetic markers in *P. falciparum*.

---

## Future Work

* Improve feature engineering to capture functional impacts of mutations.
* Test alternative imputation strategies (e.g., KNN, multiple imputation).
* Integrate additional biological and geographical data.
* Validate models on independent datasets for generalizability.
* Apply interpretability methods (e.g., SHAP values) to identify key markers.
* Extend the approach to other antimalarial drugs.
