# Customer Shopping Behavior Analysis

A complete end-to-end data analytics project — from raw data to an interactive dashboard — covering data cleaning, exploratory analysis, SQL-based business analysis, and dashboard/reporting deliverables.

---

## 📌 Overview

This project analyzes customer shopping behavior using transactional retail data. The goal is to uncover insights into spending patterns, customer segments, product preferences, and subscription behavior in order to support data-driven business decisions.

The workflow covers the full analytics pipeline:
**Python (EDA & Cleaning) → SQL (Business Analysis) → Power BI (Dashboard) → Report & Presentation**

---

## 🗂️ Dataset

- **Rows:** 3,900 transactions
- **Columns:** 18
- **Key features:**
  - Customer demographics (Age, Gender, Location, Subscription Status)
  - Purchase details (Item Purchased, Category, Purchase Amount, Season, Size, Color)
  - Shopping behavior (Discount Applied, Promo Code Used, Previous Purchases, Frequency of Purchases, Review Rating, Shipping Type)
- **Data quality note:** 37 missing values identified in the Review Rating column and handled during cleaning.

---

## 🛠️ Tools & Technologies

| Category | Tools |
|---|---|
| Programming | Python (pandas) |
| Database | PostgreSQL / MySQL / SQL Server |
| Visualization | Power BI |
| Reporting | Microsoft Word / PDF |
| Presentation | Gamma |
| Environment | Jupyter Notebook |

---

## 🔄 Project Steps

### 1. Data Loading & Exploration
- Imported the dataset using `pandas`
- Used `df.info()` and `df.describe()` to understand structure and summary statistics

### 2. Data Cleaning
- Identified and imputed missing values in the Review Rating column using the median rating per product category
- Standardized column names to snake_case for consistency
- Checked for redundant columns (e.g., verified overlap between Discount Applied and Promo Code Used) and removed duplicates

### 3. Feature Engineering
- Created an `age_group` column by binning customer ages into segments
- Created a `purchase_frequency_days` column to quantify purchase cadence numerically

### 4. Database Integration
- Connected the cleaned Python DataFrame to a SQL database (PostgreSQL / MySQL / SQL Server)
- Loaded the dataset into a relational table for structured querying

### 5. SQL Analysis
Performed business-focused SQL queries to answer key questions, including:
- Revenue by gender
- High-spending customers who used discounts
- Top-rated products
- Shipping type comparison
- Subscriber vs. non-subscriber spending behavior
- Discount-dependent products
- Customer segmentation (New, Returning, Loyal)
- Top products per category
- Repeat buyer and subscription correlation
- Revenue by age group

### 6. Dashboard Development
- Built an interactive Power BI dashboard to visualize key metrics and trends
- Included filters for subscription status, gender, category, and shipping type

### 7. Reporting & Presentation
- Compiled findings into a structured written report
- Created a summary presentation using Gamma for stakeholder communication

---

## 📊 Dashboard

The Power BI dashboard provides an interactive view of:
- Total customers, average purchase amount, and average review rating
- Revenue and sales breakdown by category
- Revenue and sales breakdown by age group
- Customer distribution by subscription status

*(Insert dashboard screenshot or Power BI link here)*

---

## 📈 Key Results

- Male customers generated significantly higher total revenue than female customers
- "Loyal" customers make up the majority of the customer base (~80%)
- Young Adults contributed the highest revenue among all age groups
- Certain products (e.g., Hat, Sneakers, Coat) show strong dependency on discounts to drive sales
- Non-subscribers generate a larger share of total revenue than subscribers, despite similar average spend

---

## 💡 Business Recommendations

- **Boost subscriptions** by promoting exclusive member benefits
- **Strengthen loyalty programs** to convert returning customers into loyal customers
- **Review discount strategy** to protect margins on discount-dependent products
- **Prioritize top-rated and best-selling products** in marketing campaigns
- **Target high-revenue age groups** with tailored marketing efforts

---

## ▶️ How to Run

### Prerequisites
- Python 3.x
- Jupyter Notebook (via Anaconda recommended)
- PostgreSQL / MySQL / SQL Server installed and running
- Power BI Desktop

### Setup

```bash
# Clone the repository
git clone <repository-url>
cd customer-shopping-behavior-analysis

# Install required Python packages
pip install pandas sqlalchemy psycopg2-binary
```

### Running the Analysis

1. Open the Jupyter notebook and run the data cleaning and EDA cells
2. Update your database connection credentials (username, password, host, port, database name)
3. Run the cell that loads the cleaned DataFrame into your SQL database
4. Execute the SQL queries provided in the `/sql` folder against your database
5. Open the Power BI file (`.pbix`) and refresh the data connection to your database
6. Review the generated report and presentation in the `/reports` folder

---

## 📁 Project Structure

```
├── data/
│   └── customer_shopping_behavior.csv
├── notebooks/
│   └── data_cleaning_eda.ipynb
├── sql/
│   └── business_queries.sql
├── dashboard/
│   └── customer_behavior_dashboard.pbix
├── reports/
│   ├── Customer_Shopping_Behavior_Analysis.pdf
│   └── Customer_Shopping_Behavior_Analysis.pptx
└── README.md
```

---

## 📬 Contact

For questions or collaboration opportunities, feel free to reach out.
