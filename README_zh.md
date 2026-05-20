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

指令架构采用分层设计，入口文件同时承担全局规则制定与专业子 Agent 路由的任务。

### 1. `AGENTS.md` / `CLAUDE.md` / `GEMINI.md` (通用入口与路由)
这是**全局入口点**和核心规则手册。它定义了：
- **核心行为**: 沟通语言、基本原则（如“先思考再编码”、“简单至上”）。
- **Agent 路由与注册**: 定义系统如何根据任务类型和关键词识别并激活专业 Agent（`code_dev`、`code_review`、`writer`）。
- **全局基线**: 通用编码标准（如 Python 3.10+）、数据处理规则和 Git 安全协议。
- **输出契约**: 最终回复的预期格式、验证要求及风险提示。

### 2. `config.toml` (运行时配置)
对于 Codex，此文件现在仅用于**系统与性能设置**：
- **并发控制**: `max_threads` 定义并行任务数。
- **递归深度**: `max_depth` 定义 Agent 递归限制。
- *注意：Agent 注册表和关键词路由已迁移至 `AGENTS.md`，以便于集中管理。*

### 3. `agents/*.toml` 或 `agents/*.md` (专业角色设定)
这些文件定义了特定角色的细颗粒度行为、自检清单和技术优先级。

- **`code_dev` (代码开发)**:
  - 负责实现、调试、性能优化及量化回测。
  - 遵循：正确性 > 性能 > 简洁。
- **`code_review` (代码审查)**:
  - 独立审计与风险评估。只读模式，强调边界条件和逻辑严密性。
- **`writer` (内容写作)**:
  - 多渠道内容创作（小红书、公众号、深度文章）。强调排版规范与事实核验。

## 工作原理 (读取顺序)

### Codex CLI
1. **`AGENTS.md`**: 确立基本规则并执行 **Agent 路由**。
2. **`config.toml`**: 应用运行时限制（线程/深度）。
3. **`agents/<agent>.toml`**: 加载所选 Agent 的具体指令。

### Claude Code
1. **`CLAUDE.md`**: 基础规则与 Agent 分发逻辑。
2. **`agents/<agent>.md`**: 特定 Agent 的行为约束。

### Antigravity (Gemini)
1. **`GEMINI.md`**: 基础规则与 Agent 分发逻辑。

*若规则发生冲突，专业 Agent 文件的优先级高于全局指令。*

## 部署方法

1. **Codex CLI**:
   - 将 `codex/AGENTS.md`（或 `codex_zh/AGENTS.md`）复制到项目根目录或 `~/.codex/`。
   - 将 `config.toml` 和 `agents/` 目录同步到相同位置。

2. **Claude Code**:
   - 将 `claude/CLAUDE.md`（或 `claude_zh/CLAUDE.md`）复制到项目根目录。
   - 在提示词或上下文中引用 `agents/` 目录下的专业 Agent 指令。

3. **Antigravity (Gemini)**:
   - 将 `gemini/GEMINI.md`（或 `gemini_zh/GEMINI.md`）复制到项目根目录。

## 修改与扩展

- **修改全局规则**: 编辑 `AGENTS.md`（如更新编码规范）。
- **调整 Agent 行为**: 编辑 `agents/` 目录下的相应文件。
- **添加新 Agent**: 在 `agents/` 中创建 `.toml`/`.md` 文件，并在 `AGENTS.md` 的路由表中注册。
