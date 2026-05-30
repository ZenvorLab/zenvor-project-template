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
    stage0_0_xxx.md       # 子阶段文档，一个子阶段一个文件
  stage1/
    stage1_0_xxx.md
    stage1_1_xxx.md

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
| docs/stage<N>/ | 阶段文档，每个子阶段一个文件 | 是 |
| scripts/ | 阶段脚本、冒烟验证、分析脚本 | 按需 |
| configs/ | 配置和 schema | 按需 |
| outputs/ | 运行产物 | 否 |
| logs/ | 日志 | 否 |

