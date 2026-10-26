# Evo 论文与当前源码对照

核对日期：2026-10-23；源码：`c94389b`。论文为本地英文版 [EvoOntology.pdf](/Users/jeffacode/Documents/semantic-hub/docs/参考文章/EvoOntology.pdf)，arXiv:2609.15779v1。页码按 PDF 页序。

使用背景：参考 Evo 重新实现语义层服务，供 Logmind 使用。这里检查论文方法与源码的对应关系，不讨论直接安装 Evo 的接入方案，也不写解决方案。

## 判断

**内容模型和按需查询接口基本对应；构建、归因和演化有 Skill 流程；完整的三层版本隔离与实验复现链条，当前源码没有闭合。**

Skill 是供 Agent 执行的程序性指引，也是实现的一部分。不能因为没有 Python 算法就说“完全没实现”；但 Skill 中的要求，不等于服务端已经检查并保证。

## 逐项对照

| 机制 | 论文 | 当前实现 | 判断 |
| --- | --- | --- | --- |
| 内容模型 | 第 3 页：Term、Mapping、Constraint、Evidence 四类节点，语义关系与结构引用两类边 | [models.py](../evoontology/ontology/models.py#L58) 有对应对象；引用由 ID 表达 | 基本一致。五类 JSON 文件不代表误解了论文的“四类节点” |
| 按需取语义 | 第 3 页：manifest 提供入口，browse 找相关对象，resolve 取完整语义 | [runtime.py](../evoontology/runtime/runtime.py#L96) 有三者；通过 MCP 暴露 | 基本一致。论文没有指定必须使用向量检索，不能仅凭词法匹配判定偏离论文 |
| 初始构建 | 第 3 页：从训练问题提出概念，探查数据，只提交经过验证的内容 | [Builder Skill](../plugins/claude-code/skills/build-ontology/SKILL.md#L109) 写了完整步骤；[validate.py](../evoontology/validate.py#L19) 只做结构、引用和可加载性检查 | 流程对应；证据是否真实、能否支持结论，主要依赖 Agent。见 [EVO-036](issues/036-evidence-grounding-validation.md) |
| 诊断与归因 | 第 3–4 页：分析轨迹，把问题归到 Content、Tool 或 Schema，并说明预期影响 | [Evolver Skill](../plugins/claude-code/skills/evolve-ontology/SKILL.md#L85) 有诊断、竞争解释、归因和试验步骤 | 有 Agent 流程实现；不能当成已经自动证明因果关系的算法 |
| 局部修改 | 第 4 页：每个候选只修改一个层级，借此隔离假设 | Skill 使用“主要层级”“同一主要机制”的表述；[begin_round](../evoontology/evolution/session.py#L202) 不检查实际改动层级 | 约束比论文弱，也没有程序化差异检查。见 [EVO-037](issues/037-single-layer-intervention.md) |
| 三层版本 | 第 2–4 页：版本状态包含 Content、Schema、Tool；拒绝候选保留父版本 | [store.py](../evoontology/ontology/store.py#L23) 按版本读取五类内容；模型、工具和 manifest 逻辑主要由当前代码定义 | 内容版本已实现；单靠本体版本名不能切换整套三层状态。见 [EVO-005](issues/005-schema-versioning.md)、[EVO-006](issues/006-tool-layer-versioning.md) |
| 配对评测 | 第 4 页：同模型、同验证集、同预算比较父版和候选，达到改进门槛才接受 | [EvaluationGate](../evoontology/evaluation/evaluation.py#L25) 做评分聚合；[发布检查](../evoontology/evolution/session.py#L432) 检查配对数组和 case_ids | 有数值门槛，但不核验完整执行条件与原始产物；候选改写后仍可沿用旧评分。见 [EVO-010](issues/010-evaluation-evidence.md)、[EVO-015](issues/015-evaluated-candidate-changes.md) |
| 双向两折实验 | 第 4 页：A 内部 70/30 用于构建演化/验证，冻结后测 B，再反向运行并平均 | Skill 写有划分规范；benchmark adapter 能评测指定版本，未找到全流程编排入口 | 能运行评测部件，不等于仓库已提供论文完整实验复现。见 [EVO-014](issues/014-two-fold-evaluation.md) |

## 最关键的差距：三层演化

论文第 7 页表 6 专门比较 Content-only、Tool-only、Schema-only 与三层演化；第 10 页图 7 报告 Tool 修改占累计收益的 57%，主要来自 manifest 和工具的信息呈现方式。

所以 Tool 演化不能只理解为“增加工具”：修改已有工具的返回内容、排序和使用提示，也属于论文的 Tool 层。

源码允许 Agent 修改这些代码，但 `ontology_vN` 没有绑定对应的 runtime、manifest 和模型结构。若直接改共享代码，再分别加载 Parent/Candidate 内容，两边可能已经用了同一套新工具，无法仅凭版本名证明比较的是两套完整状态。外部 Git/worktree 隔离可以承担这部分工作，但当前本体发布入口没有替调用方管理它。

## 通用核心也不等于完整的 benchmark 实现

DDR Agent [实际加载 DDRSemanticLayer](../benchmarks/ddr_10k/agent/data_agent.py#L1139)，其 MCP 服务也[加载这个专用实现](../benchmarks/ddr_10k/tool_server/semantic_mcp.py#L20)。它的 [manifest](../benchmarks/ddr_10k/tceo/runtime.py#L31) 包含财务概念与 fact_name 对应，并指引使用返回的 sample_query。这与通用核心的行为并不完全相同。

此外，论文第 7 页描述某些演化结果在 Evidence 中保留短 SQL 片段库；当前 [Builder Skill](../plugins/claude-code/skills/build-ontology/SKILL.md#L197) 禁止 SQL 模板和任务解法。探查用 SQL 证据与解题模板并非同一回事，但这些描述的边界不同，不能假定论文中的每种产物都能按当前 Skill 原样重建。

## 结论边界

- 当前代码可作为内容模型、查询接口、轨迹记录和演化流程的参考；不能据此认定完整的三层演化保证与论文实验结果已经复现。
- Rolling-Trajectory、无标准答案的 LLM Judge、可视化和发布状态机，是仓库的产品化能力或扩展，不宜逐项要求论文必须描述。
- HTTP、多用户隔离、中文检索与 Logmind 的关系属于应用工程问题；它们不能直接证明论文实现不一致。
- 本次读了论文方法、实验及相关图表，并核对源码和 Skill；没有重新运行付费模型 benchmark，也不据此判断论文实验结果真伪。
