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

英文版为源，`_zh` 目录是保持同步的译本。与语言无关的配置文件保持字节一致，包含正文的文件保持语义一致。

## 什么该写在哪一层

指令集刻意保持精简。每条规则都要通过同一个判据：**它是在补模型的能力短板，还是在陈述模型无法知道的事实？** 前者会随模型变强而过期，后者不会。分三层：

| 层 | 判据 | 举例 |
|---|---|---|
| **全局**（`CLAUDE.md` / `AGENTS.md` / `GEMINI.md`） | 适用于该工具下所有项目、无法从仓库或 harness 推断，且**在还没有代码可模仿的新项目里依然成立**的个人默认值 | 沟通偏好、授权边界、交付约定；只有真正通用时才放技术栈或领域默认值 |
| **单个仓库**（该仓库自己的指令文件） | 该仓库特有的事实或规则 | 架构、语言、领域、schema、数据路径、验证命令、项目编码规范 |
| **哪儿都不写** | 仓库或宿主 harness 已经说明或强制 | 工具链与 lint 配置（`uv.lock`、`pyproject.toml`）、现有代码风格、通用安全和工作流行为 |

两条推论值得明写：

- **不写通用工程美德。** 「先想再做」「简单优先」「不编造」「禁裸 except」「向量化而非行循环」—— 当前模型默认就做，宿主 harness 往往也已经写了。重复一遍只是占用 context，还可能因措辞不同产生冲突。
- **不设强制路由表和文件传递流水线。** 把每个任务都推过 `PRD → 设计 → 实现 → 审查` 对小工作是纯仪式，而且会和 harness 自身的默认行为（除非 subagent 确有帮助，否则主线程处理）打架。

## 核心文件与架构

### 1. `AGENTS.md` / `CLAUDE.md` / `GEMINI.md`（全局约定）

每个 session 都需要的长期个人默认值。Codex 版本刻意保持跨仓库通用，把架构、项目语言、技术栈、领域、数据和验证细节留给具体仓库；Claude 版本目前额外包含 Python、存储和量化默认值。这种差异是有意的，应当明确保留。全局文件在每个 session 里常驻，务必保持精简。

### 2. `config.toml`（运行时配置，仅 Codex）

可移植的模型、推理强度、feature 和 memory 默认值。本机生成的 plugin、MCP、notification 和 trusted-project 配置不放在这里。当前可移植配置不含需要本地化的正文，因此 `codex/config.toml` 与 `codex_zh/config.toml` 应保持字节一致。

### 3. `agents/*.toml` 或 `agents/*.md`（专业角色设定）

范围明确的可选角色，**只有生成对应 Agent 时才加载**。正因为是惰性加载，不用到的 agent 成本为零 —— 所以 agent 文件可以比全局文件更专门。

- **`product_manager`**：聚焦需求、Lite PRD、范围、用户故事和研究简报。
- **`architect`**：负责架构、数据模型、接口、系统流程和实施计划，不编写生产代码。
- **`code_review`**：独立、只读地检查正确性、回归、安全、测试缺口和实质性风险。
- **`writer`**（仅 Claude）：文章、报告、说明、教程、README、纪要。

通用 agent 里的领域检查写成**条件式一行**（「涉及时序或量化代码时，按相关性检查……」），这样 agent 保持通用，检查只在相关时触发。

### 4. `templates/`（种子，不加载）

需要常驻才有用、但应当按项目裁剪的规范。这里的内容**不会被自动加载**，必须复制进项目才生效。

- **`code-dev.md`**：Python 实现规范。它不是 agent —— 写代码是主线程的默认工作，包成 subagent 只会白付一次冷启动。

## 工作原理（读取顺序）

### Codex CLI

1. **`config.toml`**：应用可移植的运行时默认值。
2. **`AGENTS.md`**：提供全局约定，之后再叠加更具体的仓库指令。
3. **`agents/<agent>.toml`**：仅在明确生成对应自定义 Agent 时加载。

### Claude Code

1. **`CLAUDE.md`**：全局约定，整个 session 常驻，并被 subagent 继承。
2. **`agents/<agent>.md`**：行为约束，spawn 该 agent 时才读取。

### Antigravity (Gemini)

1. **`GEMINI.md`**：全局约定。

*若规则发生冲突，专业 Agent 文件的优先级高于全局指令。*

## 部署方法

1. **Codex CLI**:
   - 将 `codex/AGENTS.md`（或 `codex_zh/AGENTS.md`）复制到项目根目录或 `~/.codex/`。
   - 将 `agents/` 目录同步到 `~/.codex/agents/`。
   - 将可移植的 `config.toml` 配置合并到 `~/.codex/config.toml`；不要直接覆盖由本机生成的插件或 MCP 设置。

2. **Claude Code**:
   - 将 `claude/CLAUDE.md`（或 `claude_zh/CLAUDE.md`）复制到 `~/.claude/`。**文件名必须保持 `CLAUDE.md`** —— 改名（比如 `CLAUDE_zh.md`）会导致它完全不被加载。
   - 将 `agents/` 同步到 `~/.claude/agents/`。
   - 将 `templates/` 同步到 `~/.claude/templates/`；需要生效时把模板复制进具体项目。

3. **Antigravity (Gemini)**:
   - 将 `gemini/GEMINI.md`（或 `gemini_zh/GEMINI.md`）复制到项目根目录。
