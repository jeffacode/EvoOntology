# EVO-035：本体预览只提供服务端地址，远程 Logmind 用户拿不到页面

## 基本信息

- 类型：接入差异
- 归类：原包复用限制
- 状态：仅复用旧组件时适用
- 核对：临时渲染与源码核对；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

这是复用 Evo 本地预览器时的远程展示限制。新服务是否提供本体浏览页面及其产品范围尚不由本条决定；不能把重写这套 HTML 预览器当作语义服务必做功能。

## 当前情况

Evo 默认尝试在运行服务的机器上打开 HTML；关闭打开动作后，只返回该机器的文件路径。codex 展示模式提供的地址也绑定 127.0.0.1。

## 不合理的地方

独立部署时，服务所在机器和用户浏览器不是同一台机器。服务端文件路径或回环地址不能直接作为用户的页面入口。

## 影响

构建或演进可以成功，但远程用户仍无法按返回地址查看本体。Logmind 现有会话文件下载能力不会自动接管这个服务端 HTML。

## 代码位置

- [evoontology/runtime/ops.py](../../evoontology/runtime/ops.py#L43)：`visualize_ontology`，第 43 行。仅支持 external / codex 展示方式。
- [evoontology/visualization/preview.py](../../evoontology/visualization/preview.py#L114)：`serve`，第 114 行。只监听 127.0.0.1，返回本机 URL。
- [Logmind · backend/app/services/session_runtime/turns/results.py](/Users/jeffacode/Documents/logmind/backend/app/services/session_runtime/turns/results.py:120)：`结果导出`，第 120 行。已有下载地址基于 Logmind 会话文件。

## 核对依据与边界

临时生成真实 HTML，拦截浏览器打开动作：默认尝试 file:// 服务端路径，browser_url 为空；open_browser=false 仍只有服务器文件路径。未启动真实浏览器。仅针对远程部署，本地插件同机使用不受此限制。

[返回问题索引](README.md)

