# AI 指令工作区

[English](README.md)

## 使用背景

在 Codex、Claude Code 和 Antigravity 之间切换时，沟通方式、修改权限和交付习惯往往需要反复交代。这个仓库把这些约定、可复用技能和独立审查 Agent 放在一起，方便在不同项目和机器上复用。

全局文件保存个人默认习惯，项目自己的指令文件记录架构、技术栈和验证命令。各工具的用途相近，配置可以有所不同。模型更新后，也可以继续调整这些文件，保留有用的偏好。

## 选择版本

| 工具 | 英文版 | 中文版 | 全局文件 |
|---|---|---|---|
| Codex | `codex/` | `codex_zh/` | `AGENTS.md` |
| Claude Code | `claude/` | `claude_zh/` | `CLAUDE.md` |
| Antigravity | `antigravity/` | `antigravity_zh/` | `GEMINI.md` |

每个工具选择一种语言即可。英文为源，中文保持语义同步，两份 Codex `config.toml` 保持字节一致。

## 安装方法

复制前备份已有文件，并合并需要保留的个人修改。

### Codex

1. 将 `codex/AGENTS.md` 复制到 `~/.codex/AGENTS.md`。
2. 将 `codex/agents/` 中的文件复制到 `~/.codex/agents/`。
3. 将 `codex/skills/` 中的技能目录复制到 `~/.agents/skills/`。
4. 将 `codex/config.toml` 合并到 `~/.codex/config.toml`，保留已有 MCP、插件和项目设置。

使用中文版时，将源目录换成 `codex_zh/`。默认模型为 `gpt-6-astra`，推理强度为 `medium`。自定义 Agent 未覆盖模型或推理设置时，继承主 Agent 的设置。本仓库附带 `architect`、`product-manager` 技能和 `code-review` Agent。

### Claude Code

1. 将 `claude/CLAUDE.md` 复制到 `~/.claude/CLAUDE.md`。
2. 将 `claude/agents/` 中的文件复制到 `~/.claude/agents/`。
3. 将 `claude/templates/` 保存到 `~/.claude/templates/` 供复用。需要时，把模板裁剪后写入项目指令；模板不会自动加载。

使用中文版时，将源目录换成 `claude_zh/`。全局文件名保持 `CLAUDE.md`。

### Antigravity

1. 将 `antigravity/GEMINI.md` 复制到 `~/.gemini/GEMINI.md`。
2. 将 `antigravity/agents/code-review.md` 复制到 `~/.gemini/config/agents/code-review.md`。
3. 将 `antigravity/skills/` 中的技能目录复制到 `~/.gemini/config/skills/`。
4. 将 `antigravity/templates/` 保存到 `~/.gemini/templates/`，按需裁剪到具体项目中使用。模板不会自动加载。

使用中文版时，将源目录换成 `antigravity_zh/`。该目录面向 Antigravity，不再面向 Gemini CLI。保留宿主要求的全局文件名 `GEMINI.md` 和 `~/.gemini/` 安装路径；仓库目录名称不决定宿主的发现路径。

安装路径和 Agent 工具声明依据官方的 [Antigravity 规则](https://antigravity.google/docs/rules-workflows)、[技能](https://antigravity.google/docs/skills/)和[子 Agent](https://antigravity.google/docs/subagents/)文档。审查 Agent 提供读取、搜索和终端工具，不提供文件编辑工具；指令将终端操作限制为只读检查。允许使用终端并不等同于文件系统层面的强制只读。

## 从旧版本迁移

仓库目录 `gemini/`、`gemini_zh/` 已改为 `antigravity/`、`antigravity_zh/`。引用旧仓库路径的本地复制脚本或符号链接也需要更新。

备份并合并个人修改后：

- Codex：将已安装的 `code_review.toml` 替换为 `code-review.toml`，同时更新其中的 `name`。安装替代技能后，将旧的 `architect.toml` 和 `product_manager.toml` 移出 Agent 发现目录。
- Antigravity：将审查 Agent 安装到 `~/.gemini/config/agents/`，技能安装到 `~/.gemini/config/skills/`。将来自本仓库的旧 `architect.md`、`product-manager.md`、`writer.md` 移出各 Agent 发现目录；新审查 Agent 安装完成后，停用旧的 `~/.gemini/agents/code-review.md` 副本。
- 合并更新后的全局指令。若曾在个人配置中额外注册旧角色，应移除失效的注册项；保留其他 Agent、技能和设置。

这里保存的是仓库模板，修改它们不会自动更新或删除已安装的副本。

## 日常使用

普通任务直接交给主 Agent。技能在当前对话中提供工作方法；需要独立执行时再委派 Agent，没有必须走完的固定流程。

| 能力 | Codex | Antigravity | Claude Code |
|---|---|---|---|
| 需求、范围和验收标准 | `product-manager` 技能 | `product-manager` 技能 | `product-manager` Agent |
| 架构设计与实施计划 | `architect` 技能 | `architect` 技能 | `architect` Agent |
| 独立代码审查 | `code-review` Agent | `code-review` Agent | `code-review` Agent |
| 文章、报告和文档 | — | `writer` 技能 | `writer` Agent |

Codex 和 Antigravity 统一使用 `code-review` 作为 Agent 名称和文件名主体，分别为 `code-review.toml`、`code-review.md`。扩展名遵循各自宿主格式，正文保留各平台的专业约定。Claude Code 继续使用现有 Agent 配置。

例如：

- “使用 `product-manager` 技能澄清需求和验收标准。”
- “使用 `architect` 技能给出实施方案，先不写生产代码。”
- “委派 `code-review` Agent 独立审查当前修改，不要改文件。”
- 在 Antigravity 中：“使用 `writer` 技能把这些笔记整理成文章。”

加载技能无需创建子 Agent。在 Codex CLI 或 IDE 扩展中，也可以用 `$architect`、`$product-manager` 明确指定技能。机制说明见官方 [Codex 技能](https://learn.chatgpt.com/docs/build-skills)与[子 Agent](https://learn.chatgpt.com/docs/agent-configuration/subagents)文档。

项目特有的规则写入该项目自己的指令文件。更新本仓库模板后，将变更合并到已安装的副本，供后续会话使用。
