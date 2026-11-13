# algo-trading

A modular Python system for researching, backtesting, and paper trading systematic strategies on US ETFs using daily bars.

> **Status: Phase 0 (setup).** The repository structure, tooling, and CI are being put in place. No strategy results yet. This README describes the design and will be updated as each phase lands.

This is a learning and engineering project. Nothing here is financial advice, and historical backtest results do not guarantee future returns.

## Goals

- Build a backtester whose correctness I can explain and test, not just use.
- Run the same strategy, risk, and execution code in backtest and paper trading. Only the data source and broker implementations change.
- Document research honestly: assumptions, costs, and the statistical uncertainty of any result.

## Scope

| Item | Choice |
|---|---|
| Market | US ETFs (stocks later) |
| Frequency | Daily bars, monthly rebalance |
| Language | Python 3.12 |
| Strategy 1 | Long-only ETF trend following (12-month time-series momentum, about 10 ETFs, equal weight, cash when the signal is off) |
| Strategy 2 | Pairs trading (later phase) |
| Out of scope for now | ML, options, intraday, live trading |

## Architecture

One package with separate modules:

```
data        load and validate price data (Parquet storage)
strategy    turn data into target signals
risk        position sizing and limits
execution   turn target positions into orders
broker      broker interface (simulated for backtests, Alpaca for paper)
backtest    vectorized daily simulation engine
analytics   returns, drawdown, Sharpe, reporting
config      settings and parameters
```

Design rules:

- **No lookahead.** The engine enforces a one-day lag between signal and trade.
- **Swappable edges.** `DataProvider` and `Broker` are interfaces; strategy, risk, and execution code does not know which implementation it is using.
- **Own backtester first.** I write a vectorized engine from scratch to learn the mechanics, then cross-check against an established library.

## Tooling

- pandas, NumPy
- Parquet for price data, SQLite for the order log
- pytest for tests, ruff for linting
- GitHub Actions for CI
- Data: Tiingo (history), Alpaca (paper trading), FRED (risk-free rate)

## Roadmap

| Phase | Focus |
|---|---|
| 0 | Repo setup, package layout, tests, CI |
| 1 | Data ingestion, validation, return calculations |
| 2 | Backtester v1 with transaction costs and tests |
| 3 | Strategy 1 research and written results |
| 4 | Risk management and position sizing |
| 5 | Paper trading loop via Alpaca |
| 6 | Evaluation, cross-validation against another engine, pairs trading |

Live trading is optional and only considered after several months of clean paper results.

## Known limitations

- Daily bars only. Fills are assumed at the next open or close with a fixed cost model.
- Common ETF history starts around 2007, so performance estimates carry wide error bars. For example, a Sharpe ratio estimated over that window has a standard error near 0.24.
- One strategy at a time, and no intraday risk checks.

## Setup

Not available yet. Install and usage instructions will be added in Phase 0.

API keys belong in a local `.env` file, which is gitignored. Never commit credentials.

## License

MIT