# America Bank New — DAX Measures

Extracted from `American_Bank_Analysis.vpax` (DAX Studio View Metrics export).
These are the 14 measures defined in the data model, found in the "All Measures" table.

---

## Total Balance
```dax
Total Balance = SUM('America Bank New'[Balance])
```
Format: Currency (`$#,0.00`)

---

## Female Balance
```dax
Female Balance = CALCULATE([Total Balance], 'America Bank New'[Gender] = "Female")
```
Format: Currency (`$#,0.00`)

---

## Male Balance
```dax
Male Balance = CALCULATE([Total Balance], 'America Bank New'[Gender] = "Male")
```
Format: Currency (`$#,0.00`)

---

## House Owners Balance
```dax
House Owners Balance = CALCULATE([Total Balance], 'America Bank New'[houseloan] = "yes")
```
Format: Currency (`$#,0.00`)

---

## Renters Balance
```dax
Renters Balance = CALCULATE([Total Balance], 'America Bank New'[houseloan] = "no")
```
Format: Currency (`$#,0.00`)

---

## White Collar
```dax
White Collar = CALCULATE([Total Balance], 'America Bank New'[Job Classification] = "White Collar")
```
Format: Currency (`$#,0.00`)

---

## Blue Collar
```dax
Blue Collar = CALCULATE([Total Balance], 'America Bank New'[Job Classification] = "Blue Collar")
```
Format: Currency (`$#,0.00`)

---

## Total Customers
```dax
Total Customers = COUNTROWS('America Bank New')
```
Format: Whole number (`0`)

---

## Avg Balalance
*(spelled as-is in the original model)*
```dax
Avg Balalance = AVERAGE('America Bank New'[Balance])
```
Format: Currency (`$#,0.###############`)

---

## Avg Age
```dax
Avg Age = AVERAGE('America Bank New'[Age])
```

---

## Measure Selected
```dax
Measure Selected = SELECTEDVALUE('Selection Table'[Measure], "Balance")
```
Used to drive a dynamic measure selector (likely tied to a slicer on the report).

---

## Measure Logic
```dax
Measure Logic =
SWITCH(
    TRUE(),
    'All Measures'[Measure Selected] = "Balance",        [Total Balance],
    'All Measures'[Measure Selected] = "Avg Balance",    [Avg Balalance],
    'All Measures'[Measure Selected] = "# of Customers", [Total Customers],
    'All Measures'[Measure Selected] = "Avg Age",        [Avg Age],
    [Total Balance]
)
```
Dynamic measure that switches its output based on what's selected in the
`Measure Selected` slicer — a common "field parameter" style pattern letting
one chart swap between Balance / Avg Balance / Customer Count / Avg Age.

---

## User1
```dax
User1 = USERNAME()
```
Returns the domain\username of whoever opens the report.

---

## User2
```dax
User2 = USERPRINCIPALNAME()
```
Returns the email/UPN of whoever opens the report.

---

## Model Reference (for context)

**Tables in the model:**
| Table | Rows | Notes |
|---|---|---|
| America Bank New | 3,994 | Main fact table (27 columns) |
| All Measures | 1 | Holding table for the 14 measures above |
| Selection Table | 4 | Drives the "Measure Selected" dynamic measure |
| Dims | 19,970 | Supporting dimension table |
| DateTableTemplate_* | 1 | Auto-generated hidden date table |
| LocalDateTable_* | 365 | Auto-generated date table tied to "Date Joined" |
