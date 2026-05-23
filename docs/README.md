# Documentation Index

本文是项目文档索引。新 Agent 进入仓库后，应先阅读根目录 AGENTS.md（通用）、CLAUDE.md（Claude Code 专属）和 docs/project_status.md，再根据任务需要读取本索引中的具体文档。

## 当前状态入口

- [project_status.md](project_status.md)：轻量当前状态入口，记录当前阶段、当前定位、当前禁止事项、最近关键结论、最近一次执行汇报和下一步建议。

## 多 Agent 项目规则

- [project/AI_WORKFLOW.md](project/AI_WORKFLOW.md)：多 Agent 分工、标准协作流程、上下文和额度节省规则。
- [project/PROJECT_STATUS_POLICY.md](project/PROJECT_STATUS_POLICY.md)：docs/project_status.md 的用途、模板和限制。
- [project/TASK_PROMPT_TEMPLATE.md](project/TASK_PROMPT_TEMPLATE.md)：通用任务提示词，以及文档维护、阶段推进、bug 修复、closure / failure analysis 模板。
- [project/DEVELOPMENT_BOUNDARIES.md](project/DEVELOPMENT_BOUNDARIES.md)：planning、artifact、validation、execution 的边界规则。
- [project/AGENT_USAGE_POLICY.md](project/AGENT_USAGE_POLICY.md)：Codex、Claude Code、DeepSeek、其它 VSCode Agent 的使用和切换策略。
- [project/GIT_AND_STAGE_WORKFLOW.md](project/GIT_AND_STAGE_WORKFLOW.md)：Git 工作流、commit message、Stage 编号、tag 和汇报规则。
- [project/REMOTE_AGENT_ORCHESTRATION.md](project/REMOTE_AGENT_ORCHESTRATION.md)：用户不在电脑前时的 Agent A / Agent B 远程编排、权限确认和消息汇报规则。
- [project/GPT_REMOTE_TASK_PROTOCOL.md](project/GPT_REMOTE_TASK_PROTOCOL.md)：给 ChatGPT 阅读的角色说明 — 告诉 GPT 如何检查进展、给出下一步任务提示词。
- [project/REPO_STRUCTURE.md](project/REPO_STRUCTURE.md)：推荐仓库结构和目录用途。

## 阶段文档

详细历史、阶段计划、阶段总结和 closure 文档应放在 docs/stage*/ 下。

docs/project_status.md 不复制完整阶段历史，不保存长命令日志，不保存 outputs 内容；它只保留最近一次完整执行汇报，下一次任务完成后覆盖更新。
