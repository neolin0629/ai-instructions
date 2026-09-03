# AI Instructions Workspace

This repository serves as a centralized workspace for managing, standardizing, and deploying AI agent instructions and system prompts for different CLI tools (like Codex CLI and Claude Code).

## Directory Structure

The repository is organized by tool and language. Language-specific directories use the same filenames to maintain consistent cross-referencing:

- `codex/`: English instructions and configurations for Codex CLI.
- `codex_zh/`: Chinese instructions and configurations for Codex CLI.
- `claude/`: English instructions for Claude Code.
- `claude_zh/`: Chinese instructions for Claude Code.
- `gemini/`: English instructions for Antigravity (Gemini).
- `gemini_zh/`: Chinese instructions for Antigravity (Gemini).

English is the source of truth; the `_zh` directories are translations kept in sync. Language-neutral configuration files stay byte-identical, while prose-bearing files stay semantically aligned.

## What Belongs Where

The instruction set stays small on purpose. Every rule must earn its place against one test: **is this compensating for a model limitation, or is it stating something the model cannot know?** The first kind expires as models improve; the second does not. Three tiers:

| Tier | Test | Examples |
|---|---|---|
| **Global** (`CLAUDE.md` / `AGENTS.md` / `GEMINI.md`) | A personal default that applies across projects using that tool, cannot be inferred from the repository or harness, and **remains true in a new project with no code to imitate** | Communication preferences, authorization boundaries, delivery conventions; stack or domain defaults only when genuinely universal |
| **Per repository** (that repo's own instruction file) | A fact or rule specific to this repository | Architecture, language, domain, schemas, data paths, verification commands, project coding standards |
| **Written nowhere** | The repository or host harness already states or enforces it | Toolchain and lint config (`uv.lock`, `pyproject.toml`), existing code style, generic safety and workflow behavior |

Two consequences worth stating explicitly:

- **No generic engineering virtues.** "Think before acting", "prefer simplicity", "don't fabricate", "no bare except", "vectorize instead of row loops" — current models do these by default, and the host harness often states them already. Repeating them costs context and creates conflicting phrasings.
- **No mandatory routing tables or file-handoff pipelines.** Forcing every task through `PRD → design → implement → review` is ceremony for small work, and it fights the harness's own default that the main thread handles tasks unless a subagent genuinely helps.

## Core Files & Architecture

### 1. `AGENTS.md` / `CLAUDE.md` / `GEMINI.md` (Global Agreements)

Long-lived personal defaults that belong in every session. The Codex version deliberately stays cross-repository and keeps architecture, project language, stack, domain, data, and verification details local to each repository. The Claude version currently carries additional Python, storage, and quant defaults. This divergence is intentional and should remain explicit. These global files are resident in every session, so keep them short.

### 2. `config.toml` (Runtime Configuration, Codex only)

Portable model, reasoning-effort, feature, and memory defaults. Keep machine-generated plugin, MCP, notification, and trusted-project settings out of these files. Because the current portable configuration contains no localized prose, `codex/config.toml` and `codex_zh/config.toml` should remain byte-identical.

### 3. `agents/*.toml` or `agents/*.md` (Specialized Personas)

Narrow, optional roles loaded **only** when that agent is spawned. Because they are lazily loaded, an unused agent costs nothing — so an agent file may be more specialized than the global file.

- **`product_manager`**: Focused requirements, Lite PRDs, scope, user stories, and research briefs.
- **`architect`**: Architecture, data models, interfaces, system flows, and implementation plans; no production code.
- **`code_review`**: Independent, read-only review of correctness, regressions, security, test gaps, and material risks.
- **`writer`** (Claude & Antigravity): Articles, reports, explainers, tutorials, READMEs, and notes.

Domain-specific checks inside a generic agent are written as **conditional one-liners** ("for time-series or quant code, check … when relevant") so the agent stays generally useful and the check fires only when it applies.

### 4. `templates/` (Seeds, not loaded)

Standards that need to be resident to be useful, but should be tailored per project. Nothing here is auto-loaded — it must be copied into a project to take effect.

- **`code-dev.md`**: Python implementation standards. Not an agent: writing code is the main thread's default job, so a subagent would only pay a cold-start cost.

## How It Works (Read Order)

### Codex CLI

1. **`config.toml`**: Applies portable runtime defaults.
2. **`AGENTS.md`**: Supplies global agreements, followed by more specific repository instructions.
3. **`agents/<agent>.toml`**: Loads only when that custom agent is explicitly spawned.

### Claude Code

1. **`CLAUDE.md`**: Global agreements, resident for the whole session and inherited by subagents.
2. **`agents/<agent>.md`**: Behavioral constraints, read at spawn time for that agent only.

### Antigravity (Gemini)

1. **`GEMINI.md`**: Global agreements.
2. **`agents/<agent>.md`**: Specialized persona instructions loaded when spawning custom subagents. Antigravity also provides built-in `research` and `self` subagents, plus native planning mode.

*Specific agent files override global instructions in case of conflict.*

## Deployment

1. **Codex CLI**:
   - Copy `codex/AGENTS.md` (or `codex_zh/AGENTS.md`) to your project root or `~/.codex/`.
   - Sync the `agents/` directory to `~/.codex/agents/`.
   - Merge the portable `config.toml` entries into `~/.codex/config.toml`; do not overwrite machine-generated plugin or MCP settings blindly.

2. **Claude Code**:
   - Copy `claude/CLAUDE.md` (or `claude_zh/CLAUDE.md`) to `~/.claude/`. **The filename must stay `CLAUDE.md`** — a renamed file (`CLAUDE_zh.md`) is not loaded at all.
   - Sync `agents/` to `~/.claude/agents/`.
   - Sync `templates/` to `~/.claude/templates/`; copy a template into a project when you want it to apply.

3. **Antigravity (Gemini)**:
   - Copy `gemini/GEMINI.md` (or `gemini_zh/GEMINI.md`) to `~/.gemini/`. **The filename must stay `GEMINI.md`** — a renamed file (`GEMINI_zh.md`) is not loaded at all.
   - Sync `agents/` to `~/.gemini/agents/`.
   - Sync `templates/` to `~/.gemini/templates/`; copy a template into a project when you want it to apply.
