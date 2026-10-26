# EVO-018：演化遇到暂时阻塞时的生命周期与继续条件

## 基本信息

- 类型：能力缺口
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：缺数据结束后，原演进不能恢复
- 核对：临时数据复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

用户已提出验证不足时保留过程、等待补充后继续。核心问题是：暂时等待与本轮结束分别意味着什么，继续时原候选、评测条件和问题记录还是否有效。Logmind 自己已有等待状态。

Evo 不能恢复 incomplete 是当前实现证据；新服务不必继承它的状态名，也不预设一定复用同一个 run ID。

## 当前情况

Evo 把 incomplete 当作终态。resume() 只接受 running；预算扩展也只允许 running。Logmind 自己已有可恢复的等待状态，但不会自动改变 Evo 的状态规则。

## 不合理的地方

相对用户希望“验证不足时等待补充后继续”的需求，Evo 终态语义不能直接表达这类继续关系。后来补齐数据，原 run 仍不能直接继续；新服务是否沿用同一个 run ID 属于另外的实现选择。

## 影响

已保存的候选和过程还在，但继续处理必须另开 run，无法沿用原流程直接恢复。

## 代码位置

- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L47)：`TERMINAL_STATES`，第 47 行。incomplete 属于终态。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L155)：`resume`，第 155 行。拒绝恢复终态。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L285)：`extend_budget`，第 285 行。只允许 running。
- [Logmind · backend/app/services/agent_runtime/turn/durable/contracts.py](/Users/jeffacode/Documents/logmind/backend/app/services/agent_runtime/turn/durable/contracts.py:20)：`TurnStatus / TRANSITIONS`，第 20 行。Logmind 已有 waiting_action / waiting_dependency 及恢复转换。

## 核对依据与边界

临时 run 因 missing_data 进入 incomplete 后调用 resume，得到“已经结束，请新建 run”的异常。Evo 进程中断但 run 仍为 running 时可以恢复，不在本条问题范围内。

[返回问题索引](README.md)

