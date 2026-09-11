# Global Working Agreements

Personal cross-project defaults. Repository-specific architecture, domain, schemas, and verification commands belong in that repository's own `CLAUDE.md` / `AGENTS.md`; more specific instructions override this file.

## Communication and Language

- Reply in the language the user writes in unless they request otherwise.
- Lead with the outcome, keep explanations proportional to the task, and state material uncertainty directly.
- Comments, commit messages, document prose: **Chinese**. Exception: a repository with an established English convention (open source, external collaboration) keeps its own.
- Identifiers, function names, class names, log messages, config keys, table names, field names, file names: **English** (for grep-ability).

## Scope and Authorization

- Prefer the smallest safe change that satisfies the request. Modify only requested files and their direct dependencies; avoid speculative abstractions, unrelated refactoring, and drive-by cleanup.
- Do not silently change architecture, dependencies, credentials, data paths, public APIs, or database schemas. Ask when a missing choice would materially change behavior or scope.
- Carry authorized work through completion. State low-risk assumptions and continue; do not ask again for authorization already given in the session. If a decision is needed, continue independent work while awaiting the answer.
- Do not commit, push, publish, deploy, open pull requests, or send external messages unless explicitly requested.
- Never output or commit real credentials, tokens, private keys, or real values from local environment files.
- Uncommitted changes in the working tree are user-owned. Preserve them — never revert, stash, or overwrite them to make your own work apply cleanly.
- When asked only to review, diagnose, explain, or report, stay read-only unless changes were also requested.
- When a commit is requested, write a concise Conventional Commit message unless the repository specifies another convention.

## New-Project Defaults (Python projects; existing projects follow their own repo)

- Python 3.10+, `from __future__ import annotations` as the first statement — after the module docstring when there is one; manage dependencies with `uv`, not pip.
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

- The main agent handles tasks by default; there is no mandatory routing table or multi-agent pipeline.
- Use subagents when the user requests them or independent work would materially improve speed, quality, or context isolation.
- Available roles: `architect` for design only, `product-manager` for requirements only, `writer` for writing, and `code-review` for independent read-only review. Ordinary implementation stays with the main agent; role availability does not require delegation.
- Give each delegated task a bounded scope, relevant context, constraints, and expected output. Subagents must preserve existing work and follow the same authorization and deliverable boundaries.
- Never edit overlapping files in parallel. Give parallel writers disjoint ownership, run dependent stages sequentially, and identify the source of material conclusions or artifacts.

## Verification and Delivery

- Use the repository's required checks and verification proportionate to risk. Once they pass, stop unless new changes, failures, or unresolved risks justify more checks. Do not automatically fix unrelated failures.
- Report what verification ran and its result. If verification cannot complete, explain why, what was checked manually, the remaining risk, and the exact command the user can run next.
- Create a standalone document only when the user requests one or provides a path. Implementation and fix requests authorize necessary source edits and updates to existing documentation within scope.
- After changes, summarize modified files, behavioral impact, verification, and residual risk.

## Bootstrap

To establish Python coding standards in a new project, take the seed from `~/.claude/templates/code-dev.md`, copy it into that project's `CLAUDE.md`, and adapt it — don't rewrite it from memory.
