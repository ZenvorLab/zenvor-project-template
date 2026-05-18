# Project Status

更新时间：2026-05-18
当前分支：以本地 git 当前分支为准
当前阶段：Stage 0 / project init
当前定位：初始化多 Agent 通用项目协作框架
当前最高优先级：建立可复用的轻量项目管理文档入口

## 当前摘要

本仓库正在建立一套可复用的多 Agent 协作框架，用于后续项目初始化时快速同步工作流、阶段边界、阅读预算、汇报格式和验证要求。

当前任务只涉及项目管理文档和忽略规则，不写业务功能代码，不修改核心数据结构，不执行应用逻辑。

## 当前禁止事项

- 不写业务代码。
- 不修改核心逻辑。
- 不引入新依赖。
- 不提交敏感数据。
- 不提交 outputs/。
- 不提交 logs/。
- 不把 planning 写成 execution。
- 不把 offline artifact、dry-run、interface validation、simulation validation 写成真实能力或 real execution。

## 最近关键结论

- AGENTS.md 应作为所有 Agent 的统一入口，不只服务 Codex。
- docs/project_status.md 应保持轻量，不作为完整开发日志。
- 多 Agent 协作规则应长期放在 docs/project/。
- 详细阶段历史应放在 docs/stage*/ 或 docs/project/history/。

## 下一步建议

- 根据具体项目创建 docs/stage0/ 下的 stage0 plan。
- 根据具体项目需要创建项目初始 schema 或配置草案。
- 在进入任何业务实现前，先确认当前阶段目标和禁止事项。

## 历史详情入口

- docs/README.md
- docs/project/AI_WORKFLOW.md
- docs/project/PROJECT_STATUS_POLICY.md
- docs/project/TASK_PROMPT_TEMPLATE.md
- docs/project/DEVELOPMENT_BOUNDARIES.md
- docs/project/AGENT_USAGE_POLICY.md
- docs/project/REPO_STRUCTURE.md
