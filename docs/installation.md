# 安装与同步

AI Framework 的共享规则由中央 Git 仓库发布；每个目标工程用 `ai-framework.lock` 固定一个 tag 和完整 commit。个人本地记录不进入目标代码仓库。

## 首次接入

在一个新项目中，先放入 `tools/ai-framework` 启动器，再在目标代码仓库根目录执行：

```bash
./tools/ai-framework init \
  --source <framework-git-url> \
  --ref <release-tag-or-commit> \
  --project-name <stable-project-name>
```

该命令必须在主工作树根目录执行，要求目标目录已是 Git 仓库，并且不存在 `AGENTS.md`、`ai-framework.lock` 与 `agent_bootstrap/`；它不会接管已有的本地控制平面。它会：

1. 下载指定的框架版本并写入 `ai-framework.lock`，其中包含实际完整 commit。
2. 创建根 `AGENTS.md`、根 `PROJECT-CONTEXT.md` 与外层 `.gitignore` 的 `/agent_bootstrap/` 规则。
3. 初始化 `agent_bootstrap/.git` 的 `main` 分支，作为个人本地工作记录 Git，并创建首次基线提交。
4. 仅首次创建 `project/` 和 `<project_name>_knowledge/`；知识库默认包含 `raw/` 原始来源目录。
5. 安装共享合约、角色、治理规则和模板。

`agent_bootstrap/` 只存在于主工作树。同一仓库的 linked worktree 通过 `git rev-parse --git-common-dir` 找到主工作树并公共读取它，不复制或初始化另一份治理目录。

首次基线提交使用框架身份 `AI Framework Bootstrap <ai-framework@local>`，不依赖开发者的全局 Git 姓名和邮箱；后续个人记录提交使用开发者自己的 Git 身份。

`--project-name` 可省略，脚本默认使用初始化者的目标仓库根目录名。它只允许字母、数字、点、下划线和连字符。项目初始化者应在提交前确认该名称；一旦 `ai-framework.lock` 进入版本管理，其他成员必须沿用其中的 `project_name` 与 `knowledge_dir`，不能根据个人克隆目录重新推导。

随后将目标工程根目录的 `AGENTS.md`、`PROJECT-CONTEXT.md`、`ai-framework.lock`、`tools/ai-framework` 和 `.gitignore` 提交到代码仓库。

## 日常同步

在目标工程根目录执行：

```bash
./tools/ai-framework sync
./tools/ai-framework sync --ref v2.4.4
./tools/ai-framework sync --latest
./tools/ai-framework status
./tools/ai-framework path
```

无参数 `sync` 严格按 lock 固定版本同步。`sync --ref <tag-or-commit>` 升级到指定版本；`sync --latest` 查询来源仓库并升级到最新稳定语义版本 tag。升级成功后会更新 lock 中的 `ref` 与完整 commit。`path` 输出公共 Agent 工作区、项目记录和知识库的实际绝对路径。`status` 和 `path` 可以从主工作树或 linked worktree 执行；`sync` 只能从主工作树执行，并更新其中唯一的公共 `agent_bootstrap/`。

它校验 lock 中的完整 commit，并更新该发布版本管理的内容：

```text
agent_bootstrap/README.md
agent_bootstrap/agents/AGENTS.md
agent_bootstrap/agents/roles/
agent_bootstrap/governance/
agent_bootstrap/templates/
tools/ai-framework
```

启动器始终从 lock 固定的同一发布版本更新；内容变化时，`sync` 会提示将 `tools/ai-framework` 提交到目标代码仓库。这样新成员首次 clone 后使用的启动器与项目选择的框架版本一致。

它不会覆盖：

```text
agent_bootstrap/.git/
agent_bootstrap/.gitignore
PROJECT-CONTEXT.md
agent_bootstrap/project/
ai-framework.lock 指定的 knowledge_dir/
```

## 升级框架

在主工作树执行 `./tools/ai-framework sync --ref <release-tag-or-commit>`，或执行 `./tools/ai-framework sync --latest` 选择最新稳定版本。命令成功后会更新 `ai-framework.lock` 的 `ref` 与完整 commit；经正常审查后提交 lock 和启动器变更。每位开发者随后运行 `./tools/ai-framework sync`。

## Worktree 使用

根 `AGENTS.md`、`PROJECT-CONTEXT.md`、`ai-framework.lock` 和 `tools/ai-framework` 必须由目标代码仓库 Git 跟踪，因此新 worktree 会自动带入这些入口。`agent_bootstrap/` 被忽略且不得复制到 linked worktree。

创建 worktree 后执行：

```bash
test -f AGENTS.md
test -f PROJECT-CONTEXT.md
test -f ai-framework.lock
test -x tools/ai-framework
test ! -e agent_bootstrap
./tools/ai-framework status
./tools/ai-framework path
```

`status` 和 `path` 会从当前 worktree 的 Git common dir 推导主工作树，读取同一份 Framework、项目记录和知识库。若失败，回到主工作树修复公共 Agent 工作区；不要在 Task worktree 执行 `init` 或复制治理目录。

既有 worktree 若已经包含 `agent_bootstrap/`，启动器会停止。先执行 `git ls-files -- agent_bootstrap`：有输出表示旧分支仍跟踪治理目录，应先将该分支更新到已移除跟踪并提交四个入口文件的新基线；无输出表示本机旧副本，必须先核对其中是否有主工作树尚未保存的项目记录或知识，再进行明确的本地清理。脚本不会自动删除或合并旧副本。

## 状态检查

```bash
./tools/ai-framework status
```

它显示公共 Agent 工作区、lock 版本及本地已安装的 commit。

## 迁移旧版复制接入

已通过旧版 `cp` 接入，或已有旧版 lock 但缺少 `project_name` / `knowledge_dir` 的项目不能运行 `init`。先将新的 `tools/ai-framework` 放入项目，然后执行：

```bash
./tools/ai-framework migrate \
  --source <framework-git-url> \
  --ref <release-tag-or-commit> \
  --project-name <stable-project-name> \
  --dry-run
```

确认报告后再执行：

```bash
./tools/ai-framework migrate \
  --source <framework-git-url> \
  --ref <release-tag-or-commit> \
  --project-name <stable-project-name> \
  --apply \
  --replace-shared
```

迁移必须从主工作树执行。它会补齐或更新 lock，将项目启动器升级到目标发布版本，将旧 `agents/PROJECT-CONTEXT.md` 复制到目标工程根目录，将标准旧 `knowledge/` 移到 lock 指定的 `<project_name>_knowledge/`，保留 `project/`，并将旧的 `agent_bootstrap/AGENTS.md` 归档为迁移记录。无法识别的自定义知识库目录不会自动移动。若旧根 `AGENTS.md` 不是旧入口的原样副本，迁移停止；先人工合并本地规则，或在确认后附加 `--replace-root-agent`。

旧共享文件是否被本地修改无法自动推断，因此 `--apply` 必须显式附加 `--replace-shared`。
