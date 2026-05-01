---
title: OpenClaw 小龙虾免费接入 ChatGPT Images 2.0 教程：无需 API Key，无需消耗 Token
published: 2026-05-02
description: '这篇教程讲清楚如何在 OpenClaw 中通过 OpenAI Codex OAuth 接入 ChatGPT Images 2.0，对接 openai/gpt-image-2 做图，不单独配置 OpenAI API Key，也不走 OpenAI Platform 的按量 Token 计费。'
image: './openclaw-chatgpt-images-2-cover.svg'
tags: [OpenClaw, ChatGPT, AI绘图, GPT-Image-2, 教程]
category: '免费资源'
draft: false
lang: 'zh_CN'
---

很多人想用 ChatGPT 的图片生成功能，卡住的往往不是提示词，而是接入方式：

- 要不要单独开 OpenAI API？
- 没有 API Key 能不能用？
- 一张图一张图按量扣费，会不会越用越贵？

如果你本来就在用 ChatGPT / Codex 这一套账号体系，其实现在有一条更省事的路：

**直接在 OpenClaw 里走 OpenAI Codex OAuth，把图片生成模型设成 `openai/gpt-image-2`，这样就可以不单独配置 `OPENAI_API_KEY`。**

先说明一个容易误解的点。

本文标题里的“无需消耗 Token”，更准确地说，是：

- 不走 OpenAI Platform API 的按量 Token 计费
- 不需要单独创建和管理 `OPENAI_API_KEY`
- 改为复用 ChatGPT / Codex 的登录授权

这并不等于“完全零成本无限白嫖”。  
前提通常是你已经有可用的 ChatGPT / Codex 订阅授权，或者你的账号已经能正常使用对应的 OAuth 路线。

截至 **2026 年 5 月 2 日**，OpenClaw 官方文档已经明确支持：

- `openai-codex/*` 作为 ChatGPT / Codex OAuth 登录路线
- `image_generate` 工具接入 `openai/gpt-image-2`
- 图片生成既可以走 `OPENAI_API_KEY`，也可以走 `OpenAI Codex OAuth`

所以，如果你的目标是：

- 不折腾 API Key
- 不想走 OpenAI Platform 充值扣费
- 想把聊天和出图统一放到 OpenClaw 里

那这套方案确实值得直接用起来。

---

## 一、这套方案到底适合谁？

这篇教程最适合下面这几类人：

- 已经在用 OpenClaw，想把图片生成一起接进去
- 有 ChatGPT / Codex 可用账号，但不想再单独管理 API Key
- 希望通过命令或代理工作流统一调用 GPT 出图
- 想做“聊天 + 生成图片 + 改图”一体化流程

如果你更在意的是：

- 稳定的企业级 API 调用
- 精细化的按量成本核算
- 后续还要接自己写的脚本、服务端和批处理

那 `OPENAI_API_KEY` 路线依然更标准。  
但如果你是个人使用、内容创作、轻量工作流，这篇讲的 OAuth 路线往往更顺手。

---

## 二、先说结论：OpenClaw 里真正要用的模型名是什么？

很多人搜索时会看到“ChatGPT Images 2.0”“Images 2”“GPT Image 2”这类叫法，但在 OpenClaw 里，实际要配置的模型引用是：

```text
openai/gpt-image-2
```

也就是说：

- 文章标题可以写 ChatGPT Images 2.0，便于大家理解
- 真正落到 OpenClaw 配置里，要写的是 `openai/gpt-image-2`

这一点一定别写错。  
如果你照着旧教程去找 `gpt-image-1` 或其他旧名字，效果和默认行为都可能不一样。

---

## 三、准备工作

开始前，先准备好这几样东西：

### 1. 一台能跑 OpenClaw 的机器

官方当前建议：

- `Node 24` 推荐
- `Node 22.14+` 也支持
- Windows、macOS、Linux 都可以

如果你是 Windows 用户，PowerShell 或 WSL2 都能跑。  
官方文档提到 WSL2 通常更稳定，但原生 Windows 也在支持范围内。

### 2. 已安装 OpenClaw

如果你还没装，最省事的是直接走官方安装脚本。

Windows PowerShell 可用：

```powershell
& ([scriptblock]::Create((iwr -useb https://openclaw.ai/install.ps1)))
```

如果你已经自己管好了 Node，也可以直接：

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

### 3. 可用的 ChatGPT / Codex 登录授权

这是本文方案的关键。  
因为我们不是走 `OPENAI_API_KEY`，而是走 `OpenAI Codex OAuth`。

换句话说，你至少要满足下面一点：

- 已经能正常使用 ChatGPT / Codex 登录
- 愿意在 OpenClaw 里完成一次 OpenAI Codex OAuth 登录

---

## 四、核心思路：不用 API Key，改走 OpenAI Codex OAuth

OpenClaw 对 OpenAI 目前有两条主路线：

### 路线 1：API Key

- 走 `openai/*`
- 典型是 `OPENAI_API_KEY`
- 对应 OpenAI Platform 按量计费

### 路线 2：Codex 订阅 OAuth

- 走 `openai-codex/*`
- 用 ChatGPT / Codex 登录授权
- 不需要单独配置 `OPENAI_API_KEY`

而图片生成这块，OpenClaw 的最新文档已经把它打通了：

- 图片生成默认模型可以设为 `openai/gpt-image-2`
- 当本机已配置 `openai-codex` OAuth 时，图片请求可以通过这个 OAuth 路线转发

这正是本文主题成立的原因。

---

## 五、实操步骤

下面直接进入最有用的部分。

### 第一步：完成 OpenAI Codex OAuth 登录

如果你已经安装好了 OpenClaw，先执行：

```bash
openclaw onboard --auth-choice openai-codex
```

如果你不想重新跑 onboarding，也可以直接登录：

```bash
openclaw models auth login --provider openai-codex
```

完成后，OpenClaw 就会拿到 `openai-codex` 这条认证路线。

建议你顺手验证一下：

```bash
openclaw models list --provider openai-codex
```

如果这里能看到可用模型，说明 OAuth 这一步基本已经打通。

---

### 第二步：把默认聊天模型设好

如果你平时也想在 OpenClaw 里直接聊天，可以顺手设一下默认模型：

```bash
openclaw config set agents.defaults.model.primary openai-codex/gpt-5.4
```

这一步不是图片生成的必需项，但很推荐。  
因为后面你让代理一边理解需求、一边直接出图时，整个工作流会更顺。

---

### 第三步：把图片生成模型设成 `openai/gpt-image-2`

这一句才是本文最关键的配置：

```bash
openclaw config set agents.defaults.imageGenerationModel.primary openai/gpt-image-2
```

如果你希望图片请求更稳一点，也可以顺手加上超时：

```json
{
  "agents": {
    "defaults": {
      "imageGenerationModel": {
        "primary": "openai/gpt-image-2",
        "timeoutMs": 180000
      }
    }
  }
}
```

配置好以后，OpenClaw 在检测到你的 `openai-codex` OAuth 已经存在时，就可以把图片生成请求通过这条授权路线发出去，而不是强制要求你填写 `OPENAI_API_KEY`。

---

### 第四步：确认 `image_generate` 工具已经可用

你可以直接在 OpenClaw 里发一句测试请求，比如：

```text
生成一张适合博客封面的海报：深色科技背景，一只极简风格的小龙虾，带一点未来感，标题区域留白。
```

或者更明确一点：

```text
请用 image_generate 生成一张 16:9 封面图：
- 主题：OpenClaw 接入 ChatGPT Images 2.0
- 风格：科技感、简洁、适合博客头图
- 元素：小龙虾、对话气泡、图片画框
- 颜色：深蓝、青色、少量暖橙点缀
```

如果一切正常，OpenClaw 会自动调用 `image_generate`。  
你不需要自己手动再去拼 OpenAI Images API 请求。

---

## 六、如果你想直接改图，也能这样用

`openai/gpt-image-2` 不只是文生图，也支持带参考图的编辑流程。  
如果你已经有一张图，想局部重绘、换材质、改背景、换风格，也可以继续走这条路线。

比如：

```text
保留主体构图，把封面风格改成更适合技术博客的极简扁平风，提升标题区域对比度，减少杂乱细节。
```

对很多内容创作者来说，这一点比“纯生成”更实用。  
因为真正发布时，经常不是从零开始，而是基于已有封面做二次微调。

---

## 七、为什么这条路比 API Key 路线更适合普通人？

它最大的优点不是“更高级”，而是**更省心**。

### 1. 不用单独创建和保存 `OPENAI_API_KEY`

少一套密钥管理，少一层泄露风险，也少一个环境变量配置步骤。

### 2. 不走 OpenAI Platform 的 API 充值逻辑

如果你本来就是订阅用户，这条路的心理门槛低很多。  
至少你不用先开 API、绑卡、充值、再盯着按量消耗。

### 3. 更适合放进个人工作流

OpenClaw 的好处本来就是把聊天、代理、工具调用、图片生成统一起来。  
如果聊天走一套授权，出图又走另一套 API，管理体验会变碎。

---

## 八、几个容易踩坑的地方

虽然这条路好用，但还是有几个细节值得提前知道。

### 1. “无需 Token”不等于完全无限制

更准确地说，这里是不走 OpenAI Platform API 的按量 Token 计费。  
你的可用性仍然取决于：

- 账号本身是否具备可用的 Codex / ChatGPT OAuth 权限
- OpenClaw 当前版本是否已支持这条图片路由
- 你的本地 OAuth 状态有没有过期

### 2. 有些旧教程还停留在 `gpt-image-1`

截至 2026 年 5 月 2 日，OpenClaw 官方文档里已经明确把 `openai/gpt-image-2` 作为默认 OpenAI 图片生成模型。  
如果你看到旧文章还在教你优先用 `gpt-image-1`，最好重新核对一遍。

### 3. 透明背景并不一定默认走 `gpt-image-2`

OpenClaw 文档提到，透明背景场景下，旧的 `gpt-image-1.5` 仍然有它的意义。  
所以如果你做的是：

- PNG 透明背景图标
- WebP 透明背景素材
- 需要干净抠图边缘的素材

就别死磕默认模型，按场景切换会更稳。

### 4. 登录成功不代表图片一定马上可用

有时聊天模型可用，但图片模型还没被设为默认。  
这时候通常不是认证坏了，而是你少做了这一步：

```bash
openclaw config set agents.defaults.imageGenerationModel.primary openai/gpt-image-2
```

---

## 九、我更建议你这样用

如果你想把这套方案真正变成日常工作流，我更推荐下面这个组合：

### 组合一：写作配图

- 聊天模型：`openai-codex/gpt-5.4`
- 图片模型：`openai/gpt-image-2`
- 用途：文章封面、插图、社媒配图

### 组合二：改图优化

- 先让代理帮你整理提示词
- 再用 `image_generate` 生成第一版
- 最后继续基于参考图改结构、改材质、改风格

### 组合三：博客自动化

- 让 OpenClaw 帮你写标题
- 帮你生成封面提示词
- 直接调用图片生成
- 再回填到文章 frontmatter

这时候 OpenClaw 才真正体现出“小龙虾工作流”的价值。  
它不是单纯替代一个出图网站，而是把出图并进你的内容生产链路。

---

## 十、常见问题

### 1. 没有 `OPENAI_API_KEY`，真的能在 OpenClaw 里做图吗？

可以。  
前提是你已经完成 `openai-codex` 的 OAuth 登录，并把图片生成模型设成 `openai/gpt-image-2`。

### 2. 这是不是完全免费？

不建议这样理解。  
更准确地说，是**不用单独开 OpenAI API Key，也不走 OpenAI Platform 的按量 Token 计费**。是否“免费”，取决于你当前账号本身具备什么订阅和权限。

### 3. 图片生成为什么没反应？

优先检查这几项：

- `openai-codex` 是否登录成功
- `agents.defaults.imageGenerationModel.primary` 是否已经设成 `openai/gpt-image-2`
- 当前 OpenClaw 版本是否足够新

### 4. 我能不能继续用 API Key？

当然可以。  
如果你更重视稳定的 API 工作流、预算管理和程序化调用，API Key 路线依然完全成立。

### 5. 这条路适合长期用吗？

如果你本来就是 ChatGPT / Codex 用户，而且主要是个人内容创作、轻量代理和工作流整合，我觉得很适合。  
因为它真的能少掉很多 API 层面的折腾。

---

## 结语

如果你之前一直觉得“ChatGPT 出图很好用，但 API 接入太麻烦”，那 OpenClaw 现在这条路线，确实把门槛降下来了。

你可以把它理解成：

- 聊天继续走 ChatGPT / Codex 登录
- 出图也复用这套授权
- 不额外管理 API Key
- 不再走 OpenAI Platform 的按量 Token 逻辑

对普通用户来说，这比单纯讨论模型参数更重要。  
因为真正拦住大多数人的，从来不是不会写 prompt，而是配置链路太碎、太杂、太容易劝退。

如果你已经在用 OpenClaw，这篇教程最值得记住的其实只有两句：

```bash
openclaw models auth login --provider openai-codex
openclaw config set agents.defaults.imageGenerationModel.primary openai/gpt-image-2
```

把这两步打通，你基本就已经把“OpenClaw 小龙虾免费接入 ChatGPT Images 2.0”这件事跑起来了。

---

## 参考资料

- [OpenClaw OpenAI Provider 文档](https://docs.openclaw.ai/providers/openai)
- [OpenClaw Image Generation 文档](https://docs.openclaw.ai/tools/image-generation)
- [OpenClaw Install 文档](https://docs.openclaw.ai/install)
