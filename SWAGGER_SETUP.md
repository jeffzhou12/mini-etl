# 如何在 GitHub 上展示 Swagger UI

## 🎯 方案对比

### ✅ 方案 1：使用在线 Swagger Editor（推荐 - 无需配置）

**优势**：零配置，立即可用

只需点击 README 中的 Swagger Editor 链接即可：
```
https://editor.swagger.io/?url=https://raw.githubusercontent.com/jeffzhou12/mini-etl/main/etl_openapi.json
```

### ✅ 方案 2：启用 GitHub Pages（完全自定义）

**优势**：拥有自己的 API 文档站点，更专业

#### 步骤：

1. **启用 GitHub Pages**
   - 进入仓库设置：Settings → Pages
   - Source: 选择 `Deploy from a branch`
   - Branch: 选择 `main` 分支，目录选择 `/ (root)`
   - 点击 Save

2. **等待部署完成**（通常 1-2 分钟）
   
3. **访问 Swagger UI**
   ```
   https://jeffzhou12.github.io/mini-etl/swagger-ui.html
   ```

4. **更新 README**（可选）
   
   添加 GitHub Pages 链接到 README：
   ```markdown
   [![View API Docs](https://img.shields.io/badge/API%20Docs-GitHub%20Pages-blue?style=for-the-badge)](https://jeffzhou12.github.io/mini-etl/swagger-ui.html)
   ```

### ✅ 方案 3：本地查看

直接在浏览器中打开 `swagger-ui.html` 文件：

```bash
# Windows
start swagger-ui.html

# macOS
open swagger-ui.html

# Linux
xdg-open swagger-ui.html
```

## 📝 其他在线工具

### Redoc（更美观的文档）
```
https://redocly.github.io/redoc/?url=https://raw.githubusercontent.com/jeffzhou12/mini-etl/main/etl_openapi.json
```

### Stoplight
适合团队协作的 API 设计平台

## 🔧 自定义 Swagger UI

如果需要自定义样式，可以编辑 `swagger-ui.html` 文件：

- 修改主题颜色
- 添加自定义 logo
- 配置默认展开的标签
- 设置认证信息

## 📚 参考资源

- [Swagger UI 官方文档](https://swagger.io/tools/swagger-ui/)
- [GitHub Pages 文档](https://docs.github.com/pages)
- [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
