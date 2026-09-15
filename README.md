# Virtual Mobile Game Studio

这是一个由主控 Agent 协调多个专业 Agent 的虚拟手游开发工作室骨架，并作为多款游戏共享的 Studio Template 来源。

## Repository Model

- `YuanQuan/ai-studio-template`：只维护可跨游戏复用的角色职责、工作流程、治理规则、Schema 与空白项目模板。
- 每款具体游戏使用独立 GitHub Repository，保存该游戏的 PRD、美术、技术选型、代码、任务、审批、测试和 Dashboard。
- 新游戏创建时带入模板仓库最新 Studio Layer，并通过 `.studio-lock.json` 锁定模板 commit。
- 已有游戏不会自动跟随模板更新；需要同步时先做差异/影响 Review，再由用户批准。
- 仓库边界与同步规则见 `governance/REPOSITORY_SYNC_POLICY.md` 与 `TEMPLATE_MANIFEST.yaml`。

## 目标

- 用户只与 Master Agent 沟通。
- Master Agent 负责理解需求、拆解任务、建立依赖、分派专业 Agent、组织评审与 QA。
- 专业 Agent 通过结构化任务和文件交付协作，不依赖聊天记忆传递关键事实。
- 项目事实统一沉淀在 `project/`，任务状态统一沉淀在 `tasks/`，交付物统一沉淀在 `deliverables/`。

## 核心目录

- `AGENTS.md`：整个工作室最高级协作规则。
- `STUDIO.md`：当前工作室与项目级配置。
- `agents/`：每个专业 Agent 的岗位契约。
- `rules/`：任务流、评审、升级和完成定义。
- `schemas/`：结构化任务、交付、请求、评审格式。
- `project/`：共享项目知识库。
- `tasks/`：任务队列与状态。
- `deliverables/`：专业 Agent 的正式交付物。
- `client/`、`server/`：未来真实游戏代码目录。
- `tests/`：测试计划与缺陷记录。

## 建议入口

任何新需求先交给 Master Agent。Master Agent 必须先阅读 `AGENTS.md`、`STUDIO.md`、`agents/master.md` 和相关项目事实，再决定是否拆任务、请求澄清、并行执行或进入审批。
