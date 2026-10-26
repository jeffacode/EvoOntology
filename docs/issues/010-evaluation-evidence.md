# EVO-010：版本评测的证据来源与可比性

## 基本信息

- 类型：实现缺口
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：发布检查相信提交的评分，缺少结果来源核验
- 核对：源码核对及临时数据复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

核心问题是：评分是否对应实际执行过的任务，新旧版本是否在可比条件下运行，以及“没有不可接受退步”究竟由什么证据支持。

gate_input 信任调用方是 Evo 当前接口的限制；这里不预设评测必须由语义服务还是 Logmind 执行。

## 当前情况

发布从候选评测记录中读取 gate_input。GT 分支检查配对分数、唯一 case_ids 和数值；是否存在不可接受的退步，由调用方提交一个布尔值。

逐题结果和产物路径可以为空，provenance 也不决定能否发布。

## 不合理的地方

格式正确不代表评测实际执行过，也不能证明新旧版本使用了同一批题、数据和模型。单独提交“没有严重退步”也不等于逐题核对过。

## 影响

遗漏结果、错用评分或提交未经验证的判断，都可能进入正式发布决定。

## 代码位置

- [evoontology/evolution/adapter.py](../../evoontology/evolution/adapter.py#L43)：`normalize_result`，第 43 行。cases 和 artifact_paths 可以为空。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L300)：`record_evaluation`，第 300 行。保存调用方提交的摘要。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L432)：`_publication_gate`，第 432 行。直接使用 gate_input 作决定。

## 核对依据与边界

临时实验中，没有真实评测产物，只提交一题模拟分数和 unacceptable_regressions=false，也能通过发布。此实验只验证接口门槛，不指控现有业务数据或作者实验造假。

另外，BIRD adapter 的常规输出没有 gate_input，直接记录后不能发布，报“Record candidate evaluation gate_input before publication”。通用 adapter 注释所说“metrics 足够作决定”不等于当前发布入口已接通。结果文件串用及验证题范围问题分别见 EVO-030、EVO-031。

[返回问题索引](README.md)

