# QA Test Plan

## Task / Requirement
- Task ID:
- Approved PRD / Acceptance version:
- Approved Art / UI / VFX artifacts (if applicable):
- 视觉锚点版本及代表视窗/状态（如适用）：
- Tech Design / Client / Server Feature Brief references:

## Default Mini-Game Scope
### Client
- Default execution platform: Web
- WeChat/Douyin platform/device testing: out of scope unless explicitly requested
- Functional areas:
- UI states:
- Visual checkpoints:

### Server
- APIs/messages/business rules:
- Persistence/reward/state changes:

## Out of Scope by Default
Unless explicitly requested by the user/task:
- WeChat/Douyin platform/device execution testing
- network/weak-network/reconnect testing
- 超出已批准功能性能预算的性能/内存/负载专项
- broad regression outside the changed feature

## Test Cases
- `CLIENT_TEST_CASES.md`
- `SERVER_TEST_CASES.md`
- visual evidence where applicable

## Severity / Flow Gate
- P0: functional execution blocking; must block the flow
- P1: behavior does not match approved Product requirements but does not directly prevent execution; must block the flow
- P2: function meets requirements but details such as font size, dimensions, color, spacing/alignment or similar polish have issues; may carry to next version but must be listed in `TEST_REPORT.md`

## Visual QA
Use approved Art/UI/VFX artifacts as the baseline. UI uses fixed Web viewport/DPR/test data/screenshots. VFX uses fixed camera/background/trigger conditions plus key frames/short recordings where applicable. Automated/visual diff helps detect change; final visual acceptance remains based on approved artifacts and professional Art/UI/VFX review.

视觉资源逐项列出已批准锚点，在固定视窗和状态下留图或视频。功能、视觉、适用性能分别报告 `PASS/FAIL/NOT_TESTED`。`FUNCTIONAL` 默认使用已批准的 Web/测试环境；`RELEASE` 覆盖声明的设备与平台。单元任务若提前要求目标机检验，须在获批计划中写明设备、环境与阈值；没有证据不得填 `PASS`。

## Explicit Exclusions / Risks
- 
