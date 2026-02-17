
# FVG Sessions Strategy — Implementation Guide
> Converting `FVG Sessions [LuxAlgo]` from Indicator to Strategy (Pine Script v5)

---

## Overview

This guide converts the **FVG Sessions [LuxAlgo]** indicator into a fully functional Pine Script v5 strategy. The indicator identifies session-based Fair Value Gaps (FVGs) and tracks their mitigation. The strategy will trade those FVGs by entering positions when price revisits the gap, and exiting via defined take-profit and stop-loss rules.

Every section below **must be implemented**. Nothing is optional unless explicitly marked.

**Ensure** all function calls are written on a single line, with no line breaks inside function arguments, to comply with Pine Script syntax rules  
---

## Phase 1 — Script Header & Declaration

- [ ] Replace `indicator(...)` with `strategy(...)` at the top of the script
- [ ] Set `title` to `"FVG Sessions Strategy [LuxAlgo]"`
- [ ] Set `overlay = true` to keep all visual elements on the price chart
- [ ] Set `max_lines_count = 500` and `max_boxes_count = 500` (carry over from indicator)
- [ ] Set `default_qty_type = strategy.percent_of_equity`
- [ ] Set `default_qty_value = 10` (10% of equity per trade — user can adjust)
- [ ] Set `initial_capital = 10000`
- [ ] Set `commission_type = strategy.commission.percent`
- [ ] Set `commission_value = 0.05` (0.05% per trade — adjust per broker)
- [ ] Set `slippage = 2` (2 ticks of slippage)
- [ ] Set `pyramiding = 0` (no pyramiding — only one open trade at a time)
- [ ] Set `calc_on_every_tick = false` (bar-close calculation only for accuracy)

---

## Phase 2 — Preserve All Existing Settings Inputs

All original inputs from the indicator **must be preserved exactly** — do not remove or rename them.

- [ ] Keep `bullCss` — color input for bullish FVG level line (default: `color.teal`)
- [ ] Keep `bullAreaCss` — color input for bullish FVG area box (default: `color.new(color.teal, 50)`)
- [ ] Keep `bullMitigatedCss` — color input for mitigated bullish FVG area (default: `color.new(color.teal, 80)`)
- [ ] Keep `bearCss` — color input for bearish FVG level line (default: `color.red`)
- [ ] Keep `bearAreaCss` — color input for bearish FVG area box (default: `color.new(color.red, 50)`)
- [ ] Keep `bearMitigatedCss` — color input for mitigated bearish FVG area (default: `color.new(color.red, 80)`)

---

## Phase 3 — Add New Strategy Inputs

Add a clearly labeled input group called `"Strategy Settings"` containing all of the following:

### Entry Settings
- [ ] `entryMode` — `input.string`, title `"Entry Mode"`, options `["FVG Midpoint", "FVG Top/Bottom", "FVG Formation"]`, default `"FVG Midpoint"`
  - `"FVG Midpoint"` — enter when price crosses the midpoint (average) of the FVG
  - `"FVG Top/Bottom"` — enter when price touches the near edge of the FVG zone
  - `"FVG Formation"` — enter immediately on the bar the FVG is confirmed (market order)
- [ ] `tradeDirection` — `input.string`, title `"Trade Direction"`, options `["Both", "Bullish Only", "Bearish Only"]`, default `"Both"`

### Risk Management
- [ ] `slType` — `input.string`, title `"Stop Loss Type"`, options `["FVG Far Edge", "Session High/Low", "ATR", "Fixed Pips"]`, default `"FVG Far Edge"`
  - `"FVG Far Edge"` — stop loss placed at the opposite edge of the FVG zone
  - `"Session High/Low"` — stop at the session range high (for shorts) or low (for longs)
  - `"ATR"` — stop is a multiple of ATR from entry
  - `"Fixed Pips"` — stop is a user-defined pip distance
- [ ] `slATRMult` — `input.float`, title `"ATR Multiplier (SL)"`, default `1.5`, minval `0.1`, step `0.1` — only used when `slType == "ATR"`
- [ ] `slFixedPips` — `input.float`, title `"Fixed Pips (SL)"`, default `20.0`, minval `1.0` — only used when `slType == "Fixed Pips"`
- [ ] `atrLength` — `input.int`, title `"ATR Length"`, default `14`, minval `1` — used for ATR-based SL and filter

### Take Profit
- [ ] `tpType` — `input.string`, title `"Take Profit Type"`, options `["Risk:Reward", "FVG Far Edge", "Session High/Low", "ATR", "Fixed Pips"]`, default `"Risk:Reward"`
  - `"Risk:Reward"` — TP is placed at a multiple of the stop loss distance from entry
  - `"FVG Far Edge"` — TP placed at the opposite edge of the FVG zone
  - `"Session High/Low"` — TP at the session range high (for longs) or low (for shorts)
  - `"ATR"` — TP is a multiple of ATR from entry
  - `"Fixed Pips"` — TP is a user-defined pip distance
- [ ] `rrRatio` — `input.float`, title `"Risk:Reward Ratio"`, default `2.0`, minval `0.1`, step `0.1` — only used when `tpType == "Risk:Reward"`
- [ ] `tpATRMult` — `input.float`, title `"ATR Multiplier (TP)"`, default `3.0`, minval `0.1`, step `0.1` — only used when `tpType == "ATR"`
- [ ] `tpFixedPips` — `input.float`, title `"Fixed Pips (TP)"`, default `40.0`, minval `1.0` — only used when `tpType == "Fixed Pips"`

### Mitigation Behaviour
- [ ] `exitOnMitigation` — `input.bool`, title `"Exit Trade on FVG Mitigation"`, default `true`
  - When `true`, if an open position's FVG becomes mitigated (as defined in the original indicator logic), the strategy will immediately close the trade at market

### Filters
- [ ] `useSessionFilter` — `input.bool`, title `"Enable Session Time Filter"`, default `false`
- [ ] `sessionTime` — `input.session`, title `"Allowed Session"`, default `"0800-1700"` — only used when `useSessionFilter == true`
- [ ] `useMitigatedFilter` — `input.bool`, title `"Skip Mitigated FVGs"`, default `true`
  - When `true`, do not enter a trade if the FVG has already been mitigated before entry is triggered
- [ ] `useOneTradePerSession` — `input.bool`, title `"One Trade Per Session"`, default `true`
  - When `true`, only one trade is allowed per daily session — once a trade is taken, no new entries until the next session

---

## Phase 4 — Preserve All UDTs Exactly

Both User Defined Types from the indicator **must be kept without modification**.

- [ ] Keep `type fvg` with all fields: `top`, `btm`, `mitigated`, `isnew`, `isbull`, `lvl`, `area`
- [ ] Keep `type session_range` with fields: `max`, `min`

---

## Phase 5 — Preserve All Methods Exactly

Both methods from the indicator **must be kept without modification**.

- [ ] Keep `method set_fvg(fvg id, offset, bg_css, l_css)` — draws FVG box and midline
- [ ] Keep `method set_range(session_range id)` — updates session high/low range lines

---

## Phase 6 — Preserve All Variables Exactly

All variables from the indicator **must be carried forward**. Do not remove any.

- [ ] Keep `var chartCss` — chart foreground color for session delimiters
- [ ] Keep `var fvg sfvg` — the current session's FVG object
- [ ] Keep `var session_range sesr` — the current session range object
- [ ] Keep `var box area` and `var line avg` — drawing references
- [ ] Keep `bull_fvg` — boolean condition: `low > high[2] and close[1] > high[2]`
- [ ] Keep `bear_fvg` — boolean condition: `high < low[2] and close[1] < low[2]`
- [ ] Keep all six alert boolean flags: `bull_isnew`, `bear_isnew`, `bull_mitigated`, `bear_mitigated`, `within_bull_fvg`, `within_bear_fvg` — initialised to `false` each bar

### Add New Strategy Variables
- [ ] `var bool tradeOpenThisSession` — initialised to `false`; tracks whether a trade was already taken in the current session (used for one-trade-per-session filter)
- [ ] `var float entryPrice` — stores the computed entry price for the pending trade
- [ ] `var float stopLossPrice` — stores the computed stop loss price
- [ ] `var float takeProfitPrice` — stores the computed take profit price
- [ ] `var bool pendingLong` — `true` when a bullish FVG has been identified and the strategy is waiting for entry confirmation
- [ ] `var bool pendingShort` — `true` when a bearish FVG has been identified and the strategy is waiting for entry confirmation
- [ ] `atr14` — non-persistent: computed each bar using `ta.atr(atrLength)`
- [ ] `pipSize` — non-persistent: computed as `syminfo.mintick * 10` to convert pips to price units

---

## Phase 7 — Preserve All Session Logic Exactly

The new session detection and drawing logic **must be kept exactly** as in the original indicator.

- [ ] Keep `dtf = timeframe.change('D')` — daily session change detection
- [ ] Keep the `if dtf` block:
  - Draw the vertical dashed session delimiter line
  - Instantiate a new `session_range` object
  - Set `sfvg.isnew := true`
  - Close the prior session's FVG drawing coordinates (`set_x2`, `set_right`)
  - **Add**: Reset `tradeOpenThisSession := false` on new session (required for one-trade-per-session filter)
  - **Add**: Reset `pendingLong := false` and `pendingShort := false` on new session (clear stale pending signals)
- [ ] Keep the `else if not na(sesr)` block:
  - Call `sesr.set_range()` to extend session range
  - Update session range line colors based on FVG direction

---

## Phase 8 — Preserve All FVG Detection Logic Exactly

The FVG identification and mitigation logic from the indicator **must be kept exactly**.

- [ ] Keep the `if bull_fvg and sfvg.isnew` block — creates new bullish FVG, calls `set_fvg`, sets `bull_isnew := true`
- [ ] Keep the `else if bear_fvg and sfvg.isnew` block — creates new bearish FVG, calls `set_fvg`, sets `bear_isnew := true`
- [ ] Keep the mitigation check block (`if not sfvg.mitigated`):
  - Bullish mitigation: `close < sfvg.btm`
  - Bearish mitigation: `close > sfvg.top`
  - Call `set_fvg` with mitigated colors on mitigation
  - Set `sfvg.mitigated := true`
  - Set `bull_mitigated` or `bear_mitigated` flags
- [ ] Keep the `if not sfvg.isnew` block — extends the FVG drawing to the current bar

---

## Phase 9 — Compute Entry, Stop Loss, and Take Profit

This phase adds new logic **after** all preserved indicator logic. These calculations prepare values for the strategy entry functions.

### Entry Price Calculation
- [ ] If `entryMode == "FVG Midpoint"`:
  - `entryPrice := math.avg(sfvg.top, sfvg.btm)` — the midpoint of the FVG zone
- [ ] If `entryMode == "FVG Top/Bottom"`:
  - For bullish: `entryPrice := sfvg.btm` — bottom edge of the bullish FVG (demand zone top)
  - For bearish: `entryPrice := sfvg.top` — top edge of the bearish FVG (supply zone bottom)
- [ ] If `entryMode == "FVG Formation"`:
  - For bullish: `entryPrice := close` — enter at close of the FVG formation bar
  - For bearish: `entryPrice := close` — enter at close of the FVG formation bar

### Stop Loss Calculation (Bullish)
- [ ] If `slType == "FVG Far Edge"`: `stopLossPrice := sfvg.btm - syminfo.mintick`
- [ ] If `slType == "Session High/Low"`: `stopLossPrice := sesr.min.get_y2() - syminfo.mintick`
- [ ] If `slType == "ATR"`: `stopLossPrice := entryPrice - (atr14 * slATRMult)`
- [ ] If `slType == "Fixed Pips"`: `stopLossPrice := entryPrice - (slFixedPips * pipSize)`

### Stop Loss Calculation (Bearish)
- [ ] If `slType == "FVG Far Edge"`: `stopLossPrice := sfvg.top + syminfo.mintick`
- [ ] If `slType == "Session High/Low"`: `stopLossPrice := sesr.max.get_y2() + syminfo.mintick`
- [ ] If `slType == "ATR"`: `stopLossPrice := entryPrice + (atr14 * slATRMult)`
- [ ] If `slType == "Fixed Pips"`: `stopLossPrice := entryPrice + (slFixedPips * pipSize)`

### Take Profit Calculation (Bullish)
- [ ] If `tpType == "Risk:Reward"`: `takeProfitPrice := entryPrice + ((entryPrice - stopLossPrice) * rrRatio)`
- [ ] If `tpType == "FVG Far Edge"`: `takeProfitPrice := sfvg.top`
- [ ] If `tpType == "Session High/Low"`: `takeProfitPrice := sesr.max.get_y2()`
- [ ] If `tpType == "ATR"`: `takeProfitPrice := entryPrice + (atr14 * tpATRMult)`
- [ ] If `tpType == "Fixed Pips"`: `takeProfitPrice := entryPrice + (tpFixedPips * pipSize)`

### Take Profit Calculation (Bearish)
- [ ] If `tpType == "Risk:Reward"`: `takeProfitPrice := entryPrice - ((stopLossPrice - entryPrice) * rrRatio)`
- [ ] If `tpType == "FVG Far Edge"`: `takeProfitPrice := sfvg.btm`
- [ ] If `tpType == "Session High/Low"`: `takeProfitPrice := sesr.min.get_y2()`
- [ ] If `tpType == "ATR"`: `takeProfitPrice := entryPrice - (atr14 * tpATRMult)`
- [ ] If `tpType == "Fixed Pips"`: `takeProfitPrice := entryPrice - (tpFixedPips * pipSize)`

---

## Phase 10 — Entry Logic

All entry conditions must be checked here, after Phase 9 calculations.

### Set Pending Signal on New FVG
- [ ] When `bull_isnew == true` and `tradeDirection != "Bearish Only"`:
  - Set `pendingLong := true`
  - Set `pendingShort := false`
  - If `entryMode == "FVG Formation"`: trigger the long entry immediately (skip pending)
- [ ] When `bear_isnew == true` and `tradeDirection != "Bullish Only"`:
  - Set `pendingShort := true`
  - Set `pendingLong := false`
  - If `entryMode == "FVG Formation"`: trigger the short entry immediately (skip pending)

### Filter Checks (apply to all entry triggers)
- [ ] `filterSessionOK` — `true` if `useSessionFilter == false`, or if `useSessionFilter == true` and current bar is within `sessionTime`
- [ ] `filterOneTrade` — `true` if `useOneTradePerSession == false`, or if `tradeOpenThisSession == false`
- [ ] `filterMitigated` — `true` if `useMitigatedFilter == false`, or if `sfvg.mitigated == false`
- [ ] `noOpenTrade` — `true` if `strategy.position_size == 0`
- [ ] All four filters must be `true` for entry to proceed

### Bullish Entry Trigger
- [ ] If `pendingLong == true` and all filters pass and `noOpenTrade`:
  - If `entryMode == "FVG Midpoint"`: enter when `ta.crossover(close, entryPrice)`
  - If `entryMode == "FVG Top/Bottom"`: enter when `low <= entryPrice and close >= entryPrice`
  - If `entryMode == "FVG Formation"`: already entered on signal bar (handled above)
  - Call `strategy.entry("Long", strategy.long)`
  - Call `strategy.exit("Long Exit", "Long", stop = stopLossPrice, limit = takeProfitPrice)`
  - Set `tradeOpenThisSession := true`
  - Set `pendingLong := false`

### Bearish Entry Trigger
- [ ] If `pendingShort == true` and all filters pass and `noOpenTrade`:
  - If `entryMode == "FVG Midpoint"`: enter when `ta.crossunder(close, entryPrice)`
  - If `entryMode == "FVG Top/Bottom"`: enter when `high >= entryPrice and close <= entryPrice`
  - If `entryMode == "FVG Formation"`: already entered on signal bar (handled above)
  - Call `strategy.entry("Short", strategy.short)`
  - Call `strategy.exit("Short Exit", "Short", stop = stopLossPrice, limit = takeProfitPrice)`
  - Set `tradeOpenThisSession := true`
  - Set `pendingShort := false`

---

## Phase 11 — Exit Logic

### Exit on FVG Mitigation
- [ ] If `exitOnMitigation == true`:
  - If `bull_mitigated == true` and `strategy.position_size > 0`:
    - Call `strategy.close("Long", comment = "FVG Mitigated")`
  - If `bear_mitigated == true` and `strategy.position_size < 0`:
    - Call `strategy.close("Short", comment = "FVG Mitigated")`

### Exit on New Session (Optional Safety)
- [ ] If `dtf == true` (new session detected) and `strategy.position_size != 0`:
  - Call `strategy.close_all(comment = "Session End")` to avoid holding trades overnight
  - **Note**: This is controlled by `useOneTradePerSession` — document this behaviour in a tooltip so the user is aware

---

## Phase 12 — Preserve All Alert Conditions Exactly

All six `alertcondition()` calls from the original indicator **must be kept without modification**.

- [ ] Keep `alertcondition(bull_isnew, 'Bullish FVG', 'New session bullish fvg')`
- [ ] Keep `alertcondition(bear_isnew, 'Bearish FVG', 'New session bearish fvg')`
- [ ] Keep `alertcondition(bull_mitigated, 'Mitigated Bullish FVG', 'Session bullish fvg has been mitigated')`
- [ ] Keep `alertcondition(bear_mitigated, 'Mitigated Bearish FVG', 'Session bearish fvg has been mitigated')`
- [ ] Keep `alertcondition(close >= sfvg.btm and close <= sfvg.top and sfvg.isbull and not sfvg.isnew, 'Price Within Bullish FVG', ...)`
- [ ] Keep `alertcondition(close >= sfvg.btm and close <= sfvg.top and not sfvg.isbull and not sfvg.isnew, 'Price Within Bearish FVG', ...)`
- [ ] Keep `alertcondition(ta.cross(close, math.avg(sfvg.top, sfvg.btm)) and sfvg.isbull and not sfvg.isnew, 'Bullish FVG AVG Cross', ...)`
- [ ] Keep `alertcondition(ta.cross(close, math.avg(sfvg.top, sfvg.btm)) and not sfvg.isbull and not sfvg.isnew, 'Bearish FVG AVG Cross', ...)`

### Add New Strategy Alerts
- [ ] `alertcondition(bull_isnew and pendingLong, 'Long Entry Signal', 'Bullish FVG long entry is pending')`
- [ ] `alertcondition(bear_isnew and pendingShort, 'Short Entry Signal', 'Bearish FVG short entry is pending')`

---

## Phase 13 — Visual Debugging Overlays (Optional but Recommended)

These are additional plot elements to aid visual debugging during testing. They do not affect trade logic.

- [ ] Plot entry price when a position is open:
  - `plot(strategy.position_size != 0 ? entryPrice : na, title = "Entry", color = color.yellow, style = plot.style_linebr, linewidth = 1)`
- [ ] Plot stop loss price when a position is open:
  - `plot(strategy.position_size != 0 ? stopLossPrice : na, title = "Stop Loss", color = color.red, style = plot.style_linebr, linewidth = 1)`
- [ ] Plot take profit price when a position is open:
  - `plot(strategy.position_size != 0 ? takeProfitPrice : na, title = "Take Profit", color = color.green, style = plot.style_linebr, linewidth = 1)`
- [ ] Plot a background highlight when price is inside the active FVG zone:
  - `bgcolor(close >= sfvg.btm and close <= sfvg.top and not sfvg.isnew ? color.new(color.yellow, 90) : na, title = "Inside FVG")`

---

## Phase 14 — Validation Checklist

Before considering the strategy complete, verify every item below.

### Compilation
- [ ] Script compiles without any errors or warnings in the Pine Script editor
- [ ] All indicator visuals render exactly as in the original — session delimiters, FVG boxes, midlines, range lines

### Logic Verification
- [ ] A new bullish FVG on the first qualifying bar of a session correctly sets `sfvg.isnew := false` after creation, preventing duplicate FVG entries in the same session
- [ ] A new bearish FVG on the first qualifying bar of a session behaves the same
- [ ] When `useOneTradePerSession == true`, only one `strategy.entry()` fires per daily session
- [ ] When `useMitigatedFilter == true`, no entry fires after `sfvg.mitigated == true`
- [ ] `strategy.exit()` stop and limit levels are correctly placed on the chart (visible via the Strategy Tester's trade list)
- [ ] `exitOnMitigation` correctly closes open trades when the FVG is mitigated
- [ ] Session close (`strategy.close_all`) fires on new session when a trade is open
- [ ] ATR, Fixed Pips, and Risk:Reward TP/SL values compute correctly (manually verify one trade in the tester)

### Backtesting
- [ ] Run on at least 3 different forex pairs (e.g. EURUSD, GBPUSD, USDJPY) on H1 timeframe
- [ ] Run on the D1 timeframe to verify session logic works at higher timeframes
- [ ] Confirm the Strategy Tester shows trades — if zero trades appear, debug `pendingLong`/`pendingShort` logic and filter conditions
- [ ] Confirm no look-ahead bias: all entries and exits reference current or prior bar values only (no `[negative offsets]`)
- [ ] Check that `pyramiding = 0` is respected — no double entries should appear

---

## Phase 15 — Known Limitations & Notes

Document these in a comment block at the top of the final strategy file.

- [ ] The FVG is session-scoped — only the **first** FVG detected in a session is traded. Subsequent FVGs within the same session are ignored (matching original indicator behaviour)
- [ ] `entryMode = "FVG Formation"` enters at bar close of the FVG candle — this is the earliest possible entry and carries the most risk
- [ ] `slType = "Session High/Low"` may produce very wide stop losses on volatile sessions — use with lower position sizing
- [ ] The strategy does not account for spread — add spread manually via the `commission_value` input when backtesting on instruments with high spreads
- [ ] `calc_on_every_tick = false` is intentional — tick-level calculation would create inconsistencies with the session-based FVG logic
- [ ] The strategy was designed for **daily sessions** — using it on weekly or monthly timeframe change may produce unexpected behaviour with the `timeframe.change('D')` trigger

---

*End of Implementation Guide*
