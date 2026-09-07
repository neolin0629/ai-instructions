# AI Instructions Workspace

[中文](README_zh.md)

## Background

Working across Codex, Claude Code, and Antigravity means carrying the same preferences between tools: how to communicate, what changes are authorized, and how to deliver results. This repository keeps those agreements, reusable skills, and independent review agents together so they can be reused across projects and machines.

Global files hold personal defaults; each project's instructions hold its architecture, stack, and verification commands. The tools share a purpose, but their configurations can differ. As models improve, these files can be revised without losing useful preferences.

## Choose a version

| Tool | English | Chinese | Global file |
|---|---|---|---|
| Codex | `codex/` | `codex_zh/` | `AGENTS.md` |
| Claude Code | `claude/` | `claude_zh/` | `CLAUDE.md` |
| Antigravity | `antigravity/` | `antigravity_zh/` | `GEMINI.md` |

Choose one language per tool. English is the source; Chinese versions stay semantically aligned, and the two Codex `config.toml` files stay byte-identical.

## Setup

Back up existing files before copying, and merge any personal changes you want to keep.

### Codex

1. Copy `codex/AGENTS.md` to `~/.codex/AGENTS.md`.
2. Copy the files in `codex/agents/` to `~/.codex/agents/`.
3. Copy the skill folders in `codex/skills/` to `~/.agents/skills/`.
4. Merge `codex/config.toml` into `~/.codex/config.toml`, preserving existing MCP, plugin, and project settings.

Use `codex_zh/` instead for Chinese. The default is `gpt-6-astra` with `medium` reasoning effort. Custom agents inherit the parent model and reasoning settings unless overridden. This repository includes the `architect` and `product-manager` skills and the `code-review` agent.

### Claude Code

1. Copy `claude/CLAUDE.md` to `~/.claude/CLAUDE.md`.
2. Copy the files in `claude/agents/` to `~/.claude/agents/`.
3. Keep `claude/templates/` in `~/.claude/templates/` for reuse. Adapt a template into a project's instructions when needed; it is not loaded automatically.

Use `claude_zh/` instead for Chinese. Keep the global filename `CLAUDE.md`.

### Antigravity

1. Copy `antigravity/GEMINI.md` to `~/.gemini/GEMINI.md`.
2. Copy `antigravity/agents/code-review.md` to `~/.gemini/config/agents/code-review.md`.
3. Copy the skill folders in `antigravity/skills/` to `~/.gemini/config/skills/`.
4. Keep `antigravity/templates/` in `~/.gemini/templates/` for reuse, and adapt them into individual projects as needed. Templates are not loaded automatically.

Use `antigravity_zh/` instead for Chinese. This directory targets Antigravity, not Gemini CLI. Keep the host-required global filename `GEMINI.md` and the `~/.gemini/` installation paths. The repository directory name does not determine the host's discovery paths.

Installation paths and agent tool declarations follow the official [Antigravity rules](https://antigravity.google/docs/rules-workflows), [skills](https://antigravity.google/docs/skills/), and [subagents](https://antigravity.google/docs/subagents/) documentation. The reviewer exposes inspection and shell tools without file-editing tools; its instructions restrict commands to read-only inspection. Shell access is not a hard read-only filesystem boundary.

## Migrating an existing installation

The repository directories `gemini/` and `gemini_zh/` are now `antigravity/` and `antigravity_zh/`. Update any local copy scripts or symlinks that refer to the old repository paths.

After backing up and merging personal changes:

- Codex: replace the installed `code_review.toml` with `code-review.toml`, including its new `name`. Move the old `architect.toml` and `product_manager.toml` out of the agent discovery directory after installing their replacement skills.
- Antigravity: install the reviewer under `~/.gemini/config/agents/` and skills under `~/.gemini/config/skills/`. Move this repository's old `architect.md`, `product-manager.md`, and `writer.md` out of any agent discovery directories. Retire the previous `~/.gemini/agents/code-review.md` copy after installing the new one.
- Merge the updated global instruction file. Remove obsolete role registrations from personal configuration if you added them previously; preserve unrelated agents, skills, and settings.

These are repository templates. Editing them does not update or remove installed copies automatically.

## Everyday use

Work directly with the main agent for ordinary tasks. Skills supply methods within the current conversation; agents run independently when delegation helps. There is no required sequence.

| Capability | Codex | Antigravity | Claude Code |
|---|---|---|---|
| Requirements, scope, and acceptance criteria | `product-manager` skill | `product-manager` skill | `product-manager` agent |
| Architecture and implementation plans | `architect` skill | `architect` skill | `architect` agent |
| Independent code review | `code-review` agent | `code-review` agent | `code-review` agent |
| Articles, reports, and documentation | — | `writer` skill | `writer` agent |

Codex and Antigravity use the same agent name and filename stem: `code-review.toml` and `code-review.md`. Extensions differ because the hosts require different formats. Both retain their platform-specific guidance. Claude Code keeps its existing agent setup.

For example:

- “Use the `product-manager` skill to clarify requirements and acceptance criteria.”
- “Use the `architect` skill to propose an implementation plan without writing production code.”
- “Delegate to the `code-review` agent to independently review the current changes without editing files.”
- In Antigravity: “Use the `writer` skill to turn these notes into an article.”

Loading a skill does not require creating a subagent. In Codex CLI or the IDE extension, skills can also be named explicitly with `$architect` or `$product-manager`. See the official [Codex skills](https://learn.chatgpt.com/docs/build-skills) and [subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents) documentation.

Put project-specific rules in that project's own instruction file. After updating these templates, merge the changes into the installed copies to use them in subsequent sessions.
