# Blogs

白玩dev 的个人博文源文件仓库，用于存储所有公开发布的 Markdown 原创文章。

- **线上博客地址**：[https://whitemeta.cn/blog](https://whitemeta.cn/blog)
- **博客渲染引擎**：[WhiteP1ay/neo-blog](https://github.com/WhiteP1ay/neo-blog) (Next.js 15 + ISR)

---

## 自动化同步链路

本目录作为**博文纯内容源**，统一在知识库（kb）仓库中做版本管理，并通过 GitHub Actions 与博客前端解耦：

```
[知识库: iCloud Obsidian kb/blogs/] 
       │ git push origin main
       ▼
[GitHub: WhiteP1ay/kb]
       │ GitHub Actions (.github/workflows/sync-blogs.yml 仅监听 blogs/** 变动)
       ▼
[阿里云 ECS /data/posts/]
       │ Docker Volume 只读挂载 (/data/posts:/data/posts:ro)
       ▼
[neo-blog 容器] ── Next.js 15 读取 Markdown 并由 ISR 自动缓存呈现
```

每次向 `main` 分支推送且 `blogs/` 下文章有变动时（或 Obsidian Git 插件自动备份推送时），GitHub Actions 会自动通过 SSH 登录服务器拉取最新文章并同步到 `/data/posts`，文章在几秒内自动上线，无需重启任何服务。

---

## 文章 Frontmatter 规范

每篇 Markdown 文件头部需包含规范的 YAML Frontmatter：

```yaml
---
title: 文章标题
date: 2026-09-23
slug: english-url-slug
types: [技术, 前端]
tags: [Next.js, React]
excerpt: 可选，不填则自动提取正文前200字符
coverUrl: 可选，封面图片地址
---

正文内容...
```

### 字段说明与约束

| 字段 | 必填 | 类型 | 说明 |
|---|---|---|---|
| `title` | 是 | `string` | 文章标题，展示在列表页和文章头 |
| `date` | 是 | `string` | 发布日期，格式统一为 `YYYY-MM-DD`（列表按日期倒序） |
| `slug` | **是** | `string` | **英文/URL友好的唯一标识**（如 `why-tailwind`）。**不可使用中文**，避免 URL 编码与路由匹配异常导致 404 |
| `types` | 否 | `string[]` 或 `string` | 分类列表，如 `[技术, 前端]`，支持按分类筛选 |
| `tags` | 否 | `string[]` 或 `string` | 标签列表，展示为 `#tag` |
| `excerpt` | 否 | `string` | 自定义摘要。若不填，前端会自动剔除格式符号截取正文前 200 字 |
| `coverUrl` | 否 | `string` | 文章封面图链接 |

---

## 发布流程

在本地知识库编辑文章后，只需推送到 GitHub（Obsidian Git 插件也会自动备份推送）：

```bash
cd ~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/kb

git add blogs/<文章文件名>.md
git commit -m "post: 新增文章 <标题>"
git push origin main
```

