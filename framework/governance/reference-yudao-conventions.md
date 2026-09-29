# 参考素材：芋道（ruoyi-vue-pro / yudao）工程约定

> **性质：参考资料，不是治理合约。**
> 本文件**不构成强制性规则**，仅供本项目在设计自身架构与编码约定时参考。与
> `java-code-style.md` 等治理合约冲突时，以治理合约和根目录 `PROJECT-CONTEXT.md` 为准。

## 0. 素材来源与许可

| 项 | 内容 |
| --- | --- |
| 上游项目 | ruoyi-vue-pro（社区常称"芋道源码 / Yudao"） |
| 仓库 | https://gitee.com/zhijiantianya/ruoyi-vue-pro |
| 分支 / 版本 | `master` / `2026.09-jdk8-SNAPSHOT` |
| 快照 commit | `1697112` |
| 获取日期 | 2026-09-29 |
| 许可 | MIT License，Copyright (c) 2021 ruoyi-vue-pro |
| 提取方式 | 浅克隆仓库后，从源码结构与根 `README.md` 归纳；原文未整体复制 |

MIT 许可允许自由使用、修改与再分发（需保留原始版权声明）。本文件为**归纳性参考**，
未复制上游大段正文；若后续需要引入具体代码或文档片段，须另行保留其版权与许可声明。

## 1. 上游项目定位

芋道是一套 **Spring Boot 多模块 + Vue 前后端分离**的企业级快速开发平台，
提供 RBAC 权限、数据权限、SaaS 多租户、Flowable 工作流、代码生成器等基础能力，
并按业务域拆分为多个独立 Maven Module（system / infra / bpm / pay / crm / erp /
mall / member / mp / report / ai / iot / im 等）。

与本项目的关系：本项目**模仿其架构思路**，因此重点参考其**工程约定**（分层、命名、
模块边界），而不是其业务功能清单。

## 2. 顶层工程结构（参考）

```text
<project-root>/
  <project>-dependencies/   # Maven 依赖版本统一管理（BOM）
  <project>-framework/      # 框架层扩展：以 spring-boot-starter 形式提供通用能力
  <project>-server/         # 应用启动入口，聚合各业务模块
  <project>-module-<biz>/   # 各业务模块，一个业务域一个 Module
```

要点：

- **依赖版本集中管理**：另立 `*-dependencies` 模块统一锁定第三方版本，业务模块不各自声明版本。
- **框架能力下沉到 starter**：通用技术能力（Web、MyBatis、Redis、Security、MQ、监控等）
  做成 `spring-boot-starter-*` 形式，业务模块只依赖 starter，不重复造轮子。
- **一个业务域一个 Module**：模块间不直接依赖彼此内部实现，通过 `api` 包协作（见第 4 节）。
- **server 只做聚合与启动**，不承载业务逻辑。

## 3. 模块内部分层（参考）

单个业务模块内部的包结构约定：

```text
<module>/
  api/            # 暴露给其他模块的接口、DTO、消息（对外契约）
  controller/     # 入站适配层：REST 接口、VO
  service/        # 业务编排与领域逻辑
  dal/            # 数据访问层
    dataobject/   # 持久化对象（DO）
    mysql/        # MyBatis Mapper 接口
    redis/        # 缓存访问
  convert/        # 对象转换（MapStruct）
  enums/          # 枚举与常量
  framework/      # 本模块的技术配置与扩展（非业务）
  job/            # 定时任务
  mq/             # 消息生产/消费
  util/           # 模块内工具
```

分层依赖方向（参考其实际做法）：

```text
controller → service → dal
api  ← 其他模块依赖此处；api 的实现类（*ApiImpl）委托给本模块 service
convert 为 service 与 controller 之间提供 DO/VO/DTO 转换
```

关键约定：

- **`api` 是对外契约**：跨模块调用只允许依赖对方的 `api` 包（接口 + DTO），
  不得直接依赖对方的 `service`、`dal`、`dataobject`。
- **DO / VO / DTO 分离**：持久化对象（DO）不出模块；对外传输用 DTO；
  接口入参出参用 VO，不与 DO 混用。
- 模块内按**业务子域**再分包（如 `controller/admin/user/vo/user/`），而不是按技术层全局堆放。

## 4. 命名约定（参考）

| 类型 | 约定 | 示例 |
| --- | --- | --- |
| 实体 / 持久化对象 | `XxxDO` | `AdminUserDO` |
| 数据访问接口 | `XxxMapper` | `UserMapper` |
| 业务接口 / 实现 | `XxxService` / `XxxServiceImpl` | `AdminUserService` / `AdminUserServiceImpl` |
| 入站控制器 | `XxxController` | `UserController` |
| 跨模块对外接口 / 实现 | `XxxApi` / `XxxApiImpl` | `AdminUserApi` / `AdminUserApiImpl` |
| 对外传输对象 | `XxxDTO` | `AdminUserRespDTO` |
| 请求参数对象 | `XxxReqVO` | `UserSaveReqVO` |
| 分页请求对象 | `XxxPageReqVO` | `UserPageReqVO` |
| 响应对象 | `XxxRespVO` | `UserRespVO` |
| 对象转换器 | `XxxConvert` | `UserConvert` |

补充：

- VO 按用途细分后缀（`SaveReqVO` / `UpdateReqVO` / `PageReqVO` / `RespVO` / `ImportExcelVO`），
  一个接口一个 VO，不复用宽泛 DTO。
- 包名全小写；类名 `UpperCamelCase`；与 `java-code-style.md` 的通用命名规则一致。

## 5. 与本项目现有约定的取舍

本项目已有 `java-code-style.md`（按业务能力分包 + 能力内分层，依赖向核心收敛）。
芋道的做法与之**同源但表述不同**，落地时注意：

| 维度 | 本项目现有合约 | 芋道做法 | 建议 |
| --- | --- | --- | --- |
| 顶层拆分 | 按业务能力分包 | 按 Maven Module 拆分业务域 | 可直接借鉴模块化拆分 |
| 模块内分层 | api/application/domain/adapter | api/controller/service/dal/convert | 二者可映射，择一保持一致 |
| 跨模块协作 | 通过 `api/` 或事件 | 通过 `api/` 包 + `*ApiImpl` | 一致，可借鉴 `*ApiImpl` 命名 |
| 对象命名 | 精确业务名，避免宽泛 DTO | DO / VO / DTO 后缀体系 | 可借鉴其 VO 细分后缀 |
| 依赖方向 | 依赖向 domain 收敛 | controller→service→dal | 芋道未强制内层纯净，需按本项目合约收敛 |

**结论**：可借鉴其**模块化拆分**、**`api` 契约包**、**DO/VO/DTO 命名体系**与
**starter 化通用能力**；但本项目 `java-code-style.md` 更强调分层依赖向核心收敛，
不应因模仿而放松这一约束。

## 6. 维护约定

- 本文件为一次性参考快照（对应上游 commit `1697112`），**不随上游自动更新**。
- 上游后续变更如需跟进，重新提取并更新第 0 节的 commit 与日期。
- 引用范围保持"归纳与摘要"，不整体搬运上游文档；如需引入具体片段，须标注来源与 MIT 许可。
