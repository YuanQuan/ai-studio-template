# Task Queue

- `backlog/`：已记录但尚未可执行或尚未开始的任务。
- `active/`：SPEC / READY / IN_PROGRESS / INTEGRATION / QA 等进行中任务。
- `review/`：等待专业评审或 Master 验收的任务。
- `done/`：满足 Definition of Done 的任务。

任务移动目录时仍必须保留 Task Packet 内的 `status` 字段，并保持两者一致。

正式任务由 Master Agent 创建。其他 Agent 只能提交 Agent Request，不能直接向这些目录增加正式子任务。
