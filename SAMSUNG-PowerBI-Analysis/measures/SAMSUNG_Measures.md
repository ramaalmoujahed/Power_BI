# SAMSUNG Supply Chain — DAX Measures

Extracted directly from the data model in `SAMSUNG powerBI Analysis.pbix`. There are **30 measures**. Most are in **Measures Table**; `Total_Unit_Cost`, `Avg_Unit_Cost` and `Avg_quality_score` are stored on `fact_procurement`.

Comments (`--`) inside the formulas are the author's notes from the latest round of fixes.

## Contents

- **Sales:** Total_Revenue, Profit, Profit Margin %, discount, Discount %, Growth_Revenue, Total_Sales_Quantity, Total_Sales_Cost
- **Procurement:** Order_QTY, Avg_Load_Time, Total_Unit_Cost, Avg_Unit_Cost, Avg_quality_score
- **Inventory:** Inventory_Value, Inventory_Units, Safety_Stock, Reorder Point, Turnover_Rate, Days_of_Inventory
- **Production:** Defect_Rate, Defect Rate %
- **Shipment:** Total_Shipment, Total_Shipment_Quantity, Shipment_cost, Total_Delivered_ship, Delivered %, Total_Delay, Total_Delivered_Quantity, Total_QTY_Delay, Perfect Order %

---

## Sales

### Total_Revenue

*Table:* `Measures Table` · *Format:* General (not set)

```dax
Total_Revenue =
SUM(fact_sales[net_revenue])
```

Net revenue after discounts.

### Profit

*Table:* `Measures Table` · *Format:* General (not set)

```dax
Profit =
SUM(fact_sales[profit])
```

Total profit from sales.

### Profit Margin %

*Table:* `Measures Table` · *Format:* Percentage (`0.00%`)

```dax
Profit Margin % =
DIVIDE([Profit],[Total_Revenue])
```

Profit as a share of net revenue.

### discount

*Table:* `Measures Table` · *Format:* General (not set)

```dax
discount =
SUM(fact_sales[discount_amount])
```

Total discount given on sales.

### Discount %

*Table:* `Measures Table` · *Format:* Percentage (`0.00%`)

```dax
Discount % =
var product_amt = [discount]+[Total_Revenue]
return DIVIDE([discount],product_amt)
```

Discount as a share of revenue before discount.

### Growth_Revenue

*Table:* `Measures Table` · *Format:* Percentage (`0.00%`)

```dax
Growth_Revenue =
-- REMOVEFILTERS(dim_date) added: without it a year/month filter blocks last year's dates and the result was always blank
VAR curr_rev = [Total_Revenue]
VAR prev_rev = CALCULATE ( [Total_Revenue], SAMEPERIODLASTYEAR ( dim_date[date] ), REMOVEFILTERS ( dim_date ) )
RETURN IF ( NOT ISBLANK ( curr_rev ), DIVIDE ( curr_rev - prev_rev, prev_rev ) )
```

Year-over-year revenue growth against the same period last year.

### Total_Sales_Quantity

*Table:* `Measures Table` · *Format:* Number (`0`)

```dax
Total_Sales_Quantity =
SUM(fact_sales[quantity_sold])
```

Units sold.

### Total_Sales_Cost

*Table:* `Measures Table` · *Format:* General (not set)

```dax
Total_Sales_Cost =
-- Was SUM(fact_procurement[total_cost]) = purchasing spend, not cost of goods sold
SUM ( fact_sales[total_cost] )
```

Cost of goods sold, from the sales table.

## Procurement

### Order_QTY

*Table:* `Measures Table` · *Format:* Number (`0`)

```dax
Order_QTY =
SUM(fact_procurement[order_quantity])
```

Units ordered from suppliers.

### Avg_Load_Time

*Table:* `Measures Table` · *Format:* General (not set)

```dax
Avg_Load_Time =
AVERAGE(fact_procurement[lead_time_days])
```

Average procurement lead time in days (order date to delivery date).

### Total_Unit_Cost

*Table:* `fact_procurement` · *Format:* General (not set)

```dax
Total_Unit_Cost =
SUM(fact_procurement[total_cost])
```

Total procurement spend. Used by the Supplier-page selector as "Unit Cost".

### Avg_Unit_Cost

*Table:* `fact_procurement` · *Format:* General (not set)

```dax
Avg_Unit_Cost =
-- Was AVERAGE(dim_product[unit_cost]): not filtered by supplier, so every supplier showed the same value
DIVIDE ( SUM ( fact_procurement[total_cost] ), SUM ( fact_procurement[order_quantity] ) )
```

Average cost per unit ordered: procurement spend ÷ units ordered, per supplier.

### Avg_quality_score

*Table:* `fact_procurement` · *Format:* General (not set)

```dax
Avg_quality_score =
AVERAGE(fact_procurement[quality_score])
```

Average quality score of delivered purchase orders.

## Inventory

### Inventory_Value

*Table:* `Measures Table` · *Format:* Currency (`$#,0`)

```dax
Inventory_Value =
-- Money value of stock at cost, at the latest month-end snapshot in context (not summed over months)
VAR _lastKey = MAX ( fact_inventory[date_key] )
RETURN
    CALCULATE (
        SUMX ( fact_inventory, fact_inventory[stock_level] * RELATED ( dim_product[unit_cost] ) ),
        fact_inventory[date_key] = _lastKey
    )
```

Money value of stock at unit cost, at the latest snapshot in the current filter.

### Inventory_Units

*Table:* `Measures Table` · *Format:* Number (`#,0`)

```dax
Inventory_Units =
-- Units in stock at the latest month-end snapshot in context
VAR _lastKey = MAX ( fact_inventory[date_key] )
RETURN
    CALCULATE ( SUM ( fact_inventory[stock_level] ), fact_inventory[date_key] = _lastKey )
```

Units in stock at the latest snapshot in the current filter.

### Safety_Stock

*Table:* `Measures Table` · *Format:* Number (`0`)

```dax
Safety_Stock =
-- Snapshot value: latest month in context, not a sum over months
VAR _lastKey = MAX ( fact_inventory[date_key] )
RETURN
    CALCULATE ( SUM ( fact_inventory[safety_stock_level] ), fact_inventory[date_key] = _lastKey )
```

Safety-stock level at the latest snapshot.

### Reorder Point

*Table:* `Measures Table` · *Format:* Number (`0`)

```dax
Reorder Point =
-- Snapshot value: latest month in context, not a sum over months
VAR _lastKey = MAX ( fact_inventory[date_key] )
RETURN
    CALCULATE ( SUM ( fact_inventory[reorder_point] ), fact_inventory[date_key] = _lastKey )
```

Reorder point at the latest snapshot.

### Turnover_Rate

*Table:* `Measures Table` · *Format:* Number (`0.00`)

```dax
Turnover_Rate =
-- Units sold / average TOTAL stock across month-end snapshots (was averaging single product-warehouse rows)
VAR _avgStock =
    AVERAGEX ( VALUES ( fact_inventory[date_key] ), CALCULATE ( SUM ( fact_inventory[stock_level] ) ) )
RETURN
    DIVIDE ( [Total_Sales_Quantity], _avgStock )
```

Units sold ÷ average total stock across snapshots.

### Days_of_Inventory

*Table:* `Measures Table` · *Format:* Number (`0.0`)

```dax
Days_of_Inventory =
-- Days in the selected period / turnover (was always 365, wrong for months and multi-year totals)
VAR _lastSalesKey = CALCULATE ( MAX ( fact_sales[date_key] ), REMOVEFILTERS () )
VAR _days = COUNTROWS ( FILTER ( VALUES ( dim_date[date_key] ), dim_date[date_key] <= _lastSalesKey ) )
RETURN
    DIVIDE ( _days, [Turnover_Rate] )
```

Days in the selected period ÷ turnover rate: how many days the stock lasts.

## Production

### Defect_Rate

*Table:* `Measures Table` · *Format:* Percentage (`0.00%`)

```dax
Defect_Rate =
-- Defective units / units produced (was a count of defective units)
DIVIDE ( SUM ( fact_production[defective_units] ), SUM ( fact_production[quantity_produced] ) )
```

Defective units ÷ units produced.

### Defect Rate %

*Table:* `Measures Table` · *Format:* Percentage (`0.00%`)

```dax
Defect Rate % =
DIVIDE ( SUM ( fact_production[defective_units] ), SUM ( fact_production[quantity_produced] ) )
```

Identical to `Defect_Rate`. One of the two can be removed.

## Shipment

### Total_Shipment

*Table:* `Measures Table` · *Format:* Number (`0`)

```dax
Total_Shipment =
DISTINCTCOUNT(fact_shipment[shipment_id])
```

Number of unique shipments.

### Total_Shipment_Quantity

*Table:* `Measures Table` · *Format:* Number (`0`)

```dax
Total_Shipment_Quantity =
SUM(fact_shipment[quantity])
```

Units shipped.

### Shipment_cost

*Table:* `Measures Table` · *Format:* General (not set)

```dax
Shipment_cost =
SUM(fact_shipment[shipping_cost])
```

Total shipping cost.

### Total_Delivered_ship

*Table:* `Measures Table` · *Format:* Number (`0`)

```dax
Total_Delivered_ship =
CALCULATE([Total_Shipment], fact_shipment[status] ="Delivered")
```

Number of delivered shipments.

### Delivered %

*Table:* `Measures Table` · *Format:* Percentage (`0.00%`)

```dax
Delivered % =
-- Delivered shipments / ALL shipments. REMOVEFILTERS on status so a status filter or a click on the status donut cannot turn it into 100%
DIVIDE (
    CALCULATE ( [Total_Shipment], REMOVEFILTERS ( fact_shipment[status] ), fact_shipment[status] = "Delivered" ),
    CALCULATE ( [Total_Shipment], REMOVEFILTERS ( fact_shipment[status] ) )
)
```

Delivered shipments ÷ all shipments. Ignores status filters, so it cannot become 100% when a status is selected.

### Total_Delay

*Table:* `Measures Table` · *Format:* Number (`0`)

```dax
Total_Delay =
CALCULATE([Total_Shipment], fact_shipment[status]="Delayed")
```

Number of delayed shipments.

### Total_Delivered_Quantity

*Table:* `Measures Table` · *Format:* Number (`#,0`)

```dax
Total_Delivered_Quantity =
-- Units on delivered shipments. (Sales quantity filtered by shipment status does not work: the two fact tables are not related, so it returned all units sold.)
CALCULATE ( SUM ( fact_shipment[quantity] ), fact_shipment[status] = "Delivered" )
```

Units on delivered shipments.

### Total_QTY_Delay

*Table:* `Measures Table` · *Format:* Number (`0`)

```dax
Total_QTY_Delay =
-- Was summing SALES quantity filtered by shipment status (unrelated tables). Uses shipment quantity now.
CALCULATE ( SUM ( fact_shipment[quantity] ), fact_shipment[status] = "Delayed" )
```

Units on delayed shipments.

### Perfect Order %

*Table:* `Measures Table` · *Format:* Percentage (`0%`)

```dax
Perfect Order % =
var perfectOrder = CALCULATE([Total_Shipment],fact_shipment[status]="Delivered",fact_production[defect_rate_pct]<1)RETURN
    DIVIDE(perfectOrder,[Total_Shipment])
```

Intended as the share of delivered orders with low production defects. **Known issue:** `fact_production` is not related to `fact_shipment`, so the defect filter has no effect and the result equals the delivered share.

---

## Model reference

| Table | Notes |
|---|---|
| Measures Table | Holds most measures. Also contains an empty measure named `Measure`, which can be deleted. |
| Cost_Qty_Supplier | Field parameter that switches the Supplier-page trend chart between `Total_Unit_Cost` and `Order_QTY` |
| fact_sales, fact_procurement, fact_inventory, fact_production, fact_shipment | Fact tables |
| dim_date, dim_product, dim_supplier, dim_facility, dim_customer | Dimension tables |
| Folder | Leftover folder-import table, not used by the report. Can be deleted. |
