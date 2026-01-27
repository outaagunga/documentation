
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

---
```

---


