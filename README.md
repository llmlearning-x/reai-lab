# 热 AI 实验室

热 AI 实验室（Hot AI Lab）的内容总集与静态门户网站。

## 在线访问

- **门户首页**：https://reai-lab-ego039ho.edgeone.cool?eo_token=af5907b2c1e137a35c837ca973977624&eo_time=1784255944
- **WAIC 2026 · 十大镇馆之宝**：https://reai-lab-ego039ho.edgeone.cool/waic-2026.html?eo_token=af5907b2c1e137a35c837ca973977624&eo_time=1784255944

> 注意：EdgeOne Pages 预览域名 URL 中的 `?eo_token=...&eo_time=...` 为鉴权参数，需完整保留。

## 仓库结构

```
reai-lab/
├── index.html                    # 门户网站首页
├── waic-2026.html               # 单篇文章页
├── qr-community.png             # 实验室社区群二维码
├── qr-contact.png               # 运营负责人二维码
├── DEPLOYMENT-GUIDE.md          # 部署经验文档
└── README.md
```

## 部署说明

本项目使用 [EdgeOne Pages](https://pages.edgeone.ai/) 托管，并与本 GitHub 仓库关联实现自动 CI/CD：

- `main` 分支推送到 GitHub 后，EdgeOne 会自动构建并部署到生产环境。
- 详细流程、踩坑记录与命令速查见 [DEPLOYMENT-GUIDE.md](./DEPLOYMENT-GUIDE.md)。

## 参与与联系

- 加入社区：扫描 `qr-community.png`
- 联系运营：扫描 `qr-contact.png`
