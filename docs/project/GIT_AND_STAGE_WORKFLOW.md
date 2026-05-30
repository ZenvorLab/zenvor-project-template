# Git and Stage Workflow

## 分支与推送策略

分支结构：
- `main`：稳定主线，只接受阶段完成后的 squash merge。
- `stageX`：大阶段开发分支，从 main 创建。

```
main ────●────────────●────────────●
         ↑            ↑            ↑
      stage1       stage2       stage3
    (全部完成后      (全部完成后
     squash merge)   squash merge)
```

工作流：
1. 从 main 开阶段分支：`git checkout -b stage1 main`
2. 阶段内频繁本地 commit，不推远端
3. 阶段全部完成后 squash 合并到 main：
   ```
   git checkout main
   git merge --squash stage1
   git commit -m "feat(stage1): 完成阶段1"
   git push
   ```
4. 下一阶段从最新 main 开分支

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

- `Stage X`：大阶段（建议不超过 10 个）。
- `Stage X.Y`：子阶段，可独立汇报和提交。
- `Stage X.Y.Z`：更细粒度的任务或验证点。
- **子阶段必须完成才能开启下一个**，不能只做了一部分就跳到新阶段。
- 遇到阻塞时先搜索网络查找解决方案，不主观猜测。

每个子阶段只写一个文档，文件名格式：`stage<X>_<Y>_<descriptive_name>.md`。
例如：`stage12_0_global_localization_preflight.md`、`stage12_1_simulation_mission_contract.md`

每次任务完成后必须更新 docs/project_status.md，确保阶段信息与实际进度一致。

## Tag 规则

- 大阶段完成并验证后可以打 tag，格式建议：`stage0-complete`、`v0.1.0`
- 小修复或未闭环阶段不打 tag。
- 未经用户确认不创建或推送 tag。

## project_status.md 与 Stage Summary 的边界

- docs/project_status.md：轻量当前状态入口，只保留最近一次执行汇报。
- docs/stage<N>/：阶段文档（如 `docs/stage1/stage1_0_xxx.md`）。
