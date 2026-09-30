---
name: quant-guardrails
description: "Correctness guardrails and storage tiers for quantitative work. Use when writing, designing, or reviewing code that touches market data (K-line, tick), factors, signals, backtests, paper or live trading, or when choosing storage (PostgreSQL / ClickHouse / DuckDB / Redis / Parquet) for a data pipeline. 触发词：行情、K 线、tick、因子、信号、回测、模拟盘、实盘、复权、存储选型。"
---

# Quant Guardrails

## Correctness

- **No look-ahead bias**: a historical backtest must not use data unavailable at that point in time. When using lag / shift, state the lag period and distinguish the signal-generation timestamp from the execution timestamp.
- **Adjustment and price basis**: state the adjustment method; distinguish the price used for signal computation from the price used for order execution.
- **Timezone**: datetimes are either all timezone-aware or all naive — never mixed.
- **Backtest realism**: account for slippage, commissions, capital constraints, margin, and liquidation risk. Never assume infinite capital at zero cost.
- **Deployment order**: historical backtest → paper trading → small-capital live. No skipping stages.
- **No fabricated quant logic**: never invent factor formulas, adjustment rules, data fields, or market-vendor APIs. When unsure, ask for the documentation.

## Storage tiers (choose by purpose, never mix)

| Purpose | Choice |
|---|---|
| Business system of record: config, orders, accounts, metadata; needs transactions and relations | PostgreSQL |
| Massive historical time series: K-line, tick; append-only, read-heavy analytical queries | ClickHouse |
| Single-machine analysis, reading Parquet, fast SQL on medium data | DuckDB |
| Real-time layer: message dispatch, tick cache, cross-process state, distributed locks, rate limiting | Redis |

- Redis is never the system of record; critical data must land periodically in PG / CH. Neither historical nor relational data goes in Redis.
- No CSV beyond 100k rows unless explicitly requested; use Parquet (zstd / snappy) for local caching.

## Verification

Prove each guardrail with a check, not a claim: e.g. a test that truncates the input at time t and confirms every signal up to t is unchanged, an assertion that all timestamps share one tz policy, and a backtest report that lists the cost and slippage parameters used.
