---
name: quant-guardrails
description: "Apply quant data and execution contracts when designing, changing, or reviewing market data, factors, backtests, trading behavior, or their storage."
---

# Quant Guardrails

Apply the relevant contracts below to the affected behavior. Use the request, vendor documentation, and existing code to establish the actual conventions; a naming or documentation-only edit does not require a pipeline audit.

## Data and execution contracts

- Establish instrument scope, data frequency, time range, and historical, paper, or live execution mode when these affect correctness.
- Historical signals may use only data available at that time. Distinguish event time from availability time, define any lag, and separate signal-generation time from execution time.
- State the price-adjustment basis and distinguish signal prices from executable prices. Keep a consistent timezone policy and instrument-code representation; define ordering, duplicates, missing values, NaN, and Inf behavior where affected.
- Backtests must state applicable fees, slippage, capital constraints, margin, and liquidation assumptions. Missing inputs remain explicit assumptions or evidence gaps; do not fabricate factor formulas, vendor fields, or APIs.
- Plan rollout as historical backtest → paper trading → small-capital live trading. Execute only the authorized stages; writing trading code does not authorize placing live orders.

## Storage preferences

These are personal defaults for new storage decisions, not a migration mandate. Preserve the existing stack unless changing it is in scope, and introduce only the components the workload needs.

| Purpose | Default |
|---|---|
| Transactional business records: orders, accounts, configuration, metadata | PostgreSQL |
| Large, append-heavy historical K-line or tick analytics | ClickHouse |
| Local analysis and SQL over Parquet | DuckDB |
| Transient real-time messaging, caches, shared state, locks, rate limits | Redis |

- Keep authoritative business records and historical data in durable storage; Redis is not their sole source of truth.
- For local caches above 100k rows, prefer Parquet with zstd or snappy over CSV unless the user or an integration requires CSV.

## Verification

Choose checks for the contracts the change affects. For historical signal changes, a useful check compares signals through time t with and without later input; timestamp checks verify the chosen timezone policy; backtest results identify the cost assumptions used. Reuse existing evidence and stop once relevant checks pass; do not require every check for every task. A design-only request describes needed checks without executing a backtest or trading stage.
