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
| 任务 | ETLJob | DataSourceId | Guid | 是 | 关联上游数据源ID |
| 任务 | ETLJob | TargetSinkId | Guid | 是 | 关联下游目标ID |
| 任务 | ETLJob | ScriptId | Guid | 否 | 关联清洗脚本ID（可选） |
| 任务 | ETLJob | FieldMappings | json/array | 否 | 字段映射列表（或引用映射配置ID） |
| 任务 | ETLJob | DSL | json | 是 | 任务执行内容，这里会关联上下游配置，脚本配置，字段映射等 |
| 脚本 | TransformScript | Id | Guid | 是 | 脚本唯一标识 |
| 脚本 | TransformScript | Language | enum(ScriptLanguage) | 是 | 脚本语言：Python/JS/C#/Java |
| 脚本 | TransformScript | Content | string | 是 | 脚本内容 |
| 目标 | TargetSink | Id | Guid | 是 | 下游目标唯一标识 |
| 目标 | TargetSink | Type | enum(TargetType) | 是 | 数据库/API订阅 |
| 目标 | TargetSink | Config | json | 否 | 下游连接配置，可以是API或者数据库连接地址 |
| 执行记录 | JobExecutionLog | Id | Guid | 是 | 执行记录唯一标识 |
| 执行记录 | JobExecutionLog | JobId | Guid | 是 | 关联的ETL任务ID |
| 执行记录 | JobExecutionLog | JobName | string | 是 | 任务名称快照（用于展示） |
| 执行记录 | JobExecutionLog | TriggerType | enum(TriggerType) | 是 | 触发方式：Scheduled(定时)/Manual(手动)/API(接口调用) |
| 执行记录 | JobExecutionLog | Status | enum(ExecutionStatus) | 是 | 执行状态：Pending(待执行)/Running(执行中)/Success(成功)/Failed(失败)/Cancelled(已取消) |
| 执行记录 | JobExecutionLog | StartTime | DateTime | 是 | 开始执行时间 |
| 执行记录 | JobExecutionLog | EndTime | DateTime? | 否 | 结束时间（执行中时为空） |
| 执行记录 | JobExecutionLog | Duration | long | 否 | 执行耗时（毫秒） |
| 执行记录 | JobExecutionLog | CurrentStage | enum(ExecutionStage) | 否 | 当前阶段：Extract(采集)/Transform(转换)/Load(写入)/Complete(完成) |
| 执行记录 | JobExecutionLog | Progress | int | 否 | 执行进度百分比（0-100） |
| 执行记录 | JobExecutionLog | SourceRecordCount | int | 否 | 源数据记录数 |
| 执行记录 | JobExecutionLog | ProcessedCount | int | 否 | 已处理记录数 |
| 执行记录 | JobExecutionLog | SuccessCount | int | 否 | 成功写入记录数 |
| 执行记录 | JobExecutionLog | FailedCount | int | 否 | 失败记录数 |
| 执行记录 | JobExecutionLog | SkippedCount | int | 否 | 跳过记录数（如数据过滤） |
| 执行记录 | JobExecutionLog | ErrorMessage | string | 否 | 错误信息（失败时记录） |
| 执行记录 | JobExecutionLog | ErrorStackTrace | text | 否 | 异常堆栈信息 |
| 执行记录 | JobExecutionLog | ExecutionDetails | json | 否 | 详细执行信息（各阶段耗时、中间结果等） |
| 执行记录 | JobExecutionLog | TriggeredBy | string | 否 | 触发人/系统标识 |
| 执行记录 | JobExecutionLog | ServerHost | string | 否 | 执行服务器地址 |
| 执行记录 | JobExecutionLog | CreatedAt | DateTime | 是 | 记录创建时间 |
| 执行记录 | JobExecutionLog | UpdatedAt | DateTime | 是 | 记录最后更新时间 |
