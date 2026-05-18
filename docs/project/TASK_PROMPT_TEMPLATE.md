# Task Prompt Template

本文提供可复用的 Agent 任务提示词模板。模板应根据当前项目阶段和任务边界裁剪后使用。

## 通用任务模板

```text
你现在在 GitHub 仓库：

<owner>/<repo>

当前分支：

<branch>

任务目标：

<task goal>

Agent 适配：

本任务可由 Codex / Claude Code / 其它 VSCode Agent 执行。
不要依赖某个特定 Agent 的私有能力。
以仓库文件和本提示词为准。

阅读预算：

必须阅读：
- AGENTS.md
- docs/project_status.md
- <directly related files>

可选阅读：
- <only if needed>

不要阅读：
- outputs/
- logs/
- unrelated stage docs
- unrelated source directories
- binary artifacts

当前背景：

<short context>

本次任务允许：

<allowed actions>

本次任务禁止：

<forbidden actions>

需要修改：

<files>

验证要求：

<commands>

完成后汇报：

- 修改了哪些文件
- 新增了什么能力
- 运行了哪些命令
- 哪些通过
- 哪些未运行及原因
- 是否有失败或不确定结果
- 当前是否可以进入下一步
```

## 文档维护任务模板

```text
你现在在 GitHub 仓库：

<owner>/<repo>

任务目标：

维护或补充项目文档：<doc task>

Agent 适配：

本任务可由 Codex / Claude Code / 其它 VSCode Agent 执行。
不要依赖某个特定 Agent 的私有能力。
以仓库文件和本提示词为准。

阅读预算：

必须阅读：
- AGENTS.md
- docs/project_status.md
- <docs to edit>

可选阅读：
- docs/README.md
- docs/project/REPO_STRUCTURE.md

不要阅读：
- outputs/
- logs/
- 无关 stage 历史文档
- 无关源码目录
- 二进制 artifact

本次任务允许：

- 新增或更新指定文档。
- 修正文档索引链接。
- 更新 docs/project_status.md 的轻量状态。

本次任务禁止：

- 写业务功能代码。
- 修改核心逻辑。
- 引入新依赖。
- 把 planning 写成 execution。
- 读取或提交 outputs/、logs/。

验证要求：

- git diff --check

完成后汇报：

- 修改文件
- 新增或更新的文档规则
- 验证命令及结果
- 未运行项及原因
- 是否可以进入下一步
```

## 阶段推进任务模板

```text
你现在在 GitHub 仓库：

<owner>/<repo>

任务目标：

推进当前阶段：<stage task>

Agent 适配：

本任务可由 Codex / Claude Code / 其它 VSCode Agent 执行。
不要依赖某个特定 Agent 的私有能力。
以仓库文件和本提示词为准。

阅读预算：

必须阅读：
- AGENTS.md
- docs/project_status.md
- <current stage plan>

可选阅读：
- <current stage summary>
- <current stage closure>
- <directly related scripts or configs>

不要阅读：
- outputs/
- logs/
- unrelated stage docs
- unrelated source directories
- binary artifacts

本次任务允许：

- 按当前阶段边界修改指定文件。
- 更新当前 stage 文档。
- 更新 docs/project_status.md 的当前状态和下一步。

本次任务禁止：

- 擅自进入下一阶段。
- 擅自扩大范围。
- 把 dry-run、interface validation、simulation validation 写成 real execution。
- 把单点验证写成系统完成。

验证要求：

<commands>

完成后汇报：

- 修改文件
- 当前阶段推进了什么
- 验证命令及结果
- 未运行项及原因
- 当前阶段剩余事项
- 是否建议进入下一步
```

## Bug 修复任务模板

```text
你现在在 GitHub 仓库：

<owner>/<repo>

任务目标：

修复问题：<bug description>

Agent 适配：

本任务可由 Codex / Claude Code / 其它 VSCode Agent 执行。
不要依赖某个特定 Agent 的私有能力。
以仓库文件和本提示词为准。

阅读预算：

必须阅读：
- AGENTS.md
- docs/project_status.md
- <bug report or failing file>
- <directly related source or config files>

可选阅读：
- <related tests>
- <related stage doc>

不要阅读：
- outputs/
- logs/
- unrelated stage docs
- unrelated source directories
- binary artifacts

本次任务允许：

- 最小范围修复 bug。
- 添加或更新直接相关测试。
- 更新相关文档中的真实状态。

本次任务禁止：

- 顺手重构无关模块。
- 修改核心数据结构，除非任务明确要求。
- 把失败分析完成写成验证通过。
- 隐藏未验证风险。

验证要求：

<commands>

完成后汇报：

- 根因判断
- 修改文件
- 修复方式
- 验证命令及结果
- 未运行项及原因
- 残余风险
- 是否可以进入下一步
```

## Closure / Failure Analysis 任务模板

```text
你现在在 GitHub 仓库：

<owner>/<repo>

任务目标：

完成阶段收尾或失败分析：<closure or failure task>

Agent 适配：

本任务可由 Codex / Claude Code / 其它 VSCode Agent 执行。
不要依赖某个特定 Agent 的私有能力。
以仓库文件和本提示词为准。

阅读预算：

必须阅读：
- AGENTS.md
- docs/project_status.md
- <current stage summary or failure record>

可选阅读：
- <current stage plan>
- <directly related validation notes>
- <specific command logs only if provided in docs>

不要阅读：
- outputs/
- logs/
- unrelated stage docs
- unrelated source directories
- binary artifacts

本次任务允许：

- 整理真实完成情况。
- 记录失败、阻塞和未验证项。
- 更新 closure / failure analysis 文档。
- 更新 docs/project_status.md 的轻量状态。

本次任务禁止：

- 把失败分析完成写成验证通过。
- 把未运行命令写成已通过。
- 把 simulation validation 写成 real execution。
- 补写不存在的成果。

验证要求：

- git diff --check

完成后汇报：

- 修改文件
- closure 或 failure analysis 的核心结论
- 通过项
- 失败项
- 未运行项及原因
- 是否建议继续、修复、暂停或进入下一阶段
```
