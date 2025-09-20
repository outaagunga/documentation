
We now have a CSV file (`sales_data_sample.csv`) that we can use as the foundation for our **practical, project-based code-along guide**.

Here’s how we’ll proceed:

* We'll use this dataset as the core example throughout all the modules.
* Each module will include step-by-step exercises and executable Python code.
* By the end, you'll have built a **complete data analysis project** using real data.

Let's start with **Module 1** and immediately integrate your uploaded file into the learning process.

---

## **Python for Data Analysis: Code-Along Guide**

**Project Theme:**
We will analyze **sales performance data** to answer key business questions, such as:

* Which product line generates the most revenue?
* How do sales vary by region and time period?
* Which customers are the most valuable?

---

### **Module 1: Introduction to Python and Data Analysis**

#### **1.1 Overview of Data Analysis**

Data analysis involves **collecting, cleaning, exploring, and visualizing data** to generate insights for decision-making.

**Types of Data Analysis:**

* **Descriptive:** What happened? (e.g., total sales last year)
* **Diagnostic:** Why did it happen? (e.g., drop in sales in Q2 due to low supply)
* **Predictive:** What will happen next? (e.g., forecast sales for next quarter)
* **Prescriptive:** What should we do? (e.g., offer discounts on low-performing products)

**Why Python?**

* Rich ecosystem of libraries: `pandas`, `numpy`, `matplotlib`, `seaborn`
* Easy to learn and write
* Open-source and widely adopted

---

#### **1.2 Setting Up Your Environment**

We'll use **Jupyter Notebook** for step-by-step coding and visualization.

**Required Libraries:**

```bash
pip install pandas numpy matplotlib seaborn openpyxl
```

---

#### **1.3 Python Basics Refresher**

Let's load your dataset and preview it as a quick warm-up.

```python
# Step 1: Import libraries
import pandas as pd

# Step 2: Load the dataset
df = pd.read_csv("sales_data_sample.csv", encoding='latin1')

# Step 3: Display first 5 rows
df.head()
```

**💡 Tip:**
The `head()` method quickly shows the top rows of a DataFrame.

---

### **Task 1: Explore Your Dataset**

1. Check the **shape** of the data (rows & columns).
2. Identify the **column names**.
3. Get **basic summary statistics**.

```python
# Shape of the dataset
print("Rows and Columns:", df.shape)

# Column names
print("Columns:", df.columns)

# Summary statistics
df.describe(include='all')
```

---

Absolutely! You can **follow this entire code-along guide directly in VS Code**.
We'll set up a clean workflow so you can write, run, and debug Python code step-by-step while analyzing your sales dataset.

Here’s a structured approach to get started:

---

## **Step 1: Setting Up VS Code for Data Analysis**

We'll configure VS Code as our main development environment.

### **1.1 Install VS Code**

* If you don't already have it, download VS Code from:
  [https://code.visualstudio.com/](https://code.visualstudio.com/)

---

### **1.2 Install Python**

* Install Python 3.10+
  [https://www.python.org/downloads/](https://www.python.org/downloads/)

---

### **1.3 Install Required VS Code Extensions**

Go to **Extensions** (`Ctrl + Shift + X` or `Cmd + Shift + X` on Mac) and install:

1. **Python** (by Microsoft) – enables Python features like IntelliSense, linting, and debugging.
2. **Jupyter** – allows you to run notebook-style cells directly in VS Code.
3. **Pylance** – for enhanced Python IntelliSense.
4. **GitLens** *(optional)* – for version control if you plan to use Git.

---

### **1.4 Create a Project Folder**

Let's organize everything neatly:

```plaintext
python-data-analysis/
│
├── data/                # raw data files
│   └── sales_data_sample.csv
│
├── notebooks/           # optional Jupyter notebooks
│   └── exploration.ipynb
│
├── scripts/             # python scripts
│   └── data_cleaning.py
│
├── venv/                # virtual environment (auto-generated)
│
└── main.py              # entry point for your project
```

Move your uploaded `sales_data_sample.csv` into the **data/** folder.

---

### **1.5 Create a Virtual Environment**

Virtual environments keep your dependencies clean and project-specific.

In your **project root folder** (`python-data-analysis`), open a terminal and run:

```bash
# Create a virtual environment
python -m venv venv

# Activate it
# Windows:
venv\Scripts\activate

# Mac/Linux:
source venv/bin/activate
```

> 💡 **Tip:** You'll know it's activated when you see `(venv)` at the start of your terminal prompt.

---

### **1.6 Install Required Libraries**

With your virtual environment activated, install the essential data analysis libraries:

```bash
pip install pandas numpy matplotlib seaborn openpyxl jupyter
```

To freeze dependencies into a `requirements.txt` file:

```bash
pip freeze > requirements.txt
```

---

### **1.7 Configure VS Code Python Interpreter**

* **Ctrl + Shift + P** → *"Python: Select Interpreter"*
* Choose the interpreter inside your `venv` folder.

---

## **Step 2: Writing Your First Analysis Script**

We'll start by loading and exploring your `sales_data_sample.csv`.

Create a new file: `scripts/load_data.py`

```python
# scripts/load_data.py
import pandas as pd

# Step 1: Load the dataset
df = pd.read_csv("../data/sales_data_sample.csv", encoding='latin1')

# Step 2: Preview the data
print("First 5 rows of the dataset:")
print(df.head())

# Step 3: Basic info
print("\nDataset Shape:", df.shape)
print("\nColumn Names:", df.columns.tolist())

# Step 4: Summary statistics
print("\nSummary Statistics:")
print(df.describe(include='all'))
```

Run it from the terminal:

```bash
python scripts/load_data.py
```

---

## **Step 3: Using Jupyter-Like Cells in VS Code**

You can run code like a Jupyter Notebook **without leaving VS Code**:

1. Open any `.py` file.
2. Add **cell markers** like this:

```python
# %% [markdown]
# ### Load and Inspect Data

# %% 
import pandas as pd
df = pd.read_csv("data/sales_data_sample.csv", encoding='latin1')
df.head()

# %% [markdown]
# ### Check dataset info

# %%
df.info()
```

3. Hover over a cell and click **Run Cell** ▶️.
   VS Code will open an **interactive window** (like Jupyter Notebook).

---

## **Step 4: Your First Mini Data Analysis**

Let's add more functionality to your project step-by-step.

Create a file: `scripts/basic_analysis.py`

```python
# scripts/basic_analysis.py

import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load data
df = pd.read_csv("../data/sales_data_sample.csv", encoding='latin1')

# Check null values
print("Null values in each column:")
print(df.isnull().sum())

# Total sales by product line
sales_by_product = df.groupby('PRODUCTLINE')['SALES'].sum().sort_values(ascending=False)
print("\nSales by Product Line:")
print(sales_by_product)

# Visualize the sales by product line
plt.figure(figsize=(10, 5))
sns.barplot(x=sales_by_product.index, y=sales_by_product.values)
plt.title('Total Sales by Product Line')
plt.xticks(rotation=45)
plt.ylabel('Total Sales')
plt.show()
```

Run it:

```bash
python scripts/basic_analysis.py
```

---

## **Step 5: Best Workflow Tips**

* **Use Git for version control**

  ```bash
  git init
  git add .
  git commit -m "Initial commit - sales data project setup"
  ```

* **Split your project into reusable modules**:

  * `data_loading.py` – load raw data
  * `data_cleaning.py` – clean and prepare data
  * `visualization.py` – all charts and plots

* **Comment and document everything**
  This makes it easier to revisit your project later.

---

## **Next Steps**

In **Module 2**, we will:

* Dive deeper into **Python data structures** (`lists`, `dicts`, etc.).
* Create helper functions to clean and transform your dataset.
* Start building reusable scripts for the project.

Would you like me to prepare a **detailed coding checklist** for Module 2 so you can follow along step-by-step inside VS Code?

Let's move on to **Module 2**, where we’ll focus on **core Python foundations** for data analysis while continuing to use your `sales_data_sample.csv` dataset.

By the end of this module, you will:

* Understand Python’s essential data structures.
* Practice manipulating text and numerical data.
* Learn how to read, write, and manage files effectively.
* Build **reusable functions and modules** to organize your project.

We'll do this step-by-step inside **VS Code**, in a practical, code-along format.

---

# **Module 2: Core Python Foundations for Data Analysis**

---

## **2.1 Data Structures**

Python has several built-in data structures, each useful for different data manipulation tasks.

---

### **2.1.1 Lists**

Lists store ordered, mutable collections of items.
We'll use lists to represent product categories, regions, etc.

**Example: Product Lines**

```python
# scripts/data_structures.py

# %% [markdown]
# ### Working with Lists

# %%
# Create a list of product lines
product_lines = ['Classic Cars', 'Motorcycles', 'Planes', 'Ships', 'Trains', 'Trucks and Buses']

# Display the list
print("Product Lines:", product_lines)

# Access the first product line
print("First product line:", product_lines[0])

# Add a new product line
product_lines.append('Vintage Cars')
print("After adding new line:", product_lines)

# Remove a product line
product_lines.remove('Ships')
print("After removing Ships:", product_lines)

# Sorting product lines
product_lines.sort()
print("Sorted product lines:", product_lines)
```

> **Use Case in Data Analysis:**
> A list can store unique product categories extracted from a dataset for filtering or reporting.

---

### **2.1.2 Tuples**

Tuples are **immutable** ordered collections — useful when you don’t want data to change.

**Example:**

```python
# %%
# Tuple of region codes
regions = ('APAC', 'EMEA', 'NA')

print("Regions:", regions)
print("First Region:", regions[0])
```

---

### **2.1.3 Sets**

Sets store **unique, unordered items**, making them perfect for de-duplication.

```python
# %%
# Extract unique countries from the dataset
import pandas as pd

df = pd.read_csv("../data/sales_data_sample.csv", encoding='latin1')

unique_countries = set(df['COUNTRY'])
print("Unique countries:", unique_countries)
print("Number of unique countries:", len(unique_countries))
```

---

### **2.1.4 Dictionaries**

Dictionaries store **key-value pairs**, ideal for mappings.

```python
# %%
# Create a dictionary of currency conversion rates
conversion_rates = {
    'USD': 1.0,
    'EUR': 0.92,
    'GBP': 0.80
}

# Convert 100 USD to EUR
usd_amount = 100
eur_amount = usd_amount * conversion_rates['EUR']
print(f"100 USD is equal to {eur_amount} EUR")
```

> **Real Use Case:**
> Map country names to region codes or tax rates.

---

### **2.1.5 Choosing the Right Data Structure**

| Data Structure | Best For                     | Example Use Case                          |
| -------------- | ---------------------------- | ----------------------------------------- |
| **List**       | Ordered, mutable collections | Storing ordered sales records             |
| **Tuple**      | Fixed collections            | Defining product code + description pairs |
| **Set**        | Unique, unordered data       | Unique customer IDs                       |
| **Dict**       | Key-value lookups            | Country → Tax rate mapping                |

---

## **2.2 Working with Strings**

We often need to **clean and format text data**, such as product names or customer addresses.

---

### **2.2.1 String Basics**

```python
# scripts/string_basics.py

# %%
customer_name = "John Doe"
print(customer_name.upper())  # Uppercase
print(customer_name.lower())  # Lowercase
print(customer_name.split())  # Split into list
```

---

### **2.2.2 String Methods in Context**

Example: cleaning product codes in your dataset.

```python
# %%
df['PRODUCTCODE'] = df['PRODUCTCODE'].str.strip().str.upper()
print(df['PRODUCTCODE'].head())
```

---

### **2.2.3 Formatting Strings**

F-strings are the modern way to format strings in Python.

```python
# %%
sales = 15000
region = "APAC"

message = f"Total sales in {region} were ${sales:,}"
print(message)
```

---

### **2.2.4 Regular Expressions for Pattern Matching**

Find product codes that start with `"S"`:

```python
# %%
import re

sample_codes = df['PRODUCTCODE'].unique()
codes_starting_with_s = [code for code in sample_codes if re.match(r'^S', code)]
print("Product codes starting with 'S':", codes_starting_with_s)
```

---

## **2.3 File Handling**

Being able to read, write, and manage files is crucial for data projects.

---

### **2.3.1 Reading & Writing Text Files**

```python
# scripts/file_handling.py

# %%
# Write a simple log file
with open("../data/log.txt", "w") as file:
    file.write("Data analysis started...\n")

# Read the file back
with open("../data/log.txt", "r") as file:
    print(file.read())
```

---

### **2.3.2 Working with CSV Files**

```python
# %%
# Save cleaned sales dataset
df.to_csv("../data/cleaned_sales.csv", index=False)

# Load the cleaned file
cleaned_df = pd.read_csv("../data/cleaned_sales.csv")
print("Loaded cleaned data:", cleaned_df.head())
```

---

### **2.3.3 Handling JSON Files**

```python
# %%
import json

data = {
    "region": "NA",
    "total_sales": 50000
}

# Write JSON
with open("../data/sales_summary.json", "w") as json_file:
    json.dump(data, json_file, indent=4)

# Read JSON
with open("../data/sales_summary.json", "r") as json_file:
    loaded_data = json.load(json_file)
    print(loaded_data)
```

---

### **2.3.4 Reading & Writing Excel Files**

```python
# %%
# Save to Excel
df.to_excel("../data/cleaned_sales.xlsx", index=False)

# Load from Excel
excel_df = pd.read_excel("../data/cleaned_sales.xlsx")
print(excel_df.head())
```

---

### **2.3.5 Best Practices**

* Keep raw data **read-only**.
* Always save cleaned datasets in a **separate file**.
* Use clear, descriptive filenames like `cleaned_sales_2025_Q1.csv`.

---

## **2.4 Python Functions and Modules**

Functions help you **organize repetitive tasks** into reusable code.

---

### **2.4.1 Creating Reusable Functions**

```python
# scripts/helpers.py

def calculate_total_sales(df):
    """Calculate total sales from a dataset"""
    return df['SALES'].sum()
```

**Usage:**

```python
# %%
from helpers import calculate_total_sales

total = calculate_total_sales(df)
print(f"Total Sales: {total}")
```

---

### **2.4.2 Lambda Functions**

Quick, one-line anonymous functions.

```python
# %%
df['DISCOUNTED_SALES'] = df['SALES'].apply(lambda x: x * 0.90)
print(df[['SALES', 'DISCOUNTED_SALES']].head())
```

---

### **2.4.3 Importing and Using Modules**

* Any `.py` file in your project folder can be imported as a module.

```python
import helpers

total_sales = helpers.calculate_total_sales(df)
print("Total Sales via module:", total_sales)
```

---

### **2.4.4 Creating Custom Modules**

**Folder structure:**

```
scripts/
│
├── __init__.py
├── data_loading.py
├── data_cleaning.py
└── visualization.py
```

`__init__.py` makes the folder a **package**, allowing clean imports like:

```python
from scripts.data_loading import load_sales_data
```

---

### **2.4.5 Virtual Environments Recap**

* Virtual environments keep dependencies **project-specific**.
* Update dependencies regularly using:

```bash
pip install -U pandas numpy matplotlib seaborn
```

---

## **Mini Project for Module 2**

Goal: Clean and prepare the sales dataset using functions and modules.

**Steps:**

1. Load raw data from CSV.
2. Remove leading/trailing spaces in all string columns.
3. Standardize column names (lowercase, underscores).
4. Save the cleaned dataset as `cleaned_sales.csv`.

---

### **Example Code**

`scripts/data_cleaning.py`

```python
import pandas as pd

def load_raw_sales_data(filepath="../data/sales_data_sample.csv"):
    return pd.read_csv(filepath, encoding='latin1')

def clean_sales_data(df):
    # Strip whitespace and lowercase all string columns
    for col in df.select_dtypes(include=['object']).columns:
        df[col] = df[col].str.strip().str.lower()
    return df

def save_cleaned_data(df, filepath="../data/cleaned_sales.csv"):
    df.to_csv(filepath, index=False)

if __name__ == "__main__":
    data = load_raw_sales_data()
    cleaned = clean_sales_data(data)
    save_cleaned_data(cleaned)
    print("Cleaned sales data saved successfully.")
```

Run it in terminal:

```bash
python scripts/data_cleaning.py
```

---

## **Next Steps (Module 3 Preview)**

In **Module 3**, we’ll:

* Dive deeper into **NumPy** for numerical analysis.
* Master **Pandas** for advanced data manipulation.
* Learn **Matplotlib** and **Seaborn** for professional visualizations.

Would you like me to start Module 3 by focusing on **NumPy arrays and practical dataset manipulation**?

Let's dive into **Module 3**, where we focus on **essential libraries for data analysis**.

This is where the real power of Python for data analysis comes in — working with **NumPy**, **Pandas**, and **visualization libraries** to manipulate, clean, and visualize your dataset.

We'll continue to use your `sales_data_sample.csv` file, which we've already cleaned in the previous module, stored as `cleaned_sales.csv` in the `data/` folder.

---

# **Module 3: Essential Libraries for Data Analysis**

---

## **3.1 NumPy: Numerical Computing**

NumPy is the foundation of numerical computing in Python.

* It introduces **arrays** that are faster and more memory-efficient than Python lists.
* Supports vectorized operations (perform calculations on entire datasets without loops).

---

### **3.1.1 Introduction to NumPy and Arrays**

**Install NumPy (if not already installed):**

```bash
pip install numpy
```

**Example:**

```python
# scripts/numpy_basics.py
import numpy as np

# Create a NumPy array
sales_array = np.array([100, 200, 300, 400, 500])

print("Sales Array:", sales_array)
print("Type:", type(sales_array))
print("Shape:", sales_array.shape)
```

> **Why NumPy?**
>
> * Faster than Python lists
> * Built-in statistical and mathematical operations
> * Foundation for Pandas and other libraries

---

### **3.1.2 Creating Arrays**

```python
# %%
import numpy as np

# From a list
arr_from_list = np.array([1, 2, 3, 4, 5])

# Create arrays with zeros or ones
zeros_array = np.zeros((3, 4))  # 3x4 matrix of zeros
ones_array = np.ones((2, 2))    # 2x2 matrix of ones

# Create a range of numbers
range_array = np.arange(0, 10, 2)  # start, stop, step

print("Zeros Array:\n", zeros_array)
print("Ones Array:\n", ones_array)
print("Range Array:", range_array)
```

---

### **3.1.3 Indexing and Slicing**

```python
# %%
sales_array = np.array([100, 200, 300, 400, 500])

# Indexing
print("First element:", sales_array[0])

# Slicing
print("First three elements:", sales_array[:3])

# Updating
sales_array[0] = 999
print("Updated Array:", sales_array)
```

---

### **3.1.4 Array Operations**

NumPy makes it easy to do math without loops.

```python
# %%
sales = np.array([100, 200, 300])
costs = np.array([50, 120, 150])

profit = sales - costs
print("Profit:", profit)

# Element-wise multiplication
taxed_sales = sales * 1.1
print("Taxed Sales:", taxed_sales)

# Statistical operations
print("Mean sales:", sales.mean())
print("Max sales:", sales.max())
print("Sum of sales:", sales.sum())
```

---

### **3.1.5 Broadcasting**

Broadcasting allows operations between arrays of different shapes.

```python
# %%
sales = np.array([100, 200, 300])
tax_rate = 0.1

# NumPy automatically applies tax_rate to every element
sales_with_tax = sales + (sales * tax_rate)
print("Sales with Tax:", sales_with_tax)
```

---

### **3.1.6 Random Number Generation**

Useful for simulations, sampling, and testing.

```python
# %%
random_sales = np.random.randint(100, 1000, size=10)
print("Random Sales Data:", random_sales)
```

---

### **3.1.7 Practical Use Case with Your Dataset**

We can use NumPy with Pandas for performance.

```python
# %%
import pandas as pd
import numpy as np

df = pd.read_csv("../data/cleaned_sales.csv")

# Convert 'SALES' column to NumPy array
sales_array = df['SALES'].values

print("NumPy Sales Array:", sales_array[:10])

# Calculate total sales using NumPy
total_sales = np.sum(sales_array)
average_sales = np.mean(sales_array)

print("Total Sales:", total_sales)
print("Average Sales:", average_sales)
```

---

## **3.2 Pandas: Data Manipulation**

Pandas is the **go-to library for data analysis** in Python.

* It builds on NumPy to offer **Series** and **DataFrames** for tabular data.
* Handles importing, cleaning, transforming, and exporting data.

---

### **3.2.1 Series and DataFrames**

```python
# scripts/pandas_basics.py
import pandas as pd

# Create a Series
sales_series = pd.Series([100, 200, 300], name="SALES")
print("Series:\n", sales_series)

# Create a DataFrame from a dictionary
data = {
    "PRODUCT": ["Car", "Truck", "Plane"],
    "SALES": [1000, 1500, 2000]
}
df_manual = pd.DataFrame(data)
print("\nDataFrame:\n", df_manual)
```

---

### **3.2.2 Reading and Writing Data**

We'll continue using your cleaned dataset.

```python
# %%
# Load CSV
df = pd.read_csv("../data/cleaned_sales.csv")

# Save to Excel
df.to_excel("../data/sales_output.xlsx", index=False)

# Save to JSON
df.to_json("../data/sales_output.json", orient="records", indent=4)
```

---

### **3.2.3 Data Cleaning Basics**

#### **Handling Missing Values**

```python
# %%
# Check for missing values
print(df.isnull().sum())

# Drop rows with missing values
df.dropna(inplace=True)

# Fill missing values with zero
df.fillna(0, inplace=True)
```

#### **Dropping Duplicates**

```python
# %%
# Remove duplicate rows
df.drop_duplicates(inplace=True)
```

#### **Data Type Conversion**

```python
# %%
# Convert 'ORDERDATE' to datetime
df['ORDERDATE'] = pd.to_datetime(df['ORDERDATE'])
```

---

### **3.2.4 Filtering and Selecting Data**

```python
# %%
# Filter sales greater than 5000
high_sales = df[df['SALES'] > 5000]
print(high_sales.head())

# Select specific columns
product_sales = df[['PRODUCTLINE', 'SALES']]
print(product_sales.head())
```

---

### **3.2.5 Grouping and Aggregation**

Find **total sales per product line**:

```python
# %%
grouped = df.groupby('PRODUCTLINE')['SALES'].sum().sort_values(ascending=False)
print(grouped)
```

---

### **3.2.6 Merging and Joining DataFrames**

Example with a dummy dataset.

```python
# %%
# Create sample dataframes
df_customers = pd.DataFrame({
    'CustomerID': [1, 2, 3],
    'CustomerName': ['Alice', 'Bob', 'Charlie']
})

df_orders = pd.DataFrame({
    'CustomerID': [1, 2, 2, 3],
    'OrderAmount': [200, 150, 400, 300]
})

# Merge them
merged_df = pd.merge(df_customers, df_orders, on='CustomerID', how='inner')
print(merged_df)
```

---

### **3.2.7 Reshaping Data**

Pivot sales data by year and product line.

```python
# %%
pivot_table = pd.pivot_table(df, values='SALES', index='YEAR_ID', columns='PRODUCTLINE', aggfunc='sum')
print(pivot_table)
```

---

### **3.2.8 Pandas Performance Tips**

* Use `.loc` and `.iloc` for efficient indexing.
* Convert text columns to `category` type for memory savings:

```python
df['PRODUCTLINE'] = df['PRODUCTLINE'].astype('category')
```

---

## **3.3 Data Visualization**

Visualizations help you **understand trends and patterns** in data.

We'll use:

* **Matplotlib** for basic plotting
* **Seaborn** for advanced statistical plots

---

### **3.3.1 Introduction to Data Visualization**

Install libraries:

```bash
pip install matplotlib seaborn
```

---

### **3.3.2 Basic Plotting with Matplotlib**

```python
# scripts/basic_plots.py
import matplotlib.pyplot as plt

# Simple line plot
sales = [100, 200, 300, 400, 500]
plt.plot(sales)
plt.title("Sales Growth")
plt.xlabel("Months")
plt.ylabel("Sales")
plt.show()
```

---

### **3.3.3 Bar Chart Example with Real Data**

```python
# %%
import seaborn as sns

# Total sales by product line
sales_by_product = df.groupby('PRODUCTLINE')['SALES'].sum().sort_values(ascending=False)

plt.figure(figsize=(10, 5))
sns.barplot(x=sales_by_product.index, y=sales_by_product.values)
plt.title("Total Sales by Product Line")
plt.xticks(rotation=45)
plt.show()
```

---

### **3.3.4 Seaborn for Advanced Visuals**

#### **Heatmap: Correlation**

```python
# %%
plt.figure(figsize=(8, 5))
sns.heatmap(df.corr(), annot=True, cmap='Blues')
plt.title("Correlation Heatmap")
plt.show()
```

#### **Pair Plot**

```python
# %%
sns.pairplot(df[['SALES', 'QUANTITYORDERED', 'PRICEEACH']])
plt.show()
```

---

### **3.3.5 Best Practices for Visualization**

* Keep it simple and clear.
* Use titles, labels, and legends.
* Avoid cluttered plots.
* Match visualization type to the data.

---

## **Mini Project for Module 3**

**Goal:** Create a dashboard script that:

1. Loads cleaned sales data.
2. Generates:

   * Bar chart of total sales by product line.
   * Line chart of sales over time.
   * Heatmap of correlations.

`scripts/dashboard.py`

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load cleaned data
df = pd.read_csv("../data/cleaned_sales.csv")
df['ORDERDATE'] = pd.to_datetime(df['ORDERDATE'])

# Bar chart: sales by product line
plt.figure(figsize=(10, 5))
sales_by_product = df.groupby('PRODUCTLINE')['SALES'].sum().sort_values(ascending=False)
sns.barplot(x=sales_by_product.index, y=sales_by_product.values)
plt.title("Total Sales by Product Line")
plt.xticks(rotation=45)
plt.show()

# Line chart: sales over time
plt.figure(figsize=(10, 5))
sales_by_month = df.groupby(df['ORDERDATE'].dt.to_period("M"))['SALES'].sum()
sales_by_month.plot()
plt.title("Sales Over Time")
plt.xlabel("Month")
plt.ylabel("Total Sales")
plt.show()

# Heatmap: correlation
plt.figure(figsize=(8, 5))
sns.heatmap(df.corr(), annot=True, cmap='coolwarm')
plt.title("Correlation Heatmap")
plt.show()
```

Run it:

```bash
python scripts/dashboard.py
```

---

## **Next Steps (Module 4 Preview)**

In **Module 4**, we’ll:

* Deep dive into **data wrangling and cleaning**.
* Handle **missing data**, **outliers**, and **feature engineering**.
* Build **automated cleaning pipelines** for consistent data preparation.

Would you like me to start Module 4 by showing how to detect and handle missing data in your sales dataset?

Let's move into **Module 4**, which focuses on **Data Wrangling and Cleaning**.

This is a **critical step** in any data analysis project because **clean data = accurate insights**.
We'll build on our `cleaned_sales.csv` dataset and focus on **identifying, handling, and preparing data** for advanced analysis or machine learning.

---

# **Module 4: Data Wrangling and Cleaning**

---

## **4.1 Introduction to Data Wrangling**

Data wrangling is the process of **transforming raw data** into a clean, structured format suitable for analysis.

**Key steps in data wrangling:**

1. **Loading raw data** into Pandas.
2. **Inspecting data** to understand its structure and quality.
3. **Identifying issues** like missing values, duplicates, and outliers.
4. **Cleaning and transforming data** using systematic methods.
5. **Exporting cleaned data** for future use.

> *Goal:* By the end of this module, you'll have a **clean, reliable dataset** ready for analysis or modeling.

---

## **4.2 Setting Up**

We'll use your previously cleaned sales file but now apply **deep cleaning** and **transformations**.

**Folder structure:**

```
project/
│
├── data/
│   ├── raw_sales.csv
│   ├── cleaned_sales.csv
│
├── scripts/
│   ├── clean_data.py
│
└── notebooks/
    └── data_wrangling.ipynb
```

---

## **4.3 Step 1: Inspecting the Dataset**

We'll start by looking for **hidden issues** that may still exist.

```python
# scripts/clean_data.py
import pandas as pd

# Load data
df = pd.read_csv("../data/cleaned_sales.csv")

# Display first few rows
print("Preview of dataset:")
print(df.head())

# Get general info
print("\nDataset Info:")
print(df.info())

# Summary statistics
print("\nSummary Statistics:")
print(df.describe(include='all'))
```

---

## **4.4 Step 2: Handling Missing Data**

### **4.4.1 Identifying Missing Data**

```python
# %%
missing_values = df.isnull().sum()
print("Missing values per column:\n", missing_values)
```

---

### **4.4.2 Dropping Missing Data**

If the missing data is small and random, you can **drop it**:

```python
# %%
df.dropna(inplace=True)
```

---

### **4.4.3 Filling Missing Data**

If missing data is **significant**, fill with appropriate values:

* **Numeric columns:** use mean, median, or mode.
* **Categorical columns:** use the most common category or "Unknown".

```python
# %%
# Fill numeric columns with median
df['SALES'].fillna(df['SALES'].median(), inplace=True)

# Fill categorical columns with 'Unknown'
df['PRODUCTLINE'].fillna('Unknown', inplace=True)
```

---

### **4.4.4 Advanced Fill: Forward or Backward Fill**

Useful for **time series** data.

```python
# %%
df.sort_values('ORDERDATE', inplace=True)
df['SALES'].fillna(method='ffill', inplace=True)  # forward fill
```

---

## **4.5 Step 3: Handling Duplicates**

Duplicate rows can **inflate your results** if not handled.

```python
# %%
duplicates = df.duplicated().sum()
print("Number of duplicate rows:", duplicates)

# Drop duplicates
df.drop_duplicates(inplace=True)
```

---

## **4.6 Step 4: Handling Outliers**

Outliers can **distort averages** and **mislead analysis**.

### **4.6.1 Visualizing Outliers**

Using boxplot:

```python
# %%
import matplotlib.pyplot as plt
import seaborn as sns

plt.figure(figsize=(8, 5))
sns.boxplot(x=df['SALES'])
plt.title("Sales Outlier Detection")
plt.show()
```

---

### **4.6.2 Detecting Outliers with IQR**

```python
# %%
Q1 = df['SALES'].quantile(0.25)
Q3 = df['SALES'].quantile(0.75)
IQR = Q3 - Q1

# Define outlier boundaries
lower_bound = Q1 - (1.5 * IQR)
upper_bound = Q3 + (1.5 * IQR)

# Identify outliers
outliers = df[(df['SALES'] < lower_bound) | (df['SALES'] > upper_bound)]
print("Number of outliers:", len(outliers))

# Remove outliers
df = df[(df['SALES'] >= lower_bound) & (df['SALES'] <= upper_bound)]
```

---

## **4.7 Step 5: Standardizing and Normalizing Data**

### **4.7.1 Standardization**

Useful when preparing data for machine learning.

```python
# %%
from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()
df['SALES_STANDARDIZED'] = scaler.fit_transform(df[['SALES']])
```

---

### **4.7.2 Normalization**

Rescale values to **0–1 range**.

```python
# %%
from sklearn.preprocessing import MinMaxScaler

scaler = MinMaxScaler()
df['SALES_NORMALIZED'] = scaler.fit_transform(df[['SALES']])
```

---

## **4.8 Step 6: Feature Engineering**

Feature engineering **creates new variables** that can provide more insights.

---

### **4.8.1 Extract Date Components**

```python
# %%
df['ORDERDATE'] = pd.to_datetime(df['ORDERDATE'])
df['ORDER_YEAR'] = df['ORDERDATE'].dt.year
df['ORDER_MONTH'] = df['ORDERDATE'].dt.month
df['ORDER_DAY'] = df['ORDERDATE'].dt.day
```

---

### **4.8.2 Create Profit Feature**

If you have cost data, calculate profit:

```python
# %%
df['COST'] = df['SALES'] * 0.6  # assuming 40% profit margin
df['PROFIT'] = df['SALES'] - df['COST']
```

---

### **4.8.3 Sales Category Binning**

Classify sales into categories:

```python
# %%
df['SALES_CATEGORY'] = pd.cut(
    df['SALES'],
    bins=[0, 1000, 5000, 10000],
    labels=['Low', 'Medium', 'High']
)
```

---

## **4.9 Step 7: Encoding Categorical Variables**

Machine learning models require numeric data.

```python
# %%
df = pd.get_dummies(df, columns=['PRODUCTLINE', 'SALES_CATEGORY'], drop_first=True)
```

---

## **4.10 Step 8: Finalizing and Exporting Clean Data**

```python
# %%
df.to_csv("../data/final_clean_sales.csv", index=False)
print("Final cleaned dataset saved to final_clean_sales.csv")
```

---

## **4.11 Automation: Create a Cleaning Script**

Instead of repeating steps, automate them.

```python
# scripts/data_cleaning_pipeline.py
import pandas as pd
from sklearn.preprocessing import MinMaxScaler

def clean_sales_data(input_path, output_path):
    df = pd.read_csv(input_path)

    # Handle missing data
    df.fillna({'SALES': df['SALES'].median(), 'PRODUCTLINE': 'Unknown'}, inplace=True)

    # Remove duplicates
    df.drop_duplicates(inplace=True)

    # Normalize SALES
    scaler = MinMaxScaler()
    df['SALES_NORMALIZED'] = scaler.fit_transform(df[['SALES']])

    # Feature engineering
    df['ORDERDATE'] = pd.to_datetime(df['ORDERDATE'])
    df['ORDER_YEAR'] = df['ORDERDATE'].dt.year
    df['ORDER_MONTH'] = df['ORDERDATE'].dt.month

    # Export
    df.to_csv(output_path, index=False)
    print(f"Cleaned data exported to {output_path}")

# Run
if __name__ == "__main__":
    clean_sales_data("../data/cleaned_sales.csv", "../data/final_clean_sales.csv")
```

Run it:

```bash
python scripts/data_cleaning_pipeline.py
```

---

## **4.12 Mini Project: Automated Cleaning Pipeline**

**Goal:**
Create a **fully automated cleaning pipeline** that:

1. Loads raw data.
2. Cleans missing values, duplicates, and outliers.
3. Generates engineered features.
4. Exports the final dataset for analysis.

---

## **4.13 Checklist Before Moving Forward**

Before moving to the next module, make sure you can:

* [ ] Identify and fix missing values.
* [ ] Detect and remove duplicates.
* [ ] Detect and handle outliers using IQR.
* [ ] Normalize and standardize data.
* [ ] Engineer new features (dates, profit, categories).
* [ ] Encode categorical variables.
* [ ] Automate the cleaning process with a Python script.

---

## **4.14 Next Steps (Module 5 Preview)**

In **Module 5**, we’ll focus on:

* **Exploratory Data Analysis (EDA)** using Pandas, Matplotlib, and Seaborn.
* Visualizing **trends, patterns, and distributions**.
* Building **interactive dashboards** with Plotly.

Would you like me to start Module 5 by guiding you on how to perform EDA step-by-step?

Let's dive into **Module 5: Exploratory Data Analysis (EDA)**.

This module is all about **understanding your dataset**, uncovering trends, relationships, and patterns through **statistical summaries and visualizations**.
We'll use the cleaned dataset you created in Module 4: `final_clean_sales.csv`.

By the end of this module, you'll be able to:

* Summarize and describe data effectively.
* Detect hidden relationships between features.
* Visualize data using various plots and techniques.
* Build an EDA report for stakeholders.

---

# **Module 5: Exploratory Data Analysis (EDA)**

---

## **5.1 Introduction to EDA**

**Exploratory Data Analysis (EDA)** is the process of **exploring and understanding data before modeling or reporting**.

> *Goal:*
> Identify important patterns, detect anomalies, test assumptions, and decide next steps.

---

### **Why EDA is Important**

1. Detect **errors or outliers** missed during cleaning.
2. Understand **relationships between variables**.
3. Formulate **hypotheses** for further analysis.
4. Choose the right **visualization techniques**.
5. Communicate insights **clearly to stakeholders**.

---

## **5.2 Setting Up**

Make sure you are in your project folder:

```
project/
│
├── data/
│   └── final_clean_sales.csv
│
├── notebooks/
│   └── eda.ipynb
│
└── scripts/
    └── generate_eda_report.py
```

We'll primarily use **Jupyter Notebook** for this module.

Install required libraries (if not installed):

```bash
pip install matplotlib seaborn pandas numpy plotly
```

---

## **5.3 Loading the Dataset**

```python
# notebooks/eda.ipynb
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load data
df = pd.read_csv("../data/final_clean_sales.csv")

# Display basic info
df.head()
```

---

## **5.4 Understanding the Dataset**

### **5.4.1 Shape and Columns**

```python
print("Shape of dataset:", df.shape)
print("Columns:", df.columns.tolist())
```

---

### **5.4.2 Data Types**

```python
df.dtypes
```

---

### **5.4.3 Summary Statistics**

```python
df.describe(include='all')
```

---

### **5.4.4 Quick Insights**

```python
print("Missing values per column:\n", df.isnull().sum())
print("\nDuplicate rows:", df.duplicated().sum())
print("\nUnique values per column:\n", df.nunique())
```

---

## **5.5 Descriptive Statistics**

### **5.5.1 Mean, Median, Mode**

```python
print("Mean Sales:", df['SALES'].mean())
print("Median Sales:", df['SALES'].median())
print("Mode Sales:", df['SALES'].mode()[0])
```

---

### **5.5.2 Standard Deviation & Variance**

```python
print("Standard Deviation of Sales:", df['SALES'].std())
print("Variance of Sales:", df['SALES'].var())
```

---

### **5.5.3 Skewness & Kurtosis**

* **Skewness** → symmetry of data distribution.
* **Kurtosis** → heaviness of tails.

```python
print("Sales Skewness:", df['SALES'].skew())
print("Sales Kurtosis:", df['SALES'].kurt())
```

> **Rule of thumb:**
>
> * Skewness near **0** → Normal distribution.
> * Kurtosis > **3** → Heavy tails (outliers present).

---

## **5.6 Visual EDA with Matplotlib & Seaborn**

We'll use **visualizations** to uncover hidden patterns.

---

### **5.6.1 Sales Distribution**

```python
plt.figure(figsize=(8, 5))
sns.histplot(df['SALES'], bins=30, kde=True)
plt.title('Sales Distribution')
plt.xlabel('Sales Amount')
plt.ylabel('Frequency')
plt.show()
```

---

### **5.6.2 Boxplot to Detect Outliers**

```python
plt.figure(figsize=(8, 5))
sns.boxplot(x=df['SALES'])
plt.title("Sales Outlier Detection")
plt.show()
```

---

### **5.6.3 Sales by Product Line**

```python
plt.figure(figsize=(10, 6))
sns.barplot(x='PRODUCTLINE', y='SALES', data=df, estimator=sum, ci=None)
plt.title("Total Sales by Product Line")
plt.xticks(rotation=45)
plt.show()
```

---

### **5.6.4 Time Series: Sales Over Time**

```python
df['ORDERDATE'] = pd.to_datetime(df['ORDERDATE'])
monthly_sales = df.groupby(df['ORDERDATE'].dt.to_period('M'))['SALES'].sum()

plt.figure(figsize=(12, 6))
monthly_sales.plot(kind='line', marker='o')
plt.title("Monthly Sales Trend")
plt.xlabel('Month')
plt.ylabel('Total Sales')
plt.show()
```

---

### **5.6.5 Pair Plot: Relationships Between Features**

```python
sns.pairplot(df[['SALES', 'QUANTITYORDERED', 'PRICEEACH']], diag_kind='kde')
plt.show()
```

---

## **5.7 Correlation Analysis**

### **5.7.1 Correlation Matrix**

```python
correlation = df[['SALES', 'QUANTITYORDERED', 'PRICEEACH']].corr()
print(correlation)
```

---

### **5.7.2 Correlation Heatmap**

```python
plt.figure(figsize=(8, 5))
sns.heatmap(correlation, annot=True, cmap='coolwarm', vmin=-1, vmax=1)
plt.title('Correlation Heatmap')
plt.show()
```

> **Tip:**
>
> * **0.7 to 1.0** → Strong correlation
> * **0.3 to 0.7** → Moderate correlation
> * **0 to 0.3** → Weak correlation

---

## **5.8 Comparative Analysis**

### **5.8.1 Sales by Country**

```python
plt.figure(figsize=(12, 6))
sns.barplot(x='COUNTRY', y='SALES', data=df, estimator=sum, ci=None)
plt.title("Total Sales by Country")
plt.xticks(rotation=45)
plt.show()
```

---

### **5.8.2 Sales by Year**

```python
plt.figure(figsize=(10, 5))
sns.barplot(x='ORDER_YEAR', y='SALES', data=df, estimator=sum, ci=None)
plt.title("Yearly Sales Performance")
plt.show()
```

---

## **5.9 Advanced EDA Techniques**

### **5.9.1 Detecting Hidden Patterns**

Create a **pivot table** to compare metrics:

```python
pivot = df.pivot_table(
    index='PRODUCTLINE',
    columns='ORDER_YEAR',
    values='SALES',
    aggfunc='sum'
)
print(pivot)
```

---

### **5.9.2 Visualizing Pivot with Heatmap**

```python
plt.figure(figsize=(10, 6))
sns.heatmap(pivot, annot=True, fmt=".0f", cmap='Blues')
plt.title("Sales by Product Line and Year")
plt.show()
```

---

### **5.9.3 Dimensionality Reduction (PCA) Overview**

> PCA helps reduce **high-dimensional data** into **2D for visualization**.

```python
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler

# Select numeric features
numeric_features = df[['SALES', 'QUANTITYORDERED', 'PRICEEACH']]

# Standardize
scaler = StandardScaler()
scaled_data = scaler.fit_transform(numeric_features)

# Apply PCA
pca = PCA(n_components=2)
pca_result = pca.fit_transform(scaled_data)

df['PCA1'] = pca_result[:, 0]
df['PCA2'] = pca_result[:, 1]

plt.figure(figsize=(8, 6))
sns.scatterplot(x='PCA1', y='PCA2', data=df, hue='PRODUCTLINE')
plt.title('PCA Visualization')
plt.show()
```

---

## **5.10 Automating EDA with a Script**

Create a script to **generate automated reports**.

```python
# scripts/generate_eda_report.py
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

def generate_report(input_file):
    df = pd.read_csv(input_file)
    
    # Summary
    print("Summary Statistics:\n", df.describe())
    
    # Missing values
    print("\nMissing Values:\n", df.isnull().sum())
    
    # Correlation heatmap
    correlation = df.corr()
    sns.heatmap(correlation, annot=True, cmap='coolwarm')
    plt.title("Correlation Heatmap")
    plt.show()

if __name__ == "__main__":
    generate_report("../data/final_clean_sales.csv")
```

Run it:

```bash
python scripts/generate_eda_report.py
```

---

## **5.11 Mini Project: Build an EDA Notebook**

**Goal:**
Create a comprehensive **EDA notebook** that:

1. Summarizes dataset statistics.
2. Creates multiple visualizations.
3. Exports findings into a PDF or HTML report.

> **Bonus:** Use libraries like **pandas-profiling** or **sweetviz** for quick reports.

---

## **5.12 Checklist Before Moving Forward**

Make sure you can:

* [ ] Summarize and describe datasets with Pandas.
* [ ] Create histograms, boxplots, and bar charts.
* [ ] Detect outliers and anomalies visually.
* [ ] Analyze correlations using heatmaps.
* [ ] Automate basic EDA steps.

---

## **5.13 Next Steps (Module 6 Preview)**

In **Module 6**, we’ll cover **Working with Databases**, including:

* SQL fundamentals for data analysts.
* Connecting Python to SQL databases.
* Importing and exporting data between Pandas and databases.

Would you like me to continue with **Module 6: Working with Databases**?

Let's move into **Module 6**, where we focus on **Working with Databases**.

In real-world data analysis, most datasets come from **databases**, not CSV files.
This module will teach you how to **query, extract, and store data** using SQL and Python, enabling seamless interaction with relational databases like **PostgreSQL, MySQL, and SQLite**.

By the end of this module, you'll be able to:

* Understand relational database concepts.
* Write basic SQL queries.
* Connect Python to databases using libraries.
* Read data into Pandas for analysis.
* Export cleaned data back to a database.

---

# **Module 6: Working with Databases**

---

## **6.1 Introduction to Databases**

### **6.1.1 What is a Database?**

A **database** is an organized collection of data stored and accessed electronically.
In data analysis, we mostly use **relational databases**, where data is stored in **tables** with rows and columns.

**Examples of relational databases:**

* PostgreSQL
* MySQL
* SQLite
* Microsoft SQL Server

---

### **6.1.2 Why Databases Matter for Data Analysis**

* Centralized, **real-time data storage**.
* **Secure** and controlled access.
* Handle **large datasets** efficiently.
* Ability to **query and filter data** before loading into Python.

---

### **6.1.3 Setting Up SQLite (Local Testing)**

For this module, we'll use **SQLite**, a lightweight, file-based database perfect for practice.

**Install SQLite:**

* Windows:
  Download from [https://www.sqlite.org/download.html](https://www.sqlite.org/download.html)
* Linux/Mac:
  Already installed by default.

---

## **6.2 SQL Basics for Data Analysis**

We'll cover the most essential SQL commands.

> **SQL Table We'll Use:**
> Imagine a table `sales` with these columns:
>
> ```
> order_id | order_date | product_line | quantity | price_each | sales | country
> ```

---

### **6.2.1 Creating a Table**

```sql
CREATE TABLE sales (
    order_id INTEGER PRIMARY KEY,
    order_date TEXT,
    product_line TEXT,
    quantity INTEGER,
    price_each REAL,
    sales REAL,
    country TEXT
);
```

---

### **6.2.2 Inserting Data**

```sql
INSERT INTO sales (order_id, order_date, product_line, quantity, price_each, sales, country)
VALUES (101, '2024-01-15', 'Classic Cars', 2, 35.50, 71.00, 'USA');
```

---

### **6.2.3 Reading Data (SELECT)**

```sql
SELECT * FROM sales;
```

Filter specific columns:

```sql
SELECT order_id, sales FROM sales;
```

---

### **6.2.4 Filtering Data (WHERE)**

```sql
SELECT * FROM sales
WHERE country = 'USA';
```

Filter by date range:

```sql
SELECT * FROM sales
WHERE order_date BETWEEN '2024-01-01' AND '2024-03-31';
```

---

### **6.2.5 Sorting Data (ORDER BY)**

```sql
SELECT * FROM sales
ORDER BY sales DESC;
```

---

### **6.2.6 Aggregations (SUM, AVG, COUNT)**

```sql
SELECT country, SUM(sales) AS total_sales
FROM sales
GROUP BY country
ORDER BY total_sales DESC;
```

---

### **6.2.7 Joins (Combining Tables)**

Suppose we have a second table: `customers`.

```sql
SELECT s.order_id, s.sales, c.customer_name
FROM sales AS s
JOIN customers AS c ON s.customer_id = c.customer_id;
```

---

## **6.3 Connecting Python to Databases**

We'll now connect **Python** to a database and run SQL queries using Pandas and SQLite.

---

### **6.3.1 Using SQLite with Python**

Install required library:

```bash
pip install sqlalchemy sqlite3 pandas
```

---

### **6.3.2 Create a Database and Table**

```python
# scripts/setup_db.py
import sqlite3

# Connect to database (creates file if not exists)
conn = sqlite3.connect("../data/sales_database.db")
cursor = conn.cursor()

# Create sales table
cursor.execute("""
CREATE TABLE IF NOT EXISTS sales (
    order_id INTEGER PRIMARY KEY,
    order_date TEXT,
    product_line TEXT,
    quantity INTEGER,
    price_each REAL,
    sales REAL,
    country TEXT
)
""")

# Insert a sample record
cursor.execute("""
INSERT INTO sales (order_id, order_date, product_line, quantity, price_each, sales, country)
VALUES (1, '2025-01-10', 'Classic Cars', 3, 25.50, 76.50, 'USA')
""")

conn.commit()
conn.close()
print("Database setup complete!")
```

Run the script:

```bash
python scripts/setup_db.py
```

---

## **6.4 Reading Data from Database into Pandas**

```python
# notebooks/db_connection.ipynb
import sqlite3
import pandas as pd

# Connect to the database
conn = sqlite3.connect("../data/sales_database.db")

# Query the data
df = pd.read_sql_query("SELECT * FROM sales", conn)

# Display the dataframe
print(df.head())

conn.close()
```

---

## **6.5 Writing Pandas DataFrame Back to Database**

Suppose you cleaned data in Python and want to **store it in the database**:

```python
# notebooks/db_write.ipynb
conn = sqlite3.connect("../data/sales_database.db")

# Export DataFrame to table
df.to_sql("cleaned_sales", conn, if_exists="replace", index=False)

print("Cleaned data successfully saved to database!")
conn.close()
```

---

## **6.6 Using SQLAlchemy for Advanced Connections**

SQLAlchemy is a popular library for database connections.

Install it:

```bash
pip install sqlalchemy
```

Example usage:

```python
from sqlalchemy import create_engine

# SQLite engine
engine = create_engine("sqlite:///../data/sales_database.db")

# Read data
df = pd.read_sql("SELECT * FROM sales", engine)
print(df.head())
```

> **Bonus:**
> Replace `sqlite:///` with PostgreSQL or MySQL connection strings for production use:
>
> ```
> postgresql://user:password@localhost/db_name
> mysql+pymysql://user:password@localhost/db_name
> ```

---

## **6.7 Automating Database Extracts**

Create a reusable script to **fetch data automatically**:

```python
# scripts/fetch_data.py
import pandas as pd
import sqlite3

def fetch_sales(query):
    conn = sqlite3.connect("../data/sales_database.db")
    df = pd.read_sql_query(query, conn)
    conn.close()
    return df

# Example usage
if __name__ == "__main__":
    query = "SELECT country, SUM(sales) as total_sales FROM sales GROUP BY country"
    result = fetch_sales(query)
    print(result)
```

Run:

```bash
python scripts/fetch_data.py
```

---

## **6.8 Mini Project: Database to Pandas Pipeline**

**Goal:**
Create a mini project that:

1. Loads raw sales data into a database (`sales_database.db`).
2. Queries specific subsets using SQL.
3. Cleans and transforms data using Pandas.
4. Exports final analysis-ready data back to the database.

> **Tip:**
> Use Jupyter Notebook for exploration and `.py` scripts for automation.

---

## **6.9 Checklist Before Moving Forward**

Before starting Module 7, ensure you can:

* [ ] Create and query a database using SQL.
* [ ] Connect Python to a database using SQLite.
* [ ] Load data from a database into Pandas.
* [ ] Write cleaned data back to the database.
* [ ] Automate queries with Python scripts.

---

## **6.10 Next Steps (Module 7 Preview)**

In **Module 7**, we'll cover **Working with Large Datasets**, including:

* Optimizing Pandas performance for massive datasets.
* Chunk processing for big data.
* Introducing Dask for parallelized data processing.

Would you like me to continue with **Module 7: Working with Large Datasets**?

Let's move into **Module 7**, where we focus on **Working with Large Datasets**.

By this point, you’ve learned how to handle data using Pandas and SQL.
However, when dealing with **millions of rows or gigabytes of data**, regular Pandas may become **slow and memory inefficient**.

This module teaches strategies and tools for efficiently handling **big data** within Python.

---

# **Module 7: Working with Large Datasets**

---

## **7.1 Introduction to Big Data Challenges**

### **7.1.1 What Counts as "Big Data"?**

* **Small data**: < 100 MB → Easy to handle in Pandas.
* **Medium data**: 100 MB – 1 GB → Requires optimization techniques.
* **Big data**: > 1 GB → Requires distributed processing tools like Dask or Spark.

---

### **7.1.2 Common Challenges**

1. **Memory limits** – Data too large to fit into RAM.
2. **Slow computations** – Pandas operations become very slow.
3. **I/O bottlenecks** – Reading/writing huge files takes too long.
4. **Complex transformations** – Hard to maintain performance at scale.

---

### **7.1.3 Strategies for Managing Large Datasets**

* Load data in **chunks** instead of all at once.
* Use **optimized file formats** (Parquet, Feather, HDF5).
* Leverage **parallel processing** (Dask or multiprocessing).
* Push processing to the **database** (SQL first, Python second).

---

## **7.2 Optimizing Data Loading**

Loading data efficiently is the first step when working with big datasets.

---

### **7.2.1 Use Specific Data Types**

When loading CSVs, specify column data types to reduce memory usage.

```python
import pandas as pd

dtype_options = {
    "order_id": "int32",
    "quantity": "int16",
    "price_each": "float32",
    "sales": "float32",
    "country": "category"
}

df = pd.read_csv("sales_data_sample.csv", dtype=dtype_options)
print(df.info(memory_usage='deep'))
```

---

### **7.2.2 Load Only Needed Columns**

Instead of loading every column, load a subset using `usecols`.

```python
df = pd.read_csv("sales_data_sample.csv", usecols=["order_id", "sales", "country"])
print(df.head())
```

---

### **7.2.3 Load Large CSV in Chunks**

For extremely large files, use **iterators** to process data in smaller parts.

```python
chunk_size = 100000
chunks = pd.read_csv("sales_data_sample.csv", chunksize=chunk_size)

total_sales = 0
for chunk in chunks:
    total_sales += chunk["sales"].sum()

print("Total Sales:", total_sales)
```

> **Tip:** Start with 50,000 or 100,000 rows per chunk, then adjust based on your machine's performance.

---

## **7.3 Using Efficient File Formats**

CSV is **human-readable** but not optimized for big data.

---

### **7.3.1 Parquet Format**

* Columnar storage (ideal for analytics).
* Smaller file size.
* Faster reads/writes.

```python
# Convert CSV to Parquet
df = pd.read_csv("sales_data_sample.csv")
df.to_parquet("sales_data.parquet")

# Read Parquet file
df_parquet = pd.read_parquet("sales_data.parquet")
print(df_parquet.head())
```

---

### **7.3.2 Feather Format**

Feather is great for quick data exchange between Python and R.

```python
df.to_feather("sales_data.feather")
df_feather = pd.read_feather("sales_data.feather")
```

---

### **7.3.3 HDF5 Format**

For very large, hierarchical datasets.

```python
df.to_hdf("sales_data.h5", key="sales", mode="w")
df_hdf = pd.read_hdf("sales_data.h5", "sales")
```

---

## **7.4 Processing Data in Chunks**

Sometimes you need to **transform data without loading it all into memory**.

Example: Calculate average sales by country in chunks.

```python
chunk_size = 50000
chunks = pd.read_csv("sales_data_sample.csv", chunksize=chunk_size)

country_sales = {}

for chunk in chunks:
    group = chunk.groupby("country")["sales"].sum()
    for country, total in group.items():
        country_sales[country] = country_sales.get(country, 0) + total

print(country_sales)
```

---

## **7.5 Introducing Dask for Parallel Computing**

**Dask** is a powerful Python library for **parallelized, out-of-core data processing**, similar to Pandas but built for big data.

---

### **7.5.1 Installation**

```bash
pip install dask[complete]
```

---

### **7.5.2 Dask Basics**

```python
import dask.dataframe as dd

# Load CSV with Dask
ddf = dd.read_csv("sales_data_sample.csv")

# Lazy operations (compute only when needed)
result = ddf.groupby("country")["sales"].sum()

# Trigger actual computation
final_result = result.compute()
print(final_result)
```

---

### **7.5.3 Benefits of Dask**

* Scales Pandas workflows seamlessly.
* Handles datasets larger than RAM.
* Supports distributed computing (multi-core and multi-machine).

---

## **7.6 Pushing Computations to the Database**

Instead of loading all data into Python, let **SQL handle aggregations** first.

Example using SQLite:

```python
import sqlite3
import pandas as pd

conn = sqlite3.connect("sales_database.db")

query = """
SELECT country, SUM(sales) as total_sales
FROM sales
GROUP BY country
"""

df = pd.read_sql_query(query, conn)
conn.close()

print(df)
```

> **Why this works well:**
> SQL runs the heavy lifting inside the database engine, sending only summarized data to Python.

---

## **7.7 Memory Optimization Techniques**

### **7.7.1 Downcasting Numeric Columns**

Convert `float64` → `float32` or `int64` → `int32` to reduce memory usage.

```python
df["quantity"] = pd.to_numeric(df["quantity"], downcast="integer")
df["sales"] = pd.to_numeric(df["sales"], downcast="float")
```

---

### **7.7.2 Convert Strings to Category**

For repetitive string columns (like country names), use Pandas categories.

```python
df["country"] = df["country"].astype("category")
```

---

### **7.7.3 Drop Unnecessary Columns**

Remove columns you don't need before processing.

```python
df = df.drop(columns=["unnecessary_column"])
```

---

## **7.8 Case Study: Scalable Sales Data Analysis**

**Goal:** Analyze 10 million rows of sales data efficiently.

**Steps:**

1. Load data in chunks using Pandas.
2. Convert CSV to **Parquet** for faster reads.
3. Use **Dask** to process the dataset in parallel.
4. Perform aggregation:

   * Total sales per country.
   * Monthly sales trends.
   * Top 10 products by revenue.
5. Export final cleaned dataset to the database.

---

## **7.9 Mini Project: Big Data Pipeline**

### **Task Overview:**

* Dataset: 2GB sales dataset.
* Build a scalable pipeline with the following stages:

  1. **Extract**: Load raw data in chunks.
  2. **Transform**: Clean and optimize data (downcasting, categories).
  3. **Analyze**: Summarize sales by product line and country.
  4. **Load**: Save processed data to a Parquet file and SQLite database.

---

## **7.10 Checklist Before Moving Forward**

Before moving to Module 8, ensure you can:

* [ ] Load massive datasets without crashing your system.
* [ ] Work with Parquet, Feather, and HDF5 file formats.
* [ ] Use chunk processing to aggregate data efficiently.
* [ ] Perform basic operations with Dask.
* [ ] Optimize Pandas workflows using memory reduction techniques.
* [ ] Push heavy aggregations to SQL before Python.

---

## **7.11 Next Steps (Module 8 Preview)**

In **Module 8**, we’ll focus on **Data Visualization**, including:

* Static visualizations with **Matplotlib** and **Seaborn**.
* Interactive visualizations with **Plotly**.
* Building **dashboards** to share insights.

Would you like me to continue with **Module 8: Data Visualization**?

Let's continue with **Module 8**, where we focus on building a **complete data analysis workflow** and applying everything you’ve learned so far to a real-world, end-to-end project.

The main goal is to simulate a **real-world data analytics process** — from defining a problem, gathering data, cleaning it, exploring it, analyzing it, and finally preparing a report that you can share with stakeholders.

We'll continue using your **`sales_data_sample.csv`** dataset as the core project dataset, but we’ll also bring in additional techniques like **web scraping**, **APIs**, and combining multiple data sources.

---

# **Module 8: Data Analysis Project Workflow**

---

## **8.1 Introduction**

This module will guide you through:

* Understanding **how real-world data projects are structured**.
* Building **a repeatable workflow** for your data analysis projects.
* Creating a **final report and visualizations** to present your findings.

> **Scenario:**
> You’ve been hired as a Data Analyst for a retail company.
> Your task is to **analyze sales performance** across regions, products, and time periods, and **generate actionable insights** for decision-making.

---

## **8.2 Project Planning**

The first step is **planning your analysis**, similar to how professionals operate in the field.

---

### **8.2.1 Define the Problem Statement**

Clearly describe what you want to achieve.

**Example problem statement:**

> "Identify sales trends by region and product line to optimize inventory and marketing efforts for the upcoming quarter."

---

### **8.2.2 Identify Stakeholders**

Stakeholders are the people who will use your analysis. Examples:

* **Management:** Needs actionable summaries.
* **Sales team:** Wants detailed performance insights.
* **Finance team:** Needs accurate numbers for forecasting.

---

### **8.2.3 Gather Requirements**

Ask questions like:

* What **KPIs** matter most? (e.g., revenue, profit margin, sales growth)
* What time period should we focus on?
* Are there **budget constraints** or **regional limitations**?

> **Practical Tip:**
> Create a one-page document summarizing the requirements before starting coding.

---

### **8.2.4 Identify Data Sources**

You may have multiple data sources:

* Internal datasets (`sales_data_sample.csv`).
* External APIs (currency conversion rates, weather data).
* Public datasets (from Kaggle or government portals).

---

## **8.3 Data Collection**

Once you know what data you need, collect it.

---

### **8.3.1 Collecting Data from Files**

We already have the main dataset:

```python
import pandas as pd

df = pd.read_csv("sales_data_sample.csv")
print(df.head())
print(df.info())
```

---

### **8.3.2 Web Scraping with BeautifulSoup**

If you need supplementary data from a website (e.g., competitor prices):

```python
import requests
from bs4 import BeautifulSoup

url = "https://example.com/products"
response = requests.get(url)
soup = BeautifulSoup(response.text, 'html.parser')

# Extract product names
products = [p.text for p in soup.find_all('h2', class_='product-title')]
print(products[:10])
```

> **Note:** Always check a website's `robots.txt` and terms of service before scraping.

---

### **8.3.3 Collecting Data via API**

For example, pulling currency exchange rates from a free API:

```python
import requests

api_url = "https://api.exchangerate-api.com/v4/latest/USD"
response = requests.get(api_url)
data = response.json()

print("Exchange Rate Data:", data)
```

This can be merged with your sales data if you need to adjust for currency fluctuations.

---

## **8.4 Data Cleaning and Preparation**

The **data wrangling phase** is critical because raw data is rarely clean.

---

### **8.4.1 Identify Missing Data**

```python
missing_values = df.isnull().sum()
print("Missing Values:\n", missing_values)
```

---

### **8.4.2 Handle Missing Data**

* **Drop rows or columns:**

```python
df.dropna(subset=['sales'], inplace=True)
```

* **Fill missing values:**

```python
df['quantity'] = df['quantity'].fillna(df['quantity'].mean())
```

---

### **8.4.3 Standardize Column Names**

```python
df.columns = df.columns.str.strip().str.lower().str.replace(" ", "_")
```

---

### **8.4.4 Remove Duplicates**

```python
df.drop_duplicates(inplace=True)
```

---

### **8.4.5 Optimize Data Types**

```python
df['country'] = df['country'].astype('category')
df['quantity'] = pd.to_numeric(df['quantity'], downcast='integer')
df['sales'] = pd.to_numeric(df['sales'], downcast='float')
```

---

## **8.5 Exploratory Data Analysis (EDA)**

This step helps you **understand the data before modeling or reporting**.

---

### **8.5.1 Basic Statistical Summary**

```python
print(df.describe())
print(df['country'].value_counts())
```

---

### **8.5.2 Visualizing Sales Trends**

```python
import matplotlib.pyplot as plt
import seaborn as sns

plt.figure(figsize=(10,5))
sns.barplot(x='country', y='sales', data=df, estimator=sum, ci=None)
plt.title('Total Sales by Country')
plt.xticks(rotation=45)
plt.show()
```

---

### **8.5.3 Monthly Sales Trends**

```python
df['order_date'] = pd.to_datetime(df['order_date'])
df['month'] = df['order_date'].dt.to_period('M')

monthly_sales = df.groupby('month')['sales'].sum()

monthly_sales.plot(kind='line', figsize=(12,6))
plt.title('Monthly Sales Trends')
plt.xlabel('Month')
plt.ylabel('Sales')
plt.show()
```

---

### **8.5.4 Correlation Analysis**

```python
plt.figure(figsize=(8,6))
sns.heatmap(df.corr(), annot=True, cmap='coolwarm')
plt.title('Correlation Heatmap')
plt.show()
```

---

## **8.6 Final Analysis and Insights**

Now that you understand your data, **answer key business questions**.

Example questions:

1. Which country generates the most revenue?
2. Which product line has the highest profit margin?
3. What are the top 5 customers by total sales?

---

### **8.6.1 Total Sales by Country**

```python
country_sales = df.groupby('country')['sales'].sum().sort_values(ascending=False)
print(country_sales.head())
```

---

### **8.6.2 Top 5 Customers**

```python
top_customers = df.groupby('customer_name')['sales'].sum().sort_values(ascending=False).head(5)
print(top_customers)
```

---

## **8.7 Creating a Final Report**

Once your analysis is done, you need to **share it effectively**.

---

### **8.7.1 Export to CSV or Excel**

```python
final_summary = df.groupby(['country', 'product_line'])['sales'].sum().reset_index()
final_summary.to_csv("final_sales_summary.csv", index=False)
```

---

### **8.7.2 Export to PDF Report**

Use `reportlab` or `matplotlib` to create a simple PDF.

```python
from fpdf import FPDF

pdf = FPDF()
pdf.add_page()
pdf.set_font("Arial", size=12)
pdf.cell(200, 10, txt="Sales Analysis Report", ln=True, align="C")

pdf.cell(200, 10, txt=f"Top Country: {country_sales.idxmax()} - ${country_sales.max():,.2f}", ln=True)

pdf.output("sales_report.pdf")
```

---

## **8.8 Building Interactive Dashboards**

Dashboards allow stakeholders to **explore the data themselves**.

---

### **8.8.1 Streamlit Setup**

Install Streamlit:

```bash
pip install streamlit
```

Create a file called `dashboard.py`:

```python
import streamlit as st
import pandas as pd

df = pd.read_csv("sales_data_sample.csv")

st.title("Sales Dashboard")
st.write("Interactive sales data exploration")

country = st.selectbox("Select Country", df['country'].unique())
filtered_df = df[df['country'] == country]

st.bar_chart(filtered_df.groupby('product_line')['sales'].sum())
```

Run the dashboard:

```bash
streamlit run dashboard.py
```

---

## **8.9 Final Project Checklist**

Before completing the module, ensure you can:

* [ ] Define a clear business problem and KPIs.
* [ ] Gather data from multiple sources (CSV, API, Web scraping).
* [ ] Clean and preprocess the dataset.
* [ ] Perform EDA and extract insights.
* [ ] Create meaningful visualizations.
* [ ] Generate final reports for stakeholders.
* [ ] Build a basic interactive dashboard.

---

## **8.10 Mini Project: Sales Insights Dashboard**

**Goal:** Build a full **end-to-end project** where you:

1. Load and clean the `sales_data_sample.csv` dataset.
2. Combine with currency exchange data from an API.
3. Analyze top countries, products, and sales trends.
4. Create visualizations and summaries.
5. Build an interactive Streamlit dashboard.
6. Generate a PDF report for management.

---

## **8.11 Next Steps (Module 9 Preview)**

In **Module 9**, we’ll explore **Machine Learning for Data Analysis**, including:

* Basics of machine learning concepts.
* Implementing simple models with Scikit-learn.
* Predicting future sales using historical data.

Would you like to proceed to **Module 9** on Machine Learning?

Let's move on to **Module 9**, where we'll introduce **Machine Learning (ML)** and apply it to your dataset.

The focus here is on **practical machine learning for data analysis**, so you’ll learn how to **use ML models to gain insights and make predictions**, such as predicting future sales trends or identifying high-value customers.

We’ll use **Scikit-learn**, the most popular ML library in Python, and build step-by-step code-along examples.

---

# **Module 9: Introduction to Machine Learning for Data Analysis**

---

## **9.1 Introduction to Machine Learning**

---

### **9.1.1 What is Machine Learning?**

Machine Learning (ML) is a subset of Artificial Intelligence (AI) where computers **learn patterns from data** without being explicitly programmed.

**Example in our project:**

* Predicting future **sales revenue** based on past trends.
* Classifying whether a customer is **likely to churn** or **stay loyal**.

---

### **9.1.2 Key Machine Learning Terminology**

| Term              | Definition                                                                       |
| ----------------- | -------------------------------------------------------------------------------- |
| **Features (X)**  | Input variables used for predictions (e.g., `quantity`, `price_each`, `country`) |
| **Target (y)**    | The output or value we want to predict (e.g., `sales`)                           |
| **Model**         | The algorithm that finds patterns in the data                                    |
| **Training Data** | Data used to teach the model                                                     |
| **Testing Data**  | Data used to evaluate the model                                                  |
| **Overfitting**   | When the model memorizes the training data instead of generalizing               |

---

### **9.1.3 Types of Machine Learning**

1. **Supervised Learning**

   * Model is trained on labeled data (features + target).
   * Example: Predicting future sales.

2. **Unsupervised Learning**

   * Model finds patterns in **unlabeled data**.
   * Example: Customer segmentation.

3. **Reinforcement Learning** (advanced, not covered here)

   * Model learns by interacting with an environment.

---

## **9.2 Setting up Scikit-learn**

Install Scikit-learn if not already installed:

```bash
pip install scikit-learn
```

Import libraries:

```python
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score
```

---

## **9.3 Preparing Data for Machine Learning**

Before training models, your data needs to be cleaned and structured properly.

---

### **9.3.1 Load and Clean Data**

We'll continue with `sales_data_sample.csv`.

```python
# Load the dataset
df = pd.read_csv("sales_data_sample.csv")

# Clean column names
df.columns = df.columns.str.strip().str.lower().str.replace(" ", "_")

# Check for missing values
print(df.isnull().sum())

# Drop rows with missing 'sales'
df.dropna(subset=['sales'], inplace=True)
```

---

### **9.3.2 Feature Selection**

We'll use these features to predict `sales`:

* `quantity`
* `price_each`
* `country`

```python
features = ['quantity', 'price_each']
target = 'sales'

X = df[features]
y = df[target]
```

---

### **9.3.3 Splitting Data into Training and Testing Sets**

We'll split the dataset into:

* **Training set** (80%) → For building the model.
* **Testing set** (20%) → For evaluating the model.

```python
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

print("Training set size:", X_train.shape)
print("Testing set size:", X_test.shape)
```

---

## **9.4 Simple Linear Regression**

The first model we’ll build is a **Linear Regression model** to predict sales.

---

### **9.4.1 What is Linear Regression?**

Linear regression finds a straight-line relationship between **independent variables (X)** and a **dependent variable (y)**.

**Example formula:**

```
sales = (coefficient1 * quantity) + (coefficient2 * price_each) + intercept
```

---

### **9.4.2 Building the Model**

```python
model = LinearRegression()
model.fit(X_train, y_train)

# Print coefficients
print("Intercept:", model.intercept_)
print("Coefficients:", model.coef_)
```

---

### **9.4.3 Making Predictions**

```python
y_pred = model.predict(X_test)

print("First 10 predictions:", y_pred[:10])
```

---

### **9.4.4 Evaluating the Model**

We use two key metrics:

* **Mean Squared Error (MSE)** → Measures prediction error.
* **R² Score** → Measures how well the model fits the data.

```python
mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)

print("Mean Squared Error:", mse)
print("R² Score:", r2)
```

> **Interpretation:**
>
> * R² close to 1.0 means the model explains most of the variance.
> * MSE closer to 0 means fewer errors.

---

## **9.5 Visualization of Model Results**

Visualize how well the model fits the actual data.

```python
import matplotlib.pyplot as plt

plt.scatter(y_test, y_pred, alpha=0.7)
plt.xlabel("Actual Sales")
plt.ylabel("Predicted Sales")
plt.title("Actual vs Predicted Sales")
plt.show()
```

---

## **9.6 Classification Basics**

Sometimes, instead of predicting a numeric value, you need to **classify categories**.

**Example problem:**
Classify whether an order is **High Value** or **Low Value** based on its total sales.

---

### **9.6.1 Create a Target Column**

```python
# Define a threshold for high-value orders
threshold = df['sales'].median()

df['high_value'] = np.where(df['sales'] >= threshold, 1, 0)
print(df[['sales', 'high_value']].head())
```

---

### **9.6.2 Train-Test Split for Classification**

```python
X_class = df[['quantity', 'price_each']]
y_class = df['high_value']

X_train_class, X_test_class, y_train_class, y_test_class = train_test_split(
    X_class, y_class, test_size=0.2, random_state=42
)
```

---

### **9.6.3 Logistic Regression Model**

```python
from sklearn.linear_model import LogisticRegression

classifier = LogisticRegression()
classifier.fit(X_train_class, y_train_class)

# Predictions
y_pred_class = classifier.predict(X_test_class)

print("Predictions:", y_pred_class[:10])
```

---

### **9.6.4 Evaluate Classification Performance**

```python
from sklearn.metrics import accuracy_score, confusion_matrix, classification_report

# Accuracy
accuracy = accuracy_score(y_test_class, y_pred_class)
print("Accuracy:", accuracy)

# Confusion Matrix
cm = confusion_matrix(y_test_class, y_pred_class)
print("Confusion Matrix:\n", cm)

# Detailed Report
print(classification_report(y_test_class, y_pred_class))
```

---

## **9.7 Improving Model Performance**

Basic techniques to improve performance:

1. **Feature Engineering**

   * Create new features like `total_discount` or `average_sales_per_customer`.

2. **Feature Scaling**

   * Standardize data using `StandardScaler` for better algorithm performance.

```python
from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()
X_scaled = scaler.fit_transform(X_train)
```

3. **Cross-Validation**

   * Split data multiple times for more reliable results.

```python
from sklearn.model_selection import cross_val_score

scores = cross_val_score(model, X, y, cv=5)
print("Cross-validation scores:", scores)
print("Average CV score:", scores.mean())
```

---

## **9.8 Mini Project: Predicting Future Sales**

**Goal:** Build a regression model to **forecast sales** using historical data.

**Steps:**

1. Load and clean `sales_data_sample.csv`.
2. Engineer new features:

   * `month`
   * `total_order_value`
3. Split the data into training and testing sets.
4. Train a Linear Regression model.
5. Evaluate performance using R² and MSE.
6. Visualize predicted vs actual sales.
7. Save the model for later use.

---

### **9.8.1 Save the Model**

```python
import joblib

joblib.dump(model, "sales_forecast_model.pkl")
```

---

### **9.8.2 Load the Model**

```python
loaded_model = joblib.load("sales_forecast_model.pkl")
prediction = loaded_model.predict([[10, 100.00]])  # Example input
print("Predicted Sales:", prediction)
```

---

## **9.9 Checklist Before Moving Forward**

Before completing this module, ensure you can:

* [ ] Explain supervised vs unsupervised learning.
* [ ] Clean and prepare data for ML.
* [ ] Split data into training and test sets.
* [ ] Build and evaluate a Linear Regression model.
* [ ] Build and evaluate a Logistic Regression classifier.
* [ ] Visualize model performance.
* [ ] Save and load trained models for reuse.

---

## **9.10 What's Next (Module 10 Preview)**

In **Module 10**, we'll focus on **Data Communication and Reporting**:

* Exporting results to CSV, Excel, and PDF.
* Building **interactive dashboards** with Streamlit or Dash.
* Best practices for presenting your findings to stakeholders.

Would you like me to continue with **Module 10**?


Here’s **Module 10**, which focuses on **data communication and reporting**.
The goal is to **present insights effectively** so stakeholders can make informed decisions.

We will cover:

* Exporting results
* Building **interactive dashboards**
* Automating reports
* Best practices for communicating findings

---

# **Module 10: Data Communication and Reporting**

---

## **10.1 Introduction to Data Communication**

---

### **10.1.1 What is Data Communication?**

Data communication is about **presenting insights clearly and effectively** so others can act on them.

**Example:**
After analyzing sales data:

* Instead of sending a raw CSV file, you:

  * Create a **summary dashboard** with charts.
  * Include **actionable recommendations** in a report.

> 💡 *Good data analysis is useless without clear communication.*

---

### **10.1.2 Types of Data Communication**

| **Method**                       | **When to Use**                                                        |
| -------------------------------- | ---------------------------------------------------------------------- |
| **Static Reports** (PDF/Excel)   | For official documentation, regulatory compliance, or offline sharing. |
| **Interactive Dashboards**       | For live data monitoring and decision-making.                          |
| **Automated Alerts** (Email/SMS) | For quick updates and notifications.                                   |

---

### **10.1.3 Reporting Workflow**

1. **Collect & Analyze Data** (Modules 1–9)
2. **Summarize Key Insights**
3. **Choose a Reporting Method** (PDF, Excel, Dashboard)
4. **Automate Report Generation**
5. **Deliver to Stakeholders**

---

## **10.2 Exporting Data**

---

### **10.2.1 Export to CSV**

```python
import pandas as pd

# Load dataset
df = pd.read_csv("sales_data_sample.csv")

# Save cleaned data
df.to_csv("cleaned_sales_data.csv", index=False)
print("Cleaned data saved to CSV.")
```

> **Use case:** Share raw data with analysts or other teams.

---

### **10.2.2 Export to Excel**

```python
df.to_excel("cleaned_sales_data.xlsx", index=False)
print("Cleaned data saved to Excel.")
```

> **Benefit:** Excel is widely used by non-technical teams.

---

### **10.2.3 Export to PDF (Summary Report)**

Install **ReportLab** for PDF generation:

```bash
pip install reportlab
```

#### Example: Simple PDF Export

```python
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas

c = canvas.Canvas("sales_summary.pdf", pagesize=letter)
c.drawString(100, 750, "Sales Summary Report")
c.drawString(100, 730, "Total Sales: $500,000")
c.drawString(100, 710, "Top Country: USA")
c.save()

print("PDF report generated.")
```

> **Use case:** Send official reports to management.

---

## **10.3 Data Visualization for Reporting**

---

### **10.3.1 Exporting Matplotlib Charts to Files**

You can save visualizations as images for reports:

```python
import matplotlib.pyplot as plt

# Example chart
df.groupby('country')['sales'].sum().plot(kind='bar')

plt.title("Total Sales by Country")
plt.ylabel("Sales")
plt.xlabel("Country")
plt.tight_layout()

plt.savefig("sales_by_country.png")
print("Chart saved as PNG.")
```

---

### **10.3.2 Adding Charts to PDF Reports**

```python
from reportlab.platypus import SimpleDocTemplate, Paragraph, Image
from reportlab.lib.styles import getSampleStyleSheet

doc = SimpleDocTemplate("sales_report.pdf", pagesize=letter)
styles = getSampleStyleSheet()
elements = []

# Add title
elements.append(Paragraph("Sales Report", styles['Title']))

# Add image
elements.append(Image("sales_by_country.png", width=400, height=300))

doc.build(elements)
print("PDF with chart generated.")
```

---

## **10.4 Building Interactive Dashboards**

Sometimes, stakeholders need **live, interactive reports**.

We’ll use **Streamlit**, a Python library for interactive dashboards.

---

### **10.4.1 Install Streamlit**

```bash
pip install streamlit
```

---

### **10.4.2 Create a Simple Dashboard**

Save as `sales_dashboard.py`:

```python
import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt

# Load data
df = pd.read_csv("sales_data_sample.csv")

st.title("Sales Dashboard")
st.write("Interactive dashboard for exploring sales data")

# Filter by country
country = st.selectbox("Select a country", df['country'].unique())

filtered_df = df[df['country'] == country]

# Display filtered table
st.write(filtered_df)

# Sales by product line chart
st.subheader("Sales by Product Line")
sales_by_product = filtered_df.groupby('productline')['sales'].sum()

fig, ax = plt.subplots()
sales_by_product.plot(kind='bar', ax=ax)
st.pyplot(fig)
```

Run the dashboard:

```bash
streamlit run sales_dashboard.py
```

---

### **10.4.3 Adding Multiple Filters**

You can add filters for:

* **Date range**
* **Country**
* **Product line**

```python
start_date = st.date_input("Start date")
end_date = st.date_input("End date")

date_filtered = df[(pd.to_datetime(df['orderdate']) >= pd.to_datetime(start_date)) &
                   (pd.to_datetime(df['orderdate']) <= pd.to_datetime(end_date))]

st.write(date_filtered)
```

---

## **10.5 Automating Reports**

---

### **10.5.1 Schedule Reports with Python**

You can run Python scripts **automatically** using:

* **Windows Task Scheduler** (Windows)
* **Cron Jobs** (Linux/Mac)

Example command to run daily at 8 AM:

```
0 8 * * * /usr/bin/python3 /path/to/generate_report.py
```

---

### **10.5.2 Automate Emails with Reports**

Install SMTP library:

```bash
pip install yagmail
```

Send automated emails:

```python
import yagmail

receiver = "manager@example.com"
subject = "Daily Sales Report"
body = "Attached is the latest sales report."

yag = yagmail.SMTP("your_email@gmail.com", "your_password")
yag.send(
    to=receiver,
    subject=subject,
    contents=body,
    attachments="sales_summary.pdf",
)

print("Email sent successfully.")
```

> **Use case:** Automatically send daily or weekly reports to stakeholders.

---

## **10.6 Best Practices for Data Communication**

---

### **10.6.1 Keep Reports Simple**

* Focus on **key insights**, not every detail.
* Use **visuals** instead of raw tables.

---

### **10.6.2 Use Consistent Formatting**

* Standardize fonts, colors, and chart styles.
* Add **titles, labels, and legends** to all visuals.

---

### **10.6.3 Tell a Story**

* Present findings in a logical order:

  1. **Problem** → What are we solving?
  2. **Analysis** → How did we approach it?
  3. **Results** → What did we find?
  4. **Recommendations** → What action should be taken?

---

### **10.6.4 Provide Context**

Avoid presenting numbers without explanation.
Example:

> ❌ *"Sales were \$500,000 last month."*
> ✅ *"Sales were \$500,000 last month, which is a 10% increase from the previous month due to our new marketing strategy."*

---

## **10.7 Mini Project: Automated Sales Dashboard and PDF Report**

---

### **Goal**

Create a pipeline that:

1. Loads sales data.
2. Generates visualizations.
3. Saves a PDF report.
4. Updates an interactive dashboard.
5. Emails the report automatically.

---

### **Suggested Steps**

1. Create a Python script `generate_report.py` that:

   * Loads and cleans the dataset.
   * Summarizes key metrics (total sales, top country, top product line).
   * Saves summary and charts to a PDF.

2. Build a **Streamlit dashboard** with filters and charts.

3. Schedule the script to run **daily** using a task scheduler.

4. Automatically **email the PDF** to stakeholders after generation.

---

## **10.8 Checklist Before Finishing Module 10**

* [ ] Export data to CSV, Excel, and PDF.
* [ ] Build charts and save them for reports.
* [ ] Create an interactive Streamlit dashboard.
* [ ] Automate reports using schedulers.
* [ ] Send reports via email automatically.
* [ ] Follow best practices for clear and actionable reporting.

---

## **10.9 What's Next (Module 11 Preview)**

In **Module 11**, we’ll focus on **final project integration**:

* Combine everything from data collection to reporting.
* Create a **full data analytics pipeline**.
* Deploy your dashboard and reporting system to the **cloud** for real-time access.

Would you like me to continue with **Module 11** and final project details?

Here’s **Module 11**, the **Final Project and Capstone**, where we bring together everything we’ve covered in Modules 1–10 into a **complete, end-to-end data analysis solution**.

The goal is to simulate a **real-world data analytics project** using the `sales_data_sample.csv` you uploaded earlier.
You will practice **data cleaning, EDA, visualization, dashboarding, reporting, and automation** step-by-step.

---

# **Module 11: Final Project and Capstone**

---

## **11.1 Project Overview**

### **11.1.1 Objective**

Build a **Sales Performance Analytics System** that allows stakeholders to:

1. **Understand business performance** through clean data and analysis.
2. **Visualize trends** using dashboards and reports.
3. **Make data-driven decisions** with automated reports and alerts.

---

### **11.1.2 Problem Statement**

> The sales department struggles to track performance across regions, products, and time.
> They rely on manual Excel reports that are **slow, inconsistent, and prone to error**.

**Your task:**
Create a **Python-powered analytics solution** that automates:

* Data cleaning and validation
* Sales performance analysis
* Interactive dashboards
* Automated reporting via email

---

### **11.1.3 Skills You’ll Demonstrate**

By completing this module, you’ll showcase:

* **Pandas and NumPy** for data cleaning and manipulation
* **Matplotlib and Seaborn** for data visualization
* **Streamlit** for building interactive dashboards
* **ReportLab** for automated PDF report generation
* **SMTP/Email integration** for delivering reports
* **Project planning and documentation** for real-world deployment

---

## **11.2 Project Workflow**

The project will follow these stages:

| **Step** | **Task**                            | **Output**                         |
| -------- | ----------------------------------- | ---------------------------------- |
| 1        | **Data Collection**                 | Raw sales dataset                  |
| 2        | **Data Wrangling & Cleaning**       | Cleaned, structured dataset        |
| 3        | **EDA (Exploratory Data Analysis)** | Visual insights and key findings   |
| 4        | **Dashboard Development**           | Interactive web app                |
| 5        | **Report Generation**               | PDF report with visuals            |
| 6        | **Automation**                      | Scheduled reports & email delivery |
| 7        | **Final Presentation**              | Project report & live demo         |

---

## **11.3 Step-by-Step Implementation**

---

### **Step 1: Project Setup**

1. **Create a project folder**

   ```bash
   mkdir sales_analytics_project
   cd sales_analytics_project
   ```

2. **Set up a virtual environment**

   ```bash
   python -m venv venv
   source venv/bin/activate  # Linux/Mac
   venv\Scripts\activate     # Windows
   ```

3. **Install required libraries**

   ```bash
   pip install pandas numpy matplotlib seaborn streamlit reportlab yagmail openpyxl
   ```

4. **Folder structure**

   ```
   sales_analytics_project/
   ├── data/
   │   └── sales_data_sample.csv
   ├── reports/
   │   └── generated_reports/
   ├── dashboard/
   │   └── dashboard.py
   ├── scripts/
   │   └── generate_report.py
   │   └── clean_data.py
   └── main.py
   ```

---

### **Step 2: Data Wrangling and Cleaning**

Create a cleaning script `scripts/clean_data.py`:

```python
import pandas as pd

def clean_sales_data(input_file, output_file):
    df = pd.read_csv(input_file)

    # 1. Remove duplicates
    df.drop_duplicates(inplace=True)

    # 2. Handle missing values
    df.fillna({"state": "Unknown"}, inplace=True)

    # 3. Convert date column to datetime
    df['orderdate'] = pd.to_datetime(df['orderdate'], errors='coerce')

    # 4. Standardize country names
    df['country'] = df['country'].str.title()

    # 5. Save cleaned data
    df.to_csv(output_file, index=False)
    print(f"Cleaned data saved to {output_file}")

    return df

if __name__ == "__main__":
    clean_sales_data("data/sales_data_sample.csv", "data/cleaned_sales_data.csv")
```

Run it:

```bash
python scripts/clean_data.py
```

---

### **Step 3: Exploratory Data Analysis (EDA)**

Create a notebook or script `scripts/eda.py`:

```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

df = pd.read_csv("data/cleaned_sales_data.csv")

# 1. Summary statistics
print(df.describe())

# 2. Sales by country
plt.figure(figsize=(10,5))
sns.barplot(x='country', y='sales', data=df, estimator=sum)
plt.title("Total Sales by Country")
plt.xticks(rotation=45)
plt.tight_layout()
plt.savefig("reports/sales_by_country.png")
plt.show()

# 3. Correlation heatmap
plt.figure(figsize=(8,6))
sns.heatmap(df.corr(), annot=True, cmap="coolwarm")
plt.title("Correlation Heatmap")
plt.tight_layout()
plt.savefig("reports/correlation_heatmap.png")
plt.show()
```

> **Goal:** Identify trends and outliers before reporting.

---

### **Step 4: Build Streamlit Dashboard**

Create `dashboard/dashboard.py`:

```python
import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt

# Load data
df = pd.read_csv("data/cleaned_sales_data.csv")

st.title("📊 Sales Performance Dashboard")

# Filter by country
country = st.selectbox("Select Country", df['country'].unique())
filtered_df = df[df['country'] == country]

# Show filtered data
st.write(filtered_df.head())

# Sales by product line
st.subheader("Sales by Product Line")
sales_by_product = filtered_df.groupby('productline')['sales'].sum()

fig, ax = plt.subplots()
sales_by_product.plot(kind='bar', ax=ax, color='skyblue')
plt.ylabel("Total Sales")
st.pyplot(fig)
```

Run:

```bash
streamlit run dashboard/dashboard.py
```

> 💡 *Stakeholders can now explore data interactively.*

---

### **Step 5: Generate PDF Report**

Create `scripts/generate_report.py`:

```python
from reportlab.platypus import SimpleDocTemplate, Paragraph, Image, Spacer
from reportlab.lib.styles import getSampleStyleSheet
import pandas as pd

def generate_pdf_report():
    df = pd.read_csv("data/cleaned_sales_data.csv")

    total_sales = df['sales'].sum()
    top_country = df.groupby('country')['sales'].sum().idxmax()

    # Prepare PDF
    doc = SimpleDocTemplate("reports/generated_reports/sales_summary.pdf")
    styles = getSampleStyleSheet()
    elements = []

    elements.append(Paragraph("Sales Summary Report", styles['Title']))
    elements.append(Spacer(1, 20))

    elements.append(Paragraph(f"Total Sales: ${total_sales:,.2f}", styles['Normal']))
    elements.append(Paragraph(f"Top Performing Country: {top_country}", styles['Normal']))
    elements.append(Spacer(1, 20))

    # Add chart
    elements.append(Image("reports/sales_by_country.png", width=400, height=300))

    doc.build(elements)
    print("PDF report generated successfully!")

if __name__ == "__main__":
    generate_pdf_report()
```

Run:

```bash
python scripts/generate_report.py
```

---

### **Step 6: Automate Email Delivery**

```python
import yagmail

def send_report():
    yag = yagmail.SMTP("your_email@gmail.com", "your_password")
    yag.send(
        to="manager@example.com",
        subject="Daily Sales Report",
        contents="Attached is the latest sales summary report.",
        attachments="reports/generated_reports/sales_summary.pdf"
    )
    print("Email sent successfully!")

if __name__ == "__main__":
    send_report()
```

---

### **Step 7: Schedule Automation**

**Windows:**

* Use **Task Scheduler** to run `generate_report.py` daily.

**Linux/Mac:**

* Use **cron job**:

  ```
  0 8 * * * /usr/bin/python3 /path/to/scripts/generate_report.py
  ```

---

## **11.4 Final Project Deliverables**

| **Deliverable**          | **Description**                          |
| ------------------------ | ---------------------------------------- |
| `cleaned_sales_data.csv` | Cleaned dataset                          |
| `eda.py`                 | EDA visualizations                       |
| `dashboard.py`           | Interactive Streamlit dashboard          |
| `sales_summary.pdf`      | Generated report                         |
| `generate_report.py`     | Automated report generation script       |
| Documentation            | Project report with workflow explanation |

---

## **11.5 Presentation Structure**

When presenting your project:

1. **Introduction**

   * Brief overview of problem and goals.
2. **Data Understanding**

   * Show raw vs cleaned data.
3. **EDA Results**

   * Present key findings visually.
4. **Dashboard Demo**

   * Live demo of Streamlit dashboard.
5. **Automation Showcase**

   * Run report generation live.
6. **Recommendations**

   * Suggest business actions based on findings.

---

## **11.6 Capstone Evaluation Criteria**

| **Area**                     | **Points** |
| ---------------------------- | ---------- |
| Data Cleaning & Preparation  | 20         |
| EDA & Insights               | 20         |
| Dashboard Functionality      | 20         |
| Automated Reporting          | 20         |
| Documentation & Presentation | 20         |
| **Total**                    | **100**    |

---

## **11.7 Wrap-Up and Next Steps**

Congratulations! 🎉
You’ve now:

* Learned **Python for data analysis** end-to-end.
* Built a **real-world project** with dashboards and reports.
* Automated reporting to save time.

### **Next Steps:**

1. **Deploy Streamlit app** to [Streamlit Cloud](https://streamlit.io/cloud) or Heroku.
2. **Learn cloud services** like AWS or Azure for hosting and scaling.
3. **Integrate with live data sources**, such as APIs or databases.
4. Explore **machine learning techniques** for predictive analytics.

---

## **11.8 Final Checklist**

* [ ] Dataset cleaned and saved
* [ ] EDA notebook completed with visuals
* [ ] Streamlit dashboard built and running
* [ ] PDF report generation working
* [ ] Email automation set up
* [ ] Project documented and ready for presentation

---

Would you like me to generate a **one-page summary PDF template** for your final project documentation?
