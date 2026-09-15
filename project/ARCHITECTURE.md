# Architecture

## Current State

当前项目的第一版正式技术基线：
- 目标平台：本地 Web、微信小游戏、抖音小游戏。
- 客户端：Cocos Creator + TypeScript。
- 服务端：Node.js + NestJS + TypeScript；采用模块化单体。
- 仓库：Monorepo。
- 网络：标准 WebSocket；服务端基于 `ws`；生产环境使用安全传输 `wss`。
- 服务端：模块化单体，当前不采用微服务。
- 权威模型：关键业务数据与可信结果由服务端权威判定。

## Architecture Principles

- 优先简单、可测试、可替换的模块边界，避免为未来假设提前复杂化。
- 基础框架同时支持普通请求/响应型玩法和实时消息型玩法，但不强迫所有业务使用高频实时模型。
- 客户端表现与服务端权威状态通过明确 Protocol / Contract 协作。
- 平台差异通过 Platform Adapter / SDK 隔离，核心业务不得直接依赖微信或抖音平台 API。
- 新增大型依赖、SDK、数据库、基础设施或协议必须形成技术决策。
- 技术实现不得静默改变产品语义。
- 重复组件、公共方法、公共配置、协议定义和基础能力应在真实复用需求出现后抽离到合理共享层，避免重复实现，也避免过度抽象。

## Monorepo Direction

建议目标结构：

```text
game/
├── apps/
│   ├── client/          # Cocos Creator 客户端
│   ├── server/          # TypeScript 模块化单体服务端
│   └── tools/           # 开发与调试工具
├── packages/
│   ├── protocol/        # 前后端共享协议定义
│   ├── shared/          # 无平台依赖的纯 TypeScript 公共能力
│   ├── client-core/     # 客户端基础框架
│   ├── server-core/     # 服务端基础框架
│   └── platform-sdk/    # Web / WeChat / Douyin 平台适配
└── tooling/             # 构建、生成、校验、测试工具
```

具体目录可由后续实现任务微调，但必须保持模块边界清晰，禁止把所有复用代码堆入巨型 `common` / `utils`。

## Client

客户端负责：
- 玩法表现、UI、输入、动画、VFX/音频触发。
- 平台适配、网络接入、资源加载、生命周期、设备适配和客户端性能。
- 本地开发 Web 环境必须尽可能完整复现业务功能。

平台能力统一通过抽象层访问：

```text
Game Logic
   ↓
Platform SDK
   ├── WebAdapter
   ├── WeChatAdapter
   └── DouyinAdapter
```

登录、分享、广告、支付、震动、生命周期等平台能力不得散落为 `wx.*` / `tt.*` 直接调用。

## Server

服务端采用 Node.js + NestJS + TypeScript 的模块化单体：
- 使用 NestJS Module 作为主要业务模块边界。
- 标准 WebSocket 传输层使用 `ws`，不以 Socket.IO 作为默认协议层。
- 各业务领域独立模块化，共享基础设施与业务模块分离。
- 当前不建立微服务拆分作为默认开发模式。
- 后续只有在真实扩展、部署、可靠性或团队边界需要时才评估拆分。
- NestJS 负责模块组织、依赖注入、生命周期和基础工程能力；游戏协议、Session、路由、重连、幂等、实时玩法等保持为清晰可替换的游戏基础层，避免与业务模块强耦合。

### Global / Zone topology

采用“Global 层 + 独立 Zone 层”的数据与服务边界。

Global 层负责跨区、平台级且不应随单区迁移的数据，默认包括：
- 平台账号与身份映射，例如微信/抖音平台身份到内部账号的映射。
- 支付订单、支付幂等与跨区不可重复处理的交易事实。
- 区服列表、区服元数据与可用状态等全局目录信息。
- 跨服玩法/跨服榜等真正跨区的数据聚合（具体玩法出现后再设计）。
- 运营后台需要的全局级数据与配置（具体范围后续确认）。

Global 数据使用独立 Global DB / Global 数据源，不归属于任一游戏区。Zone 业务不得通过跨库 Join 直接依赖 Global DB；需要 Global 数据时通过明确的 Global Service Contract 访问，避免单区数据库迁移牵动账号、支付和跨区数据。

Zone 层采用“一个游戏服务区对应一个游戏进程”的单点式部署：
- 每个区服进程只处理本区玩家与本区玩法状态。
- 角色、背包、任务、装备、区内邮件、区内活动进度、区内战斗/排行等默认属于 Zone 数据。
- 每个区服通过独立的数据源配置绑定自己的 MySQL Database/Schema 与 Redis 数据源，禁止业务代码硬编码具体数据库地址。
- 架构必须允许某个区服的数据源独立迁移到新的 MySQL / Redis 物理实例，而不要求其他区服同时迁移。
- 区服间默认不进行数据库直连或跨库 Join；跨服需求通过 Global/聚合层显式实现。
- 单进程模式必须监控 event-loop lag、CPU、内存和连接数；未来若实时战斗出现明显 CPU 重负载，再评估 Worker Thread / 独立实时计算单元，不提前拆微服务。

边界原则：
- Global 事实不复制成某个 Zone 的唯一事实源。
- Zone 玩法数据不默认汇总到 Global DB；只有明确跨区/运营需求的数据才形成受控同步或聚合。
- 单区迁移、合服、拆服时，账号与支付等 Global 数据不应随 Zone DB 一起搬迁；Zone 数据迁移方案另行处理。

服务端是关键业务状态的权威来源，包括但不限于：
- 资产、货币、奖励。
- 排行和共享状态。
- 需要可信校验的任务、活动和战斗结果。
- 客户端不可通过自报最终结果改变权威状态。

## Network

传输层采用标准 WebSocket：
- 本地开发：允许 `ws`。
- 生产：使用 `wss`。
- 业务层不得直接依赖某个平台 Socket API，应通过统一 Transport Adapter。

网络框架至少支持：
- Connect / Disconnect / Reconnect。
- Heartbeat。
- Request / Response。
- Server Push。
- 实时消息流。
- Session / Auth / Resume。
- Timeout / Retry / Idempotency。
- Sequence / Replay Protection（按需要）。
- Rate Limit / Message Validation。

消息编码采用 Protobuf。协议定义集中维护在 `packages/protocol/`，由前后端共享生成的 TypeScript 类型与编解码代码。禁止自行发明另一套并行消息结构或私有二进制格式。

## Realtime Support

基础框架必须允许未来接入实时战斗，但当前只建立必要的传输和模块边界，不预设所有玩法使用同一种同步模型。

实时玩法后续按具体需求选择：
- 状态同步。
- 帧同步。
- 其他混合模型。

## UI / VFX / Assets

UI、VFX、美术资源应与业务逻辑分离并独立组织。公共组件、公共 VFX、公共 UI 资源与具体业务资源应有清晰目录和依赖方向。

## Development Showcase / Labs

项目早期即建设独立调试/展示入口，至少规划：
- UI Lab。
- Component Lab。
- VFX Lab。
- Animation Lab。
- Network Lab。
- Platform API Lab。

这些调试页面首先应能在本地 Web 完整运行，并保持可构建到微信小游戏和抖音小游戏，以便独立验证组件、特效、UI 和平台能力。

## Code Review & Refactoring Governance

Tech Lead 对 Client / Server 持续进行代码健康 Review：
- 检查职责过大的模块、Manager、工具类和配置集合。
- 检查重复组件、重复方法、重复配置、重复协议和基础能力。
- 检查循环依赖、跨层访问和不清晰的 package 边界。
- 发现真实复用需求时，要求对应 Agent 抽离或重构到合理共享层。
- 不为单次使用提前抽象，不创建无边界的 `shared/common/utils` 大包。

## Data / Storage

当前正式数据基础设施：
- MySQL：主持久化数据库，是玩家长期业务数据的权威持久化来源。
- Redis：在线用户高速缓存与短期状态层，可用于 Session、限流、在线态、短期状态、排行榜/计数等；不作为关键业务唯一持久化事实源。

MySQL 建模原则：
- 为便于统计、检索、索引和迁移，业务数据优先采用明确字段、规范化子表和关系结构，不把可查询的核心用户数据整体塞入 JSON 字段。
- “不使用 JSON”不等于把所有数据堆在一张 `user` 宽表；一对多、可增长集合（如背包、任务、邮件、装备）应优先拆分为独立业务表。
- JSON 仅允许用于确实不需要关系查询、结构极不稳定或纯快照/调试类数据，并需要 Tech Lead Review。

Redis / MySQL 一致性原则：
- MySQL 保持权威；Redis 是缓存/在线运行层。
- 关键经济、资产、付费、奖励等不可只修改 Redis 后异步“碰运气”落库，必须设计可恢复的一致性写入策略。
- 对可重建缓存优先采用 Cache-Aside / 写库后更新或失效缓存的策略；高频运行态是否允许延迟刷盘按具体模块单独评审。

定时任务原则：
- Redis TTL 适合表达“过期/失效”，但不作为业务关键定时任务的唯一可靠触发器。
- 需要准时且可恢复执行的定时任务，应保存明确 `executeAt`/状态，并使用 Redis Sorted Set、数据库任务表或等价可恢复调度机制；TTL/keyspace notification 可作为辅助提示而不是唯一事实源。

具体 ORM / SQL 工具、连接池、迁移工具、Redis 客户端仍待 Server Agent 与 Tech Lead 后续确认。

## Performance Budget

最低设备、FPS、内存、包体、启动时间、网络延迟和服务器并发目标待后续确定。
