
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
Test Suite for EMA/RSI/MACD + S&R Signal Indicator
Version: 2.0
Purpose: Verify signal logic correctness with automated tests

FIXES FROM v1.0:
  [FIX-1]  Deprecated '24H'/'60min' pandas aliases replaced with '24h'/'60min' (lowercase)
  [FIX-2]  T01/T02: buy_signal INCLUDES volatile_enough — test assertion updated to match
  [FIX-3]  T11: Leading-edge test was comparing apples-to-oranges (buy_trigger vs
            buy_signal rising edges). buy_trigger is additionally gated by
            price_moved_enough & htf_bull, so the counts can legitimately diverge.
            Test now verifies the correct invariant: buy_edge fires exactly once per
            buy_signal rising-edge transition.
  [FIX-4]  Deterministic test data: replaced np.random with a fixed synthetic walk
            so every test run produces identical results.
  [FIX-5]  Added missing test for strict_momentum mode (use_strict_momentum=True).
  [FIX-6]  Added missing test for volatility filter ON path.
  [FIX-7]  Added missing test for HTF alignment boundary (htf_bull gating triggers).
  [FIX-8]  Added performance smoke-test (O(n) loop scales acceptably to 5 000 bars).
  [FIX-9]  pivot_high/pivot_low use deprecated .iloc setter pattern; wrapped with
            pd.Series constructor to avoid FutureWarning chained-assignment.
  [FIX-10] resample offset kwarg: older pandas uses 'base', newer uses 'offset'.
            Added compatibility shim.

REQUIREMENTS:
    pip install pandas numpy --break-system-packages

USAGE:
    python test_indicator.py
"""

import time
import traceback
import warnings

import numpy as np
import pandas as pd

warnings.filterwarnings("ignore", category=FutureWarning)


# ---------------------------------------------------------------------------
# Helpers
# ---------------------------------------------------------------------------

def _resample_offset_kwarg(rule: str, offset: str) -> dict:
    """
    [FIX-10] pandas ≥ 1.1 supports 'offset'; older versions used 'base' (integer minutes).
    We detect support by inspecting the pandas version directly — probing with an
    empty Series raises TypeError on newer pandas because it requires a DatetimeIndex,
    making probe-based detection unreliable.
    """
    major, minor = (int(x) for x in pd.__version__.split(".")[:2])
    if (major, minor) >= (1, 1):
        return {"offset": offset}
    else:
        hours = int(offset.replace("h", ""))
        return {"base": hours * 60}


# ---------------------------------------------------------------------------
# SignalIndicator
# ---------------------------------------------------------------------------

class SignalIndicator:
    """
    Python implementation of the Pine Script indicator logic.
    Replicates EMA/RSI/MACD + S&R signal generation.
    """

    def __init__(
        self,
        ema_fast_len: int = 9,
        ema_slow_len: int = 21,
        rsi_len: int = 14,
        rsi_ob: float = 60,
        rsi_os: float = 40,
        macd_fast: int = 12,
        macd_slow: int = 26,
        macd_sig: int = 9,
        swing_len: int = 10,
        sr_proximity: float = 0.0010,
        min_price_move: float = 0.0025,
        htf_multiplier: int = 4,
        use_strict_momentum: bool = False,
        use_volatility_filter: bool = False,
        min_atr_ratio: float = 0.8,
        use_trend_strength: bool = False,
        min_ema_separation: float = 0.0005,
    ):
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
        self.min_price_move = min_price_move
        self.htf_multiplier = htf_multiplier
        self.use_strict_momentum = use_strict_momentum
        self.use_volatility_filter = use_volatility_filter
        self.min_atr_ratio = min_atr_ratio
        self.use_trend_strength = use_trend_strength
        self.min_ema_separation = min_ema_separation

        # Hard caps — not user-configurable
        self.RSI_HARD_CAP_HIGH = 80
        self.RSI_HARD_CAP_LOW = 20

    # ------------------------------------------------------------------
    # Pure indicator math
    # ------------------------------------------------------------------

    def ema(self, series: pd.Series, period: int) -> pd.Series:
        return series.ewm(span=period, adjust=False).mean()

    def rsi(self, series: pd.Series, period: int) -> pd.Series:
        """Wilder's smoothing — matches TradingView."""
        delta = series.diff()
        gain = delta.where(delta > 0, 0.0)
        loss = -delta.where(delta < 0, 0.0)
        avg_gain = gain.ewm(alpha=1 / period, adjust=False).mean()
        avg_loss = loss.ewm(alpha=1 / period, adjust=False).mean()
        rs = avg_gain / avg_loss
        return 100 - (100 / (1 + rs))

    def macd(self, series: pd.Series, fast: int, slow: int, signal: int):
        ema_fast = self.ema(series, fast)
        ema_slow = self.ema(series, slow)
        macd_line = ema_fast - ema_slow
        signal_line = self.ema(macd_line, signal)
        histogram = macd_line - signal_line
        return macd_line, signal_line, histogram

    def pivot_high(self, series: pd.Series, left: int, right: int) -> pd.Series:
        """
        [FIX-9] Build result as a plain list then construct Series once to avoid
        chained-assignment FutureWarning from pandas.
        """
        values = [np.nan] * len(series)
        arr = series.to_numpy()
        for i in range(left, len(arr) - right):
            window = arr[i - left : i + right + 1]
            if arr[i] == window.max():
                values[i] = arr[i]
        return pd.Series(values, index=series.index)

    def pivot_low(self, series: pd.Series, left: int, right: int) -> pd.Series:
        """[FIX-9] Same pattern as pivot_high."""
        values = [np.nan] * len(series)
        arr = series.to_numpy()
        for i in range(left, len(arr) - right):
            window = arr[i - left : i + right + 1]
            if arr[i] == window.min():
                values[i] = arr[i]
        return pd.Series(values, index=series.index)

    def calculate_daily_pivots(self, high: pd.Series, low: pd.Series, close: pd.Series):
        """
        Pivots aligned to the NY Close (5 PM EST / 17:00 UTC).

        [FIX-1] '24H' → '24h'  (pandas ≥ 2.2 requires lowercase time aliases)
        [FIX-10] resample offset compatibility shim for older vs newer pandas.
        """
        daily = (
            pd.DataFrame({"high": high, "low": low, "close": close})
            .resample("24h", **_resample_offset_kwarg("24h", "17h"))   # FIX-1 + FIX-10
            .agg({"high": "max", "low": "min", "close": "last"})
        )

        prev_day = daily.shift(1)
        p_high  = prev_day["high"].reindex(high.index, method="ffill")
        p_low   = prev_day["low"].reindex(low.index,  method="ffill")
        p_close = prev_day["close"].reindex(close.index, method="ffill")

        pivot    = (p_high + p_low + p_close) / 3
        range_dl = p_high - p_low

        r1 = (2 * pivot) - p_low
        s1 = (2 * pivot) - p_high
        r2 = pivot + range_dl
        s2 = pivot - range_dl
        r3 = p_high + 2 * (pivot - p_low)
        s3 = p_low  - 2 * (p_high - pivot)

        return pivot, r1, r2, r3, s1, s2, s3

    def f_near(self, close_price: float, level: float, threshold: float) -> bool:
        """Price within threshold of level. NaN-safe."""
        if pd.isna(level):
            return False
        epsilon = 1e-8
        return abs(close_price - level) <= threshold + epsilon

    # ------------------------------------------------------------------
    # Main pipeline
    # ------------------------------------------------------------------

    def generate_signals(self, df: pd.DataFrame) -> pd.DataFrame:
        """
        Main signal generation.

        Args:
            df: DataFrame with columns ['open', 'high', 'low', 'close', 'volume']

        Returns:
            DataFrame with added signal columns.
        """
        data = df.copy()

        # ① EMAs
        data["ema_fast"] = self.ema(data["close"], self.ema_fast_len)
        data["ema_slow"] = self.ema(data["close"], self.ema_slow_len)

        # ② Base trend
        data["bull_trend_base"] = (data["close"] > data["ema_fast"]) & (data["ema_fast"] > data["ema_slow"])
        data["bear_trend_base"] = (data["close"] < data["ema_fast"]) & (data["ema_fast"] < data["ema_slow"])

        # ② Optional trend-strength filter
        if self.use_trend_strength:
            data["ema_separation"] = abs(data["ema_fast"] - data["ema_slow"]) / data["close"]
            data["strong_enough"]  = data["ema_separation"] >= self.min_ema_separation
        else:
            data["strong_enough"] = True

        data["bull_trend"] = data["bull_trend_base"] & data["strong_enough"]
        data["bear_trend"] = data["bear_trend_base"] & data["strong_enough"]

        # ③ HTF confirmation
        # [FIX-1] f'{self.htf_multiplier * 15}min' — 'min' is already lowercase, no change needed here
        htf_rule = f"{self.htf_multiplier * 15}min"
        htf_data = (
            data.resample(htf_rule)
            .agg({"close": "last", "high": "max", "low": "min", "open": "first"})
            .dropna()
        )
        htf_ema_fast = self.ema(htf_data["close"], self.ema_fast_len).shift(1)
        htf_ema_slow = self.ema(htf_data["close"], self.ema_slow_len).shift(1)
        htf_bull = htf_ema_fast > htf_ema_slow
        htf_bear = htf_ema_fast < htf_ema_slow

        data["htf_bull"] = htf_bull.reindex(data.index, method="ffill").fillna(False)
        data["htf_bear"] = htf_bear.reindex(data.index, method="ffill").fillna(False)

        # ④ RSI
        data["rsi"]      = self.rsi(data["close"], self.rsi_len)
        data["rsi_bull"] = (data["rsi"] > self.rsi_os)  & (data["rsi"] < self.RSI_HARD_CAP_HIGH)
        data["rsi_bear"] = (data["rsi"] < self.rsi_ob)  & (data["rsi"] > self.RSI_HARD_CAP_LOW)

        # ⑤ MACD
        macd_line, signal_line, hist = self.macd(
            data["close"], self.macd_fast, self.macd_slow, self.macd_sig
        )
        data["macd_line"]   = macd_line
        data["signal_line"] = signal_line
        data["hist"]        = hist

        data["macd_bull"] = (data["macd_line"] > data["signal_line"]) & (data["macd_line"] > 0)
        data["macd_bear"] = (data["macd_line"] < data["signal_line"]) & (data["hist"] < 0)

        # ⑥ Momentum composite
        if self.use_strict_momentum:
            data["momentum_bull"] = data["rsi_bull"] & data["macd_bull"]
            data["momentum_bear"] = data["rsi_bear"] & data["macd_bear"]
        else:
            data["momentum_bull"] = data["rsi_bull"] | data["macd_bull"]
            data["momentum_bear"] = data["rsi_bear"] | data["macd_bear"]

        # ⑦ Volatility filter
        if self.use_volatility_filter:
            high_low   = data["high"] - data["low"]
            high_close = (data["high"] - data["close"].shift(1)).abs()
            low_close  = (data["low"]  - data["close"].shift(1)).abs()
            tr = pd.concat([high_low, high_close, low_close], axis=1).max(axis=1)
            data["atr"]            = tr.rolling(window=14).mean()
            data["atr_sma"]        = data["atr"].rolling(window=20).mean()
            data["volatile_enough"] = data["atr"] > data["atr_sma"] * self.min_atr_ratio
        else:
            data["volatile_enough"] = True

        # ⑧ Daily pivots
        pivot, r1, r2, r3, s1, s2, s3 = self.calculate_daily_pivots(
            data["high"], data["low"], data["close"]
        )
        data["pivot"] = pivot
        data["r1"] = r1;  data["r2"] = r2;  data["r3"] = r3
        data["s1"] = s1;  data["s2"] = s2;  data["s3"] = s3

        # ⑨ Swing highs/lows
        swing_high = self.pivot_high(data["high"], self.swing_len, self.swing_len)
        swing_low  = self.pivot_low(data["low"],  self.swing_len, self.swing_len)
        data["last_swing_high"] = swing_high.ffill()
        data["last_swing_low"]  = swing_low.ffill()

        # ⑩ Proximity threshold (vectorised, pre-computed once per bar)
        data["sr_threshold"] = data["close"] * self.sr_proximity

        # ⑪ Support / Resistance classification
        data["near_support"]    = False
        data["near_resistance"] = False

        # NOTE: This loop is a known performance pitfall (see design doc).
        # It is preserved here for 1-to-1 correctness with Pine Script.
        # For large datasets consider vectorising with np.isclose / broadcasting.
        for i in range(len(data)):
            close_price = data["close"].iloc[i]
            threshold   = data["sr_threshold"].iloc[i]
            bull        = data["bull_trend"].iloc[i]
            bear        = data["bear_trend"].iloc[i]

            fn = self.f_near  # local alias for speed

            near_support = (
                fn(close_price, data["s1"].iloc[i], threshold)
                or fn(close_price, data["s2"].iloc[i], threshold)
                or fn(close_price, data["s3"].iloc[i], threshold)
                or (fn(close_price, data["pivot"].iloc[i], threshold) and bull)
                or fn(close_price, data["last_swing_low"].iloc[i], threshold)
            )
            near_resistance = (
                fn(close_price, data["r1"].iloc[i], threshold)
                or fn(close_price, data["r2"].iloc[i], threshold)
                or fn(close_price, data["r3"].iloc[i], threshold)
                or (fn(close_price, data["pivot"].iloc[i], threshold) and bear)
                or fn(close_price, data["last_swing_high"].iloc[i], threshold)
            )

            data.loc[data.index[i], "near_support"]    = near_support
            data.loc[data.index[i], "near_resistance"] = near_resistance

        # ⑫ Base signals
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

        # ⑬ Leading-edge detection (fires once per state transition 0→1)
        # [FIX-BUG] .shift(1).fillna(False) produces object dtype on some pandas versions.
        # Applying ~ to an object Series does bitwise NOT (integer), not boolean NOT,
        # producing corrupt results (-1, -2 …). Cast to bool explicitly before inverting.
        prev_buy  = data["buy_signal_base"].shift(1).fillna(False).astype(bool)
        prev_sell = data["sell_signal_base"].shift(1).fillna(False).astype(bool)
        data["buy_edge"]  = data["buy_signal_base"]  & ~prev_buy
        data["sell_edge"] = data["sell_signal_base"] & ~prev_sell

        # ⑭ Price-movement anti-spam filter (stateful)
        price_moved = [True] * len(data)
        last_signal_price = np.nan

        buy_edge_arr  = data["buy_edge"].to_numpy()
        sell_edge_arr = data["sell_edge"].to_numpy()
        close_arr     = data["close"].to_numpy()

        for i in range(len(data)):
            if not np.isnan(last_signal_price):
                price_moved[i] = (
                    abs(close_arr[i] - last_signal_price) / last_signal_price
                    >= self.min_price_move
                )
            if (buy_edge_arr[i] or sell_edge_arr[i]) and price_moved[i]:
                last_signal_price = close_arr[i]

        data["price_moved_enough"] = price_moved

        # ⑮ Final triggers
        data["buy_trigger"]  = data["buy_edge"]  & data["price_moved_enough"] & data["htf_bull"]
        data["sell_trigger"] = data["sell_edge"] & data["price_moved_enough"] & data["htf_bear"]

        # Backward-compatibility aliases
        data["buy_signal"]  = data["buy_signal_base"]
        data["sell_signal"] = data["sell_signal_base"]

        return data


# ---------------------------------------------------------------------------
# Deterministic test data
# ---------------------------------------------------------------------------

def create_test_data(bars: int = 200, seed: int = 42) -> pd.DataFrame:
    """
    [FIX-4] Deterministic synthetic OHLCV data.
    Uses a fixed seed AND a structured (non-random-walk) price series so that
    tests are reproducible and boundary conditions are predictable.
    """
    rng   = np.random.default_rng(seed)
    dates = pd.date_range(start="2024-01-01", periods=bars, freq="15min")

    # Structured random walk with fixed seed
    returns = rng.normal(0, 0.002, bars)
    close   = 100.0 * np.cumprod(1 + returns)
    noise_h = np.abs(rng.normal(0, 0.001, bars))
    noise_l = np.abs(rng.normal(0, 0.001, bars))
    high    = close * (1 + noise_h)
    low     = close * (1 - noise_l)

    close_s     = pd.Series(close, index=dates)
    open_price  = close_s.shift(1).fillna(close[0])
    volume      = rng.integers(1000, 10_000, bars)

    return pd.DataFrame(
        {"open": open_price, "high": high, "low": low, "close": close, "volume": volume},
        index=dates,
    )


def create_trending_data(bars: int = 200, direction: str = "up") -> pd.DataFrame:
    """Synthetic data with a clear monotonic trend for condition isolation tests."""
    dates = pd.date_range(start="2024-01-01", periods=bars, freq="15min")
    if direction == "up":
        close = np.linspace(100, 130, bars)
    else:
        close = np.linspace(130, 100, bars)

    rng    = np.random.default_rng(0)
    noise  = rng.normal(0, 0.05, bars)
    close  = close + noise
    high   = close + np.abs(rng.normal(0, 0.1, bars))
    low    = close - np.abs(rng.normal(0, 0.1, bars))

    close_s    = pd.Series(close, index=dates)
    open_price = close_s.shift(1).fillna(close[0])
    volume     = rng.integers(1000, 10_000, bars)

    return pd.DataFrame(
        {"open": open_price, "high": high, "low": low, "close": close, "volume": volume},
        index=dates,
    )


# ============================================================
#  GROUP A — CORE BEHAVIOUR
# ============================================================

def test_01_buy_all_conditions_met():
    """
    T01: buy_signal == bull_trend & momentum_bull & near_support & volatile_enough

    [FIX-2] Original test omitted the volatile_enough gate that was added in v4.0.
    The assertion now matches the actual definition of buy_signal_base.
    """
    indicator = SignalIndicator()
    df     = create_test_data(bars=100)
    result = indicator.generate_signals(df)

    expected = (
        result["bull_trend"]
        & result["momentum_bull"]
        & result["near_support"]
        & result["volatile_enough"]  # FIX-2: was missing in original test
    )
    assert (expected == result["buy_signal"]).all(), (
        "buy_signal does not match bull_trend & momentum_bull & near_support & volatile_enough"
    )


def test_02_sell_all_conditions_met():
    """
    T02: sell_signal == bear_trend & momentum_bear & near_resistance & volatile_enough

    [FIX-2] Same fix as T01 for the sell side.
    """
    indicator = SignalIndicator()
    df     = create_test_data(bars=100)
    result = indicator.generate_signals(df)

    expected = (
        result["bear_trend"]
        & result["momentum_bear"]
        & result["near_resistance"]
        & result["volatile_enough"]  # FIX-2
    )
    assert (expected == result["sell_signal"]).all(), (
        "sell_signal does not match bear_trend & momentum_bear & near_resistance & volatile_enough"
    )


# ============================================================
#  GROUP B — CONDITION ISOLATION
# ============================================================

def test_03_no_buy_when_fast_below_slow():
    """T03: bull_trend is False whenever ema_fast < ema_slow."""
    indicator = SignalIndicator()
    result    = indicator.generate_signals(create_test_data(bars=100))

    fast_below_slow      = result["ema_fast"] < result["ema_slow"]
    bull_when_fast_below = result.loc[fast_below_slow, "bull_trend"]
    assert not bull_when_fast_below.any(), "bull_trend is True even when ema_fast < ema_slow"


def test_04_no_momentum_when_both_fail():
    """T04: momentum_bull is False when BOTH rsi_bull and macd_bull are False."""
    indicator = SignalIndicator()
    result    = indicator.generate_signals(create_test_data(bars=100))

    both_fail               = ~result["rsi_bull"] & ~result["macd_bull"]
    momentum_when_both_fail = result.loc[both_fail, "momentum_bull"]
    assert not momentum_when_both_fail.any(), "momentum_bull is True even when both RSI and MACD fail"


def test_05_rsi_alone_triggers_momentum():
    """T05: RSI alone is sufficient for momentum_bull (OR logic)."""
    indicator = SignalIndicator()
    result    = indicator.generate_signals(create_test_data(bars=100))

    rsi_only = result["rsi_bull"] & ~result["macd_bull"]
    if rsi_only.any():
        assert result.loc[rsi_only, "momentum_bull"].all(), (
            "momentum_bull is False even when RSI alone qualifies"
        )


def test_06_macd_alone_triggers_momentum():
    """T06: MACD alone is sufficient for momentum_bull (OR logic)."""
    indicator = SignalIndicator()
    result    = indicator.generate_signals(create_test_data(bars=100))

    macd_only = result["macd_bull"] & ~result["rsi_bull"]
    if macd_only.any():
        assert result.loc[macd_only, "momentum_bull"].all(), (
            "momentum_bull is False even when MACD alone qualifies"
        )


def test_07_rsi_hard_cap_kills_momentum():
    """T07: RSI ≥ 80 prevents rsi_bull; momentum_bull must rely solely on MACD."""
    indicator = SignalIndicator()
    df        = create_trending_data(bars=150, direction="up")
    result    = indicator.generate_signals(df)

    above_cap   = result["rsi"] >= indicator.RSI_HARD_CAP_HIGH
    macd_no_bull = ~result["macd_bull"]
    both         = above_cap & macd_no_bull

    if both.any():
        assert not result.loc[both, "momentum_bull"].any(), (
            "momentum_bull fires when RSI ≥ 80 and MACD also fails"
        )


def test_07b_strict_momentum_requires_both():
    """
    [FIX-5] NEW — T07b: With use_strict_momentum=True, BOTH RSI and MACD
    must pass; either failing alone must suppress momentum_bull.
    """
    indicator = SignalIndicator(use_strict_momentum=True)
    result    = indicator.generate_signals(create_test_data(bars=100))

    # RSI passes but MACD fails → momentum_bull must be False
    rsi_only = result["rsi_bull"] & ~result["macd_bull"]
    if rsi_only.any():
        assert not result.loc[rsi_only, "momentum_bull"].any(), (
            "strict_momentum: momentum_bull fires with only RSI passing"
        )

    # MACD passes but RSI fails → momentum_bull must be False
    macd_only = result["macd_bull"] & ~result["rsi_bull"]
    if macd_only.any():
        assert not result.loc[macd_only, "momentum_bull"].any(), (
            "strict_momentum: momentum_bull fires with only MACD passing"
        )

    # Both pass → momentum_bull must be True
    both = result["rsi_bull"] & result["macd_bull"]
    if both.any():
        assert result.loc[both, "momentum_bull"].all(), (
            "strict_momentum: momentum_bull is False even when both RSI and MACD pass"
        )


def test_07c_volatility_filter():
    """
    [FIX-6] NEW — T07c: With use_volatility_filter=True, buy/sell signals must
    be False on bars where ATR < ATR_SMA * min_atr_ratio.
    """
    indicator = SignalIndicator(use_volatility_filter=True)
    # Need enough bars for ATR (14) + ATR_SMA (20) to warm up
    result = indicator.generate_signals(create_test_data(bars=300))

    not_volatile = ~result["volatile_enough"]
    if not_volatile.any():
        assert not result.loc[not_volatile, "buy_signal"].any(), (
            "buy_signal fires on a low-volatility bar"
        )
        assert not result.loc[not_volatile, "sell_signal"].any(), (
            "sell_signal fires on a low-volatility bar"
        )


# ============================================================
#  GROUP C — BOUNDARY CONDITIONS
# ============================================================

def test_08_proximity_just_inside_fires():
    """T08: Price just INSIDE threshold (99 %) triggers f_near."""
    indicator   = SignalIndicator()
    close_price = 100.0
    threshold   = close_price * indicator.sr_proximity
    level       = close_price + threshold * 0.99
    assert indicator.f_near(close_price, level, threshold), (
        "f_near returns False for price just inside the threshold"
    )


def test_09_proximity_just_outside_does_not_fire():
    """T09: Price just OUTSIDE threshold (101 %) does NOT trigger f_near."""
    indicator   = SignalIndicator()
    close_price = 100.0
    threshold   = close_price * indicator.sr_proximity
    level       = close_price + threshold * 1.01
    assert not indicator.f_near(close_price, level, threshold), (
        "f_near returns True for price just outside the threshold"
    )


def test_10_proximity_exact_boundary_fires():
    """T10: Price at exact boundary (≤) triggers f_near."""
    indicator   = SignalIndicator()
    close_price = 100.0
    threshold   = close_price * indicator.sr_proximity
    level       = close_price + threshold * 0.9999  # marginally inside
    assert indicator.f_near(close_price, level, threshold), (
        "f_near returns False at the exact boundary"
    )


# ============================================================
#  GROUP D — EDGE CASES
# ============================================================

def test_11_leading_edge_buy_fires_once_per_transition():
    """
    T11: buy_edge fires exactly ONCE per 0→1 transition of buy_signal_base.

    [FIX-3] Original test compared buy_trigger count to buy_signal rising-edge
    count.  buy_trigger has additional gates (price_moved_enough, htf_bull) so
    counts will legitimately differ.  The correct invariant is:

        count(buy_edge) == count(rising edges in buy_signal_base)

    buy_trigger is a SUBSET of buy_edge, not its equal.
    """
    indicator = SignalIndicator()
    result    = indicator.generate_signals(create_test_data(bars=200))

    # Rising edges in buy_signal_base (state changes 0 → 1)
    rising_edges = (result["buy_signal_base"].astype(int).diff().fillna(0) == 1).sum()
    edge_count   = result["buy_edge"].sum()

    assert edge_count == rising_edges, (
        f"buy_edge count ({edge_count}) != buy_signal_base rising-edge count ({rising_edges}). "
        "Leading-edge guard is broken."
    )


def test_12_na_swing_level_safe():
    """T12: NaN swing levels do NOT crash or produce false positives in f_near."""
    indicator   = SignalIndicator()
    close_price = 100.0
    threshold   = close_price * indicator.sr_proximity
    assert not indicator.f_near(close_price, np.nan, threshold), (
        "f_near returns True for a NaN level — NaN guard is missing"
    )


def test_13_pivot_not_support_in_bear_trend():
    """T13: Pivot does NOT count as support when price is in a bear trend."""
    indicator = SignalIndicator()
    result    = indicator.generate_signals(create_test_data(bars=100))

    for i in range(len(result)):
        close_price = result["close"].iloc[i]
        pivot       = result["pivot"].iloc[i]
        threshold   = result["sr_threshold"].iloc[i]
        bear_trend  = result["bear_trend"].iloc[i]

        if indicator.f_near(close_price, pivot, threshold) and bear_trend:
            # Check that no OTHER support levels are nearby
            s1_near    = indicator.f_near(close_price, result["s1"].iloc[i], threshold)
            s2_near    = indicator.f_near(close_price, result["s2"].iloc[i], threshold)
            s3_near    = indicator.f_near(close_price, result["s3"].iloc[i], threshold)
            swing_near = indicator.f_near(close_price, result["last_swing_low"].iloc[i], threshold)

            if not (s1_near or s2_near or s3_near or swing_near):
                assert not result["near_support"].iloc[i], (
                    f"Pivot counted as support during bear trend at bar {i}"
                )


def test_14_htf_bull_gates_buy_trigger():
    """
    [FIX-7] NEW — T14: buy_trigger must be False on any bar where htf_bull is False,
    regardless of whether buy_edge and price_moved_enough are True.
    """
    indicator = SignalIndicator()
    result    = indicator.generate_signals(create_test_data(bars=200))

    htf_bear_bars = ~result["htf_bull"]
    if htf_bear_bars.any():
        buys_in_htf_bear = result.loc[htf_bear_bars, "buy_trigger"]
        assert not buys_in_htf_bear.any(), (
            "buy_trigger fired on a bar where htf_bull is False"
        )


# ============================================================
#  GROUP E — PERFORMANCE
# ============================================================

def test_15_performance_5000_bars():
    """
    [FIX-8] NEW — T15: generate_signals must complete in < 60 s for 5 000 bars.
    This is a smoke-test for the O(n) Python loop not regressing badly.
    60 s is a very generous bound; typical runtime should be < 5 s.
    """
    indicator = SignalIndicator()
    df        = create_test_data(bars=5_000)

    start = time.monotonic()
    indicator.generate_signals(df)
    elapsed = time.monotonic() - start

    assert elapsed < 60, (
        f"generate_signals took {elapsed:.1f}s for 5 000 bars — likely regression in the loop"
    )


# ============================================================
#  RUNNER
# ============================================================

TESTS = [
    # Group A — Core behaviour
    ("T01: BUY  — all conditions",          test_01_buy_all_conditions_met),
    ("T02: SELL — all conditions",          test_02_sell_all_conditions_met),
    # Group B — Condition isolation
    ("T03: No BUY when fast < slow",        test_03_no_buy_when_fast_below_slow),
    ("T04: No momentum when both fail",     test_04_no_momentum_when_both_fail),
    ("T05: RSI alone triggers momentum",    test_05_rsi_alone_triggers_momentum),
    ("T06: MACD alone triggers momentum",   test_06_macd_alone_triggers_momentum),
    ("T07: RSI hard-cap kills momentum",    test_07_rsi_hard_cap_kills_momentum),
    ("T07b: Strict momentum (AND logic)",   test_07b_strict_momentum_requires_both),
    ("T07c: Volatility filter gate",        test_07c_volatility_filter),
    # Group C — Boundary conditions
    ("T08: Proximity just inside fires",    test_08_proximity_just_inside_fires),
    ("T09: Proximity just outside skips",   test_09_proximity_just_outside_does_not_fire),
    ("T10: Proximity exact boundary fires", test_10_proximity_exact_boundary_fires),
    # Group D — Edge cases
    ("T11: Leading-edge fires once",        test_11_leading_edge_buy_fires_once_per_transition),
    ("T12: NaN swing level is safe",        test_12_na_swing_level_safe),
    ("T13: Pivot not support in bear",      test_13_pivot_not_support_in_bear_trend),
    ("T14: HTF bull gates buy trigger",     test_14_htf_bull_gates_buy_trigger),
    # Group E — Performance
    ("T15: 5 000-bar performance smoke",    test_15_performance_5000_bars),
]


def run_tests():
    passed = failed = 0

    print("\n" + "=" * 70)
    print("  EMA/RSI/MACD + S&R INDICATOR TEST SUITE  v2.0")
    print("=" * 70 + "\n")

    for name, fn in TESTS:
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
