# Studio Configuration

## 项目

- 名称：未命名手游项目
- 类型：Mobile Game
- 当前阶段：Prototype / Pre-production
- 目标平台：本地 Web、微信小游戏、抖音小游戏
- 客户端技术栈：Cocos Creator + TypeScript
- 服务端技术栈：Node.js + NestJS + TypeScript，模块化单体
- 网络：标准 WebSocket，服务端采用 `ws`，生产使用 `wss`
- 消息编码：Protobuf
- 数据层：MySQL + Redis
- 区服：默认 1 个游戏服务区对应 1 个游戏进程，并绑定独立可迁移的 MySQL / Redis 数据源
- 全局层：独立 Global Service / Global DB 管理平台账号身份、支付订单/幂等、区服目录及未来跨服聚合数据；Zone 玩法数据保持区服隔离
- 仓库：Monorepo
- 权威模型：关键业务数据与可信结果由服务端权威判定
- 主控工作模式：`APPROVAL`
- 流程控制：Producer / Workflow Controller Agent 负责全局任务线、Artifact 版本与用户审批门禁

## 工作模式定义

### AUTO
适合 Bug、小型 UI 调整、重构、补测试等非正式功能迭代的低风险任务。正式功能迭代仍必须遵守逐环节 Artifact 用户审批门禁。

### APPROVAL
默认模式。涉及核心玩法、经济、付费、角色方向、架构选型、破坏性 API、明显扩大范围的需求时，Master Agent 必须在关键决策点请求用户批准。

### REVIEW
适合原画、UI 方向、多个产品候选、体验方案等。Agent 可以先产出候选，用户选择后再继续。

## 开发原则

1. 产品规则优先于实现猜测。
2. 已记录决策优先于重复讨论。
3. 客户端与服务端通过 API Contract 协作。
4. 所有正式功能必须有可验证的 Acceptance Criteria。
5. 所有可发布功能必须经过 QA。
6. Agent 之间通过 Task、固定 Artifact、Deliverable、Request、Review 协作；聊天用于讨论，不作为正式上下游输入。
7. Product/Art/UI/VFX/Tech/Client/Server/QA 必须按 `rules/artifact_contract.md` 交付各自固定产物；正式功能迭代中，下游只有在上游当前版本通过专业 Review 且获得用户明确批准后才能解锁。
8. 公共游戏配置表使用 `.xlsx` 人工源表与数据字典，由 Tech Lead 设计结构/校验、Product 维护玩法语义/数值，统一遵守 `project/config/README.md`。
9. Producer 持续维护 `project/WORKFLOW_STATUS.md`、`project/MILESTONE_LOG.md`、`project/APPROVAL_LOG.md` 与 `project/dashboard/index.html`；任何部门角色一旦开始正式执行任务，关键节点、Owner、Artifact 版本与真实仓库路径、审批/阻塞、下一动作都必须同步到 Dashboard，用户可以随时查看当前状态与历史流转。
10. Product / Art / UI 等允许反复 Draft/Revision；只有最终 `USER_APPROVED` 版本可正式交付 Tech Lead 和开发。
11. Art Agent 作为 Art Director 维护主体视觉方向；UI/VFX 的视觉部分必须遵守用户批准的 Art Direction / `project/ART_GUIDE.md`，并在送用户审批前先经过 Art 的视觉一致性 Review。
12. 关键知识必须落到文件，不依赖会话记忆。

## 直接沟通模式

用户可以直接与任一专业 Agent 进行能力培养会话，讨论并确定该 Agent 的职责、技术偏好、禁止项、专注方向、工作方法和项目内 Skill / Playbook。

角色能力配置不等同于项目正式选型：
- `agents/<role>/CAPABILITIES.md` 表示该 Agent 会什么、擅长什么。
- `agents/<role>/CONSTRAINTS.md` 表示该 Agent 在工作中不能做什么或必须遵守什么。
- `project/ARCHITECTURE.md`、本文件和 `project/DECISIONS.md` 才表示当前项目正式采用什么。

专业 Agent 在用户明确批准后可更新自身配置；涉及跨角色契约或项目级决定时，交由 Master 组织同步和落盘。

## 主控默认行为

当用户给出一句自然语言任务时，Master Agent 应：
1. 判断该任务是咨询、设计、开发、Bug、测试还是复合任务。
2. 阅读与任务相关的共享项目事实，并读取相关 Agent 的最新能力配置和 `governance/CAPABILITY_REGISTRY.yaml`。
3. 创建一个父任务并拆成必要的专业子任务，并让 Producer 登记任务线与审批点。
4. 建立依赖，只并行执行无依赖冲突的任务；不能绕过 Producer 的用户审批门禁。
5. 将每个专业阶段通过 Review 的具体 Artifact 版本呈现给用户确认；把用户反馈交还对应 Agent 迭代。
6. 基于 Producer 维护的状态与审批记录组织 Integration / QA。
7. 将关键新事实写回 `project/` 或 `project/DECISIONS.md`。
8. 用简洁报告向用户说明结果、风险、阻塞、待审批项和下一步。
