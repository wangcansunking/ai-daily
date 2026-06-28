---
title: "三个全天候个人 AI 助手，国内怎么选"
slug: gemini-spark-always-on-agent-showdown-2026-06-01
date: 2026-06-01
cover: gemini-spark-always-on-agent-showdown-2026-06-01.png
tags: [Gemini Spark, Claude Cowork, ChatGPT Agent, 个人AI助手, AI Agent, 谷歌, 国产大模型]
category: auto-research
track: big-news
description: "谷歌 Gemini Spark 5 月 30 日向美区 Google AI Ultra 订阅者开放，每月 100 美元起，跑在谷歌云上，电脑手机关机它也在干活。这是 2026 年第三个走红的全天候个人 AI 助手——另两个是 Anthropic 的 Claude Cowork 和 OpenAI 的 ChatGPT Agent。这篇把三家的运行架构（云端常驻 vs 本地桌面 vs 会话虚拟机）、能力边界、价格、隐私模型摆到一张表里横着比，再说清楚国内能不能用、国产豆包系 ArkClaw、智谱 AutoClaw、Kimi 谁在对位。"
weekday: "星期一"
---
# 三个全天候个人 AI 助手，国内怎么选

![三个全天候个人 AI 助手如同夜里持续运行的光球，谷歌 Spark 借一束光连向远端云端数据中心，在设备休眠时仍在工作](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/gemini-spark-always-on-agent-showdown-2026-06-01/gemini-spark-always-on-agent-showdown-2026-06-01.png)

100 美元一个月，换一个关了电脑还在替你干活的 AI 助手——这是谷歌 5 月 30 日摆到美区用户面前的价码。这一天，谷歌把 I/O 上预告的 Gemini Spark 正式开放给了 Google AI Ultra 订阅者，安卓、iOS、网页端同时上线。它最招人的一句宣传是：给它一个任务，它在后台 7×24 小时干，**哪怕你的手机和笔记本都关着机。**

这不是孤例。把时间往前拨，2026 年已经有三个走红的"全天候个人 AI 助手"先后登场：年初 Anthropic 把 Claude 做成了桌面智能体 Cowork，OpenAI 在更早的 2025 年 7 月就给 ChatGPT 配了能自己开浏览器干活的 Agent 模式，现在谷歌带着 Spark 入场。三家都想回答同一个问题——**当一个 AI 助手可以不用你盯着、自己把多步骤的活儿干完，它到底该长成什么样。**

这篇文章想讲清楚三件事：**这三家在架构上走了三条不同的路、各自的价格和隐私边界差在哪、以及最实在的一条——国内能不能用、国产有没有对位的东西。** 先把结论放这儿：三家看着都叫"个人 AI 助手"，但底子完全不同——谷歌押注云端常驻，Anthropic 守在本地桌面，OpenAI 站在会话触发的虚拟机上；三家眼下都没在国内直接开放，而国内这条赛道并不空，字节、智谱、月之暗面各有各的打法。

## Gemini Spark：把助手搬到云上，让它替你"熬夜"

先说这次的主角。Gemini Spark 最关键的一点不在功能清单，而在它跑在哪——**它跑在谷歌云上，不是你的手机或电脑上。**

这决定了它那句"设备关机也在干活"不是营销话术。传统的 AI 助手要么是你打开 App 跟它聊、关了就停，要么是装在你电脑上、电脑睡眠它也跟着停。Spark 把执行环节整个挪到了谷歌的服务器：你下了一个指令，比如"盯着这几家公司的实习岗位、有新的就整理给我"，它就在云端持续跑这个任务，你这边手机锁屏、笔记本合盖，都不影响它继续盯。

![谷歌 Gemini 官方页面对 Spark 的定位：7×24 小时在你身边，给它任务它在后台一直做](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/gemini-spark-always-on-agent-showdown-2026-06-01/source-spark-gemini-overview-2026-06-01.png)

底层模型是谷歌 5 月 19 日发布的 Gemini 3.5 Flash，外面套着一层名叫 Antigravity 的智能体框架——这是谷歌那套能并行调度多个子任务的智能体平台。谷歌把 3.5 Flash 定位成"为行动而生、不只是回答问题"的默认模型，Gemini App 和搜索里的 AI 模式现在都换成了它，Spark 是它在"个人助手"这条线上的落地。

Spark 能接的活儿按谷歌的划分有几类：跨 Gmail、日历、Drive、文档、表格、幻灯片、YouTube、地图的多步任务；按时间或条件触发的自动化；还有可以自定义的"技能"——比如让它学你过往邮件的语气，做一个"代笔"技能帮你起草邮件。WIRED 一位编辑实测时把生活的一大摊子交给了它，体验里既有它真能跨应用把事串起来的惊喜，也有它会自作主张的别扭——这正是这类全天候助手共同的尴尬：越自主，越需要你想清楚授权到哪一步。

谷歌在隐私上做了几道明确的设计：

- **数据源默认关闭**：Gmail、日历、Drive 这些连接，得你一项一项手动打开
- **不无差别翻邮件**：谷歌明确声明它不会随意读你的全部邮件
- **重要动作先确认**：尤其是之后要上线的"能替你花钱"，它被设计成动手前先问你一声

换句话说，谷歌很清楚把一个常驻云端、连着你全部个人数据的智能体交给用户，信任是要一点点建立的，所以它把"自主"和"先问你一声"绑在了一起。

价格这块要说清楚：Spark 不单卖，它捆在 Google AI Ultra 这个订阅里，**入门价每月 99.99 美元起**（约合人民币 720 元）。有意思的是这个套餐的定价一路在调——它最早传出来是每月 250 美元，后来降到 100 美元这一档，Spark 才跟着开放。

眼下它给的是美区、年满 18 岁的 Ultra 订阅者加部分企业用户，谷歌说会在接下来几周快速扩量。今年夏天还要给 Spark 加上"能替你花钱"的能力，并登陆桌面版 Gemini App。

## Claude Cowork：守在你电脑上的那一个

第二个要比的是 Anthropic 的 Claude Cowork。它和 Spark 走了恰好相反的一条路——**Cowork 跑在你自己的电脑上，不是云端。**

Anthropic 给它的定位是"给其余工作用的 Claude Code"。Claude Code 本来是给程序员的，但 Anthropic 发现自家市场、数据这些非技术团队也绕开聊天框去用 Claude Code，图的就是它能干多步骤的复杂活儿。Cowork 就是把这份能力做成一个不需要技术背景的桌面版：它直接读写你电脑上的本地文件和文件夹，操作你日常用的那些应用，跨 Google Drive、Gmail、日历、Slack、Excel、PowerPoint 把信息汇到一起，把活儿干完再交给你。

它真正改变体验的那一块叫 Dispatch（派发）。你布置一个任务，关掉 App，Cowork 还在后台接着跑，盯着条件、该动手时动手，干完或者需要你拿主意时再回来找你。配套还有几样：

- **定时任务**：日报、周报这种重复活儿按时间表自动跑，不用写代码
- **Computer Use（电脑操作）**：碰上没有接口的老软件，它能像人一样动鼠标键盘、点按钮填表格
- **auto 模式**：一个分类器在每个动作执行前先筛一遍，安全的放行、有风险的拦下来或升级给你确认

这套设计的用意是，让长时间自己跑的智能体不至于每隔几分钟就停下来问一次权限，也不至于完全没人看着。

这里有个要点容易被宣传话术盖过去：**Cowork 的"后台持续"有个前提——你的机器得开着、Cowork 得在跑。** 它不像 Spark 那样把执行整个搬到云上，所以你合上笔记本、关了机，本地这一侧的任务也就停了（手机派发到桌面执行的衔接是另一回事）。这是本地优先路线天生的代价，换来的是另一头的好处：你的文件、你的操作都发生在自己电脑上，掌控感和隐私边界更直观。

价格上 Cowork 反而是三家里门槛最低的——它**含在 Claude 的各档付费订阅里**，入门的 Pro 个人版每月 17 美元起（年付，约合人民币 122 元），不额外收费。它的用量是按"5 小时一个共享池"来算的：聊天、Cowork、Claude Code 共用同一份额度，复杂任务消耗得快些，到顶就得等窗口重置。

## ChatGPT Agent：会话一关就停的那一个

第三个是 OpenAI 的 ChatGPT Agent。它入场最早——2025 年 7 月 17 日上线，但走的又是第三条路：**它跑在 OpenAI 给的一台云端"虚拟电脑"上，但本质是会话触发的，不是常驻的。**

它的工作方式是：你在 ChatGPT 里点开"Tools"选 agent 模式，用大白话描述任务，它就在一台自带的虚拟电脑上开干——这台虚拟机里有可视化浏览器、纯文本浏览器、能跑代码的终端，还能连 Gmail、GitHub 这些应用拉数据。需要登录的网站，它会开一个特殊的浏览器视图让你安全登进去，它再接着深入操作，比如查收件箱、替你填表。它是 OpenAI 把更早的 Operator（网页操作）和 Deep Research（深度检索）两个工具合并升级来的，能在浏览、跑代码、出文件之间无缝切换，整个过程你随时能打断、改方向、暂停，它从断点接着来。

![Anthropic Claude Cowork 把同一份智能体能力做成桌面版，直接在你电脑的本地文件和应用里干活](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/gemini-spark-always-on-agent-showdown-2026-06-01/source-spark-claude-cowork-2026-06-01.png)

但它和前两家最大的区别是：**ChatGPT Agent 是"一次会话"的智能体，会话结束、任务交付，它就停了，不会在你不在的时候继续替你盯着什么。** 它没有 Spark 那种云端常驻，也没有 Cowork 那种派发后台。你要它干什么，它当场干完给你，仅此而已。这让它更像一个随叫随到、能力很强的临时工，而不是一个一直在岗的助手。

价格按 ChatGPT 的订阅档走，配的是"agent 任务次数"配额：Plus 档每月 20 美元（约合人民币 144 元），含 40 次 agent 任务；Pro 档每月 200 美元（约合人民币 1440 元），含 400 次；Business 席位每人每月按年付约 20 美元，每席 40 次。超了之后企业版可以用 workspace 额度按次续，一次 agent 消息约 30 个额度点。免费版和 8 美元的 Go 档不含 agent 模式。

## 三家摆到一张表上：到底差在哪

把上面三段拆开的东西并到一起，差异就清楚了。下面这张表把架构、价格、国内可用性放在一处，方便直接对照。

![三家全天候个人 AI 助手横评表：运行位置、设备关机后是否继续跑、触发方式、入门月费、任务配额、国内可用性逐项对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/gemini-spark-always-on-agent-showdown-2026-06-01/source-spark-three-way-matrix-2026-06-01.png)

几个判断可以直接落下来：

- **要"真·全天候、电脑关了也在干"，只有 Spark 一家做到了。** 因为它把执行搬上了云。Cowork 的"后台"绑在你开机的机器上，ChatGPT Agent 干脆是会话一关就停。
- **要"我的数据全程在自己电脑上"，选 Cowork。** 本地优先是它的立身之本，代价是设备得开着。
- **要"能力强、随叫随到、不用养着它"，ChatGPT Agent 最划算。** 20 美元就能上手，适合临时甩一个复杂任务给它。
- **价格这条最反直觉：** 最贵的不是功能最全的，而是常驻成本最高的——Spark 捆在 100 美元的 Ultra 里，因为云端 7×24 替你跑是实打实烧机器的；Cowork 反而是含在 17 美元订阅里门槛最低，因为执行成本落在你自己电脑上。

还有一点三家是一致的：**它们都把"先问你一声"做成了硬约束。** 谷歌重要动作前确认、Anthropic 用 auto 模式的分类器筛动作、OpenAI 让你随时打断——没有谁敢把一个连着你全部数据、能替你做事的智能体做成完全放手的。这说明这个阶段行业的共识是：自主能力越强，人在环里（human-in-the-loop）的那道闸就越不能省。

## 国内可用性与国产对位横评

最后说最实在的一条。上面这海外三家眼下都**没有在国内直接开放**：谷歌那款限美区 Ultra 订阅，另两款在国内也都没有官方可用的入口。所以对国内用户来说，与其等它们进来，不如看看本土这条赛道——它并不空。

这波"全天候个人助手"的国内引信，其实是年初一个叫 OpenClaw 的开源项目。它由一位奥地利开发者做出来，主打"本地优先 + 真正自主执行"，在硅谷火了一把，早期用户把整个生活交给它做消息和日程自动化，闹出过不少乌龙，但也把"个人 AI 助手"这个品类彻底点着了。国内厂商很快跟上：

- **字节火山引擎 ArkClaw**：定位是云端托管版的 OpenClaw，主打零门槛、零代码，登录火山方舟就能一键创建，自然语言下指令。它对标的正是 Spark 那套"关了电脑任务还在后台跑"的云端常驻模式，给每个用户一份专属云服务器资源、不暴露公网端口，还能跨飞书、钉钉、企业微信发消息，对接对象存储同步文件。这是国内离 Spark 路线最近的一个。
- **智谱 AutoClaw（澳龙）**：智谱 AutoGLM 团队 3 月发布，定位是国内首个真正一键安装的**本地版** OpenClaw——下载装好就能在自己电脑上跑。这条本地优先的路线，恰好对位的是 Cowork。
- **月之暗面 Kimi**：Kimi K2.5 本身有很强的工具调用和智能体能力，官方提供了一整套工具（联网搜索、代码执行、Excel 分析、记忆存储等），开发者能基于它快速搭专业智能体。它的长处在长文本理解和多文档处理，更偏内容创作和专业文档分析，办公自动化、定时任务这类"全场景事务"覆盖得相对浅一些。
- **开源这一支也热闹**：像 CountBot 这类轻量中文优先框架，两万行出头的代码就实现了记忆系统、定时任务、多渠道（飞书/钉钉/QQ/微信），深度适配智谱、千问、Kimi、MiniMax、DeepSeek 这些国产模型，给想自己折腾的开发者留了口子。

![两条架构主线映射图：云端常驻一线海外是谷歌 Spark、国内对位字节 ArkClaw，本地优先一线海外是 Claude Cowork、国内对位智谱 AutoClaw，ChatGPT Agent 走会话触发第三条路单列](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/gemini-spark-always-on-agent-showdown-2026-06-01/source-spark-route-map-2026-06-01.png)

所以格局大致是这样：**海外三家用三条不同的架构定义了"全天候个人助手"该长什么样，国内则沿着同样的两条主线——云端常驻（字节 ArkClaw）和本地优先（智谱 AutoClaw）——给出了能落地的对位答案，模型底座换成了 GLM、Kimi、豆包、千问这些国产大模型。** 对国内用户来说，想要 Spark 那种"关机也在跑"的体验，ArkClaw 是现成的路；想要 Cowork 那种"数据在本地"的踏实，AutoClaw 和开源框架都能上手。

回到开头那个 100 美元的问题。它真正的价值不在那个价签，而在它逼着整个行业把一件事摆上了台面——AI 助手正在从"你问它答"的工具，长成"你不在它也在干"的伙伴。三家用三条路在试错，国内沿着同样的方向在补位，模型、框架、隐私模型一块往前走。这条路还早，但方向已经清楚，而且离我们并不远。

---

## 参考链接

[1] Google's Agentic AI Tool Gemini Spark Is Now Available — PCMag：https://ca.pcmag.com/ai/15947/googles-agentic-ai-tool-gemini-spark-is-now-available
[2] Gemini Spark — 你的 7×24 个人 AI 助手（谷歌官方）：https://gemini.google/overview/agent/spark/
[3] Gemini 3.5：frontier intelligence with action — Google Blog：https://blog.google/innovation-and-ai/models-and-research/gemini-models/gemini-3-5/
[4] Gemini 3.5 Flash is Google's new default AI model — Digital Trends：https://www.digitaltrends.com/computing/gemini-3-5-flash-is-googles-new-default-ai-model-and-its-built-to-act-not-just-answer/
[5] Hands-On With Gemini Spark — WIRED：https://www.wired.com/story/google-gemini-spark-ai-agent-hands-on/
[6] Google introduces Gemini Spark — TechCrunch：https://techcrunch.com/2026/05/19/google-introduces-gemini-spark-a-24-7-agentic-assistant-with-gmail-integration/
[7] Google's 24/7 AI agent Spark rolls out to Ultra subscribers — Android Police：https://www.androidpolice.com/gemini-spark-starts-landing-for-ai-ultra-subscribers/
[8] Claude Cowork — Anthropic 官方产品页：https://www.anthropic.com/product/claude-cowork
[9] Claude Cowork: The Always-On AI Agent for Knowledge Workers：https://jameskillick.co/blog/claude-cowork-knowledge-workers
[10] OpenAI unveils 'ChatGPT agent' — VentureBeat：https://venturebeat.com/ai/openai-unveils-chatgpt-agent-that-gives-chatgpt-its-own-computer-to-autonomously-use-your-email-and-web-apps-download-and-create-files-for-you/
[11] ChatGPT agent — OpenAI Help Center：https://help.openai.com/en/articles/11752874
[12] ChatGPT Operator Pricing 2026 — o-mega：https://o-mega.ai/articles/chatgpt-operator-pricing-what-does-it-cost-you-2026
[13] 2026 年 AI Agent 平台对比：火山引擎 ArkClaw vs Kimi AI — 火山引擎：https://www.volcengine.com/article/36659
[14] AutoClaw — 智谱一键安装本地 AI Agent：https://sofindai.com/tools/autoclaw
[15] 用 Kimi K2.5 模型搭建 Agent — Kimi 开放平台：https://platform.kimi.com/docs/guide/use-kimi-k2-to-setup-agent
[16] CountBot — 中文优先开源个人 AI Agent：https://github.com/roydonGuo/CountBot
