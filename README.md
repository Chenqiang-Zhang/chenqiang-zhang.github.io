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

在 `src/content/posts/` 下新建一个 `.md`(或 `.mdx`)文件。**文件名即文章网址**(`/posts/<文件名>/`),
建议用英文短横线 slug(如 `my-first-post.md`),标题本身可以是中文。

front-matter(文件顶部 `---` 之间的部分)字段说明:

```yaml
---
title: "文章标题"                        # 必填
pubDatetime: 2026-06-30T12:00:00+08:00   # 必填,发布时间(ISO 格式,+08:00 = 北京时间)
description: "用于文章列表、搜索和分享卡片的摘要" # 必填
tags:                                    # 可选,不写默认 ["others"]
  - 标签1
  - 标签2
cover: "/img/my-first-post/cover.webp"   # 可选,文章顶部封面图(见下方「图片」)
featured: false                          # 可选,true = 首页顶部「Featured」区置顶
draft: false                             # 可选,true = 草稿,不会发布
modDatetime: 2026-07-01T09:00:00+08:00   # 可选,最后修改时间
---

正文从这里开始,直接写 Markdown……
```

写完本地 `pnpm dev` 预览没问题后,提交推送即可自动上线(见「部署」)。

## 编辑已有内容

| 想改什么 | 改哪个文件 |
|---|---|
| 某篇文章的标题 / 正文 / 标签 / 封面 | `src/content/posts/<对应文件>.md` |
| 「关于」页 | `src/content/pages/about.md` |
| 首页欢迎语(hero 标题和介绍) | `src/pages/index.astro` |
| 站点标题 / 作者 / 描述 / 社交链接(GitHub、邮箱) | `astro-paper.config.ts` |
| 每页文章数、是否启用搜索/归档等开关 | `astro-paper.config.ts` |

> 删除文章 = 删除对应的 `.md` 文件;暂时下线 = 在 front-matter 里设 `draft: true`。

## 修改图标与图像

所有图片资源放在 `public/` 目录,网页里用 `/` 开头的绝对路径引用(例如 `public/img/a/b.png` → `/img/a/b.png`)。

| 图像 | 文件位置 | 建议尺寸 | 在哪里引用 |
|---|---|---|---|
| **网站图标 favicon** | `public/favicon.svg`、`public/favicon.ico`、`public/apple-touch-icon.png` | 正方形 | `src/layouts/Layout.astro` |
| **首页横幅**(明/暗两版) | `public/img/banner-light.webp`、`public/img/banner-dark.webp` | 约 1920×1080 | `src/pages/index.astro` |
| **首页头像**(横幅上的圆形 logo) | `public/apple-touch-icon.png` | 正方形 | `src/pages/index.astro` |
| **文章封面** | `public/img/<文章名>/cover.webp` | 1200×630 | 文章 front-matter 的 `cover:` 字段 |
| **文章正文配图** | `public/img/<文章名>/xxx.png` | 不限 | 正文里 `![说明](/img/<文章名>/xxx.png)` |
| **默认分享图**(没设 `cover` 时的兜底) | `public/default-og.jpg` | 1200×630 | `astro-paper.config.ts` 的 `ogImage` |

**改主题强调色**(链接、按钮、高亮等):编辑 `src/styles/theme.css`,改 `--accent`
(浅色模式和深色模式各一个,当前是旧站的红 `#a31f34` / `#e8556b`)。

**图片要先压缩再放进仓库**(原图动辄几 MB,会拖慢加载)。本项目已装了 `sharp`,可一行命令压缩:

```bash
# 把一张大图压成 1200×630、约 100KB 的 webp 封面(在项目根目录运行)
node -e "require('sharp')('原图.jpg').resize(1200,630,{fit:'cover'}).webp({quality:80}).toFile('public/img/文章名/cover.webp')"

# 从一张方形 PNG 重新生成全套 favicon
node -e "const s=require('sharp');const f='logo.png';s(f).resize(128,128).png().toBuffer().then(b=>require('fs').writeFileSync('public/favicon.svg',`<svg xmlns='http://www.w3.org/2000/svg' width='128' height='128'><image width='128' height='128' href='data:image/png;base64,${b.toString('base64')}'/></svg>`));s(f).resize(32,32).png().toFile('public/favicon.ico');s(f).resize(180,180).png().toFile('public/apple-touch-icon.png')"
```

## 部署

推送到 `main` 分支后,`.github/workflows/deploy.yml` 会自动用 pnpm 构建并发布到 GitHub Pages。
首次需在仓库 **Settings → Pages → Source** 选择 **GitHub Actions**。

## 历史内容

迁移前的旧静态站点(以及早期 Hexo 尝试)完整保存在 **`legacy`** 分支。
旧文章已抢救为 Markdown 迁入本站(见 `src/content/posts/` 中 2024 年的文章)。
