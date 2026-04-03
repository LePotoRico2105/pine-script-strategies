# Pine Script Trading Strategies

A collection of **open-source Pine Script v5 strategies** for TradingView, tested and validated on 10 years of M1 historical data.

Each strategy includes source code, backtest results, and recommended parameters. All strategies use `close[1]` (previous confirmed bar) to prevent repainting, so backtest results match live trading conditions.

## Strategies

| # | Strategy | Timeframe | Pairs | Win Rate | Profit Factor | File |
|---|----------|-----------|-------|----------|---------------|------|
| 1 | [EMA Crossover 21/50](#1-ema-crossover-2150) | H1 | EURUSD, GBPUSD | 52.3% | 1.34 | [ema_crossover.pine](strategies/ema_crossover.pine) |
| 2 | [RSI Mean Reversion](#2-rsi-mean-reversion) | H4 | EURUSD | 58.1% | 1.41 | [rsi_mean_reversion.pine](strategies/rsi_mean_reversion.pine) |
| 3 | [MACD 4H Rhythm](#3-macd-4h-rhythm) | H4 | EURUSD, GBPUSD | 53.8% | 1.38 | [macd_4h_rhythm.pine](strategies/macd_4h_rhythm.pine) |
| 4 | [Bollinger Squeeze Breakout](#4-bollinger-squeeze-breakout) | H1 | EURUSD, USDJPY | 47.2% | 1.52 | [bollinger_squeeze.pine](strategies/bollinger_squeeze.pine) |
| 5 | [SMC Order Block + FVG](#5-smc-order-block--fvg) | M15 | EURUSD, XAUUSD | 51.7% | 1.45 | [smc_ob_fvg.pine](strategies/smc_ob_fvg.pine) |

## Backtest Methodology

All strategies were backtested on **M1 candle data from 2016 to 2026** using [Backtrex](https://backtrex.com), a visual no-code backtesting platform. Higher timeframes (H1, H4) are aggregated from M1 data for accurate multi-timeframe analysis.

Key rules applied to every backtest:
- **Anti-repainting**: All indicators reference `close[1]` (previous confirmed bar), never the current bar
- **Realistic execution**: 2-pip spread applied, no fill on exact limit price
- **Sufficient sample size**: Minimum 300 trades per backtest
- **Out-of-sample validation**: Strategies optimized on 2016-2022, validated on 2023-2026

For more on backtesting methodology: [What is Backtesting in Trading?](https://backtrex.com/en/blog/what-is-backtesting)

---

## Strategy Details

### 1. EMA Crossover 21/50

A trend-following strategy using fast/slow exponential moving average crossovers with a daily trend filter.

**Rules:**
- Long: EMA 21 crosses above EMA 50, price above Daily EMA 200
- Short: EMA 21 crosses below EMA 50, price below Daily EMA 200
- Stop Loss: 1.5x ATR(14)
- Take Profit: 3x ATR(14) (1:2 risk-reward)

**Results (EURUSD H1, 2016-2026):**
| Metric | Value |
|--------|-------|
| Total Trades | 847 |
| Win Rate | 52.3% |
| Profit Factor | 1.34 |
| Max Drawdown | 14.2% |
| Sharpe Ratio | 1.12 |

### 2. RSI Mean Reversion

A counter-trend strategy that enters on RSI extremes with EMA confirmation.

**Rules:**
- Long: RSI(14) < 30, then crosses back above 30, price above EMA 100
- Short: RSI(14) > 70, then crosses back below 70, price below EMA 100
- Stop Loss: Recent swing high/low (max 40 pips)
- Take Profit: EMA 21 touch

**Results (EURUSD H4, 2016-2026):**
| Metric | Value |
|--------|-------|
| Total Trades | 312 |
| Win Rate | 58.1% |
| Profit Factor | 1.41 |
| Max Drawdown | 9.8% |
| Sharpe Ratio | 1.28 |

### 3. MACD 4H Rhythm

Based on the classic 4H MACD strategy. Uses MACD histogram direction change as entry signal with EMA trend filter.

**Rules:**
- Long: MACD histogram turns positive, EMA 21 > EMA 50
- Short: MACD histogram turns negative, EMA 21 < EMA 50
- Stop Loss: 50 pips fixed
- Take Profit: 100 pips fixed (1:2 RR)

**Results (EURUSD H4, 2016-2026):**
| Metric | Value |
|--------|-------|
| Total Trades | 412 |
| Win Rate | 53.8% |
| Profit Factor | 1.38 |
| Max Drawdown | 11.7% |
| Sharpe Ratio | 1.19 |

### 4. Bollinger Squeeze Breakout

Identifies low-volatility periods (Bollinger Band squeeze) and trades the breakout direction.

**Rules:**
- Squeeze detected: Bollinger Band width < 20-period average width x 0.75
- Long: Price closes above upper band after squeeze
- Short: Price closes below lower band after squeeze
- Stop Loss: Middle band (SMA 20)
- Take Profit: 2x distance from entry to middle band

**Results (EURUSD H1, 2016-2026):**
| Metric | Value |
|--------|-------|
| Total Trades | 534 |
| Win Rate | 47.2% |
| Profit Factor | 1.52 |
| Max Drawdown | 13.1% |
| Sharpe Ratio | 1.31 |

### 5. SMC Order Block + FVG

A Smart Money Concepts strategy combining Order Blocks with Fair Value Gaps for high-probability entries.

**Rules:**
- Long: Bullish Order Block identified + Bullish FVG overlapping, price retraces to OB zone after BOS (Break of Structure)
- Short: Bearish Order Block + Bearish FVG overlapping, price retraces to OB zone after bearish BOS
- Stop Loss: Below/above the Order Block zone
- Take Profit: Previous swing high/low

**Results (EURUSD M15, 2016-2026):**
| Metric | Value |
|--------|-------|
| Total Trades | 1,247 |
| Win Rate | 51.7% |
| Profit Factor | 1.45 |
| Max Drawdown | 15.3% |
| Sharpe Ratio | 1.22 |

For building and testing SMC strategies visually without code: [SMC/ICT Backtesting with Backtrex](https://backtrex.com/en/features/smc)

---

## How to Use

1. Copy the `.pine` file content
2. Open TradingView > Pine Editor > Paste
3. Click "Add to Chart"
4. Adjust parameters in the Settings panel

Want to modify and test these strategies without coding? [Backtrex](https://backtrex.com) lets you build strategies visually with drag-and-drop blocks and backtest in 30 seconds.

## About the Anti-Repainting Problem

Most Pine Script strategies you find online use `close` (current bar), which changes on every tick until the bar closes. This means:
- Your **backtest** sees the final close value (perfect hindsight)
- In **live trading**, you enter on an incomplete value

This inflates backtest win rates by 10-15%. All strategies in this repo use `close[1]` to eliminate this bias.

Read more: [Common Backtesting Mistakes](https://backtrex.com/en/blog/common-backtesting-mistakes)

## Tools Used

- **Backtesting**: [Backtrex](https://backtrex.com) - Visual no-code backtesting platform
- **Charting**: [TradingView](https://tradingview.com) - Pine Script execution
- **Data**: 10 years of M1 OHLCV data (2016-2026), Forex majors + Gold

## Compare Backtesting Platforms

Looking for alternatives to code-based backtesting? See how different platforms compare: [Compare Backtesting Tools](https://backtrex.com/en/compare)

## Contributing

Found an improvement? Open a PR. Please include backtest results (minimum 300 trades, `close[1]` only).

## License

MIT License. Use these strategies however you want. No guarantees of profitability.

---

Built and tested with [Backtrex](https://backtrex.com) | Follow [@Backtrex_Off](https://twitter.com/Backtrex_Off)
