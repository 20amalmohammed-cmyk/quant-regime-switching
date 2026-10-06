# Multi-Asset Regime-Switching Volatility Strategy

A quantitative trading strategy that dynamically allocates between
SPY, GLD, TLT, and Cash based on detected market volatility regimes.

## Overview

The strategy uses a **four-layer pipeline**:

1. **PCA Walk-Forward** — extracts a "market factor" from SPY/GLD/TLT returns.
2. **Kalman Filter** — smooths the factor to reduce daily noise.
3. **GARCH(1,1)** — estimates conditional volatility of the market factor.
4. **Regime Detection** — combines GARCH and 20-day realized volatility
   with fixed thresholds (20% enter, 15% exit) and hysteresis.

The detected regime drives a **defensive allocation**:

- **Calm** (low vol): 70% SPY, 15% GLD, 15% TLT, 0% Cash.
- **Turbulent** (high vol): 10% SPY, 30% GLD, 20% TLT, 40% Cash.

## Methodology

### Data

- **Assets**: SPY (US equities), GLD (gold), TLT (long-term treasuries).
- **Period**: 2010-01-01 to 2024-12-31.
- **In-Sample**: 2013-2018 (after warm-up).
- **Out-of-Sample**: 2019-2024.

### Key Design Choices

- **No look-ahead bias**: walk-forward PCA, forward-only Kalman filter.
- **Frozen parameters in OOS**: no refitting outside the IS period.
- **Fixed volatility thresholds**: 20% (enter turbulent), 15% (exit turbulent).
- **Cash as defensive asset**: addresses liquidity crises (COVID 2020, 2022 rate hike).
- **max(GARCH, realized)**: faster regime detection at crisis onset.

## Results

### Out-of-Sample (2019-2024)

| Metric | Strategy | Benchmark (EW) | Difference |
|---|---:|---:|---:|
| Cumulative Return | 67.3% | 53.5% | +13.8% |
| Annualized Return | 9.08% | 7.52% | +1.56% |
| Annualized Vol | 8.72% | 10.71% | -1.99% |
| Sharpe Ratio | 1.04 | 0.70 | +0.34 |
| Sortino Ratio | 1.43 | 0.95 | +0.48 |
| Max Drawdown | -15.63% | -23.99% | +8.36% |
| Calmar Ratio | 0.58 | 0.31 | +0.27 |

### Sub-Period Highlights

The strategy outperforms in **4 out of 5 sub-periods**, including:

- **COVID Crash**: DD improved by ~4% vs benchmark.
- **2022 Bear Market**: outperformance +6.8%, DD improvement +7.7%.
- **Post-2022 Recovery**: outperformance +5.3%.

The only underperformance occurred during the Pre-COVID risk-on rally,
an acceptable trade-off for downside protection.

## Installation

```bash
git clone https://github.com/20amalmohammed-cmyk/quant-regime-switching.git
cd quant-regime-switching
pip install -r requirements.txt