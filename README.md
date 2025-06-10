**This repository presents a unified workflow—from raw XPT conversion through exploratory statistics to predictive modeling—showing how pairing objective accelerometer measures with subjective surveys boosts health‐status classification performance.**

**🔄 Data Conversion & Documentation**

Source & Format: Started with NHANES XPT files and accompanying data dictionaries citeturn0file0.

XPT → CSV Pipeline: Developed a conversion script to batch‐translate each XPT dataset into CSV, preserving variable labels and facilitating downstream analysis.

Dataset Selection: Chose three core sources for modeling:

day_data.csv: Daily aggregates of wear metrics (sleep, wake, MIMS sum)

subjective_data.csv: Questionnaire responses on work, recreation, walking, sedentary behavior

demo_data.csv: Demographics & medical‐consult indicators, including age, gender, income, education, and medical advice flags

**🔍 Exploratory Analysis & Aggregation**

Participant‐Level Summaries: Grouped day_data by participant (SEQN), computing per‐person mean/σ for key metrics across ~6 917 individuals:

| Metric  | Header 2 | Header 3 |
| -------- | -------- | -------- |
| Row 1A   | Row 1B   | Row 1C   |
| Row 2A   | Row 2B   | Row 2C   |


**Wear‐Time Patterns:**

First/Last Day: Partial data on days 1 & 9 (≈ 500 min), full wear (≈ 1 436 min) on days 2–8.

Weekday vs. Weekend: Nearly identical wear and QC flags; wake/sleep ratio ~2.05 both on weekdays and weekends.

Wake/Sleep by Day: Consistent 2:1 wake/sleep ratio across days 2–8.

**Quantile Stratification:**

Binned PAXMTSD into Low/Medium/High tertiles and compared wake/sleep ratios:

Quantile

Mean Wake/Sleep

σ

Low

1.63

0.34

Medium

2.05

0.41

High

2.73

0.57

**Subjective vs. Objective:**

Weak correlations (|r|<0.11) between survey items and MIMS sum, highlighting reporting bias and the need for sensor data.

**Demographic Correlations:**

Age vs. MIMS: r=–0.21 (older → less movement)

Income vs. MIMS: r≈0.02 (no link)

Household size vs. MIMS: r=0.18 (larger households → more movement)

Education/income: negligible effects

**🧪 Statistical Testing & Target Definition**

**ANOVA & Tukey HSD on five‐level health status (HSD010) vs. continuous metrics:**

Significant separation between categories 1–3 (homogeneous cluster) and 4–5 (lower‐activity cluster).

Example: Δ MIMS sum between Excellent (1) and Poor (5) = –2 002 units (p‑adj<0.0001).

Binary Target Creation: Collapsed HSD010 into 0 = best health (1–3), 1 = worst health (4–5) based on statistical homogeneity and improved power.

**📓 Notebooks & Modeling**

**1. Data Preprocessing**

initial_work.ipynb: ETL of CSVs, variable pruning (selecting pa_level_activity, sleep_wear, wake_wear, selected demo/medical flags), dtype alignment, missing‐value handling.

**2. Statsmodels Logistic Regression**

Framework: statsmodels.Logit pipelines for subjective, objective, and combined data, with full inference output (coefficients, z‑scores, p‑values, Pseudo R²).

**Key Findings:**

Combined model: Pseudo R² ≈ 0.175; objective‑only: 0.119; subjective‑only: 0.170.

Subjective features alone nearly match combined fit, underscoring survey power.

**3. scikit‑learn Logistic Regression**

Mirror statsmodels setups using LogisticRegression + StandardScaler, single‐run and oversampled (×10) evaluations, confirming consistent coefficient direction and metrics.

**4. Random Forest Classification**

sklearn.ensemble.RandomForestClassifier on three feature sets.

Imbalance Strategies: class weights, random oversampling, and threshold tuning (optimized for F1 on class 1).

Outcome: Combined feature RF (tuned) achieved best recall (~0.21) and F1 (~0.28) for “worst health,” demonstrating the synergy of subjective + objective inputs.

**🚀 Key Insights**

Objective data alone excels at characterizing the majority (high precision) but misses most at‐risk cases (low recall).

Subjective surveys boost recall for the at‐risk class (↑ 18% vs. 12%) at minor precision cost.

Combined models deliver the best balance: highest AUC, recall, and F1 for critical binary classification.

Threshold tuning and class weighting are essential to improve minority‐class detection in health‐risk contexts.


