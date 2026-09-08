# <Task-ID>: <Task Name>

> 基本信息、范围和验收标准由项目经理创建并维护；Task Owner 维护执行与交付记录；Acceptance Owner 只维护验收和最终完成结论。

**Status:** Ready / In Progress / In Review / Done / Blocked
**Task Owner:**
**Acceptance Owner:**
**Created / Updated:** YYYY-MM-DD / YYYY-MM-DD
**Workstream:**
**Test Level:** Quick / Module / High
**Additional Gates:** comma-separated list of None / Integration / Hardware-Target / <project-defined>

## Context

- **Sprint:**
- **Local Decision Packages:** 从 Sprint 的必要关联 LDP 中填写与本 Task 相关的决定；不适用写“无”
- **Issue / Requirement:** 无则写“无”

## Goal and Boundary

- **Result:**
- **Included:**
- **Not Included:**
- **Owned Files / Resources:** 由项目经理确定本 Task 独占修改的文件或资源；不适用写“无”
- **Dependencies:**

## Acceptance Criteria

- 可观察、可验证的完成条件。

## Risks / Blocker

- 风险与升级条件；`Blocked` 时填写事实、Owner、解除条件和阻塞前状态。

## Environment Change Authorization (按需)

> 仅在 Task 会改变目标环境时保留本节。项目经理创建本节；平台工程师在执行前填写技术边界；项目经理记录人工 Owner 的明确授权。`Approval` 为 `Pending` 时不得执行有副作用的目标环境操作，未明确允许的操作均不在授权范围内。

- **Target and Change:** 由平台工程师填写目标环境、操作对象、固定交付输入和预期变化；项目经理核对范围。
- **Execution Envelope:** 预检 / 构建或上传 / 迁移 / 服务更新 / 健康检查 / 只读诊断 / 同范围局部修复 / 重试 / 恢复中允许的动作；不适用写“无”。
- **Attempt Budget:** 按部署治理合约填写本次部署预算、独立恢复预算及适用的诊断时长 / 频率 / 费用限制；与默认规则不同的额度须记录人工批准。
- **Reauthorization Triggers:** 仅填写目标、固定交付输入、权限/数据/网络范围、恢复方案、成本/资源预算、生产或不可逆影响等实质变化。
- **Execution Boundary:** 由平台工程师填写允许的影响、关键禁止事项、停止条件与恢复方式；项目经理核对风险边界。
- **Approval:** 由项目经理根据人工 Owner 的明确授权记录来源和日期；未批准时填写 `Pending`。

## Delivery Record

> 由 Task Owner 维护。

- **Delivered by / Date:**
- **Delivery commit:** immutable target-project commit; for a non-code delivery, link its committed project record
- **Integration base commit:** delivery 提交所基于的集成分支 commit；更新基线并改变交付内容时，填写新的 delivery commit 后重新验收。
- **Changed behavior / files:**
- **Owner verification and results:** 由 Task Owner 在干净 worktree 中记录交付前验证及结果。
- **Execution attempts:** 涉及部署时由平台工程师按部署治理合约记录部署 / 恢复轮次、轮内操作和失败回执；不适用写“无”。
- **Skipped / unverified:** 原因、验收影响和处置；没有则写“无”。

## Acceptance Record

> 仅由与 Task Owner 不同的 Acceptance Owner 维护。

- **Reviewed by / Date:**
- **Reviewed delivery commit:** 必须与实际验收对象一致。
- **Verification and result:**
- **Independent Gate:** Passed / Remain In Review (待补证) / Return to In Progress / Blocked
- **Skipped disposition:**
- **Remaining risks:**

## Merge and Completion Record

> 合并信息由实际执行者记录；最终结论由 Acceptance Owner 核对并维护。

- **Merge required:** Yes / No
- **Merge commit:** 不需要合并时写“无”。
- **Integration HEAD before merge:** 合并前的集成分支 commit；不需要合并时写“无”。
- **Post-merge verification:** 在干净 worktree 中执行的命令/步骤、结果；不适用写“无”。
- **Worktree closure:** removed / retained
- **Cleanup / retention record:** 已清理时填写执行者和日期；保留时填写原因、Owner 和后续处置。
- **Completed by / Date:** Acceptance Owner / YYYY-MM-DD
- **Conclusion:** Done / Remain In Review (待补证) / Return to In Progress / Blocked
