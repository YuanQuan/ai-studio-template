# API / Protocol Specification

## Status

当前已锁定网络与编码基础：
- Transport：标准 WebSocket。
- Development：允许 `ws`。
- Production：必须使用 `wss`。
- Server WebSocket implementation：`ws`。
- Message encoding：Protobuf。
- Protocol source of truth：`packages/protocol/`。

具体业务消息尚未定义。

## Protocol Rules

所有正式网络消息都必须来自统一 Protocol Contract，不允许 Client / Server 私自维护同名不同义的数据结构。

每个正式消息至少定义：
- Message / command type。
- Direction：Client -> Server、Server -> Client 或双向。
- Authentication / Authorization requirement。
- Request / payload fields：字段编号、类型、必填语义和业务含义。
- Response / push fields：字段编号、类型和业务含义。
- Error semantics。
- Timeout / retry semantics（如适用）。
- Idempotency / duplicate request behavior（如适用）。
- Versioning / compatibility requirement。

## Protobuf Rules

- `.proto` 文件是网络字段结构的正式事实源。
- 字段源代码名称不会作为字符串进入 Protobuf 二进制线上包；线上包体优化优先依赖字段编号、整数范围、默认值省略和减少重复元数据。缩写仅按 `project/PROTOCOL_REGISTRY.md` 统一管理，禁止各模块自行创造冲突缩写。
- 前后端共享由 `.proto` 生成的 TypeScript 类型和编解码代码。
- 已发布字段编号不得复用给不同语义。
- 删除字段时应保留/标记对应编号，避免未来误用。
- 协议升级优先采用向后兼容的新增字段；破坏性修改必须经过 Change Review。
- 禁止为了局部方便绕过 Protobuf 再引入第二套业务消息编码。

## Message Patterns

基础框架必须支持：
- Request / Response。
- Server Push。
- Realtime stream / high-frequency messages。
- Heartbeat。
- Session authentication / resume。

统一 Envelope 已锁定，详见 `project/PROTOCOL_REGISTRY.md`。当前最小包头字段为：`mt`、`mi`、`ri`、`sq`、`ec`、`p`；未使用/default 字段由 Protobuf 正常省略。Session/Auth/Protocol Version 默认绑定或协商在连接级，不在每个普通业务包重复传输。消息类型使用数值 `mi` 映射，不在线上传 route string。

## Security / Reliability

- 生产环境只允许 `wss`。
- 客户端数据不作为权威事实；服务端必须执行权限、范围和业务合法性校验。
- 关键操作需要按业务风险设计幂等、防重放、限流和超时处理。
- 断线重连与 Session Resume 必须由统一网络层处理，不允许各业务模块自行实现互不兼容的重连逻辑。

## Lock Rule

当 Client 与 Server 已基于某个 Contract 并行实现时，该 Contract 视为锁定。任何破坏性调整必须提交 `CHANGE` Request，由 Master 协调 Client / Server / Tech Lead 评估影响并同步更新。

## Messages

暂无正式业务消息。
