# Chenqiang's Blog

个人博客,基于 [Astro](https://astro.build/) + [AstroPaper](https://github.com/satnaing/astro-paper) 主题。
文章以 Markdown 管理,通过 GitHub Actions 自动构建并部署到 GitHub Pages。

线上地址:<https://chenqiang-zhang.github.io/>

## 环境要求

- **Node.js ≥ 22.12**(Astro 6 要求)
- **pnpm**(本仓库用 pnpm 管理依赖,版本见 `package.json` 的 `packageManager` 字段)

> 本机若用 conda 管理 Node,可建一个专用环境:
> ```bash
> conda create -n node22 -c conda-forge "nodejs>=22.12"
> conda run -n node22 npm install -g pnpm
> ```
> 之后命令前加 `conda run -n node22` 即可。

## 本地开发

```bash
pnpm install            # 安装依赖
pnpm dev                # 本地预览 http://localhost:4321
pnpm build              # 构建到 dist/(含 astro check + pagefind 搜索索引)
pnpm preview            # 预览构建产物
```

## 写新文章

在 `src/content/posts/` 下新建 `.md` / `.mdx`,front-matter 示例:

```yaml
---
title: "文章标题"
pubDatetime: 2026-06-30T12:00:00.000Z
description: "用于列表和 SEO 的摘要(必填)"
tags:
  - 标签1
  - 标签2
featured: false   # 可选:是否首页置顶
draft: false      # 可选:草稿不发布
---
```

- 文章图片放在 `public/img/<文章目录>/`,正文用绝对路径 `/img/...` 引用。
- 「关于」页在 `src/content/pages/about.md`。
- 站点标题、作者、社交链接等在 `astro-paper.config.ts`。

## 部署

推送到 `main` 分支后,`.github/workflows/deploy.yml` 会自动用 pnpm 构建并发布到 GitHub Pages。
首次需在仓库 **Settings → Pages → Source** 选择 **GitHub Actions**。

## 历史内容

迁移前的旧静态站点(以及早期 Hexo 尝试)完整保存在 **`legacy`** 分支。
旧文章已抢救为 Markdown 迁入本站(见 `src/content/posts/` 中 2024 年的文章)。
