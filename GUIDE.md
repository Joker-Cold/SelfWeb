# SelfWeb 个人主页修改与部署指南

## 项目概览

这是一个基于 **Hugo** + **PaperMod** 主题的个人网站，通过 GitHub Actions 自动部署到 GitHub Pages。

```
SelfWeb/
├── content/          # 你的文章和页面内容（主要编辑这里）
│   ├── _index.md     # 首页
│   ├── about/        # 关于页面
│   └── projects/     # 项目展示
├── hugo.toml         # 网站全局配置
├── static/           # 静态资源（图片、文件等）
├── layouts/          # 自定义布局（覆盖主题模板）
├── assets/           # 需要 Hugo 处理的资源（CSS/JS）
└── themes/PaperMod/  # 主题（不要直接修改）
```

---

## 1. 本地开发环境

### 方式一：Docker（推荐，无需安装 Hugo）

```bash
docker compose up
```

浏览器访问 `http://localhost:1313`，修改文件后页面会自动刷新。

### 方式二：安装 Hugo

1. 安装 Hugo Extended >= 0.147.0（参考 https://gohugo.io/installation/）
2. 启动本地服务：

```bash
hugo server -D
```

`-D` 表示同时显示草稿（draft）文章。

---

## 2. 修改网站配置

编辑 `hugo.toml`：

### 修改网站标题和个人介绍

```toml
title = 'Joker Cold'   # 网站标题，显示在浏览器标签页

[params.homeInfoParams]
  Title = "Hi, I'm Joker Cold"          # 首页大标题
  Content = "Developer | Builder | Explorer"  # 首页副标题/简介
```

### 修改社交链接

```toml
[[params.socialIcons]]
  name = "github"
  url = "https://github.com/Joker-Cold"

[[params.socialIcons]]
  name = "email"
  url = "mailto:your@email.com"    # 改成你的真实邮箱
```

支持的图标名称：`github`、`email`、`twitter`、`linkedin`、`youtube`、`bilibili`、`zhihu`、`weibo` 等。完整列表见 [PaperMod Icons](https://github.com/adityatelange/hugo-PaperMod/wiki/Icons)。

### 添加/修改导航菜单

```toml
[[menus.main]]
  name = "Blog"        # 显示名称
  url = "/blog/"       # 对应 content/blog/ 目录
  weight = 4           # 排序权重，数字越小越靠前
```

### 其他常用配置

```toml
[params]
  defaultTheme = "auto"       # "auto" | "light" | "dark"
  showReadingTime = true       # 显示阅读时间
  showShareButtons = true      # 显示分享按钮
  showBreadCrumbs = true       # 显示面包屑导航
  showCodeCopyButtons = true   # 代码块复制按钮
  showToc = true               # 默认显示文章目录
```

---

## 3. 编辑现有页面

### 编辑「关于」页面

文件：`content/about/index.md`

```markdown
---
title: "About"
layout: "single"
---

## About Me

在这里写你的个人介绍...

## Skills

- Python, JavaScript, Go
- React, Vue
- Docker, Git, Linux

## Education

- XX 大学 - XX 专业（20XX - 20XX）
```

### 编辑项目页面

文件：`content/projects/project-1.md`

```markdown
---
title: "我的项目名称"
summary: "一句话介绍这个项目"
date: 2026-01-01
tags: ["Python", "Docker"]
showToc: false
---

## Overview

项目介绍...

## Tech Stack

- Python
- Docker

## Links

- [GitHub Repo](https://github.com/你的链接)
- [Live Demo](https://演示地址)
```

---

## 4. 创建新内容

### 创建新项目

在 `content/projects/` 下新建 `.md` 文件，例如 `content/projects/my-new-project.md`：

```markdown
---
title: "新项目名称"
summary: "项目简介"
date: 2026-03-31
tags: ["Go", "Kubernetes"]
showToc: false
---

项目内容...
```

### 创建博客功能

1. 创建目录 `content/blog/`
2. 创建列表页 `content/blog/_index.md`：

```markdown
---
title: "Blog"
layout: "list"
---
```

3. 创建文章 `content/blog/my-first-post.md`：

```markdown
---
title: "我的第一篇博客"
date: 2026-03-31
draft: false
tags: ["日常", "技术"]
summary: "这是文章摘要"
showToc: true
---

## 正文开始

你的文章内容...
```

4. 在 `hugo.toml` 中添加导航菜单（参考上面第 2 节）。

> **注意**：`draft: true` 的文章不会出现在正式构建中，只有本地 `hugo server -D` 才能看到。发布时记得改为 `draft: false`。

---

## 5. 添加图片和静态资源

### 方式一：放在 static/ 目录

将图片放入 `static/images/` 目录，在 Markdown 中引用：

```markdown
![图片描述](/SelfWeb/images/my-photo.jpg)
```

> 注意：路径需要包含 baseURL 中的子路径 `/SelfWeb/`。

### 方式二：页面资源（Page Bundle）

将图片和页面放在同一目录：

```
content/blog/my-post/
├── index.md
└── cover.jpg
```

在 frontmatter 中设置封面：

```markdown
---
title: "文章标题"
cover:
  image: "cover.jpg"
  alt: "封面描述"
  hidden: false
---
```

---

## 6. 自定义样式

在 `assets/css/extended/` 下创建自定义 CSS 文件（PaperMod 会自动加载该目录的 CSS）：

```css
/* assets/css/extended/custom.css */
body {
  font-family: "Noto Sans SC", sans-serif;
}
```

---

## 7. 推送更新到 Git 并部署

### 日常更新流程

```bash
# 1. 查看修改了哪些文件
git status

# 2. 添加修改的文件到暂存区
git add content/                    # 添加内容修改
git add hugo.toml                   # 添加配置修改
git add static/                     # 添加静态资源
# 或者添加所有修改：
git add -A

# 3. 提交（写清楚你改了什么）
git commit -m "添加新博客文章：我的第一篇博客"

# 4. 推送到 GitHub
git push origin main
```

推送后，GitHub Actions 会自动构建并部署到 GitHub Pages。

### 查看部署状态

- 打开 GitHub 仓库页面 → Actions 标签页，可以看到构建进度
- 部署成功后访问：`https://joker-cold.github.io/SelfWeb/`

### 常见 Git 场景

```bash
# 只修改了一个文件，快速提交
git add content/blog/my-post.md
git commit -m "更新博客文章"
git push origin main

# 添加了新图片和文章
git add content/blog/new-post/ static/images/
git commit -m "新增博客：XXX"
git push origin main

# 查看远程仓库地址
git remote -v
```

---

## 8. Frontmatter 参考速查

文章顶部 `---` 之间的配置项：

| 字段 | 说明 | 示例 |
|------|------|------|
| `title` | 文章标题 | `"我的文章"` |
| `date` | 发布日期 | `2026-03-31` |
| `draft` | 是否为草稿 | `true` / `false` |
| `tags` | 标签 | `["Go", "Web"]` |
| `summary` | 摘要 | `"一句话简介"` |
| `showToc` | 显示目录 | `true` / `false` |
| `weight` | 排序权重 | `1`（越小越靠前） |
| `cover.image` | 封面图片 | `"cover.jpg"` |
| `author` | 作者 | `"Joker Cold"` |
| `hiddenInHomeList` | 首页隐藏 | `true` |

---

## 快速上手 Checklist

- [ ] 修改 `hugo.toml` 中的邮箱和个人信息
- [ ] 编辑 `content/about/index.md` 填写真实个人介绍
- [ ] 替换 `content/projects/` 下的示例项目为你的真实项目
- [ ] （可选）创建 `content/blog/` 开始写博客
- [ ] （可选）在 `static/images/` 放一张头像
- [ ] `git add -A && git commit -m "更新个人信息" && git push origin main`
