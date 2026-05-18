# Repository Structure

推荐结构：

```text
docs/
  README.md
  project_status.md
  project/
    AI_WORKFLOW.md
    PROJECT_STATUS_POLICY.md
    TASK_PROMPT_TEMPLATE.md
    DEVELOPMENT_BOUNDARIES.md
    AGENT_USAGE_POLICY.md
    REPO_STRUCTURE.md
  stage0/
  stage1/
  stage2/

scripts/
  stage0/
  stage1/
  smoke/
  analysis/

configs/

outputs/       # ignored
logs/          # ignored
```

## 目录说明

- docs/project_status.md 只放当前状态。
- docs/stage*/ 放阶段计划、总结、分析、closure。
- docs/project/ 放长期项目规则。
- scripts/stage*/ 放阶段脚本。
- scripts/smoke/ 放轻量冒烟验证脚本。
- scripts/analysis/ 放分析脚本。
- configs/ 放配置和 schema。
- outputs/ 不提交。
- logs/ 不提交。

## 阅读建议

小任务优先阅读 AGENTS.md、docs/project_status.md 和直接相关文件。

跨阶段整理任务再阅读 docs/project/REPO_STRUCTURE.md、docs/README.md 和多个 stage summary。
