# EVO-036：语义正确性的证据，与结构校验的区别

## 基本信息

- 类型：实现缺口
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：结构校验通过，不能证明语义已经过数据验证
- 核对：论文、源码及临时数据复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

核心问题是：业务定义、数据映射和约束各自依据什么成立，Evidence 是否真正支持这些主张，以及“验证通过”究竟表示格式正确还是语义可信。

空证据也能通过 validate 是 Evo 的实现证据。这里不要求程序独立理解所有业务含义，也不否认 Builder 已可能执行真实探查。

## 当前情况

论文第 3 页要求通过数据探查验证后才提交语义对象。Builder Skill 也要求 Mapping 对应真实数据、Evidence 可复现。程序校验主要检查 JSON、ID 引用以及能否加载。

## 不合理的地方

没有 Evidence 的 Term 和 Mapping 也能通过校验。校验不会连接数据源检查表和字段，也不会确认探查结果能支持语义结论。流程中的“已经验证”与程序返回的 `passed=true` 含义不同。

## 影响

如果参考实现时把 validate 当成语义可信度检查，就可能把没有依据的业务定义当作已经验证的知识。

## 代码位置

- [Builder Skill](../../plugins/claude-code/skills/build-ontology/SKILL.md#L209)：要求真实 Mapping 和可复现 Evidence。
- [validate.py](../../evoontology/validate.py#L115)：有引用才检查引用对象存在，不要求对象一定有证据。
- [validate.py](../../evoontology/validate.py#L145)：最终检查运行时能否加载。

## 核对依据与边界

临时目录创建一个 Term 和一个 Mapping，表、字段填写占位名，Evidence 列表为空。运行 validate 得到 `passed=true, errors=[]`。没有连接或修改真实数据。

这证明的是服务端校验范围有限，不证明 Builder 实际执行时一定跳过探查。论文来源见[对照文档](../paper-code-comparison.md)。

[返回问题索引](README.md)
