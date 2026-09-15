# Shared Game Configuration Standard

公共配置用于把产品/关卡/角色/地图等可数据驱动内容从业务代码中分离。Tech Lead 负责表结构与工程规范，Product 负责玩法语义和数值内容，Client/Server 共同消费经过校验和生成的运行时数据。

## Source of Truth

人工维护源文件默认放在：

`project/config/source/`

格式优先使用 `.xlsx`。不要同时手工维护多份 JSON/TS/CSV 作为同一配置的事实源；运行时格式由后续生成工具从 xlsx 产生。

## 初始配置类别

按需求创建，不要求空表提前全部生成：
- `character_level.xlsx`：人物/角色等级 -> 属性、经验、解锁等。
- `stage.xlsx`：关卡 ID、开放条件、敌人/目标、奖励引用、地图引用等。
- `map.xlsx`：地图 ID、场景/地图资源引用、出生点/区域/关卡关联等。
- `item.xlsx`：道具/装备基础定义。
- `reward.xlsx`：奖励组/奖励项定义。
- 其他出现真实跨模块复用需求的公共配置。

## 每张配置表必须定义

- 唯一主键/ID。
- 字段名称、稳定缩写（如需要）、数据类型。
- 字段业务含义。
- 默认值/空值规则。
- 枚举/范围/唯一性约束。
- 外键/引用关系及被引用表。
- Client-only / Server-only / Shared 可见范围。
- 是否允许热更新（若项目后续启用）。
- 兼容/废弃策略。

数据字典默认放在：

`project/config/schema/<config-name>.md`

## Ownership

### Tech Lead
- 设计表结构、ID 规则、数据类型、引用关系、校验规则、生成/加载边界。
- Review 公共配置是否重复、臃肿或存在不合理耦合。
- 不自行决定产品平衡数值。

### Product
- 提供并维护玩法含义、数值目标、平衡内容和业务约束。
- 不随意新增技术字段或破坏既有引用关系；需要结构变化时与 Tech Lead 评审。

### Client / Server
- 不手工复制公共配置成各自私有版本。
- 通过统一生成/加载层消费配置。
- 若发现运行时性能或安全要求需要拆分 Client/Server 数据，应提出 Tech Review。

## Validation

正式配置进入实现/构建前至少检查：
- ID 唯一。
- 引用目标存在。
- 类型/范围正确。
- 必填项不为空。
- 已废弃字段/ID 未被误用。
- Client 不得到不应下发的服务端敏感字段。

具体 xlsx 模板、生成器和运行时二进制/数据格式等到 Client/Server 基础框架任务中再锁定。
