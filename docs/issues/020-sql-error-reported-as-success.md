# EVO-020：SQL 查询失败，却被 Logmind 记成工具执行成功

## 基本信息

- 类型：代码缺陷
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：模拟接入链路复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：服务结果、协议结果与宿主记录中的成功失败是否一致。关联 [EVO-001](001-trajectory-recording.md)、[EVO-012](012-failure-attribution.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

Evo 捕获 SQL 错误后返回 status=error，并在自己的任务记录中标记失败；MCP 外层却返回 isError=false。Logmind 据外层结果把调用记为 succeeded。

## 不合理的地方

同一次调用，在 Evo 是失败，在 Logmind 是成功。后续按工具状态统计或选择轨迹时，两边结论不一致。

## 影响

失败查询可能混入成功任务，影响错误提示、任务判断和演进材料。

## 代码位置

- [evoontology/workflow.py](../../evoontology/workflow.py#L280)：`ProjectWorkflow.execute_query`，第 280 行。捕获错误后正常返回。
- [evoontology/runtime/mcp_server.py](../../evoontology/runtime/mcp_server.py#L98)：`SemanticMCPServer.dispatch`，第 98 行。操作正常返回就设置 isError=false。
- [Logmind · backend/app/services/agent_runtime/turn/durable/adapters.py](/Users/jeffacode/Documents/logmind/backend/app/services/agent_runtime/turn/durable/adapters.py:176)：`DurableToolAdapter.execute`，第 176 行。宿主失败判断。
- [Logmind · backend/app/services/agent_runtime/turn/durable/service.py](/Users/jeffacode/Documents/logmind/backend/app/services/agent_runtime/turn/durable/service.py:860)：`工具完成记录`，第 860 行。按 failed 字段保存成功或失败。

## 核对依据与边界

用临时 SQLite 查询不存在的表，贯穿 Evo dispatch、Logmind MCPTool、DurableToolAdapter 和内存账本。实际结果：Evo error=true，MCP isError=false，Logmind failed=false、status=succeeded。没有连接真实服务或数据库。

[返回问题索引](README.md)

