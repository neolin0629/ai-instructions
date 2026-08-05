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

## Core Files & Architecture

The instruction architecture separates durable global agreements, portable runtime defaults, and optional specialized agents. Repository-specific rules belong in each repository rather than in the global files.

### 1. `AGENTS.md` / `CLAUDE.md` / `GEMINI.md` (Global Agreements)
These files define durable defaults such as communication, change boundaries, safety, verification, and final-response expectations. 

### 2. `config.toml` (Runtime Configuration)
The Codex template contains portable defaults for the model, reasoning effort, personality, subagent concurrency, and memories. 

### 3. `agents/*.toml` or `agents/*.md` (Specialized Personas)
These files define narrow, optional roles that are loaded only when the corresponding custom agent is spawned.

- **`product_manager`**: Focused requirements, Lite PRDs, scope, user stories, and research briefs.
- **`architect`**: Architecture, data models, interfaces, system flows, and implementation plans; no production code.
- **`code_review`**: Independent, read-only review of correctness, regressions, security, test gaps, and material risks.

## How It Works (Read Order)

### Codex CLI
1. **`config.toml`**: Applies portable runtime defaults.
2. **`AGENTS.md`**: Supplies global agreements, followed by more specific repository instructions.
3. **`agents/<agent>.toml`**: Loads only when that custom agent is explicitly spawned.

### Claude Code
1. **`CLAUDE.md`**: Foundational rules and agent dispatch logic.
2. **`agents/<agent>.md`**: Behavioral constraints for the specific agent.

### Antigravity (Gemini)
1. **`GEMINI.md`**: Foundational rules and agent dispatch logic.

*Specific agent files override global instructions in case of conflict.*

## Deployment

1. **Codex CLI**:
   - Copy `codex/AGENTS.md` (or `codex_zh/AGENTS.md`) to your project root or `~/.codex/`.
   - Sync the `agents/` directory to `~/.codex/agents/`.
   - Merge the portable `config.toml` entries into `~/.codex/config.toml`; do not overwrite machine-generated plugin or MCP settings blindly.

2. **Claude Code**:
   - Copy `claude/CLAUDE.md` (or `claude_zh/CLAUDE.md`) to your project root.
   - Sync the `agents/` directory to the same location under `~/.claude/`.

3. **Antigravity (Gemini)**:
   - Copy `gemini/GEMINI.md` (or `gemini_zh/GEMINI.md`) to your project root.

## Modifying Instructions

- **Global rules**: Keep only cross-repository working agreements in `AGENTS.md`; put language, domain, architecture, and verification rules in each repository.
- **Agent behavior**: Update the corresponding file in `agents/`.
- **New Agents**: Add a narrow `.toml`/`.md` file to the appropriate `agents/` directory.
