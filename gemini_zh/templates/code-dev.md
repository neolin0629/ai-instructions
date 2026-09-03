# 模板：Python 编码规范（项目级）

**用法**：复制到目标项目的 `GEMINI.md`（或作为其中一节），按该项目实际情况删改。这不是 agent —— 写代码是主线程的默认工作，不需要为它开 subagent。

**与全局的关系**：语言约定、`uv`、存储分层、量化护栏已在 `~/.gemini/GEMINI.md`，此处不重复。本模板只放实现层面的规范。

---

## 基础

- 格式与静态检查：`ruff format`、`ruff check`
- 类型标注：所有公开 API 签名和跨模块调用边界必须有
- Docstring：公开模块、类、核心函数用 Google 风格；核心函数标注时间/空间复杂度
- MRE 入口：有可运行逻辑的文件以 `if __name__ == "__main__":` 结尾，带真实感的 dummy data 作为最小可复现示例。豁免：纯库模块（`__init__.py`、纯类型/接口/常量模块），以及 `__main__` 块本身就是真实入口的文件 —— CLI 命令、服务启动器、定时任务。绝不用 dummy data 顶掉真实入口

## 数据完整性

- `NaN` / `Inf` / 除零 / 空 DataFrame 必须显式处理，不静默 drop 或 fillna，策略要有注释说明
- 类型一致：浮点统一 `float64`，标的代码统一 `str`
- 索引与排序：操作前显式检查 `is_monotonic_increasing` 和重复值

## 性能（优先级从高到低）

1. 向量化优先（Pandas / NumPy / Polars）。大 DataFrame 上禁止 `.iterrows()` 和行级循环
2. 避开 `apply`，改用 `.shift()` / `.rolling()` / `np.where` / `pl.col(...).over(...)`
3. 循环无法避免时默认选 `numba.jit(nopython=True, cache=True)` —— 但 numba 若不在项目现有依赖里，先说明并取得同意再加
4. 大数据集评估峰值内存，必要时分块
5. 百万行以上默认 Polars 或 DuckDB
6. 热路径用 `time.perf_counter()` 做改动前后对比，不用 `time.time()`，不靠感觉

## 日志与异常

- 不用 `print()`，统一 `logging` 或 `loguru`；临时调试输出当场删掉
- 级别：`DEBUG` 开发预览 / `INFO` 业务里程碑 / `WARNING` 预期内异常 / `ERROR` 系统中断
- 日志消息英文 + 结构化字段：`logger.info("backtest_done", extra={"strategy": name, "sharpe": s})`
- 网络请求、API、文件 IO 只在有恢复策略、或需要给错误补充上下文时才捕获，否则让异常向上传播 —— 捕获后只记日志再继续会掩盖故障；多任务并行时单个任务失败不得让主线程崩溃
- 禁止裸 `except:` 和 `except Exception: pass`

## 测试与依赖

- 核心数值计算和纯函数 pytest 覆盖率 > 80%；仓库自己配了阈值就以仓库为准
- 测试覆盖正常输入、边界（空、单元素、极值）、异常输入（NaN / Inf）
- 涉及随机性时固定 `random.seed` 和 `np.random.seed`
- 依赖走 `pyproject.toml`，dev 与 runtime 分开：`uv sync` / `uv add` / `uv add --dev` / `uv run` / `uv lock`

```bash
uv run pytest tests/ -v --cov=src
uv run ruff format <改动的文件>
uv run ruff check <改动的文件> --fix
```

`--fix` 限定在本次任务改过的文件上。对整个 `src/` 跑会改写任务范围外的代码，diff 就没法审了。

## 输出约定

- 代码先行，然后解释要点
- 在注释里标注隐式假设：`# Memory heavy: peak ~2GB on 10M rows` / `# Assumes df is sorted by datetime` / `# Not thread-safe` / `# Requires ClickHouse v23.8+`
- 改完给出验证命令
