# Decision Log

所有影响多个任务或长期有效的决定都记录在这里。

## Format

### DEC-YYYYMMDD-NNN — Title
- Date:
- Status: Proposed / Accepted / Superseded
- Context:
- Decision:
- Reason:
- Impact:
- Related tasks:
- Supersedes:

---

### DEC-20260914-001 — 初始化虚拟手游工作室协作模型
- Date: 2026-09-14
- Status: Accepted
- Context: 需要通过 Master Agent 协调产品、技术、美术、UI、VFX、客户端、服务端和 QA。
- Decision: 采用 Master 统一创建正式任务、专业 Agent 通过结构化 Task/Deliverable/Request/Review 与共享文件协作的模式。
- Reason: 减少多 Agent 上下文漂移、越权、重复决策和无限递归派发。
- Impact: 后续所有正式任务遵守 `AGENTS.md` 与 `rules/`。
- Related tasks: Studio bootstrap
- Supersedes: None

### DEC-20260914-002 — 第一版跨平台技术基线
- Date: 2026-09-14
- Status: Accepted
- Context: 项目目标是使用 Cocos Creator 开发可在本地 Web 完整调试并发布到微信小游戏、抖音小游戏的手机游戏，需要统一前后端语言、网络和工程结构，并兼顾普通玩法与未来实时玩法。
- Decision: 客户端采用 Cocos Creator + TypeScript；服务端采用 TypeScript、模块化单体；仓库采用 Monorepo；前后端通过标准 WebSocket 通信，生产使用 `wss`；关键业务状态采用服务端权威；基础框架支持请求/响应与实时消息两类模式，但不提前为实时玩法过度设计；平台能力通过 Web/WeChat/Douyin Adapter 隔离。
- Reason: 统一 TypeScript 技术栈和共享协议降低前后端协作成本；模块化单体控制早期复杂度；统一 WebSocket 和平台适配层满足跨平台、快速、稳定的通信与本地 Web 调试需求。
- Impact: Tech Lead、Client、Server、QA 以及后续所有网络、协议、平台和构建相关任务必须遵守该基线。
- Related tasks: Architecture calibration
- Supersedes: None

### DEC-20260914-003 — Tech Lead 持续代码健康治理
- Date: 2026-09-14
- Status: Accepted
- Context: 前后端长期迭代容易产生重复组件、公共方法/配置散落、Manager 膨胀和模块职责失控。
- Decision: Tech Lead 持续对 Client / Server 进行代码与架构 Review；除功能正确性外，重点检查代码臃肿、职责过大、重复实现、公共能力碎片化、循环依赖和跨层调用。发现真实重复或跨模块复用后，向对应 Agent 提出明确的抽离、复用或重构要求，同时避免为单次使用提前过度抽象。
- Reason: 保持长期可维护性和复用边界，防止日常功能堆叠侵蚀架构。
- Impact: Client / Server 的 Review 阶段必须响应 Tech Lead 的代码健康 Review Action。
- Related tasks: Ongoing architecture review
- Supersedes: None

### DEC-20260914-004 — 数据基础设施采用 MySQL + Redis
- Date: 2026-09-14
- Status: Accepted
- Context: 服务端需要同时满足可靠持久化、Session、缓存、限流、排行/计数和短期高频状态访问。
- Decision: 使用 MySQL 作为主持久化数据库；Redis 作为缓存、Session、限流、短期状态、排行榜/计数等高速数据层。Redis 不作为关键业务数据的唯一持久化事实源。
- Reason: MySQL 提供成熟可靠的事务与持久化能力，Redis 提供低延迟内存数据结构；职责分离有利于一致性和性能治理。
- Impact: Server、Tech Lead、QA 的数据访问、恢复、一致性和测试方案必须遵守该职责边界。
- Related tasks: Server architecture calibration
- Supersedes: None

### DEC-20260914-005 — 服务端框架与网络协议栈
- Date: 2026-09-14
- Status: Accepted
- Context: 已确定 TypeScript 全栈、模块化单体、标准 WebSocket 和普通/实时玩法兼容，需要锁定服务端框架与消息编码。
- Decision: 服务端采用 Node.js + NestJS + TypeScript 的模块化单体；WebSocket 服务端实现采用标准 `ws`，生产通过 `wss`；消息编码采用 Protobuf；`.proto` 与生成代码统一维护在 Monorepo 的 `packages/protocol/`。
- Reason: NestJS 的模块化与依赖注入适合长期维护的模块化单体；`ws` 保持标准 WebSocket 与小游戏跨平台兼容；Protobuf 提供紧凑二进制编码、明确 Schema 和成熟的兼容演进规则。
- Impact: Client、Server、Tech Lead、QA 以及所有协议/网络相关实现必须遵循统一 Protobuf Contract；不得私建另一套业务消息编码。
- Related tasks: Network framework, Protocol package, Server foundation
- Supersedes: None

### DEC-20260914-006 — 区服拓扑、数据建模与在线缓存原则
- Date: 2026-09-14
- Status: Accepted
- Context: 项目计划按游戏服务区独立运行，需要便于未来按区迁移数据，并希望 MySQL 便于统计、Redis 承载在线用户缓存与短期状态。
- Decision: 每个游戏服务区默认对应一个游戏进程；每个区通过独立数据源配置绑定自己的 MySQL Database/Schema 与 Redis 数据源；MySQL 是玩家长期业务数据的权威持久化来源，核心可查询数据优先使用明确字段和规范化业务表，不把核心用户数据整体放入 JSON 字段；Redis 用作在线用户缓存、Session、短期状态、限流、排行/计数等高速层；区服数据源必须允许单区独立迁移而不影响其他区。
- Reason: 通过区服级数据隔离降低迁移和故障影响范围，保持统计/索引友好性，同时利用 Redis 降低在线访问延迟。
- Impact: Server 的数据层、Repository、Redis 封装、部署配置和迁移工具必须按区服数据源隔离设计；禁止业务代码硬编码具体 DB/Redis endpoint，也禁止默认进行跨区数据库 Join。
- Related tasks: Server data layer, Zone deployment, Redis cache layer
- Supersedes: None

### DEC-20260914-007 — Protobuf Envelope 与消息 ID 注册机制
- Date: 2026-09-14
- Status: Accepted
- Context: 需要统一 Request/Response、Server Push、实时流和错误处理，同时尽量控制线上包体并避免字段/消息缩写冲突。
- Decision: 使用统一 Protobuf Envelope，核心字段为 `mt`、`mi`、`ri`、`sq`、`ec`、`p`；具体含义和缩写映射由 `project/PROTOCOL_REGISTRY.md` 统一登记；普通业务包不重复携带已绑定到连接的 Session/Auth/Protocol Version；业务消息使用数值 `mi` 注册表而不是 route string；高频字段使用低 Protobuf field number，高频消息优先分配较小 `mi`。
- Reason: Protobuf 二进制线上传输的是 field tag/value 而不是字段名字符串；统一 Envelope 和数字消息 ID 能在保持可维护性的同时减少重复元数据和路由字符串开销。
- Impact: Client / Server 网络层、协议生成器、Network Lab、日志解码和 QA 协议测试必须使用同一 Registry；任何新增缩写或 message ID 必须中央登记且发布后不得复用语义。
- Related tasks: Protocol package, Network framework, Network Lab
- Supersedes: None

### DEC-20260914-008 — Redis Key 命名规范
- Date: 2026-09-14
- Status: Accepted
- Context: Redis 作为在线用户缓存、Session、限流、排行榜/计数和短期状态层，需要统一、短小、可迁移且避免缩写冲突的 Key 命名规则。
- Decision: 采用 `<ns>:<id>[:<sub>[:<subId>]]` 作为基础格式；固定 namespace 与 user sub-key 必须进入中央注册表；生产环境在区服 Redis 数据源已隔离时不重复写 zone/environment 前缀；缩写冲突时增加 1-2 个有意义字母并登记；TTL 仅用于过期/失效，不作为关键定时任务唯一触发机制。正式规范见 `project/REDIS_KEY_PROPOSAL.md`（Status: ACCEPTED）。
- Reason: 降低 Key 内存开销、保持命名一致、避免多模块重复发明缩写，并让单区迁移无需修改业务 Key。
- Impact: Server Agent 新增任何 Redis Key 前必须遵守中央注册规范；Tech Lead Review 负责阻止未登记缩写、冗余前缀和职责不清的 Redis Key。
- Related tasks: Redis cache layer, Server data layer
- Supersedes: None

### DEC-20260914-009 — Global / Zone 数据边界
- Date: 2026-09-14
- Status: Accepted
- Context: 各游戏区需要独立迁移、扩容和故障隔离，但账号、平台身份、支付订单、区服目录等数据不应绑定到任一区服数据库。
- Decision: 建立独立 Global Service / Global DB 作为跨区与平台级事实层；平台账号/身份映射、支付订单与支付幂等、区服列表/元数据、未来跨服聚合数据和全局运营数据归 Global；角色、背包、任务、装备、区内活动/战斗/排行等归对应 Zone。Zone 不通过跨库 Join 直接访问 Global DB，而通过明确 Global Service Contract 访问。单区迁移时 Global 数据不随 Zone DB 搬迁。
- Reason: 将账号/支付等全局事实与可独立迁移的区服玩法数据解耦，降低迁服、合服、扩容与故障影响范围。
- Impact: Server 数据模型、部署、账号/支付、区服目录和未来跨服功能必须遵守 Global / Zone 边界；不得把全局事实偷偷落成某个 Zone 的唯一数据源。
- Related tasks: Global service, Account, Payment, Zone routing, Data migration
- Supersedes: None

### DEC-20260915-010 — 固定产物驱动的多 Agent 协作
- Date: 2026-09-15
- Status: Accepted
- Context: 多 Agent 如果主要依赖聊天传递结果，容易出现上下文漂移、下游输入不完整、责任不清和验收不可追溯。
- Decision: 所有正式跨 Agent 协作改为固定 Artifact 驱动，统一遵守 `rules/artifact_contract.md`。Product 提交 PRD/验收；Art 按角色/场景/道具/图标等类别提交设计图和资产清单；UI 提交 UI Spec、配色方案、页面/组件效果图；VFX 提交轻量特效规格；Client/Server 在非琐碎实现前提交 Feature Brief、完成后提交 Implementation Report；QA 提交测试用例、测试报告和缺陷记录；Tech Lead 提交技术设计/Review，并对公共数据驱动需求设计 `.xlsx` 配置表结构和数据字典。公共配置由 Tech Lead 负责结构/类型/引用/校验，Product 负责玩法语义和数值内容。
- Reason: 让每个下游任务基于稳定、可版本控制、可验收的正式输入工作，降低沟通失真和后期返工。
- Impact: Master 创建任务时必须声明 Required Artifacts；缺少必要上游 Artifact 的下游任务不得进入 READY；Review 和 Definition of Done 必须检查完整 Artifact 链。
- Related tasks: All future studio tasks, Shared config system
- Supersedes: None

### DEC-20260915-011 — Producer 流程控制与逐环节用户审批
- Date: 2026-09-15
- Status: Accepted
- Context: 后续功能迭代需要严格控制各部门产物流转，Product/Art/UI 等可能经历多轮沟通，且用户希望逐环节确认并能随时查询全部任务线状态。
- Decision: 新增 Producer / Workflow Controller Agent，专责全局任务线状态、依赖、Artifact 版本链、审批门禁和进度看板；Master 继续负责用户沟通、目标理解和正式任务编排。所有正式功能迭代默认采用逐环节用户审批：专业产物先完成专业 Review，再进入 `USER_REVIEW`；只有用户明确批准的具体版本标记为 `USER_APPROVED` 后，下游正式任务才能解锁。用户退回进入 Revision，保留旧版本，不覆盖历史。Product/Art/UI 等可多轮 Draft/Revision，只有最终批准版本交付 Tech Lead / Client / Server。Producer 维护 `project/WORKFLOW_STATUS.md` 和 `project/APPROVAL_LOG.md`。
- Reason: 将“内容决策/任务编排”和“流程监督/状态事实源”分离，避免 Master 职责过载；通过版本审批门禁防止未定稿需求或视觉方案提前进入开发，同时支持用户随时获取准确全局进度。
- Impact: Master、Producer、所有专业 Agent、Task Schema、Artifact Contract、Review、QA 和 Definition of Done 均必须遵守用户审批门禁；专业 Review 通过不代表自动进入下游。
- Related tasks: All future feature iterations, Workflow governance
- Supersedes: None

### DEC-20260915-012 — 产品总纲、模块树与详细 PRD 规则
- Date: 2026-09-15
- Status: Accepted
- Context: 零散功能直接进入开发容易导致系统边界不清、跨模块依赖遗漏和后期返工，需要在需求阶段建立清晰的产品结构并尽量前置消化问题。
- Decision: 游戏立项或大型版本需求先建立产品总纲和模块树，再按“大模块 -> 子模块 -> 功能 PRD”逐层细化；每个 PRD 必须标明模块归属、依赖、流程、状态、边界、异常、数值/内容需求和验收条件。存在真实取舍时 Product 给多个方案并比较收益/成本/风险；明显存在最优解时直接推荐最优方案。Product 负责数值设计，初期可先采用简单粗粒度模型，后续逐步细化。
- Reason: 通过详细需求和模块化产品结构尽量在实现前发现冲突、遗漏和依赖，降低 Art/UI/Tech/Client/Server/QA 的返工。
- Impact: Master 拆需求、Producer 检查 Product Artifact、后续所有专业 Agent 消费需求时均以用户批准的模块化 PRD 为正式产品输入。
- Related tasks: All future product and feature work
- Supersedes: None

### DEC-20260915-013 — Product Research 独立资料库与 PRD 引用规则
- Date: 2026-09-15
- Status: Accepted
- Context: Product 后续需要进行同类/竞品对比、版权/IP 初步风险、市场接受度、用户画像和平台趋势等外部研究；这些资料需要长期保留用于讨论，但不能与已批准需求混淆。
- Decision: 建立独立 `project/research/` 资料库，按 comparables / market / users / ip_copyright / platforms / misc 分类保存外部研究。每份研究必须记录研究问题、范围、来源、事实、分析、置信度、限制、产品影响、开放问题和建议。Research 是产品决策依据，不自动成为需求；只有被 PRD / Decision 显式引用采用后才影响下游。已批准 PRD 因研究需要修改时先做影响分析、生成新版本并重新用户审批。版权/IP/合规类文档仅作为初步风险评估，高风险事项必须建议进一步专业确认。
- Reason: 让外部信息可追溯、可复查、可持续讨论，同时保持“研究证据”和“正式需求”之间清晰边界，降低基于过时或未经验证信息直接开发的风险。
- Impact: Product、Master、Producer 在使用外部研究推动需求变更时必须维护 Research -> PRD/Decision 的引用链；下游 Agent 不得直接把 Research 推断当成正式需求。
- Related tasks: Product research, Competitive analysis, Market research, User research, IP/copyright risk assessment
- Supersedes: None

### DEC-20260915-014 — Art Director 视觉治理与 UI/VFX 方向门禁
- Date: 2026-09-15
- Status: Accepted
- Context: 长期迭代中角色、场景、道具、UI 和 VFX 容易分别演进，最终偏离最初主体构思；同时用户希望主要与 Art Agent 反复推敲视觉内容，而不是频繁分别管理 UI/VFX 视觉方向。
- Decision: Art Agent 提升为项目 Art Director / 美术总监，负责主体艺术方向、视觉语言、长期一致性和视觉参考治理。UI Agent 继续负责布局、信息层级、交互和组件状态；VFX Agent 继续负责触发、时序、性能与实现规格，但二者视觉部分必须遵循用户批准的 Art Direction / `project/ART_GUIDE.md`，并在进入用户审批前通过 Art 的视觉一致性 Review。Art 的重大主体风格和重要最终视觉产物必须明确记录设计思路/理由。Art 可收集多来源参考并进行抽象、融合与重新创作，但不得通过临摹、换色、微调或机械拼接受保护表达规避版权风险；参考来源与授权/风险记录进入 `project/art_reference/`，高风险项进入 `project/research/ip_copyright/`。
- Reason: 通过单一 Art Direction Owner 维持长期视觉一致性，降低多视觉角色并行导致的风格漂移，并让用户以 Art 为主要视觉沟通入口，同时保留 UI/VFX 各自专业职责。
- Impact: Art、UI、VFX、Producer、Master、Client 的视觉相关任务和 Review 必须遵守 Art Direction 门禁；未经用户批准的新主体视觉方向不得通过局部 UI/VFX 迭代静默生效。
- Related tasks: All future art/UI/VFX work, Visual review, Asset production
- Supersedes: None

### DEC-20260915-015 — QA 分线测试、Web 默认范围与 P0/P1/P2 门禁
- Date: 2026-09-15
- Status: Accepted
- Context: 功能进入开发后，QA 需要提前明确未来测试工作，并让测试范围、视觉验收和缺陷阻断规则与 Product/Art/UI/VFX/Client/Server 的正式 Artifact 对齐。
- Decision: QA 以用户批准的 PRD/Acceptance 为功能测试首要事实源；正式编码前同步产出 TEST_PLAN，并与相关 Client/Server FEATURE_BRIEF 组成“开发开工包”一起提交用户审批，批准后才进入编码。测试用例按 Client/Web 与 Server 两条线设计。当前默认只测试 Web；微信/抖音、网络/弱网、性能和广泛回归只有用户或 Task 明确要求时才测试。视觉验收使用固定 Web 环境、UI/VFX Lab、截图/关键帧/短录屏、基线差异发现及 Art/UI/VFX 专业 Review。缺陷分为 P0/P1/P2：P0 为功能直接运行阻断，P1 为功能不符合批准需求但不直接阻断，P2 为功能符合但字号/大小/颜色/间距等细节问题；P0/P1 阻塞流程，P2 可带入下一版本但必须在 TEST_REPORT 明确报告。
- Reason: 将 QA 前置到开发开始阶段，保证用例与正式需求同步设计；同时通过清晰测试范围和缺陷门禁，避免把未测试范围误解为已验证，也避免 P2 细节问题无谓阻断整体迭代。
- Impact: Master/Producer 在 Client/Server 正式编码前必须同步组织 FEATURE_BRIEF + QA TEST_PLAN 的用户审批门禁；QA 报告必须区分已测试/未测试和 P0/P1/P2；Definition of Done 按 P0/P1=0、P2 可延期执行。
- Related tasks: All future Client/Server feature development and QA
- Supersedes: Previous generic QA severity/default coverage rules

### DEC-20260915-016 — Producer 静态项目 Dashboard
- Date: 2026-09-15
- Status: Accepted
- Context: 用户希望无需阅读多份任务/审批文件，即可直观查看项目整体流程、全部任务线进度、当前审批点、阻塞、角色固定产物和待处理事项。
- Decision: Producer 维护 `project/dashboard/index.html` 作为零依赖静态可视化 Dashboard，并在正式任务状态、Artifact 版本、用户审批或阻塞变化后同步刷新。Dashboard 展示项目流程、WORKFLOW_STATUS 任务线、APPROVAL_LOG 审批、角色固定产物与当前待确定事项，但只作为正式状态源的可视化快照，不替代 Task / Artifact / Review / WORKFLOW_STATUS / APPROVAL_LOG。
- Reason: 降低用户获取全局进度的成本，同时保持底层事实源可追溯、可审计。
- Impact: Producer 的状态维护工作增加 Dashboard 同步步骤；任何页面显示不得推断用户审批或制造不存在的任务/进度。
- Related tasks: All future workflow status updates
- Supersedes: None

### DEC-20260915-017 — 执行任务关键节点与 Artifact 路径全程可视化
- Date: 2026-09-15
- Status: Accepted
- Context: 用户要求任何部门角色一旦开始执行任务，不能只看到最终完成状态；各关键节点、当前进度和对应产物位置都应在静态 Dashboard 中持续可见。
- Decision: 新增 `project/MILESTONE_LOG.md` 作为正式任务关键节点历史源。任何专业 Owner 进入 `IN_PROGRESS` 后，Producer 必须记录 `TASK_STARTED`，并在 Artifact Draft、专业 Review、用户审批、Handoff、Blocked/Unblocked、实现完成、QA、Done 等关键事件发生时持续追加 Milestone；存在 Artifact 时必须记录版本和真实仓库路径。Producer 同步更新 `WORKFLOW_STATUS.md` 与 `project/dashboard/index.html`。Master 负责确保任务没有绕过该可视化与追踪机制向后流转。
- Reason: 让用户既能看到当前状态，也能追溯每条任务线如何流转、哪个角色在何时产出了什么，以及对应产物在哪里，避免状态与实际文件脱节。
- Impact: Master、Producer 和所有正式任务执行角色均受该规则约束；Dashboard 必须展示当前任务状态和关键节点历史，并能定位到具体 Artifact 路径。
- Related tasks: All future formal tasks and workflow updates
- Supersedes: Extends DEC-20260915-016
