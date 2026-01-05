
- give bulleted summary of this [e.g transcript]:  
- give a simplified version that actually works for beginners  
- give a step-by-step checklist  
- Give your output in this format
- Spot hidden assumptions/ Structural logic conflict/ Logical ambiguity/Blind spot/ Explore multiple perspectives/ Asking the right question?
- Explain this in clear, concise bulleted points without adding new assumptions    
- I need help with [your task]. Before you start, ask me 3 questions to make sure you understand exactly what I need.
- Which part of the code is ambiguous  
- Translate this ruleset into pinescript friendly logic  
- Pine Script doesn't allow multi-line function calls. Everything needs to be on one line  
---
---
```vb

I want you to act as a **professional momentum trader** specializing in Bollinger Bands and the “Walking the Bands” methodology. I will provide you with price data and chart patterns. Your task is to **help me implement this in pinescript**.

**Key Guidelines (must be strictly followed):**

### **1️⃣ Core Principle: Overstretch ≠ Reversal**

* When price **overstretches the outer Bollinger Bands**, this indicates **market imbalance**, **not overbought/oversold conditions**.
* **Default bias:** **continuation**, not reversal.
* Only consider a reversal (fade) if **clear exhaustion signals** are present.
* Ask yourself: *Is this overstretch being ACCEPTED by the market, or REJECTED?*

---

### **2️⃣ Trade Type #1 – Pullback Continuation (Highest Probability)**

**When to trade:**

* After overstretch candles, wait for **price to pull back inside the outer band**.
* Price must hold **above Upper-1 band in uptrend** or **below Lower-1 band in downtrend**.
* Bands must remain **expanded and angled**.

**Entry:**

* Enter on **first bullish candle after pullback** (uptrend).
* Enter on **first bearish candle after pullback** (downtrend).

**Stop:**

* Below pullback low (uptrend)
* Above pullback high (downtrend)

**Target:**

* Band expansion continuation.
* Trail stops using middle band or EMA.

> This is the **core “Walking the Bands” trade**.

---

### **3️⃣ Trade Type #2 – Break-and-Go (Acceptance Trade)**

**When to trade:**

* One or more **large full-body candles**.
* Candle **opens inside → closes outside** outer band.
* **Next candle holds outside the band**.

**Entry:**

* Enter on **small consolidation** or **shallow pullback** that **does not reach the mid-band**.

**Stop:**

* Inside the band (if acceptance fails).

**Target:**

* Measured move or trailing band.

> Do **not** fade against this trend until clear failure appears.

---

### **4️⃣ Trade Type #3 – Exhaustion Fade (Only After Clear Signals)**

**Do NOT fade just because price is outside the band.**
**Fade only if at least 3 of these signals appear:**

1. Climax candle (huge range)
2. Upper/lower band flattening
3. Wicks rejecting further expansion
4. Momentum divergence
5. Close back **inside both outer AND inner bands**
6. Volume spike followed by compression

**Entry:**

* On **close back inside the band**, preferably after a failed continuation attempt.

**Stop:**

* Above/below exhaustion high/low.

**Target:**

* Mean (basis) or opposite inner band.

---

### **5️⃣ Market Context Insights**

* Upper bollinger1/ upper bollinger2 and lower bollinger1/ lower bollinger2 **green/red shaded gaps** show volatility expansion.
* Sharp sell candles breaking structure indicate **distribution → acceptance → continuation**, not mean reversion.
* Correct trades: **trade pullbacks along trend**, not against overstretched bands.

---

### **6️⃣ Simple Memorized Rule Set**

| Condition                            | Best Action        |
| ------------------------------------ | ------------------ |
| Bands expanding + angled             | Trade continuation |
| Pullback holds inner band            | Enter trend        |
| Closes outside band + follow-through | Break-and-go       |
| Band flattens + rejection            | Prepare fade       |
| Price snaps back inside + fails      | Mean reversion     |
```
---
---
```vb
## AI PROMPT — Rubber Band Mean Reversion Trading Strategy

**Role:**
Act as a professional quantitative trader and strategy designer. Your task is to identify and trade **mean-reversion (“Rubber Band”) setups** in the market using precise, rule-based logic.

---

### STRATEGY OBJECTIVE

Trade **mean reversion** by identifying when price becomes statistically **overextended** away from its mean and then enters only after confirmation that price is snapping back toward equilibrium.

---

### INDICATORS & DEFINITIONS

**Mean (Equilibrium):**

* 20-period Simple Moving Average (SMA)

**Overextension Boundaries:**

* Bollinger Bands (20-period, 2 standard deviations)

**Momentum & Exhaustion Filters:**

* RSI (14)
* Stochastic Oscillator (14, 3, 3)

---

### STEP 1 — IDENTIFY A STRETCHED MARKET

A market is considered **“stretched”** when **at least two** of the following conditions are true:

1. **Distance from Mean:**
   Price is visually and statistically far from the 20-period SMA.

2. **Bollinger Band Breach:**
   A candle **closes outside** the upper or lower Bollinger Band.

3. **RSI Extreme:**

   * RSI > 70 → Overbought (short bias)
   * RSI < 30 → Oversold (long bias)

4. **Stochastic Exhaustion:**

   * Stochastic > 80 (short bias) or < 20 (long bias)
   * Oscillator begins to curve back toward the mid-range.

5. **EMA Slope:**

   * Long Entry: Current EMA > EMA (1 bar ago)
   * Short Entry: Current EMA < EMA (1 bar ago)
---

### STEP 2 — ENTRY TRIGGER (THE SNAP)

Do **not** enter immediately on overextension.

Enter a trade **only after confirmation** that price is reverting:

1. **Band Pierce:**
   Price first moves outside the Bollinger Band.

2. **Re-Entry Signal (Required):**
   A candle **closes back inside** the Bollinger Band.

3. **Reversal Confirmation (Required):**
   Presence of a clear reversal candlestick pattern at the extreme, such as:

   * Pin Bar / Hammer
   * Engulfing candle
   * Strong rejection wick

**Trade Direction:**

* Overbought stretch → Short
* Oversold stretch → Long

---

### STEP 3 — RISK MANAGEMENT

**Stop Loss:**

* Long trades: Below the lowest wick of the stretched move
* Short trades: Above the highest wick of the stretched move

Stops must invalidate the idea of mean reversion if hit.

---

### STEP 4 — PROFIT TARGETS

**Primary Target (Mandatory Partial Take-Profit):**

* Take 50–70% profit at the **20-period SMA** (the mean).

**Secondary Target (Optional Runner):**

* Hold remaining position toward the **opposite Bollinger Band**, only if momentum supports continuation.

---

### STEP 5 — TRADE FILTER (WHEN NOT TO TRADE)

Avoid all Rubber Band trades when:

* Market is in a **strong trend**
* Price is **walking the Bollinger Bands**
* Price respects one band repeatedly without reverting to the mean

In such conditions, mean-reversion logic is invalid.

---

### OUTPUT REQUIREMENTS (WHEN APPLICABLE)

When generating signals, code, or analysis:

* Clearly label **Stretch Detection**
* Clearly label **Entry Trigger**
* Clearly label **Stop Loss**
* Clearly label **Targets**
* User can toggle **on/off each filter**
* User can adjust numerical values without editing the code
* Reject trades that violate the trend filter
* Single-line function calls **(Pine Script requirement)**
* Properly compiled code with NO logical/ Mathematical ambiguity or blind spot
```
---
---
```vb
//@version=5
strategy("Rubber Band Mean Reversion - STRICT", overlay=true, initial_capital=10000, default_qty_type=strategy.percent_of_equity, default_qty_value=10)

// ==========================================
// USER INPUTS & TOGGLES
// ==========================================
grp1 = "Step 1: Overextension (Stretch)"
smaLen = input.int(20, "20-period SMA (The Mean)", group=grp1)
bbMult = input.float(2.0, "BB StdDev Boundary", group=grp1)
rsiLen = input.int(14, "RSI Period", group=grp1)
rsiOB  = input.int(70, "RSI Overbought", group=grp1)
rsiOS  = input.int(30, "RSI Oversold", group=grp1)
stocLen= input.int(14, "Stoch Period", group=grp1)

grp2 = "Step 1.5: EMA Slope Filter"
useSlope = input.bool(true, "Enable EMA Slope Filter", group=grp2)
emaSlopeLen = input.int(9, "EMA Slope Period", group=grp2)

grp3 = "Step 5: Trend Filter (Avoid Walking Bands)"
useTrendFilt = input.bool(true, "ADX Trend Filter", group=grp3)
adxLimit     = input.int(25, "Max ADX for Reversion", group=grp3)

// ==========================================
// INDICATOR CALCULATIONS
// ==========================================
// Mean and Bands
meanSMA = ta.sma(close, smaLen)
[topBB, midBB, lowBB] = ta.bb(close, smaLen, bbMult)

// Oscillators
rsiVal = ta.rsi(close, rsiLen)
stochRaw = ta.stoch(close, high, low, stocLen)
stochK = ta.sma(stochRaw, 3)
stochD = ta.sma(stochK, 3)

// EMA Slope
slopeEMA = ta.ema(close, emaSlopeLen)
emaRising  = slopeEMA > slopeEMA[1]
emaFalling = slopeEMA < slopeEMA[1]

// ADX for Trend Filter
[plusDI, minusDI, adxVal] = ta.dmi(14, 14)

// ==========================================
// STEP 1: STRETCH DETECTION (At least 2 required)
// ==========================================
// 1. Distance from Mean (Price > BB which is 2SD away)
distUpper = close > topBB
distLower = close < lowBB

// 2. Bollinger Band Breach (Close outside)
breachUpper = close > topBB
breachLower = close < lowBB

// 3. RSI Extreme
rsiOB_cond = rsiVal > rsiOB
rsiOS_cond = rsiVal < rsiOS

// 4. Stochastic Exhaustion
stochOB_cond = stochK > 80
stochOS_cond = stochK < 20

// Tallying the Stretch
stretchCountLong  = (distLower ? 1 : 0) + (breachLower ? 1 : 0) + (rsiOS_cond ? 1 : 0) + (stochOS_cond ? 1 : 0)
stretchCountShort = (distUpper ? 1 : 0) + (breachUpper ? 1 : 0) + (rsiOB_cond ? 1 : 0) + (stochOB_cond ? 1 : 0)

isStretchedLong  = stretchCountLong >= 2
isStretchedShort = stretchCountShort >= 2

// ==========================================
// STEP 2: ENTRY TRIGGER (THE SNAP)
// ==========================================
// 1. Check for recent stretch (within last 5 bars)
wasStretchedL = ta.barssince(isStretchedLong) <= 5
wasStretchedS = ta.barssince(isStretchedShort) <= 5

// 2. Re-Entry Signal (Close back inside)
reEntryL = close > lowBB and close[1] <= lowBB[1]
reEntryS = close < topBB and close[1] >= topBB[1]

// 3. Reversal Confirmation (Pin Bar or Engulfing)
isPinBarL = (close > open) and (lowBB - low) > (high - low) * 0.6
isEngulfL = (close > open) and (close > high[1])
revConfL  = isPinBarL or isEngulfL

isPinBarS = (close < open) and (high - topBB) > (high - low) * 0.6
isEngulfS = (close < open) and (close < low[1])
revConfS  = isPinBarS or isEngulfS

// Final Combined Logic
trendOK = not useTrendFilt or adxVal < adxLimit
slopeOK_L = not useSlope or emaRising
slopeOK_S = not useSlope or emaFalling

longEntry  = wasStretchedL and reEntryL and revConfL and trendOK and slopeOK_L
shortEntry = wasStretchedS and reEntryS and revConfS and trendOK and slopeOK_S

// ==========================================
// STEP 3 & 4: RISK & TARGETS
// ==========================================
// Capture the extreme of the stretch for Stop Loss
var float longSL = na
var float shortSL = na

if (longEntry and strategy.position_size == 0)
    longSL := ta.lowest(low, 5)
    strategy.entry("Long", strategy.long)

if (shortEntry and strategy.position_size == 0)
    shortSL := ta.highest(high, 5)
    strategy.entry("Short", strategy.short)

// Exits
if (strategy.position_size > 0)
    strategy.exit("TP1_L", "Long", qty_percent=60, limit=meanSMA, stop=longSL, comment="MeanTP")
    strategy.exit("TP2_L", "Long", qty_percent=100, limit=topBB, stop=longSL, comment="OppBandTP")

if (strategy.position_size < 0)
    strategy.exit("TP1_S", "Short", qty_percent=60, limit=meanSMA, stop=shortSL, comment="MeanTP")
    strategy.exit("TP2_S", "Short", qty_percent=100, limit=lowBB, stop=shortSL, comment="OppBandTP")

// ==========================================
// VISUALS
// ==========================================
plot(meanSMA, "Mean", color=color.blue, linewidth=2)
p1 = plot(topBB, "Upper Band", color=color.red)
p2 = plot(lowBB, "Lower Band", color=color.green)
fill(p1, p2, color=color.new(color.gray, 90))

plotshape(longEntry, "Long Snap", shape.triangleup, location.belowbar, color.green, size=size.small)
plotshape(shortEntry, "Short Snap", shape.triangledown, location.abovebar, color.red, size=size.small)
```
---
---
## 🥉 **Best Structure-Based Trend Filter**
### **Market Structure (HH / HL / LL / LH)**

**Why pros love it**
* Works even when EMAs lag
* Excellent for scalping reversals inside trends

**Rules**
* Uptrend = Higher Highs + Higher Lows
* Downtrend = Lower Highs + Lower Lows
* Only fade bands **in the direction of structure**

📌 Best when paired with **session highs/lows**

## 🔥 **Best Professional Combo (Highly Recommended)**
If you want **high win-rate scalping**, use this:

> **200 EMA (direction)**
> **50 EMA (alignment)**
> **Bollinger Bands (entry)**
> **RSI(7) or Stoch (timing)**
```vb
//@version=6
strategy("Mean-Reversion Scalper (Trend Filtered)", overlay=true, initial_capital=10000, default_qty_type=strategy.percent_of_equity, default_qty_value=10)

// --- INPUTS ---
grp_trend = "Trend Filter"
use_trend  = input.bool(true, "Enable 200 EMA Trend Filter", group=grp_trend)
ema_len    = input.int(200, "Trend EMA Length", group=grp_trend)

grp_bb     = "Bollinger Bands"
bb_len     = input.int(20, "BB Length", group=grp_bb)
bb_mult    = input.float(2.0, "BB StdDev", group=grp_bb)

grp_filt   = "Volatility & Filters"
use_vol    = input.bool(true, "Filter: BBW > BBW Average", group=grp_filt)
bbw_avg_l  = input.int(50, "BBW MA Length", group=grp_filt)
max_slope  = input.float(2.0, "Max MA Slope (%)", group=grp_filt)

grp_mom    = "Momentum"
mom_src    = input.string("RSI", "Confirmation Source", options=["RSI", "Stochastic"], group=grp_mom)
rsi_len    = input.int(7, "RSI Length", group=grp_mom)
stoch_k    = input.int(5, "Stoch K", group=grp_mom)
stoch_d    = input.int(3, "Stoch D", group=grp_mom)

// --- INDICATOR CALCULATIONS ---
// Trend EMA
ema_200 = ta.ema(close, ema_len)

// Bollinger Bands
[basis, upper, lower] = ta.bb(close, bb_len, bb_mult)
bbw = (upper - lower) / basis
bbw_avg = ta.sma(bbw, bbw_avg_l)

// Momentum (RSI or Stoch)
rsi_val = ta.rsi(close, rsi_len)
stoch_k_val = ta.stoch(close, high, low, stoch_k)
stoch_d_val = ta.sma(stoch_k_val, stoch_d)

// Slope Filter (Price change over 3 bars to avoid steep trends)
ma_slope = math.abs(basis - basis[3]) / basis[3] * 100

// --- CONDITION GATES ---
trend_bull = not use_trend or (close > ema_200)
trend_bear = not use_trend or (close < ema_200)
vol_ok     = not use_vol or (bbw > bbw_avg)
slope_ok   = ma_slope < max_slope

// --- CANDLESTICK REJECTION ---
bull_rev = (low < lower and close > lower) and (close > open)
bear_rev = (high > upper and close < upper) and (close < open)

// --- TRADING LOGIC ---
long_cond = trend_bull and vol_ok and slope_ok and bull_rev and (mom_src == "RSI" ? (rsi_val > 30 and rsi_val[1] <= 30) : ta.crossover(stoch_d_val, 20))
short_cond = trend_bear and vol_ok and slope_ok and bear_rev and (mom_src == "RSI" ? (rsi_val < 70 and rsi_val[1] >= 70) : ta.crossunder(stoch_d_val, 80))

// --- EXECUTION ---
if long_cond
    strategy.entry("Long", strategy.long, comment="Trend Long")

if short_cond
    strategy.entry("Short", strategy.short, comment="Trend Short")

// Exit at Middle Band or Swing High/Low
if strategy.position_size > 0
    strategy.exit("LX", "Long", limit=basis, stop=ta.lowest(low, 3)[1])

if strategy.position_size < 0
    strategy.exit("SX", "Short", limit=basis, stop=ta.highest(high, 3)[1])

// --- VISUALS ---
plot(ema_200, color=color.new(color.orange, 0), linewidth=2, title="200 EMA Trend")
plot(basis, color=color.gray, title="20 MA")
p_up = plot(upper, color=color.red, title="Upper Band")
p_lo = plot(lower, color=color.green, title="Lower Band")
fill(p_up, p_lo, color=color.new(color.blue, 96))

plotshape(long_cond, style=shape.triangleup, location=location.belowbar, color=color.green, size=size.small)
plotshape(short_cond, style=shape.triangledown, location=location.abovebar, color=color.red, size=size.small)
```
---
---
```vb
//@version=5
strategy("Early Entry: BB Squeeze + Momentum Expansion", overlay=true, initial_capital=1000)

// --- INPUTS ---
bbLength = input.int(20, "BB Length")
bbMult   = input.float(2.0, "BB StdDev")
sqzLookback = input.int(20, "Squeeze Lookback Period")
emaFilterLen = input.int(200, "Trend Filter (EMA)")

// --- 4HR HIGHS/LOWS CALCULATION ---
// "D" is daily, "240" is 4 hours. 
// gaps=barmerge.gaps_off ensures the line stays continuous.
high4hr = request.security(syminfo.tickerid, "240", high, lookahead=barmerge.lookahead_off)
low4hr  = request.security(syminfo.tickerid, "240", low, lookahead=barmerge.lookahead_off)

// --- CALCULATIONS ---
[bbMid, bbUpper, bbLower] = ta.bb(close, bbLength, bbMult)
ema200 = ta.ema(close, emaFilterLen)

// Bollinger Band Width (Volatility Measure)
bbWidth = (bbUpper - bbLower) / bbMid
isSqueezing = bbWidth <= ta.lowest(bbWidth, sqzLookback) // True if bands are at their tightest
volatilityExpanding = bbWidth > bbWidth[1]

// --- ENTRY LOGIC ---
// We check if we WERE squeezing recently and are NOW expanding
longCondition = isSqueezing[1] and volatilityExpanding and close > ema200
shortCondition = isSqueezing[1] and volatilityExpanding and close < ema200

// --- EXECUTION ---
if (longCondition)
    strategy.entry("Sqz Breakout Long", strategy.long)

if (shortCondition)
    strategy.entry("Sqz Breakout Short", strategy.short)

// --- EXIT LOGIC (Trailing or Target) ---
if (ta.cross(close, bbMid))
    strategy.close_all()

// --- VISUALS ---
plot(ema200, color=color.yellow, title="200 EMA Trend Filter")
fill(plot(bbUpper, color=color.new(color.gray, 0)), plot(bbLower, color=color.new(color.gray, 0)), color = isSqueezing ? color.new(color.blue, 80) : color.new(color.gray, 90))
plotshape(longCondition, style=shape.triangleup, location=location.belowbar, color=color.green, size=size.small)
plotshape(shortCondition, style=shape.triangledown, location=location.abovebar, color=color.red, size=size.small)

// --- PLOT 4HR LEVELS ---
plot(high4hr, color=color.new(color.red, 0), style=plot.style_stepline, linewidth=0.5, title="4hr High")
plot(low4hr, color=color.new(color.green, 0), style=plot.style_stepline, linewidth=0.5, title="4hr Low")
```
---
---
```vb
//@version=5
strategy("Early Entry: BB Squeeze + Momentum Expansion", overlay=true, initial_capital=1000)

// --- INPUTS ---
bbLength = input.int(20, "BB Length")
bbMult   = input.float(2.0, "BB StdDev")
sqzLookback = input.int(20, "Squeeze Lookback Period")
emaFilterLen = input.int(200, "Trend Filter (EMA)")

// --- 4HR HIGHS/LOWS CALCULATION ---
// "D" is daily, "240" is 4 hours. 
// gaps=barmerge.gaps_off ensures the line stays continuous.
high4hr = request.security(syminfo.tickerid, "240", high, lookahead=barmerge.lookahead_on)
low4hr  = request.security(syminfo.tickerid, "240", low, lookahead=barmerge.lookahead_on)

// --- CALCULATIONS ---
[bbMid, bbUpper, bbLower] = ta.bb(close, bbLength, bbMult)
ema200 = ta.ema(close, emaFilterLen)

// Bollinger Band Width (Volatility Measure)
bbWidth = (bbUpper - bbLower) / bbMid
isSqueezing = bbWidth <= ta.lowest(bbWidth, sqzLookback) // True if bands are at their tightest

// --- ENTRY LOGIC (Catching the move start) ---
longCondition = isSqueezing[1] and ta.crossover(close, bbUpper) and close > ema200
shortCondition = isSqueezing[1] and ta.crossunder(close, bbLower) and close < ema200

// --- EXECUTION ---
if (longCondition)
    strategy.entry("Sqz Breakout Long", strategy.long)

if (shortCondition)
    strategy.entry("Sqz Breakout Short", strategy.short)

// --- EXIT LOGIC (Trailing or Target) ---
if (ta.cross(close, bbMid))
    strategy.close_all()

// --- VISUALS ---
plot(ema200, color=color.yellow, title="200 EMA Trend Filter")
fill(plot(bbUpper, color=color.new(color.gray, 0)), plot(bbLower, color=color.new(color.gray, 0)), color = isSqueezing ? color.new(color.blue, 80) : color.new(color.gray, 90))
plotshape(longCondition, style=shape.triangleup, location=location.belowbar, color=color.green, size=size.small)
plotshape(shortCondition, style=shape.triangledown, location=location.abovebar, color=color.red, size=size.small)

// --- PLOT 4HR LEVELS ---
plot(high4hr, color=color.new(color.green, 0), style=plot.style_stepline, linewidth=2, title="4hr High")
plot(low4hr, color=color.new(color.red, 0), style=plot.style_stepline, linewidth=2, title="4hr Low")
```
---
---
To catch the market before the "meat" of the move happens, we need to enter immidiately before squeeze explodes
we use **Bollinger Band Squeeze** detector

### **The "Early Entry" Logic**

* **The Squeeze:** We calculate the "Width" of the Bollinger Bands. When the width is at a 20-period low, the background turns blue. This is your "get ready" signal.
* **The Expansion:** The trade triggers the moment price breaks the band while the width starts to expand, ensuring you are there for the very first "big candle."

### **Expert Tip for "Zero-Lag" Entry**
If you want to be even faster, look at the **1-minute chart** when the **15-minute chart** is in a squeeze.
1. Wait for the 15m Squeeze to turn blue.
2. Drop down to the 1m chart.
3. Enter the moment the 1m chart shows a volume spike. This often gets you into the trade 5–10 minutes before the 15m candle even closes.
```vb
//@version=5
strategy("Early Entry: BB Squeeze + Momentum Expansion", overlay=true, initial_capital=1000)

// --- INPUTS ---
bbLength = input.int(20, "BB Length")
bbMult   = input.float(2.0, "BB StdDev")
sqzLookback = input.int(20, "Squeeze Lookback Period")
emaFilterLen = input.int(200, "Trend Filter (EMA)")

// --- CALCULATIONS ---
[bbMid, bbUpper, bbLower] = ta.bb(close, bbLength, bbMult)
ema200 = ta.ema(close, emaFilterLen)

// Bollinger Band Width (Volatility Measure)
bbWidth = (bbUpper - bbLower) / bbMid
isSqueezing = bbWidth <= ta.lowest(bbWidth, sqzLookback) // True if bands are at their tightest

// --- ENTRY LOGIC (Catching the move start) ---
// Long: We are in a squeeze + price closes ABOVE upper band + price is above 200 EMA
longCondition = isSqueezing[1] and ta.crossover(close, bbUpper) and close > ema200

// Short: We are in a squeeze + price closes BELOW lower band + price is below 200 EMA
shortCondition = isSqueezing[1] and ta.crossunder(close, bbLower) and close < ema200

// --- EXECUTION ---
if (longCondition)
    strategy.entry("Sqz Breakout Long", strategy.long)

if (shortCondition)
    strategy.entry("Sqz Breakout Short", strategy.short)

// --- EXIT LOGIC (Trailing or Target) ---
// Exit when price touches the Mid line (Mean Reversion) or a Trailing Stop
if (ta.cross(close, bbMid))
    strategy.close_all()

// --- VISUALS ---
plot(ema200, color=color.yellow, title="200 EMA Trend Filter")
fill(plot(bbUpper), plot(bbLower), color = isSqueezing ? color.new(color.blue, 80) : color.new(color.gray, 90))
plotshape(longCondition, style=shape.triangleup, location=location.belowbar, color=color.green, size=size.small)
plotshape(shortCondition, style=shape.triangledown, location=location.abovebar, color=color.red, size=size.small)
```
---
---
Top down analys. The lines are the 4 hour highs and lows. They are you point of interest when you move to lower timeframe e.g 15min  
```vb
//@version=5
indicator("Top-Down Analysis Dashboard", overlay=true)

// --- INPUTS ---
htf_trend = input.timeframe("D", "Higher Timeframe (Trend)")
mtf_structure = input.timeframe("240", "Medium Timeframe (Structure)")
ema_length = input.int(20, "Trend EMA Length")

// --- CALCULATIONS ---
// Get HTF Trend (Daily EMA)
daily_ema = request.security(syminfo.tickerid, htf_trend, ta.ema(close, ema_length))
daily_close = request.security(syminfo.tickerid, htf_trend, close)
is_bullish_htf = daily_close > daily_ema

// Get MTF Structure (4-Hour Highs/Lows)
mtf_high = request.security(syminfo.tickerid, mtf_structure, ta.highest(high, 10))
mtf_low = request.security(syminfo.tickerid, mtf_structure, ta.lowest(low, 10))

// --- VISUAL SECTIONS ---
// Plot the MTF Support/Resistance Zones
plot(mtf_high, "MTF Resistance", color=color.new(color.red, 50), style=plot.style_linebr)
plot(mtf_low, "MTF Support", color=color.new(color.green, 50), style=plot.style_linebr)

// --- DASHBOARD TABLE ---
var table board = table.new(position.top_right, 2, 3, bgcolor=color.new(color.black, 80), border_width=1)

if barstate.islast
    // Header
    table.cell(board, 0, 0, "Timeframe", text_color=color.white)
    table.cell(board, 1, 0, "Bias", text_color=color.white)
    
    // Row 1: Daily Trend
    table.cell(board, 0, 1, "Daily (Trend)", text_color=color.white)
    table.cell(board, 1, 1, is_bullish_htf ? "BULLISH" : "BEARISH", bgcolor = is_bullish_htf ? color.green : color.red)
    
    // Row 2: 4H Structure
    table.cell(board, 0, 2, "4H (Zones)", text_color=color.white)
    table.cell(board, 1, 2, close > mtf_low and close < mtf_high ? "RANGING" : "BREAKOUT", text_color=color.white)

// --- ALERTS / SIGNALS ---
buy_condition = is_bullish_htf and ta.crossover(close, mtf_low)
sell_condition = not is_bullish_htf and ta.crossunder(close, mtf_high)

plotshape(buy_condition, "Buy Signal", shape.triangleup, location.belowbar, color.green, size=size.small)
plotshape(sell_condition, "Sell Signal", shape.triangledown, location.abovebar, color.red, size=size.small)
```
---
---
```vb
//@version=5
strategy("Trend-Filtered Mean Reversion: BB + RSI + Stoch", overlay=true, initial_capital=1000, default_qty_type=strategy.percent_of_equity, default_qty_value=10)

// --- INPUTS ---
emaLength = input.int(200, "Trend EMA Length")
bbLength   = input.int(20, "BB Length")
bbMult     = input.float(2.0, "BB StdDev")
rsiLength  = input.int(14, "RSI Length")
stochK     = input.int(14, "Stoch %K Length")
stochD     = input.int(3, "Stoch %D Smoothing")
stochSm    = input.int(3, "Stoch %K Smoothing")

// --- FILTER TOGGLES ---
useTrendFilter = input.bool(true, "Use Trend Filter (EMA)")
useBBFilter = input.bool(true, "Use Bollinger Bands Filter")
useRSIFilter = input.bool(true, "Use RSI Filter")
useStochFilter = input.bool(true, "Use Stochastic Filter")

// --- CALCULATIONS ---
// 1. Trend Filter
ema200 = ta.ema(close, emaLength)

// 2. Bollinger Bands
[bbMiddle, bbUpper, bbLower] = ta.bb(close, bbLength, bbMult)

// 3. RSI
rsiValue = ta.rsi(close, rsiLength)

// 4. Stochastic (with Snap-Back Logic)
k = ta.sma(ta.stoch(close, high, low, stochK), stochSm)
d = ta.sma(k, stochD)

// --- STRATEGY LOGIC ---
// Trend Check
isBullish = close > ema200
isBearish = close < ema200

// Long Condition: Trend is UP + Price at Lower Band + RSI Oversold + Stoch Snap-Back
longCondition = (useTrendFilter ? isBullish : true) and (useBBFilter ? (close <= bbLower or close[1] <= bbLower[1]) : true) and (useRSIFilter ? (rsiValue < 45) : true) and (useStochFilter ? ta.crossover(k, 20) : true)

// Short Condition: Trend is DOWN + Price at Upper Band + RSI Overbought + Stoch Snap-Back
shortCondition = (useTrendFilter ? isBearish : true) and (useBBFilter ? (close >= bbUpper or close[1] >= bbUpper[1]) : true) and (useRSIFilter ? (rsiValue > 55) : true) and (useStochFilter ? ta.crossunder(k, 80) : true)

// --- EXECUTION ---
if (longCondition)
    strategy.entry("Long", strategy.long, comment="Trend Long")

if (shortCondition)
    strategy.entry("Short", strategy.short, comment="Trend Short")

// Exit logic: Exit at Middle Band (Mean Reversion)
if (ta.cross(close, bbMiddle))
    strategy.close_all(comment="Exit: Mean")

// --- VISUALS ---
plot(ema200, color=color.new(color.white, 20), linewidth=2, title="200 EMA Trend")
plot(bbUpper, color=color.new(color.blue, 60), title="BB Upper")
plot(bbLower, color=color.new(color.blue, 60), title="BB Lower")

plotshape(longCondition, style=shape.labelup, location=location.belowbar, color=color.green, text="BUY", textcolor=color.white)
plotshape(shortCondition, style=shape.labeldown, location=location.abovebar, color=color.red, text="SELL", textcolor=color.white)
```

```vb
//@version=5
strategy("Improved Mean Reversion: BB + RSI + Stoch Snap-Back", overlay=true, initial_capital=1000)

// --- INPUTS ---
bbLength = input.int(20, "BB Length")
bbMult   = input.float(2.0, "BB StdDev")
rsiLen   = input.int(14, "RSI Length")
stochK   = input.int(14, "Stoch %K")
stochD   = input.int(3, "Stoch %D")
stochSm  = input.int(3, "Stoch Smooth")

// --- FILTER TOGGLES ---
useBBFilter = input.bool(true, "Use Bollinger Bands Filter")
useRSIFilter = input.bool(true, "Use RSI Filter")
useStochFilter = input.bool(true, "Use Stochastic Filter")

// --- CALCULATIONS ---
[bbMid, bbUpper, bbLower] = ta.bb(close, bbLength, bbMult)
rsiVal = ta.rsi(close, rsiLen)
k = ta.sma(ta.stoch(close, high, low, stochK), stochSm)
d = ta.sma(k, stochD)

// --- IMPROVED LOGIC ---
// Long: Price was low + RSI was oversold + Stoch NOW crossing back ABOVE 20
longCondition = (useBBFilter ? (close <= bbLower or close[1] <= bbLower[1]) : true) and (useRSIFilter ? (rsiVal < 40) : true) and (useStochFilter ? ta.crossover(k, 20) : true)

// Short: Price was high + RSI was overbought + Stoch NOW crossing back BELOW 80
shortCondition = (useBBFilter ? (close >= bbUpper or close[1] >= bbUpper[1]) : true) and (useRSIFilter ? (rsiVal > 60) : true) and (useStochFilter ? ta.crossunder(k, 80) : true)

// --- EXECUTION ---
if (longCondition)
    strategy.entry("Long", strategy.long)

if (shortCondition)
    strategy.entry("Short", strategy.short)

// Exit at Middle Band (Basis)
if (ta.cross(close, bbMid))
    strategy.close_all()

// --- VISUALS ---
plot(bbUpper, color=color.new(color.gray, 50))
plot(bbLower, color=color.new(color.gray, 50))
plotshape(longCondition, style=shape.labelup, location=location.belowbar, color=color.green, text="BUY", textcolor=color.white)
plotshape(shortCondition, style=shape.labeldown, location=location.abovebar, color=color.red, text="SELL", textcolor=color.white)
```
---
---
```vb
//@version=5
indicator("4H Candle Bias Filter for 15m", overlay=true)

// --- Inputs ---
htf = input.timeframe("240", "Higher Timeframe Bias (4H)")
ema_fast_length = input.int(9, "Fast EMA Length", minval=1)
ema_slow_length = input.int(21, "Slow EMA Length", minval=1)

// --- Fetch 4H Data ---
htf_open  = request.security(syminfo.tickerid, htf, open, lookahead=barmerge.lookahead_on)
htf_close = request.security(syminfo.tickerid, htf, close, lookahead=barmerge.lookahead_on)

// --- Define Bias ---
is_bullish_bias = close > htf_open
is_bearish_bias = close < htf_open

// --- Visualizing the "Permission to Trade" ---
// Green background = Only look for BUYS | Red = Only look for SELLS
bg_color = is_bullish_bias ? color.new(color.green, 90) : is_bearish_bias ? color.new(color.red, 90) : na
bgcolor(bg_color)

// Optional: Plot the 4H Open price as a "Support/Resistance" line
plot(htf_open, "4H Open Level", color=color.gray, style=plot.style_stepline, linewidth=2)

// --- EMA Calculation ---
ema_fast = ta.ema(close, ema_fast_length)
ema_slow = ta.ema(close, ema_slow_length)

// --- Plot EMAs ---
plot(ema_fast, "EMA 9", color=color.blue, linewidth=2)
plot(ema_slow, "EMA 21", color=color.orange, linewidth=2)

// --- Entry Signal Example (EMA Cross + 4H Bias) ---
buy_signal  = is_bullish_bias and ta.crossover(ema_fast, ema_slow)
sell_signal = is_bearish_bias and ta.crossunder(ema_fast, ema_slow)

plotshape(buy_signal, "Buy Alert", shape.triangleup, location.belowbar, color.green, size=size.small)
plotshape(sell_signal, "Sell Alert", shape.triangledown, location.abovebar, color.red, size=size.small)
```

```vb
//@version=5
indicator("4H Bias + EMA Crossover + Prev Day Range", overlay=true)

// ============================================================================
// INPUTS
// ============================================================================
htf = input.timeframe("240", "Higher Timeframe Bias (4H)")

// EMA Settings
show_emas = input.bool(true, "Show EMAs", group="EMAs")
ema_fast_length = input.int(9, "Fast EMA Length", group="EMAs", minval=1)
ema_slow_length = input.int(21, "Slow EMA Length", group="EMAs", minval=1)

// RSI Settings
show_rsi = input.bool(true, "Show RSI", group="RSI")
rsi_length = input.int(14, "RSI Length", group="RSI", minval=1)
rsi_overbought = input.int(70, "RSI Overbought Level", group="RSI", minval=50, maxval=100)
rsi_oversold = input.int(30, "RSI Oversold Level", group="RSI", minval=0, maxval=50)
rsi_source = input.source(close, "RSI Source", group="RSI")

// Previous Day Range Settings
show_pdr = input.bool(true, "Show Previous Day High/Low", group="Levels")

// Signal Settings
show_ema_signals = input.bool(true, "Show EMA Crossover Signals", group="Signals")
show_pdr_signals = input.bool(true, "Show PDR Breakout Signals", group="Signals")
show_rsi_signals = input.bool(true, "Show RSI Overbought/Oversold Signals", group="Signals")

// ============================================================================
// 4H BIAS CALCULATION
// ============================================================================
htf_open = request.security(syminfo.tickerid, htf, open, lookahead=barmerge.lookahead_on)
htf_close = request.security(syminfo.tickerid, htf, close, lookahead=barmerge.lookahead_on)

is_bullish_bias = close > htf_open
is_bearish_bias = close < htf_open

// ============================================================================
// EMA CALCULATION
// ============================================================================
ema_fast = ta.ema(close, ema_fast_length)
ema_slow = ta.ema(close, ema_slow_length)

// ============================================================================
// RSI CALCULATION
// ============================================================================
rsi = ta.rsi(rsi_source, rsi_length)
is_overbought = rsi > rsi_overbought
is_oversold = rsi < rsi_oversold

// RSI crossing into/out of zones
rsi_enters_overbought = ta.crossover(rsi, rsi_overbought)
rsi_exits_overbought = ta.crossunder(rsi, rsi_overbought)
rsi_enters_oversold = ta.crossunder(rsi, rsi_oversold)
rsi_exits_oversold = ta.crossover(rsi, rsi_oversold)

// ============================================================================
// PREVIOUS DAY RANGE
// ============================================================================
pdh = request.security(syminfo.tickerid, "D", high[1], lookahead=barmerge.lookahead_on)
pdl = request.security(syminfo.tickerid, "D", low[1], lookahead=barmerge.lookahead_on)

// ============================================================================
// VISUALS: BIAS BACKGROUND
// ============================================================================
bg_color = is_bullish_bias ? color.new(color.green, 90) : is_bearish_bias ? color.new(color.red, 90) : na
bgcolor(bg_color)

// ============================================================================
// VISUALS: 4H OPEN LEVEL
// ============================================================================
plot(htf_open, "4H Open Level", color=color.gray, style=plot.style_stepline, linewidth=2)

// ============================================================================
// VISUALS: EMAs
// ============================================================================
plot(show_emas ? ema_fast : na, "EMA 9", color=color.blue, linewidth=2)
plot(show_emas ? ema_slow : na, "EMA 21", color=color.orange, linewidth=2)

// ============================================================================
// VISUALS: PREVIOUS DAY RANGE
// ============================================================================
// Lines for current/future bars
var line hi_line = na
var line lo_line = na

if barstate.islast and show_pdr
    line.delete(hi_line)
    line.delete(lo_line)
    hi_line := line.new(bar_index - 50, pdh, bar_index + 10, pdh, color=color.new(color.gray, 20), style=line.style_dashed, width=1)
    lo_line := line.new(bar_index - 50, pdl, bar_index + 10, pdl, color=color.new(color.gray, 20), style=line.style_dashed, width=1)
    label.new(bar_index + 10, pdh, "Prev Day High", style=label.style_none, textcolor=color.gray, size=size.small)
    label.new(bar_index + 10, pdl, "Prev Day Low", style=label.style_none, textcolor=color.gray, size=size.small)

// Historical plot for previous day range
plot(show_pdr ? pdh : na, "PDH Historical", color=color.new(color.gray, 70), style=plot.style_stepline)
plot(show_pdr ? pdl : na, "PDL Historical", color=color.new(color.gray, 70), style=plot.style_stepline)

// ============================================================================
// TRADING SIGNALS
// ============================================================================

// EMA Crossover Signals (with 4H bias filter)
buy_ema_signal = is_bullish_bias and ta.crossover(ema_fast, ema_slow)
sell_ema_signal = is_bearish_bias and ta.crossunder(ema_fast, ema_slow)

// Previous Day Range Breakout Signals (with 4H bias filter)
long_pdr_breakout = is_bullish_bias and ta.crossover(close, pdh)
short_pdr_breakout = is_bearish_bias and ta.crossunder(close, pdl)

// RSI Signals (with 4H bias filter)
buy_rsi_signal = is_bullish_bias and rsi_exits_oversold  // Bullish when RSI exits oversold
sell_rsi_signal = is_bearish_bias and rsi_exits_overbought  // Bearish when RSI exits overbought

// ============================================================================
// SIGNAL VISUALIZATION
// ============================================================================

// EMA Crossover Signals
plotshape(show_ema_signals and buy_ema_signal, "Buy EMA Cross", shape.triangleup, location.belowbar, color.green, size=size.small)
plotshape(show_ema_signals and sell_ema_signal, "Sell EMA Cross", shape.triangledown, location.abovebar, color.red, size=size.small)

// RSI Signals
plotshape(show_rsi_signals and buy_rsi_signal, "RSI Buy Signal", shape.circle, location.belowbar, color.new(color.aqua, 0), size=size.tiny)
plotshape(show_rsi_signals and sell_rsi_signal, "RSI Sell Signal", shape.circle, location.abovebar, color.new(color.fuchsia, 0), size=size.tiny)

// Background highlighting for RSI zones
bgcolor(show_rsi and is_overbought ? color.new(color.red, 95) : na, title="Overbought BG")
bgcolor(show_rsi and is_oversold ? color.new(color.green, 95) : na, title="Oversold BG")

// PDR Breakout Signals
plotshape(show_pdr_signals and long_pdr_breakout, "PDH Breakout", shape.labelup, location.belowbar, color.new(color.lime, 0), text="PDH BREAK", textcolor=color.white, size=size.small)
plotshape(show_pdr_signals and short_pdr_breakout, "PDL Breakout", shape.labeldown, location.abovebar, color.new(color.maroon, 0), text="PDL BREAK", textcolor=color.white, size=size.small)

// ============================================================================
// ALERTS
// ============================================================================
alertcondition(buy_ema_signal, "Buy Signal - EMA Cross", "Bullish EMA crossover on {{ticker}}")
alertcondition(sell_ema_signal, "Sell Signal - EMA Cross", "Bearish EMA crossunder on {{ticker}}")
alertcondition(long_pdr_breakout, "Buy Signal - PDH Break", "Price broke above Previous Day High on {{ticker}}")
alertcondition(short_pdr_breakout, "Sell Signal - PDL Break", "Price broke below Previous Day Low on {{ticker}}")

alertcondition(buy_rsi_signal, "Buy Signal - RSI Oversold Exit", "RSI exited oversold zone on {{ticker}}")
alertcondition(sell_rsi_signal, "Sell Signal - RSI Overbought Exit", "RSI exited overbought zone on {{ticker}}")
alertcondition(rsi_enters_oversold, "RSI Oversold", "RSI entered oversold zone (<{{plot_0}}) on {{ticker}}")
alertcondition(rsi_enters_overbought, "RSI Overbought", "RSI entered overbought zone (>{{plot_0}}) on {{ticker}}")
```
---
---


## Indicator 
If you plan to use this for strategy testing: Add **Use strategy()** instead of **indicator()** to simulate PnL, win rate, drawdown
The "beginning" of a color change occurs when the two EMAs cross each other. The signals should be exactly at the start of change of ribbon colour

* **Buy Signal (Green Ribbon Starts):** Triggered when the **Fast EMA crosses over the Slow EMA**. This indicates bullish momentum.
* **Sell Signal (Red Ribbon Starts):** Triggered when the **Fast EMA crosses under the Slow EMA**. This indicates bearish momentum.
* **EMA ribbon is shaded between the EMAs**, so the ribbon itself visibly changes color exactly at the crossover. 
* **Trend EMA:** e.g EMA50 is plotted as a single gray line—no shading, just trend context.
* **RSI**: no buys when RSI > 70, no sells when RSI < 30. To prevent entering just as price may be reversing
* **Input**: user can adjust input values (e.g from 20 to 50)
* **EMA trend Slope/ Momentum:** EMAtrend slope > 0 for buys, < 0 for sells. This ensures trades go in the direction of momentum.
* **Alert** Add alertcondition for buy/sell triggers.
* **Multiple Timeframe**: only allow trades on the lower timeframe (e.g., 15m) in the same direction as the higher timeframe.
* **Highlight Crossover Candle:** Highlight the candle where the crossover occurs with different background color.
* s
* Bullish Warning: EMAs squeezing + price above trend EMA + upward slope + RSI healthy
* Bearish Warning: EMAs squeezing + price below trend EMA + downward slope + RSI healthy
* This prevents false warnings in choppy/uncertain conditions

```pinescript
// Define the cross conditions
buySignal  = ta.crossover(fastEMA, slowEMA)
sellSignal = ta.crossunder(fastEMA, slowEMA)

// Plot shapes on the chart
plotshape(buySignal,  style=shape.triangleup,   location=location.belowbar, color=color.green, size=size.small, title="Buy Entry")
plotshape(sellSignal, style=shape.triangledown, location=location.abovebar, color=color.red,   size=size.small, title="Sell Entry")

```
4. Adjust inputs to match your timeframe:
   - **Scalping (5m-15m)**: Fast 8, Slow 21, Trend 50
   - **Day Trading (1H-4H)**: Fast 12, Slow 26, Trend 50 (default)
   - **Swing Trading (D)**: Fast 20, Slow 50, Trend 200
     
**Indicator**
```vb
//@version=5
indicator("EMA Crossover + Confluence", overlay=true)

// ═══════════════════════════════════════════════════════════
// INPUTS - Adjust to Your Preference
// ═══════════════════════════════════════════════════════════
fastLength = input.int(12, "Fast EMA", minval=1)
slowLength = input.int(26, "Slow EMA", minval=1)
trendLength = input.int(50, "Trend EMA", minval=1)
rsiLength = input.int(14, "RSI Length", minval=1)
rsiOverbought = input.int(70, "RSI Overbought", minval=50, maxval=100)
rsiOversold = input.int(30, "RSI Oversold", minval=0, maxval=50)

// ═══════════════════════════════════════════════════════════
// CALCULATIONS
// ═══════════════════════════════════════════════════════════
fastEMA = ta.ema(close, fastLength)
slowEMA = ta.ema(close, slowLength)
trendEMA = ta.ema(close, trendLength)
rsi = ta.rsi(close, rsiLength)

// Trend slope (momentum direction)
trendSlope = trendEMA - trendEMA[1]

// EMA squeeze detection (EMAs coming together = momentum building)
emaDistance = math.abs(fastEMA - slowEMA)
avgDistance = ta.sma(emaDistance, 20)
isSqueezing = emaDistance < avgDistance * 0.8

// ═══════════════════════════════════════════════════════════
// SIGNAL CONDITIONS WITH CONFLUENCE
// ═══════════════════════════════════════════════════════════
// Basic crossover/crossunder
emaCrossUp = ta.crossover(fastEMA, slowEMA)
emaCrossDown = ta.crossunder(fastEMA, slowEMA)

// BUY: All conditions aligned bullish
buySignal = emaCrossUp and close > trendEMA and trendSlope > 0 and rsi < rsiOverbought and isSqueezing

// SELL: All conditions aligned bearish
sellSignal = emaCrossDown and 
             close < trendEMA and 
             trendSlope < 0 and 
             rsi > rsiOversold and 
             isSqueezing

// ═══════════════════════════════════════════════════════════
// VISUALS
// ═══════════════════════════════════════════════════════════
// 1. Assign plot IDs to variables
p1 = plot(fastEMA, "Fast EMA", color=color.new(color.blue, 0), linewidth=2)
p2 = plot(slowEMA, "Slow EMA", color=color.new(color.orange, 0), linewidth=2)
plot(trendEMA, "Trend EMA", color=color.new(color.gray, 0), linewidth=2)

// 2. Reference those IDs (p1 and p2) in the fill
ribbonColor = fastEMA > slowEMA ? color.new(color.green, 80) : color.new(color.red, 80)
fill(p1, p2, color=ribbonColor, title="EMA Ribbon")

// Signal shapes
plotshape(buySignal, style=shape.triangleup, location=location.belowbar, 
          color=color.new(color.green, 0), size=size.normal, title="Buy Signal")
plotshape(sellSignal, style=shape.triangledown, location=location.abovebar, 
          color=color.new(color.red, 0), size=size.normal, title="Sell Signal")

// Highlight crossover candle backgrounds
bgcolor(buySignal ? color.new(color.green, 90) : na, title="Buy Candle Highlight")
bgcolor(sellSignal ? color.new(color.red, 90) : na, title="Sell Candle Highlight")

// ═══════════════════════════════════════════════════════════
// ALERTS
// ═══════════════════════════════════════════════════════════
alertcondition(buySignal, title="Buy Alert", 
               message="BUY Signal: EMA Cross + All Filters Aligned ✓")
alertcondition(sellSignal, title="Sell Alert", 
               message="SELL Signal: EMA Cross + All Filters Aligned ✓")

// ═══════════════════════════════════════════════════════════
// DASHBOARD (Optional info display)
// ═══════════════════════════════════════════════════════════
var table infoTable = table.new(position.top_right, 2, 5, 
                                 bgcolor=color.new(color.black, 85), 
                                 border_width=1)

if barstate.islast
    table.cell(infoTable, 0, 0, "RSI", text_color=color.white)
    table.cell(infoTable, 1, 0, str.tostring(rsi, "#.##"), 
               text_color=rsi > rsiOverbought ? color.red : rsi < rsiOversold ? color.green : color.white)
    
    table.cell(infoTable, 0, 1, "Trend", text_color=color.white)
    table.cell(infoTable, 1, 1, trendSlope > 0 ? "UP ▲" : "DOWN ▼", 
               text_color=trendSlope > 0 ? color.green : color.red)
    
    table.cell(infoTable, 0, 2, "Position", text_color=color.white)
    table.cell(infoTable, 1, 2, close > trendEMA ? "Above" : "Below", 
               text_color=close > trendEMA ? color.green : color.red)
    
    table.cell(infoTable, 0, 3, "Squeeze", text_color=color.white)
    table.cell(infoTable, 1, 3, isSqueezing ? "YES" : "NO", 
               text_color=isSqueezing ? color.yellow : color.gray)
    
    table.cell(infoTable, 0, 4, "Ribbon", text_color=color.white)
    table.cell(infoTable, 1, 4, fastEMA > slowEMA ? "BULL" : "BEAR", 
               text_color=fastEMA > slowEMA ? color.green : color.red)
```
---
---
**The new and latest version**

## 🎓 **HOW TO USE THIS**

### **Backtest (Do This First)**
1. Test period: atleast 2 years data (includes bull, bear, and sideways)
2. Record:
   - Total trades
   - Win rate
   - Profit factor
   - Max drawdown
   - Average R:R actually achieved

### **Recommended Settings**
- This is recommended settings for high volatile currencies (e.g  Bitcoin, BTC/USDT)
- Each currency pair require its own settings depending on volatility
  
**Conservative (Beginner friendly):**
- riskPercent = 1.0
- atrMultiplier = 3.5
- tpRiskRatio = 2.0
- partialTPLevel = 1.8
- confluenceThreshold = 8/10

**Balanced (Optimized):**
- riskPercent = 1.5
- atrMultiplier = 3.5
- tpRiskRatio = 2.5
- partialTPLevel = 2.0
- usePartialTP = false  // Try full exits only

**Aggressive (Try for maximum profits):**
- riskPercent = 2.0
- atrMultiplier = 3.0
- tpRiskRatio = 3.0
- partialTPLevel = 2.5
- trailOffset = 1.5

## ⚠️ **CRITICAL REMINDERS**
1. **Commission is 0.1%** (set for crypto exchanges like Binance)
   - If using different exchange, adjust in code

2. **This is 4H timeframe optimized**
   - 1H = More trades, more noise
   - Daily = Fewer trades, better signals


```vb
□ 1. Check your strategy Dashboard on the TradingView
     - Bull Score: ___/10 (must be ≥ 8.0)
     - Regime: ___________ (prefer 📈 TRENDING, avoid ⚡ RANGING/TRANSITION)
     - Session: Active ✓ or Inactive ✗ (avoid 02:00–06:00 UTC)
     - Volume: Good ✓ or Low ✗ (Volume ≥ 0.7× 24H Avg)
     
□ 2. Write Down Signal Details (from chart)
     Entry Price: $__________
     Stop Loss: $__________ (ATR-based)
     Take Profit: $__________ (≈ 2R)
     Partial TP: $__________ (50% exit at ≈ 1.3R)
     
□ 3. Go to Binance Account
     Login → Derivatives → USDT-M Futures

□ 4. Select Correct Pair
     - Click pair name (top left)
     - Search: e.g BTCUSDT
     - Select: e.g BTC/USDT Perpetual
     
□ 5. Set to ISOLATED MARGIN ✓
     - Top of order panel
     - Click "Cross" → Change to "Isolated"
     - Confirm margin mode
     
□ 6. Calculate Position Size (Risk-Based)
     Account Balance: $________
     Risk Per Trade: 1.5% = $________
     Stop Distance: $________ (|Entry − SL|)
     Position Size: Risk ÷ Stop Distance = $_______ (contract value)
     
□ 7. Select ORDER TYPE:
     - Preferred: LIMIT (at signal close)
     - Alternative: MARKET (only if strong momentum)
     
□ 8. Enter Trade Details (from TradingView)
     - Side: BUY (Long) or SELL (Short)
     - Price: $__________ (Entry price)
     - Amount: __________ (contracts / coin)
     - Click "Buy/Long" or "Sell/Short"
     
□ 9. SET STOP LOSS (Immediately!)
     - Click "TP/SL" on position
     - Stop Loss Price: $__________
     - Stop Loss Type: Last Price
     - Click "Confirm"
     
□ 10. SET TAKE PROFIT
      - Take Profit Price: $__________
      - TP Type: Last Price
      - Click "Confirm"
      
□ 11. SET PRICE ALERT for Partial TP
      - Right-click chart → "Add Alert"
      - Price crosses: $__________ (Partial TP ≈ 1.3R)
      - Message: "Close 50% at Partial TP"
```

## Crypto trading strategy  
```pine
    //@version=5
    strategy("Confluence Pro V3 - Crypto Volatility Optimized", 
             overlay=true, 
             initial_capital=10000, 
             default_qty_type=strategy.percent_of_equity,
             default_qty_value=100,
             commission_type=strategy.commission.percent,
             commission_value=0.1) // 0.1% trading fee for crypto exchanges
    
    // ========================================
    // 🚀 CRYPTO-OPTIMIZED INPUTS
    // ========================================
    // Risk Management - Crypto Calibrated
    riskPercent = input.float(1.5, "Risk % Per Trade", minval=0.5, maxval=3.0, group="💰 Risk Management")
    atrMultiplier = input.float(3.5, "ATR Stop Loss Multiplier (Wider for Crypto)", minval=2.0, maxval=5.0, group="💰 Risk Management")
    tpRiskRatio = input.float(2.0, "Take Profit R:R Ratio", minval=1.2, maxval=3.0, group="💰 Risk Management")
    
    // Partial Profit Taking (Crypto-Specific)
    usePartialTP = input.bool(true, "Enable Partial Profit Taking", group="💰 Risk Management")
    partialTPLevel = input.float(1.3, "Take 50% Profit at X R", minval=1.0, maxval=2.5, group="💰 Risk Management")
    useBreakevenStop = input.bool(true, "Move SL to Breakeven at 1.5R", group="💰 Risk Management")
    
    // Trailing Stop (Essential for Crypto Trends)
    useTrailingStop = input.bool(true, "Use Trailing Stop", group="💰 Risk Management")
    trailActivation = input.float(1.5, "Activate Trail at X R", minval=1.0, maxval=3.0, group="💰 Risk Management")
    trailOffset = input.float(2.0, "Trail Offset (ATR Multiplier)", minval=1.0, maxval=4.0, group="💰 Risk Management")
    
    // Strategy Settings - Higher Bar for Entry
    confluenceThreshold = input.int(8, "Min Score for Signal (Higher = Fewer Trades)", minval=5, maxval=10, group="⚙️ Strategy")
    useRegimeFilter = input.bool(true, "Enable Market Regime Filter", group="⚙️ Strategy")
    useMandatoryTrend = input.bool(true, "Mandatory Trend Alignment (EMA)", group="⚙️ Strategy")
    
    // Crypto Volume Deadzone Filter
    useVolumeFilter = input.bool(true, "Avoid Low Volume Hours (2AM-6AM UTC)", group="⚙️ Strategy")
    minVolumeMultiple = input.float(0.7, "Min Volume vs 24H Avg", minval=0.3, maxval=1.5, group="⚙️ Strategy")
    
    // Market Regime - Crypto Adjusted
    trendingADX = input.int(22, "ADX Threshold for Trending (Lower for Crypto)", minval=15, maxval=35, group="📊 Regime Detection")
    rangingADX = input.int(18, "ADX Threshold for Ranging", minval=10, maxval=25, group="📊 Regime Detection")
    
    // Volatility Expansion Filter (Prevents Chop Trading)
    requireVolExpansion = input.bool(true, "Require Volatility Expansion for Entry", group="📊 Regime Detection")
    
    // ========================================
    // 🎯 CRYPTO MARKET STRUCTURE
    // ========================================
    [diplus, diminus, adx] = ta.dmi(14, 14)
    isTrending = adx > trendingADX
    isRanging = adx < rangingADX
    isTransition = not isTrending and not isRanging
    
    // ========================================
    // 📊 VOLUME ANALYSIS (Crypto-Specific)
    // ========================================
    vol24hAvg = ta.sma(volume, 24)
    currentVolRatio = volume / vol24hAvg
    volumeOK = not useVolumeFilter or currentVolRatio >= minVolumeMultiple
    
    // Dead Zone Filter (2AM-6AM UTC = Low Liquidity)
    currentHour = hour(time, "UTC")
    isDeadZone = currentHour >= 2 and currentHour < 6
    timeOK = not useVolumeFilter or not isDeadZone
    
    // ========================================
    // CATEGORY 1: TREND (Crypto Price Momentum)
    // ========================================
    // 1.1 EMA Ribbon - Multi-Timeframe Trend
    ema20 = ta.ema(close, 20)
    ema50 = ta.ema(close, 50)
    ema200 = ta.ema(close, 200)
    
    // Bullish: 20>50>200 and price above all
    bull_trend1 = close > ema20 and ema20 > ema50 and ema50 > ema200
    bear_trend1 = close < ema20 and ema20 < ema50 and ema50 < ema200
    
    // 1.2 ADX Directional Strength
    bull_trend2 = diplus > diminus and adx > rangingADX
    bear_trend2 = diminus > diplus and adx > rangingADX
    
    // ========================================
    // CATEGORY 2: MOMENTUM (Divergence-Aware)
    // ========================================
    // 2.1 RSI with Crypto Zones
    rsi = ta.rsi(close, 14)
    bull_momentum1 = rsi > 55 and rsi < 75  // Above neutral, not overbought
    bear_momentum1 = rsi < 45 and rsi > 25  // Below neutral, not oversold
    
    // 2.2 MACD with Histogram Acceleration
    [macdLine, signalLine, macdHist] = ta.macd(close, 12, 26, 9)
    histRising = macdHist > macdHist[1]
    histFalling = macdHist < macdHist[1]
    bull_momentum2 = macdLine > signalLine and histRising
    bear_momentum2 = macdLine < signalLine and histFalling
    
    // ========================================
    // CATEGORY 3: VOLATILITY & BREAKOUTS
    // ========================================
    // 3.1 Bollinger Bands - Expansion + Position
    [bb_mid, bb_upper, bb_lower] = ta.bb(close, 20, 2.5)  // Wider bands for crypto
    bbWidth = (bb_upper - bb_lower) / bb_mid
    bbExpanding = bbWidth > ta.sma(bbWidth, 20)
    
    bull_volatility1 = close > bb_mid and (not requireVolExpansion or bbExpanding)
    bear_volatility1 = close < bb_mid and (not requireVolExpansion or bbExpanding)
    
    // 3.2 ATR Expansion - Breakout Confirmation
    atr = ta.atr(14)
    atrRising = atr > ta.sma(atr, 20) * 1.1  // ATR 10% above average
    bull_volatility2 = close > close[1] and atrRising
    bear_volatility2 = close < close[1] and atrRising
    
    // ========================================
    // CATEGORY 4: MEAN REVERSION (Range Detection)
    // ========================================
    // 4.1 Stochastic RSI (More Sensitive for Crypto)
    stochRSI_K = ta.stoch(rsi, rsi, rsi, 14)
    stochRSI_D = ta.sma(stochRSI_K, 3)
    
    bull_reversion1 = ta.crossover(stochRSI_K, stochRSI_D) and stochRSI_K < 80
    bear_reversion1 = ta.crossunder(stochRSI_K, stochRSI_D) and stochRSI_K > 20
    
    // 4.2 CCI Momentum Cycles
    cci = ta.cci(close, 20)
    bull_reversion2 = cci > 0 and cci < 150  // Positive but not extreme
    bear_reversion2 = cci < 0 and cci > -150
    
    // ========================================
    // CATEGORY 5: CRYPTO PRICE ACTION
    // ========================================
    // 5.1 Higher High/Lower Low Structure (Key for Crypto)
    hhll_period = 3
    highest_high = ta.highest(high, hhll_period)
    lowest_low = ta.lowest(low, hhll_period)
    
    bull_structure = high >= highest_high[1] and low >= lowest_low[1]
    bear_structure = high <= highest_high[1] and low <= lowest_low[1]
    
    // 5.2 Candle Strength (Body Dominance)
    bodySize = math.abs(close - open)
    avgBody = ta.sma(bodySize, 20)
    wickRatio = bodySize / (high - low)
    
    bull_candle = close > open and bodySize > avgBody * 1.5 and wickRatio > 0.6
    bear_candle = close < open and bodySize > avgBody * 1.5 and wickRatio > 0.6
    
    // ========================================
    // 🧮 REGIME-ADAPTIVE SCORING SYSTEM
    // ========================================
    // Dynamic weighting based on market conditions
    trendWeight = isTrending ? 1.2 : 0.7       // Boost trend signals when trending
    momentumWeight = 1.0                        // Always important
    volatilityWeight = bbExpanding ? 1.2 : 0.8  // Boost on expansion
    reversionWeight = isRanging ? 1.0 : 0.4     // Only valuable in ranges
    structureWeight = 1.1                       // Price action always key
    
    // Calculate weighted scores (max ~11 points with weights)
    longScore = 
         (bull_trend1 ? trendWeight : 0) + 
         (bull_trend2 ? trendWeight : 0) + 
         (bull_momentum1 ? momentumWeight : 0) + 
         (bull_momentum2 ? momentumWeight : 0) + 
         (bull_volatility1 ? volatilityWeight : 0) + 
         (bull_volatility2 ? volatilityWeight : 0) + 
         (bull_reversion1 ? reversionWeight : 0) + 
         (bull_reversion2 ? reversionWeight : 0) + 
         (bull_structure ? structureWeight : 0) + 
         (bull_candle ? structureWeight : 0)
    
    shortScore = 
         (bear_trend1 ? trendWeight : 0) + 
         (bear_trend2 ? trendWeight : 0) + 
         (bear_momentum1 ? momentumWeight : 0) + 
         (bear_momentum2 ? momentumWeight : 0) + 
         (bear_volatility1 ? volatilityWeight : 0) + 
         (bear_volatility2 ? volatilityWeight : 0) + 
         (bear_reversion1 ? reversionWeight : 0) + 
         (bear_reversion2 ? reversionWeight : 0) + 
         (bear_structure ? structureWeight : 0) + 
         (bear_candle ? structureWeight : 0)
    
    // Normalize to 0-10 scale for display
    normalizedLongScore = longScore / 1.15 * 10  // Adjust for max possible ~11.5
    normalizedShortScore = shortScore / 1.15 * 10
    
    // ========================================
    // 🎯 ENTRY FILTERS (Multi-Layer)
    // ========================================
    mandatoryTrend = not useMandatoryTrend or (bull_trend1 or bear_trend1)
    regimeOK = not useRegimeFilter or not isTransition
    noActivePosition = strategy.position_size == 0
    
    // Final Entry Conditions
    longCondition = longScore >= confluenceThreshold and mandatoryTrend and regimeOK and volumeOK and timeOK and noActivePosition
    shortCondition = shortScore >= confluenceThreshold and mandatoryTrend and regimeOK and volumeOK and timeOK and noActivePosition
    
    // ========================================
    // 💰 DYNAMIC POSITION SIZING
    // ========================================
    accountEquity = strategy.equity
    riskAmount = accountEquity * (riskPercent / 100)
    stopDistance = atr * atrMultiplier
    
    // Position size based on risk and stop distance
    positionSize = (riskAmount / stopDistance) * close
    positionSizePercent = (positionSize / accountEquity) * 100
    
    // Safety cap: never risk more than 100% of equity in position value
    safePositionSize = math.min(positionSizePercent, 95)
    
    // ========================================
    // 📍 STOP LOSS & TAKE PROFIT
    // ========================================
    var float entryPrice = na
    var float longStopPrice = na
    var float longTPPrice = na
    var float shortStopPrice = na
    var float shortTPPrice = na
    var bool partialTPTaken = false
    var bool breakEvenSet = false
    
    // ========================================
    // 🚀 ENTRY EXECUTION
    // ========================================
    if longCondition
        entryPrice := close
        longStopPrice := close - stopDistance
        longTPPrice := close + (stopDistance * tpRiskRatio)
        partialTPTaken := false
        breakEvenSet := false
        strategy.entry("Long", strategy.long, qty=safePositionSize)
    
    if shortCondition
        entryPrice := close
        shortStopPrice := close + stopDistance
        shortTPPrice := close - (stopDistance * tpRiskRatio)
        partialTPTaken := false
        breakEvenSet := false
        strategy.entry("Short", strategy.short, qty=safePositionSize)
    
    // ========================================
    // 🎯 ADVANCED EXIT MANAGEMENT
    // ========================================
    if strategy.position_size > 0  // LONG POSITION
        currentProfit = (close - entryPrice) / stopDistance  // Profit in R multiples
        
        // 1. Partial Profit Taking
        if usePartialTP and not partialTPTaken and currentProfit >= partialTPLevel
            strategy.close("Long", qty_percent=50, comment="Partial TP 50%")
            partialTPTaken := true
        
        // 2. Move to Breakeven
        if useBreakevenStop and not breakEvenSet and currentProfit >= 1.5
            longStopPrice := entryPrice + (atr * 0.1)  // Slightly above BE
            breakEvenSet := true
        
        // 3. Trailing Stop
        if useTrailingStop and currentProfit >= trailActivation
            trailStop = close - (atr * trailOffset)
            longStopPrice := math.max(longStopPrice, trailStop)
        
        // Execute exit
        strategy.exit("Exit Long", "Long", stop=longStopPrice, limit=longTPPrice)
    
    if strategy.position_size < 0  // SHORT POSITION
        currentProfit = (entryPrice - close) / stopDistance
        
        // 1. Partial Profit Taking
        if usePartialTP and not partialTPTaken and currentProfit >= partialTPLevel
            strategy.close("Short", qty_percent=50, comment="Partial TP 50%")
            partialTPTaken := true
        
        // 2. Move to Breakeven
        if useBreakevenStop and not breakEvenSet and currentProfit >= 1.5
            shortStopPrice := entryPrice - (atr * 0.1)
            breakEvenSet := true
        
        // 3. Trailing Stop
        if useTrailingStop and currentProfit >= trailActivation
            trailStop = close + (atr * trailOffset)
            shortStopPrice := math.min(shortStopPrice, trailStop)
        
        // Execute exit
        strategy.exit("Exit Short", "Short", stop=shortStopPrice, limit=shortTPPrice)
    
    // ========================================
    // 📊 VISUAL ELEMENTS
    // ========================================
    // EMA Ribbon
    plot(ema20, color=color.new(color.yellow, 0), linewidth=1, title="EMA 20")
    plot(ema50, color=color.new(color.orange, 0), linewidth=2, title="EMA 50")
    plot(ema200, color=color.new(color.gray, 40), linewidth=3, title="EMA 200")
    
    // Bollinger Bands
    p1 = plot(bb_upper, color=color.new(color.blue, 70), title="BB Upper")
    p2 = plot(bb_lower, color=color.new(color.blue, 70), title="BB Lower")
    fill(p1, p2, color=color.new(color.blue, 93))
    
    // Entry Signals - Larger for visibility
    plotshape(longCondition, style=shape.triangleup, location=location.belowbar, 
             color=color.new(color.lime, 0), size=size.normal, title="🚀 LONG")
    plotshape(shortCondition, style=shape.triangledown, location=location.abovebar, 
             color=color.new(color.red, 0), size=size.normal, title="🔻 SHORT")
    
    // Stop Loss & Take Profit Lines
    plot(strategy.position_size > 0 ? longStopPrice : na, color=color.new(color.red, 0), 
         style=plot.style_linebr, linewidth=2, title="Stop Loss")
    plot(strategy.position_size > 0 ? longTPPrice : na, color=color.new(color.lime, 0), 
         style=plot.style_linebr, linewidth=2, title="Take Profit")
    plot(strategy.position_size < 0 ? shortStopPrice : na, color=color.new(color.red, 0), 
         style=plot.style_linebr, linewidth=2, title="Stop Loss")
    plot(strategy.position_size < 0 ? shortTPPrice : na, color=color.new(color.lime, 0), 
         style=plot.style_linebr, linewidth=2, title="Take Profit")
    
    // Partial TP Level (visual guide)
    partialTPLine = strategy.position_size > 0 ? entryPrice + (stopDistance * partialTPLevel) : strategy.position_size < 0 ? entryPrice - (stopDistance * partialTPLevel) : na
    plot(partialTPLine, color=color.new(color.yellow, 50), style=plot.style_circles, linewidth=1, title="Partial TP")
    
    // ========================================
    // 📱 ADVANCED DASHBOARD
    // ========================================
    var table dashboard = table.new(position.top_right, 3, 10, bgcolor=color.new(#000000, 85), border_width=2, border_color=color.new(color.gray, 50))
    
    if barstate.islast
        // Header
        table.cell(dashboard, 0, 0, "🚀 CRYPTO CONFLUENCE V3", 
                  text_color=color.white, text_size=size.normal, 
                  bgcolor=color.new(#1E3A8A, 60))
        table.merge_cells(dashboard, 0, 0, 2, 0)
        
        // Market Regime
        regimeText = isTrending ? "📈 TRENDING" : isRanging ? "📊 RANGING" : "⚡ TRANSITION"
        regimeColor = isTrending ? color.lime : isRanging ? color.orange : color.yellow
        table.cell(dashboard, 0, 1, "Regime", text_color=color.gray, text_size=size.small)
        table.cell(dashboard, 1, 1, regimeText, text_color=regimeColor, text_size=size.small)
        table.cell(dashboard, 2, 1, "ADX:" + str.tostring(math.round(adx, 1)), 
                  text_color=color.white, text_size=size.small)
        
        // Confluence Scores
        longScoreRounded = math.round(normalizedLongScore, 1)
        shortScoreRounded = math.round(normalizedShortScore, 1)
        
        table.cell(dashboard, 0, 2, "🐂 Bull", text_color=color.lime, text_size=size.small)
        table.cell(dashboard, 1, 2, str.tostring(longScoreRounded) + "/10", 
                  text_color=color.lime, text_size=size.normal, 
                  bgcolor=longScore >= confluenceThreshold ? color.new(color.green, 80) : na)
        table.cell(dashboard, 2, 2, longScore >= confluenceThreshold ? "✓ READY" : "⏳ Wait", 
                  text_color=longScore >= confluenceThreshold ? color.lime : color.gray, 
                  text_size=size.small)
        
        table.cell(dashboard, 0, 3, "🐻 Bear", text_color=color.red, text_size=size.small)
        table.cell(dashboard, 1, 3, str.tostring(shortScoreRounded) + "/10", 
                  text_color=color.red, text_size=size.normal,
                  bgcolor=shortScore >= confluenceThreshold ? color.new(color.red, 80) : na)
        table.cell(dashboard, 2, 3, shortScore >= confluenceThreshold ? "✓ READY" : "⏳ Wait", 
                  text_color=shortScore >= confluenceThreshold ? color.red : color.gray, 
                  text_size=size.small)
        
        // Volatility Metrics
        table.cell(dashboard, 0, 4, "⚡ ATR", text_color=color.gray, text_size=size.small)
        table.cell(dashboard, 1, 4, "$" + str.tostring(math.round(atr, 0)), 
                  text_color=color.white, text_size=size.small)
        table.cell(dashboard, 2, 4, atrRising ? "📈 Rising" : "📉 Falling", 
                  text_color=atrRising ? color.orange : color.blue, text_size=size.small)
        
        // Volume Status
        volStatus = currentVolRatio >= minVolumeMultiple ? "✓ Good" : "⚠ Low"
        volColor = currentVolRatio >= minVolumeMultiple ? color.lime : color.orange
        table.cell(dashboard, 0, 5, "📊 Volume", text_color=color.gray, text_size=size.small)
        table.cell(dashboard, 1, 5, str.tostring(math.round(currentVolRatio, 2)) + "x", 
                  text_color=color.white, text_size=size.small)
        table.cell(dashboard, 2, 5, volStatus, text_color=volColor, text_size=size.small)
        
        // Time/Session Status
        timeStatus = timeOK ? "✓ Active" : "🌙 Dead Zone"
        timeColor = timeOK ? color.lime : color.red
        table.cell(dashboard, 0, 6, "🕐 Time", text_color=color.gray, text_size=size.small)
        table.cell(dashboard, 1, 6, str.tostring(currentHour) + ":00 UTC", 
                  text_color=color.white, text_size=size.small)
        table.cell(dashboard, 2, 6, timeStatus, text_color=timeColor, text_size=size.small)
        
        // Position Info
        posText = strategy.position_size > 0 ? "🚀 LONG" : 
                  strategy.position_size < 0 ? "🔻 SHORT" : "⏸ No Position"
        posColor = strategy.position_size > 0 ? color.lime : 
                   strategy.position_size < 0 ? color.red : color.gray
        table.cell(dashboard, 0, 7, "Position", text_color=color.gray, text_size=size.small)
        table.cell(dashboard, 1, 7, posText, text_color=posColor, text_size=size.normal)
        table.merge_cells(dashboard, 1, 7, 2, 7)
        
        // Risk Metrics
        if strategy.position_size != 0
            currentPnL = strategy.position_size > 0 ? 
                         (close - entryPrice) : (entryPrice - close)
            pnlPercent = (currentPnL / entryPrice) * 100
            pnlColor = currentPnL > 0 ? color.lime : color.red
            
            table.cell(dashboard, 0, 8, "💰 P&L", text_color=color.gray, text_size=size.small)
            table.cell(dashboard, 1, 8, str.tostring(math.round(pnlPercent, 2)) + "%", 
                      text_color=pnlColor, text_size=size.normal)
            table.cell(dashboard, 2, 8, "$" + str.tostring(math.round(currentPnL, 0)), 
                      text_color=pnlColor, text_size=size.small)
        else
            table.cell(dashboard, 0, 8, "Risk/Trade", text_color=color.gray, text_size=size.small)
            table.cell(dashboard, 1, 8, str.tostring(riskPercent) + "%", 
                      text_color=color.white, text_size=size.small)
            table.cell(dashboard, 2, 8, "$" + str.tostring(math.round(riskAmount, 0)), 
                      text_color=color.white, text_size=size.small)
        
        // Exit Alerts
        if strategy.position_size != 0
            exitStatus = partialTPTaken ? "✓ 50% Taken" : 
                         breakEvenSet ? "🔒 BE Set" : "⏳ Running"
            exitColor = partialTPTaken ? color.lime : breakEvenSet ? color.yellow : color.white
            table.cell(dashboard, 0, 9, "Exit Status", text_color=color.gray, text_size=size.small)
            table.cell(dashboard, 1, 9, exitStatus, text_color=exitColor, text_size=size.small)
            table.merge_cells(dashboard, 1, 9, 2, 9)
```

## 🎯 **STEP-BY-STEP: Converting this Crypto trading strategy Settings & code to Forex**
### **YOUR CURRENT CRYPTO SETTINGS (What's in the code now)**
```
✓ Risk Per Trade: 1.5%
✓ ATR Stop Multiplier: 3.5
✓ Take Profit Ratio: 2.0
✓ Partial TP Level: 1.8
✓ Trail Offset: 2.0
✓ Confluence Threshold: 8
✓ Commission: 0.1%
```

## 🔄 **FOREX CONVERSION TABLE (Simple Copy-Paste)**
### **STEP 1: Choose Your Forex Pair Category**

| Your Pair | Category | Examples |
|-----------|----------|----------|
| **Major Low-Vol** | Category A | EUR/USD, USD/CHF, EUR/GBP |
| **Major Med-Vol** | Category B | GBP/USD, AUD/USD, USD/CAD, USD/JPY |
| **Major High-Vol** | Category C | GBP/JPY, EUR/JPY, AUD/JPY |

### **STEP 2: Change These Numbers in TradingView Settings**

**Open your strategy → Click gear icon ⚙️ → Change these inputs:**

#### **FOR CATEGORY A (EUR/USD, USD/CHF, EUR/GBP)**
```
Risk % Per Trade: 1.0
ATR Stop Loss Multiplier: 1.5
Take Profit R:R Ratio: 2.5
Take 50% Profit at X R: 1.3
Trail Offset (ATR Multiplier): 1.2
Min Score for Signal: 8
ADX Threshold for Trending: 25
ADX Threshold for Ranging: 20
```

#### **FOR CATEGORY B (GBP/USD, AUD/USD, USD/JPY)**
```
Risk % Per Trade: 1.5
ATR Stop Loss Multiplier: 1.8
Take Profit R:R Ratio: 2.2
Take 50% Profit at X R: 1.5
Trail Offset (ATR Multiplier): 1.3
Min Score for Signal: 7
ADX Threshold for Trending: 23
ADX Threshold for Ranging: 20
```

#### **FOR CATEGORY C (GBP/JPY, EUR/JPY, AUD/JPY)**
```
Risk % Per Trade: 2.0
ATR Stop Loss Multiplier: 2.2
Take Profit R:R Ratio: 2.0
Take 50% Profit at X R: 1.8
Trail Offset (ATR Multiplier): 1.5
Min Score for Signal: 7
ADX Threshold for Trending: 22
ADX Threshold for Ranging: 18
```

### **STEP 3: Change Commission Settings (IMPORTANT!)**
**Find this section at the very top of the code:**
```pinescript
strategy("Confluence Pro V3 - Crypto Volatility Optimized", 
         overlay=true, 
         initial_capital=10000, 
         default_qty_type=strategy.percent_of_equity,
         default_qty_value=100,
         commission_type=strategy.commission.percent,    // ← CHANGE THIS LINE
         commission_value=0.1)                           // ← CHANGE THIS LINE
```

**REPLACE those 2 lines with:**
```pinescript
         commission_type=strategy.commission.cash_per_contract,
         commission_value=0.00007)  // This = ~7 pips spread for EUR/USD
```

**Commission by Broker Type:**
- ECN Broker: 0.00005 (5 pips)
- Standard Broker: 0.0001 (10 pips)
- High spread broker: 0.00015 (15 pips)

*Check your broker's typical EUR/USD spread and use that number*

### **STEP 4: Enable Forex Trading Sessions**
**Find this code section (around line 50-60):**
```pinescript
// Dead Zone Filter (2AM-6AM UTC = Low Liquidity)
currentHour = hour(time, "UTC")
isDeadZone = currentHour >= 2 and currentHour < 6
timeOK = not useVolumeFilter or not isDeadZone
```

**REPLACE with this:**
```pinescript
// Forex Session Filter
londonSession = hour(time, "GMT") >= 7 and hour(time, "GMT") < 16
nySession = hour(time, "GMT") >= 12 and hour(time, "GMT") < 21
londonNYOverlap = hour(time, "GMT") >= 12 and hour(time, "GMT") < 16

// Trade only during London/NY overlap (best liquidity)
timeOK = not useVolumeFilter or londonNYOverlap
```

### **STEP 5: Disable Crypto Volume Filter**
**Find this section (around line 60-70):**
```pinescript
// Volume Analysis (Crypto-Specific)
vol24hAvg = ta.sma(volume, 24)
currentVolRatio = volume / vol24hAvg
volumeOK = not useVolumeFilter or currentVolRatio >= minVolumeMultiple
```

**CHANGE the last line to:**
```pinescript
volumeOK = true  // Always true for forex (volume unreliable)
```

## ✅ **FINAL FOREX CHECKLIST**
```
□ I changed commission to cash_per_contract
□ I changed commission value to 0.00007
□ I adjusted ATR multiplier for my pair
□ I adjusted TP ratio for my pair
□ I enabled session filter (London/NY)
□ I disabled crypto volume filter
□ I backtested on 5+ years of data
□ My backtest shows 50%+ win rate
□ My backtest shows 1.5:1+ R:R
□ I understand what each parameter does
□ I have $500+ to start (minimum)
□ I will paper trade for 30 days first
□ I will start with 0.5% risk per trade
□ I will only trade EUR/USD initially
□ I have read my broker's spread/commission
```
**If you checked ALL boxes → You're ready!**
---
**version: 2 - No repainting
```vb
   //@version=5
strategy("Confluence Pro V3 - Comprehensive Fix", 
         overlay=true, 
         initial_capital=10000, 
         default_qty_type=strategy.percent_of_equity,
         default_qty_value=100,
         commission_type=strategy.commission.percent,
         commission_value=0.1,
         slippage=3)

// ========================================
// 🚀 INPUTS
// ========================================
// Risk Management
riskPercent = input.float(1.5, "Risk % Per Trade", minval=0.5, maxval=3.0, group="💰 Risk")
atrMultiplier = input.float(3.5, "ATR Stop Multiplier", minval=2.0, maxval=5.0, group="💰 Risk")
tpRiskRatio = input.float(2.0, "TP R:R Ratio", minval=1.2, maxval=3.0, group="💰 Risk")

// 🆕 Position Sizing Safety
maxPositionSize = input.float(40.0, "Max Position %", minval=10.0, maxval=60.0, group="💰 Risk")
minATRFloor = input.float(0.5, "Min ATR Floor %", minval=0.1, maxval=2.0, group="💰 Risk")
volSqueezeRiskReduction = input.bool(true, "Reduce Risk in Squeezes", group="💰 Risk")

usePartialTP = input.bool(true, "Partial Profit Taking", group="💰 Risk")
partialTPLevel = input.float(1.3, "Take 50% at X R", minval=1.0, maxval=2.5, group="💰 Risk")
useBreakevenStop = input.bool(true, "Breakeven at 1.5R", group="💰 Risk")
useTrailingStop = input.bool(true, "Trailing Stop", group="💰 Risk")
trailActivation = input.float(1.5, "Trail at X R", minval=1.0, maxval=3.0, group="💰 Risk")
trailOffset = input.float(2.0, "Trail Offset", minval=1.0, maxval=4.0, group="💰 Risk")

// Strategy
confluenceThreshold = input.int(80, "Min Score %", minval=50, maxval=95, group="⚙️ Strategy")
useRegimeFilter = input.bool(true, "Regime Filter", group="⚙️ Strategy")
useMandatoryTrend = input.bool(true, "Mandatory Trend", group="⚙️ Strategy")
lockRegimeOnSignal = input.bool(true, "Lock Regime on Signal", group="⚙️ Strategy")
useStrictStructure = input.bool(true, "Anti-Repainting", group="⚙️ Strategy")
useVolumeConfirmation = input.bool(true, "Volume Confirm", group="⚙️ Strategy")
useVolumeFilter = input.bool(true, "Avoid Dead Hours", group="⚙️ Strategy")
minVolumeMultiple = input.float(0.7, "Min Volume", minval=0.3, maxval=1.5, group="⚙️ Strategy")

// Regime
trendingADX = input.int(22, "Trending ADX", minval=15, maxval=35, group="📊 Regime")
rangingADX = input.int(18, "Ranging ADX", minval=10, maxval=25, group="📊 Regime")
useAdaptiveVolThreshold = input.bool(true, "Adaptive Vol Threshold", group="📊 Regime")
requireVolExpansion = input.bool(true, "Require Vol Expansion", group="📊 Regime")

// ========================================
// VOLATILITY REGIME & ATR SNAPSHOT
// ========================================
atr = ta.atr(14)
atrLongTerm = ta.sma(atr, 100)
atrPriceRatio = (atr / close) * 100

isLowVol = atrPriceRatio < 1.0
isMedVol = atrPriceRatio >= 1.0 and atrPriceRatio < 2.5
isHighVol = atrPriceRatio >= 2.5

// 🆕 Pre-entry ATR snapshot
var float atrSnapshot = na
if bar_index > 0
    atrSnapshot := atr[1]

// ========================================
// MARKET STRUCTURE
// ========================================
[diplus, diminus, adx] = ta.dmi(14, 14)
isTrending = adx > trendingADX
isRanging = adx < rangingADX
isTransition = not isTrending and not isRanging

// ========================================
// VOLUME ANALYSIS
// ========================================
vol24hAvg = ta.sma(volume, 24)
currentVolRatio = volume / vol24hAvg
volumeOK = not useVolumeFilter or currentVolRatio >= minVolumeMultiple

currentHour = hour(time, "UTC")
isDeadZone = currentHour >= 2 and currentHour < 6
timeOK = not useVolumeFilter or not isDeadZone

// ========================================
// CATEGORY 1: TREND
// ========================================
ema20 = ta.ema(close, 20)
ema50 = ta.ema(close, 50)
ema200 = ta.ema(close, 200)

bull_trend1 = close > ema20 and ema20 > ema50 and ema50 > ema200
bear_trend1 = close < ema20 and ema20 < ema50 and ema50 < ema200

bull_trend2 = diplus > diminus and adx > rangingADX
bear_trend2 = diminus > diplus and adx > rangingADX

// ========================================
// CATEGORY 2: MOMENTUM
// ========================================
rsi = ta.rsi(close, 14)
bull_momentum1 = rsi > 55 and rsi < 75
bear_momentum1 = rsi < 45 and rsi > 25

[macdLine, signalLine, macdHist] = ta.macd(close, 12, 26, 9)
histRising = macdHist > macdHist[1]
histFalling = macdHist < macdHist[1]
bull_momentum2 = macdLine > signalLine and histRising
bear_momentum2 = macdLine < signalLine and histFalling

// ========================================
// CATEGORY 3: VOLATILITY
// ========================================
[bb_mid, bb_upper, bb_lower] = ta.bb(close, 20, 2.5)
bbWidth = (bb_upper - bb_lower) / bb_mid
bbExpanding = bbWidth > ta.sma(bbWidth, 20)

bull_volatility1 = close > bb_mid and (not requireVolExpansion or bbExpanding)
bear_volatility1 = close < bb_mid and (not requireVolExpansion or bbExpanding)

// 🆕 Adaptive ATR Rising
atrStdDev = ta.stdev(atr, 20)
atrSMA = ta.sma(atr, 20)

atrRising = useAdaptiveVolThreshold ? 
     atr > atrSMA + (atrStdDev * 1.5) :
     atr > atrSMA * 1.10

bull_volatility2 = close > close[1] and atrRising
bear_volatility2 = close < close[1] and atrRising

// ========================================
// CATEGORY 4: MEAN REVERSION (🆕 FIXED)
// ========================================
// 🆕 Corrected Stochastic RSI
rsi_high = ta.highest(rsi, 14)
rsi_low = ta.lowest(rsi, 14)
stochRSI_K = rsi_high != rsi_low ? ((rsi - rsi_low) / (rsi_high - rsi_low)) * 100 : 50
stochRSI_D = ta.sma(stochRSI_K, 3)

bull_reversion1 = ta.crossover(stochRSI_K, stochRSI_D) and stochRSI_K < 80
bear_reversion1 = ta.crossunder(stochRSI_K, stochRSI_D) and stochRSI_K > 20

cci = ta.cci(close, 20)
bull_reversion2 = cci > 0 and cci < 150
bear_reversion2 = cci < 0 and cci > -150

// ========================================
// CATEGORY 5: STRUCTURE (🆕 ANTI-REPAINT)
// ========================================
hhll_period = 3
volumeExpansion = volume > vol24hAvg * 1.2

var bool bull_structure = false
var bool bear_structure = false

if useStrictStructure
    highest_high_confirmed = ta.highest(high[1], hhll_period)
    lowest_low_confirmed = ta.lowest(low[1], hhll_period)
    
    bull_structure_base = high[1] >= highest_high_confirmed and low[1] >= lowest_low_confirmed
    bear_structure_base = high[1] <= highest_high_confirmed and low[1] <= lowest_low_confirmed
    
    bull_structure := useVolumeConfirmation ? bull_structure_base and volumeExpansion : bull_structure_base
    bear_structure := useVolumeConfirmation ? bear_structure_base and volumeExpansion : bear_structure_base
else
    highest_high = ta.highest(high, hhll_period)
    lowest_low = ta.lowest(low, hhll_period)
    bull_structure := high >= highest_high[1] and low >= lowest_low[1]
    bear_structure := high <= highest_high[1] and low <= lowest_low[1]

bodySize = math.abs(close - open)
avgBody = ta.sma(bodySize, 20)
wickRatio = bodySize / (high - low)

bull_candle = close > open and bodySize > avgBody * 1.5 and wickRatio > 0.6
bear_candle = close < open and bodySize > avgBody * 1.5 and wickRatio > 0.6

// ========================================
// SCORING (🆕 DYNAMIC NORMALIZATION)
// ========================================
trendWeight = isTrending ? 1.2 : 0.7
momentumWeight = 1.0
volatilityWeight = bbExpanding ? 1.2 : 0.8
reversionWeight = isRanging ? 1.0 : 0.4
structureWeight = 1.1

longScore = 
     (bull_trend1 ? trendWeight : 0) + (bull_trend2 ? trendWeight : 0) +
     (bull_momentum1 ? momentumWeight : 0) + (bull_momentum2 ? momentumWeight : 0) +
     (bull_volatility1 ? volatilityWeight : 0) + (bull_volatility2 ? volatilityWeight : 0) +
     (bull_reversion1 ? reversionWeight : 0) + (bull_reversion2 ? reversionWeight : 0) +
     (bull_structure ? structureWeight : 0) + (bull_candle ? structureWeight : 0)

shortScore = 
     (bear_trend1 ? trendWeight : 0) + (bear_trend2 ? trendWeight : 0) +
     (bear_momentum1 ? momentumWeight : 0) + (bear_momentum2 ? momentumWeight : 0) +
     (bear_volatility1 ? volatilityWeight : 0) + (bear_volatility2 ? volatilityWeight : 0) +
     (bear_reversion1 ? reversionWeight : 0) + (bear_reversion2 ? reversionWeight : 0) +
     (bear_structure ? structureWeight : 0) + (bear_candle ? structureWeight : 0)

maxPossibleScore = (trendWeight + momentumWeight + volatilityWeight + reversionWeight + structureWeight) * 2

normalizedLongScore = maxPossibleScore > 0 ? (longScore / maxPossibleScore) * 100 : 0
normalizedShortScore = maxPossibleScore > 0 ? (shortScore / maxPossibleScore) * 100 : 0

// ========================================
// ENTRY FILTERS
// ========================================
mandatoryTrend = not useMandatoryTrend or (bull_trend1 or bear_trend1)
regimeOK = not useRegimeFilter or not isTransition
noActivePosition = strategy.position_size == 0

longCondition = normalizedLongScore >= confluenceThreshold and mandatoryTrend and regimeOK and volumeOK and timeOK and noActivePosition
shortCondition = normalizedShortScore >= confluenceThreshold and mandatoryTrend and regimeOK and volumeOK and timeOK and noActivePosition

// ========================================
// POSITION SIZING (🆕 COMPREHENSIVE FIX)
// ========================================
accountEquity = strategy.equity

// 🆕 Squeeze detection & risk reduction
atrCompression = atr / atrLongTerm
isVolatilitySqueeze = atrCompression < 0.7

effectiveRiskPercent = volSqueezeRiskReduction and isVolatilitySqueeze ? riskPercent * 0.6 : riskPercent
riskAmount = accountEquity * (effectiveRiskPercent / 100)

// 🆕 Use snapshot ATR
safeATR = atrSnapshot > 0 ? atrSnapshot : atr

// 🆕 ATR floor
minATRValue = close * (minATRFloor / 100)
enforcedATR = math.max(safeATR, minATRValue)

stopDistance = enforcedATR * atrMultiplier

// 🆕 Hard position cap
positionSizeFromRisk = (riskAmount / stopDistance) * close
positionSizePercent = (positionSizeFromRisk / accountEquity) * 100
safePositionSize = math.min(positionSizePercent, maxPositionSize)

// ========================================
// TRADE MANAGEMENT
// ========================================
var float entryPrice = na
var float longStopPrice = na
var float longTPPrice = na
var float shortStopPrice = na
var float shortTPPrice = na
var bool partialTPTaken = false
var bool breakEvenSet = false
var float lockedStopDistance = na

if longCondition
    entryPrice := close
    lockedStopDistance := stopDistance
    longStopPrice := close - stopDistance
    longTPPrice := close + (stopDistance * tpRiskRatio)
    partialTPTaken := false
    breakEvenSet := false
    strategy.entry("Long", strategy.long, qty=safePositionSize)

if shortCondition
    entryPrice := close
    lockedStopDistance := stopDistance
    shortStopPrice := close + stopDistance
    shortTPPrice := close - (stopDistance * tpRiskRatio)
    partialTPTaken := false
    breakEvenSet := false
    strategy.entry("Short", strategy.short, qty=safePositionSize)

if strategy.position_size > 0
    currentProfit = (close - entryPrice) / lockedStopDistance
    
    if usePartialTP and not partialTPTaken and currentProfit >= partialTPLevel
        strategy.close("Long", qty_percent=50, comment="Partial TP")
        partialTPTaken := true
    
    if useBreakevenStop and not breakEvenSet and currentProfit >= 1.5
        longStopPrice := entryPrice + (enforcedATR * 0.1)
        breakEvenSet := true
    
    if useTrailingStop and currentProfit >= trailActivation
        trailStop = close - (enforcedATR * trailOffset)
        longStopPrice := math.max(longStopPrice, trailStop)
    
    strategy.exit("Exit Long", "Long", stop=longStopPrice, limit=longTPPrice)

if strategy.position_size < 0
    currentProfit = (entryPrice - close) / lockedStopDistance
    
    if usePartialTP and not partialTPTaken and currentProfit >= partialTPLevel
        strategy.close("Short", qty_percent=50, comment="Partial TP")
        partialTPTaken := true
    
    if useBreakevenStop and not breakEvenSet and currentProfit >= 1.5
        shortStopPrice := entryPrice - (enforcedATR * 0.1)
        breakEvenSet := true
    
    if useTrailingStop and currentProfit >= trailActivation
        trailStop = close + (enforcedATR * trailOffset)
        shortStopPrice := math.min(shortStopPrice, trailStop)
    
    strategy.exit("Exit Short", "Short", stop=shortStopPrice, limit=shortTPPrice)

// ========================================
// VISUALS
// ========================================
plot(ema20, color=color.new(color.yellow, 0), linewidth=1)
plot(ema50, color=color.new(color.orange, 0), linewidth=2)
plot(ema200, color=color.new(color.gray, 40), linewidth=3)

p1 = plot(bb_upper, color=color.new(color.blue, 70))
p2 = plot(bb_lower, color=color.new(color.blue, 70))
fill(p1, p2, color=color.new(color.blue, 93))

plotshape(longCondition, style=shape.triangleup, location=location.belowbar, 
         color=color.new(color.lime, 0), size=size.normal)
plotshape(shortCondition, style=shape.triangledown, location=location.abovebar, 
         color=color.new(color.red, 0), size=size.normal)

plot(strategy.position_size > 0 ? longStopPrice : na, color=color.new(color.red, 0), 
     style=plot.style_linebr, linewidth=2)
plot(strategy.position_size > 0 ? longTPPrice : na, color=color.new(color.lime, 0), 
     style=plot.style_linebr, linewidth=2)
plot(strategy.position_size < 0 ? shortStopPrice : na, color=color.new(color.red, 0), 
     style=plot.style_linebr, linewidth=2)
plot(strategy.position_size < 0 ? shortTPPrice : na, color=color.new(color.lime, 0), 
     style=plot.style_linebr, linewidth=2)

// ========================================
// DASHBOARD
// ========================================
var table dash = table.new(position.top_right, 3, 10, bgcolor=color.new(#000000, 85), 
                           border_width=2, border_color=color.new(color.gray, 50))

if barstate.islast
    table.cell(dash, 0, 0, "🚀 FIXED V3", text_color=color.white, bgcolor=color.new(#1E3A8A, 60))
    table.merge_cells(dash, 0, 0, 2, 0)
    
    // Vol Regime
    volText = isLowVol ? "LOW" : isMedVol ? "MED" : "HIGH"
    volColor = isLowVol ? color.blue : isMedVol ? color.yellow : color.red
    table.cell(dash, 0, 1, "Vol", text_color=color.gray, text_size=size.small)
    table.cell(dash, 1, 1, volText, text_color=volColor, text_size=size.small)
    table.cell(dash, 2, 1, isVolatilitySqueeze ? "⚠️ SQZ" : "✓", 
              text_color=isVolatilitySqueeze ? color.purple : color.lime, text_size=size.small)
    
    // Regime
    regText = isTrending ? "TREND" : isRanging ? "RANGE" : "TRANS"
    regColor = isTrending ? color.lime : isRanging ? color.orange : color.yellow
    table.cell(dash, 0, 2, "Regime", text_color=color.gray, text_size=size.small)
    table.cell(dash, 1, 2, regText, text_color=regColor, text_size=size.small)
    table.cell(dash, 2, 2, str.tostring(math.round(adx)), text_color=color.white, text_size=size.small)
    
    // Scores
    table.cell(dash, 0, 3, "🐂", text_color=color.lime, text_size=size.small)
    table.cell(dash, 1, 3, str.tostring(math.round(normalizedLongScore)) + "%", 
              text_color=color.lime, text_size=size.normal,
              bgcolor=normalizedLongScore >= confluenceThreshold ? color.new(color.green, 80) : na)
    table.cell(dash, 2, 3, normalizedLongScore >= confluenceThreshold ? "✓" : "✗", 
              text_color=normalizedLongScore >= confluenceThreshold ? color.lime : color.gray)
    
    table.cell(dash, 0, 4, "🐻", text_color=color.red, text_size=size.small)
    table.cell(dash, 1, 4, str.tostring(math.round(normalizedShortScore)) + "%", 
              text_color=color.red, text_size=size.normal,
              bgcolor=normalizedShortScore >= confluenceThreshold ? color.new(color.red, 80) : na)
    table.cell(dash, 2, 4, normalizedShortScore >= confluenceThreshold ? "✓" : "✗", 
              text_color=normalizedShortScore >= confluenceThreshold ? color.red : color.gray)
    
    // Position
    posText = strategy.position_size > 0 ? "LONG" : strategy.position_size < 0 ? "SHORT" : "NONE"
    posColor = strategy.position_size > 0 ? color.lime : strategy.position_size < 0 ? color.red : color.gray
    table.cell(dash, 0, 5, "Position", text_color=color.gray, text_size=size.small)
    table.cell(dash, 1, 5, posText, text_color=posColor, text_size=size.normal)
    table.merge_cells(dash, 1, 5, 2, 5)
    
    // Position Size
    table.cell(dash, 0, 6, "Size", text_color=color.gray, text_size=size.small)
    table.cell(dash, 1, 6, str.tostring(math.round(safePositionSize)) + "%", 
              text_color=color.white, text_size=size.small)
    table.cell(dash, 2, 6, "Max:" + str.tostring(math.round(maxPositionSize)), 
              text_color=color.yellow, text_size=size.small)
    
    // Risk
    table.cell(dash, 0, 7, "Risk", text_color=color.gray, text_size=size.small)
    table.cell(dash, 1, 7, str.tostring(effectiveRiskPercent) + "%", 
              text_color=color.white, text_size=size.small)
    table.cell(dash, 2, 7, "$" + str.tostring(math.round(riskAmount)), 
              text_color=color.white, text_size=size.small)
    
    // P&L
    if strategy.position_size != 0
        pnl = strategy.position_size > 0 ? (close - entryPrice) : (entryPrice - close)
        pnlPct = (pnl / entryPrice) * 100
        pnlColor = pnl > 0 ? color.lime : color.red
        
        table.cell(dash, 0, 8, "P&L", text_color=color.gray, text_size=size.small)
        table.cell(dash, 1, 8, str.tostring(math.round(pnlPct, 2)) + "%", 
                  text_color=pnlColor, text_size=size.normal)
        table.cell(dash, 2, 8, "$" + str.tostring(math.round(pnl)), 
                  text_color=pnlColor, text_size=size.small)
    
    // Exit Status
    if strategy.position_size != 0
        exitText = partialTPTaken ? "50% OUT" : breakEvenSet ? "BE SET" : "ACTIVE"
        exitColor = partialTPTaken ? color.lime : breakEvenSet ? color.yellow : color.white
        table.cell(dash, 0, 9, "Status", text_color=color.gray, text_size=size.small)
        table.cell(dash, 1, 9, exitText, text_color=exitColor, text_size=size.small)
        table.merge_cells(dash, 1, 9, 2, 9)
```
---
---
**Using different indicators easily**
```pine
    # Trading Strategy (Not Indicator)
    - Build a strategy that uses multiple indicator to scan the market (atleat 10 indicators)
    - The indicator used should be indicators that look at the market through different lenses (not doubling up on same data). e.g **Price Action**, **Momentum**, **Volume**, **Volatility**, e.tc.
    - Pine script at as the gatekeeper and analyzes the indicators through a score based logic. If the score hits threshold (e.g., 7/10), the signal triggers  
    - Either one or two indicators must be mandatory (for extra protection)
    
    ### 1. The "Multi-Lens" Indicator Selection (Each category- 2 indicators)
    
    To ensure we aren't just doubling up on the same data, we will categorize our 10 indicators:
    
    | Category | Indicators Used | Data Source |
    | --- | --- | --- |
    | **Trend** | EMA 200, SuperTrend | Price Path |
    | **Momentum** | RSI, MFI | Speed vs. Flow |
    | **Volatility** | Bollinger Bands, ATR | Price Expansion |
    | **Volume** | On-Balance Volume (OBV), Volume Oscillator | Buying/Selling Pressure |
    | **Mean Reversion** | Stochastic, CCI | Overextended Levels |
    
    ### 2. Strategy Logic: The Confluence Filter
    
    
    The script acts as a "Gatekeeper." It will only plot a **Long** signal if at least 7 out of 10 indicators are bullish, and a **Short** signal if at least 7 are bearish.
    
    We will use a **Dynamic Stop Loss (SL)** based on the ATR (Average True Range), which adjusts to current market volatility.
    
    
    ### 3. Pine Script Template (v5)
    
    SHould be structured in a way that you can easily toggle the **"threshold/ "Minimum Confluence"** requirement.
    
    **Example of pinescript logic**
    ```pinescript
        //@version=5
    strategy("10-Indicator Confluence Pro", overlay=true, initial_capital=1000, default_qty_type=strategy.percent_of_equity, default_qty_value=100)
    
    // --- INPUTS ---
    confluenceThreshold = input.int(7, "Min Score for Signal", minval=1, maxval=10)
    useEmaFilter        = input.bool(true, "Mandatory EMA 200 Trend Filter")
    minAdx              = input.int(20, "Min ADX (Volatility Filter)", minval=0)
    atrMultiplier       = input.float(1.5, "ATR Stop Loss Multiplier")
    tpMultiplier        = input.float(3.0, "ATR Take Profit Multiplier")
    
    // --- 1. TREND (Price Path) ---
    ema200 = ta.ema(close, 200)
    bull_1 = close > ema200
    bear_1 = close < ema200
    
    // --- 2. MOMENTUM (RSI) ---
    rsi = ta.rsi(close, 14)
    bull_2 = rsi > 50
    bear_2 = rsi < 50
    
    // --- 3. VOLUME FLOW (MFI) ---
    mfi = ta.mfi(hlc3, 14)
    bull_3 = mfi > 50
    bear_3 = mfi < 50
    
    // --- 4. VOLATILITY (Bollinger Bands) ---
    [bb_mid, bb_upper, bb_lower] = ta.bb(close, 20, 2)
    bull_4 = close > bb_mid
    bear_4 = close < bb_mid
    
    // --- 5. ACCUMULATION (OBV) ---
    obv = ta.obv
    bull_5 = ta.change(obv) > 0
    bear_5 = ta.change(obv) < 0
    
    // --- 6. MEAN REVERSION (Stoch) ---
    stochK = ta.stoch(close, high, low, 14)
    stochD = ta.sma(stochK, 3) 
    bull_6 = stochK > stochD
    bear_6 = stochK < stochD
    
    // --- 7. TREND STRENGTH (ADX) ---
    [diplus, diminus, adx] = ta.dmi(14, 14)
    bull_7 = diplus > diminus
    bear_7 = diminus > diplus
    
    // --- 8. CYCLES (CCI) ---
    cci = ta.cci(close, 20)
    bull_8 = cci > 0
    bear_8 = cci < 0
    
    // --- 9. VOLUME MOMENTUM (Volume Osc) ---
    volOsc = ta.ema(volume, 5) - ta.ema(volume, 10)
    bull_9 = volOsc > 0
    bear_9 = volOsc < 0
    
    // --- 10. PSYCHOLOGY (Rate of Change) ---
    roc = ta.roc(close, 9)
    bull_10 = roc > 0
    bear_10 = roc < 0
    
    // --- CONFLUENCE CALCULATION ---
    longScore = (bull_1 ? 1:0) + (bull_2 ? 1:0) + (bull_3 ? 1:0) + (bull_4 ? 1:0) + (bull_5 ? 1:0) + (bull_6 ? 1:0) + (bull_7 ? 1:0) + (bull_8 ? 1:0) + (bull_9 ? 1:0) + (bull_10 ? 1:0)
    shortScore = (bear_1 ? 1:0) + (bear_2 ? 1:0) + (bear_3 ? 1:0) + (bear_4 ? 1:0) + (bear_5 ? 1:0) + (bear_6 ? 1:0) + (bear_7 ? 1:0) + (bear_8 ? 1:0) + (bear_9 ? 1:0) + (bear_10 ? 1:0)
    
    // --- ENTRY FILTERS ---
    // Price must be on correct side of EMA if filter is ON, and ADX must be high enough
    longFilter  = (not useEmaFilter or bull_1) and (adx >= minAdx)
    shortFilter = (not useEmaFilter or bear_1) and (adx >= minAdx)
    
    // --- EXIT LOGIC (ATR) ---
    atr = ta.atr(14)
    longStop = close - (atr * atrMultiplier)
    longTP   = close + (atr * tpMultiplier)
    shortStop = close + (atr * atrMultiplier)
    shortTP   = close - (atr * tpMultiplier)
    
    // --- EXECUTION ---
    if (longScore >= confluenceThreshold and longFilter)
        strategy.entry("Long", strategy.long)
        strategy.exit("Exit Long", "Long", stop=longStop, limit=longTP)
    
    if (shortScore >= confluenceThreshold and shortFilter)
        strategy.entry("Short", strategy.short)
        strategy.exit("Exit Short", "Short", stop=shortStop, limit=shortTP)
    
    // --- VISUALS ---
    plot(ema200, color=color.new(color.gray, 50), title="EMA 200 Trend")
    plotshape(longScore >= confluenceThreshold and longFilter, style=shape.triangleup, location=location.belowbar, color=color.green, size=size.small, title="Long Signal")
    plotshape(shortScore >= confluenceThreshold and shortFilter, style=shape.triangledown, location=location.abovebar, color=color.red, size=size.small, title="Short Signal")
    
    // --- DASHBOARD TABLE ---
    var table scoreBoard = table.new(position.bottom_right, 2, 2, bgcolor=color.new(color.black, 80), border_width=1, border_color=color.gray)
    if barstate.islast
        table.cell(scoreBoard, 0, 0, "Bull Score", text_color=color.green, text_size=size.small)
        table.cell(scoreBoard, 1, 0, str.tostring(longScore) + "/10", text_color=color.green, text_size=size.small)
        table.cell(scoreBoard, 0, 1, "Bear Score", text_color=color.red, text_size=size.small)
        table.cell(scoreBoard, 1, 1, str.tostring(shortScore) + "/10", text_color=color.red, text_size=size.small)
    ```
```
---
---
**Pullback trading strateggy**  
```pinescript
    # 📋 PULLBACK TRADING STRATEGY - PINE SCRIPT v6 SPECIFICATION
    
    ## 🎯 STRATEGY OVERVIEW
    Create a **TradingView Pine Script v6 strategy** (not an indicator) that identifies **pullback-based entry and exit signals** using a structured approach: **Market Regime → Context → Setup → Trigger → Confirmation**.
    
    ### Core Requirements
    - Evaluate signals on every confirmed historical bar using only current and past data
    - No repainting logic, no pivot lookahead, no future bar references
    - Add On/Off toggle (boolean input) for **each optional filter**
    - `overlay = true`
    
    ---
    
    ## 📐 CORE INPUTS & VARIABLES
    
    ```pinescript
    // Length inputs
    emaFastLen = input.int(20, "Fast EMA Length", minval=5, maxval=100)
    emaSlowLen = input.int(50, "Slow EMA Length", minval=10, maxval=200)
    rsiLen = input.int(14, "RSI Length", minval=5, maxval=50)
    atrLen = input.int(14, "ATR Length", minval=5, maxval=50)
    pullbackBars = input.int(5, "Pullback Lookback Bars", minval=3, maxval=20)
    
    // Filter toggles (each can be turned on/off)
    useTrendFilter = input.bool(true, "Use Trend Filter")
    useEmaZoneFilter = input.bool(true, "Use EMA Support/Resistance Zone")
    useRsiFilter = input.bool(true, "Use RSI Setup Filter")
    useDivergenceFilter = input.bool(false, "Use Momentum Divergence")
    useRejectionFilter = input.bool(true, "Use Rejection Candle")
    useVolumeFilter = input.bool(false, "Use Volume Confirmation")
    useVolatilityFilter = input.bool(true, "Use Volatility Filter")
    useHTFFilter = input.bool(false, "Use Higher Timeframe Alignment")
    
    // Core calculations
    emaFast = ta.ema(close, emaFastLen)
    emaSlow = ta.ema(close, emaSlowLen)
    atr = ta.atr(atrLen)
    rsi = ta.rsi(close, rsiLen)
    ```
    
    ---
    
    ## 1️⃣ MARKET REGIME FILTER (Context - Mandatory Foundation)
    
    **Purpose:** Defines whether we're in a tradeable trend environment. This is a **state**, not an entry signal.
    
    ### 1.1 EMA Alignment
    ```pinescript
    emaAlignedUp = emaFast > emaSlow
    emaAlignedDown = emaFast < emaSlow
    ```
    
    ### 1.2 Trend Persistence (Slope Confirmation)
    **Ensures EMA is actually trending, not just randomly crossed**
    ```pinescript
    emaSlopePercent = (emaSlow - emaSlow[10]) / emaSlow[10] * 100
    emaSlowRising = emaSlopePercent >= 0.15  // 0.15% rise over 10 bars
    emaSlowFalling = emaSlopePercent <= -0.15  // 0.15% fall over 10 bars
    ```
    
    ### 1.3 Price Position (Acceptance)
    ```pinescript
    priceAcceptedAbove = close >= emaSlow
    priceAcceptedBelow = close <= emaSlow
    ```
    
    ### 1.4 EMA Separation (Anti-Chop Filter)
    **Prevents trading in tight consolidations**
    ```pinescript
    emaSeparationPercent = math.abs(emaFast - emaSlow) / emaSlow * 100
    trendStrongUp = (emaFast - emaSlow) / emaSlow >= 0.003  // 0.3% separation minimum
    trendStrongDown = (emaSlow - emaFast) / emaSlow >= 0.003
    ```
    
    ### 1.5 Final Trend Qualification
    ```pinescript
    upTrend = emaAlignedUp and emaSlowRising and priceAcceptedAbove and trendStrongUp
    downTrend = emaAlignedDown and emaSlowFalling and priceAcceptedBelow and trendStrongDown
    ```
    
    📌 **Critical:** This only determines **eligibility** to look for trades, not entry timing.
    
    ---
    
    ## 2️⃣ CONTEXT: VALID PULLBACK LOCATION
    
    **Purpose:** Confirms price has pulled back to a logical support/resistance zone within the trend.
    
    ### 2.1 Prior Expansion Detection
    **Ensures there was a trend move before the pullback**
    ```pinescript
    // For longs: confirm price extended above EMA before pulling back
    recentHigh = ta.highest(high, 10)
    hadExpansionUp = (recentHigh - emaFast) >= atr * 0.6
    
    // For shorts: confirm price extended below EMA before pulling back
    recentLow = ta.lowest(low, 10)
    hadExpansionDown = (emaFast - recentLow) >= atr * 0.6
    ```
    
    ### 2.2 EMA Support/Resistance Zone Definition
    **Creates a buffer zone around the EMAs for pullback detection**
    ```pinescript
    // Long setup: Support zone
    emaZoneHighLong = emaFast + atr * 0.2
    emaZoneLowLong = emaSlow - atr * 0.6
    
    // Short setup: Resistance zone
    emaZoneHighShort = emaSlow + atr * 0.6
    emaZoneLowShort = emaFast - atr * 0.2
    ```
    
    ### 2.3 Pullback Location Check
    **Verifies price actually touched the EMA zone during recent bars**
    ```pinescript
    // For longs: Recent low must have touched support zone
    pullbackLowLong = ta.lowest(low, pullbackBars)
    inEmaZoneLong = pullbackLowLong <= emaZoneHighLong and pullbackLowLong >= emaZoneLowLong
    
    // For shorts: Recent high must have touched resistance zone
    pullbackHighShort = ta.highest(high, pullbackBars)
    inEmaZoneShort = pullbackHighShort >= emaZoneLowShort and pullbackHighShort <= emaZoneHighShort
    ```
    
    ### 2.4 Pullback Bounce Confirmation
    **Price must show signs of reversing from the zone, not continuing through it**
    ```pinescript
    // For longs: Price bouncing up from zone
    priceBouncedFromSupport = close > ta.sma(close, 3)
    
    // For shorts: Price bouncing down from zone
    priceBouncedFromResistance = close < ta.sma(close, 3)
    ```
    
    ### 2.5 Pullback Quality (Momentum Filter)
    **Avoids deep momentum sell-offs or blow-off tops**
    ```pinescript
    // For longs: No large bearish candles (panic selling)
    largeBearishCandle = close < open and (open - close) > atr * 0.7
    controlledPullbackLong = not largeBearishCandle
    
    // For shorts: No large bullish candles (panic buying)
    largeBullishCandle = close > open and (close - open) > atr * 0.7
    controlledPullbackShort = not largeBullishCandle
    ```
    
    ### 2.6 Valid Pullback Context State
    ```pinescript
    // Long context
    validPullbackContextLong = upTrend and hadExpansionUp and inEmaZoneLong and priceBouncedFromSupport and controlledPullbackLong
    
    // Short context
    validPullbackContextShort = downTrend and hadExpansionDown and inEmaZoneShort and priceBouncedFromResistance and controlledPullbackShort
    ```
    
    ---
    
    ## 3️⃣ SETUP FILTERS (Optional Quality Enhancers)
    
    ### 3.1 RSI Setup Filter (Adaptive Thresholds - Recommended)
    **Confirms momentum is shifting in favor of trend direction**
    **Adapts to market strength - catches both power pullbacks and standard pullbacks**
    
    ```pinescript
    // Calculate RSI's recent operating range (last 20 bars)
    rsiHigh20 = ta.highest(rsi, 20)
    rsiLow20 = ta.lowest(rsi, 20)
    rsiRange = rsiHigh20 - rsiLow20
    
    // For longs: Define "oversold" as lower 30% of recent RSI range
    rsiOversoldLevel = rsiLow20 + rsiRange * 0.3
    rsiWasLow = rsi[1] <= rsiOversoldLevel or rsi[2] <= rsiOversoldLevel
    rsiTurningUp = rsi > rsi[1] and rsi[1] > rsi[2]  // 2 consecutive rising bars
    rsiAboveRecent = rsi > ta.sma(rsi, 3)  // Above 3-bar average
    rsiSetupLong = rsiWasLow and rsiTurningUp and rsiAboveRecent
    
    // For shorts: Define "overbought" as upper 30% of recent RSI range
    rsiOverboughtLevel = rsiHigh20 - rsiRange * 0.3
    rsiWasHigh = rsi[1] >= rsiOverboughtLevel or rsi[2] >= rsiOverboughtLevel
    rsiTurningDown = rsi < rsi[1] and rsi[1] < rsi[2]  // 2 consecutive falling bars
    rsiBelowRecent = rsi < ta.sma(rsi, 3)  // Below 3-bar average
    rsiSetupShort = rsiWasHigh and rsiTurningDown and rsiBelowRecent
    ```
    
    **Why Adaptive Thresholds:**
    - ✅ **Strong uptrends** (RSI range 45-75): oversold ≈ 52 - catches power pullbacks where RSI barely dips
    - ✅ **Weak uptrends** (RSI range 30-60): oversold ≈ 39 - catches standard pullbacks
    - ✅ **Ranging markets** (RSI range 25-75): oversold ≈ 40 - similar to static threshold
    - ✅ **Automatic adjustment** - no manual intervention needed across different market regimes
    
    ### 3.2 Momentum Divergence Filter (Optional - Zero Delay)
    **Detects divergence between price and RSI using fixed lookback comparison (no pivot lookahead)**
    
    ```pinescript
    // Adjustable lookback for divergence detection
    divLookback = input.int(10, "Divergence Lookback Bars", minval=5, maxval=30)
    
    // For longs: Bullish divergence (price lower low, RSI higher low)
    recentLowPrice = ta.lowest(low, 3)  // Recent 3-bar low
    priorLowPrice = ta.lowest(low[divLookback - 2], 3)  // Low from ~lookback bars ago
    recentLowRSI = ta.lowest(rsi, 3)  // Recent 3-bar RSI low
    priorLowRSI = ta.lowest(rsi[divLookback - 2], 3)  // RSI from ~lookback bars ago
    
    // Bullish divergence: price making lower low, RSI making higher low (with RSI filter)
    bullishDiv = recentLowPrice < priorLowPrice and recentLowRSI > priorLowRSI and rsi < 50
    
    // For shorts: Bearish divergence (price higher high, RSI lower high)
    recentHighPrice = ta.highest(high, 3)  // Recent 3-bar high
    priorHighPrice = ta.highest(high[divLookback - 2], 3)  // High from ~lookback bars ago
    recentHighRSI = ta.highest(rsi, 3)  // Recent 3-bar RSI high
    priorHighRSI = ta.highest(rsi[divLookback - 2], 3)  // RSI from ~lookback bars ago
    
    // Bearish divergence: price making higher high, RSI making lower high (with RSI filter)
    bearishDiv = recentHighPrice > priorHighPrice and recentHighRSI < priorHighRSI and rsi > 50
    ```
    
    **Why This Approach:**
    - ✅ Zero delay - triggers immediately on current bar
    - ✅ No repainting - uses only confirmed historical data
    - ✅ No lookahead bias - doesn't require future bars for confirmation
    - ✅ Consistent with other filters - all signals appear in real-time
    
    ### 3.3 Volume Filter (Optional)
    **Confirms pullback is not a panic event**
    
    ```pinescript
    avgVolume = ta.sma(volume, 20)
    volumeAcceptable = volume <= avgVolume * 1.5  // Not excessive volume (panic)
    ```
    
    ### 3.4 Volatility Filter (Optional)
    **Avoids dead markets with insufficient movement**
    
    ```pinescript
    avgATR = ta.sma(atr, 20)
    isVolatileEnough = atr > avgATR * 0.7  // At least 70% of recent average volatility
    ```
    
    ### 3.5 Higher Timeframe Alignment (Optional - Advanced)
    - **Ensures trend alignment with larger timeframe.**
    - ⚠️**Disabled by default - reduces signal frequency significantly**
    - **Enable ONLY if you want additional confluence**
    
    ```pinescript
    // HTF timeframe input (default 4H, but user can adjust)
    htfTimeframe = input.timeframe("240", "Higher Timeframe for Trend Filter", group="Filters")
    
    // Request HTF EMA with CRITICAL non-repainting settings
    // Use barmerge.lookahead_off to prevent the strategy from "cheating" by seeing the HTF close early
    htfEma = request.security(syminfo.tickerid, htfTimeframe, ta.ema(close, 50), gaps=barmerge.gaps_off, lookahead=barmerge.lookahead_off)
    
    // Alignment checks using confirmed HTF data only
    alignedWithHTFLong = close > htfEma
    alignedWithHTFShort = close < htfEma
    ```
    **Verification Test: to check if Higher timeframe alignment works correctly**
    ```pinescript
    // Temporary debug: HTF EMA should only change at HTF bar closes
    htfEmaChanged = htfEma != htfEma[1]
    plotshape(htfEmaChanged, "HTF Update", shape.diamond, location.top, color=color.yellow, size=size.tiny)
    // On 1H chart with 4H HTF: Should see diamonds only every 4 hours
    ```
    ---
    
    ## 4️⃣ TRIGGER: REJECTION CANDLE (Price Action Confirmation)
    
    **Purpose:** Identifies the specific candle that shows demand/supply absorption.
    
    ### 4.1 Candle Metrics
    ```pinescript
    body = math.abs(close - open)
    candleRange = high - low
    lowerWick = math.min(open, close) - low
    upperWick = high - math.max(open, close)
    ```
    
    ### 4.2 Rejection Scoring System (0-4 points)
    
    **For Long Entries (Bullish Rejection):**
    ```pinescript
    rejectionScoreLong = 0
    
    // Wick rejection (0-2 points)
    if lowerWick > upperWick * 1.5
        rejectionScoreLong := rejectionScoreLong + 2
    else if lowerWick > upperWick
        rejectionScoreLong := rejectionScoreLong + 1
    
    // Wick significance (0-1 point)
    if lowerWick > atr * 0.4
        rejectionScoreLong := rejectionScoreLong + 1
    
    // Structure rejection: failed breakdown (0-2 points)
    structureRejectLong = low < ta.lowest(low[1], pullbackBars) and close > ta.lowest(low[1], pullbackBars)
    if structureRejectLong
        rejectionScoreLong := rejectionScoreLong + 2
    
    // Strong close in upper range (0-1 point)
    if close >= low + candleRange * 0.6
        rejectionScoreLong := rejectionScoreLong + 1
    
    // Valid rejection requires at least 3 points
    validRejectionLong = rejectionScoreLong >= 3
    ```
    
    **For Short Entries (Bearish Rejection):**
    ```pinescript
    rejectionScoreShort = 0
    
    // Wick rejection (0-2 points)
    if upperWick > lowerWick * 1.5
        rejectionScoreShort := rejectionScoreShort + 2
    else if upperWick > lowerWick
        rejectionScoreShort := rejectionScoreShort + 1
    
    // Wick significance (0-1 point)
    if upperWick > atr * 0.4
        rejectionScoreShort := rejectionScoreShort + 1
    
    // Structure rejection: failed breakout (0-2 points)
    structureRejectShort = high > ta.highest(high[1], pullbackBars) and close < ta.highest(high[1], pullbackBars)
    if structureRejectShort
        rejectionScoreShort := rejectionScoreShort + 2
    
    // Strong close in lower range (0-1 point)
    if close <= high - candleRange * 0.6
        rejectionScoreShort := rejectionScoreShort + 1
    
    // Valid rejection requires at least 3 points
    validRejectionShort = rejectionScoreShort >= 3
    ```
    
    ---
    
    ## 5️⃣ SETUP BAR IDENTIFICATION
    
    **Purpose:** Combines all filters to identify a potential setup bar (not yet an entry).
    
    ### 5.1 Setup Bar Conditions
    ```pinescript
    // Long setup bar
    setupBarLong = validPullbackContextLong and (not useRsiFilter or rsiSetupLong) and (not useDivergenceFilter or bullishDiv) and (not useVolumeFilter or volumeAcceptable) and (not useVolatilityFilter or isVolatileEnough) and (not useHTFFilter or alignedWithHTFLong) and (not useRejectionFilter or validRejectionLong)
    
    // Short setup bar
    setupBarShort = validPullbackContextShort and (not useRsiFilter or rsiSetupShort) and (not useDivergenceFilter or bearishDiv) and (not useVolumeFilter or volumeAcceptable) and (not useVolatilityFilter or isVolatileEnough) and (not useHTFFilter or alignedWithHTFShort) and (not useRejectionFilter or validRejectionShort)
    ```
    
    ### 5.2 Pullback Timer & Invalidation
    **Prevents chasing old setups or broken pullbacks**
    
    ```pinescript
    // Track how long we've been in pullback context
    var int pullbackCounterLong = 0
    var int pullbackCounterShort = 0
    
    // Long pullback tracking
    if setupBarLong
        pullbackCounterLong := 0
    else if validPullbackContextLong
        pullbackCounterLong += 1
    else
        pullbackCounterLong := 0
    
    // Short pullback tracking
    if setupBarShort
        pullbackCounterShort := 0
    else if validPullbackContextShort
        pullbackCounterShort += 1
    else
        pullbackCounterShort := 0
    
    // Invalidation conditions
    pullbackTooLongLong = pullbackCounterLong > 10
    pullbackTooLongShort = pullbackCounterShort > 10
    
    invalidateSetupLong = (close < emaSlow) or (rsi < 35) or pullbackTooLongLong
    invalidateSetupShort = (close > emaSlow) or (rsi > 65) or pullbackTooLongShort
    ```
    
    ---
    
    ## 6️⃣ CONFIRMATION CANDLE (Entry Trigger)
    
    **Purpose:** Waits for the next bar to confirm momentum follow-through before entering.
    
    ### 6.1 Confirmation Logic with Trend State Locking
    **Critical:** Locks the trend state at setup to prevent flickering from invalidating good setups
    
    ```pinescript
    // State variables to track pending confirmation AND locked trend state
    var bool waitingForConfirmationLong = false
    var bool waitingForConfirmationShort = false
    var float confirmationLevelLong = na
    var float confirmationLevelShort = na
    var bool trendWasValidLong = false  // Lock trend state at setup
    var bool trendWasValidShort = false  // Lock trend state at setup
    
    // Long confirmation setup - preserve trend state when setup forms
    if setupBarLong and not waitingForConfirmationLong
        waitingForConfirmationLong := true
        confirmationLevelLong := high  // Must break above setup bar high
        trendWasValidLong := upTrend  // Lock the trend state from setup bar
    
    // Short confirmation setup - preserve trend state when setup forms
    if setupBarShort and not waitingForConfirmationShort
        waitingForConfirmationShort := true
        confirmationLevelShort := low  // Must break below setup bar low
        trendWasValidShort := downTrend  // Lock the trend state from setup bar
    
    // Confirmation triggers (price action only - trend already locked)
    confirmationLong = waitingForConfirmationLong and close > confirmationLevelLong
    confirmationShort = waitingForConfirmationShort and close < confirmationLevelShort
    
    // Reset confirmation states when triggered or invalidated
    if confirmationLong or invalidateSetupLong
        waitingForConfirmationLong := false
        confirmationLevelLong := na
        trendWasValidLong := false  // Reset locked state
    
    if confirmationShort or invalidateSetupShort
        waitingForConfirmationShort := false
        confirmationLevelShort := na
        trendWasValidShort := false  // Reset locked state
    ```
    
    **Why Trend State Locking:**
    - ✅ Prevents missed entries due to temporary trend flickering during confirmation bar
    - ✅ If setup was valid when it formed, it remains valid through confirmation phase
    - ✅ Only invalidation rules (Section 5.2) can cancel the pending setup
    - ✅ Aligns with how traders think: "Setup was good, just waiting for trigger"
    
    ---
    
    ## 7️⃣ FINAL ENTRY CONDITIONS
    
    **Purpose:** Execute trades using locked trend state from setup bar to prevent flickering invalidation
    
    ```pinescript
    // Execute long entry - uses locked trend state from setup bar
    // All filter conditions were already validated in setupBarLong
    // Only confirmation (price breaking setup high) is needed
    longEntry = trendWasValidLong and confirmationLong
    
    // Execute short entry - uses locked trend state from setup bar
    // All filter conditions were already validated in setupBarShort
    // Only confirmation (price breaking setup low) is needed
    shortEntry = trendWasValidShort and confirmationShort
    ```
    
    **Why This Works:**
    - Trend state is captured and locked at setup bar (Section 6.1)
    - Prevents temporary trend flickering from canceling valid setups
    - All other filters already passed in `setupBarLong` / `setupBarShort`
    - Only invalidation rules (Section 5.2) can cancel pending entries
    
    ---
    
    ## 8️⃣ STOP LOSS & TAKE PROFIT
    
    ### 8.1 Stop Loss Calculation
    **Uses recent swing structure, not arbitrary ATR multiples**
    
    ```pinescript
    // Long stop: Below recent swing low with buffer
    swingLowLong = ta.lowest(low, pullbackBars + 2)  // Include setup and confirmation bars
    stopPriceLong = swingLowLong - atr * 0.5  // Buffer
    minStopLong = close * 0.98  // Never more than 2% risk
    finalStopLong = math.max(stopPriceLong, minStopLong)
    
    // Short stop: Above recent swing high with buffer
    swingHighShort = ta.highest(high, pullbackBars + 2)
    stopPriceShort = swingHighShort + atr * 0.5
    maxStopShort = close * 1.02  // Never more than 2% risk
    finalStopShort = math.min(stopPriceShort, maxStopShort)
    ```
    
    ### 8.2 Take Profit Calculation
    **Risk-based R-multiples**
    
    ```pinescript
    // Long targets
    riskAmountLong = close - finalStopLong
    takeProfit1Long = close + riskAmountLong * 2.0  // 2R
    takeProfit2Long = close + riskAmountLong * 3.0  // 3R
    
    // Short targets
    riskAmountShort = finalStopShort - close
    takeProfit1Short = close - riskAmountShort * 2.0  // 2R
    takeProfit2Short = close - riskAmountShort * 3.0  // 3R
    ```
    
    ### 8.3 Strategy Execution
    ```pinescript
    if longEntry
        strategy.entry("Long", strategy.long)
        strategy.exit("Long-Exit", "Long", stop=finalStopLong, limit=takeProfit2Long)
    
    if shortEntry
        strategy.entry("Short", strategy.short)
        strategy.exit("Short-Exit", "Short", stop=finalStopShort, limit=takeProfit2Short)
    ```
    
    ---
    
    ## 9️⃣ VISUAL ELEMENTS
    
    ### 9.1 EMA Plots
    ```pinescript
    plot(emaFast, title="Fast EMA", color=color.blue, linewidth=2)
    plot(emaSlow, title="Slow EMA", color=color.red, linewidth=2)
    ```
    
    ### 9.2 Entry Markers
    ```pinescript
    plotshape(longEntry, title="", style=shape.triangleup, location=location.belowbar, color=color.rgb(110,194,7), size=size.small)
    plotshape(shortEntry, title="", style=shape.triangledown, location=location.abovebar, color=color.rgb(90,14,36), size=size.small)
    ```
    
    ### 9.3 Setup Bar Markers (Optional - for debugging)
    ```pinescript
    plotshape(setupBarLong, title="", style=shape.circle, location=location.belowbar, color=color.new(color.green, 70), size=size.tiny)
    plotshape(setupBarShort, title="", style=shape.circle, location=location.abovebar, color=color.new(color.red, 70), size=size.tiny)
    ```
    
    ---
    
    ## 🔟 ALERTS
    
    ```pinescript
    // Alert when entry signals occur (trigger once per bar)
    alertcondition(longEntry, title="Long Entry Signal", message="Pullback Strategy: LONG entry triggered")
    alertcondition(shortEntry, title="Short Entry Signal", message="Pullback Strategy: SHORT entry triggered")
    
    // Optional: Alert on setup bars (pre-warning)
    alertcondition(setupBarLong, title="Long Setup Detected", message="Pullback Strategy: Long setup bar formed - watch for confirmation")
    alertcondition(setupBarShort, title="Short Setup Detected", message="Pullback Strategy: Short setup bar formed - watch for confirmation")
    ```
    
    ---
    
    ## 📊 IMPLEMENTATION NOTES
    
    ### Code Quality Requirements
    1. **Clear section comments** for each major component
    2. **Descriptive variable names** (avoid single letters except in loops)
    3. **Single-line function calls** (Pine Script requirement)
    4. **No repainting logic** - all calculations use confirmed bar data only
    5. **Efficient calculation** - reuse variables where possible
```
---
---
**Strategy Usage Recommendation** 
```pine
    ### **Currency Pair Suitability:**
    **Best:**
    - EUR/USD (trending, liquid)
    - GBP/USD (volatile, clear trends)
    - AUD/USD (clean pullbacks)
    
    **Moderate:**
    - USD/JPY (respects EMAs well)
    - EUR/GBP (slower, fewer signals)
    
    **Avoid:**
    - Exotic pairs (wide spreads, gaps)
    - Crypto (too volatile for current settings)
    ---
    
    ## 🔧 **Common Issues & Fixes:**
    
    ### **Issue 1: Too Few Signals**
    **Solution:**
    - Reduce `emaSlowLen` to 30-40
    - Disable HTF filter
    - Disable divergence filter
    - Reduce rejection score threshold to 2
    
    ### **Issue 2: Too Many Losses**
    **Solution:**
    - Enable all filters
    - Increase `pullbackBars` to 7-8
    - Increase rejection score threshold to 4
    - Enable HTF filter
    
    ### **Issue 3: Stops Too Tight**
    **Solution:**
    ```pine
    // Change stop calculation:
    stopPriceLong = swingLowLong - atr * 0.5  // Change to 0.8 or 1.0
    minStopLong = close * 0.98  // Change to 0.97 (3% max stop)
    ```
    
    ### **Issue 4: Targets Not Reached**
    **Solution:**
    ```pine
    // Reduce target multipliers:
    takeProfit1Long = close + riskAmountLong * 1.5  // From 2.0
    takeProfit2Long = close + riskAmountLong * 2.5  // From 3.0

    
    ## 📊 **Parameter Adjustment Matrix- If you want to trade high volatilty curency like crypto or Exotic Pairs**
    
    ### **Priority 1: CRITICAL - Must Adjust (Will Break Otherwise)**
    
    | Parameter | Forex Default | Crypto Adjustment | Exotic Forex | Why Critical |
    |-----------|---------------|-------------------|--------------|--------------|
    | **EMA Separation %** | 0.003 (0.3%) | 0.010-0.015 (1-1.5%) | 0.005-0.008 (0.5-0.8%) | Crypto/exotics have wider EMA spreads naturally |
    | **EMA Slope %** | 0.15% over 10 bars | 0.4-0.6% over 10 bars | 0.25-0.35% over 10 bars | Higher volatility = steeper slopes needed for "trend" |
    | **ATR Multipliers** | All 0.3-0.7x | All 1.0-1.5x | All 0.5-1.0x | Wider price swings require larger buffers |
    | **Min Stop %** | 2% (0.98) | 5-8% (0.92-0.95) | 3-5% (0.95-0.97) | Prevent premature stops in volatile assets |
    
    ---
    
    ### **Priority 2: HIGH - Should Adjust (Performance Impact)**
    
    | Parameter | Forex Default | Crypto Adjustment | Exotic Forex | Why Important |
    |-----------|---------------|-------------------|--------------|---------------|
    | **RSI Period** | 14 | 21-28 | 18-21 | Longer period smooths extreme volatility |
    | **EMA Lengths** | 20/50 | 30/75 or 50/100 | 25/60 | Reduce whipsaw from noise |
    | **Pullback Bars** | 5 | 3-4 | 4-5 | Crypto pullbacks are sharper/faster |
    | **Pullback Timer** | 10 bars | 5-7 bars | 8-10 bars | Crypto moves expire faster |
    | **Rejection Score Threshold** | 3/6 | 4/6 | 3-4/6 | Need stronger confirmation in noise |
    | **Volume Filter** | 1.5x average | 2.0-3.0x average | 1.8-2.2x average | Crypto has more volume spikes |
    
    ---
    
    ### **Priority 3: MODERATE - Consider Adjusting (Optimization)**
    
    | Parameter | Forex Default | Crypto Adjustment | Exotic Forex | Why Helpful |
    |-----------|---------------|-------------------|--------------|-------------|
    | **Divergence Lookback** | 10 | 7-8 | 8-10 | Crypto trends shorter-lived |
    | **HTF Timeframe** | 4H (240) | 1D (1D) | 6H (360) | Need higher TF for stability |
    | **Commission %** | 0.1% | 0.2-0.5% | 0.2-0.3% | Crypto fees higher |
    | **R:R Targets** | 2R/3R | 1.5R/2.5R | 2R/3R | Crypto less likely to hit 3R |
    ---
    ```
    ---
```
---
---
**Support Resistance Channels- by LonesomeTheBlue**    
set the indicator to:  
- Pivot Period --> 10
- Loopback Period -->292
- Number of S/R to show -->6
- Maximum Channel Width -->5  
**Quick**  
- RSI set to 14 SMA (overbought/oversold)- when `below 30` enter buy trade/ `above 70` enter sell trade. (when this forms around support/ resistance- possible reversal)
- MACD set to 12,26,9 EMA (for mementum strength/ shifts)- `fast_line crosses upwards` at 0 point enter buy trade/ `crosses downwards` at 0 point enter sell trade
- When marking support/ resistance manually, Only mark zones that led to 50+ pip moves (or 0.5%+ on stocks)
- Alerts and pine script on strategy: 6 below  

---
**pinescript code latest version**  
```pine
//@version=6
strategy("Pullback Trading Strategy", overlay=true, initial_capital=10000, default_qty_type=strategy.percent_of_equity, default_qty_value=100, commission_type=strategy.commission.percent, commission_value=0.1)

// ═══════════════════════════════════════════════════════════════════════════
// 📐 INPUTS & SETTINGS
// ═══════════════════════════════════════════════════════════════════════════

// Core Length Inputs
emaFastLen = input.int(20, "Fast EMA Length", minval=5, maxval=100, group="Core Settings")
emaSlowLen = input.int(50, "Slow EMA Length", minval=10, maxval=200, group="Core Settings")
rsiLen = input.int(14, "RSI Length", minval=5, maxval=50, group="Core Settings")
atrLen = input.int(14, "ATR Length", minval=5, maxval=50, group="Core Settings")
pullbackBars = input.int(5, "Pullback Lookback Bars", minval=3, maxval=20, group="Core Settings")
divLookback = input.int(10, "Divergence Lookback Bars", minval=5, maxval=30, group="Core Settings")

// Filter Toggles
useTrendFilter = input.bool(true, "Use Trend Filter", group="Filters")
useEmaZoneFilter = input.bool(true, "Use EMA Support/Resistance Zone", group="Filters")
useRsiFilter = input.bool(true, "Use RSI Setup Filter", group="Filters")
useDivergenceFilter = input.bool(false, "Use Momentum Divergence", group="Filters")
useRejectionFilter = input.bool(true, "Use Rejection Candle", group="Filters")
useVolumeFilter = input.bool(false, "Use Volume Confirmation", group="Filters")
useVolatilityFilter = input.bool(true, "Use Volatility Filter", group="Filters")
useHTFFilter = input.bool(false, "Use Higher Timeframe Alignment (Advanced)", group="Filters")
htfTimeframe = input.timeframe("240", "Higher Timeframe", group="Filters")

// Core Calculations
emaFast = ta.ema(close, emaFastLen)
emaSlow = ta.ema(close, emaSlowLen)
atr = ta.atr(atrLen)
rsi = ta.rsi(close, rsiLen)

// ═══════════════════════════════════════════════════════════════════════════
// 1️⃣ MARKET REGIME FILTER (Trend Definition)
// ═══════════════════════════════════════════════════════════════════════════

// EMA Alignment
emaAlignedUp = emaFast > emaSlow
emaAlignedDown = emaFast < emaSlow

// Trend Persistence (Slope Confirmation)
emaSlopePercent = (emaSlow - emaSlow[10]) / emaSlow[10] * 100
emaSlowRising = emaSlopePercent >= 0.15
emaSlowFalling = emaSlopePercent <= -0.15

// Price Position
priceAcceptedAbove = close >= emaSlow
priceAcceptedBelow = close <= emaSlow

// EMA Separation (Anti-Chop)
trendStrongUp = (emaFast - emaSlow) / emaSlow >= 0.003
trendStrongDown = (emaSlow - emaFast) / emaSlow >= 0.003

// Final Trend Qualification
upTrend = emaAlignedUp and emaSlowRising and priceAcceptedAbove and trendStrongUp
downTrend = emaAlignedDown and emaSlowFalling and priceAcceptedBelow and trendStrongDown

// ═══════════════════════════════════════════════════════════════════════════
// 2️⃣ CONTEXT: VALID PULLBACK LOCATION
// ═══════════════════════════════════════════════════════════════════════════

// Prior Expansion Detection
recentHigh = ta.highest(high, 10)
hadExpansionUp = (recentHigh - emaFast) >= atr * 0.6
recentLow = ta.lowest(low, 10)
hadExpansionDown = (emaFast - recentLow) >= atr * 0.6

// EMA Zone Definition
emaZoneHighLong = emaFast + atr * 0.2
emaZoneLowLong = emaSlow - atr * 0.6
emaZoneHighShort = emaSlow + atr * 0.6
emaZoneLowShort = emaFast - atr * 0.2

// Pullback Location Check
pullbackLowLong = ta.lowest(low, pullbackBars)
inEmaZoneLong = pullbackLowLong <= emaZoneHighLong and pullbackLowLong >= emaZoneLowLong
pullbackHighShort = ta.highest(high, pullbackBars)
inEmaZoneShort = pullbackHighShort >= emaZoneLowShort and pullbackHighShort <= emaZoneHighShort

// Pullback Bounce Confirmation
priceBouncedFromSupport = close > ta.sma(close, 3)
priceBouncedFromResistance = close < ta.sma(close, 3)

// Pullback Quality (No Panic Moves)
largeBearishCandle = close < open and (open - close) > atr * 0.7
controlledPullbackLong = not largeBearishCandle
largeBullishCandle = close > open and (close - open) > atr * 0.7
controlledPullbackShort = not largeBullishCandle

// Valid Pullback Context State
validPullbackContextLong = upTrend and hadExpansionUp and inEmaZoneLong and priceBouncedFromSupport and controlledPullbackLong
validPullbackContextShort = downTrend and hadExpansionDown and inEmaZoneShort and priceBouncedFromResistance and controlledPullbackShort

// ═══════════════════════════════════════════════════════════════════════════
// 3️⃣ SETUP FILTERS (Optional Quality Enhancers)
// ═══════════════════════════════════════════════════════════════════════════

// 3.1 RSI Setup Filter (Adaptive Thresholds)
rsiHigh20 = ta.highest(rsi, 20)
rsiLow20 = ta.lowest(rsi, 20)
rsiRange = rsiHigh20 - rsiLow20
rsiOversoldLevel = rsiLow20 + rsiRange * 0.3
rsiWasLow = rsi[1] <= rsiOversoldLevel or rsi[2] <= rsiOversoldLevel
rsiTurningUp = rsi > rsi[1] and rsi[1] > rsi[2]
rsiAboveRecent = rsi > ta.sma(rsi, 3)
rsiSetupLong = rsiWasLow and rsiTurningUp and rsiAboveRecent
rsiOverboughtLevel = rsiHigh20 - rsiRange * 0.3
rsiWasHigh = rsi[1] >= rsiOverboughtLevel or rsi[2] >= rsiOverboughtLevel
rsiTurningDown = rsi < rsi[1] and rsi[1] < rsi[2]
rsiBelowRecent = rsi < ta.sma(rsi, 3)
rsiSetupShort = rsiWasHigh and rsiTurningDown and rsiBelowRecent

// 3.2 Momentum Divergence Filter (Zero Delay)
recentLowPrice = ta.lowest(low, 3)
priorLowPrice = ta.lowest(low[divLookback - 2], 3)
recentLowRSI = ta.lowest(rsi, 3)
priorLowRSI = ta.lowest(rsi[divLookback - 2], 3)
bullishDiv = recentLowPrice < priorLowPrice and recentLowRSI > priorLowRSI and rsi < 50
recentHighPrice = ta.highest(high, 3)
priorHighPrice = ta.highest(high[divLookback - 2], 3)
recentHighRSI = ta.highest(rsi, 3)
priorHighRSI = ta.highest(rsi[divLookback - 2], 3)
bearishDiv = recentHighPrice > priorHighPrice and recentHighRSI < priorHighRSI and rsi > 50

// 3.3 Volume Filter
avgVolume = ta.sma(volume, 20)
volumeAcceptable = volume <= avgVolume * 1.5

// 3.4 Volatility Filter
avgATR = ta.sma(atr, 20)
isVolatileEnough = atr > avgATR * 0.7

// 3.5 Higher Timeframe Alignment
htfEma = request.security(syminfo.tickerid, htfTimeframe, ta.ema(close, 50), gaps=barmerge.gaps_off, lookahead=barmerge.lookahead_off)
alignedWithHTFLong = close > htfEma
alignedWithHTFShort = close < htfEma

// ═══════════════════════════════════════════════════════════════════════════
// 4️⃣ TRIGGER: REJECTION CANDLE (Price Action Confirmation)
// ═══════════════════════════════════════════════════════════════════════════

// Candle Metrics
body = math.abs(close - open)
candleRange = high - low
lowerWick = math.min(open, close) - low
upperWick = high - math.max(open, close)

// Long Rejection Scoring (0-6 points, need 3+)
rejectionScoreLong = 0
rejectionScoreLong := rejectionScoreLong + (lowerWick > upperWick * 1.5 ? 2 : (lowerWick > upperWick ? 1 : 0))
rejectionScoreLong := rejectionScoreLong + (lowerWick > atr * 0.4 ? 1 : 0)
structureRejectLong = low < ta.lowest(low[1], pullbackBars) and close > ta.lowest(low[1], pullbackBars)
rejectionScoreLong := rejectionScoreLong + (structureRejectLong ? 2 : 0)
rejectionScoreLong := rejectionScoreLong + (close >= low + candleRange * 0.6 ? 1 : 0)
validRejectionLong = rejectionScoreLong >= 3

// Short Rejection Scoring (0-6 points, need 3+)
rejectionScoreShort = 0
rejectionScoreShort := rejectionScoreShort + (upperWick > lowerWick * 1.5 ? 2 : (upperWick > lowerWick ? 1 : 0))
rejectionScoreShort := rejectionScoreShort + (upperWick > atr * 0.4 ? 1 : 0)
structureRejectShort = high > ta.highest(high[1], pullbackBars) and close < ta.highest(high[1], pullbackBars)
rejectionScoreShort := rejectionScoreShort + (structureRejectShort ? 2 : 0)
rejectionScoreShort := rejectionScoreShort + (close <= high - candleRange * 0.6 ? 1 : 0)
validRejectionShort = rejectionScoreShort >= 3

// ═══════════════════════════════════════════════════════════════════════════
// 5️⃣ SETUP BAR IDENTIFICATION
// ═══════════════════════════════════════════════════════════════════════════

// Combine All Filters for Setup Bar
setupBarLong = validPullbackContextLong and (not useRsiFilter or rsiSetupLong) and (not useDivergenceFilter or bullishDiv) and (not useVolumeFilter or volumeAcceptable) and (not useVolatilityFilter or isVolatileEnough) and (not useHTFFilter or alignedWithHTFLong) and (not useRejectionFilter or validRejectionLong)
setupBarShort = validPullbackContextShort and (not useRsiFilter or rsiSetupShort) and (not useDivergenceFilter or bearishDiv) and (not useVolumeFilter or volumeAcceptable) and (not useVolatilityFilter or isVolatileEnough) and (not useHTFFilter or alignedWithHTFShort) and (not useRejectionFilter or validRejectionShort)

// Pullback Timer & Invalidation
var int pullbackCounterLong = 0
var int pullbackCounterShort = 0
if setupBarLong
    pullbackCounterLong := 0
else if validPullbackContextLong
    pullbackCounterLong += 1
else
    pullbackCounterLong := 0
if setupBarShort
    pullbackCounterShort := 0
else if validPullbackContextShort
    pullbackCounterShort += 1
else
    pullbackCounterShort := 0
pullbackTooLongLong = pullbackCounterLong > 10
pullbackTooLongShort = pullbackCounterShort > 10
invalidateSetupLong = (close < emaSlow) or (rsi < 35) or pullbackTooLongLong
invalidateSetupShort = (close > emaSlow) or (rsi > 65) or pullbackTooLongShort

// ═══════════════════════════════════════════════════════════════════════════
// 6️⃣ CONFIRMATION CANDLE (Entry Trigger with Trend State Locking)
// ═══════════════════════════════════════════════════════════════════════════

var bool waitingForConfirmationLong = false
var bool waitingForConfirmationShort = false
var float confirmationLevelLong = na
var float confirmationLevelShort = na
var bool trendWasValidLong = false
var bool trendWasValidShort = false

// Long Confirmation Setup
if setupBarLong and not waitingForConfirmationLong
    waitingForConfirmationLong := true
    confirmationLevelLong := high
    trendWasValidLong := upTrend

// Short Confirmation Setup
if setupBarShort and not waitingForConfirmationShort
    waitingForConfirmationShort := true
    confirmationLevelShort := low
    trendWasValidShort := downTrend

// Confirmation Triggers
confirmationLong = waitingForConfirmationLong and close > confirmationLevelLong
confirmationShort = waitingForConfirmationShort and close < confirmationLevelShort

// Reset Confirmation States
if confirmationLong or invalidateSetupLong
    waitingForConfirmationLong := false
    confirmationLevelLong := na
    trendWasValidLong := false
if confirmationShort or invalidateSetupShort
    waitingForConfirmationShort := false
    confirmationLevelShort := na
    trendWasValidShort := false

// ═══════════════════════════════════════════════════════════════════════════
// 7️⃣ FINAL ENTRY CONDITIONS
// ═══════════════════════════════════════════════════════════════════════════

longEntry = trendWasValidLong and confirmationLong
shortEntry = trendWasValidShort and confirmationShort

// ═══════════════════════════════════════════════════════════════════════════
// 8️⃣ STOP LOSS & TAKE PROFIT
// ═══════════════════════════════════════════════════════════════════════════

// Long Stop Loss
swingLowLong = ta.lowest(low, pullbackBars + 2)
stopPriceLong = swingLowLong - atr * 0.5
minStopLong = close * 0.98
finalStopLong = math.max(stopPriceLong, minStopLong)

// Short Stop Loss
swingHighShort = ta.highest(high, pullbackBars + 2)
stopPriceShort = swingHighShort + atr * 0.5
maxStopShort = close * 1.02
finalStopShort = math.min(stopPriceShort, maxStopShort)

// Long Take Profit
riskAmountLong = close - finalStopLong
takeProfit1Long = close + riskAmountLong * 2.0
takeProfit2Long = close + riskAmountLong * 3.0

// Short Take Profit
riskAmountShort = finalStopShort - close
takeProfit1Short = close - riskAmountShort * 2.0
takeProfit2Short = close - riskAmountShort * 3.0

// ═══════════════════════════════════════════════════════════════════════════
// 9️⃣ STRATEGY EXECUTION
// ═══════════════════════════════════════════════════════════════════════════

if longEntry
    strategy.entry("Long", strategy.long)
    strategy.exit("Long-Exit", "Long", stop=finalStopLong, limit=takeProfit2Long)

if shortEntry
    strategy.entry("Short", strategy.short)
    strategy.exit("Short-Exit", "Short", stop=finalStopShort, limit=takeProfit2Short)

// ═══════════════════════════════════════════════════════════════════════════
// 🔟 VISUAL ELEMENTS
// ═══════════════════════════════════════════════════════════════════════════

// EMA Plots
plot(emaFast, title="Fast EMA", color=color.blue, linewidth=2)
plot(emaSlow, title="Slow EMA", color=color.red, linewidth=2)

// HTF EMA (if enabled)
plot(useHTFFilter ? htfEma : na, title="HTF EMA", color=color.new(color.purple, 30), linewidth=3, style=plot.style_stepline)

// Entry Markers
plotshape(longEntry, title="Long Entry", style=shape.triangleup, location=location.belowbar, color=color.green, size=size.tiny)
plotshape(shortEntry, title="Short Entry", style=shape.triangledown, location=location.abovebar, color=color.orange, size=size.tiny)

// Setup Bar Markers (Debug - Optional)
plotshape(setupBarLong and not longEntry, title="Long Setup", style=shape.circle, location=location.belowbar, color=color.new(color.green, 70), size=size.tiny)
plotshape(setupBarShort and not shortEntry, title="Short Setup", style=shape.circle, location=location.abovebar, color=color.new(color.red, 70), size=size.tiny)

// ═══════════════════════════════════════════════════════════════════════════
// 1️⃣1️⃣ ALERTS
// ═══════════════════════════════════════════════════════════════════════════

alertcondition(longEntry, title="Long Entry Signal", message="Pullback Strategy: LONG entry triggered at {{close}}")
alertcondition(shortEntry, title="Short Entry Signal", message="Pullback Strategy: SHORT entry triggered at {{close}}")
alertcondition(setupBarLong, title="Long Setup Detected", message="Pullback Strategy: Long setup bar formed - watch for confirmation")
alertcondition(setupBarShort, title="Short Setup Detected", message="Pullback Strategy: Short setup bar formed - watch for confirmation")
```
---
---
## M15 Continuation Engine Strategy:  
```pine
//@version=6
strategy(
     "PA M15 Continuation Engine (Adaptive) - Safe Entry/Exit v6 Sticky Trend",
     overlay = true,
     pyramiding = 0,
     default_qty_type = strategy.percent_of_equity,
     default_qty_value = 1
)

//─────────────────────────────
// INPUTS
//─────────────────────────────
left  = input.int(4, "Pivot Left", 1, 10)
minScoreTrending = input.int(4, "Min Score (Trending)")
minScoreBalanced = input.int(6, "Min Score (Balanced)")
minScoreVolatile = input.int(7, "Min Score (Volatile)")
atrLen = input.int(14, "ATR Length")
atr = ta.atr(atrLen)

//─────────────────────────────
// MARKET STRUCTURE
//─────────────────────────────
isLocalHigh = high > ta.highest(high[1], left)
isLocalLow  = low  < ta.lowest(low[1], left)

var float HH = na
var float HL = na
var float LH = na
var float LL = na
var int trendState = 0
pullbackBuffer = atr * 0.5  // buffer for sticky trend

// Update pivots
if isLocalHigh
    if trendState == 1 or trendState == 0
        if na(HH) or high > HH
            HH := high
        else
            LH := high
    else
        HH := high
        HL := na
        trendState := 0

if isLocalLow
    if trendState == -1 or trendState == 0
        if na(LL) or low < LL
            LL := low
        else
            HL := low
    else
        LL := low
        LH := na
        trendState := 0

// Trend detection
isUptrend   = not na(HH) and not na(HL) and close > HL
isDowntrend = not na(LL) and not na(LH) and close < LH

//─────────────────────────────
// STICKY TREND STATE LOGIC
//─────────────────────────────
if isUptrend
    trendState := 1
else if isDowntrend
    trendState := -1
else
    // only neutralize if price breaks significant level beyond buffer
    if trendState == 1 and close < HL - pullbackBuffer
        trendState := 0
    else if trendState == -1 and close > LH + pullbackBuffer
        trendState := 0
    // otherwise keep previous trendState

longStructureOK  = trendState == 1 and low > HL
shortStructureOK = trendState == -1 and high < LH

//─────────────────────────────
// MARKET TYPE DETECTION
//─────────────────────────────
rangeN = ta.highest(high, 20) - ta.lowest(low, 20)
impulse = math.abs(close - open)
compression = rangeN > 0 ? impulse / rangeN : 0

lowerWick = math.min(open, close) - low
upperWick = high - math.max(open, close)
wickRatio = (lowerWick + upperWick) / math.max(impulse, atr * 0.2)

marketType =
    compression > 0.25 ? 1 :
    wickRatio > 1.8    ? 2 :
                          0

minScore =
    marketType == 1 ? minScoreTrending :
    marketType == 2 ? minScoreVolatile :
                      minScoreBalanced

//─────────────────────────────
// CANDLE QUALITY
//─────────────────────────────
body = math.abs(close - open)
lw   = lowerWick
uw   = upperWick

bullReject = lw > body * 1.5
bearReject = uw > body * 1.5

bullMomentum = close > open and close > high[1]
bearMomentum = close < open and close < low[1]

//─────────────────────────────
// SCORE LOGIC
//─────────────────────────────
longScore = 0
shortScore = 0

if longStructureOK
    if marketType == 0
        longScore += (bullReject or bullMomentum) ? 3 : 0
        longScore += compression > 0.2 ? 2 : 0
    else if marketType == 1
        longScore += bullMomentum ? 3 : 0
        longScore += bullReject ? 1 : 0
        longScore += compression > 0.2 ? 2 : 0
    else
        longScore += bullReject ? 3 : 0
        longScore += bullMomentum ? 1 : 0
        longScore += compression > 0.2 ? 2 : 0

if shortStructureOK
    if marketType == 0
        shortScore += (bearReject or bearMomentum) ? 3 : 0
        shortScore += compression > 0.2 ? 2 : 0
    else if marketType == 1
        shortScore += bearMomentum ? 3 : 0
        shortScore += bearReject ? 1 : 0
        shortScore += compression > 0.2 ? 2 : 0
    else
        shortScore += bearReject ? 3 : 0
        shortScore += bearMomentum ? 1 : 0
        shortScore += compression > 0.2 ? 2 : 0

//─────────────────────────────
// GLOBAL VARIABLES
//─────────────────────────────
var float pendingLongEntry  = na
var float pendingShortEntry = na
var float activeLongEntry   = na
var float activeShortEntry  = na
var float longSL            = na
var float shortSL           = na
var float longTP            = na
var float shortTP           = na
maxSL = atr * 2

//─────────────────────────────
// SET PENDING ENTRY PRICES
//─────────────────────────────
if strategy.position_size == 0
    // Reset pending entries if conditions are no longer valid
    if not (longStructureOK and longScore >= minScore)
        pendingLongEntry := na
    if not (shortStructureOK and shortScore >= minScore)
        pendingShortEntry := na

    // Set new pending entries if conditions are valid
    if longStructureOK and longScore >= minScore and na(pendingLongEntry)
        pendingLongEntry := high + syminfo.mintick * 2
        rawSL = math.min(HL, low) - atr * 0.15
        longSL := math.max(pendingLongEntry - maxSL, rawSL)
        longTP := pendingLongEntry + (pendingLongEntry - longSL) * 2.2

    if shortStructureOK and shortScore >= minScore and na(pendingShortEntry)
        pendingShortEntry := low - syminfo.mintick * 2
        rawSL = math.max(LH, high) + atr * 0.15
        shortSL := math.min(pendingShortEntry + maxSL, rawSL)
        shortTP := pendingShortEntry - (shortSL - pendingShortEntry) * 2.2

//─────────────────────────────
// EXECUTE ENTRIES
//─────────────────────────────
if not na(pendingLongEntry)
    strategy.entry("LONG", strategy.long, stop = pendingLongEntry)
if not na(pendingShortEntry)
    strategy.entry("SHORT", strategy.short, stop = pendingShortEntry)

//─────────────────────────────
// LOCK ACTIVE ENTRY AFTER FILL
//─────────────────────────────
if strategy.position_size > 0 and na(activeLongEntry)
    activeLongEntry := pendingLongEntry
    pendingLongEntry := na
if strategy.position_size < 0 and na(activeShortEntry)
    activeShortEntry := pendingShortEntry
    pendingShortEntry := na

//─────────────────────────────
// RESET ACTIVE ENTRIES WHEN FLAT
//─────────────────────────────
if strategy.position_size == 0
    activeLongEntry := na
    activeShortEntry := na

//─────────────────────────────
// EXECUTE EXITS
//─────────────────────────────
if strategy.position_size > 0
    strategy.exit("XL", "LONG", stop = longSL, limit = longTP)
if strategy.position_size < 0
    strategy.exit("XS", "SHORT", stop = shortSL, limit = shortTP)

//─────────────────────────────
// VISUALS
//─────────────────────────────
plot(HL, "HL", color=color.new(color.green, 80))
plot(LH, "LH", color=color.new(color.red, 80))

bgcolor(
    marketType == 1 ? color.new(color.green, 92) :
    marketType == 2 ? color.new(color.red, 92) :
                      color.new(color.gray, 94)
)
```

---
---

# ✅ **HTF Wick Strategy — Step-by-Step Trading Checklist (Beginner Version)**  


# **📌 STEP 1 — Determine Trend/Bias (Higher Timeframe)**  

### **On the 4H & Daily chart:**  

* ☐ Is price **below** the 50 EMA AND the EMA sloping down?  
  → **Bias = SELL only**  
* ☐ Is price **above** the 50 EMA AND the EMA sloping up?  
  → **Bias = BUY only**  

✔ Only trade in ONE direction per session  
✔ NO countertrend trades  


# **📌 STEP 2 — Wait for a New 4H Candle to Open**  

4H candles start at:  
**02:00, 06:00, 10:00, 14:00, 18:00, 22:00** (broker time varies)  

* ☐ Has a new 4H candle opened?  

If **SELL BIAS** → expect price to push UP first (form top wick)  
If **BUY BIAS** → expect price to push DOWN first (form bottom wick)  

**Do NOT enter before the manipulation/wick forms.**  


# **📌 STEP 3 — Mark Your Setup Area (Fair Value Gaps)**  

Switch to **5-minute or 15-minute chart**:  

* ☐ Mark the most recent unmitigated FVG located near previous swing high/low  

If **SELLING:**  

* ☐ Mark the nearest FVG *above* current price  

If **BUYING:**  

* ☐ Mark the nearest FVG *below* current price  

**Only trade if price taps into that FVG.**  


# **📌 STEP 4 — Wait for Price to Tap the FVG**  

* ☐ Has price reached the FVG zone?  
  → If *no*, **NO trade**  

Once price taps:  

* For **SELL**: price must tap UP into the FVG  
* For **BUY**: price must tap DOWN into the FVG  

This is the wick of the HTF candle.  


# **📌 STEP 5 — Look for Simple LTF Confirmation (1M–3M chart)**  

Switch to 1M–3M:  

### **SELL Confirmation Checklist**  

* ☐ Price entered FVG  
* ☐ Price rejected and moved down  
* ☐ A recent **structure low is broken**  

### **BUY Confirmation Checklist**  

* ☐ Price entered FVG  
* ☐ Price rejected and moved up  
* ☐ A recent **structure high is broken**  

If this does NOT happen → **NO trade**.  


# **📌 STEP 6 — Entry & Stop Loss**  

### **SELL Entry**  

* ☐ Enter after pullback following BOS (break of structure)  
* ☐ Stop loss above the wick that tapped the FVG  

### **BUY Entry**  

* ☐ Enter after pullback following BOS  
* ☐ Stop loss below the wick that tapped the FVG  

Stops should be clean and small.  


# **📌 STEP 7 — Take Profit Target**  

Choose ONE:  

* ☐ Previous swing low/high (safest)  
* ☐ 2R–3R (balanced and reliable)  
* ☐ 4H candle completion (advanced / higher reward)  


# **📌 STEP 8 — Post-Trade Review**  

After trade closes, evaluate:  

* ☐ Did I follow the bias rules?  
* ☐ Did I wait for a 4H candle?  
* ☐ Did I ONLY trade after tapping the FVG?  
* ☐ Did I wait for confirmation?  
* ☐ Did I avoid impulsive entries?  


# ⭐ Summary: The Only Things You Need  

* **Trend from 4H & Daily (50 EMA)**  
* **New 4H candle opening**  
* **Clear 5m–15m FVG**  
* **LTF confirmation (BOS)**  
* **Simple SL + 2R–3R TP**  

This is the simplest and safest way for beginners to trade the HTF wick concept.  

---  
---  
---  

# Strategy: 2  

## ✅ Choppy Market Range-Bounce Strategy — Step-by-Step Checklist (Beginner Version)  

### 📌 STEP 1 — Identify the Range (H1/H4)  
* ☐ Mark clear Support zone  
* ☐ Mark clear Resistance zone  
* ☐ Range width must be obvious and wide enough (not less than 40 pips)  

### 📌 STEP 2 — Check Market Condition  
* ☐ Is the 200 EMA flat?
* ☐ No major trend on Daily timeframe (price not making strong higher highs/lower lows)  
* ☐ Price has touched Support/Resistance atleast 2 times?  
* ☐ Is price bouncing between Support/Resistance?  
* If “yes” → proceed.  
* If “no” → skip.  

### 📌 STEP 3 — Prepare for Trade at the Edges  
BUY Setup  
* ☐ Wait for price to touch Support  
* ☐ Confirm RSI oversold (below 30)  
* ☐ Look for bullish rejection candle  
* ☐ Rejection candle pattern is clear (pin bar, engulfing, hammer/star)

SELL Setup  
* ☐ Wait for price to touch Resistance  
* ☐ Confirm RSI overbought (above 70)  
* ☐ Look for bearish rejection candle
* ☐ Rejection candle pattern is clear (pin bar, engulfing, hammer/star)  

### 📌 STEP 4 — Entry  
* ☐ Enter **only after** the rejection candle closes  
* ☐ No entries mid-range  
* ☐ No entries early  

### 📌 STEP 5 — Stop Loss  
* ☐ SL placed outside the Support/Resistance zone  
* ☐ Added 5–10 pip safety padding  

### 📌 STEP 6 — Take Profit  
Choose one:  
* ☐ TP1: Mid-range (quick, safe)  
* ☐ TP2: Opposite zone (full move)  

### 📌 STEP 7 — Risk Rules  
* ☐ Risk max 1% per trade  
* ☐ No revenge trades  
* ☐ No trading during major news releases  

### 📌 STEP 8 — Review After the Trade  
* ☐ Did I trade only at the edges?  
* ☐ Did I wait for RSI + candle confirmation?  
* ☐ Did I avoid trading in the middle?  
* ☐ Did I follow SL/TP rules?  

⭐ **Summary: The Only Things You Need**  
- Clear range  
- Flat 200 EMA  
- Edges only  
- RSI confirmation  
- Simple SL outside range  
- TP mid-range or opposite edge  

---
---
--- 
# Strategy: 3  

## Quick Reference: Choppy Market Trading Strategy

### SETUP (Do Once)
- **Timeframe**: 4-hour chart
- **Pairs**: EUR/USD, GBP/USD, or USD/JPY only
- **Indicators**: 
  - Bollinger Bands (20, 2)
  - RSI (14)
  - Moving Average (50)

---

### STEP 1: Open Chart & Identify Market Type
- Look at the chart - is price moving sideways in a range?
- Check if Bollinger Bands are parallel (not widening/narrowing rapidly)
- If yes = ranging/choppy market → continue
- If no = skip trading today

---

### STEP 2: Wait for Price to Reach Extreme Zones
- **For BUY**: Wait for price to touch/cross lower Bollinger Band
- **For SELL**: Wait for price to touch/cross upper Bollinger Band
- Set price alerts if not there yet and walk away

---

### STEP 3: Check RSI Confirmation
- **For BUY**: RSI must be below 30 (oversold)
- **For SELL**: RSI must be above 70 (overbought)
- If RSI is between 30-70 → No trade, wait

---

### STEP 4: Wait for Reversal Candle
- **For BUY**: Wait for a strong green/bullish candle at lower band
- **For SELL**: Wait for a strong red/bearish candle at upper band
- Patience! Don't jump in immediately

---

### STEP 5: Enter Trade
- Enter when the next candle opens after the reversal candle
- Enter only 1% of your account risk

---

### STEP 6: Set Stop Loss Immediately
- **For BUY**: Place stop loss 20-30 pips below the lower Bollinger Band
- **For SELL**: Place stop loss 20-30 pips above the upper Bollinger Band

---

### STEP 7: Set Take Profit
- **Target 1** (50% position): Middle MA line
- **Target 2** (remaining 50%): Opposite Bollinger Band (but exit 10-20 pips before)

---

### STEP 8: Walk Away
- Don't watch every tick
- Let the trade run
- Check only 2-3 times per day

---
---
---
# Strategy: 4
## Quick Reference: Daily Candle Entry Strategy (Beginner-Friendly)

### SETUP (Do Once)

* **Timeframe**: Daily chart to identify zones, drop to 5-min or 15-min for entries
* **Pairs / Markets**: Any liquid market (Forex, indices, crypto)
* **Indicators**: None required (price-action based), optional small EMA for structure reference

---

### STEP 1: Mark Daily Candle Close

* Wait for the **previous daily candle to fully close**
* Draw a horizontal line at the **closing price**
* This will be your **key reference zone**

---

### STEP 2: Determine Bias

* **Bullish daily close** → look to **buy below the close**
* **Bearish daily close** → look to **sell above the close**
* Do not trade against the daily bias

---

### STEP 3: Drop to Lower Timeframe

* Use **5-minute or 15-minute charts**
* Watch how price behaves around the daily close zone

---

### STEP 4: Wait for Entry Signal

* **For BUY below bullish close**:

  * Price dips below daily close line
  * Look for small **rejection candles**, **wicks**, or minor **structure shift**
* **For SELL above bearish close**:

  * Price rises above daily close line
  * Look for small **rejection candles**, **wicks**, or minor **structure shift**
* Optional: Enter at **imbalance / liquidity grab** if visible

---

### STEP 5: Enter Trade

* Enter when the lower timeframe shows **confirmation of reversal**
* Risk **1–2% of account per trade**

---

### STEP 6: Set Stop Loss Immediately

* Place stop loss **just beyond the wick or structure extreme**
* Typically **5–10 pips for Forex**, adjust for volatility

---

### STEP 7: Set Take Profit

* Target **1:2 or 1:3 risk-reward ratio**
* Optionally use next **minor swing high/low** on lower timeframe as TP

---

### STEP 8: Walk Away

* Mark your levels and **don’t over-monitor**
* Let price move
* Adjust TP if market structure changes, but do not chase trades

---

This method simplifies daily candle entries, uses **low-risk zones**, and gives beginners a **structured way to trade without relying on complex indicators**.


---
---
---

# Strategy: 5  
---

### SETUP (Do Once)

* **Timeframes**:

  * Daily (D1) → direction
  * 1 Hour (1H) → entry
* **Pairs**: EUR/USD, GBP/USD, USD/JPY, USD/CAD
* **Indicators**:

  * Session Indicator (Asia, London, New York)
* **Risk**: 1% per trade
* **RR**: Fixed 1:3

---

### STEP 1: Check Direction on Daily (Very Simple)

* Open **Daily chart**
* Look only at the **last 2 candles**

  * **Bullish bias**:

    * Today’s candle closed **above yesterday’s high**
  * **Bearish bias**:

    * Today’s candle closed **below yesterday’s low**
* If neither → **NO TRADE**
* Do **not** analyze trends, HH/HL, or patterns

---

### STEP 2: Mark Yesterday’s Range

* On the **Daily chart**:

  * Draw a horizontal line at **yesterday’s high**
  * Draw a horizontal line at **yesterday’s low**
* This box is your **only trading area**
* Do nothing else on Daily

---

### STEP 3: Go to 1H Before London Open

* Switch to **1H timeframe**
* Be on charts **before London opens**
* Turn on **Session Indicator**
* Focus only on:

  * End of **Asian session**
  * Start of **London session**

---

### STEP 4: Wait for Liquidity Sweep (Key Beginner Rule)

* **Bullish setup**:

  * Price dips below **yesterday’s low** OR **Asian low**
  * Then closes back **inside the range**
* **Bearish setup**:

  * Price moves above **yesterday’s high** OR **Asian high**
  * Then closes back **inside the range**
* If no sweep → **NO TRADE**

---

### STEP 5: Confirm With Candle Close (No Structure Needed)

* **Bullish trade**:

  * A strong **bullish candle** closes after the sweep
* **Bearish trade**:

  * A strong **bearish candle** closes after the sweep
* Do **not** wait for break of structure
* Candle close is enough for beginners

---

### STEP 6: Enter Trade

* Enter **at the close** of the confirmation candle
* Risk **only 1%**
* One trade per pair per day (max)

---

### STEP 7: Set Stop Loss & Take Profit

* **Stop Loss**:

  * BUY → below the sweep low
  * SELL → above the sweep high
* **Take Profit**:

  * Fixed **1:3 Risk–Reward**
* No trailing stop
* No moving TP

---

### STEP 8: Walk Away

* Once trade is placed:

  * Do not manage
  * Do not watch lower timeframes
* Check result later

---

## Beginner Rules (Very Important)

* Trade **only London session**
* Skip days with:

  * Big news (NFP, CPI, FOMC)
  * Very small daily candles
* Journal **every trade**
* Judge strategy only after **30 trades**  

---
---
---
# Strategy: 6  

Once the alert fires, you **do not rush**. You go through a checklist.

### A. Trend Filter (Non-negotiable)  
* Price above 200 EMA = buy trade only
* Price below 200 EMA = sell trade only
* Price within ±50 pips of 200 EMA = no trade (indecision zone)

### B. RSI Signal
* RSI < 30 (for buy in uptrend only)
* RSI > 70 (for sell in downtrend only)

### C. Price Action (mandatory)
For BUY (Pick one):
* Bullish engulfing (body must be 1.5x average candle)
* Rejection wick (wick must be 2x body size minimum)

For SELL (Pick one):
* Bearish engulfing (body must be 1.5x average candle)
* Rejection wick (wick must be 2x body size minimum)

If no price action is met → **no trade**  

### D. One extra confirmation (Pick only one- not all)
* MACD crossover in direction of trade or
* MACD histogram must flip in trade direction of trade or
* Volume spike towards the direction of trade  

### E. Entry (Where to enter trade)
* For Buy: Must be at support zone (previous low/demand area)
* Sell: Must be at resistance zone (previous high/supply area)
  
### F. Stop Loss & Take Profit
**Stop Loss**
* For Buy → below recent swing low
* For Sell → above recent swing high

**Take Profit**
* Minimum **1:2 risk-to-reward** or
* Next support/resistance level  


## Pine Script  
Script Version: 1  
```pinescript
//@version=5
indicator("RSI Alert – Oversold & Overbought", overlay=false)

// === INPUTS ===
rsiLength = input.int(14, title="RSI Length")
oversoldLevel = input.int(30, title="Oversold Level")
overboughtLevel = input.int(70, title="Overbought Level")

// === RSI CALCULATION ===
rsiValue = ta.rsi(close, rsiLength)

// === CONDITIONS ===
rsiOversold = ta.crossunder(rsiValue, oversoldLevel)
rsiOverbought = ta.crossover(rsiValue, overboughtLevel)

// === ALERT CONDITIONS ===
alertcondition(
     rsiOversold,
     title="RSI Oversold Alert",
     message="RSI crossed BELOW 30 on {{ticker}} ({{interval}}). Look for BUY confirmation."
)

alertcondition(
     rsiOverbought,
     title="RSI Overbought Alert",
     message="RSI crossed ABOVE 70 on {{ticker}} ({{interval}}). Look for SELL confirmation."
)

// === VISUALS ===
plot(rsiValue, title="RSI", color=color.blue, linewidth=2)
hline(oversoldLevel, "Oversold", color=color.green)
hline(overboughtLevel, "Overbought", color=color.red)

plotshape(
     rsiOversold,
     title="Oversold Signal",
     location=location.bottom,
     style=shape.labelup,
     text="RSI < 30",
     color=color.green,
     textcolor=color.white
)

plotshape(
     rsiOverbought,
     title="Overbought Signal",
     location=location.top,
     style=shape.labeldown,
     text="RSI > 70",
     color=color.red,
     textcolor=color.white
)
```  
Script Version: 2  
```pinescript
//@version=5
indicator("RSI Strategy with Trend & Momentum Filter", overlay=true)

// ═══════════════════════════════════════════════════════
// INPUTS
// ═══════════════════════════════════════════════════════
rsiLength = input.int(14, title="RSI Length", minval=1)
rsiOversold = input.int(30, title="RSI Oversold Level")
rsiOverbought = input.int(70, title="RSI Overbought Level")
emaLength = input.int(200, title="Trend Filter EMA")
atrLength = input.int(14, title="ATR Length for Stop Loss")
atrMultiplier = input.float(1.5, title="ATR Multiplier for SL")

// MACD Settings
macdFast = input.int(12, title="MACD Fast Length")
macdSlow = input.int(26, title="MACD Slow Length")
macdSignal = input.int(9, title="MACD Signal Length")

// ═══════════════════════════════════════════════════════
// CALCULATIONS
// ═══════════════════════════════════════════════════════
rsiValue = ta.rsi(close, rsiLength)
ema200 = ta.ema(close, emaLength)
atr = ta.atr(atrLength)

// MACD
[macdLine, signalLine, macdHist] = ta.macd(close, macdFast, macdSlow, macdSignal)
macdBullish = ta.crossover(macdLine, signalLine)
macdBearish = ta.crossunder(macdLine, signalLine)

// Price Action Patterns
bullishEngulfing = close > open and close[1] < open[1] and close > open[1] and open < close[1]
bearishEngulfing = close < open and close[1] > open[1] and close < open[1] and open > close[1]

// Hammer (bullish rejection)
bodySize = math.abs(close - open)
lowerWick = open < close ? open - low : close - low
upperWick = open > close ? high - open : high - close
isHammer = lowerWick > bodySize * 2 and upperWick < bodySize * 0.3

// Shooting Star (bearish rejection)
isShootingStar = upperWick > bodySize * 2 and lowerWick < bodySize * 0.3

// ═══════════════════════════════════════════════════════
// TREND FILTER
// ═══════════════════════════════════════════════════════
uptrend = close > ema200
downtrend = close < ema200
noTrend = math.abs(close - ema200) < (atr * 0.5)

// ═══════════════════════════════════════════════════════
// SIGNAL CONDITIONS
// ═══════════════════════════════════════════════════════
// BUY Signal: RSI oversold + uptrend + price action + MACD bullish
buyCondition = rsiValue < rsiOversold and 
               uptrend and 
               not noTrend and
               (bullishEngulfing or isHammer) and 
               macdBullish

// SELL Signal: RSI overbought + downtrend + price action + MACD bearish  
sellCondition = rsiValue > rsiOverbought and 
                downtrend and 
                not noTrend and
                (bearishEngulfing or isShootingStar) and 
                macdBearish

// ═══════════════════════════════════════════════════════
// STOP LOSS & TAKE PROFIT LEVELS
// ═══════════════════════════════════════════════════════
var float buyStopLoss = na
var float buyTakeProfit1 = na
var float buyTakeProfit2 = na

var float sellStopLoss = na
var float sellTakeProfit1 = na
var float sellTakeProfit2 = na

if buyCondition
    buyStopLoss := close - (atr * atrMultiplier)
    buyTakeProfit1 := close + (atr * atrMultiplier * 1.5)
    buyTakeProfit2 := close + (atr * atrMultiplier * 2.5)

if sellCondition
    sellStopLoss := close + (atr * atrMultiplier)
    sellTakeProfit1 := close - (atr * atrMultiplier * 1.5)
    sellTakeProfit2 := close - (atr * atrMultiplier * 2.5)

// ═══════════════════════════════════════════════════════
// ALERTS
// ═══════════════════════════════════════════════════════
alertcondition(buyCondition, 
     title="BUY Signal", 
     message="🟢 BUY SIGNAL on {{ticker}} {{interval}}\n✅ RSI Oversold\n✅ Uptrend (>200EMA)\n✅ Bullish Pattern\n✅ MACD Bullish\nSL: {{plot_0}}\nTP1: {{plot_1}}\nTP2: {{plot_2}}")

alertcondition(sellCondition, 
     title="SELL Signal", 
     message="🔴 SELL SIGNAL on {{ticker}} {{interval}}\n✅ RSI Overbought\n✅ Downtrend (<200EMA)\n✅ Bearish Pattern\n✅ MACD Bearish\nSL: {{plot_3}}\nTP1: {{plot_4}}\nTP2: {{plot_5}}")

// ═══════════════════════════════════════════════════════
// VISUALS
// ═══════════════════════════════════════════════════════
// Plot EMA on chart
plot(ema200, title="200 EMA", color=color.new(color.orange, 0), linewidth=2)

// Plot signals
plotshape(buyCondition, 
     title="BUY", 
     location=location.belowbar, 
     style=shape.labelup, 
     text="BUY",
     color=color.new(color.green, 0), 
     textcolor=color.white, 
     size=size.normal)

plotshape(sellCondition, 
     title="SELL", 
     location=location.abovebar, 
     style=shape.labeldown, 
     text="SELL",
     color=color.new(color.red, 0), 
     textcolor=color.white, 
     size=size.normal)

// Plot stop loss and take profit levels
plot(buyStopLoss, title="Buy SL", color=color.new(color.red, 70), style=plot.style_cross)
plot(buyTakeProfit1, title="Buy TP1", color=color.new(color.green, 70), style=plot.style_cross)
plot(buyTakeProfit2, title="Buy TP2", color=color.new(color.green, 50), style=plot.style_cross)

plot(sellStopLoss, title="Sell SL", color=color.new(color.red, 70), style=plot.style_cross)
plot(sellTakeProfit1, title="Sell TP1", color=color.new(color.green, 70), style=plot.style_cross)
plot(sellTakeProfit2, title="Sell TP2", color=color.new(color.green, 50), style=plot.style_cross)

// Background color for trend
bgcolor(uptrend and not noTrend ? color.new(color.green, 95) : 
        downtrend and not noTrend ? color.new(color.red, 95) : 
        color.new(color.gray, 97), title="Trend Background")
```
Script Version: 3  

```pinescript
//@version=5
indicator("RSI Strategy - High Frequency Version", overlay=true)

// ═══════════════════════════════════════════════════════
// INPUTS
// ═══════════════════════════════════════════════════════
rsiLength = input.int(14, title="RSI Length", minval=1)
rsiOversold = input.int(35, title="RSI Oversold Level") // Slightly loosened
rsiOverbought = input.int(65, title="RSI Overbought Level") // Slightly loosened
emaLength = input.int(200, title="Trend Filter EMA")
atrLength = input.int(14, title="ATR Length for Stop Loss")
atrMultiplier = input.float(1.5, title="ATR Multiplier for SL")

// MACD Settings
macdFast = input.int(12, title="MACD Fast Length")
macdSlow = input.int(26, title="MACD Slow Length")
macdSignal = input.int(9, title="MACD Signal Length")

// ═══════════════════════════════════════════════════════
// CALCULATIONS
// ═══════════════════════════════════════════════════════
rsiValue = ta.rsi(close, rsiLength)
ema200 = ta.ema(close, emaLength)
atr = ta.atr(atrLength)

// MACD State (Modified to check for "is bullish" rather than "just crossed")
[macdLine, signalLine, macdHist] = ta.macd(close, macdFast, macdSlow, macdSignal)
isMacdBullish = macdLine > signalLine
isMacdBearish = macdLine < signalLine

// Price Action Patterns
bullishEngulfing = close > open and close[1] < open[1] and close > open[1] and open < close[1]
bearishEngulfing = close < open and close[1] > open[1] and close < open[1] and open > close[1]

bodySize = math.abs(close - open)
lowerWick = open < close ? open - low : close - low
upperWick = open > close ? high - open : high - close
isHammer = lowerWick > bodySize * 1.5 and upperWick < bodySize * 0.5
isShootingStar = upperWick > bodySize * 1.5 and lowerWick < bodySize * 0.5

// ═══════════════════════════════════════════════════════
// SIGNAL CONDITIONS
// ═══════════════════════════════════════════════════════
// BUY: Price > EMA AND MACD is Bullish AND (RSI Low OR Price Action)
// This captures the bounce more effectively.
buyCondition = close > ema200 and 
               isMacdBullish and 
               rsiValue < 45 and // Increased threshold to catch pullbacks
               (bullishEngulfing or isHammer)

// SELL: Price < EMA AND MACD is Bearish AND (RSI High OR Price Action)
sellCondition = close < ema200 and 
                isMacdBearish and 
                rsiValue > 55 and // Decreased threshold to catch pullbacks
                (bearishEngulfing or isShootingStar)

// ═══════════════════════════════════════════════════════
// VISUALS & ALERTS
// ═══════════════════════════════════════════════════════
plot(ema200, title="200 EMA", color=color.new(color.orange, 0), linewidth=2)

plotshape(buyCondition, title="BUY", location=location.belowbar, style=shape.labelup, text="BUY", color=color.green, textcolor=color.white, size=size.normal)
plotshape(sellCondition, title="SELL", location=location.abovebar, style=shape.labeldown, text="SELL", color=color.red, textcolor=color.white, size=size.normal)

// Alert logic
alertcondition(buyCondition, title="New Buy Signal", message="Strategic Buy Setup")
alertcondition(sellCondition, title="New Sell Signal", message="Strategic Sell Setup")
```

Script Version: 4  
```pinescript
//@version=5
indicator("RSI Pullback Strategy (3–5 Trades/Day)", overlay=true)

// ═════════════════════════════
// INPUTS
// ═════════════════════════════
rsiLength = input.int(14)
rsiBuyLevel = input.int(40)
rsiSellLevel = input.int(60)

emaLength = input.int(200)
atrLength = input.int(14)
atrMultiplier = input.float(1.2)

maxTradesPerDay = input.int(5)
minBarsBetweenTrades = input.int(10)

// MACD
[macdLine, signalLine, macdHist] = ta.macd(close, 12, 26, 9)

// ═════════════════════════════
// CALCULATIONS
// ═════════════════════════════
rsi = ta.rsi(close, rsiLength)
ema200 = ta.ema(close, emaLength)
atr = ta.atr(atrLength)

// Trend
uptrend = close > ema200
downtrend = close < ema200
noTrend = math.abs(close - ema200) < atr * 0.4

// Momentum
macdBullish = macdLine > signalLine and macdHist > macdHist[1]
macdBearish = macdLine < signalLine and macdHist < macdHist[1]

// Price Action (simplified)
bullishCandle = close > open
bearishCandle = close < open

// ═════════════════════════════
// DAILY TRADE LIMITER
// ═════════════════════════════
var int tradesToday = 0
var int lastTradeBar = na

newDay = ta.change(time("D"))
if newDay
    tradesToday := 0

canTrade = tradesToday < maxTradesPerDay and
           (na(lastTradeBar) or bar_index - lastTradeBar > minBarsBetweenTrades)

// ═════════════════════════════
// ENTRY CONDITIONS
// ═════════════════════════════
buyCondition =
    canTrade and
    uptrend and
    not noTrend and
    rsi < rsiBuyLevel and
    macdBullish and
    bullishCandle

sellCondition =
    canTrade and
    downtrend and
    not noTrend and
    rsi > rsiSellLevel and
    macdBearish and
    bearishCandle

// ═════════════════════════════
// EXECUTION LOGIC
// ═════════════════════════════
if buyCondition
    tradesToday += 1
    lastTradeBar := bar_index

if sellCondition
    tradesToday += 1
    lastTradeBar := bar_index

// ═════════════════════════════
// VISUALS
// ═════════════════════════════
plot(ema200, color=color.orange, linewidth=2)

plotshape(buyCondition, title="BUY", location=location.belowbar,
     color=color.green, style=shape.labelup, text="BUY")

plotshape(sellCondition, title="SELL", location=location.abovebar,
     color=color.red, style=shape.labeldown, text="SELL")

bgcolor(
    uptrend and not noTrend ? color.new(color.green, 92) :
    downtrend and not noTrend ? color.new(color.red, 92) :
    color.new(color.gray, 96)
)
```

## How to activate alerts  

## 🔧 PART 1: ADDING THE SCRIPT

### **Step 1: Open Pine Editor**

1. Go to [TradingView.com](https://www.tradingview.com)
2. Open any chart (EUR/USD, whatever)
3. At the bottom of the screen, click **"Pine Editor"** tab
   - If you don't see it, click the **`</>`** icon at the bottom toolbar

### **Step 2: Create New Indicator**

1. In the Pine Editor, you'll see some default code
2. Click **"Open"** dropdown (top left of Pine Editor)
3. Select **"New indicator"**
4. **Delete ALL the default code** that appears

### **Step 3: Paste the Enhanced Script**

1. Copy the entire Pine Script (from `//@version=5` to the last line)
2. Paste it into the empty Pine Editor
3. Click **"Save"** (disk icon at top)
4. Name it: `RSI Strategy - Enhanced` (or whatever you want)

### **Step 4: Add to Chart**

1. Click **"Add to Chart"** button (top right of Pine Editor)
2. The indicator should now appear:
   - **On the main chart**: You'll see the 200 EMA line (orange)
   - **BUY/SELL signals**: Green "BUY" labels below bars, Red "SELL" labels above bars
   - **Background shading**: Slight green tint in uptrends, red in downtrends
   - **Stop Loss & Take Profit levels**: Crosses appear when signals fire

---

## 🔔 PART 2: SETTING UP ALERTS (CRITICAL!)

### **Step 1: Open Alert Menu**

1. Look at the **top toolbar** of your chart
2. Click the **alarm clock icon** (🔔) or press `Alt + A`
3. A popup window opens: "Create Alert"

### **Step 2: Configure the Alert**

In the alert setup window:

#### **Condition Section:**
- **First dropdown**: Select e.g `RSI Strategy - Enhanced` (your script name)
- **Second dropdown**: You'll see two options:
  - **`BUY Signal`** ← Select this for buy entries
  - **`SELL Signal`** ← Select this for sell entries

*You need to create TWO separate alerts (one for BUY, one for SELL)*

#### **Options Section:**
- **"Only Once"**: ❌ DON'T use this
- **"Once Per Bar"**: ❌ Not ideal
- **"Once Per Bar Close"**: ✅ **SELECT THIS** (prevents false signals from wicks)

#### **Expiration:**
- Set to **"Open-ended"** (never expires)

#### **Alert Actions:**
Check these boxes:
- ✅ **Notify on App** (if you have TradingView mobile app)
- ✅ **Show Popup** (desktop alert)
- ✅ **Send Email** (optional but recommended)
- ❌ **Play Sound** (annoying if you're watching charts)
- ❌ **Send Email-to-SMS** (unless you want text messages)

#### **Alert Name:**
Give it a clear name:
- Example: `EUR/USD 1H - BUY Signal`
- Example: `GBP/JPY 4H - SELL Signal`

#### **Message:**
The default message I included has this format:
```
🟢 BUY SIGNAL on EURUSD 1H
✅ RSI Oversold
✅ Uptrend (>200EMA)
✅ Bullish Pattern
✅ MACD Bullish
SL: 1.0850
TP1: 1.0920
TP2: 1.0975
```

**Don't change this** — it's already formatted to give you all the info you need.

### **Step 3: Create the Alert**

1. Click **"Create"** at the bottom
2. **Repeat Steps 1-3** for the SELL signal (remember you need only TWO alerts total per chart i.e `buy` and `sell`)

---

## 📱 PART 3: MOBILE APP SETUP (OPTIONAL BUT USEFUL)

If you want alerts on your phone:

1. Download **TradingView app** (iOS/Android)
2. Log in with the same account
3. Go to **Profile → Settings → Notifications**
4. Enable **"Push Notifications"**
5. Make sure alerts are ON for your watchlist

Now you'll get phone notifications when signals fire.

---

## ⚙️ PART 4: CUSTOMIZING THE INDICATOR SETTINGS

You might want to tweak some parameters. Here's how:

### **Step 1: Open Indicator Settings**

1. On your chart, find the indicator name in the **top-left overlay list**
2. Click the **gear icon** (⚙️) next to "RSI Strategy - Enhanced"
3. A settings menu opens

### **Step 2: Key Parameters you can Adjust if you like**

Here's what each setting does:

| Parameter | Default | What It Does | When to Change |
|-----------|---------|--------------|----------------|
| **RSI Length** | 14 | Sensitivity of RSI | Lower = more signals (noisier), Higher = fewer signals |
| **RSI Oversold** | 30 | Buy trigger level | In strong uptrends, try 35-40 |
| **RSI Overbought** | 70 | Sell trigger level | In strong downtrends, try 60-65 |
| **Trend Filter EMA** | 200 | Long-term trend | 100 for faster trends, 200 for major trends |
| **ATR Length** | 14 | Volatility measure | Keep at 14 (standard) |
| **ATR Multiplier** | 1.5 | Stop loss distance | 1.5 = tighter stops, 2.0 = wider stops |

### **My Recommendation:**
**Don't change anything yet.** Test with defaults first, then adjust based on your backtest results.

---

## 🎯 PART 5: TESTING THE SETUP

Before you trust this with real money:

### **Immediate Test:**

1. **Scroll back in time** on your chart (click and drag the chart left)
2. Look for past BUY/SELL signals
3. **Manually check each one:**
   - Did it respect the 200 EMA trend filter?
   - Was there actually a bullish/bearish pattern?
   - Would the stop loss have been hit?
   - Did it reach TP1? TP2?

### **Demo Test:**

1. Open a **TradingView Paper Trading account** (fake money)
2. When you get an alert, **place the trade** in paper trading
3. Track results in a spreadsheet for at least **50 trades**

---

## 🚨 COMMON MISTAKES (AVOID THESE)

### ❌ **Mistake #1: Using "Any Alert Function Call"**
Some people select "Any alert() function call" instead of the specific signal. **Don't do this.** You want only BUY or SELL signals, not every calculation.

### ❌ **Mistake #2: Not Using "Once Per Bar Close"**
If you use "Once Per Bar," you'll get alerts **while the candle is forming**, which leads to false signals. Always wait for bar close.

### ❌ **Mistake #3: Setting Alerts on Multiple Timeframes with Same Pair**
Don't create EUR/USD 15M, 1H, 4H alerts all at once. Pick **ONE timeframe** per pair to avoid confusion.

### ❌ **Mistake #4: Ignoring Alerts in Chop**
Just because the script fires an alert doesn't mean you **must** trade it. If the market looks choppy or you're unsure, **skip it.** The script is a tool, not a robot master.

### ❌ **Mistake #5: Not Checking Price Action Yourself**
The script detects patterns, but **you need to visually confirm**:
- Is the engulfing candle strong or weak?
- Is the rejection wick clean or messy?
- Are we at actual support/resistance?

**Never blindly follow alerts.**

---

## 🔧 TROUBLESHOOTING

### **Problem: No signals appearing**
**Causes:**
- Market is ranging near 200 EMA (no clear trend)
- RSI not hitting 30/70 levels (low volatility)
- MACD not confirming (momentum misalignment)

**Solution:** This is normal. Good strategies don't signal every day. Wait.

### **Problem: Too many signals**
**Causes:**
- Timeframe too low (5M/15M = noise)
- Choppy market conditions

**Solution:** Move to higher timeframe (1H minimum, 4H ideal for swing trading)

### **Problem: Alerts not firing**
**Causes:**
- Alert set to "Only Once" and already fired
- Wrong condition selected
- TradingView session expired

**Solution:** 
- Delete and recreate alert
- Make sure condition is "BUY Signal" or "SELL Signal"
- Stay logged into TradingView (doesn't need to be active, just logged in)

### **Problem: Alert fired but no visual signal on chart**
**Causes:**
- You're looking at wrong timeframe
- You zoomed in/out and can't see the label

**Solution:** Check the alert message — it tells you the timeframe. Match it exactly.

---

## ✅ FINAL CHECKLIST

Before you go live:

- [ ] Script added to chart successfully
- [ ] BUY alert created with "Once Per Bar Close"
- [ ] SELL alert created with "Once Per Bar Close"  
- [ ] Mobile app notifications enabled
- [ ] Email alerts enabled (optional)
- [ ] Tested on paper trading for 50+ trades
- [ ] Win rate and RR documented
- [ ] Position sizing calculator ready
- [ ] Trade journal spreadsheet prepared
- [ ] Economic calendar bookmarked

---

**You're now set up.** But remember: **The script is only 20% of success.** The other 80% is your discipline, risk management, and ability to sit on your hands when conditions aren't right. Most traders fail because they overtrade, not because their strategy is bad.
---
---
---
# Strategy: 7  
https://www.youtube.com/watch?v=S2HaCa0b-bY 
# MACD Money Map: 3-System Trading Strategy

## SYSTEM 1: THE TREND SYSTEM
**Catches big moves that run for days or weeks**

### Zero Line Foundation
- MACD above zero = ONLY look for buy trades
- MACD below zero = ONLY look for sell trades
- Never fight this rule—it's your trend compass

### Crossover Entry
- Only take crossovers far from zero line (above +0.5 or below -0.5)
- Crossovers near zero are in the "chop zone"—avoid them
- Wait 2-3 candles after crossover before entering to confirm it's real
- Above zero + bullish crossover above +0.5 = continuation long entry
- Below zero + bearish crossover below -0.5 = continuation short entry

## SYSTEM 2: THE REVERSAL SYSTEM
**Catches major turning points before they happen**

### Divergence Detection
- Bearish divergence: Price makes higher high, MACD makes lower high = reversal coming
- Bullish divergence: Price makes lower low, MACD makes higher low = bottom forming
- Draw lines on both price and MACD to spot disagreement
- Never trade divergence alone—wait for histogram confirmation

### Histogram Confirmation (3 Key Patterns)
- **The Flip**: First green bar after red bars (or vice versa) = momentum shift
- **The Shrinking Tower**: Bars getting smaller = move running out of gas
- **The Zero Bounce**: Histogram bounces away from zero = trend still strong
- Entry: Divergence spotted + histogram pattern appears = take the trade

## SYSTEM 3: THE CONFIRMATION SYSTEM
**Filters out losing trades**

### Triple Timeframe Stack
- **Daily**: Which side of zero? Sets your bias
- **4-Hour**: Crossover or divergence? That's your signal
- **1-Hour**: Histogram confirming? That's your trigger
- All three must align—if not, pass on the trade
- Use 4x multiplier (e.g., 15min/1hr/4hr or 1hr/4hr/daily)

### Price Action Confirmation
- Crossovers at support/resistance have higher win rates
- Look for MACD signals + candlestick patterns (hammer, engulfing, etc.)
- Wait for MACD crossover AFTER trendline breaks, not before

## 5-MINUTE MORNING SCAN ROUTINE

**Step 1: Check the Trend** (5 seconds)
- Look at daily MACD—above or below zero?
- That's your bias for the entire day

**Step 2: Find the Setup**
- Scan for crossovers far from zero (System 1 trend trades)
- Scan for divergence forming (System 2 reversal trades)
- Mark your top 3 setups

**Step 3: Confirm Everything**
- Check all three timeframes align
- Confirm price is at key level
- Confirm histogram pattern present
- If anything missing, skip the trade

## TRADE EXECUTION RULES

**Entry**
- Enter at candle close, never mid-candle

**Stop Loss**
- Place at recent swing high (for shorts) or swing low (for longs)

**Target**
- Always 2x your risk (2R)

**Trade Management**
- Take half profit at target
- Move stop to breakeven on remaining position
- Trail remaining position with opposite MACD crossover
---

**pinescript for strategy: 7**  
```pinescript
//@version=5
indicator("MACD Money Map - Indicator", shorttitle="MACD MM", overlay=false)

// ========== INPUTS ==========
// MACD Settings
fastLength = input.int(12, "Fast Length", minval=1, group="MACD Settings")
slowLength = input.int(26, "Slow Length", minval=1, group="MACD Settings")
signalSmoothing = input.int(9, "Signal Smoothing", minval=1, group="MACD Settings")
macdSource = input.source(close, "Source", group="MACD Settings")

// System 1: Trend System
useTrendSystem = input.bool(true, "Enable Trend System", group="System 1: Trend")
minMacdLevel = input.float(0.5, "Min MACD Level for Entry", step=0.1, group="System 1: Trend")
confirmCandles = input.int(2, "Confirmation Candles After Crossover", minval=1, maxval=5, group="System 1: Trend")

// System 2: Reversal System
useReversalSystem = input.bool(true, "Enable Reversal System", group="System 2: Reversal")
lookbackPeriod = input.int(14, "Divergence Lookback Period", minval=5, group="System 2: Reversal")
histogramConfirm = input.bool(true, "Require Histogram Confirmation", group="System 2: Reversal")

// System 3: Multi-Timeframe
useMultiTimeframe = input.bool(true, "Enable Multi-Timeframe Filter", group="System 3: MTF")
higherTF1 = input.timeframe("240", "Higher Timeframe 1", group="System 3: MTF")
higherTF2 = input.timeframe("D", "Higher Timeframe 2", group="System 3: MTF")

// Display Options
showSignals = input.bool(true, "Show Entry Signals", group="Display")
showDivergence = input.bool(true, "Show Divergence Markers", group="Display")
showInfoTable = input.bool(true, "Show Info Table", group="Display")

// ========== MACD CALCULATION ==========
[macdLine, signalLine, histLine] = ta.macd(macdSource, fastLength, slowLength, signalSmoothing)

// Higher Timeframe MACD
[macdHTF1, signalHTF1, histHTF1] = request.security(syminfo.tickerid, higherTF1, ta.macd(macdSource, fastLength, slowLength, signalSmoothing))
[macdHTF2, signalHTF2, histHTF2] = request.security(syminfo.tickerid, higherTF2, ta.macd(macdSource, fastLength, slowLength, signalSmoothing))

// ========== SYSTEM 1: TREND SYSTEM ==========
// Zero line rules
aboveZero = macdLine > 0
belowZero = macdLine < 0

// Crossovers
bullCross = ta.crossover(macdLine, signalLine)
bearCross = ta.crossunder(macdLine, signalLine)

// Far from zero requirement
bullCrossFar = bullCross and macdLine > minMacdLevel
bearCrossFar = bearCross and macdLine < -minMacdLevel

// Confirmation candles
var int bullCrossConfirmCount = 0
var int bearCrossConfirmCount = 0

if bullCrossFar
    bullCrossConfirmCount := 1
else if bullCrossConfirmCount > 0 and bullCrossConfirmCount < confirmCandles and macdLine > signalLine
    bullCrossConfirmCount += 1
else if macdLine < signalLine
    bullCrossConfirmCount := 0

if bearCrossFar
    bearCrossConfirmCount := 1
else if bearCrossConfirmCount > 0 and bearCrossConfirmCount < confirmCandles and macdLine < signalLine
    bearCrossConfirmCount += 1
else if macdLine > signalLine
    bearCrossConfirmCount := 0

trendLongSignal = useTrendSystem and aboveZero and bullCrossConfirmCount == confirmCandles
trendShortSignal = useTrendSystem and belowZero and bearCrossConfirmCount == confirmCandles

// ========== SYSTEM 2: REVERSAL SYSTEM ==========
// Pivot detection
pivotHigh = ta.pivothigh(high, lookbackPeriod, lookbackPeriod)
pivotLow = ta.pivotlow(low, lookbackPeriod, lookbackPeriod)
macdPivotHigh = ta.pivothigh(macdLine, lookbackPeriod, lookbackPeriod)
macdPivotLow = ta.pivotlow(macdLine, lookbackPeriod, lookbackPeriod)

// Divergence detection
var float lastPriceHigh = na
var float lastMacdHigh = na
var float lastPriceLow = na
var float lastMacdLow = na

if not na(pivotHigh)
    lastPriceHigh := high[lookbackPeriod]
if not na(macdPivotHigh)
    lastMacdHigh := macdLine[lookbackPeriod]
if not na(pivotLow)
    lastPriceLow := low[lookbackPeriod]
if not na(macdPivotLow)
    lastMacdLow := macdLine[lookbackPeriod]

// Bearish divergence: higher price high, lower MACD high
bearishDiv = not na(pivotHigh) and not na(lastPriceHigh) and 
             not na(macdPivotHigh) and not na(lastMacdHigh) and
             high[lookbackPeriod] > lastPriceHigh and 
             macdLine[lookbackPeriod] < lastMacdHigh

// Bullish divergence: lower price low, higher MACD low
bullishDiv = not na(pivotLow) and not na(lastPriceLow) and 
             not na(macdPivotLow) and not na(lastMacdLow) and
             low[lookbackPeriod] < lastPriceLow and 
             macdLine[lookbackPeriod] > lastMacdLow

// Histogram patterns
histFlipBull = histLine > 0 and histLine[1] <= 0
histFlipBear = histLine < 0 and histLine[1] >= 0

// Reversal signals with histogram confirmation
reversalLongSignal = useReversalSystem and bullishDiv and (not histogramConfirm or histFlipBull)
reversalShortSignal = useReversalSystem and bearishDiv and (not histogramConfirm or histFlipBear)

// ========== SYSTEM 3: MULTI-TIMEFRAME CONFIRMATION ==========
htf1Bullish = macdHTF1 > 0
htf1Bearish = macdHTF1 < 0
htf2Bullish = macdHTF2 > 0
htf2Bearish = macdHTF2 < 0

mtfLongConfirm = not useMultiTimeframe or (htf1Bullish and htf2Bullish)
mtfShortConfirm = not useMultiTimeframe or (htf1Bearish and htf2Bearish)

// ========== COMBINED SIGNALS ==========
longSignal = (trendLongSignal or reversalLongSignal) and mtfLongConfirm
shortSignal = (trendShortSignal or reversalShortSignal) and mtfShortConfirm

// ========== PLOTTING - MACD INDICATOR ==========
// Zero line and thresholds
hline(0, "Zero Line", color=color.new(color.gray, 0), linestyle=hline.style_solid, linewidth=2)
hline(minMacdLevel, "Upper Threshold", color=color.new(color.green, 70), linestyle=hline.style_dashed)
hline(-minMacdLevel, "Lower Threshold", color=color.new(color.red, 70), linestyle=hline.style_dashed)

// MACD Lines
plot(macdLine, "MACD Line", color=color.new(color.blue, 0), linewidth=3)
plot(signalLine, "Signal Line", color=color.new(color.orange, 0), linewidth=3)

// Histogram with gradient coloring
histColor = histLine >= 0 ? 
     (histLine > histLine[1] ? color.new(color.green, 0) : color.new(color.green, 50)) :
     (histLine < histLine[1] ? color.new(color.red, 0) : color.new(color.red, 50))
plot(histLine, "Histogram", color=histColor, style=plot.style_histogram, linewidth=5)

// Crossover signals on MACD
plotshape(showSignals and longSignal, "Long Signal", shape.triangleup, location.bottom, color.new(color.lime, 0), size=size.normal, text="BUY")
plotshape(showSignals and shortSignal, "Short Signal", shape.triangledown, location.top, color.new(color.red, 0), size=size.normal, text="SELL")

// Divergence markers on MACD
plotshape(showDivergence and bullishDiv, "Bull Div", shape.labelup, location.bottom, color.new(color.aqua, 20), text="BULL DIV", textcolor=color.white, size=size.small)
plotshape(showDivergence and bearishDiv, "Bear Div", shape.labeldown, location.top, color.new(color.fuchsia, 20), text="BEAR DIV", textcolor=color.white, size=size.small)

// Background coloring for trend direction
bgcolor(aboveZero ? color.new(color.green, 95) : color.new(color.red, 95), title="Trend Background")

// ========== INFO TABLE ==========
if showInfoTable
    var table infoTable = table.new(position.top_right, 2, 6, border_width=1)
    
    if barstate.islast
        table.cell(infoTable, 0, 0, "Trend", text_color=color.white, bgcolor=color.new(color.blue, 50))
        table.cell(infoTable, 1, 0, aboveZero ? "BULLISH ↑" : "BEARISH ↓", 
             text_color=color.white, bgcolor=aboveZero ? color.new(color.green, 30) : color.new(color.red, 30))
        
        table.cell(infoTable, 0, 1, "MACD", text_color=color.white, bgcolor=color.new(color.blue, 50))
        table.cell(infoTable, 1, 1, str.tostring(math.round(macdLine, 5)), text_color=color.white, bgcolor=color.new(color.gray, 70))
        
        table.cell(infoTable, 0, 2, "Signal", text_color=color.white, bgcolor=color.new(color.blue, 50))
        table.cell(infoTable, 1, 2, str.tostring(math.round(signalLine, 5)), text_color=color.white, bgcolor=color.new(color.gray, 70))
        
        table.cell(infoTable, 0, 3, "Histogram", text_color=color.white, bgcolor=color.new(color.blue, 50))
        table.cell(infoTable, 1, 3, str.tostring(math.round(histLine, 5)), 
             text_color=color.white, bgcolor=histLine >= 0 ? color.new(color.green, 50) : color.new(color.red, 50))
        
        table.cell(infoTable, 0, 4, "HTF1 (" + higherTF1 + ")", text_color=color.white, bgcolor=color.new(color.blue, 50))
        table.cell(infoTable, 1, 4, htf1Bullish ? "BULL" : "BEAR", 
             text_color=color.white, bgcolor=htf1Bullish ? color.new(color.green, 50) : color.new(color.red, 50))
        
        table.cell(infoTable, 0, 5, "HTF2 (" + higherTF2 + ")", text_color=color.white, bgcolor=color.new(color.blue, 50))
        table.cell(infoTable, 1, 5, htf2Bullish ? "BULL" : "BEAR", 
             text_color=color.white, bgcolor=htf2Bullish ? color.new(color.green, 50) : color.new(color.red, 50))

// ========== ALERTS ==========
alertcondition(longSignal, "Long Entry", "MACD Money Map: Long Signal - Enter LONG")
alertcondition(shortSignal, "Short Entry", "MACD Money Map: Short Signal - Enter SHORT")
alertcondition(bullishDiv, "Bullish Divergence", "MACD Money Map: Bullish Divergence Detected")
alertcondition(bearishDiv, "Bearish Divergence", "MACD Money Map: Bearish Divergence Detected")
alertcondition(bullCross, "Bullish Crossover", "MACD Money Map: Bullish Crossover")
alertcondition(bearCross, "Bearish Crossover", "MACD Money Map: Bearish Crossover")

// Export signals for strategies
plot(longSignal ? 1 : 0, "Long Signal Export", display=display.none)
plot(shortSignal ? 1 : 0, "Short Signal Export", display=display.none)
```
How to trade with this MACD strategy:

### **Reading the Signals:**

**1. BUY Signals (Green triangles pointing up)**
- Appears at the bottom of the MACD panel
- Label says "BUY"
- This means all 3 systems have aligned for a long entry

**2. SELL Signals (Red triangles pointing down)**
- Appears at the top of the MACD panel
- Label says "SELL"
- This means all 3 systems have aligned for a short entry

**3. Divergence Warnings**
- "BULL DIV" = Bullish reversal coming (potential bottom)
- "BEAR DIV" = Bearish reversal coming (potential top)

### **The Info Table (Top Right)**
Shows you:
- **Trend**: BULLISH ↑ or BEARISH ↓ (your directional bias)
- **MACD, Signal, Histogram values**
- **HTF1 (4H)**: Higher timeframe trend
- **HTF2 (Daily)**: Highest timeframe trend

### **Manual Trading Steps:**

**When you see a BUY signal:**
1. ✅ Confirm the signal appears on the MACD panel
2. ✅ Check the price chart - is price at support or a key level?
3. ✅ Look at the Info Table - are all timeframes aligned?
4. ✅ Enter LONG on the next candle close
5. 📍 Place stop loss below recent swing low
6. 🎯 Set target at 2x your risk (measure: entry - stop = risk, then entry + 2×risk = target)

**When you see a SELL signal:**
1. ✅ Confirm the signal appears on the MACD panel
2. ✅ Check the price chart - is price at resistance or a key level?
3. ✅ Look at the Info Table - are all timeframes aligned?
4. ✅ Enter SHORT on the next candle close
5. 📍 Place stop loss above recent swing high
6. 🎯 Set target at 2x your risk

### **Setting Up Alerts:**

1. Click the **3 dots** next to the indicator name
2. Select **"Add Alert"**
3. Choose the alert type:
   - "Long Entry" - for buy signals
   - "Short Entry" - for sell signals
   - "Bullish Divergence" - for reversal warnings
4. Set it to notify you via app, email, or webhook

### **Best Practices:**

✅ **Only trade in the direction of the trend** (check background color - green = longs only, red = shorts only)

✅ **Wait for confirmation** - Don't jump in immediately, watch for 1-2 candles after the signal

✅ **Check higher timeframes** - All should be aligned in the Info Table

✅ **Use proper position sizing** - Risk 1-2% of your account per trade

✅ **Be patient** - Good setups don't come every hour. Wait for quality signals.

### **What NOT to Do:**

❌ Don't take every signal blindly - confirm with price action

❌ Don't trade near the zero line (chop zone) - wait for clear trends

❌ Don't ignore the Info Table - if timeframes conflict, skip the trade

❌ Don't revenge trade after a loss - wait for the next setup

---

Would you like me to also create a **simple overlay version** that puts small arrows directly on the price chart so you can see both at once?
### Yes, overlay script that print directly to chart    
```pinescript
//@version=5
indicator("MACD Money Map - Overlay", shorttitle="MACD Signals", overlay=true)

// ========== INPUTS ==========
// MACD Settings
fastLength = input.int(12, "Fast Length", minval=1, group="MACD Settings")
slowLength = input.int(26, "Slow Length", minval=1, group="MACD Settings")
signalSmoothing = input.int(9, "Signal Smoothing", minval=1, group="MACD Settings")
macdSource = input.source(close, "Source", group="MACD Settings")

// System 1: Trend System
useTrendSystem = input.bool(true, "Enable Trend System", group="System 1: Trend")
minMacdLevel = input.float(0.5, "Min MACD Level for Entry", step=0.1, group="System 1: Trend")
confirmCandles = input.int(2, "Confirmation Candles After Crossover", minval=1, maxval=5, group="System 1: Trend")

// System 2: Reversal System
useReversalSystem = input.bool(true, "Enable Reversal System", group="System 2: Reversal")
lookbackPeriod = input.int(14, "Divergence Lookback Period", minval=5, group="System 2: Reversal")
histogramConfirm = input.bool(true, "Require Histogram Confirmation", group="System 2: Reversal")

// System 3: Multi-Timeframe
useMultiTimeframe = input.bool(true, "Enable Multi-Timeframe Filter", group="System 3: MTF")
higherTF1 = input.timeframe("240", "Higher Timeframe 1", group="System 3: MTF")
higherTF2 = input.timeframe("D", "Higher Timeframe 2", group="System 3: MTF")

// Display Options
showLabels = input.bool(true, "Show Signal Labels", group="Display")
showDivergence = input.bool(true, "Show Divergence", group="Display")
showStopLoss = input.bool(true, "Show Stop Loss Levels", group="Display")
showTargets = input.bool(true, "Show Target Levels", group="Display")

// Risk Management
riskRewardRatio = input.float(2.0, "Risk:Reward Ratio", minval=1.0, step=0.1, group="Risk Management")
atrMultiplier = input.float(1.5, "ATR Multiplier for Stop Loss", step=0.1, group="Risk Management")
atrLength = input.int(14, "ATR Length", group="Risk Management")

// ========== MACD CALCULATION ==========
[macdLine, signalLine, histLine] = ta.macd(macdSource, fastLength, slowLength, signalSmoothing)

// Higher Timeframe MACD
[macdHTF1, signalHTF1, histHTF1] = request.security(syminfo.tickerid, higherTF1, ta.macd(macdSource, fastLength, slowLength, signalSmoothing))
[macdHTF2, signalHTF2, histHTF2] = request.security(syminfo.tickerid, higherTF2, ta.macd(macdSource, fastLength, slowLength, signalSmoothing))

// ========== SYSTEM 1: TREND SYSTEM ==========
aboveZero = macdLine > 0
belowZero = macdLine < 0

bullCross = ta.crossover(macdLine, signalLine)
bearCross = ta.crossunder(macdLine, signalLine)

bullCrossFar = bullCross and macdLine > minMacdLevel
bearCrossFar = bearCross and macdLine < -minMacdLevel

var int bullCrossConfirmCount = 0
var int bearCrossConfirmCount = 0

if bullCrossFar
    bullCrossConfirmCount := 1
else if bullCrossConfirmCount > 0 and bullCrossConfirmCount < confirmCandles and macdLine > signalLine
    bullCrossConfirmCount += 1
else if macdLine < signalLine
    bullCrossConfirmCount := 0

if bearCrossFar
    bearCrossConfirmCount := 1
else if bearCrossConfirmCount > 0 and bearCrossConfirmCount < confirmCandles and macdLine < signalLine
    bearCrossConfirmCount += 1
else if macdLine > signalLine
    bearCrossConfirmCount := 0

trendLongSignal = useTrendSystem and aboveZero and bullCrossConfirmCount == confirmCandles
trendShortSignal = useTrendSystem and belowZero and bearCrossConfirmCount == confirmCandles

// ========== SYSTEM 2: REVERSAL SYSTEM ==========
pivotHigh = ta.pivothigh(high, lookbackPeriod, lookbackPeriod)
pivotLow = ta.pivotlow(low, lookbackPeriod, lookbackPeriod)
macdPivotHigh = ta.pivothigh(macdLine, lookbackPeriod, lookbackPeriod)
macdPivotLow = ta.pivotlow(macdLine, lookbackPeriod, lookbackPeriod)

var float lastPriceHigh = na
var float lastMacdHigh = na
var float lastPriceLow = na
var float lastMacdLow = na

if not na(pivotHigh)
    lastPriceHigh := high[lookbackPeriod]
if not na(macdPivotHigh)
    lastMacdHigh := macdLine[lookbackPeriod]
if not na(pivotLow)
    lastPriceLow := low[lookbackPeriod]
if not na(macdPivotLow)
    lastMacdLow := macdLine[lookbackPeriod]

bearishDiv = not na(pivotHigh) and not na(lastPriceHigh) and 
             not na(macdPivotHigh) and not na(lastMacdHigh) and
             high[lookbackPeriod] > lastPriceHigh and 
             macdLine[lookbackPeriod] < lastMacdHigh

bullishDiv = not na(pivotLow) and not na(lastPriceLow) and 
             not na(macdPivotLow) and not na(lastMacdLow) and
             low[lookbackPeriod] < lastPriceLow and 
             macdLine[lookbackPeriod] > lastMacdLow

histFlipBull = histLine > 0 and histLine[1] <= 0
histFlipBear = histLine < 0 and histLine[1] >= 0

reversalLongSignal = useReversalSystem and bullishDiv and (not histogramConfirm or histFlipBull)
reversalShortSignal = useReversalSystem and bearishDiv and (not histogramConfirm or histFlipBear)

// ========== SYSTEM 3: MULTI-TIMEFRAME CONFIRMATION ==========
htf1Bullish = macdHTF1 > 0
htf1Bearish = macdHTF1 < 0
htf2Bullish = macdHTF2 > 0
htf2Bearish = macdHTF2 < 0

mtfLongConfirm = not useMultiTimeframe or (htf1Bullish and htf2Bullish)
mtfShortConfirm = not useMultiTimeframe or (htf1Bearish and htf2Bearish)

// ========== COMBINED SIGNALS ==========
longSignal = (trendLongSignal or reversalLongSignal) and mtfLongConfirm
shortSignal = (trendShortSignal or reversalShortSignal) and mtfShortConfirm

// ========== STOP LOSS & TARGET CALCULATION ==========
atr = ta.atr(atrLength)

var float longEntry = na
var float longStop = na
var float longTarget = na
var float shortEntry = na
var float shortStop = na
var float shortTarget = na

if longSignal
    longEntry := close
    longStop := low - (atr * atrMultiplier)
    riskAmount = longEntry - longStop
    longTarget := longEntry + (riskAmount * riskRewardRatio)

if shortSignal
    shortEntry := close
    shortStop := high + (atr * atrMultiplier)
    riskAmount = shortStop - shortEntry
    shortTarget := shortEntry - (riskAmount * riskRewardRatio)

// ========== PLOTTING ON PRICE CHART ==========

// Entry Signals - Arrows
plotshape(longSignal, "Long Entry", shape.triangleup, location.belowbar, 
     color.new(color.green, 0), size=size.normal)
plotshape(shortSignal, "Short Entry", shape.triangledown, location.abovebar, 
     color.new(color.red, 0), size=size.normal)

// Entry Labels
plotshape(showLabels and longSignal, "Long Label", shape.labelup, location.belowbar, 
     color.new(color.green, 20), text="BUY", textcolor=color.white, size=size.small)

plotshape(showLabels and shortSignal, "Short Label", shape.labeldown, location.abovebar, 
     color.new(color.red, 20), text="SELL", textcolor=color.white, size=size.small)

// Divergence Markers
plotshape(showDivergence and bullishDiv, "Bullish Divergence", shape.diamond, location.belowbar, 
     color.new(color.aqua, 30), size=size.tiny, text="DIV")
plotshape(showDivergence and bearishDiv, "Bearish Divergence", shape.diamond, location.abovebar, 
     color.new(color.fuchsia, 30), size=size.tiny, text="DIV")

// Stop Loss Lines
var line longStopLine = na
var line shortStopLine = na
var label longStopLabel = na
var label shortStopLabel = na

if longSignal and showStopLoss
    if not na(longStopLine)
        line.delete(longStopLine)
    if not na(longStopLabel)
        label.delete(longStopLabel)
    longStopLine := line.new(bar_index, longStop, bar_index + 20, longStop, 
         color=color.new(color.red, 0), width=2, style=line.style_dashed)
    longStopLabel := label.new(bar_index + 20, longStop, "SL: " + str.tostring(longStop, format.mintick), 
         style=label.style_label_left, color=color.red, textcolor=color.white, size=size.tiny)

if shortSignal and showStopLoss
    if not na(shortStopLine)
        line.delete(shortStopLine)
    if not na(shortStopLabel)
        label.delete(shortStopLabel)
    shortStopLine := line.new(bar_index, shortStop, bar_index + 20, shortStop, 
         color=color.new(color.red, 0), width=2, style=line.style_dashed)
    shortStopLabel := label.new(bar_index + 20, shortStop, "SL: " + str.tostring(shortStop, format.mintick), 
         style=label.style_label_left, color=color.red, textcolor=color.white, size=size.tiny)

// Target Lines
var line longTargetLine = na
var line shortTargetLine = na
var label longTargetLabel = na
var label shortTargetLabel = na

if longSignal and showTargets
    if not na(longTargetLine)
        line.delete(longTargetLine)
    if not na(longTargetLabel)
        label.delete(longTargetLabel)
    longTargetLine := line.new(bar_index, longTarget, bar_index + 20, longTarget, 
         color=color.new(color.green, 0), width=2, style=line.style_solid)
    longTargetLabel := label.new(bar_index + 20, longTarget, "TP: " + str.tostring(longTarget, format.mintick), 
         style=label.style_label_left, color=color.green, textcolor=color.white, size=size.tiny)

if shortSignal and showTargets
    if not na(shortTargetLine)
        line.delete(shortTargetLine)
    if not na(shortTargetLabel)
        label.delete(shortTargetLabel)
    shortTargetLine := line.new(bar_index, shortTarget, bar_index + 20, shortTarget, 
         color=color.new(color.green, 0), width=2, style=line.style_solid)
    shortTargetLabel := label.new(bar_index + 20, shortTarget, "TP: " + str.tostring(shortTarget, format.mintick), 
         style=label.style_label_left, color=color.green, textcolor=color.white, size=size.tiny)

// ========== BACKGROUND COLORING ==========
// Subtle background to show trend direction
bgcolor(aboveZero ? color.new(color.green, 97) : color.new(color.red, 97), title="Trend Background")

// ========== INFO TABLE ==========
var table infoTable = table.new(position.bottom_right, 3, 4, border_width=1)

if barstate.islast
    // Header
    table.cell(infoTable, 0, 0, "MACD STATUS", text_color=color.white, bgcolor=color.new(color.blue, 30), text_size=size.small)
    table.cell(infoTable, 1, 0, "", text_color=color.white, bgcolor=color.new(color.blue, 30))
    table.cell(infoTable, 2, 0, "", text_color=color.white, bgcolor=color.new(color.blue, 30))
    
    // Trend
    table.cell(infoTable, 0, 1, "Trend:", text_color=color.white, bgcolor=color.new(color.gray, 70), text_size=size.tiny)
    table.cell(infoTable, 1, 1, aboveZero ? "BULLISH" : "BEARISH", 
         text_color=color.white, bgcolor=aboveZero ? color.new(color.green, 40) : color.new(color.red, 40), text_size=size.tiny)
    table.cell(infoTable, 2, 1, aboveZero ? "↑" : "↓", 
         text_color=color.white, bgcolor=aboveZero ? color.new(color.green, 40) : color.new(color.red, 40), text_size=size.small)
    
    // HTF1
    table.cell(infoTable, 0, 2, higherTF1 + ":", text_color=color.white, bgcolor=color.new(color.gray, 70), text_size=size.tiny)
    table.cell(infoTable, 1, 2, htf1Bullish ? "BULL" : "BEAR", 
         text_color=color.white, bgcolor=htf1Bullish ? color.new(color.green, 60) : color.new(color.red, 60), text_size=size.tiny)
    table.cell(infoTable, 2, 2, htf1Bullish ? "↑" : "↓", 
         text_color=color.white, bgcolor=htf1Bullish ? color.new(color.green, 60) : color.new(color.red, 60), text_size=size.tiny)
    
    // HTF2
    table.cell(infoTable, 0, 3, higherTF2 + ":", text_color=color.white, bgcolor=color.new(color.gray, 70), text_size=size.tiny)
    table.cell(infoTable, 1, 3, htf2Bullish ? "BULL" : "BEAR", 
         text_color=color.white, bgcolor=htf2Bullish ? color.new(color.green, 60) : color.new(color.red, 60), text_size=size.tiny)
    table.cell(infoTable, 2, 3, htf2Bullish ? "↑" : "↓", 
         text_color=color.white, bgcolor=htf2Bullish ? color.new(color.green, 60) : color.new(color.red, 60), text_size=size.tiny)

// ========== ALERTS ==========
alertcondition(longSignal, "Long Entry", "🟢 MACD Money Map: LONG Signal - Entry: {{close}}")
alertcondition(shortSignal, "Short Entry", "🔴 MACD Money Map: SHORT Signal - Entry: {{close}}")
alertcondition(bullishDiv, "Bullish Divergence", "💎 MACD Money Map: Bullish Divergence - Watch for Reversal")
alertcondition(bearishDiv, "Bearish Divergence", "💎 MACD Money Map: Bearish Divergence - Watch for Reversal")
```

---
---
---

Strategy: 8  
- 1.  
```pinescript
//@version=5
strategy("RSI + S/R Rejection Strategy", overlay=true, initial_capital=10000, default_qty_type=strategy.percent_of_equity, default_qty_value=10)

// --- Input Settings ---
rsiLength = input.int(14, "RSI Length")
rsiOverbought = input.int(70, "RSI Overbought Level")
rsiOversold = input.int(30, "RSI Oversold Level")
useRSIFilter = input.bool(true, "Use RSI Filter?")

// Pivot Settings for Support/Resistance
pivotLookback = input.int(5, "S/R Pivot Lookback")
wickSizePercent = input.float(50.0, "Min Wick Size % of Candle Range", minval=0.0, maxval=100.0)

// --- Indicators ---
rsiValue = ta.rsi(close, rsiLength)

// Identify Support and Resistance via Pivots
pivotHigh = ta.pivothigh(high, pivotLookback, pivotLookback)
pivotLow = ta.pivotlow(low, pivotLookback, pivotLookback)

// Track the most recent levels
var float lastResistance = na
var float lastSupport = na

if not na(pivotHigh)
    lastResistance := pivotHigh
if not na(pivotLow)
    lastSupport := pivotLow

// --- Rejection Candle Logic ---
candleRange = high - low
upperWick = high - math.max(open, close)
lowerWick = math.min(open, close) - low

isBullishRejection = lowerWick > (candleRange * (wickSizePercent / 100))
isBearishRejection = upperWick > (candleRange * (wickSizePercent / 100))

// --- Entry Conditions ---
// RSI Logic (can be toggled off)
rsiLongConf = not useRSIFilter or (rsiValue < rsiOversold)
rsiShortConf = not useRSIFilter or (rsiValue > rsiOverbought)

// Price touching S/R (within a small threshold)
touchingSupport = low <= lastSupport
touchingResistance = high >= lastResistance

longCondition = touchingSupport and rsiLongConf and isBullishRejection
shortCondition = touchingResistance and rsiShortConf and isBearishRejection

// --- Execution ---
if longCondition
    strategy.entry("Long", strategy.long)

if shortCondition
    strategy.entry("Short", strategy.short)

// --- Visuals (Plotting) ---
plotshape(longCondition, title="Buy Signal", style=shape.triangleup, location=location.belowbar, color=color.green, size=size.small, text="BUY")
plotshape(shortCondition, title="Sell Signal", style=shape.triangledown, location=location.abovebar, color=color.red, size=size.small, text="SELL")

// Plot S/R lines for visual confirmation
plot(lastResistance, "Resistance Level", color=color.new(color.red, 50), style=plot.style_linebr)
plot(lastSupport, "Support Level", color=color.new(color.green, 50), style=plot.style_linebr)

// --- Alerts ---
alertcondition(longCondition, title="Long Signal Alert", message="RSI/Support Bullish Rejection on {{ticker}}")
alertcondition(shortCondition, title="Short Signal Alert", message="RSI/Resistance Bearish Rejection on {{ticker}}")

```
**How the Logic Works**

* **Support & Resistance:** The script uses `ta.pivothigh` and `ta.pivotlow`. These identify the "peaks" and "valleys" on your chart. When price returns to these levels, it triggers the first part of your logic.
* **Rejection Candles:** Instead of just looking for a green or red candle, the script calculates the **wick size**. If the bottom wick is more than 50% of the total candle size (customizable in settings), it is flagged as a "Bullish Rejection."
* **RSI Toggle:** In the **Settings > Inputs** tab, you will find a checkbox for "Use RSI Filter." Unchecking this will allow the strategy to fire signals based purely on price action at S/R levels.
* **Visuals:** I have added horizontal lines that track the last known support and resistance levels so you can see exactly what the script is reacting to.

- 2.  
```pinescript
//@version=5
strategy("RSI + S/R + Trend Filter", overlay=true, initial_capital=10000, default_qty_type=strategy.percent_of_equity, default_qty_value=10)

// --- Input Settings ---
rsiLength = input.int(14, "RSI Length")
rsiOverbought = input.int(70, "RSI Overbought Level")
rsiOversold = input.int(30, "RSI Oversold Level")
useRSIFilter = input.bool(true, "Use RSI Filter?")

// Trend Filter Settings
useTrendFilter = input.bool(true, "Use EMA 200 Trend Filter?")
emaTrendLen = input.int(200, "EMA Trend Length")

// Pivot Settings for Support/Resistance
pivotLookback = input.int(5, "S/R Pivot Lookback")
wickSizePercent = input.float(40.0, "Min Wick Size % (Rejection)", minval=0.0, maxval=100.0)

// --- Indicators ---
rsiValue = ta.rsi(close, rsiLength)
emaTrend = ta.ema(close, emaTrendLen)

// Identify Support and Resistance
pivotHigh = ta.pivothigh(high, pivotLookback, pivotLookback)
pivotLow = ta.pivotlow(low, pivotLookback, pivotLookback)

var float lastResistance = na
var float lastSupport = na
if not na(pivotHigh)
    lastResistance := pivotHigh
if not na(pivotLow)
    lastSupport := pivotLow

// --- Rejection & Trend Logic ---
candleRange = high - low
upperWick = high - math.max(open, close)
lowerWick = math.min(open, close) - low

isBullishRejection = lowerWick > (candleRange * (wickSizePercent / 100))
isBearishRejection = upperWick > (candleRange * (wickSizePercent / 100))

// Trend Confirmation Logic
isUptrend = not useTrendFilter or (close > emaTrend)
isDowntrend = not useTrendFilter or (close < emaTrend)

// --- Entry Conditions ---
rsiLongConf = not useRSIFilter or (rsiValue < rsiOversold)
rsiShortConf = not useRSIFilter or (rsiValue > rsiOverbought)

longCondition = (low <= lastSupport) and rsiLongConf and isBullishRejection and isUptrend
shortCondition = (high >= lastResistance) and rsiShortConf and isBearishRejection and isDowntrend

// --- Execution ---
if longCondition
    strategy.entry("Long", strategy.long)

if shortCondition
    strategy.entry("Short", strategy.short)

// --- Visuals ---
plot(emaTrend, "Trend EMA", color=color.new(color.gray, 50), linewidth=2)
plotshape(longCondition, title="Buy Signal", style=shape.triangleup, location=location.belowbar, color=color.green, size=size.small, text="BUY")
plotshape(shortCondition, title="Sell Signal", style=shape.triangledown, location=location.abovebar, color=color.red, size=size.small, text="SELL")

// Alerts
alertcondition(longCondition, "Trend-Aligned Long", "Bullish Rejection at Support + Trend")
alertcondition(shortCondition, "Trend-Aligned Short", "Bearish Rejection at Resistance + Trend")

```
---
---
---

Strategy: 9  

# 🧠 INSTITUTIONAL-STYLE RULESET

*(Pullback Continuation Strategy)*

---

## 1️⃣ MARKET REGIME FILTER (MANDATORY)

> **Purpose:** Decide *if* we are allowed to trade — not *where*.

### 1.1 Trend Qualification

**Uptrend is valid only if ALL are true:**

1. **EMA Alignment**

   * EMA(20) > EMA(50)

2. **Trend Persistence**

   * EMA(50) has been rising for **at least 5 bars**

3. **Price Acceptance**

   * Close is above EMA(50)

4. **Trend Strength (Anti-Chop)**

   * Distance between EMA(20) and EMA(50) ≥ **0.5 × ATR(14)**

📌 **Why this matters**

* Prevents trading during EMA compression
* Eliminates false trends
* Institutional systems *do not trade transitions*

---

### 1.2 No-Trade Conditions (Explicit)

**Do NOT trade if:**

* EMA(20) and EMA(50) are flat or converging
* Price is oscillating around EMA(50)
* ATR is expanding while EMA slope is flat (volatility chop)

---

## 2️⃣ CONTEXT: PULLBACK LOCATION (MANDATORY)

> **Purpose:** Ensure we are buying *discounts* inside a valid trend.

### 2.1 Valid Pullback Definition

A pullback is valid ONLY if:

1. **Prior Expansion Exists**

   * Price moved **≥ 1.0 × ATR above EMA(20)** within the last 10 bars

2. **Controlled Retracement**

   * Price retraces into the **EMA(20)–EMA(50) zone**
   * Lowest low of pullback is **no deeper than EMA(50) − 0.3 × ATR**

3. **Pullback Structure**

   * Pullback consists of **lower highs or overlapping candles**
   * No strong bearish momentum candles

📌 **What this removes**

* Random EMA touches
* Buying weak trends
* Buying late-stage exhaustion moves

---

## 3️⃣ SETUP FILTERS (CONDITIONAL, TIME-LIMITED)

> **Purpose:** Confirm momentum is *pausing*, not *reversing*.

---

### 3.1 RSI State Filter (MANDATORY)

RSI(14) must:

1. Stay **above 40** throughout pullback
2. Make a **higher low relative to the start of pullback**
3. Begin to **turn upward by at least 2 RSI points**

📌 **Why**

* RSI hooks alone are noise
* We want **momentum compression → expansion**

---

### 3.2 Momentum Divergence (OPTIONAL, CONFIRMATION ONLY)

> Divergence is **never required**, only supportive.

Bullish divergence is valid only if:

1. Price makes a **lower low within pullback**
2. RSI makes a **higher low on the SAME bar**
3. Divergence occurs **inside EMA zone**
4. Occurs within **last 5 bars of pullback**

📌 **Institutional truth**

* Divergence increases confidence, not expectancy
* Never trade divergence alone

---

## 4️⃣ TRIGGER: ENTRY EXECUTION (STRICT)

> **Purpose:** Define the exact moment risk is taken.

### 4.1 Rejection + Confirmation Model

**Step 1 — Rejection Candle**
At least **2 of the following must be true** on the same bar:

* Lower wick ≥ 1.2 × body
* Low sweeps prior pullback low and closes back above it
* Close is in the top 30% of candle range

**Step 2 — Confirmation Close**

* Next candle closes **above the rejection candle high**

📌 **Why**

* Removes premature entries
* Confirms actual demand, not just absorption

---

## 5️⃣ ENTRY RULE (LONG)

Enter **LONG** at the **close of the confirmation candle** ONLY IF:

* Market regime is valid
* Pullback context is intact
* RSI filter remains valid
* (Optional) Divergence present
* Rejection + confirmation completed
* Entry occurs **within 3 bars max** after pullback low

⛔ **Invalidate setup immediately if:**

* Price closes below EMA(50)
* RSI closes below 40
* Pullback exceeds 8 bars
* Strong bearish momentum candle appears

---

## 6️⃣ RISK MANAGEMENT (FIXED, NON-NEGOTIABLE)

### 6.1 Stop Loss

* Stop = **Lowest low of pullback − 0.3 × ATR**

📌 More adaptive than static ATR stops.

---

### 6.2 Take Profit

Two-tier model (institutional standard):

* TP1 = **1.5R** → partial close
* TP2 = **3.0R** → final exit

OR

* Trail stop using EMA(20) after TP1

---

## 7️⃣ SHORT TRADES (MIRRORED, NOT FLIPPED)

Everything above applies inversely:

* Downtrend confirmation
* Pullback into EMA resistance
* RSI below 60, holding above 30
* Bearish rejection + confirmation
* Stops above pullback high

📌 No symmetry shortcuts — bearish markets behave differently.



