# EVO-017：刚发现缺数据，也被要求先拒绝两次候选

## 基本信息

- 类型：代码缺陷
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：临时数据复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：真实阻塞是否能如实表达，而不是依赖人为凑够拒绝次数。关联 [EVO-018](018-incomplete-resume.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

missing_data、unreliable_evaluation、external_block 这三种停止理由，默认要求本轮已有至少两次 Reject。

## 不合理的地方

数据缺失或评测无法进行，不代表还能正常完成两个候选实验。停止条件却依赖此前的拒绝次数。

## 影响

真实阻塞发生时不能直接如实结束，流程可能卡住，或产生没有实际评测依据的拒绝记录。

## 代码位置

- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L49)：`DEFAULT_MIN_REJECTS_BEFORE_INCOMPLETE`，第 49 行。默认值为 2。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L465)：`mark_incomplete`，第 465 行。对三种理由检查 Reject 次数。
- [tests/test_evolution_session.py](../../tests/test_evolution_session.py#L279)：`test_mark_incomplete_judgment_reasons_require_rejected_rounds`，第 279 行。现有测试也固定了这一行为。

## 核对依据与边界

新建 run 后立即调用 mark_incomplete('missing_data')，得到“至少需要两次拒绝”的异常。阈值可配置，且该限制不适用于所有停止理由。

[返回问题索引](README.md)

