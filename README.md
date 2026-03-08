# Easy Ichimoku v14

**Last updated:** March 2026

**€£$¥ Ichimoku v14** — Production-style TradingView Pine Script (v5) implementing a preset-optimized Ichimoku Cloud trend-following system with layered filters, structured exits, and webhook-ready JSON alerts for automated execution.

Part of the **Easy Trading Software** suite.

This repository demonstrates:

- Structured forward validation methodology
- Window-normalized compounding analysis
- Portfolio partition modeling
- Production-ready alert architecture

---

# Why This Project Stands Out

- Preset-optimized per symbol (BTC, ETH, SOL, XRP — 1H & 5m)
- Multi-layer entry validation (TK cross + cloud + Chikou + ADX + RSI)
- Structured exit stack (ATR stop, TP, breakeven, 6H BE band, trailing, RSI exit)
- Time guardrails (weekend, evening, 8–9 AM PT volatility filter)
- 100+ enriched JSON fields for analytics + automation
- Designed for real webhook → execution agent deployment

This is a full production strategy — not a snippet.

---

# Reproducibility (Strategy Tester Settings)

All performance shown uses:

- Venue: Coinbase USA Perp Nano Futures
- Timeframe: 1H preset configurations (1H results); 5min for ETH ETP 5min preset
- Commission: 0.04%
- Position size: 80%
- Execution: `calc_on_every_tick=true`

Intrabar evaluation materially impacts exit precision. Results differ under bar-close evaluation.

---

# Evidence — Live Forward Test Results

Forward test windows captured directly from TradingView Strategy Tester.

## 1H Preset Results

_Record date: March 7, 2026. Source: TradingView Strategy Tester backtest screenshots._

| Preset | 30 Day Est | 1 Year Est | Max DD | Win Rate | Trades | Profit Factor | Window | Total P&L |
|--------|------------|------------|--------|----------|--------|---------------|--------|-----------|
| BTC (BIP) | ~3.78% | ~54.7% | 4.25% | 66.67% | 78 | 2.148 | Jul 18 2025 – Mar 7 2026 | +34.47% |
| ETH (ETP) | ~7.65% | ~125% | 14.26% | 78.38% | 74 | 3.082 | Jul 18 2025 – Mar 7 2026 | +78.10% |
| SOL (SLP) | ~9.55% | ~158% | 8.64% | 71.95% | 82 | 2.413 | Aug 15 2025 – Mar 7 2026 | +89.10% |
| XRP (XPP) | ~4.04% | ~56.1% | 8.50% | 72.84% | 81 | 1.787 | Aug 15 2025 – Mar 7 2026 | +31.38% |

## 5min ETP ETH Preset Results

| Preset | 30 Day Est | 1 Year Est | Max DD | Win Rate | Trades | Profit Factor | Window | Total P&L |
|--------|------------|------------|--------|----------|--------|---------------|--------|-----------|
| **ETH (ETP) 5min** | ~7.65% | ~103% | 6.77% | 50% | — | 1.93 | Jul 18 2025 – Mar 7 2026 | **+76.33%** |

_5min ETP preset: T17 K12 Senkou B9 Disp8; ADX 14/13; RSI L13; RSI exit 43/55; BE 1.5/1.2%; 6H BE ON 0.1/0.1%; Trail 1.5/1%. Record date March 7, 2026. See Historical Presets (below) and Guide Section 13 for full settings._

---

# Forward Test Screenshots

All screenshots are unedited Strategy Tester exports using the settings listed above.

### 1H Presets

#### BTC — 1H Preset
![BTC 1H Forward Test](screenshots/BTC.jpg)

#### ETH — 1H Preset
![ETH 1H Forward Test](screenshots/ETH.jpg)

#### SOL — 1H Preset
![SOL 1H Forward Test](screenshots/SOL.jpg)

#### XRP — 1H Preset
![XRP 1H Forward Test](screenshots/XRP.jpg)

### 5min Preset

#### ETH — 5min Preset
![ETH 5min Forward Test](screenshots/ETH5min.png)

---

# Window-Normalized Compounding (Derived)

To normalize uneven forward windows:

CAGR = (1 + R)^(365 / days) − 1

**1H presets**

| Preset | Days | Compounded Monthly | Annualized CAGR |
|--------|------|-------------------|-----------------|
| BTC | 232 | ~3.78% | ~54.7% |
| ETH | 232 | ~7.65% | ~125% |
| SOL | 205 | ~9.55% | ~158% |
| XRP | 205 | ~4.04% | ~56.1% |

**5min ETP ETH preset**

| Preset | Days | Compounded Monthly | Annualized CAGR |
|--------|------|-------------------|-----------------|
| ETH (ETP) 5min | 232 | ~7.65% | ~103% |

Annualized CAGR is mathematical normalization, not a guarantee of persistence.

---

# 4-Symbol Portfolio Normalization

Derived monthly rates (from 1H preset backtests, record date Mar 7, 2026):

- BTC ≈ 3.78%
- ETH ≈ 7.65%
- SOL ≈ 9.55%
- XRP ≈ 4.04%

Summed normalization (theoretical upper bound): ≈ 25.0% per month

---

# Capital Deployment & Execution Model

The TradingView strategy does not execute trades directly.

When a signal triggers:

TradingView Strategy  
→ emits structured JSON webhook  
→ Easy TradingView Agent receives signal  
→ Agent validates capital availability  
→ Agent executes via broker API (Coinbase Perp Nano Futures)

All capital logic is enforced at the Agent layer — not in TradingView.

---

## Partition Architecture (Agent-Enforced)

The Easy TradingView Agent operates under a controlled capital framework:

- 3 capital partitions
- 1/3 account equity allocated per position
- 3× leverage per trade
- Maximum 3 concurrent positions
- Effective exposure ≈ 1× per active trade (leverage offset by sizing)

This ensures:

- No single trade consumes full account risk
- Capital exposure is bounded
- Concurrency is capped
- Exposure scales predictably
- Capital is dynamically recycled as positions close

---

## Concurrency & Signal Handling

Signals across BTC, ETH, SOL, and XRP may overlap.

When multiple alerts arrive:

- The Agent assigns the next available partition
- If all 3 partitions are active, additional signals are ignored
- No pyramiding beyond partition limits
- No capital stacking during correlation spikes

This prevents uncontrolled leverage expansion during volatility clusters.

---

## Risk & Correlation Management

Because crypto majors often move together:

- Simultaneous entries may increase portfolio drawdown
- Correlation clustering can compress performance during regime shifts
- Realized blended returns vary based on signal overlap

The ~25% blended monthly normalization (from current 1H backtests) is a theoretical upper bound derived from independent symbol compounding.

Live results depend on:

- Signal timing overlap
- Regime persistence
- Funding costs
- Slippage
- Exchange liquidity
- Agent partition availability

---

## Why This Matters

Many public strategy repos show per-symbol backtests without accounting for:

- Capital reuse
- Concurrency limits
- Correlation risk
- Real execution constraints

This deployment model explicitly accounts for:

- Capital partitioning
- Position overlap
- Leverage normalization
- Portfolio-level exposure control
- Real broker execution

The Easy TradingView Agent enforces these constraints automatically at execution time.
---

# Compounding Illustration (Model Only)

If ~25% monthly (1H blended upper bound) were sustained:

Final = Initial × (1.25)^n

Starting capital: $2,000

| Month | Balance |
|--------|---------|
| 0 | $2,000 |
| 1 | $2,500 |
| 2 | $3,125 |
| 3 | $3,906 |
| 4 | $4,883 |
| 5 | $6,104 |
| 6 | $7,629 |
| 7 | $9,537 |
| 8 | $11,921 |
| 9 | $14,901 |
| 10 | $18,626 |
| 11 | $23,283 |
| 12 | $29,104 |

This demonstrates compounding mechanics only. Realized performance depends on regime, execution, funding, slippage, and concurrency.

---

# Strategy Architecture

TradingView Strategy  
↓ webhook JSON  
Easy TradingView Agent  
↓ execution engine  
Broker API (Coinbase Perp Nano Futures)  
↓  
3-Partition Capital Model  

Alerts include enriched JSON payloads (100+ fields).

---

# What’s Included

- easy_ichimoku_v14.pine
- Presets for BTC, ETH, SOL, XRP (5m + 1H)
- Full layered exit logic
- Historical Enhancer JSON payloads

Requirements:
- TradingView (Pine v5)
- Optional webhook execution agent

---

# Key Features

- Ichimoku cloud structure validation
- ADX trend strength + optional acceleration
- RSI entry + RSI fade exit
- ATR or fixed % stop
- Take profit targets
- Breakeven + 6H BE band (on by default for 1H presets)
- Trailing stop
- Weekend + evening block
- 8–9 AM PT volatility guard
- Intrabar exit evaluation

---

# Risk Considerations

- Trend-following systems underperform in chop
- Funding + slippage impact net results
- Correlation spikes increase DD
- Presets require periodic validation
- Forward testing continues

---

# Historical Presets

Preset settings and backtest results are recorded in **EASY_ICHIMOKU_V14_GUIDE.md** (Section 13) so we can track performance and retain prior configurations when presets are updated.

**5Min ETP / ETH PERP (Coinbase USA) — Record date March 7, 2026**

| Preset | 30 Day Est | 1 Year Est | Max DD | Win Rate | Trades | Profit Factor | Window | Total P&L | Settings (summary) |
|--------|------------|------------|--------|----------|--------|---------------|--------|-----------|--------------------|
| **Current** | ~7.65% | ~103% | 6.77% | 50% | — | 1.93 | Jul 18 2025 – Mar 7 2026 | **+76.33%** | T17 K12 SB9 D8; ADX 14/13; RSI L13; exit 43/55; BE 1.5/1.2%; 6H BE ON; Trail 1.5/1% |
| **Previous** (archived Mar 7, 2026) | ~7.1% | ~92% | 12.4% | 50% | — | 1.5 | Jul 18 2025 – Mar 7 2026 | +69.33% | T16 K12 SB10 D10; ADX 13/14; RSI L9; exit 25/65; BE 1.5/0.5%; 6H BE OFF; Trail 1.5/1.3% |

_30-day / 1-year: (1 + total_return)^(30/232)−1 and (1 + total_return)^(365/232)−1. Backtest = 232 days._  
**Full settings and results:** **EASY_ICHIMOKU_V14_GUIDE.md** Section 13 (current and previous presets with complete parameter lists and when results were recorded).

---

# Disclaimer

Educational and research purposes only.  
Not financial advice.  
Derivatives trading involves substantial risk of loss.  
Past results do not guarantee future performance.
