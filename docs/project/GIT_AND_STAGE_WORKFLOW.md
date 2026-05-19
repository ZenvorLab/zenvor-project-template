# Git and Stage Workflow

本文用于统一多 Agent 项目的 Git 工作流、commit message 格式、阶段编号、小阶段拆分、tag 使用和完成汇报格式。规则适用于基于本模板初始化的后续项目，不绑定某个具体 Agent、模型或业务项目。

## 分支规则

推荐分支用途：

- `main`：稳定主线。
- `stageX` 或 `stageX_<name>`：大阶段开发分支。
- `feature/<short-name>`：功能分支。
- `fix/<short-name>`：修复分支。
- `docs/<short-name>`：文档维护分支。

通用规则：

- 不允许无确认 force push。
- 只提交本任务相关文件。
- 如果用户明确说“本次先不 git”，Agent 不执行 `git add`、`git commit`、`git push`。
- 如果当前分支不符合任务要求，先报告，不擅自切换。
- 如果存在与本任务无关的未提交修改，先报告，不覆盖。

## Commit Message 格式

采用简化 Conventional Commits：

```text
<type>(<scope>): <summary>
```

要求：

- `type` 使用小写。
- `scope` 使用小写，必要时可包含 stage 编号，如 `stage0.1`。
- `summary` 使用英文或项目约定语言均可，但应简短明确。
- 不要使用含糊提交信息，例如 `update`、`fix bug`、`修改一下`。
- 每个 commit 应尽量对应一个明确任务。

推荐 `type`：

- `docs`：文档变更。
- `feat`：新功能。
- `fix`：问题修复。
- `refactor`：重构，不改变外部行为。
- `test`：测试相关。
- `chore`：仓库维护、配置、杂项。
- `build`：构建、依赖、打包相关。
- `ci`：CI / workflow 相关。
- `data`：样例数据、schema、数据结构草案相关。

推荐 `scope`：

- `repo`
- `docs`
- `workflow`
- `privacy`
- `stage0`
- `stage0.1`
- `stage1`
- `stage1.1`
- `scripts`
- `configs`
- `schemas`
- `samples`
- `tests`

示例：

```text
docs(workflow): add git and stage policy
docs(stage0.1): align agent collaboration rules
feat(stage1.1): add initial module entry pages
fix(schema): correct sample field name
refactor(scripts): simplify smoke check entry
test(schema): add schema validation command
chore(repo): update ignore rules
ci(repo): add minimal validation workflow
data(samples): add fake task examples
```

## Stage 编号规则

定义：

- `Stage X`：大阶段，例如 `Stage 0`、`Stage 1`、`Stage 2`。
- `Stage X.Y`：阶段内子阶段，例如 `Stage 1.1`、`Stage 1.2`。
- `Stage X.Y.Z`：更细的小任务、验证点或小闭环，例如 `Stage 1.2.1`。

使用原则：

- `Stage X` 用于路线级推进。
- `Stage X.Y` 用于能独立汇报、独立提交、独立验证的小开发阶段。
- `Stage X.Y.Z` 用于更细粒度的任务或验证点，不一定每个都需要单独文档。
- 小阶段编号必须服务于项目管理，不要为了显得复杂而过度拆分。
- docs/project_status.md 只记录当前阶段和最近关键结论，不保存完整历史。
- 详细阶段历史放入 docs/stage*/ 或 docs/project/history/。

示例：

```text
Stage 0 - Project initialization
Stage 0.1 - Agent workflow alignment
Stage 0.2 - Git and stage policy setup
Stage 1 - First usable prototype
Stage 1.1 - Entry page and module index
Stage 1.2 - Sample data display
Stage 1.2.1 - Schema validation smoke check
```

## Tag 规则

- 大阶段完成并通过基本验证后，可以打 tag。
- tag 格式建议：
  - `stage0-complete`
  - `stage1-prototype`
  - `v0.1.0`
- 文档整理、小修复、小阶段未闭环时通常不打 tag。
- tag 必须对应一个清晰、可回退的节点。
- 打 tag 前应确认 docs/project_status.md 已更新。
- 未经用户明确确认，不创建或推送 tag。

## project_status.md 与 Stage Summary 边界

- docs/project_status.md 是轻量当前状态入口。
- 不保存完整日志。
- 不保存长命令输出。
- 不复制完整 stage summary。
- stage plan / summary / closure 放入 docs/stage*/。
- 历史记录放入 docs/project/history/。

## Agent 汇报中的 Git 信息

如果任务执行了 Git 操作，完成后必须汇报：

- 当前分支。
- commit hash。
- commit message。
- 是否 push。
- remote。
- 是否存在未提交修改。
- 是否创建 tag。
