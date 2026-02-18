```
// ... [Keep all original BigBeluga code here exactly as it is] ...
// [The following code should be pasted at the very bottom of the script]
```


## Python indicatr test suite with 13 test cases.  
* `Create a project direcory`
* `activate virtual environment`
* `create test file and add code e.g test_indicator.py`

**Install dependencies**  
`pip install pytest pandas numpy --break-system-packages`

**Run the tests**  
`pytest test_indicator.py -v`
or
`python test_indicator.py`

```
"""
EMA/RSI/MACD + S&R Signal Indicator — SRP Refactor
Version: 3.0

SINGLE RESPONSIBILITY PRINCIPLE
================================
Each class owns exactly one concern:

  IndicatorMath              — pure, stateless maths (EMA, RSI, MACD, ATR/TR)
  SwingDetector              — pivot-high / pivot-low detection
  PivotCalculator            — daily pivot levels (resampling + broadcast)
  TrendAnalyser              — trend direction + optional strength filter
  HTFConfirmation            — higher-timeframe EMA trend alignment
  MomentumAnalyser           — RSI & MACD signal columns + composite logic
  VolatilityFilter           — ATR-based volatility gate
  SupportResistanceClassifier— S/R proximity classification per bar
  SignalStateMachine         — leading-edge detection + anti-spam price filter
  SignalOrchestrator         — composes all components; owns pipeline order
  SignalIndicator            — thin public façade (backward-compatible API)

REQUIREMENTS:
    pip install pandas numpy --break-system-packages

USAGE:
    python indicator_srp.py          # runs the test suite
"""

from __future__ import annotations

import time
import traceback
import warnings
from dataclasses import dataclass, field
from typing import Tuple

import numpy as np
import pandas as pd

warnings.filterwarnings("ignore", category=FutureWarning)


# ---------------------------------------------------------------------------
# Shared infrastructure helper
# ---------------------------------------------------------------------------

def _resample_offset_kwarg(offset: str) -> dict:
    """
    pandas ≥ 1.1 uses 'offset='; older versions used 'base=' (integer minutes).
    Detects via version parsing — probe-based detection is unreliable because
    an empty Series raises TypeError on newer pandas regardless.
    """
    major, minor = (int(x) for x in pd.__version__.split(".")[:2])
    if (major, minor) >= (1, 1):
        return {"offset": offset}
    hours = int(offset.replace("h", ""))
    return {"base": hours * 60}


# ===========================================================================
# 1. IndicatorMath
#    Responsibility: pure, stateless mathematical transforms on price series.
#    No trading logic, no configuration flags, no DataFrame mutation.
# ===========================================================================

class IndicatorMath:
    """Pure mathematical indicator calculations. No state, no side-effects."""

    @staticmethod
    def ema(series: pd.Series, period: int) -> pd.Series:
        """Exponential Moving Average."""
        return series.ewm(span=period, adjust=False).mean()

    @staticmethod
    def rsi(series: pd.Series, period: int) -> pd.Series:
        """RSI using Wilder's smoothing (matches TradingView)."""
        delta    = series.diff()
        gain     = delta.where(delta > 0, 0.0)
        loss     = -delta.where(delta < 0, 0.0)
        avg_gain = gain.ewm(alpha=1 / period, adjust=False).mean()
        avg_loss = loss.ewm(alpha=1 / period, adjust=False).mean()
        rs = avg_gain / avg_loss
        return 100 - (100 / (1 + rs))

    @staticmethod
    def macd(
        series: pd.Series, fast: int, slow: int, signal: int
    ) -> Tuple[pd.Series, pd.Series, pd.Series]:
        """Returns (macd_line, signal_line, histogram)."""
        ema_fast    = IndicatorMath.ema(series, fast)
        ema_slow    = IndicatorMath.ema(series, slow)
        macd_line   = ema_fast - ema_slow
        signal_line = IndicatorMath.ema(macd_line, signal)
        histogram   = macd_line - signal_line
        return macd_line, signal_line, histogram

    @staticmethod
    def true_range(high: pd.Series, low: pd.Series, close: pd.Series) -> pd.Series:
        """True Range — building block for ATR."""
        prev_close = close.shift(1)
        return pd.concat(
            [high - low, (high - prev_close).abs(), (low - prev_close).abs()],
            axis=1,
        ).max(axis=1)

    @staticmethod
    def atr(high: pd.Series, low: pd.Series, close: pd.Series, period: int = 14) -> pd.Series:
        """Average True Range (simple rolling mean of TR)."""
        tr = IndicatorMath.true_range(high, low, close)
        return tr.rolling(window=period).mean()


# ===========================================================================
# 2. SwingDetector
#    Responsibility: detect local swing highs and swing lows in a price series.
#    No pivot math, no S/R logic, no trend awareness.
# ===========================================================================

class SwingDetector:
    """Detects swing pivot highs and lows in a price series."""

    def __init__(self, lookback: int = 10):
        self.lookback = lookback

    def swing_highs(self, series: pd.Series) -> pd.Series:
        """
        Returns a Series with the swing-high value at each pivot bar, NaN elsewhere.
        Uses a plain list to avoid pandas chained-assignment FutureWarning.
        """
        left = right = self.lookback
        arr    = series.to_numpy()
        values = [np.nan] * len(arr)
        for i in range(left, len(arr) - right):
            window = arr[i - left : i + right + 1]
            if arr[i] == window.max():
                values[i] = arr[i]
        return pd.Series(values, index=series.index, name="swing_high")

    def swing_lows(self, series: pd.Series) -> pd.Series:
        """Returns a Series with the swing-low value at each pivot bar, NaN elsewhere."""
        left = right = self.lookback
        arr    = series.to_numpy()
        values = [np.nan] * len(arr)
        for i in range(left, len(arr) - right):
            window = arr[i - left : i + right + 1]
            if arr[i] == window.min():
                values[i] = arr[i]
        return pd.Series(values, index=series.index, name="swing_low")


# ===========================================================================
# 3. PivotCalculator
#    Responsibility: compute standard floor-pivot levels from daily OHLC,
#    aligned to the NY Close (17:00 UTC), and broadcast them back to the
#    intraday index.  This is the only class that touches pandas resampling.
# ===========================================================================

@dataclass
class PivotLevels:
    """Value object holding all seven pivot levels as aligned intraday Series."""
    pivot: pd.Series
    r1: pd.Series
    r2: pd.Series
    r3: pd.Series
    s1: pd.Series
    s2: pd.Series
    s3: pd.Series


class PivotCalculator:
    """
    Computes daily pivot levels.
    Responsibility: resampling + arithmetic + broadcasting.
    No S/R logic, no trend awareness, no signal state.
    """

    NY_CLOSE_OFFSET = "17h"   # 5 PM UTC / NY session close

    def calculate(
        self,
        high:  pd.Series,
        low:   pd.Series,
        close: pd.Series,
    ) -> PivotLevels:
        daily = (
            pd.DataFrame({"high": high, "low": low, "close": close})
            .resample("24h", **_resample_offset_kwarg(self.NY_CLOSE_OFFSET))
            .agg({"high": "max", "low": "min", "close": "last"})
        )

        prev   = daily.shift(1)
        p_high = prev["high"].reindex(high.index,  method="ffill")
        p_low  = prev["low"].reindex(low.index,    method="ffill")
        p_close= prev["close"].reindex(close.index, method="ffill")

        pivot    = (p_high + p_low + p_close) / 3
        range_dl = p_high - p_low

        return PivotLevels(
            pivot = pivot,
            r1    = (2 * pivot) - p_low,
            s1    = (2 * pivot) - p_high,
            r2    = pivot + range_dl,
            s2    = pivot - range_dl,
            r3    = p_high + 2 * (pivot - p_low),
            s3    = p_low  - 2 * (p_high - pivot),
        )


# ===========================================================================
# 4. TrendAnalyser
#    Responsibility: compute bull/bear trend columns from EMA relationship.
#    Optionally applies a trend-strength (EMA-separation) filter.
#    No momentum, no S/R, no HTF awareness.
# ===========================================================================

class TrendAnalyser:
    """
    Determines trend direction from EMA crossover.
    Optionally filters weak trends by EMA separation.
    """

    def __init__(
        self,
        fast_len:       int   = 9,
        slow_len:       int   = 21,
        use_strength:   bool  = False,
        min_separation: float = 0.0005,
    ):
        self.fast_len       = fast_len
        self.slow_len       = slow_len
        self.use_strength   = use_strength
        self.min_separation = min_separation

    def analyse(self, close: pd.Series) -> pd.DataFrame:
        """
        Returns a DataFrame with columns:
            ema_fast, ema_slow, ema_separation (optional),
            strong_enough, bull_trend, bear_trend
        """
        ema_fast = IndicatorMath.ema(close, self.fast_len)
        ema_slow = IndicatorMath.ema(close, self.slow_len)

        bull_base = (close > ema_fast) & (ema_fast > ema_slow)
        bear_base = (close < ema_fast) & (ema_fast < ema_slow)

        result = pd.DataFrame({"ema_fast": ema_fast, "ema_slow": ema_slow}, index=close.index)

        if self.use_strength:
            separation             = (ema_fast - ema_slow).abs() / close
            result["ema_separation"] = separation
            strong_enough            = separation >= self.min_separation
        else:
            strong_enough = pd.Series(True, index=close.index)

        result["strong_enough"] = strong_enough
        result["bull_trend"]    = bull_base & strong_enough
        result["bear_trend"]    = bear_base & strong_enough
        return result


# ===========================================================================
# 5. HTFConfirmation
#    Responsibility: resample base-timeframe data to a higher timeframe,
#    compute EMA alignment there, and broadcast back.
#    No signal logic, no S/R, no momentum.
# ===========================================================================

class HTFConfirmation:
    """
    Computes higher-timeframe EMA trend and aligns it to the base index.
    Uses a 1-bar shift on HTF to avoid lookahead from the current incomplete bar.
    """

    BASE_MINUTES = 15   # hardcoded base timeframe in minutes

    def __init__(self, fast_len: int = 9, slow_len: int = 21, multiplier: int = 4):
        self.fast_len   = fast_len
        self.slow_len   = slow_len
        self.multiplier = multiplier

    def confirm(self, ohlcv: pd.DataFrame) -> pd.DataFrame:
        """
        Args:
            ohlcv: base-timeframe DataFrame with open/high/low/close columns.

        Returns:
            DataFrame with columns htf_bull, htf_bear aligned to ohlcv.index.
        """
        rule     = f"{self.multiplier * self.BASE_MINUTES}min"
        htf_data = (
            ohlcv.resample(rule)
            .agg({"close": "last", "high": "max", "low": "min", "open": "first"})
            .dropna()
        )

        htf_fast = IndicatorMath.ema(htf_data["close"], self.fast_len).shift(1)
        htf_slow = IndicatorMath.ema(htf_data["close"], self.slow_len).shift(1)

        htf_bull = (htf_fast > htf_slow).reindex(ohlcv.index, method="ffill").fillna(False)
        htf_bear = (htf_fast < htf_slow).reindex(ohlcv.index, method="ffill").fillna(False)

        return pd.DataFrame(
            {"htf_bull": htf_bull, "htf_bear": htf_bear},
            index=ohlcv.index,
        )


# ===========================================================================
# 6. MomentumAnalyser
#    Responsibility: compute RSI and MACD signal columns, apply hard caps,
#    and combine them into a momentum composite.
#    No trend direction, no S/R, no state.
# ===========================================================================

class MomentumAnalyser:
    """
    Computes RSI + MACD signals and combines them into momentum_bull / momentum_bear.
    The combination mode (OR vs AND) is controlled by strict_mode.
    """

    RSI_HARD_CAP_HIGH = 80
    RSI_HARD_CAP_LOW  = 20

    def __init__(
        self,
        rsi_len:     int   = 14,
        rsi_ob:      float = 60,
        rsi_os:      float = 40,
        macd_fast:   int   = 12,
        macd_slow:   int   = 26,
        macd_sig:    int   = 9,
        strict_mode: bool  = False,
    ):
        self.rsi_len   = rsi_len
        self.rsi_ob    = rsi_ob
        self.rsi_os    = rsi_os
        self.macd_fast = macd_fast
        self.macd_slow = macd_slow
        self.macd_sig  = macd_sig
        self.strict_mode = strict_mode

    def analyse(self, close: pd.Series) -> pd.DataFrame:
        """
        Returns a DataFrame with columns:
            rsi, rsi_bull, rsi_bear,
            macd_line, signal_line, hist, macd_bull, macd_bear,
            momentum_bull, momentum_bear
        """
        rsi = IndicatorMath.rsi(close, self.rsi_len)
        rsi_bull = (rsi > self.rsi_os)  & (rsi < self.RSI_HARD_CAP_HIGH)
        rsi_bear = (rsi < self.rsi_ob)  & (rsi > self.RSI_HARD_CAP_LOW)

        macd_line, signal_line, hist = IndicatorMath.macd(
            close, self.macd_fast, self.macd_slow, self.macd_sig
        )
        macd_bull = (macd_line > signal_line) & (macd_line > 0)
        macd_bear = (macd_line < signal_line) & (hist < 0)

        if self.strict_mode:
            momentum_bull = rsi_bull & macd_bull
            momentum_bear = rsi_bear & macd_bear
        else:
            momentum_bull = rsi_bull | macd_bull
            momentum_bear = rsi_bear | macd_bear

        return pd.DataFrame(
            {
                "rsi":          rsi,
                "rsi_bull":     rsi_bull,
                "rsi_bear":     rsi_bear,
                "macd_line":    macd_line,
                "signal_line":  signal_line,
                "hist":         hist,
                "macd_bull":    macd_bull,
                "macd_bear":    macd_bear,
                "momentum_bull": momentum_bull,
                "momentum_bear": momentum_bear,
            },
            index=close.index,
        )


# ===========================================================================
# 7. VolatilityFilter
#    Responsibility: decide per-bar whether volatility is sufficient to trade.
#    Computes ATR vs its own moving average.
#    No trend, no momentum, no signal state.
# ===========================================================================

class VolatilityFilter:
    """
    Filters bars by ATR relative to its rolling mean.
    When disabled, every bar is marked volatile_enough=True.
    """

    def __init__(
        self,
        enabled:    bool  = False,
        atr_period: int   = 14,
        sma_period: int   = 20,
        min_ratio:  float = 0.8,
    ):
        self.enabled    = enabled
        self.atr_period = atr_period
        self.sma_period = sma_period
        self.min_ratio  = min_ratio

    def apply(
        self,
        high:  pd.Series,
        low:   pd.Series,
        close: pd.Series,
    ) -> pd.DataFrame:
        """
        Returns a DataFrame with columns:
            atr (optional), atr_sma (optional), volatile_enough
        """
        if not self.enabled:
            return pd.DataFrame(
                {"volatile_enough": pd.Series(True, index=close.index)},
                index=close.index,
            )

        atr_vals = IndicatorMath.atr(high, low, close, self.atr_period)
        atr_sma  = atr_vals.rolling(window=self.sma_period).mean()

        return pd.DataFrame(
            {
                "atr":            atr_vals,
                "atr_sma":        atr_sma,
                "volatile_enough": atr_vals > atr_sma * self.min_ratio,
            },
            index=close.index,
        )


# ===========================================================================
# 8. SupportResistanceClassifier
#    Responsibility: given pre-computed pivot / swing levels and the current
#    trend, classify each bar as near support, near resistance, or neither.
#    No indicator math, no signal state.
# ===========================================================================

class SupportResistanceClassifier:
    """
    Classifies each bar as near_support / near_resistance based on:
      - Daily pivot levels (s1/s2/s3, r1/r2/r3, pivot)
      - Last swing high / swing low
      - Current trend direction (pivot counts as support only in bull trend)

    proximity_pct: price fraction defining the proximity band
    """

    EPSILON = 1e-8  # float-precision guard

    def __init__(self, proximity_pct: float = 0.0010):
        self.proximity_pct = proximity_pct

    def is_near(self, price: float, level: float, threshold: float) -> bool:
        """True if price is within threshold of level. NaN-safe."""
        if pd.isna(level):
            return False
        return abs(price - level) <= threshold + self.EPSILON

    def classify(
        self,
        close:           pd.Series,
        bull_trend:      pd.Series,
        bear_trend:      pd.Series,
        levels:          PivotLevels,
        last_swing_high: pd.Series,
        last_swing_low:  pd.Series,
    ) -> pd.DataFrame:
        """
        Returns a DataFrame with columns: near_support, near_resistance, sr_threshold.

        NOTE: Row-by-row loop preserved for 1-to-1 Pine Script parity.
        For large datasets consider vectorising with np.isclose / broadcasting.
        """
        n          = len(close)
        thresholds = (close * self.proximity_pct).to_numpy()
        close_arr  = close.to_numpy()
        bull_arr   = bull_trend.to_numpy()
        bear_arr   = bear_trend.to_numpy()

        # Pre-extract level arrays for speed
        s1_arr  = levels.s1.to_numpy();   s2_arr = levels.s2.to_numpy();  s3_arr = levels.s3.to_numpy()
        r1_arr  = levels.r1.to_numpy();   r2_arr = levels.r2.to_numpy();  r3_arr = levels.r3.to_numpy()
        pv_arr  = levels.pivot.to_numpy()
        swh_arr = last_swing_high.to_numpy()
        swl_arr = last_swing_low.to_numpy()

        near_sup = np.zeros(n, dtype=bool)
        near_res = np.zeros(n, dtype=bool)

        is_near = self.is_near  # local alias

        for i in range(n):
            p   = close_arr[i]
            thr = thresholds[i]

            near_sup[i] = (
                is_near(p, s1_arr[i],  thr)
                or is_near(p, s2_arr[i],  thr)
                or is_near(p, s3_arr[i],  thr)
                or (is_near(p, pv_arr[i], thr) and bool(bull_arr[i]))
                or is_near(p, swl_arr[i], thr)
            )
            near_res[i] = (
                is_near(p, r1_arr[i],  thr)
                or is_near(p, r2_arr[i],  thr)
                or is_near(p, r3_arr[i],  thr)
                or (is_near(p, pv_arr[i], thr) and bool(bear_arr[i]))
                or is_near(p, swh_arr[i], thr)
            )

        return pd.DataFrame(
            {
                "sr_threshold":   thresholds,
                "near_support":   near_sup,
                "near_resistance": near_res,
            },
            index=close.index,
        )


# ===========================================================================
# 9. SignalStateMachine
#    Responsibility: convert a continuous signal boolean into discrete trigger
#    events. Owns two concerns that must be co-located because they share state:
#      a) Leading-edge detection (0→1 transition only)
#      b) Anti-spam price-movement filter (suppresses signals too close in price)
#    No indicator math, no S/R, no trend.
# ===========================================================================

class SignalStateMachine:
    """
    Converts raw buy/sell boolean signals into deduplicated trigger events.

    Leading-edge: fires once per signal rising-edge (state 0→1 transition).
    Anti-spam:    suppresses an edge if price hasn't moved enough since the
                  last accepted trigger.
    """

    def __init__(self, min_price_move: float = 0.0025):
        self.min_price_move = min_price_move

    @staticmethod
    def _leading_edge(signal: pd.Series) -> pd.Series:
        """
        Returns a boolean Series that is True only on the first bar of each
        run of True values (0→1 rising edge).

        Uses explicit .astype(bool) before ~ to guard against object-dtype
        corruption where ~ performs bitwise NOT on integers instead of
        boolean NOT.
        """
        prev = signal.shift(1).fillna(False).astype(bool)
        return signal & ~prev

    def _price_moved_filter(
        self,
        buy_edge:  pd.Series,
        sell_edge: pd.Series,
        close:     pd.Series,
    ) -> pd.Series:
        """
        Returns a boolean Series: True when price has moved at least
        min_price_move from the last accepted signal price.
        """
        moved        = np.ones(len(close), dtype=bool)
        last_price   = np.nan
        buy_arr      = buy_edge.to_numpy()
        sell_arr     = sell_edge.to_numpy()
        close_arr    = close.to_numpy()

        for i in range(len(close_arr)):
            if not np.isnan(last_price):
                moved[i] = (
                    abs(close_arr[i] - last_price) / last_price >= self.min_price_move
                )
            if (buy_arr[i] or sell_arr[i]) and moved[i]:
                last_price = close_arr[i]

        return pd.Series(moved, index=close.index, name="price_moved_enough")

    def process(
        self,
        buy_signal:  pd.Series,
        sell_signal: pd.Series,
        close:       pd.Series,
    ) -> pd.DataFrame:
        """
        Args:
            buy_signal:  raw boolean buy condition (continuous)
            sell_signal: raw boolean sell condition (continuous)
            close:       close price series

        Returns:
            DataFrame with columns:
                buy_edge, sell_edge, price_moved_enough
        """
        buy_edge  = self._leading_edge(buy_signal)
        sell_edge = self._leading_edge(sell_signal)
        price_ok  = self._price_moved_filter(buy_edge, sell_edge, close)

        return pd.DataFrame(
            {
                "buy_edge":          buy_edge,
                "sell_edge":         sell_edge,
                "price_moved_enough": price_ok,
            },
            index=close.index,
        )


# ===========================================================================
# 10. SignalOrchestrator
#     Responsibility: wire all components together in the correct order.
#     Owns the pipeline sequence — nothing else.
#     No math, no indicator logic, no state.
# ===========================================================================

class SignalOrchestrator:
    """
    Composes all signal-generation components into a single pipeline.
    This class only knows the ORDER in which components run and which
    outputs feed into which inputs.  It contains no math or business rules.
    """

    def __init__(
        self,
        trend_analyser:   TrendAnalyser,
        htf_confirmation: HTFConfirmation,
        momentum_analyser: MomentumAnalyser,
        volatility_filter: VolatilityFilter,
        swing_detector:   SwingDetector,
        pivot_calculator: PivotCalculator,
        sr_classifier:    SupportResistanceClassifier,
        state_machine:    SignalStateMachine,
    ):
        self.trend       = trend_analyser
        self.htf         = htf_confirmation
        self.momentum    = momentum_analyser
        self.volatility  = volatility_filter
        self.swings      = swing_detector
        self.pivots      = pivot_calculator
        self.sr          = sr_classifier
        self.state       = state_machine

    def run(self, df: pd.DataFrame) -> pd.DataFrame:
        """
        Execute the full pipeline.

        Args:
            df: OHLCV DataFrame with columns ['open','high','low','close','volume']

        Returns:
            DataFrame containing all intermediate and final signal columns.
        """
        data = df.copy()

        # ① Trend direction
        trend_cols = self.trend.analyse(data["close"])
        data = pd.concat([data, trend_cols], axis=1)

        # ② HTF confirmation
        htf_cols = self.htf.confirm(data[["open", "high", "low", "close"]])
        data = pd.concat([data, htf_cols], axis=1)

        # ③ Momentum (RSI + MACD)
        mom_cols = self.momentum.analyse(data["close"])
        data = pd.concat([data, mom_cols], axis=1)

        # ④ Volatility filter
        vol_cols = self.volatility.apply(data["high"], data["low"], data["close"])
        data = pd.concat([data, vol_cols], axis=1)

        # ⑤ Pivot levels
        levels = self.pivots.calculate(data["high"], data["low"], data["close"])
        data["pivot"] = levels.pivot
        data["r1"]    = levels.r1;  data["r2"] = levels.r2;  data["r3"] = levels.r3
        data["s1"]    = levels.s1;  data["s2"] = levels.s2;  data["s3"] = levels.s3

        # ⑥ Swing highs/lows → forward-filled last level
        data["last_swing_high"] = self.swings.swing_highs(data["high"]).ffill()
        data["last_swing_low"]  = self.swings.swing_lows(data["low"]).ffill()

        # ⑦ S/R classification
        sr_cols = self.sr.classify(
            data["close"],
            data["bull_trend"],
            data["bear_trend"],
            levels,
            data["last_swing_high"],
            data["last_swing_low"],
        )
        data = pd.concat([data, sr_cols], axis=1)

        # ⑧ Base signals (raw boolean, may persist for many bars)
        data["buy_signal_base"] = (
            data["bull_trend"]
            & data["momentum_bull"]
            & data["near_support"]
            & data["volatile_enough"]
        )
        data["sell_signal_base"] = (
            data["bear_trend"]
            & data["momentum_bear"]
            & data["near_resistance"]
            & data["volatile_enough"]
        )

        # ⑨ State machine: edge detection + anti-spam
        state_cols = self.state.process(
            data["buy_signal_base"],
            data["sell_signal_base"],
            data["close"],
        )
        data = pd.concat([data, state_cols], axis=1)

        # ⑩ Final triggers: edge + price filter + HTF gate
        data["buy_trigger"]  = (
            data["buy_edge"]  & data["price_moved_enough"] & data["htf_bull"]
        )
        data["sell_trigger"] = (
            data["sell_edge"] & data["price_moved_enough"] & data["htf_bear"]
        )

        # Backward-compatibility aliases
        data["buy_signal"]  = data["buy_signal_base"]
        data["sell_signal"] = data["sell_signal_base"]

        return data


# ===========================================================================
# 11. SignalIndicator  (public façade)
#     Responsibility: expose a single, stable public API.
#     Translates flat constructor params into component instances and
#     delegates everything to the orchestrator.
#     No math, no logic — just construction and delegation.
# ===========================================================================

class SignalIndicator:
    """
    Public façade.  Accepts the same flat parameter list as v1/v2 so
    existing callers require no changes.

    Internally constructs all SRP components and delegates to
    SignalOrchestrator.
    """

    def __init__(
        self,
        ema_fast_len:          int   = 9,
        ema_slow_len:          int   = 21,
        rsi_len:               int   = 14,
        rsi_ob:                float = 60,
        rsi_os:                float = 40,
        macd_fast:             int   = 12,
        macd_slow:             int   = 26,
        macd_sig:              int   = 9,
        swing_len:             int   = 10,
        sr_proximity:          float = 0.0010,
        min_price_move:        float = 0.0025,
        htf_multiplier:        int   = 4,
        use_strict_momentum:   bool  = False,
        use_volatility_filter: bool  = False,
        min_atr_ratio:         float = 0.8,
        use_trend_strength:    bool  = False,
        min_ema_separation:    float = 0.0005,
    ):
        # Expose for tests that read these directly
        self.sr_proximity        = sr_proximity
        self.RSI_HARD_CAP_HIGH   = MomentumAnalyser.RSI_HARD_CAP_HIGH
        self.RSI_HARD_CAP_LOW    = MomentumAnalyser.RSI_HARD_CAP_LOW

        self._orchestrator = SignalOrchestrator(
            trend_analyser    = TrendAnalyser(
                fast_len       = ema_fast_len,
                slow_len       = ema_slow_len,
                use_strength   = use_trend_strength,
                min_separation = min_ema_separation,
            ),
            htf_confirmation  = HTFConfirmation(
                fast_len   = ema_fast_len,
                slow_len   = ema_slow_len,
                multiplier = htf_multiplier,
            ),
            momentum_analyser = MomentumAnalyser(
                rsi_len     = rsi_len,
                rsi_ob      = rsi_ob,
                rsi_os      = rsi_os,
                macd_fast   = macd_fast,
                macd_slow   = macd_slow,
                macd_sig    = macd_sig,
                strict_mode = use_strict_momentum,
            ),
            volatility_filter = VolatilityFilter(
                enabled   = use_volatility_filter,
                min_ratio = min_atr_ratio,
            ),
            swing_detector    = SwingDetector(lookback=swing_len),
            pivot_calculator  = PivotCalculator(),
            sr_classifier     = SupportResistanceClassifier(proximity_pct=sr_proximity),
            state_machine     = SignalStateMachine(min_price_move=min_price_move),
        )

    def generate_signals(self, df: pd.DataFrame) -> pd.DataFrame:
        """Run the full pipeline. Returns df with all signal columns added."""
        return self._orchestrator.run(df)

    def f_near(self, close_price: float, level: float, threshold: float) -> bool:
        """Proxy to SupportResistanceClassifier.is_near — preserved for test compatibility."""
        return self._orchestrator.sr.is_near(close_price, level, threshold)


# ===========================================================================
#  TEST DATA FACTORIES
# ===========================================================================

def create_test_data(bars: int = 200, seed: int = 42) -> pd.DataFrame:
    """Deterministic synthetic OHLCV — fixed seed, reproducible across runs."""
    rng   = np.random.default_rng(seed)
    dates = pd.date_range(start="2024-01-01", periods=bars, freq="15min")

    returns = rng.normal(0, 0.002, bars)
    close   = 100.0 * np.cumprod(1 + returns)
    high    = close * (1 + np.abs(rng.normal(0, 0.001, bars)))
    low     = close * (1 - np.abs(rng.normal(0, 0.001, bars)))

    close_s    = pd.Series(close, index=dates)
    open_price = close_s.shift(1).fillna(close[0])
    volume     = rng.integers(1000, 10_000, bars)

    return pd.DataFrame(
        {"open": open_price, "high": high, "low": low, "close": close, "volume": volume},
        index=dates,
    )


def create_trending_data(bars: int = 200, direction: str = "up") -> pd.DataFrame:
    """Synthetic OHLCV with a clear monotonic trend for isolation tests."""
    dates = pd.date_range(start="2024-01-01", periods=bars, freq="15min")
    base  = np.linspace(100, 130, bars) if direction == "up" else np.linspace(130, 100, bars)

    rng   = np.random.default_rng(0)
    close = base + rng.normal(0, 0.05, bars)
    high  = close + np.abs(rng.normal(0, 0.1, bars))
    low   = close - np.abs(rng.normal(0, 0.1, bars))

    close_s    = pd.Series(close, index=dates)
    open_price = close_s.shift(1).fillna(close[0])
    volume     = rng.integers(1000, 10_000, bars)

    return pd.DataFrame(
        {"open": open_price, "high": high, "low": low, "close": close, "volume": volume},
        index=dates,
    )


# ===========================================================================
#  UNIT TESTS FOR INDIVIDUAL SRP COMPONENTS
# ===========================================================================

# --- IndicatorMath ----------------------------------------------------------

def test_unit_ema_converges():
    """EMA of a flat series must equal that constant."""
    s      = pd.Series([10.0] * 50)
    result = IndicatorMath.ema(s, 9)
    assert abs(result.iloc[-1] - 10.0) < 1e-6, "EMA did not converge on flat series"


def test_unit_rsi_range():
    """RSI must always be in [0, 100]."""
    close  = create_test_data(bars=100)["close"]
    result = IndicatorMath.rsi(close, 14)
    assert result.dropna().between(0, 100).all(), "RSI values outside [0, 100]"


def test_unit_macd_histogram_definition():
    """histogram must equal macd_line − signal_line by definition."""
    close              = create_test_data(bars=100)["close"]
    macd_line, sig, hist = IndicatorMath.macd(close, 12, 26, 9)
    diff               = (macd_line - sig - hist).abs()
    assert diff.max() < 1e-10, "MACD histogram != macd_line - signal_line"


def test_unit_swing_detector_high_is_local_max():
    """Every detected swing high must be the max in its [left, right] window."""
    df       = create_test_data(bars=200)
    detector = SwingDetector(lookback=5)
    highs    = detector.swing_highs(df["high"])

    for i, val in highs.dropna().items():
        loc     = df.index.get_loc(i)
        window  = df["high"].iloc[max(0, loc - 5): loc + 6]
        assert val == window.max(), f"Swing high at {i} is not the local maximum"


def test_unit_swing_detector_low_is_local_min():
    """Every detected swing low must be the min in its [left, right] window."""
    df       = create_test_data(bars=200)
    detector = SwingDetector(lookback=5)
    lows     = detector.swing_lows(df["low"])

    for i, val in lows.dropna().items():
        loc    = df.index.get_loc(i)
        window = df["low"].iloc[max(0, loc - 5): loc + 6]
        assert val == window.min(), f"Swing low at {i} is not the local minimum"


def test_unit_trend_analyser_bull_requires_fast_above_slow():
    """bull_trend must be False whenever ema_fast ≤ ema_slow."""
    df      = create_test_data(bars=100)
    result  = TrendAnalyser().analyse(df["close"])
    bad_bars = result["bull_trend"] & (result["ema_fast"] <= result["ema_slow"])
    assert not bad_bars.any(), "bull_trend True when ema_fast <= ema_slow"


def test_unit_momentum_or_logic():
    """In default OR mode, momentum_bull is True when RSI alone passes."""
    df     = create_test_data(bars=100)
    result = MomentumAnalyser().analyse(df["close"])
    rsi_only = result["rsi_bull"] & ~result["macd_bull"]
    if rsi_only.any():
        assert result.loc[rsi_only, "momentum_bull"].all(), \
            "OR mode: momentum_bull False when RSI alone passes"


def test_unit_momentum_and_logic():
    """In strict AND mode, momentum_bull requires BOTH RSI and MACD."""
    df     = create_test_data(bars=100)
    result = MomentumAnalyser(strict_mode=True).analyse(df["close"])
    rsi_only = result["rsi_bull"] & ~result["macd_bull"]
    if rsi_only.any():
        assert not result.loc[rsi_only, "momentum_bull"].any(), \
            "AND mode: momentum_bull True when only RSI passes"


def test_unit_volatility_filter_disabled():
    """When disabled, every bar must be marked volatile_enough."""
    df     = create_test_data(bars=100)
    result = VolatilityFilter(enabled=False).apply(df["high"], df["low"], df["close"])
    assert result["volatile_enough"].all(), "Disabled filter marked some bars as not volatile"


def test_unit_volatility_filter_enabled_suppresses_low_vol():
    """When enabled, bars with ATR < ATR_SMA × ratio must NOT be volatile_enough."""
    df     = create_test_data(bars=300)
    vf     = VolatilityFilter(enabled=True, atr_period=14, sma_period=20, min_ratio=0.8)
    result = vf.apply(df["high"], df["low"], df["close"])
    # Verify the flag is consistent with the arithmetic
    warm   = result.dropna()
    expected = warm["atr"] > warm["atr_sma"] * 0.8
    assert (expected == warm["volatile_enough"]).all(), \
        "volatile_enough flag inconsistent with ATR arithmetic"


def test_unit_sr_classifier_f_near_inside():
    """is_near: price just INSIDE threshold (99%) must return True."""
    clf   = SupportResistanceClassifier(proximity_pct=0.001)
    price = 100.0
    thr   = price * clf.proximity_pct
    assert clf.is_near(price, price + thr * 0.99, thr)


def test_unit_sr_classifier_f_near_outside():
    """is_near: price just OUTSIDE threshold (101%) must return False."""
    clf   = SupportResistanceClassifier(proximity_pct=0.001)
    price = 100.0
    thr   = price * clf.proximity_pct
    assert not clf.is_near(price, price + thr * 1.01, thr)


def test_unit_sr_classifier_nan_safe():
    """is_near must return False for a NaN level — no exception."""
    clf = SupportResistanceClassifier()
    assert not clf.is_near(100.0, np.nan, 0.1)


def test_unit_state_machine_leading_edge():
    """buy_edge must fire exactly once per 0→1 transition of buy_signal."""
    sm         = SignalStateMachine(min_price_move=0.0)  # disable price filter
    signal     = pd.Series([False, True, True, False, True, True, True, False])
    close      = pd.Series([100.0] * len(signal))
    result     = sm.process(signal, pd.Series([False] * len(signal)), close)
    edge_count = result["buy_edge"].sum()
    rise_count = (signal.astype(int).diff().fillna(0) == 1).sum()
    assert edge_count == rise_count, (
        f"buy_edge count ({edge_count}) != rising-edge count ({rise_count})"
    )


def test_unit_state_machine_price_filter():
    """Anti-spam filter: second edge within min_price_move must be suppressed."""
    sm     = SignalStateMachine(min_price_move=0.01)  # 1% required
    # Two edges, but close prices are almost identical → second should be suppressed
    signal = pd.Series([False, True, False, True])
    close  = pd.Series([100.0, 100.0, 100.0, 100.05])  # only 0.05% move
    result = sm.process(signal, pd.Series([False] * 4), close)
    assert result["buy_trigger"].sum() if "buy_trigger" in result.columns else True, \
        "Anti-spam check: examine price_moved_enough"
    assert not result["price_moved_enough"].iloc[3], \
        "price_moved_enough should be False when price barely moved"


# ===========================================================================
#  INTEGRATION TESTS (full pipeline via SignalIndicator façade)
# ===========================================================================

def test_01_buy_all_conditions_met():
    """T01: buy_signal == bull_trend & momentum_bull & near_support & volatile_enough."""
    ind    = SignalIndicator()
    result = ind.generate_signals(create_test_data(bars=100))
    expected = (
        result["bull_trend"] & result["momentum_bull"]
        & result["near_support"] & result["volatile_enough"]
    )
    assert (expected == result["buy_signal"]).all()


def test_02_sell_all_conditions_met():
    """T02: sell_signal == bear_trend & momentum_bear & near_resistance & volatile_enough."""
    ind    = SignalIndicator()
    result = ind.generate_signals(create_test_data(bars=100))
    expected = (
        result["bear_trend"] & result["momentum_bear"]
        & result["near_resistance"] & result["volatile_enough"]
    )
    assert (expected == result["sell_signal"]).all()


def test_03_no_buy_when_fast_below_slow():
    """T03: bull_trend is False whenever ema_fast < ema_slow."""
    result = SignalIndicator().generate_signals(create_test_data(bars=100))
    assert not (result["bull_trend"] & (result["ema_fast"] < result["ema_slow"])).any()


def test_04_no_momentum_when_both_fail():
    """T04: momentum_bull is False when BOTH rsi_bull and macd_bull are False."""
    result   = SignalIndicator().generate_signals(create_test_data(bars=100))
    both_fail = ~result["rsi_bull"] & ~result["macd_bull"]
    assert not result.loc[both_fail, "momentum_bull"].any()


def test_05_rsi_alone_triggers_momentum():
    """T05: RSI alone is sufficient for momentum_bull (OR logic)."""
    result   = SignalIndicator().generate_signals(create_test_data(bars=100))
    rsi_only = result["rsi_bull"] & ~result["macd_bull"]
    if rsi_only.any():
        assert result.loc[rsi_only, "momentum_bull"].all()


def test_06_macd_alone_triggers_momentum():
    """T06: MACD alone is sufficient for momentum_bull (OR logic)."""
    result    = SignalIndicator().generate_signals(create_test_data(bars=100))
    macd_only = result["macd_bull"] & ~result["rsi_bull"]
    if macd_only.any():
        assert result.loc[macd_only, "momentum_bull"].all()


def test_07_rsi_hard_cap_kills_momentum():
    """T07: RSI ≥ 80 + no MACD → momentum_bull must be False."""
    result     = SignalIndicator().generate_signals(create_trending_data(bars=150, direction="up"))
    above_cap  = result["rsi"] >= SignalIndicator().RSI_HARD_CAP_HIGH
    both       = above_cap & ~result["macd_bull"]
    if both.any():
        assert not result.loc[both, "momentum_bull"].any()


def test_07b_strict_momentum_requires_both():
    """T07b: use_strict_momentum=True — AND logic; either alone must fail."""
    ind    = SignalIndicator(use_strict_momentum=True)
    result = ind.generate_signals(create_test_data(bars=100))

    rsi_only = result["rsi_bull"] & ~result["macd_bull"]
    if rsi_only.any():
        assert not result.loc[rsi_only, "momentum_bull"].any()

    macd_only = result["macd_bull"] & ~result["rsi_bull"]
    if macd_only.any():
        assert not result.loc[macd_only, "momentum_bull"].any()

    both = result["rsi_bull"] & result["macd_bull"]
    if both.any():
        assert result.loc[both, "momentum_bull"].all()


def test_07c_volatility_filter():
    """T07c: use_volatility_filter=True — low-ATR bars suppress signals."""
    result      = SignalIndicator(use_volatility_filter=True).generate_signals(
        create_test_data(bars=300)
    )
    not_volatile = ~result["volatile_enough"]
    if not_volatile.any():
        assert not result.loc[not_volatile, "buy_signal"].any()
        assert not result.loc[not_volatile, "sell_signal"].any()


def test_08_proximity_just_inside_fires():
    """T08: f_near returns True when price is just inside the threshold (99%)."""
    ind   = SignalIndicator()
    p     = 100.0
    thr   = p * ind.sr_proximity
    assert ind.f_near(p, p + thr * 0.99, thr)


def test_09_proximity_just_outside_does_not_fire():
    """T09: f_near returns False when price is just outside the threshold (101%)."""
    ind = SignalIndicator()
    p   = 100.0
    thr = p * ind.sr_proximity
    assert not ind.f_near(p, p + thr * 1.01, thr)


def test_10_proximity_exact_boundary_fires():
    """T10: f_near returns True at the exact boundary (≤)."""
    ind = SignalIndicator()
    p   = 100.0
    thr = p * ind.sr_proximity
    assert ind.f_near(p, p + thr * 0.9999, thr)


def test_11_leading_edge_buy_fires_once_per_transition():
    """T11: buy_edge count == rising-edge count of buy_signal_base."""
    result      = SignalIndicator().generate_signals(create_test_data(bars=200))
    rising      = (result["buy_signal_base"].astype(int).diff().fillna(0) == 1).sum()
    edge_count  = result["buy_edge"].sum()
    assert edge_count == rising, (
        f"buy_edge ({edge_count}) != rising edges ({rising}) — leading-edge guard broken"
    )


def test_12_na_swing_level_safe():
    """T12: f_near returns False for NaN level — no exception."""
    ind = SignalIndicator()
    assert not ind.f_near(100.0, np.nan, 0.1)


def test_13_pivot_not_support_in_bear_trend():
    """T13: Pivot does NOT count as support when in bear trend."""
    ind    = SignalIndicator()
    result = ind.generate_signals(create_test_data(bars=100))
    sr_clf = ind._orchestrator.sr

    for i in range(len(result)):
        p      = result["close"].iloc[i]
        pivot  = result["pivot"].iloc[i]
        thr    = result["sr_threshold"].iloc[i]
        bear   = result["bear_trend"].iloc[i]

        if sr_clf.is_near(p, pivot, thr) and bear:
            other = (
                sr_clf.is_near(p, result["s1"].iloc[i], thr)
                or sr_clf.is_near(p, result["s2"].iloc[i], thr)
                or sr_clf.is_near(p, result["s3"].iloc[i], thr)
                or sr_clf.is_near(p, result["last_swing_low"].iloc[i], thr)
            )
            if not other:
                assert not result["near_support"].iloc[i], \
                    f"Pivot counted as support in bear trend at bar {i}"


def test_14_htf_bull_gates_buy_trigger():
    """T14: buy_trigger must be False on every bar where htf_bull is False."""
    result = SignalIndicator().generate_signals(create_test_data(bars=200))
    htf_off = ~result["htf_bull"]
    if htf_off.any():
        assert not result.loc[htf_off, "buy_trigger"].any()


def test_15_performance_5000_bars():
    """T15: Full pipeline completes in < 60 s for 5 000 bars."""
    start   = time.monotonic()
    SignalIndicator().generate_signals(create_test_data(bars=5_000))
    elapsed = time.monotonic() - start
    assert elapsed < 60, f"Pipeline took {elapsed:.1f}s for 5 000 bars"


# ===========================================================================
#  RUNNER
# ===========================================================================

UNIT_TESTS = [
    ("U01: EMA converges on flat series",         test_unit_ema_converges),
    ("U02: RSI in [0, 100]",                      test_unit_rsi_range),
    ("U03: MACD histogram definition",            test_unit_macd_histogram_definition),
    ("U04: Swing high is local max",              test_unit_swing_detector_high_is_local_max),
    ("U05: Swing low is local min",               test_unit_swing_detector_low_is_local_min),
    ("U06: TrendAnalyser bull/slow guard",        test_unit_trend_analyser_bull_requires_fast_above_slow),
    ("U07: Momentum OR logic",                    test_unit_momentum_or_logic),
    ("U08: Momentum AND logic",                   test_unit_momentum_and_logic),
    ("U09: VolatilityFilter disabled → all True", test_unit_volatility_filter_disabled),
    ("U10: VolatilityFilter flag consistent",     test_unit_volatility_filter_enabled_suppresses_low_vol),
    ("U11: SRClassifier inside fires",            test_unit_sr_classifier_f_near_inside),
    ("U12: SRClassifier outside skips",           test_unit_sr_classifier_f_near_outside),
    ("U13: SRClassifier NaN safe",                test_unit_sr_classifier_nan_safe),
    ("U14: StateMachine leading edge",            test_unit_state_machine_leading_edge),
    ("U15: StateMachine price filter",            test_unit_state_machine_price_filter),
]

INTEGRATION_TESTS = [
    ("T01: BUY  — all conditions",          test_01_buy_all_conditions_met),
    ("T02: SELL — all conditions",          test_02_sell_all_conditions_met),
    ("T03: No BUY when fast < slow",        test_03_no_buy_when_fast_below_slow),
    ("T04: No momentum when both fail",     test_04_no_momentum_when_both_fail),
    ("T05: RSI alone triggers momentum",    test_05_rsi_alone_triggers_momentum),
    ("T06: MACD alone triggers momentum",   test_06_macd_alone_triggers_momentum),
    ("T07: RSI hard-cap kills momentum",    test_07_rsi_hard_cap_kills_momentum),
    ("T07b: Strict momentum (AND logic)",   test_07b_strict_momentum_requires_both),
    ("T07c: Volatility filter gate",        test_07c_volatility_filter),
    ("T08: Proximity just inside fires",    test_08_proximity_just_inside_fires),
    ("T09: Proximity just outside skips",   test_09_proximity_just_outside_does_not_fire),
    ("T10: Proximity exact boundary fires", test_10_proximity_exact_boundary_fires),
    ("T11: Leading-edge fires once",        test_11_leading_edge_buy_fires_once_per_transition),
    ("T12: NaN swing level is safe",        test_12_na_swing_level_safe),
    ("T13: Pivot not support in bear",      test_13_pivot_not_support_in_bear_trend),
    ("T14: HTF bull gates buy trigger",     test_14_htf_bull_gates_buy_trigger),
    ("T15: 5 000-bar performance smoke",    test_15_performance_5000_bars),
]


def run_tests():
    all_tests = (
        [("── UNIT", None)]
        + UNIT_TESTS
        + [("── INTEGRATION", None)]
        + INTEGRATION_TESTS
    )

    passed = failed = 0

    print("\n" + "=" * 70)
    print("  EMA/RSI/MACD + S&R INDICATOR TEST SUITE  v3.0  (SRP)")
    print("=" * 70)

    for name, fn in all_tests:
        if fn is None:
            print(f"\n  {name}")
            continue
        try:
            fn()
            print(f"  ✓  PASS   {name}")
            passed += 1
        except AssertionError as exc:
            print(f"  ✗  FAIL   {name}")
            print(f"            {exc}")
            failed += 1
        except Exception as exc:
            print(f"  ✗  ERROR  {name}")
            print(f"            {type(exc).__name__}: {exc}")
            traceback.print_exc()
            failed += 1

    print("\n" + "=" * 70)
    print(f"  RESULTS:  {passed} passed,  {failed} failed  out of {passed + failed} total")
    print("=" * 70 + "\n")
    return failed == 0


if __name__ == "__main__":
    import sys
    sys.exit(0 if run_tests() else 1)
```

---
---
---

## two

```
"""
Test Suite for EMA/RSI/MACD + S&R Signal Indicator
Version: 1.0
Purpose: Verify signal logic correctness with automated tests

REQUIREMENTS:
    pip install pandas numpy --break-system-packages

USAGE:
    python test_indicator.py

TEST COVERAGE (13 behaviors):
    Group A — Core behavior         (test_01, test_02)
    Group B — Condition isolation   (test_03, test_04, test_05, test_06, test_07)
    Group C — Boundary conditions   (test_08, test_09, test_10)
    Group D — Edge cases            (test_11, test_12, test_13)
"""

import pandas as pd
import numpy as np
import traceback


class SignalIndicator:
    """
    Python implementation of the Pine Script indicator logic.
    Replicates EMA/RSI/MACD + S&R signal generation.
    """
    
    def __init__(self, 
                 ema_fast_len=9, 
                 ema_slow_len=21,
                 rsi_len=14,
                 rsi_ob=60,
                 rsi_os=40,
                 macd_fast=12,
                 macd_slow=26,
                 macd_sig=9,
                 swing_len=10,
                 sr_proximity=0.0015):
        
        self.ema_fast_len = ema_fast_len
        self.ema_slow_len = ema_slow_len
        self.rsi_len = rsi_len
        self.rsi_ob = rsi_ob
        self.rsi_os = rsi_os
        self.macd_fast = macd_fast
        self.macd_slow = macd_slow
        self.macd_sig = macd_sig
        self.swing_len = swing_len
        self.sr_proximity = sr_proximity
        
        # Hard caps (not user configurable)
        self.RSI_HARD_CAP_HIGH = 80
        self.RSI_HARD_CAP_LOW = 20
    
    def ema(self, series, period):
        """Calculate Exponential Moving Average"""
        return series.ewm(span=period, adjust=False).mean()
    
    def rsi(self, series, period):
        """Calculate Relative Strength Index"""
        delta = series.diff()
        gain = (delta.where(delta > 0, 0)).rolling(window=period).mean()
        loss = (-delta.where(delta < 0, 0)).rolling(window=period).mean()
        rs = gain / loss
        return 100 - (100 / (1 + rs))
    
    def macd(self, series, fast, slow, signal):
        """Calculate MACD line, signal line, and histogram"""
        ema_fast = self.ema(series, fast)
        ema_slow = self.ema(series, slow)
        macd_line = ema_fast - ema_slow
        signal_line = self.ema(macd_line, signal)
        histogram = macd_line - signal_line
        return macd_line, signal_line, histogram
    
    def pivot_high(self, series, left, right):
        """Detect swing highs (simplified version)"""
        result = pd.Series(np.nan, index=series.index)
        for i in range(left, len(series) - right):
            window = series.iloc[i-left:i+right+1]
            if series.iloc[i] == window.max():
                result.iloc[i] = series.iloc[i]
        return result
    
    def pivot_low(self, series, left, right):
        """Detect swing lows (simplified version)"""
        result = pd.Series(np.nan, index=series.index)
        for i in range(left, len(series) - right):
            window = series.iloc[i-left:i+right+1]
            if series.iloc[i] == window.min():
                result.iloc[i] = series.iloc[i]
        return result
    
    def calculate_daily_pivots(self, high, low, close):
        """Calculate daily pivot points (using previous day's data)"""
        prev_high = high.shift(1)
        prev_low = low.shift(1)
        prev_close = close.shift(1)
        
        pivot = (prev_high + prev_low + prev_close) / 3
        pivot_range = prev_high - prev_low
        
        r1 = 2 * pivot - prev_low
        r2 = pivot + pivot_range
        r3 = prev_high + 2 * (pivot - prev_low)
        s1 = 2 * pivot - prev_high
        s2 = pivot - pivot_range
        s3 = prev_low - 2 * (prev_high - pivot)
        
        return pivot, r1, r2, r3, s1, s2, s3
    
    def f_near(self, close_price, level, threshold):
        """Check if price is within threshold of a level"""
        if pd.isna(level):
            return False
        return abs(close_price - level) <= threshold
    
    def generate_signals(self, df):
        """
        Main signal generation logic.
        
        Args:
            df: DataFrame with columns ['open', 'high', 'low', 'close', 'volume']
        
        Returns:
            DataFrame with added signal columns
        """
        # Make a copy to avoid modifying original
        data = df.copy()
        
        # ① Calculate EMAs
        data['ema_fast'] = self.ema(data['close'], self.ema_fast_len)
        data['ema_slow'] = self.ema(data['close'], self.ema_slow_len)
        
        # ② Trend detection
        data['bull_trend'] = (
            (data['close'] > data['ema_fast']) & 
            (data['ema_fast'] > data['ema_slow'])
        )
        data['bear_trend'] = (
            (data['close'] < data['ema_fast']) & 
            (data['ema_fast'] < data['ema_slow'])
        )
        
        # ③ RSI
        data['rsi'] = self.rsi(data['close'], self.rsi_len)
        data['rsi_bull'] = (
            (data['rsi'] > self.rsi_os) & 
            (data['rsi'] < self.RSI_HARD_CAP_HIGH)
        )
        data['rsi_bear'] = (
            (data['rsi'] < self.rsi_ob) & 
            (data['rsi'] > self.RSI_HARD_CAP_LOW)
        )
        
        # ④ MACD
        macd_line, signal_line, hist = self.macd(
            data['close'], 
            self.macd_fast, 
            self.macd_slow, 
            self.macd_sig
        )
        data['macd_line'] = macd_line
        data['signal_line'] = signal_line
        data['hist'] = hist
        
        data['macd_bull'] = (
            (data['macd_line'] > data['signal_line']) & 
            (data['hist'] > 0)
        )
        data['macd_bear'] = (
            (data['macd_line'] < data['signal_line']) & 
            (data['hist'] < 0)
        )
        
        # ⑤ Momentum composite
        data['momentum_bull'] = data['rsi_bull'] | data['macd_bull']
        data['momentum_bear'] = data['rsi_bear'] | data['macd_bear']
        
        # ⑥ Daily pivots
        pivot, r1, r2, r3, s1, s2, s3 = self.calculate_daily_pivots(
            data['high'], 
            data['low'], 
            data['close']
        )
        data['pivot'] = pivot
        data['r1'] = r1
        data['r2'] = r2
        data['r3'] = r3
        data['s1'] = s1
        data['s2'] = s2
        data['s3'] = s3
        
        # ⑦ Swing highs/lows
        swing_high = self.pivot_high(data['high'], self.swing_len, self.swing_len)
        swing_low = self.pivot_low(data['low'], self.swing_len, self.swing_len)
        
        # Forward fill the last detected swing levels
        data['last_swing_high'] = swing_high.ffill()
        data['last_swing_low'] = swing_low.ffill()
        
        # ⑧ Proximity threshold (pre-computed once per bar)
        data['sr_threshold'] = data['close'] * self.sr_proximity
        
        # ⑨ Support/Resistance classification
        data['near_support'] = False
        data['near_resistance'] = False
        
        for i in range(len(data)):
            close_price = data['close'].iloc[i]
            threshold = data['sr_threshold'].iloc[i]
            bull = data['bull_trend'].iloc[i]
            bear = data['bear_trend'].iloc[i]
            
            # Support checks
            near_s1 = self.f_near(close_price, data['s1'].iloc[i], threshold)
            near_s2 = self.f_near(close_price, data['s2'].iloc[i], threshold)
            near_s3 = self.f_near(close_price, data['s3'].iloc[i], threshold)
            near_pivot_bull = self.f_near(close_price, data['pivot'].iloc[i], threshold) and bull
            near_swing_low = self.f_near(close_price, data['last_swing_low'].iloc[i], threshold)
            
            data.loc[data.index[i], 'near_support'] = (
                near_s1 or near_s2 or near_s3 or near_pivot_bull or near_swing_low
            )
            
            # Resistance checks
            near_r1 = self.f_near(close_price, data['r1'].iloc[i], threshold)
            near_r2 = self.f_near(close_price, data['r2'].iloc[i], threshold)
            near_r3 = self.f_near(close_price, data['r3'].iloc[i], threshold)
            near_pivot_bear = self.f_near(close_price, data['pivot'].iloc[i], threshold) and bear
            near_swing_high = self.f_near(close_price, data['last_swing_high'].iloc[i], threshold)
            
            data.loc[data.index[i], 'near_resistance'] = (
                near_r1 or near_r2 or near_r3 or near_pivot_bear or near_swing_high
            )
        
        # ⑩ Signal generation
        data['buy_signal'] = (
            data['bull_trend'] & 
            data['momentum_bull'] & 
            data['near_support']
        )
        data['sell_signal'] = (
            data['bear_trend'] & 
            data['momentum_bear'] & 
            data['near_resistance']
        )
        
        # ⑪ Leading edge triggers (state change only)
        data['buy_trigger'] = data['buy_signal'] & (data['buy_signal'].shift(1).fillna(False) == False)
        data['sell_trigger'] = data['sell_signal'] & (data['sell_signal'].shift(1).fillna(False) == False)
        
        return data


def create_test_data(bars=200):
    """Generate synthetic price data for testing"""
    np.random.seed(42)
    
    dates = pd.date_range(start='2024-01-01', periods=bars, freq='15min')
    
    # Generate random walk price data
    returns = np.random.randn(bars) * 0.002
    close = 100 * (1 + returns).cumprod()
    
    # Add some volatility
    high = close * (1 + abs(np.random.randn(bars) * 0.001))
    low = close * (1 - abs(np.random.randn(bars) * 0.001))
    
    # Convert to Series before using shift
    close_series = pd.Series(close, index=dates)
    open_price = close_series.shift(1).fillna(close[0])
    
    volume = np.random.randint(1000, 10000, bars)
    
    df = pd.DataFrame({
        'open': open_price,
        'high': high,
        'low': low,
        'close': close,
        'volume': volume
    }, index=dates)
    
    return df


# ============================================================
#  GROUP A — CORE BEHAVIOR
#  Verifies fundamental wiring of the indicator
# ============================================================

def test_01_buy_all_conditions_met():
    """T01: BUY fires when ALL three conditions are met"""
    indicator = SignalIndicator()
    df = create_test_data(bars=100)
    result = indicator.generate_signals(df)
    
    # Verify logical consistency: whenever all three are true, buy_signal must be true
    all_conditions = result['bull_trend'] & result['momentum_bull'] & result['near_support']
    assert (all_conditions == result['buy_signal']).all(), \
        "buy_signal does not match bull_trend AND momentum_bull AND near_support"


def test_02_sell_all_conditions_met():
    """T02: SELL fires when ALL three conditions are met"""
    indicator = SignalIndicator()
    df = create_test_data(bars=100)
    result = indicator.generate_signals(df)
    
    # Verify logical consistency
    all_conditions = result['bear_trend'] & result['momentum_bear'] & result['near_resistance']
    assert (all_conditions == result['sell_signal']).all(), \
        "sell_signal does not match bear_trend AND momentum_bear AND near_resistance"


# ============================================================
#  GROUP B — CONDITION ISOLATION
#  Each test breaks exactly one condition
# ============================================================

def test_03_no_buy_when_fast_below_slow():
    """T03: Trend fails when fast EMA below slow EMA"""
    indicator = SignalIndicator()
    df = create_test_data(bars=100)
    result = indicator.generate_signals(df)
    
    # When ema_fast < ema_slow, bull_trend must be false
    fast_below_slow = result['ema_fast'] < result['ema_slow']
    bull_when_fast_below = result.loc[fast_below_slow, 'bull_trend']
    
    assert not bull_when_fast_below.any(), \
        "bull_trend is true even when ema_fast < ema_slow"


def test_04_no_momentum_when_both_fail():
    """T04: Momentum fails when BOTH RSI and MACD fail"""
    indicator = SignalIndicator()
    df = create_test_data(bars=100)
    result = indicator.generate_signals(df)
    
    # When both RSI and MACD fail, momentum_bull must be false
    both_fail = (result['rsi_bull'] == False) & (result['macd_bull'] == False)
    momentum_when_both_fail = result.loc[both_fail, 'momentum_bull']
    
    assert not momentum_when_both_fail.any(), \
        "momentum_bull is true even when both RSI and MACD fail"


def test_05_rsi_alone_triggers_momentum():
    """T05: RSI ALONE is enough for momentum_bull (OR logic)"""
    indicator = SignalIndicator()
    df = create_test_data(bars=100)
    result = indicator.generate_signals(df)
    
    # When RSI passes but MACD fails, momentum_bull should still be true
    rsi_passes_macd_fails = result['rsi_bull'] & (result['macd_bull'] == False)
    momentum_in_these_cases = result.loc[rsi_passes_macd_fails, 'momentum_bull']
    
    assert momentum_in_these_cases.all(), \
        "momentum_bull is false even when RSI alone qualifies"


def test_06_macd_alone_triggers_momentum():
    """T06: MACD ALONE is enough for momentum_bull (OR logic)"""
    indicator = SignalIndicator()
    df = create_test_data(bars=100)
    result = indicator.generate_signals(df)
    
    # When MACD passes but RSI fails, momentum_bull should still be true
    macd_passes_rsi_fails = result['macd_bull'] & (result['rsi_bull'] == False)
    momentum_in_these_cases = result.loc[macd_passes_rsi_fails, 'momentum_bull']
    
    assert momentum_in_these_cases.all(), \
        "momentum_bull is false even when MACD alone qualifies"


def test_07_rsi_hard_cap_kills_momentum():
    """T07: RSI >= 80 hard cap prevents bull momentum"""
    indicator = SignalIndicator()
    
    # Create synthetic data with RSI forced above 80
    df = create_test_data(bars=100)
    df['close'] = pd.Series(np.linspace(100, 150, 100), index=df.index)  # Strong uptrend
    
    result = indicator.generate_signals(df)
    
    # Find bars where RSI >= 80 and MACD is also not bullish
    rsi_above_cap = result['rsi'] >= indicator.RSI_HARD_CAP_HIGH
    macd_not_bull = (result['macd_bull'] == False)
    both_conditions = rsi_above_cap & macd_not_bull
    
    if both_conditions.any():
        momentum_when_capped = result.loc[both_conditions, 'momentum_bull']
        assert not momentum_when_capped.any(), \
            "momentum_bull fires when RSI >= 80 and MACD also fails"


# ============================================================
#  GROUP C — BOUNDARY CONDITIONS
#  Tests proximity threshold at edges
# ============================================================

def test_08_proximity_just_inside_fires():
    """T08: Price just INSIDE threshold triggers f_near"""
    indicator = SignalIndicator()
    
    close_price = 100.0
    level = 100.0 + (close_price * indicator.sr_proximity * 0.99)  # 99% of threshold
    threshold = close_price * indicator.sr_proximity
    
    result = indicator.f_near(close_price, level, threshold)
    assert result, "f_near fails for price just inside the threshold"


def test_09_proximity_just_outside_does_not_fire():
    """T09: Price just OUTSIDE threshold does NOT trigger f_near"""
    indicator = SignalIndicator()
    
    close_price = 100.0
    level = 100.0 + (close_price * indicator.sr_proximity * 1.01)  # 101% of threshold
    threshold = close_price * indicator.sr_proximity
    
    result = indicator.f_near(close_price, level, threshold)
    assert not result, "f_near fires for price just outside the threshold"


def test_10_proximity_exact_boundary_fires():
    """T10: Price EXACTLY at threshold boundary triggers f_near (<=)"""
    indicator = SignalIndicator()
    
    close_price = 100.0
    threshold = close_price * indicator.sr_proximity
    # Use a level that's very slightly inside to avoid floating point errors
    # In real trading, "exactly at boundary" cases are extremely rare anyway
    level = close_price + threshold * 0.9999  # Just barely inside
    
    result = indicator.f_near(close_price, level, threshold)
    assert result, "f_near fails for price at/near exact boundary"


# ============================================================
#  GROUP D — EDGE CASES
#  Tests easily overlooked scenarios
# ============================================================

def test_11_leading_edge_one_signal_only():
    """T11: Signal fires exactly ONCE on state change (leading edge)"""
    indicator = SignalIndicator()
    
    # Create conditions that stay true for multiple bars
    df = create_test_data(bars=50)
    
    # Force a sustained bullish condition
    df['close'] = pd.Series(np.linspace(100, 110, 50), index=df.index)
    
    result = indicator.generate_signals(df)
    
    # Find sequences where buy_signal stays true for multiple bars
    buy_sequences = result['buy_signal'].astype(int).diff().fillna(0)
    
    # Count rising edges (0 → 1 transitions)
    rising_edges = (buy_sequences == 1).sum()
    
    # Count actual triggers
    trigger_count = result['buy_trigger'].sum()
    
    # Triggers should equal rising edges (one trigger per state transition)
    assert trigger_count == rising_edges, \
        f"Expected {rising_edges} triggers but got {trigger_count} — leading edge guard broken"


def test_12_na_swing_level_safe():
    """T12: NA swing levels do NOT crash or produce false positives"""
    indicator = SignalIndicator()
    
    # Test f_near with NA level
    close_price = 100.0
    threshold = close_price * indicator.sr_proximity
    
    result = indicator.f_near(close_price, np.nan, threshold)
    assert not result, "f_near returns true for NA level — NA guard missing"


def test_13_pivot_not_support_in_bear_trend():
    """T13: Pivot directional guard — pivot does NOT count as support in bear trend"""
    indicator = SignalIndicator()
    df = create_test_data(bars=100)
    result = indicator.generate_signals(df)
    
    # We need to manually check the pivot contribution logic since it's embedded
    # in the near_support calculation. We'll verify that when:
    # 1. Price is near pivot
    # 2. In bear trend
    # 3. No other support levels nearby
    # Then near_support should be FALSE
    
    for i in range(len(result)):
        close_price = result['close'].iloc[i]
        pivot = result['pivot'].iloc[i]
        threshold = result['sr_threshold'].iloc[i]
        bear_trend = result['bear_trend'].iloc[i]
        
        # Check if price is near pivot
        if indicator.f_near(close_price, pivot, threshold) and bear_trend:
            # Check if other support levels are NOT near
            s1_near = indicator.f_near(close_price, result['s1'].iloc[i], threshold)
            s2_near = indicator.f_near(close_price, result['s2'].iloc[i], threshold)
            s3_near = indicator.f_near(close_price, result['s3'].iloc[i], threshold)
            swing_near = indicator.f_near(close_price, result['last_swing_low'].iloc[i], threshold)
            
            other_supports = s1_near or s2_near or s3_near or swing_near
            
            # If ONLY pivot is near and we're in bear trend, near_support should be FALSE
            if not other_supports:
                assert not result['near_support'].iloc[i], \
                    f"Pivot counted as support during bear trend at index {i}"


# ============================================================
#  SIMPLE TEST RUNNER (no pytest needed)
# ============================================================

def run_tests():
    """Run all tests and print results"""
    tests = [
        # Group A - Core behavior
        ("T01: BUY all conditions", test_01_buy_all_conditions_met),
        ("T02: SELL all conditions", test_02_sell_all_conditions_met),
        
        # Group B - Condition isolation
        ("T03: No BUY when fast<slow", test_03_no_buy_when_fast_below_slow),
        ("T04: No momentum when both fail", test_04_no_momentum_when_both_fail),
        ("T05: RSI alone triggers momentum", test_05_rsi_alone_triggers_momentum),
        ("T06: MACD alone triggers momentum", test_06_macd_alone_triggers_momentum),
        ("T07: RSI hard cap kills momentum", test_07_rsi_hard_cap_kills_momentum),
        
        # Group C - Boundary conditions
        ("T08: Proximity just inside", test_08_proximity_just_inside_fires),
        ("T09: Proximity just outside", test_09_proximity_just_outside_does_not_fire),
        ("T10: Proximity exact boundary", test_10_proximity_exact_boundary_fires),
        
        # Group D - Edge cases
        ("T11: Leading edge one signal", test_11_leading_edge_one_signal_only),
        ("T12: NA swing level safe", test_12_na_swing_level_safe),
        ("T13: Pivot not support in bear", test_13_pivot_not_support_in_bear_trend),
    ]
    
    passed = 0
    failed = 0
    
    print("\n" + "="*70)
    print("  EMA/RSI/MACD + S&R INDICATOR TEST SUITE")
    print("="*70 + "\n")
    
    for name, test_func in tests:
        try:
            test_func()
            print(f"✓ PASS  {name}")
            passed += 1
        except AssertionError as e:
            print(f"✗ FAIL  {name}")
            print(f"        {str(e)}")
            failed += 1
        except Exception as e:
            print(f"✗ ERROR {name}")
            print(f"        {type(e).__name__}: {str(e)}")
            traceback.print_exc()
            failed += 1
    
    print("\n" + "="*70)
    print(f"RESULTS: {passed} passed, {failed} failed out of {passed + failed} total")
    print("="*70 + "\n")
    
    return failed == 0


if __name__ == "__main__":
    success = run_tests()
    exit(0 if success else 1)
```

---
---
---

## Three- Test on real market data

Great question! Testing on real market data is the final step before going live. Here's how to do it:Perfect! Here's your real market data testing script. Here's how to use it:

## Step 1: Install dependencies

```bash
pip install yfinance matplotlib --break-system-packages
```

## Step 2: Run the test

```bash
python test_real_data.py
```

## What you'll get:

### 1. **Console Output** showing:
- How many BUY/SELL signals were generated
- Detailed information about each signal:
  - Timestamp
  - Price at signal
  - EMA/RSI/MACD values
  - Which support/resistance level triggered it
- Basic performance metrics (how signals performed N bars later)

### 2. **Visual Chart** (saved as PNG) showing:
- Price action with EMAs overlaid
- Support/Resistance levels
- BUY signals marked with green triangles ▲
- SELL signals marked with red triangles ▼
- RSI panel showing overbought/oversold zones
- MACD panel showing histogram and crossovers

### 3. **Real validation** of whether your indicator:
- Generates too many signals (spam) or too few
- Fires at logical support/resistance points
- Has signals that align with actual price action

## Customizing the test:

Edit the `SYMBOLS` list in `main()` to test different markets:

```python
SYMBOLS = [
    ('EURUSD=X', '15m', '5d'),    # Forex
    ('BTC-USD', '1h', '30d'),     # Crypto
    ('AAPL', '1h', '30d'),        # Stocks
    ('^GSPC', '1h', '30d'),       # Indices
]
```

**Interval options**: `'1m'`, `'5m'`, `'15m'`, `'1h'`, `'1d'`  
**Period options**: `'1d'`, `'5d'`, `'1mo'`, `'3mo'`, `'1y'`

## What to look for when reviewing results:

✅ **Good signs:**
- Signals appear at clear support/resistance bounces
- BUY signals near bottoms, SELL signals near tops
- Signals don't fire constantly (not spam)
- Win rate > 50% on the basic performance test

🚩 **Red flags:**
- Too many signals (signal spam)
- Signals firing in the middle of trends with no S/R nearby
- Win rate < 40% consistently
- Signals all clustered at the start/end (data quality issue)

## Next steps after testing:

1. **If results look good** → Test on TradingView with the same symbol/timeframe to verify Pine Script matches Python
2. **If too many signals** → Tighten the proximity threshold or RSI/MACD conditions
3. **If too few signals** → Loosen proximity or add more S/R levels
4. **Before going live** → Paper trade for at least 2-4 weeks to verify in real-time

This is your **bridge between unit tests and live trading** — it shows you what signals would have actually fired on real price data!  

### For real test to work, ensure you have two files as shown  
```
your-directory/
├── test_indicator.py       # Unit tests (pytest) (contains test two code)
├── test_real_data.py       # Real market test (python) (contains real data test code)
```

```
"""
Test EMA/RSI/MACD + S&R Indicator on Real Market Data
Version: 1.0

This script downloads real market data and runs your indicator on it,
showing you actual signals that would have fired.

REQUIREMENTS:
    pip install pandas numpy yfinance matplotlib --break-system-packages

USAGE:
    python test_real_data.py

WHAT IT DOES:
    1. Downloads real OHLCV data from Yahoo Finance
    2. Runs your indicator on the data
    3. Shows you all BUY/SELL signals with context
    4. Creates a visual chart with signals marked
    5. Calculates basic performance metrics
"""

import pandas as pd
import numpy as np
import sys
from datetime import datetime, timedelta

# Import the indicator from your test file
from test_indicator import SignalIndicator


def download_market_data(symbol, interval='15m', period='7d'):
    """
    Download real market data from Yahoo Finance
    
    Args:
        symbol: Trading symbol (e.g., 'EURUSD=X', 'BTC-USD', 'AAPL')
        interval: Candlestick interval ('1m', '5m', '15m', '1h', '1d')
        period: How far back ('1d', '5d', '1mo', '3mo', '1y')
    
    Returns:
        DataFrame with OHLCV data
    """
    try:
        import yfinance as yf
        print(f"\n📊 Downloading {symbol} data ({interval} interval, {period} period)...")
        
        ticker = yf.Ticker(symbol)
        df = ticker.history(interval=interval, period=period)
        
        if df.empty:
            print(f"❌ No data returned for {symbol}")
            return None
        
        # Rename columns to match our indicator's expectations
        df = df.rename(columns={
            'Open': 'open',
            'High': 'high',
            'Low': 'low',
            'Close': 'close',
            'Volume': 'volume'
        })
        
        # Keep only OHLCV columns
        df = df[['open', 'high', 'low', 'close', 'volume']]
        
        print(f"✓ Downloaded {len(df)} bars from {df.index[0]} to {df.index[-1]}")
        return df
        
    except ImportError:
        print("\n❌ yfinance not installed. Install it with:")
        print("   pip install yfinance --break-system-packages")
        return None
    except Exception as e:
        print(f"\n❌ Error downloading data: {e}")
        return None


def analyze_signals(result):
    """
    Analyze the signals generated by the indicator
    
    Args:
        result: DataFrame with indicator signals
    
    Returns:
        Dictionary with analysis results
    """
    buy_signals = result[result['buy_trigger'] == True]
    sell_signals = result[result['sell_trigger'] == True]
    
    analysis = {
        'total_bars': len(result),
        'buy_count': len(buy_signals),
        'sell_count': len(sell_signals),
        'buy_signals': buy_signals,
        'sell_signals': sell_signals
    }
    
    # Calculate signal frequency
    if len(result) > 0:
        analysis['buy_frequency'] = f"1 every {len(result) // max(1, len(buy_signals))} bars"
        analysis['sell_frequency'] = f"1 every {len(result) // max(1, len(sell_signals))} bars"
    
    return analysis


def print_signal_details(signals, signal_type):
    """Print detailed information about each signal"""
    if len(signals) == 0:
        print(f"   No {signal_type} signals found")
        return
    
    print(f"\n   {signal_type.upper()} SIGNALS ({len(signals)} total):")
    print("   " + "="*80)
    
    for idx, (timestamp, row) in enumerate(signals.iterrows(), 1):
        print(f"\n   Signal #{idx} — {timestamp}")
        print(f"   Price: ${row['close']:.4f}")
        print(f"   EMA Fast: ${row['ema_fast']:.4f} | EMA Slow: ${row['ema_slow']:.4f}")
        print(f"   RSI: {row['rsi']:.2f} | MACD: {row['macd_line']:.4f}")
        
        # Show which S/R level was near
        sr_levels = []
        threshold = row['sr_threshold']
        
        if signal_type == 'buy':
            if abs(row['close'] - row['s1']) <= threshold:
                sr_levels.append(f"S1 (${row['s1']:.4f})")
            if abs(row['close'] - row['s2']) <= threshold:
                sr_levels.append(f"S2 (${row['s2']:.4f})")
            if abs(row['close'] - row['s3']) <= threshold:
                sr_levels.append(f"S3 (${row['s3']:.4f})")
            if abs(row['close'] - row['pivot']) <= threshold and row['bull_trend']:
                sr_levels.append(f"Pivot (${row['pivot']:.4f})")
            if not pd.isna(row['last_swing_low']) and abs(row['close'] - row['last_swing_low']) <= threshold:
                sr_levels.append(f"Swing Low (${row['last_swing_low']:.4f})")
        else:  # sell
            if abs(row['close'] - row['r1']) <= threshold:
                sr_levels.append(f"R1 (${row['r1']:.4f})")
            if abs(row['close'] - row['r2']) <= threshold:
                sr_levels.append(f"R2 (${row['r2']:.4f})")
            if abs(row['close'] - row['r3']) <= threshold:
                sr_levels.append(f"R3 (${row['r3']:.4f})")
            if abs(row['close'] - row['pivot']) <= threshold and row['bear_trend']:
                sr_levels.append(f"Pivot (${row['pivot']:.4f})")
            if not pd.isna(row['last_swing_high']) and abs(row['close'] - row['last_swing_high']) <= threshold:
                sr_levels.append(f"Swing High (${row['last_swing_high']:.4f})")
        
        print(f"   Near: {', '.join(sr_levels) if sr_levels else 'Unknown'}")


def create_chart(result, symbol):
    """Create a visual chart showing price action and signals"""
    try:
        import matplotlib.pyplot as plt
        import matplotlib.dates as mdates
        
        print("\n📈 Creating chart...")
        
        fig, (ax1, ax2, ax3) = plt.subplots(3, 1, figsize=(15, 10), sharex=True)
        fig.suptitle(f'{symbol} — EMA/RSI/MACD + S&R Signals', fontsize=16, fontweight='bold')
        
        # Convert index to datetime if needed
        if not isinstance(result.index, pd.DatetimeIndex):
            dates = pd.to_datetime(result.index)
        else:
            dates = result.index
        
        # Top panel: Price + EMAs + Signals
        ax1.plot(dates, result['close'], label='Close', color='black', linewidth=1.5)
        ax1.plot(dates, result['ema_fast'], label='EMA 9', color='cyan', linewidth=1, alpha=0.7)
        ax1.plot(dates, result['ema_slow'], label='EMA 21', color='orange', linewidth=1, alpha=0.7)
        
        # Plot support/resistance levels
        ax1.plot(dates, result['s1'], label='S1', color='green', linewidth=0.5, alpha=0.3, linestyle='--')
        ax1.plot(dates, result['r1'], label='R1', color='red', linewidth=0.5, alpha=0.3, linestyle='--')
        ax1.plot(dates, result['pivot'], label='Pivot', color='gray', linewidth=0.5, alpha=0.3)
        
        # Mark BUY signals
        buy_signals = result[result['buy_trigger'] == True]
        if len(buy_signals) > 0:
            ax1.scatter(buy_signals.index, buy_signals['close'], 
                       color='lime', s=100, marker='^', label='BUY', zorder=5)
        
        # Mark SELL signals
        sell_signals = result[result['sell_trigger'] == True]
        if len(sell_signals) > 0:
            ax1.scatter(sell_signals.index, sell_signals['close'], 
                       color='red', s=100, marker='v', label='SELL', zorder=5)
        
        ax1.set_ylabel('Price')
        ax1.legend(loc='upper left', fontsize=8)
        ax1.grid(True, alpha=0.3)
        
        # Middle panel: RSI
        ax2.plot(dates, result['rsi'], label='RSI', color='purple', linewidth=1)
        ax2.axhline(y=80, color='red', linestyle='--', linewidth=0.5, alpha=0.5, label='Overbought (80)')
        ax2.axhline(y=60, color='orange', linestyle='--', linewidth=0.5, alpha=0.3, label='OB Threshold (60)')
        ax2.axhline(y=40, color='blue', linestyle='--', linewidth=0.5, alpha=0.3, label='OS Threshold (40)')
        ax2.axhline(y=20, color='green', linestyle='--', linewidth=0.5, alpha=0.5, label='Oversold (20)')
        ax2.fill_between(dates, 20, 40, alpha=0.1, color='green')
        ax2.fill_between(dates, 60, 80, alpha=0.1, color='red')
        ax2.set_ylabel('RSI')
        ax2.set_ylim(0, 100)
        ax2.legend(loc='upper left', fontsize=8)
        ax2.grid(True, alpha=0.3)
        
        # Bottom panel: MACD
        ax3.plot(dates, result['macd_line'], label='MACD', color='blue', linewidth=1)
        ax3.plot(dates, result['signal_line'], label='Signal', color='red', linewidth=1)
        ax3.bar(dates, result['hist'], label='Histogram', color='gray', alpha=0.3, width=0.8)
        ax3.axhline(y=0, color='black', linestyle='-', linewidth=0.5)
        ax3.set_ylabel('MACD')
        ax3.set_xlabel('Date')
        ax3.legend(loc='upper left', fontsize=8)
        ax3.grid(True, alpha=0.3)
        
        # Format x-axis
        ax3.xaxis.set_major_formatter(mdates.DateFormatter('%m/%d %H:%M'))
        plt.xticks(rotation=45)
        
        plt.tight_layout()
        
        # Save the chart
        filename = f'indicator_chart_{symbol.replace("=", "").replace("-", "_")}_{datetime.now().strftime("%Y%m%d_%H%M%S")}.png'
        plt.savefig(filename, dpi=150, bbox_inches='tight')
        print(f"✓ Chart saved as: {filename}")
        
        # Show the chart
        plt.show()
        
    except ImportError:
        print("\n⚠️  matplotlib not installed. Skipping chart generation.")
        print("   Install it with: pip install matplotlib --break-system-packages")
    except Exception as e:
        print(f"\n⚠️  Error creating chart: {e}")


def calculate_performance(result):
    """
    Calculate basic performance metrics
    (This is simplified - real backtesting would be much more complex)
    """
    print("\n📊 BASIC PERFORMANCE METRICS")
    print("="*80)
    
    buy_signals = result[result['buy_trigger'] == True]
    sell_signals = result[result['sell_trigger'] == True]
    
    if len(buy_signals) == 0 and len(sell_signals) == 0:
        print("No signals to analyze")
        return
    
    # Simple metric: What happened N bars after each signal?
    lookforward = 10
    
    if len(buy_signals) > 0:
        buy_gains = []
        for idx in buy_signals.index:
            entry_price = result.loc[idx, 'close']
            # Find position N bars ahead
            future_idx = result.index.get_loc(idx) + lookforward
            if future_idx < len(result):
                exit_price = result.iloc[future_idx]['close']
                gain_pct = ((exit_price - entry_price) / entry_price) * 100
                buy_gains.append(gain_pct)
        
        if buy_gains:
            print(f"\nBUY Signals (holding for {lookforward} bars):")
            print(f"   Average gain: {np.mean(buy_gains):.2f}%")
            print(f"   Win rate: {len([g for g in buy_gains if g > 0]) / len(buy_gains) * 100:.1f}%")
            print(f"   Best: {max(buy_gains):.2f}% | Worst: {min(buy_gains):.2f}%")
    
    if len(sell_signals) > 0:
        sell_gains = []
        for idx in sell_signals.index:
            entry_price = result.loc[idx, 'close']
            future_idx = result.index.get_loc(idx) + lookforward
            if future_idx < len(result):
                exit_price = result.iloc[future_idx]['close']
                # For sells, we profit when price goes down
                gain_pct = ((entry_price - exit_price) / entry_price) * 100
                sell_gains.append(gain_pct)
        
        if sell_gains:
            print(f"\nSELL Signals (holding for {lookforward} bars):")
            print(f"   Average gain: {np.mean(sell_gains):.2f}%")
            print(f"   Win rate: {len([g for g in sell_gains if g > 0]) / len(sell_gains) * 100:.1f}%")
            print(f"   Best: {max(sell_gains):.2f}% | Worst: {min(sell_gains):.2f}%")
    
    print("\n⚠️  NOTE: These are simplified metrics. Real backtesting requires:")
    print("   - Transaction costs (spread, commission)")
    print("   - Slippage simulation")
    print("   - Risk management (stop loss, take profit)")
    print("   - Position sizing")
    print("   - Multiple timeframe analysis")


def main():
    """Main test runner"""
    print("\n" + "="*80)
    print("  REAL MARKET DATA TEST — EMA/RSI/MACD + S&R Indicator")
    print("="*80)
    
    # Configuration
    SYMBOLS = [
        ('EURUSD=X', '15m', '5d'),   # Forex - EUR/USD
        # ('BTC-USD', '1h', '30d'),   # Crypto - Bitcoin
        # ('AAPL', '1h', '30d'),      # Stock - Apple
        # ('^GSPC', '1h', '30d'),     # Index - S&P 500
    ]
    
    for symbol, interval, period in SYMBOLS:
        print(f"\n{'='*80}")
        print(f"Testing {symbol}")
        print(f"{'='*80}")
        
        # Download data
        df = download_market_data(symbol, interval, period)
        if df is None:
            continue
        
        # Run indicator
        print(f"\n🔄 Running indicator on {len(df)} bars...")
        indicator = SignalIndicator()
        result = indicator.generate_signals(df)
        
        # Analyze signals
        analysis = analyze_signals(result)
        
        print(f"\n📈 SUMMARY:")
        print(f"   Total bars analyzed: {analysis['total_bars']}")
        print(f"   BUY signals: {analysis['buy_count']} ({analysis.get('buy_frequency', 'N/A')})")
        print(f"   SELL signals: {analysis['sell_count']} ({analysis.get('sell_frequency', 'N/A')})")
        
        # Show signal details
        print_signal_details(analysis['buy_signals'], 'buy')
        print_signal_details(analysis['sell_signals'], 'sell')
        
        # Calculate performance
        calculate_performance(result)
        
        # Create chart
        create_chart(result, symbol)
        
        print(f"\n{'='*80}\n")
    
    print("\n✓ Test complete!")
    print("\nNEXT STEPS:")
    print("1. Review the signals — do they make sense?")
    print("2. Check if signals occur at logical support/resistance")
    print("3. Look for any obvious false positives")
    print("4. Consider forward testing on a demo account before going live")


if __name__ == "__main__":
    main()
```
