# 📊 Amazon Sales Data Analysis Project (End-to-End)

## 🔖 Project Overview

This project demonstrates an **end-to-end Data Analytics workflow** using Python libraries:
**NumPy, Pandas, Matplotlib, and Seaborn**.

You’ll start from loading raw CSV data (with deliberate errors), clean it step-by-step using Pandas, explore insights visually, and finally export the cleaned data.

---

## 🔗 Dataset

Download the sample dataset here:
[amazon_sales_dirty.csv](sandbox:/mnt/data/amazon_sales_dirty.csv)

The dataset includes intentional data-quality issues:

* Inconsistent date formats
* Text numbers (e.g., `₹1,299.00`)
* Missing values, duplicates, typos, and negative values
* Mixed discount formats (`0.1`, `10%`, etc.)

---

## 🔧 Python Libraries Required

```bash
pip install pandas numpy matplotlib seaborn openpyxl
```

---

## 📚 1. Import Libraries and Load Data

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Load CSV file
df = pd.read_csv("amazon_sales_dirty.csv")

# Preview data
print(df.head())
print("Shape:", df.shape)
print(df.info())
print(df.describe(include='all'))
```

---

## 🔎 2. Data Exploration & Auditing

```python
# Missing values
df.isnull().sum()

# Duplicates
df.duplicated().sum()

# Unique values per column
df.nunique()

# Data types
df.dtypes
```

---

## 🔧 3. Data Cleaning & Transformation

### Convert data types

```python
df['order_date'] = pd.to_datetime(df['order_date'], errors='coerce', dayfirst=True)
df['ship_date'] = pd.to_datetime(df['ship_date'], errors='coerce', dayfirst=True)

df['unit_price'] = pd.to_numeric(df['unit_price'].astype(str).str.replace(r'[\u20b9,]', '', regex=True), errors='coerce')
```

### Handle missing data

```python
df['customer_name'] = df['customer_name'].fillna('Unknown')
df['shipping_cost'] = pd.to_numeric(df['shipping_cost'].astype(str).str.replace(',', ''), errors='coerce').fillna(df['shipping_cost'].median())
```

### Remove duplicates

```python
df = df.drop_duplicates()
```

### Fix categorical/text inconsistencies

```python
df['country'] = df['country'].replace({'IND':'India','india':'India','Bharat':'India'})
df['order_status'] = df['order_status'].replace({'delivered':'Delivered','canceled':'Cancelled','RETURND':'Returned'})
df['customer_name'] = df['customer_name'].str.strip()
```

### Handle outliers

```python
df['quantity'] = df['quantity'].clip(lower=0)
```

---

## 📊 4. Feature Engineering

```python
df['net_sales'] = df['quantity'] * df['unit_price']
df['discount_clean'] = df['discount'].astype(str).str.replace('%','').astype(float)/100
df['final_sales'] = df['net_sales'] * (1 - df['discount_clean'])

df['order_month'] = df['order_date'].dt.to_period('M')
df['is_returned'] = np.where(df['order_status']=='Returned', 1, 0)
```

---

## 📊 5. Exploratory Data Analysis (EDA)

```python
# Summary by city
city_sales = df.groupby('city')['final_sales'].sum().sort_values(ascending=False)
print(city_sales)

# Average sales by category
category_sales = df.groupby('category')['final_sales'].mean()
print(category_sales)
```

---

## 🖉 6. Visualization with Matplotlib

```python
plt.figure(figsize=(10,6))
plt.bar(city_sales.index, city_sales.values)
plt.title('Total Sales by City')
plt.xlabel('City')
plt.ylabel('Sales (INR)')
plt.xticks(rotation=45)
plt.show()
```

---

## 🖉 7. Visualization with Seaborn

```python
sns.boxplot(x='category', y='final_sales', data=df)
plt.title('Sales Distribution by Category')
plt.xticks(rotation=45)
plt.show()

sns.heatmap(df.corr(numeric_only=True), annot=True, cmap='coolwarm')
plt.title('Correlation Heatmap')
plt.show()
```

---

## 🛠️ 8. Aggregation & Grouping

```python
# Total sales per month
df.groupby('order_month')['final_sales'].sum().plot(kind='line', figsize=(10,5), marker='o')
plt.title('Monthly Sales Trend')
plt.show()

# Profit summary by segment
segment_summary = df.groupby('segment').agg({'final_sales':'sum', 'profit':'mean', 'order_id':'nunique'})
print(segment_summary)
```

---

## 📄 9. Export Clean Data

```python
df.to_csv('amazon_sales_cleaned.csv', index=False)
df.to_excel('amazon_sales_cleaned.xlsx', index=False)
```

---

## 📌 10. Full Pandas Method Reference (Chronological)

* **Import & Load:** `read_csv`, `read_excel`
* **Explore:** `head`, `tail`, `sample`, `shape`, `columns`, `dtypes`, `info`, `describe`
* **Audit:** `isna`, `duplicated`, `nunique`, `value_counts`
* **Select:** `loc`, `iloc`, `at`, `iat`
* **Filter:** Boolean indexing, `query`
* **Type Conversion:** `to_datetime`, `to_numeric`, `astype`
* **Clean:** `fillna`, `dropna`, `replace`, `drop_duplicates`, `rename`, `reset_index`
* **String Ops:** `.str.lower()`, `.str.strip()`, `.str.replace()`
* **Aggregation:** `groupby`, `agg`, `transform`, `pivot_table`, `crosstab`
* **Join/Concat:** `merge`, `concat`, `melt`, `stack`, `unstack`
* **Dates:** `.dt.year`, `.dt.month`, `resample`, `rolling`, `shift`
* **Outliers:** `clip`, `where`
* **Export:** `to_csv`, `to_excel`, `ExcelWriter`

---

## 📚 11. Optional: One-Cell Data Audit Function

```python
def audit(df):
    return {
        'shape': df.shape,
        'columns': list(df.columns),
        'dtypes': df.dtypes.astype(str).to_dict(),
        'missing': df.isna().sum().to_dict(),
        'duplicates': int(df.duplicated().sum())
    }

print(audit(df))
```

---

## 📈 Expected Learning Outcomes

By the end of this project, students will:

* Understand Pandas DataFrame structure deeply.
* Clean real-world data with multiple issues.
* Perform descriptive and statistical analysis.
* Visualize results using Matplotlib & Seaborn.
* Export professional-quality cleaned datasets.

---

**Instructor:** Eshwari 
**Course:** Data Analytics with Python
**Topic:** Pandas + NumPy + Visualization End-to-End Project
