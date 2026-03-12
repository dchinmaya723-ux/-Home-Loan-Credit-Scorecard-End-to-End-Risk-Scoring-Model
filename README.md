# 🏠 Home Loan Credit Scorecard — End-to-End Risk Scoring Model

**Author:** Chinmaya Shakti Prasad Das
**Tools:** Microsoft Excel · Stata (Logistic Regression)
**Domain:** Retail Banking · Credit Risk Modelling
**Focus:** Default Prediction · WoE Binning · Scorecard Development · Model Validation · Interactive Dashboard

---

## 📌 Project Overview

This project builds a **production-style credit scorecard** for home loan default prediction — the kind of model used by retail banks to assign credit scores to loan applicants and make approve/reject decisions.

Starting from raw application data, the project walks through every stage of the credit risk modelling pipeline: exploratory analysis, missing value treatment, Weight of Evidence (WoE) binning, logistic regression via Stata, scorecard scaling, and out-of-sample model validation using a confusion matrix and KS statistic.

The final output is a **point-based scorecard** where each applicant's attributes (loan amount, property value, derogatory history, debt-to-income ratio, etc.) map to a score — lower scores indicate higher default risk. A fully **interactive Excel dashboard** presents all model results in a single-sheet visual interface, including a live scorecard calculator powered by dropdown menus and VLOOKUP formulas.

---

## 📂 Project Structure

```
Home-Loan-Credit-Scorecard-Chinmaya-Shakti-Prasad-Das/
│
├── Home_Loan_Credit_Scorecard.xlsx    # Complete model workbook (9 sheets)
└── README.md                          # Project documentation
```

### Workbook Sheet Guide

| # | Sheet Name | Purpose |
|---|---|---|
| 1 | `📊 DASHBOARD` | **Interactive analytics dashboard** — KPI cards, charts, KS curve, confusion matrix, live scorecard calculator, descriptive stats |
| 2 | `Data Dictionary` | Variable definitions, full names, and descriptions |
| 3 | `Raw Data` | Full dataset (5,962 rows) with missing value % diagnostics and train/test split flag |
| 4 | `Descriptive Statistics` | Distribution stats: mean, median, std dev, skewness, kurtosis per variable |
| 5 | `WoE Training Data` | 70% training set — WoE bin labels, WoE-encoded features, IV tables per variable |
| 6 | `Logistic Regression Output` | Stata logit output — coefficients, z-scores, p-values, model fit statistics |
| 7 | `Scorecard` | Final scorecard — PDO scaling inputs, factor/offset calculations, category point scores |
| 8 | `Scored Test Data` | 30% out-of-sample scoring — Z-values, predicted probabilities, binary classification |
| 9 | `KS & Confusion Matrix` | Model validation — KS decile table and confusion matrix with precision/recall/accuracy |

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

## 🖥️ Interactive Excel Dashboard (`📊 DASHBOARD` sheet)

The first sheet of the workbook is a fully self-contained **interactive analytics dashboard** built entirely in Excel using charts, conditional formatting, VLOOKUP formulas, and data validation dropdowns. No macros or VBA are used — the dashboard is compatible with Excel 2016 and later.

### Dashboard Sections

#### Section A — Applicant Profile & Predictor Analysis
- **KPI Cards (top row):** Total Applicants · Training Set · Test Set · Default Rate
- **KPI Cards (second row):** KS Statistic · Model Accuracy · Precision · Recall
- **Chart 1 — Applicants by Job Category:** horizontal bar chart showing applicant volume by occupation type
- **Chart 2 — Default Rate by Job Category:** default rate (%) per job segment, highlighting which occupations carry higher risk
- **Chart 3 — Information Value Ranking:** IV bar chart ranking all 9 predictors by total IV; dark navy = Strong (IV > 0.1), mid-blue = Medium (0.02–0.1), grey = Weak (< 0.02)

#### Section B — Model Performance: KS Curve & Regression Coefficients
- **Chart 4 — KS Separation Curve:** line chart plotting cumulative % of defaults vs cumulative % of solvents across 10 deciles — the visual gap between the two lines at each decile is the KS statistic; the curves cross the maximum separation at Decile 7 (KS = 0.201)
- **Chart 5 — Regression Coefficients (β):** horizontal bar chart showing the magnitude and direction of each variable's logistic regression coefficient — all negative coefficients mean higher values lower the log-odds of default

#### Section C — Confusion Matrix & KS Decile Table
- **Colour-coded confusion matrix:** True Positives and True Negatives shown in green, False Positives and False Negatives in red — with model metrics (accuracy, precision, recall) displayed below
- **Full KS decile table:** all 10 deciles with average probability, default count, solvent count, cumulative percentages, and KS statistic; Decile 7 (max KS) highlighted in amber

#### Section D — Interactive Scorecard Calculator ⭐
The most interactive part of the dashboard. Select one bin per variable using the **yellow dropdown cells** — the credit score, risk verdict, and estimated default probability all recalculate instantly via VLOOKUP formulas.

| Variable | Dropdown Options | Scorecard Points Range |
|---|---|---|
| `LOAN` | LOAN 1 through LOAN 10 (10 bins by loan amount) | 68.6 – 86.1 pts |
| `DEBTINC` | D1 through D10 (by DTI bracket) | 71.1 – 82.8 pts |
| `NINQ` | N1 through N4 (by inquiry count) | 74.4 – 74.7 pts |
| `CLAGE` | C1 through C10 (by credit age in months) | 74.5 – 74.7 pts |
| `VALUE` | V1 through V10 (by property value) | 74.4 – 74.7 pts |
| `DEROG` | D1 (no derogatory) / D2 (has derogatory) | 74.5 – 74.6 pts |

**Score output panel:**
- **Credit Score** — sum of all 6 selected bin scores (updates automatically)
- **Risk Verdict** — APPROVE / MANUAL REVIEW / HIGH RISK / DECLINE (formula-driven)
- **Estimated Default Probability** — linear approximation mapping the score to an indicative probability of default
- **Score Interpretation Guide** — band thresholds for all four risk categories

#### Section E — Variable Descriptive Statistics Summary
A formatted table covering all 9 predictors with mean, median, standard deviation, skewness, min, max, and valid observation count.

### How to Use the Dashboard

1. Open `Home_Loan_Credit_Scorecard.xlsx` in **Excel 2016 or later**
2. Navigate to the **📊 DASHBOARD** tab (first sheet)
3. To use the **Scorecard Calculator** (Section D):
   - Click the **yellow dropdown cell** under each variable label
   - Select the bin that matches the applicant's attribute (e.g. if DEBTINC = 38%, select `D7 — 38-40%`)
   - Repeat for all 6 variables
   - Read the **Credit Score**, **Risk Verdict**, and **Estimated Default Probability** from the dark panel on the right
4. All charts and tables are static (pre-populated from model results) and require no interaction — they update only if the underlying data sheets are modified

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

**IV Summary — Predictor Ranking:**

| Variable | Total IV | Predictive Strength |
|---|---|---|
| `LOAN` | 0.1784 | 🔵 Strong |
| `DEBTINC` | 0.1740 | 🔵 Strong |
| `NINQ` | 0.0311 | 🟡 Medium |
| `VALUE` | 0.0280 | 🟡 Medium |
| `CLAGE` | 0.0253 | 🟡 Medium |
| `CLNO` | 0.0186 | 🟡 Medium |
| `YOJ` | 0.0070 | 🔴 Weak |
| `DELINQ` | 0.0025 | 🔴 Weak |

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

### Confusion Matrix — Train Set (`KS & Confusion Matrix` sheet)
Default classification threshold set at **p = 0.2403** (Decile 7 cutoff — the point of maximum KS separation).

| | Actual Default | Actual Solvent |
|---|---|---|
| **Predicted Default** | 408 ✅ TP | 841 ❌ FP |
| **Predicted Solvent** | 483 ❌ FN | 2,429 ✅ TN |

| Metric | Value | Formula |
|---|---|---|
| Accuracy | 68.2% | (TP + TN) / Total |
| Precision | 32.7% | TP / (TP + FP) |
| Recall | 45.8% | TP / (TP + FN) |

The threshold of 0.2403 was selected at the KS-maximising decile, balancing the capture of true defaults against false positives given the class imbalance (~21% base default rate).

---

## 💡 Key Business Insights

- **Debt-to-Income Ratio (`DEBTINC`)** is the single most powerful predictor of home loan default (β = −0.91, p < 0.001, IV = 0.174). Applicants with high DTI are significantly more likely to default — this aligns with real-world underwriting policy.
- **Recent Credit Inquiries (`NINQ`)** is the second-strongest signal (β = −0.95, p < 0.001). A high inquiry count indicates either financial distress or aggressive credit-seeking behaviour — both red flags for lending.
- **Loan Amount and Property Value** are statistically significant and negatively associated with default — larger loans tend to go to higher-quality borrowers or carry better collateral coverage.
- **Derogatory Reports (`DEROG`)** produced a near-zero coefficient with p = 0.998, contributing no additional explanatory power once other variables are controlled for — likely due to multicollinearity with `DELINQ`.
- **Years at Job (`YOJ`)** was not statistically significant at the 5% level (p = 0.179), suggesting employment tenure alone is not a strong standalone predictor in this dataset.
- The scorecard assigns the **lowest point totals (highest risk)** to applicants with small loan amounts, low property values, high DTI, and many recent inquiries — fully consistent with credit underwriting intuition.
- The **interactive dashboard** allows any user to score a hypothetical applicant in under 60 seconds by selecting bins from dropdown menus — no Stata or formula knowledge required.

---

## 🛠️ Tools & Techniques Used

| Tool / Technique | Application |
|---|---|
| Microsoft Excel | Data preparation, WoE binning, IV calculation, PDO scaling, scorecard table, interactive dashboard |
| Excel Charts | KS curve, job distribution, IV ranking, coefficient magnitude — embedded in dashboard sheet |
| Excel Data Validation | Dropdown menus in the scorecard calculator (Section D of dashboard) |
| Excel VLOOKUP + IF | Live scorecard calculator — maps dropdown selections to score points and risk verdicts |
| Excel Conditional Formatting | KS table colour scale, confusion matrix cell colours |
| Stata | Binary logistic regression estimation (`logit`) |
| WoE Transformation | Feature encoding for linear logistic regression input |
| Information Value (IV) | Variable selection and predictive power ranking |
| PDO Scaling | Converting log-odds coefficients into integer scorecard points |
| KS Statistic | Model discrimination validation (decile separation test) |
| Confusion Matrix | Classification performance at KS-optimal threshold |

---

## ▶️ How to Use the Workbook

### Option A — Use the Interactive Dashboard
1. Open `Home_Loan_Credit_Scorecard.xlsx` in Excel 2016 or later
2. Go to the **📊 DASHBOARD** sheet (first tab)
3. Scroll to **Section D — Scorecard Calculator**
4. Click the yellow dropdown under each variable and select the bin matching your applicant
5. The Credit Score, Risk Verdict, and Estimated Default Probability update automatically

### Option B — Use the Full Scorecard Sheet Manually
1. Open the `Scorecard` sheet
2. **Collect applicant data** for all 10 input variables
3. **Find the applicant's bin** for each variable in the Category column (e.g. if `LOAN = ₹8,500,000` → **LOAN 1 → 86 points**)
4. **Sum all category points** across the 9 scored variables to get the total credit score
5. **Apply the cutoff:** Applicants with a predicted default probability above **0.2403** are flagged as high-risk — reject or refer for manual review

### Score Interpretation Guide

| Total Score | Risk Band | Recommended Action |
|---|---|---|
| > 470 | 🟢 Low Risk | Approve — Standard Terms |
| 440 – 470 | 🟡 Medium Risk | Approve — Enhanced Monitoring |
| 400 – 440 | 🟠 High Risk | Refer for Manual Review |
| < 400 | 🔴 Very High Risk | Decline Application |

---

## 🔗 Resume Bullet Point

> Built an end-to-end home loan credit scorecard using logistic regression (Stata) and WoE binning in Excel across 5,962 applicants — achieving KS = 0.20 and 68.2% training accuracy; deployed a PDO-scaled point system and an interactive Excel dashboard with a live applicant scoring calculator for credit underwriting decisions.

---

*This project is built for learning and portfolio demonstration purposes using a publicly available home loan dataset.*
