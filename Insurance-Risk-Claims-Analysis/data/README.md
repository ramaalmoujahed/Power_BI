# Data Dictionary — `insurance_policies_data.xlsx`

One sheet (`insurance_data_car`) with **37,542 rows × 16 columns**. Each row is one car-insurance policy and the claims made against it. This is demo data with synthetic values, not real customer records.

**Data quality:** every column is 100% valid with no errors and no empty values. `ID` is unique for all but one value, which appears twice.

| Column | Type | Description | Values / range |
|---|---|---|---|
| `ID` | Text | Policy identifier, used as the row key | e.g. `62-2999778` |
| `BirthDate` | Date | Policyholder's date of birth; used to derive **Age** and **Age Group** | 1952 – 2005 |
| `Car Color` | Text | Colour of the insured vehicle | e.g. Green, Turquoise, Crimson |
| `Car Make` | Text | Vehicle manufacturer | 78 makes (Ford, Chevrolet, Dodge, Toyota, …) |
| `Car Model` | Text | Vehicle model within the make | e.g. TSX, Pathfinder |
| `Car Use` | Text | How the vehicle is used | Private, Commercial |
| `Car Year` | Whole number | Year the vehicle was manufactured | 1916 – 2020 |
| `Coverage Zone` | Text | Area type where the policy applies | Urban, Highly Urban, Suburban, Rural, Highly Rural |
| `Education` | Text | Policyholder's highest education level | High School, Bachelors, Masters, PhD |
| `Gender` | Text | Policyholder's gender | Male, Female |
| `Marital Status` | Text | Policyholder's marital status | Single, Married, Divorced, Seperated *(spelling as in source)* |
| `Parent` | Text | Whether the policyholder is a parent | Yes, No |
| `Claim Amount` | Decimal ($) | Total amount paid out on the policy's claims | Total $187.8M; average ≈ $5,000 |
| `Claim Freq` | Whole number | Number of claims filed in the period | Average 0.51 |
| `Household Income` | Decimal ($) | Annual household income of the policyholder | — |
| `Kids Driving` | Whole number | Number of children in the household who drive the car | 0 – 3 |

## Columns added in Power BI

| Column | Formula idea | Values |
|---|---|---|
| `Age` | Years between `BirthDate` and today | Changes over time |
| `Age Group` | 10-year buckets of `Age` | 15–25, 26–35, 36–45, 46–55, 56–65, 66–75, 76+ |
