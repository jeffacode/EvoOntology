# EVO-001：任务轨迹的范围，以及 Logmind 与语义服务的责任边界

## 基本信息

- 类型：能力缺口
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：轨迹记录仍依赖 Agent 主动补记
- 核对：源码核对；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

Logmind 已有工具调用和结果自动落库。核心问题是：一次分析任务包含哪些事件，何时算使用了某套本体，哪些记录足以支持后续归因。已有日志不等于已经形成完整的语义任务记录。

Agent 主动补记只是 Evo 的当前做法，不是新服务必须继承的机制。

## 当前情况

Evo 的普通 browse / resolve 调用不自动归档。除专门的查询包装工具外，Agent 要另外调用记录工具，最后再结束任务。

Logmind 已有工具调用和结果的自动落库；已检查的运行时路径中，未找到把这些记录交给 Evo 的专门逻辑。

## 不合理的地方

漏调记录工具就会漏记。Evo 的完成检查只要求至少一条成功的数据工具结果，无法证明整段分析过程都被记录。

## 影响

后续演进可能拿到不完整的执行过程，遗漏错误、重试或关键查询。

## 代码位置

- [evoontology/runtime/mcp_server.py](../../evoontology/runtime/mcp_server.py#L73)：`SemanticMCPServer.dispatch`，第 73 行。普通语义查询入口。
- [evoontology/workflow.py](../../evoontology/workflow.py#L189)：`ProjectWorkflow.record_event / finish_task`，第 189 行。显式记事件与完成检查。
- [plugins/claude-code/skills/build-ontology/references/workload-experience.md](../../plugins/claude-code/skills/build-ontology/references/workload-experience.md#L53)：`Run and record real tasks`，第 53 行。要求 Agent 额外记录宿主工具结果。
- [Logmind · backend/app/services/agent_runtime/turn/durable/engine.py](/Users/jeffacode/Documents/logmind/backend/app/services/agent_runtime/turn/durable/engine.py:161)：`DurableTurnEngine.run`，第 161 行。工具执行前后交给运行时保存状态。
- [Logmind · backend/app/services/agent_runtime/turn/durable/postgres.py](/Users/jeffacode/Documents/logmind/backend/app/services/agent_runtime/turn/durable/postgres.py:219)：`PostgresTurnRepository._save`，第 219 行。写入 agent_tool_calls 和 agent_turn_events。

## 核对依据与边界

这是 Evo 插件记录方式及两边对接的缺口，不是说 Logmind 没有自动记录。未检查运行中数据库或外部部署的自定义转换程序。

[返回问题索引](README.md)

