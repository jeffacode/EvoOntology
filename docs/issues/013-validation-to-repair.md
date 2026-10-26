# EVO-013：验证发现的问题及其后续使用资格

## 基本信息

- 类型：能力缺口
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：验证发现的问题没有完整的修复与延续记录
- 核对：源码与 notes 核对；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

核心问题是：验证暴露的缺陷是否仍待处理，案例是否已经参与修改，以及跨轮后哪些结论和未知问题仍然有效。修好已知题与证明整体改进是不同结论。

原笔记中的分阶段预留属于讨论方案，本条不把比例或具体工作流当成已确定设计。

## 当前情况

现有流程保存轮次、评测和来源路径，但没有专门记录“这个验证案例已用于修改，不再算独立验证”的状态，也没有完整管理未修复问题跨轮延续的流程。

## 不合理的地方

验证题一旦用于修改，性质已经改变；原有分数和轮次文件不能自动说明它是否还能用于正式验收。新一轮按检查点选轨迹，也不代表旧问题会自动继续被处理。

## 影响

可能重复使用已暴露的验证题，也可能漏掉上一轮遗留的重要问题。

## 代码位置

- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L249)：`record_round / record_evaluation`，第 249 行。当前轮次和评测记录。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L374)：`previous_trajectory_sources`，第 374 行。复用来源路径，不等于管理未解决问题。
- [evoontology/trajectory/trajectory.py](../../evoontology/trajectory/trajectory.py#L134)：`TrajectoryStore.list_since`，第 134 行。按检查点读取新轨迹。
- [plugins/claude-code/skills/evolve-ontology/references/ontology-layer-data-boundary.md](../../plugins/claude-code/skills/evolve-ontology/references/ontology-layer-data-boundary.md#L123)：`Validation Reserve`，第 123 行。验证反馈不得用于候选设计的约束。

## 核对依据与边界

notes 中的验证转修复、分阶段预留和补充新验证任务仍是讨论方案。本条只记录当前缺口，不把这些方案写成已实现功能。

[返回问题索引](README.md)

