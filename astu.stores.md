
> **Act as a records management and inventory systems expert.**
> I am the Stores Officer in charge at the Anti-Stock Theft Unit (ASTU). I want to design an Excel-based stores management system to automate inventory records. The system will manage:
> * Operational equipment and tools
> * Food supplies issued to officers
> The system should improve accuracy, reduce losses, ensure accountability, and support audits and reporting.

> **Instructions:**
> * Begin by creating a **comprehensive outline and system blueprint** only.
> * Define recommended Excel sheets, data fields, stock movement processes, validation controls, user responsibilities, and reporting structures.
> * Consider real-world issues such as stock theft, discrepancies, expiry dates, and officer accountability.
> * Do not implement anything yet.  
> * After the outline is complete and approved, we will proceed step-by-step guided by the outline.

---
---

```
# **ASTU Excel-Based Stores Management System**

## **System Blueprint & Outline**

---

## 1. **System Objectives & Control Philosophy**

**Primary objectives**

* Ensure **accurate, real-time stock visibility**
* Reduce **losses, pilferage, and unauthorized issues**
* Strengthen **officer accountability**
* Support **internal and external audits**
* Track **expiry, condition, and lifecycle** of items
* Provide **clear reporting for command decisions**

**Control philosophy**

* Every stock movement must:

  * Be **authorized**
  * Be **traceable to a person**
  * Leave a **non-editable audit trail**
* Separation of duties:

  * Receiving ≠ Issuing ≠ Reviewing
* Excel used as a **controlled system**, not an open worksheet

---

## 2. **Overall System Structure (Workbook Architecture)**

One protected Excel workbook containing **interlinked sheets**, grouped into logical modules:

### Core Modules

1. **Master Data**
2. **Stock Records**
3. **Transactions (Movements)**
4. **Officer Accountability**
5. **Controls & Validation**
6. **Reports & Dashboards**
7. **Audit & Exception Logs**

---

## 3. **Recommended Excel Sheets & Purpose**

### 3.1 **Item Master Register**

*(Foundation of the system – controlled sheet)*

**Purpose**

* Holds permanent records of all approved items
* Prevents duplicate or inconsistent item naming

**Key Data Fields**

* Item Code (unique, system-generated)
* Item Name
* Category

  * Operational Equipment
  * Tools
  * Food Supplies
* Sub-Category (e.g., Firearms Accessories, Rations, Uniform Gear)
* Unit of Measure (pcs, kg, litres, cartons)
* Reorder Level
* Maximum Stock Level
* Shelf Life (for food)
* Controlled Item? (Yes/No)
* Condition Tracking Required? (Yes/No)
* Active / Obsolete Status

**Controls**

* Locked after approval
* Drop-down referenced by all transaction sheets

---

### 3.2 **Supplier / Source Register**

*(For traceability and audits)*

**Purpose**

* Records where stock originates

**Fields**

* Supplier / Source Code
* Supplier Name
* Type (Procurement, Donation, Transfer)
* Contact / Reference
* Contract / LPO / GRN Reference
* Approved Status

---

### 3.3 **Officer Register**

*(Accountability backbone)*

**Purpose**

* Links stock issues to specific officers

**Fields**

* Service Number
* Officer Name
* Rank
* Unit / Station
* Designation
* Signature on File (Yes/No)
* Active Status

---

### 3.4 **Stores Ledger (Auto-Calculated)**

*(System-calculated – no manual entry)*

**Purpose**

* Shows current stock balance per item

**Fields**

* Item Code
* Item Name
* Opening Balance
* Total Received
* Total Issued
* Adjustments
* Closing Balance
* Variance Flag (Auto)

**Controls**

* Formula-driven only
* Protected from editing

---

## 4. **Stock Movement Process Design**

### 4.1 **Goods Received Sheet (GRN)**

*(Receiving Control Point)*

**Purpose**

* Records all incoming stock

**Fields**

* GRN Number (unique)
* Date Received
* Item Code (drop-down)
* Quantity Received
* Batch / Lot Number (for food)
* Expiry Date (mandatory for food)
* Condition on Receipt
* Source / Supplier Code
* Received By (Stores Officer)
* Verified By (Supervisor)
* Remarks

**Controls**

* Expiry date validation for food
* Quantity must be > 0
* Dual sign-off enforced

---

### 4.2 **Stock Issue Voucher (SIV)**

*(Critical accountability sheet)*

**Purpose**

* Records all stock issued to officers or units

**Fields**

* Issue Voucher Number
* Date Issued
* Item Code
* Quantity Issued
* Officer Service Number
* Purpose of Issue
* Expected Return Date (for tools)
* Issued By
* Authorized By
* Receiver Signature (physical or scanned reference)

**Controls**

* Cannot issue more than available balance
* Mandatory officer selection
* Supervisor authorization required
* Controlled items flagged for extra approval

---

### 4.3 **Returns & Recoveries Register**

*(Often overlooked but critical)*

**Purpose**

* Tracks return of tools, equipment, unused rations

**Fields**

* Return Reference Number
* Original Issue Voucher
* Item Code
* Quantity Returned
* Condition on Return
* Returned By
* Accepted By
* Remarks

---

### 4.4 **Adjustments & Losses Register**

*(High-risk, high-control sheet)*

**Purpose**

* Records discrepancies, losses, damages, expiries

**Fields**

* Adjustment Reference
* Date
* Item Code
* Quantity Adjusted (+ / -)
* Reason Code

  * Damage
  * Expired
  * Lost
  * Theft
  * Count Correction
* Investigation Reference
* Approved By (Senior Officer)
* Remarks

**Controls**

* No adjustment without approval
* Mandatory justification
* Automatically flagged for audit review

---

## 5. **Food Supplies Specific Controls**

**Additional tracking**

* Batch/Lot numbers
* Expiry date monitoring
* First-Expiry-First-Out (FEFO) logic
* Daily/weekly ration issue summaries

**System warnings**

* Near-expiry alerts
* Overstock vs consumption trend
* Unusual ration issue volumes per officer/unit

---

## 6. **Validation & Data Integrity Controls**

### Built-in Excel Controls

* Data validation lists (no free typing)
* Conditional formatting for:

  * Negative stock
  * Expired items
  * Overstock / understock
* Sheet protection with passwords
* Locked formulas
* Sequential document numbering

### Behavioral Controls

* Mandatory remarks for exceptions
* Dual authorization on sensitive transactions
* Restricted editing rights by role

---

## 7. **User Roles & Responsibilities**

### Stores Officer

* Data entry for receipts, issues, returns
* Daily stock verification
* First-line discrepancy reporting

### Supervising Officer

* Authorization of issues and adjustments
* Weekly review of stock movement
* Sign-off on reports

### Command / Audit

* Read-only access
* Monthly and quarterly reviews
* Investigation triggers

---

## 8. **Reporting & Audit Structures**

### Standard Reports

* Current Stock Balance Report
* Daily / Weekly Issue Report
* Officer-wise Issue Summary
* Food Consumption Report
* Expiry & Near-Expiry Report
* Losses & Adjustments Report

### Audit Reports

* Transaction Log (who did what, when)
* Unauthorized attempt log
* Variance & discrepancy report
* High-risk item movement report

---

## 9. **Risk Areas & Mitigation Built into Design**

| Risk                     | Design Response                    |
| ------------------------ | ---------------------------------- |
| Stock theft              | Officer-linked issues, audit trail |
| Data manipulation        | Locked sheets, role separation     |
| Expired food issuance    | Expiry validation & alerts         |
| Ghost issues             | Mandatory officer register         |
| Unauthorized adjustments | Approval & justification required  |

---
```

---
---

```
# ASTU Excel-Based Stores Management System  
## Pilot Project – Implementation Guide (implementation.md)

---

## 1. Purpose of This Implementation

This document provides a **step-by-step implementation guide** for piloting the ASTU Excel-Based Stores Management System before full-scale rollout.

The pilot aims to:
- Validate system effectiveness
- Identify operational gaps
- Test controls, accountability, and reporting
- Build user confidence before upscaling

---

## 2. Pilot Scope Definition

### 2.1 Items Included in Pilot

#### A. Food Staff
1. Maize flour  
2. Rice  
3. Cooking oil  
4. Milk powder  
5. Sugar  
6. Dry maize  
7. Dry beans  
8. Tea leaves  
9. Table salt  
10. Compo 10 ration  
11. Green grams  

#### B. Horse Feed
1. Horse meal  
2. Horse cube  
3. Maize germ  
4. Wheat bran  
5. Molasses  
6. Mineral salt  
7. Hay (Rhodes grass)  

#### C. Water Treatment Chemicals
1. Aluminum sulphate  
2. Soda ash  
3. Chlorine  

#### D. Police Uniform
1. Combat  
2. Jungle boots  
3. Smoke jacket  
4. Angola shirt  
5. Camouflaged T-shirt  
6. Camouflaged P-cap  
7. Black beret  

---

## 3. Pilot Duration & Governance

### 3.1 Duration
- Recommended pilot period: **3 months**
- Review points:
  - End of Month 1
  - End of Month 2
  - Final evaluation at Month 3

### 3.2 Pilot Team
- Stores Officer (Primary user)
- Supervising Officer
- Command/Audit observer
- Excel System Administrator

---

## 4. Implementation Phases Overview

| Phase | Name | Output |
|-----|-----|------|
| Phase 1 | Preparation & Setup | Approved pilot plan |
| Phase 2 | Workbook Build | Fully structured Excel system |
| Phase 3 | Master Data Population | Clean, validated master registers |
| Phase 4 | Control Configuration | Locked, protected system |
| Phase 5 | Pilot Go-Live | Live transactions begin |
| Phase 6 | Monitoring & Review | Issues logged and resolved |
| Phase 7 | Pilot Evaluation | Go / Modify / Scale decision |

---

## 5. Phase 1 – Preparation & Setup

### 5.1 Approvals
- Obtain written approval to run pilot
- Define:
  - Pilot store/location
  - Authorized officers
  - Pilot items list (as above)

### 5.2 Assign Roles
- Nominate:
  - Stores Officer
  - Supervising Officer
  - Audit observer (read-only)

### 5.3 Baseline Physical Stock Count
- Conduct **joint physical stock count** for all pilot items
- Record:
  - Quantity
  - Condition
  - Expiry dates (food & chemicals)
- This becomes **Opening Balance**

---

## 6. Phase 2 – Excel Workbook Build

### 6.1 Create Core Sheets
Create the following sheets in one workbook:

1. Item Master Register  
2. Supplier / Source Register  
3. Officer Register  
4. Goods Received (GRN)  
5. Stock Issue Voucher (SIV)  
6. Returns & Recoveries  
7. Adjustments & Losses  
8. Stores Ledger (Auto)  
9. Audit & Exception Log  
10. Reports Dashboard  

### 6.2 Naming & Numbering Standards
- GRN No: `GRN-YYYY-###`
- Issue Voucher: `SIV-YYYY-###`
- Adjustment Ref: `ADJ-YYYY-###`

---

## 7. Phase 3 – Master Data Population

### 7.1 Item Master Register Setup

For each pilot item:
- Assign unique **Item Code**
- Category:
  - Food Supplies
  - Horse Feed
  - Chemicals
  - Uniforms
- Unit of Measure:
  - kg, litres, bags, packets, pairs, pieces
- Shelf life:
  - Mandatory for food & chemicals
- Controlled Item:
  - YES (uniforms, chemicals)
- Condition Tracking:
  - YES (uniforms, tools)

Lock sheet after validation.

---

### 7.2 Supplier / Source Register
Populate:
- Government procurement
- Donations
- Internal transfers

Mark only approved sources as **Active**.

---

### 7.3 Officer Register
- Enter service number, rank, unit
- Mark active officers only
- No free-text officer names allowed in transactions

---

## 8. Phase 4 – Controls & Protection Configuration

### 8.1 Data Validation
- Drop-downs for:
  - Item codes
  - Officer service numbers
  - Reason codes
- Prevent:
  - Negative quantities
  - Missing expiry dates
  - Unauthorized item entry

### 8.2 Sheet Protection
- Lock:
  - Item Master
  - Stores Ledger
- Password-protect formulas
- Restrict editing rights by role

### 8.3 Conditional Formatting
- Red flag:
  - Negative balances
  - Expired items
  - Near-expiry (<30 days)
- Amber flag:
  - Overstock
  - Unusual issue quantities

---

## 9. Phase 5 – Pilot Go-Live

### 9.1 Opening Balance Entry
- Enter opening balances via:
  - Special “Opening Balance” GRN
- Verified and approved jointly

### 9.2 Live Transaction Rules
- No manual ledger editing
- All movements must be:
  - GRN
  - SIV
  - Return
  - Adjustment
- No backdating without approval

---

## 10. Phase 6 – Monitoring & Review

### 10.1 Daily Controls
- Stores Officer:
  - Verify stock balances
  - Check expiry alerts
- Supervisor:
  - Review issues and adjustments

### 10.2 Weekly Reviews
- Generate:
  - Weekly issue report
  - Expiry report
  - Officer-wise issue summary
- Investigate anomalies immediately

### 10.3 Audit & Exception Logging
- Log:
  - Override attempts
  - Adjustment frequency
  - Authorization delays

---

## 11. Phase 7 – Pilot Evaluation

### 11.1 Evaluation Criteria

| Area | Measure |
|----|-------|
| Stock accuracy | Physical vs system variance |
| Accountability | Officer-linked issues |
| Food control | Expiry losses |
| Compliance | Authorization adherence |
| Usability | User feedback |

---

### 11.2 Pilot Outcome Decisions
- **Proceed to scale**
- **Modify system controls**
- **Extend pilot duration**

Document lessons learned.

---

## 12. Scaling Readiness Checklist

Before rollout:
- Controls tested
- Reports validated
- Users trained
- SOPs documented
- Audit satisfied

---

## 13. Key Success Factors

- Discipline over speed
- No shortcuts on controls
- Leadership support
- Immediate correction of deviations

---

## 14. Conclusion

This pilot is not just a system test—it is a **culture shift toward accountability, transparency, and audit readiness**.

Successful pilot = controlled scale-up.

```

---
---
Perfect — **single store** simplifies things nicely 👍
Now we can build a **tight, auditable Excel system** without multi-location complexity.

Below is **PHASE 2: WORKBOOK BUILD**, written as an **implementation playbook** you can follow step-by-step.
Do *not* populate data yet — this phase is **structure only**.

---

# ✅ PHASE 2 – EXCEL WORKBOOK BUILD (SINGLE STORE)

## 🎯 Phase 2 Objective

Create a **controlled, standardized Excel workbook** that:

* Forces correct data entry
* Prevents manual ledger manipulation
* Automatically generates stock balances
* Is audit-ready from day one

**Phase 2 is complete when:**

* All sheets exist
* Columns are finalized
* Formulas are in place
* No transactions are entered yet

---

## 1️⃣ Workbook Creation Standards

### File Naming

```
ASTU_Stores_Management_Pilot.xlsx
```

### File Location

* One controlled folder
* Restricted write access
* Daily backup enabled (manual or automatic)

---

## 2️⃣ Sheet Structure (Create in This Exact Order)

Create **one workbook** with **10 sheets**, named *exactly* as follows:

1. `Item_Master`
2. `Supplier_Register`
3. `Officer_Register`
4. `GRN_Receipts`
5. `SIV_Issues`
6. `Returns_Recoveries`
7. `Adjustments_Losses`
8. `Stores_Ledger`
9. `Audit_Exception_Log`
10. `Reports_Dashboard`

> ⚠️ Naming consistency matters for formulas later.

---

## 3️⃣ Sheet-by-Sheet Build Details

---

## 3.1 `Item_Master` (Foundation Sheet)

**Purpose:** Control *what* can exist in the system.

### Columns (in order)

| Column | Field Name        | Notes                                   |
| ------ | ----------------- | --------------------------------------- |
| A      | Item_Code         | Unique, no blanks                       |
| B      | Item_Name         | Standardized naming                     |
| C      | Category          | Food / Horse Feed / Chemicals / Uniform |
| D      | Unit_of_Measure   | kg, litres, bags, pairs, etc            |
| E      | Shelf_Life_Months | Mandatory for food & chemicals          |
| F      | Controlled_Item   | YES / NO                                |
| G      | Condition_Tracked | YES / NO                                |
| H      | Reorder_Level     | Optional (numeric)                      |
| I      | Max_Level         | Optional                                |
| J      | Status            | ACTIVE / INACTIVE                       |

### Rules

* No formulas here
* Data entry **only during Phase 3**
* Will be locked later

---

## 3.2 `Supplier_Register`

**Purpose:** Control stock sources.

### Columns

| Column | Field                                           |
| ------ | ----------------------------------------------- |
| A      | Supplier_Code                                   |
| B      | Supplier_Name                                   |
| C      | Source_Type (Procurement / Donation / Transfer) |
| D      | Status (ACTIVE / INACTIVE)                      |

---

## 3.3 `Officer_Register`

**Purpose:** Prevent anonymous issues.

### Columns

| Column | Field                      |
| ------ | -------------------------- |
| A      | Service_No                 |
| B      | Officer_Name               |
| C      | Rank                       |
| D      | Unit                       |
| E      | Status (ACTIVE / INACTIVE) |

---

## 3.4 `GRN_Receipts`

**Purpose:** Capture **all inflows**, including opening balance.

### Columns

| Column | Field             |
| ------ | ----------------- |
| A      | GRN_No            |
| B      | GRN_Date          |
| C      | Item_Code         |
| D      | Quantity_Received |
| E      | Unit              |
| F      | Expiry_Date       |
| G      | Supplier_Code     |
| H      | Received_By       |
| I      | Approved_By       |
| J      | Remarks           |

📌 Opening balances will be entered here later as:

```
GRN-OPEN-2026
```

---

## 3.5 `SIV_Issues`

**Purpose:** Capture **all outflows**.

### Columns

| Column | Field                |
| ------ | -------------------- |
| A      | SIV_No               |
| B      | Issue_Date           |
| C      | Item_Code            |
| D      | Quantity_Issued      |
| E      | Unit                 |
| F      | Issued_To_Service_No |
| G      | Authorized_By        |
| H      | Purpose              |
| I      | Remarks              |

---

## 3.6 `Returns_Recoveries`

**Purpose:** Handle stock coming back.

### Columns

| Column | Field             |
| ------ | ----------------- |
| A      | Return_Ref        |
| B      | Return_Date       |
| C      | Item_Code         |
| D      | Quantity_Returned |
| E      | Unit              |
| F      | Condition         |
| G      | Received_By       |
| H      | Remarks           |

---

## 3.7 `Adjustments_Losses`

**Purpose:** Capture exceptions (loss, damage, correction).

### Columns

| Column | Field                   |
| ------ | ----------------------- |
| A      | Adjustment_Ref          |
| B      | Adjustment_Date         |
| C      | Item_Code               |
| D      | Quantity_Adjusted (+/-) |
| E      | Reason_Code             |
| F      | Authorized_By           |
| G      | Remarks                 |

---

## 3.8 `Stores_Ledger` (AUTO – NO MANUAL ENTRY)

**Purpose:** System-generated running balance.

### Columns

| Column | Field            |
| ------ | ---------------- |
| A      | Transaction_Date |
| B      | Ref_No           |
| C      | Item_Code        |
| D      | Transaction_Type |
| E      | Qty_In           |
| F      | Qty_Out          |
| G      | Running_Balance  |

📌 This sheet will:

* Pull from GRN, SIV, Returns, Adjustments
* Be **formula-only**
* Be locked permanently

---

## 3.9 `Audit_Exception_Log`

**Purpose:** Transparency & audit trail.

### Columns

| Column | Field           |
| ------ | --------------- |
| A      | Log_Date        |
| B      | Ref_No          |
| C      | Issue_Type      |
| D      | Description     |
| E      | Logged_By       |
| F      | Resolution      |
| G      | Resolved_By     |
| H      | Resolution_Date |

---

## 3.10 `Reports_Dashboard`

**Purpose:** Management visibility.

Leave blank for now — structure only.
(Charts come in Phase 6.)

---

## 4️⃣ Numbering & Reference Standards (Set Now)

Use these formats **consistently**:

* GRN: `GRN-YYYY-###`
* SIV: `SIV-YYYY-###`
* Returns: `RET-YYYY-###`
* Adjustments: `ADJ-YYYY-###`

📌 No free-text reference numbers.

---

## 5️⃣ Phase 2 Validation Checklist

Before moving to Phase 3, confirm:

✅ All 10 sheets exist
✅ Sheet names match exactly
✅ Column order is correct
✅ No data entered yet
✅ Stores_Ledger is empty (no formulas yet is OK)

---

## 📦 Phase 2 Deliverable

**File:**
`ASTU_Stores_Management_Pilot.xlsx`

**Status:**

> *Structure complete – ready for master data population*

---

## 🚀 Next Phase Preview (Phase 3)

We will:

* Design **Item Codes**
* Populate Item Master
* Populate Officer & Supplier registers
* Validate and lock master data

---

👉 **Confirm once Phase 2 workbook structure is created**, and I’ll walk you through **Phase 3 with item codes designed specifically for your pilot list** (no generic examples).

---
---

Great — now we’re getting into the **brain of the system**.

Below is **PHASE 3: MASTER DATA POPULATION**, written so you can **populate once, lock forever**, and never argue with auditors later.

This phase is slow, deliberate, and exact. No transactions yet.

---

# ✅ PHASE 3 – MASTER DATA POPULATION

## 🎯 Phase 3 Objective

Create **clean, validated, controlled master data** that:

* Eliminates free-text entries
* Standardizes items, officers, and sources
* Becomes the **only allowed reference** for transactions

**Rule:**

> If it’s not in a master register, it does not exist in the system.

---

## 1️⃣ Item Coding Standard (DEFINE FIRST)

### Item Code Structure

```
[Category]-[Subgroup]-[###]
```

### Category Codes

| Category      | Code |
| ------------- | ---- |
| Food Supplies | FD   |
| Horse Feed    | HF   |
| Chemicals     | CH   |
| Uniform       | UN   |

### Subgroup Codes

| Subgroup        | Code |
| --------------- | ---- |
| Food Staff      | FS   |
| Horse Feed      | HF   |
| Water Chemicals | WC   |
| Police Uniform  | PU   |

📌 Example:

```
FD-FS-001
HF-HF-003
CH-WC-002
UN-PU-005
```

---

## 2️⃣ Populate `Item_Master` (MOST IMPORTANT SHEET)

Populate **only pilot items** — nothing else.

### A. Food Staff Items

| Item_Code | Item_Name       | Category      | Unit    | Shelf_Life | Controlled | Condition |
| --------- | --------------- | ------------- | ------- | ---------- | ---------- | --------- |
| FD-FS-001 | Maize flour     | Food Supplies | kg      | 6          | NO         | NO        |
| FD-FS-002 | Rice            | Food Supplies | kg      | 12         | NO         | NO        |
| FD-FS-003 | Cooking oil     | Food Supplies | litres  | 12         | NO         | NO        |
| FD-FS-004 | Milk powder     | Food Supplies | kg      | 12         | NO         | NO        |
| FD-FS-005 | Sugar           | Food Supplies | kg      | 24         | NO         | NO        |
| FD-FS-006 | Dry maize       | Food Supplies | kg      | 12         | NO         | NO        |
| FD-FS-007 | Dry beans       | Food Supplies | kg      | 12         | NO         | NO        |
| FD-FS-008 | Tea leaves      | Food Supplies | kg      | 18         | NO         | NO        |
| FD-FS-009 | Table salt      | Food Supplies | kg      | 36         | NO         | NO        |
| FD-FS-010 | Compo 10 ration | Food Supplies | packets | 6          | NO         | NO        |
| FD-FS-011 | Green grams     | Food Supplies | kg      | 12         | NO         | NO        |

---

### B. Horse Feed Items

| Item_Code | Item_Name          | Category   | Unit   | Shelf_Life | Controlled | Condition |
| --------- | ------------------ | ---------- | ------ | ---------- | ---------- | --------- |
| HF-HF-001 | Horse meal         | Horse Feed | kg     | 6          | NO         | NO        |
| HF-HF-002 | Horse cube         | Horse Feed | kg     | 6          | NO         | NO        |
| HF-HF-003 | Maize germ         | Horse Feed | kg     | 6          | NO         | NO        |
| HF-HF-004 | Wheat bran         | Horse Feed | kg     | 6          | NO         | NO        |
| HF-HF-005 | Molasses           | Horse Feed | litres | 12         | NO         | NO        |
| HF-HF-006 | Mineral salt       | Horse Feed | kg     | 24         | NO         | NO        |
| HF-HF-007 | Hay (Rhodes grass) | Horse Feed | bales  | 12         | NO         | NO        |

---

### C. Water Treatment Chemicals

| Item_Code | Item_Name         | Category  | Unit | Shelf_Life | Controlled | Condition |
| --------- | ----------------- | --------- | ---- | ---------- | ---------- | --------- |
| CH-WC-001 | Aluminum sulphate | Chemicals | kg   | 24         | YES        | NO        |
| CH-WC-002 | Soda ash          | Chemicals | kg   | 24         | YES        | NO        |
| CH-WC-003 | Chlorine          | Chemicals | kg   | 12         | YES        | NO        |

---

### D. Police Uniform Items

| Item_Code | Item_Name           | Category | Unit   | Shelf_Life | Controlled | Condition |
| --------- | ------------------- | -------- | ------ | ---------- | ---------- | --------- |
| UN-PU-001 | Combat              | Uniform  | pieces | –          | YES        | YES       |
| UN-PU-002 | Jungle boots        | Uniform  | pairs  | –          | YES        | YES       |
| UN-PU-003 | Smoke jacket        | Uniform  | pieces | –          | YES        | YES       |
| UN-PU-004 | Angola shirt        | Uniform  | pieces | –          | YES        | YES       |
| UN-PU-005 | Camouflaged T-shirt | Uniform  | pieces | –          | YES        | YES       |
| UN-PU-006 | Camouflaged P-cap   | Uniform  | pieces | –          | YES        | YES       |
| UN-PU-007 | Black beret         | Uniform  | pieces | –          | YES        | YES       |

📌 Use **blank or N/A** for Shelf_Life where not applicable.

---

## 3️⃣ Populate `Supplier_Register`

Use **controlled source codes**.

| Supplier_Code | Supplier_Name          | Source_Type | Status |
| ------------- | ---------------------- | ----------- | ------ |
| GOV-001       | Government Procurement | Procurement | ACTIVE |
| DON-001       | Donations              | Donation    | ACTIVE |
| INT-001       | Internal Transfer      | Transfer    | ACTIVE |

📌 No private suppliers unless officially approved.

---

## 4️⃣ Populate `Officer_Register`

### Rules

* Use **official service numbers**
* No nicknames
* Status = ACTIVE only for serving officers

| Service_No | Officer_Name | Rank | Unit       | Status |
| ---------- | ------------ | ---- | ---------- | ------ |
| 123456     | John Doe     | Sgt  | Stores     | ACTIVE |
| 234567     | Jane Smith   | Insp | Operations | ACTIVE |

(Replace with real data.)

---

## 5️⃣ Master Data Validation (MANDATORY)

Before locking:

### Checks

* No blank Item Codes
* No duplicate Item Codes
* Units are consistent (kg ≠ kgs ≠ KG)
* Shelf life present for all food & chemicals
* Controlled items correctly flagged
* Only ACTIVE officers visible

📌 Supervisor must review and sign off.

---

## 6️⃣ Lock Master Sheets (END OF PHASE 3)

After approval:

* Lock:

  * `Item_Master`
  * `Supplier_Register`
  * `Officer_Register`
* Allow edit **only to Excel Admin**
* Any change requires:

  * Written approval
  * Audit log entry

---

## 7️⃣ Phase 3 Completion Checklist

✅ All pilot items loaded
✅ Suppliers approved
✅ Officers validated
✅ Masters reviewed & signed
✅ Sheets locked

---

## 📦 Phase 3 Deliverable

**Workbook status:**

> *Master data complete and frozen*

---

## 🚦 Next Phase: PHASE 4 – CONTROLS & PROTECTION

Next we will:

* Build drop-down validations
* Create the automatic Stores Ledger
* Protect formulas
* Set conditional alerts (expiry, negative stock)

When you’re ready, say **“Proceed to Phase 4”** and we’ll lock this system down properly.

---
---

Excellent. This is the **make-or-break phase**.
After Phase 4, the system stops being “Excel” and starts behaving like a **controlled stores application**.

Below is **PHASE 4 – CONTROLS & PROTECTION CONFIGURATION**, written as a **build checklist with exact rules**. Follow in order.

---

# ✅ PHASE 4 – CONTROLS & PROTECTION CONFIGURATION

## 🎯 Phase 4 Objective

Configure Excel so that:

* Users **cannot bypass controls**
* Transactions only use approved master data
* Stock balances are **system-calculated**
* Exceptions are visible and traceable

**Rule:**

> If Excel allows it, someone will abuse it — so we prevent it now.

---

## 1️⃣ Create Named Ranges (Foundation for Controls)

Go to **Formulas → Name Manager** and define:

### From `Item_Master`

* `ItemCodes` → Item_Code (Status = ACTIVE)
* `ItemUnits` → Unit_of_Measure
* `ControlledItems` → Controlled_Item = YES

### From `Supplier_Register`

* `Suppliers` → Supplier_Code (Status = ACTIVE)

### From `Officer_Register`

* `ActiveOfficers` → Service_No (Status = ACTIVE)

📌 These will feed drop-downs everywhere.

---

## 2️⃣ Data Validation Rules (STOP BAD DATA)

### 2.1 GRN_Receipts

Apply validations:

| Column            | Rule                                 |
| ----------------- | ------------------------------------ |
| Item_Code         | List → `=ItemCodes`                  |
| Quantity_Received | Decimal > 0                          |
| Unit              | List → from Item_Master              |
| Expiry_Date       | Required if Item is Food or Chemical |
| Supplier_Code     | List → `=Suppliers`                  |
| Received_By       | List → `=ActiveOfficers`             |
| Approved_By       | List → `=ActiveOfficers`             |

❌ No blanks allowed except Remarks.

---

### 2.2 SIV_Issues

| Column               | Rule                     |
| -------------------- | ------------------------ |
| Item_Code            | List → `=ItemCodes`      |
| Quantity_Issued      | Decimal > 0              |
| Unit                 | List                     |
| Issued_To_Service_No | List → `=ActiveOfficers` |
| Authorized_By        | List → `=ActiveOfficers` |

📌 Later we’ll stop issuing more than balance.

---

### 2.3 Returns_Recoveries

| Column            | Rule                            |
| ----------------- | ------------------------------- |
| Item_Code         | List                            |
| Quantity_Returned | Decimal > 0                     |
| Condition         | List (Good / Damaged / Expired) |

---

### 2.4 Adjustments_Losses

| Column            | Rule                                             |
| ----------------- | ------------------------------------------------ |
| Item_Code         | List                                             |
| Quantity_Adjusted | Whole number (allow negative)                    |
| Reason_Code       | List (Loss / Damage / Expiry / Count Correction) |
| Authorized_By     | List → ActiveOfficers                            |

📌 Adjustments are **always signed**.

---

## 3️⃣ Build the Stores Ledger (SYSTEM CORE)

This sheet is **formula-only**.

### 3.1 Ledger Logic

Ledger pulls from 4 sources:

| Source      | Qty In | Qty Out |
| ----------- | ------ | ------- |
| GRN         | +      | –       |
| SIV         | –      | +       |
| Returns     | +      | –       |
| Adjustments | +/-    | +/-     |

---

### 3.2 Ledger Columns Formula Concept

| Column           | Logic                                 |
| ---------------- | ------------------------------------- |
| Transaction_Date | From source date                      |
| Ref_No           | GRN / SIV / RET / ADJ                 |
| Item_Code        | From source                           |
| Transaction_Type | Receipt / Issue / Return / Adjustment |
| Qty_In           | Positive inflows                      |
| Qty_Out          | Issues                                |
| Running_Balance  | Cumulative per Item_Code              |

📌 Use `SUMIFS()` for balance per item.

> **IMPORTANT:**
> Running_Balance =
> Total In (Item) − Total Out (Item)

No manual entries. Ever.

---

## 4️⃣ Prevent Negative Stock (HARD STOP)

In `SIV_Issues`:

* Create helper column:

  * Available_Balance (VLOOKUP / SUMIFS from Ledger)
* Data Validation:

  * Quantity_Issued ≤ Available_Balance
* Custom error message:

  > “Insufficient stock. Issue blocked.”

📌 This single rule kills 80% of abuse.

---

## 5️⃣ Conditional Formatting (VISUAL CONTROL)

### 5.1 Red Flags 🔴

* Negative balance → Red fill
* Expired items → Red text
* Adjustment quantity ≠ small threshold → Red border

### 5.2 Amber Flags 🟠

* Near expiry (<30 days)
* Issue quantity > historical average
* Stock above Max_Level

---

## 6️⃣ Sheet Protection & Role Control

### Lock Completely

* `Item_Master`
* `Supplier_Register`
* `Officer_Register`
* `Stores_Ledger`

### Partially Lock

* Transaction sheets (lock formulas, unlock input cells)

### Password Policy

* One password for:

  * Masters
  * Ledger
* Excel Admin only

📌 Passwords recorded in sealed envelope / command file.

---

## 7️⃣ Audit Trail Configuration

### Auto-log when:

* Adjustment entered
* Validation overridden (if allowed)
* Backdated entry attempted

Log into:
`Audit_Exception_Log`

---

## 8️⃣ Phase 4 Validation Checklist

Supervisor + Audit Observer must confirm:

✅ Drop-downs working
✅ Invalid entries blocked
✅ Ledger auto-updates
✅ Negative stock blocked
✅ Sheets protected
✅ Alerts visible

Only then proceed.

---

## 📦 Phase 4 Deliverable

**Workbook Status:**

> *Controlled, protected, audit-ready system*

At this point:

* Users can’t cheat easily
* Mistakes are visible
* Data integrity is enforced

---

## 🚦 Next Phase: PHASE 5 – PILOT GO-LIVE

Next we will:

* Enter certified opening balances
* Activate live GRN & SIV usage
* Issue operating rules to users

Say **“Proceed to Phase 5”** when Phase 4 controls are in place.

---
---

Alright — this is where the system **goes live**.
From this point on, **discipline matters more than Excel skills**.

Below is **PHASE 5 – PILOT GO-LIVE**, written as an **operational launch procedure** you can issue to the stores team.

---

# ✅ PHASE 5 – PILOT GO-LIVE

## 🎯 Phase 5 Objective

Move from setup to **controlled live operations**, ensuring:

* Opening balances are correctly captured
* All stock movement flows through the system
* Manual registers are retired (for pilot items)

**Golden Rule:**

> If it’s not in Excel, it did not happen.

---

## 1️⃣ System Activation Declaration

### Action

Issue a **Go-Live Notice** (internal memo).

### Content (minimum):

* Go-live date and time
* Pilot items list (unchanged)
* Excel workbook file name & location
* Statement:

  > “All receipts, issues, returns, and adjustments for pilot items shall be recorded exclusively in the Excel-Based Stores Management System.”

### Signatories:

* Supervising Officer
* Stores Officer

📌 **Output:**
✔ Go-Live Notice filed in Pilot Control File

---

## 2️⃣ Opening Balance Entry (One-Time Only)

### 2.1 Use GRN_Receipts ONLY

Enter **opening balances** as a **special GRN**:

| Field         | Value                             |
| ------------- | --------------------------------- |
| GRN_No        | `GRN-OPEN-2026` (or current year) |
| GRN_Date      | Go-Live Date                      |
| Supplier_Code | `INT-001`                         |
| Remarks       | “Opening balance – Pilot start”   |

For each item:

* Quantity = certified physical count
* Expiry date = actual expiry (food & chemicals)
* Unit = as per Item_Master

📌 **Rules**

* No editing after posting
* Joint verification before save

---

## 3️⃣ Opening Balance Verification

### Action

Generate **Stores Ledger** after entry.

Verify:

* Each item balance = physical count
* No negative balances
* Expiry alerts trigger correctly

### Certification Statement:

> “Opening balances entered and verified against certified physical stock.”

Signed by:

* Stores Officer
* Supervising Officer
* Audit Observer

📌 **Output:**
✔ Opening Balance Verification Certificate

---

## 4️⃣ Live Transaction Operating Rules

### 4.1 Receipts (GRN)

* All receipts recorded **same day**
* No receipt without:

  * Approved Supplier
  * Expiry date (where applicable)
  * Approval officer
* Partial deliveries = separate GRNs

---

### 4.2 Issues (SIV)

* No stock issued without:

  * Authorized officer
  * Valid purpose
  * System-approved quantity
* System blocks:

  * Over-issuing
  * Unauthorized officers

---

### 4.3 Returns & Recoveries

* Returned items must state:

  * Condition
  * Original SIV reference (in remarks)
* Damaged/expired returns ≠ usable stock

---

### 4.4 Adjustments & Losses (EXCEPTION ONLY)

Allowed only for:

* Verified loss
* Expiry write-off
* Count correction

Requirements:

* Reason code
* Supervising Officer approval
* Logged in Audit_Exception_Log

📌 High adjustment frequency = investigation trigger.

---

## 5️⃣ Manual Registers Policy (Critical Control)

For **pilot items only**:

* Manual bin cards → **Read-only reference**
* Excel → **Primary record**
* Discrepancies:

  * Excel prevails
  * Physical recount required

---

## 6️⃣ Daily & Weekly Operating Discipline

### Daily (Stores Officer)

* Check:

  * Stock balances
  * Expiry alerts
  * Blocked transactions

### Weekly (Supervisor)

* Review:

  * Issue patterns
  * Adjustments
  * Expiry losses
* Sign weekly review note

---

## 7️⃣ Phase 5 Stabilization Period

Duration: **First 2 weeks**

### Focus:

* Correct usage habits
* No shortcuts
* Immediate correction of errors

📌 Expect mistakes — do **not** relax controls.

---

## 8️⃣ Phase 5 Completion Checklist

✅ Opening balances entered
✅ Balances verified
✅ Go-Live Notice issued
✅ Live transactions running
✅ Manual override stopped

---

## 📦 Phase 5 Deliverables

1. Go-Live Notice
2. Opening Balance GRN
3. Opening Balance Verification Certificate
4. First week transaction records

---

## 🚦 Next Phase: PHASE 6 – MONITORING & REVIEW

Next we will:

* Set up weekly reports
* Track anomalies and trends
* Prepare audit-ready evidence
* Capture lessons in real time

When you’re ready, say **“Proceed to Phase 6”** and we’ll move into **control monitoring and reporting**.

---
---

Good — Phase 6 is where you **prove the system works**.
This is less about Excel and more about **discipline, evidence, and early correction**.

Below is **PHASE 6 – MONITORING & REVIEW**, written as an **operational control framework** you can actually run week after week.

---

# ✅ PHASE 6 – MONITORING & REVIEW

## 🎯 Phase 6 Objective

Continuously:

* Detect errors early
* Prevent loss and abuse
* Produce audit-ready evidence
* Build confidence for scale-up

**Rule:**

> Problems are not failures — unlogged problems are.

---

## 1️⃣ Daily Controls (Stores Officer)

### Every Working Day

#### 1. Stock Position Check

From `Stores_Ledger`:

* Confirm no negative balances
* Confirm all transactions posted correctly
* Confirm no unexplained balance swings

#### 2. Expiry Monitoring

* Review:

  * Expired items
  * Near-expiry items (<30 days)
* Flag items for:

  * Priority issue
  * Write-off planning

#### 3. Exception Awareness

* Check:

  * Blocked SIV attempts
  * Data validation errors
* Log repeated issues in `Audit_Exception_Log`

📌 **Evidence:**
Daily check mark or initials on a printed or saved checklist.

---

## 2️⃣ Weekly Supervisory Review (MANDATORY)

### Once per Week (Fixed Day)

Generate and review the following:

### A. Weekly Issue Report

From `SIV_Issues`:

* Item-wise issue totals
* Officer-wise issue summary

**Review Questions:**

* Any unusual quantities?
* Same officer receiving repeatedly?
* Issues without clear purpose?

---

### B. Stock Balance Report

From `Stores_Ledger`:

* Current balance per item
* Compare against:

  * Reorder level
  * Max level

Flag:

* Overstock
* Understock
* Zero balances on critical items

---

### C. Expiry Status Report

From GRN + Ledger:

* Expired stock
* Stock expiring within 30 / 60 days

---

### D. Adjustment Register Review

From `Adjustments_Losses`:

* Number of adjustments
* Reasons used
* Authorizing officers

📌 **Rule:**
More than **2 adjustments per item per month** = investigation.

---

### Supervisory Output

* Short **Weekly Review Note**
* Signed by Supervisor
* Filed in Pilot Control File

---

## 3️⃣ Monthly Internal Audit Snapshot

### Conducted by Audit Observer

#### Focus Areas:

* Physical vs system balance (sample items)
* Adjustments justification
* Authorization compliance
* Expiry handling

### Method:

* Select:

  * 5–10 high-risk items
  * At least 1 controlled item
* Perform spot physical count
* Compare to Stores_Ledger

📌 **Output:**
✔ Monthly Audit Snapshot Report
✔ Variances logged (if any)

---

## 4️⃣ Audit & Exception Log Usage (LIVE DOCUMENT)

### What MUST be Logged

* Adjustment entries
* Blocked negative-stock attempts
* Backdating attempts
* Late approvals
* Repeated data entry errors

### What MUST NOT happen

❌ Silent corrections
❌ Deleting rows
❌ “Fixing later” without log

---

## 5️⃣ KPI Tracking (Simple but Powerful)

Track monthly:

| KPI                 | Target           |
| ------------------- | ---------------- |
| Stock accuracy      | ≥ 98%            |
| Unauthorized issues | 0                |
| Expiry loss         | ↓ month-on-month |
| Adjustments         | ≤ threshold      |
| User errors         | ↓ trend          |

📌 These KPIs feed Phase 7 decision.

---

## 6️⃣ User Feedback Capture

At end of:

* Month 1
* Month 2

Ask:

* What is slow?
* What is confusing?
* What is being bypassed?

Log feedback — do **not** change controls mid-pilot unless critical.

---

## 7️⃣ Corrective Action Protocol

### If issue found:

1. Log it
2. Correct transaction via proper method
3. Document cause
4. Train / warn user
5. Monitor recurrence

📌 No punishment culture — but no cover-ups.

---

## 8️⃣ Phase 6 Completion Checklist

By end of Month 3:

✅ Daily checks documented
✅ Weekly reviews signed
✅ Monthly audit snapshots done
✅ Exceptions logged and resolved
✅ KPIs calculated

---

## 📦 Phase 6 Deliverables

1. Weekly Review Notes
2. Monthly Audit Snapshot Reports
3. Audit & Exception Log
4. KPI Summary

---

## 🚦 Next & Final Phase: PHASE 7 – PILOT EVALUATION

Phase 7 will:

* Compare system vs physical reality
* Decide scale / modify / extend
* Produce the **final pilot report**

When ready, say **“Proceed to Phase 7”** and we’ll close this properly — like an audit-grade project, not a demo.

---
---

This is the **closure phase** — where you turn 3 months of discipline into a **clear decision** leadership can defend.

Below is **PHASE 7 – PILOT EVALUATION**, structured as a **formal assessment + decision framework**.
When this is done properly, scaling becomes easy.

---

# ✅ PHASE 7 – PILOT EVALUATION

## 🎯 Phase 7 Objective

Determine—using **evidence, not opinions**—whether the Excel-Based Stores Management System should be:

* **Scaled**
* **Modified**
* **Extended**

**Rule:**

> If it’s not documented, it didn’t happen.

---

## 1️⃣ Pilot Evaluation Team

The evaluation must be conducted jointly by:

* Supervising Officer (Chair)
* Stores Officer
* Audit Observer
* Excel System Administrator
* Command / Management Representative

📌 No single-person evaluation.

---

## 2️⃣ Evaluation Period & Data Cut-Off

* Evaluation Date: End of Month 3
* Data Cut-Off Date: Last pilot transaction date
* No transactions after cut-off until evaluation completed

---

## 3️⃣ Physical Stock Verification (Final Test)

### Action

Conduct a **full physical stock count** for all pilot items.

### Method

* Same process as Phase 1 baseline
* Same officers present (where possible)
* Use Stores Ledger balances as system reference

### Variance Calculation

For each item:

```
Variance = Physical Count − System Balance
Variance % = (Variance / System Balance) × 100
```

---

## 4️⃣ Evaluation Criteria & Scoring

Use the following **evidence-based scorecard**:

### 4.1 Stock Accuracy (30%)

| Measure               | Target |
| --------------------- | ------ |
| Variance rate         | ≤ ±2%  |
| Unexplained variances | 0      |

Score based on % of items within tolerance.

---

### 4.2 Accountability & Control (20%)

Check:

* All issues linked to officers
* No anonymous transactions
* Adjustment reasons documented
* Authorizations present

Score:

* 100% compliance = full score

---

### 4.3 Food & Chemical Control (15%)

Measure:

* Expiry losses vs opening stock
* Timeliness of expiry alerts
* Proper write-offs

Target:

* Expiry loss reduced vs historical baseline

---

### 4.4 Compliance with Procedures (20%)

Check:

* No manual ledger use for pilot items
* No backdating without approval
* Proper use of GRN / SIV / Adjustments

---

### 4.5 Usability & Adoption (15%)

Based on:

* User feedback
* Error frequency
* Training sufficiency

---

## 5️⃣ Audit Findings Summary

Audit Observer presents:

* Number of audit exceptions
* Nature of exceptions
* Resolution status
* Risk rating:

  * Low / Medium / High

📌 Unresolved high-risk issues block scaling.

---

## 6️⃣ Lessons Learned Register

Document:

* What worked well
* What failed
* What was bypassed
* What must change before scale

Structure:

| Area | Issue | Root Cause | Recommendation |
| ---- | ----- | ---------- | -------------- |

---

## 7️⃣ Pilot Outcome Decision Matrix

### Decision Rules

| Outcome              | Conditions                                         |
| -------------------- | -------------------------------------------------- |
| **Proceed to Scale** | Accuracy ≥ 98%, controls effective, low audit risk |
| **Modify & Scale**   | System sound, control gaps identified              |
| **Extend Pilot**     | Usage issues, insufficient data                    |
| **Abort**            | Fundamental control failure                        |

Decision must be:

* Written
* Signed
* Referenced

---

## 8️⃣ Final Pilot Report (MANDATORY)

### Report Sections

1. Executive Summary
2. Pilot Scope & Duration
3. System Description
4. Performance Against Criteria
5. Audit Findings
6. Lessons Learned
7. Decision & Next Steps

📌 This becomes the **authority document** for rollout.

---

## 9️⃣ Scaling Readiness Confirmation

Before scale-up, confirm:

✅ Controls tested
✅ Reports validated
✅ SOPs documented
✅ Users trained
✅ Audit satisfied

If any ❌ → fix before scale.

---

## 10️⃣ Phase 7 Closure Checklist

✅ Final physical count completed
✅ Variances analyzed
✅ Evaluation scorecard completed
✅ Decision documented
✅ Final report signed

---

## 📦 Phase 7 Deliverables

1. Final Physical Stock Count Sheets
2. Evaluation Scorecard
3. Audit Findings Summary
4. Lessons Learned Register
5. Final Pilot Report
6. Scale / Modify / Extend Decision Memo

---

## 🏁 FINAL NOTE

This pilot was never about Excel.

It was about:

* **Control**
* **Accountability**
* **Audit readiness**
* **Changing behavior**

If Phase 7 is done honestly, **leadership will trust the system**.

---

If you want, next I can help you:

* Convert this into **SOPs**
* Design the **scale-up (multi-store) architecture**
* Create **training materials** for officers
* Harden this into a **semi-automated Excel app**

Just tell me where you want to go next.





