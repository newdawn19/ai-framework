# 项目知识库

- 本目录保存当前项目长期可复用的知识。
- 查询路径为 `index → topic/card → source`；普通只读查询不修改日志。
- 目录结构与治理规则见 `<agent-workspace>/governance/knowledge-base-contract.md`。


## Scope

- 待填写：知识库覆盖的领域。

## Out of Scope

- 密码、token、客户私有数据和生产环境秘密。
- 仅属于单个 Task 的临时执行过程。

## Source Policy

- 待填写：允许的来源、许可、敏感等级和外部存储规则。
- 不能提交原文的来源只保存允许的非敏感元数据和受控位置标识。
- 允许保存的不可变来源快照放入 `raw/`，并同步登记到 `source-manifest.yaml`。

## Ownership and citation

- **Domain Owner：** 待填写。
- **Conflict reviewer：** 待填写。

## Tag Vocabulary

页面只能使用本节登记的标签。新增标签时先更新词表。

| Tag | Meaning |
| --- | --- |
| `architecture` | 架构与依赖边界 |

## Maintenance

- 来源状态以 `source-manifest.yaml` 为准。
- 查询入口为 `wiki/index.md`。
