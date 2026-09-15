# Tech Lead Agent — Decisions

记录你与 Tech Lead Agent 已确认的长期技术治理方式、架构偏好和评审原则。

## 2026-09-14 — 第一轮技术架构校准

已确认：
- 目标平台：本地 Web 完整开发/调试环境、微信小游戏、抖音小游戏；暂不以原生 App 市场为目标。
- 客户端技术方向：Cocos Creator + TypeScript。
- 服务端开发语言与框架：Node.js + NestJS + TypeScript。
- 前后端连接：标准 WebSocket；开发环境可使用 `ws`，生产环境使用 `wss`。
- 权威模型：关键业务数据和结果采用服务端权威。
- 基础网络/业务框架同时支持普通请求/响应型玩法和实时战斗型玩法，但默认避免为未来实时玩法提前过度复杂化。
- 服务端架构：模块化单体；当前不采用微服务作为默认架构。
- 仓库结构：采用 Monorepo，前端、服务端、协议、共享代码和工具统一管理，同时保持模块边界。
- 跨平台策略：平台能力通过 Adapter/Platform SDK 隔离，核心业务代码不直接绑定微信或抖音 API。
- 开发基础设施：从早期建设 UI、组件、VFX、网络和平台能力的独立调试/展示入口，并要求这些调试能力同样可跨平台运行。

## Code Review 治理决策

Tech Lead 长期负责 Client / Server 的架构与代码健康 Review：
- 防止日常迭代使代码、模块和 Manager 持续臃肿。
- 识别重复组件、公共方法、公共配置、协议定义和基础能力。
- 对真实存在的重复和跨模块复用，要求对应 Client / Server Agent 抽离、合并或重构到合适的共享层。
- 避免把所有公共内容堆进无边界的 `common` / `utils`。
- 避免为了预期未来需求而提前做不必要的复杂抽象。

## 2026-09-14 — 数据层确认

已确认：
- 主数据库使用 MySQL。
- 缓存使用 Redis。
- Redis 默认作为缓存、会话、短期状态、限流、排行榜/计数等高速层，不作为关键业务的唯一持久化事实源。

## 2026-09-14 — 服务端框架与协议确认

已确认：
- 服务端 runtime / framework：Node.js + NestJS + TypeScript。
- WebSocket 实现：服务端使用标准 `ws`；不以 Socket.IO 作为默认协议层。
- 消息编码：Protobuf。
- Protobuf Contract 统一维护在 Monorepo 的 `packages/protocol/`，前后端共享生成代码和类型。
- MySQL 作为主持久化数据库；Redis 作为缓存、Session、限流、短期状态、排行榜/计数等高速层。

## 2026-09-14 — 区服、数据建模与协议 Envelope 补充

已确认：
- 每个游戏服务区默认对应一个游戏进程；区服绑定独立、可迁移的 MySQL Database/Schema 与 Redis 数据源配置。
- MySQL 是玩家长期数据权威源；核心可查询数据优先明确字段/规范化业务表，避免以 JSON 字段承载主要用户业务结构。
- Redis 重点承载在线用户缓存与短期状态；TTL 适合过期语义，但关键定时任务必须采用可恢复的 due-time 调度方案，不能只依赖过期通知。
- Protobuf 使用统一轻量 Envelope：`mt`、`mi`、`ri`、`sq`、`ec`、`p`；缩写和 numeric message id 统一登记在 `project/PROTOCOL_REGISTRY.md`。
- Redis Key 规范已确认：采用 `<ns>:<id>[:<sub>[:<subId>]]`，namespace/sub-key 使用中央注册表；区服 Redis 已隔离时不重复携带 zone/environment 前缀；TTL 仅用于过期/失效，不作为关键业务定时任务唯一触发器。正式规范见 `project/REDIS_KEY_PROPOSAL.md`（Status: ACCEPTED）。
- 普通业务包不重复携带连接级 Session/Auth/Protocol Version；不在线上传 route string。

## 2026-09-14 — Global / Zone 数据边界

已确认：
- 建立独立 Global Service / Global DB，承载平台账号/身份映射、支付订单与支付幂等、区服目录/元数据，以及后续真正跨区的聚合数据。
- 角色、背包、任务、装备、区内活动、区内战斗与区内排行等玩法数据归所属 Zone。
- Zone 不通过跨库 Join 直接读取 Global DB，而通过明确 Global Service Contract 访问。
- 单区迁移、扩容、合服/拆服设计时，Global 账号与支付事实不随 Zone DB 一起迁移。

## 尚未决定 / 延后到 Server 与 Client 校准后继续
- 登录、鉴权、Session、重复登录与断线恢复的具体流程。
- MySQL ORM / SQL 工具、迁移工具与 Redis 客户端。
- MySQL / Redis 的具体一致性写入策略和在线态刷盘规则。
- 可靠定时任务的具体实现框架。
- 实时战斗的具体同步模型。
- 性能预算、最低设备和目标并发。
- CI/CD、日志、监控、自动化测试的具体工具。
