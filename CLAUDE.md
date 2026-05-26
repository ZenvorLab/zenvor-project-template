# CLAUDE.md

本文件是 Claude Code 进入仓库时的入口。所有 Agent 通用规则见 [AGENTS.md](AGENTS.md)。

## 默认入口

进入仓库后先读取：
- AGENTS.md（通用 Agent 规则）
- docs/project_status.md（当前阶段和状态）

## Claude Code 配置

```json
{
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(python *)",
      "Bash(npm *)"
    ]
  }
}
```

权限策略和自动化模式由项目具体配置，模板此处为示例。

## 远程编排

本仓库支持远程 Agent 编排（可选，详见 docs/project/REMOTE_AGENT_ORCHESTRATION.md）。Claude Code 在远程模式下作为 Agent B 执行任务。

## 与 AGENTS.md 的关系

- AGENTS.md：所有 Agent 通用规则，是仓库唯一权威入口。
- CLAUDE.md：Claude Code 专属入口和配置。
- 两者冲突时以 AGENTS.md 为准。
