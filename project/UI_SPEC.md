# UI Specification

## Status

当前没有已锁定页面或交互流程。

## Screen Contract

每个正式 Screen 至少定义：
- Screen ID / Name。
- Entry / Exit。
- Layout regions。
- Components。
- Displayed data。
- User actions / emitted events。
- Navigation。
- Normal / Pressed / Disabled / Loading / Empty / Error 等适用状态。
- Asset requirements。
- Client integration notes。

## Interaction Rules

- UI 不自行改变 Product 规则。
- Loading、Empty、Error 等非理想状态必须按功能风险定义。
- 未接入真实设计工具时，UI Agent 的文本/结构输出属于 UI Spec，不等同于最终可用视觉资产。
