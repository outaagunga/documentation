
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
2. Data → Data Validation  
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


