# EVO-012：失败是否由语义层引起，以及归因有多可信

## 基本信息

- 类型：能力边界
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：失败原因的判断没有程序化核验
- 核对：源码核对；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

核心问题是：缺知识、工具使用不当、执行故障和模型波动，分别能从哪些现象中得到支持。错误次数或最终低分本身不能证明本体需要修改。

归因可以依赖 Agent 或人工判断；缺少自动分类器本身不构成设计缺陷。关注的是判断依据和不确定性。

## 当前情况

Skill 让 Agent 判断失败来自知识、工具还是其他因素。任务记录保存错误标记，演进轮次保存 hypothesis 和 notes；这些内容主要由调用方填写。

## 不合理的地方

现有代码保存 Agent 的归因，但不会独立确认归因成立。若把假设文字、失败次数或最终低分直接当成已证实的根因，就会高估这些记录的证明力。缺少自动分类器本身不是缺陷。

## 影响

如果归因不准，候选修改可能针对错误原因，评测波动也可能被误当成本体改善。

## 代码位置

- [plugins/claude-code/skills/evolve-ontology/SKILL.md](../../plugins/claude-code/skills/evolve-ontology/SKILL.md#L88)：`Step 1 / Step 2`，第 88 行。诊断与归因交给 Agent。
- [evoontology/workflow.py](../../evoontology/workflow.py#L189)：`ProjectWorkflow.record_event`，第 189 行。记录错误标记。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L202)：`begin_round`，第 202 行。保存假设。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L249)：`record_round`，第 249 行。保存自由形式的指标和说明。

## 核对依据与边界

记录的是可靠性边界，不是说所有失败都会被错误归因，也不预设这类判断必须完全自动化。

[返回问题索引](README.md)

