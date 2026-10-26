# EVO-004：一个任务使用多套本体时的来源与版本关系

## 基本信息

- 类型：能力缺口
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：任务记录只正式绑定一套本体的一个版本
- 核对：源码核对；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

同一任务可能同时使用销售、财务等本体。核心问题是：一次分析实际依赖哪些本体、哪些版本，以及这段轨迹能说明哪套本体的问题。

单个 ontology_version 是 Evo 的表达限制；通用工具日志中的零散参数不等于完整的依赖关系。

## 当前情况

Evo 的任务保存在当前 workspace，只有一个 ontology_version。任务内的 resolve 也从该 workspace 和版本加载知识。

Logmind 的 ToolCall 能保留各次调用参数和结果，但没有因此自动形成 Evo 的多本体任务关系。

## 不合理的地方

同一个分析任务实际使用多套本体时，Evo 现有任务字段不能完整表达各套本体及版本的参与关系。调用返回值里的零散信息不等于正式的任务绑定。

## 影响

后续难以直接按任务确定用了哪些本体，也难以确定每套本体应使用哪些轨迹做诊断。

## 代码位置

- [evoontology/workflow.py](../../evoontology/workflow.py#L168)：`ProjectWorkflow.start_task`，第 168 行。单个 ontology_version 字段。
- [evoontology/workflow.py](../../evoontology/workflow.py#L288)：`ProjectWorkflow.resolve_task`，第 288 行。固定从任务所属 workspace 读取。
- [Logmind · backend/app/services/agent_runtime/turn/durable/contracts.py](/Users/jeffacode/Documents/logmind/backend/app/services/agent_runtime/turn/durable/contracts.py:166)：`ToolCall`，第 166 行。通用工具记录结构。

## 核对依据与边界

这是对多本体任务需求的差距。并不否认 Agent 可以显式调用不同 workspace，也不声称返回结果完全没有来源信息。

[返回问题索引](README.md)

