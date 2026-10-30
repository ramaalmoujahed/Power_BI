# SAMSUNG Supply Chain & Logistics Dashboard — Power BI

A multi-page Power BI report that follows Samsung products through the supply chain: from component suppliers, through manufacturing and warehousing, to shipment and the retail customers that sell them. The dataset is synthetic, covers **2023–2024**, and is not real Samsung data.

![Overview page](screenshots/Screenshot%202026-07-29%20184521.png)

## Overview

The report has a **Home** page with navigation buttons and five analysis pages:

| Page | Question it answers | Key visuals |
|---|---|---|
| **Home** | Where do I start? | Page navigator to every section |
| **Overview** | How is the whole chain performing? | Revenue, profit, margin and perfect-order KPIs; one card per stage (Supplier, Manufacturer, Shipment, Customer) with drill-through arrows; top suppliers, products, carriers and customers |
| **Supplier** | Which suppliers are slow or expensive? | Unit cost / order quantity by month (switchable), average lead time by supplier and by country |
| **Inventory & Production** | Are stock levels healthy and where do defects come from? | Defective units and inventory by month, defects by product, current stock vs. safety stock vs. reorder point |
| **Shipment** | Which carriers deliver on time? | Delivered vs. delayed shipments by carrier, status split, shipping cost by month, delay reasons |
| **Customer** | Who buys, and how profitable are they? | Revenue, profit and YoY growth by month; revenue by customer and channel; discount % by product; category bubble chart |

## Key insights

- **$176.95M net revenue** ($186.86M gross before $9.92M of discounts) and **$48.56M profit**, a **27.4% profit margin**.
- **Smartphones drive 56% of revenue** ($98.7M). The **Galaxy S24 Ultra** ($34.5M) and **Galaxy Z Fold5** ($27.7M) are the top products.
- **Revenue is spread evenly across customers:** Amazon, Flipkart, Best Buy and MediaMarkt each bring in about $35–37M. Samsung's own Direct Store is lowest at $32.2M.
- **Online is the largest channel** (41% of revenue), just ahead of retailers (40%). Direct sales make up 18%.
- **Lead times average 11.5 days.** Suppliers in China, Japan, South Korea and Taiwan take about 12.1–12.3 days, while the Samsung assembly sites in India and Vietnam take about 9.7–9.9.
- **75% of shipments are delivered**, 15% are in transit, 8% are delayed and 2% are processing. **Maersk Line** has the most delays. The top delay reasons are carrier capacity, documentation issues and port congestion.
- **Defective units rise sharply in Q4** (October–December), following the same pattern as inventory levels.

## Data model

A star schema with five fact tables sharing five dimensions, plus a dedicated `Measures Table`.

![Data model](screenshots/Screenshot%202026-07-22%20202613.png)

| Table | Type | Rows | Grain |
|---|---|---|---|
| `fact_sales` ✓ | Fact | 8,500 | One sales order line |
| `fact_shipment` | Fact | 7,500 | One shipment |
| `fact_production` ✓ | Fact | 4,500 | One production batch |
| `fact_procurement` ✓ | Fact | 2,200 | One purchase order |
| `fact_inventory` ✓ | Fact | 1,152 | Stock snapshot per product, warehouse and date |
| `dim_date` | Dimension | 731 | One day (2023-01-01 to 2024-12-31) |
| `dim_product` | Dimension | 16 | Product SKU (smartphones, tablets, TVs, appliances, wearables) |
| `dim_supplier` ✓ | Dimension | 7 | Component supplier |
| `dim_facility` | Dimension | 6 | 3 factories and 3 distribution centres |
| `dim_customer` | Dimension | 5 | Retail or online customer |

The `data/` folder contains the 5 tables marked with ✓ below. The other tables are included in the `.pbix` file but not exported as CSVs. Column descriptions are in the [data dictionary](data/README.md).

## DAX measures

25 measures are grouped by area. Full formulas and descriptions are in [`measures/SAMSUNG_Measures.md`](measures/SAMSUNG_Measures.md).

| Area | Measures |
|---|---|
| Sales | Total Revenue, Profit, Profit Margin %, Discount, Discount %, Growth Revenue (YoY), Total Sales Quantity, Total Sales Cost |
| Procurement | Order Quantity, Avg Lead Time |
| Inventory | Inventory Value, Safety Stock, Reorder Point, Turnover Rate, Days of Inventory |
| Production | Defect Rate (defective units) |
| Shipment | Total Shipment, Total Shipment Quantity, Shipment Cost, Total Delivered Shipments, Delivered %, Total Delay, Total Delayed Quantity, Total Delivered Quantity, Perfect Order % |

## Dashboard design

- **Navigation:** a home page with a page navigator, plus arrow buttons on the Overview cards that jump to each section.
- **Consistent layout:** every page has the same header, a KPI column on the left, and charts on a rounded light-blue panel.
- **Visual selector:** the Supplier page uses a button slicer to switch the trend chart between unit cost and order quantity.
- **Product images:** `dim_product[Img]` holds an image URL for each product.

## Repository structure

```
SAMSUNG-PowerBI-Analysis/
├── SAMSUNG powerBI Analysis.pbix
├── README.md                   (this file)
├── data/
│   ├── README.md               (data dictionary)
│   ├── dim_supplier.csv
│   ├── fact_inventory.csv
│   ├── fact_procurement.csv
│   ├── fact_production.csv
│   └── fact_sales.csv
├── measures/
│   └── SAMSUNG_Measures.md
└── screenshots/
    └── README.md               (page-by-page gallery)
```

## How to use

1. Open `SAMSUNG powerBI Analysis.pbix` in **Power BI Desktop**. All tables are already loaded in the file.
2. Start on the **Home** page and use the buttons to move between sections.
3. The CSVs in `data/` let you inspect the main tables without Power BI.

## Known issues

These were found in the previous version of the report. Some may already be fixed in the current `.pbix`:

| Issue | Where | Suggested fix |
|---|---|---|
| **Delivered %** card shows 100%, but only 75% of shipments are delivered | Shipment page | Check the filter context of the card. The measure itself (`Total_Delivered_ship / Total_Shipment`) returns 75% on the full table. |
| `Total_Delivered_Quantity` filters on `status = ""`, which matches no rows | Measures Table | Change the filter to `"Delivered"` |
| `Perfect Order %` filters `fact_shipment` and `fact_production` together, but the two facts are only linked through shared dimensions | Measures Table | Define a perfect order using shipment columns only, such as delivered and no delay reason |
| **Turnover Rate** is formatted as a percentage (134.8K%) | Supplier and Inventory pages | Format it as a decimal number |
| The name **Avg Load Time** should be **Avg Lead Time**, and four Supplier-page charts share the same title | Supplier page | Rename the measure and give each chart its own title |
| **Defect Rate** returns a count of defective units, not a rate | Measures Table | Rename it to *Defective Units*, or divide by `SUM(fact_production[quantity_produced])` |
| **Inventory Value** sums stock units, not money | Measures Table | Rename it to *Inventory Units*, or multiply by `dim_product[unit_cost]` |

## Tools

Power BI Desktop · Power Query · DAX · DAX Studio
