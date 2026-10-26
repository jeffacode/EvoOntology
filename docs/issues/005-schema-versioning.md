# EVO-005：语义版本是否包含 Schema，以及旧版本的解释方式

## 基本信息

- 类型：能力缺口
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：Schema 没有随本体版本独立保存和切换
- 核对：源码核对；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

如果允许 Schema 演化，版本改变的不只是记录内容，还包括字段和引用规则。核心问题是：评测或回到旧版本时，究竟承诺恢复哪些语义行为。

每版必须复制一套 Python 模型并不是既定要求；需要区分内容版本与 Schema 变化的关系。

## 当前情况

本体版本保存 terms、mappings、relations、constraints、evidence 五类记录。对象字段由 Python 模型定义；可视化中的 Schema 也读取当前代码定义。

## 不合理的地方

切换本体版本不会同时切换对应的模型结构。候选若涉及字段结构或表示规则变化，现有版本名不能完整说明它使用的 Schema。

## 影响

“回到旧本体版本”不能保证回到旧的结构解释方式；Schema 层变化也没有完整的独立版本比较边界。

## 代码位置

- [evoontology/ontology/store.py](../../evoontology/ontology/store.py#L23)：`VERSION_FILES`，第 23 行。版本目录的五类记录。
- [evoontology/ontology/models.py](../../evoontology/ontology/models.py#L58)：`Term 及其他数据类`，第 58 行。对象结构写在代码中。
- [evoontology/visualization/renderer.py](../../evoontology/visualization/renderer.py#L434)：`build_schema_view`，第 434 行。从当前模型生成 Schema 展示。

## 核对依据与边界

确认的是现有版本存储和加载方式。记录内容、类型值可以变化，不等于模型结构已按本体版本独立管理。

[返回问题索引](README.md)

