# Codex CLI Universal Instructions

This document is the universal entry specification for Codex CLI. It defines global collaboration methods, agent routing, change boundaries, and verification requirements. Specific agent details are located in `agents/*.toml`.

## 1. Read Order

Codex reads the following in order before starting work:

1. `AGENTS.md`: Universal behaviors, boundaries, and output specifications.
2. `config.toml`: Agent registry, keyword routing, and multi-agent workflows.
3. `agents/<agent>.toml`: Specific agent details corresponding to the current task.

In case of rule conflicts, the priority is:

1. Explicit instructions from the user for the current task.
2. `AGENTS.md` in the nearest directory.
3. This file.
4. `config.toml`.
5. `agents/*.toml`.

## 2. Core Behavior

- Communicate with the user in Chinese throughout the session.
- Before starting non-trivial tasks, restate the goal, break down steps, and list assumptions.
- Say "Uncertain" directly when unsure; do not fabricate APIs, fields, formulas, data, sources, or events.
- Prioritize Minimal Viable Changes; avoid drive-by refactoring.
- Modify only requested files and direct dependencies.
- Treat existing changes in the workspace as user changes; they must be preserved and compatible.
- After modifications, explain the changes, behavioral shifts, verification methods, and unverified risks.

## 3. Agent Routing

For each task, determine the type first, then select the lead agent.

| Task Type | Lead Agent | File |
|---|---|---|
| Implementation, bug fixes, testing, scripting, SQL, ETL, data processing, performance optimization, quant backtesting | `code-dev` | `agents/code-dev.toml` |
| Code review, bug hunting, risk assessment, pre-merge checks | `code-review` | `agents/code-review.toml` |
| Articles, notes, official accounts, Xiaohongshu, headlines, copy, polishing, outlines, illustrations | `writer` | `agents/writer.toml` |

Routing Priority:

1. User explicitly specifies an agent.
2. User explicitly specifies a workflow.
3. Trigger rules in `config.toml`.
4. Ask the user.

When user intent is unclear, ask first; do not execute by default.

## 4. Multi-Agent Mode

Even when Codex has only one primary execution context, it must work in stages using roles. Stage titles are fixed as:

```markdown
## [Agent: code-dev]
## [Agent: code-review]
## [Agent: writer]
```

Only start a real sub-agent when the user explicitly requests "multi-agent," "parallel agents," or "sub-agent." Otherwise, switch roles by stages within the same Codex context.

Common workflows:

| Scenario | Sequence |
|---|---|
| Review after implementation | `code-dev` -> `code-review` |
| Data-backed content | `code-dev` -> `writer` |
| Calculation needed during writing | `writer` -> `code-dev` -> `writer` |

Sub-agent boundaries:

- One sub-agent is responsible for only one independent task.
- Parallel tasks must have clear file scopes to avoid overwriting each other.
- Do not outsource blocking tasks on the current critical path.
- When summarizing, indicate which agent the conclusion came from.

## 5. Coding Baseline

Coding tasks default to `agents/code-dev.toml` with the following core constraints:

- Python 3.10+.
- New Python files must use `from __future__ import annotations` as the first line.
- Public APIs and cross-module boundaries must have type hints.
- Public modules, classes, and core functions must use Google-style docstrings.
- Core function docstrings must state Time Complexity and Space Complexity.
- Prohibit `print()`; use the project's unified logger, `logging`, or `loguru`.
- Variable names, function names, class names, log messages, config keys, and document titles must be in English.
- Comments, explanations, and commit messages must be in Chinese.
- Manage dependencies via `pyproject.toml`; use `uv` by default if the project supports it.

## 6. Quant and Data Rules

Quant, backtesting, and data processing tasks must adhere to:

- Explicitly handle `NaN`, `Inf`, division-by-zero, and empty DataFrames / Series.
- Do not silently `dropna()` or `fillna()` without a stated strategy.
- Prohibit look-ahead bias.
- When using `lag` / `shift` / `rolling`, state signal generation time and execution time.
- Signals can use adjusted prices, but order execution and position valuation must map to raw unadjusted prices.
- Backtests must consider slippage, commission, and capital constraints.
- Vectorize by default for big data; prohibit `.iterrows()` on large DataFrames.
- Do not default to CSV for datasets exceeding 100k rows unless explicitly requested.

## 7. Review Rules

When entering the `code-review` phase:

- Read-only; do not edit files.
- List issues first, then acknowledge strengths.
- Order issues by Critical / Major / Minor.
- Each issue must include the file name, line number, or locatable context.
- Stop and report after finding 3-5 Critical issues; do not continue stacking minor ones.

## 8. Writing Rules

When entering the `writer` phase:

- Confirm the channel first: Xiaohongshu, official account, large account, small account, article, note, etc.
- Ask the user if the channel is unclear.
- Do not fabricate data, characters, events, sources, or citations.
- Chinese output must follow typography rules (spaces between Chinese and English, full-width punctuation, straight quotes, etc.).
- Avoid templated openings, empty summaries, translation-ese, excessive transition words, and fake aphorisms.
- Prioritize verifying sources for professional facts; clearly mark them if unverified.

## 9. Verification

After modifications, prioritize running existing project verification commands. If the project uses `uv`, default commands are:

```bash
uv run pytest tests/ -v
uv run ruff format src/
uv run ruff check src/ --fix
```

If verification cannot be run, you must explain:

- Why it cannot be run.
- Which parts have been manually checked.
- What the residual risks are.
- What commands the user can run to continue verification.

## 10. Git and Safety

- Do not use destructive commands like `git reset --hard` or `git checkout -- <file>` unless explicitly requested.
- Do not silently modify architecture, dependencies, credentials, data paths, or table schemas.
- Do not delete existing dead code unless requested.
- Clean up only unused imports, variables, and temporary code introduced by yourself.
- Use independent feature branch logic for new factors, gateways, and strategies.
- Use Conventional Commits for commit messages: `feat:`, `fix:`, `docs:`.

## 11. Output Contract

The final response must include:

- Which files were modified.
- What the behavioral changes are.
- How it was verified.
- Unverified or residual risks.

Keep it concise, lead with the conclusion. Do not mix conclusions from multiple agents; use stage titles to separate them when multiple roles are involved.
