# Test Plan

## Purpose

本文件维护项目级测试策略。单个功能的详细 Test Cases 与执行结果放在 `deliverables/qa/` 或关联 Bug Report 中。

## Default Coverage

当前默认只测试 Web，并按变更范围拆成两条测试线：
- Client/Web：主流程、Acceptance Criteria、交互状态、Loading/Empty/Error、必要边界，以及任务涉及的 UI/VFX 视觉对齐。
- Server：协议/接口、业务规则、状态变化、必要持久化结果、奖励/资产结果与相关边界。

以下内容默认不测试，除非用户或正式 Task 明确要求：
- 微信小游戏 / 抖音小游戏。
- 网络、弱网、断线、重连、超时专项。
- 性能、内存、负载专项。
- 本次变更范围之外的广泛回归测试。

Visual QA 使用固定 Web 环境、截图/关键帧/短录屏、基线叠加/差异发现和 Art/UI/VFX 专业 Review，详细规则见 `agents/qa/VISUAL_QA_GUIDE.md`。

## Severity

- `P0`：功能直接运行阻断。服务端严重数据/经济/安全损坏是否无条件升级 P0，待用户确认。
- `P1`：功能不符合已批准产品需求预期，但功能不会直接阻断。
- `P2`：功能符合需求，字号、大小、颜色、间距、局部对齐等细节存在问题。
- 关键视觉效果缺失或关键布局明显偏离批准稿时 QA 建议按 P1，待用户确认该边界。

## Release Rule

- P0 = 0。
- P1 = 0。
- P2 可以带入下一版本，但必须在 `TEST_REPORT.md` 中明确列出并保留追踪。
