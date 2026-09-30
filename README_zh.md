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
| `python-bootstrap/code-dev.md` | Python 项目规范种子，由 `python-bootstrap` 技能按项目需要改写使用 |
| `claude/settings.json` | Claude Code 的权限规则：确定性执行推送确认、敏感文件读取确认等硬约束 |
| `codex/config.toml` | Codex 模型、推理强度和记忆功能的默认设置 |
| `reference/` | 供编写指令时参考的材料，不会自动加载 |

### 专业能力

| 能力 | Codex | Antigravity | Claude Code |
|---|---|---|---|
| 需求、范围和验收标准 | `product-manager` 技能 | `product-manager` 技能 | `product-manager` 技能 |
| 架构设计与实施计划 | `architect` 技能 | `architect` 技能 | `architect` 技能 |
| 独立代码审查 | `code-review` Agent | `code-review` Agent | `code-review` Agent |
| 文章、报告和文档 | `pro-writer` 技能（仅专业文档） | `pro-writer` 技能（仅专业文档） | `pro-writer` 技能（仅专业文档） |
| 量化正确性护栏与存储分层 | `quant-guardrails` 技能 | `quant-guardrails` 技能 | `quant-guardrails` 技能 |

## 使用方法

### 安装

复制前备份已有文件，并合并需要保留的个人修改。

#### Codex

1. 将 `codex/AGENTS.md` 复制到 `~/.codex/AGENTS.md`。
2. 将 `codex/agents/` 中的文件复制到 `~/.codex/agents/`。
3. 将 `codex/skills/` 中的技能目录复制到 `~/.agents/skills/`。
4. 将 `codex/config.toml` 合并到 `~/.codex/config.toml`，保留已有 MCP、插件和项目设置。

使用中文版时，将源目录换成 `codex_zh/`。默认模型为 `gpt-6-astra`，推理强度为 `medium`。自定义 Agent 未覆盖模型或推理设置时，继承主 Agent 的设置。本仓库附带 `architect`、`product-manager`、`pro-writer`、`quant-guardrails` 技能和 `code-review` Agent。

Codex 指令参考 OpenAI 的 [Astra 指南](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra)：收窄技能描述，按任务需要阅读资料，并持续推进到用户要求的结果。技能保持简短、自包含。设计与需求的边界仅适用于各自阶段，不会阻止已授权的后续实现。

下表说明本仓库将 Claude Code 约定适配到 Codex 时的指令取舍，重点保留个人偏好和任务约定，不代表两款产品能力的普遍差异。[模型指南](https://developers.openai.com/api/docs/guides/latest-model?model=gpt-6-astra#prompting-best-practices)指出 Astra 指令遵循能力强、验证充分，也更可能寻求澄清或提前停止，因此这里明确完成边界，并限制重复检查。

| 本仓库的 Claude Code 约定 | Codex 的适配方式 |
|---|---|
| 中文正文、英文技术名称、`uv` | 移植为个人默认偏好，用户明确要求和仓库已有约定优先 |
| 量化护栏与存储分层 | 新增限定范围的技能，记录数据与执行约定；存储作为默认偏好，只验证受影响行为 |
| 可独立执行的 spec、计划，以及对照 spec 的审查 | 补充验收场景、实现交接要求和需求遗漏检查 |
| 大范围探索时委派子 Agent；同一修复失败两次后换思路或询问；非平凡或有风险的修改后要求独立审查 | 按任务决定阅读、诊断和委派，不规定重试次数，也不要求每次非平凡修改后都运行审查 Agent |
| 专业文档 `pro-writer` | 精简保留证据、论证与表达标准，合并动笔前五项检查，按需使用其他写作或排版技能 |
| 日常写作与整套 Python 初始化模板 | 普通写作和实现交给主 Agent，项目工具与规范写入项目配置或 `AGENTS.md` |
| Claude 权限规则、`@` 导入和压缩指令 | 不移植为 Codex 指令；使用宿主的权限与上下文机制，按具体环境配置 |

`quant-guardrails` 适用于量化行为及其存储，不用于所有数据库任务。存储偏好不构成迁移技术栈的授权，上线顺序也不构成交易授权。项目长期必需的具体约定写入该项目的 `AGENTS.md`，不依赖技能每次都被选中。

`pro-writer` 用于撰写或修订专业报告、白皮书、正式方案、综述和长篇技术文章，负责论证、证据与表达；用户要求的需求与架构决策仍由对应技能处理。日常消息、README 和简单润色不触发该技能。

配置附带官方 schema 指令，供编辑器校验。模型、推理强度和记忆设置值保持不变：`memories.disable_on_external_context = true` 将使用 MCP、网页搜索或工具搜索的对话排除在记忆生成之外，不会禁用记忆读取。其他设置若未在别处配置，则沿用宿主默认值，详见[配置参考](https://learn.chatgpt.com/docs/config-file/config-reference)。安装后，可分别尝试小修复、设计请求和只读审查，检查实际环境中的路由与完成行为；静态校验不能证明模型表现。

#### Claude Code

1. 将 `claude/CLAUDE.md` 复制到 `~/.claude/CLAUDE.md`。
2. 将 `claude/agents/` 中的文件复制到 `~/.claude/agents/`。
3. 将 `claude/skills/` 中的技能目录复制到 `~/.claude/skills/`。
4. 将 `claude/settings.json` 中的 `permissions` 合并到 `~/.claude/settings.json`，保留已有设置。

使用中文版时，将源目录换成 `claude_zh/`。全局文件名保持 `CLAUDE.md`。如果之前安装过旧版，删除 `~/.claude/agents/` 下的 `architect.md`、`product-manager.md`、`writer.md` 和 `~/.claude/templates/`，避免与同名技能重复。

Claude Code 指令参考官方[最佳实践](https://code.claude.com/docs/en/best-practices)：
- `CLAUDE.md` 只保留每次会话都适用、且 Claude 无法从代码推断的规则；只在部分任务相关的知识和流程放进按需加载的技能。
- 需求与设计需要和用户来回沟通，子 Agent 无法向用户提问，因此 `product-manager`、`architect`、`pro-writer`改为在主对话中运行的技能；只保留 `code-review` 作为子 Agent，利用全新上下文做对抗式审查。
- 必须每次执行、不容例外的约束交给 `settings.json` 的权限规则，而不是依赖文字提醒。
- 量化项目可在项目 `CLAUDE.md` 中加入 `@~/.claude/skills/quant-guardrails/SKILL.md`，让护栏每次会话都加载，而不依赖技能自动触发。
- `python-bootstrap` 只能手动调用（`/python-bootstrap`），用于把 Python 规范改写进项目的 `CLAUDE.md`。

安装后运行 `/context` 确认指令和技能已加载，再分别尝试小修复、设计请求和只读审查，观察行为是否符合预期。

#### Antigravity

1. 将 `antigravity/GEMINI.md` 复制到 `~/.gemini/GEMINI.md`。
2. 将 `antigravity/agents/` 中的文件复制到 `~/.gemini/config/agents/`。
3. 将 `antigravity/skills/` 中的技能目录复制到 `~/.gemini/config/skills/`。

使用中文版时，将源目录换成 `antigravity_zh/`。Antigravity 使用 `GEMINI.md` 作为全局指令文件，安装路径仍位于 `~/.gemini/` 下。如果之前安装过旧版，删除 `~/.gemini/config/skills/writer` 和 `~/.gemini/templates/`，避免与同名技能重复。本仓库附带 `architect`、`product-manager`、`pro-writer`、`quant-guardrails`、`python-bootstrap` 技能和 `code-review` Agent。

### 日常调用

普通任务直接交给主 Agent。技能在当前对话中提供工作方法；需要独立执行时再委派 Agent，没有必须走完的固定流程。

例如：

- “使用 `product-manager` 技能澄清需求和验收标准。”
- “使用 `architect` 技能给出实施方案，先不写生产代码。”
- “委派 `code-review` Agent 独立审查当前修改，不要改文件。”
- 在 Codex 中：“使用 `$quant-guardrails` 检查这次回测变更的数据可见性和执行假设。”
- 在 Antigravity、Codex 或 Claude Code 中：“使用 `pro-writer` 技能把这些笔记整理成研究报告。”
- 在 Antigravity 中：“使用 `python-bootstrap` 技能为当前 Python 项目落地编码规范。”
- 在 Claude Code 中：“/python-bootstrap” 为当前 Python 项目落地编码规范。

加载技能无需创建子 Agent。在 Codex CLI 或 IDE 扩展中，也可以用 `$architect`、`$product-manager`、`$pro-writer` 明确指定技能。机制说明见官方 [Codex 技能](https://learn.chatgpt.com/docs/build-skills)与[子 Agent](https://learn.chatgpt.com/docs/agent-configuration/subagents)文档。

项目特有的规则写入项目自己的指令文件。仓库文件需要安装到上面的目标路径才会生效。
