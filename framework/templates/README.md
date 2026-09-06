# Project Record Templates

仅在创建对应记录时读取模板。目标工程统一使用 `<agent-workspace>/project/` 保存这些记录，并保留相同的权限边界、状态和可追溯字段。

| Template | When to use |
| --- | --- |
| `Decision-Package.md` | 当前工作中的本地产品、架构、安全、数据或外部合同决策 |
| `Issue.md` | 已验证问题、阻塞事实或需要升级的决策问题 |
| `Sprint.md` | 目标、范围、验收、Readiness 和 Gate 计划 |
| `Task-Index.md` | 复制为 `<agent-workspace>/project/tasks/<sprint-id>/README.md`，保存 Task 导航与派生状态 |
| `Task.md` | 一项可独立交付和验收的工作；改变目标环境时保留其中的环境变更授权章节 |
| `Test-Plan.md` / `Test-Report.md` | High 风险、Sprint 终验或项目要求的独立测试记录 |
| `Sprint-Review.md` | Sprint 收尾判定 |

## IDs and filenames

- 新项目默认使用 `LDP-####`、`ISSUE-####`、`SPRINT-###`、`TEST-PLAN-###` 和 `TEST-REPORT-###`。
- Task ID 使用项目内唯一、能表达 workstream 的稳定编号；不要在 ID 中编码可变状态。
- 文件名以 ID 开头，后接可选的小写 kebab-case 摘要。
- 遗留项目可以保留已有编号体系，不为采用模板批量改名；新建记录保持项目内唯一和一致。
