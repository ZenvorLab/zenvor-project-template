# Project Status

更新时间：2026-05-19
当前分支：main
当前阶段：Stage 0.1 / template workflow refinement
当前定位：补充通用 Git、commit message、Stage 编号和 tag 规则，使模板库更适合多 Agent 长期项目复用。
当前最高优先级：完成模板库的协作规则补强，不进入具体业务项目开发。

## 当前摘要

本仓库是通用项目模板库，当前只维护长期可复用的项目管理文档。当前任务不写业务代码，不创建具体项目功能，不引入依赖。

## 当前禁止事项

- 不写业务代码。
- 不修改核心逻辑。
- 不引入新依赖。
- 不创建具体项目的 stage plan。
- 不创建 outputs/ 或 logs/。
- 不提交敏感数据。
- 不把模板规则写成某个具体项目已经完成的成果。

## 最近关键结论

- 模板库已有多 Agent 协作入口。
- 需要补充 commit message 格式。
- 需要补充 Stage X / Stage X.Y / Stage X.Y.Z 编号规则。
- 需要补充 tag 使用边界。
- 需要在任务模板中加入 Git 汇报字段。
- 已补充远程 Agent A / Agent B 编排规则，用于支持用户不在电脑前时的任务转交、结果汇报和权限确认。

## 下一步建议

- 将该模板规则同步应用到具体项目仓库。
- 每个具体项目应根据自身目录结构、运行环境和隐私边界裁剪模板规则。
- 进入业务实现前，应先确认该项目自己的当前阶段、禁止事项和验证边界。
- 如需远程连续执行任务，先在本地 Agent A 工作区跑通文件队列模式，再接入 Telegram、飞书或其它消息通道。

## 历史详情入口

- docs/README.md
- docs/project/AI_WORKFLOW.md
- docs/project/PROJECT_STATUS_POLICY.md
- docs/project/TASK_PROMPT_TEMPLATE.md
- docs/project/DEVELOPMENT_BOUNDARIES.md
- docs/project/AGENT_USAGE_POLICY.md
- docs/project/GIT_AND_STAGE_WORKFLOW.md
- docs/project/REMOTE_AGENT_ORCHESTRATION.md
- docs/project/REPO_STRUCTURE.md
