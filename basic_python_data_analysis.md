
Perfect 🚀 — you’ve uploaded **sales\_data\_sample.csv** which we’ll use as the core dataset for your **project-based Python for Data Analysis code-along guide**. Since you want to start from **Module 1**, we’ll structure the guide progressively so you can build both Python and data analysis skills step by step.

We’ll use your uploaded dataset as the project backbone and create **modules** that gradually move from basics → intermediate → advanced analysis → visualization → mini-projects.

---

# 🧑‍💻 Python for Data Analysis — Code-Along Guide

*Project: Exploring and Analyzing Sales Data*

---

## ✅ Module 1: Getting Started with Data

**Goal**: Learn how to load, inspect, and explore tabular data.

### 1.1 Importing Libraries

We start by importing **pandas**, the most widely used Python library for data analysis.

```python
# Import pandas library
import pandas as pd
```

---

### 1.2 Loading CSV Data

We’ll write a function to load the sales data.

```python
def get_csv_data(file_name):
    print(f"Trying to read the file {file_name}")
    data = pd.read_csv(file_name)
    print("Data Read Successfully")
    return data

# Load the dataset
data = get_csv_data("/mnt/data/sales_data_sample.csv")
```

---

### 1.3 Exploring the Data Structure

Now let’s take a first look at the dataset.

```python
# View the column names
print("Columns in dataset:", data.columns.tolist())

# Find out the size of the dataset (rows, columns)
print("Shape of data (rows, cols):", data.shape)

# Preview the first 3 rows
print("First 3 rows of data:")
print(data.head(3))

# Summary statistics (numerical columns only)
print("Summary statistics:")
print(data.describe())
```

---

### 1.4 Accessing Specific Columns

We can extract one column at a time.

```python
# Example: COUNTRY column
country_data = data['COUNTRY']
print("Sample of COUNTRY column:")
print(country_data.head(10))
```

---

### 📝 Tasks for Practice

1. Print all the **columns** in the dataset.
2. Print the **statistical averages** (`.describe()`) of the dataset.
3. Print all values in the **PRICEEACH** column.

---

👉 That completes **Module 1**. You’ve now loaded and explored real-world sales data.

---

⚡ Question for you before I draft **Module 2**:
Do you want me to **continue with the guide module by module** (so you code along after each one), or should I generate the **entire structured guide (all modules)** at once for you?

Great 👍 — let’s move to **Module 2**.
Here we’ll focus on **Filtering and Aggregating Data**, which are core skills for any data analyst.

---

# 🧑‍💻 Module 2: Filtering and Aggregating Data

**Goal**: Learn how to filter rows, apply conditions, and summarize grouped data.

---

### 2.1 Filtering Data with Conditions

We can use `.query()` or boolean indexing to filter rows.

```python
import pandas as pd

# Load the dataset again for a fresh start
data = pd.read_csv("/mnt/data/sales_data_sample.csv")

# 1. Filter rows where PRICEEACH < 100
filtered_price = data.query("PRICEEACH < 100")
print("Top 3 rows where PRICEEACH < 100")
print(filtered_price.head(3))

# 2. Filter rows where QUANTITYORDERED > 50
filtered_qty = data.query("QUANTITYORDERED > 50")
print("Top 3 rows where QUANTITYORDERED > 50")
print(filtered_qty.head(3))

# 3. Filter rows where COUNTRY is France
filtered_fr = data.query("COUNTRY == 'France'")
print("Top 3 rows where COUNTRY == 'France'")
print(filtered_fr.head(3))
```

---

### 2.2 Aggregating Data with `.sum()`

Now let’s calculate totals for specific subsets.

```python
# Total quantity ordered by France
total_qty_fr = filtered_fr['QUANTITYORDERED'].sum()
print("Total quantity ordered by France:", total_qty_fr)
```

---

### 2.3 Grouping and Aggregating

We often need to group by categories (e.g., country, product line).

```python
# Group data by COUNTRY and sum the QUANTITYORDERED
grouped_data = data.groupby("COUNTRY").agg(
    Total_Quantity=("QUANTITYORDERED", "sum")
)

print("Aggregated quantities ordered by country:")
print(grouped_data.head())
```

---

### 📝 Tasks for Practice

1. Filter the data where `COUNTRY == 'USA'` and print the **top 3 rows**.
2. (Advanced) Group the data by `QUANTITYORDERED` and calculate the **aggregate sum of SALES** for each quantity ordered.

*Hint*: Use `.groupby("QUANTITYORDERED")["SALES"].sum()`

---

👉 That wraps up **Module 2**. You now know how to **filter rows** and **aggregate grouped data**.

Would you like me to continue to **Module 3: Data Visualization** (where we’ll build bar charts and pie charts with matplotlib/pandas plotting)?

Perfect 👌 — let’s move to **Module 3**, where we’ll bring our data to life using **visualizations**.
Visuals make it much easier to understand trends and patterns in sales.

---

# 🧑‍💻 Module 3: Data Visualization with Pandas & Matplotlib

**Goal**: Learn how to create bar charts, pie charts, and explore trends visually.

---

### 3.1 Setup: Import Libraries for Visualization

We’ll use **matplotlib** along with pandas’ built-in `.plot()` functionality.

```python
import pandas as pd
import matplotlib.pyplot as plt   # main plotting library

# Load dataset
data = pd.read_csv("/mnt/data/sales_data_sample.csv")
```

---

### 3.2 Bar Chart: Sales by Product Line

Bar charts are useful for comparing totals across categories.

```python
# Group by PRODUCTLINE and sum SALES
sales_by_product = data.groupby("PRODUCTLINE")["SALES"].sum()

# Plot bar chart
sales_by_product.plot(
    kind="barh",               # horizontal bar
    title="Total Sales by Product Line",
    figsize=(8, 5)
)

plt.xlabel("Total Sales")
plt.ylabel("Product Line")
plt.show()
```

---

### 3.3 Pie Chart: Sales by Country (Top 6)

Pie charts help show proportions, especially for top categories.

```python
# Group by COUNTRY and sum SALES
sales_by_country = data.groupby("COUNTRY")["SALES"].sum()

# Get top 6 countries by sales
top6_countries = sales_by_country.sort_values(ascending=False).head(6)

# Plot pie chart
top6_countries.plot(
    kind="pie",
    autopct="%1.1f%%",         # show percentages
    title="Sales by Country (Top 6)",
    figsize=(6, 6)
)

plt.ylabel("")  # remove default label
plt.show()
```

---

### 3.4 Line Plot: Sales Trend by Month

If the dataset has a `MONTH_ID` column, we can explore trends.

```python
# Group by MONTH_ID and sum SALES
monthly_sales = data.groupby("MONTH_ID")["SALES"].sum()

# Plot line chart
monthly_sales.plot(
    kind="line",
    marker="o",
    title="Monthly Sales Trend",
    figsize=(8, 5)
)

plt.xlabel("Month")
plt.ylabel("Total Sales")
plt.show()
```

---

### 📝 Tasks for Practice

1. Create a **bar chart** showing total `QUANTITYORDERED` by `PRODUCTLINE`.
2. Create a **pie chart** of sales by **DEALSIZE**.
3. Create a **line plot** of sales grouped by `YEAR_ID`.

---

👉 That completes **Module 3**. You now know how to create **bar charts, pie charts, and line charts** directly from your data.

Do you want me to continue with **Module 4: Data Cleaning & Transformation** (handling missing values, changing column formats, creating new calculated columns)?

Excellent 🙌 — let’s move on to **Module 4**, which is all about **Data Cleaning & Transformation**.
In real-world data analysis, cleaning and reshaping data often takes more time than the actual analysis.

---

# 🧑‍💻 Module 4: Data Cleaning & Transformation

**Goal**: Learn how to handle missing values, change data types, rename columns, and create new calculated fields.

---

### 4.1 Checking for Missing Values

It’s important to check whether any columns contain null (missing) values.

```python
import pandas as pd

# Load dataset
data = pd.read_csv("/mnt/data/sales_data_sample.csv")

# Check for missing values in each column
print("Missing values per column:")
print(data.isnull().sum())
```

---

### 4.2 Handling Missing Data

There are different strategies:

* Drop rows/columns
* Fill missing values with a constant or mean/median

```python
# Drop rows with any missing values (use with caution!)
cleaned_data = data.dropna()

# Fill missing numeric values with 0
data_filled = data.fillna(0)

# Fill missing categorical values with "Unknown"
data_filled = data.fillna({"COUNTRY": "Unknown"})
```

---

### 4.3 Renaming Columns

Sometimes column names are too long or inconsistent.

```python
# Rename a single column
data = data.rename(columns={"QUANTITYORDERED": "QTY_ORDERED"})

# Rename multiple columns
data = data.rename(columns={
    "PRICEEACH": "UNIT_PRICE",
    "ORDERNUMBER": "ORDER_NO"
})

print("Renamed columns:", data.columns.tolist())
```

---

### 4.4 Changing Data Types

Ensure numeric columns are numbers, dates are dates, etc.

```python
# Convert ORDERDATE to datetime
data["ORDERDATE"] = pd.to_datetime(data["ORDERDATE"])

# Convert QUANTITYORDERED to integer (if not already)
data["QTY_ORDERED"] = data["QTY_ORDERED"].astype(int)

# Convert UNIT_PRICE to float
data["UNIT_PRICE"] = data["UNIT_PRICE"].astype(float)
```

---

### 4.5 Creating New Columns (Feature Engineering)

We can add calculated fields to enrich analysis.

```python
# Create a new column: Total Revenue (Quantity * Unit Price)
data["TOTAL_REVENUE"] = data["QTY_ORDERED"] * data["UNIT_PRICE"]

# Create a new column: Profit Margin (assume 30% margin on SALES)
data["PROFIT"] = data["SALES"] * 0.30

# Extract Year and Month from ORDERDATE
data["ORDER_YEAR"] = data["ORDERDATE"].dt.year
data["ORDER_MONTH"] = data["ORDERDATE"].dt.month

print(data[["ORDERDATE", "ORDER_YEAR", "ORDER_MONTH", "TOTAL_REVENUE", "PROFIT"]].head(5))
```

---

### 📝 Tasks for Practice

1. Check if there are missing values in the **DEALSIZE** column. If yes, fill them with `"Unknown"`.
2. Rename the column `SALES` to `TOTAL_SALES`.
3. Convert the column `ORDERDATE` into datetime format and extract the **day of the week** into a new column.
4. Create a new column `DISCOUNTED_SALES` where sales are reduced by 10%.

---

👉 That completes **Module 4**. You now know how to **clean, transform, and create new features** from your dataset.

Would you like me to continue with **Module 5: Exploratory Data Analysis (EDA)** (finding insights, correlations, and patterns in the data)?

Awesome 🚀 — let’s dive into **Module 5**, where we’ll go beyond cleaning and start exploring the dataset for **patterns, trends, and relationships**. This stage is often called **Exploratory Data Analysis (EDA)**.

---

# 🧑‍💻 Module 5: Exploratory Data Analysis (EDA)

**Goal**: Learn how to explore relationships between variables, detect patterns, and summarize insights.

---

### 5.1 Distribution of a Numeric Variable

We’ll explore how numerical values are distributed.

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load cleaned dataset
data = pd.read_csv("/mnt/data/sales_data_sample.csv")

# Histogram of SALES
data["SALES"].plot(
    kind="hist",
    bins=30,
    title="Distribution of Sales",
    figsize=(8, 5),
    edgecolor="black"
)

plt.xlabel("Sales Amount")
plt.ylabel("Frequency")
plt.show()
```

---

### 5.2 Relationship Between Two Variables (Scatter Plot)

Scatter plots help reveal relationships.

```python
# Scatter plot: Unit Price vs Quantity Ordered
data.plot(
    kind="scatter",
    x="PRICEEACH",
    y="QUANTITYORDERED",
    title="Unit Price vs Quantity Ordered",
    figsize=(8, 5)
)
plt.show()
```

---

### 5.3 Correlation Analysis

Correlation measures strength and direction of relationships.

```python
# Compute correlation matrix (numeric columns only)
correlation = data.corr(numeric_only=True)

print("Correlation matrix:")
print(correlation)

# Heatmap-style visualization (basic)
import seaborn as sns
plt.figure(figsize=(8,6))
sns.heatmap(correlation, annot=True, cmap="coolwarm")
plt.title("Correlation Heatmap")
plt.show()
```

---

### 5.4 Categorical Analysis (Value Counts)

For categorical variables like COUNTRY or DEALSIZE, check frequencies.

```python
# Frequency of DEALSIZE categories
print("Deal Size distribution:")
print(data["DEALSIZE"].value_counts())

# Bar chart of deal sizes
data["DEALSIZE"].value_counts().plot(
    kind="bar",
    title="Count of Deal Sizes",
    figsize=(6,4)
)
plt.xlabel("Deal Size")
plt.ylabel("Count")
plt.show()
```

---

### 5.5 Sales Insights by Groups

Group by and find averages/totals to uncover business insights.

```python
# Average sales per product line
avg_sales = data.groupby("PRODUCTLINE")["SALES"].mean()
print("Average Sales by Product Line:")
print(avg_sales)

# Plot as bar chart
avg_sales.plot(
    kind="bar",
    title="Average Sales by Product Line",
    figsize=(8,5)
)
plt.ylabel("Average Sales")
plt.show()
```

---

### 📝 Tasks for Practice

1. Create a histogram of the `QUANTITYORDERED` column.
2. Make a scatter plot of `SALES` vs `QUANTITYORDERED`.
3. Find and plot the **average SALES by COUNTRY**.
4. Check which column has the **strongest correlation** with `SALES`.

---

👉 That wraps up **Module 5**. You now know how to **visualize distributions, relationships, correlations, and group patterns** — the backbone of exploratory analysis.

Do you want me to continue with **Module 6: Advanced Analysis (Pivot Tables, Multi-Level Grouping, Time Series)** next?

Perfect ⚡ — let’s step into **Module 6**, where we’ll cover some **advanced analysis techniques**. These include **pivot tables, multi-level grouping, and time series analysis** — essential tools for summarizing and slicing data like a pro.

---

# 🧑‍💻 Module 6: Advanced Data Analysis

**Goal**: Learn how to summarize data using pivot tables, perform multi-level grouping, and explore time series trends.

---

### 6.1 Pivot Tables in Pandas

Pivot tables let you reshape and summarize data quickly.

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load dataset
data = pd.read_csv("/mnt/data/sales_data_sample.csv")

# Pivot: Total SALES by COUNTRY and PRODUCTLINE
pivot_table = pd.pivot_table(
    data,
    values="SALES",
    index="COUNTRY",       # rows
    columns="PRODUCTLINE", # columns
    aggfunc="sum",         # aggregation
    fill_value=0
)

print("Pivot table: Total SALES by Country and Product Line")
print(pivot_table.head())
```

---

### 6.2 Multi-Level Grouping

We can group by multiple columns to get detailed summaries.

```python
# Group by COUNTRY and DEALSIZE, sum SALES
multi_group = data.groupby(["COUNTRY", "DEALSIZE"])["SALES"].sum()

print("Multi-level grouped sales:")
print(multi_group.head(10))
```

---

### 6.3 Aggregating Multiple Metrics at Once

You can calculate more than one metric in a single `.agg()` call.

```python
# Group by PRODUCTLINE and calculate multiple metrics
metrics = data.groupby("PRODUCTLINE").agg(
    Total_Sales=("SALES", "sum"),
    Avg_Price=("PRICEEACH", "mean"),
    Total_Quantity=("QUANTITYORDERED", "sum")
)

print("Multiple aggregations per Product Line:")
print(metrics)
```

---

### 6.4 Time Series Analysis

If the dataset has **ORDERDATE**, we can look at sales trends over time.

```python
# Convert ORDERDATE to datetime
data["ORDERDATE"] = pd.to_datetime(data["ORDERDATE"])

# Group sales by month
monthly_sales = data.groupby(data["ORDERDATE"].dt.to_period("M"))["SALES"].sum()

print("Monthly sales:")
print(monthly_sales.head())

# Plot monthly sales
monthly_sales.plot(
    kind="line",
    marker="o",
    title="Monthly Sales Trend",
    figsize=(10, 5)
)

plt.xlabel("Month")
plt.ylabel("Total Sales")
plt.show()
```

---

### 6.5 Ranking & Sorting

Ranking helps find top-performing products, countries, or customers.

```python
# Top 5 countries by total sales
top_countries = data.groupby("COUNTRY")["SALES"].sum().sort_values(ascending=False).head(5)
print("Top 5 countries by sales:")
print(top_countries)

# Top 5 products by total revenue
top_products = data.groupby("PRODUCTCODE")["SALES"].sum().sort_values(ascending=False).head(5)
print("Top 5 products by revenue:")
print(top_products)
```

---

### 📝 Tasks for Practice

1. Create a **pivot table** showing total `SALES` by `YEAR_ID` and `PRODUCTLINE`.
2. Group by `COUNTRY` and `YEAR_ID`, then find the **average SALES**.
3. Create a time series plot of **monthly quantity ordered (`QUANTITYORDERED`)**.
4. Find the **top 10 customers** by total `SALES`.

---

👉 That completes **Module 6**. You now know how to use **pivot tables, multi-level groupings, advanced aggregations, and time series analysis** — essential skills for real-world analytics.

Would you like me to continue with **Module 7: Data Visualization (Advanced Dashboards with Matplotlib & Seaborn)** next?

Awesome 🎯 — let’s level up with **Module 7**, where we’ll move from basic plots into **advanced visualizations and dashboards**. These tools make it easier to communicate insights effectively.

---

# 🧑‍💻 Module 7: Advanced Data Visualization

**Goal**: Learn how to create professional, multi-plot dashboards using **Matplotlib** and **Seaborn**.

---

### 7.1 Setting Up for Visualization

We’ll use both **matplotlib** and **seaborn** for richer visuals.

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load dataset
data = pd.read_csv("/mnt/data/sales_data_sample.csv")

# Convert ORDERDATE to datetime for time-based plots
data["ORDERDATE"] = pd.to_datetime(data["ORDERDATE"])
```

---

### 7.2 Barplot with Seaborn (Total Sales by Country)

Seaborn automatically adds nice styling.

```python
# Total sales by country (top 10)
country_sales = data.groupby("COUNTRY")["SALES"].sum().sort_values(ascending=False).head(10)

plt.figure(figsize=(10,6))
sns.barplot(x=country_sales.values, y=country_sales.index, palette="Blues_d")
plt.title("Top 10 Countries by Sales")
plt.xlabel("Total Sales")
plt.ylabel("Country")
plt.show()
```

---

### 7.3 Heatmap (Sales by Year & Product Line)

Pivot tables + heatmaps = powerful!

```python
# Pivot: Total SALES by Year and Product Line
pivot_sales = pd.pivot_table(
    data,
    values="SALES",
    index="PRODUCTLINE",
    columns="YEAR_ID",
    aggfunc="sum",
    fill_value=0
)

plt.figure(figsize=(8,6))
sns.heatmap(pivot_sales, annot=True, fmt=".0f", cmap="YlGnBu")
plt.title("Sales Heatmap by Product Line & Year")
plt.show()
```

---

### 7.4 Line Plot with Multiple Categories (Monthly Sales by Year)

Let’s explore trends across years.

```python
# Extract year-month
data["YearMonth"] = data["ORDERDATE"].dt.to_period("M")

# Group sales by YearMonth and Year
monthly_sales = data.groupby(["YearMonth", "YEAR_ID"])["SALES"].sum().reset_index()

# Convert YearMonth to string for plotting
monthly_sales["YearMonth"] = monthly_sales["YearMonth"].astype(str)

plt.figure(figsize=(12,6))
sns.lineplot(data=monthly_sales, x="YearMonth", y="SALES", hue="YEAR_ID", marker="o")
plt.xticks(rotation=45)
plt.title("Monthly Sales Trend by Year")
plt.xlabel("Year-Month")
plt.ylabel("Total Sales")
plt.show()
```

---

### 7.5 Mini Dashboard with Subplots

We can combine multiple charts into one dashboard.

```python
fig, axes = plt.subplots(2, 2, figsize=(14,10))

# 1. Sales by product line
data.groupby("PRODUCTLINE")["SALES"].sum().plot(
    kind="bar", ax=axes[0,0], color="skyblue", title="Sales by Product Line"
)

# 2. Sales by top 6 countries (pie)
data.groupby("COUNTRY")["SALES"].sum().sort_values(ascending=False).head(6).plot(
    kind="pie", autopct="%1.1f%%", ax=axes[0,1], title="Top 6 Countries by Sales"
)

# 3. Monthly sales trend
data.groupby(data["ORDERDATE"].dt.to_period("M"))["SALES"].sum().plot(
    kind="line", marker="o", ax=axes[1,0], title="Monthly Sales Trend"
)

# 4. Deal size distribution
data["DEALSIZE"].value_counts().plot(
    kind="bar", ax=axes[1,1], color="orange", title="Deal Size Distribution"
)

plt.tight_layout()
plt.show()
```

---

### 📝 Tasks for Practice

1. Create a **bar plot** of `QUANTITYORDERED` by `PRODUCTLINE`.
2. Build a **heatmap** of average `SALES` by `DEALSIZE` and `PRODUCTLINE`.
3. Make a **line plot** showing sales trends for the top 3 countries over time.
4. Create your own **dashboard with 3–4 subplots** showing different business insights.

---

👉 That completes **Module 7**. You now know how to build **professional visualizations and mini dashboards** with Matplotlib & Seaborn.

Would you like me to continue with **Module 8: Statistical Analysis & Hypothesis Testing** (finding patterns, testing assumptions, making data-driven decisions)?

Fantastic 💡 — let’s dive into **Module 8**, where we’ll use Python for **Statistical Analysis & Hypothesis Testing**.
This is where we start validating business insights with **data-driven evidence**, not just visual trends.

---

# 🧑‍💻 Module 8: Statistical Analysis & Hypothesis Testing

**Goal**: Learn how to compute statistical measures, detect differences between groups, and apply hypothesis testing with Python.

---

### 8.1 Descriptive Statistics Recap

We’ve already used `.describe()`, but let’s go deeper with additional stats.

```python
import pandas as pd
import numpy as np
from scipy import stats

# Load dataset
data = pd.read_csv("/mnt/data/sales_data_sample.csv")

# Mean, median, mode of SALES
mean_sales = data["SALES"].mean()
median_sales = data["SALES"].median()
mode_sales = data["SALES"].mode()[0]

print(f"Mean Sales: {mean_sales:.2f}")
print(f"Median Sales: {median_sales:.2f}")
print(f"Mode Sales: {mode_sales:.2f}")

# Standard deviation and variance
std_sales = data["SALES"].std()
var_sales = data["SALES"].var()
print(f"Standard Deviation: {std_sales:.2f}, Variance: {var_sales:.2f}")
```

---

### 8.2 Distribution Fitting (Normality Check)

We can test whether sales are normally distributed (important for choosing the right statistical test).

```python
# Shapiro-Wilk test for normality
stat, p = stats.shapiro(data["SALES"].sample(500))  # sample to avoid large data issues
print(f"Shapiro-Wilk Test p-value: {p}")

if p > 0.05:
    print("Sales distribution looks normal")
else:
    print("Sales distribution is NOT normal")
```

---

### 8.3 Comparing Groups (t-test)

Let’s test if **average sales differ** between two deal sizes (e.g., "Small" vs "Medium").

```python
# Separate sales by deal size
sales_small = data[data["DEALSIZE"] == "Small"]["SALES"]
sales_medium = data[data["DEALSIZE"] == "Medium"]["SALES"]

# Independent t-test
t_stat, p_val = stats.ttest_ind(sales_small, sales_medium, equal_var=False)
print(f"T-test p-value: {p_val}")

if p_val < 0.05:
    print("There is a significant difference between Small and Medium deal sales")
else:
    print("No significant difference between Small and Medium deal sales")
```

---

### 8.4 ANOVA (Comparing More Than 2 Groups)

If we want to compare **all deal sizes at once**.

```python
sales_small = data[data["DEALSIZE"] == "Small"]["SALES"]
sales_medium = data[data["DEALSIZE"] == "Medium"]["SALES"]
sales_large = data[data["DEALSIZE"] == "Large"]["SALES"]

f_stat, p_val = stats.f_oneway(sales_small, sales_medium, sales_large)
print(f"ANOVA p-value: {p_val}")

if p_val < 0.05:
    print("Significant differences exist between deal size groups")
else:
    print("No significant differences between deal size groups")
```

---

### 8.5 Correlation Significance

We can test whether correlation between `QUANTITYORDERED` and `SALES` is statistically significant.

```python
corr, p_val = stats.pearsonr(data["QUANTITYORDERED"], data["SALES"])
print(f"Correlation: {corr:.2f}, p-value: {p_val}")

if p_val < 0.05:
    print("Correlation is statistically significant")
else:
    print("Correlation is not significant")
```

---

### 📝 Tasks for Practice

1. Test whether **average sales** differ between **USA** and **France** using a **t-test**.
2. Use **ANOVA** to test whether sales differ significantly across different **PRODUCTLINEs**.
3. Check if correlation between `PRICEEACH` and `SALES` is statistically significant.
4. Interpret results: What do they mean for business decisions?

---

👉 That wraps up **Module 8**. You now know how to perform **statistical analysis, hypothesis testing, and interpret results** — critical for data-driven decision-making.

Do you want me to continue with **Module 9: Machine Learning for Data Analysis (Regression & Predictions)** next?

Great choice 🚀 — let’s step into **Module 9**, where we’ll introduce **Machine Learning for Data Analysis**.
We’ll focus on **regression and prediction models** that can forecast sales or uncover what drives revenue.

---

# 🧑‍💻 Module 9: Machine Learning for Data Analysis (Regression & Predictions)

**Goal**: Learn how to prepare data for machine learning, build simple regression models, and make predictions.

---

### 9.1 Setup: Import ML Libraries

We’ll use **scikit-learn** for modeling.

```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score

# Load dataset
data = pd.read_csv("/mnt/data/sales_data_sample.csv")
```

---

### 9.2 Feature Selection & Cleaning

For ML, we select relevant features (independent variables) and a target (dependent variable).

```python
# Choose features (independent variables)
X = data[["QUANTITYORDERED", "PRICEEACH"]]  # features
y = data["SALES"]                           # target

print("Shape of X:", X.shape)
print("Shape of y:", y.shape)
```

---

### 9.3 Train-Test Split

Split data into training (to learn) and testing (to evaluate).

```python
# Split data into train (80%) and test (20%)
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

print("Training set:", X_train.shape)
print("Testing set:", X_test.shape)
```

---

### 9.4 Train a Linear Regression Model

We’ll predict SALES from QUANTITYORDERED and PRICEEACH.

```python
# Initialize model
model = LinearRegression()

# Train model
model.fit(X_train, y_train)

# Coefficients
print("Model coefficients:", model.coef_)
print("Model intercept:", model.intercept_)
```

---

### 9.5 Make Predictions & Evaluate

Now test the model on unseen data.

```python
# Predict on test set
y_pred = model.predict(X_test)

# Evaluation metrics
mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)

print(f"Mean Squared Error: {mse:.2f}")
print(f"R² Score: {r2:.2f}")  # closer to 1 = better fit
```

---

### 9.6 Visualizing Predictions

Compare predicted vs actual values.

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(8,6))
plt.scatter(y_test, y_pred, alpha=0.6, color="blue")
plt.plot([y.min(), y.max()], [y.min(), y.max()], "r--")  # reference line
plt.xlabel("Actual Sales")
plt.ylabel("Predicted Sales")
plt.title("Actual vs Predicted Sales")
plt.show()
```

---

### 9.7 Extending the Model (Advanced)

We can add categorical features (like COUNTRY or PRODUCTLINE) by encoding them.

```python
from sklearn.preprocessing import OneHotEncoder
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline

# Features: numeric + categorical
X = data[["QUANTITYORDERED", "PRICEEACH", "COUNTRY"]]
y = data["SALES"]

# Preprocess categorical feature
preprocessor = ColumnTransformer(
    transformers=[("cat", OneHotEncoder(handle_unknown="ignore"), ["COUNTRY"])],
    remainder="passthrough"
)

# Build pipeline: preprocessing + model
pipeline = Pipeline(steps=[("preprocessor", preprocessor),
                           ("model", LinearRegression())])

# Train-test split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train
pipeline.fit(X_train, y_train)

# Evaluate
y_pred = pipeline.predict(X_test)
print("R² Score with COUNTRY included:", r2_score(y_test, y_pred))
```

---

### 📝 Tasks for Practice

1. Build a regression model predicting `SALES` using `QUANTITYORDERED`, `PRICEEACH`, and `YEAR_ID`.
2. Evaluate the model using **R²** and **Mean Squared Error**.
3. Plot actual vs predicted sales values.
4. (Advanced) Use `PRODUCTLINE` and `COUNTRY` as categorical features to see if model performance improves.

---

👉 That wraps up **Module 9**. You now know how to **build, train, and evaluate regression models** for prediction.

Do you want me to continue with **Module 10: Building a Mini End-to-End Project (Combining Everything)** where we’ll tie all the modules into a real analysis workflow?

Perfect timing 🎯 — let’s wrap up everything into a **Mini End-to-End Project**.
Module 10 will be like a **capstone** where you combine all the skills (cleaning, exploration, visualization, grouping, and prediction) into a **real analysis workflow**.

---

# 🧑‍💻 Module 10: End-to-End Data Analysis Project

**Goal**: Use the `sales_data_sample.csv` dataset to build an analysis and prediction pipeline from raw data → insights → visualization → machine learning.

---

## 10.1 Load & Inspect Data

```python
import pandas as pd

# Load dataset
data = pd.read_csv("/mnt/data/sales_data_sample.csv")

# Quick inspection
print("Shape:", data.shape)
print("Columns:", data.columns.tolist())
print(data.head(3))
```

---

## 10.2 Data Cleaning

* Check missing values
* Ensure numeric columns are numeric
* Drop or fill missing data

```python
# Check missing values
print(data.isnull().sum())

# Convert numeric columns (if needed)
data["SALES"] = pd.to_numeric(data["SALES"], errors="coerce")
data["QUANTITYORDERED"] = pd.to_numeric(data["QUANTITYORDERED"], errors="coerce")
data["PRICEEACH"] = pd.to_numeric(data["PRICEEACH"], errors="coerce")

# Drop rows with missing important values
data = data.dropna(subset=["SALES", "QUANTITYORDERED", "PRICEEACH"])
```

---

## 10.3 Exploratory Data Analysis (EDA)

```python
# Basic stats
print(data.describe())

# Top 5 countries by sales
print(data.groupby("COUNTRY")["SALES"].sum().sort_values(ascending=False).head(5))

# Top 5 product lines by revenue
print(data.groupby("PRODUCTLINE")["SALES"].sum().sort_values(ascending=False).head(5))
```

---

## 10.4 Data Visualization

```python
import matplotlib.pyplot as plt

# Sales by product line (bar chart)
data.groupby("PRODUCTLINE")["SALES"].sum().plot(
    kind="barh", figsize=(8,5), title="Total Sales by Product Line"
)
plt.show()

# Sales by country (top 6, pie chart)
data.groupby("COUNTRY")["SALES"].sum().sort_values(ascending=False).head(6).plot(
    kind="pie", autopct="%1.1f%%", figsize=(6,6), title="Top 6 Countries by Sales"
)
plt.ylabel("")
plt.show()
```

---

## 10.5 Advanced Grouping & Aggregation

```python
# Yearly sales trend
yearly_sales = data.groupby("YEAR_ID")["SALES"].sum()
print(yearly_sales)

yearly_sales.plot(kind="line", marker="o", title="Yearly Sales Trend")
plt.show()

# Country + Year breakdown
country_year_sales = data.groupby(["COUNTRY", "YEAR_ID"])["SALES"].sum().unstack()
print(country_year_sales.head())
```

---

## 10.6 Predictive Modeling (Regression)

We’ll predict **SALES** using **QUANTITYORDERED + PRICEEACH + COUNTRY**.

```python
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score
from sklearn.preprocessing import OneHotEncoder
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline

# Features and target
X = data[["QUANTITYORDERED", "PRICEEACH", "COUNTRY"]]
y = data["SALES"]

# Preprocess categorical variable
preprocessor = ColumnTransformer(
    transformers=[("cat", OneHotEncoder(handle_unknown="ignore"), ["COUNTRY"])],
    remainder="passthrough"
)

# Build pipeline
pipeline = Pipeline(steps=[("preprocessor", preprocessor),
                           ("model", LinearRegression())])

# Train-test split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train model
pipeline.fit(X_train, y_train)

# Predict & evaluate
y_pred = pipeline.predict(X_test)
print("MSE:", mean_squared_error(y_test, y_pred))
print("R²:", r2_score(y_test, y_pred))
```

---

## 10.7 Deliver Insights

At this point, you can summarize findings such as:

* Which product lines generate the most revenue?
* Which countries are the strongest markets?
* How has sales performance changed over years?
* Can we predict sales accurately from order data?

---

## 📝 Tasks for Practice

1. **Add Yearly Prediction**: Include `YEAR_ID` in the regression model.
2. **Create Dashboard-style Plots**: One figure with multiple subplots (sales by product, by country, by year).
3. **Segment Analysis**: Compare sales patterns between USA vs Europe.
4. **Export Results**: Save aggregated results into a CSV (e.g., `sales_summary.csv`).

---

✅ That completes **Module 10 (Capstone Project)** — you’ve gone full cycle from **raw data → analysis → visualization → prediction**.

Would you like me to design a **Module 11: Capstone Project Extension**, where you’ll turn this into a **report or dashboard (Excel, Power BI, or Jupyter Notebook style)** for presentation?


🔥 Awesome — let’s build on the capstone by creating a **professional report/dashboard** from our analysis.
Module 11 is about **communicating insights effectively** — packaging your analysis so others can understand and take action.

---

# 🧑‍💻 Module 11: Capstone Project Extension — Reporting & Dashboarding

**Goal**: Learn how to export, visualize, and present your analysis in formats like CSV reports, Excel summaries, or interactive dashboards.

---

## 11.1 Exporting Aggregated Results

Often we need to share **summary tables**. Let’s export sales insights into a CSV/Excel file.

```python
# Sales by country
sales_by_country = data.groupby("COUNTRY")["SALES"].sum().reset_index()

# Sales by product line
sales_by_product = data.groupby("PRODUCTLINE")["SALES"].sum().reset_index()

# Save to CSV
sales_by_country.to_csv("sales_by_country.csv", index=False)
sales_by_product.to_csv("sales_by_product.csv", index=False)

# Save to Excel (with multiple sheets)
with pd.ExcelWriter("sales_summary.xlsx") as writer:
    sales_by_country.to_excel(writer, sheet_name="Country Sales", index=False)
    sales_by_product.to_excel(writer, sheet_name="Product Sales", index=False)
```

✅ Now you can open `sales_summary.xlsx` in Excel and share it with stakeholders.

---

## 11.2 Creating a Dashboard-Style Report (Matplotlib)

Instead of single plots, we can arrange multiple plots into a **dashboard figure**.

```python
import matplotlib.pyplot as plt

fig, axes = plt.subplots(2, 2, figsize=(12, 10))

# 1. Sales by product line
data.groupby("PRODUCTLINE")["SALES"].sum().plot(
    kind="bar", ax=axes[0,0], title="Total Sales by Product Line", color="skyblue"
)

# 2. Top 6 countries
data.groupby("COUNTRY")["SALES"].sum().sort_values(ascending=False).head(6).plot(
    kind="pie", ax=axes[0,1], autopct="%1.1f%%", title="Top 6 Countries by Sales"
)
axes[0,1].set_ylabel("")

# 3. Yearly sales trend
data.groupby("YEAR_ID")["SALES"].sum().plot(
    kind="line", marker="o", ax=axes[1,0], title="Yearly Sales Trend"
)

# 4. Quantity vs Sales scatter
axes[1,1].scatter(data["QUANTITYORDERED"], data["SALES"], alpha=0.5, color="purple")
axes[1,1].set_title("Quantity Ordered vs Sales")
axes[1,1].set_xlabel("Quantity Ordered")
axes[1,1].set_ylabel("Sales")

plt.tight_layout()
plt.show()
```

This gives you a **dashboard-style overview** in one figure.

---

## 11.3 Generating a PDF Report

You can convert analysis into a **PDF report** using `reportlab` or `pandas + matplotlib` images.
Here’s a lightweight approach using Pandas profiling (EDA reports).

```python
# Install if needed
# pip install ydata-profiling

from ydata_profiling import ProfileReport

profile = ProfileReport(data, title="Sales Data Analysis Report", explorative=True)
profile.to_file("sales_analysis_report.html")
```

👉 This generates an **interactive HTML report** (clickable, with stats, plots, correlations).
You can then convert HTML → PDF if needed.

---

## 11.4 Interactive Dashboard (Optional: Plotly / Dash)

For more professional dashboards:

```python
import plotly.express as px

# Interactive bar chart
fig = px.bar(
    sales_by_product,
    x="PRODUCTLINE",
    y="SALES",
    title="Sales by Product Line (Interactive)"
)
fig.show()
```

👉 You can extend this using **Dash** (Flask-like framework) for a **web dashboard**.

---

## 📝 Tasks for Practice

1. Export **sales by year and country** into an Excel sheet.
2. Create a **4-panel dashboard** with:

   * Product line sales
   * Top 6 countries
   * Yearly sales
   * Scatter of Price vs Sales
3. Generate an **EDA report** using `ydata-profiling` and open it in your browser.
4. (Advanced) Build a **Plotly dashboard** combining bar, line, and scatter plots interactively.

---

✅ That completes **Module 11** — you’ve now gone from raw data → insights → predictive modeling → **presentation-ready dashboards/reports**.

Would you like me to design a **Module 12: Advanced Project (Time Series Forecasting with ARIMA/Prophet)**, so you can predict **future sales trends** instead of just analyzing historical data?
Excellent 🚀 — let’s push further with **Time Series Forecasting**, one of the most exciting parts of data analysis.
In **Module 12**, we’ll learn how to **predict future sales trends** using **time series models** like ARIMA and Prophet.

---

# 🧑‍💻 Module 12: Time Series Forecasting with Python

**Goal**: Use historical sales data to forecast **future sales**.

---

## 12.1 Preparing the Data for Time Series

We need **date-based data**. The dataset has `ORDERDATE` and `SALES`.
Let’s clean and aggregate it by **month** to see trends.

```python
import pandas as pd

# Load dataset
data = pd.read_csv("/mnt/data/sales_data_sample.csv")

# Convert ORDERDATE to datetime
data["ORDERDATE"] = pd.to_datetime(data["ORDERDATE"])

# Aggregate sales by month
monthly_sales = data.groupby(pd.Grouper(key="ORDERDATE", freq="M"))["SALES"].sum().reset_index()

print(monthly_sales.head())
```

---

## 12.2 Visualize the Sales Trend

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(10,6))
plt.plot(monthly_sales["ORDERDATE"], monthly_sales["SALES"], marker="o")
plt.title("Monthly Sales Trend")
plt.xlabel("Date")
plt.ylabel("Total Sales")
plt.grid(True)
plt.show()
```

✅ Now we see a **time series** (trend + seasonality).

---

## 12.3 Forecasting with ARIMA

ARIMA is a classic statistical model for time series forecasting.

```python
from statsmodels.tsa.arima.model import ARIMA

# Set index as datetime for time series
monthly_sales.set_index("ORDERDATE", inplace=True)

# Build ARIMA model (p=1, d=1, q=1 as a start)
model = ARIMA(monthly_sales["SALES"], order=(1,1,1))
model_fit = model.fit()

# Forecast next 12 months
forecast = model_fit.get_forecast(steps=12)
forecast_index = pd.date_range(monthly_sales.index[-1], periods=12, freq="M")

# Plot
plt.figure(figsize=(10,6))
plt.plot(monthly_sales.index, monthly_sales["SALES"], label="Historical")
plt.plot(forecast_index, forecast.predicted_mean, label="Forecast", color="red")
plt.fill_between(forecast_index,
                 forecast.conf_int()["lower SALES"],
                 forecast.conf_int()["upper SALES"],
                 color="pink", alpha=0.3)
plt.legend()
plt.title("ARIMA Sales Forecast (12 months ahead)")
plt.show()
```

---

## 12.4 Forecasting with Prophet (Facebook Prophet)

Prophet is user-friendly and handles seasonality well.

```python
# Install if needed:
# pip install prophet

from prophet import Prophet

# Prepare data for Prophet
df_prophet = monthly_sales.reset_index()[["ORDERDATE", "SALES"]]
df_prophet.columns = ["ds", "y"]

# Build & fit model
model = Prophet()
model.fit(df_prophet)

# Make future dataframe (12 months ahead)
future = model.make_future_dataframe(periods=12, freq="M")
forecast = model.predict(future)

# Plot forecast
fig = model.plot(forecast)
fig.show()

# Plot forecast components (trend, seasonality)
model.plot_components(forecast)
```

---

## 12.5 Evaluate Forecast Accuracy

Split into **train (past)** and **test (recent months)** to check accuracy.

```python
# Train-test split (last 12 months as test)
train = monthly_sales.iloc[:-12]
test = monthly_sales.iloc[-12:]

# Fit ARIMA on train
model = ARIMA(train["SALES"], order=(1,1,1))
model_fit = model.fit()

# Forecast same period as test
forecast = model_fit.forecast(steps=12)

# Compare
plt.figure(figsize=(10,6))
plt.plot(train.index, train["SALES"], label="Train")
plt.plot(test.index, test["SALES"], label="Test", color="orange")
plt.plot(test.index, forecast, label="Forecast", color="red")
plt.legend()
plt.title("ARIMA Forecast vs Actual")
plt.show()
```

---

## 📝 Tasks for Practice

1. Aggregate **daily sales** instead of monthly, and forecast the next 30 days.
2. Compare **ARIMA vs Prophet** predictions on the same data.
3. Evaluate forecast accuracy with **Mean Absolute Error (MAE)** and **Root Mean Squared Error (RMSE)**.
4. Add **seasonality detection** with Prophet and see if there’s a yearly pattern.
5. Export your forecast results into a CSV (`sales_forecast.csv`).

---

✅ That wraps up **Module 12** — you can now **predict future sales trends** using ARIMA and Prophet.

Would you like me to continue with **Module 13: Advanced Machine Learning (Classification — predicting categorical outcomes, like whether an order will be “High Value” or “Low Value”)?**
Perfect 🔥 — time to level up with **Classification Models**.
Unlike regression (where we predict a numeric value like SALES), **classification predicts categories** (labels).

---

# 🧑‍💻 Module 13: Advanced Machine Learning — Classification

**Goal**: Predict whether an order is **“High Value” or “Low Value”** using features like quantity, price, and country.

---

## 13.1 Creating a Classification Target

We’ll define a **binary target**:

* High Value = SALES above median
* Low Value = SALES below or equal to median

```python
import pandas as pd

# Load dataset
data = pd.read_csv("/mnt/data/sales_data_sample.csv")

# Create target variable
threshold = data["SALES"].median()
data["HIGH_VALUE_ORDER"] = (data["SALES"] > threshold).astype(int)

print(data[["SALES", "HIGH_VALUE_ORDER"]].head())
```

✅ Now we have a binary classification target.

---

## 13.2 Feature Selection

We’ll use numerical + categorical features.

```python
X = data[["QUANTITYORDERED", "PRICEEACH", "COUNTRY", "PRODUCTLINE"]]
y = data["HIGH_VALUE_ORDER"]
```

---

## 13.3 Train-Test Split

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)
```

---

## 13.4 Preprocessing (Encode Categoricals)

```python
from sklearn.preprocessing import OneHotEncoder
from sklearn.compose import ColumnTransformer

preprocessor = ColumnTransformer(
    transformers=[("cat", OneHotEncoder(handle_unknown="ignore"), ["COUNTRY", "PRODUCTLINE"])],
    remainder="passthrough"
)
```

---

## 13.5 Logistic Regression Classifier

```python
from sklearn.pipeline import Pipeline
from sklearn.linear_model import LogisticRegression

# Pipeline: preprocess + model
clf = Pipeline(steps=[
    ("preprocessor", preprocessor),
    ("classifier", LogisticRegression(max_iter=1000))
])

# Train model
clf.fit(X_train, y_train)

# Evaluate
y_pred = clf.predict(X_test)
```

---

## 13.6 Evaluation Metrics

```python
from sklearn.metrics import classification_report, confusion_matrix

print(confusion_matrix(y_test, y_pred))
print(classification_report(y_test, y_pred))
```

✅ You’ll see **precision, recall, and F1-score** for both High and Low Value orders.

---

## 13.7 Try a More Powerful Model: Random Forest

```python
from sklearn.ensemble import RandomForestClassifier

rf_clf = Pipeline(steps=[
    ("preprocessor", preprocessor),
    ("classifier", RandomForestClassifier(n_estimators=100, random_state=42))
])

rf_clf.fit(X_train, y_train)
y_pred_rf = rf_clf.predict(X_test)

print(classification_report(y_test, y_pred_rf))
```

---

## 13.8 Feature Importance

With tree-based models, we can check which features matter most.

```python
import matplotlib.pyplot as plt

# Extract feature names after one-hot encoding
encoded_features = rf_clf.named_steps["preprocessor"].get_feature_names_out()
importances = rf_clf.named_steps["classifier"].feature_importances_

# Sort & plot
feat_imp = pd.Series(importances, index=encoded_features).sort_values(ascending=False)
feat_imp.head(10).plot(kind="barh", title="Top 10 Important Features")
plt.show()
```

---

## 📝 Tasks for Practice

1. Train a **Decision Tree Classifier** and visualize it.
2. Compare Logistic Regression vs Random Forest vs Decision Tree — which performs best?
3. Use `YEAR_ID` as a feature — does year affect high-value orders?
4. Plot a confusion matrix heatmap with `seaborn`.
5. Save model using `joblib` (`high_value_model.pkl`) and reload it later.

---

✅ That completes **Module 13** — you can now build **classification models** to predict categories (e.g., high vs low value orders).

Would you like me to continue with **Module 14: Clustering & Customer Segmentation** (unsupervised learning to group similar customers/products)?

Awesome 🚀 — let’s dive into **unsupervised learning** with **clustering**, one of the most practical tools in data analysis.
Unlike regression or classification (which need labels), clustering **finds hidden patterns or groups** in data automatically.

---

# 🧑‍💻 Module 14: Clustering & Customer Segmentation

**Goal**: Use clustering to group customers or products into segments (e.g., “price-sensitive”, “bulk buyers”, “premium customers”).

---

## 14.1 Select Features for Clustering

We’ll try clustering on customers based on **total sales** and **average order value**.

```python
import pandas as pd

# Load dataset
data = pd.read_csv("/mnt/data/sales_data_sample.csv")

# Aggregate by customer
customer_data = data.groupby("CUSTOMERNAME").agg({
    "SALES": "sum",
    "QUANTITYORDERED": "sum"
}).reset_index()

# Add average order value
customer_data["AVG_ORDER_VALUE"] = customer_data["SALES"] / customer_data["QUANTITYORDERED"]

print(customer_data.head())
```

---

## 14.2 Standardize the Data

Clustering works best when features are on the same scale.

```python
from sklearn.preprocessing import StandardScaler

X = customer_data[["SALES", "AVG_ORDER_VALUE"]]
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)
```

---

## 14.3 K-Means Clustering

```python
from sklearn.cluster import KMeans

# Fit KMeans with 3 clusters (you can adjust)
kmeans = KMeans(n_clusters=3, random_state=42, n_init="auto")
customer_data["CLUSTER"] = kmeans.fit_predict(X_scaled)

print(customer_data.head())
```

---

## 14.4 Visualize Clusters

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(8,6))
plt.scatter(customer_data["SALES"], customer_data["AVG_ORDER_VALUE"],
            c=customer_data["CLUSTER"], cmap="viridis", alpha=0.7)
plt.xlabel("Total Sales")
plt.ylabel("Average Order Value")
plt.title("Customer Segmentation (KMeans Clusters)")
plt.colorbar(label="Cluster")
plt.show()
```

---

## 14.5 Choosing the Best Number of Clusters (Elbow Method)

```python
inertia = []
for k in range(1, 10):
    km = KMeans(n_clusters=k, random_state=42, n_init="auto").fit(X_scaled)
    inertia.append(km.inertia_)

plt.plot(range(1, 10), inertia, marker="o")
plt.xlabel("Number of Clusters (k)")
plt.ylabel("Inertia")
plt.title("Elbow Method for Optimal k")
plt.show()
```

✅ Look for the “elbow” point — where adding more clusters doesn’t reduce inertia much.

---

## 14.6 Advanced: Hierarchical Clustering

```python
from scipy.cluster.hierarchy import dendrogram, linkage

# Perform hierarchical clustering
Z = linkage(X_scaled, method="ward")

plt.figure(figsize=(10, 6))
dendrogram(Z, labels=customer_data["CUSTOMERNAME"].values, leaf_rotation=90, leaf_font_size=8)
plt.title("Hierarchical Clustering Dendrogram")
plt.show()
```

---

## 14.7 Segment Profiling

We can now analyze each cluster to describe **customer segments**.

```python
cluster_summary = customer_data.groupby("CLUSTER").agg({
    "SALES": "mean",
    "AVG_ORDER_VALUE": "mean",
    "CUSTOMERNAME": "count"
}).rename(columns={"CUSTOMERNAME": "NUM_CUSTOMERS"})

print(cluster_summary)
```

👉 This tells you things like:

* Cluster 0: High sales, high order value → “Premium Buyers”
* Cluster 1: Low sales, low order value → “Casual Buyers”
* Cluster 2: Moderate sales, moderate value → “Regular Buyers”

---

## 📝 Tasks for Practice

1. Cluster **products** instead of customers, using total sales + average price.
2. Try different values of `k` for KMeans and compare results.
3. Add `COUNTRY` as a feature and see if clustering aligns with geography.
4. Use **DBSCAN** (density-based clustering) to detect outliers.
5. Create a **cluster report** in Excel with customer names and assigned clusters.

---

✅ That completes **Module 14** — you now know how to use **clustering for segmentation** (one of the most valuable business analytics techniques).

Would you like me to continue with **Module 15: Building a Recommendation System** (suggesting products to customers based on purchase history)?

Perfect 💡 — let’s wrap all this into something very **business-applicable**: a **Recommendation System**.
In **Module 15**, we’ll build a **product recommender** (like Amazon or Netflix style), suggesting products based on purchase patterns.

---

# 🧑‍💻 Module 15: Building a Recommendation System

**Goal**: Recommend products to customers using their purchase history.
We’ll cover both **simple recommenders** (popularity-based) and **collaborative filtering** (customer similarity).

---

## 15.1 Prepare Data

We’ll use `CUSTOMERNAME`, `PRODUCTCODE`, and `SALES`.

```python
import pandas as pd

# Load dataset
data = pd.read_csv("/mnt/data/sales_data_sample.csv")

# Keep relevant columns
purchase_data = data[["CUSTOMERNAME", "PRODUCTCODE", "SALES"]]

print(purchase_data.head())
```

---

## 15.2 Popularity-Based Recommender

This is the simplest — recommend top-selling products overall.

```python
# Top 5 most popular products
top_products = purchase_data.groupby("PRODUCTCODE")["SALES"].sum().sort_values(ascending=False).head(5)
print("Top Products Recommended to Everyone:\n", top_products)
```

✅ Works as a **baseline recommender**.

---

## 15.3 Customer-Product Matrix

For collaborative filtering, create a matrix where:

* Rows = customers
* Columns = products
* Values = total sales (or counts)

```python
customer_product_matrix = purchase_data.pivot_table(
    index="CUSTOMERNAME", 
    columns="PRODUCTCODE", 
    values="SALES", 
    aggfunc="sum", 
    fill_value=0
)

print(customer_product_matrix.shape)
```

---

## 15.4 User-Based Collaborative Filtering

We recommend products by finding **similar customers**.

```python
from sklearn.metrics.pairwise import cosine_similarity

# Compute similarity between customers
similarity = cosine_similarity(customer_product_matrix)
similarity_df = pd.DataFrame(similarity, index=customer_product_matrix.index, columns=customer_product_matrix.index)

# Find similar customers for one customer
customer = "Euro+ Shopping Channel"
similar_customers = similarity_df[customer].sort_values(ascending=False).head(5)
print("Customers most similar to", customer, ":\n", similar_customers)
```

---

## 15.5 Generate Recommendations

Now recommend products purchased by similar customers but not yet by the target customer.

```python
def recommend_products(target_customer, top_n=5):
    # Get top similar customers
    similar_customers = similarity_df[target_customer].sort_values(ascending=False).index[1:6]

    # Products purchased by similar customers
    similar_purchases = customer_product_matrix.loc[similar_customers].sum().sort_values(ascending=False)

    # Exclude products already bought by target
    target_purchases = customer_product_matrix.loc[target_customer]
    recommendations = similar_purchases[target_purchases == 0].head(top_n)

    return recommendations

print("Recommendations for Euro+ Shopping Channel:\n", recommend_products("Euro+ Shopping Channel"))
```

---

## 15.6 Advanced: Item-Based Collaborative Filtering

Instead of looking for **similar customers**, look for **similar products**.

```python
# Compute similarity between products
product_similarity = cosine_similarity(customer_product_matrix.T)
product_similarity_df = pd.DataFrame(
    product_similarity, 
    index=customer_product_matrix.columns, 
    columns=customer_product_matrix.columns
)

# Recommend products similar to one product
product = "S18_3232"
similar_products = product_similarity_df[product].sort_values(ascending=False).head(5)
print("Products similar to", product, ":\n", similar_products)
```

---

## 15.7 Visualizing Recommendations (Heatmap)

```python
import seaborn as sns
import matplotlib.pyplot as plt

plt.figure(figsize=(10,6))
sns.heatmap(similarity_df.iloc[:20, :20], cmap="viridis")
plt.title("Customer Similarity Heatmap (Top 20 Customers)")
plt.show()
```

---

## 📝 Tasks for Practice

1. Build a **Top-N recommender** (e.g., recommend 10 products for each customer).
2. Compare **user-based** vs **item-based** collaborative filtering.
3. Recommend products for at least 3 different customers.
4. Visualize most recommended products with a **bar chart**.
5. Save recommendations to a CSV (`recommendations.csv`).

---

✅ That completes **Module 15** — you now know how to build **recommendation systems** (the foundation of modern e-commerce platforms).

Would you like me to continue with **Module 16: Automating the Data Analysis Pipeline** (building a reusable script/notebook that runs cleaning, analysis, ML, and reporting automatically)?

Great 🚀 — in **Module 16**, we’ll bring everything together by **automating the full data analysis pipeline**.
This will make your workflow reusable, professional, and production-ready.

---

# 🧑‍💻 Module 16: Automating the Data Analysis Pipeline

**Goal**: Create a **Python pipeline** that runs data cleaning, analysis, visualization, and reporting automatically.
This will save you time and ensure consistency for recurring analyses.

---

## 16.1 Why Pipelines?

* Repeatable → run the same analysis on new data easily
* Organized → separate steps for data loading, cleaning, analysis, visualization
* Scalable → extend with ML models, reporting, dashboards

---

## 16.2 Project Setup

We’ll organize our pipeline into **functions** and a main script.

```
project/
│── data/
│   └── sales_data_sample.csv
│── pipeline.py
```

---

## 16.3 Step 1: Data Loader

```python
import pandas as pd

def load_data(file_path):
    """Load CSV file into pandas DataFrame"""
    print("📂 Loading data...")
    data = pd.read_csv(file_path)
    print(f"✅ Loaded {data.shape[0]} rows and {data.shape[1]} columns")
    return data
```

---

## 16.4 Step 2: Data Cleaning

```python
def clean_data(df):
    """Handle missing values and correct data types"""
    print("🧹 Cleaning data...")
    df = df.dropna(subset=["SALES", "PRICEEACH"])  # drop rows with missing sales
    df["ORDERDATE"] = pd.to_datetime(df["ORDERDATE"], errors="coerce")  # ensure dates
    print("✅ Data cleaned")
    return df
```

---

## 16.5 Step 3: Analysis

```python
def analyze_data(df):
    """Perform key aggregations"""
    print("📊 Running analysis...")
    
    sales_by_country = df.groupby("COUNTRY")["SALES"].sum().sort_values(ascending=False).head(5)
    sales_by_product = df.groupby("PRODUCTLINE")["SALES"].sum().sort_values(ascending=False)
    
    print("✅ Analysis complete")
    return sales_by_country, sales_by_product
```

---

## 16.6 Step 4: Visualization

```python
import matplotlib.pyplot as plt

def visualize_data(sales_by_country, sales_by_product):
    """Generate charts for analysis results"""
    print("📈 Creating visualizations...")
    
    sales_by_country.plot(kind="bar", title="Top 5 Countries by Sales", figsize=(6,4))
    plt.savefig("country_sales.png")
    plt.close()
    
    sales_by_product.plot(kind="pie", autopct="%1.1f%%", title="Sales by Product Line", figsize=(6,6))
    plt.savefig("product_sales.png")
    plt.close()
    
    print("✅ Charts saved: country_sales.png, product_sales.png")
```

---

## 16.7 Step 5: Reporting

We’ll export results into a **CSV + text summary**.

```python
def export_report(sales_by_country, sales_by_product):
    """Save results to CSV and text file"""
    print("📝 Exporting report...")
    
    sales_by_country.to_csv("sales_by_country.csv")
    sales_by_product.to_csv("sales_by_product.csv")
    
    with open("report.txt", "w") as f:
        f.write("Sales Analysis Report\n")
        f.write("=====================\n\n")
        f.write("Top 5 Countries by Sales:\n")
        f.write(str(sales_by_country) + "\n\n")
        f.write("Sales by Product Line:\n")
        f.write(str(sales_by_product) + "\n")
    
    print("✅ Report generated: report.txt, CSVs")
```

---

## 16.8 Step 6: Main Pipeline

```python
def run_pipeline():
    """Full pipeline execution"""
    data = load_data("data/sales_data_sample.csv")
    cleaned_data = clean_data(data)
    sales_by_country, sales_by_product = analyze_data(cleaned_data)
    visualize_data(sales_by_country, sales_by_product)
    export_report(sales_by_country, sales_by_product)

if __name__ == "__main__":
    run_pipeline()
```

---

## 16.9 Running the Pipeline

In VS Code terminal (Ubuntu):

```bash
python3 pipeline.py
```

✅ The pipeline will:

1. Load → Clean → Analyze → Visualize → Export.
2. Generate **charts, CSVs, and a text report** automatically.

---

## 📝 Tasks for Practice

1. Add a **logging system** (use `logging` instead of print).
2. Extend the pipeline with **Module 15 recommender system**.
3. Automate **emailing the report** using `smtplib`.
4. Schedule the pipeline with **cron jobs** (Ubuntu).
5. Wrap the pipeline into a **Jupyter Notebook** with Markdown explanations.

---

👉 That completes **Module 16: Automating the Data Analysis Pipeline** 🎯.

Do you want me to continue with **Module 17: Deploying Your Analysis (Dashboards & APIs)** so you can share results interactively with others?
Perfect! 🚀 Now that you’ve automated your analysis, let’s make it **shareable and interactive**.
In **Module 17**, we’ll focus on **deployment** — exposing your analysis through **dashboards** and **APIs**.

---

# 🧑‍💻 Module 17: Deploying Your Analysis (Dashboards & APIs)

**Goal**: Share insights with non-technical users by:

1. Building an **interactive dashboard** (using `Streamlit`)
2. Exposing your analysis as an **API** (using `Flask`)

---

## 17.1 Why Deployment?

* Stakeholders don’t want to run Python scripts.
* Dashboards → Interactive exploration (charts, filters).
* APIs → Integrate analysis with web apps or automation.

---

## 17.2 Option A: Streamlit Dashboard

First, install streamlit:

```bash
pip install streamlit
```

---

### 📊 Dashboard Code (`dashboard.py`)

```python
import pandas as pd
import streamlit as st
import matplotlib.pyplot as plt

# Load data
@st.cache_data
def load_data():
    df = pd.read_csv("data/sales_data_sample.csv")
    df["ORDERDATE"] = pd.to_datetime(df["ORDERDATE"], errors="coerce")
    return df

data = load_data()

st.title("📊 Sales Data Dashboard")

# Sidebar filter
country = st.sidebar.selectbox("Select Country", sorted(data["COUNTRY"].unique()))

filtered = data[data["COUNTRY"] == country]

st.subheader(f"Sales Analysis for {country}")
st.write(filtered.head())

# Sales over time
sales_time = filtered.groupby("ORDERDATE")["SALES"].sum()

fig, ax = plt.subplots(figsize=(10,4))
sales_time.plot(ax=ax, title=f"Sales Over Time - {country}")
st.pyplot(fig)

# Product line sales
st.bar_chart(filtered.groupby("PRODUCTLINE")["SALES"].sum())
```

---

### ▶️ Run the Dashboard

```bash
streamlit run dashboard.py
```

✅ Open [http://localhost:8501](http://localhost:8501) to interact with your dashboard.

---

## 17.3 Option B: Flask API

Install Flask:

```bash
pip install flask
```

---

### 🌐 API Code (`app.py`)

```python
from flask import Flask, jsonify
import pandas as pd

app = Flask(__name__)

# Load data once
data = pd.read_csv("data/sales_data_sample.csv")

@app.route("/top_countries")
def top_countries():
    result = data.groupby("COUNTRY")["SALES"].sum().sort_values(ascending=False).head(5)
    return jsonify(result.to_dict())

@app.route("/top_products")
def top_products():
    result = data.groupby("PRODUCTLINE")["SALES"].sum().sort_values(ascending=False)
    return jsonify(result.to_dict())

if __name__ == "__main__":
    app.run(debug=True)
```

---

### ▶️ Run the API

```bash
python3 app.py
```

Check endpoints in browser:

* `http://127.0.0.1:5000/top_countries`
* `http://127.0.0.1:5000/top_products`

✅ Returns JSON with aggregated results.

---

## 17.4 Deployment Options

* **Local** → Run on your machine (good for learning).
* **Cloud** → Deploy using:

  * Streamlit Cloud (free for simple apps).
  * Render / Railway (for Flask APIs).
  * Dockerize and deploy on AWS/GCP/Azure.

---

## 📝 Tasks for Practice

1. Add a **filter** in Streamlit (year, product line, country).
2. Add an **API endpoint** that returns **recommendations** (from Module 15).
3. Combine Streamlit + Flask: host an **interactive dashboard + API backend**.
4. Deploy your dashboard to **Streamlit Cloud** and share the link.
5. Add authentication (e.g., password-protected dashboard).

---

✅ That completes **Module 17: Deploying Your Analysis (Dashboards & APIs)**.
This makes your work **usable by others** — the final step for real-world data analysts.

---

Would you like me to continue with **Module 18: Advanced Python for Data Analysis (Performance, Parallelization, and Big Data Tools)** to take things to the next level?

Awesome ⚡ — you’ve now deployed dashboards and APIs.
In **Module 18**, we’ll push into **advanced Python for data analysis** — making your code faster, more scalable, and ready for **big data**.

---

# 🧑‍💻 Module 18: Advanced Python for Data Analysis (Performance, Parallelization, and Big Data Tools)

**Goal**: Learn how to handle **large datasets** efficiently, speed up computations, and work with **big data frameworks**.

---

## 18.1 Performance Challenges in Data Analysis

* Pandas is great, but can struggle with **millions of rows**.
* Common bottlenecks:

  * Inefficient loops
  * Memory usage
  * Single-threaded execution

---

## 18.2 Profiling Code

Before optimizing, profile your code to see where time is spent.

```python
import pandas as pd
import time

data = pd.read_csv("data/sales_data_sample.csv")

start = time.time()
total_sales = data.groupby("COUNTRY")["SALES"].sum()
end = time.time()

print(total_sales)
print("Execution Time:", end - start, "seconds")
```

👉 Use **`%timeit`** in Jupyter Notebook for even more precise profiling.

---

## 18.3 Vectorization (Avoid Loops)

Inefficient loop ❌:

```python
# Looping through rows (slow)
total = 0
for s in data["SALES"]:
    total += s
print(total)
```

Efficient vectorization ✅:

```python
# Fast with pandas vectorized operations
print(data["SALES"].sum())
```

---

## 18.4 Parallelization with `multiprocessing`

Use multiple CPU cores.

```python
from multiprocessing import Pool

def process_chunk(chunk):
    return chunk["SALES"].sum()

chunks = [data.iloc[i:i+100] for i in range(0, len(data), 100)]

with Pool() as pool:
    results = pool.map(process_chunk, chunks)

print("Total Sales:", sum(results))
```

---

## 18.5 Working with Larger-than-Memory Data

### Option A: **Dask** (Parallel Pandas)

```bash
pip install dask
```

```python
import dask.dataframe as dd

# Load with Dask (lazy loading)
df = dd.read_csv("data/sales_data_sample.csv")

# Compute only when needed
print(df["SALES"].sum().compute())
```

✅ Handles **out-of-memory datasets** with parallelism.

---

### Option B: **Vaex** (Memory-efficient DataFrames)

```bash
pip install vaex
```

```python
import vaex

df = vaex.from_csv("data/sales_data_sample.csv", convert=True)
print(df["SALES"].sum())
```

---

## 18.6 Big Data with PySpark

When data gets HUGE → move to **Spark**.

```bash
pip install pyspark
```

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("SalesAnalysis").getOrCreate()

df = spark.read.csv("data/sales_data_sample.csv", header=True, inferSchema=True)

df.groupBy("COUNTRY").sum("SALES").show()
```

✅ Spark distributes computations across a cluster (good for **100M+ rows**).

---

## 18.7 Memory Optimization in Pandas

Reduce memory footprint:

```python
# Convert float64 → float32, int64 → int32
data["QUANTITYORDERED"] = pd.to_numeric(data["QUANTITYORDERED"], downcast="integer")
data["SALES"] = pd.to_numeric(data["SALES"], downcast="float")

# Convert object → category (when limited unique values)
data["COUNTRY"] = data["COUNTRY"].astype("category")
```

---

## 18.8 Parallel Visualization

```python
import matplotlib.pyplot as plt
import seaborn as sns
import dask.dataframe as dd

df = dd.read_csv("data/sales_data_sample.csv")

sales = df.groupby("COUNTRY")["SALES"].sum().compute()

plt.figure(figsize=(8,4))
sns.barplot(x=sales.index, y=sales.values)
plt.xticks(rotation=45)
plt.title("Sales by Country (Parallel with Dask)")
plt.show()
```

---

## 📝 Tasks for Practice

1. Compare execution time of:

   * A loop vs vectorized sum
   * Pandas vs Dask vs PySpark (on the same dataset).
2. Optimize memory usage of `sales_data_sample.csv`.
3. Split dataset into chunks and process with `multiprocessing`.
4. Try loading a **100MB+ dataset** (can download from Kaggle) and test with Pandas vs Dask.
5. Build a Spark job to compute **monthly sales trends**.

---

✅ That completes **Module 18: Advanced Python for Data Analysis (Performance & Big Data Tools)**.
Now your workflows can scale from **small CSVs → large distributed datasets**.

---

Would you like me to continue with **Module 19: Machine Learning for Data Analysis** (using scikit-learn to build predictive models from your dataset)?
Perfect! 🎯 You’ve mastered cleaning, analysis, visualization, automation, deployment, and scaling.
Now let’s step into **predictive analytics**.

---

# 🧑‍💻 Module 19: Machine Learning for Data Analysis

**Goal**: Use **scikit-learn** to build predictive models (regression & classification) based on your dataset.

---

## 19.1 Why Machine Learning in Data Analysis?

* Traditional analysis → describes what happened.
* ML → **predicts what will happen**.
* Examples:

  * Predict **future sales**
  * Predict **whether an order will be high-value**
  * Recommend **products** (we did in Module 15)

---

## 19.2 Setup

Install scikit-learn if not available:

```bash
pip install scikit-learn
```

---

## 19.3 Preparing the Data

Let’s predict **SALES** based on order details.

```python
import pandas as pd
from sklearn.model_selection import train_test_split

# Load data
data = pd.read_csv("data/sales_data_sample.csv")

# Features & target
X = data[["QUANTITYORDERED", "PRICEEACH"]]
y = data["SALES"]

# Split dataset (80% train, 20% test)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

print(X_train.shape, X_test.shape)
```

---

## 19.4 Linear Regression (Predicting Sales)

```python
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score

# Train model
model = LinearRegression()
model.fit(X_train, y_train)

# Predictions
y_pred = model.predict(X_test)

# Evaluation
print("R² Score:", r2_score(y_test, y_pred))
print("MSE:", mean_squared_error(y_test, y_pred))
```

✅ You now have a **regression model** that predicts sales given order details.

---

## 19.5 Classification (High vs Low Value Orders)

Let’s classify whether an order is **high value** (> 3000).

```python
import numpy as np
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, classification_report

# Create binary target
data["HighValue"] = np.where(data["SALES"] > 3000, 1, 0)

X = data[["QUANTITYORDERED", "PRICEEACH"]]
y = data["HighValue"]

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train classification model
clf = LogisticRegression()
clf.fit(X_train, y_train)

y_pred = clf.predict(X_test)

print("Accuracy:", accuracy_score(y_test, y_pred))
print(classification_report(y_test, y_pred))
```

✅ You now have a **classification model** to flag high-value orders.

---

## 19.6 Decision Trees (More Interpretable Models)

```python
from sklearn.tree import DecisionTreeClassifier, plot_tree
import matplotlib.pyplot as plt

tree_clf = DecisionTreeClassifier(max_depth=4, random_state=42)
tree_clf.fit(X_train, y_train)

plt.figure(figsize=(12,6))
plot_tree(tree_clf, feature_names=["QUANTITYORDERED", "PRICEEACH"], class_names=["Low", "High"], filled=True)
plt.show()
```

✅ Decision trees give interpretable rules.

---

## 19.7 Feature Engineering

Add new features to improve predictions:

```python
data["DiscountedPrice"] = data["MSRP"] - data["PRICEEACH"]
data["RevenuePerItem"] = data["SALES"] / data["QUANTITYORDERED"]
```

Then retrain models with additional features.

---

## 19.8 Cross-Validation & Hyperparameter Tuning

```python
from sklearn.model_selection import cross_val_score

scores = cross_val_score(tree_clf, X, y, cv=5)
print("Cross-validation scores:", scores)
print("Average Accuracy:", scores.mean())
```

---

## 19.9 Saving Models for Reuse

```python
import joblib

joblib.dump(clf, "logistic_model.pkl")

# Load later
loaded_model = joblib.load("logistic_model.pkl")
print(loaded_model.predict([[40, 95]]))  # Example input
```

---

## 📝 Tasks for Practice

1. Train a **Random Forest** model to predict `HighValue` orders.
2. Compare **Linear Regression vs Decision Tree vs Random Forest** performance.
3. Add **country** (categorical variable) to the model using **one-hot encoding**.
4. Use cross-validation to tune **max\_depth** for the decision tree.
5. Save the best model and integrate it into your **Flask API** (from Module 17).

---

✅ That completes **Module 19: Machine Learning for Data Analysis** 🎯.
Now you can move from **descriptive analytics → predictive analytics**.

---

Would you like me to continue with **Module 20: Time Series Forecasting** (predicting future sales trends)?

Perfect timing ⏳ — you’re now ready for **time-based predictions**.
In **Module 20**, we’ll dive into **Time Series Forecasting** to predict **future sales trends** from your dataset.

---

# 🧑‍💻 Module 20: Time Series Forecasting

**Goal**: Learn how to handle time-series data and build forecasts using **pandas**, **statsmodels**, and **scikit-learn**.

---

## 20.1 What is Time Series Forecasting?

* A **time series** = data indexed by time (daily sales, monthly revenue, stock prices).
* Forecasting uses **past trends** to predict the **future**.
* Common use cases:

  * Predicting **sales for next month**
  * **Inventory planning**
  * **Customer demand forecasting**

---

## 20.2 Prepare the Data

```python
import pandas as pd

# Load data
data = pd.read_csv("data/sales_data_sample.csv")

# Ensure datetime
data["ORDERDATE"] = pd.to_datetime(data["ORDERDATE"], errors="coerce")

# Aggregate sales by month
monthly_sales = data.groupby(pd.Grouper(key="ORDERDATE", freq="M"))["SALES"].sum()

print(monthly_sales.head())
```

✅ We now have monthly sales totals.

---

## 20.3 Visualize the Time Series

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(10,4))
monthly_sales.plot(title="Monthly Sales Over Time")
plt.ylabel("Sales")
plt.show()
```

---

## 20.4 Moving Average (Smoothing)

```python
monthly_sales.rolling(window=3).mean().plot(title="3-Month Moving Average")
plt.show()
```

✅ Helps detect **trends** by smoothing fluctuations.

---

## 20.5 Train/Test Split

Keep last few months as test set.

```python
train = monthly_sales[:-6]
test = monthly_sales[-6:]

print("Train size:", train.shape[0])
print("Test size:", test.shape[0])
```

---

## 20.6 Forecasting with ARIMA

Install statsmodels if needed:

```bash
pip install statsmodels
```

```python
from statsmodels.tsa.arima.model import ARIMA

# Build ARIMA model (p=1, d=1, q=1 as a start)
model = ARIMA(train, order=(1,1,1))
model_fit = model.fit()

# Forecast next 6 months
forecast = model_fit.forecast(steps=6)

print("Forecasted Sales:\n", forecast)

# Plot
plt.figure(figsize=(10,4))
plt.plot(train, label="Train")
plt.plot(test, label="Test")
plt.plot(forecast, label="Forecast", linestyle="--")
plt.legend()
plt.show()
```

---

## 20.7 Evaluating Forecast Accuracy

```python
from sklearn.metrics import mean_squared_error

mse = mean_squared_error(test, forecast)
print("MSE:", mse)
```

✅ Lower MSE = better forecasting accuracy.

---

## 20.8 Seasonal Decomposition

Check for **trend, seasonality, and residuals**.

```python
from statsmodels.tsa.seasonal import seasonal_decompose

result = seasonal_decompose(monthly_sales, model="additive")
result.plot()
plt.show()
```

---

## 20.9 Advanced: Prophet (Facebook’s Forecasting Tool)

```bash
pip install prophet
```

```python
from prophet import Prophet

df = monthly_sales.reset_index()
df.columns = ["ds", "y"]  # Prophet expects these column names

model = Prophet()
model.fit(df)

future = model.make_future_dataframe(periods=6, freq="M")
forecast = model.predict(future)

model.plot(forecast)
plt.show()
```

✅ Prophet handles **seasonality & holidays** very well.

---

## 📝 Tasks for Practice

1. Compare ARIMA vs Prophet forecasts.
2. Forecast **next 12 months** of sales.
3. Plot actual vs predicted sales for test period.
4. Test different ARIMA orders `(p, d, q)` and see which is best.
5. Add **country-level sales** forecasting (multi-time-series).

---

✅ That completes **Module 20: Time Series Forecasting** 🎯.
Now you can not only analyze sales but also **predict future sales trends**.

---

Would you like me to continue with **Module 21: Natural Language Processing (NLP) for Data Analysis** (extracting insights from customer comments, reviews, or text data)?

Great 🚀 — you’re moving into **text analytics** now!
In **Module 21**, we’ll cover **Natural Language Processing (NLP)** and how it can be applied in sales/customer datasets (like analyzing customer comments, product reviews, or service feedback).

---

# 🧑‍💻 Module 21: Natural Language Processing (NLP) for Data Analysis

**Goal**: Learn how to process, clean, and analyze **textual data** to extract insights.

---

## 21.1 What is NLP?

* NLP = **teaching computers to understand human language**.
* Use cases in sales/business:

  * Analyzing **customer reviews** (positive/negative)
  * Extracting **topics customers talk about**
  * Detecting **sentiment trends**
  * Automating **chatbots & support**

---

## 21.2 Example Dataset

If your sales dataset doesn’t have comments, you can simulate customer reviews:

```python
data = {
    "Customer": ["John", "Mary", "Ali", "Grace", "Hassan"],
    "Review": [
        "Great service and fast delivery!",
        "The product was broken when it arrived",
        "Excellent quality, will buy again",
        "Customer support was rude and unhelpful",
        "Loved the packaging and overall experience"
    ]
}
import pandas as pd
df = pd.DataFrame(data)
print(df)
```

---

## 21.3 Text Preprocessing

Steps:

1. Lowercasing
2. Removing punctuation & stopwords (the, is, and, etc.)
3. Tokenization (splitting words)
4. Lemmatization (convert words to base form)

```python
import nltk
from nltk.corpus import stopwords
from nltk.stem import WordNetLemmatizer
import re

nltk.download("stopwords")
nltk.download("punkt")
nltk.download("wordnet")

stop_words = set(stopwords.words("english"))
lemmatizer = WordNetLemmatizer()

def preprocess(text):
    text = text.lower()
    text = re.sub(r"[^a-z\s]", "", text)   # remove punctuation/numbers
    tokens = nltk.word_tokenize(text)
    tokens = [lemmatizer.lemmatize(word) for word in tokens if word not in stop_words]
    return " ".join(tokens)

df["Cleaned_Review"] = df["Review"].apply(preprocess)
print(df)
```

---

## 21.4 Sentiment Analysis

Using `textblob`:

```bash
pip install textblob
```

```python
from textblob import TextBlob

df["Sentiment"] = df["Review"].apply(lambda x: TextBlob(x).sentiment.polarity)

print(df[["Review", "Sentiment"]])
```

* Sentiment polarity:

  * `> 0` = Positive
  * `< 0` = Negative
  * `≈ 0` = Neutral

---

## 21.5 Word Frequency

```python
from collections import Counter

all_words = " ".join(df["Cleaned_Review"]).split()
word_counts = Counter(all_words)

print(word_counts.most_common(5))
```

✅ Shows most common words in customer feedback.

---

## 21.6 Word Cloud Visualization

```bash
pip install wordcloud
```

```python
from wordcloud import WordCloud
import matplotlib.pyplot as plt

wordcloud = WordCloud(width=800, height=400, background_color="white").generate(" ".join(df["Cleaned_Review"]))

plt.figure(figsize=(10,5))
plt.imshow(wordcloud, interpolation="bilinear")
plt.axis("off")
plt.show()
```

---

## 21.7 Topic Modeling (LDA)

Discover hidden **topics** in reviews:

```python
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.decomposition import LatentDirichletAllocation

vectorizer = CountVectorizer(max_df=0.95, min_df=2, stop_words="english")
X = vectorizer.fit_transform(df["Cleaned_Review"])

lda = LatentDirichletAllocation(n_components=2, random_state=42)
lda.fit(X)

for i, topic in enumerate(lda.components_):
    print(f"Topic {i}:")
    print([vectorizer.get_feature_names_out()[index] for index in topic.argsort()[-5:]])
```

---

## 21.8 Advanced: Transformers (BERT)

For high-quality sentiment and topic detection:

```bash
pip install transformers torch
```

```python
from transformers import pipeline

sentiment_model = pipeline("sentiment-analysis")
print(sentiment_model("I love the fast delivery but hate the packaging"))
```

✅ Outputs **positive/negative + confidence score**.

---

## 📝 Tasks for Practice

1. Run sentiment analysis on **all reviews**.
2. Create a **bar chart** of positive vs negative reviews.
3. Generate a **word cloud** for positive reviews only.
4. Use **topic modeling** to find 3 main themes in reviews.
5. Try a transformer model for better accuracy.

---

🎯 That wraps up **Module 21: NLP for Data Analysis**.
Now you can turn **customer text data into insights**.

---

Would you like me to continue with **Module 22: Recommendation Systems** (building a “customers who bought this also bought” type engine)?

Perfect — now you’re entering one of the most **exciting parts of applied data analysis** 🚀.
In **Module 22**, we’ll learn to build a **Recommendation System** — the same type of system used by **Amazon, Netflix, Spotify, etc.**

---

# 🧑‍💻 Module 22: Building a Recommendation System

**Goal**: Learn how to recommend products to customers based on their past purchases or preferences.

---

## 22.1 What is a Recommendation System?

Recommendation systems are algorithms that suggest items to users.
Two main types:

1. **Content-Based Filtering** – recommends items similar to those a user liked (based on features like product category).
2. **Collaborative Filtering** – recommends items based on what *similar users* liked.

We’ll try both using your sales dataset.

---

## 22.2 Sample Data Setup

We’ll use your `sales_data_sample.csv` (orders, customers, products).

```python
import pandas as pd

data = pd.read_csv("../../data/sales_data_sample.csv")

# Keep only useful columns
df = data[["CUSTOMERNAME", "PRODUCTCODE", "PRODUCTLINE", "SALES"]]
df.head()
```

---

## 22.3 Content-Based Filtering

We recommend products based on **PRODUCTLINE similarity**.

```python
# Example: Recommend other products from the same product line
customer = "Euro Shopping Channel"

# Get products bought by this customer
customer_products = df[df["CUSTOMERNAME"] == customer]["PRODUCTLINE"].unique()
print("Customer's product lines:", customer_products)

# Recommend other products in the same product lines
recommendations = df[df["PRODUCTLINE"].isin(customer_products)]["PRODUCTCODE"].unique()[:5]
print("Recommended Products:", recommendations)
```

✅ This gives quick, **category-based recommendations**.

---

## 22.4 Collaborative Filtering (User-Item Matrix)

We create a matrix where:

* Rows = Customers
* Columns = Products
* Values = Purchases (Sales)

```python
user_item_matrix = df.pivot_table(
    index="CUSTOMERNAME", 
    columns="PRODUCTCODE", 
    values="SALES", 
    aggfunc="sum"
).fillna(0)

print(user_item_matrix.head())
```

---

## 22.5 Cosine Similarity Between Customers

We find **similar customers** using cosine similarity.

```python
from sklearn.metrics.pairwise import cosine_similarity

# Compute similarity between customers
similarity_matrix = cosine_similarity(user_item_matrix)
similarity_df = pd.DataFrame(similarity_matrix, 
                             index=user_item_matrix.index, 
                             columns=user_item_matrix.index)

# Example: Find similar customers
customer = "Euro Shopping Channel"
similar_customers = similarity_df[customer].sort_values(ascending=False).head(5)
print("Customers similar to", customer, ":\n", similar_customers)
```

---

## 22.6 Making Recommendations

Now, recommend products that **similar customers bought but the target customer hasn’t**.

```python
target_customer = "Euro Shopping Channel"

# Find most similar customer
most_similar_customer = similar_customers.index[1]  # skip self
print("Most similar customer:", most_similar_customer)

# Products bought by similar customer
similar_customer_products = df[df["CUSTOMERNAME"] == most_similar_customer]["PRODUCTCODE"].unique()

# Products not yet bought by target customer
target_customer_products = df[df["CUSTOMERNAME"] == target_customer]["PRODUCTCODE"].unique()
recommended_products = list(set(similar_customer_products) - set(target_customer_products))

print("Recommended Products for", target_customer, ":", recommended_products[:5])
```

---

## 22.7 Advanced: Matrix Factorization (SVD)

A powerful approach to collaborative filtering.

```python
from sklearn.decomposition import TruncatedSVD

# Apply SVD
svd = TruncatedSVD(n_components=5, random_state=42)
latent_matrix = svd.fit_transform(user_item_matrix)

# Create customer embeddings
customer_embeddings = pd.DataFrame(latent_matrix, index=user_item_matrix.index)
print(customer_embeddings.head())
```

✅ This reduces dimensionality and lets you build scalable recommendation engines.

---

## 22.8 Evaluating Recommendation Systems

Metrics to check performance:

* **Precision\@K** – % of recommended items that were relevant
* **Recall\@K** – % of relevant items that were recommended
* **F1 Score** – balance of precision & recall

---

## 📝 Tasks for Practice

1. Write a function `recommend_products(customer_name)` that returns top 5 recommendations.
2. Try **content-based filtering** using `PRODUCTLINE` and compare results with collaborative filtering.
3. Build a **bar chart** showing top 5 recommended products and their sales values.
4. Experiment with **different similarity metrics** (Euclidean, Pearson).
5. Bonus: Try **movie dataset** (`movielens`) to practice recommendation systems.

---

🎯 That wraps up **Module 22: Recommendation Systems**.
Now you can build **personalized product suggestions** like big e-commerce companies!

---

Do you want me to continue with **Module 23: Time Series Forecasting** (predicting future sales, trends, and demand)?
Awesome 👌 — now we move into **forecasting the future** 📈.
In **Module 23**, we’ll cover **Time Series Forecasting** — predicting sales, demand, and trends using historical data.

---

# 🧑‍💻 Module 23: Time Series Forecasting with Python

**Goal**: Learn how to analyze time-based data and forecast future values (e.g., future sales).

---

## 23.1 What is Time Series?

* A **time series** is data collected at successive points in time (daily sales, monthly revenue, etc.).
* Key steps in time series analysis:

  1. Parse dates
  2. Resample data (daily, monthly, yearly)
  3. Decompose trends and seasonality
  4. Build forecasting models

---

## 23.2 Load Sales Data with Dates

Your dataset has an **ORDERDATE** column. Let’s convert it into proper datetime.

```python
import pandas as pd

data = pd.read_csv("../../data/sales_data_sample.csv", encoding="latin1")

# Convert ORDERDATE to datetime
data["ORDERDATE"] = pd.to_datetime(data["ORDERDATE"], errors="coerce")

# Check min and max dates
print("Date range:", data["ORDERDATE"].min(), "to", data["ORDERDATE"].max())
```

---

## 23.3 Resampling Sales Data

We’ll create **monthly sales totals**.

```python
monthly_sales = data.groupby("ORDERDATE")["SALES"].sum().resample("M").sum()

print(monthly_sales.head())
```

---

## 23.4 Plot Time Series

```python
import matplotlib.pyplot as plt

monthly_sales.plot(figsize=(10, 5), title="Monthly Sales")
plt.show()
```

✅ This gives a quick view of sales trends over time.

---

## 23.5 Decomposition of Time Series

Break down into **trend, seasonality, residuals**.

```python
from statsmodels.tsa.seasonal import seasonal_decompose

decomposition = seasonal_decompose(monthly_sales, model="additive")
decomposition.plot()
plt.show()
```

---

## 23.6 Forecasting with ARIMA

```bash
pip install pmdarima
```

```python
from pmdarima import auto_arima

# Fit ARIMA model
model = auto_arima(monthly_sales, seasonal=True, m=12)
forecast = model.predict(n_periods=6)

# Plot forecast
plt.figure(figsize=(10,5))
plt.plot(monthly_sales, label="History")
plt.plot(pd.date_range(monthly_sales.index[-1], periods=6, freq="M"), forecast, label="Forecast")
plt.legend()
plt.show()
```

✅ Forecasts next **6 months of sales**.

---

## 23.7 Forecasting with Prophet (Alternative)

```bash
pip install prophet
```

```python
from prophet import Prophet

# Prepare data for Prophet
df_prophet = monthly_sales.reset_index()
df_prophet.columns = ["ds", "y"]

# Fit model
model = Prophet()
model.fit(df_prophet)

# Future dataframe (next 6 months)
future = model.make_future_dataframe(periods=6, freq="M")
forecast = model.predict(future)

# Plot forecast
model.plot(forecast)
plt.show()

# Components (trend + seasonality)
model.plot_components(forecast)
plt.show()
```

---

## 23.8 Evaluating Forecast Accuracy

Split data into **train/test** and measure accuracy.

```python
train = monthly_sales[:-6]
test = monthly_sales[-6:]

model = auto_arima(train, seasonal=True, m=12)
forecast = model.predict(n_periods=6)

from sklearn.metrics import mean_absolute_error, mean_squared_error
import numpy as np

mae = mean_absolute_error(test, forecast)
rmse = np.sqrt(mean_squared_error(test, forecast))

print("MAE:", mae)
print("RMSE:", rmse)
```

✅ Helps you know how reliable the forecast is.

---

## 📝 Tasks for Practice

1. Resample **daily sales** and plot the trend.
2. Forecast the next **12 months** instead of 6.
3. Compare ARIMA vs Prophet results — which works better?
4. Plot **top 5 countries** and forecast their sales separately.
5. Bonus: Forecast **sales per product line** to see demand trends.

---

🎯 That wraps up **Module 23: Time Series Forecasting**.
Now you can **predict future sales and trends** for business planning.

---

Do you want me to continue with **Module 24: Data Pipelines & Automation** (automating data cleaning, transformations, and analysis workflows)?
