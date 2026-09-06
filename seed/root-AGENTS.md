# AI Framework Entry

本工程使用 AI Framework。开始项目工作前，先执行 `./tools/ai-framework path`。该命令通过 Git common dir 定位主工作树中唯一的公共 Agent 工作区；不得在 linked worktree 中复制或创建 `agent_bootstrap/`。

确认返回的 `workspace` 中存在 `agents/AGENTS.md`；缺失时执行 `./tools/ai-framework sync`。随后读取 `<workspace>/agents/AGENTS.md`，再按其中的顺序加载根目录 `PROJECT-CONTEXT.md`、当前角色和已分配工作。项目记录与知识库的实际位置也以 `path` 输出为准。

目标工程可以在本文件补充更严格的本地规则。
