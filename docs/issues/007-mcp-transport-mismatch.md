# EVO-007：Evo 的 stdio 入口不能直接接到 Logmind 的 HTTP 客户端

## 基本信息

- 类型：接入差异
- 归类：原包复用限制
- 状态：仅复用旧组件时适用
- 核对：两边源码核对；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

这是把 Evo 原包直接接入 Logmind 才会遇到的 stdio/HTTP 入口差异。当前目标是重新实现服务，因此它不再是前置修复任务。Logmind 使用 HTTP 仍是已知宿主条件，但没有必要为了新服务先改造旧 Evo 的入口。

## 当前情况

Evo 的 MCP 主入口从标准输入读请求，向标准输出写响应。Logmind 的 MCPToolCaller 则向配置的 URL 发送 HTTP 请求，并处理初始化和会话信息。

## 不合理的地方

两边使用不同的传输入口。Evo 的启动脚本或本地目录不能直接当成 Logmind 所需的服务 URL。

## 影响

仅复制三个 Skill，无法让 Logmind 直接调用这份 Evo 核心入口。

## 代码位置

- [evoontology/runtime/mcp_server.py](../../evoontology/runtime/mcp_server.py#L146)：`_write / run / main`，第 146 行。stdio 读写和启动入口。
- [Logmind · backend/app/services/mcp/mcp_tool_caller.py](/Users/jeffacode/Documents/logmind/backend/app/services/mcp/mcp_tool_caller.py:169)：`_ensure_initialized`，第 169 行。HTTP 客户端初始化。
- [Logmind · backend/app/services/mcp/mcp_tool_caller.py](/Users/jeffacode/Documents/logmind/backend/app/services/mcp/mcp_tool_caller.py:198)：`_post_jsonrpc`，第 198 行。向 URL 发请求，读取 JSON 响应。

## 核对依据与边界

已核对 Logmind 客户端代码，不把 HTTP 客户端列为 Logmind 缺失能力。本轮用模拟 HTTP 层连接真实客户端与 Evo dispatch，成功发现 29 个工具；这不代表 Evo 已有 HTTP 服务。未检查外部代理，也未做真实部署联调。

[返回问题索引](README.md)

