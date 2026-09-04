
# SportPesa Tax-Adjusted Betting Framework

## 1. The four numbers you need

For every selection, record:

| Item                      | Meaning                                        |
| ------------------------- | ---------------------------------------------- |
| **Odds**                  | SportPesa's price for the selection            |
| **Deposit**               | Amount you put into your account               |
| **Estimated probability** | Your estimate of the chance the selection wins |
| **Tax rates**             | 5% deposit excise + 20% withholding tax        |

---

# 2. Calculate your actual betting stake

Assuming the **5% deposit excise tax reduces the amount available to bet**:

### Formula

**Actual Stake = Deposit × 0.95**

Example:

**KSh 1,000 × 0.95 = KSh 950**

So your KSh 1,000 deposit gives you:

**Actual stake = KSh 950**

---

# 3. Calculate the gross payout

### Formula

**Gross Payout = Stake × Odds**

Example:

Odds = **1.80**

**KSh 950 × 1.80 = KSh 1,710**

---

# 4. Calculate the gross profit

### Formula

**Gross Profit = Gross Payout − Stake**

Therefore:

**KSh 1,710 − KSh 950 = KSh 760**

---

# 5. Apply the 20% withholding tax

Under the simplified assumption that WHT is charged on the betting profit:

### Formula

**WHT = Gross Profit × 20%**

Example:

**KSh 760 × 20% = KSh 152**

---

# 6. Calculate your net profit

### Formula

**Net Profit = Gross Profit − WHT**

**KSh 760 − KSh 152 = KSh 608**

Therefore:

### Final amount after winning

**Stake + Net Profit = KSh 950 + KSh 608 = KSh 1,558**

So, under this model:

> **KSh 1,000 deposit → KSh 950 stake → KSh 1,558 final amount if 1.80 odds win.**

---

# 7. The shortcut formula

You can combine everything into one formula:

### **Final Amount = Deposit × 0.95 × [1 + 0.80 × (Odds − 1)]**

But for everyday use, I'd recommend sticking with the five-step method because it is easier to check for mistakes.

---

# 8. The MOST important part: Break-even probability

The key question isn't:

> **"Which bet has the highest probability?"**

Instead ask:

> **"How likely does this bet need to win for the odds to be profitable after tax?"**

### Tax-adjusted break-even formula

**Break-even Probability =**

**1 ÷ [0.95 × (1 + 0.80 × (Odds − 1))]**

For example, at **1.80 odds**:

**Break-even ≈ 67.5%**

That means your estimated probability needs to be **above 67.5%** for the bet to have positive expected value under this model.

---

# 9. Calculate the value gap

Now compare **your estimated probability** against the **tax-adjusted break-even probability**.

### Formula

**Value Gap = Estimated Probability − Break-even Probability**

### Example A

**Double Chance @ 1.40**

Your estimated probability = **80%**

Break-even = **75.7%**

Therefore:

**80% − 75.7% = +4.3 percentage points**

That's potentially valuable.

### Example B

**Over 1.5 Goals @ 1.20**

Estimated probability = **90%**

Break-even = **87.7%**

Therefore:

**90% − 87.7% = +2.3 percentage points**

Although Bet B has the **higher probability of winning**, Bet A has the **larger estimated edge**.

---

# 10. Your SportPesa selection workflow

When you have a list of matches, don't immediately ask:

> "Which bet is most likely to win?"

Instead, work through this:

### Step 1 — Identify the market

For example:

* Double Chance
* Over 1.5 Goals
* Under 4.5 Goals
* BTTS
* Draw No Bet

### Step 2 — Record the odds

Example:

**Double Chance — 1.40**

### Step 3 — Convert odds into the tax-adjusted break-even probability

Example:

**1.40 → 75.7% break-even**

### Step 4 — Estimate the true probability

Based on your analysis:

**Estimated probability = 80%**

### Step 5 — Calculate the value gap

**80% − 75.7% = +4.3 percentage points**

### Step 6 — Rank the selections

The best candidates are generally those with the **largest positive value gap**, provided your probability estimate is reasonably reliable.

---

# 11. Your analysis table

This is the format I'd use for every SportPesa selection:

| Market        | Odds | Implied Probability* | Tax-Adjusted Break-Even | Your Estimated Probability | Value Gap | Decision |
| ------------- | ---: | -------------------: | ----------------------: | -------------------------: | --------: | -------- |
| Double Chance | 1.40 |                71.4% |                   75.7% |                        80% | **+4.3%** | 🟢 Value |
| Over 1.5      | 1.20 |                83.3% |                   87.7% |                        90% | **+2.3%** | 🟢 Value |
| BTTS          | 1.70 |                58.8% |                       — |                          — |         — | Evaluate |
| DNB           | 1.55 |                64.5% |                       — |                          — |         — | Evaluate |

*The ordinary implied probability is **1 ÷ odds**. It is useful for understanding the bookmaker's price, but **the tax-adjusted break-even probability is the number you should use for your final profitability test**.

---

# 12. Initial markets to screen

Based on your objective of finding **high-probability bets that still offer value after tax**, your initial screening list can be:

### 🥇 1. Double Chance

Look for strong teams where **1X or X2** gives you a high estimated probability without odds becoming excessively low.

### 🥈 2. Over 1.5 Goals

Potentially useful when the matchup has a strong likelihood of producing at least two goals.

### 🥉 3. Under 4.5 Goals

Can provide relatively high probabilities in matches where five or more goals are unlikely.

### 4. BTTS

Useful when both teams have strong scoring tendencies, but requires more careful probability estimation.

### 5. Draw No Bet

Useful when you strongly favor one team but want protection against a draw.

---

# 13. The final decision rule

You can reduce the entire system to this:

> **Odds → Break-even probability → Estimate true probability → Compare → Calculate value gap**

And:

### 🟢 Positive value

**Estimated probability > tax-adjusted break-even**

### 🔴 Negative value

**Estimated probability < tax-adjusted break-even**

### ⭐ Stronger candidate

**Large positive value gap + high estimated probability + strong supporting evidence**

The important distinction is that **high probability alone does not make a bet good**. A 90% bet at extremely low odds can be worse than an 80% bet at better odds if the latter has a larger tax-adjusted edge.

**One caution:** the tax treatment in your original framework is an assumption. Before using this for real-money decisions, the actual current SportPesa/Kenya tax mechanics should be verified, because whether the 5% and 20% charges are applied exactly this way materially changes the break-even calculation.
