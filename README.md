# PrismBot — Fully Automated 24/7 Crypto Trading System

<p align="center">
  <strong>Modular • Scalable • Paper-to-Live Crypto Trading</strong><br>
  A fully automated trading system designed for continuous 24/7 operation.
</p>

<p align="center">
  <a href="README.fa.md">🇮🇷 فارسی</a> ·
  <a href="#features">Features</a> ·
  <a href="#architecture">Architecture</a> ·
  <a href="#quick-start">Quick Start</a> ·
  <a href="#risk-management">Risk Management</a> ·
  <a href="#desktop-ui">Desktop UI</a>
</p>

---

## Overview

**PrismBot** is a fully automated, modular, and scalable crypto trading system designed to start with **Paper Trading** and transition to **Live Trading** with a simple operating-mode switch.

Its architecture separates data, strategies, portfolio allocation, risk management, execution, monitoring, and orchestration, making the system easier to extend and operate.

> ⚠️ **Disclaimer:** PrismBot is an educational and research project. Live trading involves the risk of losing capital and no profitability is guaranteed.

---

## Features

- 🧩 **Modular architecture** — Separate layers for data, strategies, risk, execution, and monitoring
- 🧠 **Hybrid multi-strategy support** — Rule-based strategies combined with ML/Sentiment filters
- 🔄 **Paper → Live** — Simple switch between simulated and live trading
- 🛡️ **Risk controls** — Kill-switch, drawdown limits, and maximum position size
- ⚡ **24/7 ready** — Designed for VPS deployment with Docker
- 🔌 **Extensible** — Easy to add strategies, markets, or broker/exchange integrations

---

## Architecture

```text
┌──────────────────────────────────────────────────────────┐
│                       PrismBot                            │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Data ──► Strategies ──► Portfolio ──► Risk ──►         │
│                                                Execution  │
│                                                    │     │
│                                                    ▼     │
│                                               Monitoring │
│                                                          │
│                 Core Orchestrator                        │
└──────────────────────────────────────────────────────────┘
```

### Core Components

| Component | Responsibility |
|---|---|
| `data/` | Market data ingestion and processing via CCXT |
| `strategies/` | Hybrid trading strategies |
| `portfolio/` | Capital allocation and ensemble logic |
| `risk/` | Risk management and kill-switch |
| `execution/` | Paper and Live execution via CCXT |
| `monitoring/` | Logs, metrics, and Telegram integration |
| `core/` | Main system orchestrator |

---

## Project Structure

```text
prismbot/
├── config/                 # YAML configuration
├── src/
│   ├── data/               # Data ingestion and processing (CCXT)
│   ├── strategies/         # Hybrid strategies
│   ├── portfolio/          # Capital allocation and Ensemble
│   ├── risk/               # Risk management and Kill-switch
│   ├── execution/          # Paper + Live execution (CCXT)
│   ├── monitoring/         # Logs, metrics, Telegram
│   └── core/               # Main orchestrator
├── scripts/                # backtest, live, train
├── ui/                     # Desktop UI
├── docker-compose.yml
├── Dockerfile
└── .env.example
```

---

## Quick Start

### 1. Clone and prepare

```bash
cd prismbot
python -m venv .venv

# Linux / macOS
source .venv/bin/activate

# Windows
# .venv\Scriptsctivate

pip install -r requirements.txt
cp .env.example .env
```

### 2. Configure

Edit `.env`:

```env
MODE=paper
EXCHANGE=binance
API_KEY=your_key_here
API_SECRET=your_secret_here
TELEGRAM_BOT_TOKEN=
TELEGRAM_CHAT_ID=
```

Configure `config/*.yaml` according to your capital, risk settings, trading pairs, and strategy parameters.

### 3. Run with Docker

Recommended for VPS deployment:

```bash
docker compose up -d --build
```

### 4. Run without Docker

```bash
python -m src.core.live
```

---

## Operating Modes

| Mode | Command | Purpose |
|---|---|---|
| 🧪 **Paper** | `MODE=paper python -m src.core.live` | Simulated trading without real capital |
| 🔴 **Live** | `MODE=live python -m src.core.live` | Real-market trading |
| 📈 **Backtest** | `python scripts/backtest.py` | Historical-data testing |

> **Recommended workflow:** Backtest → Paper Trading → Live Trading.

---

## Risk Management

Risk controls are a mandatory part of the system:

- Maximum daily and overall drawdown
- Maximum position size
- **Kill-switch** via the `KILL` file or Telegram
- **Circuit breaker** for abnormal volatility or connection failures

### Emergency Kill-Switch

Create the `KILL` file:

```bash
touch KILL
```

Or trigger the Telegram command:

```text
/kill
```

---

## Adding a New Strategy

1. Create a new strategy file under `src/strategies/`.
2. Inherit from `BaseStrategy`.
3. Register it in `config/strategies.yaml`.
4. Restart the bot.

See `docs/` for additional documentation.

---

## Desktop UI

### Tauri + React + Rust

PrismBot includes a desktop dashboard for monitoring and controlling the trading bot.

### Requirements

- Node.js 18+
- Rust via `rustup`
- Python trading bot running
- Status API available on port `8765`

### Development

**Terminal 1 — Python Bot**

```bash
cd prismbot
source .venv/bin/activate
pip install -r requirements.txt
python -m src.core.live
```

**Terminal 2 — UI**

```bash
cd ui
npm install
npm run tauri dev
```

### Production Build

```bash
cd ui
npm install
npm run tauri build
```

Build output:

```text
ui/src-tauri/target/release/
```

### UI Capabilities

- Equity, Daily PnL, Drawdown, and Uptime
- Open positions
- Active strategies and weights
- Live logs
- Kill-switch control
- Paper / Live mode support

Status API:

```text
http://127.0.0.1:8765
```

---

## Security Notes

- **Always start with Paper Trading.**
- Live trading can result in loss of capital.
- Never give the bot withdrawal permission through its API key.
- Use trading-only permissions whenever possible.
- Keep secrets in `.env` and never commit them to Git.
- Review risk limits before enabling Live mode.

---

## Development & Testing

Run the test suite:

```bash
pytest tests/
```

Logs:

```text
logs/
```

Documentation:

```text
docs/
```

---

## Disclaimer

PrismBot is provided for educational and research purposes.

Nothing in this repository constitutes financial advice, an investment recommendation, or a guarantee of trading performance.

**Validate the system, strategies, execution behavior, and risk controls independently before enabling Live Trading.**

---

<p align="center">
  Built with a modular architecture for full user ownership and extensibility.
</p>
