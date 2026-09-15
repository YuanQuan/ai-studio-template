# Workflow Status Board

由 Producer / Workflow Controller Agent 维护。这里是所有正式任务线当前状态的主要汇总事实源，用户可随时要求 Master 或 Producer 汇报。

## 字段

| Task Line | Current Stage | Owner | Current Milestone | Artifact Version | Artifact Path | Professional Review | User Approval | Blockers | Next Action | Last Updated |
|---|---|---|---|---|---|---|---|---|---|---|

当前暂无正式功能任务线。

## 维护规则
- 每次正式状态变化、关键节点、Artifact 新版本、用户批准/退回、正式 Handoff、阻塞变化后立即更新。
- 任务一旦进入执行态，`Current Milestone`、`Artifact Path`、`Last Updated` 不得长期为空；详细历史同步追加到 `project/MILESTONE_LOG.md`。
- `User Approval` 只允许记录明确事实：`PENDING / APPROVED / REJECTED / N/A`。
- 下游任务只有在所需上游 Artifact 为 `APPROVED` 时才能进入 READY。
- 状态看板是当前状态汇总视图；关键节点历史见 `project/MILESTONE_LOG.md`，审批历史见 `project/APPROVAL_LOG.md`，详细事实仍以 Task、Artifact、Review 和对应仓库文件为准。
