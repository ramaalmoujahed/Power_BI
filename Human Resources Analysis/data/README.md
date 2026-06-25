# Data

Raw tables exported from the Power BI data model using DAX Studio (`EVALUATE` queries). This is training/coursework data, not real employee information.

## Files

### `Human_Resources_FullData.csv`
The main fact table. One row per employee record.

- **Rows:** 3,520
- **Columns:** 24 (Gender, Marital Status, Education, Business Unit, Departments, Job Classification, Job Type, Emp. Type, Job Involvement, Job Satisfaction, Relationship Satisfaction, Performance Rating, Years in Current Role, Years Since Last Promotion, Salary, Sick Days, Balance Days, and supporting/date columns)
- **Source query:**
  ```dax
  EVALUATE 'Human Resources'
  ```

### `Date.csv`
The model's calendar/date table, used for the `LY Salary` (last-year) time-intelligence measure.

- **Rows:** 2,190
- **Source query:**
  ```dax
  EVALUATE 'Date'
  ```

### `Dims.csv`
A supporting dimension table used elsewhere in the model.

- **Rows:** 35,200
- **Source query:**
  ```dax
  EVALUATE Dims
  ```

### `Metrics.csv`
A small lookup table that drives the "Measure Selected" slicer, letting report visuals switch between Salary and Employee Count views.

- **Rows:** 2
- **Source query:**
  ```dax
  EVALUATE Metrics
  ```

### `Avg_Metrics.csv`
A small lookup table that drives the "Measure Selection Avg" slicer, letting report visuals switch between Average Salary, Average Balance Days, and Average Sick Days.

- **Rows:** 3
- **Source query:**
  ```dax
  EVALUATE 'Avg Metrics'
  ```

## Notes
- All tables were exported directly from the underlying Power BI semantic model, not the report visuals, so they reflect the full underlying data rather than whatever happened to be filtered or summarized on a dashboard page.
- The model also contains several auto-generated hidden date tables (`LocalDateTable_*`, `DateTableTemplate_*`) and a `Last Refresh Date` table. These are Power BI internals rather than meaningful business data, so they aren't included here.
- See the `measures/` folder for how these tables are used in calculations, and `queries/` for the complete list of DAX queries run against this model.
