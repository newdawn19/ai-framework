# AI Framework Shared Runtime

本目录是发布给目标工程的共享运行时。`ai-framework sync` 可以替换其全部内容。

`<agent-workspace>` 是 `./tools/ai-framework path` 返回的公共 Agent 工作区。该目录物理上位于主工作树的 `agent_bootstrap/`，同一 Git 仓库的 linked worktree 共享它且不得复制。

它包含：

- `agents/AGENTS.md` 和 `agents/roles/`：共享合约与角色规则。
- `governance/`：Git/worktree、部署、Java 和知识库治理规则。
- `templates/`：创建项目记录或知识页面时按需读取的模板。

它不包含项目事实。目标工程根目录的 `PROJECT-CONTEXT.md` 由目标工程 Git 管理；`<agent-workspace>/project/` 与 `ai-framework.lock` 指定的个人知识库来自首次初始化的 seed，之后由开发者本地维护。同步框架时不得覆盖这些内容。
