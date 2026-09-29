---
name: python-bootstrap
description: "Seed or refresh a Python project's CLAUDE.md with coding, testing, and performance conventions adapted to that repository."
disable-model-invocation: true
---

# Python Bootstrap

Seed the current project's `CLAUDE.md` with the Python conventions in [code-dev.md](code-dev.md). Adapt; don't copy blindly.

1. Read the project's existing `CLAUDE.md` / `AGENTS.md`, `pyproject.toml`, and CI / lint / test config. If there is no `CLAUDE.md`, suggest running `/init` first, or create one from what the repository shows.
2. Read `code-dev.md`. Keep only rules that apply to this project and that Claude could not infer from its code or config. Drop anything the repository already enforces through tooling (e.g. ruff settings already in `pyproject.toml`).
3. Replace generic commands with the project's real ones (test runner, paths such as `src/` vs a package dir, coverage threshold).
4. When the project conflicts with a rule, the project's existing convention wins; note the difference instead of changing the code.
5. Do not repeat rules from `~/.claude/CLAUDE.md` or the `quant-guardrails` skill. For a quant project, add `@~/.claude/skills/quant-guardrails/SKILL.md` to its `CLAUDE.md` so the guardrails load every session.
6. Keep the resulting `CLAUDE.md` concise — well under 200 lines. For each line, ask whether removing it would cause mistakes; if not, cut it.
7. Show the diff and summarize what was added, adapted, and dropped. Do not commit.
