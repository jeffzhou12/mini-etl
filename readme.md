# ETL 平台设计文档汇总

本文件汇总了模型设计、流程图、关键 API、分层架构、脚本引擎与 DSL 设计。

## 1. 模型设计（Excel）

- 文件：`etl_model_design.xlsx`

- 说明：包含数据源、字段映射、任务、脚本、下游目标等核心模型字段定义。

## 2. 流程图设计（Drawio）

- 文件：`etl_flow.drawio`

- 说明：覆盖触发器、采集、映射、脚本清洗、下游写入与审计日志。

## 3. 关键 API 设计（OpenAPI JSON）

- 文件：`etl_openapi.json`

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
