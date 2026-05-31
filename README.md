# Telecom-Customer-Churn-Dashboard
Power BI dashboard analyzing 7K+ telecom customers to identify churn drivers. Achieved 26.54% churn insights with 1.8K churned users. Highlights high-risk segments like month-to-month contracts and 0–6 months tenure (52.94% churn), enabling data-driven retention strategies.
# Telecom_Customer_Churn_Dashboard
Dashboard using Power BI
Telecom Customer Churn Analysis Dashboard — Power BI

> An end-to-end business intelligence project that transforms raw, unclean telecom customer data into an interactive churn analysis dashboard, enabling stakeholders to monitor churn rates, identify at-risk segments, and make data-driven retention decisions.

---

## Table of Contents
1. [Project Overview](#project-overview)
2. [Problem Statement](#problem-statement)
3. [Dataset](#dataset)
4. [Tools & Technologies](#tools--technologies)
5. [Methodology](#methodology)
6. [Dashboard & Output](#dashboard--output)
7. [How to Run](#how-to-run)
8. [Results & Conclusion](#results--conclusion)
9. [Future Work](#future-work)
10. [Author & Contact](#author--contact)

---

## Project Overview

This project builds a **Telecom Customer Churn Analysis Dashboard** using Power BI, powered by a cleaned and transformed version of a telecom customer dataset. The dashboard provides an interactive single-page report that gives a complete picture of customer churn behaviour — who is leaving, why, and what can be done to retain them.

The project covers the complete data pipeline: raw unclean data → data cleaning & transformation in Power BI (Power Query) → DAX calculations → interactive visual dashboard → business insight delivery.

**What was done:**
- Identified and resolved data quality issues in the raw dataset (missing values, inconsistent casing, trailing whitespaces, duplicate contract labels)
- Engineered four new calculated columns to enrich the dataset for deeper analysis
- Built custom DAX measures for churn rate, average charges, and risk segmentation
- Developed a fully interactive single-page dashboard with slicers for contract type, internet service, gender, and tenure band
- Extracted actionable business insights on churn drivers and retention strategies

---

## Problem Statement

Telecom companies face significant revenue loss from customer churn. Without a centralised view of churn behaviour across customer segments, identifying at-risk customers and designing effective retention strategies requires manual, time-consuming data analysis.

**Business questions this dashboard answers:**
- What is the overall churn rate, and how many customers have already churned?
- Which contract types are most associated with churn?
- How does churn rate vary by customer tenure — are newer customers more likely to leave?
- What is the relationship between online services (security, backup) and churn behaviour?
- Are existing or new customers generating more monthly charges?
- Which customers are high-risk retained customers who may churn next?

**The goal** was to build a single, interactive dashboard that replaces ad-hoc spreadsheet analysis and gives the retention team a clear, visual answer to these questions at a glance.

---

## Dataset

The project uses two CSV files representing the raw (unclean) and processed (clean) versions of the same telecom customer dataset.

### File 1: `telco_churn_unclean.csv` — Raw Data
- **Rows:** 7,048 customer records
- **Issues identified:**
  - 126 missing values in `TotalCharges` column
  - Inconsistent column naming (mixed camelCase and Title Case)
  - Trailing whitespace in `PaymentMethod` values (e.g., `"Electronic check "`)
  - Inconsistent contract label casing (`"month to month"` vs `"Month-to-month"`)
  - No derived/calculated columns for deeper segmentation

- **Key columns:**

| Column | Description |
|---|---|
| `customerID` | Unique customer identifier |
| `gender` | Male / Female |
| `SeniorCitizen` | Whether the customer is a senior citizen (0/1) |
| `Partner` | Whether the customer has a partner (Yes/No) |
| `Dependents` | Whether the customer has dependents (Yes/No) |
| `tenure` | Number of months the customer has been with the company |
| `PhoneService` | Whether the customer has phone service (Yes/No) |
| `MultipleLines` | Whether the customer has multiple lines |
| `InternetService` | DSL / Fiber optic / No |
| `OnlineSecurity` | Whether the customer has online security add-on |
| `OnlineBackup` | Whether the customer has online backup add-on |
| `DeviceProtection` | Whether the customer has device protection |
| `TechSupport` | Whether the customer has tech support |
| `StreamingTV` | Whether the customer streams TV |
| `StreamingMovies` | Whether the customer streams movies |
| `Contract` | Month-to-month / One year / Two year |
| `PaperlessBilling` | Whether the customer uses paperless billing |
| `PaymentMethod` | Electronic check / Mailed check / Bank transfer / Credit card |
| `MonthlyCharges` | Current monthly charge amount |
| `TotalCharges` | Total amount charged to date |
| `Churn` | Whether the customer has churned (Yes/No) — **target variable** |

### File 2: `telco_churn_clean.csv` — Cleaned & Enriched Data
- **Rows:** 7,048 records (same base, cleaned)
- **Cleaning steps applied:**
  - Imputed 126 missing `TotalCharges` values
  - Standardised column naming to Title Case
  - Trimmed trailing whitespace from `PaymentMethod` values
  - Standardised contract label casing
- **4 new calculated columns added:**

| New Column | Description |
|---|---|
| `Annual Charges` | `MonthlyCharges × 12` — annualised revenue per customer |
| `Tenure Years` | `Tenure ÷ 12` — customer tenure expressed in years |
| `Monthly Charges` (cleaned) | Cleaned version of MonthlyCharges with consistent formatting |
| `Total Services Subscribed` | Count of active add-on services per customer (range: 0–7) |

**Total records: 7,048 customers**
**Churn breakdown: 1,871 churned (26.54%) vs. 5,177 retained (73.46%)**

---

## Tools & Technologies

| Category | Tool / Technology | Purpose |
|---|---|---|
| **BI & Visualization** | Power BI Desktop | Data modeling, dashboard design, visual creation |
| **Data Transformation** | Power Query (M Language) | Data cleaning, column standardisation, imputation |
| **DAX** | Data Analysis Expressions | KPI measures, churn rate, risk segmentation, calculated columns |
| **Data Format** | CSV (Excel-compatible) | Raw data staging and cleaned data preparation |
| **Version Control** | Git & GitHub | Project hosting and sharing |

---

## Methodology

### Step 1 — Data Inspection & Quality Assessment
- Loaded `telco_churn_unclean.csv` into Power BI via Power Query
- Profiled all columns using Column Quality, Column Distribution, and Column Profile tools
- Identified: 126 nulls in `TotalCharges`, trailing whitespace in `PaymentMethod`, inconsistent contract label casing, and mixed column naming conventions

### Step 2 — Data Cleaning (Power Query)
- **Missing values:** Imputed 126 null `TotalCharges` values using `MonthlyCharges × Tenure` as a proxy for customers with zero tenure (new customers with no charges yet)
- **Whitespace:** Applied `Text.Trim()` to `PaymentMethod` to remove leading/trailing spaces — reduced from 8 apparent payment categories to the correct 4
- **Casing standardisation:** Applied `Text.Proper()` to contract labels to unify `"month to month"` and `"Month-to-month"` into a single consistent label
- **Column renaming:** Standardised all column names to Title Case for consistency

### Step 3 — Feature Engineering
Created four new columns to enable richer segmentation in the dashboard:

```m
// Annual Charges
Annual Charges = [MonthlyCharges] * 12

// Tenure in Years
Tenure Years = [Tenure] / 12

// Total Services Subscribed (count of Yes values across 7 service columns)
Total Services Subscribed =
    (if [OnlineSecurity] = "Yes" then 1 else 0) +
    (if [OnlineBackup] = "Yes" then 1 else 0) +
    (if [DeviceProtection] = "Yes" then 1 else 0) +
    (if [TechSupport] = "Yes" then 1 else 0) +
    (if [StreamingTV] = "Yes" then 1 else 0) +
    (if [StreamingMovies] = "Yes" then 1 else 0) +
    (if [PhoneService] = "Yes" then 1 else 0)
```

### Step 4 — DAX Calculations
Created the following measures and calculated columns in Power BI:

```dax
-- Total Customers
Total Customers = COUNTROWS(telco_churn_clean)

-- Churned Customers
Churned Customers = CALCULATE(COUNTROWS(telco_churn_clean),
    telco_churn_clean[Churn] = "Yes")

-- Churn Rate %
Churn Rate% = DIVIDE([Churned Customers], [Total Customers], 0)

-- Average Monthly Charges (Churned Only)
Avg Monthly Charges (Churned) =
    CALCULATE(AVERAGE(telco_churn_clean[MonthlyCharges]),
    telco_churn_clean[Churn] = "Yes")

-- Customer Type (New vs Existing)
Customer Type =
    IF(telco_churn_clean[Tenure] <= 12, "New Customer", "Existing Customer")

-- Tenure Band
Tenure Band =
    SWITCH(TRUE(),
        telco_churn_clean[Tenure] <= 6,  "0-6 Months",
        telco_churn_clean[Tenure] <= 12, "6-12 Months",
        "12+ Months")

-- Risk Segmentation (for retained customers)
Risk Type =
    IF(telco_churn_clean[Churn] = "No" &&
       telco_churn_clean[Contract] = "Month-to-month" &&
       telco_churn_clean[Tenure] <= 12,
       "High Risk", "Normal")
```

### Step 5 — Exploratory Data Analysis (EDA) & Insights
Key patterns explored:
- **Churn by contract type:** Month-to-month customers churn at dramatically higher rates than annual/two-year contract holders
- **Churn by tenure:** Early-tenure customers (0–6 months) churn at 52.94% — the highest risk period
- **Online services impact:** Customers without online security and online backup generate higher monthly charges among churners
- **Customer type revenue split:** Existing customers account for the vast majority of monthly charge volume ($375K vs $81K for new customers)
- **Risk profiling:** 633 retained customers classified as High Risk (month-to-month + short tenure) — a prime retention intervention target

### Step 6 — Dashboard Development
- Built one report page in Power BI with 9 visuals covering KPIs, churn breakdown, revenue analysis, and risk segmentation
- Added interactive slicers for Contract Type, Internet Service, Gender, and Tenure Band
- Exported final dashboard as PDF for stakeholder sharing

---

## Dashboard & Output

### Customer Churn Analysis Dashboard

**Purpose:** Provides a complete, at-a-glance view of customer churn behaviour, revenue impact, and retention risk across all customer segments.

**Visuals used:**

| Visual | Type | Key Insight Shown |
|---|---|---|
| Total Customers | KPI Card | 7,043 total customers on the platform |
| Churn Rate % | KPI Card | 26.54% overall churn rate |
| Churned Customers | KPI Card | 1,869 customers have churned |
| Customer Retention Overview | Doughnut Chart | 73.46% retained vs. 26.54% churned |
| Churned Customers by Contract | Horizontal Bar Chart | Month-to-month contracts account for 1,655 of 1,869 churns (88.6%) |
| Churn Rate% by Tenure Band | Clustered Bar Chart | 0–6 month customers churn at 52.94%; 12+ month at only 17.13% |
| Monthly Charges by Online Services | Grouped Bar Chart | Customers without security/backup show distinct charge patterns |
| Monthly Charges by Customer Type | Bar Chart | Existing customers: $375K vs New customers: $81K in monthly charges |
| Avg Monthly Charges (Churned vs Retained) | Clustered Bar | Churned customers average $74/month vs $61 for retained |
| Retained Customers by Risk Type | Matrix/Table | 4,541 Normal risk vs. 633 High Risk retained customers |

**Slicers:** Contract Type, Internet Service, Gender, Tenure Band

---

## How to Run

### Prerequisites
- Power BI Desktop installed (free from Microsoft — [download here](https://powerbi.microsoft.com/desktop/))
- Git (to clone the repo)

### Steps

**1. Clone the repository**
```bash
git clone https://github.com/shafaqueara8-arch/Telecom_Customer_Churn_Dashboard.git
cd Telecom_Customer_Churn_Dashboard
```

**2. Open the Power BI file**
- Open `telecom_customer_churn.pbix` in Power BI Desktop

**3. Verify data source path**
- Go to **Home → Transform Data → Data Source Settings**
- Update the file path to point to `telco_churn_clean.csv` in your local `data/` folder if prompted
- Click **Close & Apply**

**4. Explore the dashboard**
- The report opens on the Customer Churn Analysis Dashboard page
- Use the slicers (Contract, Internet Service, Gender, Tenure Band) to filter and drill into specific segments
- Hover over any visual for detailed tooltips

**5. (Optional) Review the data cleaning steps**
- Go to **Home → Transform Data** to open Power Query Editor
- Review the applied steps on `telco_churn_clean` to see all cleaning and transformation logic
- Compare against `telco_churn_unclean.csv` to see the before/after

---

## Results & Conclusion

| Metric | Value |
|---|---|
| Total Customers | **7,043** |
| Overall Churn Rate | **26.54%** |
| Total Churned Customers | **1,869** |
| Churn Rate — Month-to-Month Contracts | **~42%** (vs 11% One Year, 3% Two Year) |
| Churn Rate — 0–6 Month Tenure Band | **52.94%** |
| Churn Rate — 12+ Month Tenure Band | **17.13%** |
| Top Churn Driver by Contract | **Month-to-Month (88.6% of all churns)** |
| Avg Monthly Charges — Churned Customers | **$74** |
| Avg Monthly Charges — Retained Customers | **$61** |
| Existing Customer Monthly Charge Volume | **$375K (82% of total)** |
| High Risk Retained Customers | **633** |
| Normal Risk Retained Customers | **4,541** |

**Conclusion:** The dashboard successfully identifies month-to-month contract type and early tenure (0–6 months) as the two strongest predictors of customer churn. Churned customers also pay higher average monthly charges ($74 vs $61), suggesting that perceived value-for-money may be a key churn driver. The 633 high-risk retained customers represent an immediate, actionable target for retention campaigns — converting even a fraction of these to annual contracts could meaningfully reduce the churn rate. These insights provide a clear, data-backed foundation for the retention team's strategy.

---

## Future Work

- **Predictive churn model:** Integrate a Python-based logistic regression or random forest churn prediction model and surface churn probability scores per customer in the dashboard
- **Customer lifetime value (CLV):** Build a CLV measure using tenure and monthly charges to rank customers by long-term value, enabling prioritised retention outreach
- **Cohort analysis:** Add a cohort-based churn view to track how churn rates evolve for customers who joined in different months/quarters
- **Automated refresh:** Set up a scheduled data refresh pipeline to automatically update the dashboard as new customer data becomes available
- **Retention ROI calculator:** Add an interactive slicer showing the estimated revenue impact of reducing churn by 1%, 5%, or 10% — making the business case for retention investment tangible for stakeholders
- **Geographic analysis:** If location data is available, add a filled map visual showing churn concentration by region or state

---

## Author & Contact

**Shafaque Ara**

| Platform | Link |
|---|---|
| GitHub | [https://github.com/shafaqueara8-arch/Telecom_Customer_Churn_Dashboard](https://github.com/shafaqueara8-arch/Telecom_Customer_Churn_Dashboard) |
| LinkedIn | [www.linkedin.com/in/shafaqueara](https://www.linkedin.com/in/shafaqueara) |
| Email | shafaqueara8@gmail.com |

---

