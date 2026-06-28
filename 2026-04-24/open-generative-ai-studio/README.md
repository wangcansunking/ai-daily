---
title: "227 个模型塞进一个 Electron：Higgsfield 开源平替冲到 Trending #3"
date: 2026-04-24
tags: [Open-Generative-AI, Electron, AIGC, Higgsfield, GitHub Trending]
cover: open-gen-ai-article-cover.png
description: "Anil-matcha/Open-Generative-AI 把 Higgsfield/Freepik/Krea/OpenArt 4 家付费工具核心能力塞进一个 MIT Electron 应用，227 条 model entry / 224 unique 模型。GitHub Trending #3 / 7,578 ⭐ / 单日 +847。截至 2026-04-24 国内 36氪/量子位/机器之心/虎嗅 未检索到中文报道，auto-research arbitrage 选题。"
---

# 227 个模型塞进一个 Electron：Higgsfield 开源平替冲到 Trending #3

![Open-Generative-AI 文章封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-24/open-generative-ai-studio/open-gen-ai-article-cover.png)

> **7,578 个 star、一天新增 847 个、今日 GitHub Trending #3**。`Anil-matcha/Open-Generative-AI` 在 2026-04-24 把 Higgsfield、Freepik、Krea、OpenArt 四家付费工具的核心功能塞进一个 MIT 协议的 Electron 应用——图生图 52、视频 43、图编辑 57、图到视频 62、视频改视频 4、口型同步 9，227 条 model entry，跨类别去重后 224 个 unique 模型。下面拆开讲——什么能用、什么有坑、中国用户要注意什么。

---

## 仓库实况：一天涨 847 star 的冷启动

2026-04-24 20:30 UTC 的实时数据（全部 `gh api repos/Anil-matcha/Open-Generative-AI` 实查）：

| 指标 | 数值 |
|---|---|
| Stars | 7,578 |
| Forks | 1,409 |
| Watchers（subscribers）| 63 |
| 今日新增 stars | 847（GitHub Trending Daily 排名 #3） |
| 仓库 updated_at | 2026-04-24 20:30 UTC |
| 最新 commit pushed_at | 2026-04-24 09:35 UTC |
| 协议 | README 声明 MIT（仓库根目录未放 LICENSE 文件，GitHub 未能自动识别） |

三个关键差异点：

- **第一个把图 / 视频 / 口型同步 / 影棚控制全塞进一个前端的开源项目**。ComfyUI 做类似的事，但 ComfyUI 是节点图，Open-Generative-AI 是传统 GUI，面向非技术用户
- **227 个模型 entry 是实测**。我下载了仓库里的 `packages/studio/src/models.js`（265 KB），用 brace-depth 解析法数了一遍：`t2iModels` 52、`t2vModels` 43、`i2iModels` 57、`i2vModels` 62、`v2vModels` 4、`lipsyncModels` 9，合计 227 条。其中 `flux-pulid`、`flux-redux`、`qwen-text-to-image-2512` 三个 id 同时出现在 t2i 和 i2i 数组里，去重后 **224 个 unique id**
- **作者是老手**。Anil Chandra Naidu Matcha 项目最早叫 `Open-Higgsfield-AI`（Medium 文章 URL 里还留着这个 slug），后来改名 `Open-Generative-AI` 并扩展模型覆盖范围

![Open-Generative-AI GitHub 仓库 banner](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-24/open-generative-ai-studio/open-gen-ai-banner.png)

---

## 五个 Studio 拆开讲：它到底能干什么

README 把整套 UI 分成 5 个 Studio。我按"上手第一眼能看到什么"重新排了一遍：

![Open-Generative-AI Studio 演示界面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-24/open-generative-ai-studio/open-gen-ai-studio-demo.webp)

### 1. Image Studio：52 个 t2i × 57 个 i2i，一键切模型

进去是熟悉的 ChatGPT 风格对话框。右上角能切 Flux Dev、Nano Banana 2、Seedream 5.0、Ideogram v3、Midjourney v7、GPT-4o、SDXL 等 52 个 text-to-image 模型。

**更狠的是 Image-to-Image 模块**。README 原文 "feed up to 14 reference images into compatible models"——塞 14 张参考图进去，让它生成"14 张人脸的融合"或"14 个建筑元素风格的新图"。Flux Kontext Pro、GPT-4o Edit、Seededit v3 都支持多图输入。

本地 ComfyUI + Stable Diffusion 栈要装好几个插件才能做到同样的事。

### 2. Video Studio：43 个 t2v + 62 个 i2v + 4 个 v2v

文字转视频列表：Kling v3、Sora 2、Veo 3、Wan 2.6、Seedance 2.0、Hailuo 2.3、Runway Gen-3——国内视频圈常提的头部模型基本全了。

图到视频 62 个，直接对应"生成图动起来"的典型工作流。付费工具一般只挑 2-3 个模型独家卖，这里全放。

### 3. Cinema Studio：这个才是差异化所在

前两个模块说白了是"多模型聚合前端"，技术上不难。**Cinema Studio 才是项目的灵魂**——作者把商业片场"选镜头、选光圈、选胶片"那套动作搬进了 UI。

![Cinema Studio 镜头预设：Compact Anamorphic](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-24/open-generative-ai-studio/open-gen-ai-lens-compact-anamorphic.webp)

Cinema Studio 的每个镜头预设背后，是一段塞给模型的 prompt 前缀。举几个仓库里的实例：

- **Compact Anamorphic**（紧凑变形镜头）：电影感横向拉伸，典型商业大片观感
- **70s Cinema Prime**（70 年代定焦胶片）：复古颗粒感 + 暖色偏移
- **Classic 16mm Film**（16 毫米经典胶片）：小成本独立电影质感
- **Swirl Bokeh Portrait**（旋转焦外人像）：老胶片镜头的旋转虚化
- **Grand Format 70mm Film**（70 毫米大画幅）：诺兰式 IMAX 史诗质感

![Cinema Studio 光圈预设：f/1.4](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-24/open-generative-ai-studio/open-gen-ai-aperture-f14.webp)

光圈预设 f/1.4、f/4、f/11 三档。作者在 Medium 写过为什么要这么设计——他想把"写 prompt"换成"当导演"。你不再跟模型说 "a portrait with shallow depth of field, soft bokeh"，而是点一下 "f/1.4" 预设，UI 把对应的 prompt token 帮你塞进去。

作者 Medium 原话：

> "With this project, we shifted the mental model from Writing to Directing."

**从 Writing 换成 Directing**——这句话放到 2026 年图像模型已经足够强的当下，比任何 prompt engineering 教程都切题。

### 4. Lip Sync Studio：9 个模型做音驱动口型

一张静态人像 + 一段音频，让图开口讲话。9 个模型覆盖主流方案：Infinite Talk I2V/V2V、Wan 2.2 Speech to Video、LTX 2.3/2 19B Lipsync、Sync、LatentSync、Creatify、Veed。对应小红书 / 抖音近期爆火的"老照片开口说话"那类短视频。

### 5. Workflow Studio：节点图拼多模型管线

ComfyUI 逻辑的轻量版——拖拽节点把几个模型串起来跑一条完整管线。举例：`Flux Dev 生图 → GPT-4o Edit 二次调色 → Kling v2.1 I2V 动起来 → LTX Lipsync 对口型`，一键跑通。

---

## 模型生态一张表看清

![Open-Generative-AI 模型类别与数量](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-24/open-generative-ai-studio/open-gen-ai-model-breakdown.png)

数字出处：我本地 curl 了 `packages/studio/src/models.js`，用 brace-depth 解析数了每个 `export const xxxModels = [` 数组里的顶层对象数量。合计 **227 条 model entry**，其中 `flux-pulid`、`flux-redux`、`qwen-text-to-image-2512` 三个 id 跨 t2i 和 i2i 重复出现，去重后 **224 个 unique id**。

README 宣传 "200+ models"。实测 entry 数 227 / unique 224 都在 200 出头——**"200+" 说法成立，但并没有夸张到我原本以为的接近 300**。

---

## 三种部署方式的性价比

国内用户最关心的部分。README 给了三种运行方式，成本、门槛、天花板完全不同：

### 方式 A：托管版（dev.muapi.ai/open-generative-ai）

- **动作**：浏览器注册一个 Muapi 账号即用
- **成本**：**不免费**。Muapi 按次计费。公开价目参考：Heygen 视频翻译 $0.05/秒、SD 2.0 视频去水印 $0.013/秒起（最低 $0.065）。不同模型单独标价，注册后账户余额 "$0"，需要充值
- **优势**：零部署，支持全部 227 个模型
- **坑**：支付要外币卡；国内直连速度看落地

### 方式 B：桌面应用（macOS / Windows / Linux）

- **动作**：下载官方 DMG / EXE 一键装
- **成本**：**应用免费，但仍走 Muapi API**。桌面版把网页壳打进 Electron，模型调用还是过 Muapi 网关收钱
- **特殊权限**：macOS 装完要手动 `xattr -cr` 去掉 Gatekeeper 隔离；Windows 会弹 SmartScreen，点"仍然运行"；Ubuntu 24.04+ 要把 `apparmor_restrict_unprivileged_userns` 关掉
- **优势**：支持"本地生成"模式——内嵌 stable-diffusion.cpp，在 Apple Silicon 上用 Metal GPU 直接跑小模型，**完全不走 Muapi，不花钱**

本地模式可用的模型（直接从 README 摘）：

| 本地模型 | 权重大小 | 特性 |
|---|---|---|
| Z-Image Turbo | 2.5 GB + 2.7 GB 附属 | 8 步快速采样 |
| Z-Image Base | 3.5 GB + 2.7 GB 附属 | 50 步高质量 |
| Dreamshaper 8 | 2.1 GB | SD 1.5 底座 |
| Realistic Vision v5.1 | 2.1 GB | 照片写实 |
| Anything v5 | 2.1 GB | 动漫 / 插画 |
| SDXL Base 1.0 | 6.9 GB | 高分辨率 |

最低硬件门槛：**16 GB RAM**（README 原话 "Recommended: 16 GB RAM for Z-Image models (7.4 GB weights + 2.4 GB compute buffer)"）。M 系列 Mac 有 Metal 加速；Intel Mac / Windows 可以纯 CPU 跑。

但作者在 README 里直说 "The system may slow during generation — the process uses all available CPU cores while running"——翻译成人话就是**跑图时别指望同时做别的事**。

### 方式 C：本地 npm 自部署

从仓库页面 clone 到本地后，进到项目目录跑 `npm install` 和 `npm run dev` 即可。默认监听 `http://localhost:3000`，首次启动会弹 Muapi API key 输入框。

- **成本**：按 Muapi 计费（本地开发环境也要 Muapi key）
- **场景**：二开 / 自定义品牌 / 企业内网套壳
- **门槛**：Node.js v18+

---

## 技术栈和"Universal Adapter"的价值

仓库根目录看，技术栈标准前端组合：

- **Next.js 14**（App Router）+ **React 18** + **Tailwind CSS v3**
- **npm workspaces** monorepo，核心逻辑抽到 `packages/studio`
- **Electron** 做桌面包装（`electron/` 和 `afterPack.js` 能看到）
- **stable-diffusion.cpp** 做本地推理，Metal / CPU 后端

作者最巧的设计不在前端，而在 `packages/studio/src/muapi.js`。他把跟 Muapi 打交道的细节抽成了一个"通用适配器"。上层 UI 只说"我要一张高质量图""我要一个 10 秒视频"，具体怎么映射到 Flux / Kling / Sora 的不同 API 格式，muapi.js 去翻译。

副产品是：**只要 Muapi 支持的新模型，UI 几乎不用改就能接入**。这也解释了为什么项目从早期 Open-Higgsfield-AI 时的"一把入门模型"能扩展到如今 227 条 entry——Anil 大部分时间不是在对接模型，而是在同步 Muapi 的模型目录到 `models.js` 数组。

这是项目最容易被低估的价值。**它本质上不是一个模型集合，而是一个 UI 层 + 标准化接入约定**。国内类似的想法（HuggingFace Space、ModelScope 的 demo 页面）都没做到这种程度的统一 UX。

---

## 国内用户上手清单（实操）

按我的经验，中国开发者从零到能跑一次生成，大致这几步：

1. **先定模式**：只是玩票 / 跑本地 → 下 macOS 桌面版走 stable-diffusion.cpp；想用 Kling / Sora 等云端高端模型 → 必须接受 Muapi 计费
2. **Muapi 注册**：muapi.ai 邮箱注册，账户页充值。**需要外币信用卡 / PayPal**——国内用户最大摩擦点
3. **API key 粘贴**：桌面版首次启动会弹窗；Web 版账户页拷贝
4. **本地模式选模型**：16 GB Mac 先跑 Z-Image Turbo（2.5 GB 权重，8 步采样）测通链路；SDXL 那 6.9 GB 看内存压力再上
5. **隐私边界**：本地模式数据不出设备；Muapi 模式所有 prompt、生成的图都会过 Muapi 服务器——敏感内容慎用

### 必须讲清楚的合规和版权问题

README 最抓眼球的一句是 "no content filters, no prompt rejections, and no guardrails"——**无审查**。英文社区当卖点，对国内用户是坑：

- **中国大陆境内使用**需遵守《生成式 AI 服务管理暂行办法》，内容安全过滤不是可选项
- "无审查"不等于"不违法"——用户要为输出负责
- Muapi 本身是美国托管的 SaaS，数据跨境
- Midjourney v7 / Sora 2 / Veo 3 这些模型本身有严格**服务条款**，用第三方聚合器调属于灰色区域——Midjourney ToS 明确禁止 automation 和未授权的第三方脚本调用

**结论写死**：国内用户，这个项目只适合个人技术研究 + 本地模型玩票，**不建议**商用场景直接上（版权 / 合规 / API 条款都有风险）。

---

## 和 ComfyUI、Fooocus、AUTOMATIC1111 的定位差异

国内用户上手前常问——"这不就是 ComfyUI 吗？"。不是。几个维度区别很清楚：

| 维度 | Open-Generative-AI | ComfyUI | Fooocus | A1111 WebUI |
|---|---|---|---|---|
| 界面形态 | 传统 GUI（类 ChatGPT 对话 + 表单）| 节点图 | 简化 GUI | 滚动长表单 |
| 模型调用 | 优先 Muapi 云 API，可选本地推理 | 纯本地推理 | 纯本地推理 | 纯本地推理 |
| 视频 / 口型同步 | ✅ 一把全包 | 要装 custom nodes | ❌ 纯生图 | 要装扩展 |
| 云端高端模型（Kling/Sora/Veo） | ✅ 原生接 | 需要第三方 node | ❌ | ❌ |
| 本地免费门槛 | 需 16 GB RAM | 视模型而定（8-32 GB）| 8 GB RAM 可玩 | 6-12 GB RAM |
| 上手难度 | 低 | 中高 | 低 | 中 |

**结论直接**：

- 想跑"云端最先进的生图生视频模型"又懒得自己接 API → Open-Generative-AI
- 想完全本地 + 愿意折腾节点图 → ComfyUI
- 想低门槛玩本地生图、不要太多参数 → Fooocus
- 想要传统"左边参数右边图"的熟悉感 → A1111

---

## 项目的槽点：不该藏着掖着

几条该挂出来的：

1. **"完全免费"宣传话术有误导**。README 和 Medium 文章反复强调 "no subscription fees"，但真正让项目跑通的 Muapi 网关是按次收费。UI 层免费，工作流跑一趟不免费
2. **License 文件缺失**。README 声明 MIT，仓库根目录却没有 LICENSE 文件，GitHub 自动识别为 "No license"（`gh api` 返回 `license: None`）。这会让某些企业法务拒绝引入——7.5k star 项目犯这种低级失误不该
3. **本地模式模型数量有限**。云端 227 个，本地只有 6 个（Z-Image 系列 + Dreamshaper + Realistic Vision + Anything + SDXL）。"开源自部署"的话术在本地部分打折
4. **无审查是卖点也是雷**。HN 评论区已有人指出：无过滤包装成 "free speech" 的项目长期会被 Hugging Face / GitHub 平台 TOS 盯上
5. **单一维护者风险**。Commit 历史大部分是 Anil 一个人。项目能不能长期活下去，取决于 Muapi 这家公司给他多少空间

---

## 我的建议

分场景下结论：

- **个人 AI 玩家**（Mac 16 GB 以上）：下桌面版用本地模式，跑 Z-Image Turbo + Dreamshaper，免费够玩
- **内容创作者 / 小团队**：能接受按次计费（按每天生成量自己估算 Muapi 余额）→ 注册账号，用 Cinema Studio 做正经视频素材，把原本交给 Higgsfield 等付费工具订阅的钱省下来
- **企业 / 商用**：**别直接用**。先把 `packages/studio/src/muapi.js` 拆出来替换成自家云厂商（Azure / 华为云 / 阿里云百炼）的 API 代理，加一套内容审核 hook，再做合规评估
- **技术研究**：项目最该读的不是 UI，是 `muapi.js` 的适配器模式——**比 LangChain 的 provider abstraction 干净多了**
- **国内做聚合平台的**：这是现成的抄作业对象——五个 Studio 的 UX、`models.js` 的 schema、Cinema 的预设做法，三天复刻一版

---

## 写在最后

7,578 个 star 里，多数不是投给功能的，是投给"一个人、一个月、把 200+ 模型接进一个 Electron 应用"这件事本身。

它证明了 2026 年做"AI 应用"的生态位已经下沉到——一个印度开发者 + 一个 Muapi API + 一个周末，就能把一个付费工具的核心功能复刻到开源世界。

**真正该追问的是**：国内什么时候有自己的 "Muapi"？豆包 / 通义 / 腾讯混元 / 智谱的 API，能不能被一个统一的适配器层抹平，让下一个 Anil Matcha 写出中国版的 Open-Generative-AI？

这道题到现在还没有标准答案。
