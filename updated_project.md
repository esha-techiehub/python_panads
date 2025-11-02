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

## 🧰 Python Libraries Required

```bash
pip install pandas numpy matplotlib seaborn openpyxl
```

---

## 📘 1. Import Libraries and Load Data

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Load CSV file
df = pd.read_csv("amazon_sales_dirty.csv")

# Preview data
print(df.head())           # Displays first 5 rows
print("Shape:", df.shape)   # Shows (rows, columns)
print(df.info())           # Data type & memory info
print(df.describe(include='all'))   # Statistical summary
```

### 💬 Meaning:

* `pd.read_csv()` loads the dataset into memory.
* `df.head()` helps you preview the data structure.
* `df.shape` gives total rows and columns.
* `df.info()` shows column datatypes and null counts.
* `df.describe()` summarizes numeric columns.

---

## 🔍 2. Data Exploration & Auditing

```python
# Missing values
df.isnull().sum()   # Count missing cells per column

# Duplicates
df.duplicated().sum()   # Count duplicate rows

# Unique values per column
df.nunique()   # Shows how many unique values in each column

# Data types
df.dtypes   # Displays data type of each column
```

### 💬 Meaning:

* `isnull()` and `sum()` check for missing data.
* `duplicated()` helps identify duplicate rows.
* `nunique()` shows variety in each column.
* `dtypes` ensures correct column types.

---

## 🧹 3. Data Cleaning & Transformation

### Convert data types

```python
df['order_date'] = pd.to_datetime(df['order_date'], errors='coerce', dayfirst=True)   # Convert to date format
df['ship_date'] = pd.to_datetime(df['ship_date'], errors='coerce', dayfirst=True)     # Convert to date format

df['unit_price'] = pd.to_numeric(df['unit_price'].astype(str).str.replace(r'[₹,]', '', regex=True), errors='coerce')   # Convert string price to number
```

### 💬 Meaning:

* Converts inconsistent date formats into standard datetime.
* Removes currency symbols (₹, commas) and converts text numbers to float.

### Handle missing data

```python
df['customer_name'] = df['customer_name'].fillna('Unknown')   # Fill missing names with 'Unknown'
df['shipping_cost'] = pd.to_numeric(df['shipping_cost'].astype(str).str.replace(',', ''), errors='coerce').fillna(df['shipping_cost'].median())   # Fill missing shipping cost with median
```

### 💬 Meaning:

* `.fillna()` replaces blanks or nulls with specified values.
* `pd.to_numeric()` converts text-based numeric columns to real numbers.

### Remove duplicates

```python
df = df.drop_duplicates()   # Removes duplicate rows
```

### 💬 Meaning:

* Ensures no repeated records.

### Fix categorical/text inconsistencies

```python
df['country'] = df['country'].replace({'IND':'India','india':'India','Bharat':'India'})   # Unify inconsistent country names
df['order_status'] = df['order_status'].replace({'delivered':'Delivered','canceled':'Cancelled','RETURND':'Returned'})   # Fix typo variations
df['customer_name'] = df['customer_name'].str.strip()   # Removes extra spaces
```

### 💬 Meaning:

* `.replace()` substitutes incorrect spellings.
* `.str.strip()` trims spaces.

### Handle outliers

```python
df['quantity'] = df['quantity'].clip(lower=0)   # Replace negative values with 0
```

### 💬 Meaning:

* `.clip()` enforces lower or upper bounds.

---

## ⚙️ 4. Feature Engineering

```python
df['net_sales'] = df['quantity'] * df['unit_price']   # Calculates total amount before discount
df['discount_clean'] = df['discount'].astype(str).str.replace('%','').astype(float)/100   # Converts discount strings into decimal

df['final_sales'] = df['net_sales'] * (1 - df['discount_clean'])   # Applies discount to get final sale value

df['order_month'] = df['order_date'].dt.to_period('M')   # Extracts month from order_date
df['is_returned'] = np.where(df['order_status']=='Returned', 1, 0)   # Creates flag 1 if returned, else 0
```

### 💬 Meaning:

* `df['quantity'] * df['unit_price']`: multiplies columns element-wise → new column of total sales.
* `.astype(str).str.replace('%','')`: removes % symbol.
* `.dt.to_period('M')`: extracts monthly period from datetime.
* `np.where()`: quick if-else to mark returned orders.

---

## 📊 5. Exploratory Data Analysis (EDA)

```python
city_sales = df.groupby('city')['final_sales'].sum().sort_values(ascending=False)   # Total sales per city
category_sales = df.groupby('category')['final_sales'].mean()   # Avg sales per category
```

### 💬 Meaning:

* `.groupby()` groups rows by a column.
* `.sum()` or `.mean()` performs aggregations.

---

## 📉 6. Visualization with Matplotlib

```python
plt.figure(figsize=(10,6))
plt.bar(city_sales.index, city_sales.values)
plt.title('Total Sales by City')
plt.xlabel('City')
plt.ylabel('Sales (INR)')
plt.xticks(rotation=45)
plt.show()
```

### 💬 Meaning:

* Creates bar chart using Matplotlib.
* `plt.figure()` defines canvas size.
* `plt.bar()` plots bars for cities.

---

## 📈 7. Visualization with Seaborn

```python
sns.boxplot(x='category', y='final_sales', data=df)   # Boxplot by category
plt.title('Sales Distribution by Category')
plt.xticks(rotation=45)
plt.show()

sns.heatmap(df.corr(numeric_only=True), annot=True, cmap='coolwarm')   # Correlation heatmap
plt.title('Correlation Heatmap')
plt.show()
```

### 💬 Meaning:

* `sns.boxplot()` shows value spread & outliers.
* `sns.heatmap()` shows numeric correlations visually.

---

## 🧮 8. Aggregation & Grouping

```python
df.groupby('order_month')['final_sales'].sum().plot(kind='line', figsize=(10,5), marker='o')   # Monthly trend
plt.title('Monthly Sales Trend')
plt.show()

segment_summary = df.groupby('segment').agg({'final_sales':'sum', 'profit':'mean', 'order_id':'nunique'})   # Aggregation by segment
print(segment_summary)
```

### 💬 Meaning:

* `.agg()` performs multiple aggregations together.
* `.plot(kind='line')` plots time trends.

---

## 📤 9. Export Clean Data

```python
df.to_csv('amazon_sales_cleaned.csv', index=False)   # Export cleaned data to CSV
df.to_excel('amazon_sales_cleaned.xlsx', index=False) # Export cleaned data to Excel
```

### 💬 Meaning:

* Saves final cleaned dataset for future use.

---

## 📚 10. Full Pandas Method Reference

* **Load:** `read_csv`, `read_excel`
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

## 🧩 11. One-Cell Data Audit Function

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

### 💬 Meaning:

* Quickly summarizes dataset quality.

---

## 🎯 Learning Outcomes

By the end of this project, students will:

* Understand Pandas DataFrame structure deeply.
* Clean and transform real-world messy data.
* Apply mathematical operations and create new columns.
* Visualize trends and insights effectively.
* Export polished, ready-to-analyze datasets.

---

**Instructor:** Eshwari (Binnbash Academy)
**Course:** Data Analytics with Python
**Topic:** Pandas + NumPy + Visualization End-to-End Project
