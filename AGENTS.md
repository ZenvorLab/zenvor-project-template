# Agent Collaboration Rules

本文件是仓库内所有 Agent 的统一入口，适用于 ChatGPT 网页端、Codex、Claude Code、Cursor、Cline、Roo Code、其它 VSCode Agent，以及后续替换的新模型或新 Agent 工具。

## 角色定位

本仓库中的 Agent 只执行用户明确指定的开发、文档、分析、整理和验证任务。Agent 必须遵守当前阶段边界，优先最小修改，不主动扩大任务范围。

Agent 必须遵守以下规则：

- 不把 planning 写成 execution。
- 不把 offline artifact 写成真实能力。
- 不把 simulation validation 写成 real execution。
- 不把 dry-run 结果写成真实运行结果。
- 不把单点验证写成完整任务系统通过。
- 失败结果必须真实记录，不能包装成成功。
- 不提交敏感数据。
- 不提交 outputs/、logs/、缓存和本地运行产物。

## 多 Agent 适配

本框架不依赖某个特定 Agent 的私有能力。所有任务应以仓库文档、当前任务提示词和实际文件状态为准。

Agent 不应依赖聊天上下文长期记忆作为唯一依据。如果上下文不足，应优先读取轻量状态文档，而不是全仓库扫描。

默认入口：

- AGENTS.md（所有 Agent 通用）
- CLAUDE.md（Claude Code 专属）
- docs/project_status.md
- 当前任务直接相关文件

## 默认阅读规则

小任务只读：

- AGENTS.md
- docs/project_status.md
- 本任务直接相关的 1 到 3 个文件

阶段推进任务再读：

- 当前 stage plan / summary / closure
- 当前任务直接相关脚本或配置

跨阶段整理任务才读：

- docs/project/REPO_STRUCTURE.md
- docs/README.md
- 多个 stage summary

不要默认读取：

- outputs/
- logs/
- .git/
- 临时产物
- 无关 stage 历史文档
- 大型二进制文件
- 图片、视频、bag、db3，除非任务明确要求

## 阶段边界规则

每个任务必须明确区分：

- planning
- offline artifact
- dry-run
- interface validation
- simulation validation
- real execution

记录和汇报时必须遵守：

- planning 只能写成计划，不能写成已实现。
- dry-run 通过不能写成真实运行通过。
- offline artifact 不能写成真实能力。
- simulation validation 不能写成 real execution。
- 单点验证不能写成完整任务系统通过。
- 失败结果必须真实记录，不能包装成成功。

## Git / commit / Stage 规则

详细规则见 docs/project/GIT_AND_STAGE_WORKFLOW.md。

关键要求：

- commit message 使用 `<type>(<scope>): <summary>`。
- 大阶段使用 `Stage X`。
- 子阶段使用 `Stage X.Y`。
- 小任务或验证点可使用 `Stage X.Y.Z`。
- 不允许无确认 force push。
- 只提交本任务相关文件。
- 如果用户明确说“本次先不 git”，Agent 不执行 `git add`、`git commit`、`git push`。

## 远程 Agent 编排规则

> **这是可选增强功能，不影响本地开发。** 不启用时不产生任何影响。

用户不在电脑前时，可通过协调 Agent（Agent A / Zenvoy）将任务转交给执行 Agent（Agent B / Zenforge）。详细规则见 [docs/project/REMOTE_AGENT_ORCHESTRATION.md](docs/project/REMOTE_AGENT_ORCHESTRATION.md)。

关键约束：

- Agent A / Zenvoy 不修改 GPT 原始提示词，原样转发给 Agent B / Zenforge。
- Agent B / Zenforge 只执行当前任务提示词明确允许的修改。
- 每次任务执行完毕后，执行 Agent 按任务授权处理 commit / push，并按边界更新 `docs/project_status.md`。
- push、tag、force push、依赖安装、删除大量文件、修改权限、读取敏感数据等高风险操作必须确认。
- 通信 token、本地队列、真实消息记录、logs、outputs 和临时产物不提交到业务仓库。
- GPT 角色协议见 [docs/project/GPT_REMOTE_TASK_PROTOCOL.md](docs/project/GPT_REMOTE_TASK_PROTOCOL.md)，GPT 连接仓库后应先阅读该文档。

## project_status 规则

docs/project_status.md 是轻量当前状态入口，也保存最近一次任务的完整执行汇报，供 GPT 和后续 Agent 快速接续。它不保存完整历史日志，建议控制在 150 到 250 行以内。

详细历史应放入：

- docs/stage*/
- docs/project/history/

每次任务只更新：

- 当前状态
- 最近关键结论
- 最近一次执行汇报
- 当前禁止事项
- 下一步建议
- 详细文档入口

最近一次执行汇报必须包含：

- 修改了哪些文件
- 新增或更新了什么能力或文档规则
- 运行了哪些命令
- 哪些通过
- 哪些未运行及原因
- 是否有失败或不确定结果
- Git 状态（如 commit / push / tag / 未提交修改）
- 是否产生 outputs/ 或 logs/
- 是否涉及敏感或临时文件
- 当前是否可以进入下一步

## 输出和隐私规则

outputs/ 默认不提交。logs/ 默认不提交。

以下内容默认不提交：

- bag、db3、sqlite、截图、视频、缓存
- .env、token、API key、cookie、session、账号信息
- 个人隐私数据、健康数据、账单、日记、照片

如果项目需要示例数据，必须使用 fake/sample 数据。

## 汇报要求

Agent 完成任务后必须汇报：

- 修改了哪些文件
- 新增了什么能力或文档规则
- 运行了哪些命令
- 哪些通过
- 哪些未运行及原因
- 是否有失败或不确定结果
- 是否产生 outputs/ 或 logs/
- 是否涉及敏感或临时文件
- 当前是否可以进入下一步

同一份汇报必须同步写入 `docs/project_status.md` 的“最近一次执行汇报”。聊天回复可以摘要，但仓库内状态文档必须足够让 GPT 或后续 Agent 直接了解上一步执行情况。
