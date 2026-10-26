# EVO-031：BIRD 的 cases 参数被当成数据库名，而不是题目 ID

## 基本信息

- 类型：接口差异
- 归类：论文与 benchmark 参考
- 状态：按研究或复用范围适用
- 核对：调用参数复现与源码核对；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

这是 BIRD adapter 的题目范围参数与实际筛选行为不一致。不复用该 adapter 时，不形成新服务的直接待修任务；它仍说明 [EVO-009](009-validation-data-isolation.md) 和 [EVO-010](010-evaluation-evidence.md) 不能只相信调用方声明的验证范围。

## 当前情况

通用 adapter 接口把 cases 描述为评测案例子集。BIRD 实现却将它拼成 --db-ids，runner 再用这些值筛选数据库题目文件。

## 不合理的地方

传入正式验证题的 ID，实际限制的不是这些题。即使传入数据库名，也会选择整份文件，而不是其中指定问题。

## 影响

调用方可能以为已经固定验证题，实际评测范围却为空或扩大到同库其他题，影响评测边界。

## 代码位置

- [evoontology/evolution/adapter.py](../../evoontology/evolution/adapter.py#L23)：`EvolutionAdapter.evaluate`，第 23 行。cases 的通用约定。
- [benchmarks/bird/evolution_adapter.py](../../benchmarks/bird/evolution_adapter.py#L59)：`BirdEvolutionAdapter.evaluate`，第 59 行。将 cases 转为 --db-ids。
- [benchmarks/bird/run_evaluation.py](../../benchmarks/bird/run_evaluation.py#L487)：`main 中的文件筛选`，第 487 行。按 JSON 文件名筛数据库。

## 核对依据与边界

隔离调用 evaluate(..., cases=['q17'])，捕获到命令参数 --db-ids q17。runner 的文件筛选已逐行核对；未执行真实模型评测。与 EVO-030 不同：本条是输入范围错误，不是结果文件串用。

[返回问题索引](README.md)

