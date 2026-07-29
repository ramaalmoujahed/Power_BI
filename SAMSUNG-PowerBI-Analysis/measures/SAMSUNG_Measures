# Procurement & Supply Chain Analytics — DAX Measures

Extracted from the Power BI data model using DAX Studio.
These are the 26 measures defined in the data model, found in the **"Measures Table"**.

---

## Avg Load Time
```dax
Avg_Load_Time = AVERAGE(fact_procurement[lead_time_days])
```
Format: Decimal number

Calculates the average procurement lead time in days.

---

## Days of Inventory
```dax
Days_of_Inventory = DIVIDE(365, [Turnover_Rate])
```
Format: Whole number

Estimates how many days the current inventory level can support based on inventory turnover.

---

## Defect Rate
```dax
Defect_Rate = SUM(fact_production[defective_units])
```
Format: Whole number

Calculates the total number of defective production units.

---

## Delivered %
```dax
Delivered % = DIVIDE([total_delivered_ship],[Total_Shipment])
```
Format: Percentage

Measures the percentage of shipments successfully delivered.

---

## Discount
```dax
discount = SUM(fact_sales[discount_amount])
```
Format: Currency

Calculates the total discount amount applied to sales.

---

## Discount %
```dax
Discount % =
VAR product_amt = [discount]+[Total_Revenue]
RETURN
DIVIDE([discount],product_amt)
```
Format: Percentage

Calculates the discount percentage compared to total revenue before discount.

---

## Growth Revenue
```dax
Growth_Revenue =
VAR curr_rev = [Total_Revenue]
VAR prev_rev =
    CALCULATE(
        [Total_Revenue],
        SAMEPERIODLASTYEAR(dim_date[date])
    )
RETURN
DIVIDE(curr_rev-prev_rev,prev_rev)
```
Format: Percentage

Calculates year-over-year revenue growth.

---

## Inventory Value
```dax
Inventory_Value = SUM(fact_inventory[stock_level])
```
Format: Whole number

Shows the total available inventory stock level.

---

## Order Quantity
```dax
Order_QTY = SUM(fact_procurement[order_quantity])
```
Format: Whole number

Calculates the total quantity ordered from procurement activities.

---

## Perfect Order %
```dax
Perfect Order % =
VAR perfectOrder =
    CALCULATE(
        [Total_Shipment],
        fact_shipment[status]="Delivered",
        fact_production[defect_rate_pct]<1
    )
RETURN
DIVIDE(perfectOrder,[Total_Shipment])
```
Format: Percentage

Measures the percentage of orders completed successfully without delivery issues or production defects.

---

## Profit
```dax
Profit = SUM(fact_sales[profit])
```
Format: Currency

Calculates the total profit generated from sales.

---

## Profit Margin %
```dax
Profit Margin % = DIVIDE([Profit],[Total_Revenue])
```
Format: Percentage

Calculates profit as a percentage of total revenue.

---

## Reorder Point
```dax
Reorder Point = SUM(fact_inventory[reorder_point])
```
Format: Whole number

Shows the inventory threshold where replenishment should be triggered.

---

## Safety Stock
```dax
Safety_Stock = SUM(fact_Inventory[safety_stock_level])
```
Format: Whole number

Shows the additional inventory kept to avoid stock shortages.

---

## Shipment Cost
```dax
Shipment_cost = SUM(fact_shipment[shipping_cost])
```
Format: Currency

Calculates the total shipping and transportation cost.

---

## Total Delay
```dax
Total_Delay =
CALCULATE(
    [Total_Shipment],
    fact_shipment[status]="Delayed"
)
```
Format: Whole number

Counts the number of delayed shipments.

---

## Total Delivered Quantity
```dax
Total_Delivered_Quantity =
CALCULATE(
    [Total_Sales_Quantity],
    fact_shipment[status]=""
)
```
Format: Whole number

Calculates the quantity delivered based on shipment status.

---

## Total Delivered Shipments
```dax
Total_Delivered_ship =
CALCULATE(
    [Total_Shipment],
    fact_shipment[status]="Delivered"
)
```
Format: Whole number

Counts the number of successfully delivered shipments.

---

## Total Delayed Quantity
```dax
Total_QTY_Delay =
CALCULATE(
    [Total_Sales_Quantity],
    fact_shipment[Status]="Delayed"
)
```
Format: Whole number

Calculates the quantity affected by delayed shipments.

---

## Total Revenue
```dax
Total_Revenue = SUM(fact_sales[net_revenue])
```
Format: Currency

Calculates the total revenue generated from sales.

---

## Total Sales Cost
```dax
Total_Sales_Cost = SUM(fact_procurement[total_cost])
```
Format: Currency

Calculates the total procurement cost associated with sales activities.

---

## Total Sales Quantity
```dax
Total_Sales_Quantity = SUM(fact_sales[quantity_sold])
```
Format: Whole number

Calculates the total quantity of products sold.

---

## Total Shipment
```dax
Total_Shipment =
DISTINCTCOUNT(fact_shipment[shipment_id])
```
Format: Whole number

Counts the total number of unique shipments.

---

## Total Shipment Quantity
```dax
Total_Shipment_Quantity =
SUM(fact_shipment[quantity])
```
Format: Whole number

Calculates the total quantity included in shipments.

---

## Turnover Rate
```dax
Turnover_Rate =
DIVIDE(
    [Total_Sales_Quantity],
    AVERAGE(fact_inventory[stock_level])
)
```
Format: Decimal number

Measures how efficiently inventory is being sold and replaced.

---

## Model Reference (for context)

**Tables in the model:**

| Table | Notes |
|---|---|
| Measures Table | Holding table containing the 26 analytical measures |
| fact_sales | Main sales transaction table |
| fact_procurement | Procurement and purchasing data |
| fact_inventory | Inventory and stock management data |
| fact_shipment | Shipment and delivery tracking data |
| fact_production | Production quality and defect information |
| dim_date | Date dimension used for time intelligence calculations |

---

**Total Measures: 26**

**Measure Table:** `Measures Table`
