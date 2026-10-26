# EVO-024：共享服务中的本体访问范围与操作权限

## 基本信息

- 类型：接入边界
- 归类：核心设计议题
- 状态：设计梳理（不是旧代码修复任务）
- 原记录：服务的默认目录不能限制可访问的本体范围
- 核对：临时目录复现；2026-10-23；Evo `c94389b`

## 与重实现目标的关系

在多个用户或项目共用服务时，核心问题是：谁能发现、读取、修改和发布哪些本体。进程能访问某个目录，与当前调用者有权限操作该本体，是两回事。

这是共享部署下的设计边界；本地插件允许多个 workspace 本身不等于漏洞，现有证据也不证明线上已发生越权。

## 当前情况

Evo 接受调用方传入的 workspace，并直接解析为文件路径。服务启动时的 --store 只是默认值。Logmind 的 MCP 包装也不会把内部用户身份直接交给 Evo。

## 不合理的地方

多个用户共用 Evo 服务时，仅配置默认目录不能限制他们只能读写自己的本体。

## 影响

如果服务进程能访问多个项目目录，调用参数也能指向这些目录；是否允许访问，当前 Evo 核心没有按用户判断。

## 代码位置

- [evoontology/runtime/mcp_server.py](../../evoontology/runtime/mcp_server.py#L78)：`SemanticMCPServer.dispatch`，第 78 行。读取客户端指定的 workspace。
- [evoontology/runtime/ops.py](../../evoontology/runtime/ops.py#L25)：`_workspace`，第 25 行。直接解析操作目录。
- [evoontology/workspace.py](../../evoontology/workspace.py#L46)：`resolve_workspace`，第 46 行。接受显式路径。
- [Logmind · backend/app/services/agent_runtime/tools/impl/mcp_tools.py](/Users/jeffacode/Documents/logmind/backend/app/services/agent_runtime/tools/impl/mcp_tools.py:73)：`MCPTool.invoke`，第 73 行。剥离宿主内部上下文。

## 核对依据与边界

服务默认指向临时目录 A，传入同级目录 B 后，save_version 和 browse 都成功。只证明默认目录不是访问边界；没有测试真实用户越权，也没有核对代理、容器或部署鉴权。当前本地插件支持多 workspace 本身不等于漏洞。

[返回问题索引](README.md)

