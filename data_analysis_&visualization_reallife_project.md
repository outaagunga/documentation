
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
---
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
import pandas as pd

# Load dataset
df = pd.read_excel("bankdata.xlsx", sheet_name="bankdata")

# --- BEFORE CLEANING ---
print("Initial rows:", len(df))

# Remove duplicate transactions (based on transaction ID)
df = df.drop_duplicates(subset=['trxn_id'])
print("After removing duplicates:", len(df))

# Handle missing values (optional — drop rows with critical missing info)
df = df.dropna(subset=['timestamp', 'amount', 'currency', 'acct_id'])

# --- FEATURE ENGINEERING ---
# Extract useful time-based features
df['hour'] = df['timestamp'].dt.hour
df['day'] = df['timestamp'].dt.day_name()

# Basic currency conversion to KES (use approximate rates)
conversion = {'USD': 150, 'EUR': 160, 'KES': 1}
df['amount_kes'] = df['amount'] * df['currency'].map(conversion)

# --- CLEANUP CHECKS ---
print("\nPreview of cleaned data:")
print(df.head())

print("\nMissing values per column:")
print(df.isnull().sum())

# --- SAVE CLEANED FILE ---
df.to_csv("cleaned_bankdata.csv", index=False)
print("\n✅ Cleaned dataset saved as 'cleaned_bankdata.csv'")

```

---

### 🧮 **Step 2: Detect Anomalies**

You can define “fraudulent” or suspicious transactions as:

* Amounts greater than 3× a customer’s average
* Unusual hours (e.g. between midnight and 5 AM)
* Transactions from multiple IPs or locations in a short period

Example:

```python
# This code will do everything from to showing the suspicious transactions.....
import pandas as pd

# === STEP 1: LOAD DATA ===
df = pd.read_excel("bankdata.xlsx", sheet_name="bankdata")

print("Initial rows:", len(df))

# === STEP 2: CLEANING ===
# Remove duplicates based on transaction ID
df = df.drop_duplicates(subset=['trxn_id'])
print("After removing duplicates:", len(df))

# Drop rows with critical missing values
df = df.dropna(subset=['timestamp', 'amount', 'currency', 'acct_id'])

# Extract time features
df['hour'] = df['timestamp'].dt.hour
df['day'] = df['timestamp'].dt.day_name()

# Currency conversion to KES (approximate rates)
conversion = {'USD': 150, 'EUR': 160, 'KES': 1}
df['amount_kes'] = df['amount'] * df['currency'].map(conversion)

# === STEP 3: ANOMALY DETECTION ===
# Calculate average transaction per account
avg_amounts = df.groupby('acct_id')['amount_kes'].mean()

# Map each customer's average back to main DataFrame
df['avg_amount'] = df['acct_id'].map(avg_amounts)

# Flag suspicious transactions:
#   - Any transaction 3x higher than the customer's average
#   - Any transaction made before 5 AM
df['is_suspicious'] = (df['amount_kes'] > 3 * df['avg_amount']) | (df['hour'] < 5)

# === STEP 4: RESULTS SUMMARY ===
total_txns = len(df)
suspicious_txns = df['is_suspicious'].sum()
print(f"\n🚨 Suspicious transactions detected: {suspicious_txns} out of {total_txns} ({(suspicious_txns/total_txns)*100:.2f}%)")

# Show top 10 suspicious transactions
print("\nSample of suspicious transactions:")
print(df[df['is_suspicious']].head(10)[['timestamp', 'acct_id', 'amount_kes', 'avg_amount', 'hour', 'place', 'trxn_type']])

# === STEP 5: SAVE RESULTS ===
df.to_csv("fraud_detection_results.csv", index=False)
print("\n✅ Results saved to 'fraud_detection_results.csv'")

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
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# === STEP 1: LOAD CLEANED/FLAGGED DATA ===
# (Use the output from the previous script)
df = pd.read_csv("fraud_detection_results.csv")

# Set general style
sns.set(style="whitegrid", palette="muted")

# === STEP 2: HISTOGRAM OF TRANSACTION AMOUNTS ===
plt.figure(figsize=(10,6))
sns.histplot(df['amount_kes'], bins=50, kde=True)
plt.title("Distribution of Transaction Amounts (KES)")
plt.xlabel("Transaction Amount (KES)")
plt.ylabel("Frequency")
plt.tight_layout()
plt.show()

# === STEP 3: BOXPLOT — OUTLIERS PER TRANSACTION TYPE ===
plt.figure(figsize=(10,6))
sns.boxplot(x='trxn_type', y='amount_kes', data=df)
plt.title("Transaction Amounts by Transaction Type")
plt.xlabel("Transaction Type")
plt.ylabel("Amount (KES)")
plt.tight_layout()
plt.show()

# === STEP 4A: BAR CHART — SUSPICIOUS TRANSACTIONS BY LOCATION ===
suspicious_by_location = df[df['is_suspicious']].groupby('place').size().sort_values(ascending=False)
plt.figure(figsize=(10,6))
sns.barplot(x=suspicious_by_location.values, y=suspicious_by_location.index)
plt.title("Number of Suspicious Transactions by Location")
plt.xlabel("Count of Suspicious Transactions")
plt.ylabel("Location")
plt.tight_layout()
plt.show()

# === STEP 4B: BAR CHART — SUSPICIOUS TRANSACTIONS BY HOUR ===
suspicious_by_hour = df[df['is_suspicious']].groupby('hour').size()
plt.figure(figsize=(10,6))
sns.barplot(x=suspicious_by_hour.index, y=suspicious_by_hour.values)
plt.title("Suspicious Transactions by Hour of Day")
plt.xlabel("Hour of Day")
plt.ylabel("Number of Suspicious Transactions")
plt.tight_layout()
plt.show()

print("\n✅ All visualizations generated successfully.")

```
**🧠 Notes:**
- Run this after your anomaly detection script — it uses fraud_detection_results.csv.
- The charts will open automatically in your Python environment.
- You can right-click and save each chart as an image (PNG/JPG) for your report or Power BI dashboard.

---

### 📈 **Step 4: Power BI Dashboard Ideas**

After exporting your cleaned data to CSV, import it into Power BI and build:

1. KPI card — number of suspicious transactions
2. Map — locations of red-flagged transactions
3. Bar chart — fraud frequency by hour or device
4. Donut chart — transaction types (ATM, MOBILE, POS)

**what to place on the X-axis, Y-axis, and Values** for each Power BI visual.

### 🧮 1. **KPI Card — Suspicious Transactions**

**Purpose:** Show how many transactions were flagged as suspicious.

**Fields:**

* **Values:**
  → `is_suspicious` (set to count or count of TRUE values)
* **Alternative:**
  → Use a *measure* like

  ```DAX
  Suspicious Count = COUNTROWS(FILTER(bankdata, bankdata[is_suspicious] = TRUE()))
  ```

**Tip:**
Format as a big number card, e.g.
🟥 **Suspicious Transactions: 152**

---

### 🗺️ 2. **Map — Locations of Red-Flagged Transactions**

**Purpose:** Show where suspicious transactions are happening.

**Fields:**

* **Location (Map visual’s Location field):** `place`
* **Values (Bubble Size):** Count of `trxn_id` (filtered where `is_suspicious` = TRUE)

**Optional Filters:**

* Add a **filter pane** for `is_suspicious = TRUE`
* Add a **Legend:** `trxn_type` (to see type of fraud per location)

---

### 📊 3. **Bar Chart — Fraud Frequency by Hour or Device**

#### Option A: By Hour of Day

**Purpose:** Detect unusual activity times.

**Fields:**

* **X-axis:** `hour`
* **Y-axis:** Count of `trxn_id`
* **Filters:** `is_suspicious = TRUE`

💡 Insight Example: “Most fraud alerts occur between 00:00 – 05:00.”

#### Option B: By Device Type

**Purpose:** See which channels have more suspicious activity.

**Fields:**

* **X-axis:** `trxn_type` (ATM, MOBILE, POS)
* **Y-axis:** Count of `trxn_id`
* **Filters:** `is_suspicious = TRUE`

💡 Insight Example: “Mobile transactions show more anomalies than ATM or POS.”

---

### 🍩 4. **Donut Chart — Transaction Type Breakdown**

**Purpose:** Show share of total transactions by type.

**Fields:**

* **Legend (Category):** `trxn_type`
* **Values:** Count of `trxn_id`

**Optional Variations:**

* For fraud share only → filter to `is_suspicious = TRUE`
* For all transactions → leave unfiltered for comparison

---

### 🧱 5. **Recommended Layout (Dashboard Page)**

| Section      | Visual Type | Focus                         |
| ------------ | ----------- | ----------------------------- |
| Top Left     | KPI Card    | Total Suspicious Transactions |
| Top Right    | Donut Chart | % by Transaction Type         |
| Bottom Left  | Map         | Fraud by Location             |
| Bottom Right | Bar Chart   | Fraud Frequency by Hour       |

---  
Perfect 👌 — let’s design a **professional, presentation-ready Power BI dashboard layout** for your **Equity Bank Fraud Detection Project**.
This guide gives you:

✅ Recommended **colors and theme**
✅ **Visual layout design** (placement & size)
✅ **Titles, formatting, and labels**
✅ Optional storytelling elements for your 3–5 minute presentation

---

## 🎨 **1. Dashboard Theme and Color Scheme**

Choose a modern, clean theme inspired by **Equity Bank’s colors**:

| Element          | Color                       | Usage                             |
| ---------------- | --------------------------- | --------------------------------- |
| Primary Accent   | 🔴 **#A10000** (deep red)   | Highlight suspicious transactions |
| Secondary Accent | ⚫ **#333333**               | Titles and text                   |
| Background       | ⚪ **#F9F9F9** or light grey | Clean, minimal look               |
| Safe Color       | 🟢 **#009D57**              | For normal transactions           |
| Neutral          | 🟣 **#B0B0B0**              | Background visual elements        |

💡 **In Power BI:**
Go to **View → Themes → Customize current theme → Colors**, and set these.

---

## 🧱 **2. Dashboard Layout (1-page)**

### 🖥️ **Canvas Setup**

* Page Size: **16:9 (Widescreen)**
* Background: Light grey (`#F9F9F9`)
* Add a subtle border or rectangle header with the title in white on red background

---

### 📌 **Top Section — Header Bar**

**Add a shape (Rectangle):**

* Fill color: Deep red `#A10000`
* Text:
  **“Equity Bank: Fraud Detection Dashboard (Nov 2025)”**
  *(Font: Segoe UI Bold, 20pt, White)*

Add subtitle text box below:
🕵️ *Detecting anomalies in mobile, ATM, and POS transactions.*

---

### 📊 **Main Visual Grid**

| Position         | Visual             | Description                              | Fields                                                                          |
| ---------------- | ------------------ | ---------------------------------------- | ------------------------------------------------------------------------------- |
| **Top Left**     | 🧮 **KPI Card**    | **Suspicious Transactions Count**        | Values: `is_suspicious` (Count TRUE)                                            |
| **Top Right**    | 🍩 **Donut Chart** | **Transaction Breakdown by Type**        | Legend: `trxn_type`<br>Values: Count of `trxn_id`                               |
| **Bottom Left**  | 🗺️ **Map**        | **Locations of Suspicious Transactions** | Location: `place`<br>Values: Count of `trxn_id` (Filtered `is_suspicious=TRUE`) |
| **Bottom Right** | 📊 **Bar Chart**   | **Suspicious Transactions by Hour**      | X-axis: `hour`<br>Y-axis: Count of `trxn_id`<br>Filter: `is_suspicious=TRUE`    |

---

### 🧠 **Optional Extras**

| Add-on                      | What it shows                                          | How to create                                       |
| --------------------------- | ------------------------------------------------------ | --------------------------------------------------- |
| **Tooltip Chart**           | When hovering over a location, show count by type      | Enable Tooltip Page                                 |
| **Trend Line (Line Chart)** | Suspicious transactions by day                         | X: `timestamp (date)` → Y: Count of `is_suspicious` |
| **Filter Pane (Slicers)**   | User can filter by `trxn_type`, `place`, or `currency` | Add slicers for interactivity                       |

---

## 🗣️ **3. Dashboard Storytelling (for 3–5 min Presentation)**

Here’s a suggested **presentation script flow** you can follow:

> **Slide 1:**
> “Our project focuses on detecting fraudulent transactions at Equity Bank using data analytics.”

> **Dashboard Intro:**
> “This Power BI dashboard summarizes over 1,900 transactions from mobile, POS, and ATM channels.”

> **KPI Card:**
> “We detected *X suspicious transactions* based on unusually high amounts or off-hour activity.”

> **Bar Chart (Hour):**
> “Notice the spike between midnight and 4 AM — this suggests fraud tends to occur outside business hours.”

> **Map:**
> “Nairobi, Nakuru, and Eldoret show the highest frequency of red-flagged activity.”

> **Donut Chart:**
> “Mobile transactions account for the largest share of both total and suspicious activity.”

> **Conclusion:**
> “This simple analytics system can serve as an early-warning tool for fraud detection and risk monitoring.”

---

## 🧰 **4. Finishing Touches**

✅ Titles:

* Use **bold** white text on colored backgrounds.
* Add icons for visuals (e.g., 🔴 for fraud, 🗺️ for map).

✅ Formatting:

* Align visuals neatly with consistent spacing.
* Use **rounded corners (radius 10–20)** for cards.

✅ Fonts:

* Titles: Segoe UI Bold (14–20pt)
* Labels: Segoe UI Regular (10–12pt)

---

Would you like me to give you a **Power BI layout wireframe (mockup image)** showing the recommended visual positions and color design — so you can visually replicate it in Power BI?

---

### 🧩 **Step 5: Report Summary**

Your report sections can be:

--- 
