---
title: "谷歌给 8 个办公应用接上 Agent，Claude 开始追着 PR 修 | AI 日报 | 2026-11-27"
date: "2026-11-27"
weekday: "星期五"
slug: "ai-daily-2026-11-27"
category: "ai-daily"
description: "谷歌 Workspace 开放 Agent 接入，Claude Ship 跟进代码提交，另有国产旗舰进度与 5 个 GitHub 热榜项目。"
cover: "https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/ai-daily-2026-11-27.png"
batch: "morning"
cover_style: "magazine-collage"
---

# 谷歌给 8 个办公应用接上 Agent，Claude 开始追着 PR 修 | AI 日报 | 2026-11-27

谷歌给 Gmail、Drive 等 8 个办公应用开了 Agent 专用入口，Anthropic 则把 AI 编程的触角伸到了提交代码之后。两条消息都指向同一件事：AI 能写出答案之后，产品开始争夺下一步的执行权。

## 要点速览

- Workspace 的远程 MCP 与本地 CLI 同时转正，明年 2 月开始执行 Agent 接入新规。
- Claude Ship 把审查、修复和批准串成一条线，现有分支规则仍决定谁能合并。
- 路透称智谱下一代旗舰进度落后，月之暗面的发布周期也可能被算力拉长。
- GitHub 语言日榜选出 5 个项目，工具注册表 treg 单日新增 557 星。

## 大厂与前沿

11 月 25 至 26 日的新进展之外，还有几项本周启动的产品更新。

### 谷歌给 8 个办公应用换上 Agent 接口

Google Workspace 在 11 月 24 日宣布远程 MCP 和官方 CLI 正式可用，覆盖 Gmail、Drive、Docs、Sheets、Slides、Calendar、Chat 和 Tasks。云端助手可以调远程工具，本地终端助手可以走 CLI。谷歌计划从 2027 年 2 月 1 日起执行面向 Agent 的接入要求。

一个实际变化是，助理可以先从邮件和网盘提取材料，再准备下一步操作。遇到共享文件等敏感动作，服务端可以要求用户确认。过去每家应用自行拼接权限与确认体验，现在谷歌开始把这些环节做进工具入口。

![Google Calendar 的 Agent 创建会议确认示意](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/workspace-calendar.png)

*图片来源：Google Workspace 开发者文档，查阅于 2026-11-27。*

### Claude Ship 接过 PR 之后的三件事

Anthropic 在 11 月 23 日推出 Claude Ship 测试版。Code Review 提交问题，PR Steward 跟进失败的 CI、冲突与反馈，Claude Approvals 则按仓库策略判断能否给出批准。前者已正式可用，后两项仍在测试。

![Claude Code Review 在拉取请求中给出审查意见](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/ship-review.png)

*图片来源：Anthropic Claude Ship 发布页，2026-11-23。*

有了这套流程，开发者提交改动后可以先去做下一件事，例行修复留给机器人。高风险改动依旧能通过仓库规则交给指定人员。仓库级功能面向连接 GitHub 的 Claude Team 和 Enterprise，使用量随审查与修复的工作量增加。

![PR Steward 在拉取请求内反馈修复](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/ship-steward.png)

*图片来源：Anthropic Claude Ship 发布页，2026-11-23。*

### Gemini 4 的数学 Agent 一次公开 6 篇论文

Google Research 在 11 月 25 日的一次提交中放出 6 篇署名 Cogentic 的论文，题目横跨整数分解、博弈和图论。Cogentic 是谷歌构建的数学推导系统，这批材料使用 Gemini 4 作为底模。

最引人注意的是整数分解论文提出的复杂度结论。仓库记录能证明论文已公开，结论还需要数学界逐步复核。六篇稿件同时出现，意味着同行接下来面对的是一批可读、可讨论的证明，而非只有模型答题分数。

### 270 亿参数塞进 5.9 GB

PrismML 在 11 月 24 日发布 Ternary Bonsai 2.5 27B，给出的权重大小是 5.9 GB。它用三值表示压缩权重，以 Apache 2.0 开放，目标是本地代码和知识任务。对一台内存有限的电脑，模型文件变小意味着试跑大参数模型的门槛下降。

权重体积只是一部分。长提示词、生成缓存和运行时也占空间，5.9 GB 无法直接换算成显存要求。愿意动手的人可以先拿一个短的代码修改任务试速度，再把上下文加到自己的真实工作量。

## 🇨🇳 国内 AI 观察

### 智谱旗舰进度落后，Kimi 的半年节奏面临变数

路透 11 月 26 日援引知情人士称，智谱下一代旗舰 GLM-5.5 进度落后，时间可能推到 2027 年一季度或更晚。报道还称，如果失去海外算力，月之暗面的主要版本间隔可能从约半年拉长到一年或以上。两个时间点都是报道中的估计。

旗舰训练需要长时间占用成组的加速卡。训练能否排上机器，会进一步影响后训练、评测和上线窗口。对依赖模型 API 的团队，现有版本何时能被更强版本替代，会直接影响应用的升级计划。

### 腾讯 Hy4 Flash 上了 Fireworks，llama.cpp 也跟进

Fireworks 在 11 月 25 日上线腾讯开放权重模型 Hy4 Flash。对已经通过 Fireworks 部署应用的团队，变化是可以在同一服务内试用腾讯模型，省去先下载权重、再搭推理服务的工作。

Fireworks 自测的 CorporateBench 企业任务中，Hy4 Flash 得 75.9%，Claude Opus 5.5 得 75.4%。这组单项得分接近。换成自己的任务，工具调用次数和返工量还会改变总成本。

开源端也有动静。llama.cpp 在 11 月 26 日的 b12755 预发布版加入 Hy4 Flash 支持，腾讯也提供了 AngelSlim 的 GGUF 文件。低位版本仍有百余 GB，适合从手头机器的内存和存储出发选方案。

![Fireworks 对比 Hy4 Flash 与 Claude Opus 的任务表现和开销](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/hy4.png)

*图片来源：Fireworks AI Hy4 Flash 发布页，2026-11-25。*

## 📦 GitHub Trending

以下来自 11 月 27 日北京时间早间的 GitHub 官方语言日榜。新增为榜面标注，总星为抓取时读数。

| 项目 | 日榜位置与星标 | 有趣在哪 |
| --- | --- | --- |
| treg | Python #17，9,542 星，+557 | 面向 Agent 的工具注册表，适合从多个工具入口里找到所需能力。 |
| Search | Swift #2，4,205 星，+87 | 面向 macOS 的轻量 WebKit 浏览器，给想体验小体积原生浏览器的人一个新选择。 |
| mygo | Go #12，6,918 星，+52 | 用 Go 做桌面程序，界面可以走原生组件或 Web 前端。 |
| mini-swe-agent | Python #12，10,120 星，+45 | 把代码 Agent 缩到便于阅读的实现，适合拆解从命令输出到下一步动作的循环。 |
| Bend | TypeScript #5，25,919 星，+39 | Bend 2 把证明放进编程流程，适合探索怎样给 AI 生成的实现加上机器可检查的约束。 |

今天的两条主线，一条把 Agent 接进办公室和代码仓库，另一条把模型训练与运行拉回机器、内存和时间这些硬约束。

## 来源

- [Google Workspace 官方发布](https://workspaceupdates.googleblog.com/2026/11/mcp-servers-cli-standardized-usage-tiers-now-available.html)
- [Anthropic Claude Ship](https://claude.com/resources/articles/introducing-claude-ship)
- [Google Research · Cogentic 论文提交](https://github.com/google-research/google-research/tree/4a7bc8ab0bc770e71a259b7966c500496b99010a/cogentic_research_papers)
- [PrismML · Ternary Bonsai 2.5 27B](https://prismml.com/news/prismml-launches-ternary-bonsai-2-5-27b)
- [路透社关于智谱及月之暗面的报道（Daily Maverick 转载）](https://www.dailymaverick.co.za/article/2026-11-26-us-chip-curbs-distillation-crackdown-threaten-chinese-ai-model-development-sources-say/)
- [Fireworks AI · Hy4 Flash](https://fireworks.ai/blog/hy4-flash-on-fireworks)
- [llama.cpp b12755](https://github.com/ggml-org/llama.cpp/releases/tag/b12755)
- [腾讯 AngelSlim · Hy4 Flash GGUF](https://huggingface.co/AngelSlim/Hy4-flash-GGUF/tree/main)
- [GitHub Python 语言日榜](https://github.com/trending/python?since=daily)
- [GitHub Swift 语言日榜](https://github.com/trending/swift?since=daily)
- [GitHub Go 语言日榜](https://github.com/trending/go?since=daily)
- [GitHub TypeScript 语言日榜](https://github.com/trending/typescript?since=daily)
- [superdesigndev/treg](https://github.com/superdesigndev/treg)
- [driceroland/Search](https://github.com/driceroland/Search)
- [egoist/mygo](https://github.com/egoist/mygo)
- [SWE-agent/mini-swe-agent](https://github.com/SWE-agent/mini-swe-agent)
- [bendlang/bend](https://github.com/bendlang/bend)
