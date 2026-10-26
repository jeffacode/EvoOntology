# EVO-026：并发演进会串写状态，已接受的 run 也能变回运行中

## 基本信息

- 类型：代码缺陷
- 归类：Evo 当前实现问题
- 状态：实现参考（未修复，不要求先修 Evo）
- 核对：并发与延迟写入复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

本条确认的是 Evo 当前代码的具体行为，不证明重新实现的服务也有同样问题，不作为先修旧代码的任务。

它对应的新服务设计关注点是：并发操作和迟到写入是否使同一演化过程产生矛盾状态。关联 [EVO-018](018-incomplete-resume.md)。归入实现问题不表示可以忽略这一正确性要求。

## 当前情况

run 编号由扫描目录得到，写入使用固定临时文件名。Session 还会缓存 run，保存时不核对磁盘是否已被其他调用更新。

## 不合理的地方

两个执行器可以争用同一个 run；晚到的旧状态也能覆盖已经完成的新状态。

## 影响

调用返回值、磁盘记录和实际生效版本可能互相矛盾。多进程共享 workspace 时尤其容易暴露。

## 代码位置

- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L125)：`start_run`，第 125 行。检查运行状态、分配编号后写入。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L82)：`_write_json_atomic`，第 82 行。固定 .tmp 文件名。
- [evoontology/evolution/session.py](../../evoontology/evolution/session.py#L547)：`_require_run / _save_run`，第 547 行。读取缓存并整份覆盖保存。

## 核对依据与边界

两线程同时算出 run_1：一个报文件不存在，另一个成功返回，但磁盘保存的是失败调用的数据。另一次用两个 Session：A accept 后，B 提交延迟 Reject，磁盘 accepted 变回 running，active 仍为新版本。当前单个 stdio 进程顺序处理，不会自行产生这种并发。

[返回问题索引](README.md)

