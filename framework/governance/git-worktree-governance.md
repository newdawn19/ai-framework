# 目标工程 Git 与 Worktree 治理合约

本文件位于目标工程的 `<agent-workspace>/governance/`，约束目标工程的 Git 与 worktree；不假定目标工程使用 `main`、`develop` 或固定分支前缀。

- **目标工程代码仓库：** 当前 Task 明确指定的代码仓库。
- **Agent 工作目录：** `./tools/ai-framework path` 返回的 `<agent-workspace>/`；其中同时保存共享规则和当前工程维护的项目事实。它只存在于主工作树，所有 linked worktree 公共读取。
- **项目记录：** Task 必须链接相关目标工程 commit。

## 1. 项目必须声明的 Git 配置

任何分支、worktree 或合并操作前，从目标工程根目录 `PROJECT-CONTEXT.md` 读取：

- 稳定 / 受保护分支。
- 日常集成分支；可以与稳定分支相同。
- Task 分支命名规则。
- 是否要求独立 worktree，或已批准的等价隔离方式。
- 允许合并和发布的 Owner。

缺少必要配置时不得猜测分支名称或执行分支、worktree、合并与删除操作。

## 2. 分支与隔离责任

| 对象 | 用途 | 约束 |
| --- | --- | --- |
| 稳定 / 受保护分支 | 可发布或项目认可的稳定状态 | 只有取得项目明确人工授权后才能执行受保护操作 |
| 日常集成分支 | 接收已验收 Task 并形成集成目标 | 不直接进行未隔离的 Task 实现；与稳定分支相同时仍遵守相同验收规则 |
| Task 分支 | 单个 Task 的实现与工程师验证 | 从当前集成基线创建，只包含该 Task 范围 |
| Task worktree | Task 的文件系统隔离 | 一个 worktree 只对应一个 Task 分支，不混入其他工作 |

默认使用独立 worktree 隔离工程 Task。遗留项目无法使用 worktree 时，在项目上下文记录等价隔离方式和理由。

每个新 worktree 都必须包含 Agent 入口和项目上下文：

```bash
TASK_WORKTREE_PATH='replace-with-task-worktree-path'
test -f "$TASK_WORKTREE_PATH/AGENTS.md"
test -f "$TASK_WORKTREE_PATH/PROJECT-CONTEXT.md"
test -f "$TASK_WORKTREE_PATH/ai-framework.lock"
test -x "$TASK_WORKTREE_PATH/tools/ai-framework"
test ! -e "$TASK_WORKTREE_PATH/agent_bootstrap"
"$TASK_WORKTREE_PATH/tools/ai-framework" status
"$TASK_WORKTREE_PATH/tools/ai-framework" path
```

linked worktree 不承载 `agent_bootstrap/`。`status` 或 `path` 失败时，回到主工作树检查公共 Agent 工作区及版本锁定；不得在 Task worktree 初始化另一份治理目录或猜测规则继续执行。

既有 linked worktree 已存在 `agent_bootstrap/` 时必须停止：若 `git ls-files -- agent_bootstrap` 有输出，先更新该 Task 分支到不再跟踪治理目录的新基线；若无输出，先确认其中没有主工作树尚未保存的项目记录或知识，再由人工明确清理。不得由脚本静默删除、覆盖或合并旧副本。

## 3. Task 的 Git 交付

1. 从当前集成基线创建 Task 分支和隔离 worktree。
2. 在 Task worktree 中完成实现、工程师验证和范围内提交；交付、验收和合并后验证前，工作树必须干净。
3. 在 Task 中记录 delivery commit 及其集成基线 commit，并转为 `In Review`。
4. 独立验收通过后，按项目声明的方式合并到集成分支。
5. 在 Task 中记录 merge commit 和最小合并后验证。
6. Task Owner 确认 worktree 没有未处理修改、分支已经合并且不再需要后，可按项目权限自行清理该 Task 的 worktree 和已合并分支，并在 Task 中记录结果。

## 4. 合并规则

合并 Task 分支前必须满足：

- 提交仅包含该 Task 的相关修改。
- Task worktree 在交付、验收和合并后验证时没有未提交或未跟踪的修改；项目明确忽略的构建产物除外。
- Task 已记录 delivery commit、其集成基线 commit，并完成对该 delivery commit 的独立验收。
- 合并前必须确认当前集成分支 HEAD。若它不同于记录的集成基线，先检查新增提交对本 Task 的代码、依赖、合同和验收范围的影响及合并冲突；不能仅因 HEAD 前进就要求更新 Task 基线或整套重验。无相关影响且可无冲突合并时，由 Acceptance Owner 记录证据复用依据，保留原 delivery commit，按项目约定合并并做最小合并后验证。
- 存在相关影响、冲突或无法证明无影响时，按项目约定更新基线或形成候选集成结果，绑定实际待验 commit 并由 Acceptance Owner 做定向独立复验；影响扩大时扩大回归。改变交付内容后重新进入 `In Review`，未受影响的历史证据可按共享规则复用，不将旧验收结论直接套在新 commit 上。最终合并前再次核对集成 HEAD，发生变化时按同一规则评估。
- Task commit 不包含合约升级或无关项目记录；合约升级必须是独立依赖更新。

合并后必须记录 merge commit、合并前的集成分支 HEAD，并在干净 worktree 中运行 Task 定义的最小合并后验证。

## 5. 安全与清理

- 不改写未经明确授权的共享历史，不强推受保护分支。
- 不删除无法证明已合并、已备份或可安全丢弃的分支、worktree、tag 或数据。
- 不再推进的 Task 分支应记录保留原因、Owner 和后续处置，不以静默删除代替取消记录。
- Sprint Review、发布前检查或项目结束 Review 必须清点遗留 worktree。未清理项必须记录关联 Task、Owner、保留原因和后续处置；不得为收尾强制删除。
