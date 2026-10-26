# EVO-023：MCP 会话失效后，Logmind 一直复用旧会话

## 基本信息

- 类型：代码缺陷
- 归类：Logmind 客户端问题
- 状态：宿主兼容待评估（未修复）
- 核对：真实客户端＋模拟 HTTP；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

这是 Logmind 客户端持有失效 MCP 会话后的恢复问题。只有接入使用会话的服务时才适用；不属于语义内容或演化算法，重新实现 Evo 也不会自动改变 Logmind 客户端。

## 当前情况

初始化成功后，Logmind 的 caller 保存 session ID。后续请求收到表示会话失效的 HTTP 404 时，只返回错误，初始化标记仍为 true。

## 不合理的地方

同一个工具继续调用时，仍带着失效 ID，也不会重新进入初始化流程。

## 影响

上游服务重启或会话过期后，长驻工具可能持续失败，新的调用也无法恢复。

## 代码位置

- [Logmind · backend/app/services/mcp/mcp_tool_caller.py](/Users/jeffacode/Documents/logmind/backend/app/services/mcp/mcp_tool_caller.py:129)：`_safe_request`，第 129 行。HTTP 错误只转换为失败结果。
- [Logmind · backend/app/services/mcp/mcp_tool_caller.py](/Users/jeffacode/Documents/logmind/backend/app/services/mcp/mcp_tool_caller.py:169)：`_ensure_initialized`，第 169 行。已初始化时直接返回。
- [Logmind · backend/app/services/mcp/mcp_tool_caller.py](/Users/jeffacode/Documents/logmind/backend/app/services/mcp/mcp_tool_caller.py:74)：`close`，第 74 行。状态清理在关闭流程中。
- [Logmind · backend/app/services/agent_runtime/tools/impl/mcp_tools.py](/Users/jeffacode/Documents/logmind/backend/app/services/agent_runtime/tools/impl/mcp_tools.py:60)：`MCPTool.__init__`，第 60 行。工具对象持有 caller。

## 核对依据与边界

模拟服务初始化返回 session ID，之后连续三次调用返回 404。只发生一次 initialize，三次都携带同一旧 ID，均报 MCP_REQUEST_FAILED。本条针对带会话的 HTTP 服务；未测试真实服务重启，也不要求自动重放已经失败的写操作。

[返回问题索引](README.md)

