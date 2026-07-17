# 热 AI 实验室 EdgeOne Pages 部署经验文档

> 记录从 GitHub 仓库创建到 EdgeOne Pages 自动部署的完整流程，方便后续项目复用。

## 一、背景与目标

- **项目**：热 AI 实验室门户网站
- **仓库**：`llmlearning-x/reai-lab`
- **用途**：静态内容总集、博文门户
- **核心诉求**：
  1. 通过 GitHub 仓库管理代码
  2. 使用 EdgeOne Pages 托管静态站点
  3. 实现 `git push` 后自动构建部署（CI/CD）
  4. 首页与单篇文章独立 URL 访问

## 二、最终成果

- **GitHub 仓库**：https://github.com/llmlearning-x/reai-lab
- **线上访问地址**：https://reai-lab-ego039ho.edgeone.cool?eo_token=af5907b2c1e137a35c837ca973977624&eo_time=1784255944
- **WAIC 文章页**：https://reai-lab-ego039ho.edgeone.cool/waic-2026.html?eo_token=af5907b2c1e137a35c837ca973977624&eo_time=1784255944
- **控制台**：https://console.cloud.tencent.com/edgeone/makers/project/makers-y18y9ivcrcsy/index

> 注意：线上 URL 中的 `?eo_token=...&eo_time=...` 是 EdgeOne 预览域名鉴权参数，必须完整保留。如需长期稳定公开访问，建议绑定已完成备案的自定义域名。

## 三、目录结构

```
reai-lab/
├── .gitignore
├── README.md
├── index.html                    # 门户网站首页
├── waic-2026.html               # 单篇文章页，独立 URL 访问
├── qr-community.png             # 社区群二维码
└── qr-contact.png               # 运营负责人二维码
```

## 四、完整流程

### 1. 创建 GitHub 仓库

使用 `gh` CLI（已登录状态下）：

```bash
gh repo create reai-lab --public --add-readme
```

- `--public`：公开仓库
- `--add-readme`：初始化 README.md

### 2. 本地初始化并整理内容

```bash
cd /Users/fanghua/code/hotai
git init
git remote add origin https://github.com/llmlearning-x/reai-lab.git

# 原始文件重命名为 waic-2026.html 作为单篇文章保留
cp "WAIC2026-十大镇馆之宝.html" waic-2026.html

# 新建 index.html 作为门户首页
cat > index.html << 'EOF'
# 门户网站首页内容
EOF
```

### 3. 添加 .gitignore

```gitignore
# Tool metadata
.workbuddy/

# macOS
.DS_Store

# Editor/IDE
.idea/
.vscode/
*.swp
*.swo

# Logs
*.log

# Local env files
.env
.env.local

# Tencent Cloud EdgeOne
.edgeone/*
.tef_dist/*

# Playwright MCP temporary files
.playwright-mcp/
```

### 4. 推送代码到 GitHub

```bash
git add .
git commit -m "Initial commit: 热 AI 实验室门户网站首页"
git branch -M main

# 合并远程 README 后推送
git fetch origin
git merge origin/main --allow-unrelated-histories
git push origin main
```

### 5. 安装 EdgeOne CLI

```bash
npm install -g edgeone@latest
```

验证版本（必须 >= 1.6.0）：

```bash
edgeone -v
```

### 6. 登录 EdgeOne

在桌面环境下，执行浏览器登录：

```bash
export PAGES_SOURCE=skills
edgeone login --site china
```

> 在 Agent/CI/无桌面环境中，建议使用 Token 登录：`edgeone login --token <token>`。

### 7. 首次部署

```bash
export PAGES_SOURCE=skills
cd /Users/fanghua/code/hotai
edgeone makers deploy -n reai-lab --json
```

输出示例：

```json
{
  "status": "success",
  "url": "https://reai-lab-ego039ho.edgeone.cool?eo_token=...&eo_time=...",
  "type": "preset",
  "projectId": "makers-y18y9ivcrcsy",
  "deploymentId": "dpsdckg7947h",
  "consoleUrl": "https://console.cloud.tencent.com/edgeone/pages/project/makers-y18y9ivcrcsy/deployment/dpsdckg7947h"
}
```

### 8. 关联 GitHub 仓库实现自动 CI/CD

通过 EdgeOne 控制台（项目设置 → Git 管理）完成：

1. 进入项目设置页：`https://console.cloud.tencent.com/edgeone/makers/project/<projectId>/setting`
2. 在「Git 管理」中选择 **GitHub**
3. 选择账号 `llmlearning-x` 下的 `reai-lab` 仓库
4. 确认「环境管理」中：
   - **生产环境**：关联分支 `main`，自动部署 **开启**
   - **预览环境**：按需配置

之后每次 `git push origin main` 都会自动触发构建和部署。

## 五、关键配置项

### 构建部署配置（纯静态站点）

| 配置项 | 值 | 说明 |
|--------|-----|------|
| 框架预设 | 未设置 / 静态站点 | 无需构建框架 |
| 根目录 | `.` | 仓库根目录 |
| 输出目录 | 留空 | 上传整个目录 |
| 编译命令 | 留空 | 无需编译 |
| 安装命令 | 留空 | 无需安装依赖 |

### 环境管理

| 环境 | 关联分支 | 自动部署 |
|------|----------|----------|
| 生产 | `main` | 开启 |
| 预览 | 所有未分配分支 | 关闭 |

## 六、踩坑与注意事项

### 1. URL 必须完整携带鉴权参数

❌ 错误（会 401）：

```
https://reai-lab-xxx.edgeone.cool
```

✅ 正确：

```
https://reai-lab-xxx.edgeone.cool?eo_token=xxx&eo_time=xxx
```

### 2. 首页必须是 `index.html`

EdgeOne Pages 默认以 `index.html` 作为站点入口。如果只有 `waic-2026.html` 等单篇文章而不创建 `index.html`，访问根路径会展示 README 或 404。

### 3. CLI 版本要求

`edgeone` CLI 必须在 `1.6.0` 以上，否则 `whoami` 和登录流程在非交互环境中会挂起。安装时务必：

```bash
npm install -g edgeone@latest
```

### 4. 登录站点选择

中国站与国际站账号体系独立。CLI 登录必须显式指定 `--site`：

```bash
edgeone login --site china
# 或
edgeone login --site global
```

### 5. `.edgeone` 目录不要提交到 Git

CLI 会在本地生成 `.edgeone/` 和 `.tef_dist/` 构建产物，必须加入 `.gitignore`。

### 6. 浏览器会话复用陷阱

如果之前登录过国际站再登录中国站，浏览器可能静默复用旧会话，导致账号绑定错误。解决：先退出所有腾讯云控制台，或使用 Token 登录。

## 七、常用命令速查

```bash
# 检查登录状态
edgeone whoami

# 部署当前项目（已关联项目）
edgeone makers deploy

# 部署为新项目
edgeone makers deploy -n reai-lab --json

# 预览环境部署
edgeone makers deploy -e preview

# 使用 Token 部署
edgeone makers deploy -n reai-lab -t <token> --json
```

## 八、后续可优化方向

1. **自定义域名**：在控制台「域名管理」中绑定备案域名，获得稳定公开访问地址。
2. **Markdown 转静态页面**：引入简单构建脚本，将 `posts/*.md` 自动转为 HTML。
3. **文章列表自动生成**：在 `index.html` 中动态读取文章元数据，避免手动维护列表。
4. **多环境工作流**：开启预览环境自动部署，PR 合并前预览效果。

## 九、参考链接

- [EdgeOne Pages 官方文档](https://pages.edgeone.ai/zh/document)
- [EdgeOne Makers Tools Skill](https://github.com/TencentEdgeOne/edgeone-makers-tools)
- [GitHub 仓库](https://github.com/llmlearning-x/reai-lab)
- [热 AI 实验室线上站点](https://reai-lab-ego039ho.edgeone.cool?eo_token=af5907b2c1e137a35c837ca973977624&eo_time=1784255944)

---

文档版本：2026-07-17  
维护者：热 AI 实验室
