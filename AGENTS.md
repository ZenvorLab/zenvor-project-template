# Agent Collaboration Rules

本文件是仓库内所有 Agent 的统一入口，适用于 ChatGPT、Codex、Claude Code、Cursor 及其它 AI Agent 工具。

## 入口

进入仓库后先读：
- AGENTS.md（本文件，所有 Agent 通用）
- CLAUDE.md（Claude Code 专属配置）
- docs/project_status.md（当前阶段和状态）

小任务只读这 3 个和直接相关的 1-3 个文件。跨阶段整理才读 docs/project/ 下的详细规则。

本框架不依赖某个特定 Agent 的私有能力。所有任务以仓库文档、当前任务提示词和实际文件状态为准。Agent 不应依赖聊天上下文长期记忆作为唯一依据，上下文不足时应优先读取轻量状态文档而非全仓库扫描。

## 核心规则

- 只执行用户明确指定的任务，不主动扩大范围。
- 严格区分任务状态：planning / offline artifact / dry-run / interface validation / simulation / real execution。
  - 不把"已设计"写成"已实现"。
  - 不把 dry-run 通过写成真实运行通过。
  - 不把单点验证写成系统完成。
- 失败结果必须真实记录，不包装成成功。
- 不提交敏感数据（.env、token、密码、账号、cookie、session）。
- 不提交 outputs/、logs/、缓存和本地临时产物。
- 示例数据使用 fake/sample。

## 阶段规则

- 一个项目建议不超过 10 个大阶段，每个阶段给出较大的任务块，避免碎片化。
- 阶段编号：`Stage X`（大阶段）/ `Stage X.Y`（子阶段）/ `Stage X.Y.Z`（验证点）。
- **子阶段必须完成才能开启下一个**，不能只做了一部分就跳到新阶段，避免与总路线偏离。
- 遇到阻塞时，先搜索网络查找类似问题和解决方案，不主观猜测。
- 每个子阶段只写一个文档（如 `stage1_0_xxx.md`），不无限堆叠。
- **每次任务完成后必须更新 docs/project_status.md**，确保文档反映当前真实阶段。
- docs/project_status.md 保持轻量（建议 150~250 行），不累积长期流水，只保留最近一次执行汇报。

## Git 规则

- commit message：`<type>(<scope>): <summary>`
- 每个大阶段从 main 开分支，阶段内频繁本地 commit，不推远端。
- 阶段全部完成后 squash merge 到 main 再推送，远端只看到干净的阶段提交。
- 不允许 force push 到 main。
- 用户说"本次先不 git"时不执行 git 操作。
- 详细规则见 [docs/project/GIT_AND_STAGE_WORKFLOW.md](docs/project/GIT_AND_STAGE_WORKFLOW.md)。

## 汇报要求

每次任务完成后汇报：
- 修改了哪些文件
- 新增/更新了什么
- 运行了什么命令及结果（通过/失败/未运行及原因）
- 是否有失败或不确定结果
- 是否产生 outputs/ 或 logs/
- 是否涉及敏感或临时文件
- Git 状态
- 当前是否可以进入下一步

同一份汇报同步写入 `docs/project_status.md` 的"最近一次执行汇报"。

## 远程编排（可选）

支持通过 Agent A (Zenvoy) / Agent B (Zenforge) 远程执行任务，详见 [docs/project/REMOTE_AGENT_ORCHESTRATION.md](docs/project/REMOTE_AGENT_ORCHESTRATION.md)。

## 推荐仓库结构

```
AGENTS.md
CLAUDE.md
docs/
  project_status.md
  project/
    GIT_AND_STAGE_WORKFLOW.md
    REMOTE_AGENT_ORCHESTRATION.md
    TASK_PROMPT_TEMPLATE.md
    REPO_STRUCTURE.md
  stage0/
    stage0_0_xxx.md
  stage1/
    stage1_0_xxx.md
    stage1_1_xxx.md

scripts/
configs/
outputs/       # 不提交
logs/          # 不提交
```

详细说明见 [docs/project/REPO_STRUCTURE.md](docs/project/REPO_STRUCTURE.md)。
