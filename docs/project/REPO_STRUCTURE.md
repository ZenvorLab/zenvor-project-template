# Repository Structure

## 推荐结构

```
AGENTS.md              # 所有 Agent 通用规则（权威入口）
CLAUDE.md              # Claude Code 专属配置
docs/
  project_status.md    # 轻量当前状态入口
  project/             # 长期项目规则
    GIT_AND_STAGE_WORKFLOW.md
    REMOTE_AGENT_ORCHESTRATION.md
    TASK_PROMPT_TEMPLATE.md
    REPO_STRUCTURE.md
  stage0/
    stage0.1/          # 阶段 0.1：计划、总结、closure
    stage0.2/
  stage1/
    stage1.1/
    stage1.2/

scripts/               # 按阶段组织脚本
configs/
outputs/               # 不提交
logs/                  # 不提交
```

## 目录说明

| 目录 | 用途 | 提交 |
|---|---|---|
| docs/project_status.md | 当前阶段和最近一次执行汇报 | 是 |
| docs/project/ | 长期项目规则 | 是 |
| docs/stage<N>/stage<N>.<M>/ | 阶段计划、总结、closure | 是 |
| scripts/ | 阶段脚本、冒烟验证、分析脚本 | 按需 |
| configs/ | 配置和 schema | 按需 |
| outputs/ | 运行产物 | 否 |
| logs/ | 日志 | 否 |

## 阅读建议

- 小任务：AGENTS.md + docs/project_status.md + 1-3 个直接相关文件。
- 阶段推进：加上当前 stage plan/summary。
- 跨阶段整理：加上 docs/project/ 下的规则文档。
