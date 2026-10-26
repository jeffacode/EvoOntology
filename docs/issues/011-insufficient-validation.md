# EVO-011：什么证据足以说明候选值得发布

## 基本信息

- 类型：实现缺口
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：一条验证结果就可能通过发布
- 核对：源码核对及临时数据复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

核心问题是：局部成功能否代表目标业务范围，评测波动会不会改变结论，以及证据不足时是否还能声称整体更好。

一题通过是当前门槛的反例，不表示所有业务都需要同一个最低题数，也不把重跑次数当成充分证明。

## 当前情况

GT 规则是候选均分更高；Judge 规则是候选胜出题数更多且无严重错误。发布检查要求非空，但没有验证样本数量、业务覆盖或重复评测记录的要求。

## 不合理的地方

单题提高也会被程序判为通过。Skill 虽要求小样本时重跑，代码不会确认是否重跑，或结论是否稳定。

## 影响

偶然成功或只覆盖局部问题的候选，可能被当成整体更好的版本。

## 代码位置

- [evoontology/evaluation/evaluation.py](../../evoontology/evaluation/evaluation.py#L28)：`EvaluationGate.decide_gt`，第 28 行。均分比较。
- [evoontology/evaluation/evaluation.py](../../evoontology/evaluation/evaluation.py#L44)：`EvaluationGate.decide_judge`，第 44 行。胜出数量与严重错误判断。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L448)：`_publication_gate`，第 448 行。非空及格式检查。
- [plugins/claude-code/skills/evolve-ontology/SKILL.md](../../plugins/claude-code/skills/evolve-ontology/SKILL.md#L230)：`Step 4`，第 230 行。小样本重跑的文字要求。

## 核对依据与边界

临时实验只提供一题 parent=0、candidate=1，且声明无不可接受退步，发布成功。这里不预设一个适用于所有业务的最低题数。

[返回问题索引](README.md)

