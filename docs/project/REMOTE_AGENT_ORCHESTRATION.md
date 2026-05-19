# Remote Agent Orchestration

本文定义“用户不在电脑前时”的 Agent 协作方式。目标是让一个协调 Agent 代替用户做任务转发、结果汇总和风险判断，同时保持 Git、权限和项目状态可审查。

## 角色定义

ChatGPT / Codex：

- 判断阶段边界。
- 检查 GitHub 上的最新结果。
- 生成下一步可直接复制给执行 Agent 的任务提示词。
- 做路线、架构和风险把关。

Agent A / coordinator：

- 代表用户接收 ChatGPT / Codex 的任务判断和提示词。
- 原样转发 GPT 任务提示词给 Agent B，不修改、不包装、不添加额外约束。
- 汇总 Agent B 的执行结果、命令结果、diff 摘要和 Git 状态。
- 根据当前模式决定是否继续、暂停、请求用户确认或建议提交。
- 通过 Telegram、飞书或其它通道向用户汇报关键结果。

Agent B / executor：

- 通过 Claude Code CLI 或 Codex CLI 在本地开发环境中执行具体文件修改、命令和验证。
- Claude Code CLI 启动时携带 `--dangerously-skip-permissions` 以避免交互式权限确认。
- Codex CLI 通过 `~/.codex/config.toml` 中 `sandbox_mode = danger-full-access` 和 per-project `trust_level = trusted` 实现自动审查。
- Agent A 首次转交任务时会询问用户选择哪个执行器，用户可随时切换。
- 遵守 AGENTS.md、docs/project_status.md 和当前任务提示词。
- 不擅自扩大任务范围。
- 完成后向 Agent A 汇报完整执行结果。

用户：

- 选择自动化模式。
- 确认高风险操作。
- 确认是否 commit、push、tag 或进入下一阶段，除非当前模式明确允许 Agent A 自主判断。

GitHub：

- 保存可审查的仓库状态。
- 作为 ChatGPT / Codex 远程检查最新进展的事实来源。
- 不保存本地队列、通信 token、临时日志或用户私密数据。

## 推荐本地工作区

实际运行工作区不放在项目仓库内，推荐使用：

```text
D:\Claude_Code_Workspace\project-steward\
```

推荐目录：

```text
project-steward/
  README.md
  config/
    agent-a.local.template.json
  prompts/
    agent-a.system.md
    agent-b.executor-wrapper.md
  queue/
    to-agent-a/
    to-agent-b/
    from-agent-b/
    decisions/
    archive/
  scripts/
    start-agent-a.ps1
    new-handoff.ps1
```

其中 `config/*.local.json`、`queue/`、`logs/`、通信 token 和运行输出不应提交到业务仓库。

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

## 任务流

标准闭环：

1. ChatGPT / Codex 检查 GitHub 或仓库状态。
2. ChatGPT / Codex 给出下一步任务提示词。
3. Agent A 保存原始提示词，原样转发给 Agent B（不修改、不包装）。
4. Agent A 按当前模式决定是否先请求用户确认。
5. Agent B 通过 Claude Code CLI 执行任务并汇报。
6. Agent A 汇总：
   - GPT 任务判断。
   - Agent B 执行结果。
   - 修改文件。
   - 验证命令。
   - Git 状态。
   - 风险和未确认项。
   - 建议的 commit / push 决策。
7. 用户确认 Git 操作或下一步。
8. 如完成 push，ChatGPT / Codex 通过 GitHub 检查最新结果并生成下一轮提示词。

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

用户在任务途中可随时指定 Agent B 的模型和思考程度：

**Claude Code CLI（接入 DeepSeek，通过 `cc switch` 切换）：**

| 模型 ID | 对应原模型 | 说明 |
|---|---|---|
| `deepseek-v4-pro[1M]` | sonnet / opus | 默认，最强 |
| `deepseek-v4-flash` | haiku | 快速轻量 |

思考级别通过 `CLAUDE_CODE_EFFORT_LEVEL` 环境变量控制，当前设为 `max`。

**Codex CLI:**

- 模型：通过 `codex -m <model>` 指定（默认 `gpt-5.5`）
- 思考程度：通过 `codex -c model_reasoning_effort=<level>` 指定（`low` / `medium` / `high` / `maximum`）

用户直接告诉 Agent A："用 pro"、"换成 flash"、"Codex 用 high effort"即可切换。

## 上下文管理与会话

Agent A 监控上下文窗口使用率，超过 70% 时主动提醒用户压缩或开新对话。

| 操作 | 说明 |
|---|---|
| `/compact` | 压缩当前对话，保留关键信息 |
| `/rename` | 重命名当前会话 |
| 新对话 | 先汇报当前状态，然后用新 `-SessionName` 重新启动 Agent A |

## 通信通道建议

第一阶段使用本地文件队列，降低外部依赖：

- `queue/to-agent-a/`：用户或 GPT 放给 Agent A 的任务。
- `queue/to-agent-b/`：Agent A 转交给 Agent B 的执行提示词。
- `queue/from-agent-b/`：Agent B 执行结果。
- `queue/decisions/`：等待用户确认的 commit / push / 权限决策。
- `queue/archive/`：已完成任务归档。

第二阶段再接入 Telegram、飞书或其它消息通道。

通信 token 应只放在用户环境变量、系统凭据管理器或未提交的本地配置中，不写入业务仓库。

## 当前建议

Agent B 通过 Claude Code CLI 或 Codex CLI 执行，不依赖 VSCode 插件 UI 自动化：

- Agent A 将 GPT 原始提示词保存到 `queue/to-agent-b/*.md`。
- Agent A 首次任务时询问用户选择 `claude-code` 或 `codex` 作为执行器。
- Claude Code: `claude --dangerously-skip-permissions --append-system-prompt agent-b.system.md`。
- Codex: `codex`（自动审查已通过 `sandbox_mode=danger-full-access` + trusted projects 配置）。
- 用户使用 VSCode 主要用于阅读代码和文档，而非 Agent 执行的必要环境。
