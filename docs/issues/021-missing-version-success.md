# EVO-021：版本不存在，却返回成功的空查询结果

## 基本信息

- 类型：代码缺陷
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：模拟接入链路复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：知识未命中与指定版本读取失败是否被混为一谈。关联 [EVO-004](004-task-ontology-bindings.md)、[EVO-012](012-failure-attribution.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

SemanticLayer.load() 把读取版本时的 FileNotFoundError 转成空本体。即使调用方明确指定了版本，也使用同样处理。

## 不合理的地方

版本写错或文件缺失，被表现为“没有找到概念”，而不是本次读取失败。

## 影响

Logmind 可能继续按“知识缺口”处理，实际问题却是选错版本或版本损坏。

## 代码位置

- [evoontology/runtime/runtime.py](../../evoontology/runtime/runtime.py#L63)：`SemanticLayer.load`，第 63 行。缺文件时回退到空本体。
- [evoontology/runtime/runtime.py](../../evoontology/runtime/runtime.py#L79)：`_empty_store`，第 79 行。返回 uninitialized。
- [evoontology/runtime/mcp_server.py](../../evoontology/runtime/mcp_server.py#L87)：`SemanticMCPServer.dispatch`，第 87 行。正常返回被标为成功。

## 核对依据与边界

已有有效 ontology_v1 时，显式查询 typo-v999：返回 status=ok、isError=false、version=uninitialized；browse 为空，resolve 为 unresolved。经真实 Logmind 工具包装仍正常返回。uninitialized 字段仍可供模型识别，不能说故障完全不可见。

[返回问题索引](README.md)

