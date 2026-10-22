# DAX — Insurance Risk & Claims Analysis

All objects live in the table `insurance_data_car`, except the field parameter, which is its own table.

## Measures

```DAX
Total Policies = COUNT(insurance_data_car[ID])
```
Number of policies, which is the size of the customer base. Shown with display units set to *None* on the KPI card.

```DAX
Total Claim Amount = SUM(insurance_data_car[Claim Amount])
```
Total payout across all claims. Format: Currency ($), shown in millions.

```DAX
Average Claim Amount = AVERAGE(insurance_data_car[Claim Amount])
```
Average payout per policy, which indicates claim severity. Format: Currency ($).

```DAX
Average Claim Frequency = AVERAGE(insurance_data_car[Claim Freq])
```
Average number of claims per policy (≈ 0.51).

```DAX
Male Count = CALCULATE(COUNT(insurance_data_car[ID]), insurance_data_car[Gender] = "Male")

Female Count = CALCULATE(COUNT(insurance_data_car[ID]), insurance_data_car[Gender] = "Female")
```
Policies by gender, shown side by side with icons in the KPI panel.

## Calculated columns

```DAX
Age = DATEDIFF(insurance_data_car[BirthDate], TODAY(), YEAR)
```
Policyholder age as of today. Because it uses `TODAY()`, the values update over time.

```DAX
Age Group =
SWITCH(
    TRUE(),
    insurance_data_car[Age] >= 15 && insurance_data_car[Age] <= 25, "15-25",
    insurance_data_car[Age] >= 26 && insurance_data_car[Age] <= 35, "26-35",
    insurance_data_car[Age] >= 36 && insurance_data_car[Age] <= 45, "36-45",
    insurance_data_car[Age] >= 46 && insurance_data_car[Age] <= 55, "46-55",
    insurance_data_car[Age] >= 56 && insurance_data_car[Age] <= 65, "56-65",
    insurance_data_car[Age] >= 66 && insurance_data_car[Age] <= 75, "66-75",
    "76+"
)
```
10-year age buckets used by the Age Group histogram.

## Field parameter

```DAX
Select Measure = {
    ("Total Claim Amount", NAMEOF('insurance_data_car'[Total Claim Amount]), 0),
    ("Total Policies", NAMEOF('insurance_data_car'[Total Policies]), 1)
}
```
Created with *Modeling → New parameter → Fields*. The parameter is placed on each chart's value axis and drives a single-select dropdown slicer, so every chart can switch between claim amount and policy count.

## Expected values (full dataset)

| Measure | Value |
|---|---|
| Total Policies | 37,542 |
| Total Claim Amount | $187.8M |
| Average Claim Amount | ≈ $5,003 |
| Average Claim Frequency | 0.51 |
| Male Count / Female Count | 18,736 / 18,806 |
