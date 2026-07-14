# HR-Attrition-Risk-Decision-Support
AI-driven HR attrition risk analysis with rule-based DSS + Logistic Regression, visualized in Power BI
# Employee Attrition Risk & Decision Support System

An end-to-end HR analytics pipeline that identifies employee attrition risk using two complementary layers — a transparent rule-based Decision Support System (DSS) and a Logistic Regression predictive model — with the results translated into an interactive Power BI dashboard.


<img width="1311" height="687" alt="Dashbord screenshot" src="https://github.com/user-attachments/assets/99735fa8-7fc8-4d7c-b5ce-8244bf9086ed" />


## Why two layers?

Most HR analytics projects pick one approach: either a simple rule-based system (easy to trust, hard to improve) or a black-box ML model (accurate but unexplainable). This project builds both, side by side, and compares them:

- **v1 — Rule-Based DSS**: transparent, auditable thresholds an HR team can trust and act on immediately, no data science background required to understand *why* someone was flagged
- **v2 — Logistic Regression**: a model trained on the data itself, surfacing which factors actually drive attrition and outputting a probability rather than a fixed bucket

The two methods agree on 64.3% of employees — validating the rule-based thresholds — while disagreeing on the rest, which surfaces edge cases a fixed rule misses (e.g. an employee with very low satisfaction but overtime just under the threshold).

## Project structure

```
├── AI_HR_Analytics_DSS.ipynb          # full analysis notebook
├── employee_data_raw.csv               # raw synthetic dataset (600 employees)
├── employee_risk_dashboard_data.csv    # final dataset with risk levels + ML predictions
├── HR_Attrition_Dashboard.pbix         # Power BI dashboard
└── README.md
```

## Data

The dataset is **synthetically generated** (600 employees) with deliberately correlated features — overtime drives down satisfaction and attendance, which in turn drive attrition probability — so the downstream analysis reflects realistic workplace dynamics rather than pure noise. This is disclosed for portfolio transparency; no real company data is used.

**Features:** `employee_id`, `department`, `age`, `tenure_years`, `satisfaction_score`, `attendance_percent`, `overtime_hours`, `performance_rating`, `attrition`

## Methodology

### 1. Data cleaning & validation
Missing value checks, duplicate ID checks, and range validation on all numeric fields.

### 2. Exploratory data analysis
Attrition rate by department, and the relationship between overtime hours and satisfaction score (colored by attrition outcome).

### 3. Rule-based risk classification
| Risk Level | Condition |
|---|---|
| High Risk | satisfaction_score < 3 **and** overtime_hours > 15 |
| Medium Risk | satisfaction_score < 3 **or** attendance_percent < 90 |
| Low Risk | everything else |

Each risk level maps to a specific recommended HR action (immediate intervention, monitoring, or maintain/reward).

### 4. Predictive layer — Logistic Regression
Trained on `satisfaction_score`, `attendance_percent`, `overtime_hours`, `tenure_years`, `performance_rating`, with `class_weight='balanced'` to handle the ~23% attrition class imbalance. Features were standardized before training.

- **ROC-AUC: 0.66** — a moderate but genuine predictive signal, appropriately honest given the deliberate noise in the synthetic data generation
- Risk tiers assigned via **quantile banding** (top 20% = High, next 30% = Medium) rather than fixed probability cutoffs, since raw probabilities clustered tightly
- Feature importance (standardized coefficients) confirms `satisfaction_score` and `overtime_hours` as the two strongest predictors — validating the rule-based thresholds independently

### 5. Dashboard
Power BI dashboard with KPI cards, risk distribution, department-level risk composition, an overtime-vs-satisfaction scatter plot, a feature importance chart, and a filterable recommended-actions table.

## Key insights

- Employees combining low satisfaction and high overtime show the highest attrition risk — a burnout signature, not just a "bad fit" signal
- Sales, Operations, and Finance carry the highest attrition rates; HR has the lowest
- The two classification methods agree on the majority of employees, but the ML layer catches high-risk individuals just under the rule-based thresholds

## Limitations & next steps

- Dataset is synthetic, not a real company export
- ROC-AUC of 0.66 indicates moderate signal — this is a directional risk indicator, not a certainty, and should support (not replace) HR judgment
- No benchmark comparison against industry attrition norms is included — a real deployment would add this
- No cost-of-attrition estimate is included — a natural next step is translating risk counts into estimated financial exposure
- Future iteration: try Random Forest or XGBoost and compare against the Logistic Regression baseline; add SHAP values for per-employee explainability

## Tech stack

Python (pandas, numpy, scikit-learn, matplotlib) · Power BI · DAX

## How to run

1. Open `AI_HR_Analytics_DSS.ipynb` in Jupyter or Google Colab
2. Run all cells — this regenerates the dataset and re-runs the full pipeline
3. Open `HR_Attrition_Dashboard.pbix` in Power BI Desktop, or load `employee_risk_dashboard_data.csv` fresh via Get Data → Text/CSV
