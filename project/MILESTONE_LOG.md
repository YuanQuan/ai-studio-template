# Project Milestone Log

由 Producer / Workflow Controller Agent 维护。记录所有正式任务线在执行过程中的关键节点历史，是 Dashboard 的过程追踪事实源之一。

## 记录格式

| Date/Time | Task ID | Task Line | Role | Milestone | Status | Artifact | Artifact Version | Artifact Path | Review / Approval | Notes |
|---|---|---|---|---|---|---|---|---|---|---|

当前暂无正式任务关键节点。

## 必须记录的关键节点

任务开始执行后，至少在下列事件发生时追加记录：
- `TASK_STARTED`：角色正式开始执行任务。
- `ARTIFACT_DRAFT_READY`：关键产物形成可评审 Draft。
- `PROFESSIONAL_REVIEW`：专业 Review 完成，记录结论。
- `USER_REVIEW`：当前 Artifact 版本进入用户审批。
- `USER_APPROVED` / `USER_REJECTED`：用户批准或退回具体版本。
- `HANDOFF`：已批准 Artifact 正式移交下一角色。
- `BLOCKED` / `UNBLOCKED`：阻塞产生或解除。
- `IMPLEMENTATION_DONE`：Client / Server 实现与 Implementation Report 完成。
- `QA_STARTED`：QA 开始正式验证。
- `QA_RESULT`：QA 输出测试结论和 TEST_REPORT。
- `TASK_DONE`：任务满足 Definition of Done。

## 规则
- 每条关键节点必须记录对应 Task ID、Role、状态和实际仓库路径；存在 Artifact 时不得只写名称不写路径。
- 路径必须指向当前仓库中的真实文件/目录；版本变化时记录新版本，不覆盖旧记录。
- `USER_APPROVED` / `USER_REJECTED` 必须与 `project/APPROVAL_LOG.md` 一致。
- `WORKFLOW_STATUS.md` 保存当前状态；本文件保存历史关键节点；Dashboard 同时展示两者的可视化结果。
- Producer 负责维护，Master 负责确保正式任务没有绕过关键节点登记直接向后流转。
