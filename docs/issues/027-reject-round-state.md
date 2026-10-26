# EVO-027：重复 Reject 会虚增次数，已拒绝候选仍能接受

## 基本信息

- 类型：代码缺陷
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：临时状态机复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：轮次决定是否有效，重复调用是否被误算成新的实验。关联 [EVO-018](018-incomplete-resume.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

record_round('reject') 每次都追加一行，不要求已经开启候选轮次，也不关闭当前轮次。rejected_count() 直接数记录行数。

## 不合理的地方

同一次请求的重试被当成多次实验。候选已经记为 Reject，仍保留继续 Accept 的状态。

## 影响

拒绝次数不可信；用拒绝次数判断是否允许结束，也能被空记录或重复记录满足。

## 代码位置

- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L249)：`record_round`，第 249 行。直接追加 Reject 记录。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L228)：`rejected_count`，第 228 行。按记录行数计数。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L403)：`accept`，第 403 行。不核对当前轮次是否已拒绝。

## 核对依据与边界

同一轮连续两次 Reject 后，round=1、rejected_count=2，仍可 Accept。另一个新 run 未开轮次、没有候选，也能记两次 Reject 后以 missing_data 结束。与 EVO-017 不同：本条是轮次状态和重复调用问题。

[返回问题索引](README.md)

