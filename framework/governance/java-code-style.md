# Java 命名与工程架构合约

命名和基础编码规则参照《阿里巴巴 Java 开发手册》的通行实践；“按业务能力分包、能力内分层和依赖向核心收敛”是本框架采用的工程架构约定，不声称来自该手册。

## 1. 适用范围与规则等级

本合约适用于目标工程中的 Java 生产代码与测试代码。目标是让新增代码易读、易改、易测，同时避免对遗留项目进行无关迁移。

- **必须 / 禁止：** 可客观检查的底线，除项目既有兼容性要求外不得以个人偏好跳过。
- **推荐：** 新项目的默认选择。遗留项目可继续使用一致的既有约定，并在项目上下文或 Task 中说明偏离原因。
- 已有 formatter、lint 和静态分析配置优先决定排版；不得为采用本合约制造全量格式化或批量改名。
- 没有既有格式规则时，使用 UTF-8、4 个空格、禁止 Tab、单行不超过 120 字符，并为控制语句使用大括号。
- 只使用项目声明的 Java 版本支持的语言和标准库能力；升级 Java 基线不是普通风格调整。

## 2. 命名

### 2.1 通用规则

- 标识符必须使用清晰英文；禁止中文、拼音、拼音与英文混写，以及团队外无法理解的私有缩写。
- 包名全小写；类型使用 `UpperCamelCase`；方法、字段、参数和局部变量使用 `lowerCamelCase`；常量使用 `UPPER_SNAKE_CASE`。
- 缩写在普通类型和方法名中按单词处理，例如 `HttpClient`、`UrlParser`；行业稳定后缀可以保留大写，例如 `OrderDTO`、`OrderDO`。同一缩写在项目内必须保持一致。
- 一个顶层 `public` 类型对应一个同名 `.java` 文件。

### 2.2 包和类型

- 包优先以业务能力或明确技术边界命名，例如 `order.application`、`payment.adapter.outbound`；新项目推荐包段使用单数英文词，遗留项目保持现有一致约定。
- 禁止把业务代码持续堆入 `common`、`util`、`misc` 等无明确所有权的包。确需共享的能力必须有稳定语义和明确 Owner。
- 抽象类使用 `AbstractXxx`；异常使用 `XxxException`；测试类使用 `XxxTest`；枚举使用有业务含义的 `XxxStatus`、`XxxType` 或 `XxxCode`，不使用无信息量的 `XxxEnum`。
- 接口不使用 `I` 前缀，以能力或边界命名，例如 `OrderRepository`、`PaymentClient`。实现类优先表达技术或渠道，例如 `JpaOrderRepository`、`HttpPaymentClient`；只有项目已有一致约定且技术来源确实无意义时才使用 `XxxImpl`。

### 2.3 边界对象

- HTTP、消息和公开接口优先使用精确名称，例如 `CreateOrderRequest`、`OrderResponse`、`OrderCreatedEvent`，不得让不同边界复用同一个宽泛 DTO。
- 跨应用或模块传输对象可以使用 `XxxDTO`。持久化对象沿用项目统一选择的 `XxxDO`（阿里风格）或 `XxxEntity`，同一边界不得混用。
- `VO` 容易同时表示 View Object 和 Value Object，除非项目上下文已定义唯一含义，否则不使用该后缀。领域值对象优先直接使用业务名称，例如 `Money`、`EmailAddress`。
- 传输对象、持久化对象和领域对象不得互相承担对方的持久化、副作用或核心业务职责。

### 2.4 方法、字段和常量

- 方法以动词或动宾短语开头，例如 `createOrder`、`findById`、`validateRequest`；查询方法不得隐藏写入，写入方法不得伪装成查询。
- 名称应表达单位和语义，例如 `timeoutMillis`、`maxRetries`、`customerId`；避免 `data`、`info`、`obj`、`tmp` 等无上下文名称。
- primitive `boolean` 字段使用状态词，如 `enabled`、`deleted`，访问器通常使用 `isEnabled()`；包装类型 `Boolean` 按项目 Bean/序列化约定使用 `getXxx()` 或框架要求的形式。字段本身避免 `isXxx`，集合使用复数名。
- 常量放在拥有其语义的最小类型或模块中；禁止散落魔法值，也禁止创建无边界的全局 `Constants` 垃圾箱。

## 3. 新项目默认工程架构

新项目按业务能力分包，每个能力内部再按职责分层。示例中的 `order/` 是订单业务能力，不是全项目共享技术层。

```text
com.example.product/
  order/                         # 订单业务能力的全部代码
    api/                         # 对其他业务能力公开的稳定接口、模型与事件
    application/                 # 用例编排、事务边界与权限协调
    domain/                      # 核心业务规则、领域对象与端口；不依赖技术实现
    adapter/
      inbound/                   # REST、MQ Consumer、CLI 等外部请求入口
      outbound/                  # JPA、HTTP Client、MQ Producer 等外部实现
  customer/                      # 客户业务能力；内部按需要采用相同结构
  shared/                        # 极少量无单一业务归属、语义稳定的共享能力
  infrastructure/                # 全应用装配、框架配置、过滤器与迁移配置；不放业务规则
```

依赖必须向业务核心收敛：

```text
adapter/inbound → application → domain
adapter/outbound ──implements──> domain/application ports
infrastructure ──assembles──> application + adapters
```

- `domain` 不依赖 Web、数据库、消息或具体框架实现。
- `adapter/outbound` 只实现核心定义的端口，不把持久化或远程模型泄漏到领域层。
- `infrastructure` 负责跨应用装配和技术配置，不承载业务判断。
- 业务能力之间通过对方 `api/`、事件或明确用例协作，不得直接依赖对方的 adapter、DAO、持久化对象或数据库实现。
- 不要求每个能力机械创建所有目录；没有真实职责时不要创建空层。

## 4. 遗留项目

- 以根目录 `PROJECT-CONTEXT.md` 声明的现有模块、目录、公开边界和依赖方向为准；本合约不要求迁移到默认结构。
- 不得为了合约进行全量移动、重命名或重写。修改既有模块时优先保持模块内一致，只改善当前 Task 实际触及的代码。
- “不得直接依赖 DAO、JPA Entity 或内部实现”主要约束跨模块和新旧能力边界；在既有模块内部扩展时可以遵循其已有模式，不强行增加无收益适配层。
- 新模块可以采用默认架构，但必须先明确它与遗留模块的边界；架构迁移必须是独立 Task，先补回归测试，再按业务能力渐进迁移，不与功能交付混在同一提交。

## 5. 关键编码与评审底线

- 公开边界必须校验输入并提供明确失败语义；不得暴露内部可变集合、持久化对象或基础设施类型。
- 金额使用 `BigDecimal` 的字符串或整数构造方式，不使用 `new BigDecimal(double)`；值类型实现 `equals` 时必须同步实现 `hashCode`。
- 资源使用 try-with-resources 或等价的明确生命周期管理；不得吞掉异常，转换异常时保留 cause 和可行动上下文。
- 日志使用参数化形式，不使用 `printStackTrace()` 或 `System.out` 作为生产日志，不记录秘密、完整敏感载荷或生产地址。
- 共享可变状态必须有明确线程所有权和同步策略；线程池必须有有界队列、拒绝策略和可识别线程名，不使用无边界工厂默认值。
- 超时、重试、幂等性和取消是 API 行为的一部分；禁止无边界重试和在循环中进行未受控远程调用或数据库写入。
- 新增或修改行为必须覆盖相关正常、失败和边界路径；Bug 修复应使用测试锁定已证实回归。时间、随机、网络和外部 IO 应通过可控边界测试。

## 参考基线

- 《阿里巴巴 Java 开发手册》：命名和基础工程实践参考。
- 项目采用的 Java SE 版本与框架官方文档：语言、资源管理和框架行为的权威来源。
- 本文件第 3 节：本框架的业务能力分包与依赖方向约定。
