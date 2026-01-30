# ETL 平台设计文档汇总

本文件汇总了模型设计、流程图、关键 API、分层架构、脚本引擎与 DSL 设计。

## 1. 模型设计

### 📊 在线预览（Markdown 版本）

查看 **[etl_model_design.md](./etl_model_design.md)** - 已转换为 Markdown 表格，GitHub 原生支持预览 ✅

<details>
<summary>📋 点击展开模型定义表格</summary>

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

</details>

### 📥 原始文件

- Excel 文件：[etl_model_design.xlsx](./etl_model_design.xlsx)（需下载查看）

## 2. 流程图设计

### 🎨 在线预览

**当前状态**：Drawio 源文件 - [etl_flow.drawio](./etl_flow.drawio)

> 📌 **如何导出为图片以便在 GitHub 中预览：**
> 
> 请查看 **[导出说明](./EXPORT_DRAWIO.md)** 了解如何将 Drawio 文件导出为 PNG/SVG 图片。
> 
> **快速步骤**：
> 1. 安装推荐扩展（见下方）
> 2. 打开 `etl_flow.drawio` 文件
> 3. 导出为 SVG/PNG 格式
> 4. 提交图片文件到仓库

```vscode-extensions
hediet.vscode-drawio
```

### 📝 说明

覆盖触发器、采集、映射、脚本清洗、下游写入与审计日志的完整 ETL 流程。

## 3. 关键 API 设计

### 🌐 在线预览方式

#### 方式 1：Swagger UI 在线查看（推荐）✨

点击下方链接在 Swagger Editor 中查看可交互的 API 文档：

[![Open in Swagger Editor](https://img.shields.io/badge/Open%20in-Swagger%20Editor-85EA2D?style=for-the-badge&logo=swagger&logoColor=white)](https://editor.swagger.io/?url=https://raw.githubusercontent.com/jeffzhou12/mini-etl/main/etl_openapi.json)

> 🚀 **优势**：可交互的 API 文档，支持在线测试、参数说明、响应示例等

#### 方式 2：其他在线工具

- **Redoc**: [![Open in Redoc](https://img.shields.io/badge/Open%20in-Redoc-8A2BE2?style=flat-square&logo=redoc)](https://redocly.github.io/redoc/?url=https://raw.githubusercontent.com/jeffzhou12/mini-etl/main/etl_openapi.json) - 更美观的文档展示
- **Stoplight**: [![Open in Stoplight](https://img.shields.io/badge/Open%20in-Stoplight-00D4AA?style=flat-square)](https://stoplight.io/p/docs/gh/jeffzhou12/mini-etl) - 专业的 API 设计平台

#### 方式 3：GitHub 原生 JSON 预览

查看 **[etl_openapi.json](./etl_openapi.json)** - GitHub 会自动格式化显示 JSON 内容

### 📄 说明

定义了 ETL 平台的核心 API 接口，包括数据源管理、任务配置、脚本执行等关键操作。

## 4. C# 分层架构草案（API / Service / Engine / Script Sandbox）

```text
ETL.Platform
├─ ETL.Api                // Web API 层（HTTP入口）
│  ├─ Controllers         // 控制器，接收请求
│  └─ Filters             // 统一异常 / 鉴权 / 日志
├─ ETL.Application        // 应用层（用例编排）
│  ├─ Services            // 业务服务（调度 / 执行编排）
│  └─ DTOs                // 输入输出模型
├─ ETL.Domain             // 领域层（核心模型）
│  ├─ Entities            // DataSource/ETLJob/Script等实体
│  ├─ ValueObjects        // Cron/Mapping等值对象
│  └─ Enums               // 类型枚举
├─ ETL.Engine             // 执行引擎层（ETL运行时）
│  ├─ Extractors          // 数据采集器（DB/API）
│  ├─ Transformers        // 脚本执行器
│  ├─ Loaders             // 写入器（DB/API）
│  └─ Pipeline            // 执行管线
├─ ETL.Sandbox            // 脚本沙箱层
│  ├─ RoslynRunner        // C#脚本执行
│  ├─ JsRunner            // JS脚本执行
│  └─ JavaRunner          // Java进程执行
└─ ETL.Infrastructure     // 基础设施层
   ├─ Repositories        // 数据持久化
   ├─ Scheduler           // 调度器
   └─ Logging             // 日志与审计
```

## 5. 脚本执行引擎设计（Roslyn / JS / Java Process）

```csharp
// 脚本执行统一接口，所有语言实现此接口
public interface IScriptExecutor
{
    // language: 脚本语言类型
    // input: 上游数据列表
    // return: 清洗后的结果
    Task<List<Dictionary<string, object>>> ExecuteAsync(
        ScriptLanguage language,
        string scriptContent,
        List<Dictionary<string, object>> input);
}

// Roslyn执行器：适用于 C# 脚本
public class RoslynExecutor : IScriptExecutor
{
    public async Task<List<Dictionary<string, object>>> ExecuteAsync(
        ScriptLanguage language,
        string scriptContent,
        List<Dictionary<string, object>> input)
    {
        // 关键点：传入上下文对象给脚本
        var context = new ScriptContext { Input = input };

        // Roslyn脚本执行（仅设计）
        // return CSharpScript.EvaluateAsync<List<Dictionary<string, object>>>(
        //    scriptContent, globals: context);

        return new List<Dictionary<string, object>>();
    }
}

// JS执行器：适用于 JavaScript 脚本
public class JsExecutor : IScriptExecutor
{
    public Task<List<Dictionary<string, object>>> ExecuteAsync(
        ScriptLanguage language,
        string scriptContent,
        List<Dictionary<string, object>> input)
    {
        // 关键点：限制执行时间 / 内存 / API暴露
        // var engine = new Engine(cfg => cfg.TimeoutInterval(...));
        // engine.SetValue("input", input);
        // engine.Execute(scriptContent);
        // var output = engine.GetValue("output");

        return Task.FromResult(new List<Dictionary<string, object>>());
    }
}

// Java执行器：通过独立进程执行
public class JavaExecutor : IScriptExecutor
{
    public async Task<List<Dictionary<string, object>>> ExecuteAsync(
        ScriptLanguage language,
        string scriptContent,
        List<Dictionary<string, object>> input)
    {
        // 关键点：将 input 序列化为 JSON，通过 STDIN 传递给 Java
        // 通过 STDOUT 获取输出 JSON
        // 需要控制超时、资源与安全隔离

        return new List<Dictionary<string, object>>();
    }
}
```

## 6. 数据流编排 DSL 设计 (可选)

### 6.1 DSL 示例

```yaml
pipeline:
  id: "etl_user_sync"
  name: "用户同步任务"
  schedule: "0 */5 * * * ?"     # Cron表达式
  source:
    type: "api"
    config:
      url: "https://api.example.com/users"
      method: "GET"
      headers:
        Authorization: "Bearer xxx"
  mapping:
    - source: "user_name"
      target: "name"
    - source: "user_age"
      target: "age"
  script:
    language: "python"
    entry: "transform"
    content: |
      def transform(input):
          output = []
          for row in input:
              row["name"] = row["name"].strip()
              row["age"] = int(row["age"])
              output.append(row)
          return output
  target:
    type: "database"
    config:
      provider: "SqlServer"
      connectionString: "Server=...;Database=...;User Id=...;Password=...;"
      table: "t_users"
```

### 6.2 DSL 解析模型（C# 草案）

```csharp
// DSL主对象
public class PipelineDsl
{
    public string Id { get; set; }
    public string Name { get; set; }
    public string Schedule { get; set; }
    public SourceDsl Source { get; set; }
    public List<MappingDsl> Mapping { get; set; }
    public ScriptDsl Script { get; set; }
    public TargetDsl Target { get; set; }
}

// 数据源配置
public class SourceDsl
{
    public string Type { get; set; }           // api/db/file
    public Dictionary<string, object> Config { get; set; }
}

// 字段映射
public class MappingDsl
{
    public string Source { get; set; }
    public string Target { get; set; }
}

// 脚本
public class ScriptDsl
{
    public string Language { get; set; }       // python/js/csharp/java
    public string Entry { get; set; }          // 函数入口
    public string Content { get; set; }
}

// 下游目标
public class TargetDsl
{
    public string Type { get; set; }           // database/api
    public Dictionary<string, object> Config { get; set; }
}
```
