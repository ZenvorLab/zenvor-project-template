# Remote Agent Orchestration

> **范围边界：本文仅适用于”用户不在电脑前时”的远程开发场景。**
> 本地开发工作流（正常坐在电脑前使用 VSCode / CLI）不受本文影响，详见 [AGENT_USAGE_POLICY.md](AGENT_USAGE_POLICY.md) 和 [AI_WORKFLOW.md](AI_WORKFLOW.md)。
> 远程编排是可选增强功能，不启用（不运行 Agent A、不配置 Telegram）时仓库行为完全不变。

本文定义远程开发时的 Agent 协作方式。核心约束：**ChatGPT 通过 GitHub App 只能读取仓库，不能写入**——因此 GPT 到 Agent A 的任务传递需要用户手动复制（通常从手机 GPT App → Telegram）。GPT 能够自主检查 GitHub 进展（只读），但无法主动触发工作流。

## 角色定义

ChatGPT / Codex：

- 判断阶段边界，做路线、架构和风险把关。
- 通过 GitHub App（只读）检查仓库最新 commit、文件变更、project_status。
- 生成下一步任务提示词（用户手动复制到 Telegram 发给 Agent A）。
- **能力边界：ChatGPT GitHub App 只能读取、不能写入。** GPT 不能直接 commit、不能写文件到仓库、不能通过仓库文件自动触发 Agent A。GPT 的 Scheduled Tasks 功能可定时检查仓库进展，但同样不能写入。

Agent A / coordinator：

- 通过 Telegram / 飞书接收用户转发的 GPT 任务提示词（也支持直接接收用户自己写的任务）。
- 原样转发 GPT 任务提示词给 Agent B，不修改、不包装、不添加额外约束。
- 管理多个项目的多个 Agent B，统一路由、权限门和结果汇总。
- 根据当前模式决定是否继续、暂停、请求用户确认或建议提交。
- 通过 Telegram / 飞书向用户汇报 Agent B 执行结果、Git 状态和建议操作。
- 用户回复 Telegram 决定 commit / push / 切换执行器 / 切换模型。

Agent B / executor：

- 通过 Claude Code CLI 或 Codex CLI 在本地开发环境中执行具体文件修改、命令和验证。
- Claude Code CLI 启动时携带 `--dangerously-skip-permissions` 以避免交互式权限确认。
- Codex CLI 通过 `~/.codex/config.toml` 中 `sandbox_mode = danger-full-access` 和 per-project `trust_level = trusted` 实现自动审查。
- Agent A 首次转交任务时会询问用户选择哪个执行器，用户可随时切换。
- 遵守 AGENTS.md、docs/project_status.md 和当前任务提示词。
- 不擅自扩大任务范围。
- 完成后向 Agent A 汇报完整执行结果。

用户（在户外，通过手机操作）：

- 从手机 GPT App 复制任务提示词 → 粘贴到 Telegram 发给 Agent A。
- 收到 Agent A 汇报后回复：commit / push / 切换执行器 / 切换模型等指令。
- GPT 下次检查时直接从 GitHub 读取最新进展，用户不需要把 Agent B 的汇报反向发给 GPT。

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

适合早期调试。

- Agent A 每次收到 ChatGPT / Codex 提示词后先发给用户确认。
- 用户确认后才转交 Agent B。
- Agent B 完成后，Agent A 汇总结果并询问是否 commit / push。

### Mode 2: report-on-task-complete

适合常规开发。

- Agent A 可以直接将已确认来源的任务转交 Agent B。
- Agent B 在任务完成后汇报。
- Agent A 汇总后请求用户决定 Git 操作。
- 期间遇到权限不足、高风险操作或任务范围变化时暂停询问。

### Mode 3: autonomous-until-risk

适合用户明确授权的低风险维护任务。

- Agent A 可连续转交和检查小任务。
- Agent B 可在授权范围内运行验证命令。
- Agent A 可建议 commit message，也可在用户预授权时执行 commit。
- push、tag、force push、删除大量文件、安装依赖、修改权限、真实外部 API 写入等仍需用户确认，除非用户在当前任务中明确授权。

## 权限边界

默认可自动执行：

- 读取 AGENTS.md、docs/project_status.md 和任务直接相关文件。
- 编辑任务明确允许的文件。
- 运行任务指定的本地验证命令。
- 生成 diff 摘要和 Git 状态报告。
- 创建本地任务队列文件。

默认需要确认：

- `git push`
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
│  Telegram → Agent A                                     │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│ 电脑端（自动）                                           │
│                                                         │
│  Agent A 判断项目/执行器/权限 → 转发 Agent B              │
│       │                                                 │
│       ▼                                                 │
│  Agent B (Claude Code / Codex CLI) 执行任务              │
│       │                                                 │
│       ▼                                                 │
│  Agent A 汇总结果 → Telegram 汇报给用户                   │
│       │                                                 │
│       ▼                                                 │
│  用户回复 Telegram → Agent A 执行 commit/push            │
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
2. **用户手动复制**：将 GPT 任务提示词粘贴到 Telegram 发给 Agent A。
3. **Agent A**：保存原始提示词，按模式判断是否需确认，原样转发 Agent B。
4. **Agent B**：通过 Claude Code CLI 或 Codex CLI 执行并汇报。
5. **Agent A 汇总**：执行结果 + 修改文件 + Git 状态 + 建议 commit/push 决策 → Telegram 发给用户。
6. **用户决定**：回复 Telegram（commit / push / 不 commit / 切换执行器 / 切换模型）。
7. **push → GitHub**（如用户确认）。
8. **下一次循环**：用户打开手机 GPT，"检查进展，给下一步任务"，GPT 通过 GitHub 读取最新状态。

## Agent A 汇报格式

Agent A 每次汇报必须包含：

- 当前项目和分支。
- 当前模式。
- GPT / Codex 原始结论摘要。
- 已转交给 Agent B 的提示词摘要。
- Agent B 的原始汇报或摘要。
- 修改文件列表。
- 运行命令和结果。
- Git 状态。
- 是否有未提交修改。
- 建议操作：
  - 不 commit。
  - 只 commit。
  - commit 并 push。
  - 需要用户先审查。
  - 需要 GPT / Codex 复查。
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

### 主通道：Telegram / 飞书

用户与 Agent A 之间的消息链路：

- 用户 → Telegram → Agent A：GPT 任务提示词、决策指令（commit/push/切换执行器）
- Agent A → Telegram → 用户：执行汇报、Git 状态、确认请求

通信 token（Bot Token、Chat ID、Webhook URL）只放在用户环境变量、系统凭据管理器或未提交的本地配置中，不写入业务仓库。

### 辅助通道：本地文件队列

Agent A 与 Agent B 之间通过本地文件队列中转（Agent A 和 Agent B 运行在同一台机器上）：

- `queue/to-agent-a/`：Agent A 收到 Telegram 消息后保存的原始任务。
- `queue/to-agent-b/`：Agent A 原样转发的 GPT 任务提示词（Agent B 直接读取执行）。
- `queue/from-agent-b/`：Agent B 完成后的执行汇报。
- `queue/decisions/`：等待用户确认的 commit / push / 权限决策。
- `queue/archive/`：已完成任务归档。

文件队列也是消息通道不可用时的 fallback——用户可以直接把任务写入 `queue/to-agent-a/`。

### 不可用方案

以下方案曾被评估但不可行：

- **GPT 直接写入 GitHub**：ChatGPT GitHub App 只读，不能写文件或 commit。
- **Scheduled Tasks 触发 GPT 写仓库**：同上，GPT 仍无写入权限。
- **Claude Code / Codex 操控浏览器**：两个 CLI 均无浏览器自动化能力。

