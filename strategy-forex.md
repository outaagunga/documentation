
- give bulleted summary of this [e.g transcript]:  
- give a simplified version that actually works for beginners  
- give a step-by-step checklist  
- Give your output in this format
- Spot hidden assumptions/ Structural logic conflict/ Logical ambiguity/Blind spot/ Explore multiple perspectives/ Asking the right question?  
- I need help with [your task]. Before you start, ask me 3 questions to make sure you understand exactly what I need.
- Which part of the code is ambiguous  
- Translate this ruleset into pinescript friendly logic  
- Pine Script doesn't allow multi-line function calls. Everything needs to be on one line  
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



