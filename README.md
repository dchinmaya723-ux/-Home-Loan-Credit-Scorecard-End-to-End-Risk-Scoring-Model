# 🏠 Home Loan Credit Scorecard — End-to-End Risk Scoring Model

**Author:** Chinmaya Shakti Prasad Das
**Tools:** Microsoft Excel · Stata (Logistic Regression)
**Domain:** Retail Banking · Credit Risk Modelling
**Focus:** Default Prediction · WoE Binning · Scorecard Development · Model Validation

---

## 📌 Project Overview

This project builds a **production-style credit scorecard** for home loan default prediction — the kind of model used by retail banks to assign credit scores to loan applicants and make approve/reject decisions.

Starting from raw application data, the project walks through every stage of the credit risk modelling pipeline: exploratory analysis, missing value treatment, Weight of Evidence (WoE) binning, logistic regression via Stata, scorecard scaling, and out-of-sample model validation using a confusion matrix and KS statistic.

The final output is a **point-based scorecard** where each applicant's attributes (loan amount, property value, derogatory history, debt-to-income ratio, etc.) map to a score — lower scores indicate higher default risk.

---

## 📂 Project Structure

```
Home_Loan_Credit_Scorecard/
│
├── Home_Loan_Credit_Scorecard.xlsx    # Complete model workbook (all pipeline stages)
└── README.md                          # Project documentation
```

### Workbook Sheet Guide

| # | Sheet Name | Purpose |
|---|---|---|
| 1 | `Data Dictionary` | Variable definitions, full names, and descriptions |
| 2 | `Raw Data` | Full dataset (5,962 rows) with missing value % diagnostics |
| 3 | `Descriptive Statistics` | Distribution stats: mean, median, std dev, skewness, kurtosis per variable |
| 4 | `WoE Training Data` | 70% training set — WoE bin labels, WoE-encoded features, IV tables per variable |
| 5 | `Logistic Regression Output` | Stata logit output — coefficients, z-scores, p-values, model fit statistics |
| 6 | `Scorecard` | Final scorecard — PDO scaling inputs, factor/offset calculations, category point scores |
| 7 | `Scored Test Data` | 30% out-of-sample scoring — Z-values, predicted probabilities, binary classification |
| 8 | `KS & Confusion Matrix` | Model validation — KS decile table and confusion matrix with precision/recall/accuracy |

---

## 📊 Dataset Summary

| Attribute | Value |
|---|---|
| Total Observations | 5,962 |
| Train Set | 4,161 (≈ 70%) |
| Test Set | 1,800 (≈ 30%) |
| Overall Default Rate | ~21.4% (891 defaults in train) |
| Target Variable | `DEF` — 1 = Default / Seriously Delinquent; 0 = Repaid |

---

## 🗂️ Variable Dictionary

| Variable | Full Name | Description |
|---|---|---|
| `DEF` | Default Indicator | 1 = Defaulted or seriously delinquent; 0 = Repaid |
| `LOAN` | Loan Amount (₹) | Total loan request amount |
| `VALUE` | Property Value (₹) | Current value of the mortgaged property |
| `JOB` | Occupation Category | Mgr / Office / ProfExe / Sales / Self / Other |
| `YOJ` | Years at Current Job | Tenure at present employer |
| `DEROG` | Derogatory Reports | Number of major derogatory credit reports |
| `DELINQ` | Delinquent Credit Lines | Count of currently delinquent credit lines |
| `CLAGE` | Credit Line Age (Months) | Age of the applicant's oldest credit line |
| `NINQ` | Recent Credit Inquiries | Number of credit inquiries in recent period |
| `CLNO` | Number of Credit Lines | Total active credit lines |
| `DEBTINC` | Debt-to-Income Ratio (%) | Total monthly debt obligations as % of income |

---

## 🔬 Methodology

The project follows the standard **retail credit scorecard development pipeline**:

### Step 1 — Exploratory Data Analysis & Descriptive Statistics
Full distribution statistics (mean, median, mode, standard deviation, skewness, kurtosis, range) were computed for each continuous variable. Strong right-skew was identified in `LOAN`, `VALUE`, and `DEROG` — a common characteristic of credit application data. The `JOB` frequency breakdown showed `Other` dominating (2,666 records), followed by `ProfExe` (1,276) and `Office` (948).

### Step 2 — Train / Test Split
A random number column (`RANDOM`) was generated using Excel's `RAND()` function. Observations with `RANDOM < 0.7` were assigned to the training set and the remainder to the test set, producing a clean 70/30 split — recorded in the `Raw Data` sheet.

### Step 3 — WoE Binning & Information Value (IV)
Each continuous variable was binned into 10 equal-frequency deciles on the training set. For each bin, the following metrics were calculated and stored in the `WoE Training Data` sheet:

- **Default Rate (%)** — proportion of defaults within the bin
- **% Solvent / % Default** — distribution of goods and bads
- **Weight of Evidence (WoE)** — `ln(% Non-event / % Event)`
- **Information Value (IV)** — `Σ (% Non-event − % Event) × WoE`

WoE values replaced raw variable values across all training observations, transforming all predictors onto a single linear scale suitable for logistic regression.

### Step 4 — Logistic Regression (Stata)
A binary logistic regression model was estimated in Stata:

```stata
logit def loan value yoj derog delinq clage ninq clino debtinc
```

Full output is preserved verbatim in the `Logistic Regression Output` sheet.

**Regression Results Summary:**

| Variable | Coefficient | z-stat | p-value | Significant? |
|---|---|---|---|---|
| `LOAN` | −0.4345 | −5.52 | 0.000 | ✅ Yes |
| `VALUE` | −0.4744 | −2.28 | 0.022 | ✅ Yes |
| `YOJ` | −0.5440 | −1.34 | 0.179 | ❌ No |
| `DEROG` | +0.0014 | 0.00 | 0.998 | ❌ No |
| `DELINQ` | −0.3489 | −0.85 | 0.395 | ❌ No |
| `CLAGE` | −0.4476 | −1.92 | 0.055 | ⚠️ Marginal |
| `NINQ` | −0.9466 | −4.21 | 0.000 | ✅ Yes |
| `CLNO` | −0.5260 | −1.89 | 0.059 | ⚠️ Marginal |
| `DEBTINC` | −0.9085 | −9.72 | 0.000 | ✅ Yes |
| `_cons` | −1.3803 | −31.83 | 0.000 | ✅ Yes |

- **Observations:** 4,161 · **LR Chi²(9):** 201.52 · **Prob > Chi²:** 0.0000 · **Pseudo R²:** 0.0466

`DEBTINC` (debt-to-income ratio) and `NINQ` (recent inquiries) emerged as the two strongest predictors — consistent with industry credit underwriting experience.

### Step 5 — Scorecard Scaling (PDO Method)
Logistic regression coefficients were converted into integer point scores using industry-standard **Points to Double the Odds (PDO)** scaling. Parameters are live inputs in the `Scorecard` sheet:

| Parameter | Value |
|---|---|
| PDO | 20 |
| Target Score | 600 |
| Target Odds | 50 |

The scaling formulas derive a **Factor** and **Offset** to convert each WoE-encoded bin into an additive point score. An applicant's total credit score is the sum of their category points across all variables — higher total score = lower default probability.

**Example — LOAN Score Points:**

| Category | Range | WoE | Score Points |
|---|---|---|---|
| LOAN 1 | Loan < ₹9,040,000 | −0.921 | 86 pts |
| LOAN 4 | ₹9,950,000 – ₹10,240,000 | +0.277 | 71 pts |
| LOAN 6 | ₹10,800,000 – ₹11,392,000 | +0.400 | 70 pts |
| LOAN 9 | ₹15,000,000 – ₹18,768,000 | +0.475 | 69 pts |

*(Smaller loan amounts carry higher risk → fewer score points)*

---

## 📈 Model Validation

### KS Statistic — Decile Analysis (`KS & Confusion Matrix` sheet)

| Decile | Avg. Probability | Defaults | Solvents | Cum % Default | Cum % Solvent | KS |
|---|---|---|---|---|---|---|
| 1 | 0.122 | 49 | 368 | 5.5% | 11.3% | 0.058 |
| 2 | 0.140 | 52 | 364 | 11.3% | 22.4% | 0.110 |
| 3 | 0.155 | 63 | 353 | 18.4% | 33.2% | 0.148 |
| 4 | 0.169 | 70 | 346 | 26.3% | 43.8% | 0.175 |
| 5 | 0.186 | 83 | 333 | 35.6% | 53.9% | 0.184 |
| 6 | 0.208 | 78 | 338 | 44.3% | 64.3% | 0.199 |
| **7** | **0.240** | **88** | **329** | **54.2%** | **74.3%** | **0.201 ← Max KS** |
| 8 | 0.289 | 91 | 324 | 64.4% | 84.3% | 0.198 |
| 9 | 0.354 | 126 | 290 | 78.6% | 93.1% | 0.146 |
| 10 | 0.574 | 191 | 225 | 100.0% | 100.0% | 0.000 |

**Maximum KS = 0.201** at Decile 7. Industry benchmark: KS > 0.20 is acceptable for a retail credit scorecard.

### Confusion Matrix — Test Set (`KS & Confusion Matrix` sheet)
Default classification threshold set at **p = 0.2403** (Decile 7 cutoff — the point of maximum KS separation).

| | Predicted Default | Predicted Solvent |
|---|---|---|
| **Actual Default** | 373 ✅ TP | 518 ❌ FN |
| **Actual Solvent** | 684 ❌ FP | 2,586 ✅ TN |

| Metric | Value |
|---|---|
| Accuracy | 71.1% |
| Precision | 35.3% |
| Recall | 41.9% |

The threshold of 0.2403 was selected at the KS-maximising decile, balancing the capture of true defaults against false positives given the class imbalance (~21% base default rate).

---

## 💡 Key Business Insights

- **Debt-to-Income Ratio (`DEBTINC`)** is the single most powerful predictor of home loan default (β = −0.91, p < 0.001). Applicants with high DTI are significantly more likely to default — this aligns with real-world underwriting policy.
- **Recent Credit Inquiries (`NINQ`)** is the second-strongest signal (β = −0.95, p < 0.001). A high inquiry count indicates either financial distress or aggressive credit-seeking behaviour — both red flags for lending.
- **Loan Amount and Property Value** are statistically significant and negatively associated with default — larger loans tend to go to higher-quality borrowers or carry better collateral coverage.
- **Derogatory Reports (`DEROG`)** produced a near-zero coefficient with p = 0.998, contributing no additional explanatory power once other variables are controlled for — likely due to multicollinearity with `DELINQ`.
- **Years at Job (`YOJ`)** was not statistically significant at the 5% level (p = 0.179), suggesting employment tenure alone is not a strong standalone predictor in this dataset.
- The scorecard assigns the **lowest point totals (highest risk)** to applicants with small loan amounts, low property values, high DTI, and many recent inquiries — fully consistent with credit underwriting intuition.

---

## 🛠️ Tools & Techniques Used

| Tool / Technique | Application |
|---|---|
| Microsoft Excel | Data preparation, WoE binning, IV calculation, PDO scaling, scorecard table |
| Stata | Binary logistic regression estimation (`logit`) |
| WoE Transformation | Feature encoding for linear logistic regression input |
| Information Value (IV) | Variable selection and predictive power ranking |
| PDO Scaling | Converting log-odds coefficients into integer scorecard points |
| KS Statistic | Model discrimination validation (decile separation test) |
| Confusion Matrix | Classification performance at KS-optimal threshold |

---

## ▶️ How to Use the Scorecard

1. Open the `Scorecard` sheet in `Home_Loan_Credit_Scorecard.xlsx`
2. **Collect applicant data** for all 10 input variables
3. **Find the applicant's bin** for each variable in the Category column (e.g. if `LOAN = ₹8,500,000` → **LOAN 1 → 86 points**)
4. **Sum all category points** across the 9 scored variables to get the total credit score
5. **Apply the cutoff:** Applicants with a predicted default probability above **0.2403** are flagged as high-risk — reject or refer for manual review

---

*This project is built for learning and portfolio demonstration purposes using a publicly available home loan dataset.*
