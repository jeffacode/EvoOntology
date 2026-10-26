# EVO-034：检查点会漏掉时间戳相同的其他轨迹

## 基本信息

- 类型：代码缺陷
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：临时轨迹复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：增量处理是否遗漏尚未处理但时间相同的任务。关联 [EVO-001](001-trajectory-recording.md)、[EVO-013](013-validation-to-repair.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

list_since(task_id) 找到检查点后，只比较 recorded_at，返回时间严格大于检查点的记录。

## 不合理的地方

同一时间戳下可能有多条不同任务。即使检查点只处理到其中一条，其余记录也会被排除。

## 影响

导入轨迹或多个执行器使用相同源时间时，未处理任务可能不再进入后续演进。

## 代码位置

- [evoontology/trajectory/trajectory.py](../../evoontology/trajectory/trajectory.py#L112)：`TrajectoryStore.append`，第 112 行。允许传入 recorded_at。
- [evoontology/trajectory/trajectory.py](../../evoontology/trajectory/trajectory.py#L152)：`TrajectoryStore.list_since`，第 152 行。只做严格的时间比较。

## 核对依据与边界

写入 a、b 两条相同 recorded_at 的记录，全量读取为 2 条，list_since('a') 却为空。默认时间包含微秒，本条不声称每次并发都会撞时间。与 EVO-019 不同：这里即使检查点选对，时间相等仍会漏读。

[返回问题索引](README.md)

