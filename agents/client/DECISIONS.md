# Client Agent — Decisions

记录你与 Client Agent 已确认的长期客户端能力与工作方式。

建议后续逐项确认：
- 引擎与语言。
- UI 技术。
- 网络与序列化。
- 异步/任务模型。
- 资源与热更新。
- 架构模式。
- 允许/禁止的第三方库。
- 测试策略。
- 性能预算与支持设备范围。

## 当前项目已确定的外部技术约束

以下是项目级决定，不代表 Client Agent 已完成自身能力校准：
- 客户端使用 Cocos Creator + TypeScript。
- 目标运行平台：本地 Web、微信小游戏、抖音小游戏。
- 本地 Web 必须尽可能完整复现业务功能与调试效果。
- 平台差异通过 Web / WeChat / Douyin Adapter 隔离，业务代码不直接散落调用平台 API。
- 前后端使用标准 WebSocket；生产环境使用 `wss`。
- 网络消息使用 Protobuf，Contract 来自 Monorepo 的 `packages/protocol/`。
- 服务端是关键业务状态与可信结果的权威来源。
- UI、VFX、美术资源与业务代码独立组织；项目早期建设可跨平台的 UI / Component / VFX / Network / Platform Lab。

## 2026-09-14 — 第一轮 Client 工作方式校准

已确认：
- Client 与 Server 一样，优先简洁、直接、可维护的实现，重点降低持续迭代后的返工成本。
- 非琐碎需求编码前先形成轻量 Feature Brief，并与 Tech Lead 沟通模块边界、复用点、协议/平台影响、主要风险和性能影响。
- 实现前主动检查现有 Component / Service / Manager / Utility / Config / Prefab / UI / VFX 是否可复用，避免重复实现。
- 逻辑、UI、VFX、资源加载和持续运行逻辑必须主动评估客户端性能，重点关注 Update/Timer、GC、节点/DrawCall、Atlas、资源生命周期、粒子/Shader/Overdraw 和低端机降级。
- 日志描述默认尽量使用中文；模块标签、错误码、Protobuf/message id、字段名等稳定机器标识保持可检索和跨端一致。
- 在质量与性能可靠的前提下关注模型和 Token 成本：确定性工作优先较快/低成本模型和最小必要上下文，高风险架构/性能/兼容问题再升级推理能力。
- Token 数量是效率指标，但不能以牺牲正确性、性能、可维护性、测试和减少返工为代价。

当前已完成第一轮工作方式校准，但尚未完成完整 Client 能力校准。UI 技术、异步方案、资源/热更新、客户端架构模式、第三方库、具体性能预算、支持设备范围和测试策略仍待继续确认。
