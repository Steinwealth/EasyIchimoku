# Easy Ichimoku v14

**€£$¥ Ichimoku v14** — Production-style TradingView Pine Script (v5) for Ichimoku Cloud trend-following with preset-optimized parameters, multi-layer filters, and webhook-ready alerts. Use with your own broker and the Easy TradingView Agent to process TradingView alerts into executed decisions for your crypto broker.

> **Positioning:** Part of the "Easy Trading Software" suite. Validation is based on structured historical backtests and ongoing live forward tests using the 1H preset configurations. Designed for reliability and automation: preset defaults per symbol/timeframe, time filters, breakeven and trailing logic, and rich JSON alert payloads for downstream execution and analytics. 1H & 5m presets for Coinbase USA Perp Nano Futures BTC, ETH, SOL, & XRP are included.

---

### Why this project stands out

- **Preset-optimized per symbol** — BTC, ETH, SOL, XRP (1h/5m) and other pairs have dedicated presets (Ichimoku periods, ADX, RSI, risk, breakeven, trailing) tuned from backtests; one-click "Use Preset Defaults."
- **Multi-layer entry and exit logic** — TK cross + cloud + Chikou + cloud color + ADX + RSI; exits via stop/target, breakeven, 6H breakeven, trailing stop, and RSI-based early exit.
- **Time filters and guardrails** — Weekend block, evening block, news-volatility filter (8–9 AM PT); configurable so you avoid low-quality sessions.
- **Webhook-ready and analytics-ready** — Alerts emit full JSON (strategy_id, side, price, symbol, timeframe, and 100+ `historical_enhancer` fields) for execution agents and ML/backtest pipelines.

*This is a complete strategy script — not a snippet.*

---

### New here?

| Goal | Where to go |
|------|-------------|
| **Load and run** | [Quick start](#quick-start) |
| **See proof** | [Evidence (historical backtest)](#evidence-historical-backtest-snapshot) · [Evidence (forward test)](#evidence-forward-test-results--live) |
| **Understand the strategy** | [What is Ichimoku?](#what-is-ichimoku) · [What’s in v14](#whats-in-v14) |
| **Presets and features** | [Presets](#presets) · [Main features](#main-features-v14) |
| **Risk and filters** | [Risk and guardrails](#risk-and-guardrails) |
| **Alerts and automation** | [Strategy ID and alerts](#strategy-id-and-alerts) · [Example alert payload](#example-alert-payload) |

---

## Reproducibility (Strategy Tester settings used)

Settings used for the evidence tables in this README:

- **Venue:** Coinbase USA nano futures
- **Timeframes represented in evidence:**
  - 1H preset configurations (all performance tables in this README)
- **Additional presets available in script:**
  - 5m configurations
- **Commission:** 0.04%
- **Position size:** 80%
- **Execution:** `calc_on_every_tick=true` (exits evaluated intrabar)

Results differ on bar close vs every tick, and by symbol liquidity/spread. Historical backtest snapshot and forward test use the same settings unless noted above.

---

## Evidence (Historical Backtest Snapshot)

**Backtest window:** Oct 3 – Nov 13, 2025 (41 days). Coinbase USA nano futures, 5-minute charts, 0.04% commission, 80% position size. 30-day estimate = `(1 + raw_return)^(30/41) − 1`.

Results below are from the 1H preset configurations.

| Preset | Raw return (41 d) | 30-day est. | Max DD | Trades | Win rate | Profit factor |
|--------|-------------------|-------------|--------|--------|----------|---------------|
| **BTC PERP (BIP)** | +34.62% | **+23.5%** | 2.05% | 20 | 80.0% | 15.91 |
| **ETH PERP (ETP)** | +87.43% | **+58.3%** | 4.46% | 51 | 92.2% | 17.62 |
| **SOL PERP (SLP)** | +103.30% | **+67.9%** | 3.87% | 36 | 72.2% | 7.51 |
| **XRP PERP (XPP)** | +38.00% | **+26.6%** | 2.22% | 16 | 75.0% | 13.62 |

> Results depend on symbol, timeframe, commission, and execution (e.g. on bar close vs every tick). Past performance does not guarantee future results. **This project is not financial advice.** See [Disclaimer](#disclaimer).

## Evidence (Forward Test Results — Live)

Forward test results from TradingView Strategy Tester. These are real-time forward runs (not retrospective curve-fit) and are being continued throughout the year to evaluate regime robustness, consistency, and capital efficiency. Forward test runs are kept live and updated periodically; this repo reports the latest captured window.

Results below are from the 1H preset configurations running continuously in TradingView Strategy Tester.

| Preset | Window | Total P&L | Max DD | Trades | Win rate | Profit factor |
|--------|--------|-----------|--------|--------|----------|---------------|
| **BTC (BIP)** | Jul 18, 2025 – Feb 22, 2026 | +30.34% | 4.25% | 71 | 67.61% | 2.122 |
| **ETH (ETP)** | Jul 18, 2025 – Feb 22, 2026 | +100.04% | 4.93% | 70 | 80.00% | 11.402 |
| **SOL (SLP)** | Aug 15, 2025 – Feb 22, 2026 | +94.89% | 8.64% | 78 | 71.79% | 2.767 |
| **XRP (XPP)** | Aug 15, 2025 – Feb 22, 2026 | +37.47% | 8.50% | 73 | 76.71% | 2.194 |

### Compounded rate (derived)

To normalize performance across uneven windows, we compute a compounded monthly rate and an annualized CAGR using: **CAGR = (1 + R)^(365/days) − 1**, where R is total return over the window and *days* is the number of calendar days in the window.

Annualized CAGR assumes compounding and continuous deployment; actual realized annual returns will vary by regime and execution conditions.

| Preset | Days | Compounded monthly | Annualized CAGR |
|--------|------|--------------------|-----------------|
| **BTC** | 219 | ~3.75% | ~55.5% |
| **ETH** | 219 | ~10.12% | ~217.6% |
| **SOL** | 191 | ~11.22% | ~257.9% |
| **XRP** | 191 | ~5.20% | ~83.7% |

Annualized CAGR is a mathematical normalization of the observed forward window and does not imply the same return rate will persist.

### 4-symbol portfolio (compounded blend)

Using compounded monthly rates derived from forward-tested windows:

- BTC: ~3.75% / month  
- ETH: ~10.12% / month  
- SOL: ~11.22% / month  
- XRP: ~5.20% / month  

Blended compounded monthly rate (sum of rates): ~30.3% / month

Execution model: 3 partitions with 1/3 capital allocation per trade; 3× leverage is offset by 1/3 sizing, yielding approximately 1× effective exposure per active position. Blended modeling does not assume leverage amplification.

This normalization framework (window-adjusted compounding + blended portfolio modeling) is used internally to monitor strategy stability and cross-symbol capital efficiency.

#### Compounded projection (illustrative, derived from ~30.3% monthly rate)

| Period | Total Multiple | Total Return |
|--------|----------------|--------------|
| 1 month | 1.30× | +30.3% |
| 6 months | 4.99× | +399% |
| 12 months | 24.9× | +2,390% |

Example (starting capital $4,000):

- 1 month → ~$5,211  
- 6 months → ~$19,960  
- 12 months → ~$99,600  

These figures are mathematical projections assuming the blended compounded monthly rate persists and capital remains continuously deployed. Real-world results will vary due to volatility regimes, correlation, execution timing, funding, fees, and slippage.

---


## What’s included

- **`easy_ichimoku_v14.pine`** — Full v14 strategy: Ichimoku (Tenkan/Kijun/Senkou/Chikou), ADX/RSI filters, breakeven (standard + 3H/6H), trailing stop, RSI exit, time filters (weekend, evening, news), and **Historical Enhancer** JSON in alerts for webhooks and analytics.

**Requirements:** TradingView (Pine Script v5). Optional: webhook endpoint (e.g. your own execution agent) to receive alerts.

---

## What is Ichimoku?

**Ichimoku Kinko Hyo** (“one-glance cloud chart”) is a trend-following system: five components (Tenkan, Kijun, Senkou A/B “cloud,” Chikou) give trend direction, momentum, and dynamic support/resistance. Classic settings (9/26/52) target daily charts; this script uses **preset-optimized periods** for 5m/1h so the cloud and signals respond in line with shorter timeframes.

**Entry logic (all must align when filters are on):** Tenkan/Kijun cross (or trend), price above/below cloud, Chikou confirmation, cloud color, ADX (trend strength), RSI (not overbought/oversold). **Exit logic:** initial stop/target (ATR-based or fixed %), breakeven, 3H/6H breakeven band, trailing stop, and RSI-based early exit.

---

## What’s in v14

| Area | v14 highlights |
|------|----------------|
| **6-hour breakeven** | Closes when P&amp;L enters a small profit band (e.g. +0.1% to +0.5%) after 6+ hours in the trade. |
| **Granular monitoring** | `calc_on_every_tick=true` so exits trigger when conditions are met, not only on bar close. |
| **Time filters** | Weekend block (Fri 04:00 PT – Mon 01:00 PT), evening block (20:00–01:00 PT), news filter (8–9 AM PT). |
| **ADX acceleration** | Optional: require ADX to be rising (trend strengthening). |
| **Historical Enhancer** | 100+ fields per entry/exit in alert JSON (Ichimoku, ADX, RSI, VWAP, volume, momentum, etc.). |
| **Presets** | V14-optimized defaults for BTC, ETH, SOL, XRP (5m and 1h) and other listed presets. |
| **Info panel** | On-chart table showing preset and key parameters. |

---

## Presets

Presets set Ichimoku periods, ADX, RSI, risk (ATR or fixed %), breakeven, trailing, and time filters per symbol/timeframe.

- **Coinbase USA:** 5m and 1h for BTC (BIP), ETH (ETP), SOL (SLP), XRP (XPP).
- **Coinbase INTX / Binance US / Kraken US:** 5m/15m BTC-style.

Leave **Use Preset Defaults** on for one-click optimized values, or turn it off to tune inputs manually.

---

## Main features (v14)

- **Ichimoku:** TK cross (or trend), cloud filter, cloud color, Chikou filter.
- **ADX** (with optional acceleration filter).
- **RSI** entry filter and **RSI exit** (activation % and long/short thresholds).
- **Risk:** ATR-based or fixed % stop; take-profit %.
- **Breakeven:** trigger % and lock %; optional **3-hour** and **6-hour** breakeven exits (profit band after hold time).
- **Trailing stop:** trigger % and trail distance %.
- **Time filters:** weekend block, evening block, news-volatility (8–9 AM PT).
- **Historical Enhancer:** rich JSON in entry/exit alerts (100+ fields) for logging, execution, or ML.

---

## Risk and guardrails

- **Position risk:** ATR-based or fixed % stop; take-profit; breakeven and trailing to lock gains.
- **Time-based:** Weekend and evening entry blocks; news filter to avoid open chaos (in our tests, this improved outcomes; exact lift varies by symbol/regime).
- **Hold-time exits:** 6H breakeven close when profit is in a small band after 6+ hours, to avoid giving back gains on stale trades.
- **RSI exit:** Optional early exit when momentum fades (activation % and long/short thresholds).

Exits are evaluated on every tick when using default settings, so behavior matches execution agents that monitor frequently.

---

## Strategy ID and alerts

- **Strategy ID** is set by the **Timeframe Preset** (e.g. BTC 5m → `ichimoku-coinbase-btc-5m`, ETH 1h → `ichimoku-coinbase-eth-1h`). You can override it in **Strategy ID Override.**
- **Alerts:** Create one alert per chart with **“Any alert() function call”** and your webhook URL. Leave the message empty; the script sends the full JSON (strategy_id, side, price, symbol, timeframe, historical_enhancer).

---

## Example alert payload

Entry and exit alerts include a payload like the following (abbreviated). The `historical_enhancer` object contains 100+ fields (Ichimoku, ADX, RSI, VWAP, volume, momentum, etc.).

```json
{
  "strategy_id": "ichimoku-coinbase-btc-5m",
  "side": "buy",
  "price": 98085.0,
  "cid": "20251209-192500",
  "bar_time": "2025-12-09T19:25:00",
  "symbol": "COINBASE:BIPZ2030",
  "timeframe": "5",
  "historical_enhancer": {
    "entry_direction": "LONG",
    "session": "US",
    "day_of_week": "Monday",
    "tenkan": 98050.0,
    "kijun": 98020.0,
    "cloud_bullish": true,
    "price_above_cloud": true,
    "adx": 15.5,
    "rsi": 45.2,
    "vwap_value": 98000.0,
    "volume_ratio_5": 1.25
  }
}
```

Use your own webhook URL in TradingView; this repo does not contain any agent or server URLs.

---

## Quick start

1. **Open TradingView** → Pine Editor.
2. **Paste** the contents of `easy_ichimoku_v14.pine` into a new script → **Add to chart.**
3. **Set chart** to the desired symbol (e.g. `COINBASE:BIPZ2030` for BTC) and timeframe (5m or 1h).
4. **Strategy settings** → **Timeframe Preset** → choose the matching preset (e.g. "5Min BIP / BTC PERP (Coinbase USA)").
5. Leave **Use Preset Defaults** checked so optimized values load.
6. **Properties** → set commission (e.g. 0.04%), execution (e.g. after fill + on every tick), position size (default 80%).
7. **(Optional)** Create an alert → condition: **Any alert() function call** → webhook URL = your endpoint → message empty.

---

## Repository structure

```text
easy-ichimoku/
├── README.md                 # This file
├── CHANGELOG.md              # Version history and v14 highlights
└── easy_ichimoku_v14.pine    # Full v14 strategy (Pine Script v5)
```

---

## Limitations and robustness plan

- **Regime dependence** — Performance varies in trend vs chop; the strategy is tuned for trend-following.
- **Funding, fees, slippage** — Futures funding and execution costs affect live P&L; backtest/forward test use stated commission only.
- **Execution model** — Intrabar (every tick) vs bar-close execution can change fill quality and drawdowns.
- **Correlation spikes** — Crypto majors can move together; blended estimates do not account for simultaneous drawdowns.
- **Preset drift** — Market structure shifts over time; presets benefit from periodic re-validation.
- **Forward test** — Continuing through the year to evaluate robustness, consistency, and capital efficiency across regimes.

---

## Disclaimer

**This repository is for educational and research purposes only. It does not constitute financial, investment, or trading advice.** Trading derivatives involves substantial risk of loss. Past backtest and forward test results do not guarantee future performance; live results will depend on execution, fees, slippage, and market conditions. You are solely responsible for your own trading and risk decisions. Validate in paper/simulated environments before any live use. Consult a qualified professional for advice specific to your situation.

---

## License

Provided as-is. See [LICENSE](LICENSE) in this repository if present.

---

*Public repo. Use your own webhook URL and broker/agent; do not commit credentials. Production deployment uses a separate, private project.*
