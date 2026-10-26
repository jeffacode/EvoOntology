# EVO-025：发布中途报错后已生效，重试又生成一个版本

## 基本信息

- 类型：代码缺陷
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：故障注入复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：发布是否生效、流程是否完成，以及重试是否改变既有结果。关联 [EVO-005](005-schema-versioning.md)、[EVO-018](018-incomplete-resume.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

accept() 先发布并切换 active，再推进检查点，最后保存 run 的 accepted 状态。中间一步失败，不会撤回前面已完成的写入。

## 不合理的地方

调用者收到失败，实际版本却已切换；run 又仍然显示 running。默认重试会按新的最大版本号继续发布。

## 影响

同一个候选可以产生两个正式版本，任务状态也不能准确说明究竟有没有发布。

## 代码位置

- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L422)：`EvolutionSession.accept`，第 422 行。三个阶段依次写入。
- [evoontology/ontology/store.py](../../evoontology/ontology/store.py#L213)：`SemanticStore.publish`，第 213 行。先切换 active。

## 核对依据与边界

在检查点写入处模拟一次 OSError：active 已为 ontology_v1，run 仍 running。重新恢复并 accept，生成 ontology_v2，两版内容相同。只模拟写入失败；未制造真实磁盘故障。与 EVO-019 的批次选取错误不同。

[返回问题索引](README.md)

