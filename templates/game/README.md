# Standard Mini Game Project Template

这是 `YuanQuan/ai-studio-template` 的标准小游戏项目骨架。以后创建新的小游戏项目时，Master 默认以本目录作为 Project Layer 初始模板，并同时带入模板仓库当时最新的 Studio Layer（`AGENTS.md`、`agents/`、`rules/`、`schemas/`、`governance/`）。

## 默认定位
- 项目类型：小游戏（Mini Game）。
- 开发/调试：本地 Web。
- 发布目标：微信小游戏、抖音小游戏。
- Client：Cocos Creator + TypeScript。
- Server：Node.js + NestJS + TypeScript，模块化单体。
- Network：标准 WebSocket；Server 使用 `ws`；生产 `wss`。
- Protocol：Protobuf。
- Storage：MySQL + Redis。
- Repository：Monorepo。
- Workflow：Master 编排 + Producer 流程控制 + 逐阶段用户审批。

这些是新小游戏的默认技术基线，不代表不可修改。若具体游戏需要改变平台、技术栈、数据层、网络、测试范围或部署模型，必须在该游戏仓库的 `project/DECISIONS.md` 中形成项目级决策，不回写为模板事实，除非用户明确要求升级 Studio Template。

## 新项目初始化
1. 复制最新 Studio Layer。
2. 展开本目录到新 Game Repository 根目录。
3. 将 `.studio-lock.json.example` 复制为 `.studio-lock.json`，写入实际模板 commit。
4. 填写 `STUDIO.md` 的游戏名称和仓库地址。
5. 保持 `project/DECISIONS.md`、任务状态、审批历史、Milestone 与 Dashboard 为新项目空状态。
6. 从 Product 总纲 / 模块树开始第一条正式任务线。

## 不允许继承的内容
新游戏不得继承其他游戏的：PRD、数值、美术风格、实际素材、业务协议消息、任务历史、审批历史、Bug、玩家数据、密钥或具体项目 Decision。

## 日常维护与 Web 验证默认流程

- 模型仅选择 `gpt-6.1-sol` 与 `gpt-6-luna`，不使用 `gpt-6-astra`。主对话、浏览器效果验证、复杂代码与疑难分析首选 `gpt-6.1-sol`；明确范围的检索、日志整理和简单文档维护可用 `gpt-6-luna`。按已授权委派范围选择子 Agent 模型；主对话由用户在界面切换。长期调度规则见 Studio Layer 的 `agents/master/DECISIONS.md`。
- 首选代码维护 → Creator CLI 构建 → 构建日志与实际输出核验 → 本地 HTTP 服务 → Agent 在内置浏览器直接截图和交互验证；按获批条件测试多个模拟手机视口。无需每次打开编辑器或请用户发送截图。
- 安装路径、执行权限、构建输出与日志目录在新项目中实际确认，不能继承其他项目的机器路径或提权结论。父进程返回不代表构建完成；file:// 不替代 HTTP 运行验证；安全策略拒绝时按正规权限流程处理。
- 实验产物与正式资源分开，按用户指令清理实验目录、临时服务与证据；不自动清理共享缓存或正式资源。上述默认值不替代 Artifact 用户审批、正式 QA 或性能验收。
