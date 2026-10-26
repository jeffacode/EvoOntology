# EVO-019：发布会把本轮未处理的新轨迹一起划过检查点

## 基本信息

- 类型：代码缺陷
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：临时数据复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：被标记为已处理的轨迹，是否实际进入过本轮分析。关联 [EVO-001](001-trajectory-recording.md)、[EVO-013](013-validation-to-repair.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

accept() 不带批次末尾 ID 调用 advance_checkpoint()。后者默认选取发布时最新的一条轨迹，而不是本轮冻结批次的最后一条。

## 不合理的地方

演进进行期间，业务仍可能产生新任务。这些任务还没参与本轮诊断，检查点却可能越过它们。

## 影响

下一轮只读检查点之后的轨迹时，会漏掉这些新任务。文件没有丢失，但它们从后续增量处理范围里消失。

## 代码位置

- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L423)：`accept`，第 423 行。发布后无参推进检查点。
- [evoontology/trigger/trigger.py](../../evoontology/trigger/trigger.py#L160)：`EvolutionTrigger.advance_checkpoint`，第 160 行。默认选当前最新轨迹。
- [evoontology/trajectory/trajectory.py](../../evoontology/trajectory/trajectory.py#L134)：`TrajectoryStore.list_since`，第 134 行。排除检查点及更早记录。

## 核对依据与边界

临时复现：本轮登记旧任务作为来源 → 演进期间写入新任务 → 接受候选。检查点变成新任务，list_since 返回空，虽然新任务没有进入登记的批次。

[返回问题索引](README.md)

