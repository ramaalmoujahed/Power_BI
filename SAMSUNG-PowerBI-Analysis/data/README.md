# Data Dictionary

Ten CSV files form a star schema: five fact tables and five dimensions. The data is synthetic. Transactions run from **1 January 2023 to 31 December 2024**, and `dim_date` extends to 31 January 2025 so that purchase orders delivered in January 2025 have a matching date. Fact tables link to `dim_date` through `date_key` columns, not through date values.

Every key in every fact table matches a row in its dimension table.

## Dimensions

### `dim_date.csv` (762 rows)
| Column | Description |
|---|---|
| `date_key` | Surrogate key (1–762), referenced by every fact table |
| `date` | Calendar date |
| `year`, `quarter`, `month`, `month_name` | Calendar attributes |
| `week`, `day`, `day_of_week`, `day_name` | Week and day attributes (`day_of_week`: 0 = Monday) |
| `is_weekend` | 1 for Saturday and Sunday |

### `dim_product.csv` (16 rows)
| Column | Description |
|---|---|
| `product_id` | Primary key |
| `product_key` | Samsung model code, e.g. `SM-S928UZKAXAA` |
| `product_name` | e.g. Galaxy S24 Ultra, Neo QLED 85" 4K |
| `category` | Smartphone, Tablet, Television, Appliance, Wearable |
| `product_line` | e.g. Galaxy S, Galaxy Z, Galaxy Tab |
| `spec`, `color` | Variant details |
| `unit_price`, `unit_cost` | List price and unit cost (USD) |
| `weight_kg` | Unit weight |
| `Img` | Product image URL, used in the report. Empty for 4 products: OLED 65" 4K, Smart Front Load Washer, Galaxy Watch6 Classic, Galaxy Buds2 Pro. |

### `dim_supplier.csv` (7 rows)
| Column | Description |
|---|---|
| `supplier_id` | Primary key |
| `supplier_key`, `supplier_name` | e.g. SK Hynix Inc., BOE Technology |
| `country`, `city` | Location (South Korea, Taiwan, Japan, China, Vietnam, India) |
| `specialty` | What they supply, e.g. Processors, Display Panels |
| `tier` | Tier 1 (component makers) or Tier 2 (assembly) |
| `avg_quality_score` | Supplier quality score (0–100) |

### `dim_facility.csv` (6 rows)
| Column | Description |
|---|---|
| `facility_id` | Primary key |
| `facility_key`, `facility_name` | e.g. Gumi Manufacturing 1, Poland DC |
| `country`, `city` | Location |
| `facility_type` | Manufacturing (IDs 1–3) or Warehouse (IDs 4–6) |
| `specialization` | Product focus or region served |
| `annual_capacity` | Units per year |

### `dim_customer.csv` (5 rows)
| Column | Description |
|---|---|
| `customer_id` | Primary key |
| `customer_key`, `customer_name` | Amazon, Best Buy, MediaMarkt Saturn, Flipkart, Samsung Direct Store |
| `country` | Customer country |
| `channel_type` | Online, Retailer, Direct |
| `size` | Account size |
| `annual_volume_usd` | Annual purchase volume |

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

### `fact_shipment.csv` (7,500 rows)
One row per outbound shipment, sent from a distribution centre (about 81%) or directly from a factory (about 19%). Keys: `ship_date_key`, `delivery_date_key`, `product_id`, `facility_id`, `customer_id`.
| Column | Description |
|---|---|
| `quantity`, `total_weight_kg` | Shipment size |
| `carrier` | One of 9 carriers (Maersk Line, DHL Express, FedEx International, …) |
| `status` | Delivered (75%), In Transit (15%), Delayed (8%), Processing (2%) |
| `shipping_cost` | Shipment cost (USD) |
| `tracking_number` | Carrier tracking reference |
| `delay_reason` | Filled for delayed shipments, e.g. Carrier Capacity, Port Congestion |
