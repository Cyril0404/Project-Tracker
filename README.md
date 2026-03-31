# Booksea 出海网站开发手册

> **项目代号**：Booksea
> **需求方**：神冢
> **负责人**：员工201 / 员工202
> **手册版本**：v1.0 | 2026-03-30

---

## 目录

1. [项目概述](#1-项目概述)
2. [本地开发环境搭建](#2-本地开发环境搭建)
3. [目录结构说明](#3-目录结构说明)
4. [核心模块解析](#4-核心模块解析)
5. [部署说明](#5-部署说明)
6. [开发规范](#6-开发规范)
7. [常见问题 FAQ](#7-常见问题-faq)
8. [联系方式](#8-联系方式)

---

## 1. 项目概述

### 1.1 项目定位

Booksea 是一个面向海外市场的**中国公版书阅读平台**，核心使命是将中国经典文学（四大名著、论语、道德经、孙子兵法等）以多语言形式（中文 / English / Español）呈现给全球读者。

**与竞品的差异点：**
- 双语对照阅读（中英/中西段落级对照）
- 多语言一键切换，无需刷新
- 纯静态架构，CDN 友好，全球访问速度快

### 1.2 核心功能

| 功能 | 说明 |
|------|------|
| 📚 图书浏览 | 首页展示封面、评分、标签、章节数 |
| 🌐 多语言切换 | 中文 / English / Español，localStorage 记忆偏好 |
| 📖 章节阅读 | 动态加载章节内容，段落级双语对照 |
| 🔍 搜索 | 标题、作者、标签全文搜索 |
| 🏷️ 分类筛选 | 按标签（Philosophy / Classic / Adventure 等）过滤 |
| ⭐ 评分展示 | 豆瓣类 5 星评分展示 |

### 1.3 技术栈

| 层级 | 技术选型 | 说明 |
|------|----------|------|
| 前端框架 | Vanilla JS SPA | 无框架依赖，轻量级路由 |
| 样式 | 自定义 CSS（dark theme） | 深色主题，配色变量系统 |
| 内容存储 | 本地 `.md` 文件 | `/content/` 和 `/books/` 目录 |
| 后端服务 | Python `http.server` | 本地开发服务器（端口 8899） |
| 数据 API | 东财搜索 API | `/api/search` 端点（股票搜索） |
| 数据抓取 | efinance + 新浪 | `fetch_data.py` 定时任务 |
| 部署平台 | **Vercel**（当前）| `.vercel/` 配置已就绪 |
| 备选部署 | 腾讯云 CVM | 手动部署说明见第 5 章 |

### 1.4 内容现状

已有多语言翻译的书籍：

| 书籍 | 中文 | English | Español |
|------|:----:|:-------:|:-------:|
| 西游记 | ✅ 100 回 | ✅ 100 回 | 计划中 |
| 红楼梦 | ✅ | ✅ | ✅ |
| 水浒传 | ✅ | ✅（分章） | ✅ |
| 三国演义 | ✅ | ✅ | ✅ |
| 道德经 | ✅ | ✅ | ✅ |
| 论语 | ✅ | ✅ | ✅ |
| 孙子兵法 | ✅ | ✅ | ✅ |
| 聊斋志异 | ✅ | ✅ | 计划中 |

> **内容存储位置**：`~/openclaw/translated-books/`（独立 Git 仓库）

---

## 2. 本地开发环境搭建

### 2.1 前置条件

- **macOS / Linux / Windows（WSL）**
- Python 3.8+
- Git

### 2.2 克隆项目

```bash
# 克隆主站仓库
git clone https://github.com/Cyril0404/gupiao-helper.git booksea
cd booksea

# 克隆翻译内容仓库（子模块或独立仓库）
git clone ~/openclaw/translated-books/ ./content/translated/
# 或（如果没有本地仓库）
# git clone https://github.com/Cyril0404/translated-books.git ./content/translated/
```

> ⚠️ 注意：仓库名 `gupiao-helper` 是历史遗留，实际为 Booksea 项目代码。

### 2.3 安装依赖

```bash
# Python 依赖（仅后台数据抓取需要）
pip install efinance

# 前端无需构建，直接服务静态文件
```

### 2.4 启动本地服务器

```bash
# 方式一：直接运行 server.py（推荐）
python server.py
# 输出：Serving at http://localhost:8899

# 方式二：使用任意静态服务器
python -m http.server 8899
# 或
npx serve . -p 8899
```

### 2.5 访问验证

打开浏览器访问：**http://localhost:8899**

首页应显示 Booksea 图书卡片矩阵（深色主题），点击任意书籍进入阅读页。

---

## 3. 目录结构说明

```
booksea/
├── index.html              # 入口 HTML（SPA 壳）
├── server.py               # Python HTTP 服务器 + API
├── fetch_data.py           # 股票数据抓取脚本
├── BILINGUAL_SPEC.md       # 双语功能规格说明
│
├── css/
│   └── style.css           # 全局样式（CSS 变量 + 响应式）
│
├── js/
│   └── app.js              # 核心 SPA 逻辑（路由 + 渲染 + 数据）
│
├── content/                # 中文原文 Markdown
│   ├── xiyouji/            # 西游记（ch_001 ~ ch_100）
│   ├── honglou-en.md       # 红楼梦（英译）
│   ├── lunyu-en.md / lunyu-es.md
│   ├── daodejing-en.md / daodejing-es.md
│   ├── sunzi-en.md / sunzi-es.md
│   ├── shuihu/             # 水浒传分章英译
│   ├── sanguo-en.md
│   ├── liaozhai-en.md
│   ├── glossary.md         # 翻译术语表
│   └── original/           # 未处理原始文本
│       ├── 西游记.txt
│       ├── 水浒传.txt
│       └── ...
│
├── books/                  # 已发布书籍的翻译成品
│   └── xiyouji/
│       └── en/            # 西游记英文翻译（ch_001_en.md ~ ch_100_en.md）
│
├── .vercel/                # Vercel 部署配置
│   ├── project.json
│   └── README.txt
│
└── .git/                   # Git 仓库
```

**各文件/目录详细说明：**

| 文件 | 用途 |
|------|------|
| `index.html` | 单页应用入口，引用 `app.js` 和 `style.css`，路由由 JS 接管 |
| `server.py` | Python 内置 HTTP 服务器，支持静态文件服务 + `/api/search` 股票搜索接口 |
| `fetch_data.py` | 定时数据抓取（efinance + 新浪），写入 `website_data.json` |
| `BILINGUAL_SPEC.md` | 双语功能的 UI/UX 规格说明，是前端开发的设计依据 |
| `js/app.js` | 全站核心逻辑：书籍数据、路由（`#/`, `#/categories`, `#/book/:id`）、阅读器渲染、语言切换 |
| `css/style.css` | 配色系统（CSS 变量），布局，组件样式；深色主题（`--bg-primary: #0D0D12`） |
| `content/` | 中文原文 + 英文/西班牙文翻译（Markdown 格式），被前端 `fetch()` 动态加载 |
| `books/` | 正式发布的分章翻译，适合 SEO 友好的 URL 结构 |
| `.vercel/` | Vercel 项目 ID 和团队 ID，部署时自动读取 |

---

## 4. 核心模块解析

### 4.1 `server.py` — 服务器入口

**职责：** 同时提供静态文件服务和股票搜索 API。

```
请求 → server.py → 静态文件（index.html 等）
                  → /api/search → 东财 API → 股票搜索结果
```

**核心代码结构：**

```python
#!/usr/bin/env python3
import http.server, socketserver, json, urllib.request, urllib.parse, os

PORT = 8899
STATIC_DIR = "/Users/zifanni/openclaw/website"   # ← 项目根目录

class Handler(http.server.SimpleHTTPRequestHandler):
    def do_GET(self):
        if self.path.startswith("/api/search"):
            # 解析查询参数
            # 调用东财搜索 API（无需 Key）
            # 返回 JSON: [{name, code, market, type}, ...]
            self.send_json({"result": [...]})
        else:
            super().do_GET()  # 静态文件服务

    def send_json(self, data):
        body = json.dumps(data, ensure_ascii=False).encode("utf-8")
        # 设置 CORS 头，允许跨域调用
        self.send_response(200)
        self.send_header("Content-Type", "application/json; charset=utf-8")
        self.send_header("Access-Control-Allow-Origin", "*")
        ...
```

**关键接口：**

| 端点 | 方法 | 说明 | 响应格式 |
|------|------|------|----------|
| `/` | GET | 返回 index.html | HTML |
| `/api/search?q=茅台` | GET | 东财股票搜索 | `{"result": [{name, code, market, type}]}` |
| `/content/...` | GET | 返回 Markdown 原文 | text/markdown |
| `/books/...` | GET | 返回翻译章节文件 | text/markdown |

**端口配置：** 修改 `PORT` 变量即可更换端口。

---

### 4.2 `fetch_data.py` — 数据抓取

**职责：** 从 efinance（东方财富 Python 库）和新浪财经定时抓取市场数据，写入 `website_data.json`。

**数据源：**

| 数据类型 | 来源 | 调用方式 |
|----------|------|----------|
| 指数实时行情（上证/深证/创业板/沪深300）| efinance | `ef.get_latest_quote(["000001", "399001", ...])` |
| 自选股实时行情 | efinance | `ef.get_latest_quote([...])` |
| 涨停/跌停名单 | 新浪财经 | `curl` HTTP 请求 |

**运行方式（定时任务）：**

```bash
# 直接运行（一次性抓取）
python fetch_data.py

# 定时运行（Linux/macOS crontab）
# 每 5 分钟执行一次
*/5 * * * * /usr/bin/python3 /path/to/fetch_data.py >> fetch_data.log 2>&1
```

**输出文件：** `website_data.json`（被 `server.py` 或前端页面使用）

---

### 4.3 翻译系统

**架构概览：**

```
源文本（中文原文）
    ↓ 翻译处理
Markdown 文件（按语言/书籍/章节组织）
    ↓ fetch() 动态加载
前端阅读器渲染
```

**目录结构：**

```
content/                          # 中文原文 + 全文翻译
├── xiyouji/                      # 西游记目录（中文原文）
│   ├── ch_001.md
│   ├── ch_002.md
│   └── ...
│
├── xiyouji-en.md                 # 西游记英文全文
├── honglou-en.md / honglou-es.md
├── daodejing-en.md / daodejing-es.md
├── lunyu-en.md / lunyu-es.md
├── sunzi-en.md / sunzi-es.md
└── glossary.md                   # 翻译术语表（人名/地名/专有名词统一译法）
```

```
books/                            # 分章翻译（适合细粒度阅读）
└── xiyouji/
    └── en/
        ├── ch_001_en.md
        ├── ch_002_en.md
        └── ...
```

**翻译加载逻辑（前端）：**

```javascript
// app.js 中的语言切换逻辑
async function loadChapter(bookId, chapter, lang) {
  const langPath = {
    'zh': `/content/xiyouji/ch_${pad(chapter)}.md`,
    'en': `/content/xiyouji-en/ch_${pad(chapter)}_en.md`,   // 或 /books/xiyouji/en/
    'bilingual': '...'
  }[lang]

  const res = await fetch(langPath)
  const text = await res.text()
  renderChapter(text)  // 替换 DOM，无刷新
}
```

**翻译流程（新增书籍时）：**

1. 将中文原文放入 `content/original/`
2. 预处理：分章节（`.txt` → `ch_XXX.md`）
3. 调用翻译 API（DeepL / Google Translate / GPT）
4. 人工校对术语表 `glossary.md` 中的专有名词
5. 输出英文/西班牙文文件到 `content/` 或 `books/`
6. 前端 `app.js` 添加书籍数据卡片

---

### 4.4 内容管理

**内容分为三层：**

```
Layer 1: 原始文本     → content/original/（.txt 原始 OCR 或下载文件）
Layer 2: 预处理文本  → content/（.md 格式化章节文件）
Layer 3: 翻译文件    → content/-lang.md / books/-lang/（上线用）
```

**维护规则：**
- **中文原文**由版权到期的公版书整理而来，来源：古腾堡 Project Gutenberg、多看阅读等
- **翻译内容**存放在 `~/openclaw/translated-books/` 独立仓库，通过软链接或复制方式集成到主站
- 每次更新翻译后，复制到 `content/` 对应路径即可，无需重新构建

---

## 5. 部署说明

### 5.1 当前部署：Vercel（推荐）

项目已配置 Vercel，部署流程：

```bash
# 1. 安装 Vercel CLI
npm i -g vercel

# 2. 在项目根目录运行
cd /path/to/booksea
vercel

# 3. 按提示选择：
#    - Scope: Cyril0404
#    - Project: website
#    - URL: https://your-project.vercel.app

# 4. 生产环境部署
vercel --prod
```

> Vercel 会自动将项目识别为静态站点，无需额外配置文件。

**自定义域名（如有）：**
在 Vercel Dashboard → Domains 添加域名，DNS 指向 Vercel 分配的 CNAME。

---

### 5.2 腾讯云部署（备选）

如果需要迁移到腾讯云 CVM：

#### 5.2.1 服务器准备

```bash
# 1. 购买腾讯云 CVM（推荐 Ubuntu 22.04 LTS）
# 2. SSH 登录
ssh root@your-server-ip

# 3. 安装 Python 和 Nginx
apt update && apt install -y python3 python3-pip nginx

# 4. 安装依赖
pip3 install efinance gunicorn
```

#### 5.2.2 上传项目文件

```bash
# 方式 A: Git 拉取
git clone https://github.com/Cyril0404/gupiao-helper.git /var/www/booksea

# 方式 B: rsync 同步
rsync -avz --exclude='.git/' ./ root@your-server-ip:/var/www/booksea/
```

#### 5.2.3 Nginx 配置

```nginx
# /etc/nginx/sites-available/booksea
server {
    listen 80;
    server_name your-domain.com;  # 或服务器 IP

    root /var/www/booksea;
    index index.html;

    # 静态资源缓存
    location ~* \.(css|js|md)$ {
        expires 7d;
        add_header Cache-Control "public, immutable";
    }

    # Python API 反向代理（如果使用 FastAPI）
    location /api/ {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
    }

    # SPA 路由 fallback
    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

```bash
ln -s /etc/nginx/sites-available/booksea /etc/nginx/sites-enabled/
nginx -t && systemctl reload nginx
```

#### 5.2.4 HTTPS 配置（Let's Encrypt）

```bash
apt install -y certbot python3-certbot-nginx
certbot --nginx -d your-domain.com
# 自动续期配置好后，证书每 90 天自动更新
```

#### 5.2.5 PM2 守护进程（生产环境）

```bash
# 安装 PM2
npm i -g pm2

# 使用 Gunicorn 运行 FastAPI（如果后续迁移到 FastAPI）
pm2 start "gunicorn -w 4 -k uvicorn.workers.UvicornWorker server:app" --name booksea

# 保存进程列表，开机自启
pm2 save
pm2 startup
```

#### 5.2.6 腾讯云防火墙

在腾讯云控制台 → 安全组开放以下端口：

| 端口 | 协议 | 用途 |
|------|------|------|
| 80 | TCP | HTTP |
| 443 | TCP | HTTPS |
| 22 | TCP | SSH（仅限管理员）|

---

### 5.3 部署检查清单

- [ ] 代码已推送到 Git 远程仓库
- [ ] Vercel 部署成功并验证 https 访问
- [ ] 所有页面链接正常（首页 → 分类 → 阅读页 → 语言切换）
- [ ] 翻译章节 `fetch()` 请求返回 200
- [ ] favicon 和静态资源正常加载
- [ ] 域名 DNS 解析生效（如使用自定义域名）

---

## 6. 开发规范

### 6.1 Git 规范

#### 分支策略

```
main          ← 生产环境代码（受保护分支）
├── dev       ← 开发主分支
├── feature/xxx    ← 功能分支
├── fix/xxx        ← 修复分支
└── docs/xxx       ← 文档分支
```

**命名规范：**
- 功能分支：`feature/translation-batch`
- 修复分支：`fix/chapter-loading-error`
- 文档分支：`docs/deployment-guide`

#### 提交规范（Conventional Commits）

```
<type>(<scope>): <subject>

# 示例
feat(reader): add bilingual paragraph alignment mode
fix(api): handle empty search result edge case
docs(readme): update deployment instructions
refactor(css): extract color variables to :root
chore(deps): install efinance latest version
```

**Type 列表：**

| Type | 含义 |
|------|------|
| `feat` | 新功能 |
| `fix` | Bug 修复 |
| `docs` | 文档更新 |
| `style` | 格式/样式调整（不影响功能）|
| `refactor` | 重构（不修复不改功能）|
| `perf` | 性能优化 |
| `test` | 测试相关 |
| `chore` | 构建/工具/依赖变更 |

#### Git 工作流

```bash
# 1. 从 main 创建功能分支
git checkout -b feature/add-sanguo-translation

# 2. 开发 + 提交（每完成一个小功能提交一次）
git add .
git commit -m "feat(content): add Romance of the Three Kingdoms English chapters 1-10"

# 3. 保持分支与 main 同步
git fetch origin
git rebase origin/main

# 4. 推送远端
git push -u origin feature/add-sanguo-translation

# 5. 提 PR → Code Review → 合并到 main
```

### 6.2 代码风格

#### HTML

- 使用语义化标签（`<nav>`, `<main>`, `<article>`, `<section>`）
- 属性顺序：`id` → `class` → `data-*` → `src`/`href` → `alt`/`title`
- 始终为 `<img>` 添加 `alt` 属性

#### CSS

- 使用 CSS 变量（`var(--variable-name)`）管理颜色和间距
- BEM 命名：`block__element--modifier`（如 `nav-brand__logo--active`）
- 避免 `!important`，优先使用特指度更高的选择器
- 移动端优先响应式：`@media (min-width: 768px)`

#### JavaScript

- 使用 ES6+ 语法（`const`/`let`、`箭头函数、`async/await`）
- 避免 `var`，优先使用 `const`
- 使用模板字符串拼接 HTML
- 错误处理：`try/catch` 包裹所有 `fetch()` 调用
- 注释规范：
  ```javascript
  // ===== 模块说明（顶层）====
  // --- 子模块说明 ---
  // 单行注释（解释复杂逻辑）
  ```

#### Python

- 遵循 PEP 8
- 使用 `logging` 模块替代 `print()` 进行调试
- 异常处理：具体异常类型（`Timeout`/`json.JSONDecodeError`）而非 `except Exception`
- 常量全大写：`MAX_RETRIES = 3`

### 6.3 提交前检查

```bash
# 运行以下检查
[ ] 代码格式正确（HTML/CSS/JS 无明显语法错误）
[ ] 新增内容文件已添加至 Git
[ ] commit message 符合规范
[ ] 本地 `python server.py` 启动正常
[ ] 浏览器测试（至少 Chrome）无 console error
```

---

## 7. 常见问题 FAQ

### Q1: 启动 server.py 报错 `ModuleNotFoundError: No module named 'efinance'`

**原因：** 未安装 efinance 依赖。
**解决：**
```bash
pip install efinance
```

### Q2: 章节内容加载失败（Network Error 或 404）

**原因：** 翻译文件路径不存在，或文件权限问题。
**排查步骤：**
1. 检查 `content/` 或 `books/` 目录下对应文件是否存在
2. 确认 `server.py` 中 `STATIC_DIR` 指向正确目录
3. 检查浏览器 DevTools → Network 面板中的请求路径

### Q3: 股票搜索 API 无响应

**原因：** 东财 API 有频率限制，本地网络无法访问外网。
**解决：**
- 检查网络连接（`curl https://searchapi.eastmoney.com`）
- API 已配置 CORS，生产环境可跨域调用

### Q4: 多语言切换后内容没有更新

**原因：** `localStorage` 中缓存了旧的 `lang` 偏好。
**解决：**
- 打开 DevTools → Application → Local Storage → 清除 `lang` 键
- 或在前端控制台执行：`localStorage.removeItem('lang')`，然后刷新

### Q5: 如何新增一本书？

**步骤：**
1. 在 `js/app.js` 的 `books` 数组中添加书籍对象
2. 将中文原文放入 `content/original/bookname.txt`
3. 分章节处理：`content/bookname/ch_001.md` ~ `ch_XXX.md`
4. 添加翻译文件到 `content/bookname-en.md` 或 `books/bookname/en/`
5. 如有封面图，更新书籍对象中的 `cover` 字段

### Q6: 部署到 Vercel 后页面空白

**原因：** Vercel 识别为纯静态站点，但 SPA 路由（`#/book/1`）需要服务端配置 fallback。
**解决：** Vercel 默认识别 `index.html`，SPA 路由已由 JS 接管，无需额外配置。如仍有问题，检查 `vercel.json`：
```json
{
  "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }]
}
```

### Q7: fetch_data.py 超时无数据

**原因：** efinance 请求超过 15 秒超时。
**解决：** 代码已有超时保护（`signal.alarm(15)`），超时后跳过本次抓取。检查网络或使用代理。

### Q8: 翻译内容与 Git 仓库不同步

**原因：** 翻译内容在独立仓库 `~/openclaw/translated-books/`，与主站分离。
**解决：** 每次翻译更新后，将变更文件复制到主站 `content/` 目录，并提交主站 Git：
```bash
cp -r ~/openclaw/translated-books/孙子兵法-English/* content/sunzi/
git add content/sunzi/
git commit -m "docs(content): sync Sunzi Art of War English translation v2"
```

---

## 8. 联系方式

| 角色 | 负责人 | 职责范围 |
|------|--------|----------|
| 技术负责人 | **员工201** | 系统架构、核心开发、部署 |
| 内容负责人 | **员工202** | 翻译协调、内容管理、术语表 |

**内部协作渠道：** 请通过企业微信/飞书联系对应负责人。

**Git 仓库：**
- GitHub：`https://github.com/Cyril0404/gupiao-helper`
- Gitee：`https://gitee.com/ni-zifan/gupiao-helper`

> ⚠️ 仓库名 `gupiao-helper` 为历史遗留，实际为 Booksea 项目代码。

---

*本手册由策划部生成，最后更新：2026-03-30*
