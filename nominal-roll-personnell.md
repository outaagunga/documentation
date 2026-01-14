
* **Select the data to the end of data**
  `Ctrl + Shift + Arrow`

* **Remove formatting**

  * Select → **No Fill**
  * Remove **Borders**

* **Apply Text Wrap**

  * Select entire sheet → Click **Wrap Text**

---

## 📊 2. Formatting Data

### Format as Table

1. Select your data
2. Go to **Home → Format as Table**
3. Tick:

   * ☑ Headers
   * ☑ First Column
   * ☑ Filter Button

---

## 🧮 3. Useful Excel Formulas

### ➤ Duration in Work Station

```excel
=IF(ISNUMBER(J2), DATEDIF(J2, TODAY(), "Y") & " yrs " & DATEDIF(J2, TODAY(), "YM") & " months", "")
```

* **J2** = date reported to station

---

### ➤ Age Calculation

```excel
=DATEDIF(J2, TODAY(), "Y")
```

OR using specific date:

```excel
=DATEDIF(J2, "12/1/2025", "Y")
```

Years & Months:

```excel
=IF(I3="", "", DATEDIF(I3, TODAY(), "Y") & " yrs, " & DATEDIF(I3, TODAY(), "YM") & " mos")
```

---

### ➤ Retirement Date

```excel
=IF(I3="", "", EDATE(I3, 12*60))
```

* **I3** = Date of birth
* Assumes retirement at **60 years**

---

### ➤ Convert Year (2023 → 01/01/2023)

```excel
=IF(LEN(A2)=4, DATE(A2,1,1), A2)
```

---

### ➤ Find & Replace Dates

1. Select column
2. Press **Ctrl + H**
3. Find: `2023`
4. Replace: `01/01/2023`
5. Click **Replace All**

---

## 🔎 At a Glance – Common Formulas

| Purpose           | Formula                     |
| ----------------- | --------------------------- |
| Calculate Age     | `=DATEDIF(B1,TODAY(),"Y")`  |
| Force Date Format | `=TEXT(A2,"dd/mm/yyyy")`    |
| Uppercase         | `=UPPER(A1)`                |
| Lowercase         | `=LOWER(A1)`                |
| Proper Case       | `=PROPER(A1)`               |
| Rank              | `=RANK.EQ(A1,$A$1:$A$18,0)` |

---

## 🎯 Filters & Duplicates

### Insert Filter

`CTRL + SHIFT + L`

### Check Duplicates

* Conditional Formatting
* Highlight Cells → **Duplicate Values**

### Clear Rules

* Conditional Formatting → **Clear Rules**

---

## 📈 Data Analysis Tools

* Pivot Tables
* Format as Tables
* Data Entry Forms
* Power Query

---

# 🔁 Merging Data from Two Sheets (Power Query)

### Scenario

* Sheet1: 3000+ rows, **NSSF column empty**
* Sheet2: Fewer rows with **NSSF populated**
* Common key: **P/No**

---

## ✅ Power Query Steps

1. Open Excel workbook containing data
2. Create **new workbook**
3. Go to:
   **Data → New Query → From File → From Workbook**
4. Select source workbook
5. Tick **Select Multiple Items**
6. Click **Edit**

### Merge

7. Select destination sheet

8. Click **Merge Queries**

9. Choose:

   * Primary: Sheet1
   * Secondary: Sheet2
   * Match column: **P/No**

10. Join Type → **Left Outer Join**

11. Expand column → select:

* ☑ NSSF
* Uncheck “Use original column prefix”

12. Click **Close & Load**

13. Delete empty columns

14. Sort by NSSF

15. Remove duplicates if needed

---

# 🔧 Excel Formula Methods

## 1. VLOOKUP

```excel
=IFERROR(VLOOKUP(D2, Sheet2!D:E, 2, FALSE), "")
```

---

## 2. INDEX MATCH (Better)

```excel
=IFERROR(INDEX(Sheet2!G:G, MATCH(C2, Sheet2!C:C, 0)), "")
```

---

## 3. XLOOKUP (Best – Excel 365)

```excel
=XLOOKUP(C2, Sheet2!C:C, Sheet2!G:G, "")
```

---

# 🧾 Remove Duplicates

1. Select data
2. Data → Remove Duplicates
3. Tick only:

   * ☑ ID NO
4. OK

---

# 🧪 VBA Automation

```vba
Sub MergeData()
Dim ws1 As Worksheet, ws2 As Worksheet
Dim lastRow1 As Long, lastRow2 As Long
Dim i As Long, matchRow As Variant

Set ws1 = Sheets("Sheet1")
Set ws2 = Sheets("Sheet2")

lastRow1 = ws1.Cells(ws1.Rows.Count, "C").End(xlUp).Row
lastRow2 = ws2.Cells(ws2.Rows.Count, "C").End(xlUp).Row

For i = 2 To lastRow1
    matchRow = Application.Match(ws1.Cells(i, "C").Value, ws2.Range("C2:C" & lastRow2), 0)
    If Not IsError(matchRow) Then
        ws1.Cells(i, "E").Value = ws2.Cells(matchRow + 1, "G").Value
    End If
Next i
End Sub
```

---

# 📝 Excel Data Entry Forms

### Limit

* Max **32 columns**

### Add Form Button

**Quick Way**

* Click dropdown on Quick Access
* Commands Not in Ribbon → **Form** → Add

**Long Way**

* File → Options → Customize Ribbon
* Add **Form**

---

### Using Form

* Click inside table
* Click **Form icon**
* Use:

  * New
  * Edit
  * Find Next
  * Delete

---

## 🎯 Recommendation

| Scenario      | Best Method |
| ------------- | ----------- |
| Excel 365     | XLOOKUP     |
| Old Excel     | INDEX MATCH |
| Large data    | Power Query |
| Repeated task | VBA         |

---

If you want, I can help you:

* Turn this into a **printable PDF guide**
* Create a **checklist version**
* Add screenshots placeholders for training manual 👍


Got it 👍.
I will give you a **true step-by-step automation blueprint** — written like a practical manual you can follow while clicking in Excel. I will not assume anything is obvious, and I’ll add:

* Validation rules
* Auto calculations
* Power Query sync
* Duplicate control
* Protection
* Error handling
* Future scalability

I’ll deliver in **segments** so you can implement part by part.

---

# 🚀 EXCEL AUTOMATION GUIDE

### (Follow-Along Manual – No Coding Experience Required)

## 🎯 AUTOMATION GOALS

By the end, your sheet will:

1. Auto-calculate:

   * Age
   * Years in service
   * Retirement date
   * Rank position

2. Auto-clean:

   * Uppercase names
   * Correct date formats
   * Remove duplicates

3. Auto-import:

   * Pull NSSF from another sheet
   * Merge using P/No

4. Controlled Entry:

   * Data Entry Form
   * Validation
   * Drop-downs

5. One-click reports:

   * Pivot summary
   * Filters
   * Dashboard

---

# ======================================

# 🧩 SEGMENT 1 – STRUCTURE & FOUNDATION

# ======================================

## STEP 1: STANDARDIZE YOUR SHEET

Open your file **ASTU NOMINAL ROLL -AUTOMATION.xlsx**

### 1.1 Create These Core Columns (in this exact order)

| Column          | Purpose       |
| --------------- | ------------- |
| S/NO            | Auto number   |
| P/NO            | Unique key    |
| ID NO           | National ID   |
| NAME            | Full name     |
| DOB             | Date of birth |
| DATE REPORTED   | Start date    |
| AGE             | Auto          |
| SERVICE YEARS   | Auto          |
| RETIREMENT DATE | Auto          |
| NSSF            | From Sheet2   |
| RANK            | Position      |
| STATION         | Dropdown      |
| REMARKS         | Free text     |

👉 If any is missing – create it now.

---

## STEP 2: CONVERT TO TABLE (VERY IMPORTANT)

1. Select all data

2. Press:
   👉 **CTRL + T**

3. Tick:
   ☑ My table has headers

4. Name the table:

* Table Design → Table Name
  👉 Type:

```
tblNominal
```

### WHY?

Tables make:

* Formulas auto-fill
* Power Query stable
* Forms work
* VBA reliable

---

## STEP 3: DATA TYPES SETUP

### 3.1 Set Columns Format

| Column        | Format  |
| ------------- | ------- |
| DOB           | Date    |
| DATE REPORTED | Date    |
| AGE           | General |
| SERVICE YEARS | General |
| P/NO          | Text    |
| ID NO         | Text    |

👉 Do this:

1. Select column
2. Right click → Format Cells
3. Choose correct type

---

## STEP 4: DATA VALIDATION RULES

### 4.1 P/NO – No Duplicates

1. Select P/NO column
2. Data → Data Validation
3. Custom formula:

```
=COUNTIF(tblNominal[P/NO],A2)=1
```

Error message:

> P/NO must be unique!

---

### 4.2 ID NO – Numbers Only

Custom:

```
=ISNUMBER(VALUE(C2))
```

---

### 4.3 Station Dropdown

1. Create new sheet → name:

```
LISTS
```

2. In LISTS!A:A type stations:

* HQ
* Nairobi
* Mombasa
* Kisumu
* Eldoret

3. Back to main sheet:

Data Validation → List:

```
=LISTS!$A:$A
```

---

# ======================================

# 🧩 SEGMENT 2 – AUTO CALCULATIONS

# ======================================

## STEP 5: AGE AUTO FORMULA

In AGE column first row:

```excel
=IF([@DOB]="","",DATEDIF([@DOB],TODAY(),"Y"))
```

👉 Table will auto copy to all rows

---

## STEP 6: SERVICE YEARS

```excel
=IF([@[DATE REPORTED]]="","",DATEDIF([@[DATE REPORTED]],TODAY(),"Y"))
```

---

## STEP 7: RETIREMENT DATE (60 YEARS)

```excel
=IF([@DOB]="","",EDATE([@DOB],12*60))
```

---

## STEP 8: AUTO UPPERCASE NAME

Create helper column TEMP_NAME:

```excel
=UPPER([@NAME])
```

Later we’ll replace NAME using automation.

---

## STEP 9: RANK POSITION

```excel
=RANK.EQ([@SERVICE YEARS],tblNominal[SERVICE YEARS],0)
```

---

# ======================================

# 🧩 SEGMENT 3 – NSSF AUTO IMPORT

# ======================================

### ASSUMPTION

* Sheet2 contains:

  * P/NO
  * NSSF

---

## OPTION A – BEST (XLOOKUP)

In NSSF column:

```excel
=XLOOKUP([@P/NO],Sheet2!C:C,Sheet2!E:E,"")
```

---

## OPTION B – OLD EXCEL

```excel
=IFERROR(INDEX(Sheet2!E:E,MATCH([@P/NO],Sheet2!C:C,0)),"")
```

---

## OPTION C – POWER QUERY (MOST STABLE)

I’ll detail this in next segment because it needs many steps.

---

# ======================================

# 🧩 SEGMENT 4 – DATA ENTRY FORM

# ======================================

## STEP 10: ADD FORM BUTTON

### Quick Method

1. Click small dropdown on top left
2. → More Commands
3. Choose:
   👉 Commands Not in Ribbon
4. Add:
   👉 FORM

---

## STEP 11: USE FORM

1. Click inside table
2. Click FORM icon

You can now:

* Add
* Edit
* Search
* Delete

### FIND RECORD

1. Click CRITERIA
2. Type:

   * P/NO
     or
   * NAME
3. Find Next

---

# ======================================

I will pause here so you can tell me:

👉 Do you want me to continue with:

* SEGMENT 5 – Power Query full automation
* SEGMENT 6 – One-click cleaning macro
* SEGMENT 7 – Duplicate control
* SEGMENT 8 – Dashboard & reports
* SEGMENT 9 – Protection & user mode
* SEGMENT 10 – Auto backup

Tell me:

1. Are you using **Excel 365 or 2016/2019?**
2. Is NSSF coming from another file or same workbook?

I will tailor the next part EXACTLY to your environment 👍

Perfect 👍 — since you are on **Excel 2016/2019**, I will:

* Avoid XLOOKUP (not available)
* Use **INDEX–MATCH, Power Query, and VBA**
* Keep everything offline-compatible

Let’s continue from where we stopped.

---

# ===========================================

# 🧩 SEGMENT 5 – POWER QUERY AUTOMATION (2016/2019)

# ===========================================

## OBJECTIVE

Automatically pull **NSSF** from Sheet2 into your main sheet using **P/NO** as key — refreshable with ONE CLICK.

---

## STEP 12: PREPARE BOTH SHEETS

### 12.1 Sheet1 (Main)

* Must be formatted as table → **tblNominal**

### 12.2 Sheet2 (Source)

Ensure columns:

| P/NO | NSSF | NAME (optional) |

Convert Sheet2 to table:

1. Select Sheet2 data
2. **CTRL + T**
3. Name:

```
tblNSSF
```

---

## STEP 13: LOAD TO POWER QUERY

1. Click any cell in tblNominal
2. Go to:

👉 **Data → From Table/Range**

Power Query opens.

3. Click **Close & Load To → Only Create Connection**

Do same for tblNSSF.

---

## STEP 14: MERGE QUERIES

1. Data → Get Data → Combine → Merge
2. Choose:

* Table1: tblNominal
* Table2: tblNSSF

3. Click column **P/NO** on both sides
4. Join Type:

👉 **Left Outer**

5. OK

---

## STEP 15: EXPAND NSSF

1. New column appears
2. Click expand arrows
3. Tick ONLY:

☑ NSSF

4. Untick:

❌ Use original column prefix

---

## STEP 16: LOAD BACK

Click:

👉 Close & Load → Table

Now:

* NSSF updates anytime you press:

👉 **Data → Refresh All**

---

# ===========================================

# 🧩 SEGMENT 6 – CLEANING AUTOMATION (VBA)

# ===========================================

## OBJECTIVE

One button to:

* Uppercase names
* Trim spaces
* Remove duplicates
* Fix dates

---

## STEP 17: CREATE MACRO

Press:

👉 **ALT + F11**

Insert → Module

Paste:

```vba
Sub CleanNominal()

Dim ws As Worksheet
Set ws = ThisWorkbook.Sheets("Sheet1")

Dim lastRow As Long
lastRow = ws.Cells(ws.Rows.Count, "B").End(xlUp).Row

Dim i As Long

For i = 2 To lastRow

    'Uppercase name
    ws.Cells(i, "D").Value = UCase(Trim(ws.Cells(i, "D").Value))

    'Trim PNO
    ws.Cells(i, "B").Value = Trim(ws.Cells(i, "B").Value)

Next i

'Remove duplicates based on PNO
ws.Range("A1").CurrentRegion.RemoveDuplicates Columns:=2, Header:=xlYes

MsgBox "Cleaning Completed", vbInformation

End Sub
```

---

## STEP 18: ADD BUTTON

1. Developer → Insert → Button
2. Assign:

👉 CleanNominal

---

# ===========================================

# 🧩 SEGMENT 7 – DUPLICATE & ERROR CONTROL

# ===========================================

## STEP 19: HIGHLIGHT DUPLICATE P/NO

1. Select P/NO column
2. Conditional Formatting
3. Formula:

```excel
=COUNTIF($B:$B,$B2)>1
```

Color → RED

---

## STEP 20: INVALID DATE CHECK

For DOB column:

```excel
=NOT(ISNUMBER(E2))
```

---

# ===========================================

# 🧩 SEGMENT 8 – AUTO CALCULATIONS (2016 SAFE)

# ===========================================

### AGE

```excel
=IF(E2="","",DATEDIF(E2,TODAY(),"Y"))
```

### SERVICE YEARS

```excel
=IF(F2="","",DATEDIF(F2,TODAY(),"Y"))
```

### RETIREMENT

```excel
=IF(E2="","",EDATE(E2,720))
```

### RANK

```excel
=RANK.EQ(H2,$H:$H,0)
```

---

# ===========================================

# 🧩 SEGMENT 9 – USER ENTRY SYSTEM

# ===========================================

## STEP 21: DATA ENTRY FORM (2016 METHOD)

Add FORM to Quick Access:

* File
* Options
* Quick Access
* Commands Not in Ribbon → FORM → Add

---

## STEP 22: PROTECT FORMULAS

1. Select AGE, SERVICE, RETIREMENT
2. Format Cells → Locked

Then:

Review → Protect Sheet

Allow:

☑ Select unlocked
☑ Insert rows

---

# ===========================================

# 🧩 SEGMENT 10 – ONE CLICK WORKFLOW

# ===========================================

### DAILY PROCESS

1. Enter data via FORM
2. Click:

* Refresh All (for NSSF)
* Clean Button

3. Save

DONE.

---

# ===========================================

# 🧩 SEGMENT 11 – BACKUP SYSTEM

# ===========================================

Add macro:

```vba
Sub BackupFile()

ThisWorkbook.SaveCopyAs _
ThisWorkbook.Path & "\Backup_" & _
Format(Now, "ddmmyyyy_hhmm") & ".xlsx"

MsgBox "Backup Created"

End Sub
```

---

# NEXT STEP FROM YOU 👇

To make this 100% accurate to YOUR file:

Please confirm:

1. ✅ Which column letter is:

   * P/NO
   * NAME
   * DOB
   * DATE REPORTED

2. Is NSSF:

   * Same workbook?
   * Another file?

3. Do you want:

   * Dashboard sheet?
   * Auto ID generator?

I will rewrite the macros EXACTLY matching your real columns so you just copy–paste and run 🚀

Great choice — a **Dashboard Sheet** will turn your file from just a register into a real management tool 👍.
I will design a dashboard that works perfectly in **Excel 2016/2019 (no fancy 365 features).**

I’ll give this as a **follow-along build manual**.

---

# ==========================================

# 🧩 SEGMENT 12 – BUILD DASHBOARD SHEET

# ==========================================

## 🎯 What This Dashboard Will Show

1. Total staff
2. By station
3. By rank
4. Retirement within:

   * 1 year
   * 5 years
5. Missing NSSF
6. Age groups
7. Search box

All updating automatically.

---

# STEP 23: CREATE DASHBOARD SHEET

1. Add new sheet
   👉 Name it:

```
DASHBOARD
```

---

# STEP 24: CREATE CALCULATION SHEET

Add another sheet:

```
CALC
```

This will hold hidden formulas so dashboard stays clean.

---

# ==========================================

# 🧩 TOTAL COUNTERS

# ==========================================

## STEP 25: TOTAL STAFF

In CALC!B2

```excel
=COUNTA(tblNominal[P/NO])
```

Label in A2:

```
Total Staff
```

---

## STEP 26: MISSING NSSF

```excel
=COUNTIF(tblNominal[NSSF],"")
```

---

## STEP 27: RETIRING IN 1 YEAR

```excel
=COUNTIF(tblNominal[RETIREMENT DATE],"<="&TODAY()+365)
```

---

## STEP 28: RETIRING IN 5 YEARS

```excel
=COUNTIF(tblNominal[RETIREMENT DATE],"<="&TODAY()+1825)
```

---

# ==========================================

# 🧩 PIVOT TABLES FOR DASHBOARD

# ==========================================

## STEP 29: STAFF BY STATION

1. Insert → Pivot Table
2. Source:

👉 tblNominal
3. Place in sheet: DASHBOARD

### Setup

* Rows → STATION
* Values → Count of P/NO

Rename:

```
PT_Station
```

---

## STEP 30: BY RANK

Create another pivot:

* Rows → RANK
* Values → Count P/NO

Name:

```
PT_Rank
```

---

## STEP 31: AGE GROUPS

### Create Helper Column in tblNominal

Add column:

```
AGE GROUP
```

Formula:

```excel
=IF([@AGE]="","",
IF([@AGE]<=30,"18-30",
IF([@AGE]<=40,"31-40",
IF([@AGE]<=50,"41-50","51+"))))
```

Then pivot:

* Rows → AGE GROUP
* Values → Count P/NO

---

# ==========================================

# 🧩 DASHBOARD DESIGN

# ==========================================

## STEP 32: SUMMARY CARDS

On DASHBOARD sheet type:

### Box 1 – Total

```
TOTAL STAFF
```

Next cell:

```excel
=CALC!B2
```

### Box 2 – Missing NSSF

```excel
=CALC!B3
```

### Box 3 – Retire 1 Year

```excel
=CALC!B4
```

### Box 4 – Retire 5 Years

```excel
=CALC!B5
```

👉 Format these big & bold like cards.

---

# ==========================================

# 🧩 CHARTS (2016 SAFE)

# ==========================================

## STEP 33: STATION CHART

1. Select PT_Station
2. Insert → Column Chart
3. Title:

```
Staff by Station
```

---

## STEP 34: RANK CHART

* Insert Bar chart from PT_Rank
* Title:

```
Staff by Rank
```

---

## STEP 35: AGE GROUP CHART

* Insert Pie chart
* From AGE GROUP pivot

---

# ==========================================

# 🧩 INTERACTIVE SEARCH

# ==========================================

## STEP 36: SEARCH BOX

In DASHBOARD B2 type:

```
Search P/NO or Name
```

In C2 (user input cell)

---

## STEP 37: RESULT FORMULAS

### Name Result

```excel
=IFERROR(INDEX(tblNominal[NAME],MATCH(C2,tblNominal[P/NO],0)),"")
```

### Station

```excel
=IFERROR(INDEX(tblNominal[STATION],MATCH(C2,tblNominal[P/NO],0)),"")
```

### NSSF

```excel
=IFERROR(INDEX(tblNominal[NSSF],MATCH(C2,tblNominal[P/NO],0)),"")
```

---

# ==========================================

# 🧩 AUTO REFRESH BUTTON

# ==========================================

## STEP 38: VBA REFRESH

```vba
Sub RefreshDashboard()

ThisWorkbook.RefreshAll

Sheets("DASHBOARD").Activate

MsgBox "Dashboard Updated"

End Sub
```

Add button → assign macro.

---

# ==========================================

# 🧩 USER WORKFLOW

# ==========================================

### Daily Use

1. Enter data via FORM
2. Click:

   * Refresh Dashboard
   * Clean Data
3. View analytics

---

# ==========================================

# NEXT – I CUSTOMIZE TO YOUR REAL FILE

# ==========================================

To align EXACTLY with your sheet I need:

1. Confirm your real column names:

* Is it:

  * P/NO or PNO?
  * DATE REPORTED or DOJ?
  * STATION column exists?

2. Do you want dashboard to show:

☐ By county
☐ By gender
☐ By unit
☐ By years of service bands

3. Should I add:

☐ Export report button
☐ Print summary
☐ Retirement list sheet

Reply with:

👉 “Use current columns as they are”
or paste your exact header row.

Then I give you the FINAL copy-paste dashboard tailored to your file 🚀


