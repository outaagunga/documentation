Perfect! 🚀 Since you’ve provided your dataset (**Employee Sample Data.xlsx**), I’ll act as your **personal Power BI instructor** and walk you step-by-step through the entire lesson plan.

We’ll use your dataset for hands-on practice while covering each module. Let’s start with **Module 1: Introduction to Power BI**. I’ll give you **clear tasks** and the **exact steps** you should perform in Power BI Desktop.

---

# 📘 Module 1: Introduction to Power BI

## 1.1 Overview of Power BI

### 1.1.1 What is Business Intelligence (BI)?

**Concept:**
Business Intelligence (BI) is about turning raw data into meaningful insights using reporting, dashboards, and analysis. Power BI is one of the most popular BI tools.

**Task:**
👉 Open your Excel dataset and think about **3 questions a manager might want answered**. For example:

* How many employees are in each department?
* What’s the average salary per job role?
* What’s the gender distribution of employees?

We’ll answer these later with Power BI.

---

### 1.1.2 Power BI ecosystem (Desktop, Service, Mobile, Gateway)

**Concept:**

* **Power BI Desktop** → Where you build reports (what we’ll use now).
* **Power BI Service** → Online platform for sharing dashboards.
* **Power BI Mobile** → View reports on phones/tablets.
* **Power BI Gateway** → Connect on-premises databases to Power BI Service.

**Task:**
👉 Note: You’ll start with **Power BI Desktop**, then later publish your reports to **Power BI Service** for sharing.

---

### 1.1.3 Use cases of Power BI in business and industry

**Concept:**

* HR Analytics (employee turnover, diversity metrics)
* Finance (budget vs actuals)
* Sales (revenue by region)
* Operations (supply chain performance)

**Task with your dataset:**
👉 Write down 2 possible HR dashboards you’d like to create (e.g., Employee demographics, Attrition analysis). We’ll build them later.

---

### 1.1.4 Licensing and pricing tiers

**Concept:**

* **Free** → Create in Desktop, share locally.
* **Pro** → Publish to Power BI Service, collaborate.
* **Premium** → Enterprise scale, AI features.

**Task:**
👉 Check if you have a Microsoft 365 account—this gives you some Power BI Service features. If not, you’ll stay in Desktop for now.

---

## 1.2 Installing and Setting Up Power BI Desktop

### 1.2.1 System requirements

* Windows 10/11 (Power BI doesn’t run on Mac directly without workaround).
* 4GB RAM minimum (8GB recommended).

**Task:**
👉 Confirm your system has these specs.

---

### 1.2.2 Downloading and installing Power BI Desktop

* Download from: [Power BI Desktop Download](https://powerbi.microsoft.com/desktop/).
* Install like any Windows app.

**Task:**
👉 Open Power BI Desktop after installation.

---

### 1.2.3 Navigating the interface

* **Ribbon (top menu):** File, Home, Insert, Modeling, View.
* **Fields pane (right side):** Lists your dataset’s columns.
* **Visualizations pane (middle right):** All charts and visuals.
* **Report canvas (center):** Where you build your dashboard.

**Task with your dataset:**

1. Open Power BI Desktop.
2. Go to **Home → Get Data → Excel**.
3. Browse to your file **Employee Sample Data.xlsx**.
4. Select your employee table (likely named `Sheet1` or `Employee Data`).
5. Click **Load**.

✅ Your dataset will now appear in the **Fields pane**.

---

### 1.2.4 File formats

* **.pbix** → Main Power BI file (contains report, data, model).
* **.pbit** → Template (no data, only structure).
* **.pbids** → Data source connection file.

**Task:**
👉 Save your report as `Employee_Report.pbix`.

---

✅ At this point, you’ve **completed Module 1**. You’ve installed Power BI, loaded your dataset, and learned the basics of the environment.

---

👉 Would you like me to now continue with **Module 2: Getting Data into Power BI** and show you how to practice importing your Excel file plus other formats (CSV, web data, etc.)?

Great! Let’s dive into **Module 2: Getting Data into Power BI**. We’ll use your **Employee Sample Data.xlsx** but also practice with other formats so you can master data loading.

---

# 📘 Module 2: Getting Data into Power BI

---

## 2.1 Data Sources Overview

### 2.1.1 Types of data sources

**Concept:** Power BI connects to:

* **Files** → Excel, CSV, JSON, XML, PDF
* **Databases** → SQL Server, MySQL, PostgreSQL
* **Online services** → Google Analytics, Salesforce, Dynamics 365
* **Cloud** → Azure, SharePoint, OneDrive

**Task (Your dataset):**

1. Go to **Home → Get Data → Excel**.
2. Select **Employee Sample Data.xlsx** → Load the employee table.
3. Check your **Fields pane** to confirm columns are available.

---

### 2.1.2 Import vs DirectQuery vs Live Connection

**Concept:**

* **Import (default):** Data is copied into Power BI → fast, but must refresh.
* **DirectQuery:** Data stays in source (e.g., SQL Server) → always up-to-date, but slower.
* **Live Connection:** Connects to Analysis Services models only.

**Task:**
👉 Since you’re using Excel, Power BI uses **Import mode** automatically.
Later, when we connect to databases, we’ll test DirectQuery.

---

### 2.1.3 Connecting to multiple sources

**Concept:** You can bring in **more than one data source** and combine them.

**Task (Practice):**

1. Go to **Home → Get Data → Text/CSV**.
2. Import a simple CSV file (if you don’t have one, quickly create a 3-column CSV in Excel like: Department, Budget, Year).
3. Load both **Employee Data** and your **CSV**.
4. Notice both appear in the **Fields pane** under separate tables.

---

## 2.2 File-Based Data Sources

### 2.2.1 Excel files

**Task with your file:**

1. In **Navigator**, expand your workbook → select only the employee table.
2. If your workbook had multiple sheets, you could choose several.
3. Load → check if all employees appear in **Data view** (left panel, table icon).

---

### 2.2.2 CSV/TSV/Text files

**Practice task:**

1. Go to **Get Data → Text/CSV**.
2. Load your sample CSV (or download a free HR CSV from Kaggle).
3. Confirm Power BI detects the delimiter automatically.

---

### 2.2.3 XML and JSON files

**Practice task (optional):**

* If you don’t have XML/JSON, you can download sample data from Microsoft:

  * [Sample JSON](https://raw.githubusercontent.com/microsoft/PowerBI-Developer-Samples/master/Sandbox/CustomVisualsPlayground/src/assets/SampleData.json)
  * [Sample XML](https://people.sc.fsu.edu/~jburkardt/data/xml/cities.xml)

1. Get Data → JSON/XML.
2. Observe how Power BI transforms the hierarchical structure into tables.

---

### 2.2.4 PDFs

**Task:**

1. Go to **Get Data → PDF**.
2. Try opening any report in PDF (like a payslip or HR report).
3. Power BI will detect tables inside → select one → Load.

---

## 2.3 Database Connections

*(Since you have only Excel now, I’ll give you steps to try later when you connect to a database.)*

### 2.3.1 SQL Server

**Task:**

* Go to **Home → Get Data → SQL Server**.
* Enter `Server name` (you can test with `localhost` if SQL installed).
* Choose **Import** or **DirectQuery**.

---

### 2.3.2 MySQL, PostgreSQL, Oracle

* Same process → choose database type, enter credentials.

---

### 2.3.3 Microsoft Access

* **Get Data → Access Database** → Browse → Load.

---

### 2.3.4 ODBC connections

* **Get Data → ODBC** → Choose preconfigured DSN.

---

## 2.4 Online and Cloud Services

### 2.4.1 SharePoint & OneDrive

* If your file is in OneDrive/SharePoint → you can connect directly.
* Benefit: Automatic refresh when file changes.

---

### 2.4.2 Azure data sources

* Options include **Azure SQL DB, Azure Blob Storage, Azure Data Lake**.

---

### 2.4.3 Web data scraping

**Practice task (fun):**

1. **Get Data → Web**.
2. Enter: `https://en.wikipedia.org/wiki/List_of_countries_by_population_(United_Nations)`
3. In Navigator → select a table → Load.
   👉 You just scraped live data into Power BI!

---

### 2.4.4 Google Analytics, Salesforce, Dynamics 365

* Connectors available in **Get Data → Online Services**.
* Requires login credentials.

---

✅ At this point, you’ve **completed Module 2**. You now know how to connect to Excel (your dataset), CSV, web data, and multiple sources.

---

👉 Do you want me to continue with **Module 3: Data Preparation with Power Query**, where we’ll clean and transform your Employee dataset step-by-step? That’s where the real fun starts—removing duplicates, handling missing values, and shaping data for analysis.


Perfect timing 😃 — now we get into the **heart of Power BI**: cleaning and shaping your data with **Power Query**. We’ll use your **Employee Sample Data.xlsx** throughout this module so you can follow along step by step.

---

# 📘 Module 3: Data Preparation with Power Query

---

## 3.1 Power Query Editor Basics

### 3.1.1 Navigating Power Query interface

**Steps:**

1. In Power BI Desktop → go to **Home → Transform Data**.
2. The **Power Query Editor** opens.

   * **Left pane:** Query list (your tables).
   * **Center pane:** Data preview.
   * **Right pane:** Applied Steps (records each transformation).

**Task:** 👉 Explore the Applied Steps pane as you load your data.

---

### 3.1.2 Applied steps concept

* Each change (remove column, rename, filter) is stored as a **step**.
* Power BI replays these steps automatically on refresh.

**Task:** 👉 Try renaming a column (e.g., `Emp ID` → `EmployeeID`) → notice a new **Renamed Columns** step appears.

---

### 3.1.3 Undoing/redoing transformations

* To undo → click the **X** next to the step in Applied Steps.
* To reorder → drag steps up/down.

**Task:** 👉 Remove a column → then undo it by deleting the step.

---

## 3.2 Data Cleaning

### 3.2.1 Removing duplicates and blanks

**Steps with Employee Data:**

1. Select the **EmployeeID** column.
2. Go to **Home → Remove Rows → Remove Duplicates**.
3. To remove blanks: **Home → Remove Rows → Remove Blank Rows**.

**Further Cleaning**

If your dataset comes from an online source such as Google Forms, you may encounter mixed data types within columns. For example:

* **Column 1:** [KES. 1100], [KES. 1010], [I don’t know]
* **Column 2:** [Yes, regularly], [Yes, occasionally], [No, never]
* **Column 3:** [Yes, I consent], [No, I don’t consent], [Prefer not to say]

To analyze your data correctly, each column must contain values of the **same data type** — for instance, all numbers, all text, or all dates. Mixed data types (e.g., text and numbers in the same column) prevent proper analysis.

Here’s how to clean and standardize such data in **Power BI Desktop**:

1. **Check column data types:**

   * Right-click the column header → **Change Type** → select the appropriate data type (e.g., Text, Number, Date).

2. **Standardize inconsistent values:**

   * Right-click the same column → **Replace Values** → enter the old and new values.

   **Examples:**

   * *Column 1:* Convert to text and simplify values to **“Correct,” “Incorrect,” “I don’t know.”**
   * *Column 2:* Simplify to **“Yes,” “Yes,” “No.”**
   * *Column 3:* Standardize to **“Yes,” “No,” “Prefer not to say.”**

3. **Validate your data:**

   * Confirm all entries are now consistent and in the correct format before creating visuals.

This ensures Power BI can correctly interpret and visualize your data without errors.

---

### 3.2.2 Handling missing values

**Steps:**

1. Right-click a column (e.g., Salary).
2. Choose **Replace Values** → replace `null` with `0` or `"Unknown"`.
3. Or use **Transform → Fill → Down/Up** to fill missing data.

---

### 3.2.3 Changing data types

**Steps:**

1. Look at each column’s icon (ABC = text, 123 = number, calendar = date).
2. If wrong → click the icon → choose correct type (e.g., Salary = Decimal, HireDate = Date).

---

### 3.2.4 Standardizing formats

**Steps:**

1. For names: **Transform → Format → Capitalize Each Word**.
2. For trimming spaces: **Transform → Format → Trim**.
3. For dates: **Transform → Date → Year/Month/Day**.

---

## 3.3 Data Transformation

### 3.3.1 Splitting and merging columns

**Task:**

* If you have a `FullName` column → select it → **Home → Split Column → By Delimiter (space)** → creates FirstName, LastName.
* Merge: Hold **Ctrl**, select two columns (FirstName + LastName) → **Transform → Merge Columns**.

---

### 3.3.2 Unpivoting and pivoting

**Task:**

* Example: If you had columns like `2023 Sales, 2024 Sales`, select them → **Transform → Unpivot Columns** → turns into Year/Sales rows.
* To reverse: **Transform → Pivot Column**.

---

### 3.3.3 Grouping and aggregating

**Task:**

1. Select Department.
2. **Home → Group By** → choose Salary → Average.
   👉 This gives **average salary per department**.

---

### 3.3.4 Conditional & custom columns

**Task:**

1. **Add Column → Conditional Column** → e.g., If Salary > 5000 then `"High"` else `"Low"`.
2. For custom: **Add Column → Custom Column** → `=[Salary] * 0.1` (for bonus).

---

## 3.4 Combining Data

### 3.4.1 Merge Queries (joins)

**Task:**

1. Load both **Employee Data** and your test CSV (Dept Budget).
2. In Power Query → **Home → Merge Queries**.
3. Join on `Department`.
4. Choose type: **Left Join** → keeps all employees, adds budget info.

---

### 3.4.2 Append Queries

**Task:**

1. Suppose you had Employee Data 2023 and Employee Data 2024.
2. **Home → Append Queries** → combine into one long table.

---

### 3.4.3 Using Parameters

**Steps:**

1. **Manage Parameters → New Parameter** → e.g., “Year” with values 2023, 2024.
2. Use this parameter in filtering HireDate column.

---

### 3.4.4 Query dependencies

* **View → Query Dependencies** shows how tables connect (like a flowchart).

**Task:** 👉 Open it and check how Employee Data and Budget data relate.

---

## 3.5 Advanced Power Query (M Language)

### 3.5.1 Introduction to M

* Power Query transformations are written in **M language**.

### 3.5.2 Editing M code

**Steps:**

1. Go to **View → Advanced Editor**.
2. You’ll see code like:

   ```m
   = Table.RemoveColumns(#"Changed Type",{"Column1"})
   ```

---

### 3.5.3 Creating custom functions

* Example: Define a function to calculate bonus as `Salary * 0.1`.

---

### 3.5.4 Reusing queries

* Right-click query → Duplicate or Reference → reuse for different transformations.

---

✅ At this point, you’ve **cleaned and transformed your Employee dataset**. It’s now ready for **data modeling (Module 4)**.

---

👉 Do you want me to continue with **Module 4: Data Modeling in Power BI** (relationships, calculated columns, hierarchies) using your Employee dataset?


Perfect 👍 Now that your **Employee dataset** is cleaned in Power Query, let’s move into **Module 4: Data Modeling in Power BI**.

This is where you’ll structure your data so that reports and visuals make sense. Think of this step as building the **foundation** of your HR analytics dashboard.

---

# 📘 Module 4: Data Modeling in Power BI

---

## 4.1 Understanding Data Models

### 4.1.1 Tables, relationships, and schemas

**Concept:**

* **Tables**: Employee data, Department budgets, etc.
* **Relationships**: How tables connect (Employee.Department → DepartmentBudget.Department).
* **Schema**: Visual map of relationships.

**Task with Employee Data:**

1. Go to **Model view** (left sidebar, 3rd icon: relationship diagram).
2. You’ll see your **Employee table**.
3. If you also imported the Department Budget CSV earlier → drag `Department` from Employee table to `Department` in Budget table.
   👉 You just created a **relationship**.

---

### 4.1.2 Star schema vs Snowflake schema

* **Star schema**: One fact table (Employees, with measures like Salary) linked to dimension tables (Department, Gender).
* **Snowflake schema**: Dimensions broken into sub-dimensions (Department → Division → Region).

**Task:** 👉 Sketch your Employee dataset:

* **Fact**: Employee table (Salary, HireDate).
* **Dimensions**: Department, Gender.

---

### 4.1.3 Normalization vs Denormalization

* **Normalized**: Data split into many small tables (reduces redundancy).
* **Denormalized**: Data stored in fewer tables (faster for BI).

**Task:** 👉 Notice your Employee table is already **denormalized** (all info in one table).

---

## 4.2 Managing Relationships

### 4.2.1 One-to-one, one-to-many, many-to-many

**Task:**

* Employee.Department → Budget.Department is a **many-to-one** relationship (many employees belong to one department).
* Verify this in **Model view** (look for 1:\* symbols on the relationship line).

---

### 4.2.2 Active vs inactive relationships

* Power BI allows multiple relationships between two tables, but only one active at a time.
* Example: If Employee table had both `DeptID` and `ManagerDeptID`, only one link to Department can be active.

**Task (optional):** 👉 Try creating a second link and observe that one becomes dotted (inactive).

---

### 4.2.3 Cross-filter directions

* **Single direction**: Filters flow one way (safe, avoids loops).
* **Both directions**: Filters flow both ways (used in special cases like many-to-many).

**Task:** 👉 In Model view → select your relationship → check **Cross filter direction** in properties.

---

## 4.3 Calculated Columns & Measures

### 4.3.1 Difference between calculated columns and measures

* **Calculated column**: Stored in table, row by row (e.g., `Age = DATEDIFF([BirthDate], TODAY(), YEAR)`).
* **Measure**: Calculated on the fly, aggregates data (e.g., `TotalSalary = SUM(Employee[Salary])`).

---

### 4.3.2 Creating calculated columns

**Task:**

1. Go to **Data view** (table icon left).
2. Modeling → **New Column**.
3. Enter:

   ```DAX
   Age = DATEDIFF(Employee[HireDate], TODAY(), YEAR)
   ```

👉 Now every employee has an **Age in years** column.

---

### 4.3.3 Creating measures

**Task:**

1. Modeling → **New Measure**.
2. Enter:

   ```DAX
   Total Salary = SUM(Employee[Salary])
   ```
3. Add a **Card visual** → drag `Total Salary`.

👉 You just built your first **measure**.

---

### 4.3.4 Using quick measures

**Task:**

1. Right-click Employee table → **New Quick Measure**.
2. Choose **Average per category** → select Salary by Department.
3. Power BI auto-generates the DAX for you.

---

## 4.4 Hierarchies

### 4.4.1 Date hierarchies

**Task:**

1. In Fields pane → drag HireDate into a visual.
2. Expand it → you’ll see **Year → Quarter → Month → Day** hierarchy automatically.
   👉 Try a Line Chart with this.

---

### 4.4.2 Custom hierarchies

**Task:**

1. Right-click Employee table → **Create hierarchy**.
2. Add `Department → JobRole`.
3. Use it in a chart → drill from department down to roles.

---

### 4.4.3 Drill down functionality

**Task:**

1. Create a **Bar chart** with Department hierarchy.
2. Click the **drill button (down arrow)** in the chart.
   👉 You can now move from Department → JobRole.

---

✅ At this point, you’ve built a **data model**:

* Tables linked with relationships
* Calculated columns & measures
* Hierarchies for drill-down

This model is ready for **DAX analysis (Module 5)**.

---

👉 Do you want me to continue with **Module 5: DAX (Data Analysis Expressions)** and walk you through writing DAX formulas step by step on your Employee dataset?

Awesome 🚀 — now that your **data model** is ready, we’ll learn **DAX (Data Analysis Expressions)**. DAX is the formula language of Power BI and is used to create **calculated columns, measures, and tables**.

We’ll practice step by step using your **Employee dataset**.

---

# 📘 Module 5: DAX (Data Analysis Expressions)

---

## 5.1 Introduction to DAX

### 5.1.1 Syntax and basic structure

* DAX formula looks like Excel but works differently:

  ```DAX
  MeasureName = Function(Table[Column])
  ```

**Task:**

1. In **Modeling → New Measure**, create:

   ```DAX
   Total Employees = COUNTROWS(Employee)
   ```
2. Add a **Card visual** → drag `Total Employees`.
   👉 You now see the total number of employees.

---

### 5.1.2 Data types in DAX

DAX supports:

* Numeric (integer, decimal, currency)
* Text
* Date/time
* Boolean

**Task:**
👉 Create a new measure:

```DAX
Average Salary = AVERAGE(Employee[Salary])
```

Add it to a **Card visual** → check the data type is numeric.

---

### 5.1.3 Row context vs filter context

* **Row context** → applies to each row (calculated columns).
* **Filter context** → applies to aggregated results (measures).

**Task:**

1. Create a calculated column:

   ```DAX
   Bonus = Employee[Salary] * 0.1
   ```

👉 Each row gets a bonus → **row context**.

2. Create a measure:

   ```DAX
   Total Bonus = SUM(Employee[Bonus])
   ```

👉 Sums across the dataset → **filter context**.

---

## 5.2 Basic Functions

### 5.2.1 Aggregate functions

**Task:**
Create these measures:

```DAX
Total Salary = SUM(Employee[Salary])
Min Salary = MIN(Employee[Salary])
Max Salary = MAX(Employee[Salary])
Employee Count = COUNT(Employee[EmployeeID])
```

👉 Add them to a **Table visual**.

---

### 5.2.2 Logical functions

**Task:**

```DAX
High Earner = IF(Employee[Salary] > 5000, "Yes", "No")
```

👉 Create a **Pie Chart** with High Earner count.

---

### 5.2.3 Text functions

**Task:**

```DAX
First Name = LEFT(Employee[FullName], FIND(" ", Employee[FullName]) - 1)
```

👉 Creates first name from full name.

---

### 5.2.4 Date and time functions

**Task:**

```DAX
Years of Service = DATEDIFF(Employee[HireDate], TODAY(), YEAR)
```

👉 Add to a table to see how long each employee has worked.

---

## 5.3 Intermediate DAX

### 5.3.1 Calculated tables

**Task:**

```DAX
Department Summary = SUMMARIZE(Employee, Employee[Department], "Avg Salary", AVERAGE(Employee[Salary]))
```

👉 Creates a new table summarizing department salaries.

---

### 5.3.2 Time intelligence functions

**Task (if you have date data):**

```DAX
YTD Salary = TOTALYTD(SUM(Employee[Salary]), Employee[HireDate])
```

👉 Tracks salaries year-to-date.

---

### 5.3.3 FILTER function

**Task:**

```DAX
High Salary Total = CALCULATE(SUM(Employee[Salary]), FILTER(Employee, Employee[Salary] > 5000))
```

👉 Only sums salaries above 5000.

---

### 5.3.4 RELATED and RELATEDTABLE

*(Useful when you linked multiple tables earlier)*
**Task:**

```DAX
Dept Budget Used = SUM(Employee[Salary]) / RELATED(DeptBudget[Budget])
```

---

## 5.4 Advanced DAX

### 5.4.1 Context transition (CALCULATE)

**Task:**

```DAX
Average High Salary = CALCULATE(AVERAGE(Employee[Salary]), Employee[Salary] > 5000)
```

👉 CALCULATE changes filter context on the fly.

---

### 5.4.2 ALL, ALLEXCEPT, VALUES

**Task:**

```DAX
% of Total Salary = SUM(Employee[Salary]) / CALCULATE(SUM(Employee[Salary]), ALL(Employee))
```

👉 Shows each row’s contribution to total.

---

### 5.4.3 RANKX and advanced ranking

**Task:**

```DAX
Salary Rank = RANKX(ALL(Employee), Employee[Salary], , DESC)
```

👉 Ranks employees by salary.

---

### 5.4.4 Dynamic measures with SELECTEDVALUE

**Task:**

```DAX
Selected Dept Salary = CALCULATE(SUM(Employee[Salary]), Employee[Department] = SELECTEDVALUE(Employee[Department]))
```

👉 Changes dynamically depending on slicer selection.

---

✅ At this point, you’ve learned **DAX fundamentals to advanced**:

* Built measures & calculated columns
* Used row vs filter context
* Applied logical, text, date, time intelligence
* Created rankings and dynamic measures

Your Employee dataset is now ready for **visual storytelling in Module 6** 🎨.

---

👉 Do you want me to continue with **Module 6: Data Visualization in Power BI**, where we’ll design charts, slicers, and interactive dashboards step by step using your Employee data?

Perfect 🎉 — now that you have **cleaned data, a working model, and DAX measures**, we’ll move into the fun part: **Data Visualization in Power BI**.
We’ll build interactive, professional dashboards step by step using your **Employee dataset**.

---

# 📘 Module 6: Data Visualization in Power BI

---

## 6.1 Introduction to Data Visualization

### 6.1.1 Purpose of visualization

* Turn raw data into **insights**
* Support **decision making**
* Spot **trends, outliers, comparisons**

**Task:**
Open a blank report page → Add a **Table visual** with:

* Employee Name
* Department
* Salary
  👉 This will be our starting point before moving into advanced visuals.

---

### 6.1.2 Best practices for dashboards

* Keep it **clean** (avoid clutter)
* Use **consistent colors** (e.g., department colors)
* Highlight **KPIs** first
* Enable **interactivity** (slicers, filters)

---

## 6.2 Core Visuals in Power BI

### 6.2.1 Table & Matrix visuals

**Task:**

* Insert a **Matrix**
* Rows: Department
* Columns: Job Title
* Values: Average Salary

👉 This allows you to see salary distribution across departments.

---

### 6.2.2 Card & KPI visuals

**Task:**
Create **Card visuals** for these measures:

* `Total Employees`
* `Average Salary`
* `Total Salary`
  👉 These are your **top KPIs**.

---

### 6.2.3 Bar & Column charts

**Task:**

* Insert **Clustered Column Chart**
* Axis: Department
* Values: Total Salary

👉 Compare salaries across departments.

---

### 6.2.4 Pie & Donut charts

**Task:**

* Insert **Donut Chart**
* Legend: High Earner (Yes/No)
* Values: Employee Count

👉 Quickly see % of employees above salary threshold.

---

### 6.2.5 Line & Area charts

**Task:**
(If your dataset has dates like `HireDate`)

* Insert **Line Chart**
* Axis: HireDate (by year)
* Values: Employee Count

👉 Shows hiring trend over time.

---

### 6.2.6 Scatter plots & bubble charts

**Task:**

* Insert **Scatter Chart**
* X-axis: Salary
* Y-axis: Years of Service
* Size: Bonus
  👉 Identifies high-paid, long-serving employees.

---

## 6.3 Advanced Visuals & Customization

### 6.3.1 Slicers and filters

**Task:**
Add slicers for:

* Department
* Gender
* Job Title

👉 This makes your dashboard interactive.

---

### 6.3.2 Hierarchies and drill-down

**Task:**

* Create hierarchy → Department → Job Title
* Add to **Bar Chart**
* Enable drill-down button

👉 Allows exploring salaries from **department → role** level.

---

### 6.3.3 Conditional formatting

**Task:**
In **Matrix visual**:

* Format Salary with a **color scale** (green = high, red = low).

👉 Quick spotting of underpaid vs well-paid roles.

---

### 6.3.4 Custom visuals from AppSource

**Task:**

* Go to **Get more visuals** → Import “Bullet Chart”
* Use it for comparing **Target Salary vs Actual Salary** (if you add targets).

---

## 6.4 Designing Interactive Dashboards

### 6.4.1 Dashboard layout planning

Sections to include:

1. **Header / Title**
2. **KPI cards**
3. **Trends (line charts)**
4. **Breakdowns (bar/column/matrix)**
5. **Filters / Slicers**

---

### 6.4.2 Combining visuals

**Task:**
On one page, arrange:

* Top: Card KPIs (Employees, Avg Salary, Total Salary)
* Left: Department Salary (Bar Chart)
* Right: Salary Distribution (Donut)
* Bottom: Hiring Trend (Line Chart)

👉 You now have a **1-page Employee Dashboard**.

---

### 6.4.3 Cross-filtering and drill-through

**Task:**

* Enable **Cross-filtering** → click a department bar → updates all visuals.
* Add a **Drill-through page** for Employee details → Right-click a department → “Drill-through.”

---

### 6.4.4 Bookmarks & storytelling

**Task:**

* Create **two bookmarks**:

  * “Overview” (high-level KPIs)
  * “Department Analysis” (department breakdown)
* Add **Buttons** to switch between them.

👉 Creates a guided story for management.

---

## 6.5 Publishing & Sharing Dashboards

### 6.5.1 Exporting visuals

* Export dashboard as **PDF / PowerPoint** for static reporting.

### 6.5.2 Publishing to Power BI Service

**Task:**

* Save → Publish → Power BI Service
* Share with colleagues (requires license).

### 6.5.3 Mobile optimization

* Use **View → Phone Layout**
* Rearrange KPIs and charts for mobile use.

---

✅ By the end of Module 6, you will have:

* Created **all core Power BI visuals**
* Customized and formatted them professionally
* Built a **fully interactive dashboard** from Employee data
* Learned how to share and optimize dashboards for mobile

---

👉 Do you want me to move on to **Module 7: Power BI Deployment & Sharing**, where I’ll show you how to schedule refresh, manage permissions, and set up workspaces in Power BI Service?

Great 🎯 — now that you’ve built a full **interactive dashboard**, it’s time to **deploy and share** it. This is where Power BI goes from a personal tool to an **organizational intelligence platform**.

---

# 📘 Module 7: Power BI Deployment & Sharing

---

## 7.1 Introduction to Deployment

### 7.1.1 Local vs cloud deployment

* **Local (Power BI Desktop):** Personal, cannot share interactively.
* **Cloud (Power BI Service):** Publish, share, schedule refresh, manage permissions.

**Task:**
👉 Save your Employee dashboard in Power BI Desktop.
👉 Go to **Home → Publish → Select a Workspace** → Upload to Power BI Service.

---

## 7.2 Workspaces in Power BI Service

### 7.2.1 My Workspace vs Shared Workspaces

* **My Workspace** → personal, no team sharing.
* **Workspaces** → team collaboration, app publishing.

**Task:**
👉 In Power BI Service, create a **new workspace** → Name it *Employee Analytics*.

---

### 7.2.2 Roles & permissions

Roles in a workspace:

* **Admin** → Full control
* **Member** → Can edit reports
* **Contributor** → Can publish & share
* **Viewer** → Read-only access

**Task:**
👉 Add a colleague’s email → Assign them **Viewer role**.

---

## 7.3 Data Refresh & Gateway Setup

### 7.3.1 Scheduled refresh

* Needed if data changes in Excel / SQL / cloud.
* Frequency: daily, hourly, or near real-time.

**Task:**
👉 In Power BI Service → Dataset Settings → Schedule Refresh → Set **daily refresh** at 6 AM.

---

### 7.3.2 On-premises data gateway

* Required if source data is **local (Excel/SQL on PC/server)**.
* Acts as a secure bridge between local data and Power BI Service.

**Task:**
👉 Download and install **On-Premises Data Gateway** → Connect it to your Employee Excel source.

---

### 7.3.3 Refresh notifications

* Configure **email alerts** if a refresh fails.
  👉 Go to dataset → **Alerts → Failure notification**.

---

## 7.4 Sharing & Collaboration

### 7.4.1 Sharing dashboards

* Share directly with emails.
* Limit: Requires Power BI Pro license (or Premium capacity).

**Task:**
👉 Share your Employee Dashboard with your manager’s email.

---

### 7.4.2 Apps in Power BI Service

* Package multiple dashboards & reports.
* Easy to distribute to larger teams.

**Task:**
👉 In your workspace → Publish App → Call it *HR Insights*.

---

### 7.4.3 Comments & collaboration

* Add comments directly in dashboards.
* Tag colleagues with `@name`.

---

## 7.5 Security & Governance

### 7.5.1 Row-Level Security (RLS)

* Restrict what each user sees.
* Example: HR sees all employees, but managers see only their department.

**Task:**
👉 In Power BI Desktop → Modeling → Manage Roles → Create role:

```DAX
[Department] = "Finance"
```

👉 Publish and assign users to this role in Power BI Service.

---

### 7.5.2 Sensitivity labels

* Mark reports as **Confidential / Public**.
* Integrated with Microsoft Information Protection.

---

### 7.5.3 Audit logs & usage metrics

* Track who is accessing reports.
  👉 In Power BI Service → Settings → Usage Metrics → Enable report tracking.

---

## 7.6 Deployment Pipelines

### 7.6.1 Stages of deployment

* **Development → Test → Production**
* Ensures dashboards are validated before company-wide release.

**Task:**
👉 Create a **Deployment Pipeline** for your Employee Analytics workspace.

---

### 7.6.2 Version control in Power BI

* Use GitHub/Azure DevOps for storing `.pbix` files.
* Ensures changes are tracked.

---

### 7.6.3 Automating deployment

* Premium feature → Automate via **Power BI REST API**.

---

✅ By the end of Module 7, you will have learned how to:

* Publish dashboards to Power BI Service
* Manage workspaces, roles, and permissions
* Set up scheduled refresh & data gateways
* Implement row-level security & governance
* Deploy reports in a structured pipeline

---

👉 Do you want me to continue with **Module 8: Advanced Analytics & AI in Power BI** (AI visuals, forecasting, clustering, natural language Q\&A), or should I stop at deployment & sharing?
Perfect — now that your reports are **deployed and shared**, let’s level up with **advanced analytics & AI features** in Power BI. These tools make your dashboards smarter and more interactive. 🚀

---

# 📘 Module 8: Advanced Analytics & AI in Power BI

---

## 8.1 Introduction to Advanced Analytics

### 8.1.1 What is advanced analytics in Power BI?

It extends basic reporting with **predictions, trends, clustering, natural language insights, and external integrations (R, Python, Azure ML).**

**Task:**
👉 Think about 2–3 **predictive or advanced questions** for your Employee dataset. Example:

* Can we forecast attrition?
* Which departments are most similar in terms of employee profiles?

---

## 8.2 Forecasting & Trend Analysis

### 8.2.1 Line chart forecasting

* Power BI can **forecast future values** using historical data.

**Task:**

1. Insert a **Line chart** → Put `Hire Date` on X-axis, `Employee Count` on Y-axis.
2. Go to **Analytics pane (paint roller → magnifying glass icon)**.
3. Add a **Forecast** → Set 6 months forward.

✅ You’ll see a **shaded forecast region**.

---

### 8.2.2 Trend lines

* Add **trend lines** to show growth/decline.

**Task:**
👉 On the same Line chart, in Analytics pane → Add **Trend Line**.

---

### 8.2.3 Seasonality detection

* Power BI detects **yearly/quarterly patterns**.
* Helpful for HR hiring cycles, sales data, etc.

---

## 8.3 Clustering & Grouping

### 8.3.1 Automatic clustering

* Useful for segmenting employees (e.g., by Age, Salary, Department).

**Task:**

1. Create a **Scatter chart** → X-axis: Age, Y-axis: Salary, Size: Years at Company.
2. Right-click chart → **Automatically find clusters**.
3. Name clusters (e.g., "Young High Salary", "Senior Low Salary").

---

### 8.3.2 Manual grouping

* You can also manually group categories.

**Task:**
👉 In Fields pane → Right-click `Department` → **New Group** → Group similar ones (e.g., Finance + Accounting).

---

## 8.4 AI Visuals in Power BI

### 8.4.1 Key Influencers Visual

* Explains **what factors drive an outcome**.
* Example: What factors influence Attrition?

**Task:**

1. Insert **Key Influencers visual**.
2. Add `Attrition` as **Analyzed field**.
3. Add fields like `Age`, `Job Role`, `Monthly Income` as **Explanatory fields**.
   ✅ You’ll see which factors **increase/decrease attrition likelihood**.

---

### 8.4.2 Decomposition Tree

* Breaks down a metric step by step.
* Example: Employee count → by Department → Gender → Education.

**Task:**

1. Insert **Decomposition Tree visual**.
2. Set **Employee Count** as the metric.
3. Add breakdowns: `Department`, `Gender`, `Education`.
4. Expand nodes interactively.

---

### 8.4.3 Q\&A (Natural Language Query)

* Users can type questions like *"Show average salary by department"* and get visuals instantly.

**Task:**
👉 Insert **Q\&A visual** → Type:

> "Average monthly income by job role"

✅ Power BI auto-generates a chart.

---

## 8.5 Integration with R & Python

### 8.5.1 Enabling scripting

* Go to **File → Options → Python scripting / R scripting** → Set installation paths.

---

### 8.5.2 Python visuals

* Example: Use Python to create advanced plots.

**Task:**
👉 Insert **Python visual** → Paste:

```python
import seaborn as sns
import matplotlib.pyplot as plt

sns.boxplot(x=dataset['Department'], y=dataset['MonthlyIncome'])
plt.show()
```

---

### 8.5.3 R visuals

* Example: Clustering with R’s `kmeans()`.

---

## 8.6 What-If Parameters

### 8.6.1 Creating a What-if parameter

* Useful for HR “what if” analysis (e.g., Salary Increase %).

**Task:**

1. Go to **Modeling → New Parameter → Numeric Range**.
2. Call it **Salary Increase %** → Range 0–20%, step 1%.
3. Create a measure:

```DAX
Adjusted Salary = SUM(Employee[MonthlyIncome]) * (1 + 'Salary Increase %'[Salary Increase % Value]/100)
```

4. Add a **slider slicer** for Salary Increase %.

✅ Now you can simulate salary policy changes!

---

## 8.7 Advanced AI Integrations

### 8.7.1 Azure Cognitive Services

* Sentiment analysis, language detection, key phrase extraction.

### 8.7.2 Azure Machine Learning

* Train ML models → consume predictions inside Power BI.

### 8.7.3 AutoML (Premium feature)

* Build quick predictive models without coding.

---

✅ By the end of Module 8, you will:

* Forecast future trends
* Segment data with clustering
* Use Key Influencers & Decomposition Tree for insights
* Enable natural language Q\&A
* Run Python/R scripts inside Power BI
* Build What-if simulations for scenario planning

---

👉 Do you want me to continue with **Module 9: Security, Administration & Governance in Power BI** (Row-Level Security, Object-Level Security, Monitoring), or should we go directly to the **Capstone Project**?

Excellent 🚀 — now let’s get into **Module 9: Security, Administration & Governance**.
This is where Power BI becomes **enterprise-ready** — controlling access, ensuring compliance, and monitoring usage.

---

# 📘 Module 9: Security, Administration & Governance

---

## 9.1 Security

### 9.1.1 Row-Level Security (RLS)

RLS limits what data each user sees.
Example: HR sees **all employees**, but department heads only see their own department.

**Task (with Employee dataset):**

1. In Power BI Desktop → **Modeling → Manage Roles**.
2. Create role: `Finance_Manager`.
3. Add DAX filter:

   ```DAX
   [Department] = "Finance"
   ```
4. Save role → Publish to Power BI Service.
5. In Service → **Security → Assign users** (finance managers to that role).

✅ When they log in, they only see Finance employees.

---

### 9.1.2 Object-Level Security (OLS)

OLS hides **specific tables or columns**.
Example: Hide `Salary` column from non-HR users.

**Task:**
👉 In Power BI Desktop → Model → Right-click `MonthlyIncome` → Set OLS filter → Restrict access for non-HR roles.

---

### 9.1.3 Data Masking Techniques

* Show sensitive data in **masked format**.
* Example: Show `EmployeeID` as `XXX-123`.

**Task (DAX calculated column):**

```DAX
MaskedID = "XXX-" & RIGHT(Employee[EmployeeID], 3)
```

---

## 9.2 Advanced Analytics Governance

### 9.2.1 Integration with Python/R scripts

* Already covered in Module 8 — requires governance (decide who can run scripts).
* Admins can **enable/disable scripting** in Power BI Service.

---

### 9.2.2 Forecasting & What-If governance

* Ensure assumptions are **transparent** (document what models are used).

---

### 9.2.3 Usage of AI visuals (Key Influencers, Decomposition Tree)

* Ensure users understand limitations of AI models (bias, incomplete data).

---

## 9.3 Deployment & Administration

### 9.3.1 Version Control

* Store `.pbix` files in **GitHub or Azure DevOps**.
* Use **naming conventions** (e.g., `EmployeeReport_v1.2.pbix`).

**Task:**
👉 Save your Employee report → Upload to GitHub with version name.

---

### 9.3.2 Power BI Report Server (On-Premises)

* Alternative to cloud → Host Power BI reports locally.
* Used when **data cannot leave company servers**.

**Task:**
👉 If your organization is sensitive about cloud, explore **Power BI Report Server**.

---

### 9.3.3 Monitoring Usage & Performance

* Admins can track **who views reports, refresh times, errors**.
* Metrics:

  * Report usage
  * Dataset refresh failures
  * Query performance

**Task:**

1. In Power BI Service → Dataset → Settings → Usage Metrics.
2. View **most viewed reports** & **refresh success/failure logs**.

---

## 9.4 Governance Best Practices

* **Data Security:** Always implement RLS/OLS.
* **Naming Conventions:** Consistent report names (e.g., `HR_Attrition_Dashboard`).
* **Documentation:** Keep a data dictionary & model documentation.
* **Access Control:** Apply **least privilege principle** (users get only what they need).
* **Auditing:** Regularly review usage & access logs.

---

✅ By the end of Module 9, you will know how to:

* Secure data with RLS, OLS, and masking
* Control sensitive analytics features (Python/R, AI visuals)
* Manage deployment pipelines and version control
* Monitor report usage and system performance
* Apply governance best practices

---

👉 Shall I now guide you through the **Capstone Project (Module 10)**, where you’ll build a full HR Analytics dashboard (from data prep → modeling → DAX → visualization → sharing)?

Perfect 🙌 — now we’ll wrap up everything you’ve learned in a **Capstone Project**.
This project will simulate a **real-world HR Analytics dashboard** using your Employee dataset, applying **all the skills from Modules 1–9**.

---

# 📘 Module 10: Capstone Project

---

## 10.1 Project Definition

### 10.1.1 Business Problem Selection

Scenario:
Your HR department wants a **360° Employee Analytics Dashboard** to answer questions like:

* What’s the demographic breakdown of employees?
* Which departments have the highest attrition rates?
* How does salary compare across job roles?
* Can we forecast future hiring trends?

**Task:**
👉 Write 3–5 business questions you’ll answer with your dashboard. Example:

1. How many employees are in each department by gender?
2. What is the average salary by job role?
3. Which department has the highest attrition rate?
4. Can we forecast headcount growth for next year?

---

### 10.1.2 Identifying Relevant Data Sources

* Your **Employee Sample Data.xlsx** (primary source).
* Optional: Add another small dataset (e.g., `Department Budget` from Excel).

---

## 10.2 Development

### 10.2.1 Data Preparation

* Load Employee data into Power BI.
* Use **Power Query** to:

  * Remove duplicates & blanks
  * Standardize date formats
  * Ensure numeric columns (Salary, Age) are correct data types
  * Replace missing values if any

**Task:**
👉 In Power Query → Clean and transform Employee dataset as above.

---

### 10.2.2 Data Modeling

* Build a **Star Schema**:

  * **Fact table:** Employee records
  * **Dimension tables:** Departments, Job Roles, Gender

**Steps:**

1. Create **relationships** (Employee → Department, Employee → Job Role).
2. Define hierarchies: `Date → Year → Month`.

---

### 10.2.3 Visualization & Storytelling

Build a multi-page dashboard:

#### Page 1: **Executive Summary**

* KPI cards: Total Employees, Attrition Rate, Avg Salary, Avg Age
* Trend Line: Headcount over time
* Slicer: Department

#### Page 2: **Demographics**

* Bar Chart: Employees by Department & Gender
* Pie Chart: Education distribution
* Map (if location data exists): Employees by Region

#### Page 3: **Attrition Analysis**

* Key Influencers: What drives attrition
* Column Chart: Attrition rate by Job Role
* Table: Employee details with Attrition flag

#### Page 4: **Salary Insights**

* Boxplot (Python visual): Salary distribution by Department
* Line Chart: Avg salary trend over time
* What-if Parameter: Adjusted Salary (simulate % increase)

---

## 10.3 Presentation

### 10.3.1 Report Design Finalization

* Apply a **theme** (HR-friendly colors: blue, green, neutral tones).
* Align visuals for readability (use **Gridlines + Snap to Grid**).
* Add **company logo** in top corner.

---

### 10.3.2 Publishing & Sharing

* Save as `.pbix`.
* Publish to **Power BI Service → HR Analytics Workspace**.
* Create an **App** called *HR Insights*.
* Share with HR managers (Viewer role).

---

### 10.3.3 Feedback & Iteration

* Collect feedback from HR team: Are metrics clear? Any missing KPIs?
* Iterate on visuals, filters, and calculations.

---

✅ By completing Module 10, you will have:

* Defined a **real HR business problem**
* Cleaned & modeled data
* Built a **multi-page Power BI dashboard**
* Applied **DAX, RLS, forecasting, what-if parameters**
* Deployed and shared in Power BI Service

---

🎉 Congratulations — you’ve now walked through the **entire Power BI curriculum** step by step using your dataset.

---

👉 Do you want me to now **design the exact Capstone Dashboard layout (page by page with visuals & DAX formulas)** so you can follow along directly in Power BI?

Perfect 🔥 — let’s design the **exact layout** for your **Capstone Employee Analytics Dashboard**.
I’ll give you a **page-by-page design**, including **visuals, fields, and DAX measures** so you can build it directly in Power BI.

---

# 🎓 Capstone Project Dashboard: *Employee Analytics*

---

## 📊 Page 1: Executive Summary

**Purpose:** Provide top-level HR insights at a glance.

**Layout & Visuals:**

1. **KPI Cards (Top Row):**

   * Total Employees → `COUNTROWS(Employee)`
   * Attrition Rate →

     ```DAX
     Attrition Rate = DIVIDE(CALCULATE(COUNTROWS(Employee), Employee[Attrition] = "Yes"), COUNTROWS(Employee))
     ```
   * Avg Salary → `AVERAGE(Employee[MonthlyIncome])`
   * Avg Age → `AVERAGE(Employee[Age])`

2. **Line Chart (Center):**

   * Axis: `Hire Date (Month/Year)`
   * Values: `Employee Count = COUNTROWS(Employee)`
   * Show **trend over time**.

3. **Slicer (Right side):**

   * Field: `Department`

---

## 👥 Page 2: Demographics

**Purpose:** Understand workforce distribution by department, gender, and education.

**Layout & Visuals:**

1. **Clustered Bar Chart (Left):**

   * Axis: `Department`
   * Values: `Employee Count`
   * Legend: `Gender`

2. **Pie Chart (Right):**

   * Values: `Employee Count`
   * Legend: `EducationField`

3. **Donut Chart (Bottom):**

   * Values: `Employee Count`
   * Legend: `MaritalStatus`

4. **Card (Top-Right):**

   * Metric: Average Years at Company → `AVERAGE(Employee[YearsAtCompany])`

---

## 📉 Page 3: Attrition Analysis

**Purpose:** Identify why employees leave and where risks are highest.

**Layout & Visuals:**

1. **Key Influencers Visual (Left):**

   * Analyzed field: `Attrition`
   * Explanatory fields: `Age`, `JobRole`, `MonthlyIncome`, `OverTime`, `YearsAtCompany`

2. **Stacked Column Chart (Right):**

   * Axis: `JobRole`
   * Values: Attrition Count =

     ```DAX
     Attrition Count = CALCULATE(COUNTROWS(Employee), Employee[Attrition] = "Yes")
     ```

3. **Table (Bottom):**

   * Columns: `EmployeeID`, `Name`, `JobRole`, `MonthlyIncome`, `Attrition`

4. **KPI Card (Top):**

   * Metric: Attrition Rate (from Page 1 measure).

---

## 💰 Page 4: Salary Insights

**Purpose:** Explore salary distribution and simulate salary adjustments.

**Layout & Visuals:**

1. **Boxplot (Python Visual, Left):**

   ```python
   import seaborn as sns
   import matplotlib.pyplot as plt

   sns.boxplot(x=dataset['Department'], y=dataset['MonthlyIncome'])
   plt.show()
   ```

2. **Clustered Column Chart (Center):**

   * Axis: `Department`
   * Values: `Average MonthlyIncome`

3. **Line Chart (Right):**

   * Axis: `Hire Date (Year)`
   * Values: `Average MonthlyIncome`

4. **What-if Parameter (Bottom):**

   * Create Parameter: `Salary Increase %` (0–20, step 1).
   * DAX:

     ```DAX
     Adjusted Salary = SUM(Employee[MonthlyIncome]) * (1 + 'Salary Increase %'[Salary Increase % Value]/100)
     ```
   * Visual: Card showing **Adjusted Salary** when slider is moved.

---

## 🔮 Page 5: Forecast & Advanced Insights

**Purpose:** Provide future workforce trends & deeper analytics.

**Layout & Visuals:**

1. **Line Chart with Forecast (Top-Left):**

   * Axis: `Hire Date`
   * Values: `Employee Count`
   * Add **Forecast → 12 months**

2. **Decomposition Tree (Top-Right):**

   * Metric: Employee Count
   * Breakdown: `Department → Gender → EducationField`

3. **Q\&A Visual (Bottom):**

   * Add a box where users can type queries like:

     > “Show average salary by department”

---

# 🎨 Design & Formatting Tips

* Apply **Corporate Theme**: Blue (primary), Gray (neutral background), White (cards).
* Use **consistent font sizes**:

  * KPIs → Large (20–24pt)
  * Labels → Medium (12–14pt)
* Use **Snap-to-Grid** for alignment.
* Add **company logo** at top-left corner of every page.
* Add **page navigation buttons** (Back, Next, Home).

---

✅ At the end, you’ll have a **5-page HR Analytics Dashboard** that:

* Summarizes KPIs (Page 1)
* Shows workforce demographics (Page 2)
* Explains attrition drivers (Page 3)
* Analyzes salary patterns (Page 4)
* Forecasts future trends (Page 5)

---

👉 Do you want me to also create a **list of all DAX measures** you’ll need across the dashboard (ready to paste into Power BI)?

Perfect 👌 — here’s a **complete list of DAX measures** you’ll need to build the **Capstone Employee Analytics Dashboard**.
I’ve grouped them **by dashboard page** so you can paste them directly into Power BI.

---

# 📌 Global Measures (used across multiple pages)

```DAX
Employee Count = COUNTROWS(Employee)

Attrition Count = 
    CALCULATE(
        COUNTROWS(Employee),
        Employee[Attrition] = "Yes"
    )

Attrition Rate = 
    DIVIDE(
        [Attrition Count],
        [Employee Count],
        0
    )

Average Salary = AVERAGE(Employee[MonthlyIncome])

Average Age = AVERAGE(Employee[Age])

Average Years at Company = AVERAGE(Employee[YearsAtCompany])
```

---

# 📊 Page 1: Executive Summary

```DAX
New Hires = 
    CALCULATE(
        COUNTROWS(Employee),
        Employee[HireDate] >= DATE(YEAR(TODAY()),1,1)
    )

Active Employees = 
    CALCULATE(
        COUNTROWS(Employee),
        Employee[Attrition] = "No"
    )
```

---

# 👥 Page 2: Demographics

```DAX
Male Employees = 
    CALCULATE([Employee Count], Employee[Gender] = "Male")

Female Employees = 
    CALCULATE([Employee Count], Employee[Gender] = "Female")

Education Distribution = DISTINCTCOUNT(Employee[EducationField])

Married Employees = 
    CALCULATE([Employee Count], Employee[MaritalStatus] = "Married")

Single Employees = 
    CALCULATE([Employee Count], Employee[MaritalStatus] = "Single")
```

---

# 📉 Page 3: Attrition Analysis

```DAX
Attrition by Department = 
    CALCULATE([Attrition Count], ALLEXCEPT(Employee, Employee[Department]))

Attrition by JobRole = 
    CALCULATE([Attrition Count], ALLEXCEPT(Employee, Employee[JobRole]))

Attrition Salary Avg = 
    CALCULATE(AVERAGE(Employee[MonthlyIncome]), Employee[Attrition] = "Yes")

Retention Salary Avg = 
    CALCULATE(AVERAGE(Employee[MonthlyIncome]), Employee[Attrition] = "No")
```

---

# 💰 Page 4: Salary Insights

```DAX
Average Salary by Department = 
    AVERAGEX(
        VALUES(Employee[Department]),
        CALCULATE(AVERAGE(Employee[MonthlyIncome]))
    )

Salary Increase % = SELECTEDVALUE('Salary Increase %'[Salary Increase % Value])

Adjusted Salary = 
    SUM(Employee[MonthlyIncome]) * (1 + [Salary Increase %]/100)

Salary Difference = [Adjusted Salary] - SUM(Employee[MonthlyIncome])
```

---

# 🔮 Page 5: Forecast & Advanced Insights

*(Most visuals here use Power BI’s built-in Forecast, Q\&A, and Decomposition Tree, so minimal DAX needed — but these help for custom visuals.)*

```DAX
Employee Count YoY Growth % = 
    DIVIDE(
        [Employee Count] - CALCULATE([Employee Count], DATEADD(Employee[HireDate], -1, YEAR)),
        CALCULATE([Employee Count], DATEADD(Employee[HireDate], -1, YEAR))
    )

Salary YoY Growth % = 
    DIVIDE(
        [Average Salary] - CALCULATE([Average Salary], DATEADD(Employee[HireDate], -1, YEAR)),
        CALCULATE([Average Salary], DATEADD(Employee[HireDate], -1, YEAR))
    )
```

---

✅ With these measures, you’ll cover **all 5 pages of the dashboard**:

* Workforce size, attrition, age, tenure (KPIs)
* Demographics breakdowns
* Attrition risk analysis
* Salary benchmarking & simulation
* Forecasting trends

---

👉 Do you want me to also **map each measure to its exact visual** (so you know which measure goes where without guessing)?

Great 👍 — let’s map **each DAX measure to its exact visual** for the **Capstone Employee Analytics Dashboard**.

---

# 📊 Page 1: **Executive Summary**

**Visuals & Measures**

1. **KPI Cards (Top Row)**

   * **Employee Count** → shows total employees
   * **Attrition Rate** → % of employees who left
   * **Average Salary** → company-wide avg
   * **Average Age** → avg employee age
   * **Average Years at Company** → avg tenure

2. **Line Chart (Attrition Trend Over Time)**

   * Axis → `HireDate` (Month-Year)
   * Value → `[Attrition Count]`

3. **Donut Chart (Attrition vs Active)**

   * Legend → `Attrition (Yes/No)`
   * Values → `[Employee Count]`

---

# 👥 Page 2: **Demographics**

**Visuals & Measures**

1. **Stacked Column Chart (Gender Distribution)**

   * Axis → `Gender`
   * Values → `[Employee Count]`

2. **Bar Chart (Age Groups)**

   * Axis → Age Group (create a column: `<30`, `30-40`, `40-50`, `50+`)
   * Values → `[Employee Count]`

3. **Stacked Bar Chart (Education Distribution)**

   * Axis → `EducationField`
   * Values → `[Employee Count]`

4. **Donut Chart (Marital Status)**

   * Legend → `MaritalStatus`
   * Values → `[Employee Count]`

5. **Table (Demographics Summary)**

   * Columns → Gender, Education, Marital Status, Department
   * Values → `[Employee Count]`

---

# 📉 Page 3: **Attrition Analysis**

**Visuals & Measures**

1. **Clustered Bar Chart (Attrition by Department)**

   * Axis → `Department`
   * Values → `[Attrition by Department]`

2. **Clustered Column Chart (Attrition by Job Role)**

   * Axis → `JobRole`
   * Values → `[Attrition by JobRole]`

3. **Stacked Column Chart (Attrition Rate by Age Group)**

   * Axis → Age Group
   * Values → `[Attrition Count]` / `[Employee Count]`

4. **KPI Cards**

   * `[Attrition Salary Avg]` (Avg Salary of employees who left)
   * `[Retention Salary Avg]` (Avg Salary of employees who stayed)

5. **Scatter Plot (Attrition Risk Factors)**

   * X-Axis → `JobSatisfaction`
   * Y-Axis → `[Attrition Rate]`
   * Size → `[Employee Count]`
   * Legend → `Department`

---

# 💰 Page 4: **Salary Insights**

**Visuals & Measures**

1. **Clustered Column Chart (Average Salary by Department)**

   * Axis → `Department`
   * Values → `[Average Salary by Department]`

2. **Clustered Bar Chart (Average Salary by Job Role)**

   * Axis → `JobRole`
   * Values → `Average(Employee[MonthlyIncome])`

3. **Card (Current Avg Salary)**

   * `[Average Salary]`

4. **What-If Parameter Simulation (Salary Increase %)**

   * Slicer → Salary Increase % parameter
   * KPI Card → `[Adjusted Salary]`
   * KPI Card → `[Salary Difference]`

---

# 🔮 Page 5: **Forecast & Advanced Insights**

**Visuals & Measures**

1. **Line Chart (Employee Growth Over Time)**

   * Axis → `HireDate` (Month-Year)
   * Value → `[Employee Count]`
   * Analytics → Add **Forecast (12 months)**

2. **Line Chart (Salary Growth Over Time)**

   * Axis → `HireDate` (Month-Year)
   * Value → `[Average Salary]`
   * Analytics → Add **Forecast (12 months)**

3. **Decomposition Tree (Attrition Root Causes)**

   * Analyze → `[Attrition Count]`
   * Explain By → `Department → JobRole → Age → JobSatisfaction`

4. **Q\&A Visual (Ask questions)**

   * Example: *“What is attrition by gender?”* or *“Show average salary by education”*

5. **KPI Card (Employee Count YoY Growth %)**

   * `[Employee Count YoY Growth %]`

6. **KPI Card (Salary YoY Growth %)**

   * `[Salary YoY Growth %]`

---

✅ This mapping ensures:

* Every measure we wrote **has a clear home** on the dashboard.
* Each page tells a **cohesive story** (overview → demographics → attrition → salary → forecasting).

---

Would you like me to also **design a Power BI theme (colors, fonts, background layout)** for this dashboard, so you can just import and make it look professional instantly?

Perfect 🚀 — let’s make your **Capstone Dashboard** not just functional but **professional-looking** with a **custom Power BI theme**.
I’ll design a theme JSON file that you can import into Power BI.

---

# 🎨 Capstone Power BI Theme (Colors, Fonts, Layout)

### ✅ Color Palette (Clean & Professional HR Dashboard)

* **Primary Blue** (Corporate tone) → `#2F5597`
* **Accent Green** (Positive growth) → `#28A745`
* **Accent Red** (Attrition/loss) → `#D9534F`
* **Neutral Gray** (Background/labels) → `#6C757D`
* **Light Background** → `#F8F9FA`
* **White** → `#FFFFFF`

### ✅ Fonts

* **Titles & KPIs** → Segoe UI Bold (18–24pt)
* **Labels** → Segoe UI Regular (11–12pt)
* **Numbers** → Segoe UI Semibold (for clarity)

### ✅ Layout Best Practices

* Keep **KPI cards** in the top row on every page.
* Use **consistent axis colors** (blue for employees, red for attrition, green for growth).
* Background → soft off-white (`#F8F9FA`) for a modern clean look.
* Use **rounded corners (8–12px)** on visuals.

---

# 📂 Theme JSON File

Copy the following into **Notepad**, save as `CapstoneTheme.json`, then import in Power BI:

```json
{
  "name": "Capstone HR Dashboard Theme",
  "dataColors": [
    "#2F5597",
    "#28A745",
    "#D9534F",
    "#6C757D",
    "#17A2B8",
    "#FFC107"
  ],
  "background": "#F8F9FA",
  "foreground": "#2F5597",
  "tableAccent": "#28A745",
  "visualStyles": {
    "*": {
      "*": {
        "title": [
          {
            "fontFamily": "Segoe UI",
            "fontSize": 18,
            "fontWeight": "bold",
            "color": "#2F5597"
          }
        ],
        "labels": [
          {
            "fontFamily": "Segoe UI",
            "fontSize": 11,
            "color": "#6C757D"
          }
        ]
      }
    },
    "card": {
      "*": {
        "title": [
          {
            "fontSize": 16,
            "fontWeight": "bold",
            "color": "#2F5597"
          }
        ],
        "labels": [
          {
            "fontSize": 20,
            "fontWeight": "bold",
            "color": "#2F5597"
          }
        ]
      }
    }
  }
}
```

---

# 🚀 How to Apply Theme in Power BI

1. Save the JSON as `CapstoneTheme.json`.
2. Open **Power BI Desktop** → Go to **View** → **Themes** → **Browse for themes**.
3. Select your saved JSON file.
4. Your dashboard will instantly update with the professional color scheme, fonts, and layout.

---

👉 Do you also want me to design a **page template layout sketch (with grid placement of visuals)** for each page, so you know **exactly where to place KPIs, charts, and filters** on the canvas?

Awesome 🎯 — let’s design the **page template layouts** for your Capstone Dashboard so you know *exactly where to place visuals* on each page in Power BI.

I’ll use a **grid-style layout (3 rows × 3 columns)** that fits well on a **16:9 canvas** (default Power BI page).

---

# 📊 Page 1: **Executive Summary**

```
----------------------------------------------------
| KPI1 | KPI2 | KPI3 | KPI4 | KPI5  (Top row cards) |
----------------------------------------------------
| Line Chart (Attrition Trend)  | Donut Chart (Active vs Attrition) |
----------------------------------------------------
| Bar Chart (Attrition by Dept) | Bar Chart (Attrition by JobRole) |
----------------------------------------------------
```

---

# 👥 Page 2: **Demographics**

```
----------------------------------------------------
| KPI: Gender Split | KPI: Education Count | KPI: Marital Status |
----------------------------------------------------
| Column Chart (Gender) | Column Chart (Age Groups) | Donut (Marital Status) |
----------------------------------------------------
| Stacked Bar (Education Fields) | Table (Demographics Summary) |
----------------------------------------------------
```

---

# 📉 Page 3: **Attrition Analysis**

```
----------------------------------------------------
| KPI: Avg Salary Attrition | KPI: Avg Salary Retained |
----------------------------------------------------
| Clustered Bar (Attrition by Dept) | Clustered Column (Attrition by Job Role) |
----------------------------------------------------
| Stacked Column (Attrition Rate by Age) | Scatter Plot (Attrition Risk: Satisfaction vs Attrition) |
----------------------------------------------------
```

---

# 💰 Page 4: **Salary Insights**

```
----------------------------------------------------
| KPI: Avg Salary | KPI: Adjusted Salary | KPI: Salary Difference |
----------------------------------------------------
| Clustered Column (Salary by Dept) | Clustered Bar (Salary by Job Role) |
----------------------------------------------------
| Slicer (Salary Increase %) | Line/Area Chart (Salary Trend Over Time) |
----------------------------------------------------
```

---

# 🔮 Page 5: **Forecast & Advanced Insights**

```
----------------------------------------------------
| KPI: Employee YoY Growth | KPI: Salary YoY Growth |
----------------------------------------------------
| Line Chart (Employee Growth + Forecast) | Line Chart (Salary Growth + Forecast) |
----------------------------------------------------
| Decomposition Tree (Attrition Causes) | Q&A Visual (Natural Language) |
----------------------------------------------------
```

---

# 📌 Design Rules to Apply on Every Page

* **Top Row** → KPI Cards (quick insights first).
* **Middle Row** → Key trend or comparison visuals (line/bar).
* **Bottom Row** → Deep-dive analytics (scatter, decomposition, Q\&A, tables).
* **Left Side** → High-level summaries.
* **Right Side** → Details and drilldowns.
* **Slicers** → Place vertically on the far **left or right margin** (e.g., Department, Gender, Job Role).

---

✅ This way, every page looks **consistent, professional, and easy to read**, while telling a clear story.

---

👉 Do you want me to also create a **PowerPoint-style “storytelling script”** (talking points) that matches these layouts — so you know *what to say when presenting each dashboard page*?


Perfect 🎤 — let’s build your **PowerPoint-style storytelling script** for the Capstone Dashboard.
This gives you **talking points for each page**, so when you present the report, you’re guiding your audience through the insights like a pro.

---

# 📊 Page 1: Executive Summary

**Slide Talking Points:**

* *“This is the high-level overview of our workforce.”*
* Highlight **Employee Count**: *“We currently have X employees in the company.”*
* Point to **Attrition Rate**: *“Our attrition rate stands at Y%, which is above/below industry benchmarks.”*
* Mention **Avg Salary, Avg Age, Avg Tenure**: *“The typical employee earns \_\_\_, is \_\_\_ years old, and has been with us \_\_\_ years.”*
* Show **Attrition Trend Line**: *“Here we can see how attrition has changed over time — note the spikes in \[month/year].”*
* Show **Active vs Attrition Donut**: *“Currently, \_\_\_% are active, and \_\_\_% have left.”*

---

# 👥 Page 2: Demographics

**Slide Talking Points:**

* *“This page answers the question: Who are our employees?”*
* Gender Split: *“We have X% male and Y% female employees.”*
* Age Groups: *“Most employees fall within the \_\_\_ age range, which shows a relatively young/mature workforce.”*
* Marital Status: *“Notably, \_\_\_% are single/married, which can affect benefit preferences.”*
* Education Fields: *“We see strong representation in \[field], suggesting our talent pipeline is concentrated in certain areas.”*
* Table Summary: *“Here’s the full demographic breakdown across gender, education, marital status, and department.”*

---

# 📉 Page 3: Attrition Analysis

**Slide Talking Points:**

* *“This page dives into why people are leaving the company.”*
* Attrition by Department: *“Departments with the highest attrition are \_\_\_, suggesting possible leadership or workload issues.”*
* Attrition by Job Role: *“Job roles like \_\_\_ are more prone to turnover.”*
* Attrition by Age: *“Younger/older employees show higher attrition, pointing to retention challenges at certain career stages.”*
* Salary Comparison: *“Notice the average salary for those who left vs those who stayed — compensation may be a factor.”*
* Risk Factors Scatter: *“Here we explore job satisfaction vs attrition rates — low satisfaction clearly correlates with higher exits.”*

---

# 💰 Page 4: Salary Insights

**Slide Talking Points:**

* *“This page helps us evaluate compensation fairness and its role in retention.”*
* Avg Salary by Department: *“Departments like \_\_\_ have higher salaries than others.”*
* Avg Salary by Job Role: *“Certain roles are significantly under/over-compensated.”*
* Salary Simulation: *“Here we can model salary increases — if we apply a \_\_\_% raise, adjusted salaries rise to \_\_\_, with a total impact of \_\_\_.”*
* Salary Trend: *“Tracking salary over time, we see steady growth/flat trends, which could impact retention.”*

---

# 🔮 Page 5: Forecast & Advanced Insights

**Slide Talking Points:**

* *“This final page looks at the future and advanced insights.”*
* Employee Growth Forecast: *“Based on hiring trends, we project workforce growth of \_\_\_% over the next year.”*
* Salary Growth Forecast: *“Average salaries are expected to rise by \_\_\_%, which has implications for budgeting.”*
* Decomposition Tree: *“Attrition is most heavily driven by \_\_\_, followed by \_\_\_ — this helps us prioritize retention strategies.”*
* Q\&A Visual: *“With natural language, leadership can ask ad-hoc questions like *‘What’s attrition by gender?’* without needing a data team.”*
* YoY Growth Cards: *“Compared to last year, our workforce grew/shrank by \_\_\_% and salaries changed by \_\_\_%.”*

---

# 🎯 Closing Slide (Capstone Wrap-up)

**Slide Talking Points:**

* *“This dashboard enables HR and leadership to monitor workforce health, identify risk areas, and model compensation strategies.”*
* *“Key actions: address high attrition in specific roles/departments, review salary competitiveness, and monitor demographic shifts.”*
* *“This isn’t just about reporting — it’s a tool for strategic decision-making.”*

---

✅ Now you have a **page-by-page script** that ties visuals to insights and tells a story from **overview → demographics → problems → solutions → future outlook**.

---

👉 Do you want me to also prepare a **one-page executive summary slide** (like a cover sheet with the 5 key takeaways) that you can use to kick off or wrap up the presentation?
