# EVO-033：任务结束中途失败，会同时显示运行中和已完成

## 基本信息

- 类型：代码缺陷
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：故障注入复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：任务状态与用于演化的归档是否描述同一个执行结果。关联 [EVO-001](001-trajectory-recording.md)、[EVO-018](018-incomplete-resume.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

finish_task() 先写 trajectory，再更新 tasks 中的任务状态。这是两次独立写入。

## 不合理的地方

后一次写入失败时，归档已经完成，任务却仍可继续记事件。再次结束还能用新答案覆盖原归档。

## 影响

用于恢复的任务状态与用于演进的轨迹互相矛盾，已归档的答案也可能变化。

## 代码位置

- [evoontology/workflow.py](../../evoontology/workflow.py#L227)：`ProjectWorkflow.finish_task`，第 227 行。先归档再保存任务。
- [evoontology/workflow.py](../../evoontology/workflow.py#L195)：`ProjectWorkflow.record_event`，第 195 行。只按 tasks 中状态判断是否还能写。
- [evoontology/trajectory/trajectory.py](../../evoontology/trajectory/trajectory.py#L171)：`TrajectoryStore._write`，第 171 行。直接写同名归档。

## 核对依据与边界

模拟 trajectory 写完后任务文件保存失败：tasks=running，trajectory=completed。随后新增事件并用 answer2 再次结束，原 answer1/1事件被覆盖为 answer2/2事件。与 EVO-025 不同，本条涉及普通任务结束，而非版本发布。

[返回问题索引](README.md)

