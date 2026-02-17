
## Python indicatr test suite with 13 test cases.

**Install dependencies**
`pip install pytest pandas numpy --break-system-packages`

**Run the tests**
`pytest test_indicator.py -v`

```
"""
Test Suite for EMA/RSI/MACD + S&R Signal Indicator
Version: 1.0
Purpose: Verify signal logic correctness with automated tests

REQUIREMENTS:
    pip install pytest pandas numpy ta-lib --break-system-packages

USAGE:
    pytest test_indicator.py -v

TEST COVERAGE (13 behaviors):
    Group A — Core behavior         (test_01, test_02)
    Group B — Condition isolation   (test_03, test_04, test_05, test_06, test_07)
    Group C — Boundary conditions   (test_08, test_09, test_10)
    Group D — Edge cases            (test_11, test_12, test_13)
"""

import pytest
import pandas as pd
import numpy as np


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
        data['buy_trigger'] = data['buy_signal'] & ~data['buy_signal'].shift(1).fillna(False)
        data['sell_trigger'] = data['sell_signal'] & ~data['sell_signal'].shift(1).fillna(False)
        
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
    open_price = close.shift(1).fillna(close[0])
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
    both_fail = ~result['rsi_bull'] & ~result['macd_bull']
    momentum_when_both_fail = result.loc[both_fail, 'momentum_bull']
    
    assert not momentum_when_both_fail.any(), \
        "momentum_bull is true even when both RSI and MACD fail"


def test_05_rsi_alone_triggers_momentum():
    """T05: RSI ALONE is enough for momentum_bull (OR logic)"""
    indicator = SignalIndicator()
    df = create_test_data(bars=100)
    result = indicator.generate_signals(df)
    
    # When RSI passes but MACD fails, momentum_bull should still be true
    rsi_passes_macd_fails = result['rsi_bull'] & ~result['macd_bull']
    momentum_in_these_cases = result.loc[rsi_passes_macd_fails, 'momentum_bull']
    
    assert momentum_in_these_cases.all(), \
        "momentum_bull is false even when RSI alone qualifies"


def test_06_macd_alone_triggers_momentum():
    """T06: MACD ALONE is enough for momentum_bull (OR logic)"""
    indicator = SignalIndicator()
    df = create_test_data(bars=100)
    result = indicator.generate_signals(df)
    
    # When MACD passes but RSI fails, momentum_bull should still be true
    macd_passes_rsi_fails = result['macd_bull'] & ~result['rsi_bull']
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
    macd_not_bull = ~result['macd_bull']
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
    level = close_price + threshold  # Exactly at the boundary
    
    result = indicator.f_near(close_price, level, threshold)
    assert result, "f_near fails at exact boundary — check <= vs <"


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
#  RUN ALL TESTS
# ============================================================

if __name__ == "__main__":
    pytest.main([__file__, "-v", "--tb=short"])

```
