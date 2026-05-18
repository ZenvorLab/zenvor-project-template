# Project Status Policy

docs/project_status.md 是当前状态入口，用于让 ChatGPT、Codex、Claude Code、其它 VSCode Agent 和后续新 Agent 快速了解当前阶段。

它不是完整开发日志，不保存完整历史验证命令，不复制 stage summary。

## 建议模板

```text
# Project Status

更新时间：
当前分支：
当前阶段：
当前定位：
当前最高优先级：

## 当前摘要

## 当前禁止事项

## 最近关键结论

## 下一步建议

## 历史详情入口
```

## 内容边界

docs/project_status.md 只应记录：

- 当前阶段。
- 当前定位。
- 当前最高优先级。
- 当前禁止事项。
- 最近关键结论。
- 下一步建议。
- 详细文档入口。

## 明确限制

- 建议不超过 250 行。
- 不保存长命令日志。
- 不保存完整实验表格。
- 不复制所有阶段历史。
- 不保存 outputs 内容。
- 不保存二进制 artifact 路径列表，除非是当前关键结论。

详细历史应放入 docs/stage*/ 或 docs/project/history/。
