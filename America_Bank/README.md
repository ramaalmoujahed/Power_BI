# America Bank PowerBI Analysis

A Power BI dashboard analyzing customer banking data — built as part of an IoT/data analytics coursework project. The dataset is training data, not real customer information.

## Overview

The dashboard ("American_Bank_Analysis.pbix") explores a banking customer base of ~4,000 records, looking at balances, demographics (gender, age, marital status, education), loan behavior (house loans, other loans, defaults), and geographic distribution across a handful of US states. It includes a word cloud visual, a US state shape map, and a dynamic measure selector that lets a single chart switch between Balance, Average Balance, Customer Count, and Average Age.

This repo contains the underlying data, the DAX measures that power the dashboard, and the queries used to extract everything — exported directly from the Power BI semantic model using DAX Studio, since the raw `.pbix` data model is stored in a compressed binary format that isn't directly readable outside Power BI.

## Repository structure

```
America-Bank-PowerBI-Analysis/
├── data/
│   ├── README.md
│   ├── America_Bank_New_FullData.csv
│   ├── Dims.csv
│   └── Selection_Table.csv
├── measures/
│   ├── America_Bank_Measures.md
│   └── Measure_Values.csv
├── queries/
│   └── America_Bank_DAX_Queries.txt
└── README.md   (this file)
```

| Folder | What's inside |
|---|---|
| `data/` | The raw tables behind the dashboard: the main customer fact table, a supporting dimension table, and a small lookup table that drives a dynamic measure selector |
| `measures/` | The 14 DAX measures used in the dashboard — both their formulas and their calculated values |
| `queries/` | The DAX queries used to pull data out of the model, including both auto-generated visual queries and manual export queries |

## The dataset

The main table, `America Bank New`, has **3,994 rows** and **27 columns**, including:

- **Demographics:** Gender, Age, Marital Status, Education, State
- **Financials:** Balance, House Loan, Other Loan, Loan Default
- **Contact/Outcome:** Contact method, campaign outcome (poutcome)
- **Derived fields:** Full Name, Year/Month/Quarter, Age Groups, Balance Groups

## The measures

14 DAX measures support the dashboard, covering:
- Aggregate balance figures (total, by gender, by housing status, by job classification)
- Customer counts and averages (age, balance)
- A dynamic "Measure Logic" switch that lets one visual display different metrics based on a slicer selection
- Two informational measures (`User1`, `User2`) that return the current report viewer's username

See `measures/America_Bank_Measures.md` for the full formula list.

## How this data was extracted

Power BI stores its data model in a compressed binary format (XPress9) inside the `.pbix` file, which isn't human-readable or directly parsable outside of Power BI/Analysis Services. To get usable data and documentation out of it, this project used:

1. **DAX Studio** — connected to the live Power BI session to run `EVALUATE` queries against individual tables and pull row-level data
2. **View Metrics (VertiPaq Analyzer)** — exported as a `.vpax` file, which contains the model's schema and all measure formulas in a readable JSON/XML format
3. **Performance Analyzer** — used to capture the auto-generated DAX queries behind each visual on the report page

## Notes

- This is coursework/training data — not a real bank's customer information.
- Numbers in `measures/Measure_Values.csv` reflect a snapshot at the time of export and may not match the live dashboard if the underlying data changes.
