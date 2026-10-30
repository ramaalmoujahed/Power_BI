# Data

Raw tables exported from the Power BI data model using DAX Studio (`EVALUATE` queries). This is training/coursework data, not real customer information.

## Files

### `America_Bank_New_FullData.xlsx`
The main fact table. One row per customer record.

- **Rows:** 3,994
- **Columns:** 26 (Customer ID, First/Last Name, Gender, Age, State, Job Classification, Date Joined, Balance, Marital Status, Education, House Loan, Other Loan, Contact, Previous Outcome, Loan Default, and calculated/display columns such as Full Name, Year, Month, Quarter, Age Group, Balance Group)
- **Note:** the sheet starts with a title row (`America Bank Customer Data`), so the column headers are on row 2.
- **Source query:**
  ```dax
  EVALUATE 'America Bank New'
  ```

### `Dims.xlsx`
An unpivoted dimension table (`Customer ID`, `DimName`, `Value`) with one row per customer per dimension (Gender, Education, Job Classification, Marital Status, …). It powers the dimension selector on the *Final* page.

- **Rows:** 19,970
- **Source query:**
  ```dax
  EVALUATE Dims
  ```

### `Selection Table.xlsx`
A small lookup table that drives the dynamic "Measure Selected" slicer used by the `Measure Logic` measure (lets one chart switch between Balance / Avg Balance / # of Customers / Avg Age).

- **Rows:** 4
- **Source query:**
  ```dax
  EVALUATE 'Selection Table'
  ```

## Notes
- All tables were exported directly from the underlying Power BI semantic model, not the report visuals, so they reflect the full underlying data rather than whatever happened to be filtered or summarized on a dashboard page.
- See the `measures/` folder for how these tables are used in calculations, and `queries/` for the complete list of DAX queries run against this model.
