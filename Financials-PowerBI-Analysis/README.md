# Financials Analysis — Power BI

A Power BI report that tracks sales performance and profitability by product, market segment, country and discount band. It is built on a single `financials` sales table with the same structure as Microsoft's *Financial Sample* workbook. This is training data.

## Overview

The report has three analysis pages and one tooltip page. All of them share five slicers: **Date, Country, Segment, Product and Discount Band**.

| Page | Focus | Visuals |
|---|---|---|
| **Fin Report 1** | Where do sales and margin come from? | Multi-row KPI card (Total Sales, Total COGS, Total Margin, Profit %, Average Order); column, bar, pie and donut charts breaking sales down by segment, product, country and discount band |
| **Page 2** | How do sales and costs move over time? | KPI card, stacked area chart of sales over time, line chart of **Sales vs. Cost of Goods Sold** |
| **Fin Report 2** | Interactive deep dive | Six KPI cards, a **measure selector** that switches visuals between metrics, a filled **map** by country, four donut charts, a sales vs. COGS combo chart and a monthly area chart |
| **Report Tooltip** | Context on hover | Custom tooltip page with a KPI card and five **Sparkline by OKViz** visuals that show each KPI's trend over time |

## Business questions

- What are total sales, cost of goods sold, gross margin and profit %?
- Which **products** and **segments** (Government, Midmarket, Enterprise, Small Business, Channel Partners) drive the most revenue and margin?
- How does performance differ by **country**?
- How do **discount bands** (None, Low, Medium, High) affect sales and margin?
- How do sales and COGS move month to month, and is the gap between them growing?

## Data model

| Table | Purpose |
|---|---|
| `financials` | Fact table. One row per product, segment, country and month, with units sold, prices, gross sales, discounts, sales, COGS and profit. |
| `Date` | Calendar table used by the date slicer and the monthly trend charts |
| `All measures` | Measure table that holds every DAX measure |
| `Metrics` | Disconnected table that drives the measure selector on *Fin Report 2* |

## DAX measures

The exact formulas haven't been exported yet (see *To do*). The descriptions below are based on each measure's name and on how the report visuals use it.

| Measure | Purpose |
|---|---|
| Total Sales | Sum of sales after discounts |
| Total COGS | Sum of cost of goods sold |
| Total Margin | Total Sales − Total COGS |
| Profit % | Total Margin ÷ Total Sales |
| Average Order | Average sales value per transaction (named `Avarege Order` in the model) |
| Sales VS Cost Of Goods Sold | Comparison measure for the sales-vs-COGS trend charts |
| Measure Selected | Reads the metric chosen in the `Metrics` slicer |
| Measure Logic | `SWITCH` that returns the selected metric so one visual can show different KPIs |

## Dashboard design

- **Report-page tooltip:** hovering over a chart shows a small page with KPI values and sparkline trends for the selected item.
- **Dynamic measure switching:** a disconnected `Metrics` table plus `SWITCH` logic, so one set of visuals can show different KPIs.
- **Consistent filtering:** the same five slicers appear on every page.
- **Theme:** the *Accessible Tidal* built-in theme, with custom image icons in the slicer panel.

## Repository structure

```
Financials-PowerBI-Analysis/
├── Financials Analysis.pbix
└── README.md   (this file)
```

## How to use

1. Open `Financials Analysis.pbix` in **Power BI Desktop**. The data is already imported.
2. Use the Date, Country, Segment, Product and Discount Band slicers to filter each page.
3. On **Fin Report 2**, pick a metric in the measure selector to switch what the charts show.
4. Hover over chart points to see the sparkline tooltip.
5. The report uses the **Sparkline by OKViz** custom visual, which is embedded in the file.

## To do

This project has less documentation than the others in this repository. Still missing:

- [ ] `screenshots/`: one image per page
- [ ] `data/`: the source workbook (`financials` table)
- [ ] `measures/`: the exact DAX formulas, exported from DAX Studio or Tabular Editor
- [ ] Key insights with real numbers from the report
- [ ] Fix the typo in the measure name `Avarege Order`

## Tools

Power BI Desktop · Power Query · DAX · OKViz Sparkline custom visual
