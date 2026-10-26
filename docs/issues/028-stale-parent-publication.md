# EVO-028：当前正式版本已变，旧演进仍能覆盖它

## 基本信息

- 类型：代码缺陷
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：临时状态机复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：候选比较过的父版，与发布时被替换的正式版是否相同。关联 [EVO-005](005-schema-versioning.md)、[EVO-010](010-evaluation-evidence.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

run 保存启动时的 parent_version。accept() 发布时没有检查 active 是否仍指向这个 Parent。

## 不合理的地方

候选只和旧版本比较过，却可以替换已经切换到的新版本。

## 影响

长任务期间如果其他操作发布或切换版本，旧演进可能把新知识覆盖掉，评测结论也不再对应被替换的版本。

## 代码位置

- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L133)：`start_run`，第 133 行。保存 Parent。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L410)：`accept`，第 410 行。发布前未核对当前 active。
- [evoontology/runtime/ops.py](../../evoontology/runtime/ops.py#L109)：`set_active_version`，第 109 行。其他调用可以切换版本。

## 核对依据与边界

基于 ontology_v0 准备并评测候选，然后将 active 切到 ontology_v10。原 run Accept 成功，生成 ontology_v11 并激活旧候选。没有改写候选内容，区别于 EVO-015。

[返回问题索引](README.md)

