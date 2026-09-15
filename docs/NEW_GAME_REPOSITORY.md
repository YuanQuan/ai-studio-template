# New Game Repository Bootstrap

每款游戏使用独立 GitHub Repository。创建时从 `YuanQuan/ai-studio-template` 获取最新 Studio Layer，但之后默认锁定版本，不自动跟随模板变化。

## 创建流程

1. 读取模板仓库 `main` 最新 commit SHA。
2. 创建新的 Game Repository，例如 `YuanQuan/game-foo`。
3. 复制 Studio Layer：
   - `AGENTS.md`
   - `agents/`
   - `rules/`
   - `schemas/`
   - `governance/`
4. 将 `templates/game/` 的内容展开到游戏仓库根目录。
5. 创建 `.studio-lock.json`：

```json
{
  "source": "YuanQuan/ai-studio-template",
  "commit": "<sha>",
  "synced_at": "<ISO-8601>",
  "mode": "pinned"
}
```

6. 初始化该游戏自己的 `project/`、`tasks/`、`deliverables/`、`client/`、`server/`、`tests/`。
7. 第一次 commit 后，后续所有产品、美术、技术、代码、QA 和进度内容只提交到该 Game Repository。

## 已有游戏升级 Studio Layer

用户明确要求“同步最新 Studio 配置”时：

1. 读取 `.studio-lock.json` 的旧 commit。
2. 对比模板最新 commit。
3. 仅检查 `TEMPLATE_MANIFEST.yaml` 中 `studio_layer.syncable` 路径。
4. 生成变更摘要，特别检查：
   - 角色职责变化；
   - Artifact 要求变化；
   - Workflow / Approval Gate 变化；
   - Schema 破坏性变化；
   - 对进行中任务的影响。
5. 用户批准后才应用。
6. 更新 `.studio-lock.json`。

## 为什么不使用 Git Submodule

本项目默认不使用 Git submodule 作为 Studio Layer：
- 游戏仓库应独立可读、可运行、可审计；
- 模板更新不能静默改变进行中的游戏；
- 职责/流程/Schema 升级必须有明确 Review；
- 克隆游戏仓库后不应额外依赖另一个仓库才能理解组织规则。

因此采用“复制快照 + commit 锁定 + 显式同步”的方式。
