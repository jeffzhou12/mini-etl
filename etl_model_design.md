# ETL 模型设计

### Sheet1

| 模块 | 模型 | 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 数据源 | DataSource | Id | Guid | 是 | 数据源唯一标识 |
| 数据源 | DataSource | Name | string | 是 | 数据源名称 |
| 数据源 | DataSource | Type | enum(DataSourceType) | 是 | 数据库/API/文件等类型 |
| 数据源 | DataSource | ConnectionConfig | json | 否 | 连接信息，包含连接串、凭证等 |
| 数据源 | DataSource | ApiConfig | json | 否 | API读取配置（URL、Header、Method等） |
| 数据源 | DataSource | FilePath | string | 否 | 文件路径，可以是一个json或者xml文件上传到服务器或者对象存储端 |
| 映射 | FieldMapping | Id | Guid | 是 | 映射唯一标识 |
| 映射 | FieldMapping | SourceField | string | 是 | 上游字段名 |
| 映射 | FieldMapping | TargetField | string | 是 | 下游字段名 |
| 任务 | ETLJob | Id | Guid | 是 | 任务唯一标识 |
| 任务 | ETLJob | Name | string | 是 | 任务名称 |
| 任务 | ETLJob | Schedule | string(Cron) | 否 | 执行频率（Cron 表达式） |
| 任务 | ETLJob | ManualTrigger | bool | 否 | 是否支持手动触发 |
| 脚本 | TransformScript | Id | Guid | 是 | 脚本唯一标识 |
| 脚本 | TransformScript | Language | enum(ScriptLanguage) | 是 | 脚本语言：Python/JS/C#/Java |
| 脚本 | TransformScript | Content | string | 是 | 脚本内容 |
| 目标 | TargetSink | Id | Guid | 是 | 下游目标唯一标识 |
| 目标 | TargetSink | Type | enum(TargetType) | 是 | 数据库/API订阅 |
| 目标 | TargetSink | Config | json | 否 | 下游连接配置，可以是API或者数据库连接地址 |
