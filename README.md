# Virtual Mobile Game Studio

这是一个由主控 Agent 协调多个专业 Agent 的虚拟手游开发工作室骨架，并作为多款游戏共享的 Studio Template 来源。

## Repository Model

- `YuanQuan/ai-studio-template`：只维护可跨游戏复用的角色职责、工作流程、治理规则、Schema 与空白项目模板。
- 每款具体游戏使用独立 GitHub Repository，保存该游戏的 PRD、美术、技术选型、代码、任务、审批、测试和 Dashboard。
- 本仓库内置 `templates/game/` 标准小游戏项目模板：本地 Web 开发/调试，微信小游戏 + 抖音小游戏发布，默认 Cocos Creator + TypeScript / NestJS + TypeScript / ws / Protobuf / MySQL + Redis / Monorepo。
- 创建新小游戏时带入模板仓库最新 Studio Layer，并展开 `templates/game/` Project Layer，通过 `.studio-lock.json` 锁定模板 commit。
- 已有游戏不会自动跟随模板更新；需要同步时先做差异/影响 Review，再由用户批准。
- 仓库边界与同步规则见 `governance/REPOSITORY_SYNC_POLICY.md` 与 `TEMPLATE_MANIFEST.yaml`。

## 目标

- 用户只与 Master Agent 沟通。
- Master Agent 负责理解需求、拆解任务、建立依赖、分派专业 Agent、组织评审与 QA。
- 专业 Agent 通过结构化任务和文件交付协作，不依赖聊天记忆传递关键事实。
- 项目事实统一沉淀在 `project/`，任务状态统一沉淀在 `tasks/`，交付物统一沉淀在 `deliverables/`。

## 模板仓库核心目录

- `AGENTS.md`：整个工作室最高级协作规则。
- `agents/`：每个专业 Agent 的岗位契约。
- `rules/`：任务流、评审、升级和完成定义。
- `schemas/`：结构化任务、交付、请求、评审格式。
- `governance/`：能力与仓库同步治理。
- `templates/game/`：标准小游戏 Project Layer 模板。
- `docs/`：新项目创建和组织说明。

新小游戏仓库展开 `templates/game/` 后，才会在游戏仓库根目录拥有 `STUDIO.md`、`project/`、`tasks/`、`deliverables/`、`client/`、`server/`、`tests/` 等 Project Layer。

## 建议入口

组织/职责/流程修改从本模板仓库治理；创建新小游戏时由 Master 读取最新 Studio Layer 和 `templates/game/PROJECT_TEMPLATE_MANIFEST.yaml`，初始化独立 Game Repository 后再开始 Product 总纲与正式任务流。
