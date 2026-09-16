# New Mini Game Repository Bootstrap

每款小游戏使用独立 GitHub Repository。创建时从 `YuanQuan/ai-studio-template` 获取最新 Studio Layer，并展开标准小游戏 Project Template；之后默认锁定版本，不自动跟随模板变化。

## 默认小游戏模板

模板根目录：`templates/game/`

模板 ID：`standard-mini-game`

默认基线：
- 本地 Web 开发/调试
- 微信小游戏、抖音小游戏发布
- Cocos Creator + TypeScript
- Node.js + NestJS + TypeScript，模块化单体
- 标准 WebSocket；Server 使用 `ws`；生产 `wss`
- Protobuf
- MySQL + Redis
- Monorepo
- Master 编排 + Producer 流程控制 + 逐阶段用户审批

详细目录初始化与默认测试策略见 `templates/game/PROJECT_TEMPLATE_MANIFEST.yaml`。

## 创建流程

1. 读取模板仓库 `main` 最新 commit SHA。
2. 创建新的 Game Repository，例如 `YuanQuan/game-foo`。
3. 复制 Studio Layer：
   - `AGENTS.md`
   - `agents/`
   - `rules/`
   - `schemas/`
   - `governance/`
4. 将 `templates/game/` 的项目内容展开到游戏仓库根目录；模板自身的 `README.md` / `PROJECT_TEMPLATE_MANIFEST.yaml` 可保留为初始化依据或按项目需要归档。
5. 按 `PROJECT_TEMPLATE_MANIFEST.yaml#create_directories` 创建空目录，包括 Research、Art Reference、Config、Tasks、Deliverables、QA、Monorepo apps/packages/tooling。
6. 将 `.studio-lock.json.example` 复制为 `.studio-lock.json` 并写入实际来源：

```json
{
  "source": "YuanQuan/ai-studio-template",
  "template": "standard-mini-game",
  "template_path": "templates/game",
  "commit": "<sha>",
  "synced_at": "<ISO-8601>",
  "mode": "pinned"
}
```

7. 填写 `STUDIO.md` 的项目名称/仓库信息；保持 PRD、Game Design、Project Decisions、任务状态、审批历史、Milestone、Bug 和 Dashboard 为新项目空状态。
8. 第一次 commit 后，后续所有产品、美术、技术、代码、QA 和进度内容只提交到该 Game Repository。
9. 第一条正式工作从 Product 总纲/模块树开始，再进入 Artifact / User Approval 流程。

## 推荐本地目录布局

可以把 Game Repository 克隆到 Studio Template Repository 的子目录中，例如：

```text
ai-studio-template/
└── ai-studio-demo/   # 独立 Git Repository
```

父目录继续用于角色职责、组织流程、Schema、模板等 Studio Layer 工作；子目录只用于该小游戏的 Project Layer 工作。父仓库必须通过本地 Git exclude 忽略游戏子仓库，避免误提交。目录嵌套仅是本地工作区组织方式，不改变两个仓库独立的 `origin`、commit history 和发布边界。

## 默认 QA 范围
标准小游戏模板默认：
- 正式 Client 执行测试只跑 Web；微信/抖音平台实机测试需显式要求。
- 网络/弱网/重连测试需显式要求。
- 性能/内存/负载测试需显式要求。
- 广泛回归需显式要求。
- P0/P1 阻塞流程；P2 可带入下一版本但必须进入 TEST_REPORT。

## 已有游戏升级 Studio Layer

用户明确要求“同步最新 Studio 配置”时：
1. 读取 `.studio-lock.json` 的旧 commit。
2. 对比模板最新 commit。
3. 仅同步 `TEMPLATE_MANIFEST.yaml` 中 `studio_layer.syncable` 路径。
4. 生成变更摘要，特别检查角色职责、Artifact、Workflow/Approval Gate、Schema 和对进行中任务的影响。
5. 用户批准后才应用并更新 `.studio-lock.json`。

Project Layer 不因 Studio 更新被覆盖。小游戏模板自身发生结构升级时，也应作为独立 Project Template Migration 评估，不静默覆盖已有游戏内容。

## Why Snapshot + Lock
默认不使用 Git submodule：游戏仓库应独立可读/运行/审计；模板更新不能静默改变进行中的游戏；职责/流程/Schema 升级必须有明确 Review。因此采用“复制快照 + commit 锁定 + 显式同步”。
