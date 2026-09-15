# Server Agent — Decisions

记录你与 Server Agent 已确认的长期服务端能力与工作方式。

建议后续逐项确认：
- 语言与框架。
- 数据库与缓存。
- API / 协议 / 序列化。
- 单体或服务拆分原则。
- 部署与云平台。
- 消息/异步任务策略。
- 第三方服务政策。
- 测试、迁移和回滚策略。
- 性能、成本、安全与可观测性目标。

## 当前项目已确定的外部技术约束

以下是项目级决定，不代表 Server Agent 已完成自身能力校准：
- 服务端 runtime / framework：Node.js + NestJS + TypeScript。
- 架构：模块化单体，当前不采用微服务作为默认架构。
- 前后端使用标准 WebSocket；服务端 WebSocket 实现使用 `ws`；生产环境使用 `wss`。
- 消息编码使用 Protobuf，Contract 统一维护在 Monorepo 的 `packages/protocol/`。
- MySQL 是主持久化数据库。
- Redis 用于在线用户缓存、Session、限流、短期状态、排行榜/计数等高速场景，但不是关键业务唯一事实源。
- 每个游戏服务区默认对应一个游戏进程；每个区绑定独立可迁移的 MySQL Database/Schema 与 Redis 数据源配置。
- MySQL 核心用户数据优先使用明确字段与规范化业务表，不把可查询核心数据整体塞入 JSON 字段；一对多集合应拆独立业务表而不是无限扩张 `user` 宽表。
- Redis TTL 用于过期/失效类状态；业务关键定时任务不得只依赖 TTL/keyspace notification，必须有可恢复的 due-time 调度状态。
- Protobuf Envelope、字段缩写和 numeric message id 使用 `project/PROTOCOL_REGISTRY.md` 作为统一注册源。
- 服务端是关键业务数据与可信结果的权威来源。
- 基础框架同时支持普通请求/响应型玩法与实时玩法，但不得为尚未出现的实时需求提前过度复杂化。
- 建立独立 Global Service / Global DB：平台账号/身份映射、支付订单与支付幂等、区服目录/元数据、未来跨服聚合数据归 Global；角色、背包、任务、装备、区内活动/战斗/排行等归所属 Zone。
- Zone 不得通过跨库 Join 直接读取 Global DB；跨边界访问必须通过明确 Global Service Contract。
- 单区数据迁移时，Global 账号与支付事实不随 Zone DB 一起迁移。

## 2026-09-14 — 第一轮服务端工作方式校准

用户已确认以下长期工作要求：
- 服务端代码优先简洁、直接、可维护，重点降低功能持续迭代后的返工成本。
- 非琐碎需求在编码前先形成轻量 Feature Brief，并与 Tech Lead 沟通模块边界、复用点、协议/数据影响和主要风险。
- 实现前主动检查已有组件、公共方法、公共配置、Repository、协议和基础能力；存在真实重复时优先复用或提出抽离重构。
- 在质量可靠的前提下关注模型和 Token 成本：确定性工作优先较快/低成本模型和最小必要上下文，高风险架构/安全/一致性问题再升级推理能力。
- Token 数量是效率指标，但不能以牺牲正确性、安全、可维护性、测试和减少返工为代价。

当前尚未完成用户对 Server Agent 全部工作方式与具体服务端能力边界的校准。登录/Session、DB/Redis 具体库、一致性策略、可靠定时任务等事项延后继续确定。
