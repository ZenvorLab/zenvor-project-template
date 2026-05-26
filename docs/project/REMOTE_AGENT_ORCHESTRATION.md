# Remote Agent Orchestration

> 本文是可选增强方案，适用于用户不在电脑前、需要远程转交任务给本地执行 Agent 的场景。
> 不启用时对本地开发无任何影响。

命名约定：Agent A / Coordinator = Zenvoy，Agent B / Executor = Zenforge。

## 基本原则

- 仓库仍以 AGENTS.md、docs/project_status.md 和当前任务提示词为准。
- 远程编排只负责传递任务、执行低风险操作、汇报结果。
- 真实消息通道、token、queue、logs、outputs 不提交到业务仓库。

## 角色定义

| 角色 | 职责 |
|---|---|
| 用户 | 决定是否启用、确认高风险操作、提供任务提示词 |
| 规划 Agent (GPT) | 检查状态、判断阶段、给出任务提示词 |
| Agent A / Zenvoy | 接收任务、管理权限、转发 Agent B、汇总结果 |
| Agent B / Zenforge | 在本地执行修改、验证、按授权处理 Git、汇报结果 |

## GPT 角色协议（给只读仓库连接的 ChatGPT）

GPT 作为战略顾问，通过只读仓库连接检查项目进展：

1. 读取 `docs/project_status.md` 了解当前阶段和最近执行汇报。
2. 判断阶段：任务是否完成？是否需要修正方向？
3. 给出可直接复制的下一步任务提示词，包含任务目标、执行步骤、边界和验证方式。

GPT 不应：试图写文件（只读连接会失败）、假装能 commit、跳过 project_status.md 中的禁止事项。

任务提示词格式：

```
## 下一步任务

**任务目标：** [一句话描述]

**具体执行：**
1. [步骤]
2. [步骤]

**边界：**
- 不修改：[文件/模块]
- 验证方式：[如何验证]
```

## 自动化模式

| 模式 | 行为 |
|---|---|
| confirm-every-step | 每次任务需用户确认后才转发 |
| report-on-task-complete | 低风险任务直接转发，完成后汇报 |
| autonomous-until-risk | 连续推进已授权任务，高风险或跨阶段暂停 |

## 权限边界

默认可自动执行：读取入口文档、编辑授权文件、运行指定验证命令、更新 project_status.md、按授权执行 git 操作。

需要确认：tag、force push、删除大量文件、安装/移除依赖、修改权限、外部写入型 API、跨阶段推进。

默认禁止：提交 token/cookie/API key、提交 queue/logs/outputs/缓存、把失败写成成功、在模板仓库记录真实消息流水。

## 任务流

```
规划 Agent 或用户 → Agent A 接收 → Agent A 判断是否需要确认
  → Agent A 原样转发 Agent B → Agent B 执行并汇报
  → Agent A 汇总并通知用户
```

Agent B 完成后必须先把完整汇报写入 docs/project_status.md，再按授权提交和推送。
