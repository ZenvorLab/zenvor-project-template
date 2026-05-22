# Project Status

更新时间：2026-05-23
当前分支：main
当前阶段：Stage 0 / project template
当前定位：通用项目模板库，提供多 Agent 协作框架和项目管理文档模板。新项目克隆后根据自身需求裁剪。
当前最高优先级：保持模板干净、通用、可裁剪。不进入具体业务开发。

## 当前摘要

本仓库是通用项目模板库，只维护长期可复用的项目管理文档和 Agent 协作规则。不写业务代码、不创建功能、不引入依赖，不保存具体项目或真实消息流水。

## 当前禁止事项

- 不写业务代码。
- 不修改核心逻辑。
- 不引入新依赖。
- 不创建具体项目的 stage plan。
- 不创建 outputs/ 或 logs/。
- 不提交敏感数据。
- 不把模板规则写成某个具体项目已经完成的成果。
- 不在模板中保存真实任务队列、聊天消息、个人账号、运行流水或具体项目过程记录。

## 最近关键结论

- 状态入口只保留模板级当前状态；具体项目克隆后应替换为该项目自己的当前状态。
- 长期规则放在 docs/project/，历史或阶段详情由具体项目按需放入 docs/stage*/ 或 docs/project/history/。

## 最近任务结果

暂无需要保留的模板级任务结果。具体项目可按需记录最近一次关键任务结果，但不应写成长期流水日志。

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
