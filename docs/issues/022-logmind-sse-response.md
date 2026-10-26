# EVO-022：Logmind 声明接受 SSE，却按普通 JSON 解析

## 基本信息

- 类型：代码缺陷
- 归类：Logmind 客户端问题
- 状态：宿主兼容待评估（未修复）
- 核对：真实客户端＋模拟 HTTP；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

这是 Logmind HTTP MCP 客户端对 SSE 响应的兼容问题，不属于 Evo，也不是语义模型问题。新服务如果返回 SSE，重新实现服务本身不会自动消除这个宿主限制；仅返回普通 JSON 的调用不由此例证明有问题。

## 当前情况

Logmind 的 Accept 请求头包含 text/event-stream，但收到响应后固定调用 response.json()。

## 不合理的地方

上游通过 SSE 返回 JSON-RPC 结果时，外层不是普通 JSON，客户端会解析失败。

## 影响

新语义层服务若通过 HTTP 返回 SSE，工具发现或调用可能失败，即使消息里包含正常结果。

## 代码位置

- [Logmind · backend/app/services/mcp/mcp_tool_caller.py](/Users/jeffacode/Documents/logmind/backend/app/services/mcp/mcp_tool_caller.py:198)：`_post_jsonrpc`，第 198 行。接收响应后调用 response.json()。
- [Logmind · backend/app/services/mcp/mcp_tool_caller.py](/Users/jeffacode/Documents/logmind/backend/app/services/mcp/mcp_tool_caller.py:260)：`_headers`，第 260 行。声明接受 JSON 和 SSE。

## 核对依据与边界

MockTransport 先返回正常初始化 JSON，再返回包含对应请求 ID 和成功结果的 SSE。真实客户端报 MCP_INVALID_RESPONSE。当前 Evo 没有 HTTP 服务，本条是 Logmind 客户端的条件性兼容问题，不是已部署 Evo 联调故障。与 EVO-007 的传输入口差异不同。

[返回问题索引](README.md)

