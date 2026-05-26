# Git and Stage Workflow

## 分支规则

- `main`：稳定主线。
- `stageX` 或 `stageX_<name>`：大阶段开发分支。
- `feature/<name>` / `fix/<name>` / `docs/<name>`：功能/修复/文档分支。

通用规则：
- 不允许 force push 到 main。
- 只提交本任务相关文件。
- 用户说"本次先不 git"时不执行 git 操作。
- 存在与本任务无关的未提交修改时先报告，不覆盖。

## Commit Message

格式：`<type>(<scope>): <summary>`

推荐 type：`docs` `feat` `fix` `refactor` `test` `chore` `build` `ci` `data`

推荐 scope：`repo` `docs` `workflow` `stage0` `stage1` `scripts` `configs` `schemas` `tests`

示例：
```
docs(workflow): add git and stage policy
feat(stage1.1): add module entry pages
fix(schema): correct sample field name
```

## Stage 编号

- `Stage X`：大阶段（不超过 10 个，Stage 0 ~ Stage 9）。
- `Stage X.Y`：子阶段，可独立汇报和提交。
- `Stage X.Y.Z`：更细粒度的任务或验证点。

每次任务完成后必须更新 docs/project_status.md，确保阶段信息与实际进度一致。

## Tag 规则

- 大阶段完成并验证后可以打 tag，格式建议：`stage0-complete`、`v0.1.0`
- 小修复或未闭环阶段不打 tag。
- 未经用户确认不创建或推送 tag。

## project_status.md 与 Stage Summary 的边界

- docs/project_status.md：轻量当前状态入口，只保留最近一次执行汇报。
- docs/stage<N>/stage<N>.<M>/：阶段计划、总结、closure（如 docs/stage1/stage1.1/）。
- docs/project/history/：历史记录（如需要）。
