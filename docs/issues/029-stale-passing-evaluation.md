# EVO-029：新的重评失败后，仍可能沿用旧的通过记录

## 基本信息

- 类型：实现缺口
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：临时评测记录复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：发布采用的是哪次有效评测，后来的失败是否影响原结论。关联 [EVO-010](010-evaluation-evidence.md)、[EVO-011](011-insufficient-validation.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

发布检查按时间查看候选评测，但会跳过没有 gate_input 的记录，继续寻找更早的通过或拒绝结果。

## 不合理的地方

最新重评已经失败或结果不完整，也不会自动阻止使用旧的通过记录。常规 adapter 输出本来就可能不带 gate_input。

## 影响

程序最终采用的证据，可能不是调用者刚刚完成的那次评测。

## 代码位置

- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L328)：`record_evaluation`，第 328 行。不同 role 可保存不同记录。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L438)：`_publication_gate`，第 438 行。跳过不含 gate_input 的新记录。
- [benchmarks/bird/evolution_adapter.py](../../benchmarks/bird/evolution_adapter.py#L86)：`BirdEvolutionAdapter.evaluate`，第 86 行。普通输出没有 gate_input。

## 核对依据与边界

同候选先保存通过 gate，再以 role=rerun 保存 metrics.ex=0、case.status=error 且无 gate_input 的重评记录，仍可发布。若最新记录包含完整的失败 gate，则会拒绝。本条记录的是重评记录选择缺口，不重复 EVO-010 的结果来源核验。

[返回问题索引](README.md)

