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
   
   * Stock balance calculations
   * Pivot tables
   * Charts
   * Printable issue receipts setup
   
   Say **“Continue to Segment 4”** when ready.
   
   ---
   ---
   
   Awesome — this is the **brain of the system** 🧠
   **SEGMENT 4** turns all those entries into **live stock balances, reports, and management visibility**.
   
   ---
   
   # 🟥 SEGMENT 4: DASHBOARD (AUTOMATION + REPORTING)
   **DAILY OPERATIONS RULES**
   * Enter incoming stock only in `Receiving_Inward`
   * Enter issued stock only in `Issuing_Outward`
   * Never edit `Items_Master` (Lock it so users cannot edit it)
   * Dashboard is view-only (Lock it so users cannot edit it)
   
   ## The Dashboard shows:
   
   * Current stock per item 
   * Totals by category 
   * Totals received in a given duration 
   * Totals Issued Out in a given duration 
   * Alerts for low stock
   * All values update automatically.
   ---
   
   # 🟩 PHASE 8: BUILD THE STOCK BALANCE TABLE
   
   ## STEP 1: Create Table Headers
   
   Go to **Dashboard** sheet.
   
   Starting at **Row 5**, type:
   
   | Cell | Header          |
   | ---- | --------------- |
   | A5   | Item_Code       |
   | B5   | Item_Name       |
   | C5   | Opening_Stock   |
   | D5   | Total_Received  |
   | E5   | Total_Issued    |
   | F5   | Current_Balance |
   | G5   | Category        |
   
   Make headers **bold**.
   
   ---
   
   ## STEP 2: Pull Item List Automatically
   
   ### Item_Code
   
   ```excel
   =Items_Master!A2
   ```
   
   ### Item_Name
   
   ```excel
   =Items_Master!B2
   ```
   
   ### Opening_Stock
   
   ```excel
   =Items_Master!D2
   ```
   
   Drag **A6:C6 down** until last item.
   
   ---
   
   ## STEP 3: Calculate Total Received
   
   ### Total_Received
   
   ```excel
   =SUMIFS(Receiving_Inward!C:C, Receiving_Inward!A:A, A6)
   ```
   
   Copy down.
   
   ---
   
   ## STEP 4: Calculate Total Issued
   
   ### E6
   
   ```excel
   =SUMIFS(Issuing_Outward!C:C, Issuing_Outward!A:A, A6)
   ```
   
   Copy down.
   
   ---
   
   ## STEP 5: Calculate Current Balance (LIVE)
   (Opening_Stock + Total_Received - Total_Issued_Out)
   
   ### F6
   
   ```excel
   =C6 + D6 - E6
   ```
   
   Copy down.
   
   📌 This balance updates **instantly** when:
   
   * Stock is received
   * Stock is issued
   
   ---
   
   ## STEP 6: Pull Category (For Reporting)
   
   ### G6
   
   ```excel
   // For new excel version 
   =XLOOKUP(A6, Items_Master!A:A, Items_Master!D:D)

   // For older versions
   =INDEX(Items_Master!D:D, MATCH(A6, Items_Master!A:A, 0))
   ```
   
   Copy down.
   
   ---
   
   ## STEP 7: Convert to Table
   
   1. Click inside the stock table
   2. Press **Ctrl + T**
   3. Tick **My table has headers**
   4. OK
   
   This table becomes the **live stock register**.
   
   ---
   
   # 🟩 PHASE 9: TOP-LEVEL SUMMARY (KPIs)
   
   Place these at the **top of Dashboard** (Row 1–3).
   
   ### Total Number of Items
   
   ```excel
   =COUNTA(Items_Master!B:B) - 1
   ```
   
   ### Total Received
   
   ```excel
   =SUM(D:D)
   ```
   
   ### Total Issued
   
   ```excel
   =SUM(E:E)
   ```
   
   ### Total Stock Balance
   
   ```excel
   =SUM(F:F)
   ```
   
   Format them as **big bold numbers**.
   
   ---
   
   # 🟩 PHASE 10: PIVOT TABLE REPORTS
   
   ## STEP 1: Received Stock Report
   
   1. Select **Receiving_Inward** sheet
   2. Click any cell → Insert → PivotTable
   3. New worksheet → OK
   
   ### Pivot Layout
   
   * Rows: Item_Name
   * Values: Sum of Quantity_Received
   * Filters: Date_Received
   
   Rename sheet: **Rpt_Receiving**
   
   ---
   
   ## STEP 2: Issued Stock by Officer
   
   1. Select **Issuing_Outward**
   2. Insert → PivotTable
   
   ### Pivot Layout
   
   * Rows: Officer_Name
   * Columns: Item_Name
   * Values: Sum of Quantity_Issued
   * Filters: Date_Issued
   
   Rename sheet: **Rpt_Issuing**
   
   ---
   
   ## STEP 3: Stock by Category
   
   From **Dashboard stock table**:
   
   * Rows: Category
   * Values: Sum of Current_Balance
   
   ---
   
   # 🟩 PHASE 11: CHARTS (MANAGEMENT VIEW)
   
   Recommended charts:
   
   * 📊 Bar chart: Stock balance by category
   * 📈 Line chart: Monthly receipts vs issues
   * 📉 Column chart: Top 10 issued items
   
   Insert charts directly into **Dashboard**.
   
   ---
   
   # 🟩 PHASE 12: LOCK THE DASHBOARD
   
   1. Select entire sheet
   2. Format Cells → Protection → Locked
   3. Review → Protect Sheet
   4. No unlocked cells
   
   ✅ Users can only view.
   
   ---
   
   # ✅ END OF SEGMENT 4 CHECKLIST
   
   You now have:
   
   ✔ Automatic stock balances
   ✔ Live updating register
   ✔ Officer accountability
   ✔ Management dashboards
   ✔ Audit-ready reports
   
   ---
   
   # 🟪 WHAT’S NEXT (SEGMENT 5 – OPTIONAL ADVANCED)
   
   If you want, next we can add:
   
   * ✅ **Printable Issue Receipts**
   * ✅ **Receive / Issue Forms (button-driven)**
   * ✅ **Auto receipt numbers**
   * ✅ **Full VBA locking & role-based access**
   
   👉 Say **“Continue to Segment 5”** and tell me which features you want first.
   
   ---
   ---
   
   Love it — **Segment 5 is where this becomes a *professional, audit-ready system*** 🧾🔐
   We’ll still keep it **simple for users**, even if it uses a little VBA under the hood.
   
   This segment has **3 parts**:
   
   1. Printable Issue Receipts
   2. Receive / Issue Forms (easy buttons)
   3. Auto-locking & user protection
   
   We’ll do them **step-by-step** so nothing breaks.
   
   ---
   
   # 🟪 SEGMENT 5A: PRINTABLE ISSUE RECEIPTS
   
   ## 🎯 Purpose (Explain to Users)
   
   > Every issue entry can generate a **printed receipt** for:
   
   * Officer signature
   * Store records
   * Audit trail
   
   ---
   
   ## STEP 1: Create Receipt Sheet
   
   1. Add a new sheet
   2. Rename it: **Issue_Receipt**
   
   ---
   
   ## STEP 2: Design the Receipt Layout
   
   Starting at Row 1:
   
   ```
   ASTU STORES
   ISSUE RECEIPT
   -----------------------------------
   ```
   
   ### Receipt Fields (Manual Labels)
   
   | Cell | Label         |
   | ---- | ------------- |
   | A4   | Receipt No:   |
   | A5   | Date Issued:  |
   | A6   | Service No:   |
   | A7   | Officer Name: |
   | A8   | Coy:          |
   
   ### Item Table (Row 10)
   
   | A10 | Item Code |
   | B10 | Item Name |
   | C10 | Quantity |
   
   Add signature lines at bottom:
   
   ```
   Issued By: _____________   Date: ________
   Received By: __________   Signature: ________
   ```
   
   ---
   
   ## STEP 3: Link Receipt to Issuing_Outward (Latest Entry)
   
   Assume **latest issue is last row**.
   
   ### Receipt Number (B4)
   
   ```excel
   =LOOKUP(2,1/(Issuing_Outward!H:H<>""),Issuing_Outward!H:H)
   ```
   
   ### Date Issued (B5)
   
   ```excel
   =LOOKUP(2,1/(Issuing_Outward!G:G<>""),Issuing_Outward!G:G)
   ```
   
   ### Service No (B6)
   
   ```excel
   =LOOKUP(2,1/(Issuing_Outward!D:D<>""),Issuing_Outward!D:D)
   ```
   
   ### Officer Name (B7)
   
   ```excel
   =LOOKUP(2,1/(Issuing_Outward!E:E<>""),Issuing_Outward!E:E)
   ```
   
   ### Coy (B8)
   
   ```excel
   =LOOKUP(2,1/(Issuing_Outward!F:F<>""),Issuing_Outward!F:F)
   ```
   
   ### Item Details (Row 11)
   
   ```excel
   A11 = LOOKUP(2,1/(Issuing_Outward!A:A<>""),Issuing_Outward!A:A)
   B11 = LOOKUP(2,1/(Issuing_Outward!B:B<>""),Issuing_Outward!B:B)
   C11 = LOOKUP(2,1/(Issuing_Outward!C:C<>""),Issuing_Outward!C:C)
   ```
   
   ---
   
   ## STEP 4: Print Setup
   
   1. Page Layout → Print Area → Set Print Area
   2. Page Layout → Orientation → Portrait
   3. Margins → Narrow
   
   Users can now **Ctrl + P → Print**.
   
   ---
   
   # 🟪 SEGMENT 5B: RECEIVE & ISSUE FORMS (BEGINNER MODE)
   
   ## 🎯 Purpose
   
   > Forms prevent users from scrolling sheets and entering data wrongly.
   
   ---
   
   ## STEP 1: Enable Developer Tab
   
   1. File → Options → Customize Ribbon
   2. Tick **Developer**
   3. OK
   
   ---
   
   ## STEP 2: Simple Receive Stock Form
   
   ### Use Excel Form (No VBA First)
   
   1. Click anywhere inside **Receiving_Inward table**
   2. Press **Alt + D + O + O** (or add Form to Quick Access)
   3. Excel Data Form opens
   
   Users can:
   
   * Add new record
   * Edit
   * Delete (restrict later)
   
   ---
   
   ## STEP 3: Issue Stock Form
   
   Repeat same steps for **Issuing_Outward table**.
   
   📌 This already makes it **10× easier** for non-technical users.
   
   ---
   
   # 🟪 SEGMENT 5C: AUTO RECEIPT NUMBER (S11)
   
   ## STEP 1: Auto Generate S11 Number (Issuing)
   
   In **Issuing_Outward!H2**:
   
   ```excel
   ="S11/" & TEXT(ROW()-1,"0000")
   ```
   
   Copy down.
   
   📌 Guarantees unique receipt numbers.
   
   (You can later replace with VBA if needed.)
   
   ---
   
   # 🟪 SEGMENT 5D: AUTO LOCK ALL OTHER SHEETS (VBA – OPTIONAL)
   
   ## 🎯 Purpose
   
   > When file opens:
   
   * Dashboard locked
   * Items locked
   * Only entry sheets usable
   
   ---
   
   ### VBA Code (Optional – Safe)
   
   Press **Alt + F11 → Insert → ThisWorkbook**
   
   ```vba
   Private Sub Workbook_Open()
       Sheets("Items_Master").Protect
       Sheets("Dashboard").Protect
       Sheets("Issue_Receipt").Protect
   End Sub
   ```
   
   Save as **.xlsm**.
   
   ---
   
   # 🧠 USER DAILY WORKFLOW (FINAL)
   
   1. Open Excel file
   2. Go to **Receiving_Inward** when stock arrives
   3. Go to **Issuing_Outward** when issuing items
   4. Print receipt if needed
   5. Dashboard updates automatically
   
   ---
   
   # 🏁 FINAL SYSTEM STATUS
   
   You now have:
   
   ✅ Fully automated stock balances
   ✅ Officer accountability
   ✅ Printable receipts
   ✅ Error-proof issuing
   ✅ Management dashboards
   ✅ Audit-ready records
   
   ---
   
   If you want, next I can:
   
   * Package this into a **training SOP (1-page guide)**
   * Add **role-based access (Storekeeper vs Admin)**
   * Convert it into a **template you can reuse**
   
   Just tell me 👍
   
   ---
   ---
```






