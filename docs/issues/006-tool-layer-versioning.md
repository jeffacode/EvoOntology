# EVO-006：工具行为是否属于版本，以及变化对调用方的影响

## 基本信息

- 类型：能力缺口
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：工具定义不随本体版本变化，也没有变化推送
- 核对：源码核对；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

如果允许 Tool 演化，检索、返回内容、参数和 manifest 都可能变化。核心问题是：父版与候选实际使用哪套工具行为，Logmind 当时看到的接口是否与之相符。

固定工具集合本身不是缺陷，动态增删和变化通知也不是无条件需求。当前工具未随版本隔离，才是三层演化的主要限制。

## 当前情况

tools/list 返回固定的 TOOLS + OPERATIONS。切换本体只重新加载知识内容，工具定义和执行逻辑仍来自共享代码；服务也没有发送工具清单变化通知。

Logmind 有工具清单刷新方法，但这不等于 Evo 已提供按版本切换的工具集合。

## 不合理的地方

本体版本不能独立携带新增工具、参数变化或检索逻辑变化。只改本体记录，不会让客户端发现一个新工具。

## 影响

Tool Layer 的修改无法像知识内容一样按候选版本独立比较和回退。

## 代码位置

- [evoontology/runtime/tools.py](../../evoontology/runtime/tools.py#L31)：`TOOLS / OPERATIONS`，第 31 行。固定工具注册表。
- [evoontology/runtime/mcp_server.py](../../evoontology/runtime/mcp_server.py#L59)：`SemanticMCPServer.dispatch`，第 59 行。初始化能力、工具清单和调用分发。
- [evoontology/runtime/runtime.py](../../evoontology/runtime/runtime.py#L485)：`SemanticLayer.execute`，第 485 行。共享的查询执行入口。
- [Logmind · backend/app/services/agent_runtime/tools/service.py](/Users/jeffacode/Documents/logmind/backend/app/services/agent_runtime/tools/service.py:227)：`refresh_mcp_tools_for_server`，第 227 行。Logmind 已有的注册表刷新能力。

## 核对依据与边界

不把“工具固定”本身当成协议错误；问题是它尚不支持 notes 中讨论的工具层演进。未做工具变化通知的端到端测试。

[返回问题索引](README.md)

