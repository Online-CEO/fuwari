---
title: Astro 搭建个人博客 [Fuwari]
published: 2026-04-15
description: ''
image: ''
tags: [Astro,Fuwari]
category: '生活灵感'
draft: false 
lang: 'zh_CN'
---
## 选择搭建语言
前期纠结了很长时间,再三考虑之后还是选择了Astro

| 特性 | Hexo | Hugo | Astro 🌟 | Next.js |
| --- | --- | --- | --- | --- |
| 底层技术 | Node.js | Go | 专属 .astro 语法 + 任意 UI 框架 | React |
| 构建速度 | 慢（几百篇文章后明显卡顿） | 极快（毫秒级，业界第一） | 快 | 较快 |
| 页面加载速度 | 取决于主题 | 取决于主题 | 极快（默认零 JS 产物） | 快（SSR/SSG，但会有 React 运行时负担） |
| 学习曲线 | 低（改配置即可） | 中（需要适应 Go 模板语法） | 中低（前端友好） | 高（需懂 React 和 App Router） |
| 主题生态 | 极其丰富、成熟 | 极其丰富 | 快速增长中，质量高 | 较少（通常需要自己开发或买模板） |
| 定位 | 传统静态博客生成器 | 传统静态博客生成器 | 现代内容优先 Web 框架 | 全栈 Web 应用框架 |



## Astro 部署 **🍥**Fuwari

<aside>

💡 <a href="https://docs.astro.build/zh-cn/reference/cli-reference" target="_blank" rel="noopener noreferrer">  [Astro CLI 命令] </a> 

</aside>

### 官方使用方法

**使用方法 1**

使用 <a href="https://github.com/saicaca/fuwari/blob/main/docs/README.zh-CN.md" target="_blank" rel="noopener noreferrer"> create-fuwari </a> 在本地初始化项目。

```bash
# npm
npm create fuwari@latest

# yarn
yarn create fuwari

# pnpm
pnpm create fuwari@latest

# bun
bun create fuwari@latest

# deno
deno run -A npm:create-fuwari@latest
```

1. 通过配置文件 `src/config.ts` 自定义博客
2. 执行 `pnpm new-post <filename>` 创建新文章，并在 `src/content/posts/` 目录中编辑
3. 参考[官方指南](https://docs.astro.build/zh-cn/guides/deploy/)将博客部署至 Vercel, Netlify, GitHub Pages 等；部署前需编辑 `astro.config.mjs` 中的站点设置。

**🚀 使用方法 2**

1. 使用此模板[生成新仓库](https://github.com/saicaca/fuwari/generate)或 Fork 此仓库
2. 进行本地开发，Clone 新的仓库，执行 `pnpm install` 和 `pnpm add sharp` 以安装依赖
    - 若未安装 [pnpm](https://pnpm.io/)，执行 `npm install -g pnpm`
3. 通过配置文件 `src/config.ts` 自定义博客
4. 执行 `pnpm new-post <filename>` 创建新文章，并在 `src/content/posts/` 目录中编辑
5. 参考[官方指南](https://docs.astro.build/zh-cn/guides/deploy/)将博客部署至 Vercel, Netlify, GitHub Pages 等；部署前需编辑 `astro.config.mjs` 中的站点设置。

**⚙️ 文章 Frontmatter**

```bash
---
title: My First Blog Post
published: 2023-09-09
description: This is the first post of my new Astro blog.
image: ./cover.jpg
tags: [Foo, Bar]
category: Front-end
draft: false
lang: jp# 仅当文章语言与 `config.ts` 中的网站语言不同时需要设置
---
```

**🧞 指令**

下列指令均需要在项目根目录执行：

| **Command** | **Action** |
| --- | --- |
| `pnpm install` 并 `pnpm add sharp` | 安装依赖 |
| `pnpm dev` | 在 `localhost:4321` 启动本地开发服务器 |
| `pnpm build` | 构建网站至 `./dist/` |
| `pnpm preview` | 本地预览已构建的网站 |
| `pnpm new-post <filename>` | 创建新文章 |
| `pnpm astro ...` | 执行 `astro add`, `astro check` 等指令 |
| `pnpm astro --help` | 显示 Astro CLI 帮助 |

### 部署指南 (Cloudflare)

---

### **🚀 一、部署前准备（5 分钟）**

### **1. 本地项目检查清单**

```bash
# ✅ 确认项目结构（Fuwari 标准）
my-fuwari/
├── astro.config.mjs    # 确保 site: 'https://upsubs.com'
├── src/content/posts/  # 文章放这里
├── src/assets/         # 封面图放这里（用 import 引入）
├── package.json        # 确认有 "build": "astro build"
└── .gitignore          # 确保 node_modules/ dist/ 已忽略

# ✅ 本地构建测试（关键！）
pnpm run build
# 应无报错，生成 dist/ 目录
```

### **2. 推送代码到 GitHub**

```bash
git init
git add .
git commit -m "chore: init fuwari for upsubs.com"
git branch -M main
git remote add origin https://github.com/你的用户名/upsubs.com.git
git push -u origin main
```

---

### **☁️ 二、Cloudflare Pages 创建步骤（图文逻辑版）**

### **Step 1：登录 Cloudflare Dashboard**

→ 左侧菜单 **Workers & Pages** → **Create Application** → **Pages** → **Connect to Git**

### **Step 2：授权并选择仓库**

- 授权 Cloudflare 访问你的 GitHub（建议选「仅限指定仓库」）
- 选择 `upsubs.com` 仓库 → `main` 分支

### **Step 3：构建配置（⚠️ 关键！）**

| 配置项 | 填写值 | 说明 |
| --- | --- | --- |
| **Framework preset** | `Astro` | ✅ 自动识别最优配置 |
| **Build command** | `pnpm run build` | 如用 npm 则填 `npm run build` |
| **Build output directory** | `dist` | Astro 默认输出目录 |
| **Environment variables** | （先留空，后续按需加） | 如 `NODE_VERSION=20` |

> 💡 如果看不到 `pnpm` 选项：Cloudflare Pages 默认支持 `npm`/`yarn`/`pnpm`，只需在 `package.json` 中声明 `packageManager: "pnpm@9.x"` 即可自动识别。
> 

### **Step 4：保存并部署**

- 点击 **Save and Deploy**
- 等待 1~2 分钟，看到 ✅ `Deploy success` 即成功
- 临时域名：`https://upsubs.pages.dev`（先测试，别急绑域名）

---

### **🌐 三、绑定自定义域名 `upsubs.com`（含 www 重定向）**

### **Step 1：在 Pages 项目添加域名**

- 进入项目 → **Custom domains** → **Set up a custom domain**
- 输入 `upsubs.com` → **Continue**
- Cloudflare 会自动检测 DNS，点击 **Activate domain**

### **Step 2：DNS 配置（Cloudflare 自动完成 ✅）**

激活后，Cloudflare 会自动添加两条记录：

```bash
upsubs.com      A     @     (自动，代理状态: Proxied)
upsubs.com      CNAME @     (备用，自动)
```

> ✅ 无需手动改 DNS！Cloudflare Pages 的「自动 DNS 管理」已处理所有 CNAME Flattening 问题。
> 

### **Step 3：添加 `www` 并设置 301 重定向（关键！）**

- 回到 **Custom domains** → **Add domain**
- 输入 `www.upsubs.com` → **Continue**
- **关键一步**：勾选 ✅ **Redirect to primary domain**（自动 301 到 `upsubs.com`）
- 点击 **Activate**

> 🎯 效果：用户访问 `https://www.upsubs.com/xxx` → 自动 301 跳转到 `https://upsubs.com/xxx`，SEO 权重完全归集到裸域。
> 

---

### **⚙️ 四、Fuwari 专属优化配置（Cloudflare 环境）**

### **1. `astro.config.mjs` 最终检查**

```bash
import { defineConfig } from 'astro/config';
import fuwari from 'fuwari'; // 假设主题插件

export default defineConfig({
  site: 'https://upsubs.com', // ✅ 裸域，统一规范
  base: '/',                  // GitHub Pages 才需改，CF Pages 保持 '/'
  integrations: [fuwari()],
  image: {
    domains: ['upsubs.com'],  // 允许生成 OG 图片
    service: {
      entrypoint: 'astro/assets/services/sharp' // Cloudflare 支持 Sharp
    }
  },
  // ✅ 开启 View Transitions（Fuwari 动效核心）
  experimental: {
    viewTransitions: true
  }
});
```

### **2. 环境变量（按需添加）**

在 Pages 项目 → **Settings** → **Environment variables**：

| 变量名 | 值 | 用途 |
| --- | --- | --- |
| `PUBLIC_GISCUS_REPO` | `你的用户名/upsubs.com` | Giscus 评论 |
| `PUBLIC_GISCUS_REPO_ID` | `(从 Giscus 获取)` | 避免硬编码 |
| `NODE_VERSION` | `20` | 锁定 Node 版本，防构建漂移 |

### **3. 缓存策略（可选但推荐）**

Cloudflare 默认缓存静态资源，但可优化：

- 进入 **Rules** → **Page Rules** → **Create Rule**
- URL: `upsubs.com/*`
- Settings:
    - **Cache Level**: `Cache Everything`
    - **Edge Cache TTL**: `1 month`
    - **Browser Cache TTL**: `1 year`
- ✅ 对 `dist/` 下的哈希文件（如 `index.abc123.js`）安全有效

---

### **🔍 五、部署后验证清单（5 分钟）**

```bash
# 1. 检查重定向（终端执行）
curl -I https://www.upsubs.com
# ✅ 应返回：
# HTTP/2 301
# location: https://upsubs.com/

# 2. 检查规范标签
curl -s https://upsubs.com | grep 'rel="canonical"'
# ✅ 应返回：<link rel="canonical" href="https://upsubs.com/" />

# 3. 检查图片优化
# 访问一篇带封面图的文章 → 右键图片 → 复制链接
# ✅ 应为 .webp 或 .avif 格式，且带 ?width=xxx&format=xxx 参数

# 4. SEO 工具
- Google Search Console：提交 `https://upsubs.com/sitemap-index.xml`
- Cloudflare Web Analytics：在 Pages 项目开启，免费监控真实用户性能

# 5. 移动端/暗色模式
用手机访问 + 切换系统深色模式，确认：
- 页面过渡动画流畅（View Transitions）
- 无布局偏移（CLS ≈ 0）
- 暗色模式切换无闪烁
```

---

### **🚨 常见坑点 & 解决方案**

| 问题 | 原因 | 解法 |
| --- | --- | --- |
| 构建失败 `pnpm: command not found` | Cloudflare 默认用 npm | 在 `package.json` 加 `"packageManager": "pnpm@9.12.0"` |
| 图片 404 / OG 卡片裂图 | `image` 未用 `import` 引入 | 封面图必须 `import cover from '@/assets/xxx.webp'` |
| 评论不显示（Giscus） | `repo` 未公开 / `origin` 未配 | Giscus 设置页 `Allowed origins` 加 `https://upsubs.com` |
| 暗色模式闪烁 | `localStorage` 读取晚于渲染 | 在 `src/layouts/Layout.astro` 头部加内联 `<script>` 提前应用主题 |
| RSS 订阅 404 | `@astrojs/rss` 未安装 | `pnpm add @astrojs/rss` 并确认 `src/pages/rss.xml.js` 存在 |

---

### **🎁 附：一键部署加速脚本（可选）**

```bash
# 创建 deploy.sh（本地执行）
#!/bin/bash
echo "🚀 Building Fuwari for upsubs.com..."
pnpm run build

echo "✅ Build success! Now:"
echo "1. Push to GitHub: git push"
echo "2. Cloudflare Pages will auto-deploy in ~60s"
echo "3. Visit: https://upsubs.com"

# 赋予执行权限 + 运行
chmod +x deploy.sh && ./deploy.sh
```