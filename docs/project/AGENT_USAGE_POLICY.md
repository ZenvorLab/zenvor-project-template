# Agent Usage Policy

本文记录多 Agent 使用策略，确保项目在不同 Agent 和模型之间切换时保持一致规则。

## Agent 类型

本项目可能使用：

- ChatGPT
- Codex
- Claude Code
- Cursor / Cline / Roo Code / 其它 VSCode Agent
- 后续替换的新模型或新 Agent 工具

## Agent 切换规则

- Agent 可以替换，但仓库规则不变。
- 换 Agent 后，不需要重新解释完整工作流。
- 新 Agent 首先读取 AGENTS.md 和 docs/project_status.md。
- 如果任务跨阶段，再读取当前 stage 文档。
- 不允许新 Agent 擅自重构项目结构。
- 不允许把某个 Agent 的私有能力写成项目长期依赖。

## 额度节省策略

- 小任务不全仓库扫描。
- 不默认读取全部 stage 文档。
- 不默认读取全部 README。
- 不读取 outputs/。
- 不读取 logs/。
- 大文档应拆分。
- docs/project_status.md 保持短小。
- 详细历史只读对应 stage 文档。

## 低成本模型使用建议

DeepSeek 或其它低成本模型可用于：

- 文档整理。
- README 更新。
- 简单脚本修改。
- 格式检查。
- schema 草案。
- 非关键重构前的草案生成。

高成本模型优先用于：

- 阶段路线判断。
- 复杂 bug 分析。
- 架构边界决策。
- 高风险代码修改。
- 失败分析。

## 汇报格式统一

所有 Agent 完成任务后必须汇报：

- 修改文件。
- 新增能力。
- 验证命令。
- 通过/失败。
- 未运行项及原因。
- 是否有 outputs。
- 是否提交敏感或临时文件。
- 下一步建议。
