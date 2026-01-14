
## STEP 1: STANDARDIZE YOUR SHEET

Open your file e.g `ASTU NOMINAL ROLL -AUTOMATION.xlsx` and standardize the columns in order, for example  

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
   👉 `CTRL + T` or
Under the `Home tab` click `format as tables` 

4. Tick:
   `☑ My table has headers`
5. Name the table:
Under Table Design → `Table Name`
  👉 then type desired table name e.g `nominal_roll`

### WHY format as table?

Tables make:
* Formulas auto-fill
* Power Query stable
* Forms work
* VBA reliable

---
**To remove formatting as table:**
Click on the `table design` button- then click `convert to range`

---

## ENSURE DATA TYPES ARE OF THE CORRECT SETUP
e.g 

| Column        | Format  |
| ------------- | ------- |
| DOB           | Date    |
| DATE REPORTED | Date    |
| AGE           | General |
| SERVICE YEARS | General |
| P/NO          | Text    |
| ID NO         | Text    |

👉 To change data type:
1. Select column containing the data you want to change
2. Right click → Format Cells
3. Choose correct data type

---

## STEP 4: DATA VALIDATION RULES
We are going to enfore data validation on `P/NO` so that it does not accept Duplicates

1. Select `P/NO` column
2. Data → Data Validation
3. Custom formula, then enter this formula:
   
`=COUNTIF(tablename[P/NO],A2)=1`
Where A2 is the cell with the P/NO

Under Error message type:
`P/NO must be unique!`

---

### 4.2 ID NO – Numbers Only
we are adding validation to ID column so that it can only contain numbers
1. Select `ID NO` column
2. Data → Data Validation
3. Custom formula, then enter this formula:
   
`=ISNUMBER(VALUE(C2))`
Where C2 is the cell with the ID NO

---

### 4.3 Station Dropdown
We are going to create a dropdown for the work station so that we can only select from the given list

1. Create new sheet and name it `LISTS`

2. In the newly created sheet `LISTS` under `A` column type stations. Ensure you type in `A1`, `A2`, `A3` .... e.g:
* HQ Coy
* A Coy
* B Coy
* C Coy
* D Coy

3. Then move Back to main sheet:
1. Select `Station` column
2. Data → Data Validation → List
3. Custom formula, then enter this formula:
   
`=LISTS!$A:$A`
Where `A` is the column in the `LISTS` sheet with the `Stations`
---
**NB:** If there is another column you also want to validate value, then add their values on column `B` of the `LISTS` sheet and replace the `A` in the formula with `B`  

---

# ======================================

# 🧩 SEGMENT 2 – AUTO CALCULATIONS

# ======================================

## STEP 5: AUTOCALCULATE AGE

`=DATEDIF(J2, "12/1/2025", "Y")`

Or 
`=DATEDIF(J2, TODAY(), "Y")`

Where `J2` is the cell with date of birth and `“12/01/2025”` is todays date

Or if you want years and months, you can use this:
`=IF(J2="", "", DATEDIF(J2, TODAY(), "Y") & " yrs, " & DATEDIF(J2, TODAY(), "YM") & " mos")`

---

## STEP 6: DURATION IN WORKING STATION OR DURATION IN CURRENT RANK:
The formula to calculate duration in working station or duration in current rank is the same. Only the cell reference is what changes  

`=IF(ISNUMBER(J2), DATEDIF(J2, TODAY(), "Y") & " yrs " & DATEDIF(J2, TODAY(), "YM") & " months", "")`
Where `J2` is the cell with the date he reported to the station or the date he was promoted to the current rank

---

## STEP 7: RETIREMENT DATE (60 YEARS)

 `=IF(I3="", "", EDATE(I3, 12*60))`
Where `I3` is the cell with date of birth

---
### Convert Years to Full Dates (e.g 2023 to 02/04/2023):  

Create helper column then use this formulae

`=IF(LEN(A2)=4, DATE(A2,1,1), A2)`
Where `A2` is the cell with the dates you want to convert

---
## STEP 8: AUTO CHANGE `NAME` TO UPPERCASE

Create helper column for the NAME, then use formula:

`=UPPER([@NAME])`

Where `NAME` is the column header of the column that contains names.

**NB:** You can replace `UPPER` with `LOWER` if you want to change to lower case 
---

# ======================================

# 🧩 SEGMENT 4 – DATA ENTRY FORM

# ======================================

To add a data entry form button 

1. Click small dropdown on top left → then select `More Commands`
3. Choose: `Commands Not in Ribbon`
4. Then click Add: `FORM`

## To use the form you've added

1. Click `inside` table →  then Click `FORM` icon

From the data entry form that just popu, You can now:
* Add
* Edit
* Search
* Delete

### To find or edit data using the form
1. Click CRITERIA
2. Then Type: e.g `P/NO` or `NAME`
3. Click Find Next then add or edit the valuse 

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


