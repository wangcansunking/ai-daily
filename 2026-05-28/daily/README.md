---
title: "小米 MiMo 砍价 99% · Cursor 用 Kimi 反超 Opus · Simon 实账证 PMF | AI 日报 | 2026-05-28"
date: 2026-05-28
weekday: 星期四
slug: 2026-05-28
category: newsletter
description: "周四这一天三件事互相咬合。一是国内 token 价被拉到一个新档——小米 MiMo-V2.5 周三零点起永久降价，缓存命中输入 0.025 元 / 百万 tokens、未命中 3 元、输出 6 元，三档与 DeepSeek V4-Pro 长期公开报价完全对齐，最高降幅 99%；雷军本人在 X 上转发并点了「统一一档不分长短上下文」这一条；工程支撑由 SGLang HiCache + SWA（滑动窗口注意力）把 KV cache 在 GPU 显存 / CPU 内存 / SSD 三级搬运量压到原本的 1/7。二是海外 AI Coding 评测被一份新版本翻台面——Cursor Composer 2.5 仍跑 Kimi K2.5 底座（Aman Sanger 公开承认上一代博客「没提 Kimi」是疏漏），CursorBench v3.1 拿 63.2% 反超 Claude Opus 4.7 的 61.6%，单任务平均落在 1 美元以内，对手家旗舰同任务通常烧掉 4–6 美元；85% 算力投后训练、15% 增量预训这一结构第一次被 Cursor 公开。三是 Simon Willison 一篇博文把「Anthropic 与 OpenAI 已找到 PMF」直接钉在 HN 头页第一——截稿时 590 分 / 719 评论，论据不是营收新闻稿，是他自己 30 天编程账单——支付 200 美元订阅，按官方 API 价折算真实 token 价值 2,180.16 美元（Claude Code 1,199.79 / Codex CLI 980.37），订阅价相当于 API 价的 1/11，配套数字是 Anthropic 2026 Q2 年化营收 109 亿美元、SpaceX 每月 12.5 亿美元推理算力协议至 2029 年 5 月。配套的反 AI 信号同样硬——DuckDuckGo noai 子站 5 月 20-25 日访问量同比 +22.7%、5/24 单日峰值 +27.7%、美国 iOS 安装周均 +33%、5/25 峰值 +69.9%，PC Gamer 原文 HN 顶到 628 分 / 314 评论。GitHub Trending 头部由 affaan-m/ECC 19.6 万星 + 万星新进 mukul975/Anthropic-Cybersecurity-Skills 1.09 万和老牌项目 harry0703/MoneyPrinterTurbo 6.19 万共同撑起。"
tags:
  - 小米 MiMo V2.5 永久降价 99%
  - 缓存命中 0.025 元 / 百万 tokens
  - 对齐 DeepSeek V4-Pro 三档报价
  - 雷军 X 转发并点统一窗口
  - SGLang HiCache + SWA
  - KV cache 搬运量压到 1/7
  - Cursor Composer 2.5 反超 Opus
  - CursorBench v3.1 63.2 vs 61.6
  - Aman Sanger 承认上一代没提 Kimi
  - 单任务成本不到 1 美元
  - Cursor 85 后训练 15 增量预训
  - Simon Willison PMF 论断
  - HN 590 分 719 评论
  - 200 美元订阅 2180 美元 API 价
  - 订阅价是 API 价 1/11
  - Anthropic Q2 109 亿美元年化
  - SpaceX 12.5 亿每月至 2029
  - DuckDuckGo 反 AI 周涨 22.7
  - 美国 iOS 安装周均 33
  - HN 628 分 314 评论
  - p-e-w heretic 22k stars
  - shiyu-coder Kronos AAAI 2026
  - 45 个交易所开源金融底座
  - mukul975 Anthropic-Cybersecurity-Skills
  - harry0703 MoneyPrinterTurbo 6.19 万星
  - YouTube AI label 自动标注
cover: 28.png
---

# 小米 MiMo 砍价 99% · Cursor 用 Kimi 反超 Opus · Simon 实账证 PMF | AI 日报 | 2026-05-28

![三件事互相咬合：国产 token 砍到 0.025 元、Kimi 底座反超 Opus、海外开发者实账 11 倍证 PMF](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/daily/28.png)

## 📋 头版目录

- 💰 小米 MiMo-V2.5 永久降价 99%：缓存命中输入 0.025 元 / 百万 tokens，对齐 DeepSeek V4-Pro → 头条 1 / 国内 AI
- 🇨🇳 雷军本人 X 转发，特别点「统一一档不分长短上下文」窗口策略 → 头条 1.1
- 🧠 SGLang HiCache + SWA 把 KV cache 三级搬运量压到原本的 1/7 → 头条 1.3
- 🛠 Cursor Composer 2.5 仍跑 Kimi K2.5 底座，Aman Sanger 承认上一代没提是疏漏 → 头条 2 / AI Coding
- 🛠 CursorBench v3.1 63.2% 反超 Claude Opus 4.7 的 61.6%，单任务 < 1 美元 → 头条 2.2
- 🛠 85% 算力投后训练、15% 增量预训，Cursor 第一次公开训练结构 → 头条 2.3
- 🎙 Simon Willison《我认为 Anthropic 和 OpenAI 已找到 PMF》HN 顶到 590 分 / 719 评论 → 头条 3 / 名人说
- 💸 200 美元订阅 vs 2180.16 美元 API 价：订阅价是 API 价 1/11 → 头条 3.1
- 💸 Anthropic 2026 Q2 年化营收 109 亿美元，对比 25 年 8 月 40 亿，9 个月同比 170% → 头条 3.2
- 🏭 SpaceX 与 Anthropic 签每月 12.5 亿美元推理算力，合约至 2029 年 5 月 → 头条 3.2
- 🇨🇳 国产 token 价同档对位：DeepSeek V4-Pro 三档完全一致，国产推理工程能力首次永久价交付 → 国内 AI
- 📰 DuckDuckGo noai 子站周访问 +22.7%、美国 iOS 安装周均 +33%、5/25 峰值 +69.9% → 要闻 / 名人说
- 📰 PC Gamer 原文 HN 顶到 628 分 / 314 评论，反 AI 用户群信号 → 要闻
- 🔬 Heretic 一行命令自动 abliteration，Optuna + KL 约束（GitHub Trending 头部） → GitHub / 要闻
- 🔬 Kronos 开源金融 K 线基础模型 AAAI 2026 录用，45 个交易所预训 → GitHub / 要闻
- 🔥 affaan-m/ECC 跨 7 个 harness 的子 agent + skill + hook 抽象层连续 4 天单日新增过千 → GitHub
- 🔥 mukul975/Anthropic-Cybersecurity-Skills 把 5 大安全框架做成 Claude skill 包，一夜上 Trending 第 9 位 → GitHub
- 🔥 harry0703/MoneyPrinterTurbo 老牌 AI 短视频流水线再回 Trending 第 1 位 → GitHub
- ⚖ YouTube 公布 AI 标签自动化新策略，HN 顶到 453 分 / 263 评论 → 值得关注
- 🎙 Andy Edser / Sarah Perez 双源同日刊发 DuckDuckGo 反 AI 用户群报道，PC Gamer + TechCrunch → 名人说
- 🛡 NIST、MITRE ATT&CK、OWASP 三套标准框架做成 Claude 可调用 skill → AI Coding

## 🔥 头条深度

### 头条 1 · 小米 MiMo-V2.5 永久降价 99%：缓存命中输入 0.025 元 / 百万 tokens 落到与 DeepSeek V4-Pro 同档

![新浪财经 5-27 凌晨头条：小米 MiMo API 永久降价 99%，雷军本人转发](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/daily/sina-leijun-mimo-hero.jpg)

5 月 27 日凌晨，小米开放平台官方推送一份只有四行的更新公告，新浪财经 [1] 与虎嗅几乎同时挂上头条：MiMo-V2.5 API 永久调价，三档全部对齐 DeepSeek V4-Pro 长期公开报价。雷军本人当晚在 X 上转发 [2]，特别点了「统一一档不分长短上下文」这一条。这条调价的关键不是「又一次国产降价」，而是国产推理工程能力第一次以永久价（不是限时活动）的形式向开发者直接交付。

#### 1.1 三档价对齐 DeepSeek V4-Pro 意味着什么

| 价档（人民币 / 百万 tokens） | MiMo-V2.5 新价 | 改价前 | DeepSeek V4-Pro 长期价 |
|---|---|---|---|
| 输入（缓存命中） | **0.025** | 约 2.5 元（同档老价） | 0.025 |
| 输入（未命中） | **3** | — | 3 |
| 输出 | **6** | — | 6 |
| 窗口策略 | 统一一档（取消短 / 长上下文分档计价） | 短 / 长上下文分档 | 统一一档 |
| 生效 | 2026-05-27 0:00，永久价 | 限时活动为主 | 永久价 |

![国产 LLM token 价四档对比 · 缓存命中输入 0.025 元 / 百万 tokens 落到新档](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/daily/cn-llm-token-price-4way.png)

通稿里的 99% 是这样算的：改价前缓存命中输入约 2.5 元 / 百万 tokens 一档，新价 0.025 元 / 百万 tokens——刚好降到原档的 1/100，对外口径就是「最高降幅 99%」。这是国产对外公开 token 价历史上第一次出现「缓存命中输入档掉到三分钱以下」并以永久价形式落地的厂商。

#### 1.2 Token Plan 同步加量 5–8 倍：开发者侧账单怎么算

四档月度 Token Plan 价格不变，分别是 39 / 99 / 329 / 659 元，但可用 token 配额提升至原来的 5–8 倍。把这两条放在一起看才能算清开发者真实成本。以中度日用场景（每天调 1500 万 tokens、缓存命中率 60%、未命中 40%）估算：旧档下日均成本约 18 元 / 月度 540 元；新档下相同负载日均约 0.5 元 / 月度 15 元——这是单 API 单工作流的简化估算，多工作流叠加要再乘账面倍数，但量级判断不会变。

#### 1.3 工程支撑：SGLang HiCache + SWA 把 KV cache 搬运量压到 1/7

「永久价」这个口径不是营销话术，背后是推理工程栈过去六个月走出实验室的硬数字。SGLang [3] 这套从今年初开始被国内主流大模型厂集体采纳的推理引擎里，HiCache 模块完整支持 SWA（滑动窗口注意力），把 KV cache 在 GPU 显存 / CPU 内存 / SSD 三级之间的累计搬运量降到原本的 **1/7**。这是「0.025 元 / 百万 tokens 长期可持续」的硬约束——如果 KV cache 搬运成本压不下来，缓存命中档定价怎么算都不可能稳。完整工程拆解与四份独立来源对照见今日「小米 MiMo-V2.5 永久降价 99%」专题。

### 头条 2 · Cursor Composer 2.5 仍跑 Kimi K2.5 底座：CursorBench 63.2% 反超 Opus 4.7

![36 氪 5-27 头版报道：Cursor Composer 2.5 反超 Claude Opus 4.7](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/daily/36kr-composer-25-hero.jpg)

5 月 27 日 36 氪头版与新浪科技、智源同日挂出 Cursor Composer 2.5 [4] 复盘报道。底座这件事是焦点——上一代 Composer 2 出来时 Cursor 官方博客没有点 Kimi K2.5，引起月之暗面社区当周连续两天上 X 讨论；这次 Aman Sanger 公开 [6] 承认上一代博客「没提 Kimi 是疏漏」，Composer 2.5 仍在 Kimi K2.5 底座上做重度后训练，未替换。

#### 2.1 评测三件套：CursorBench 反超、SWE-Bench / Terminal-Bench 打平

| 评测 | Composer 2.5 | Claude Opus 4.7 | 差距 |
|---|---|---|---|
| CursorBench v3.1 | **63.2%** | 61.6% | **+1.6 pp（Composer 反超）** |
| SWE-Bench Multilingual | 79.8% | 80.5% | -0.7 pp |
| Terminal-Bench 2.0 | 69.3% | 69.4% | 基本打平 |
| 单任务平均成本 | **< 1 美元** | 4–6 美元（典型旗舰口径） | Composer 仅为对手 1/5–1/6 |

![Cursor Composer 2.5 与 Claude Opus 4.7 在 CursorBench、SWE-Bench、Terminal-Bench 三项评测对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/daily/composer-25-bench-4way.png)

这是过去 12 个月 Cursor 官方榜上第一次有非 Anthropic 系模型登顶 CursorBench。要注意的细节：CursorBench 是 Cursor 官方自评测，对自家模型有「评测校准」的天然亲和度——这条数字真正值得国内开发者细看的，不是「Composer 反超 Opus」的营销标题，而是单任务成本 < 1 美元的硬约束在不影响通过率的前提下被守住。

#### 2.2 算力分配：85% 后训练 + 15% 增量预训第一次公开

Cursor 在博客里第一次公开训练投入结构——总算力 85% 用于后训练（含定向 RL + 合成数据 + sharded Muon optimizer + 双 mesh HSDP 数据 / 张量并行），仅 15% 用于底座增量预训。这条结构对国内 AI Coding 工具厂的意义最大——通义灵码、字节 Trae、百度文心快码、智谱 CodeGeeX、阿里通义灵码 Code-LLM-Plus 走的本来就是「不自训底座、深度后训练 + 工程化」这条路，Cursor 用一个估值百亿美元的产品把这条路实测跑通了。

#### 2.3 后训练三件套：定向 RL + 25 倍合成数据 + Sharded Muon

- **定向 RL**：模型执行错误那一步直接局部提示，不像传统 RL 把整条 trajectory 重跑
- **合成数据 25 倍**：相比上一代 Composer 2，任务数扩大 25 倍，靠「功能删除法」自动生成 — 把成熟代码库里某一功能模块删除再让 agent 复原
- **Sharded Muon optimizer + 双 mesh HSDP**：数据并行 + 张量并行双 mesh 切分

完整训练曲线与国产同档对位见今日「Cursor Composer 2.5 仍跑 Kimi K2.5 底座」专题。

### 头条 3 · Simon Willison 论断 Anthropic / OpenAI 已找到 PMF：HN 590 分顶帖 + 11 倍倍数

![HN frontpage Simon Willison PMF 论断顶帖 590 分 719 评论](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/daily/source-hn-frontpage-209-v2.png)

Simon Willison [7] 这位长期对 AI 商业模式持谨慎乐观的资深开发者，5 月 27 日发出博文《我认为 Anthropic 和 OpenAI 已找到 product-market fit》。帖子由他本人 4 小时内冲上 HN 首页第一 [8]，截稿时 **590 分 / 719 条评论**，是过去一个月 AI 商业模式题材最高顶帖。关键证据不是营收新闻稿，是他自己的 30 天编程账单。

#### 3.1 11 倍倍数：订阅价是 API 价的 1/11

| 项目 | 金额（美元） | 备注 |
|---|---|---|
| Simon 月度订阅支出 | 200 | Claude Pro 100 + Codex Pro 100 |
| Claude Code 30 天 API 价折算 | 1,199.79 | 按 Anthropic 官方 API 单价口径 |
| Codex CLI 30 天 API 价折算 | 980.37 | 按 OpenAI 官方 API 单价口径 |
| 折算总价值 | **2,180.16** | — |
| 订阅价 / API 价 = | **1 / 11** | Simon 自己的结论 |

![Simon Willison 30 天 bill 从 200 美元订阅膨胀到 2180 美元 API 价的 11 倍倍数对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/daily/bill-200-to-2180.png)

Simon 在文末写：「这不是亏损补贴，是定价策略——Anthropic 和 OpenAI 在用 200 美元月费档锁定高强度用户，然后用企业按量计费档把利润收回来。」HN 置顶第一条评论来自 tptacek（Fly.io 创始团队、知名安全工程师）：「这是我读过的关于 AI 行业 PMF 最有说服力的一段，因为它的颗粒度是一个真实开发者一个月的真实账单，而不是 ARR 或者 monthly active users。」

#### 3.2 营收数字呼应：Anthropic Q2 109 亿美元 + SpaceX 12.5 亿月推理协议

- **Anthropic 2026 Q2 年化营收 109 亿美元** [9]——对比 2025 年 8 月披露的 40 亿美元，9 个月同比 +170%；据 Bloomberg / The Information 转述「强烈传闻将迎来首个盈利季度」
- **SpaceX 与 Anthropic 推理算力协议 [10]**：每月 12.5 亿美元，合约至 2029 年 5 月——SpaceX 是 OpenAI 投资方，但同时给 Anthropic 锁定 3.5 年推理算力，意味着马斯克方既不愿在 OpenAI 一家上完全押注，也实际承认 Anthropic 推理需求确实跑到这个量级
- **企业定价两次跳档**：Anthropic 2025 年 11 月把 Team 套餐改为 20 美元 / 座 / 月 + API 用量；OpenAI 2026 年 4 月跟进，取消按消息计费、所有产品统一按 token 口径计价

#### 3.3 给开发者的可复用 PMF 验证方法

Simon 这条 590 分顶帖的真正价值，是给国内做 coding agent 的同行一个可直接复用的 PMF 验证方法——拿你自己最重度用户的 30 天真实账单，按官方 API 价折算，看订阅价是 API 价的几分之一。倍数小于 3 说明定价合理但用户黏性弱；倍数大于 8 说明产品已经踩到「让人停不下来」的区间。同期通义千问全年营收约 50 亿元人民币（中信证券估算）、文心一言约 30 亿元人民币——加起来还不到 Anthropic 单季度的零头。不是国内做不出 coding agent，而是消费者侧那一根「每月 200 美元长尾订阅」的轨道还没铺出来。完整账单细节与国内对位测算见今日「Simon Willison 论断 Anthropic / OpenAI 已找到 PMF」专题。

## ⚡ 快讯速览

- DuckDuckGo noai 子站访问量 5/20-25 日均同比 +22.7%、5/24 单日峰值 +27.7%；美国 iOS 安装周均 +33%、5/25 峰值 +69.9%，全平台周均 +18.1%、5/25 峰值 +30.5%；第三方 Apptopia 给出美国日均下载 +29% / 全球 +12% 校验线 [11][12]。下一步看 6 月头两周这个曲线会不会回落到 I/O 前的 1%–9% 基线。
- mukul975/Anthropic-Cybersecurity-Skills [19] 一夜上 GitHub Trending 第 9 位，stars 10,937 / +886，把 NIST、MITRE ATT&CK、OWASP 等 5 大安全框架做成 Claude 可调用 skill 包。下一步看 Anthropic 官方会不会把这套 skill 接入官方 marketplace。
- harry0703/MoneyPrinterTurbo [18] 老牌 AI 短视频流水线再回 GitHub Trending 第 1 位，stars 累计 61,887 / 单日 +1,742。下一步看是否会接入 Veo 3 / Sora 2 等海外视频底座的本地兼容层。
- Lum1104/Understand-Anything [20] 单日 +4,465 涨到 39,714 stars，连续四天单日新增过千；colbymchenry/codegraph 与之同档竞争。下一步看 6 月会不会出现两家代码图谱项目互通的中间格式。
- HN 头页《Claude Code as a Daily Driver: Claude.md, Skills, Subagents, Plugins, and MCPs》[22] 顶到 348 分 / 220 评论。下一步看 6 月之前是否会出现国内同款「Cursor / Trae / Qoder 作为日用驱动」的中文版方法论。
- YouTube [23] 5/27 公布 AI 生成视频自动标签新策略 HN 顶到 453 分 / 263 评论。下一步看抖音 / B 站 / 快手会不会跟进同款政策框架。
- 36 氪 5/27 转引 Cursor 开发者大会：Aman Sanger 公开承认上一代 Composer 2 博客没提 Kimi K2.5 是疏漏。下一步看 Cursor 文档与官方 blog 历史版本会不会做正式更新。
- 蚂蚁、平安、招商三家国产量化都有自研时间序列模型但全部闭源；Kronos [15] 是国内量化团队第一次能直接拉到本地用私有 K 线数据微调的开源金融基础模型。下一步看 6 月之前国内量化团队会不会公布第一份基于 Kronos 的实战回测报告。

## 🎙 名人说 & X 热议

**Simon Willison（HN 590 分顶帖）**——「Anthropic and OpenAI have found product-market fit. Not as a subscription play. As an API consumption play, where the subscription is a calibration tool.」（Anthropic 和 OpenAI 已经找到 PMF，不是订阅生意，是 API 消费生意，订阅档只是一个用户校准工具。）他对自己 30 天 200 美元订阅折算 API 价 2,180.16 美元这件事的判断是：这不是亏损补贴，是定价策略——Anthropic / OpenAI 在赌未来 12 个月单 token 推理成本继续下降 50% 以上，把价差吃回来。Simon 一向对 AI 商业模式持谨慎乐观，过去两年笔记里出现频次最高的词是 "cautiously optimistic"，这次直接拿掉了 cautiously。

**Aman Sanger（Cursor 联合创始人 X）**——「I should have credited Kimi K2.5 in the Composer 2 blog post. We didn't replace it for 2.5. The breakthrough is in the post-training scaffold, not the base.」（上一代博客本应明确引用 Kimi K2.5，2.5 也没替换底座；突破来自后训练支架，不是底座替换。）这是国内大模型与海外 AI Coding 工具关系链最直接的一次表态——开源底座加上重度后训练这条路，在 Cursor 这一家估值百亿美元的工程团队这里被实测跑通。

**雷军（X 转发小米 MiMo 降价公告）**——「永久价，不是限时；统一窗口，不再分长短。」 雷军本人特别点出「统一一档不分长短上下文」这一条，意味着小米开放平台这次把窗口策略简化掉，是承认开发者「在调试期不愿意为长上下文额外计价」的真实场景。雷军直接转发开放平台的开发者侧公告这件事，在国内消费电子 CEO 里属于第一档露面，对 MiMo 在小米生态侧（手机、汽车、IoT）的下一阶段落地有强信号意义。

**Gabriel Weinberg（DuckDuckGo CEO，X 公开发声）**——「Google is force-feeding AI. There's no off switch.」（Google 在强行喂 AI，没有给用户关闭的入口。）首席沟通官 Kamyl Bazbaz 在 The Verge 采访中补一句「人们只是想要选择权」。把这两位的表态放到 noai.duckduckgo.com 子站访问周同比 +22.7% 的数字背景下看，国内三家 AI 搜索（秘塔 / 夸克 / 360）的产品同行可以直接拿来对照——保留「纯链接结果」入口这件事，本身就是一个可量化的用户黏性指标。

## 📰 精选要闻

- 🔴 **小米 MiMo-V2.5 永久降价 99%，缓存命中 0.025 元 / 百万 tokens** [1]——5 月 27 日 0 点生效，三档价与 DeepSeek V4-Pro 长期公开报价完全对齐，统一窗口策略不再分长短上下文；雷军本人 X 转发。SGLang HiCache + SWA 把 KV cache 三级搬运量压到 1/7，是这一档「永久价」长期可持续的工程底座。这是国产对外公开 token 价历史上第一次「缓存命中输入档掉到三分钱以下」并以永久价形式落地。
- 🔴 **Simon Willison 论断「Anthropic / OpenAI 已找到 PMF」** [7][8]——HN 头页 590 分 / 719 评论。Simon 用自己 200 美元月订阅折算 API 价 2,180.16 美元的 30 天真实账单做 PMF 验证证据。配套数字：Anthropic 2026 Q2 年化营收 109 亿美元 [9]、SpaceX 与 Anthropic 每月 12.5 亿美元至 2029 年 5 月推理算力协议 [10]。
- 🔴 **Cursor Composer 2.5 仍跑 Kimi K2.5，CursorBench 63.2% 反超 Opus 4.7** [4][5]——36 氪头版 + 新浪科技 + 智源 5/27 同日转载。Aman Sanger [6] 公开承认上一代博客没提 Kimi K2.5 是疏漏，Composer 2.5 在 Kimi K2.5 底座上做重度后训练；85% 算力投后训练 / 15% 增量预训这一结构第一次被 Cursor 公开。
- 🟡 **DuckDuckGo noai 子站访问量周同比 +22.7%、美国 iOS 安装周均 +33%**[11][12]——PC Gamer 原文 HN 顶到 628 分 / 314 评论。这是 DuckDuckGo 自 2024 年上线 AI 摘要可选项以来同期增长最强的一次。Google AI mode 自 5 月 20 日 I/O 推成搜索默认入口之后，反 AI 用户群信号被一周内炸出来。
- 🟡 **Heretic 22,000 stars 上 GitHub Trending：一行命令自动解 RLHF 拒答** [14]——p-e-w/heretic 用 Optuna TPE 自动搜参 + directional ablation + KL divergence 约束做 abliteration 全流程自动化。HuggingFace 已有 3000+ 个用 Heretic 产出的衍生模型。Gemma-3-12B 实测 refusal 从 97/100 降到 3/100、KL 仅 0.16（手工方案约六分之一）。社区采纳数字硬实但题材敏感，国内开发者具体使用需各自判断合规边界。

![shiyu-coder/Kronos 仓库 og 卡：金融市场语言基础模型](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/daily/source-kronos-og.png)

- 🟡 **Kronos 26,866 stars / AAAI 2026 录用：全球首个开源金融 K 线基础模型** [15][16]——shiyu-coder/Kronos 把金融 K 线序列直接类比为语言，沿用 LLM 那套 tokenizer + Transformer 范式，在 45 个交易所数据上预训。4 个版本：mini 4.1M / small 24.7M / base 102.3M（均开源）/ large 499.2M（闭源授权）。同期国内蚂蚁、平安、招商三家自研量化时间序列模型全部闭源，Kronos 是国内量化团队第一次能拉到本地用私有数据微调的开源金融基础模型。

## 🇨🇳 国内 AI 观察

- **MiMo-V2.5 永久降价补齐了国产 token 价的「合理水位」**——缓存命中 0.025 元 / 输入 3 元 / 输出 6 元这三档与 DeepSeek V4-Pro 完全对齐之后，国产 token 价的对外公开报价档位被锁在一个明确位置。开发者侧账单这一面，中度日用场景（每天 1500 万 tokens、命中率 60%）的月度成本从约 540 元降到约 15 元；这是国产推理工程能力第一次以永久价的形式向开发者直接交付。下一个观察点是字节豆包 / 通义千问 / 智谱 GLM-4 是否在 6 月跟出对位报价。
- **一款海外百亿美元 AI Coding 工具用 Kimi K2.5 底座做后训练把评测推到第一档**——通义灵码、字节 Trae、百度文心快码、智谱 CodeGeeX 走的本来就是「不自训底座、深度后训练 + 工程化」这条路，海外这次的实测证明给了国产同行一个明确的训练栈参数：85% 算力投后训练 + 15% 增量预训。月之暗面 Kimi K2.5 作为国产开源底座被海外百亿美元产品用作主力，是国产开源生态在 AI Coding 链路上的一次硬信号。
- **国内三家 AI 搜索（秘塔 / 夸克 / 360）有了一个直接对标点**——DuckDuckGo noai 子站 +22.7% 这条数字告诉国内同行：保留「纯链接结果」入口，本身就是一个可量化的用户黏性指标。秘塔 AI 搜索目前默认强 AI 摘要、夸克 AI 搜索同样以 AI 总结为主入口、360 AI 搜索保留传统结果但默认折叠——这套对照实测见今日「DuckDuckGo 一周流量涨 28%」专题。
- **Kronos 给国内量化团队提供了第一个开源金融基础模型选项**——AAAI 2026 录用 + MIT 协议 + 45 个交易所数据预训 + 4 个版本里 3 个开源 + HuggingFace 模型 + tokenizer + 微调脚本全开放。同期国内蚂蚁、平安、招商三家自研时间序列模型全部闭源；中小型私募与券商自营第一次有机会用 Kronos 做底座、在自家私有 K 线数据上微调，而不必从 LSTM / Informer / PatchTST 重训。

## 📦 GitHub Trending

![affaan-m/ECC 仓库 og 卡：19.6 万星跨 harness 抽象层](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/daily/source-github-ecc-og-2026-05-28.png)

- 🔴 **harry0703/MoneyPrinterTurbo** [18]：61,887 stars / +1,742 / Python。AI 一键生成短视频流水线，老牌项目再上 GitHub Trending 第 1 位。下一步看是否会接入 Veo 3 / Sora 2 等海外视频底座的本地兼容层。
- 🔴 **affaan-m/ECC**[17]（[跟进 5/27]）：195,990 stars / +2,062 / 29,962 forks / MIT。仓库描述「The agent harness performance optimization system」。从 5/25 v2.0.0-rc.1 升级以来连续 4 天单日新增过千；跨 7 个 harness（Claude Code / Codex / Cursor / OpenCode / Gemini / Zed / 终端）共享同一套 skill。
- 🔴 **Lum1104/Understand-Anything** [20]（[跟进 5/27]）：39,714 stars / +4,465 / TypeScript / MIT。代码图谱可视化路线，连续四天单日新增过千；累计 4 天新增超过 13,000 stars。
- 🔴 **shiyu-coder/Kronos**[15]：26,866 stars / +401 / Python / MIT。GitHub Trending 第 8 位，仓库自述「A Foundation Model for the Language of Financial Markets」，AAAI 2026 录用论文。
- 🟡 **p-e-w/heretic**[14]：21,937 stars（GitHub Trending 实查 22,000）/ +211 / Python / AGPL-3.0。仓库自述「Fully automatic censorship removal for language models」。HuggingFace 上 3000+ 衍生模型。
- 🟡 **mukul975/Anthropic-Cybersecurity-Skills** [19]：10,937 stars / +886 / Python。把 NIST、MITRE ATT&CK、OWASP 等 5 大安全框架做成 Claude 可调用 skill 包，一夜上 Trending 第 9 位。
- 🟡 **anthropics/knowledge-work-plugins** [21]（[跟进 5/22 + 5/27]）：17,253 stars / +695 / 1,952 forks。5/22 一次开源 15 个工种 plugin 之后连续 6 天单日新增过 500，累计新增超过 7,800 stars。

## 🛠 AI Coding 工具动态

- **Cursor Composer 2.5 把「开源底座 + 重度后训练」路线在百亿美元产品上跑通**[4]——CursorBench v3.1 63.2% 反超 Opus 4.7 61.6%、单任务成本 < 1 美元（对手家旗舰同任务 4-6 美元）；85% 算力投后训练 + 15% 增量预训、定向 RL + 25 倍合成数据 + Sharded Muon 这套训练栈第一次被 Cursor 公开。对国内 AI Coding 工具厂的直接意义是路线参数已经给出。
- **mukul975/Anthropic-Cybersecurity-Skills 把 5 大安全框架做成 Claude skill**[19]——10,937 stars / +886，把 NIST CSF 2.0、MITRE ATT&CK、OWASP Top 10、CIS Controls、ISO 27001 做成可一键调用的 Claude skill 包。读者今天对照昨天的 Anthropic knowledge-work-plugins 15 工种开源（5/22）+ ECC 跨 harness 抽象层来看，三件事拼出来的方向是一致的——Claude Code 用户当下缺的不是模型，是把子 agent / skill / hook / 标准框架当一等公民管起来的抽象层。
- **Aman Sanger 公开承认 Cursor 上一代博客「没提 Kimi K2.5 是疏漏」**[6]——这是过去 12 个月海外 AI Coding 工具与国产开源底座关系链最直接的一次公开表态。Composer 2.5 仍跑 Kimi K2.5 底座，未替换；这条事实链对国内月之暗面与 Cursor 双方在 6 月之后的合作公开度都是一个明确的拐点。
- **HN 头页《Claude Code as a Daily Driver》方法论顶到 348 分**[22]——arps18.github.io 一份 Claude Code 日用驱动方法论顶到 HN 头页 220 评论。中文版同款方法论目前还没出现，是国内 Trae / Qoder / Qwen Code / 文心快码用户群未来 30 天可以补的内容空缺。

## 🔭 值得关注

- **国产 token 价档位被锁在缓存命中 0.025 元 / 百万 tokens**——小米 MiMo-V2.5 与 DeepSeek V4-Pro 同档对齐之后，字节豆包 / 通义千问 / 智谱 GLM-4 三家是否在 6 月之前跟出对位报价？这条档位会不会成为国产 token 价的「事实标准」？接下来 30 天是关键观察期。
- **海外消费者订阅 PMF 信号会不会刺激国产同行重做账单**——Simon Willison 的 11 倍倍数给出可复用验证方法。国内做 coding agent 的同行（通义灵码、Trae、Qoder、文心快码、CodeGeeX）下一步是不是会主动公开自己的「月订阅 / API 价折算」倍数，是判断国内消费者侧 PMF 信号能不能炸出来的硬指标。3 个月时间窗。
- **YouTube AI 标签自动化新政会不会刺激抖音 / B 站 / 快手跟进**[23]——5/27 YouTube 政策更新 HN 顶到 453 分。视频平台对 AI 生成内容的标签策略一旦成为行业默认，对短视频 AI 流水线工具（包括 MoneyPrinterTurbo / 即梦 / 可灵）的产品形态有直接影响。值得国内同行复盘。
- **反 AI 用户群信号能不能从搜索蔓延到 IDE / 邮件客户端**——DuckDuckGo noai 子站 +22.7% 这条曲线之后，下一个可观察的「保留传统体验」赛道是开发者 IDE（已有用户在 Reddit 反映关掉 Cursor Tab 的呼声）与邮件客户端（Gmail 智能回复折叠率）。这是 6 月之前值得跟踪的横向用户行为信号。
- **国产金融基础模型会不会跟进开源**——Kronos AAAI 2026 + MIT 协议 + 45 个交易所给出了开源金融底座的工程模板。蚂蚁、平安、招商三家自研模型至今闭源；是否有任意一家在 6 月之前推一份对位开源版本？这是国产金融 AI 生态独立性的现实里程碑。

## ✍ 编辑说

- **小米 MiMo 永久降价 99% / 推荐**：如果你在做国产 LLM 推理 API 选型，今天就值得把 MiMo-V2.5 加入候选名单，三档价与 DeepSeek V4-Pro 同档对齐意味着可以与现有 DeepSeek 集成做 A/B 切换实验。对国内独立开发者与小团队，月度账单从约 540 元降到约 15 元这条对你 12 个月内的产品成本结构有直接意义。
- **Cursor Composer 2.5 + Kimi K2.5 / 推荐**：如果你是国内 AI Coding 工具的产品 / 训练负责人，今天读完这条对你接下来 6-12 个月的训练栈选型有意义——85% 后训练 + 15% 增量预训这个比例是 Cursor 给出的明确参数。如果你是日用 Cursor 的开发者，可以直接升 2.5 跑真实任务，单任务 < 1 美元这条值得跑一周日均账单做对照。
- **Simon Willison PMF 论断 / 关注**：如果你是国内大模型公司商业化负责人或 coding agent 团队 PM，今天值得把 Simon 给出的「月订阅 / API 价折算倍数」方法搬到自家产品上跑一遍。倍数 8 以上才是真正进入 PMF 区间，3 以下意味着用户没把产品当日用驱动。这条对你 12 个月内的定价策略与重度用户运营有直接参考价值。
- **DuckDuckGo 28% / 关注**：如果你在做国内 AI 搜索 / 笔记 / IDE / 邮件客户端，今天可以把 noai 子站这条数字钉在墙上。保留「纯链接 / 纯传统」入口这件事，本身就是一个可量化的用户黏性指标。下一步关注 6 月头两周这条曲线会不会回落到 I/O 前的基线。
- **Heretic 22k stars / 做技术储备**：如果你做开源模型微调或合规审核，这套 Optuna + directional ablation + KL 约束的方法论值得记下来，对你理解 RLHF 拒答行为如何被自动调试有参考价值。具体使用需各自判断合规边界，本日报不做建议。
- **Kronos 金融基础模型 / 做技术储备**：如果你在做量化、金融工程或时间序列建模，今天值得把 Kronos 4 个版本的开源权重拉一份到本地试一次。AAAI 2026 录用 + 45 个交易所预训 + MIT 协议 + 微调脚本全开，对你 6-12 个月内决定「是否从 LSTM / PatchTST 路线迁到金融基础模型路线」有意义。

## 🔗 引用链接

- [1] 新浪财经《小米 MiMo API 永久降价 99%，雷军本人转发》2026-05-27: https://finance.sina.com.cn/tech/2026-05-27/doc-xiaomi-mimo-pricecut-99.shtml
- [2] 雷军 X 转发小米开放平台 MiMo 降价公告: https://x.com/leijun
- [3] SGLang 官方仓库（HiCache + SWA 工程实现）: https://github.com/sgl-project/sglang
- [4] 36 氪《Cursor Composer 2.5 仍用 Kimi K2.5 底座，CursorBench 反超 Opus》2026-05-27: https://36kr.com/p/cursor-composer-25-kimi-k25
- [5] Cursor 官方博客 Composer 2.5 发布稿: https://cursor.com/blog/composer-2-5
- [6] Aman Sanger X 公开承认上一代博客没提 Kimi 是疏漏: https://x.com/amanrsanger
- [7] Simon Willison 博文《I think Anthropic and OpenAI have found product-market fit》2026-05-27: https://simonwillison.net/2026/May/27/product-market-fit/
- [8] HN 头页 Simon Willison 顶帖 590 分 / 719 评论: https://news.ycombinator.com/item?id=48400000
- [9] The Information / Bloomberg 转述 Anthropic 2026 Q2 109 亿美元年化营收: https://www.theinformation.com/articles/anthropic-q2-2026-revenue-10-9-billion
- [10] SpaceX 与 Anthropic 推理算力协议至 2029 年 5 月: https://www.bloomberg.com/news/articles/spacex-anthropic-inference-1-25b-monthly
- [11] PC Gamer 2026-05-26 Andy Edser DuckDuckGo 28% 报道: https://www.pcgamer.com/hardware/duckduckgos-ai-free-search-saw-nearly-28-percent-more-visits-in-the-week-following-googles-insistence-that-people-love-ai-mode/
- [12] TechCrunch 2026-05-26 Sarah Perez DuckDuckGo 安装量报道: https://techcrunch.com/2026/05/26/duckduckgo-installs-are-up-30-as-users-reject-being-force-fed-googles-ai-search/
- [13] DuckDuckGo noai 子站: https://noai.duckduckgo.com/
- [14] p-e-w/heretic 仓库: https://github.com/p-e-w/heretic
- [15] shiyu-coder/Kronos 仓库: https://github.com/shiyu-coder/Kronos
- [16] AAAI 2026 Kronos 论文页: https://aaai.org/conference/aaai-2026/kronos-foundation-model-financial-markets
- [17] affaan-m/ECC 仓库: https://github.com/affaan-m/ECC
- [18] harry0703/MoneyPrinterTurbo 仓库: https://github.com/harry0703/MoneyPrinterTurbo
- [19] mukul975/Anthropic-Cybersecurity-Skills 仓库: https://github.com/mukul975/Anthropic-Cybersecurity-Skills
- [20] Lum1104/Understand-Anything 仓库: https://github.com/Lum1104/Understand-Anything
- [21] anthropics/knowledge-work-plugins 仓库: https://github.com/anthropics/knowledge-work-plugins
- [22] HN 头页 Claude Code as a Daily Driver 顶帖 348 分: https://news.ycombinator.com/item?id=48399500
- [23] YouTube 官方博客 AI 标签自动化策略公告: https://blog.youtube/news-and-events/improving-ai-labels-viewers-creators/
