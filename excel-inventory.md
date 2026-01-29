
Got it — this is a *great* system, but it’s currently written like a narrated video. I’ll turn it into a **clear, beginner-friendly, follow-along workflow**, broken into **logical segments** so someone can literally build the file step by step without getting lost.

I’ll **not change the logic**, only the **structure, clarity, and order**.

Below is **Segment 1**.
If you like the format, say *“Continue”* and I’ll send Segment 2 next.

---

# Excel Inventory Management System

## Beginner Step-by-Step Build (Segment 1)

---

## What You Are Building (Big Picture)

By the end of this process, you will have **one Excel workbook** with:

1. **Products sheet** – master list of everything you sell
2. **Transactions sheet** – records every stock movement
3. **Inventory sheet** – shows current stock per site
4. **Orders sheet** – shows what needs reordering
5. **Reports sheet** – summary and analysis

**Important idea:**
👉 You only *manually enter data* in **Products** and **Transactions**
👉 Everything else updates **automatically**

---

## SEGMENT 1: Create the Products (Master List)

This is the **foundation** of the entire system.
If this sheet is wrong, everything else will be wrong.

---

### Step 1: Create a New Excel File

1. Open Excel
2. Create a **new blank workbook**
3. Rename **Sheet1** to:
   **`Products`**

---

### Step 2: Add Column Headers

In row 1, enter the following headers (one per column):

| Cell | Header Name   |
| ---- | ------------- |
| A1   | Product ID    |
| B1   | Product Name  |
| C1   | Category      |
| D1   | Unit Cost     |
| E1   | Reorder Level |
| F1   | Supplier      |

**What these mean (important for beginners):**

* **Product ID** → unique code for each product (e.g. P001)
* **Product Name** → description of the item
* **Category** → optional grouping (e.g. Electronics, Stationery)
* **Unit Cost** → cost per item
* **Reorder Level** → minimum stock before reordering
* **Supplier** → who you buy it from

---

### Step 3: Enter Your Products

1. Each **row = one product**
2. Make sure **Product ID is unique**
3. Fill in realistic numbers for cost and reorder level

Example:

| Product ID | Product Name       | Category    | Unit Cost | Reorder Level | Supplier   |
| ---------- | ------------------ | ----------- | --------- | ------------- | ---------- |
| P001       | LED Fidget Spinner | Toys        | 2.50      | 20            | Paperworks |
| P002       | USB Cable          | Electronics | 1.80      | 15            | TechSupply |

💡 **Tip:**
Reorder level = “When stock hits this number, I want to order more”

---

### Step 4: Convert the List into an Excel Table

This makes formulas and reports work automatically.

1. Click **any cell** inside your product list
2. Press **Ctrl + T**
3. Confirm **“My table has headers”**
4. Click **OK**

---

### Step 5: Rename the Table

1. Click anywhere inside the table
2. Go to the **Table Design** tab
3. In the **Table Name** box (top left), rename it to:
   **`Products`**
4. Press **Enter**

---

### Step 6: Create a Named List for Product IDs

This prevents data entry mistakes later.

1. Select the **Product ID column** (not the header)
2. Go to the **Formulas** tab
3. Click **Define Name**
4. Enter:

   * **Name:** `Product_List`
5. Click **OK**

📌 You will use this name for dropdown lists in the Transactions sheet.

---

### Step 7: Why This Sheet Matters

* This is your **central catalog**
* Every other sheet pulls data from here
* If you change a product name, supplier, or reorder level here →
  it updates everywhere else automatically

✔ Segment 1 complete
You now have a **clean, structured product master list**

---

### What’s Next (Segment 2)

In the next segment, we will build the **Transactions sheet**, where:

* Every sale
* Every stock receipt
* Every opening balance

is recorded correctly and safely.

👉 Say **“Continue to Segment 2”** when you’re ready.

---
---
---
Perfect — let’s keep building.
This segment is **very important** because **all stock movement starts here**.

---

# SEGMENT 2: Create the Transactions Sheet (Stock In & Out)

The **Transactions** sheet is the *heartbeat* of the inventory system.
Every time stock moves **in** or **out**, it must be recorded here.

---

## What This Sheet Does

* Records **every stock movement**
* Drives inventory balances automatically
* Creates a full **audit trail**
* Feeds the Inventory, Orders, and Reports sheets

👉 **You will only type data here — never in the Inventory sheet**

---

## Step 1: Create the Transactions Sheet

1. Click the **+** button to add a new sheet
2. Rename the sheet to:
   **`Transactions`**

---

## Step 2: Add Column Headers

In **row 1**, enter the following headers:

| Cell | Header Name    |
| ---- | -------------- |
| A1   | Transaction ID |
| B1   | Date           |
| C1   | Product ID     |
| D1   | Site           |
| E1   | Quantity       |
| F1   | Type           |

---

### What Each Column Means (Beginner-Friendly)

* **Transaction ID**
  A reference number (invoice, receipt, or internal ID)

* **Date**
  The date the stock movement happened

* **Product ID**
  Must match a Product ID from the Products sheet

* **Site**
  Where the stock is stored or sold (Store A, Store B, Warehouse, etc.)

* **Quantity**

  * **Positive number** → stock coming in
  * **Negative number** → stock going out

* **Type**
  Short description (Sale, Receipt, Opening Stock, Adjustment)

---

## Step 3: Convert to an Excel Table

1. Click any cell in your header row
2. Press **Ctrl + T**
3. Confirm **My table has headers**
4. Click **OK**

---

## Step 4: Rename the Transactions Table

1. Click inside the table
2. Go to **Table Design**
3. Rename the table to:
   **`Transactions`**
4. Press **Enter**

This ensures formulas update automatically when new rows are added.

---

## Step 5: Add Product ID Validation (VERY IMPORTANT)

This prevents typing errors.

1. Select the **Product ID column** (not the header)
2. Go to the **Data** tab
3. Click **Data Validation**
4. Under **Allow**, select **List**
5. In **Source**, press **F3**
6. Select **`Product_List`**
7. Click **OK**

✅ Now Product ID becomes a dropdown
✅ Only valid products can be selected

---

## Step 6: Enter Opening Stock

Opening stock is your **starting balance**.

Example entries:

| Transaction ID | Date       | Product ID | Site    | Quantity | Type          |
| -------------- | ---------- | ---------- | ------- | -------- | ------------- |
| OS001          | 01/01/2025 | P001       | Store A | 50       | Opening Stock |
| OS002          | 01/01/2025 | P001       | Store B | 30       | Opening Stock |

💡 One row per **product + site**

---

## Step 7: Enter Sales and Receipts

### Sales (Stock Going Out)

* Use **negative quantities**

Example:

| Transaction ID | Date       | Product ID | Site    | Quantity | Type |
| -------------- | ---------- | ---------- | ------- | -------- | ---- |
| S001           | 05/01/2025 | P001       | Store A | -5       | Sale |

---

### Receipts (Stock Coming In)

* Use **positive quantities**

Example:

| Transaction ID | Date       | Product ID | Site    | Quantity | Type    |
| -------------- | ---------- | ---------- | ------- | -------- | ------- |
| R001           | 06/01/2025 | P001       | Store A | 20       | Receipt |

---

## Step 8: Key Rules to Follow (Read Carefully)

✔ Never overwrite old transactions
✔ Always add new rows at the bottom
✔ Never type directly into Inventory
✔ Negative = out, Positive = in
✔ Every movement must be recorded here

---

## Step 9: Why This Sheet Is Powerful

* Explains *why* stock levels change
* Allows historical analysis
* Makes error tracing easy
* Automatically updates:

  * Inventory
  * Orders
  * Reports

If something looks wrong later — **this is where you check first**

---

## Segment 2 Complete 🎉

You now have:
✔ A clean master product list
✔ A controlled transaction log
✔ Error-proof product selection

---

## Next Up (Segment 3)

We will build the **Inventory sheet**, where:

* Stock is calculated automatically
* Product details are pulled using XLOOKUP
* Reorder flags are generated

👉 Say **“Continue to Segment 3”** when ready.

---
---
---
Great — this is where everything starts to *feel* like a real system.
In this segment, **Excel will start doing the work for you**.

---

# SEGMENT 3: Build the Inventory Sheet (Live Stock by Site)

The **Inventory** sheet answers one key question:

> **“How much stock do I have right now, and where is it?”**

You will **not type quantities here**.
Everything is calculated from the **Transactions** sheet.

---

## Step 1: Create the Inventory Sheet

1. Add a new worksheet
2. Rename it to:
   **`Inventory`**

---

## Step 2: Add Column Headers

In **row 1**, enter these headers:

| Cell | Header Name      |
| ---- | ---------------- |
| A1   | Site             |
| B1   | Product ID       |
| C1   | Product Name     |
| D1   | Supplier         |
| E1   | Unit Cost        |
| F1   | Reorder Level    |
| G1   | Quantity On Hand |
| H1   | Stock Value      |
| I1   | Reorder?         |
| J1   | Order Date       |

---

### What Each Column Does

* **Site / Product ID** → identify *where* and *what*
* **Product Name / Supplier / Cost / Reorder Level** → pulled from Products
* **Quantity On Hand** → calculated from Transactions
* **Stock Value** → cost × quantity
* **Reorder?** → Yes / No flag
* **Order Date** → when you decide to reorder

---

## Step 3: List All Site–Product Combinations

This step is manual but important.

For **each site**, list **every product** once.

Example:

| Site    | Product ID |
| ------- | ---------- |
| Store A | P001       |
| Store A | P002       |
| Store B | P001       |
| Store B | P002       |

💡 Tip:
Copy Product IDs from the Products sheet and paste them under each site.

---

## Step 4: Convert to an Excel Table

1. Click inside the list
2. Press **Ctrl + T**
3. Confirm **My table has headers**
4. Click **OK**

---

## Step 5: Rename the Inventory Table

1. Click inside the table
2. Go to **Table Design**
3. Rename it to:
   **`Inventory`**
4. Press **Enter**

---

## Step 6: Pull Product Details Using XLOOKUP

We now link this sheet to the **Products** table.

---

### Product Name Formula

1. Click in **C2**
2. Enter:

```excel
=XLOOKUP([@[Product ID]], Products[Product ID], Products[Product Name], "Not Found")
```

3. Press **Enter**

Excel will auto-fill down.

---

### Supplier Formula

In **D2**:

```excel
=XLOOKUP([@[Product ID]], Products[Product ID], Products[Supplier], "Not Found")
```

---

### Unit Cost Formula

In **E2**:

```excel
=XLOOKUP([@[Product ID]], Products[Product ID], Products[Unit Cost], 0)
```

---

### Reorder Level Formula

In **F2**:

```excel
=XLOOKUP([@[Product ID]], Products[Product ID], Products[Reorder Level], 0)
```

---

✅ If you change product details in **Products**, they update here automatically.

---

## Step 7: Calculate Quantity On Hand (MOST IMPORTANT STEP)

This formula adds **all transactions** for each product at each site.

1. Click in **G2**
2. Enter:

```excel
=SUMIFS(
    Transactions[Quantity],
    Transactions[Product ID], [@[Product ID]],
    Transactions[Site], [@Site]
)
```

3. Press **Enter**

This:

* Adds receipts
* Subtracts sales
* Updates instantly when new transactions are added

---

## Step 8: Calculate Stock Value

1. Click in **H2**
2. Enter:

```excel
=[@[Quantity On Hand]] * [@[Unit Cost]]
```

3. Press **Enter**

---

## Step 9: Create the Reorder Flag

This checks if stock is too low.

1. Click in **I2**
2. Enter:

```excel
=IF([@[Quantity On Hand]] <= [@[Reorder Level]], "Yes", "No")
```

3. Press **Enter**

---

## Step 10: Use Filters to Find Low Stock

1. Click the filter arrow in **Reorder?**
2. Select **Yes**

You now see:
✔ What needs reordering
✔ At which site
✔ From which supplier

---

## Step 11: Why You Never Type Here

* Quantities are formula-driven
* Manual changes would break accuracy
* All updates must come from **Transactions**

Think of this sheet as your **single source of truth**

---

## Segment 3 Complete ✅

You now have:
✔ Live stock levels per site
✔ Automatic reorder detection
✔ Inventory value calculation

---

## Next Segment (Segment 4)

We’ll build the **Orders sheet**, where:

* Reorder items are grouped by supplier
* Pivot tables create instant purchase lists
* Filters and slicers make it easy to use

👉 Say **“Continue to Segment 4”** when ready.

---
---
---

Awesome — this is where your system starts **saving you real time**.
No more scanning rows or guessing what to order.

---

# SEGMENT 4: Build the Orders Sheet (Automatic Reorder List)

The **Orders** sheet answers this question:

> **“What do I need to order, from whom, and for which site?”**

This sheet is **100% automatic** and built using a **Pivot Table**.

---

## Step 1: Create the Orders Sheet

1. Add a new worksheet
2. Rename it to:
   **`Orders`**

---

## Step 2: Prepare the Inventory Sheet for Ordering

Before building the pivot table, we need to **mark what we intend to order**.

### Enter Order Dates

1. Go to the **Inventory** sheet
2. Filter **Reorder? = Yes**
3. Select all visible cells in the **Order Date** column
4. Press **Ctrl + ;** (enters today’s date)
5. Press **Ctrl + Enter**

📌 This marks the items you want to include in today’s order.

---

## Step 3: Create the Pivot Table

1. Click anywhere inside the **Inventory table**
2. Go to **Insert → PivotTable**
3. Choose **From Table/Range**
4. Select **Existing Worksheet**
5. Location: click in **Orders!A1**
6. Click **OK**

---

## Step 4: Set Up the Pivot Table Fields

Arrange the fields exactly like this:

### Rows

* **Supplier**
* **Product ID**
* **Product Name**

### Columns

* **Site**

### Values

* **Reorder Level** (Sum)

### Filters

* **Reorder?**
* **Order Date**

---

## Step 5: Apply Filters (Critical)

1. Filter **Reorder? = Yes**
2. Filter **Order Date = Today**

Now the pivot shows **only items you intend to order today**.

---

## Step 6: Improve the Layout (Optional but Recommended)

1. Click inside the pivot table
2. Go to **Design** tab
3. Choose a **gray style**
4. **Subtotals → Show at Bottom of Group**
5. **Insert Blank Line after Each Item**

---

## Step 7: Add a Supplier Slicer

This lets you filter orders by supplier.

1. Click inside the pivot table
2. Go to **Insert → Slicer**
3. Select **Supplier**
4. Click **OK**
5. Move and resize the slicer as needed

🎯 Now you can:

* View one supplier
* Select multiple suppliers
* Instantly update the order list

---

## Step 8: How This Sheet Is Used in Practice

Typical workflow:

1. Inventory shows **Reorder? = Yes**
2. You enter an **Order Date**
3. Orders sheet updates automatically
4. You send purchase orders to suppliers
5. When goods arrive → record receipt in Transactions
6. Inventory updates → Reorder? becomes No

---

## Step 9: Why This Sheet Is Powerful

* No manual calculations
* Grouped by supplier
* Site-level quantities visible
* Refreshable with **Data → Refresh All**

---

## Segment 4 Complete 🎉

You now have:
✔ Automatic purchase order list
✔ Supplier-based ordering
✔ One-click refresh

---

## Next Segment (Segment 5)

Final segment — we’ll build the **Reports sheet**, where:

* Stock is summarized by site
* Inventory value is analyzed
* Conditional formatting highlights risks

👉 Say **“Continue to Segment 5”** when you’re ready.

---
---
---

Nice — this is the **final piece**.
Segment 5 turns all that data into **clear, decision-ready insight**.

---

# SEGMENT 5: Build the Reports Sheet (Stock Summary & Insights)

The **Reports** sheet answers:

> **“How healthy is my inventory right now?”**

This is where managers and owners spend their time.

---

## Step 1: Create the Reports Sheet

1. Add a new worksheet
2. Rename it to:
   **`Reports`**

---

## Step 2: Create Stock-on-Hand Report (Units)

### Insert Pivot Table

1. Click anywhere inside the **Inventory table**
2. Go to **Insert → PivotTable**
3. Choose **Existing Worksheet**
4. Location: click **Reports!A1**
5. Click **OK**

---

### Arrange Fields

#### Rows

* **Product Name**
* **Product ID**

#### Columns

* **Site**

#### Values

* **Quantity On Hand** (Sum)

---

### Format the Pivot

1. Click inside pivot
2. Go to **Design**
3. Choose a **gray style**
4. Ensure **Grand Totals** are on

This shows **how many units you have at each site**.

---

## Step 3: Create Stock Value Report

1. Copy the entire pivot table
2. Paste it a few rows below (e.g. starting at A15)
3. In the **Values** area:

   * Remove *Quantity On Hand*
   * Add **Stock Value** (Sum)

Now you see **money tied up in inventory**, by site.

---

## Step 4: Add Conditional Formatting (Visual Insight)

### Quantity Report

1. Select the numbers in the **Quantity On Hand pivot**
2. Go to **Home → Conditional Formatting**
3. Choose **Color Scales**
4. Select **Green–Yellow–Red**

🔴 Low stock
🟡 Medium stock
🟢 High stock

---

### Stock Value Report

Repeat the same steps for the **Stock Value pivot**.

This makes problem areas visible instantly.

---

## Step 5: Add Total Inventory Value (Headline Metric)

### Calculate Total Stock Value

1. Go back to the **Inventory** sheet
2. Select a blank cell near the top
3. Enter:

```excel
=SUM(Inventory[Stock Value])
```

4. Press **Enter**

---

### Format It

1. Format as **Currency**
2. Add:

   * Light gray fill
   * Bold text
   * Thick left border
3. Center vertically and horizontally

This becomes your **headline number**.

---

## Step 6: Optional – Saved Views (Advanced but Useful)

Saved views let you switch perspectives quickly.

### Goods Received View

1. Filter Inventory:

   * **Reorder? = No**
   * **Order Date > blank**
2. Go to **View → Custom Views**
3. Add view called: **Goods Received**

---

### Reorder View

1. Filter:

   * **Reorder? = Yes**
   * **Order Date = blank**
2. Save view as: **Reorder**

---

### All Data View

1. Clear filters
2. Save view as: **All Data**

Now you can switch views instantly.

---

## Step 7: How Everything Connects (Mental Model)

| Sheet        | Purpose       |
| ------------ | ------------- |
| Products     | What you sell |
| Transactions | What moves    |
| Inventory    | What you have |
| Orders       | What to buy   |
| Reports      | What it means |

➡ Transactions drive Inventory
➡ Inventory drives Orders
➡ Everything feeds Reports

---

## SYSTEM COMPLETE 🎉

You’ve built a:
✔ Multi-site inventory system
✔ Fully automatic reorder process
✔ Supplier-based ordering tool
✔ Real-time reporting dashboard

All in **one Excel workbook**.

---

## What You Can Do Next (Optional Enhancements)

* Data entry forms
* Barcode scanning
* User-level permissions
* VBA automation
* Power Query import
* Power BI dashboards

If you want, I can:

* Turn this into a **printable guide**
* Create a **starter template**
* Simplify it further for **absolute beginners**
* Add **error checks and safeguards**

Just tell me what you want next 👌





