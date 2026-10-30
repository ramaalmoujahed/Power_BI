# Human Resources Analysis — Power BI

A three-page Power BI report on a company's workforce: headcount, salary cost, sick leave and leave balances, broken down by business unit, department, role and employee profile. It was built as part of a data analytics course. The dataset is training data, not real employee records.

![Salary and Employees page](screenshots/Screenshot%202026-06-24%20230140.png)

## Overview

The report (`Human Resources Analysis.pbix`) covers **3,520 employees** in four business units (Houston, Los Angeles, New York City and Seattle) and 15 departments, hired between 2013 and 2018. It uses a dark theme with a "Now Showing" label that names the metric currently selected.

| Page | Focus | Key visuals |
|---|---|---|
| **Salary and Employees Report** | How many people do we have and what do they cost? | KPI cards (employees, salary cost, active employees, sick days, balance days); six donut charts by job type, gender, marital status, education, years in role and years since promotion. A **Salary / Employees selector** switches every donut. Also sick days by year and quarter, and balance vs. sick days by department and city. |
| **Averages Report** | What does a typical employee look like? | Average balance days, salary and sick days; an **Avg Salary / Avg Balance Days / Avg Sick Days selector** that drives six donut charts; a gender × job classification matrix |
| **Business Unit / Regions Report** | How do the cities compare? | City buttons, KPI row (including average values), sick vs. balance days and salary cost vs. headcount by group, and a **dimension switcher** (Business Unit, Education, Gender, Groups, Job Involvement, …) that changes the rows of a matrix comparing all measures, including last year's salary |

Every page has slicers for gender, job type, job involvement, job satisfaction, performance rating and relationship satisfaction.

## Key figures

| Measure | Value |
|---|---|
| Total Employees | 3,520 |
| Active Employees | 1,766 (50.2%) |
| Total Salary Cost | $335,271,478 |
| Average Salary | $95,247.58 |
| Total / Average Sick Days | 22,879 / 6.50 |
| Total / Average Balance Days | 43,871 / 12.46 |

## Key insights

- **The workforce is evenly balanced** across cities (843–911 employees each), genders (1,767 male / 1,753 female), job types (full time / part time) and education levels. Most differences in this dataset are small.
- **Houston is the largest unit** by headcount (911) and salary cost ($87.1M). Los Angeles is the smallest (843, $79.8M).
- **Average salaries are within 2.5% of each other** across all 15 departments, from $94.0K in Public Relations to $96.1K in Advertising and Tech Support.
- **White-collar staff earn the most on average** ($96.2K), compared with $95.2K for blue-collar and $94.3K for "Other".
- **New York City has the highest average sick days** (6.68 per employee), compared with 6.40 in Houston.
- **Only half of the employees are active**, so headcount KPIs should be read with the *Employee Type* filter in mind.

## Data model

| Table | Purpose |
|---|---|
| `Human Resources` | Main table: one row per employee, 23 columns |
| `Date` | Calendar table (2013–2018, 2,190 days) used for time intelligence (`LY Salary`) |
| `Dims` | Unpivoted dimension table (`ID`, `Dimentions`, `Value`) that powers the dimension switcher on the Business Unit page |
| `Metrics` | 2-row disconnected table for the Salary / Employees selector |
| `Avg Metrics` | 3-row disconnected table for the averages selector |
| `All Measures` | Measure table holding the 13 DAX measures |

Column details and source queries are in [`data/README.md`](data/README.md).

## DAX measures

13 measures. Full formulas are in [`measures/HR_Measures.md`](measures/HR_Measures.md), and their values at export time are in `measures/Measure_Values.csv.xlsx`.

| Group | Measures |
|---|---|
| Totals | Total Employees, Total Active Employees, Total Salary Cost, Total Sick Days, Total Balance Days |
| Averages | Average Salary, Average Sick Days, Average Balance Days |
| Time intelligence | LY Salary (`SAMEPERIODLASTYEAR`) |
| Dynamic | Measure Selected, Measure Logic (Salary vs. Employees); Measure Selection Avg, Measure Logic Avg (the three averages) |

All values have been cross-checked: Total Salary Cost ÷ Total Employees equals Average Salary exactly.

## Repository structure

```
Human Resources Analysis/
├── Human Resources Analysis.pbix
├── README.md                          (this file)
├── data/
│   ├── README.md                      (table descriptions and source queries)
│   ├── Human__Resources_FullData.xlsx
│   ├── Date.xlsx
│   ├── Dims.xlsx
│   ├── Metrics.xlsx
│   └── Avg_Metric.xlsx
├── measures/
│   ├── HR_Measures.md                 (formulas)
│   └── Measure_Values.csv.xlsx        (values at export time)
├── queries/
│   └── HR_DAX_Queries.txt
└── screenshots/
    └── README.md                      (page-by-page gallery)
```

## How the data was extracted

Power BI stores its data model in a compressed binary format (XPress9) inside the `.pbix` file, so it can't be read directly outside Power BI. The tables, measures and queries here were exported with:

1. **DAX Studio:** connected to the open report and ran `EVALUATE` queries to export each table.
2. **View Metrics (VertiPaq Analyzer):** exported a `.vpax` file containing the model schema and every measure formula.
3. **Report layout inspection:** the report's `Layout` file (UTF-16 JSON) was parsed to list table and measure names before the `.vpax` export was available.

## How to use

1. Open `Human Resources Analysis.pbix` in **Power BI Desktop**.
2. Use the buttons at the top right of each page to switch the metric, and the city buttons to focus on one business unit.
3. On the Business Unit page, use the row of buttons above the matrix to change its dimension.

## Known issues

| Issue | Where | Suggested fix |
|---|---|---|
| The Salary / Employees selector shows a **(Blank)** button | Salary and Employees page | `Metrics` has only two rows, so the blank comes from the model. Add a visual-level filter on the slicer that excludes blank values. |
| The "Now Showing" label reads *Salary* while *Avg Sick Days* is selected | Business Unit page | Point the label at `Measure Selection Avg` on that page |
| Average values in the donut charts show long decimals, e.g. 6.1312649… | Averages page | Set a fixed number of decimal places on the measures |
| **Total Sick Days by Year and Quarter** shows the same bar for every quarter | Salary and Employees page | Relate `Date[Date]` to `Human Resources[Hire Date]`, or use a date column that actually varies with sick days |
| `LY Salary` equals `Total Salary Cost` | Measures | Same cause: no active date relationship, so `SAMEPERIODLASTYEAR` doesn't filter anything |
| Typos: `Departmets`, `Years since last promition`, `Dimentions`, and the chart title "By Material Status" | Model and page titles | Rename to *Departments*, *Years Since Last Promotion*, *Dimensions*, *By Marital Status* |
| A trailing space in the column names `Metrics[Measures ]` and `Avg Metrics[Measure ]` | Model | Remove the trailing space |

## Notes

- This is coursework/training data, not a real company's employee information.
- The numbers in `measures/Measure_Values.csv.xlsx` are a snapshot taken at export time.

## Tools

Power BI Desktop · Power Query · DAX · DAX Studio · VertiPaq Analyzer
