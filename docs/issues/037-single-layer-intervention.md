# EVO-037：候选只修改一个层级的要求没有被严格落实

## 基本信息

- 类型：实现缺口
- 归类：论文与 benchmark 参考
- 状态：按研究或复用范围适用
- 核对：论文、Skill 与源码；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

严格单层修改是论文隔离假设、统计各层效果的方法要求，不是所有业务修复的普遍前提。如果新服务要沿用这种归因结论，本条仍适用；如果一次业务修改涉及多层，仅凭这一点不能判定设计错误。归因依据与版本比较仍分别见 [EVO-012](012-failure-attribution.md)、[EVO-005](005-schema-versioning.md) 和 [EVO-006](006-tool-layer-versioning.md)。

## 当前情况

论文第 4 页要求每个候选只修改 Content、Tool、Schema 中一个层级。当前 Skill 要求记录“主要演化层级”，允许同一主要机制涉及多个组件；程序开始一轮时只要求假设文字和候选版本名。

## 不合理的地方

“一个主要机制”不等于“只改一个层级”。程序没有记录并核对完整三层差异，也不检查候选是否混合改了内容与工具。只填归因说明，不能证明论文要求的单层控制成立。

## 影响

即使候选分数提高，也难判断收益来自内容修改还是工具修改；不能直接据此复现论文按层级统计的效果。

## 代码位置

- [Evolver Skill](../../plugins/claude-code/skills/evolve-ontology/SKILL.md#L170)：记录主要层级，第 180–181 行允许同一机制修改多个组件。
- [session.py](../../evoontology/evolution/session.py#L202)：`begin_round` 记录 hypothesis 和 candidate，不检查层级差异。
- [session.py](../../evoontology/evolution/session.py#L403)：`accept` 做内容校验和评分检查，没有单层修改检查。

## 核对依据与边界

多个组件可以同属一层，所以这里不声称 Skill 必然要求跨层修改。确认的是约束表述比论文宽，且程序没有强制校验。外部 Agent 仍可以自行严格遵守论文。

论文来源见[对照文档](../paper-code-comparison.md)。

[返回问题索引](README.md)
