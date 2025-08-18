#  Pandas Project: Sales Data Analysis with Visualizations

This project demonstrates step-by-step Pandas commands for exploring,
cleaning, and analyzing an **Excel dataset** (`sales_data.xlsx`) and
visualizing results using **Matplotlib**.\
It is written in **chronological order** so that beginners can practice
in Jupyter Notebook.

------------------------------------------------------------------------

##  Step 1: Import Libraries

``` python
import pandas as pd
import matplotlib.pyplot as plt
```
# Create sample sales data
data = {
    "Date": pd.date_range(start="2023-01-01", periods=10, freq="M"),
    "Product": ["Laptop", "Tablet", "Phone", "Headphones", "Monitor",
                "Laptop", "Tablet", "Phone", "Headphones", "Monitor"],
    "Region": ["North", "South", "East", "West", "North",
               "South", "East", "West", "North", "South"],
    "Sales": [2000, 1500, 3000, 1200, 2500, 2200, 1600, 3100, 1300, 2700],
    "Quantity": [5, 3, 8, 2, 6, 5, 4, 9, 3, 7]
}

# Convert to DataFrame
df = pd.DataFrame(data)

# Save as Excel
df.to_excel("sales_data.xlsx", index=False)

print("Sample dataset created: sales_data.xlsx")
------------------------------------------------------------------------

##  Step 2: Load Excel File

``` python
df = pd.read_excel("sales_data.xlsx")
```

------------------------------------------------------------------------

##  Step 3: Preview Data

``` python
print(df.head())      # first 5 rows
print(df.tail())      # last 5 rows
print(df.shape)       # number of rows and columns
print(df.columns)     # column names
print(df.dtypes)      # data types of each column
print(df.info())      # full info (data types + non-null counts)
```

------------------------------------------------------------------------

##  Step 4: Summary Statistics

``` python
print(df.describe())       # summary for numeric columns
print(df["Category"].value_counts())   # count of unique values
print(df["Product"].unique())          # unique product names
```

------------------------------------------------------------------------

##  Step 5: Checking Missing Values

``` python
print(df.isnull().sum())   # count of missing values per column
print(df.isna().any())     # check if any column has missing values
```

## Handling missing data:

``` python
df.fillna(0, inplace=True)        # replace missing with 0
df.dropna(inplace=True)            # drop rows with missing values
```

------------------------------------------------------------------------

##  Step 6: Sorting Data

``` python
print(df.sort_values("TotalSales", ascending=False))   # sort by sales
print(df.sort_values(["Category","TotalSales"]))       # multi-column sort
```

------------------------------------------------------------------------

##  Step 7: Filtering Data

``` python
print(df[df["Quantity"] > 5])   # filter rows with Quantity > 5
print(df[df["Category"] == "Electronics"])  # only electronics
```

------------------------------------------------------------------------

##  Step 8: Adding New Columns

``` python
df["Discount"] = df["TotalSales"] * 0.1   # 10% discount column
df["NetSales"] = df["TotalSales"] - df["Discount"]
print(df.head())
```

------------------------------------------------------------------------

##  Step 9: Removing Columns

``` python
# Remove single column
df.drop("NetSales", axis=1, inplace=True)

# Remove multiple columns
df.drop(["NetSales", "Discount"], axis=1, inplace=True)
```

------------------------------------------------------------------------

##  Step 10: Grouping and Aggregation

``` python
print(df.groupby("Category")["TotalSales"].sum())    # sales by category
print(df.groupby("Product")["Quantity"].mean())      # avg quantity per product
```

------------------------------------------------------------------------

##  Step 11: Indexing & Selecting

``` python
print(df.loc[0])              # first row (label-based)
print(df.iloc[0])             # first row (index-based)
print(df.loc[0:3, ["Product","TotalSales"]])   # first 4 rows, selected columns
```

------------------------------------------------------------------------

##  Step 12: Date Handling

``` python
df["Date"] = pd.to_datetime(df["Date"])   # convert to datetime
print(df["Date"].dt.month)                # extract month
print(df["Date"].dt.day_name())           # extract day name
```

------------------------------------------------------------------------

##  Step 13: Exporting Data

``` python
df.to_excel("cleaned_sales.xlsx", index=False)   # save to Excel
df.to_csv("cleaned_sales.csv", index=False)      # save to CSV
```

 Files are saved in the **same folder where your Jupyter Notebook is
running**.\
Check with:

``` python
import os
print(os.getcwd())
```

------------------------------------------------------------------------

#  Business Questions, Solutions & Visualizations

## Q1. What is the total sales revenue?

``` python
total_revenue = df["TotalSales"].sum()
print("Total Sales Revenue:", total_revenue)

# Visualization
plt.bar(["Total Revenue"], [total_revenue])
plt.ylabel("Amount")
plt.title("Total Sales Revenue")
plt.show()
```

------------------------------------------------------------------------

## Q2. Which product has the highest total sales?

``` python
product_sales = df.groupby("Product")["TotalSales"].sum().sort_values(ascending=False)
print(product_sales)

# Visualization
product_sales.plot(kind="bar", figsize=(8,5))
plt.ylabel("Total Sales")
plt.title("Total Sales by Product")
plt.show()
```

------------------------------------------------------------------------

## Q3. What is the average sales per transaction by category?

``` python
avg_sales_category = df.groupby("Category")["TotalSales"].mean()
print(avg_sales_category)

# Visualization
avg_sales_category.plot(kind="bar", color=["skyblue","orange"])
plt.ylabel("Average Sales")
plt.title("Average Sales per Transaction (by Category)")
plt.show()
```

------------------------------------------------------------------------

## Q4. On which date was the highest sales recorded?

``` python
daily_sales = df.groupby("Date")["TotalSales"].sum()
print(daily_sales)

# Visualization
daily_sales.plot(kind="line", marker="o", figsize=(10,5))
plt.ylabel("Total Sales")
plt.title("Daily Sales Trend")
plt.xticks(rotation=45)
plt.show()
```

------------------------------------------------------------------------

## Q5. What is the total sales contribution by category?

``` python
category_sales = df.groupby("Category")["TotalSales"].sum()
contribution = (category_sales / category_sales.sum()) * 100
print(contribution)

# Visualization
category_sales.plot(kind="pie", autopct="%1.1f%%", figsize=(6,6))
plt.ylabel("")
plt.title("Sales Contribution by Category")
plt.show()
```

------------------------------------------------------------------------

#  Learning Outcome

By the end of this project, you will be comfortable with: - Loading &
exporting Excel/CSV files - Inspecting data - Handling missing values -
Sorting & filtering data - Adding & removing columns - Grouping &
aggregating data - Working with dates - Answering business questions
using Pandas - Visualizing results using Matplotlib
