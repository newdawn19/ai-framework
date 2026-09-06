# 项目文档

`<agent-workspace>` 是 `./tools/ai-framework path` 返回的公共 Agent 工作区，不是当前 linked worktree 下的相对目录。

## 项目记录位置

| 记录 | 位置 | 模板 |
| --- | --- | --- |
| Local Decision Package（本地 DP） | `<agent-workspace>/project/decision_package/` | `<agent-workspace>/templates/Decision-Package.md` |
| Issue | `<agent-workspace>/project/issues/` | `<agent-workspace>/templates/Issue.md` |
| Sprint | `<agent-workspace>/project/sprints/` | `<agent-workspace>/templates/Sprint.md` |
| Task / Task Index | `<agent-workspace>/project/tasks/<sprint-id>/` | `<agent-workspace>/templates/Task.md` / `Task-Index.md` |
| Sprint Review | `<agent-workspace>/project/reviews/` | `<agent-workspace>/templates/Sprint-Review.md` |
| Test Plan | `<agent-workspace>/project/test/plans/` | `<agent-workspace>/templates/Test-Plan.md` |
| Test Report | `<agent-workspace>/project/test/reports/` | `<agent-workspace>/templates/Test-Report.md` |

`<agent-workspace>/project/decision_package/README.md` 是本地 DP 状态与取代关系索引。
