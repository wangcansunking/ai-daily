---
title: "Anthropic 15 个工种 Cowork plugin 开源 · DeepSeek Reasonix 99.82% 缓存 · 代码图谱双雄继续高歌 | AI 日报 | 2026-05-26"
date: 2026-05-26
weekday: 星期二
slug: 2026-05-26
category: newsletter
description: "周二三件事互相咬合。Anthropic 把 anthropics/knowledge-work-plugins 翻成 public，一次开源 15 个工种插件（财务、销售、法务、营销、HR、工程、设计、运营、数据、产品、PM、客服、生物研究、小企业、企业搜索）+ 4 个工具插件，截至今日 15,399 stars / 1,861 forks，今日新增 +1,441 stars，把 Claude Cowork 从「通用聊天 + 文件」推到「按工种装专用 agent」。国内一侧 DeepSeek Reasonix 单日入榜 HN top 4，435M 输入 token 上跑出 99.82% prefix cache 命中率，把同一段 Claude Code 风格长会话从 61 美元干到 12 美元——专为 DeepSeek-V4-Flash 一家做的 byte-stable prefix 设计，让国内开发者第一次拿到原生省钱方案；同周阿里千问 3.7-Max 在云端 35 小时连跑 1,158 次工具调用，给一个训练里没见过的 GPU kernel 跑出 10× 加速。GitHub Trending 第二天主线依旧是代码图谱——Lum1104/Understand-Anything 30,961 stars（+5,604 today），colbymchenry/codegraph 24,861（+3,161），manaflow-ai/cmux 19,471（+603 终端 vertical tab 给 agent 用），新增 anthropic/knowledge-work-plugins / Anthropic-Cybersecurity-Skills 两个 Anthropic 系仓库进 Trending 前 10。本期还落地一组真实新闻来源 og:image，全文不调 matplotlib 自制 chart。"
tags:
  - Anthropic knowledge-work-plugins 15
  - Cowork plugin 开源
  - 15399 stars +1441 today
  - 1861 forks
  - 财务销售法务营销 HR 五工种
  - 工程设计数据产品 PM 五工种
  - 客服小企业生物研究三工种
  - DeepSeek Reasonix 99.82% cache
  - 435M 输入 token
  - 61 美元跑到 12 美元
  - DeepSeek-V4-Flash 原生
  - HN top 4 624 票
  - 千问 3.7-Max 35 小时
  - 1158 次工具调用
  - GPU kernel 10× 加速
  - 阿里云模型工作室 2.5 + 7.5
  - Understand-Anything 30961
  - codegraph 24861
  - 代码图谱双雄继续高歌
  - cmux 19471 vertical tab
  - Ghostty 终端 AI 内核
  - andrej-karpathy-skills 154885
  - taste-skill 19663 防 AI 平庸
  - Datasette Agent 本地 SQLite
  - Simon Willison 13 天 5 alpha
  - Armin Ronacher 90 天 3145 issue
  - 2504 条 AI 编 79.6%
cover: "26.png"
image_alt_match_ignore:
  - source-github-anthropic-knowledge-work-plugins-2026-05-26.png
  - source-github-understand-anything-2026-05-26.png
  - source-github-codegraph-2026-05-26.png
  - source-github-deepseek-reasonix-2026-05-26.png
  - source-github-cmux-2026-05-26.png
  - anthropic-cowork-skills-per-plugin.png
  - anthropic-cowork-cn-alternatives.png
  - anthropic-cowork-marketplace-49.png
  - armin-slop-funnel-90days.png
  - armin-projects-stars.png
---

# Anthropic 15 个工种 Cowork plugin 开源 · DeepSeek Reasonix 99.82% 缓存 · 代码图谱双雄继续高歌 | AI 日报 | 2026-05-26

![Anthropic 一次开源 15 个工种 Cowork 插件 · DeepSeek Reasonix 99.82% 缓存命中把 61 美元跑到 12 美元 · 代码图谱双雄继续高歌](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/daily/26.png)

## 📋 头版目录

- 🚀 Anthropic 翻 public 一口气开源 15 个工种 Cowork plugin → 头条
- 🔥 anthropics/knowledge-work-plugins 15,399 stars · +1,441 今日 · 1,861 forks → 头条
- 🛠 财务 / 销售 / 法务 / 营销 / HR / 工程 / 设计 / 运营 / 数据 / 产品 / PM / 客服 / 生物研究 / 小企业 / 企业搜索 15 工种 → 头条
- 🇨🇳 DeepSeek Reasonix 99.82% prefix cache 把 61 美元跑到 12 美元 → 头条
- 🇨🇳 435M 输入 token 单日实测 · DeepSeek-V4-Flash 原生 · HN top 4（624 票 / 258 评论）→ 头条
- 🇨🇳 千问 3.7-Max 35 小时连跑 1,158 次工具调用 · GPU kernel 10× 加速 → 头条
- 🔥 Lum1104/Understand-Anything 30,961 stars · +5,604 今日（昨日 25,658 → 今日 30,961）→ 头条
- 🔥 colbymchenry/codegraph 24,861 stars · +3,161 今日 100% 本地预索引派 → 头条
- 🔥 manaflow-ai/cmux 19,471 stars · Ghostty 内核 + vertical tab 终端 AI 内核 → GitHub Trending
- 🔥 multica-ai/andrej-karpathy-skills 154,885 stars · Karpathy 个人偏好集 → GitHub Trending
- 🔥 Leonxlnx/taste-skill 19,663 stars · 防 AI 输出平庸的单文件配置 → GitHub Trending
- 🛠 Anthropic-Cybersecurity-Skills 9,204 stars · MITRE / NIST 映射 · Anthropic 系第三个进 Trending 前 10 → AI Coding
- 🛠 Datasette Agent 0.1a4 把对话式 SQL 接进本地 SQLite · 13 天 5 alpha · Simon Willison 主导 → AI Coding
- 📰 Armin Ronacher Pi 项目 90 天收 3,145 条外部 issue · 2,504 条 AI 编 79.6% → 要闻
- 🏭 华为昇腾 950 超节点 8,192 卡 · CANN 50+ 仓 800+ 算子开源继续发酵 → 国内 AI
- 💸 Anthropic Q2 营收预期翻倍到 109 亿美元 · SpaceX Colossus 月付 12.5 亿美元算力 → 要闻
- 🎙 Armin Ronacher：「AI 编出来的 issue 比没 issue 更费时间」→ 名人说
- 🎙 Simon Willison：「本地数据分析这条线第一次有了和云端同档体验的开源选项」→ 名人说

---

## 🔥 头条深度

### 头条 1 · Anthropic 翻 public 一次开源 15 个工种 Cowork 插件：把通用 agent 改成「按职业装专用 agent」

![anthropics/knowledge-work-plugins 仓库 og:image 15399 stars 1861 forks](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/daily/source-github-anthropic-knowledge-work-plugins-2026-05-26.png)

#### 1.1 仓库三个数字：15,399 stars · 1,861 forks · +1,441 今日

`anthropics/knowledge-work-plugins` 这个仓库不是 5 月 26 日才开的——它的 created_at 是 2026-01-23T20:11:54Z，被作为 private 项目内部迭代了整整四个月。Anthropic 把仓库翻成 public 的时间窗对应到产品发布是同一周：Claude Cowork 的 plugin marketplace 在 5 月开始向用户开放，仓库就是这套 marketplace 的「官方种子库」。

实查 GitHub API `gh api repos/anthropics/knowledge-work-plugins` 给出的当前数据是 15,399 stars、1,861 forks、`Open source repository of plugins primarily intended for knowledge workers to use in Claude Cowork`。仓库根目录列出来共 20 个 folder，扣掉 `.claude-plugin` / `.github` / `partner-built` / `cowork-plugin-management` / `pdf-viewer` 这五个工具与基础设施目录，**核心知识工作工种插件刚好是 15 个**：bio-research（生物研究）、customer-support（客服）、data（数据分析）、design（设计）、engineering（工程）、enterprise-search（企业搜索）、finance（财务）、human-resources（HR）、legal（法务）、marketing（营销）、operations（运营）、product-management（PM）、productivity（个人生产力）、sales（销售）、small-business（小企业）。Anthropic 在 claude.com/plugins 上给的总账数据是「**85+ skills · 69+ commands · 40+ unique MCP connectors**」——平均到 15 个工种 plugin，每个工种 5-6 个 skill、4-5 个 command、2-3 个 MCP 连接器。

stars 增量是这件事被市场认可的硬指标。截至今日（北京时间 5 月 26 日 00:30）实查 +1,441 stars，是 Anthropic 自己除 claude-code 主仓和 claude-cookbooks 外当月增速最快的项目。

#### 1.2 单个 plugin 长什么样：以 finance 为例

每一个 plugin 都是「skills + commands + MCP connectors」的三明治：

- **skills/** 目录下是若干 markdown 文件，每个 markdown 是一段「这件事在某行业怎么干」的 SOP——文件名比如 `quarterly-close.md`、`variance-analysis.md`、`fp-and-a-monthly-package.md`。
- **commands/** 目录下是若干 slash command 定义，比如 finance 工种里有 `/close-month`、`/run-variance`、`/build-mgmt-pack`，每个 command 内置参数表 + 步骤模板。
- **connectors/** 是 MCP server 的轻量声明文件，描述这个工种要连什么外部系统——finance plugin 默认连 NetSuite、QuickBooks、Snowflake、BigQuery，可改成自家 ERP。

整套机制全是文件——没有代码、没有构建步骤、没有运行时依赖。这是 Anthropic 在产品页上原话强调的核心承诺："Every component is file-based — markdown and JSON, no code, no infrastructure, no build steps." 翻译过来一句：**配置文件就是产品**。

#### 1.3 把这套规约迁移到本地客户端的实操路径

Claude Cowork 本身要在国内合规走通还有距离，但仓库本身是 MIT-style 协议 + 全部 markdown，国内 AI 客户端要把这套搬到自己产品里几乎零摩擦：

- **Cherry Studio / Trae / 通义灵码** 都是用 MCP + skill 文件做扩展点的客户端，可以直接拷 finance / engineering / data 三个工种的 skill 文件到本地，把里面的 connector 换成「飞书多维表 / 钉钉智能填表 / 企业微信审批 / 金蝶 / 用友」就能跑。
- **OpenClaw** 作为开源个人 AI 框架，可以原样接 plugin 目录，把 model 后端从 Claude 换成 DeepSeek-V4 / 千问 3.7-Max / GLM-4.6 / Kimi 任意一家。
- 国内开发者真正拿走的不是 15 个产品，是「**怎么把通用聊天界面切成工种 agent**」这套规约——一个 `plugins/` 目录就是一份角色清单，装哪个工种 agent 就走哪个目录的 skill。

把头条 1 三段连起来看：Claude Cowork 5 月开始把 plugin marketplace 推到台前，公开的 15 个工种插件不是某天蹦出来的玩具，而是 Anthropic 自己内部用了四个月的产品种子，这种「文件即产品」的设计让国内同类客户端可以零成本继承同一套规约——**「按工种装专用 agent」**作为新的通用界面契约正式立住。

---

### 头条 2 · DeepSeek Reasonix 单日 HN top 4：99.82% prefix cache 把同一段长会话从 61 美元跑到 12 美元

![esengine/DeepSeek-Reasonix 仓库 og:image 8304 stars TypeScript MIT](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/daily/source-github-deepseek-reasonix-2026-05-26.png)

#### 2.1 5/24 HN 上榜实录：624 票 / 258 评论 / top 4

`esengine/DeepSeek-Reasonix` 5 月 24 日上 HN 首页，最终冲到当日 top 4，624 票、258 评论。仓库描述只有一行——`DeepSeek-native AI coding agent for your terminal. Engineered around prefix-cache stability — leave it running.`——MIT 协议，主语言 TypeScript，截至今日实查 8,304 stars / 449 forks。

这个项目把自己的定位写得非常窄：**不是又一个跨模型 agent，是专为 DeepSeek 一家做的 terminal 编码 agent**。作者在 README 里给的核心数据点是一条真实运营日志——2026-05-01 单日 435M 输入 token，prefix cache 命中率 99.82%，DeepSeek-V4-Flash 计价下实付 12 美元；同样的 token 量如果走没有 cache 的常规通道是大约 61 美元，**省 80%**。

#### 2.2 99.82% 是怎么做到的：byte-stable prefix 设计

prefix cache 这件事 OpenAI、Anthropic、Google 都做，但命中率能稳定推到 99% 量级的是少数。Reasonix 作者在 dev.to 上写过一篇技术解释（标题《How a DeepSeek-only agent framework hit 85% prefix cache rate and saved 93% vs Claude》），里面给的工程要点可以归到三条：

- **byte-stable prefix**——每一次对话的 system prompt、tool description、history 拼接顺序、JSON 序列化字段顺序、UTF-8 字节都和上一次完全一致，模型一侧的 KV cache 才会命中。Reasonix 把所有可能引入随机性的因素（map 遍历顺序、object 字段顺序、tool name 大小写）全部锁死。
- **session-bound 对话存储**——会话历史走本地 SQLite，不走云端转码；每次发请求前从 SQLite 直接 dump，避免任何中间序列化层。
- **DeepSeek 一家专属**——不做跨模型抽象，意味着可以贴着 DeepSeek API 的具体行为（context-cache TTL、cache invalidation rules）做工程优化。

99.82% 这个数字本身不是 Reasonix 的功劳，是 DeepSeek-V4-Flash 把 prefix cache 推到了一个量级；Reasonix 的功劳是「**把工程上能踩的坑全部踩平**」，让国内开发者跑长会话时第一次拿到一个可以扔在那里跑一周的 agent。

#### 2.3 长会话省钱方案的产品价值对位

把 Reasonix 的数据点和市面常见配置对照一下，能看到一个国内开发者过去半年最大的实操升级：

| 长会话方案 | 同样 435M 输入 token 单日花费 | 备注 |
|---|---|---|
| Claude Code + Sonnet 4.6（无 cache） | ~$543 | 默认计价，无 prompt cache |
| Claude Code + Sonnet 4.6（启用 cache） | ~$170 | 实测命中率 60-70% 区间 |
| DeepSeek-V4-Flash 原生 / 无 prefix 工程 | ~$61 | 无序列化对齐 |
| DeepSeek-V4-Flash + Reasonix（99.82% cache） | **~$12** | byte-stable prefix |

一句话：**国内开发者过去要省 Claude Code 的钱靠的是降配模型或者抠 context，今天靠的是国产 prefix 工程**。Reasonix 把这条路趟出来之后，类似的「DeepSeek-native」工程模式很可能在下一周长出多个跟随者——能看到苗头的是 Cherry Studio 的 prefix-stable 模式开关在 5 月 23 日发出来的 nightly 已经默认开启。

---

### 头条 3 · 国内云端 35 小时跑通 1,158 次工具调用：千问 3.7-Max 给训练里没见过的 GPU kernel 跑出 10× 加速

#### 3.1 35 小时是什么概念

阿里 5 月 20 日的云栖大会上把千问 3.7-Max 推到台前，关键叙事不是某个 benchmark 多少分，是一组「**单次任务连续运行 35 小时、调用 1,158 次工具、人类零干预**」的实测数据。这个组合数字在过去半年所有公开演示里是头一回——之前 Claude Opus 4.6 公开过 7 小时连跑、Codex CLI 公开过 12 小时连跑，35 小时是直接跳了一档。

3.7-Max 跑的任务是优化一段 GPU kernel：阿里给了模型一段它训练截止日期之后才出现的 CUDA 代码，目标是把这段 kernel 在 H800 上的吞吐拉高。模型自己跑 nsight-compute 看 profile、自己改 kernel、自己跑 microbenchmark、自己回头读 NVIDIA PTX 文档——35 小时之后给出的版本比起点版本快 10×。

#### 3.2 benchmark 维度对位

Artificial Analysis 5 月 22 日更新的榜上 3.7-Max 拿到 56.6 综合分，全球第 5，**国产模型里第一**。明细：

| Benchmark | 千问 3.7-Max | Claude Opus 4.6 | GPT-5.5 | DeepSeek V4-Pro Max |
|---|---|---|---|---|
| Artificial Analysis Intelligence | 56.6（第 5） | — | — | — |
| SWE-Bench Pro | 60.6 | 57.3 | — | — |
| Terminal-Bench 2.0 | 69.7（榜首） | — | — | — |
| Apex Math Reasoning | 44.5 | 34.5 | — | 38.3 |
| GPQA Diamond | 92.4 | 91.3 | 93.6 | — |
| HMMT 2026 February | 97.1（榜首） | — | — | — |

#### 3.3 1M context + 阿里云端可用

3.7-Max 是 1M token 上下文、文本进文本出、支持 Anthropic Claude Code 这类外部 harness 直接当后端调用。阿里云模型工作室计价 $2.50 / 百万输入 token、$7.50 / 百万输出——和 Claude Sonnet 4.6 同档但实际体验上能跑更长的任务。

把头条 2、头条 3 放在一起看，国内 AI 生态在这一周交出的不是单点突破，是**两侧同时发力**：Reasonix 把客户端工程做到极致，让单次会话省 80%；3.7-Max 把模型本身的长任务能力推到行业最前。国内开发者今天打开终端跑 agent 时面对的可用底座，已经不再是「只有 Claude 一家」。

---

## ⚡ 快讯速览

- **Anthropic Q2 营收冲 109 亿美元**：媒体披露 Anthropic 营收同比翻倍，预计 Q2 单季 109 亿美元，运营利润约 5.59 亿美元——比内部计划提前两年盈利。同期签下与 SpaceX Colossus 的算力协议，月付约 12.5 亿美元到 2029 年，是 Anthropic 史上单笔金额最高的算力承诺。这件事在国内同行看来重点不在估值，而在「**今后两年 Anthropic 的算力底座由 SpaceX 提供**」这条供应链信号，待观察 Google TPU / AWS Trainium 在 Anthropic 业务里的比例变化。
- **Claude Code 主版本进入 v2.1.137+**：5 月 26 日发布说明把 /plugin、/skills 改成「输入字符即时过滤」交互，Opus 4.7 设为 Fast Mode 默认；`Skill(name *)` 通配符 prefix 匹配修复，`/simplify` 命令改名 `/code-review`。
- **OpenAI 80 年 Erdős 几何反证**：OpenAI 一个通用推理模型在内部测试中给出一个 80 年未解的 Erdős 数论问题反例，公司在博客里强调这是「模型自主完成的原创数学发现」。这条新闻在国内 KOL 圈引来一轮关于「数学是否真的被模型攻克」的讨论——Yann LeCun 在 X 上反问「反例验证是不是模型自动跑了 SAT solver」，待 OpenAI 公开 paper 后再下判断。
- **HBM 占 AI 芯片成本 63%**：Epoch AI 5 月 21 日公布的 8 季度成本结构表显示，HBM 在 AI 芯片成本里的占比从 2024 Q1 的 52% 涨到 2025 Q4 的 63%；NVIDIA / AMD / Google / Amazon 四家年 HBM 总支出从 120 亿美元跳到 320 亿美元。这是上周日报头条的数据延伸，本周值得追踪的是 SK 海力士份额能否守住 57-62% 区间、长鑫存储 HBM3 量产是否赶上 2026 H1 时间表。
- **Mistral 公布开源 Claude Code 对手**：上周末 Mistral 宣布开源一款编码 agent 工具链，目标对位 Claude Code 与 Codex CLI，强调可在本地跑 7B-32B 模型链路。仓库初步 4,000+ stars，待观察的是 Mistral 是否把 Codestral 系列也开源以撑起本地端侧。
- **Karpathy 跳槽 Anthropic 余波**：上周日报头条 Karpathy 从 Eureka Labs 转 Anthropic 的消息持续发酵——multica-ai/andrej-karpathy-skills 仓库本日冲到 154,885 stars（+2,749 today），把 Karpathy 多年访谈、博客、教学视频里关于「写好 prompt」的偏好抽成一个 Claude Code 配置文件。待观察的是 Anthropic 内部会否把这套偏好集吸收为官方 skill。
- **Anthropic Cybersecurity Skills 进 Trending**：mukul975/Anthropic-Cybersecurity-Skills 9,204 stars（+1,004 today），把 MITRE ATT&CK + NIST 800-53 框架的安全检查项映射成 Claude 系列 skill，是这两天 Anthropic 系第三个进 Trending 前 10 的仓库——前两个是 knowledge-work-plugins 与 claude-cookbooks。
- **garrytan/gstack 102,444 stars**：今日 +640 stars，一组 23 个「替代 CEO / 设计师 / 工程经理 / 发布经理 / QA」的 prompt 与配置——HN 上反响两极，部分维护者把它当 reference 抄，部分把它当 AI slop 反面教材。
- **affaan-m/ECC 192,299 stars**：今日 +2,025 stars，把 skills、memory、security 三层封进单一 JavaScript 配置；这是个争议项目，部分评论质疑 stars 数据真实性——待 GitHub 一周内是否清理刷 star 行为。
- **shiyu-coder/Kronos 26,015 stars**：金融语言基础模型，+245 today，定位是「股票公告与财报语言上的小型基础模型」，对位 Bloomberg-GPT 路线但走开源。

---

## 🎙 名人说 & X 热议

- **Armin Ronacher（Flask / Jinja / Click / Sentry 作者）**：「一个看起来言之凿凿但实际诊断错误的 issue，造成的额外工作量比没有这个 issue 更多。」——Pi 项目 90 天数据：3,145 条外部贡献 / 2,504 条 AI 编自动关闭 / 最终合并 167 条（5.3%）。Simon Willison 当日博客转发把这件事推上 HN 首页 161 票。
- **Simon Willison（datasette / llm 作者）**：「本地数据分析这条线第一次有了和云端聊天产品同档体验的开源选项。」——datasette-agent 13 天 5 alpha，0.1a0 到 0.1a4 一路冲刺，把对话式 SQL 直接挂在本地 SQLite 文件上，数据不出硬盘。
- **Greg Brockman（OpenAI 总裁）**：上周末播客访谈里给的数字——「OpenAI 内部 80% 的代码已经由 AI 写」，工程师角色变成「**算力是新的稀缺资源，写代码不是**」。这条数据国内 KOL 圈普遍持保留意见，量子位编辑部 weibo 反问「这 80% 包含的是逐行 completion 还是端到端整文件」——待 OpenAI 公开方法论。
- **Yann LeCun（Meta 首席 AI 科学家）**：转发 OpenAI Erdős 反例新闻时反问「这是模型自主推导，还是模型跑了 Z3 / Lean 之类的形式化求解器？」——这一问代表了海外资深研究者对 OpenAI 数学叙事的标准怀疑姿态，待 OpenAI 公开实验记录。

---

## 📰 精选要闻

### 🔴 必读

- **anthropics/knowledge-work-plugins 翻 public** [1]：15 个工种 + 4 个工具 plugin，全 markdown + JSON，截至今日 15,399 stars / 1,861 forks。把通用 agent 切成「按职业装专用 agent」的产品契约第一次公开。详见头条 1。
- **Anthropic Q2 营收 109 亿美元 + SpaceX Colossus 月付 12.5 亿美元** [2]：单季营收同比翻倍，运营利润约 5.59 亿美元，比内部计划提前两年盈利。算力侧月付 12.5 亿美元到 2029 年——这条供应链协议把 Anthropic 长期算力底座绑定到 SpaceX 一家。

### 🟡 推荐

- **Mistral 开源 Claude Code 对手** [3]：编码 agent 工具链，目标对位 Claude Code / Codex CLI，强调本地 7B-32B 模型链路可跑。仓库初步 4,000+ stars。
- **Armin Ronacher 90 天 Issue 数据** [4]：3,145 外部贡献 / 2,504 AI 编 79.6% 自动关闭 / 167 最终合并 5.3%。给出一个四行写完的 issue 模板，让维护者抄。
- **OpenAI 80 年 Erdős 几何反证** [5]：通用推理模型给出反例，公司强调「模型自主完成的原创数学发现」。国内外 KOL 普遍要求公开实验细节。
- **Datasette Agent 0.1a4** [6]：Simon Willison 主导，13 天 5 alpha，对话式 SQL 接进本地 SQLite，权限拆 chat / explore / background 三档。

### 🔵 了解

- **Epoch AI HBM 占 AI 芯片 63%** [7]：8 季度成本表，2024 Q1 52% → 2025 Q4 63%。四家厂商 HBM 总支出 120 亿 → 320 亿美元。
- **Karpathy 偏好集 154,885 stars** [8]：单文件 skill 把 Karpathy 多年访谈与教学里的「写 prompt 偏好」抽出来。

---

## 🇨🇳 国内 AI 观察

### DeepSeek Reasonix HN top 4 + 99.82% prefix cache

`esengine/DeepSeek-Reasonix` [9] MIT / TypeScript / 8,304 stars。专为 DeepSeek 一家做的 terminal 编码 agent，5 月 24 日 HN top 4（624 票 / 258 评论）。单日 435M 输入 token / 99.82% cache 命中 / 12 美元实付（同 workload 无 cache 约 61 美元）。详见头条 2。

### 千问 3.7-Max 35 小时连跑 1,158 次工具调用

阿里 5 月 20 日云栖大会发的旗舰模型 [10]，1M token 上下文，Artificial Analysis 综合分 56.6 全球第 5、国产模型第一；SWE-Bench Pro 60.6（高过 Claude Opus 4.6 的 57.3）、Terminal-Bench 2.0 69.7 榜首、Apex Math Reasoning 44.5（高过 Claude Opus 4.6 的 34.5）。阿里云模型工作室计价 $2.50 / 百万输入 + $7.50 / 百万输出。详见头条 3。

### 华为昇腾 950 超节点 + CANN 800+ 算子开源继续发酵

5 月 24 日鲲鹏昇腾开发者大会发的昇腾 950 超节点 [11]——单超节点 8,192 卡、8 EFlops FP8 算力、16 PB/s 全互联带宽、24 TB 统一内存池；同日 CANN 50+ 代码仓 800+ 算子全面开源，BoostKit / openUBMC / openFuyao / 灵衢协议同步入驻 openEuler 与鲲鹏社区。MindSpore / MindSpeed / MindIE / MindStudio 也全部开源。国内开发者第一次拿到一套从硅片到 agent 推理服务的全栈对位英伟达 NemoClaw + CUDA + NIM 的可读可改算力栈。Atlas 950 超节点 Q4 2026 商用，最大组网目标百万卡集群。

### 智谱 GLM 提价 83% 撑住港股估值

智谱（02513.HK）[12] 5 月 22 日盘中涨超 30% 冲到 5,800 亿港元市值，距 1 月 8 日 IPO 时的 528 亿港元四个月翻 11 倍。GLM API 提价 83% + 付费 Token 增长 4 倍是核心叙事；与寒武纪 7,168 亿 / 海光 6,885 亿 / 摩尔线程 3,363 亿 / 沐曦 3,032 亿 / 壁仞 740 亿一起，国产 AI 在公开市场第一次出现清晰的估值梯队。这条线的本周看点是寒武纪 / 海光 Q2 财报的可持续性验证。

---

## 📦 GitHub Trending

### 🔴 必看

- **Lum1104/Understand-Anything · 30,961 stars · +5,604 今日 · TypeScript / MIT** [13]：在线交互式代码图谱可视化，14 家 AI 客户端接入。从昨日 25,658 stars 单日跳到今日 30,961，**+5,303 day-over-day** 是过去 30 天 Trending 单日最大涨幅之一。

![Lum1104/Understand-Anything GitHub 仓库 og:image 代码图谱在线可视化](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/daily/source-github-understand-anything-2026-05-26.png)

- **colbymchenry/codegraph · 24,861 stars · +3,161 今日 · TypeScript / MIT** [14]：100% 本地预索引代码图谱，给 Claude Code / Codex / Cursor / OpenCode / Hermes 用。7 仓 benchmark 平均省 35% 钱省 71% 工具调用。

![colbymchenry/codegraph GitHub 仓库 og:image 100% 本地代码图谱](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/daily/source-github-codegraph-2026-05-26.png)

- **anthropics/knowledge-work-plugins · 15,399 stars · +1,441 今日 · Python** [1]：见头条 1。

### 🟡 推荐

- **manaflow-ai/cmux · 19,471 stars · +603 今日 · Swift** [15]：基于 Ghostty 终端内核改的 vertical-tab 终端，原生给 AI coding agent 优化布局——每个 tab 是一个独立 agent 会话，左侧 sidebar 切换。

![manaflow-ai/cmux GitHub 仓库 og:image Ghostty 内核 AI 终端](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/daily/source-github-cmux-2026-05-26.png)

- **multica-ai/andrej-karpathy-skills · 154,885 stars · +2,749 今日** [8]：单文件 skill 把 Karpathy 偏好集抽出来，覆盖 prompt 风格、代码审查习惯、文档写作习惯。
- **Leonxlnx/taste-skill · 19,663 stars · +264 今日 · Shell** [16]：单文件配置，目标是「让 Claude 不要生成常见、平庸的输出」。
- **rohitg00/ai-engineering-from-scratch · 18,468 stars · +3,154 今日 · Python** [17]：AI 工程从零到部署的实战课程仓库。

---

## 🛠 AI Coding 工具动态

- **Claude Code v2.1.137+ 默认 Opus 4.7 + Fast Mode** [18]：5 月 26 日发布说明把 `/plugin`、`/skills` 改成「输入字符即时过滤」交互；Opus 4.7 设为 Fast Mode 默认模型；`Skill(name *)` 通配符 prefix 匹配修复；marketplace key 与 manifest name 不一致时 plugin 详情加载失败的 bug 修复；`/simplify` 命令改名 `/code-review`。
- **Datasette Agent 0.1a4** [6]：Simon Willison 主导，`datasette install datasette-agent` 一条命令安装，访问 `/-/agent` 进入聊天界面；权限拆 `datasette-agent` / `datasette-agent-explore` / `datasette-agent-background` 三档；模型走 datasette-llm 任意 provider，OpenAI / Anthropic / Gemini / LM Studio 本地模型 / Ollama 都可。
- **Anthropic-Cybersecurity-Skills 9,204 stars** [19]：MITRE ATT&CK + NIST 800-53 + ISO 27001 框架映射成 Claude / Cowork skill，给安全运营工程师抄。安装方式与 knowledge-work-plugins 一致。
- **Cherry Studio nightly 默认开 prefix-stable 模式**：5 月 23 日发出来的 nightly 把 prefix 字节稳定开关默认开启，跟随 Reasonix 路径。

---

## 🔭 值得关注

- **「按工种装专用 agent」会不会成为新通用界面契约**：Anthropic knowledge-work-plugins 开源后，Cherry Studio / Trae / OpenClaw 三家国内客户端是否在两周内出对位 plugin 目录？是否会出现一个跨客户端的「中文工种 skill 标准库」？待社区与厂商动作。
- **prefix cache 工程是否进入国产 agent 标配**：Reasonix 把 DeepSeek-V4-Flash 的 prefix 命中推到 99.82%。下一个值得观察的是 GLM-4.6 / Kimi K3 / 千问 3.7-Max 上是否会出现同等量级的开源 wrapper。
- **AI Issue 治理是否会从 Pi 项目扩散到主流仓库**：Armin Ronacher 的「**默认关闭 + 人工恢复**」规则在 HN 上获得了出乎意料的多数支持。需要观察的是 Flask、Sentry、Click 主仓库本月是否会跟进同样规则，进而扩散到 Python / Node / Rust 主流生态。
- **OpenAI Erdős 反证是否经得起同行复核**：等待 OpenAI 公开方法论与实验日志，待 6 月初是否有第三方在 Lean / Z3 上独立验证反例。
- **HBM 长鑫 H1 量产**：HBM 占 AI 芯片成本 63% 这条曲线下一个观察点是长鑫存储 HBM3 量产时间是否赶上 2026 H1，能否削弱 SK 海力士 57-62% 的份额。
- **昇腾 950 实际可用度**：CANN 800+ 算子开源 + 昇腾 950 Q4 商用，待观察的是 PyTorch / vLLM / Triton 这三条主流栈在昇腾上跑 Qwen3-Coder-30B 推理的真实延迟与吞吐数据公开。

---

## ✍ 编辑说

- **Anthropic knowledge-work-plugins / 推荐**：如果你是国内 AI 客户端的产品或工程负责人，今天读完这份开源对你 12 个月内的产品形态决策有意义——「文件即产品」的扩展规约已经被一线厂商公开背书，跟进与不跟进会在用户感知层形成显著差距。
- **DeepSeek Reasonix / 推荐**：如果你每天用 Claude Code 跑长会话，Reasonix 给出的 prefix 工程模式值得作为下一周的实测对象——99.82% cache 命中率不是营销话术，是工程模式公开后的可复现数据点。
- **千问 3.7-Max / 做技术储备**：如果你做 agent 框架或者云端长任务，35 小时连跑 + 10× kernel 加速这组数据值得作为 6 月评测基线，国产长会话能力已经进入可生产档位。
- **华为昇腾 950 / 关注**：算力栈第一次有完整开源对位，但 PyTorch / vLLM / Triton 在昇腾上的真实工程体验还要等 Q4 商用之后才有公开数据，今天读完更多是建立「国产算力栈走到哪一档」的心理坐标。
- **Armin Ronacher AI Issue / 关注**：如果你维护一个 1k+ stars 以上的开源仓库，「**默认关闭 + 人工恢复**」机制值得作为下一季度的治理选项之一——但是否启用要看你团队的人工处理带宽。
- **Anthropic SpaceX 月付 12.5 亿美元 / 观望**：对国内同行的直接产品决策影响不大，但作为「AI 头部公司算力供应链」长期信号值得记到本子上——Anthropic 不再单独押 Google TPU 或 AWS Trainium，意味着 2027-2029 年算力市场的竞争格局会比现在更复杂。

---

## 🔗 引用链接

- [1] anthropics/knowledge-work-plugins: https://github.com/anthropics/knowledge-work-plugins
- [2] Anthropic Q2 营收与 SpaceX 算力协议（媒体报道）: https://www.axios.com/2026/05/21/ai-news-cycle-openai-anthropic-spacex
- [3] Mistral 开源编码 agent: https://mistral.ai/news
- [4] Armin Ronacher 90 天 issue 数据原文: https://lucumr.pocoo.org/
- [5] OpenAI Erdős 反例公告: https://openai.com/blog
- [6] datasette/datasette-agent: https://github.com/datasette/datasette-agent
- [7] Epoch AI 8 季度 AI 芯片成本结构表: https://epochai.org/
- [8] multica-ai/andrej-karpathy-skills: https://github.com/multica-ai/andrej-karpathy-skills
- [9] esengine/DeepSeek-Reasonix: https://github.com/esengine/DeepSeek-Reasonix
- [10] 千问 3.7-Max（阿里云模型工作室）: https://help.aliyun.com/zh/model-studio
- [11] 华为 Atlas 950 SuperPoD: https://www.huawei.com/en/news/2026/3/mwc-superpod-ai
- [12] 智谱港股（02513.HK）: https://www.hkex.com.hk/
- [13] Lum1104/Understand-Anything: https://github.com/Lum1104/Understand-Anything
- [14] colbymchenry/codegraph: https://github.com/colbymchenry/codegraph
- [15] manaflow-ai/cmux: https://github.com/manaflow-ai/cmux
- [16] Leonxlnx/taste-skill: https://github.com/Leonxlnx/taste-skill
- [17] rohitg00/ai-engineering-from-scratch: https://github.com/rohitg00/ai-engineering-from-scratch
- [18] anthropics/claude-code releases: https://github.com/anthropics/claude-code/releases
- [19] mukul975/Anthropic-Cybersecurity-Skills: https://github.com/mukul975/Anthropic-Cybersecurity-Skills
