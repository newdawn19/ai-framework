# <Test-Plan-ID>：<标题>

**Status:** Draft / Approved / Superseded
**Related:** Sprint / Task / LDP
**Trigger:** High Risk / Sprint Closure / Project Required
**Test Owner:**
**Gate Owner:**
**Approved by / Date:** 指定 Acceptance Owner 或 Gate Owner / YYYY-MM-DD；未确认写“待确认”。
**Target commit / build:**

范围内测试计划由指定 Acceptance Owner 或 Gate Owner 确认，`Approved` 仅表示测试计划已确认，不新增执行权限或人工审批。只有涉及已批准范围、验收标准或权限等决策边界的变化，才按共享合约请求相应决定或授权。

## Scope

测试什么，以及明确不测试什么。

## Risks

需要重点证明的失败模式或兼容性风险。

## Cases

| Case ID | Type | Command / Steps | Expected Result | Required |
| --- | --- | --- | --- | --- |
| CASE-001 | Normal | | 可观察成功结果 | Yes |
| CASE-002 | Failure | | 上一份有效状态保留或明确 fail-closed | Yes |

## Environment

记录可复现所需的非敏感系统、版本、配置和设备类型；不得记录凭据、生产地址或私有载荷。

## Pass Criteria

通过测试必须满足的条件。

## Skipped Policy

允许跳过的条件，以及跳过时必须记录的验收影响和处置。
