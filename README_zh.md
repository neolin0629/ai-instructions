# AI 指令工作区 (AI Instructions Workspace)

本仓库是一个集中的工作区，用于管理、标准化和部署适用于不同 CLI 工具（如 Codex CLI 和 Claude Code）的 AI Agent 指令与系统提示词（System Prompts）。

## 目录结构

仓库按工具和语言进行组织。为了保证引用的一致性，各语言目录下的文件名保持统一：

- `codex/`: Codex CLI 的英文指令和配置。
- `codex_zh/`: Codex CLI 的中文指令和配置。
- `claude/`: Claude Code 的英文指令。
- `claude_zh/`: Claude Code 的中文指令。
- `gemini/`: Antigravity (Gemini) 的英文指令。
- `gemini_zh/`: Antigravity (Gemini) 的中文指令。

## 核心文件与架构

指令架构将长期有效的全局约定、可移植的运行时默认值和可选的专业 Agent 分开管理。仓库特有的规则应放在对应仓库内，而不是写入全局文件。

### 1. `AGENTS.md` / `CLAUDE.md` / `GEMINI.md`（全局约定）
这些文件定义沟通方式、变更边界、安全、验证和最终回复等长期有效的默认约定。

### 2. `config.toml` (运行时配置)
Codex 模板包含模型、推理强度、personality、子 Agent 并发数和 memories 的可移植默认值。

### 3. `agents/*.toml` 或 `agents/*.md` (专业角色设定)
这些文件定义范围明确的可选角色，只有生成对应的自定义 Agent 时才会加载。

- **`product_manager`**：聚焦需求、Lite PRD、范围、用户故事和研究简报。
- **`architect`**：负责架构、数据模型、接口、系统流程和实施计划，不编写生产代码。
- **`code_review`**：独立、只读地检查正确性、回归、安全、测试缺口和实质性风险。

## 工作原理 (读取顺序)

### Codex CLI
1. **`config.toml`**：应用可移植的运行时默认值。
2. **`AGENTS.md`**：提供全局约定，之后再叠加更具体的仓库指令。
3. **`agents/<agent>.toml`**：仅在明确生成对应自定义 Agent 时加载。

### Claude Code
1. **`CLAUDE.md`**: 基础规则与 Agent 分发逻辑。
2. **`agents/<agent>.md`**: 特定 Agent 的行为约束。

### Antigravity (Gemini)
1. **`GEMINI.md`**: 基础规则与 Agent 分发逻辑。

*若规则发生冲突，专业 Agent 文件的优先级高于全局指令。*

## 部署方法

1. **Codex CLI**:
   - 将 `codex/AGENTS.md`（或 `codex_zh/AGENTS.md`）复制到项目根目录或 `~/.codex/`。
   - 将 `agents/` 目录同步到 `~/.codex/agents/`。
   - 将可移植的 `config.toml` 配置合并到 `~/.codex/config.toml`；不要直接覆盖由本机生成的插件或 MCP 设置。

2. **Claude Code**:
   - 将 `claude/CLAUDE.md`（或 `claude_zh/CLAUDE.md`）复制到项目根目录。
   - 将 `agents/` 目录同步到 `~/.claude/` 的相同位置。

3. **Antigravity (Gemini)**:
   - 将 `gemini/GEMINI.md`（或 `gemini_zh/GEMINI.md`）复制到项目根目录。

## 修改与扩展

- **修改全局规则**：`AGENTS.md` 只保留跨仓库的工作约定；语言、领域、架构和验证规则放到各个仓库中。
- **调整 Agent 行为**：编辑 `agents/` 目录下的相应文件。
- **添加新 Agent**：在对应的 `agents/` 目录中添加范围明确的 `.toml`/`.md` 文件。
