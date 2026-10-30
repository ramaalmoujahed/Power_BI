# America Bank Analysis — Power BI

A four-page Power BI report on a bank's customer base: who the customers are, where they live, how much they hold, and how home ownership, job type and loans relate to balances. It was built as part of a data analytics course. The dataset is training data, not real customer information.

![Final page](screenshots/Screenshot%202026-06-24%20222300.png)

## Overview

The report (`American Bank Analysis.pbix`) covers **3,994 customers** in four US states who joined the bank in 2017.

| Page | Focus | Key visuals |
|---|---|---|
| **Customers Demographic 1** | Who are our customers? | US state shape map, customers by job classification, gender and marital status; KPI cards for customers, balance, house owners, renters and loan defaulters; age, education and contact-method slicers |
| **Customer Demographic 2** | How are age and balance distributed? | Histograms of customers by age group and balance group, a scatter chart comparing two balance thresholds, job and education slicers |
| **By Balance** | Where is the money? | Total, male and female balance; balance by education, job classification and marital status; house-owner vs. renter and white- vs. blue-collar balance cards with icons |
| **Final** | Interactive summary | Quarter slicer, a **measure selector** (Balance / Avg Balance / # of Customers / Avg Age) and a **dimension selector** (Education / Gender / Job Classification / Marital Status) that drive the pie charts and the matrix, plus balance by month and state |

## Key insights

| Metric | Value |
|---|---|
| Customers | 3,994 |
| Total balance | $236.9M |
| Average balance | $59.3K |
| Average age | 38.6 |

- **California holds over half the customers and balances** (2,152 customers, $126.2M), followed by Nevada ($65.7M), Wyoming ($32.6M) and Oregon ($12.4M).
- **Men hold 54% of the total balance** ($128.6M vs. $108.3M for women), in line with their 54% share of customers.
- **White-collar customers hold almost twice the balance of blue-collar customers** ($116.1M vs. $61.3M).
- **Customers with a house loan** ("House Owners") hold $131.8M, compared with $105.1M for those without ("Renters").
- **Married customers are the largest group** (62%), and **secondary education** is the most common level (51%).
- **70% of customers are 25–45 years old.** Balances are right-skewed: the median is about $50K, and a long tail of large accounts pulls the average higher.

## Data model

| Table | Purpose |
|---|---|
| `America Bank New` | Main table: one row per customer, 26 columns |
| `Dims` | Unpivoted dimension table (dimension name + value) that powers the dimension selector on the *Final* page |
| `Selection Table` | 4-row disconnected table that powers the measure selector |
| `All Measures` | Measure table holding the 14 DAX measures |

Column details and source queries are in [`data/README.md`](data/README.md).

## DAX measures

14 measures. Full formulas are in [`measures/America_Bank_Measures.md`](measures/America_Bank_Measures.md).

| Group | Measures |
|---|---|
| Balance | Total Balance, Female Balance, Male Balance, House Owners Balance, Renters Balance, White Collar, Blue Collar |
| Customers | Total Customers, Avg Balance, Avg Age |
| Dynamic | Measure Selected, Measure Logic (a `SWITCH` that returns the metric picked in the selector) |
| Info | User1, User2 (return the current viewer's username) |

The values of all measures at export time are in `measures/All Measures.xlsx`.

## Repository structure

```
America_Bank/
├── American Bank Analysis.pbix
├── README.md                       (this file)
├── data/
│   ├── README.md                   (table descriptions and source queries)
│   ├── America_Bank_New_FullData.xlsx
│   ├── Dims.xlsx
│   └── Selection Table.xlsx
├── measures/
│   ├── America_Bank_Measures.md    (formulas)
│   └── All Measures.xlsx           (values at export time)
├── queries/
│   └── America_Bank_DAX_Queries.txt
└── screenshots/
    └── README.md                   (page-by-page gallery)
```

## How the data was extracted

Power BI stores its data model in a compressed binary format (XPress9) inside the `.pbix` file, so it can't be read directly outside Power BI. The tables, measures and queries here were exported with:

1. **DAX Studio:** connected to the open report and ran `EVALUATE` queries to export each table.
2. **View Metrics (VertiPaq Analyzer):** exported a `.vpax` file containing the model schema and every measure formula.
3. **Performance Analyzer:** captured the DAX query behind each visual (see `queries/`).

## How to use

1. Open `American Bank Analysis.pbix` in **Power BI Desktop**.
2. On the **Final** page, use the buttons at the top to pick a measure and the buttons below the pie charts to pick a dimension.
3. The file includes the **Word Cloud** custom visual package, although no page currently uses it.

## Known issues

| Issue | Where | Suggested fix |
|---|---|---|
| The pie chart titled **by States** shows Male/Female instead of states | Final page | Set its legend to `State` |
| The dimension matrix shows the **same values in every row**, e.g. Female and Male both $83.9M | Final page | `Dims` has a `Customer ID` column. Check that it is related to `America Bank New[Customer ID]` with a filter direction that lets `Dims[Value]` filter the main table. |
| Typo in the measure name `Avg Balalance` and in the chart title "Marital statues" | Measures, page 1 | Rename to *Avg Balance* and *Marital Status* |
| "House Owners" means customers *with a house loan*, not people who own a home | By Balance page | Relabel as *With Housing Loan* / *Without Housing Loan* |
| The data file starts with a title row, so the headers are on row 2 | `data/America_Bank_New_FullData.xlsx` | Delete the first row, or skip it when importing |

## Notes

- This is coursework/training data, not a real bank's customer information.
- The report title reads "Bank of America", but the data has no connection to the real bank.

## Tools

Power BI Desktop · Power Query · DAX · DAX Studio · VertiPaq Analyzer
