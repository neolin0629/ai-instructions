# AI Instructions Workspace

This repository serves as a centralized workspace for managing, standardizing, and deploying AI agent instructions and system prompts for different CLI tools (like Codex CLI and Claude Code).

## Directory Structure

The repository is organized by tool and language:

- `codex/`: English instructions and configurations for Codex CLI.
- `codex_zh/`: Chinese translations of the Codex CLI instructions.
- `claude/`: English instructions for Claude Code.
- `claude_zh/`: Chinese instructions for Claude Code.

## Core Files & Architecture

The instruction architecture follows a hierarchical design to ensure consistency while allowing task-specific specialization.

### 1. `AGENTS.md` / `CLAUDE.md` (Universal Instructions)
This is the **global entry point** and foundational rulebook. It defines:
- **Core Behavior**: Communication language, fundamental principles (e.g., "Think before coding", "Simplicity first").
- **Agent Routing**: How the system determines which specialized agent to use based on the user's task.
- **Global Baselines**: Universal coding standards (e.g., Python 3.10+, Type hints), data processing rules, and Git safety protocols.
- **Output Contract**: The expected format and content of the final response.

**Usage:** 
- **Codex**: Place `AGENTS.md` in the root of the project or the tool's global configuration directory (e.g., `~/.codex/AGENTS.md`).
- **Claude Code**: Place `CLAUDE.md` in the root of the project or use it as a custom instruction file.

### 2. `config.toml` (Codex Configuration & Routing)
This file (specifically for Codex) acts as the registry and router.
- **Agent Registry**: Lists available agents and points to their specific configuration files.
- **Keyword Routing**: Defines triggers (both English and Chinese) that automatically activate specific agents based on user input (e.g., "write code" triggers `code-dev`).
- **Workflows**: Defines multi-agent sequences for complex tasks (e.g., `code-dev` followed by `code-review`).

### 3. `agents/*.toml` or `agents/*.md` (Specific Agent Personas)
These files define the fine-grained behavior, checklists, and priorities for specific roles.

- **`code-dev` (Code Development Agent)**:
  - **Focus**: Implementation, debugging, data processing, quant backtesting.
  - **Rules**: Correctness > Performance > Simplicity. Strict rules on pandas vectorization, handling missing data, and avoiding look-ahead bias in quant tasks.
- **`code-review` (Code Review Agent)**:
  - **Focus**: Independent auditing, finding bugs, assessing risk.
  - **Rules**: Read-only (never edits code). Prioritizes correctness and edge cases. Stops after finding critical issues rather than overwhelming the user.
- **`writer` (Content Writing Agent)**:
  - **Focus**: Articles, social media posts, polishing, outlines.
  - **Rules**: Emphasizes typography, information density, and factual verification. Rejects templated outputs and hallucinated data.

## How It Works (The Read Order)

### Codex CLI
1. **`AGENTS.md`**: Establishes the ground rules and overall workflow.
2. **`config.toml`**: Analyzes your prompt to determine if a specific agent (`code-dev`, `code-review`, `writer`) should take the lead.
3. **`agents/<agent>.toml`**: Loads the detailed persona and checklists for the selected agent.

### Claude Code
1. **`CLAUDE.md`**: Foundational rules and agent dispatch system.
2. **`agents/<agent>.md`**: Detailed persona and behavioral constraints for the specific agent.

*If rules conflict, the specific agent file overrides the global instructions.*

## Deployment & Synchronization

To apply these instructions to your local environment:

1. **Codex CLI**:
   - Copy `codex/AGENTS.md` to your local Codex directory (e.g., `C:\Users\develop\.codex\AGENTS.md` or `~/.codex/AGENTS.md`).
   - (Optional) Sync the `config.toml` and `agents/` directory to the corresponding configuration paths.

2. **Claude Code**:
   - Copy `claude/CLAUDE.md` to your project root.
   - Copy `claude/agents/` contents to your project's agent instruction path or reference them in your session context.

3. **Chinese Versions**:
   - Use files from the `_zh` directories for a Chinese-first experience (e.g., `codex_zh/agents_zh.md` or `claude_zh/CLAUDE_zh.md`).

## Modifying Instructions

- **Global changes**: Edit `AGENTS.md` (e.g., changing the minimum Python version, updating Git commit styles).
- **Role-specific changes**: Edit the corresponding file in the `agents/` directory (e.g., adding a new database checklist to `code-review`).
- **Adding new capabilities**: Create a new agent file in `agents/` and register its trigger keywords in `config.toml`.
