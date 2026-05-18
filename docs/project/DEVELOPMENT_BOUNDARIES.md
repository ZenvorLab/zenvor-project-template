# Development Boundaries

开发、验证和汇报中必须严格区分任务状态，避免把计划、草案、离线产物或仿真结果写成真实能力。

## 必须区分的状态

文档规划：

- 只表示路线、规则、设计说明或任务拆解。
- 不代表代码已经实现。

schema / config 草案：

- 只表示配置结构或数据结构建议。
- 不代表已经被业务逻辑消费。

离线 artifact：

- 指离线生成的文档、样例、报告、图表或中间产物。
- 不代表系统具备在线生成能力。

dry-run：

- 指不触发真实外部效果的试运行。
- 不代表真实运行通过。

interface validation：

- 指接口、参数、命令入口或格式检查。
- 不代表真实业务能力已经完成。

simulation validation：

- 指仿真环境、mock 数据、sample 数据或替代环境下的验证。
- 不代表 real execution 或生产可用。

real execution：

- 指在真实目标环境中执行，并产生真实效果。
- 必须明确记录执行范围、输入、输出和风险。

production capability：

- 指可稳定复用、可交付、可维护的生产能力。
- 需要真实运行、必要测试、边界说明和残余风险记录。

## 禁止表述

- 不要把“已设计”写成“已实现”。
- 不要把“dry-run 通过”写成“真实运行通过”。
- 不要把“仿真通过”写成“实机通过”。
- 不要把“单点成功”写成“系统完成”。
- 不要把“interface validation”写成“真实业务能力”。
- 不要把“失败分析完成”写成“验证通过”。

## 汇报要求

每次汇报必须明确：

- 当前结果属于 planning、offline artifact、dry-run、interface validation、simulation validation、real execution 还是 production capability。
- 哪些命令实际运行了。
- 哪些命令未运行及原因。
- 是否存在失败、不确定结果或未验证风险。
