# EVO-003：多本体发现的范围与同名概念的含义

## 基本信息

- 类型：能力缺口
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：不能跨多套本体统一查找概念
- 核对：源码核对；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

已明确的需求是多本体、按需发现，不静默落到一个全局默认本体。核心问题是：不知道本体名称的问题能查到哪些知识，同名概念是否属于不同业务口径。

单 workspace 查询不是普遍错误，但不足以覆盖当前多本体目标。

## 当前情况

一次 browse / resolve 只加载一个 workspace 的一个版本。未传 workspace 时使用服务启动目录；上级目录下有多个可用本体时会报错。

## 不合理的地方

调用方必须先知道该查哪套本体。默认目录只是路径回退，不会按问题选择本体，也不会把多套本体的候选一起返回。

## 影响

销售、财务等多套本体并存时，未知范围的问题无法直接完成跨本体发现。

## 代码位置

- [evoontology/runtime/mcp_server.py](../../evoontology/runtime/mcp_server.py#L73)：`SemanticMCPServer.dispatch`，第 73 行。一次调用选择一个 workspace。
- [evoontology/workspace.py](../../evoontology/workspace.py#L52)：`resolve_workspace_for_version`，第 52 行。唯一子目录可发现，多个匹配报错。
- [evoontology/runtime/runtime.py](../../evoontology/runtime/runtime.py#L155)：`SemanticLayer.browse`，第 155 行。只搜索当前 store。

## 核对依据与边界

相对 notes 中“不设全局默认本体、跨本体发现”的需求记录此缺口。多 workspace 可以分别存储；这里不把“有多套目录”误写成“已经有统一检索”。

[返回问题索引](README.md)

