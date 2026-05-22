# Task Prompt Template

本文提供可复用的 Agent 任务提示词模板。复制前必须按具体项目裁剪占位内容；不要把模板示例写成真实项目状态。

## 使用原则

- 先选任务类型，再补齐通用任务骨架。
- 保留阅读预算、任务边界、验证要求、Git 授权和完成汇报。
- 删除不适用的占位项和示例项。
- 不写真实账号、token、消息内容、队列编号、本地日志或临时路径，除非它们属于当前项目且允许提交。
- 不默认允许 tag、force push、依赖安装、权限修改或外部写入型 API 调用。

## 通用任务骨架

```text
你现在在 GitHub 仓库：
<owner>/<repo>

当前分支：
<branch>

任务目标：
<one sentence goal>

Agent 适配：
本任务可由 ChatGPT 网页端、Codex、Claude Code、VSCode 内其它 Agent 或后续工具执行。
不要依赖某个特定 Agent 的私有能力，以仓库文件和本提示词为准。

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
- local queue, token, cache, temp files

当前背景：
<short context; do not paste process logs>

本次任务允许：
- <allowed action>

本次任务禁止：
- <forbidden action>

需要修改：
- <file or directory>

验证要求：
- <command or manual check>

Git 操作：
- 是否允许 git add / commit / push：<yes/no>
- 推荐 commit message：<type>(<scope>): <summary>
- 是否允许创建或推送 tag：<yes/no>
- 是否允许 force push：no，除非用户另行明确确认

完成后汇报：
- 修改文件
- 新增或更新的能力/规则
- 运行命令及结果
- 未运行项及原因
- 失败、不确定结果或残余风险
- 当前分支
- commit hash 和 commit message（如已提交）
- push / tag 状态（如已执行）
- 是否仍有未提交修改
- 是否产生 outputs、logs、敏感文件或临时文件
- 是否可以进入下一步
```

## 任务类型补充块

### 文档维护

用于 README、规则文档、状态文档、索引或阶段文档维护。

```text
任务目标：
维护或补充项目文档：<doc task>

本次任务允许：
- 新增或更新指定文档。
- 修正文档索引链接。
- 更新 docs/project_status.md 的轻量当前状态。

本次任务禁止：
- 写业务功能代码。
- 修改核心逻辑。
- 引入新依赖。
- 把 planning 写成 execution。
- 读取或提交 outputs/、logs/。

验证要求：
- git diff --check

推荐 commit message：
docs(<scope>): <summary>
```

### 阶段推进

用于推进当前 Stage 内的最小闭环。

```text
任务目标：
推进当前阶段：<stage task>

必须阅读：
- AGENTS.md
- docs/project_status.md
- <current stage plan>

可选阅读：
- <current stage summary>
- <current stage closure>
- <directly related scripts or configs>
- docs/project/GIT_AND_STAGE_WORKFLOW.md

本次任务允许：
- 按当前阶段边界修改指定文件。
- 更新当前 stage 文档。
- 更新 docs/project_status.md 的当前状态和下一步。

本次任务禁止：
- 擅自进入下一阶段。
- 擅自扩大范围。
- 把 dry-run、interface validation、simulation validation 写成 real execution。
- 把单点验证写成系统完成。

推荐 commit message：
<type>(stageX.Y): <summary>
```

### Bug 修复

用于修复已定位或可复现的问题。

```text
任务目标：
修复问题：<bug description>

必须阅读：
- AGENTS.md
- docs/project_status.md
- <bug report or failing file>
- <directly related source or config files>

本次任务允许：
- 最小范围修复 bug。
- 添加或更新直接相关测试。
- 更新相关文档中的真实状态。

本次任务禁止：
- 顺手重构无关模块。
- 修改核心数据结构，除非任务明确要求。
- 把失败分析完成写成验证通过。
- 隐藏未验证风险。

完成后额外汇报：
- 根因判断
- 修复方式
- 残余风险

推荐 commit message：
fix(<scope>): <summary>
```

### Closure / Failure Analysis

用于阶段收尾、失败复盘或未完成项整理。

```text
任务目标：
完成阶段收尾或失败分析：<closure or failure task>

必须阅读：
- AGENTS.md
- docs/project_status.md
- <current stage summary or failure record>

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

完成后额外汇报：
- closure 或 failure analysis 的核心结论
- 通过项
- 失败项
- 建议继续、修复、暂停或进入下一阶段

推荐 commit message：
docs(<scope>): <summary>
```
