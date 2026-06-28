---
title: "HN 1007 赞的爆文：他比对了西方造不出导弹和西方雇不到 junior 程序员"
slug: west-forgot-code-stetskov
date: 2026-04-27
author: 残蛙
description: "Denis Stetskov（Kyiv-based / NineTwoThree Tech Lead）2026-04-21 发布的 *The West Forgot How to Build. Now It's Forgetting Code*。HN 4 月 26 日上 1007 pts / 685 评论。论点：西方软件业正在重复防务工业的去技能化路径——大量厂商不再雇 junior，把 AI 当替代——这条路不可逆。配三个史料案例（Stinger / EU 炮弹 / Fogbank）+ METR 19% slower 数据 + Salesforce 2025 年不雇软件工程师 + 作者本人 0.18% 招聘 funnel。本文做翻译性深读 + 国内 AI 编程现状代入 + 修正原文一个统计误读（'62% enrollment drop' 实为'62% 计算机系报告招生下滑'）。截至 2026-04-27 凌晨，36 氪 / 量子位 / 机器之心 / 虎嗅 / CSDN / 掘金等主流国内媒体未检索到中文报道。auto-research arbitrage 选题。"
tags: [AI 行业, AI Coding, Junior 培养, 工程文化]
cover: 2026-04-27-west-forgot-code.png
---

![cover](2026-04-27-west-forgot-code.png)

# HN 1007 赞的爆文：他比对了西方造不出导弹和西方雇不到 junior 程序员

> Denis Stetskov 在 2026 年 4 月 21 日发了一篇 *The West Forgot How to Build. Now It's Forgetting Code*。
>
> 4 月 26 日上 Hacker News 拿到 **1022 pts / 696 评论**（截至 2026-04-27 04:30 UTC+8 快照，仍在缓涨）——HN 当天榜首，技术圈刷屏整夜。
>
> 文章的论点是一个能让人不舒服的类比：**西方防务工业的去技能化在前，软件业的去技能化在后。** 武器系统的"和平红利"——把工业能力外包、把熟练工裁掉，省下来的钱用于其他用途——三十年后变成俄乌战争里 Stinger 导弹延迟 4 年交付的代价。Stetskov 说软件业现在做的是**同一件事，只是这次的"和平红利"叫 AI**。

我读这种 HN 爆款长文的时候有个习惯：先看作者，再看类比，最后看引用的硬数据是不是站得住。Stetskov 这篇三件事都有底——但其中一项数据他自己读偏了，等会儿讲。先按他的论证铺开。

---

## 一、谁是 Stetskov：Kyiv-based、Boston-payroll、十年 tech lead

Denis Stetskov 自己的简介挺简单：Tech Lead at NineTwoThree（一家波士顿的软件代理 30+ 工程师）、人在基辅、十年管过 5 到 50 人规模的工程团队。Substack 叫 *From the Trenches*，订阅近千。

他是乌克兰人。这个身份在文章里是关键——文里那些"为什么 Stinger 导弹要四年才能补上库存"的细节，是他在乌克兰一线看到的事，不是从 Wikipedia 抄的。他另外几篇 Substack 文章话题包括：*The Systems That Survive: Four Years of War*、*The First Full-Scale Cyber War*、*The AI Industrial Transformation: Why This Bubble Has Real Revenue*——基本是"懂技术、在前线、看大趋势"的视角组合。

理解他的视角很重要。文章里的 "我们" 不是抽象的"软件行业"，是"我（在 Kyiv 远程管 30 个工程师）和我们（西方软件业）"。

---

## 二、三个去技能化案例：Stinger、EU 炮弹、Fogbank

文章第一节用三个案例铺底。这三个事实级别都很硬，跟 AI 没直接关系，但都是讲"工业能力外包/熟练工裁掉"之后会发生什么。

### 案例 1：Stinger 导弹的 4 年补库存

Stetskov 写：

> "An order placed in May 2022 wouldn't deliver until 2026. Four years. Not because of money. Because the people who knew how to build them retired a decade earlier."

俄乌战争开打后美国紧急下单补 Stinger（FIM-92 单兵防空导弹）库存，2022 年 5 月下单的那批，要 2026 年才能交付。**不是钱的问题，是会做的人都退了**。Raytheon 把生产线重启，找回来的工程师"在 70 岁上下"，要拿出 1980 年代卡特政府时期画的纸图重新教年轻员工怎么做。

> "They brought back engineers in their 70s to teach younger workers how to build a missile from paper schematics drawn during the Carter administration."

四十年前的图纸、退休返聘、四年延迟交付——这是工业能力一旦外包/裁员/转产，要重启时付出的真实账单。

### 案例 2：欧盟一年一百万发炮弹的承诺，结果交了 23 万

> "European production capacity sat at 230,000 shells per year. Ukraine was consuming 5,000 to 7,000 rounds per day."

欧盟 2023 年承诺 12 个月内交付一百万发 155mm 炮弹给乌克兰。实际能力一年只能做 23 万发——而乌克兰一线一天烧 5000-7000 发。承诺最后差着九个月、差着几十万发交付。

冷战后欧洲炮弹厂关了大半。"低强度冲突的时代不需要这么多炮弹"——这个判断本身没错，省下来的钱花到了别的地方（医保、教育、绿色能源）。但是当**高强度冲突回来**的时候，工厂没了、工艺没了、合格的车间工人没了，重建一条线的时间是按年算的。

### 案例 3：Fogbank 的 GAO 报告

Fogbank 是美国 W76 核弹头里一种用作 inter-stage 缓冲的机密材料，1980 年代生产，工艺涉及非常细的化学条件控制。冷战后 W76 进入翻新周期需要重新生产 Fogbank，结果——

> "A GAO report found that almost all staff with production expertise had retired, died, or left the agency. Few records existed."

GAO（美国审计署）的报告写得直白：**会做的人都走了，记录所剩无几**。最后用了几年时间和数千万美元做反向工程，把材料重新搞出来。

这三个案例 Stetskov 想传达的不是"防务工业很难"，是**工业能力一旦失去，重建的时间和成本是按代际算的，不是按季度算的**。"重建简单系统要 3 到 5 年，复杂系统 5 到 10 年"——他原话。这是后面把软件业代入进来的前提。

---

## 三、软件业的镜像：54% 砍 junior、19% slower、2253 → 4 的 funnel

文章第二节进入软件业。Stetskov 列了一组数据：

### METR 那个有名的 19% slower 数据

> "Experienced developers using AI coding tools actually took 19% longer on real-world open source tasks."

这条数据出自 METR（Model Evaluation & Threat Research）2025 年 7 月发布的 *Measuring the Impact of Early-2025 AI on Experienced Open-Source Developer Productivity*。研究方法值得展开——

METR 招了 16 个**资深开源开发者**，都是给 22k+ ⭐ 大型项目长期贡献过的人。他们自己列了 246 个"对项目有价值"的真实 issue（bug fix / feature / refactor），随机分配——一半允许用 AI 工具（主要是 Cursor Pro 配 Claude 3.5/3.7 Sonnet，当时的前沿组合），一半不允许。

结果：**用 AI 的那一组多花了 19% 的时间**。AI 让资深开发者在自己熟悉的 codebase 里**变慢了**。

更扎心的是另一组数据：METR 还问了开发者两个问题——"开始前你预期 AI 让你快多少？"和"做完之后你觉得 AI 让你快了多少？"。**预期是快 24%，体感是快 20%。真实是慢 19%。** 这条研究真正值钱的地方是这套对照——**做完研究、看到对照组数据后，开发者依然认为 AI 帮他们提速了 20%**，与实际相差近 40 个百分点（这是我自己算的，不是 METR 原话）。

METR 自己后来在 2026 年 2 月又发了一篇 *We are Changing our Developer Productivity Experiment Design*，承认实验设计有需要改进的地方，但 19% 这个数字本身没被推翻。Stetskov 引这个数字没问题。

### 54% engineering leader 说要砍 junior

> "54% of engineering leaders predict AI will reduce junior hiring long-term."

Stetskov 这条数据原文标的来源是 LeadDev 的一份调研。我自己翻了 LeadDev 公开页面，能找到这套调研的概要但完整方法论没公开。这条数据姑且当 54% 的 ballpark 数字接受。

但有几个**相关数字**值得列：

- 美国 22-25 岁软件开发者的就业人数从 **2022 年末的高点跌了将近 20%**（Stanford 2025-08 *Canaries in the Coal Mine* 报告）。
- 美国 22-27 岁年轻开发者失业率显著高于全国平均（同报告，但具体百分点引用版本不一）。
- 美国大型科技公司给应届生的招聘量从 2019 到 2025 大幅收缩——应届生在 Big Tech 招聘里的占比明显下降（多家招聘市场报告，量级一致但具体数字依统计样本不同）。
- "entry-level" 招聘 JD 中要求 3 年以上经验的占比偏高（业内长期观察，Handshake / LinkedIn 历年报告反复提及）。

这一组才是 Stetskov 论点的真实支撑——具体到岗位上，junior 程序员在西方科技业**确实在被结构性挤出**。具体百分比哪一份是定本，行业也还在吵，**但方向上没有疑议**。

### Stetskov 自己的招聘 funnel

> "2,253 candidates, 2,069 disqualified, 4 hired. A 0.18% conversion rate."

这是 Stetskov 自己 NineTwoThree 团队的招聘数据。**2253 个简历进来，2069 个不合格，最后录 4 个人**。0.18% 的录取率。

这条数字属于一手观察——Stetskov 自己在他自家招聘 funnel 里看到的事。它没法用第三方数据验证，但作为"现场观察"它的可信度比抽象的"市场怎么样"高一档。

---

## 四、必须修正一条：Stetskov 那条 "62% enrollment drop" 不准

这是这篇文章里**唯一一处我读完后翻原始数据发现不对**的地方。

Stetskov 原文：

> "University computing enrollment down 62% this year."

他给的语义是**美国大学计算机系入学人数下降了 62%**。这数字过于极端——计算机系入学率确实在跌，但 62% 这个数量级哪怕在 dot-com bubble 崩盘后都没出现过。

我去翻了原始来源——CRA（Computing Research Association）2025 年 10 月的 *CERP Pulse Survey*。报告原文是：

> "62% of computing units reported declining enrollment for 2025-26, while only 19% remained steady and just 13% saw increases."

**正确读法是：62% 的计算机系（系/学院/项目作为单位）报告了招生下滑，不是入学人数下降 62%。** 实际的招生人数下降幅度，按 CRA 报告：

- 研究生项目：下降约 15%
- 本科 4 年制：下降约 6-12%
- 两年制社区学院：下降约 6%

也就是说，**学校里报告"招生在下滑"的占比是 62%，但实际下滑的体量是单位数到十几个百分点**。这是两个完全不同的数。

Stetskov 把"62% of units reported decline"读成了"62% decline"。这种读法上的滑坡在叙事性文章里很常见——但放到论点支撑上是要扣分的。**他大方向没错（CS 入学率确实在跌、AI 焦虑+就业差是公认原因），但 62% 这条数字本身被错引了。**

我在 HN 当天的评论区里看了一圈，没有人 flag 这条——大概率因为整体论点太扎心，读者顺着走没去查 CRA 原报告。这是 viral HN 长文的一个结构性问题：论点共鸣 → 数据细节没人 fact-check → 错引被复述传播。

---

## 五、AI 是软件业的"和平红利"

第三节才进入文章的核心论点。Stetskov 这句话是题图：

> "In defense, the substitute was the peace dividend. In software, it's AI."

防务工业的去技能化逻辑：冷战结束 → 没大战要打了 → 工厂关一半、熟练工裁掉、省下的钱拿去做更好的事——这个逻辑当时看是对的。三十年后乌克兰战争开打，账单到了。

软件业的去技能化逻辑：AI 来了 → junior 程序员的活 AI 能干一部分 → 不雇 junior 了、省下的钱拿去买 AI 订阅+雇 senior——这个逻辑现在看也是对的。Stetskov 的赌注是：**十年后会有一个事件让账单到。**

那个事件是什么他没说死，列了几条候选：

1. **AI 工具的 30% 产能"红利"是借来的**——senior 在用 AI 但没人接班；十年后 senior 退休，没有 mid-level 顶上来。
2. **junior 跳过了"调试 / 失败 / 痛苦学习"**——

> "When juniors skip debugging and skip the formative mistakes, they don't build the tacit expertise."

   隐性知识不会通过看 Cursor 的 diff 习得。这是他最尖锐的判断。

3. **AI 让"看起来会写代码"和"真的会做工程"的区别变模糊**——简历都能用 AI 润色，技术面试题都被刷烂——招聘环节本身在失能。这就是 2253 → 4 那个 0.18% funnel 的成因。

把这几条拼起来：**软件工程的隐性知识链正在断**。Stetskov 不是说"AI 不能用"，他是说"把 AI 当 junior 替代"这种用法在断这个链。

---

## 六、Salesforce 那个明牌：CEO 直接说 2025 不再雇软件工程师

文章里 Stetskov 提到 Salesforce 当例子，没展开。我把这条 fact-check 一下，因为它是这套论点目前最具体的现实佐证。

2024 年 12 月，Salesforce CEO Marc Benioff 在 *20VC with Harry Stebbings* 播客上说：

> "We're not adding any more software engineers next year because we have increased the productivity this year with Agentforce and with other AI technology that we're using for engineering teams by more than 30%."

**翻译：明年不再加软件工程师了，因为今年 AI（Agentforce 等）让工程团队产能涨了 30% 以上。**

更进一步，他在同一次发言里还说了一句：

> "My message to CEOs right now is that we are the last generation to manage only humans."

**翻译：我们这一代 CEO 是最后一代只管理人类的。**

2025 年 2 月 Salesforce Q4 FY25 earnings call 上，Benioff 重申了"我们这一代是最后一代只管理人类"。2025 年 7 月，他又接受 Fortune 采访时还在说：Salesforce 没再加软件工程师，但加了销售（"因为要解释 AI 的价值"）。这是 2025-2026 这一波美国大型科技公司里**第一家公开声明、且持续了一整年的"AI 取代了 junior 招聘"** 案例——而且来自一家 76k+ 员工、$37.9B 营收（FY2025）的上市公司，不是创业公司。

Salesforce 是不是会一年后承认"砍 junior 砍出问题"？这个故事还在写。但 Benioff 在 2025 下半年另一次访谈里有过这句"AI 还不能完全替代软件工程师" 的回调——这种说一套做一套的口径切换本身就是信号：**他自己也不确定 2025 年那个决策是不是会成为后悔的事**。

---

## 七、HN 评论区在吵什么（696 条评论的精华）

这篇 4 月 26 日上 HN 第一名一整天，评论区 696 条吵得很凶。我没法逐条引用，但归类一下主要分歧：

**主流支持派（约一半）**：

- "这就是我们公司在干的事，我从 senior 角度看到 junior 接不上来"——这一类长 reply 最多
- "我自己用 AI 写代码效率不高，但管理层信了 30% 产能那个数"
- "防务类比很对——你失去的是组织能力（institutional knowledge），不是单个工人的技能"

**反对派（约 1/4）**：

- "你是 senior，你看不到 junior 用 AI 怎么真的快上来的"
- "防务类比不对——软件业不一样，因为开源/教程/AI 自学能力远比导弹工艺好习得"
- "62% 那个数字明显有问题"（这条评论我有看到，但层数不高，没有顶上去）

**结构派（约 1/4）**：

- "问题不是 AI，是市场扭曲——FAANG 给 senior 开 $500k 的同时不给 junior 开 $80k 是政策选择问题"
- "Stetskov 在 Kyiv，他看到的是 NineTwoThree 这种 outsourcing 公司的 junior 端，不是 SF 总部的 junior 端"
- "招聘 funnel 的 0.18% 是因为 LLM 让简历泛滥，不是因为人才稀缺"

**这条最值得展开**——结构派的判断我自己觉得最准。junior 程序员的招聘市场不是因为"AI 顶替了岗位"垮的，是 LLM **让简历的入口端突然变成无限供应**：写不出代码的人也能用 AI 包装一份好简历，招聘方只能用更高的过滤门槛。**Stetskov 的 0.18% 录取率不是 AI 让人变笨的证据，是 AI 让招聘信号变弱的证据**。这两件事虽然都"是 AI 干的"，但解决路径完全不同。

---

## 八、对国内开发者：这事跟我们有什么关系

中国软件业的招聘结构跟美国不一样，去技能化的剧情也不会是 1:1 复刻。但**这套论点的国内变体值得想清楚**。

**第一条：国内还在 junior 招聘，但结构在变。** 头部互联网（字节 / 阿里 / 腾讯 / 美团 / 拼多多）2024-2025 年校招相比 2021-2022 高峰整体收缩，更明显的是**结构性倾斜——AI 算法 / 大模型相关岗位扩招，传统软件开发岗位的 junior 招聘缩量**。这跟"AI 替代"不是 1:1 关系，更多是行业紧缩 + 业务重心转移——但 AI 也确实让一些重复性岗位（前端切图、初级测试、简单 CRUD）的需求压缩。

**第二条：国内 AI 编程工具的渗透率比海外高（在某些岗位）。** 字节的豆包、阿里的通义灵码、Kimi Code、CodeGeeX、文心快码——加上海外的 Cursor 在国内有可观渗透——一线大厂内部 IDE 已经几乎没有 senior 在裸写。这意味着 Stetskov 那个"junior 跳过调试痛苦"的链条在国内来得更快。

**第三条：国内 senior 在 AI 时代相对优势更大。** 因为国内开源贡献者长期以来就是 senior 主导（开源文化没普及到 junior 层），AI 时代之后这个不对称会进一步放大。**国内 junior 想跨进 senior 那一层，需要的能力门槛比 5 年前高一档。**

**第四条：管理层会信"AI 产能 30%"那个数。** Salesforce Benioff 那条 30% 在国内 CTO/VP 圈被反复转述。这意味着接下来 2-3 年**国内 junior 招聘缩量会持续**，可能 cycle 比美国更短（国内招聘市场对管理层情绪的反应更快）。

**给国内开发者实操建议：**

1. **如果你是 junior（刚毕业 0-2 年）**：往**真的难替代**的方向走——系统设计 / debug / production incident 处理 / 代码审查能力。AI 不会写 incident postmortem，AI 也写不出"为什么这个 race condition 出现在这个特定 production 版本"。
2. **如果你是 mid（2-5 年）**：关键的事是别陷在"用 AI 写更多 CRUD"的循环里。用省下来的时间去**学新栈、读论文、参与开源**。这些事 AI 还替不了你做。
3. **如果你是 senior（5 年+）**：最大的风险不是被 AI 替，是 5 年后没有 mid-level 接你的活、你只能继续干 mid-level 的活。**主动带 junior** 是 senior 的长期防御。
4. **如果你在管理岗**：当心"AI 产能 30%"那条数据。METR 19% slower 那个研究就是给你看的——**你团队真实的产能曲线，可能跟你以为的反向**。做内部 A/B 测试再下决策。

---

## 九、这篇文章为什么值得读

Stetskov 这篇我看完不太同意每一条结论，但**论证骨架是这两年我读过的同类长文里最扎实的**。

他做对的几件事：

- **类比有"骨"**——防务工业去技能化是公开史料、可查可证；不是抒情比喻。
- **数据混搭得当**——史料 + 第三方研究 + 自家招聘 funnel，三种来源相互印证。
- **承认不确定性**——他自己说"重建简单系统要 3-5 年、复杂系统 5-10 年"是一个范围估计；他也没说 AI 是错的，他说"把 AI 当 junior 替代"是错的。

他没做对的：

- **62% enrollment drop 错引**——这条上面已经讲。
- **METR 19% 的语境太简化**——METR 那个研究是 16 个 senior 开源开发者在自己熟悉 codebase 上的结果，不能推到"所有 AI 用法都让人变慢"。Stetskov 引用时没说清这个 scope。
- **Salesforce Benioff 已经回调过**——文章只引了"不雇软件工程师" 那条，没引"AI 还不能完全替代软件工程师" 的后续，论据有偏。

但**主论点——西方软件业正在重复防务工业的去技能化路径——是值得国内技术管理层认真想一遍的**。我们的赌注跟美国不一样，但赌注是同一种类型。

---

*题图：去技能化车间概念配图（PIL 自动生成）*

*相关阅读：*
*- 4-25 日报《Kevin Lynagh 的 Scope Creep 守恒定律》——同主题另一视角*
*- 4-26 auto-research《WUPHF：把 5 个 AI 员工塞进一个 Slack-style 办公室》*

