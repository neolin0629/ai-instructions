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
| Antigravity | `antigravity/` | `antigravity_zh/` | `AGENTS.md` |

Choose one language per tool. English and Chinese versions are semantically aligned.

### File responsibilities

| Module | Purpose |
|---|---|
| Global instructions | `AGENTS.md` and `CLAUDE.md` define communication, authorization boundaries, verification, and delivery |
| `skills/` | Task-specific methods loaded as needed within the current conversation |
| `agents/` | Specialized roles for independently delegated work |
| `python-bootstrap/code-dev.md` | Python project conventions seed, adapted to individual projects by the `python-bootstrap` skill |
| `claude/settings.json` | Claude Code permission rules that enforce hard constraints deterministically, such as confirming pushes and sensitive-file reads |
| `codex/config.toml` | Codex model, reasoning, memory, enabled Context7, and disabled social plugin defaults |
| `codex/marketplace.json` + `plugins/social-mcp/` | Personal marketplace template and on-demand social MCP plugin |
| `antigravity/settings.json` | Antigravity CLI (`agy`) defaults, sandbox execution policy, model selection (`Gemini 4 Carbon`), and command prefix permissions |
| `antigravity/mcp_config.json` | Antigravity resident global MCP configuration (`context7` only) |
| `antigravity/plugins/social-mcp/` | On-demand social media MCP plugin (`xiaohongshu` and `social-superpowers`, disabled by default) |
| `reference/` | Reference material for authoring instructions; not loaded automatically |

### Professional capabilities

| Capability | Codex | Antigravity | Claude Code |
|---|---|---|---|
| Requirements, scope, and acceptance criteria | `product-manager` skill | `product-manager` skill | `product-manager` skill |
| Architecture and implementation plans | `architect` skill | `architect` skill | `architect` skill |
| Independent code review | `code-review` agent | `code-review` agent | `code-review` agent |
| Articles, reports, and documentation | `pro-writer` skill (professional documents only) | `pro-writer` skill (professional documents only) | `pro-writer` skill (professional documents only) |
| Quant correctness guardrails and storage tiers | `quant-guardrails` skill | `quant-guardrails` skill | `quant-guardrails` skill |

## Usage

### Installation

Back up existing files before copying, and merge any personal changes you want to keep.

#### Codex

1. Copy `codex/AGENTS.md` to `~/.codex/AGENTS.md`.
2. Copy the files in `codex/agents/` to `~/.codex/agents/`.
3. Copy the skill folders in `codex/skills/` to `~/.agents/skills/`.
4. Merge `codex/config.toml` into `~/.codex/config.toml`, preserving existing MCP, plugin, and project settings.

Use `codex_zh/` instead for Chinese. The default is `gpt-6-astra` with `medium` reasoning effort. Custom agents inherit the parent model and reasoning settings unless overridden. This repository includes the `architect`, `product-manager`, `pro-writer`, and `quant-guardrails` skills and the `code-review` agent.

The Codex instructions follow OpenAI's [Astra guidance](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra): narrowly scoped skill descriptions, task-relevant reading, and completion through the requested outcome. Skills remain short and self-contained. Design and requirements boundaries apply to their respective phases, so they do not halt an already-authorized implementation.

The table below describes this repository's instruction choices when adapting Claude Code conventions for Codex, with a focus on personal preferences and task contracts. It is not a general comparison of the products' capabilities. The [model guidance](https://developers.openai.com/api/docs/guides/latest-model?model=gpt-6-astra#prompting-best-practices) describes strong instruction following, thorough verification, and a tendency to seek clarification or stop early; these call for clear completion boundaries and proportionate checks.

| This repository's Claude Code convention | Codex adaptation |
|---|---|
| Chinese prose, English technical names, `uv` | Carry over as personal defaults; explicit requests and existing repository conventions take precedence |
| Quant guardrails and storage tiers | Add a scoped skill; record data and execution contracts, treat storage choices as defaults, and verify only affected behavior |
| Self-contained specs, plans, and spec-aware reviews | Carry over acceptance scenarios, implementation handoffs, and checks for missing requirements |
| Delegate broad exploration; after two failed attempts at the same fix, change approach or ask; require independent review after non-trivial or risky changes | Choose reading, diagnosis, and delegation according to the task; do not prescribe a retry count or require a review agent after every non-trivial change |
| Professional `pro-writer` | Carry over evidence, argument, and expression standards in a concise skill; condense the five prewriting checks and use additional writing or typography skills as needed |
| Everyday writing and the full Python bootstrap template | Leave ordinary writing and implementation with the main agent; keep project-specific tooling and standards in project configuration or `AGENTS.md` |
| Claude permission rules, `@` imports, and compaction instructions | Do not port as Codex instructions; use the host's permission and context mechanisms, with environment-specific configuration when needed |

`quant-guardrails` applies to quant behavior and its storage, not every database task. Its storage defaults do not authorize a stack migration; its rollout order does not authorize trading. For recurring project-specific requirements, record the actual contracts in that project's `AGENTS.md` instead of relying on a skill being selected every time.

`pro-writer` drafts or revises professional reports, white papers, proposals, literature reviews, and long-form technical articles. It handles argument, evidence, and expression; requirements and architecture decisions remain with their respective skills when requested. Routine messages, READMEs, and quick polishing do not trigger it.

The config includes the official schema directive for editor validation. Its model, reasoning, and memory values are preserved: `memories.disable_on_external_context = true` excludes conversations using MCP, web search, or tool search from memory generation; it does not disable reading memories. Other settings use host defaults unless configured elsewhere. See the [configuration reference](https://learn.chatgpt.com/docs/config-file/config-reference). After installation, try a small fix, a design request, and a read-only review to check routing and completion in your own environment; static validation cannot prove model behavior.

##### MCP and the on-demand social plugin

Context7 is enabled by default through `npx -y @upstash/context7-mcp`. It requires Node.js / npm and network access for the initial package download. Preserve existing local Context7 authentication settings; never copy real credentials into this repository. Leave desktop components such as `node_repl` and `computer-use` to the host rather than copying application paths and environment variables.

Install the social plugin using the official [plugin format and personal marketplace mechanism](https://developers.openai.com/plugins/build/plugins). Substitute `codex_zh/` for Chinese:

1. Copy `codex/plugins/social-mcp/` to `~/.codex/plugins/social-mcp/`.
2. Install `codex/marketplace.json` at `~/.agents/plugins/marketplace.json`. If that file exists, merge the plugin entry without replacing other entries. The template path is relative to the personal marketplace root, `~`. If you keep an existing marketplace name, change `social-mcp@ai-instructions` in the config to `social-mcp@your-name` as well.
3. Ensure the plugin settings from `config.toml` are merged with `enabled = false`. Restart the desktop host, find and install `social-mcp` in the plugin directory, and verify it remains disabled after installation. Copying the plugin folder alone does not complete installation.
4. Remove or disable old global `[mcp_servers]` entries for `xiaohongshu` and `social-superpowers` to avoid duplicate connections or a global connection remaining enabled while the plugin is disabled. Preserve other MCP settings.
5. When needed, set `enabled = true` under `[plugins."social-mcp@ai-instructions"]`; restore `false` afterward. Restart the host or CLI session for changes to take effect. Use `/mcp` in the CLI to inspect connections.

The plugin configures connections; it does not install or start backend services. Xiaohongshu requires a service running at `http://localhost:18060/mcp` in the MCP execution environment and a logged-in account. Social Superpowers connects to `https://superpowers.social/mcp` and needs network access and any authentication required by that service. For another machine or remote executor, adjust the source plugin's `mcp.json` endpoints before reinstalling or refreshing its cached copy. Disabling the plugin does not stop a Xiaohongshu process you run separately.

The plugin contains no credentials and grants no publishing or interaction authorization; posting, commenting, and liking still require user authorization. The whole plugin defaults to disabled once the configuration is merged and installation is complete. See [MCP configuration](https://learn.chatgpt.com/docs/extend/mcp?surface=cli).

#### Claude Code

1. Copy `claude/CLAUDE.md` to `~/.claude/CLAUDE.md`.
2. Copy the files in `claude/agents/` to `~/.claude/agents/`.
3. Copy the skill folders in `claude/skills/` to `~/.claude/skills/`.
4. Merge the `permissions` block of `claude/settings.json` into `~/.claude/settings.json`, preserving existing settings.

Use `claude_zh/` instead for Chinese. Keep the global filename `CLAUDE.md`. If an older version is installed, delete `architect.md`, `product-manager.md`, and `writer.md` from `~/.claude/agents/`, and delete `~/.claude/templates/`, so they don't duplicate the skills of the same name.

The Claude Code instructions follow the official [best practices](https://code.claude.com/docs/en/best-practices):
- `CLAUDE.md` keeps only rules that apply in every session and that Claude cannot infer from code; knowledge and procedures relevant to some tasks live in on-demand skills.
- Requirements and design work need back-and-forth with the user, and subagents cannot ask the user questions, so `product-manager`, `architect`, and `pro-writer` are skills that run in the main conversation. Only `code-review` remains a subagent, using a fresh context for adversarial review.
- Constraints that must hold every time, with no exceptions, go into `settings.json` permission rules instead of relying on written reminders.
- A quant project can add `@~/.claude/skills/quant-guardrails/SKILL.md` to its own `CLAUDE.md` so the guardrails load every session instead of depending on automatic skill invocation.
- `python-bootstrap` is manual-only (`/python-bootstrap`) and adapts the Python conventions into a project's `CLAUDE.md`.

After installation, run `/context` to confirm the instructions and skills loaded, then try a small fix, a design request, and a read-only review to check behavior.

#### Antigravity

1. Copy or symlink `antigravity/AGENTS.md` to `~/.gemini/AGENTS.md`.
2. Copy or symlink the files in `antigravity/agents/` to `~/.gemini/config/agents/`.
3. Copy or symlink the skill folders in `antigravity/skills/` to `~/.gemini/config/skills/`.
4. Copy or symlink `antigravity/mcp_config.json` to `~/.gemini/config/mcp_config.json`, copy or symlink `antigravity/plugins/social-mcp` to `~/.gemini/config/plugins/social-mcp`, and merge or symlink `antigravity/settings.json` into `~/.gemini/antigravity-cli/settings.json`.

Use `antigravity_zh/` instead for Chinese. Antigravity natively discovers `AGENTS.md` for both global (`~/.gemini/AGENTS.md`) and project-level instructions. The `social-mcp` plugin ships disabled so coding sessions never probe local social-media endpoints; enable it when needed with `agy plugin enable social-mcp` (or via `/plugin`), and turn it back off with `agy plugin disable social-mcp`. Individual global MCP servers can also be toggled via `agy mcp enable <name>` / `agy mcp disable <name>`. If an older version is installed, delete `~/.gemini/GEMINI.md`, `~/.gemini/config/skills/writer`, and `~/.gemini/templates/`, and when merging manually, remove old global social-media entries from `mcp_config.json` as well as legacy `//**/.env*` and trailing ` *` rules from `settings.json` to avoid duplicating or conflicting with updated rules and plugins. This repository includes the `architect`, `product-manager`, `pro-writer`, `quant-guardrails`, and `python-bootstrap` skills, the `code-review` agent, and the on-demand `social-mcp` plugin.

### Everyday use

Work directly with the main agent for ordinary tasks. Skills supply methods within the current conversation; agents run independently when delegation helps. There is no required sequence.

For example:

- “Use the `product-manager` skill to clarify requirements and acceptance criteria.”
- “Use the `architect` skill to propose an implementation plan without writing production code.”
- “Delegate to the `code-review` agent to independently review the current changes without editing files.”
- In Codex: “Use `$quant-guardrails` to check this backtest change's data visibility and execution assumptions.”
- In Antigravity, Codex, or Claude Code: “Use the `pro-writer` skill to turn these notes into a research report.”
- In Antigravity: “Use the `python-bootstrap` skill to adapt Python conventions into the current project.”
- In Claude Code: “/python-bootstrap” to adapt the Python conventions into the current project.

Loading a skill does not require creating a subagent. In Codex CLI or the IDE extension, skills can also be named explicitly with `$architect`, `$product-manager`, or `$pro-writer`. See the official [Codex skills](https://learn.chatgpt.com/docs/build-skills) and [subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents) documentation.

Put project-specific rules in that project's own instruction file. Repository files must be installed at the paths above to take effect.
