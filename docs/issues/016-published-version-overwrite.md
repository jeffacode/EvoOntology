# EVO-016：已发布的正式版本仍可被覆盖

## 基本信息

- 类型：代码缺陷
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：临时数据复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：正式版本引用能否持续代表同一份内容。关联 [EVO-005](005-schema-versioning.md)、[EVO-004](004-task-ontology-bindings.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

publish() 会拒绝覆盖内容不同的正式目标版本。但 save_version() 对已有目录照常写入，没有区分正式版本与候选版本；MCP 也暴露了这个保存入口。

## 不合理的地方

发布入口的保护没有覆盖其他写入口。相同版本名在不同时间可以读出不同内容。

## 影响

历史记录和任务即使保存了版本名，也不能保证能重现当时的知识；回退结果也不可靠。

## 代码位置

- [evoontology/ontology/store.py](../../evoontology/ontology/store.py#L125)：`SemanticStore.save_version`，第 125 行。直接重写同名目录下的记录。
- [evoontology/ontology/store.py](../../evoontology/ontology/store.py#L192)：`SemanticStore.publish`，第 192 行。只有此入口限制覆盖。
- [evoontology/runtime/ops.py](../../evoontology/runtime/ops.py#L98)：`save_version`，第 98 行。MCP 保存操作直接调用 store。

## 核对依据与边界

临时复现：先发布 ontology_v1，再用 save_version 写入不同内容到 ontology_v1，重新加载后内容已改变。此问题与 EVO-015 分开：这里影响已经正式发布的历史版本。

[返回问题索引](README.md)

