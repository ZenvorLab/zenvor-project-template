# Project Status

更新时间：2026-05-24
当前分支：main
当前阶段：Stage 0 / project template
当前定位：通用项目模板库，提供多 Agent 协作框架和项目管理文档模板。新项目克隆后根据自身需求裁剪。
当前最高优先级：让 GPT 和后续 Agent 通过本文件快速了解当前状态和上一步执行结果。

## 当前摘要

本仓库是通用项目模板库，只维护长期可复用的项目管理文档和 Agent 协作规则。不写业务代码、不创建功能、不引入依赖，不保存具体项目或真实消息流水。

docs/project_status.md 是当前状态入口。每次任务完成后，执行 Agent 必须把本次完整汇报写入“最近一次执行汇报”，供 GPT 或后续 Agent 快速接续。

## 当前禁止事项

- 不写业务代码。
- 不修改核心逻辑。
- 不引入新依赖。
- 不创建具体项目的 stage plan。
- 不创建 outputs/ 或 logs/。
- 不提交敏感数据。
- 不把模板规则写成某个具体项目已经完成的成果。
- 不在模板中保存真实任务队列、聊天消息、个人账号、运行流水或具体项目过程记录。
- 不在本文件累积长期流水；只保留最近一次执行汇报，历史详情放入专门历史文档或 Git 记录。

## 最近关键结论

- 状态入口只保留模板级当前状态；具体项目克隆后应替换为该项目自己的当前状态。
- 长期规则放在 docs/project/，历史或阶段详情由具体项目按需放入 docs/stage*/ 或 docs/project/history/。
- 最近一次任务执行汇报必须写入本文件；聊天最终回复不能作为唯一状态来源。

## 最近一次执行汇报

- 时间：2026-05-24
- 任务：调整项目状态文档和相关规则，要求每次任务完成后把完整汇报写入 docs/project_status.md，方便 GPT 快速了解上一步执行情况。
- 修改文件：
  - docs/project_status.md
  - AGENTS.md
  - docs/project/PROJECT_STATUS_POLICY.md
  - docs/project/TASK_PROMPT_TEMPLATE.md
  - docs/project/GPT_REMOTE_TASK_PROTOCOL.md
  - docs/project/REMOTE_AGENT_ORCHESTRATION.md
  - docs/project/GIT_AND_STAGE_WORKFLOW.md
- 新增或更新的规则：
  - docs/project_status.md 增加“最近一次执行汇报”作为固定接续入口。
  - 每次任务完成后，执行 Agent 必须把最终汇报写入本文件。
  - 本文件只保留最近一次完整汇报，不累积长期流水。
- 运行命令：
  - git diff --check
  - Select-String 检查旧标题和待验证占位残留
- 通过项：
  - git diff --check 通过；仅有 LF/CRLF 提示，无 whitespace 错误。
  - 旧标题“最近任务结果”和待验证占位已清理。
- 未运行项及原因：
  - 未运行应用测试；本次仅修改项目管理文档，无应用代码。
- 失败或不确定结果：
  - 暂无已知失败。
- Git 状态：
  - 本次修改由承载本汇报的提交记录保存；具体 commit hash 和 push 状态以 Git 最新记录和最终回复为准。
- outputs / logs：
  - 未产生 outputs/ 或 logs/。
- 敏感或临时文件：
  - 未涉及敏感文件、token、账号、队列或本地临时产物。
- 当前是否可以进入下一步：
  - 本次规则修改完成并验证后，可以继续使用该模板作为项目初始化基线。

## 下一步建议

- 每个具体项目应根据自身目录结构、运行环境和隐私边界裁剪模板规则。
- 进入业务实现前，应先确认该项目自己的当前阶段、禁止事项和验证边界。
- 如需使用远程协调 Agent 工作流，参考 docs/project/REMOTE_AGENT_ORCHESTRATION.md。

## 历史详情入口

- docs/README.md
- docs/project/AI_WORKFLOW.md
- docs/project/PROJECT_STATUS_POLICY.md
- docs/project/TASK_PROMPT_TEMPLATE.md
- docs/project/DEVELOPMENT_BOUNDARIES.md
- docs/project/AGENT_USAGE_POLICY.md
- docs/project/GIT_AND_STAGE_WORKFLOW.md
- docs/project/REMOTE_AGENT_ORCHESTRATION.md
- docs/project/GPT_REMOTE_TASK_PROTOCOL.md
- docs/project/REPO_STRUCTURE.md
