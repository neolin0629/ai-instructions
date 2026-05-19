# AI 指令工作区 (AI Instructions Workspace)

本仓库是一个集中的工作区，用于管理、标准化和部署适用于不同 CLI 工具（如 Codex CLI 和 Claude Code）的 AI Agent 指令与系统提示词（System Prompts）。

## 目录结构

仓库按工具和语言进行组织：

- `codex/`: Codex CLI 的英文指令和配置。
- `codex_zh/`: Codex CLI 指令的中文翻译版本。
- `claude/`: Claude Code 的英文指令。
- `claude_zh/`: Claude Code 的中文指令。

## 核心文件与架构

指令架构采用分层设计，以在保持全局一致性的同时，允许特定任务的专业化定制。

### 1. `AGENTS.md` / `CLAUDE.md` (通用指令)
这是**全局入口点**和基础规则手册。它定义了：
- **核心行为**: 沟通语言、基本原则（如“先思考再编码”、“简单至上”）。
- **Agent 路由**: 系统如何根据用户任务决定使用哪个专业 Agent。
- **全局基线**: 通用编码标准（如 Python 3.10+、类型提示）、数据处理规则和 Git 安全协议。
- **输出契约**: 最终回复的预期格式和内容。

**用法**: 
- **Codex**: 将 `AGENTS.md` 放置在项目根目录或工具的全局配置目录中（例如 `~/.codex/AGENTS.md`）。
- **Claude Code**: 将 `CLAUDE.md` 放置在项目根目录，或作为自定义指令文件使用。

### 2. `config.toml` (Codex 配置与路由)
此文件（专门针对 Codex）充当注册表和路由器的角色。
- **Agent 注册表**: 列出可用的 Agent 并指向其特定的配置文件。
- **关键词路由**: 定义自动触发特定 Agent 的中英文关键词（例如，“写代码”将触发 `code-dev`）。
- **工作流**: 为复杂任务定义多 Agent 协作序列（例如，先执行 `code-dev` 随后执行 `code-review`）。

### 3. `agents/*.toml` 或 `agents/*.md` (特定 Agent 角色)
这些文件定义了特定角色的细颗粒度行为、自检清单和优先级。

- **`code-dev` (代码开发 Agent)**:
  - **核心**: 实现、调试、数据处理、量化回测。
  - **规则**: 正确性 > 性能 > 简单性。对 pandas 向量化、缺失数据处理以及量化任务中避免前视偏差有严格要求。
- **`code-review` (代码审查 Agent)**:
  - **核心**: 独立审计、找 Bug、风险评估。
  - **规则**: 只读（从不修改代码）。优先考虑正确性和边界条件。发现严重问题后立即停止并反馈，而不是用大量次要问题淹没用户。
- **`writer` (内容写作 Agent)**:
  - **核心**: 文章、社交媒体帖子、润色、提纲。
  - **规则**: 强调排版规范、信息密度和事实核验。拒绝模板化输出和编造数据。

## 工作原理 (读取顺序)

### Codex CLI
1. **`AGENTS.md`**: 建立基本规则和整体工作流。
2. **`config.toml`**: 分析你的提示词，以确定是否应由特定 Agent（`code-dev`、`code-review`、`writer`）主导。
3. **`agents/<agent>.toml`**: 加载所选 Agent 的详细人格设定和检查清单。

### Claude Code
1. **`CLAUDE.md`**: 基础规则和 Agent 分发系统。
2. **`agents/<agent>.md`**: 特定 Agent 的详细人格设定和行为约束。

*如果规则发生冲突，特定 Agent 文件的优先级高于全局指令。*

## 部署与同步

要将这些指令应用到你的本地环境：

1. **Codex CLI**:
   - 将 `codex/AGENTS.md` 复制到你的本地 Codex 目录（例如 `C:\Users\develop\.codex\AGENTS.md` 或 `~/.codex/AGENTS.md`）。
   - （可选）将 `config.toml` 和 `agents/` 目录同步到相应的配置路径。

2. **Claude Code**:
   - 将 `claude/CLAUDE.md` 复制到你的项目根目录。
   - 将 `claude/agents/` 目录下的内容复制到项目的 Agent 指令路径，或在会话上下文中引用。

3. **中文版本**:
   - 如果你希望 Agent 主要按中文规则处理，请使用 `_zh` 目录中的文件（例如使用 `codex_zh/agents_zh.md` 或 `claude_zh/CLAUDE_zh.md`）。


## 修改指令

- **全局变更**: 编辑 `AGENTS.md`（例如更改最低 Python 版本，更新 Git 提交风格）。
- **特定角色变更**: 编辑 `agents/` 目录中的相应文件（例如为 `code-review` 增加新的数据库检查清单）。
- **添加新能力**: 在 `agents/` 中创建一个新的 Agent 文件，并在 `config.toml` 中注册其触发关键词。
