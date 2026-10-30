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
- **Shipping costs total $2.95M** across 7,500 shipments. UPS Worldwide ($371K) and XPO Logistics ($356K) cost the most.
- **The overall defect rate is 0.63%** (24,102 defective out of 3.83M units produced). Defective units rise sharply in Q4 (October–December).

## Data model

A star schema with five fact tables sharing five dimensions, plus a dedicated `Measures Table`.

![Data model](screenshots/Screenshot%202026-07-22%20202613.png)

| Table | Type | Rows | Grain |
|---|---|---|---|
| `fact_sales` | Fact | 8,500 | One sales order line |
| `fact_shipment` | Fact | 7,500 | One shipment |
| `fact_production` | Fact | 4,500 | One production batch |
| `fact_procurement` | Fact | 2,200 | One purchase order |
| `fact_inventory` | Fact | 1,152 | Stock snapshot per product, warehouse and date |
| `dim_date` | Dimension | 762 | One day (2023-01-01 to 2025-01-31) |
| `dim_product` | Dimension | 16 | Product SKU (smartphones, tablets, TVs, appliances, wearables) |
| `dim_supplier` | Dimension | 7 | Component supplier |
| `dim_facility` | Dimension | 6 | 3 factories and 3 distribution centres |
| `dim_customer` | Dimension | 5 | Retail or online customer |

Every table is included in `data/` as a CSV. Column descriptions are in the [data dictionary](data/README.md).

## DAX measures

30 measures, grouped by area. Full formulas, number formats and descriptions are in [`measures/SAMSUNG_Measures.md`](measures/SAMSUNG_Measures.md).

| Area | Measures |
|---|---|
| Sales | Total Revenue, Profit, Profit Margin %, Discount, Discount %, Growth Revenue (YoY), Total Sales Quantity, Total Sales Cost (cost of goods sold) |
| Procurement | Order Quantity, Avg Lead Time, Total Unit Cost (procurement spend), Avg Unit Cost (per supplier), Avg Quality Score |
| Inventory | Inventory Value ($ at cost), Inventory Units, Safety Stock, Reorder Point, Turnover Rate, Days of Inventory. Stock measures use the latest snapshot in the current filter instead of summing months. |
| Production | Defect Rate (defective ÷ produced) |
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
│   ├── dim_customer.csv
│   ├── dim_date.csv
│   ├── dim_facility.csv
│   ├── dim_product.csv
│   ├── dim_supplier.csv
│   ├── fact_inventory.csv
│   ├── fact_procurement.csv
│   ├── fact_production.csv
│   ├── fact_sales.csv
│   └── fact_shipment.csv
├── measures/
│   └── SAMSUNG_Measures.md
└── screenshots/
    └── README.md               (page-by-page gallery)
```

## How to use

1. Open `SAMSUNG powerBI Analysis.pbix` in **Power BI Desktop**. All tables are already loaded in the file.
2. Start on the **Home** page and use the buttons to move between sections.
3. The CSVs in `data/` hold every table in the model, so you can inspect the data or rebuild the model without the `.pbix`.

## Known issues

Most formula problems from the previous version are fixed: Delivered %, delivered and delayed quantities, defect rate, inventory value, stock snapshots, turnover format, days of inventory, unit cost per supplier, sales cost and revenue growth. These items are still open:

| Issue | Where | Suggested fix |
|---|---|---|
| **Perfect Order %** filters on `fact_production[defect_rate_pct]`, but production isn't related to shipments, so that filter has no effect and the result equals the delivered share | Measures Table | Base it on shipment columns only, such as delivered with no delay reason |
| Five Supplier-page charts share the title **"Supplier by Avg Load Time"**, both Shipment-page bar charts are titled **"Total Delay Shipment"**, and one Customer-page title is cut off ("…Total_Revenue b") | Supplier, Shipment and Customer pages | Give each chart its own title |
| The measure name **Avg_Load_Time** should be *Avg Lead Time* | Measures Table | Rename it. Visuals update automatically. |
| Leftovers in the model: an empty measure named `Measure`, a `Folder` table from an old import, and `Defect Rate %`, which duplicates `Defect_Rate` | Model | Delete them |
| Auto date/time is on, and `dim_date` isn't marked as a date table, so Power BI adds hidden date tables | Model settings | Turn off *File → Options → Data Load → Auto date/time* and use *Mark as date table* on `dim_date` |
| Money measures have no currency format: Total Revenue, Profit, Discount, Shipment Cost, Total Sales Cost, Total Unit Cost | Measures Table | Set the format to Currency |
| `dim_date[Month_Short]` is only the month number, so monthly charts add January 2023 and January 2024 together | Monthly trend charts | Add a year-month column, or put Year on the axis or in a slicer |
| Four products have no image URL: OLED 65" 4K, Smart Front Load Washer, Galaxy Watch6 Classic, Galaxy Buds2 Pro | `dim_product[Img]` | Add the missing links |

## Tools

Power BI Desktop · Power Query · DAX · DAX Studio
