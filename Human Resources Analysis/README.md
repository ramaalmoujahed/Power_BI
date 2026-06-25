# Human Resources PowerBI Analysis

A Power BI dashboard analyzing employee data — built as part of a data analytics coursework project. The dataset is training data, not real employee records.

## Overview

The dashboard ("Human_Resources_Analysis.pbix") explores a workforce of 3,520 employees, looking at salary costs, attendance (sick days, balance/leave days), employment status (active vs. inactive), and demographic and role-based breakdowns (department, business unit, job classification, job type, education, gender, marital status). It includes a dynamic measure selector that lets visuals switch between Salary and Employee Count views, plus a second selector for switching between Average Salary, Average Balance Days, and Average Sick Days.

This repo contains the underlying data, the DAX measures that power the dashboard, and the queries used to extract everything — exported directly from the Power BI semantic model using DAX Studio, since the raw `.pbix` data model is stored in a compressed binary format that isn't directly readable outside Power BI.

## Repository structure

```
Human-Resources-PowerBI-Analysis/
├── data/
│   ├── README.md
│   ├── Human_Resources_FullData.csv
│   ├── Dims.csv
│   ├── Date.csv
│   ├── Metrics.csv
│   └── Avg_Metrics.csv
├── measures/
│   ├── HR_Measures.md
│   └── Measure_Values.csv
├── queries/
│   └── HR_DAX_Queries.txt
└── README.md   (this file)
```

| Folder | What's inside |
|---|---|
| `data/` | The raw tables behind the dashboard: the main employee fact table, a calendar/date table, a dimension table, and two small lookup tables that drive the dynamic measure selectors |
| `measures/` | The 13 DAX measures used in the dashboard — both their formulas and their calculated values |
| `queries/` | The DAX queries used to pull data out of the model |

## The dataset

The main table, `Human Resources`, has **3,520 rows** and **24 columns**, including:

- **Demographics:** Gender, Marital Status, Education
- **Role/Org:** Business Unit, Departments, Job Classification, Job Type, Emp. Type (Active/Inactive)
- **Performance:** Job Involvement, Job Satisfaction, Relationship Satisfaction, Performance Rating
- **Tenure:** Years in Current Role, Years Since Last Promotion
- **Financials & Attendance:** Salary, Sick Days, Balance Days

## The measures

13 DAX measures support the dashboard, covering:
- Aggregate and average figures for salary, sick days, and balance days
- Employee counts (total and active-only)
- A year-over-year salary comparison (`LY Salary`, using time intelligence)
- Two dynamic "Measure Logic" switches that let visuals display different metrics based on slicer selections (Salary vs. Employees; Avg Salary vs. Avg Balance Days vs. Avg Sick Days)

See `measures/HR_Measures.md` for the full formula list. All measure values have been cross-checked for internal consistency (e.g. Total Salary Cost ÷ Total Employees = Average Salary, exactly).

## Snapshot figures

| Measure | Value |
|---|---|
| Total Employees | 3,520 |
| Total Active Employees | 1,766 |
| Total Salary Cost | $335,271,478 |
| Average Salary | $95,247.58 |
| Total Sick Days | 22,879 |
| Average Sick Days | 6.50 |
| Total Balance Days | 43,871 |
| Average Balance Days | 12.46 |

## How this data was extracted

Power BI stores its data model in a compressed binary format (XPress9) inside the `.pbix` file, which isn't human-readable or directly parsable outside of Power BI/Analysis Services. To get usable data and documentation out of it, this project used:

1. **DAX Studio** — connected to the live Power BI session to run `EVALUATE` queries against individual tables and pull row-level data
2. **View Metrics (VertiPaq Analyzer)** — exported as a `.vpax` file, which contains the model's schema and all measure formulas in a readable JSON format
3. **Report layout inspection** — the report's `Layout` file (UTF-16 encoded JSON-in-JSON) was parsed directly to identify table and measure names before the `.vpax` export was available

## Notes

- This is coursework/training data — not a real company's employee information.
- `LY Salary` (Last Year Salary) uses `SAMEPERIODLASTYEAR` time intelligence. In this snapshot it matches `Total Salary Cost` exactly — likely because the export's filter context spans the full date range available in the `Date` table, not because of a calculation error.
- Numbers in `measures/Measure_Values.csv` reflect a snapshot at the time of export and may not match the live dashboard if the underlying data changes.
