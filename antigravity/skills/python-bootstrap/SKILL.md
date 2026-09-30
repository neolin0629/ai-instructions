---
name: python-bootstrap
description: "Seed or refresh a Python project's GEMINI.md with coding, testing, and performance conventions adapted to that repository."
---

# Python Bootstrap

Seed the current project's `GEMINI.md` with the Python conventions in [code-dev.md](code-dev.md). Adapt; don't copy blindly.

1. Read the project's existing `GEMINI.md` / `AGENTS.md`, `pyproject.toml`, and CI / lint / test config. If there is no `GEMINI.md`, create one from what the repository shows.
2. Read `code-dev.md`. Keep only rules that apply to this project and that Antigravity could not infer from its code or config. Drop anything the repository already enforces through tooling (e.g. ruff settings already in `pyproject.toml`).
3. Replace generic commands with the project's real ones (test runner, paths such as `src/` vs a package dir, coverage threshold).
4. When the project conflicts with a rule, the project's existing convention wins; note the difference instead of changing the code.
5. Do not repeat rules from `~/.gemini/GEMINI.md` or the `quant-guardrails` skill. For a quant project, note the applicability of `quant-guardrails` in its `GEMINI.md`.
6. Keep the resulting `GEMINI.md` concise — well under 200 lines. For each line, ask whether removing it would cause mistakes; if not, cut it.
7. Show the diff and summarize what was added, adapted, and dropped. Do not commit.
