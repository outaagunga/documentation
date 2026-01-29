```
> You are an Excel and inventory management expert.
> I am in charge of stores at ASTU and I want to create an **automated Excel inventory management system**.
> The Excel workbook should:

> 1. Automatically **update stock balances** when items are received or issued.
> 2. Reduce stock levels when items are **issued to officers** and show the **real-time balance**.
> 3. Increase stock levels when **new stock is received** and update the balance automatically.
> 4. Keep a **record of issued items**, including:

>    * Item name
>    * Quantity issued
>    * Name of officer
>    * Date of issue
>    * Issue receipt number
> 5. Keep a **record of received items**, including:

>    * Item name
>    * Quantity received
>    * Date received
>    * Receipt number
> 6. Allow tracking of **who received what item and on which date**.
> 7. Clearly display **current stock balance** for each item.

> The Excel system should include:

> * A **master stock sheet**
> * A **receiving (inward) sheet**
> * An **issuing (outward) sheet**
> * A **Dashboard**
> * Formulas (and VBA if necessary) to automate calculations

* **Receive Stock form**
* **Issue Stock form**
* **Lock sheets automatically**

Generate printable issue receipts

> These are the items currently in the store:

> **A. Food Stuff**

> * Maize flour
> * Rice
> * Cooking oil
> * Milk powder
> * Sugar
> * Dry maize
> * Dry beans
> * Tea leaves
> * Table salt
> * Compo 10 ration
> * Green grams

> **B. Horse Feed**

> * Horse meal
> * Horse cube
> * Maize germ
> * Wheat bran
> * Molasses
> * Mineral salt
> * Hay (Rhodes grass)

> **C. Water Treatment Chemicals**

> * Aluminum sulphate
> * Soda ash
> * Chlorine

> **D. Police Uniform**

> * Combat
> * Jungle boots
> * Smoke jacket
> * Angola shirt
> * Camouflaged T-shirt
> * Camouflaged P-cap
> * Black beret

> Implement this is phases. Start by giving the **Excel structure**, sheet layout, and formulas. Then implementation step by step.
```

---
---
---
---

This guide converts your design into a clear, step-by-step workflow. Each phase builds gradually, so even users with **no Excel experience** can follow safely     
   
We’ll do this **in segments**. This section covers the overall workflow and initial workbook setup  
   
---
   
# 🧭 OVERALL WORKFLOW (BIG PICTURE – FOR NON-EXPERT USERS)  
Think of the Excel system like this:  

1. **All_Items** --> Where items are defined **once** (names, categories, opening stock)  
2. **Incoming_Items** --> Where you record **new stock coming in**  
3. **Outgoing_Items** --> Where you record **stock given to officers**  
4. **Dashboard** --> Where Excel **automatically calculates balances**, totals, and reports  
      👉 *No one types here*  
   
**Users Input Rule**  
* Data entry is allowed only in `Receiving_Inward` and `Issuing_Outward`  
   
---
   
# 🟩 PHASE 1: CREATE THE EXCEL WORKBOOK (FOUNDATION)  
   
## STEP 1: Create a New Workbook  

1. Open **Microsoft Excel**  
2. Click **Blank Workbook**  
3. Save it immediately:  
   * File → Save As  
   * Name: **ASTU_Inventory_System.xlsx**  
     
---
   
## STEP 2: Create the 4 Sheets (Rename Tabs)  
At the bottom of Excel:  

1. Sheet1 → rename to **All_Items**  
2. Sheet2 → rename to **Incomings**  
3. Sheet3 → rename to **Outgoing**  
4. Click ➕ to add a new sheet → rename to **Dashboard**  

You should now see **ONLY these 4 tabs**  
   
---
   
# 🟩 PHASE 2: ITEMS_MASTER (MOST IMPORTANT SHEET)  
   
* This sheet is prepared **ONCE** by you (Store In-Charge)  
* Normal users should NOT edit it later.  
   
---
   
## STEP 3: Set Up Column Headers (Row 1)  
   
Go to **All_Items** sheet.  
In **Row 1**, type exactly:  
   
   | Cell | Text to Type  |
   | ---- | ------------- |
   | A1   | Item_Code     |
   | B1   | Item_Name     |
   | C1   | Measure    (Drop Down List)   |
   | D1   | Category   (Drop Down List)   |
   | E1   | Opening_Stock |
   | F1   | Total_Received (Use formula to pull data from Receiving_Inward sheet)   |
   | G1   | Total_Issued (Use formula to pull data from Issuing_Outward sheet)    |
   | H1   | Current_Balance   (Use formula to calculate the Current_Balance)  |
   
---
   
## STEP 4: Set Up Drop Down Lists (Measure and Category)  
   
In **Column D (Category)**, you will use ONLY these names:  
Select Column D → Data tab → Data Validation → Allow: List    
   * Food Stuff  
   * Horse Feed  
   * Water Treatment  
   * Police Uniform  
   
(Important: spell check because this is what you will use every time)  
   
---
   
## STEP 5: Enter Items (One Time Only)  
Now start filling rows **from Row 2 downward**  
   
### Example (Manually type values only in columns without dropdowns):  
   
   | Item_Code | Item_Name         | Measure | Category        | Opening_Stock |
   | --------- | ----------------- | ------- | --------------- | ------------- |
   | ITM001    | Maize flour       | Kg      | Food Stuff      | 0             |
   | ITM002    | Rice              | Bags    | Food Stuff      | 0             |
   | ITM003    | Cooking oil       | Ltr     | Food Stuff      | 0             |
   | ITM012    | Horse meal        | Kg      | Horse Feed      | 0             |
   | ITM019    | Aluminum sulphate | Kg      | Water Treatment | 0             |
   | ITM028    | Black beret       | Pcs     | Police Uniform  | 0             |
   
**Rules**  
   
   * Item_Code must be **unique**  
   * Item_Name must be **exact**  
   * Opening_Stock can start at **0** (recommended)  
   
Continue until **all items are entered**.  
   
---
### Create a Named List for Product IDs  
This prevents data entry mistakes later  
* Select the Product ID column (not the header)
* Go to the Formulas tab
* Click Define Name
* Enter desired Name e.g **Items_Names**
* Click OK  

📌 You will use this name for dropdown lists in any sheet you want to pull names from master  
   
---
   
**Formula to pull data from Incomings Sheet**  
* For each item, Excel automatically totals all received quantities for each Item_Code from the Receiving_Inward sheet  
```
=SUMIFS(Table_Incomings[Total_Received], Table_Incomings[Item_Code], A2)  
```
Where: 
* `A2` is the `Item_Code` in `All_Items` sheet    
  
**Formula to pull data from Issuing_Outward sheet Sheet**  
```
=SUMIFS(Issuing_Outward!$C:$C, Issuing_Outward!$A:$A, A2)  
```
**Formula to calculate the Current_Balance**  
```
=E2 +F2 -G2  
```
---

## STEP 6: Convert Items_Master into a Table (IMPORTANT)  
   
   1. Click **any cell inside your items list**  
   2. Press **Ctrl + T**  
   3. Tick **My table has headers**  
   4. Click **OK**  

### Rename the Table  
* Click anywhere inside the table  
* Go to the Table Design tab  
* In the **Table Name** box (top left), rename it to: e.g Items_Master  
* Press Enter  
   
---
   
## STEP 7: Lock Items_Master (Beginner-Proofing)  
All stock changes should happen **only via Receiving_Inward and Issuing_Outward**  
   
### 7.1 Unlock Opening_Stock Column  
   
   1. Select **Column D**  
   2. Right-click → Format Cells  
   3. Protection tab  
   4. Uncheck **Locked**  
   5. Click OK  
   
### 7.2 Protect the Sheet  
   
   1. Go to **Review → Protect Sheet**
   2. (Optional) set password
   3. Click OK
   
✅ Result:  
   
   * Users **cannot alter item names or codes**
   * Users **can edit Opening_Stock only**
   
   ---
   
   # 🧠 WHAT USERS NEED TO KNOW (SIMPLE INSTRUCTIONS)
   
   > ❌ DO NOT type balances manually
   > ❌ DO NOT add new items anywhere else
   > ✅ ONLY use Receiving_Inward and Issuing_Outward
   
   ---
   
   ## ✔ CHECKPOINT (END OF SEGMENT 1)
   
   At this stage, you now have:
   
   ✅ Workbook created
   ✅ 4 sheets named correctly
   ✅ All items safely stored
   ✅ Items_Master protected
   ✅ Ready for automation
   
   ---
   
   ### 👉 NEXT SEGMENT (Segment 2)
   
   **Receiving_Inward Sheet**
   
   * Drop-downs
   * Auto item names
   * Fool-proof data entry
   * Zero formulas for users
   
   Say **“Continue to Segment 2”** when ready.
   ---
   ---
   Nice, let’s keep rolling 🚀
   This is **SEGMENT 2**, and it’s where the system starts to feel *automatic* to users.
   
   ---
   
   # 🟦 SEGMENT 2: RECEIVING_INWARD (STOCK COMING IN)  
   
   ## 🎯 PURPOSE OF THIS SHEET (Explain to Users)  
   
   > This sheet is used **ONLY when new stock is received into the store**  
   
   Every time stock arrives:  
   
   * The store clerk records it **here**  
   * The system later **adds it automatically to stock balance**  
   * No calculations are typed by the user  
   
   ---
   
   # 🟩 PHASE 3: SET UP `Receiving_Inward` SHEET  
   
   ## STEP 1: Create Column Headers (Row 1)  
   
   Go to **Receiving_Inward** sheet.  
   
   In **Row 1**, type exactly:  
   
   | Cell | Header            |
   | ---- | ----------------- |
   | A1   | Item_Code         |
   | B1   | Item_Name         |
   | C1   | Quantity_Received |
   | D1   | Date_Received     |
   | E1   | S11_No            |
   
   Make headers **bold**.
   
   ---
   
   ## STEP 2: Create Drop-Down for Item_Name (User-Friendly)  
   
   This prevents wrong item entry.  
   
   ### 2.1 Select Column B (Item_Name)  
   
   * Click on **B2**  
   * Press **Ctrl + Shift + ↓** (or drag down to required number of rows e.g 1000)  
   
   ### 2.2 Add Data Validation  
   
   1. Go to **Data → Data Validation**  
   2. Allow: **List**  
   3. Source:
   4. Press F3 → Then select from the **named list** you had created in the **Items_Master** sheet  
   e.g 
   ```excel
   =Items_Master  
   ```
   
   4. Click **OK**  
   
   ✅ Users now select item names from a drop-down list.  
   
   ---
   
   ## STEP 3: Auto-Fill the remaining columns with the formula  
   Item_Code (No Typing)  
   
   ### 3.1 Auto populate Item_Code  
   Enter this formula in the cell you want to populate the Item_Code  
   Ensure each data is formatted as tables as you give clear table name  
   ```excel
   // In new excel version (XLOOKUP only works in Excel 365 / Excel 2021 or later)  
   =IF(B2="","", XLOOKUP(B2, Table_ItemMaster[Item_Name], Table_ItemMaster[Item_Code], "Not Found"))

   // In order excel verson (Excel 2019, 2016, or older) use this formula  
   =IF(B2="","", INDEX(Table_ItemMaster[Item_Code], MATCH(B2, Table_ItemMaster[Item_Name], 0)))
   ```
   Where `B2` is the lookup value (cell with the data you use as reference). In our case, the the **Item_Name** is what we used as our reference. It is our lookup value   
   The **1st** column is what changes in the formula e.g **Item_Code** changes to **Category**, to **Measure** e.t.c  
   
   📌 Result:
   
   * When user selects Item_Name  
   * Item_Code appears automatically  
   
   ---
   ## Unit of Measure
  Auto populate Data from the master, use this formula  
   
   ---
   
   ## STEP 4: Date Control (Beginner Safe)
   
   ### 4.1 Format Date Column
   
   1. Select **Column D**
   2. Right-click → **Format Cells**
   3. Category → **Date**
   4. Locale → **English (United Kingdom)**
   5. Date format →  **dd/mm/yyyy**
   6. OK
   
   ### 4.2 Add Data Validation (Enforce Dates)
   
   * Select Column D
   * Go to Data → **Data Validation**
   * Allow → **Date**
   * Data → **between**
      * Start date → **1/1/1900** (or your earliest acceptable date)
      * End date → **=31/12/2099** (or your latest acceptable date)
   Optional: Check Show input message → **“Enter date as dd/mm/yyyy”**
   
   Click OK

   ### 4.3 Change Windows Regional Settings  
   The date format in Data Validation is controlled by your system's regional settings, not by Excel itself. However, here are some solutions:
   
   * To Change Windows Regional Settings (affects all programs)
      * Go to Windows Control Panel
      * Click Region (or "Clock and Region" then "Region")
      * Click Additional settings
      * Go to the Date tab
      * Change the Short date format to dd/MM/yyyy
      * Click OK and Apply
        
   ---
   
   ## STEP 5: Quantity Rules (Avoid Errors)
   
   ### 5.1 Restrict Quantity to Numbers Only
   
   1. Select **Column C**
   2. Data → Data Validation
   3. Allow: **Whole number**
   4. Minimum: **1**
   5. Maximum: Enter the largest number possible e.d 125,600,000,000,000,000,000
   6. Click OK
   
   ❌ No negative values
   ❌ No text
   ✅ Clean data
   
   ---
   
   ## STEP 6: Receipt No. Validation
   
   * 1. Select the column with Receipt No.
   > 2. Go to Data - Data validation
   > 3. Allow - text length
   > 4. Minimum value 1
   Error Message: `Receipt No. cannot be empty`
   
   ---
   
   # 🟩 PHASE 4: PROTECT THE SHEET (SMART PROTECTION)  
   
   We lock formulas but allow data entry.  
   
   ### 6.1 Unlock Input Columns  
   
   Unlock:  
   
   * Column B (Item_Code)  
   * Column C (Quantity_Received)  
   * Column D (Date_Received)  
   * Column E (S11_No)  
   
   (Keep Column A locked)
   
   **How:**  
   
   1. Select those columns  
   2. Format Cells → Protection  
   3. Uncheck **Locked**  
   
   ---
   
   ### 6.2 Protect the Sheet
   
   1. Review → Protect Sheet
   2. Allow:
   
      * Select unlocked cells
   3. Set password (optional)
   4. OK
   
   ---
   
   # 🧪 HOW USERS WILL USE THIS SHEET (REAL WORKFLOW)
   
   ### Example Entry:
   
   | Item_Code | Item_Name   | Quantity_Received | Date_Received | S11_No  |
   | --------- | ----------- | ----------------- | ------------- | ------- |
   | ITM001    | Maize flour | 200               | 12/01/2026    | S11/034 |
   
   ✔ Item_Code fills automatically
   ✔ Quantity validated
   ✔ Date clean
   ✔ Ready for stock calculation
   
   ---
   
   # ✅ END OF SEGMENT 2 CHECKLIST
   
   You now have:
   
   ✔ Drop-down controlled items
   ✔ Auto item names
   ✔ Clean quantity & date entry
   ✔ Beginner-proof sheet
   ✔ No formulas touched by users
   
   ---
   
   ### 👉 NEXT SEGMENT (Segment 3)
   
   **Outgoing Sheet**
   
   * Officer tracking
   * Receipt numbers
   * Prevent issuing more than available stock (important)
   
   Say **“Continue to Segment 3”** when ready.
   
   ---
   ---
   
   Great, this is where the system becomes **operationally powerful** 💪
   **SEGMENT 3** covers issuing stock, officer tracking, and error prevention.
   
   ---
   
   # 🟨 SEGMENT 3: Outgoing sheet (STOCK GOING OUT)
   
   ## 🎯 PURPOSE OF THIS SHEET (Explain to Users)
   
   > This sheet is used **ONLY when items are issued to officers or units**.
   
   Every issue entry will:
   
   * Reduce stock automatically (later via Dashboard)
   * Record **who received what**
   * Create a permanent audit trail
   
   ---
   
   # 🟩 PHASE 5: SET UP `Outgoing` SHEET
   
   ## STEP 1: Create Column Headers (Row 1)
   
   Go to **Outgoing** sheet.
   
   Type exactly in **Row 1**:
   
   | Cell | Header          |
   | ---- | --------------- |
   | A1   | Item_Code       |
   | B1   | Item_Name       |
   | C1   | Quantity_Issued |
   | D1   | Service_No      |
   | E1   | Officer_Name    |
   | F1   | Coy             |
   | G1   | Date_Issued     |
   | H1   | S11_No          |
   
   Make headers **bold**.
   
   ---
   
   ## STEP 2: drop-down for Item_Name instead (Same as Receiving)
   
   ### 2.1 Select Column B (from B2 down)
   
   1. Click **B2**
   2. Data → Data Validation
   3. Allow: **List**
   4. Source: 
   Press **F3**, then Select the Name list you had created e.g 
   5. OK
   
   ---
   
   ## STEP 3: Auto-Fill Parts that are not being filled by user  
   
   ### Use Formula in  .....
   
   ```excel
   // In new excel version (XLOOKUP only works in Excel 365 / Excel 2021 or later)
  
   
   // In order excel verson (Excel 2019, 2016, or older) use this formula
   
   ```
   
   Copy the formula down.
   
   📌 Users never type item names.
   
   ---
   
   ## STEP 4: Quantity Control (Critical)
   
   ### 4.1 Restrict Quantity to Whole Numbers
   
   1. Select **Column C**
   2. Data → Data Validation
   3. Allow: **Whole number**
   4. Minimum: **1**
   5. OK
   
   ---
   
   ## STEP 5: Date Format
   
   1. 
   
   ---
   
   # 🟩 PHASE 6: PREVENT ISSUING MORE THAN AVAILABLE STOCK
   
   This is **VERY IMPORTANT** for accuracy.
   
   We’ll do this **without VBA** so beginners don’t break it.
   
   ---
   
   ## STEP 6: Create a Helper Column (Hidden Later)
   
   ### 6.1 Insert New Column I
   
   Header:
   
   ```
   Available_Balance
   ```
   
   ---
   
   ### 6.2 Formula in **I2**
   
   ```excel
   =IF(A2="","",
      XLOOKUP(
         A2,
         Dashboard!A:A,
         Dashboard!F:F
      )
   )
   ```
   
   ➡️ Copy down.
   
   📌 This pulls **real-time balance** from Dashboard.
   
   ---
   
   ## STEP 7: Data Validation Against Balance
   
   ### Apply Validation to **Quantity_Issued (Column C)**
   
   1. Select Column C (from C2 down)
   2. Data → Data Validation
   3. Allow: **Whole number**
   4. Minimum: **1**
   5. Maximum:
   
   ```excel
   =I2
   ```
   
   6. Error Alert:
   
      * Title: **Insufficient Stock**
      * Message: *Issued quantity exceeds available stock*
   
   ✅ Users cannot over-issue stock.
   
   ---
   
   ## STEP 8: Hide Helper Column
   
   1. Right-click Column **I**
   2. Click **Hide**
   
   Users never see it.
   
   ---
   
   # 🟩 PHASE 7: PROTECT ISSUING_OUTWARD SHEET
   
   ### Unlock Input Columns
   
   Unlock:
   
   * A (Item_Code)
   * C (Quantity_Issued)
   * D (Service_No)
   * E (Officer_Name)
   * F (Coy)
   * G (Date_Issued)
   * H (S11_No)
   
   Keep locked:
   
   * B (Item_Name)
   
   ---
   
   ### Protect Sheet
   
   1. Review → Protect Sheet
   2. Allow **Select unlocked cells**
   3. Set password (optional)
   4. OK
   
   ---
   
   # 🧪 HOW USERS WILL USE THIS SHEET (REAL EXAMPLE)
   
   | Item_Code | Item_Name   | Qty | Service_No | Officer    | Coy   | Date       | S11_No  |
   | --------- | ----------- | --- | ---------- | ---------- | ----- | ---------- | ------- |
   | ITM001    | Maize flour | 20  | 237891     | Sgt. Kamau | A Coy | 14/01/2026 | S11/056 |
   
   ✔ Name fills automatically
   ✔ Stock checked automatically
   ✔ Officer recorded
   ✔ Balance updates later
   
   ---
   
   # ✅ END OF SEGMENT 3 CHECKLIST
   
   You now have:
   
   ✔ Issuing control
   ✔ Officer accountability
   ✔ Over-issue prevention
   ✔ Clean audit trail
   ✔ Beginner-safe protection
   
   ---
   
   ### 👉 NEXT SEGMENT (Segment 4)
   
   **Dashboard**
🧠 THE DASHBOARD SHOULD ANSWER ONLY 4 TYPES OF QUESTIONS
If it answers these, it’s complete.
1️⃣ “WHAT DO WE HAVE?”
(Current situation)

2️⃣ “WHAT MOVED?”
(Receipts & issues)

3️⃣ “WHERE IS THE RISK?”
(Low stock, abuse, anomalies)

4️⃣ “WHO DID WHAT?”
(Accountability & audit)

Everything on the Dashboard must map to one of these.

   
   * Stock balance calculations
   * Pivot tables
   * Charts
   * Printable issue receipts setup
   
   ---
   ---
   Here is a visual concept for your **ASTU Inventory Management Dashboard**.

Since you are implementing this in Excel, I have designed this to look like a professional, modern application while remaining achievable with standard Excel shapes, Pivot Tables, and Slicers.

### 🎨 Dashboard Implementation Concept

{
"action": "image_generation",
"action_input": "A clean, professional Excel-style inventory dashboard for a police store (ASTU). The interface features a dark-themed sidebar with icons and a light, modern main panel. Top KPIs show 'Total Stock', 'Items Low', and 'Recent Issues'. Central content includes a clean table listing items like 'Maize Flour', 'Jungle Boots', and 'Rice' with green and red status badges for 'In Stock' and 'Low Stock'. Includes a simple bar chart for stock categories (Food Stuff, Uniform, etc.). The color palette is professional navy blue, white, and subtle grey, with clear typography. High quality, UI/UX design, 4k."
}

---

### 🛠️ How to match your Excel to this look

To make your implementation look as appealing as the image above, follow these design steps:

#### 1. The "App" Look (Clean Layout)

* **Remove Gridlines:** Go to the `View` tab and uncheck **Gridlines**. This immediately makes Excel look like software rather than a spreadsheet.
* **Background:** Fill the entire background with a very light grey or off-white.
* **Navigation Sidebar:** Create a dark blue rectangle on the left. Use **Insert > Icons** for "Home", "Inward", "Outward", and "Reports".

#### 2. KPI Cards (Top Metrics)

* Insert **Rounded Rectangles** at the top.
* Use a white fill with a subtle "Shadow" effect.
* **Linking:** Click on a Text Box inside the shape, go to the formula bar, and type `=Dashboard!$B$1` (linking it to your calculated totals).

#### 3. Color Balance & Status Badges

Use **Conditional Formatting** for the `Current_Balance` or `Status` column:

* **Green (Success):** For items with healthy stock.
* **Amber (Warning):** For items reaching the reorder level.
* **Red (Danger):** For "Out of Stock" items.

> **Tip:** Use a slightly desaturated red () and green () so it doesn't strain the eyes.

#### 4. Interaction (The "Magic" Buttons)

* Insert **Slicers** (Filter by Category or Date).
* Format the Slicers to match your navy blue theme (Slicer Styles > New Slicer Style).
* Place them near the top so the store clerk can filter the entire list with one click.

   ---
   ---
   ---
  


