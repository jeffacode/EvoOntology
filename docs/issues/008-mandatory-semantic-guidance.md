# EVO-008：语义查询在分析过程中的角色

## 基本信息

- 类型：需求差异
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：部分指引把本体查询放在数据查询之前
- 核对：源码与 notes 核对；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

已明确本体按需使用，不作为每次数据查询的强制前置步骤。核心问题是：服务和 Agent 指引是否表达同一使用边界，以及没有相关语义时是否仍可正常分析。

这是已明确的产品约束与 Evo 部分指引的差异，不需要重新讨论是否强制前置。

## 当前情况

Evo 生成的 manifest 要求在查询底层数据前先做概念 grounding。另一份交互协议又写成“语义工具有帮助时使用”。

## 不合理的地方

指引口径不一致，也与 notes 中“本体按需使用，不要求每次先查”的偏好有差异。

## 影响

照搬当前指引，Agent 可能把本体查询当成每次数据分析的固定步骤，增加不必要的调用。

## 代码位置

- [evoontology/runtime/runtime.py](../../evoontology/runtime/runtime.py#L138)：`SemanticLayer.manifest`，第 138 行。查询底层数据前先做 grounding 的提示。
- [plugins/claude-code/skills/build-ontology/references/ontology-interaction-protocol.md](../../plugins/claude-code/skills/build-ontology/references/ontology-interaction-protocol.md#L167)：`Interaction Flow`，第 167 行。写成工具有帮助时使用。
- [Logmind · backend/app/services/agent_runtime/turn/durable/adapters.py](/Users/jeffacode/Documents/logmind/backend/app/services/agent_runtime/turn/durable/adapters.py:129)：`DurableToolAdapter.execute`，第 129 行。已检查的通用执行路径没有 Evo 前置检查。

## 核对依据与边界

这是 Evo 指引与用户需求的差异，不是代码强制拦截。没有证据说明 Logmind 当前强制每次分析先查本体。

[返回问题索引](README.md)

