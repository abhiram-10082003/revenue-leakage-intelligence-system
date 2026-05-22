# Revenue Leakage Intelligence System

> A multi-source retail analytics project that identifies, quantifies, and prioritises revenue leakage across four distinct leakage types — using three real-world datasets, Python-based EDA, SQLite for data persistence, and a three-page Power BI dashboard for business reporting.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Key Findings](#key-findings)
- [Dashboard Preview](#dashboard-preview)
- [Project Structure](#project-structure)
- [Datasets Used](#datasets-used)
- [Methodology](#methodology)
  - [01 — Discount Leakage (Superstore)](#01--discount-leakage-superstore)
  - [02 — Billing Leakage (Olist)](#02--billing-leakage-olist)
  - [03 — Returns Leakage (UCI Retail)](#03--returns-leakage-uci-retail)
  - [04 — Master Leakage Analysis](#04--master-leakage-analysis)
- [Tech Stack](#tech-stack)
- [How to Run](#how-to-run)
- [Limitations & Assumptions](#limitations--assumptions)

---

## Project Overview

Revenue leakage is the silent profit killer in retail — money that a business *should* have collected but didn't, due to discount abuse, pricing inconsistencies, overbilling gaps, or unchecked return behaviour. This project simulates a real-world **Revenue Leakage Intelligence** pipeline by:

- Ingesting three publicly available retail datasets
- Engineering leakage-specific features for each dataset
- Storing all processed data in a centralised SQLite database
- Consolidating findings into a master KPI summary with severity tiers and recovery timelines
- Visualising results in an interactive Power BI dashboard

**Total Revenue Leakage Identified: $1,613,322.68**

> **Note on currency & comparability:** Each dataset originates from a different market (US retail, Brazilian e-commerce, UK wholesale). The consolidated $1.61M figure is intended as a conceptual aggregation to demonstrate the analytical framework — it is not a single-currency, single-business P&L figure.

---

## Key Findings

| Leakage Type | Amount | Contribution | Severity | Recovery Timeline |
|---|---|---|---|---|
| Returns Leakage | $1,034,085.87 | 64.1% | Critical | Structural (6–12 months) |
| Pricing Leakage | $440,380.54 | 27.3% | Medium | Process Fix (3–6 months) |
| Discount Leakage | $138,515.24 | 8.59% | Low | Recoverable (0–3 months) |
| Billing Leakage | $341.03 | 0.02% | Low | Recoverable (0–3 months) |
| **Total** | **$1,613,322.68** | **100%** | | |

**Top operational findings:**

- Returns leakage alone accounts for 64% of total leakage — a structural problem requiring long-term policy changes, not just quick fixes
- The top pricing leakage product is the GBC DocuBind P400 Electric Binding System ($15,651) — entire top-10 list falls in Office Supplies and Technology
- Binders ($39K), Tables ($31K), and Machines ($30K) are the biggest discount abuse sub-categories
- High-risk return customers (return rate >15%) represent 7.21% of the customer base but drive the bulk of returns leakage
- Customer ID 14380 shows a 303% return rate (returned $148.69 against only $48.96 in purchases) — a probable fraud or data anomaly case

---

## Dashboard Preview

### Executive Overview
![Executive Overview](Images/Executive_Overview.png)

*KPI summary cards, leakage contribution pie chart, severity prioritisation bar chart, and the Leakage Recovery Action Matrix.*

---

### Operational Leakage Analysis
![Operational Leakage Analysis](Images/Operational_Leakage_Analysis.png)

*Top products driving pricing leakage, discount-to-profit margin scatter plot, and top discount leakage drivers by product sub-category.*

---

### Return & Billing Risk
![Return & Billing Risk](Images/Return_&_Billing_Risk.png)

*Customer return risk segmentation, high-risk customers with abnormal return behaviour, top payment reconciliation gap orders, and billing leakage distribution.*

---

## Project Structure

```
Revenue_Leakage_Intelligence_System/
│
├── Data/
│   ├── Raw_Data/                         # Source datasets (not uploaded to repo)
│   │   ├── Sample - Superstore.csv
│   │   ├── olist_orders_dataset.csv
│   │   ├── olist_order_items_dataset.csv
│   │   ├── olist_order_payments_dataset.csv
│   │   └── online_retail_II.csv
│   │
│   └── Processed_Data/                   # Cleaned & feature-engineered CSVs
│       ├── superstore_analysis.csv
│       ├── olist_billing_analysis.csv
│       ├── uci_customer_return_analysis.csv
│       └── master_kpi_summary.csv
│
├── Notebooks/
│   ├── 01_Superstore_Analysis.ipynb      # Discount & Pricing leakage detection
│   ├── 02_OLIST_Analysis.ipynb           # Billing leakage detection
│   ├── 03_UCI_Analysis.ipynb             # Returns leakage & customer risk scoring
│   └── 04_Master_Leakage_Analysis.ipynb  # Consolidation, KPI summary, CSV export
│
├── PowerBI/
│   └── Revenue_Leakage_Dashboard.pbix    # 3-page interactive dashboard
│
├── Database/
│   └── revenue_leakage.db                # SQLite database — all analytical tables
│
├── Images/
│   ├── Executive_Overview.png
│   ├── Operational_Leakage_Analysis.png
│   └── Return_&_Billing_Risk.png
│
└── README.md
```

---

## Datasets Used

### 1. Sample Superstore (Kaggle)
- **Source:** [Kaggle — Sample Superstore](https://www.kaggle.com/datasets/vivek468/superstore-dataset-final)
- **Scope:** US retail transactions across Furniture, Office Supplies, and Technology categories
- **Used for:** Discount Abuse Leakage, Pricing Leakage
- **Key columns:** Order Date, Sales, Quantity, Discount, Profit, Product ID, Sub-Category, Region

### 2. Olist Brazilian E-Commerce (Kaggle)
- **Source:** [Kaggle — Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)
- **Scope:** ~100K orders from the Brazilian e-commerce platform Olist (2016–2018)
- **Used for:** Billing Leakage (payment reconciliation gap)
- **Tables merged:** olist_orders · olist_order_items · olist_order_payments
- **Key columns:** order_id, price, freight_value, payment_value, order_status

### 3. UCI Online Retail II (UCI ML Repository)
- **Source:** [UCI ML Repository — Online Retail II](https://archive.ics.uci.edu/dataset/502/online+retail+ii)
- **Scope:** UK-based online retail transactions (2009–2011), including returns (negative quantities)
- **Used for:** Returns Leakage, Customer Return Risk Segmentation
- **Key columns:** Invoice, StockCode, Quantity, Price, Customer ID

---

## Methodology

### 01 — Discount Leakage (Superstore)

**Feature engineering:**

| Feature | Logic |
|---|---|
| `Discount %` | `Discount × 100` |
| `Profit Margin %` | `(Profit / Sales) × 100` |
| `Loss Flag` | `1` if Profit < 0 |
| `High Discount Flag` | `1` if Discount % > 20 |
| `Discount Abuse Flag` | `1` if High Discount Flag = 1 AND Loss Flag = 1 |
| `Discount Abuse Amount` | `abs(Profit)` for flagged rows, else `0` |

**Rationale:** A discount is classified as abusive only when it simultaneously exceeds 20% *and* results in a loss. High discounts that still generate profit are not flagged — they may represent intentional strategic pricing.

---

**Pricing leakage logic:**

A per-product unit price benchmark is established using the **90th percentile** of observed unit prices for each `Product ID`.

```
Unit Price           = Sales / Quantity
Standard Unit Price  = 90th percentile of Unit Price per Product ID
Pricing Leakage      = max(0, (Standard Unit Price − Unit Price) × Quantity)
```

**Why 90th percentile instead of maximum?** Using the maximum observed price as a benchmark introduces distortion from one-off outlier transactions (e.g., a single premium order). The 90th percentile provides a stable, realistic premium pricing reference while remaining resistant to extreme values.

---

### 02 — Billing Leakage (Olist)

Three Olist tables are merged at the `order_id` level:

```
items_agg    = GROUP BY order_id → sum(price), sum(freight_value), count(items)
payments_agg = GROUP BY order_id → sum(payment_value), max(installments)
master       = orders LEFT JOIN items_agg LEFT JOIN payments_agg
```

**Leakage calculation:**

```
Expected Payment  = total_order_value + total_freight_value
Payment Gap       = Expected Payment − total_payment_value
Billing Leakage   = Payment Gap  (if gap > $1, else 0)
```

The `> $1` threshold removes floating-point noise from payment processor rounding differences, ensuring only genuine payment shortfalls are counted.

---

### 03 — Returns Leakage (UCI Retail)

**Returns identification:** In UCI, negative `Quantity` values represent product returns (confirmed by `Invoice` strings prefixed with "C" = Cancellation).

```
Return Amount = abs(Quantity) × Price    (for all rows where Quantity < 0)
```

**Customer-level return risk segmentation:**

Each customer's total sales and total return amounts are computed, then merged to calculate a `Return Rate %`:

```
Return Rate % = (Total Return Amount / Total Sales Amount) × 100
```

| Return Rate % | Risk Category |
|---|---|
| ≤ 5% | Low Risk |
| 5% – 15% | Medium Risk |
| > 15% | High Risk |

---

### 04 — Master Leakage Analysis

All three processed tables are loaded from SQLite. Leakage amounts are summed, contribution percentages are computed, and each leakage type is classified by severity and recovery timeline:

| Contribution % | Severity | Recovery Category |
|---|---|---|
| < 10% | Low | Recoverable (0–3 Months) |
| 10% – 30% | Medium | Process Fix (3–6 Months) |
| ≥ 30% | Critical | Structural (6–12 Months) |

The master KPI summary is stored back to SQLite (`master_kpi_summary` table) and exported as a CSV.

---

## Tech Stack

| Layer | Tools |
|---|---|
| Data processing | Python 3, pandas, numpy |
| Storage | SQLite (via `sqlite3`) |
| Visualisation (EDA) | matplotlib, seaborn |
| Business dashboard | Microsoft Power BI Desktop |
| Environment | Jupyter Notebook |

---

## How to Run

**Prerequisites:**
```
pip install pandas numpy matplotlib seaborn jupyter
```

**Step-by-step:**

1. Clone this repository
2. Place all raw CSV files in `Data/Raw_Data/` (see [Datasets Used](#datasets-used) for download links)
3. Run notebooks in order:
   ```
   01_Superstore_Analysis.ipynb   → generates superstore_analysis table in SQLite
   02_OLIST_Analysis.ipynb        → generates olist_billing_analysis table in SQLite
   03_UCI_Analysis.ipynb          → generates uci_customer_return_analysis table in SQLite
   04_Master_Leakage_Analysis.ipynb → consolidates all tables, exports CSVs
   ```
4. Open `PowerBI/Revenue_Leakage_Dashboard.pbix` in Power BI Desktop and refresh the data source to point to your local `Processed_Data/` folder

> Each notebook is self-contained for its dataset but depends on the SQLite database path `../Database/revenue_leakage.db`. Ensure the `Database/` folder exists before running notebook 01.

---

## Limitations & Assumptions

1. **Multi-currency aggregation** — Superstore values are in USD, Olist in BRL, UCI in GBP. The $1.61M consolidated total treats all values as equivalent for demonstration purposes. A production system would apply exchange rate normalisation.

2. **Pricing benchmark is relative, not absolute** — The 90th percentile pricing benchmark is calculated within each dataset's own price history. It does not reflect external market prices or competitor benchmarks.

3. **Discount threshold is heuristic** — The 20% high-discount flag is based on domain convention. An ideal threshold would be derived statistically from the discount-profit inflection point visible in the dataset.

4. **Returns anomalies (>100% return rate)** — Customers with return rates exceeding 100% (e.g., Customer ID 14380 at 303%) may represent data entry errors, fraud cases, or wholesale return aggregation effects. These are included in the current analysis but should be isolated for separate investigation in a production context.

5. **No time-series dimension** — All leakage is aggregated across the full historical period of each dataset. Trend analysis (monthly/quarterly leakage movement) is not included in the current version.

6. **Olist billing leakage is structurally small** — At $341, the billing leakage from Olist may partly reflect legitimate adjustments (vouchers, cancellation refunds, instalment rounding) that are indistinguishable from genuine gaps without access to Olist's internal payment adjustment logs.

---

*Built as part of a retail analytics portfolio project. All datasets are publicly available and used for educational and analytical demonstration purposes.*