# AI 指令工作区

[English](README.md)

## 使用背景

在 Codex、Claude Code 和 Antigravity 之间切换时，沟通方式、修改权限和交付习惯往往需要反复交代。这个仓库把这些约定、可复用技能和独立审查 Agent 放在一起，方便在不同项目和机器上复用。

全局文件保存个人默认习惯，项目自己的指令文件记录架构、技术栈和验证命令。专业技能和 Agent 按任务需要使用，适合个人开发与内容工作。

## 模块介绍

### 工具目录

| 工具 | 英文版 | 中文版 | 全局文件 |
|---|---|---|---|
| Codex | `codex/` | `codex_zh/` | `AGENTS.md` |
| Claude Code | `claude/` | `claude_zh/` | `CLAUDE.md` |
| Antigravity | `antigravity/` | `antigravity_zh/` | `GEMINI.md` |

每个工具选择一种语言即可，中英文版本语义对应。

### 文件职责

| 模块 | 用途 |
|---|---|
| 全局指令 | `AGENTS.md`、`CLAUDE.md`、`GEMINI.md` 保存沟通、授权边界和验证交付约定 |
| `skills/` | 按需加载的专业工作方法，在当前对话中使用 |
| `agents/` | 可委派的专业角色，用于独立执行任务 |
| `templates/code-dev.md` | Claude Code 和 Antigravity 的 Python 项目规范种子，按项目需要裁剪使用 |
| `codex/config.toml` | Codex 模型、推理强度和记忆功能的默认设置 |
| `reference/` | 供编写指令时参考的材料，不会自动加载 |

### 专业能力

| 能力 | Codex | Antigravity | Claude Code |
|---|---|---|---|
| 需求、范围和验收标准 | `product-manager` 技能 | `product-manager` 技能 | `product-manager` Agent |
| 架构设计与实施计划 | `architect` 技能 | `architect` 技能 | `architect` Agent |
| 独立代码审查 | `code-review` Agent | `code-review` Agent | `code-review` Agent |
| 文章、报告和文档 | — | `writer` 技能 | `writer` Agent |

## 使用方法

### 安装

复制前备份已有文件，并合并需要保留的个人修改。

#### Codex

1. 将 `codex/AGENTS.md` 复制到 `~/.codex/AGENTS.md`。
2. 将 `codex/agents/` 中的文件复制到 `~/.codex/agents/`。
3. 将 `codex/skills/` 中的技能目录复制到 `~/.agents/skills/`。
4. 将 `codex/config.toml` 合并到 `~/.codex/config.toml`，保留已有 MCP、插件和项目设置。

使用中文版时，将源目录换成 `codex_zh/`。默认模型为 `gpt-6-astra`，推理强度为 `medium`。自定义 Agent 未覆盖模型或推理设置时，继承主 Agent 的设置。本仓库附带 `architect`、`product-manager` 技能和 `code-review` Agent。

Codex 指令参考 OpenAI 的 [Astra 指南](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra)：收窄技能描述，按任务需要阅读资料，并持续推进到用户要求的结果。技能保持简短、自包含。设计与需求的边界仅适用于各自阶段，不会阻止已授权的后续实现。

配置附带官方 schema 指令，供编辑器校验。模型、推理强度和记忆设置值保持不变：`memories.disable_on_external_context = true` 将使用 MCP、网页搜索或工具搜索的对话排除在记忆生成之外，不会禁用记忆读取。其他设置若未在别处配置，则沿用宿主默认值，详见[配置参考](https://learn.chatgpt.com/docs/config-file/config-reference)。安装后，可分别尝试小修复、设计请求和只读审查，检查实际环境中的路由与完成行为；静态校验不能证明模型表现。

#### Claude Code

1. 将 `claude/CLAUDE.md` 复制到 `~/.claude/CLAUDE.md`。
2. 将 `claude/agents/` 中的文件复制到 `~/.claude/agents/`。
3. 将 `claude/templates/` 保存到 `~/.claude/templates/` 供复用。需要时，把模板裁剪后写入项目指令；模板不会自动加载。

使用中文版时，将源目录换成 `claude_zh/`。全局文件名保持 `CLAUDE.md`。

#### Antigravity

1. 将 `antigravity/GEMINI.md` 复制到 `~/.gemini/GEMINI.md`。
2. 将 `antigravity/agents/code-review.md` 复制到 `~/.gemini/config/agents/code-review.md`。
3. 将 `antigravity/skills/` 中的技能目录复制到 `~/.gemini/config/skills/`。
4. 将 `antigravity/templates/` 保存到 `~/.gemini/templates/`，按需裁剪到具体项目中使用。模板不会自动加载。

使用中文版时，将源目录换成 `antigravity_zh/`。Antigravity 使用 `GEMINI.md` 作为全局指令文件，安装路径仍位于 `~/.gemini/` 下。

### 日常调用

普通任务直接交给主 Agent。技能在当前对话中提供工作方法；需要独立执行时再委派 Agent，没有必须走完的固定流程。

例如：

- “使用 `product-manager` 技能澄清需求和验收标准。”
- “使用 `architect` 技能给出实施方案，先不写生产代码。”
- “委派 `code-review` Agent 独立审查当前修改，不要改文件。”
- 在 Antigravity 中：“使用 `writer` 技能把这些笔记整理成文章。”

加载技能无需创建子 Agent。在 Codex CLI 或 IDE 扩展中，也可以用 `$architect`、`$product-manager` 明确指定技能。机制说明见官方 [Codex 技能](https://learn.chatgpt.com/docs/build-skills)与[子 Agent](https://learn.chatgpt.com/docs/agent-configuration/subagents)文档。

项目特有的规则写入项目自己的指令文件。`templates/` 中的文件需要手动裁剪后使用；仓库文件需要安装到上面的目标路径才会生效。
