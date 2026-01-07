# Medicare Part D Spending Prediction & Outlier Analysis

## 🎯 Project Overview
This project identifies "outlier" drugs in the Medicare Part D program—pharmaceuticals with unusually high spending that may indicate abnormal pricing by manufacturers. Using historical data (2019-2023) from the US Government’s Open Data Repository, I built a predictive pipeline to forecast future spending (2024-2026) and flag pricing anomalies.

**The Business Value:** Enables insurance providers and policymakers to identify potentially overpriced drugs, facilitating more informed formulary and financial strategies.

---

## 📊 The Dataset
* **Source:** [Data.gov](https://www.data.gov) (US Government’s Open Data Repository)
* **Unit of Analysis:** Unique combinations of Brand Name, Generic Name, and Manufacturer.
* **Key Variables:** * **Independent:** Brand/Generic/Manufacturer Names, Total Spending, Total Beneficiaries, Total Dosage Units.
    * **Target:** Future Average Spending Per Dosage Unit (Forecasted to 2026).
    * **Engineered Feature:** `Year` (Transformed from wide to long format for time-series modeling).

---

## 🛠️ Technical Workflow

### 1. Data Cleaning & API Integration
* **Handling Nulls:** Implemented **Bidirectional Imputation** (Forward-fill then Backward-fill) for spending rates to preserve historical trends.
* **Confounder Control:** Used the **OpenFDA API** to check for "Orphan Drug" status. I identified that rare-disease drugs (which naturally have high costs) were not present in this specific dataset, simplifying the outlier logic.

### 2. Exploratory Data Analysis (EDA)
* **Hypothesis Testing:** Contrary to my initial hypothesis, EDA revealed that **higher volume (beneficiaries) does NOT correlate with higher average cost per unit**. In fact, price per unit is independent of demand, suggesting manufacturer pricing power is the dominant factor.
* **Skewness:** Data was heavily right-skewed (Mean >> Median), requiring log-transformation for better modeling.

### 3. Machine Learning Model
* **Task:** Time Series Forecasting followed by Statistical Outlier Detection.
* **Algorithm:** Linear Regression (applied to each unique drug-manufacturer group).
* **Evaluation:** * **R² Score:** 0.9551 (Validation set), showing a strong linear trend in aggregate market momentum.
    * **Constraint Handling:** Post-processing to ensure non-negative spending forecasts and handling division-by-zero errors.

### 4. Outlier Detection Logic
I applied a **Z-Score analysis** on the predicted 2026 spending rates.
* **Threshold:** 3.0 (Three-Sigma Rule).
* **Outcome:** Any drug exceeding this threshold is flagged as a high-priority item for pricing audits.

---

## 📈 Key Insights & Reflections
* **Model Underfitting:** While aggregate accuracy was high, individual drug groups showed a median R² of 0.67, suggesting that while linear trends exist, some drugs follow non-linear growth.
* **Future Work:** Moving from Linear Regression to **Exponential Smoothing** to better handle short (5-year) time-series data with limited historical points.
* **Ethics:** Outlier flags are intended as **triggers for investigation**, not a final judgment of guilt, to avoid unfair regulatory pressure on manufacturers.

---

## 🐍 Technologies Used
* **Python:** Pandas, NumPy, Scikit-learn
* **API:** OpenFDA API (Orphan Drug Designation)
* **Visualization:** Matplotlib, Seaborn
* **Statistics:** Z-score Analysis, Pearson Correlation
