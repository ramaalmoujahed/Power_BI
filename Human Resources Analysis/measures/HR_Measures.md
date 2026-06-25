# Human Resources — DAX Measures

Extracted from `Human_Resources_Analysis.vpax` (DAX Studio View Metrics export).
These are the 13 measures defined in the data model, found in the "All Measures" table.

---

## Average Balance Days
```dax
Average Balance Days = AVERAGE('Human Resources'[Balance Days])
```

---

## Average Salary
```dax
Average Salary = AVERAGE('Human Resources'[Salary])
```
Format: Currency (`$#,0.###############`)

---

## Average Sick Days
```dax
Average Sick Days = AVERAGE('Human Resources'[Sick Days])
```

---

## LY Salary
```dax
LY Salary = CALCULATE([Total Salary Cost], SAMEPERIODLASTYEAR('Date'[Date]))
```
Format: Currency (`$#,0.###############`)

Returns the total salary cost for the same period one year earlier, using the
`Date` table for time intelligence. **Note:** if this returns the same value as
`Total Salary Cost`, it likely means the current filter/report context spans
the table's full date range (so "last year" resolves to the same period), or
the `Date` table doesn't yet have more than one year of data to compare against.

---

## Total Active Employees
```dax
Total Active Employees = CALCULATE([Total Employees], 'Human Resources'[Emp.Type] = "Active")
```
Format: Whole number (`0`)

---

## Total Balance Days
```dax
Total Balance Days = SUM('Human Resources'[Balance Days])
```
Format: Whole number (`0`)

---

## Total Employees
```dax
Total Employees = COUNTROWS('Human Resources')
```
Format: Whole number (`0`)

---

## Total Salary Cost
```dax
Total Salary Cost = SUM('Human Resources'[Salary])
```
Format: Whole number (`0`)

---

## Total Sick Days
```dax
Total Sick Days = SUM('Human Resources'[Sick Days])
```
Format: Whole number (`0`)

---

## Measure Selected
```dax
Measure Selected = SELECTEDVALUE(Metrics[Measures ], "Salary")
```
Drives a slicer that lets the report switch between Salary and Employees views.
Note the trailing space in the column name `Measures ` — this matches the
column exactly as named in the `Metrics` table.

---

## Measure Logic
```dax
Measure Logic =
SWITCH(
    TRUE(),
    'All Measures'[Measure Selected] = "Salary",    [Total Salary Cost],
    'All Measures'[Measure Selected] = "Employees", [Total Employees],
    [Total Salary Cost]
)
```
Format: Whole number (`0`)

Dynamic measure that switches its output based on the `Measure Selected`
slicer — lets one visual swap between Total Salary Cost and Total Employees.

---

## Measure Selection Avg
```dax
Measure Selection Avg = SELECTEDVALUE('Avg Metrics'[Measure ], "Avg Salary")
```
A second, separate slicer driving the "average" family of measures. Note the
trailing space in the column name `Measure ` from the `Avg Metrics` table.

---

## Measure Logic Avg
```dax
Measure Logic Avg =
SWITCH(
    TRUE(),
    'All Measures'[Measure Selection Avg] = "Avg Salary",    [Average Salary],
    'All Measures'[Measure Selection Avg] = "Avg Bal Days",  [Average Balance Days],
    'All Measures'[Measure Selection Avg] = "Avg Sick Days", [Average Sick Days],
    [Average Salary]
)
```
Dynamic measure that switches between Average Salary, Average Balance Days,
and Average Sick Days based on the `Measure Selection Avg` slicer.

---

## Model Reference (for context)

**Tables in the model:**
| Table | Columns | Notes |
|---|---|---|
| Human Resources | 24 | Main fact table (employee records) |
| Date | 21 | Time intelligence / calendar table |
| All Measures | 2 | Holding table for the 13 measures above |
| Metrics | 3 | Drives the "Measure Selected" slicer |
| Avg Metrics | 3 | Drives the "Measure Selection Avg" slicer |
| Dims | 4 | Supporting dimension table |
| Last Refresh Date | 2 | Tracks when the dataset was last refreshed |
| DateTableTemplate_* / LocalDateTable_* | 8 each | Auto-generated hidden date tables (4 of these) |
