# Agent Collaboration Rules

本文件是所有采用本框架的项目共享合约，优先于角色配置、本地 Decision Package、Sprint 和 Task。

## Context Loading

`<agent-workspace>` 表示 `./tools/ai-framework path` 返回的 `workspace` 绝对路径。它始终指向主工作树中唯一的 `agent_bootstrap/`；linked worktree 只读取该公共目录，不得创建副本。`<knowledge-path>` 表示同一命令返回的 `knowledge` 路径。

- 未明确角色或任务时，不假定实现、决策、发布或验收权限。
- 不读取整个仓库、全部历史或任何私有凭据；角色配置可声明额外的最小必读上下文。
- 创建项目记录时，先读取 `<agent-workspace>/project/README.md` 确定位置，再只读取对应模板。
- 仅在摄入、维护、查询或检查项目知识库时，先读取 `<agent-workspace>/governance/knowledge-base-contract.md`、`<knowledge-path>/README.md`、必要索引和来源。`ai-framework.lock` 保存知识库逻辑路径，实际绝对路径以 `./tools/ai-framework path` 为准。精确参数、高风险结论或冲突内容必须继续核对原始来源。
- Sprint 或 Task 已直接链接的本地 DP（LDP）可直接读取正文；需要跨 LDP 查询、确认当前状态或追溯取代关系时，先读取 `<agent-workspace>/project/decision_package/README.md`，再只打开相关 LDP。索引不替代 LDP 正文，冲突时以正文为准。

每个 Agent 按以下顺序加载最小必要上下文：

```text
目标工程根目录的 `AGENTS.md` 入口
  ↓
<agent-workspace>/agents/AGENTS.md
  ↓
目标工程根目录的 `PROJECT-CONTEXT.md`
  ↓
<agent-workspace>/agents/roles/<role>.md
  ↓
assigned Sprint or Task and linked Decisions
```

`<agent-workspace>/agents/AGENTS.md`、`<agent-workspace>/agents/roles/`、`<agent-workspace>/governance/` 和 `<agent-workspace>/templates/` 是共享规则；除非当前 Task 明确维护 AI Framework，不得修改。根目录 `PROJECT-CONTEXT.md` 是目标工程共享事实并由目标工程 Git 管理；`<agent-workspace>/project/` 和 `<knowledge-path>/` 是个人本地记录，由 `<agent-workspace>/.git` 管理。

## Agent Roles and Human Authorization

本框架包含 [Product Manager](roles/product-manager.md)、[Project Manager](roles/project-manager.md)、[Senior Software Engineer](roles/senior-software-engineer.md) 和 [Platform Engineer](roles/platform-engineer.md) 四个 Agent 角色，具体职责与边界只在对应角色文件定义。

`Owner`、`Acceptance Owner`、Gate 负责人和 Sprint 复核者是具体记录中的责任分配，不是额外的全局角色。

本地 DP 必须指定 Local Decision Owner。Agent 只有在当前工作明确授权其代表该 Owner 时，才能接受、拒绝或修订本地 DP。AI Framework 合约变更、高影响例外、受保护分支、发布、生产和硬件操作等需要人工决定或授权的事项，必须记录明确的人工来源、范围和日期。Agent 不得自行取得或代替人工授权；目标工程中的授权也不能扩展为修改 AI Framework 源合约的权限。


## User-facing Communication

面向用户的功能讨论、方案说明、问题诊断、验收汇报和审批请求，先说明问题、拟议变化和实际影响，再提供必要技术细节。常见术语直接使用；生僻术语、底层机制和内部概念结合当前问题解释，不以内部编号或技术细节代替影响说明。请求决策时说明推荐方案、理由，以及同意后会执行什么。简单事项简短说明，不强制套用完整表格。

## Instruction Priority

```text
AGENTS.md
  > project context and role configuration
  > accepted local Decision Package
  > Sprint
  > Task
```

低优先级文档不得改变高优先级边界。Task 只定义实现工作，不能改变产品、架构、安全、对外合同或已冻结 Sprint 范围。

## Collaboration Flow

```text
Optional Discussion
  ↓
Sprint Draft
  ↓
Sprint Ready
  ↓
Task planning and implementation
  ↓
Delivery commit (`In Review`)
  ↓
Independent acceptance
  ↓
Merge and post-merge verification when applicable
  ↓
Task `Done`
  ↓
Integration gate (when applicable)
  ↓
Hardware / target-environment gate (when applicable)
  ↓
Sprint review and completion
```

- 影响产品方向、架构、数据模型、认证、权限、安全、外部 API、存储/删除语义、跨端职责或已冻结 Sprint 的变化，必须走 `Issue → Product Manager review → local DP → Sprint/Task update`。
- 普通 Bug、局部重构、实现细节和不影响已接受决定的测试补充不需要新本地 DP。
- Sprint 主状态为 `Discussion → Draft → Ready → In Progress → Completed`。`Discussion` 是可选的非权威记录；进入 `Draft` 前，命中决策边界的内容必须有相关 `Accepted` 本地 DP，未命中时必须明确目标、范围和非目标。
- `Discussion` 和 `Draft` 均不得创建 Task 或开始实现。转为 `Ready` 前必须具备必要关联的 `Accepted` 本地 DP，并确认验收标准可验证、依赖和必要合同已冻结、Gate 计划完整；已有本地 DP 适用时直接复用，没有时创建或修订本地 DP，并由明确的 Local Decision Owner 接受。
- Sprint `Ready` 后才能创建可执行 Task；首个 Task 开始时 Sprint 进入 `In Progress`。
- `Blocked` 是从 `Ready` 或 `In Progress` 进入的旁路状态，必须记录阻塞事实、Owner、解除条件和此前状态；解除后回到此前有证据支持的状态。
- `Cancelled` 必须记录原因、剩余风险和人工授权来源；不得用删除或归档代替取消记录。
- Sprint 只有在范围内必要 Task 全部 `Done`、必要 Gate 通过、skipped 已处置且 Sprint Review `Passed` 后才能 `Completed`。Review 未通过时，Sprint 回到 `In Progress`、进入 `Blocked`，或经明确决策转为 `Cancelled`。

## Task and Delivery Gates

```text
Ready → In Progress → In Review → Done
```

- **独立验收 Gate：** 每个 Task 必须指定与 Task Owner 不同的 Acceptance Owner，并绑定不可变的 delivery commit。影响验收范围的修改使原验收失效；需要合并时还必须记录 merge commit 和最小合并后验证，才能将 Task 标记为 `Done`。
- **集成验收 Gate：** 当交付跨越多个 Task、模块或服务时适用，在相关 Task 合并并形成明确集成目标后执行。核对接口、依赖、集成回归和必要的端到端结果；不能用单个 Task 的 `Done` 代替。
- **硬件 / 目标环境 Gate：** 当交付依赖实体设备、设备协议、存储介质、移动端真机、发布环境或其他目标环境时适用。它必须绑定明确的 build/commit、配置和非敏感环境标识，核对实际环境证据与已冻结合同；不能由模拟或本地结果代替。

涉及环境搭建、部署、发布或其他目标环境变更时，项目经理、平台工程师和相关验收者按需读取 `<agent-workspace>/governance/deployment-governance.md`。目标环境 Gate 验证部署结果，不作为每条环境操作的执行许可；部署授权、执行 attempt 和重新授权规则以该合约为准。

Gate 的适用性、Owner、输入和通过条件必须记录在 Task 或 Sprint 中。Gate 只验证已批准范围；新增阻塞门槛必须说明需求依据或具体风险，涉及验收标准、需求、架构或合同变化时，回到 `Issue → Product Manager review → local DP → Sprint/Task update`。

合同冻结是 Sprint `Ready` 条件，不是交付 Gate。项目可以增加专属 Gate，但不得替代三类标准 Gate。

Task 的状态、交付、验证和验收变化以 Task 正文为准。实现错误退回 `In Progress`，证据不足保持 `In Review`，依赖、权限或环境不可用进入 `Blocked`；`Blocked` 必须记录事实、Owner、解除条件和此前状态。验收标准、需求或合同需要变化时按决策边界处理。历史证据仅在相关代码、合同、配置和环境未发生影响性变化且绑定明确 commit/build 时复用，并记录理由和适用范围。

## Document Ownership and Fact Sources

目标工程统一使用 `<agent-workspace>/project/` 保存治理与执行记录，使用 `<knowledge-path>/` 保存项目知识；维护、批准和事实源遵守本合约的角色分工。

- Accepted 本地 DP 是当前工作中决策边界、约束与取舍的事实源。
- Sprint 是目标、范围、非目标、验收标准、Gate 计划和 Sprint 生命周期状态的事实源；其中的 Task 进度列表只能是派生摘要。
- Task 是执行状态、交付与合并 commit、工程验证和独立验收的事实源。
- Test Report 保存其绑定目标和环境下的原始测试结果；Task 或 Review 只链接和判定这些证据，不复制完整结果。
- Sprint Review 是 Sprint 收尾结论的事实源；看板、Task Index 和本地 DP Index 只提供导航或派生摘要。
- `<agent-workspace>/project/archive/` 是只读历史，不能作为当前需求或执行授权来源。

项目上下文只记录共享合约无法推断的工程事实、命令、Git 方式、风险和更严格规则，不配置角色职责或文档路径。它不得取消独立验收、改变上述权限分离、降低安全边界或允许低优先级记录覆盖本合约；也不得将部署执行包络内的普通诊断、局部修复或预算内重试收紧为逐命令人工审批，除非项目 Owner 在关联 LDP 中明确其额外实质风险、缩减范围和解除条件。与角色规则出现实质冲突时停止执行并请求人工处理。

项目知识库保存来源、事实、解释、置信度与争议，不是产品或架构决定，也不能授权实现。知识证据需要改变已接受决定时，必须进入 `Knowledge evidence → Issue → Product Manager review → local DP → Sprint/Task update`。

## Test Proportion Rules

验收覆盖已确认标准及修改涉及的实际风险，优先执行定向验证；只有影响扩大时才扩大回归。满足必要标准后推进，不为追求更完整继续增加检查；不得降低已确认标准、跳过必要失败路径或将实际缺陷改称可选改进。

- **Quick：** 文档、文案、小型重构或低风险局部修改，运行编译、格式检查或受影响的最小验证。
- **Module：** API、页面、服务或一般模块行为，运行受影响模块的测试，以及适用的合同或集成测试。
- **High：** 认证、授权、支付、数据迁移、存储、删除、外部 API、队列、发布或项目声明的其他高风险范围，运行定向失败路径验证和相关回归，并在 Sprint 收尾完成必要的端到端或目标环境验证。

普通 Task 的测试证据记录在 Task 中；只有 High 风险变更、Sprint 终验或项目约定要求时才创建独立 Test Plan / Test Report。测试记录必须写明命令或步骤、结果以及 skipped/未执行项。任何 skipped 都必须说明原因、验收影响和处置；命中必要验收项时，Task 不得 `Done`，Sprint Review 不得 `Passed`。

Test Report 的 `Failed` 表示测试已执行但不满足条件，`Blocked` 表示因依赖、权限或环境无法完成，二者不得混用。重试、波动结果和首次失败必须保留，不得只记录最终一次通过。

验证工具异常导致的未完成检查，不直接判为产品缺陷，也不能视为通过。既有授权内允许修复工具或使用等价方法；不改变验收标准、覆盖范围和操作权限的技术调整，不单独创建 LDP 或申请授权。记录异常及替代方法的等价依据，必要检查仍须完成。

## Safety and Repository Boundaries

### 本地资料与外传边界

- Agent 默认可直接读取、分析和使用当前工作区中的代码、配置、`.env`、本地日志、测试数据、本地数据库结果和运行时诊断信息，用于当前 Task 的实现、测试与排障。
- 可在本机运行已有开发、测试和调试命令，即使这些命令会隐式使用本地凭据。
- 本地读取、分析、调试、运行测试和生成临时结果不要求脱敏、分类、截断或额外确认；应保留足以定位问题的原始上下文。
- 不得将密码、token、API key、Cookie、私钥、完整数据库连接凭据、支付或银行卡信息、真实用户直接身份信息，或未经授权的生产数据，上传、提交、发送或复制到当前项目工作区之外的外部服务、公开渠道、第三方仓库、共享文档或其他未明确授权的位置。

- 不将 runtime 资产、构建输出、私有配置或测试临时文件提交到 Git。
- 不执行未经授权的破坏性操作；不得删除无法证明可安全删除的数据。
- Commit 只能包含当前已授权工作范围：目标工程的工程实现与项目记录均按 Task 限定；合约版本更新必须是独立、明确授权的依赖升级。保留并报告无关改动，不覆盖或一并提交。
  - 经用户在当前对话明确授权除外。
- Git 分支与 worktree 操作必须遵守根目录 `PROJECT-CONTEXT.md` 中的项目约定；未填写必要配置时，不假定项目采用固定的分支模型。
