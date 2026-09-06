# AI Framework

AI Framework 发布可复用的 Coding Agent 协作合约、角色、治理规则与模板。

团队将框架版本、项目名和个人知识库逻辑路径固定在目标工程的 `ai-framework.lock`，并使用 `ai-framework sync` 将该版本安装到主工作树唯一的 `agent_bootstrap/`。linked worktree 通过 Git common dir 自动读取这份公共 Agent 工作区，不复制治理目录。根 `PROJECT-CONTEXT.md` 由目标工程 Git 共享；个人项目记录和项目名知识库由 `agent_bootstrap/.git` 在本地管理。

目录说明见 [安装文档](docs/installation.md)；完整操作与工作流说明见 [HTML 使用指南](docs/usage-guide.html) 或 [PDF 使用指南](output/pdf/ai-framework-usage-guide.pdf)。
