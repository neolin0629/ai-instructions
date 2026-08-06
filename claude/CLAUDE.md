# Global Working Agreements

Personal cross-project defaults. Repository-specific architecture, domain, schemas, and verification commands belong in that repository's own `CLAUDE.md` / `AGENTS.md`; more specific instructions override this file.

Facts the toolchain states for itself are not written here — `uv.lock`, `pyproject.toml`, and the existing code style *are* the facts. Read them instead of encoding a rule.

## Language

- Reply in the language the user writes in.
- Comments, commit messages, document prose: **Chinese**.
- Identifiers, function names, class names, log messages, config keys, table names, field names, file names: **English** (for grep-ability).

## Change Boundaries

- Never silently change architecture, dependencies, credentials, data paths, public APIs, or database schemas — say it first.
- Never output or commit real credentials, tokens, private keys, or real values from local environment files.

## New-Project Defaults (Python projects; existing projects follow their own repo)

- Python 3.10+, first line `from __future__ import annotations`; manage dependencies with `uv`, not pip.
- Prefer Polars / DuckDB for data processing; keep pandas for small data and compatibility.
- Use FastAPI when a backend service is needed.

## Storage Tiers (choose by purpose, never mix)

| Purpose | Choice |
|---|---|
| Business system of record: config, orders, accounts, metadata; needs transactions and relations | PostgreSQL |
| Massive historical time series: K-line, tick; append-only, read-heavy analytical queries | ClickHouse |
| Single-machine analysis, reading Parquet, fast SQL on medium data | DuckDB |
| Real-time layer: message dispatch, tick cache, cross-process state, distributed locks, rate limiting | Redis |

- Redis is never the system of record; critical data must land periodically in PG / CH. Neither historical nor relational data goes in Redis.
- No CSV beyond 100k rows unless explicitly requested; use Parquet (zstd / snappy) for local caching.

## Quant Correctness Guardrails (applies when market data, factors, backtests, or live trading are involved)

- **No look-ahead bias**: a historical backtest must not use data unavailable at that point in time. When using lag / shift, state the lag period and distinguish signal-generation timestamp from execution timestamp.
- **Adjustment and price basis**: state the adjustment method; distinguish the price used for signal computation from the price used for order execution.
- **Timezone**: datetimes are either all timezone-aware or all naive — never mixed.
- **Backtest realism**: must account for slippage, commissions, capital constraints, margin, and liquidation risk. Never assume infinite capital at zero cost.
- **Deployment order**: historical backtest → paper trading → small-capital live. No skipping stages.
- **No fabricated quant logic**: never invent factor formulas, adjustment rules, data fields, or market-vendor APIs. When unsure, ask for the documentation.

## Subagents

- The main thread handles work by default. There is no mandatory role-routing table.
- Dispatch only when the user names an agent, or when independent work would materially improve speed, quality, or context isolation: read-heavy exploration, independent review, log and test analysis, cleanly separated parallel work.
- Never edit overlapping files in parallel. Run dependent stages sequentially, and say which conclusion came from which agent.

## Deliverables

- Write a file only when the user asks for an artifact or gives a path; otherwise return the result in chat.
- After changes, give copy-pasteable verification commands using the repository's own commands. When verification isn't possible, say why, what was checked manually, and what risk remains.

## Bootstrap

To establish Python coding standards in a new project, take the seed from `~/.claude/templates/code-dev.md`, copy it into that project's `CLAUDE.md`, and adapt it — don't rewrite it from memory.
