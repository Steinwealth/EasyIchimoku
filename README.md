# Easy Ichimoku v14

Public-safe TradingView Pine Script for the **€£$¥ Ichimoku v14** strategy. Use with your own broker/agent; no internal URLs or secrets.

## What’s included

- **`easy_ichimoku_v14.pine`** — Full v14 strategy: Ichimoku (Tenkan/Kijun/cloud/Chikou), ADX/RSI filters, breakeven (including 3H/6H), trailing stop, RSI exit, time filters (weekend/evening/news), and Historical Enhancer alert payload (JSON) for webhooks.

## Requirements

- **TradingView** (Pine Script v5).
- Optional: a webhook endpoint (e.g. your own TradingView Agent) to receive alerts. Alerts send JSON with `strategy_id`, `side`, `price`, and `historical_enhancer` fields.

## Strategy ID and alerts

- **Strategy ID** is chosen by the **Timeframe Preset** (e.g. BTC 5m → `ichimoku-coinbase-btc-5m`, ETH 1h → `ichimoku-coinbase-eth-1h`). You can override it in **Strategy ID Override**.
- Alerts (entry/exit) emit JSON suitable for POST to a webhook. Configure your alert in TradingView to send to your own URL.

## Presets

Presets set Ichimoku periods, ADX, RSI, risk (ATR or fixed %), breakeven, trailing, and time filters per symbol/timeframe. Supported presets include:

- **Coinbase USA**: 5m/1h for BTC (BIP), ETH (ETP), SOL (SLP), XRP (XPP).
- **Coinbase INTX / Binance US / Kraken US**: 5m/15m BTC-style.

Use **Use Preset Defaults** for one-click preset values, or turn it off to tune inputs manually.

## Main features (v14)

- **Ichimoku**: TK cross (or trend), cloud filter, cloud color, Chikou filter.
- **ADX** (with optional acceleration filter).
- **RSI** entry filter and **RSI exit** (activation % and long/short thresholds).
- **Risk**: ATR-based or fixed % stop; take-profit %.
- **Breakeven**: trigger % and lock %.
- **6-hour breakeven**: optional exit in a profit range after 6+ hours (1H presets).
- **3-hour breakeven**: optional exit in a profit range after 3+ hours (configurable).
- **Trailing stop**: trigger % and trail distance %.
- **Time filters**: weekend block (Fri 04:00 PT – Mon 01:00 PT), evening block (20:00–01:00 PT), news volatility (8–9 AM PT).
- **Historical Enhancer**: rich JSON in entry/exit alerts for logging or downstream systems.

## How to use

1. Open TradingView → Pine Editor.
2. Paste the contents of `easy_ichimoku_v14.pine` (or create a new script and copy it in).
3. Save and add to chart.
4. Choose **Timeframe Preset** and leave **Use Preset Defaults** on, or adjust inputs.
5. (Optional) Create an alert → Webhook URL = your endpoint. Message: use the default “Strategy alert” message so the JSON is sent.

## License and disclaimer

This is provided as-is for education and personal use. Trading involves risk. No warranty; you are responsible for your own trading and infrastructure (broker, webhooks, agent). Production deployment uses a separate, private project; this repo is a public-safe copy.
