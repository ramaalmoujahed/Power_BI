# Data Dictionary

Five CSV files exported from the SAMSUNG supply chain model: one dimension and four fact tables. The data is synthetic and covers **1 January 2023 – 31 December 2024**.

The report also uses `dim_product`, `dim_customer`, `dim_facility`, `dim_date` and `fact_shipment`. Those tables are included in the `.pbix` file but are not exported here, so the key columns below refer to them:

| Key column | Refers to | Values |
|---|---|---|
| `date_key`, `order_date_key`, `delivery_date_key` | `dim_date` (one row per day) | 1–731 |
| `product_id` | `dim_product` (16 products: smartphones, tablets, TVs, appliances, wearables) | 1–16 |
| `customer_id` | `dim_customer` (Amazon, Best Buy, MediaMarkt Saturn, Flipkart, Samsung Direct Store) | 1–5 |
| `facility_id` | `dim_facility` (factories 1–3, distribution centres 4–6) | 1–6 |
| `supplier_id` | `dim_supplier` (included below) | 1–7 |

## Dimension

### `dim_supplier.csv` (7 rows)
| Column | Description |
|---|---|
| `supplier_id` | Primary key |
| `supplier_key`, `supplier_name` | e.g. SK Hynix Inc., BOE Technology |
| `country`, `city` | Location (South Korea, Taiwan, Japan, China, Vietnam, India) |
| `specialty` | What they supply, e.g. Processors, Display Panels |
| `tier` | Tier 1 (component makers) or Tier 2 (assembly) |
| `avg_quality_score` | Supplier quality score (0–100) |

## Facts

### `fact_sales.csv` (8,500 rows)
One row per sales order line. Keys: `date_key`, `product_id`, `customer_id`.
| Column | Description |
|---|---|
| `quantity_sold`, `unit_price` | Units and price |
| `discount_pct`, `discount_amount` | Discount applied |
| `gross_revenue`, `net_revenue` | Revenue before and after discount |
| `total_cost`, `profit`, `profit_margin_pct` | Cost and profitability |
| `order_number` | Unique order reference |

### `fact_procurement.csv` (2,200 rows)
One row per purchase order. Keys: `order_date_key`, `delivery_date_key`, `product_id`, `supplier_id`.
| Column | Description |
|---|---|
| `order_quantity`, `unit_cost`, `total_cost` | Order size and cost |
| `lead_time_days` | Days from order to delivery |
| `quality_score` | Quality of the delivered batch |
| `po_number` | Purchase order reference |

### `fact_production.csv` (4,500 rows)
One row per production batch at a factory (`facility_id` 1–3). Keys: `date_key`, `product_id`, `facility_id`.
| Column | Description |
|---|---|
| `quantity_produced`, `defective_units` | Output and defects |
| `defect_rate_pct` | Defective units ÷ quantity produced × 100 for the batch |
| `batch_number` | Batch reference |

### `fact_inventory.csv` (1,152 rows)
Stock snapshot per product at each distribution centre (`facility_id` 4–6). Keys: `date_key`, `product_id`, `facility_id`.
| Column | Description |
|---|---|
| `stock_level` | Units on hand |
| `safety_stock_level` | Minimum buffer stock |
| `reorder_point` | Level that triggers replenishment |
