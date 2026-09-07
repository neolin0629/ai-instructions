# Template: Python Coding Standards (project level)

**Usage**: copy into the target project's `GEMINI.md` (or as a section of it) and adapt to that project. This is not an agent — writing code is the main thread's default job and doesn't need a subagent.

**Relation to global**: language conventions, `uv`, storage tiers, and quant guardrails already live in `~/.gemini/GEMINI.md` and are not repeated here. This template covers implementation-level standards only.

---

## Basics

- Format and lint: `ruff format`, `ruff check`
- Type hints: required on all public API signatures and cross-module call boundaries
- Docstrings: Google style for public modules, classes, and core functions; core functions state time / space complexity
- MRE entry: files with runnable logic end with `if __name__ == "__main__":` containing realistic dummy data as a minimal reproducible example. Excluded: pure library modules (`__init__.py`, type-only / interface-only / constants modules), and any file whose `__main__` block is the real entry point — CLI commands, service launchers, scheduled jobs. Never displace a real entry point with dummy data.

## Data Integrity

- `NaN` / `Inf` / division-by-zero / empty DataFrames must be handled explicitly — no silent drop or fillna; the strategy gets a comment
- Type consistency: floats unified as `float64`, instrument codes unified as `str`
- Index and ordering: explicitly check `is_monotonic_increasing` and duplicates before operating

## Performance (highest priority first)

1. Vectorize first (Pandas / NumPy / Polars). No `.iterrows()` or row-level loops on large DataFrames
2. Avoid `apply`; use `.shift()` / `.rolling()` / `np.where` / `pl.col(...).over(...)`
3. When a loop is unavoidable, `numba.jit(nopython=True, cache=True)` is the default choice — but if numba isn't already a project dependency, say so and get agreement before adding it
4. For large datasets, assess peak memory and chunk if necessary
5. Beyond ~1M rows, default to Polars or DuckDB
6. Benchmark hot paths with `time.perf_counter()` before and after — not `time.time()`, not by feel

## Logging and Exceptions

- No `print()`; use `logging` or `loguru`. Temporary debug output is removed immediately
- Levels: `DEBUG` dev previews / `INFO` business milestones / `WARNING` expected anomalies / `ERROR` system interruptions
- Log messages in English with structured fields: `logger.info("backtest_done", extra={"strategy": name, "sharpe": s})`
- Catch around network requests, APIs, and file IO only where there is a recovery strategy or context worth adding to the error — otherwise let it propagate. Catching to log-and-continue hides failures. In parallel execution one failed task must not crash the main thread
- No bare `except:` and no `except Exception: pass`

## Testing and Dependencies

- Core numerical computation and pure functions: pytest coverage > 80%, or the repository's own configured threshold when it has one — that wins
- Tests cover normal inputs, edges (empty, single element, extremes), and abnormal inputs (NaN / Inf)
- When randomness is involved, fix `random.seed` and `np.random.seed`
- Dependencies go through `pyproject.toml` with dev separated from runtime: `uv sync` / `uv add` / `uv add --dev` / `uv run` / `uv lock`

```bash
uv run pytest tests/ -v --cov=src
uv run ruff format <changed files>
uv run ruff check <changed files> --fix
```

`--fix` is scoped to the files the task touched. Running it across `src/` rewrites code outside the task, which makes the diff unreviewable.

## Output Conventions

- Code first, then explain the key points
- Annotate implicit assumptions in comments: `# Memory heavy: peak ~2GB on 10M rows` / `# Assumes df is sorted by datetime` / `# Not thread-safe` / `# Requires ClickHouse v23.8+`
- Give verification commands after changes
