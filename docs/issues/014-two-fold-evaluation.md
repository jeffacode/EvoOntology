# EVO-014：未找到完整执行双向两折实验的入口

## 基本信息

- 类型：能力缺口
- 归类：论文与 benchmark 参考
- 状态：按研究或复用范围适用
- 核对：源码检索；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

这是论文完整实验的复现缺口。双向两折用于评价方法，不等于业务每次发布都必须跑双向两折。新服务的发布证据仍对应 [EVO-009](009-validation-data-isolation.md) 至 [EVO-011](011-insufficient-validation.md)，不以补齐这个实验入口为前提。

## 当前情况

仓库有单版本评测适配器，BIRD 的 run_full.py 会跑 baseline 和 semantic 两种条件。Skill 文档描述了 A→B 与 B→A 的数据划分。

## 不合理的地方

已检查的入口没有串起两个方向的独立构建、演进、最终测试和成绩汇总。跑两种条件不等于跑完双向两折。

## 影响

仅运行现有单个入口，不能据此声称已经完成论文第 4 页描述的完整实验流程。

## 代码位置

- [benchmarks/__main__.py](../../benchmarks/__main__.py#L11)：`main`，第 11 行。只列出或解析 adapter。
- [benchmarks/bird/evolution_adapter.py](../../benchmarks/bird/evolution_adapter.py#L42)：`BirdEvolutionAdapter.evaluate`，第 42 行。对给定版本调用评测。
- [benchmarks/bird/scripts/run_full.py](../../benchmarks/bird/scripts/run_full.py#L14)：`main`，第 14 行。循环 baseline / semantic 条件。
- [plugins/claude-code/skills/evolve-ontology/references/ontology-layer-data-boundary.md](../../plugins/claude-code/skills/evolve-ontology/references/ontology-layer-data-boundary.md#L12)：`Fixed-Split Mode`，第 12 行。两折的文字规定。

## 核对依据与边界

核对了 benchmark 入口，并搜索双向两折相关实现，未找到完整编排器。另已阅读论文第 4 页 Reciprocal Two-Fold Evaluation，确认其要求两个方向独立适配、冻结后测试并平均。结论限于当前源码，不证明作者没有在仓库外执行实验；论文来源见[对照文档](../paper-code-comparison.md)。

[返回问题索引](README.md)

