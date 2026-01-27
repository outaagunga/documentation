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

> **A. Food Staff**

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


# ✅ FINAL STRUCTURE (ONLY 4 SHEETS)

1. **Items_Master**
2. **Receiving_Inward**
3. **Issuing_Outward**
4. **Dashboard** (PivotTables + charts + stock balance)

---

## 📘 SHEET 1: `Items_Master` (Single Source of Truth)

### Columns (Row 1)

| Col | Header        |
| --- | ------------- |
| A   | Item_Code     |
| B   | Item_Name     |
| C   | Category (Drop down list)  |
| D   | Opening_Stock |

> 🔹 **Opening_Stock is entered ONLY once here**
> 🔹 No separate Stock_Master needed

### Example

| Item_Code | Item_Name         | Category        | Opening_Stock |
| --------- | ----------------- | --------------- | ------------- |
| ITM001    | Maize flour       | Food Staff      | 500           |
| ITM012    | Horse meal        | Horse Feed      | 300           |
| ITM019    | Aluminum sulphate | Water Treatment | 100           |
| ITM028    | Black beret       | Police Uniform  | 50            |

➡️ Continue until all items are entered.

---

## 📘 SHEET 2: `Receiving_Inward`

### Headers

| Col | Header            |
| --- | ----------------- |
| A   | Item_Code         |
| B   | Item_Name         |
| C   | Quantity_Received |
| D   | Date_Received     |
| E   | S11_No            |

### Data Validation

**A:A → List**

```excel
=Items_Master!$A:$A
```

### Auto Item Code

**B2**
```excel
=IF(A2="","",XLOOKUP(A2,Items_Master!A:A,Items_Master!B:B))
```

➡️ Copy down

---

## 📘 SHEET 3: `Issuing_Outward`

### Headers

| Col | Header          |
| --- | --------------- |
| A   | Item_Code       |
| B   | Item_Name       |
| C   | Quantity_Issued |
| D   | Service_No      |
| E   | Officer_Name    |
| F   | Coy             |
| G   | Date_Issued     |
| H   | S11_No          |

> 🔹 Recieving Officer names are typed directly


### Data Validation

**A:A → List**

```excel
=Items_Master!$A:$A
```

### Auto Item Code

**B2**

```excel
=IF(A2="","",XLOOKUP(A2,Items_Master!A:A,Items_Master!B:B))
```

➡️ Copy down

---

## 📘 SHEET 4: `Dashboard` (Everything Happens Here)

This sheet **replaces Stock_Master completely**.

---

### 🔢 Stock Balance Table (Dynamic)

Create a table starting Row 5:

| Col | Header          | Formula (Row 6 example)                                 |
| --- | --------------- | ------------------------------------------------------- |
| A   | Item_Code       | `=Items_Master!A2`                                      |
| B   | Item_Name       | `=Items_Master!B2`                                      |
| C   | Opening_Stock   | `=Items_Master!D2`                                      |
| D   | Total_Received  | `=SUMIFS(Receiving_Inward!C:C,Receiving_Inward!A:A,A6)` |
| E   | Total_Issued    | `=SUMIFS(Issuing_Outward!C:C,Issuing_Outward!A:A,A6)`   |
| F   | Current_Balance | `=C6+D6-E6`                                             |

➡️ spill formula

---

### 📊 Dashboard KPIs (Top Section)

**Total Items**
```excel
=COUNTA(Items_Master!B:B)-1
```

```
=SUM(StockTable[Total_Received])
=SUM(StockTable[Total_Issued])
=SUM(StockTable[Current_Balance])
```
---

### 📈 PivotTables & Charts

Create PivotTables from:

✔ Receiving_Inward (Received by Item / Date)
✔ Issuing_Outward (Issued by Officer / Item)
✔ Add Category column to Dashboard table:
```
G = XLOOKUP(A6, Items_Master!A:B, Items_Master!C:C)
```
// Then use G column for PivotTables by category  

Suggested charts:

* Stock balance by category
* Monthly receipts vs issues
* Top issued items

---

## 🔒 Sheet Protection (NO VBA)

| Sheet            | Status                                    |
| ---------------- | ----------------------------------------- |
| Items_Master     | Protected (allow Opening_Stock edit only) |
| Receiving_Inward | Editable                                  |
| Issuing_Outward  | Editable                                  |
| Dashboard        | Fully Locked                              |

**To lock the Items_Master Sheet**

* Unlock column D (Opening_Stock) → Select D:D → Format Cells → Protection → Uncheck “Locked”
* Protect the sheet → Review → Protect Sheet → set a password if needed
* Users can now edit only Opening_Stock; all other columns are locked

---
---



