# EVO-030：BIRD 评测可能拿到其他运行的结果文件

## 基本信息

- 类型：代码缺陷
- 归类：论文与 benchmark 参考
- 状态：按研究或复用范围适用
- 核对：模拟评测产物复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

这是 BIRD adapter 选择结果文件的具体 bug。不复用该 adapter 时，不形成新服务的直接待修任务；它仍是 [EVO-010](010-evaluation-evidence.md) 所讨论的“结果到底属于哪次评测”的反例。

## 当前情况

BIRD adapter 跑完子进程后，递归扫描输出目录下所有 all_results.json，取修改时间最新的一份。

## 不合理的地方

它没有确认该文件属于本次候选、本次任务或本次子进程。共享目录里其他运行的新文件也会被选中。

## 影响

两个候选或不同任务同时评测时，成绩和产物可能串用，后续版本比较建立在错误结果上。

## 代码位置

- [benchmarks/bird/evolution_adapter.py](../../benchmarks/bird/evolution_adapter.py#L49)：`BirdEvolutionAdapter.evaluate`，第 49 行。使用配置的输出目录。
- [benchmarks/bird/evolution_adapter.py](../../benchmarks/bird/evolution_adapter.py#L71)：`结果读取`，第 71 行。通过最新文件选择结果。
- [benchmarks/bird/evolution_adapter.py](../../benchmarks/bird/evolution_adapter.py#L93)：`_latest_results`，第 93 行。递归扫描并按修改时间排序。

## 核对依据与边界

模拟本次子进程写出 q17、得分 0，另一次运行的文件稍晚，包含 q999、得分 1。adapter 实际返回 q999 和 1 分。仅用了临时 JSON 和模拟子进程，没有调用模型或真实 benchmark。

[返回问题索引](README.md)

