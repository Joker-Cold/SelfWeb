# 个人网站搭建计划

## 概览

- **技术栈**: Hugo (静态站点生成器)
- **托管**: GitHub Pages
- **类型**: 个人主页 / 作品集
- **域名**: `<username>.github.io` (后续可绑定自定义域名)

---

## 阶段一：环境准备

### 1.1 安装 Hugo

- Windows 推荐使用 `winget` 或 `scoop` 安装：
  ```bash
  winget install Hugo.Hugo.Extended
  # 或
  scoop install hugo-extended
  ```
- 验证安装：`hugo version`
- **注意**: 需安装 Extended 版本（支持 SCSS/SASS）

### 1.2 创建 GitHub 仓库

- 在 GitHub 上创建仓库，命名为 `<username>.github.io`（启用 GitHub Pages 专用域名）
- 或使用其他仓库名，后续通过仓库设置启用 Pages
- 将本地目录 `F:\SelfWork\SelfWeb` 初始化为 Git 仓库并关联远程仓库

---

## 阶段二：项目初始化

### 2.1 初始化 Hugo 项目

```bash
hugo new site . --force   # 在当前目录初始化
```

生成的目录结构：
```
SelfWeb/
├── archetypes/       # 内容模板
├── assets/           # 需要 Hugo 处理的资源 (SCSS, JS 等)
├── content/          # 网站内容 (Markdown)
├── data/             # 数据文件
├── i18n/             # 多语言支持
├── layouts/          # 自定义模板
├── static/           # 静态资源 (图片、文件等)
├── themes/           # 主题目录
└── hugo.toml         # Hugo 配置文件
```

### 2.2 选择并安装主题

推荐适合个人主页/作品集的主题（任选其一）：

| 主题 | 特点 | 链接 |
|------|------|------|
| **PaperMod** | 简洁现代，高度可定制 | hugo-PaperMod |
| **Blowfish** | 功能丰富，美观大方 | hugo-blowfish |
| **Congo** | 极简设计，Tailwind CSS | hugo-congo |
| **Terminal** | 极客风格，终端主题 | hugo-theme-terminal |

安装方式（以 PaperMod 为例，使用 Git submodule）：
```bash
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

---

## 阶段三：网站内容搭建

### 3.1 基础配置 (`hugo.toml`)

- 网站标题、描述、语言设置
- 主题配置
- 菜单导航配置
- SEO 相关配置

### 3.2 页面规划

```
content/
├── _index.md              # 首页内容
├── about/
│   └── index.md           # 关于我
├── projects/
│   ├── _index.md          # 项目列表页
│   ├── project-1.md       # 项目详情
│   └── project-2.md
└── contact/
    └── index.md           # 联系方式
```

### 3.3 各页面内容

- **首页**: 简短自我介绍、亮点技能、精选项目入口
- **关于我**: 个人背景、教育经历、技能栈
- **项目作品集**: 项目卡片展示，含描述、技术栈、链接/截图
- **联系方式**: 社交链接 (GitHub, LinkedIn, Email 等)

### 3.4 静态资源

- `static/images/` — 头像、项目截图等
- `static/files/` — 简历 PDF 等可下载文件（可选）

---

## 阶段四：本地开发与调试

### 4.1 本地预览

```bash
hugo server -D    # -D 包含草稿内容
```
访问 `http://localhost:1313` 实时预览

### 4.2 自定义样式

- 在 `assets/css/` 中添加自定义 CSS 覆盖主题样式
- 在 `layouts/` 中覆盖主题模板实现定制化

---

## 阶段五：部署到 GitHub Pages

### 5.1 配置 GitHub Actions 自动部署

创建 `.github/workflows/hugo.yml`：
- 触发条件：push 到 main 分支
- 构建步骤：安装 Hugo → 构建站点 → 部署到 GitHub Pages
- 使用 `actions/deploy-pages` 官方 Action

### 5.2 仓库设置

- 在 GitHub 仓库 Settings → Pages 中：
  - Source 选择 `GitHub Actions`

### 5.3 添加 `.gitignore`

```
public/
resources/
.hugo_build.lock
```

---

## 阶段六：优化与扩展（可选）

- [ ] 绑定自定义域名
- [ ] 添加 Google Analytics / 百度统计
- [ ] 配置 favicon 和 Open Graph 图片
- [ ] 添加深色/浅色模式切换
- [ ] SEO 优化（sitemap, robots.txt）
- [ ] 添加博客板块（如后续需要）

---

## 执行顺序

1. **环境准备** → 安装 Hugo、创建 GitHub 仓库
2. **项目初始化** → Hugo init、安装主题
3. **配置文件** → 编写 hugo.toml
4. **内容创建** → 编写各页面 Markdown
5. **本地调试** → hugo server 预览调整
6. **部署配置** → GitHub Actions workflow
7. **上线** → git push，自动构建部署
