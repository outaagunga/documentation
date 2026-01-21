
Create a helper column, then use formula  

`=DATEVALUE(TEXT(A2,"dd/mm/yyyy"))` : Useful when the date format in the cell is inconsistent or not recognized. This ensures the text is formatted correctly before conversion  
It will display as digits. To display it as date, select -> right click -> format -> date  

---
---
---

## 1️⃣ Pivot Table 1 – **Staff Count per Work Station**

### Purpose

Count how many **Active CPL staff** are in each **Work Station**.

### Pivot Field Layout

| Area        | Field                         |
| ----------- | ----------------------------- |
| **Filters** | Rank, Status                  |
| **Rows**    | Work Station                  |
| **Values**  | P/NO (Summarize by **Count**) |

### Filter Settings

* Rank = **CPL**
* Status = **Active**

### Steps

1. Click anywhere in the table on **Sheet1**
2. Go to **Insert → PivotTable**
3. Choose:

   * **From Table/Range**
   * Place on **Existing Worksheet**
   * Location: `Sheet2!A3`
4. Arrange fields as shown above.
5. In **Values**, ensure:

   * P/NO → **Value Field Settings → Count**

---
---

## 2️⃣ Pivot Table 2 – **Staff List for a Specific Work Station (e.g., Meru)**

### Purpose

List **P/NO & Name** of all **Active CPL staff** in one Work Station.

### Pivot Field Layout

| Area        | Field                      |
| ----------- | -------------------------- |
| **Filters** | Rank, Status, Work Station |
| **Rows**    | P/NO, Name                 |
| **Values**  | *(None required)*          |

### Filter Settings

* Rank = **CPL**
* Status = **Active**
* Work Station = **Meru**

### Steps

1. Select the same source table.
2. Insert another PivotTable on **Sheet2**, e.g. at `H3`
3. Arrange fields as above.
4. Apply the filters.

## To show:

> **P/NO in one column** and **Name in the next column (same row)**

you just need to change the PivotTable layout.

---
---

### Fix the Layout (Very Important Step)

1. Click anywhere inside the PivotTable.
2. On the Excel Ribbon, go to:

**PivotTable Analyze → Design → Report Layout → Show in Tabular Form**

3. Then again go to:

**Design → Report Layout → Repeat All Item Labels**
---
Here is how to fix it so your P/No and Name sit perfectly side-by-side:

1. **Remove Subtotals**

* Click inside the Pivot Table.
* Go to the Design tab on the Ribbon.
* Click Subtotals → Do Not Show Subtotals.

2. **Remove Expand/Collapse Buttons** (Optional)
To make it look like a standard flat table without the "+/-" icons:

* Go to the PivotTable Analyze tab.
* In the Show group (far right), toggle off +/- Buttons.

---

Clear all filters

* Go to PivotTable Analyze tab
* Clear -> Filters 
   
---
---
---
---
---

## STEP 1: STANDARDIZE YOUR SHEET

Open your file e.g `ASTU NOMINAL ROLL -AUTOMATION.xlsx` and  
standardize the columns in order, for example  

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

## ENSURE DATA TYPES ARE OF THE CORRECT FORMAT  
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
2. Then go to Data → Data Validation  
3. Custom formula, then enter this formula:  
   
`=COUNTIF(tablename[P/NO],A2)=1`  
Where `A2` is the cell with the P/NO and `tablename` is the name of your table  

Under Error message type:  
`P/NO must be unique!`  

---

### 4.2 ID NO – Numbers Only  
we are adding validation to ID column so that it can only contain numbers  
1. Select `ID NO` column  
2. Data → Data Validation  
3. Custom formula, then enter this formula:  
   
`=ISNUMBER(VALUE(C2))`  
Where `C2` is the cell with the ID NO  

---

### 4.3 Station Dropdown  
We are going to create a dropdown for the work station so that we can only select from the given list  

1. Create new sheet and name it:  
   `LISTS`  
3. In the newly created sheet `LISTS` under `A` column type the list of stations  
   Ensure you type in `A1`, `A2`, `A3` .... e.g:  
* HQ Coy  
* A Coy  
* B Coy  
* C Coy  
* D Coy  

Then move Back to main sheet and:  
1. Select `Station` column  
2. Data → Data Validation → List  
3. Custom formula, then enter this formula:  
   
`=LISTS!$A:$A`  
Where `A` is the column in the newly created `LISTS` sheet with the `Stations` data  

---

**NB:** If there is another column you also want to validate value, then add their values on column `B` of the `LISTS` sheet and replace the `A` in the formula with `B`  

**To clear/ Remove validation**  
1. Select column with the validation  
2. Data → Data Validation  → Clear All  
   
---  

## STEP 5: AUTO CALCULATE AGE  

`=DATEDIF(J2, "12/1/2025", "Y")`  

Or  
`=DATEDIF(J2, TODAY(), "Y")`  

Where `J2` is the cell with date of birth and `“12/01/2025”` is todays date  

Or if you want years and months, you can use this:  
`=IF(J2="", "", DATEDIF(J2, TODAY(), "Y") & " yrs, " & DATEDIF(J2, TODAY(), "YM") & " mos")`  

---

## STEP 6: DURATION CALCULATION  
* Duration in the current work station OR  
*  Duration in the current Rank  
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

**NB:** You can replace `UPPER` with `LOWER` or `PROPER` if you want to change to lower case or proper case  

---

# 🧩 SEGMENT 4 – DATA ENTRY FORM  

To add a data entry form button  

1. Click small dropdown on top left → then select `More Commands`  
3. Choose: `Commands Not in Ribbon`  
4. Then click Add: `FORM`  

### To use the form you've added  

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

### To remove the added FORM button from the quick access bar (Restore quick access bar to defaults)  
To reset the quick access bar to defaults ........  

---

## STEP 17: CREATE MACRO  

Press:  

👉 **ALT + F11**  
Insert → Module OR  
File  → Options  → Customize Ribbon  → then tick Developer options  
Then go to go to Developer tab  → then click Visual Basic  
Ensure you save the file as macro enabled e.g `.xlsm` and NOT `.xlsx`  

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

# 🧩 SEGMENT 7 – DUPLICATE & ERROR CONTROL  

## STEP 19: HIGHLIGHT DUPLICATE `P/NO`  

1. Select P/NO column
2. Conditional Formatting
3. Formula:
   
`=COUNTIF($B:$B,$B2)>1`  
Where `B` is the cell with the `P/NO`  
Color → RED  

---

## STEP 20: INVALID DATE CHECK  

E.g For DOB column:  

`=NOT(ISNUMBER(E2))`  

---

## STEP 22: PROTECT FORMULAS

1. Select the columns with the formula e.g  `AGE`,  `DURATION` in the current station, `RETIREMENT DATE`  
2. Then go to `Format Cells` → then tick `Locked`  

Then go to:  
`Review tab` → and select `Protect Sheet`    
Unde the `Allow`, tick all the things you want ordinary user to do e.g:  

☑ Select unlocked  
☑ Insert rows  
Then enter the password to `protect` the sheet  

---

# 🧩 SEGMENT 11 – BACKUP SYSTEM  

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

# 🧩 SEGMENT 12 – BUILD DASHBOARD SHEET  
**What This Dashboard Will Show e.g**  

1. Total staff  
2. By station  
3. By rank  
4. Retirement within:  
   * 1 year  
   * 5 years  
5. Missing ID NO  
6. Age groups  
7. Search box  

All updating automatically.  

**CREATE DASHBOARD SHEET**  
1. Add new sheet and name it:  
`DASHBOARD`  

**CREATE CALCULATION SHEET**  
Cretae another sheet and name it:  
`CALC`  
The `CALC` sheet will hold formula calculation formulars so that the dashboard stays clean  

---  

### Formula to create total staff  
In cell `A2` of the `CALC` sheet type:  
`Total Staff`  

In cell `B2` of the same `CALC` sheet  
Enter this formula to calculate the total number of staff  
`=COUNTA(tblName[P/NO])`  

Where `tblName` is the name of the main sheet  
`P/NO` is the cell with unique identity in the main sheet  

---

## STEP 26: MISSING ID NO
In the next row e.g `A3` type: 
`Those with missing ID Numbers`  
then in the `B3`, enter the formula to count all cells with blaks in the `ID NO` column  
`=COUNTIF(tblName[ID NO],"")`  

---

## STEP 27: RETIRING IN 1 YEAR  
In the next row of the `CALC` sheet,  
Type e.g `Officers due to retire in the next 1 year`  
Then enter this formula to calculate their total number  
`=COUNTIF(tblName[RETIREMENT DATE],"<="&TODAY()+365)`  

---

## STEP 28: RETIRING IN 5 YEARS  
In the next row of the `CALC` sheet,  
Type e.g `Officers due to retire in 5 years time`  
Then enter this formula to calculate their total number  
`=COUNTIF(tblName[RETIREMENT DATE],"<="&TODAY()+1825)`  

---  

Now go to the `Dashboard` sheet we had created earlier  
To display e.g the total number of staff,  
In e.g cell `A2` type `TOTAL STAFF`  
Then in cell `B2` enter this formula to dispay the total staff as calculated on the `CALC` sheet  

`=CALC!B2`  
Where `CALC` is the name of the sheet with calcualtions  
`B` is the cell in the `CALC` sheet with the calculated total number of staff  

---
Do the same for:  
* Missing ID NO  
* Those due to retire in the next one year  
* Those due to retire in the next five years e.t.c  

---  

# 🟢 PIVOT TABLES – SLOW GUIDE  

## Show staff by their station   
1. Click anywhere in your main table (e.g Sheet1)  
2. Go to top menu:  
👉 INSERT → Pivot Table  

3. In dialog:  
* Select Table/Range:  
  it should show your table name e.g `tbleName`  

4. Choose:  
👉 Existing Worksheet   
👉 Location → Go DASHBOARD sheet and click on the desired cell e.g `D2`  
Then come back to the dialog box. You will see the cell you clicked highlighted in the dialog box  
5. Click OK  

---

### Build the Pivot  
On right panel:  

* Drag → STATION → ROWS  
* Drag → P/NO → VALUES  
* Then set to COUNT
  
*Then you can drag each field on the created pivot table to re-arrange their position*  

---

## To Display staff by Rank  
Repeat the same process as done above  
Then under the `Setup`  

* Drag → RANK → ROWS  
* Then again Drag → P/NO → VALUES  
* Then set to COUNT  

---

# To Display staff  by thier age group  
Go to the main sheet (e.g Sheet1)  
Create a new column and name it:  

`AGE GROUP`  
Then in the firt cell of the `AGE GROUP` column, enter this formula to identify the age group of each staff  
```
=IF([@AGE]="","",
IF([@AGE]<=30,"18-30",
IF([@AGE]<=40,"31-40",
IF([@AGE]<=50,"41-50","51+"))))
```
Now add Pivot for the Age group to the Dashboard  
Repeat the process of creating the pivot table  
Then under the `Setup`  
* Drag → AGE GROUP → ROWS  
* Drag → P/NO → VALUES  
* Then set to COUNT  

---

# 🟢 FORMAT AS “CARDS”  
On DASHBOARD:  

1. Select e.g B2:B5  
2. Then Make:  
* Font size e.g 20  
* Bold  
* Add borders  
* Fill light color  

These become your summary cards.  

---

# CHARTS  

## Create a chart to display staff distribution per thier station  

1. Click inside `pivot table` you had created for staff distribution  
2. Then click `Insert` → then type of chart e.g `Column Chart`  
3. Under the `Title`, give the desired title e.g:  

`Distribuiton of Staff by their Station`  

Then drag the chart to nice position  
Add desired formatting as you like  

---

Do the same to insert charts for the remaing pivot tables  

---

Perfect 👍 — below is a **precise, click-by-click layout plan** for placing your slicers so the dashboard looks professional and is easy to use. Follow this exactly on your file.

---

# 🎯 STEP-BY-STEP SLICER LAYOUT (Excel 2016/2019)

## TARGET DESIGN

We will create:

* A slicer panel on the **left side**
* Charts on the **right**
* Summary cards on the **top**

Like this mental layout:

```
-------------------------------------------------
| TOTAL | MISSING | RET 1YR | RET 5YR           |
-------------------------------------------------
| [STATION]   |  Station Chart                  |
| [RANK]      |  Rank Chart                     |
| [AGE GROUP] |  Age Chart                      |
| [NSSF]      |                                 |
-------------------------------------------------
```

---

# 🟢 STEP 1 – INSERT ALL SLICERS

1. Go to **DASHBOARD** sheet

2. Click inside any pivot table (e.g. PT_Station)

3. On ribbon:

👉 **PivotTable Analyze → Insert Slicer**

4. Tick these fields:

☑ STATION
☑ RANK
☑ AGE GROUP
☑ NSSF

5. Click **OK**

You will see 4 slicer boxes floating on the sheet.

---

# 🟢 STEP 2 – CONNECT SLICERS TO ALL PIVOTS

### Do this for EACH slicer

1. Click the **STATION slicer**
2. Slicer tab → **Report Connections**

Tick:

☑ PT_Station
☑ PT_Rank
☑ PT_Age

3. Press OK

👉 Repeat for:

* RANK slicer
* AGE GROUP slicer
* NSSF slicer

---

# 🟢 STEP 3 – EXACT POSITIONING

We will place slicers on the **LEFT PANEL**

### 1. STATION SLICER

* Drag to:
  👉 Top-left corner around **A8**

Resize to:

* Width: about 3 columns
* Height: 10 rows

Settings:

* Slicer → Columns = **1**

---

### 2. RANK SLICER

Place:

👉 Directly under STATION slicer (around A20)

* Columns = 1
* Height similar to station

---

### 3. AGE GROUP SLICER

Place:

👉 Under RANK slicer (around A32)

* Columns = 1

---

### 4. NSSF SLICER

Place:

👉 Under AGE GROUP (around A42)

* Columns = 1

---

# 🟢 STEP 4 – STYLE THEM

For EACH slicer:

1. Click slicer
2. Slicer tab → choose:

👉 Light blue / professional style

3. Settings:

* Uncheck “Multi-select” if you want single selection
* Keep multi-select if you prefer flexible filtering

---

# 🟢 STEP 5 – ALIGN CLEANLY

Use these tools:

1. Select all slicers (Ctrl + click each)

2. Go to:

👉 Format → Align → Align Left
👉 Format → Distribute Vertically

Now they look neat.

---

# 🟢 STEP 6 – POSITION CHARTS

### Station Chart

Move to:

👉 Around D8

### Rank Chart

👉 Around D24

### Age Chart

👉 Around D40

Leave left side only for slicers.

---

# 🟢 STEP 7 – ADD “CLEAR FILTERS” BUTTON

## Create macro

ALT + F11 → Module

```vba
Sub ClearAllSlicers()

Dim slc As SlicerCache

For Each slc In ThisWorkbook.SlicerCaches
    slc.ClearManualFilter
Next slc

MsgBox "Filters Cleared"

End Sub
```

### Add button on dashboard

* Developer → Insert → Button
* Place near top
* Assign **ClearAllSlicers**

---

# 🟢 STEP 8 – FINAL USER FLOW

User will:

1. Open dashboard
2. Click:

   * STATION = Nairobi
   * RANK = Corporal

👉 All numbers + charts change instantly.

3. Click:

   * NSSF = (Blank)

👉 Instantly see non-compliant staff.

---

# 🚦 CHECKPOINT

Please tell me:

1. Were you able to:

* Insert slicers ✔?
* Connect to all pivots ✔?
* Place on left ✔?

2. Do you prefer:

A. Multi-select slicers
B. Single-select only?

Next I will help you add:

* Printable “Report by slicer selection”
* Auto title that changes like:

👉 “Nairobi – Corporals Overview”

Reply 👍
---
Great — now let’s build a **printable report that automatically follows whatever the user selects in the slicers** 👍.
This will let you:

* Select *Station = Nairobi* → print Nairobi report
* Select *Rank = Sergeant* → print only sergeants
* Combine filters and print that exact view.

I’ll guide you step-by-step.

---

# 🖨 PRINTABLE REPORT BY SLICER SELECTION

## WHAT WE WILL CREATE

1. A clean **PRINT sheet**
2. Dynamic title like:
   👉 *“Nairobi – Corporals Report”*
3. A table of staff matching slicers
4. One-click Print button.

---

# =====================================

# STEP 1 – CREATE PRINT SHEET

# =====================================

1. Add new sheet
2. Rename it:

```
PRINT
```

This sheet will be only for printing.

---

# =====================================

# STEP 2 – ADD DYNAMIC TITLE

# =====================================

### In PRINT!A1 type:

```
STAFF REPORT
```

### In PRINT!A2 put this formula

```excel
="Station: " & IFERROR(GETPIVOTDATA("P/NO",PT_Station),"All")
```

*(If GETPIVOTDATA feels complex, we’ll use a simpler method below.)*

---

### SIMPLER TITLE METHOD (Recommended)

On DASHBOARD create two helper cells:

**DASHBOARD!J2**

```excel
="Station: " & TEXTJOIN(", ",TRUE,UNIQUE(tblNominal[STATION]))
```

**DASHBOARD!J3**

```excel
="Rank: " & TEXTJOIN(", ",TRUE,UNIQUE(tblNominal[RANK]))
```

Then in PRINT sheet:

```excel
=DASHBOARD!J2
```

```excel
=DASHBOARD!J3
```

These change based on slicer results.

---

# =====================================

# STEP 3 – CREATE PRINT DATA TABLE

# =====================================

We will pull only visible (sliced) data.

## 3.1 Create a Pivot for Printing

1. Go to DASHBOARD
2. Insert → PivotTable
3. Source:

👉 tblNominal
4. Location → PRINT!A5

---

### Pivot Setup

**ROWS**

* P/NO
* NAME
* RANK
* STATION
* AGE
* NSSF
* RETIREMENT DATE

**VALUES**

* (None needed)

Rename this pivot:

```
PT_Print
```

---

## 3.2 CONNECT SLICERS TO THIS PIVOT

Very important:

1. Click any slicer
2. Report Connections
3. Tick:

☑ PT_Print

Do this for ALL slicers.

👉 Now the print pivot obeys filters.

---

# =====================================

# STEP 4 – PAGE LAYOUT

# =====================================

Go to PRINT sheet.

### 4.1 Set Page

1. Page Layout → Orientation → **Landscape**

2. Margins → **Narrow**

3. Size → **A4**

---

### 4.2 Repeat Header on Every Page

Page Layout → Print Titles

Rows to repeat:

```
$5:$5
```

---

### 4.3 Fit to One Page Wide

Page Layout → Scale to Fit

* Width: 1 page
* Height: Automatic

---

# =====================================

# STEP 5 – PRINT BUTTON

# =====================================

## Add Macro

ALT + F11 → Module

```vba
Sub PrintBySlicer()

Sheets("PRINT").Activate

ActiveWindow.SelectedSheets.PrintOut Copies:=1, Collate:=True

End Sub
```

---

## Add Button

1. Developer → Insert → Button
2. Place on DASHBOARD
3. Assign:

👉 PrintBySlicer

---

# =====================================

# HOW USER WILL WORK

# =====================================

### Example 1 – Nairobi Only

1. On DASHBOARD
2. Click slicer → STATION = Nairobi
3. Click:

👉 PRINT REPORT button

Result:
Only Nairobi staff appear on print.

---

### Example 2 – Rank Filter

1. STATION = Mombasa
2. RANK = Sergeant
3. Print

👉 Combined filtered report prints.

---

### Example 3 – NSSF Compliance

1. NSSF slicer → (Blank)
2. Print

👉 List of staff missing NSSF.

---

# =====================================

# OPTIONAL IMPROVEMENTS

# =====================================

I can add:

☐ Auto header with today’s date
☐ Logo on print
☐ Signature block
☐ Page numbers
☐ Export to PDF button

---

# 🚦 CHECKPOINT

Tell me:

1. Did you manage to create:

* PRINT sheet ✔?
* PT_Print pivot ✔?
* Connect slicers ✔?

2. Do you prefer:

A. Print directly
B. Export to PDF instead?

Reply and I’ll add the PDF/export version 👍

---
# 🔍 SEARCH BOX IMPLEMENTATION  
To be able to search staff using their `P/NO` or `Name` while on the dashboard sheet  
Add this formula to the desired cell in the Dashboard  

```excel
=IFERROR(
INDEX(tblNominal[NAME],
MATCH($B$8,tblNominal[P/NO],0)),
IFERROR(
INDEX(tblNominal[NAME],
MATCH($B$8,tblNominal[NAME],0)),""))
```
Replace with the actual values  
👉 This searches BOTH P/NO and NAME.  

Do same pattern for others if you want.  

---  


