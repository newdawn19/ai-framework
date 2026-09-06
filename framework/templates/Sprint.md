# <Sprint-ID>: <Title>

**Status:** Discussion / Draft / Ready / In Progress / Blocked / Completed / Cancelled
**Owner:** Product Manager
**Created / Updated:** YYYY-MM-DD / YYYY-MM-DD
**Sprint Review Agent:** 由 Project Manager 调用；不得参与本 Sprint 实现或任何 Task 验收。

## Goal

本 Sprint 完成后用户或系统获得的可验证结果。

## Related Local Decisions

- <LDP-ID>；未命中决策边界时写“不适用”，尚未确定时写“待确认”。

## Scope

- 本 Sprint 包含的能力。

## Out of Scope

- 明确延后的能力。

## Acceptance Criteria

- 可观察、可测试的完成标准。

## Readiness

- [ ] 目标、范围和非目标已确认。
- [ ] 必要关联 LDP 已 `Accepted`；不需要 LDP 时已明确记录“不适用”。
- [ ] 验收标准可验证。
- [ ] 依赖以及必要的 API、数据和错误合同已冻结。
- [ ] Gate 的适用性、Owner、输入和通过条件已明确。

## Gate Plan

| Gate | Applicable | Owner | Inputs / Target | Pass Criteria | Evidence |
| --- | --- | --- | --- | --- | --- |
| Integration | Yes / No | | commits / build | | pending / link |
| Hardware-Target | Yes / No | | build / config / environment | | pending / link |
| Project-specific | Yes / No | | | | pending / link |

## Project Manager Output

项目经理创建独立 Task 和 Task Index，记录 Task Owner、Acceptance Owner、文件边界、依赖、风险、测试等级与附加 Gate。

- **Task Index:** `<agent-workspace>/project/tasks/<sprint-id>/README.md`

## Blocked / Closure Record

- **Previous state:**
- **Blocked fact / Owner / Unblock condition:** 未阻塞时写“无”
- **Review:**
- **Completion or cancellation reason / Date:**
