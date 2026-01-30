# 导出 Drawio 为图片的步骤

由于 GitHub 不支持直接预览 .drawio 文件，请按以下步骤导出为图片：

## 方法 1：使用 VS Code 扩展（推荐）

1. 安装 Draw.io Integration 扩展
2. 在 VS Code 中打开 `etl_flow.drawio`
3. 使用命令面板（Ctrl+Shift+P）运行：`Draw.io: Export to...`
4. 选择导出格式：PNG 或 SVG（推荐 SVG，矢量图）
5. 保存为 `etl_flow.svg` 或 `etl_flow.png`

## 方法 2：使用在线版 Draw.io

1. 访问 https://app.diagrams.net/
2. 打开 `etl_flow.drawio` 文件
3. 点击菜单：File -> Export as -> PNG/SVG
4. 下载并保存为 `etl_flow.svg` 或 `etl_flow.png`

## 导出后

将导出的图片文件添加到仓库，然后可以在 README.md 中引用：

```markdown
![ETL 流程图](./etl_flow.svg)
```

或

```markdown
![ETL 流程图](./etl_flow.png)
```
