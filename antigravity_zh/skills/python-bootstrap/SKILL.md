---
name: python-bootstrap
description: "为 Python 项目的 GEMINI.md 落地或刷新编码、测试、性能规范，并按该仓库实际情况改写。"
---

# Python Bootstrap

把 [code-dev.md](code-dev.md) 里的 Python 规范落地到当前项目的 `GEMINI.md`。要改写，不要照搬。

1. 读项目现有的 `GEMINI.md` / `AGENTS.md`、`pyproject.toml`，以及 CI / lint / 测试配置。没有 `GEMINI.md` 时，根据仓库实际情况新建。
2. 读 `code-dev.md`。只保留适用于本项目、且 Antigravity 无法从代码或配置推断出的规则。仓库已通过工具强制执行的内容直接删掉（例如 `pyproject.toml` 里已有的 ruff 配置）。
3. 把通用命令换成项目的真实命令（测试入口、`src/` 还是包目录、覆盖率阈值）。
4. 项目约定与规则冲突时以项目为准；注明差异，不去改代码。
5. 不重复 `~/.gemini/GEMINI.md` 和 `quant-guardrails` skill 里的规则。量化项目在其 `GEMINI.md` 中说明适用 `quant-guardrails`，让护栏在相关场景加载。
6. 生成的 `GEMINI.md` 保持精简，远低于 200 行。逐行自问：删掉这一行会不会导致出错？不会就删。
7. 展示 diff，概述新增、改写和删除了哪些内容。不要 commit。
