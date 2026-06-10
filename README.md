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

The instruction architecture follows a hierarchical design where the entry file handles both global rules and the routing of specialized sub-agents.

### 1. `AGENTS.md` / `CLAUDE.md` / `GEMINI.md` (Universal Entry & Routing)
This is the **global entry point** and primary rulebook. It handles:
- **Core Behavior**: Communication language, fundamental principles (e.g., "Think before coding", "Simplicity first").
- **Agent Routing & Registry**: Defines how the system identifies and activates specialized agents (`code_dev`, `code_review`, `writer`) based on task types and keywords.
- **Global Baselines**: Universal coding standards (e.g., Python 3.10+), data processing rules, and Git safety protocols.
- **Output Contract**: The expected format and verification requirements for final responses.

### 2. `config.toml` (Runtime Configuration)
For Codex, this file is strictly for **system and performance settings**:
- **Concurrency**: `max_threads` for parallel task execution.
- **Depth**: `max_depth` for agent recursion limits.
- *Note: Agent registration and keyword routing have been moved to `AGENTS.md` for better central management.*

### 3. `agents/*.toml` or `agents/*.md` (Specialized Personas)
These files define the fine-grained behavior, checklists, and technical priorities for specific roles.

- **`product-manager` (Product Management)**:
  - Requirement convergence, PRD authoring, and market/competitive research. Only writes PRD documents.
- **`architect` (System Architecture)**:
  - System design, technology selection, data modeling, and task breakdown. Only writes design documents.
- **`code_dev` / `code-dev` (Code Development)**:
  - Implementation, debugging, performance optimization, and quant backtesting.
  - Correctness > Performance > Simplicity.
- **`code_review` / `code-review` (Code Review)**:
  - Independent auditing and risk assessment. Read-only; emphasizes edge cases and correctness.
- **`writer` (Content Writing)**:
  - General document writing (articles, reports, explainers, docs, notes). Emphasizes typography and factual integrity.

## How It Works (Read Order)

### Codex CLI
1. **`AGENTS.md`**: Establishes ground rules and performs **Agent Routing**.
2. **`config.toml`**: Applies runtime limits (threads/depth).
3. **`agents/<agent>.toml`**: Loads the selected agent's specific instructions.

### Claude Code
1. **`CLAUDE.md`**: Foundational rules and agent dispatch logic.
2. **`agents/<agent>.md`**: Behavioral constraints for the specific agent.

### Antigravity (Gemini)
1. **`GEMINI.md`**: Foundational rules and agent dispatch logic.

*Specific agent files override global instructions in case of conflict.*

## Deployment

1. **Codex CLI**:
   - Copy `codex/AGENTS.md` (or `codex_zh/AGENTS.md`) to your project root or `~/.codex/`.
   - Sync `config.toml` and the `agents/` directory to the same location.

2. **Claude Code**:
   - Copy `claude/CLAUDE.md` (or `claude_zh/CLAUDE.md`) to your project root.
   - Sync the `agents/` directory to the same location under `~/.claude/`.

3. **Antigravity (Gemini)**:
   - Copy `gemini/GEMINI.md` (or `gemini_zh/GEMINI.md`) to your project root.

## Modifying Instructions

- **Global rules**: Update `AGENTS.md` (e.g., changing coding standards).
- **Agent behavior**: Update the corresponding file in `agents/`.
- **New Agents**: Add a `.toml`/`.md` file to `agents/` and register it in the routing table within `AGENTS.md`.
