
### Method 1: The Official Chat Export (Easiest & Code-Free)

If you don't need real-time streaming and just want to pull historical blocks of data periodically:

1. **Export the Chat:** Open the WhatsApp group on your phone or desktop app, go to **Group Info** > **More** > **Export Chat** (choose *Without Media* to keep the file lightweight).
2. **Handle Duplicates on Import:** This generates a `.txt` file containing every message with structured timestamps (e.g., `[DD/MM/YYYY, HH:MM:SS] Sender Name: Message text`)

---
---
---

## Extracting Information into a given format
```
Extract key factual information from the provided text to fill the table below. 

### Instructions:
1. Output MUST be formatted as a table- like format using the exact column headers specified.
2. Extract ONLY factual data. Avoid full sentences, narrative context, or extraneous detail.
3. If a specific field/information is missing or not mentioned in the text, write "N/A" (or "Not Specified").


### Example Table Schema:
| S/NO | DATE AND PLACE | OFFENCE | DRUG & QUANTITY | ACCUSED | AGE | NATIONALITY | GENDER | METHOD OF CONCEALMENT | SOURCE AND ROUTE OR PLACE |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | **Date:** 20/08/2026 **Place:** Eldoret Airport (Seizure) / Mombasa (Arrest) | Trafficking of narcotic drugs and psychotropic substances | • **Methamphetamine:** 1,381.01 grams (marked D1–D13, soap-like solid blocks) • **Amphetamine:** 4,802.86 grams (marked F1–F14, maroon gel) | FIRDOS HUSSEIN ALI *(ID: 11460698, Mobile: 0721895647)* | 53 | Asian | Female | Concealed as soap-like solid blocks and maroon gel packages | **Source:** Dubai (Sent by Rahma Mirzah, +971525386607) **Route:** Dubai $\rightarrow$ Eldoret Airport $\rightarrow$ Dreamline Bus Service (Controlled Delivery) $\rightarrow$ Mombasa |

### Source Data:
[Paste your text/data here]

```
