---
title: "英伟达据报暂停向东南亚4国发运AI服务器 | AI 日报 | 2026-11-28"
date: "2026-11-28"
weekday: "星期六"
slug: "ai-daily-2026-11-28"
category: "ai-daily"
description: "英伟达出货踩刹车，Claude API 换上 Rust，另有黑五代理购物、国内 AI 观察及 5 个 GitHub 热榜。"
cover: "https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/ai-daily-2026-11-28.png"
batch: "morning"
cover_style: "magazine-collage"
---

# 英伟达据报暂停向东南亚4国发运AI服务器 | AI 日报 | 2026-11-28

英伟达的 AI 服务器，据报在发往东南亚四国的路上踩了刹车。另一边，Anthropic 把 Claude 的生产接口从 Python 搬到 Rust，交出的数字是单请求 CPU 用量约降七成。一个消息关乎谁能租到算力，另一个关乎同一套算力能服务多少请求。

## 要点速览

- 英伟达据报要求整机伙伴暂停向马来西亚、新加坡、泰国和印度尼西亚发货，恢复时间未公开。
- Anthropic 让新旧 API 并行处理真实流量，换来约 20% 的首 token 中位延迟降幅（Claude Code 请求样本）。
- 黑五购物入口迎来压力测试，亚马逊和 Meta 据报仍在谈 Muse 接入。
- 国产观察聚焦智谱、月之暗面的旗舰排期，以及智源与逆矩阵的物理世界模型预览。

## 大厂头条

### 英伟达服务器据报暂停发往东南亚四国

《华尔街日报》11 月 26 日报道称，英伟达要求戴尔、超微电脑等整机伙伴暂停向四个东南亚市场发运相关 AI 服务器。停运范围和恢复时间仍有待披露。美国商务部的先进芯片许可规则还列有巴西，报道所指的停运覆盖东南亚四国。

这轮影响会传到机房的施工表上。即便供电、机柜位置和液冷管路已经就绪，整机延期也会挤压集群联调和客户上线窗口；至于已部署设备与现有算力租约，仍要视合同和适用规则而定。

![Vera Rubin NVL72 实验室机柜资料照](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/rubin-rack.jpg)

*图片来源：Tom’s Hardware 英伟达实验室探访，2026-07-21。*

### Claude 的生产 API 换成 Rust，首 token 更早到

Anthropic 11 月 25 日披露，Messages API 换用 Rust 后，处理单个请求消耗的 CPU 约降 70%，内存降逾 80%，Claude Code 客户请求样本的首 token 中位延迟约降 20%。这些是接口层指标，和模型生成完整答案的耗时是两个量。

最有意思的是迁移开局：测试约有 98% 通过，真实流量里的输入路径却只约四分之一匹配。团队把生产 Python 服务留作参照，让 Rust 在旁边处理同一输入，找出经常被测试漏掉的细小差别。

![Anthropic 公布的 CPU、内存与首 token 延迟变化](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/claude-speed.png)

*图片来源：Anthropic 工程博客，2026-11-25。*

### 亚马逊封了 Muse 两个月，黑五前又坐下来谈

据《财富》11 月 25 日报道，亚马逊和 Meta 正商谈让 Muse 接入亚马逊，搜索结果呈现和广告是谈判焦点。Meta 已和沃尔玛等零售商合作，亚马逊尚未宣布恢复接入。

黑五把这个问题缩成了一次点击：当用户问“挑一台能接游戏机的电视”，谁决定屏幕上的三款商品？《财富》27 日报道的 Visa 模拟中，22 个代理参与测试，只有 57% 的购物任务买到合适商品或给出合适建议。推荐与代付之间还有很长一段路。

### 苹果下架 MiniMax 旗下 Talkie，安卓端仍可下载

调查新闻机构 TBIJ 11 月 27 日称，苹果已在全球 App Store 下架 Talkie，并将原因归于应用内的性内容。报道还称英国和澳大利亚的网页聊天也已撤下；Google Play 仍可下载，页面显示累计下载超过 5000 万。下载量和活跃用户规模应分别看待。

同一个陪伴机器人，在不同平台面对的是不同分发入口。对 MiniMax 而言，iOS 下架首先切断了通过 App Store 获取新用户的路径，安卓端继续可用让这次变动有了鲜明对照。

![TBIJ 的 Talkie 调查报道配图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/talkie.jpg)

*图片来源：TBIJ 调查报道／CFOTO（Future Publishing via Getty），2026-11-27。*

## 🇨🇳 国内 AI 观察

### 智谱与月之暗面的下一代旗舰排期受到关注

路透 11 月 26 日援引知情人士称，智谱 GLM-5.5 的开发慢于原计划；若月之暗面失去海外算力，旗舰迭代间隔可能从约半年拉长到一年或更久。这里谈的是媒体披露的内部排期与条件性预期，两款产品的新发布日期仍未确定。

据快科技报道，智谱高管杨昊翊 26 日在朋友圈否认当天下午流传的爆料，未公布 GLM-5.5 的正式发布日期。

已经能用的产品有明确坐标：智谱 11 月 23 日发布 GLM-5.4，首次在其旗舰模型上采用 Loop Transformer，重点推进视觉能力和泛编程。短期做模型选型，当前版本支持的输入与交付方式，比下一代参数传闻更有用。

### 悟界 Physis 把世界模型往真实机器人上推

智源研究院在 11 月 25 日报道中披露，智源与逆矩阵于 24 日的开放日发布了悟界·Physis-v1.0-preview。其目标是把来自多种来源的经验用于动作和物理理解。官方项目页从早期 v0.1 讲起，后续预览是同一条路线的推进，时间线上应把两个版本分开。

桌面上的物体被推走后，下一帧视频像不像真实世界，只是第一层问题。对机器人来说，下一步动作到底能不能执行，才决定预测能否接上真实操作。

![智源开放日上的 Physis 项目演示](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/baai-physis.png)

*图片来源：智源研究院 REAL 物理 AI 开放日报道，2026-11-25。*

## 📦 GitHub Trending

北京时间 11 月 28 日早间抓取 GitHub 官方语言日榜。括号内为榜别与名次，“今日新增”采用榜面数值，各语言榜独立展示。

| 项目（榜位） | 总星 / 今日新增 | 用途 |
| --- | ---: | --- |
| [agent-substrate/substrate](https://github.com/agent-substrate/substrate)（Go 第 4） | 8,521 / +120 | Agent Substrate 的核心系统 |
| [wshobson/agents](https://github.com/wshobson/agents)（Python 第 9） | 42,989 / +62 | 一套 Markdown 技能适配多个代码助手 |
| [HaD0Yun/CozyClay](https://github.com/HaD0Yun/CozyClay)（JavaScript 第 10） | 2,075 / +42 | 在浏览器里摆场景、定姿势、编排镜头 |
| [mastra-ai/mastra](https://github.com/mastra-ai/mastra)（TypeScript 第 7） | 29,862 / +25 | 用 TypeScript 组织代理和工作流 |
| [malisper/pgrust](https://github.com/malisper/pgrust)（Rust 第 2） | 6,106 / +18 | 用 Rust 重写 PostgreSQL 的项目 |

今天最容易动手试的是 CozyClay：先把角色、镜头和走位摆出来，再把画面交给视频模型。做服务端的可以对照看看 Mastra 怎么把工具调用串起来，以及 pgrust 为什么要重写数据库这一层。

今天最值得记住的两个数字是四个市场与约 20%：前者改变算力的到达时间，后者改变每次请求的第一拍。

## 来源

- [《华尔街日报》英伟达服务器停运报道](https://www.wsj.com/tech/nvidia-tells-partners-to-pause-shipments-to-southeast-asia-5b791cb0)
- [美国商务部先进半导体远程访问规则](https://www.federalregister.gov/d/2026-23529)
- [Anthropic 工程博客](https://www.anthropic.com/research/migrating-api-to-rust)
- [《财富》Muse 接入谈判](https://fortune.com/2026/11/25/exclusive-amazon-meta-talks-deal-muse-ai-shopping-agent-blockade/)
- [《财富》黑五与 Visa 代理购物模拟](https://fortune.com/2026/11/27/black-friday-ai-agentic-shopping-consumer-trust/)
- [Visa 购物代理模拟原始发布](https://usa.visa.com/about-visa/newsroom/press-releases.releaseId.22936.html)
- [TBIJ 关于 Talkie 下架的调查](https://www.thebureauinvestigates.com/stories/2026-11-27/talkie-chatbot-apple-app-store-bureau-investigation)
- [路透关于中国旗舰模型排期的报道](https://www.investing.com/news/stock-market-news/exclusiveus-chip-curbs-distillation-crackdown-threaten-chinese-ai-model-development-sources-say-5052154)
- [智谱 GLM-5.4 公告](https://z.ai/blog/glm-5.4?lang=en)
- [智源研究院开放日报道](https://hub.baai.ac.cn/view/60026)
- [逆矩阵 Physis 官方项目页](https://www.roboatlas.ai/zh-CN/models/physis)
- [Go 日榜](https://github.com/trending/go?since=daily)、[Python 日榜](https://github.com/trending/python?since=daily)、[JavaScript 日榜](https://github.com/trending/javascript?since=daily)、[TypeScript 日榜](https://github.com/trending/typescript?since=daily)、[Rust 日榜](https://github.com/trending/rust?since=daily)
- [快科技报道智谱高管的回应](https://finance.sina.com.cn/tech/roll/2026-11-26/doc-inkaapny8176881.shtml)
- [TechCrunch 英伟达报道](https://techcrunch.com/2026/11/26/nvidia-hits-pause-on-chip-shipments-to-southeast-asia/)
- [Tom’s Hardware 英伟达实验室探访](https://www.tomshardware.com/tech-industry/artificial-intelligence/behind-the-scenes-at-nvidias-engineering-superlab-vera-rubin-nvl72-running-openai-workloads-800vdc-demonstrated-and-more)
