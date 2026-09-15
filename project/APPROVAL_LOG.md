# User Approval Log

由 Producer / Workflow Controller Agent 维护，记录用户对正式 Artifact 版本的批准、退回和废弃决定。

## 记录格式

| Date | Task ID | Role | Artifact | Version | Decision | Notes |
|---|---|---|---|---|---|---|

当前暂无审批记录。

## 规则
- `Decision` 只能是 `APPROVED / REJECTED / SUPERSEDED`。
- 必须记录具体 Artifact 路径和版本，禁止只写“产品通过了”。
- 用户批准新版本后，旧的已批准版本如不再有效，应追加 `SUPERSEDED` 记录，而不是覆盖历史。
- 下游引用应能够追溯到这里的已批准版本。
