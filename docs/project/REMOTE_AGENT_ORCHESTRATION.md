# Remote Agent Orchestration

> 本文是可选增强方案，仅适用于用户不在电脑前、需要远程转交任务给本地执行 Agent 的场景。
> 本地开发工作流不依赖本文；不启用协调 Agent 时，仓库行为完全不变。

本文只定义角色、权限边界和汇报规则，不保存真实消息记录、队列内容、账号信息、token、日志路径或某次真实执行过程。

命名约定：

- Agent A / Coordinator：Zenvoy。
- Agent B / Executor：Zenforge。

## 基本原则

- 仓库仍以 AGENTS.md、docs/project_status.md 和当前任务提示词为准。
- 远程编排只负责传递任务、执行低风险授权操作、汇报结果。
- 任务提示词应原样传递，不能被协调层改写成更宽的授权。
- 真实消息通道、执行器命令、token、queue、logs、outputs 和本地配置必须留在业务仓库之外。
- 自动化模式可以提高效率，但不能绕过用户对高风险操作的确认。

## 角色定义

### 用户

- 决定是否启用远程编排。
- 选择自动化模式和执行器。
- 确认高风险操作。
- 在需要时提供下一步任务提示词。

### 规划 Agent

- 检查仓库当前状态。
- 判断阶段边界、风险和下一步方向。
- 生成可复制给执行链路的任务提示词。
- 如果连接方式是只读的，只能检查状态和生成任务，不能假装能写文件、commit 或 push。

### Agent A / Zenvoy / Coordinator

- 接收用户或规划 Agent 给出的任务提示词。
- 保存原始任务并转交给 Agent B / Zenforge。
- 管理权限门、执行器选择、暂停/继续和结果汇总。
- 遇到高风险操作时向用户请求确认。
- 不把本地队列、通信记录或凭据提交到业务仓库。

### Agent B / Zenforge / Executor

- 在本地工作区执行具体文件修改、命令和验证。
- 遵守 AGENTS.md、docs/project_status.md 和当前任务提示词。
- 不擅自扩大任务范围。
- 按任务授权决定是否 commit / push。
- 完成后向 Agent A / Zenvoy 汇报真实结果，并按边界更新 docs/project_status.md。

## 推荐控制工作区

协调工作区应独立于业务仓库，示例结构如下：

```text
<control-workspace>/
  config/
  prompts/
  queue/
  scripts/
  logs/
```

这些内容默认不属于业务仓库，尤其不能提交通信 token、本地队列、执行日志、临时文件或用户私密数据。

## 自动化模式

### Mode 1: confirm-every-step

- Agent A / Zenvoy 每次收到任务后先请求用户确认。
- 用户确认后才转交 Agent B / Zenforge。
- 适合调试、首次接入或不信任任务来源时。

### Mode 2: report-on-task-complete

- Agent A / Zenvoy 直接转交低风险任务。
- Agent B / Zenforge 按任务授权执行、验证，并在完成后汇报。
- 高风险操作仍暂停询问。

### Mode 3: autonomous-until-risk

- Agent A / Zenvoy 可连续推进用户已授权的低风险维护任务。
- 遇到高风险操作、阶段跳转或范围扩大时暂停询问。
- 适合用户已明确授权的例行维护。

## 权限边界

默认可自动执行：

- 读取 AGENTS.md、docs/project_status.md 和任务直接相关文件。
- 编辑任务明确允许的文件。
- 运行任务指定的本地验证命令。
- 更新 docs/project_status.md 的轻量当前状态。
- 在任务明确授权时执行 git add / commit / push。

默认需要确认：

- 创建或推送 tag。
- force push。
- 删除大量文件或清空目录。
- 安装、升级或移除依赖。
- 修改系统权限、防火墙、SSH key、token 或凭据。
- 真实调用外部写入型 API。
- 读取、转发或提交敏感数据。
- 扩大任务范围、跨阶段推进或修改任务提示词授权。

默认禁止：

- 将 token、cookie、API key、session 信息写入仓库。
- 将 queue、logs、outputs、缓存和临时文件提交到仓库。
- 把失败、dry-run、simulation validation 写成真实完成。
- 未经确认覆盖用户未提交修改。
- 在模板仓库中记录某次真实消息、队列编号或执行流水。

## 任务流

```text
规划 Agent 或用户生成任务提示词
  -> Agent A / Zenvoy 接收并保存原始任务
  -> Agent A / Zenvoy 按模式判断是否需要确认
  -> Agent A / Zenvoy 原样转交 Agent B / Zenforge
  -> Agent B / Zenforge 执行、验证、按授权处理 Git
  -> Agent B / Zenforge 汇报真实结果
  -> Agent A / Zenvoy 汇总状态并发给用户
```

如果规划 Agent 只能只读仓库，则下一轮检查应以 Git 远程状态、commit 记录和 docs/project_status.md 为事实来源。

## 汇报要求

Agent A / Zenvoy 汇报必须包含：

- 当前项目和分支。
- 当前执行器。
- 任务摘要。
- 修改文件列表。
- 验证命令及结果。
- Git 状态：commit / push / tag / 是否有未提交修改。
- 问题、失败、不确定结果或残余风险。
- 是否产生 outputs、logs、敏感文件或临时文件。
- 需要用户确认的问题。

Agent B / Zenforge 汇报必须包含 AGENTS.md 中要求的完整执行结果，并明确区分 planning、offline artifact、dry-run、interface validation、simulation validation、real execution 和 production capability。

## 执行器配置

具体执行器启动命令、模型参数、权限配置和消息桥接脚本应放在控制工作区或用户环境中，不写入业务仓库。

模板仓库只保留以下原则：

- 不固化某个 Agent 或模型的私有能力。
- 不在模板中提交跳过权限审查的危险命令。
- 不在模板中提交本地绝对路径、账号、token、队列内容或真实消息记录。
- 执行器可替换，但仓库规则和任务边界不变。

## 已知限制

- 只读仓库连接不能直接写文件、commit 或 push。
- 定时检查能力不等于自动执行能力。
- 远程编排无法替代用户对高风险操作、真实执行和阶段推进的确认。
