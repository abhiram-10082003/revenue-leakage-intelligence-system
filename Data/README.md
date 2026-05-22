# Data Folder Information

## Overview

The original datasets used in this project are not included in this repository because of:

* File size limitations
* Dataset licensing considerations
* GitHub repository storage best practices

This folder is structured to separate:

* Raw source datasets
* Processed analytical datasets

---

# Folder Structure

```text
Data/
│
├── Raw_Data/
│   ├── Sample - Superstore.csv
│   ├── olist_orders_dataset.csv
│   ├── olist_order_items_dataset.csv
│   ├── olist_order_payments_dataset.csv
│   └── online_retail_II.csv
│
└── Processed_Data/
    ├── superstore_analysis.csv
    ├── olist_billing_analysis.csv
    ├── uci_customer_return_analysis.csv
    └── master_kpi_summary.csv
```

---

# Datasets Used

## 1. Superstore Dataset

Used for:

* Discount leakage analysis
* Pricing leakage analysis
* Profitability analysis

## 2. Olist E-commerce Dataset

Used for:

* Billing leakage analysis
* Payment reconciliation analysis

## 3. UCI Online Retail Dataset

Used for:

* Returns leakage analysis
* Customer risk analysis

---

# Processed Data

The processed CSV files were generated using Python and SQLite-based analytical workflows.

These files were later imported into Power BI for dashboard creation.

---

# Note

To fully reproduce this project:

1. Download the original datasets
2. Place them inside the `Raw_Data` folder
3. Run the Jupyter notebooks in sequence
4. Generate processed analytical datasets
5. Import processed CSVs into Power BI
