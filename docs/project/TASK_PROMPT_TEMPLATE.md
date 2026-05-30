# Task Prompt Template

本文提供可复用的 Agent 任务提示词模板。复制前必须按具体项目裁剪占位内容，不把模板示例写成真实项目状态。

## 通用任务骨架

```
你在仓库：<owner>/<repo>，分支：<branch>

任务目标：<one sentence>

必须阅读：AGENTS.md、docs/project_status.md、<直接相关文件>
不要阅读：outputs/、logs/、无关 stage 文档、二进制文件

本次允许：<actions>
本次禁止：<prohibitions>

验证要求：<command or check>

Git：本地允许 commit，push 仅在阶段完成时 squash 执行；不允许 force push

完成后：
1. 更新 docs/project_status.md 的最近一次执行汇报
2. 汇报：修改文件、验证结果、Git 状态、是否可以进入下一步
```

## 任务类型补充

### 文档维护

```
允许：新增/更新指定文档、修正索引链接、更新 project_status.md
禁止：写业务代码、引入依赖、把 planning 写成 execution
commit：docs(<scope>): <summary>
```

### 阶段推进

```
允许：按阶段边界修改文件、更新 stage 文档和 project_status.md
禁止：擅自进入下一阶段、扩大范围、把 dry-run 写成 real execution
commit：<type>(stageX.Y): <summary>
```

### Bug 修复

```
允许：最小范围修复、添加直接相关测试、更新文档中的真实状态
禁止：顺手重构无关模块、隐藏未验证风险
完成后额外汇报：根因、修复方式、残余风险
commit：fix(<scope>): <summary>
```

### Closure / 失败分析

```
允许：整理真实完成情况、记录失败和未验证项、更新 closure 文档
禁止：把失败写成成功、把未运行写成已通过、补写不存在的成果
完成后额外汇报：通过项、失败项、建议继续/修复/暂停/进入下一阶段
commit：docs(<scope>): <summary>
```
