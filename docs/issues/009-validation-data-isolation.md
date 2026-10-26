# EVO-009：修改材料与独立验证材料的边界

## 基本信息

- 类型：能力缺口
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：修改用题和验证用题的隔离主要靠流程约定
- 核对：源码核对；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

核心问题是：一条任务及其反馈是否已经影响过候选，所谓验证结果还能证明多大范围的改进。只把题目放进不同列表，不能说明信息使用过程独立。

70/30 是 Evo 的流程选择，不是新服务已确定的比例，也不是独立性的充分条件。

## 当前情况

Skill 要求先冻结批次、按 70/30 分开，并把重复或相似问题放在同一组。核心有部分用途和题目 ID 检查，但没有完整执行这套分组、切分和使用记录。

## 不合理的地方

发布检查无法确认验证题此前是否参与了诊断、改写或候选选择。只保存一组 case_ids，不能证明这些题仍然独立。

## 影响

针对已看过的问题调好候选后，仍可能把同一批题的得分当成独立改进证据。

## 代码位置

- [plugins/claude-code/skills/evolve-ontology/references/ontology-layer-data-boundary.md](../../plugins/claude-code/skills/evolve-ontology/references/ontology-layer-data-boundary.md#L86)：`Evolution Batch / Validation Reserve`，第 86 行。冻结、70/30 和验证用途约束。
- [plugins/claude-code/skills/evolve-ontology/SKILL.md](../../plugins/claude-code/skills/evolve-ontology/SKILL.md#L110)：`Step 1`，第 110 行。近似问题分组要求。
- [evoontology/workflow.py](../../evoontology/workflow.py#L80)：`ProjectWorkflow.prepare`，第 80 行。已有的局部数据边界检查。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L432)：`_publication_gate`，第 432 行。发布时检查分数格式和协议，不检查题目的完整使用历史。

## 核对依据与边界

不是完全没有隔离措施。这里记录的是已有检查与完整防泄漏之间的差距，未声称实际实验已经泄漏。

[返回问题索引](README.md)

