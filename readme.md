# ETL 平台设计文档汇总

本文件汇总了模型设计、流程图、关键 API、分层架构、脚本引擎与 DSL 设计。

## 1. 模型设计

### 📊 在线预览（Markdown 版本）

查看 **[etl_model_design.md](./etl_model_design.md)**

<details open>
<summary>📋 点击展开模型定义表格</summary>

#### 📦 数据源模型 (DataSource)

<table border="1" cellpadding="8" cellspacing="0">
<thead>
<tr>
<th>字段</th>
<th>类型</th>
<th>必填</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr><td>Id</td><td>Guid</td><td>是</td><td>数据源唯一标识</td></tr>
<tr><td>Name</td><td>string</td><td>是</td><td>数据源名称</td></tr>
<tr><td>Type</td><td>enum(DataSourceType)</td><td>是</td><td>数据库/API/文件等类型</td></tr>
<tr><td>ConnectionConfig</td><td>json</td><td>否</td><td>连接信息，包含连接串、凭证等</td></tr>
<tr><td>ApiConfig</td><td>json</td><td>否</td><td>API读取配置（URL、Header、Method等）</td></tr>
<tr><td>FilePath</td><td>string</td><td>否</td><td>文件路径，可以是一个json或者xml文件上传到服务器或者对象存储端</td></tr>
</tbody>
</table>

#### 🔗 字段映射模型 (FieldMapping)

<table border="1" cellpadding="8" cellspacing="0">
<thead>
<tr>
<th>字段</th>
<th>类型</th>
<th>必填</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr><td>Id</td><td>Guid</td><td>是</td><td>映射唯一标识</td></tr>
<tr><td>SourceField</td><td>string</td><td>是</td><td>上游字段名</td></tr>
<tr><td>TargetField</td><td>string</td><td>是</td><td>下游字段名</td></tr>
</tbody>
</table>

#### ⚙️ 任务模型 (ETLJob)

<table border="1" cellpadding="8" cellspacing="0">
<thead>
<tr>
<th>字段</th>
<th>类型</th>
<th>必填</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr><td>Id</td><td>Guid</td><td>是</td><td>任务唯一标识</td></tr>
<tr><td>Name</td><td>string</td><td>是</td><td>任务名称</td></tr>
<tr><td>Schedule</td><td>string(Cron)</td><td>否</td><td>执行频率（Cron 表达式）</td></tr>
<tr><td>ManualTrigger</td><td>bool</td><td>否</td><td>是否支持手动触发</td></tr>
<tr><td>DataSourceId</td><td>Guid</td><td>是</td><td>关联上游数据源ID</td></tr>
<tr><td>TargetSinkId</td><td>Guid</td><td>是</td><td>关联下游目标ID</td></tr>
<tr><td>ScriptId</td><td>Guid</td><td>否</td><td>关联清洗脚本ID（可选）</td></tr>
<tr><td>FieldMappings</td><td>json/array</td><td>否</td><td>字段映射列表（或引用映射配置ID）</td></tr>
<tr><td>DSL</td><td>json</td><td>否</td><td>任务执行内容，这里会关联上下游配置，脚本配置，字段映射等</td></tr>
</tbody>
</table>

#### 📝 脚本模型 (TransformScript)

<table border="1" cellpadding="8" cellspacing="0">
<thead>
<tr>
<th>字段</th>
<th>类型</th>
<th>必填</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr><td>Id</td><td>Guid</td><td>是</td><td>脚本唯一标识</td></tr>
<tr><td>Language</td><td>enum(ScriptLanguage)</td><td>是</td><td>脚本语言：Python/JS/C#/Java</td></tr>
<tr><td>Content</td><td>string</td><td>是</td><td>脚本内容</td></tr>
</tbody>
</table>

#### 🎯 下游目标模型 (TargetSink)

<table border="1" cellpadding="8" cellspacing="0">
<thead>
<tr>
<th>字段</th>
<th>类型</th>
<th>必填</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr><td>Id</td><td>Guid</td><td>是</td><td>下游目标唯一标识</td></tr>
<tr><td>Type</td><td>enum(TargetType)</td><td>是</td><td>数据库/API订阅</td></tr>
<tr><td>Config</td><td>json</td><td>否</td><td>下游连接配置，可以是API或者数据库连接地址</td></tr>
</tbody>
</table>

</details>

### 📥 原始文件

- Excel 文件：[etl_model_design.xlsx](./etl_model_design.xlsx)（需下载查看）

## 2. 流程图设计

### 🎨 ETL 流程图

![ETL 流程图](./etl_flow.drawio.png)

### 📝 说明

覆盖触发器、采集、映射、脚本清洗、下游写入与审计日志的完整 ETL 流程。

### 📂 源文件

- **Drawio 源文件**: [etl_flow.drawio](./etl_flow.drawio) - 可使用 Draw.io 编辑
- **导出图片**: [etl_flow.drawio.png](./etl_flow.drawio.png)

<details>
<summary>💡 如何编辑流程图</summary>

**推荐安装 Draw.io 扩展**：

```vscode-extensions
hediet.vscode-drawio
```

**编辑步骤**：
1. 安装上方推荐的扩展
2. 在 VS Code 中打开 `etl_flow.drawio` 文件
3. 编辑完成后，导出为 PNG/SVG 格式
4. 提交更新后的图片到仓库

查看 **[导出说明](./EXPORT_DRAWIO.md)** 了解更多详情。

</details>

## 3. 关键 API 设计

openapi 源文件: **[etl_openapi.json](./etl_openapi.json)**

**在线查看**（无需配置）：

[![Open in Redoc](https://img.shields.io/badge/Open%20in-Redoc-8A2BE2?style=flat-square&logo=redoc)](https://redocly.github.io/redoc/?url=https://raw.githubusercontent.com/jeffzhou12/mini-etl/main/etl_openapi.json)




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

## 5. 脚本内容约定 (TransformScript.Content)

### 📋 统一约定

所有语言的脚本都必须遵循以下约定：

1. **输入参数**：脚本接收一个名为 `input` 的参数，类型为数组/列表，每个元素是一个字典/对象
2. **输出结果**：脚本必须返回处理后的数组/列表，结构与输入相同
3. **入口函数**：定义一个名为 `transform` 的函数作为入口点
4. **数据结构**：每条记录是 `key-value` 结构，key 为字段名，value 为字段值

### 💻 各语言示例

#### Python 脚本示例

```python
def transform(input):
    """
    数据转换函数
    :param input: 输入数据列表，每个元素是字典 [{"field1": "value1", ...}, ...]
    :return: 转换后的数据列表
    """
    output = []
    
    for row in input:
        # 数据清洗：去除空格
        if "name" in row:
            row["name"] = row["name"].strip()
        
        # 数据转换：类型转换
        if "age" in row:
            row["age"] = int(row["age"])
        
        # 数据过滤：跳过无效记录
        if row.get("age", 0) > 0:
            # 数据增强：添加新字段
            row["processed_at"] = "2026-01-30"
            output.append(row)
    
    return output
```

#### JavaScript 脚本示例

```javascript
function transform(input) {
    /**
     * 数据转换函数
     * @param {Array} input - 输入数据数组，每个元素是对象 [{field1: "value1", ...}, ...]
     * @return {Array} 转换后的数据数组
     */
    const output = [];
    
    for (const row of input) {
        // 数据清洗：去除空格
        if (row.name) {
            row.name = row.name.trim();
        }
        
        // 数据转换：类型转换
        if (row.age) {
            row.age = parseInt(row.age);
        }
        
        // 数据过滤：跳过无效记录
        if (row.age > 0) {
            // 数据增强：添加新字段
            row.processed_at = "2026-01-30";
            output.push(row);
        }
    }
    
    return output;
}
```

#### C# 脚本示例

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public List<Dictionary<string, object>> Transform(List<Dictionary<string, object>> input)
{
    /// <summary>
    /// 数据转换函数
    /// </summary>
    /// <param name="input">输入数据列表，每个元素是字典</param>
    /// <returns>转换后的数据列表</returns>
    
    var output = new List<Dictionary<string, object>>();
    
    foreach (var row in input)
    {
        // 数据清洗：去除空格
        if (row.ContainsKey("name") && row["name"] is string name)
        {
            row["name"] = name.Trim();
        }
        
        // 数据转换：类型转换
        if (row.ContainsKey("age"))
        {
            row["age"] = Convert.ToInt32(row["age"]);
        }
        
        // 数据过滤：跳过无效记录
        if (row.ContainsKey("age") && (int)row["age"] > 0)
        {
            // 数据增强：添加新字段
            row["processed_at"] = "2026-01-30";
            output.Add(row);
        }
    }
    
    return output;
}
```

## 6. 脚本执行引擎设计（Roslyn / JS / Java Process）

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

## 7. 数据流编排 DSL 设计 (可选)

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
