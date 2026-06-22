# End-to-End ETL Pipeline with Python, Pandas & SQLite | Data Engineering Project

As part of my **Data Engineering learning journey**, I built a complete ETL (Extract, Transform, Load) pipeline that takes raw sales CSV data and transforms it into actionable business insights — all the way from data extraction to a Power BI-style dashboard.

---

## 🛠️ Tech Stack

- **Python** — core scripting language
- **Pandas** — data manipulation and transformation
- **SQLite** — lightweight data warehousing
- **Power BI** — dashboard and KPI visualization
- **Google Colab** — development environment

---

## 🔄 Pipeline Overview

### Step 1 — Extract
Loaded raw sales data from a CSV file using Pandas for quick inspection:
```python
df = pd.read_csv("sales_data.csv")
print(df.shape)
print(df.info())
print(df.isnull().sum())
```

### Step 2 — Clean & Transform
Applied a series of data quality fixes:

**Drop duplicates**
```python
df = df.drop_duplicates()
```

**Handle missing values**
```python
df['quantity'] = df['quantity'].fillna(1)
df = df.dropna(subset=['customer_name'])
```

**Standardize date formats**
```python
df['order_date'] = pd.to_datetime(df['order_date'], errors='coerce')
```

**Fix data types**
```python
df['price'] = pd.to_numeric(df['price'], errors='coerce')
```

### Step 3 — Feature Engineering
Created new business metrics to enrich the dataset:

```python
# Revenue column
df['revenue'] = df['quantity'] * df['price']

# Extract month from order date
df['month'] = df['order_date'].dt.strftime('%B')

# Categorize orders by size
def size_label(qty):
    if qty <= 2:
        return 'Small'
    if qty <= 5:
        return 'Medium'
    return 'Large'

df['order_size'] = df['quantity'].apply(size_label)
```

### Step 4 — Load into SQLite Data Warehouse
```python
conn = sqlite3.connect('sales_dw.db')

df.to_sql(
    name='orders',
    con=conn,
    if_exists='replace',
    index=False
)
```

### Step 5 — SQL Analytics
Ran analytical queries directly on the warehouse:

```sql
SELECT region,
       SUM(revenue)  AS total_revenue,
       COUNT(*)      AS order_count,
       AVG(price)    AS avg_price
FROM   orders
GROUP  BY region
ORDER  BY total_revenue DESC
```

### Step 6 — Dashboard (Power BI)
Designed a Power BI-style dashboard to surface key business KPIs.

---

## 📊 Key Metrics & Insights

| Metric | Value | YoY Change |
|---|---|---|
| Total Revenue | $391.07K | ↑ 18.6% |
| Total Orders | 5,235 | ↑ 12.4% |
| Avg Order Value | $74.71 | ↑ 5.7% |
| Total Quantity | 12,045 | ↑ 9.3% |
| Unique Customers | 3,289 | ↑ 15.2% |

**Revenue by Region:**
- 🥇 North — $125.63K
- 🥈 West — $98.74K
- 🥉 South — $89.21K
- East — $77.49K

**Top Products by Revenue:**
1. Smartphone — $64,733.50
2. Laptop — $54,623.00
3. Headphones — $28,815.20
4. Smartwatch — $22,319.30
5. Tablet — $18,662.40

**Order Size Distribution:**
- Small: 2,349 (44.9%)
- Medium: 1,812 (34.6%)
- Large: 1,074 (20.5%)

---

## 💡 What I Learned

Working through this project gave me hands-on experience with:

- **ETL fundamentals** — how raw data flows from source to warehouse
- **Data quality** — real-world data is messy; cleaning is non-trivial
- **Feature engineering** — creating business-relevant metrics from raw fields
- **SQL analytics** — querying a warehouse to answer business questions
- **Dashboard design** — translating data into clear, actionable visuals

---

## 📁 Project Structure

```
data-engineering-etl/
├── Data_Engineering.ipynb   # Full ETL pipeline notebook
├── sales_data.csv           # Raw input data
├── sales_dw.db              # SQLite data warehouse (generated)
└── dashboard/               # Power BI dashboard assets
```

---

If you're also on a data engineering learning path, feel free to fork this and try it with your own dataset. Happy to connect and discuss! 🤝

`#DataEngineering` `#ETL` `#Python` `#Pandas` `#SQLite` `#PowerBI` `#DataScience` `#100DaysOfCode` `#LearningInPublic`
