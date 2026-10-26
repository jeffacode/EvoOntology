# EVO-015：候选评测后再修改，仍能用旧分数发布

## 基本信息

- 类型：代码缺陷
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：临时数据复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：评测对应的候选，与最终发布对象是否还是同一份。关联 [EVO-005](005-schema-versioning.md)、[EVO-010](010-evaluation-evidence.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

评测记录关联候选版本名和轮次。保存新内容时可以继续使用同一个候选版本名；发布时只重新检查结构和评分记录。

## 不合理的地方

程序没有确认“现在准备发布的内容”就是“当时评测的内容”。内容变了，旧评分仍然有效。

## 影响

正式发布的本体可能没有被那次评测检验过，发布结论与实际内容脱节。

## 代码位置

- [evoontology/ontology/store.py](../../evoontology/ontology/store.py#L125)：`SemanticStore.save_version`，第 125 行。同名候选可重写。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L300)：`record_evaluation`，第 300 行。记录版本名，不绑定内容。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L432)：`_publication_gate`，第 432 行。按轮次和版本名选择评分。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L403)：`accept`，第 403 行。校验并发布当前文件内容。

## 核对依据与边界

临时复现：保存 candidate → 提交通过的模拟评分 → 改写 candidate 的 Term 名称 → accept。结果发布成功，正式版包含评测记录之后修改的名称。没有改动项目中的真实本体。

[返回问题索引](README.md)

