# SUSPECTED FRAUD INVESTIGATION TEMPLATE (Beginner Edition)

> **Purpose:** Organize information, reconstruct events, identify relationships, test explanations, and separate facts from assumptions — adapted specifically for financial/occupational fraud.
>
> **Important:** Only use information you are legally authorized to access. Do not treat allegations or suspicions as established facts. Fraud allegations can end careers and trigger legal proceedings — precision matters more here than almost anywhere else in investigative work.

---

# STEP 0 — PREDICATION (NEW)

Before opening a fraud investigation, write down *why* you have reasonable grounds to look — not a hunch, a documented trigger.

**What triggered this investigation?**
> *Example: "Monthly reconciliation flagged a KSh 250,000 outgoing transfer with no matching purchase order or invoice."*

**Who raised it, and when?**
> ---

**Is there a legitimate business explanation you should rule out first?** (e.g., a legitimate but undocumented transaction, a system error, a duplicate payment being corrected)
> ---

**Beginner tip:** most "fraud" investigations end in an innocent explanation — a coding error, a delayed invoice, a misfiled approval. Predication keeps you honest about starting from a real trigger rather than suspicion of a specific person.

---

# STEP 1 — DEFINE THE CASE

### Case/Investigation Name
**Example:** Suspected Financial Fraud — Missing KSh 250,000

### Date
**Date analysis started:** __________________

### Analyst
**Name:** __________________

### Fraud Type Classification (NEW)

Fraud almost always falls into one of three categories — identify which one(s) you suspect, since it changes what evidence matters:

☐ **Asset misappropriation** — theft or misuse of funds/assets (most common; e.g., unauthorized transfers, fake vendors, expense fraud)
☐ **Corruption** — bribery, kickbacks, conflicts of interest, undisclosed relationships with vendors
☐ **Financial statement fraud** — manipulating records to misrepresent financial position (rarer, higher-impact)

> *Example: "Suspected asset misappropriation — unauthorized wire transfer using employee credentials."*

### Main Question

**Example:**
> What happened to the missing KSh 250,000, and who authorized or executed the transfer?

**My question:**
> ---

---

# STEP 2 — WRITE WHAT YOU ALREADY KNOW

Don't analyze yet. Just record what you currently have.

### Known Information
1. KSh 250,000 is missing.
2. The transaction occurred on 15 August.
3. The transaction was made from the company account.
4. Employee A's credentials were used.
5. Employee A denies making the transaction.

---

# STEP 3 — SEPARATE FACTS FROM CLAIMS

| Information | Fact / Claim | Source | Verified? |
| ------------ | ------------- | -------- | --------- |
| *Transfer of KSh 250,000 occurred on 15 Aug* | *Fact* | *Bank statement* | *Yes* |
| *Employee A's login was used* | *Fact* | *System access log* | *Yes* |
| *Employee A made the transfer* | *Claim (not yet fact)* | *Inference from login* | *No — login ≠ proven identity of user* |
| *Employee A denies involvement* | *Claim* | *Employee A's statement* | *N/A (statement, not evidence either way)* |

### Simple rule
**FACT** = supported by reliable evidence. **CLAIM** = someone says it happened. **ASSUMPTION** = you think it happened but don't have sufficient evidence.

**Fraud-specific trap to avoid:** "Employee A's credentials were used" is a fact. "Employee A did it" is an assumption until you've checked who else had access to that login, password-sharing practices, and whether the session originated from Employee A's usual device/location.

---

# STEP 4 — MAP AUTHORIZATION & CONTROLS (NEW — replaces generic timeline-only approach)

Before the general timeline, establish who was *supposed* to be able to do what. This is the backbone of most fraud cases.

### Segregation of duties check

| Function | Who normally performs it? | Who performed it in this case? | Match? |
| -------- | ---------------------------- | --------------------------------- | ------- |
| *Initiate payment* | *Employee A* | *Employee A* | *Yes* |
| *Approve payment* | *Manager B* | *No approval record found* | **No — gap** |
| *Reconcile account* | *Employee C* | *Employee C, 3 weeks later* | *Delayed* |

**Beginner tip:** the single most common fraud enabler is one person controlling two or more of: initiating, approving, and reconciling a transaction. A broken segregation-of-duties chain is often more telling than any single piece of "hard" evidence.

### Authorization trail
> *Example: "Payment system requires manager approval above KSh 100,000. No approval record exists for this transfer — either bypassed, or approval logs were not retained."*

---

# STEP 5 — BUILD THE TIMELINE

| Date | Time | What happened? | Source | Confidence |
| ---- | ---- | ---------------- | -------- | ------------ |
| 15 Aug | 10:07 | Computer login | System record | High |
| 15 Aug | 10:10 | Employee A enters office | CCTV | High |
| 15 Aug | 10:15 | KSh 250,000 transferred | Bank record | High |
| 15 Aug | 10:20 | Employee A leaves | CCTV | High |
| 15 Aug | 14:00 | No approval notification sent (expected) | System log | Medium |

### Ask yourself:
* What happened first, next, immediately before/after the transaction?
* Are there unexplained gaps — especially around *approval steps that should exist but don't*?

---

# STEP 6 — LIST EVERY IMPORTANT PERSON/ENTITY

| Person/Entity | Role | Relationship to Case | Evidence |
| --------------- | ------ | ----------------------- | ---------- |
| *Employee A* | *Initiator* | *Credentials used in transfer* | *Login log* |
| *Manager B* | *Approver* | *No approval on record* | *Absence of log entry* |
| *Recipient account* | *Beneficiary* | *Received funds* | *Bank record* |
| *Vendor "Zeta Supplies"* | *Possible shell entity* | *Listed as payment purpose, unverified* | *Registration records pending* |

### Fraud-specific entities to always check (NEW)
☐ Beneficiary bank accounts (whose name is on the account?)
☐ Vendors/suppliers named in the transaction (are they real, registered businesses?)
☐ Any shared addresses, phone numbers, or bank details between the vendor and an employee
☐ IP addresses / devices used to initiate digital transactions

---

# STEP 7 — CREATE A PROFILE FOR EACH IMPORTANT PERSON (with Fraud Triangle)

Copy this section for each person of interest.

## PERSON PROFILE

**Name:** ______________________________
**Known role:** _________________________
**Relationship to case:** _______________

### Fraud Triangle Analysis (NEW)

Fraud typically requires all three elements to be present. Assess each — absence of one is a meaningful contradiction, not just a gap.

**Pressure/incentive** — did this person have a personal motive (debt, financial strain, targets to hit)?
> *Example: "Recent large personal loan repayment due, per informal HR conversation — unverified, needs corroboration."*

**Opportunity** — did they have access and the ability to conceal it?
> *Example: "Had initiating credentials; approval step appears to have been bypassed or unlogged."*

**Rationalization** — is there any evidence of a justification narrative (e.g., "I was owed this," "I was going to pay it back")?
> ---

### What evidence connects this person to the case?
1. ---
### What evidence does NOT connect them?
1. ---
### What is still unknown?
1. ---

---

# STEP 8 — RECORD IMPORTANT COMMUNICATIONS

| Date | Person A | Person B | Type | What is established? | Source |
| ---- | -------- | -------- | ------------------------ | ----------------------- | -------- |
|      |          |          | Call / Message / Email |                         |        |

Don't write "They were planning the fraud." Write what's established: *"A messaged B at 14:35 asking about the approval workflow."* Then separately assess significance.

---

# STEP 9 — FOLLOW THE MONEY (expanded for fraud)

| Date | Sender | Recipient | Amount | Stated Purpose | Source |
| ---- | ------ | --------- | ------: | ----------------- | -------- |
| *15 Aug* | *Company account* | *"Zeta Supplies"* | *KSh 250,000* | *"Office equipment"* | *Bank record* |

### Fraud-specific money-trail questions (NEW)
1. Does the recipient account holder's name match the stated vendor/payee name?
2. Is the amount just under an approval threshold (a classic red flag — e.g., KSh 99,000 when approval kicks in at 100,000)?
3. Was the transaction made at an unusual time (after hours, weekends, right before a system cutover)?
4. Are there other similar transactions to the same recipient (a pattern, not a one-off)?
5. Was the transaction reversed, refunded, or followed by a second "correcting" transaction?
6. Is there a legitimate invoice, PO, and delivery record matching the stated purpose?

**Remember:** A financial connection by itself does not prove wrongdoing — but an *unexplained* one, especially with a broken authorization trail, is a strong lead.

---

# STEP 10 — TRANSACTION PATTERN / ANOMALY REVIEW (NEW)

If you have access to a broader set of transactions (not just the one in question), scan for patterns — fraud is rarely a single isolated act.

| Pattern to check | Present? | Notes |
| ------------------ | --------- | ------- |
| Multiple payments just under approval threshold | ☐ Yes ☐ No |  |
| Repeated payments to the same vendor/account | ☐ Yes ☐ No |  |
| Round-number amounts (e.g., exactly 250,000, no cents) | ☐ Yes ☐ No |  |
| Transactions clustered around month-end/quarter-end | ☐ Yes ☐ No |  |
| Vendor address matches an employee's address | ☐ Yes ☐ No |  |
| Missing or altered supporting documents | ☐ Yes ☐ No |  |

**Beginner tip:** you don't need statistical software to start — a simple sort of transactions by amount and by recipient in a spreadsheet often surfaces the pattern before any formal analysis.

---

# STEP 11 — RECORD DOCUMENTARY EVIDENCE

| Document | Date | What does it establish? | Source | Verified? |
| -------- | ---- | -------------------------- | -------- | --------- |
| *Purchase order* | *N/A* | *No PO exists for this transaction* | *Procurement system* | *Absence confirmed* |
| *Vendor registration* | *Pending* | *Whether "Zeta Supplies" is a real registered business* | *Business registry* | *Pending* |

---

# STEP 12 — RECORD DIGITAL/SYSTEM EVIDENCE (expanded)

| Evidence | Where obtained | What does it show? | What it does NOT show | Confidence |
| ---------- | ----------------- | --------------------- | ------------------------- | ------------ |
| *System login* | *IT access logs* | *Employee A's credentials used at 10:07* | *Who was physically at the keyboard* | *High for login, Low for identity* |
| *IP address* | *Network logs* | *Login originated from office network* | *Which device/desk specifically* | *Medium* |

### Important question
**What does this evidence NOT prove?**
> *Example: "System logs show Employee A's credentials were used. This does not prove Employee A personally entered them — shared passwords, unlocked workstations, or credential theft are alternative explanations that must be checked."*

---

# STEP 13 — CONNECT THE DOTS

### Connection 1
**Employee A → Recipient account**
Relationship: *Unclear — no documented business relationship found yet*
Evidence: *Payment sent from A's authenticated session*
Confidence: ☐ High ☐ Medium ☑ Low

### Connection 2
**Recipient account → "Zeta Supplies"**
Relationship: *Same account used*
Evidence: *Bank record*
Confidence: ☐ High ☑ Medium ☐ Low

---

# STEP 14 — DRAW A SIMPLE LINK MAP

```text
EMPLOYEE A ── (credentials used) ── TRANSACTION
                                        |
                                        | payment
                                        ↓
                                RECIPIENT ACCOUNT
                                        |
                                        | listed as
                                        ↓
                              "ZETA SUPPLIES" (unverified)
```

Only add a line when you have evidence supporting the connection.

---

# STEP 15 — QUANTIFY THE LOSS (NEW)

Fraud cases need a defensible number, not just "money is missing."

| Item | Amount | Basis for figure | Confirmed? |
| ------ | ------: | ------------------- | ----------- |
| *Unauthorized transfer, 15 Aug* | *KSh 250,000* | *Bank statement* | *Yes* |
| *Similar suspected transfers (if pattern found in §10)* | *TBD* | *Pending review* | *No* |
| **Total suspected loss** | *TBD* | | |

---

# STEP 16 — IDENTIFY THE MAIN EVENT

### What happened?
> ---
### When? / Where? / Who/what was involved?
> ---
### What evidence supports this?
> ---

---

# STEP 17 — DEVELOP POSSIBLE EXPLANATIONS

### Hypothesis 1
> *Example: "Employee A intentionally diverted funds to a shell vendor they control."*
Evidence supporting it: 1. --- 2. ---
Evidence contradicting it: 1. --- 2. ---

### Hypothesis 2
> *Example: "Employee A's credentials were compromised/shared, and someone else executed the transfer."*
Evidence supporting it: 1. --- 2. ---
Evidence contradicting it: 1. --- 2. ---

### Hypothesis 3
> *Example: "Legitimate transaction with missing/misfiled paperwork — administrative error, not fraud."*
Evidence supporting it: 1. --- 2. ---
Evidence contradicting it: 1. --- 2. ---

---

# STEP 18 — ASK "WHAT ELSE COULD EXPLAIN THIS?"

### Observation
> ---
### My initial interpretation
> ---
### Alternative explanation
> ---
### Evidence needed to distinguish between them
> ---

This step helps prevent confirmation bias — especially important in fraud cases, where one suspicious transaction can create tunnel vision toward one employee.

---

# STEP 19 — IDENTIFY CONTRADICTIONS

| Issue | Evidence A says | Evidence B says | What needs clarification? |
| ------ | ------------------ | ------------------ | ----------------------------- |
| *Approval* | *Policy requires manager sign-off >100,000* | *No approval record exists* | *Was it bypassed, or is the log missing?* |

Don't hide contradictions — they're often where further investigation is needed.

---

# STEP 20 — IDENTIFY INFORMATION GAPS

| Question | Why is it important? | Information needed |
| ---------- | ------------------------ | ---------------------- |
| *Is "Zeta Supplies" a registered, operating business?* | *Determines shell-company hypothesis* | *Business registry search, site visit* |
| *Who else had access to Employee A's login?* | *Tests identity assumption* | *Password policy, shared-device logs* |

---

# STEP 21 — RATE YOUR CONFIDENCE

**HIGH** — Multiple reliable, independent sources support the conclusion.
**MEDIUM** — Meaningful evidence exists, but important gaps remain.
**LOW** — Possible but based on limited or unverified information.

---

# STEP 22 — WRITE YOUR FINAL ASSESSMENT

## What we know
> ---
## What the evidence suggests
> ---
## Alternative explanation
> ---
## What we do NOT know
> ---
## Confidence
☐ High ☐ Medium ☐ Low
## Why?
> ---

---

# STEP 23 — RECOVERY & REFERRAL CHECKLIST (NEW)

Fraud investigations often need to hand off beyond your own analysis.

☐ Findings documented in a form suitable for HR / legal review
☐ Loss amount quantified and supportable (§15)
☐ Chain of custody maintained for all financial/digital records
☐ Determined whether referral is needed to: ☐ Internal audit ☐ External auditor ☐ Law enforcement ☐ Regulator
☐ Considered recovery options (insurance claim, civil recovery, bank reversal request)
☐ Confirmed whether the account/access used is still active (containment step — separate from the investigation itself)

**Note:** Containment (e.g., disabling a compromised login) is an operational decision, typically made by IT/security or management — not something the analysis itself should quietly assume has happened.

---

# STEP 24 — NEXT ACTIONS / QUESTIONS

1. ---
2. ---
3. ---
4. ---
5. ---

---

# QUICK ONE-PAGE FRAUD SUMMARY

**Case:** __________________________________
**Fraud type suspected:** ☐ Asset misappropriation ☐ Corruption ☐ Financial statement fraud
**Main question:** _________________________

### 1. WHAT HAPPENED?
---
### 2. WHEN? / WHERE?
---
### 3. WHO/WHAT IS INVOLVED?
* ---
### 4. AUTHORIZATION GAP (if any)
---
### 5. TIMELINE
| Time | Event | Source |
| ---- | ----- | ------ |
|      |       |        |
### 6. KEY EVIDENCE
* ---
### 7. LOSS AMOUNT (§15)
---
### 8. POSSIBLE EXPLANATIONS
**H1:** --- **H2:** --- **H3:** ---
### 9. CONTRADICTIONS
---
### 10. INFORMATION GAPS
---
### 11. ASSESSMENT
> ---
**Confidence:** ☐ High ☐ Medium ☐ Low
### 12. REFERRAL NEEDED?
☐ Internal audit ☐ Legal ☐ Law enforcement ☐ None yet
### 13. NEXT QUESTIONS
1. ---

---

# THE GOLDEN RULE (fraud-adapted)

**1. PREDICATE** — confirm there's a real trigger, not just suspicion
↓
**2. COLLECT**
↓
**3. ORGANIZE**
↓
**4. VERIFY**
↓
**5. MAP AUTHORIZATION & CONTROLS** — who could do what, and did the process break down?
↓
**6. BUILD THE TIMELINE**
↓
**7. IDENTIFY PEOPLE & ENTITIES** (apply the fraud triangle)
↓
**8. FOLLOW THE MONEY & CHECK FOR PATTERNS**
↓
**9. CONNECT THE EVIDENCE**
↓
**10. DEVELOP ALTERNATIVE EXPLANATIONS**
↓
**11. IDENTIFY GAPS & CONTRADICTIONS**
↓
**12. QUANTIFY THE LOSS**
↓
**13. ASSESS**
↓
**14. REPORT & REFER**

### Never reverse the process.

Don't start with "Employee A did it" and search for supporting evidence. Start with **"What do the authorization trail and the transaction records actually establish?"** — then let the evidence lead you, including toward an innocent explanation if that's where it goes.


# 
# 
#

**Mar ariyo** - Murder  
# MURDER INVESTIGATION ANALYSIS TEMPLATE (v2 — Beginner Edition)

> **Use:** For studying a homicide case, training, case analysis, or an investigation you are legally authorized to conduct.
>
> **Important:** Record what the evidence establishes separately from allegations, assumptions, and analytical judgments. Every worked example below is fictional and illustrative only.

---

# 0. LEGAL AUTHORIZATION CHECKLIST (NEW)

Complete this once, before you touch any record. Don't repeat it per-section — reference it.

☐ I am legally authorized to access the records used in this analysis
☐ Communications records were obtained lawfully (warrant, consent, or public record)
☐ Financial records were obtained lawfully
☐ CCTV/location data was obtained lawfully
☐ Digital device data was obtained lawfully (see Section 14a)
☐ Chain of custody is being logged for all physical/digital evidence (see Section 4a)

> *Example: "Phone records for Persons A and B obtained via subpoena, case #2026-0142, dated 3 March 2026."*

---

# 1. CASE INFORMATION

**Case name/number:** ______________________________
**Date of incident:** _______________________________
**Location:** _______________________________________
**Investigator/Analyst:** ____________________________
**Date analysis started:** ___________________________

### Main investigative question

> **Who was responsible for the death, what happened, and what evidence supports that conclusion?**

---

# 2. THE VICTIM

## Basic Information

**Name:** __________________________________________
**Age:** ___________________________________________
**Occupation:** ____________________________________
**Known residence:** _________________________________
**Last known location:** _____________________________

## Victimology

Don't start by asking "Who killed the victim?" Start by understanding the victim.

**Who were they?**
> *Example: "Maria, 34, worked as a bookkeeper at a mid-size logistics firm. Lived alone, recently separated."*

**Who did they regularly interact with?**
> ---

**What were their normal routines?**
> ---

**Who were their close associates?**
> ---

**Were there known disputes or conflicts?**
> *Example: "Dispute with former business partner over unpaid invoices, ongoing since January."*

**Had they received threats?**
> ---

**Were there recent changes in their life?**
> *Example: "Filed for divorce six weeks before the incident."*

**Were there important financial, family, business, legal or professional issues?**
> ---

---

# 3. LAST KNOWN MOVEMENTS

| Time | Location | Activity | Person(s) present | Source |
| ---- | -------- | -------- | ------------------ | ------ |
| *8:45 PM* | *Riverside Café* | *Dinner* | *Victim, unidentified man* | *Waitress statement* |
|      |          |          |                    |        |
|      |          |          |                    |        |

**Beginner tip:** fill this from the most reliable sources first (CCTV, receipts) — then use witness memory to fill gaps, not the other way around.

### Important questions
* Where was the victim last reliably seen?
* Who saw them?
* When?
* Where were they going?
* Who communicated with them?
* What happened immediately before the death?

---

# 4. CRIME SCENE — BASIC RECORD

**Location:** _______________________________________
**Date/time discovered:** ____________________________
**Who discovered the scene?** ________________________

### Initial observations
> *Example: "Front door unlocked. No signs of forced entry. Victim's phone missing from usual location."*

### Evidence documented
☐ Photographs ☐ CCTV/video ☐ Witnesses ☐ Documents
☐ Digital evidence ☐ Physical evidence ☐ Other: __________

**Do not interpret the evidence yet.** First record what was actually observed.

---

# 4a. CHAIN OF CUSTODY LOG (NEW)

For every item of physical or digital evidence collected.

| Item | Collected by | Date/time | Location found | Storage/transfer | Handled by (chronological) | Notes |
| ---- | ------------- | --------- | --------------- | ----------------- | --------------------------- | ----- |
| *Victim's phone* | *Officer Lin* | *3/3 9:40 PM* | *Bedroom nightstand* | *Sealed evidence bag #114* | *Lin → Evidence clerk Ortiz → Digital forensics (Chen)* | *Bag sealed and photographed before transfer* |
|      |               |           |                 |                    |                              |       |

**Why this matters:** if you can't show an unbroken chain, a defense attorney (or a skeptical reader) can argue the evidence was altered, contaminated, or misattributed. Gaps in custody don't necessarily mean tampering happened — but they weaken how much weight the evidence can carry.

---

# 5. MEDICAL / FORENSIC INFORMATION

Use only information officially available to you.

**Cause of death:** _________________________________
**Approximate time of death:** _______________________
**Relevant forensic findings:** _______________________
> ---

### Important distinction
**Cause of death** tells you what caused the death. **Time of death** is an estimate that helps establish the timeline. Neither automatically identifies the offender.

---

# 6. BUILD THE MASTER TIMELINE

| Date | Time | Event | Person(s) | Location | Evidence/source | Confidence | Flag for §7? |
| ---- | ---- | ----- | --------- | -------- | ---------------- | ---------- | ------------- |
| *3/3* | *10:15 PM* | *Neighbor hears raised voices* | *Unknown* | *Victim's apartment* | *Neighbor statement* | *Medium* | *Yes* |
|      |      |       |           |          |                   |            |               |

**Beginner tip:** add a "Flag for §7" column. Any row that falls inside your suspected time-of-death window gets flagged — this is what feeds Section 7 directly, instead of that section being a separate freeform guess.

### Timeline objective
Determine: **Before the incident → Incident → Immediately after → Discovery → Subsequent events**

---

# 7. THE CRITICAL TIME WINDOW

> Pull every row flagged "Yes" from Section 6 into this section. Don't introduce new claims here that aren't already in the master timeline.

**Start:** __________________
**End:** ___________________

### What happened during this period? (from flagged Section 6 rows)
> ---

### Who was known to be present?
1. ---
2. ---

### Who cannot yet be accounted for?
1. ---
2. ---

---

# 8. WITNESS TABLE

| Witness | What they say they observed | Time | Location | Witness type | Independent evidence? |
| ------- | ---------------------------- | ---- | -------- | ------------- | ----------------------- |
| *Mr. Okafor* | *Saw a gray sedan leave the driveway* | *~9:50 PM* | *Across the street* | *Direct eyewitness* | *Partially — CCTV shows a gray sedan on adjacent street at 9:52 PM* |
|         |                               |      |          |               |                         |

### Witness type key (NEW)
- **Direct eyewitness** — personally saw the event or a key moment
- **Circumstantial witness** — saw something adjacent (e.g., a car, a sound) but not the act itself
- **Character witness** — speaks to a person's habits/relationships, not the incident
- **Hearsay-adjacent** — repeating what someone else told them

**Beginner tip:** weight direct eyewitness accounts with independent corroboration highest; treat hearsay-adjacent accounts as leads to verify, not as evidence on their own.

### For each witness ask:
1. What did they personally observe?
2. What did someone else tell them?
3. When did they observe it?
4. Where were they?
5. Is there independent evidence supporting their account?
6. Are there contradictions?

---

# 9. PERSONS OF INTEREST

Don't automatically call someone a suspect simply because they knew the victim.

| Person | Relationship to victim | Possible relevance | Evidence | Contradicting evidence |
| ------ | ----------------------- | -------------------- | -------- | ------------------------ |
| *Former business partner* | *Co-owned company, dispute over invoices* | *Financial motive, recent conflict* | *Text messages showing hostility* | *Claims to have been out of town — unverified* |
|        |                         |                      |          |                          |

**Opportunity alone is not proof of involvement.**

---

# 9a. ALIBI VERIFICATION TABLE (NEW)

For each person of interest, record and test their stated alibi separately from your assessment of them.

| Person | Stated alibi | Claimed location/time | Who can confirm? | Evidence checked | Verified / Unverified / Contradicted |
| ------ | ------------- | ----------------------- | ------------------ | ------------------ | --------------------------------------- |
| *Former business partner* | *"Was at a conference in another city"* | *Hotel in City X, 3/3 all evening* | *Hotel staff, conference sign-in* | *Hotel check-in confirmed 6 PM; no confirmed sighting after 8 PM* | *Unverified (partial gap)* |
|        |               |                          |                     |                     |                                          |

**Beginner tip:** an alibi with a gap isn't a lie — it's an information gap. Note exactly which hours are covered and which aren't, rather than jumping to "the alibi is false."

---

# 10. MOTIVE ANALYSIS

### Person
**Name:** __________________________________
### Possible motive
> *Example: "Financial dispute escalating after victim threatened legal action over unpaid invoices."*
### Evidence supporting possible motive
1. ---
2. ---
### Evidence contradicting possible motive
1. ---
2. ---
### Confidence
☐ High ☐ Medium ☐ Low

---

# 11. THREAT HISTORY

| Date | Threat/source | Method | Content summary | Reported? | Evidence |
| ---- | -------------- | ------ | ------------------ | --------- | -------- |
| *2/10* | *Ex-partner* | *Text message* | *"You'll regret this"* | *No* | *Phone extraction, message #445* |
|      |               |        |                   |           |          |

### Ask:
* When did threats begin? Did they increase?
* Who was allegedly responsible?
* Were threats reported? Is there independent evidence?
* Did the threats contain information only certain people would know?

---

# 12. COMMUNICATION ANALYSIS

> *Legal authorization confirmed in §0.*

| Date | Time | Person A | Person B | Type | What is established? |
| ---- | ---- | -------- | -------- | ---------------------- | ---------------------- |
| *3/3* | *8:02 PM* | *Victim* | *Ex-partner* | *Phone call, 4 min* | *Contact occurred; content unknown — no recording* |
|      |      |          |          |                        |                         |

### Questions
**Who communicated with the victim before the death?**
> ---
**Who communicated with other relevant persons?**
> ---
**Were there unusual changes in communication?**
> ---
**What communications require further explanation?**
> ---

---

# 13. FINANCIAL ANALYSIS

> *Legal authorization confirmed in §0.*

| Date | Sender | Recipient | Amount | Stated purpose | Source |
| ---- | ------ | --------- | -----: | --------------- | ------ |
| *2/28* | *Ex-partner* | *Victim* | *$0 (disputed)* | *Alleged unpaid invoice* | *Bank statement request* |
|      |        |           |        |                 |        |

### Ask:
* Was there a financial dispute? Unusual transactions?
* Did money move between relevant parties?
* Was a transaction made shortly before or after a significant event?
* Is there a legitimate explanation?

**Remember:** A payment does not by itself establish criminal involvement.

---

# 14. CCTV / MOVEMENT ANALYSIS

> *Legal authorization confirmed in §0.*

| Time | Camera/location | Person/vehicle | Direction | Observation |
| ---- | ----------------- | ----------------- | --------- | ------------ |
| *9:52 PM* | *Corner of 5th & Elm* | *Gray sedan, partial plate* | *Northbound* | *Matches Mr. Okafor's description* |
|      |                    |                    |           |              |

### Objective
Build a movement timeline.
```text
LOCATION A → LOCATION B → LOCATION C → INCIDENT LOCATION → SUBSEQUENT LOCATION
```

---

# 14a. DIGITAL EVIDENCE ANALYSIS (NEW)

> *Legal authorization confirmed in §0. Digital evidence should also have a chain-of-custody entry in §4a.*

| Source | Data type | Date/time | Person | What it shows | What it does NOT show | Reliability |
| ------ | ---------- | --------- | ------- | ---------------- | ------------------------ | ------------ |
| *Victim's phone GPS* | *Location history* | *3/3, 8:00–9:40 PM* | *Victim* | *Phone was at Riverside Café then home* | *Doesn't confirm who else was present* | *High* |
| *Suspect's search history* | *Browser* | *2/20* | *Ex-partner* | *Searched "how to void a business contract"* | *Doesn't establish intent to harm* | *Medium* |
|        |            |           |         |                   |                            |               |

### Common digital sources to check
☐ Phone location/GPS history
☐ Social media activity (posts, DMs, "last active" timestamps)
☐ Search history
☐ App usage (ride-share, delivery, banking apps)
☐ Smart home / IoT device logs (door locks, cameras, voice assistants)
☐ Email metadata

**Beginner tip:** digital evidence tells you *where a device was and what it did* — not definitively *who* was holding it. Always ask: "who else had access to this device or account?"

---

# 15. VEHICLE / OBJECT ANALYSIS

| Object/Vehicle | Registration/identifier | Owner | Location | Evidence | Significance |
| ---------------- | -------------------------- | ----- | -------- | -------- | -------------- |
|                  |                            |       |          |          |               |

### Questions
* Who owns it? Who had access to it?
* Where was it before/during/after the incident?
* What evidence establishes this?

---

# 16. EVIDENCE MATRIX

| Evidence | What it establishes | What it does NOT establish | Person/entity connected | Chain of custody ref (§4a) | Source | Reliability |
| -------- | ---------------------- | ------------------------------ | -------------------------- | ----------------------------- | ------ | ------------ |
| *Text messages* | *Hostile relationship existed* | *Does not prove violence occurred* | *Ex-partner* | *N/A (records, not physical)* | *Phone extraction* | *High* |
|          |                        |                                |                            |                                |        |              |

### Golden question
> **What exactly does this evidence prove — and what does it NOT prove?**
> *(This is the single most important question in the whole template. Ask it for every row, out loud if it helps.)*

---

# 17. LINK ANALYSIS

```text
PERSON A
   | communication
   ↓
PERSON B
   | financial relationship
   ↓
ACCOUNT C
   | documented transaction
   ↓
EVENT
```

| Entity A | Entity B | Relationship | Evidence | Confidence |
| -------- | -------- | --------------- | -------- | ------------ |
|          |          |                |          |             |

---

# 18. RECONSTRUCT THE EVENT

### Before
> ---
### During
> ---
### Immediately afterward
> ---
### Later
> ---
### Evidence supporting the reconstruction
1. ---
2. ---
3. ---

---

# 19. DEVELOP COMPETING HYPOTHESES

## HYPOTHESIS 1
> ---
**Supporting evidence:** 1. --- 2. ---
**Contradicting evidence:** 1. --- 2. ---

## HYPOTHESIS 2
> ---
**Supporting evidence:** 1. --- 2. ---
**Contradicting evidence:** 1. --- 2. ---

## HYPOTHESIS 3
> ---
**Supporting evidence:** 1. --- 2. ---
**Contradicting evidence:** 1. --- 2. ---

---

# 20. CONTRADICTIONS

| Issue | Information A | Information B | What needs explanation? |
| ----- | -------------- | -------------- | -------------------------- |
| *Alibi gap* | *Ex-partner says at hotel all night* | *No confirmed sighting after 8 PM* | *Whereabouts 8–11 PM unverified* |
|       |                |                |                            |

**Do not ignore evidence simply because it conflicts with your preferred theory.**

---

# 21. INFORMATION GAPS

| Question | Why important? | Information needed |
| -------- | ------------------ | ---------------------- |
|          |                    |                        |

---

# 22. THE "ALTERNATIVE EXPLANATION" TEST

Use this for major conclusions **and** for testing alibis from §9a.

### Observation
> ---
### My interpretation
> ---
### Another possible explanation
> ---
### What evidence would distinguish them?
> ---

---

# 23. CONFIDENCE ASSESSMENT

**HIGH** — Multiple reliable, independent pieces of evidence support the conclusion.
**MEDIUM** — Meaningful evidence exists, but important gaps remain.
**LOW** — The conclusion is possible but based on limited or unverified information.

---

# 24. FINAL ANALYTICAL ASSESSMENT

## What happened? / What evidence establishes this? / Who appears relevant? / Why? / What contradicts this? / What remains unknown?
> (as in original)

## Most likely explanation
> ---
## Alternative explanation
> ---
### Overall confidence
☐ High ☐ Medium ☐ Low
### Reason for confidence level
> ---

---

# 25. INVESTIGATIVE / ANALYTICAL GAPS CHECKLIST

### Victim
☐ Background examined ☐ Recent conflicts identified ☐ Recent changes identified ☐ Threat history examined

### Timeline
☐ Last known movements established ☐ Critical time window identified (linked to §6 flags) ☐ Major events placed chronologically

### People
☐ Relevant persons identified ☐ Relationships examined ☐ Alibis logged and tested (§9a) ☐ Contradictions recorded

### Evidence
☐ Chain of custody logged (§4a) ☐ Documentary evidence examined ☐ Digital evidence examined (§14a) ☐ CCTV examined ☐ Witness accounts compared with type noted (§8) ☐ Financial records examined

### Analysis
☐ Multiple hypotheses considered ☐ Alternative explanations considered ☐ Evidence gaps identified ☐ Conclusions separated from allegations ☐ Confidence level assigned to each major claim

---

# 26. ONE-PAGE MURDER CASE SUMMARY

## CASE
**Victim:** __________________________
**Date:** ___________________________
**Location:** _______________________

### WHAT HAPPENED?
> ---

### TIMELINE
**Before:** _____________________ **Incident:** _____________________ **After:** _____________________

### KEY PEOPLE
1. --- 2. --- 3. ---

### POSSIBLE MOTIVE
> ---

### STRONGEST EVIDENCE (with confidence — NEW)
1. --- (Confidence: ☐High ☐Med ☐Low)
2. --- (Confidence: ☐High ☐Med ☐Low)
3. --- (Confidence: ☐High ☐Med ☐Low)

### IMPORTANT CONNECTIONS
> ---
### CONTRADICTIONS
> ---
### INFORMATION GAPS
> ---
### MOST LIKELY EXPLANATION
> ---
### ALTERNATIVE EXPLANATION
> ---

### OVERALL CONFIDENCE
☐ High ☐ Medium ☐ Low

### WHY?
> ---


