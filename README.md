# AI Instructions Workspace

[中文](README_zh.md)

## Background

Working across Codex, Claude Code, and Antigravity means carrying the same preferences between tools: how to communicate, what changes are authorized, and how to deliver results. This repository keeps those agreements, reusable skills, and independent review agents together so they can be reused across projects and machines.

Global files hold personal defaults; each project's instructions hold its architecture, stack, and verification commands. Specialized skills and agents support solo development and content work when the task calls for them.

## Modules

### Tool directories

| Tool | English | Chinese | Global file |
|---|---|---|---|
| Codex | `codex/` | `codex_zh/` | `AGENTS.md` |
| Claude Code | `claude/` | `claude_zh/` | `CLAUDE.md` |
| Antigravity | `antigravity/` | `antigravity_zh/` | `GEMINI.md` |

Choose one language per tool. English and Chinese versions are semantically aligned.

### File responsibilities

| Module | Purpose |
|---|---|
| Global instructions | `AGENTS.md`, `CLAUDE.md`, and `GEMINI.md` define communication, authorization boundaries, verification, and delivery |
| `skills/` | Task-specific methods loaded as needed within the current conversation |
| `agents/` | Specialized roles for independently delegated work |
| `templates/code-dev.md` | Python project conventions for Claude Code and Antigravity, to be adapted to each project |
| `codex/config.toml` | Default Codex model, reasoning effort, and memory settings |
| `reference/` | Reference material for authoring instructions; not loaded automatically |

### Professional capabilities

| Capability | Codex | Antigravity | Claude Code |
|---|---|---|---|
| Requirements, scope, and acceptance criteria | `product-manager` skill | `product-manager` skill | `product-manager` agent |
| Architecture and implementation plans | `architect` skill | `architect` skill | `architect` agent |
| Independent code review | `code-review` agent | `code-review` agent | `code-review` agent |
| Articles, reports, and documentation | — | `writer` skill | `writer` agent |

## Usage

### Installation

Back up existing files before copying, and merge any personal changes you want to keep.

#### Codex

1. Copy `codex/AGENTS.md` to `~/.codex/AGENTS.md`.
2. Copy the files in `codex/agents/` to `~/.codex/agents/`.
3. Copy the skill folders in `codex/skills/` to `~/.agents/skills/`.
4. Merge `codex/config.toml` into `~/.codex/config.toml`, preserving existing MCP, plugin, and project settings.

Use `codex_zh/` instead for Chinese. The default is `gpt-6-astra` with `medium` reasoning effort. Custom agents inherit the parent model and reasoning settings unless overridden. This repository includes the `architect` and `product-manager` skills and the `code-review` agent.

The Codex instructions follow OpenAI's [Astra guidance](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra): narrowly scoped skill descriptions, task-relevant reading, and completion through the requested outcome. Skills remain short and self-contained. Design and requirements boundaries apply to their respective phases, so they do not halt an already-authorized implementation.

The config includes the official schema directive for editor validation. Its model, reasoning, and memory values are preserved: `memories.disable_on_external_context = true` excludes conversations using MCP, web search, or tool search from memory generation; it does not disable reading memories. Other settings use host defaults unless configured elsewhere. See the [configuration reference](https://learn.chatgpt.com/docs/config-file/config-reference). After installation, try a small fix, a design request, and a read-only review to check routing and completion in your own environment; static validation cannot prove model behavior.

#### Claude Code

1. Copy `claude/CLAUDE.md` to `~/.claude/CLAUDE.md`.
2. Copy the files in `claude/agents/` to `~/.claude/agents/`.
3. Keep `claude/templates/` in `~/.claude/templates/` for reuse. Adapt a template into a project's instructions when needed; it is not loaded automatically.

Use `claude_zh/` instead for Chinese. Keep the global filename `CLAUDE.md`.

#### Antigravity

1. Copy `antigravity/GEMINI.md` to `~/.gemini/GEMINI.md`.
2. Copy `antigravity/agents/code-review.md` to `~/.gemini/config/agents/code-review.md`.
3. Copy the skill folders in `antigravity/skills/` to `~/.gemini/config/skills/`.
4. Keep `antigravity/templates/` in `~/.gemini/templates/` for reuse, and adapt them into individual projects as needed. Templates are not loaded automatically.

Use `antigravity_zh/` instead for Chinese. Antigravity uses `GEMINI.md` for global instructions and installation paths under `~/.gemini/`.

### Everyday use

Work directly with the main agent for ordinary tasks. Skills supply methods within the current conversation; agents run independently when delegation helps. There is no required sequence.

For example:

- “Use the `product-manager` skill to clarify requirements and acceptance criteria.”
- “Use the `architect` skill to propose an implementation plan without writing production code.”
- “Delegate to the `code-review` agent to independently review the current changes without editing files.”
- In Antigravity: “Use the `writer` skill to turn these notes into an article.”

Loading a skill does not require creating a subagent. In Codex CLI or the IDE extension, skills can also be named explicitly with `$architect` or `$product-manager`. See the official [Codex skills](https://learn.chatgpt.com/docs/build-skills) and [subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents) documentation.

Put project-specific rules in that project's own instruction file. Adapt files in `templates/` manually; repository files must be installed at the paths above to take effect.
