# Data

Raw tables exported from the Power BI data model using DAX Studio (`EVALUATE` queries). This is training/coursework data, not real customer information.

## Files

### `America_Bank_New_FullData.csv`
The main fact table. One row per customer record.

- **Rows:** 3,994
- **Columns:** 27 (Customer ID, First/Last Name, Gender, Age, State, Job Classification, Date Joined, Balance, Marital Status, Education, House Loan, Other Loan, Contact, Poutcome, Loan Default, and a few calculated/display columns such as Full Name, Year, Month, Quarter, Age Groups, Balance Groups)
- **Source query:**
  ```dax
  EVALUATE 'America Bank New'
  ```

### `Dims.csv`
A supporting dimension table used elsewhere in the model.

- **Rows:** 19,970
- **Source query:**
  ```dax
  EVALUATE Dims
  ```

### `Selection_Table.csv`
A small lookup table that drives the dynamic "Measure Selected" slicer used by the `Measure Logic` measure (lets one chart switch between Balance / Avg Balance / # of Customers / Avg Age).

- **Rows:** 4
- **Source query:**
  ```dax
  EVALUATE 'Selection Table'
  ```

## Notes
- All tables were exported directly from the underlying Power BI semantic model, not the report visuals, so they reflect the full underlying data rather than whatever happened to be filtered or summarized on a dashboard page.
- See the `measures/` folder for how these tables are used in calculations, and `queries/` for the complete list of DAX queries run against this model.
