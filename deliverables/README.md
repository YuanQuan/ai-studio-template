# Deliverables

正式交付物按专业角色归档：
- `producer/`
- `product/`
- `tech_lead/`
- `art/`
- `ui/`
- `vfx/`
- `client/`
- `server/`
- `qa/`

交付物必须能够追溯到 Task ID，并使用 `schemas/deliverable.schema.json` 中定义的信息结构。不要把聊天回复当成唯一交付记录。

默认目录：`deliverables/<role>/<TASK_ID>/...`。

每个角色的强制产物类型、文件名和依赖门禁统一见 `rules/artifact_contract.md`。视觉角色需要实际图稿时必须提交图片文件；程序角色的非琐碎任务必须同时有设计/实现报告；QA 必须有测试用例与测试报告；公共配置表按 `project/config/README.md` 管理。
