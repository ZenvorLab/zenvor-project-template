# Remote Agent Orchestration

> **范围边界：本文仅适用于”用户不在电脑前时”的远程开发场景。**
> 本地开发工作流（正常坐在电脑前使用 VSCode / CLI）不受本文影响，详见 [AGENT_USAGE_POLICY.md](AGENT_USAGE_POLICY.md) 和 [AI_WORKFLOW.md](AI_WORKFLOW.md)。
> 远程编排是可选增强功能，不启用（不运行协调 Agent）时仓库行为完全不变。

本文定义远程开发时的 Agent 协作方式。核心约束：**ChatGPT 通过 GitHub App 只能读取仓库，不能写入**——因此 GPT 到 Agent A 的任务传递需要用户手动复制（手机 GPT App → 消息通道发送给 Agent A）。GPT 能够自主检查 GitHub 进展（只读），但无法主动触发工作流。

## 角色定义

ChatGPT / Codex：

- 判断阶段边界，做路线、架构和风险把关。
- 通过 GitHub App（只读）检查仓库最新 commit、文件变更、project_status。
- 生成下一步任务提示词（用户手动复制，通过消息通道发给 Agent A）。
- **能力边界：ChatGPT GitHub App 只能读取、不能写入。** GPT 不能直接 commit、不能写文件到仓库、不能通过仓库文件自动触发 Agent A。GPT 的 Scheduled Tasks 功能可定时检查仓库进展，但同样不能写入。

Agent A / coordinator：

- 通过消息通道接收用户转发的 GPT 任务提示词（也支持用户直接写的任务）。
- 原样转发 GPT 任务提示词给 Agent B，不修改、不包装、不添加额外约束。
- 管理多个项目的多个 Agent B，统一路由、权限门和结果汇总。
- 根据当前模式决定是否继续、暂停、请求用户确认或建议提交。
- 通过消息通道向用户汇报执行结果和 Git 状态，请求确认。

Agent B / executor：

- 通过 CLI 执行具体文件修改、命令和验证。
- 遵守 AGENTS.md、docs/project_status.md 和当前任务提示词。
- 不擅自扩大任务范围。
- 完成后向 Agent A 汇报完整执行结果，并更新 `docs/project_status.md` 的「最近任务结果」节。

用户（远程场景，通过手机操作）：

- 从手机 GPT App 复制任务提示词 → 发给 Agent A。
- 收到 Agent A 汇报后回复 commit / push / 切换执行器等指令。
- GPT 下次检查时从 GitHub 读取 project_status.md 获取进展，不需要用户反向发送汇报。

GitHub：

- 保存可审查的仓库状态。
- 作为 ChatGPT（只读）检查最新进展的事实来源。
- 不保存本地队列、通信 token、消息日志或用户私密数据。

## 推荐本地工作区

协调工作区独立于项目仓库，推荐结构：

```text
<control-workspace>/
  config/
    projects.json
  prompts/
    agent-a.system.md
    agent-b.system.md
  queue/
    to-agent-a/
    to-agent-b/
    from-agent-b/
    decisions/
    archive/
  scripts/
    start-agent-a
    new-handoff
    messaging-bridge
```

通信 token、本地队列、logs 等不提交到业务仓库。

## 自动化模式

### Mode 1: confirm-every-step

- Agent A 每次收到任务后先发给用户确认。
- 用户确认后才转交 Agent B。
- 适合调试或不信任任务来源时。

### Mode 2: report-on-task-complete（默认）

- Agent A 直接转交 Agent B 执行。
- Agent B 自动 commit + push。
- Agent A 完成后汇总汇报。
- 高风险操作暂停询问。

### Mode 3: autonomous-until-risk

- Agent A 连续推进低风险小任务。
- 遇到高风险操作暂停询问。
- 适合用户明确授权的维护任务。

## 权限边界

默认可自动执行：

- 读取文档和任务直接相关文件。
- 编辑任务明确允许的文件。
- 运行任务指定的本地验证命令。
- `git commit` 和 `git push`（用户已授权，可通过 Agent A 切换模式）。
- 更新 `docs/project_status.md`。

默认需要确认：

- 创建或推送 tag
- force push
- 删除大量文件或清空目录
- 安装、升级或移除依赖
- 修改系统权限、防火墙、SSH key、token、凭据
- 真实调用外部写入型 API
- 读取或转发敏感数据
- 扩大任务范围或跨阶段推进

默认禁止：

- 将 token、cookie、API key、session 信息写入仓库。
- 将 queue、logs、outputs、临时文件提交到仓库。
- 把失败、dry-run、simulation validation 写成真实完成。
- 未经确认覆盖用户未提交修改。

## 任务流（实际路径）

远程闭环包含一个**手动环节**（GPT → 用户 → Agent A），其余全部自动化：

```
┌─────────────────────────────────────────────────────────┐
│ 手机端（手动）                                           │
│                                                         │
│  GPT 检查 GitHub → 给出任务提示词                         │
│       │                                                 │
│       用户复制提示词                                      │
│       │                                                 │
│       ▼                                                 │
│  消息通道 → Agent A                                     │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│ 电脑端（自动）                                           │
│                                                         │
│  Agent A 判断项目/执行器/权限 → 转发 Agent B              │
│       │                                                 │
│       ▼                                                 │
│  Agent B 执行任务                                        │
│       │                                                 │
│       ▼                                                 │
│  Agent B commit + push                                  │
│       │                                                 │
│       ▼                                                 │
│  Agent A 汇总结果 → 消息通道汇报给用户                    │
│       │                                                 │
│       ▼                                                 │
│  push → GitHub                                          │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│ GPT（下次任务）                                          │
│                                                         │
│  GPT 通过 GitHub App 读取最新进展（只读）                 │
│  不需要用户把 Agent B 汇报反向发送                         │
│  给出下一步任务 → 用户复制 → 循环                          │
└─────────────────────────────────────────────────────────┘
```

步骤：

1. **用户手机 GPT App**：检查 GitHub 最新进展，给出下一步任务提示词。
2. **用户手动复制**：将 GPT 任务提示词通过消息通道发给 Agent A。
3. **Agent A**：保存原始提示词，按模式判断是否需确认，原样转发 Agent B。
4. **Agent B**：执行任务，完成后自动 commit + push，更新 project_status.md。
5. **Agent A 汇总**：执行结果 + Git 状态 → 消息通道发给用户。
6. **下一次循环**：用户打开手机 GPT，GPT 通过 GitHub 读取最新 project_status.md 和 commit 记录。

## Agent A 汇报格式

Agent A 每次汇报必须包含：

- 当前项目和分支。
- 当前执行器。
- 任务摘要。
- 修改文件列表。
- Git 状态（commit / push / 是否有未提交修改）。
- 问题或风险（无则写"无"）。
- 需要用户确认的问题。

## Agent B 启动方式

Agent B 有两种启动方式：

**Claude Code CLI:**

```bash
claude --dangerously-skip-permissions --append-system-prompt agent-b.system.md "<GPT raw task prompt>"
```

**Codex CLI:**

```bash
codex "<GPT raw task prompt>"
```

Codex 的自动审查通过 `~/.codex/config.toml` 中的 `sandbox_mode = "danger-full-access"` 和对应项目的 `trust_level = "trusted"` 实现。

Agent A 不修改 GPT 原始提示词。Agent B 的执行边界由 Agent B 自身的系统提示词（Claude Code 下）或仓库内的 AGENTS.md（Codex 下）保证，不在每次任务中重复附加。

Agent A 首次转交任务时会询问用户选择 `claude-code` 还是 `codex`，用户可随时告知 Agent A 切换执行器。

## 模型与思考程度

用户在任务途中可随时指定 Agent B 的模型和思考程度。具体可选模型取决于执行器配置（Claude Code 通过 `--model` 或环境变量，Codex 通过 `-m` 和 `-c model_reasoning_effort=<level>`）。Agent A 不应在用户未指定时主动更换。

## 上下文管理与会话

Agent A 监控上下文窗口使用率，超过 70% 时主动提醒用户压缩或开新对话。

| 操作 | 说明 |
|---|---|
| `/compact` | 压缩当前对话，保留关键信息 |
| `/rename` | 重命名当前会话 |
| 新对话 | 先汇报当前状态，然后用新 `-SessionName` 重新启动 Agent A |

## 通信通道

### 消息通道（用户 ↔ Agent A）

用户通过消息通道（如 Telegram、飞书等）与 Agent A 通信。通信 token 只放在用户环境变量、系统凭据管理器或未提交的本地配置中，不写入业务仓库。

### 本地文件队列（Agent A ↔ Agent B）

Agent A 与 Agent B 之间通过本地文件队列中转：

- `queue/to-agent-a/`：Agent A 保存的原始任务。
- `queue/to-agent-b/`：Agent A 原样转发的任务提示词（Agent B 读取执行）。
- `queue/from-agent-b/`：Agent B 执行汇报。
- `queue/decisions/`：待确认决策。
- `queue/archive/`：已归档任务。

文件队列也是消息通道不可用时的 fallback。

### 已知限制

- ChatGPT GitHub App 只读，不能写文件或 commit。
- GPT Scheduled Tasks 同样不能写入仓库。
- Agent CLI 无浏览器自动化能力，无法操控网页版 GPT。

