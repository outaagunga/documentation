
# Data Analysis Real life Project: 

---

## 🧭 Step-by-Step Guide

### **1. Understand the Problem**

**Goal:** Detect potentially fraudulent bank transactions.

**Focus:** Find “unusual” or “anomalous” transactions that deviate from a customer’s normal behavior — e.g.,

* unusually high transaction amounts
* transactions at odd hours
* transactions from strange locations/devices

👉 Deliverable: A *problem statement paragraph* in your report.

---

### **2. Get or Create a Dataset**

You can **simulate** data if you don’t have a real dataset.

**Recommended columns:**

| Column Name    | Example             | Description               |
| -------------- | ------------------- | ------------------------- |
| transaction_id | T0001               | Unique transaction        |
| customer_id    | C123                | Customer identifier       |
| timestamp      | 2025-11-05 23:45:00 | Date/time of transaction  |
| amount         | 12500               | Transaction value         |
| device         | ATM / Mobile / POS  | Channel used              |
| location       | Nairobi / Mombasa   | City/branch               |
| balance        | 10500               | Remaining account balance |

👉 Deliverable: A `.csv` file called `transactions.csv`
e.g (Example of transaction dataset)




---

### **3. Clean the Data (Python / Pandas)**

Use Python and Pandas to:

* Handle missing values
* Format timestamps properly (`pd.to_datetime()`)
* Convert amounts to numeric
* Remove duplicates

**Example code:**

```python
import pandas as pd

df = pd.read_csv("transactions.csv")
df['timestamp'] = pd.to_datetime(df['timestamp'])
df = df.drop_duplicates().dropna()
```

👉 Deliverable: Clean dataset and summary in report (include before/after table or chart).

---

### **4. Analyze & Detect Anomalies**

Use **Python + SQL** to find irregularities.

#### **a. SQL: Identify average spending per customer**

Example:

```sql
SELECT customer_id, AVG(amount) AS avg_amount, MAX(amount) AS max_amount
FROM transactions
GROUP BY customer_id;
```

#### **b. Python: Detect anomalies**

Flag transactions that are, say, **3× higher than a customer’s average** or occur at **unusual hours**.

```python
df['hour'] = df['timestamp'].dt.hour
df['is_anomaly'] = (df['amount'] > 3 * df['amount'].mean()) | (df['hour'] < 5)
```

👉 Deliverable: Summary table or visualization showing flagged transactions.

---

### **5. Visualize with Power BI**

Import the cleaned dataset into Power BI.

**Dashboards to include:**

* 📊 Bar chart — transaction volume by hour/day
* 📈 Line chart — customer spending patterns
* 🗺️ Map — transaction locations
* 🔴 Card or KPI — number of “suspicious” transactions

👉 Deliverable: Interactive Power BI dashboard (share screenshot or `.pbix` file).

---

### **6. Report Writing (Max 6 pages)**

Structure your report as follows:

**Page 1:** Cover Page (Group name, project title, members)
**Page 2:** Problem definition, objectives, dataset summary
**Page 3:** Tools used (Python, SQL, Power BI) and technical skills
**Page 4:** Data cleaning & analysis steps (include screenshots or charts)
**Page 5:** Key findings, Power BI insights
**Page 6:** Challenges & Lessons learned

👉 Deliverable: A clean, professional report (Word or PDF, 6 pages max)

---

### **7. Presentation (3–5 min Storytelling)**

One group member should:

* Start with the *fraud problem at Equity Bank*
* Show one or two visuals from Power BI
* Explain one case of a suspicious transaction
* End with: “Our model helps detect early fraud warnings to protect customers.”

👉 Deliverable: Clear, simple slides or live dashboard demo.

---

### **8. Required Lists in Report**

* **Technical skills:** e.g., Python (data cleaning), SQL (aggregation), Power BI (dashboarding)
* **Soft skills:** teamwork, critical thinking, communication, problem-solving
* **Top 3 challenges:** e.g., data inconsistency, time constraints, visualization errors

---

## Steps to do it:
A quick summary of my dataset what you have and how to proceed next:

---

### 🧾 **Dataset Overview**

✅ **Rows:** 1,908
✅ **Columns (12):**

| Column                         | Description                   |
| ------------------------------ | ----------------------------- |
| `timestamp`                    | Date and time of transaction  |
| `trxn_id`                      | Unique transaction identifier |
| `acct_id`                      | Account number (anonymized)   |
| `name`                         | Customer name                 |
| `email`, `phone`, `ip`, `card` | Customer/device identifiers   |
| `amount`                       | Transaction amount            |
| `currency`                     | e.g. KES, USD, EUR            |
| `place`                        | Transaction location          |
| `trxn_type`                    | Channel (ATM, MOBILE, POS)    |

---

### 🧹 **Next Step 1: Data Cleaning**

You’ll need to:

1. Convert all timestamps to uniform datetime format (already done ✅).
2. Standardize currency (convert to one base, e.g., KES).
3. Check for duplicates or outliers in `trxn_id` or `amount`.
4. Optionally, extract **hour**, **day**, and **weekday** for behavior analysis.

Example Python code:

```python
# Remove duplicates
df = df.drop_duplicates(subset=['trxn_id'])

# Create new columns
df['hour'] = df['timestamp'].dt.hour
df['day'] = df['timestamp'].dt.day_name()

# Basic currency conversion (example rates)
conversion = {'USD': 150, 'EUR': 160, 'KES': 1}
df['amount_kes'] = df['amount'] * df['currency'].map(conversion)
```

---

### 🧮 **Step 2: Detect Anomalies**

You can define “fraudulent” or suspicious transactions as:

* Amounts greater than 3× a customer’s average
* Unusual hours (e.g. between midnight and 5 AM)
* Transactions from multiple IPs or locations in a short period

Example:

```python
avg_amounts = df.groupby('acct_id')['amount_kes'].mean()
df['avg_amount'] = df['acct_id'].map(avg_amounts)
df['is_suspicious'] = (df['amount_kes'] > 3 * df['avg_amount']) | (df['hour'] < 5)
```

Then show top flagged:

```python
df[df['is_suspicious']].head()
```

---

### 📊 **Step 3: Visualize (Matplotlib/Seaborn or Power BI)**

Some useful visualizations:

* **Histogram** of transaction amounts
* **Boxplot** to see outliers per transaction type
* **Bar chart** of suspicious transactions by location or hour

Example in Python:

```python
import seaborn as sns
import matplotlib.pyplot as plt

sns.boxplot(x='trxn_type', y='amount_kes', data=df)
plt.title('Transaction Amounts by Type')
plt.show()
```

---

### 📈 **Step 4: Power BI Dashboard Ideas**

After exporting your cleaned data to CSV, import it into Power BI and build:

1. KPI card — number of suspicious transactions
2. Map — locations of red-flagged transactions
3. Bar chart — fraud frequency by hour or device
4. Donut chart — transaction types (ATM, MOBILE, POS)

---

### 🧩 **Step 5: Report Summary**

Your report sections can be:

--- 
