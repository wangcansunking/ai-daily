---
title: "Uber 4 个月烧光 2026 全年 AI 预算"
slug: uber-claude-code-2026-ai-budget-burn
date: 2026-05-02
weekday: 星期六
category: AI Coding · 工具经济
tags: [Uber, Claude Code, Anthropic, AI Coding, Token 计费, 国产替代]
description: "Uber 2025-12 全员推 Claude Code，到 2026-04 烧光全年 AI 预算，CTO Praveen Neppalli Naga 公开承认 'back to the drawing board'。HN 47976415 / 345 pts。截至 2026-05-02 国内 36氪 / 量子位 / 机器之心 / 虎嗅 / CSDN / 掘金 未检索到中文报道，auto-research arbitrage 选题。"
cover: uber-claude-code-2026-ai-budget-burn.png
---

# Uber 4 个月烧光 2026 全年 AI 预算

> 12 月推、2 月翻倍、4 月见底——Uber 把全年 AI 预算用完只花了 4 个月。CTO Praveen Neppalli Naga 把话说得很直白：「我现在重做预算，因为原本以为够用的那一份，已经被打穿了。」HN 47976415 顶帖 345 分、374 评论。这是大型企业第一次把 Claude Code 全员铺开后的真实账单。

![Uber 4 个月烧光 2026 全年 AI 预算](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-02/uber-claude-code-2026-ai-budget-burn/uber-claude-code-2026-ai-budget-burn.png)

## 一、事件还原：从 12 月铺开到 4 月见底

时间线很短。2025 年 12 月，Uber 把 Claude Code 的访问权限发到工程团队。CTO Praveen Neppalli Naga 在 The Information 应用 AI newsletter 的访谈里复盘——最初的预算是按"工程师辅助补全"这个量级做的：每人一个席位、按月订阅、年初财年规划时就锁定。

2 月份用量翻倍。Naga 描述的转折点是工程师摸清了 Claude Code 的多步骤能力——异步 agent、长上下文调试、跨文件改动、CI 失败自动修复。这些场景一旦摸熟，单次会话的 token 消耗就从"几千"跳到"几十万"。

到 4 月，账单已经吃掉全年的 AI 预算盘子。Naga 在访谈里这样说：

> "I'm back to the drawing board, because the budget I thought I would need is blown away already."
>
> —— Praveen Neppalli Naga, Uber CTO（The Information / Anissa Gardizy 转引，2026-04-30）

「重做预算」这句话在英文里有一个惯用语义——不是"略作调整"，而是"原方案作废，重新画图"。这背后是几个具体数字：

- **95%** 的 Uber 工程师现在每个月都在用 AI 工具
- **70%** 的提交代码由 AI 生成
- **11%** 的后端 live 代码由 AI agent 直接写出
- 单工程师 API 月度账单 **$500 - $2000**（开发者社群推算 + The Information 报道，非 Uber 逐人逐笔披露）
- Uber 年度 R&D 预算约 **$3.4 B**（来自 Benzinga / Yahoo Finance 引用的财报口径，与 AI 单项支出不是同一笔）

值得点名的是数字口径——一些海外科技博客转载这则新闻时把"$3.4B 年度 R&D"和"AI 烧掉的钱"混作一谈，写出"Uber 在 Claude Code 上烧了 34 亿美元"的标题。这是误读。$3.4 B 是 Uber 全年研发盘子，AI 工具账单是其中一个子项。HN 上 ninjagoo 那条评论给出了一份合理估算——按 5500 名工程师 × $1250 月度中位数 × 4 个月，AI 实际花费量级在 $2.75 M 至 $12 M 之间，约占年度 R&D 的 0.3% - 1%。账单是把"AI 工具预算"这个口袋打穿了，不是把整张研发表掀翻。

紧跟其后的动作是测试 OpenAI Codex，作为 Claude Code 的成本对照实验。Anthropic 同期内部讨论的处置方向是"让 Cursor 这条线在 Uber 内部稳定下来作为补充"——但 The Information 同期数据显示，Uber 工程师的 Cursor 用量在过去两个月已经平稳，主力工具是 Claude Code。

![HN 47976415 顶赞五条评论](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-02/uber-claude-code-2026-ai-budget-burn/hn-uber-claude-code.png)

## 二、HN 上一线工程师在讨论什么

HN 顶帖 345 分、374 评论。比新闻更值得读的是评论区——美国和欧洲一线工程师把"AI 工具账单怎么算"这件事掰得很细。挑出 5 条最有信息量的。

**fidotron**（顶赞）：

> "It's actually incredible the extent to which non devs imposing KPIs on devs underestimate how badly this will get gamed, whether it's AIs, PR/line counting or whatever."

把"工具用量"当 KPI，开发者迟早会优化指标本身——这是软件工程经验里反复验证过的 Goodhart 定律。fidotron 这条评论的潜在含义是——70% commit 来自 AI、95% 月活这两个数字，已经接近被推着往上走的阶段。

**joshuastuden**（同链次顶）：

> "Exactly. At Cerebras I know of several people who burn tokens on completely USELESS tasks (randomly changing pixels in an image) just to keep them high up on the token leaderboard."

来自 Cerebras 一线工程师的实地观察——内部有 token 用量排行榜后，确实有人把 token 烧在毫无意义的任务上。这条评论把"指标即目标"这个抽象概念落到具体场景。

**ageitgey**（讨论 Anthropic 计费模型）：

> "Anthropic has a very 'interesting' business model where you get subscription pricing as long as you are under 150 employees. When you hit 151, you have to start paying API prices overnight for everyone, and your total bill instantly multiplies."

这条评论戳中了 Uber 这则新闻的真正机制——Claude Code 的 Team plan 在 150 人门槛上有一道悬崖。员工数过线就强制切到 Enterprise，原本按席位订阅的可控成本，瞬间变成按 token 透传的不可控账单。Uber 的工程团队规模约 5500 人，远超这道线，结构上从一开始就只能跑 API 计费。

**brightball**（小团队视角）：

> "I evaluated the pricing and could not justify the jump to Enterprise from Team. You lose the monthly subscription entirely when you jump to enterprise so you lose your ability to control costs."

来自一位评估过 Anthropic Enterprise 但选择不升级的工程负责人。原话直陈痛点——Enterprise 没有 rolling cap，意味着没法对单工程师的月度上限做硬限制。换言之，CFO 在年度预算评审里要把"上不封顶"四个字写进合同。

**abuani**（个人开发者视角）：

> "I take a peak every month or so at spend for my company and notice more and more are consumed $1k in tokens a month and it is bewildering to me how. I use llms daily, and see anywhere from $200-$400 tops."

abuani 自己是大模型重度用户，每月也只到 $200-$400。他对同事每月烧 $1000 token 表示费解。这条评论提示了一个真实分布——同样是"日常使用大模型"，工程师之间的实际 token 消耗能差 5 倍以上。

需要特别说明的是，HN 评论区里 mkozlows 提出了对 $500-$2000 这个区间的质疑，认为该数字在 The Information 原文中找不到对应来源、可能是 briefs.co 这家二手报道源在转述时加进去的。这是数字争议待澄清的地方——本文采用的处理是：把 $500-$2000 标注为"开发者社群推算 + 二手报道"，不归到"Uber 官方披露"。

## 三、计费模式拐点：席位制时代结束，token 计费时代开始

把过去 18 个月 AI Coding 工具的计费演进画成一条曲线，能看见一个清晰的拐点。

![AI Coding 工具计费模式拐点](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-02/uber-claude-code-2026-ai-budget-burn/ai-coding-pricing-shift.png)

**2023-2024 年：席位制时代。** GitHub Copilot $10/月、Cursor Pro $20/月、ChatGPT Plus $20/月——所有 AI Coding 工具的主力计费形态是按人头按月。这套模式下，CFO 把"工程师人数 × 月费 × 12"算出来就锁定了年度预算，厂商承担算力风险。Anthropic 当时还没有自己的 IDE 形态产品。

**2025 年：异步 agent 出现，单次成本失控。** Claude Code、Cursor 的 Background Agent、Devin、Mistral Vibe Remote 这一批工具把"一次会话"的 token 消耗推到了千万量级——长上下文调试、跨仓库改动、CI 失败自动修复这些场景，单次跑下来要几十甚至几百万 token。HN 评论区里 internetter 引用 Forbes 一条数据——Cursor 在 2025 年 3 月估算过：「一份 $200/月 的 Claude Code 订阅可以消耗高达 $5000 的算力，说明 Anthropic 在补贴。」

**2026 年：拐点。** Anthropic 把 Claude Code Team 的 150 人门槛硬化、把 Enterprise 转向 API 计费透传，本质是把"补贴时代"翻篇。Uber 这条新闻是大型企业第一次集体撞上这堵墙——按席位订阅时算出的预算，按 token 透传后算出的账单，量级完全不同。

GitHub Copilot 同样在 2025 年 12 月把 Premium Request 改成可买扩展包的 token 模式，Mistral Vibe Remote 在 4 月 30 日上线异步云端 agent 时也明确按"任务量"计费，不是按席位。整个行业的计费引力正在从订阅制移向 token 透传——Uber 这条数据是把这个移位的成本量级第一次摆到台面上。

| 计费时代 | 周期 | 主要产品 | 单工程师月费 | CFO 可预测性 |
|---|---|---|---|---|
| 订阅制 | 2023-2024 | Copilot / Cursor / ChatGPT Plus | $10-$20 | 高 |
| 混合期 | 2025 上半 | 同上 + 限速规则 | $20-$60 | 中 |
| Token 透传 | 2025 下半 - 2026 | Claude Code Enterprise / Cursor Pro+ / Codex API | $500-$2000+ | 低 |

## 四、国内对照：Qwen3-Coder、DeepSeek、通义灵码、Qoder 的实操路径

国内 AI 开发者面对同样的拐点，能选的路其实比海外宽。这里不做行动建议，只把对应方案的具体价位和能力点摆出来。

![AI Coding 工具月度成本对比表](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-02/uber-claude-code-2026-ai-budget-burn/ai-coding-cost-comparison.png)

**第一类：Claude Code 的成本控制路径。** Claude Code Pro $20/月本身没问题，问题在于触发 Enterprise 转切后单工程师月度无上限。控制成本的可行做法是 sub-agent 拆分——把 main agent 用 Sonnet、子 agent 用 Haiku 处理子任务、调度层加 token cap。Anthropic 公开的 Claude Code Hooks API 支持自定义 token 阈值告警；超阈值后回退到 Anthropic Team plan 的硬上限模式（牺牲部分能力换可预测）。

**第二类：API 中转省钱路径。** 走 OpenRouter / Anthropic 直连的差异主要在于折扣和路由。OpenRouter 上 Claude Sonnet 的输入价大致与官网持平，但配合 Cline / Roo Code 这类开源 client，可以避开 Enterprise 跳价。这条路对小团队（≤30 人）最划算，对超过 150 人的团队来说还要考虑合规与审计。

**第三类：国产 IDE / IDE 插件。** 通义灵码 Plus 包月 ¥168，含千问 Plus 调用配额；Qoder（阿里出的 vibe coding IDE）走订阅+按量混合，按 token 透传到底层 Qwen3-Coder 480B；Trae（字节）按席位+任务包；Cursor 国区订阅价与海外一致，但调底层模型时可以接 DeepSeek、千问、智谱 GLM。能力维度上 Qwen3-Coder 480B 在 SWE-Bench Verified 上的开源最佳成绩是去年公开的，作为 Claude Sonnet 的本地化替代覆盖到大多数生产场景。

**第四类：自建本地推理。** 配合 Qwen3-Coder 30B（4090/4090D 单卡可跑、INT4 量化）做日常补全，云端兜底用千问 Plus 或 DeepSeek V3.5 API（¥1/M 输入、¥4/M 输出量级），单工程师月度成本能压在 ¥200 以内。这条路的成本下限最低，但需要团队有运维本地推理服务的能力。

把四条路按"团队规模 × 成本可预测性"排个序，结论是清晰的——

- 个人开发者 / ≤10 人小团队：Cursor Pro 或 Claude Code Pro 个人订阅 + 国产 API 应急；月成本上限 $20-$40
- 10-150 人中型团队：通义灵码 Plus / Qoder / Trae + DeepSeek API 兜底；人均 ¥150-¥300
- 150 人以上企业：自建 agent 调度层 + 国产模型 API + 本地推理混合架构；按集群算账，不按席位算账
- 5000 人以上巨型工程团队（Uber 这一档）：必须跑自建 agent infra + 多供应商比价 + token 异常监控

国内已经有几家头部互联网公司在跑第四档方案——美团 LongCat 万亿基座配合内部 agent 调度、阿里通义灵码内部版按部门做 token 配额、字节 Doubao Coding 接 Trae。这套基础设施 Uber 现在正在重建，相当于补一段国内大厂已经走过的路。

## 五、几条值得记住的判断

把这件事的几个层次按一线工程师视角整理。

**第一条：Claude Code 不是"贵"，是"账单形态变了"。** $20/月订阅时代结束，跨过 150 人门槛后实质就是按 token 透传。CFO 评估 Enterprise 合同时要把"上不封顶"四个字写进风险栏，不要按月费 × 人头估算。

**第二条：5500 人工程团队 4 个月烧光 AI 预算，是制度问题不是工具问题。** Uber 撞上的不是 Claude Code 太贵，而是预算编制的颗粒度还停在"席位制时代"。新一轮预算重做大概率会引入"token 池配额 + 部门季度上限 + 异常告警"这套机制。这套机制国内大厂在算力配额管理上早已实践，是 AI 工具治理的下一个标配。

**第三条：开源国产模型的本地化部署对中型团队的吸引力会进一步上升。** Qwen3-Coder 480B 开源后，团队自建推理 + 调度层的总成本对 50-300 人这一档非常友好——比 Claude Code Enterprise 透传便宜 5-10 倍，能力差距在大多数生产场景里读起来不大。这是 Uber 这条数据给国产开源模型生态做的免费体检报告。

**第四条：token 监控仪表盘要成为 AI Coding 团队的标配。** Uber 这次撞墙的本质，是用量从"人均几千 token"跳到"人均几十万 token"时缺乏早期预警。国内团队在自建 agent infra 时把这一层做扎实——按工程师 / 项目 / 任务三个维度统计 token 消耗、设阈值、建告警——边际成本极低，但能避免下一次同样的事故。

**第五条：HN 评论区里 fidotron 那条 KPI gaming 的提醒值得每位工程负责人记住。** 70% commit by AI、95% 月活这两个数字一旦进入绩效评估体系，开发者会优化指标本身。把"AI 生成比例"作为生产力代理变量本身就是反 Goodhart 法则的——真正要测的是交付质量、缺陷率、p95 响应时间这些下游变量。

Uber CTO 这次把账单和窘境直接讲出来，反而是行业里少见的坦诚。比起把"AI 把工程效率推高 2.7 倍"贴满 KPI 仪表盘的 PR 稿，"我重做预算"这句话更接近一线工程师每天面对的真实场景。

## 六、共勉：这是国内 AI 开发者难得的好窗口

把视角拉远——Anthropic 把订阅制时代翻篇，并不只是 Claude Code 一家的定价调整，而是整个 AI Coding 工具生态进入"算力即成本"阶段的信号。海外大厂的预算压力会变成两件具体事：一是更多企业开始评估开源国产模型作为成本控制路径，二是异步 agent / 长上下文这些高 token 场景的部署门槛会重新被审视。

国内 AI 开发者站在一个相当好的位置。底座层有 Qwen3-Coder 480B、DeepSeek V3.5、智谱 GLM-4.6、Kimi K2 这一排开源 / 半开源选项；工具层有通义灵码、Qoder、Trae、Cursor 国区版、Roo Code 这些可选 IDE；推理层有阿里云、火山引擎、腾讯云、华为云、PPIO、硅基流动等多家可比价的 API 供应商。这套基础设施的横向覆盖度在全球范围内都是稀缺资源——相当于把"按 token 透传"这条路上每一段都做了国产对标。

更值得说一句的是——Uber 这条数据让 AI Coding 真实的生产力账单第一次摊到台面上。70% commit 由 AI 生成、11% 后端 live 代码由 AI agent 写——这些数字背后是真实的工作流变化，不是 PR 里那种空泛说辞。国内做底座的同行（DeepSeek、千问、智谱、Kimi、Moonshot、月之暗面、智源、百川、文心、豆包、小米 MiMo、360 智脑），手头积累的每一份 SWE-Bench 评测、每一次部署调优、每一段 agent 调度脚本，都是和 Anthropic、OpenAI 站在同一前沿的实战经验。

下一程的看点很清晰——谁能把"自建 agent infra + 多供应商比价 + 国产模型替代"这套组合拳跑通，谁就有机会承接海外大厂从 Claude Code Enterprise 流出的预算压力。这件事，国内中型互联网公司、AI 创业公司、独立开发者，都在路上。共勉。
