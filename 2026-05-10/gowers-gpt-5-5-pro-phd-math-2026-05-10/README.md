---
title: "菲尔兹奖得主实测 GPT-5.5 Pro 解博士级数学题"
slug: gowers-gpt-5-5-pro-phd-math-2026-05-10
date: 2026-05-10
weekday: 星期日
category: news
description: "5 月 8 日剑桥菲尔兹奖得主 Timothy Gowers 把 Mel Nathanson 的开放问题原样喂给 ChatGPT 5.5 Pro，模型独立思考 17 分 5 秒给出二次上界，把 diameter 从指数压到 O(k²)。HN 543 分顶贴。国内做基础科研的同行，DeepSeek-Math-V2、GLM-5、千问深度思考、DeepSeek V4 Pro 现在分别该用在哪？"
track: overseas-hot
domain: ai-math-research
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/05/10/gowers-gpt-5-5-pro-phd-math-2026-05-10.png
tags:
  - GPT-5.5 Pro
  - Timothy Gowers
  - 数学研究
  - DeepSeek-Math
  - 千问深度思考
  - GLM-5
  - 国内开发者
  - 菲尔兹奖
---

# 菲尔兹奖得主实测 GPT-5.5 Pro 解博士级数学题

![Gowers 实测 GPT-5.5 Pro 17 分 5 秒时间轴](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/gowers-gpt-5-5-pro-phd-math-2026-05-10/gowers-gpt55-pro-timeline.png)

**17 分 5 秒，一道五月份没人解出的开放题被 GPT-5.5 Pro 独立做了出来。** 5 月 8 日，剑桥三一学院的菲尔兹奖得主 Timothy Gowers 在自己用了快二十年的 wordpress 博客上贴出一篇标题朴素的短文「A recent experience with ChatGPT 5.5 Pro」。当晚被搬上 Hacker News，24 小时内冲到 540+ 分、近 400 条评论。

事实链很短：

- **题目**：美国数学家 Mel Nathanson 2025 年 5 月发到 arXiv 论文里留下的开放问题
- **思考时间**：模型独立思考 17 分 5 秒，没有人工中途干预
- **结果**：给出一个二次上界，把 diameter 从指数压到 $O(k^2)$，「显然是最优可能的」
- **Gowers 评价**：相当于「**组合学博士论文里完全合理的一章**」

发声方是菲尔兹奖得主，题目是同行真正没解出的开放题，时间是公开可计的，复核方是 MIT 的学生数学家。三句话：独立、难度、可重复。再回头横量国产模型到了什么位置，比看任何 PR 软文都要扎实。

## 一、不是 OpenAI 的公关稿：菲尔兹奖得主自费写的实测笔记

这件事和「又一篇模型公关稿」关系不大。

Timothy Gowers 是 1998 年的菲尔兹奖得主，剑桥三一学院的研究员，组合数学和泛函分析方向。他是「Polymath」众包数学项目的创始人之一，也是 Princeton Companion to Mathematics 的总主编。这个博客 2026 年之前最近一次大规模刷屏是 2024 年讨论 AlphaProof 拿 IMO 银牌的那篇——业内对 Gowers 公开评论的态度从来都是停下手里的活认真读一遍。

5 月 8 日这篇博文有几个关键事实，每一条都和「OpenAI 营销稿」拉开了距离：

- **题目不是他自己出的。** 文章里他明确点名问题来自 Mel Nathanson 2025 年 5 月发到 arXiv 的论文 [Explicit sumset sizes in additive number theory](https://www.arxiv.org/abs/2505.05329)（编号 2505.05329）。Nathanson 是纽约市立大学（CUNY）Lehman College 的数论资深教授，加性数论方向的标准教材作者。原始问题是关于 $\mathcal{R}_{\mathbb{Z}}(h,k) = \{|hA| : A \subseteq \mathbb{Z}, |A| = k\}$——给定整数集合的大小 $k$ 和 $h$ 阶 sumset，问可能取到哪些 sumset 大小，以及构造这种集合需要的「diameter」（最小区间长度）下界。
- **Gowers 自己强调 prompt 没花心思。** 原话是「I didn't even do anything clever with the prompts」（我连 prompt 上都没搞什么花哨）。这一句直接戳穿了「全靠提示词工程」的常见质疑——他给模型的就是把 Nathanson 论文里那段 open problem 的英文表述贴过去。
- **17 分 5 秒不是「连答带写论文」的总时长。** 博文里写得很精确：「ChatGPT 5.5 Pro thought for 17 minutes and 5 seconds before providing a construction that yielded a quadratic upper bound, which is clearly best possible.」这是模型给出关键构造之前的纯思考时间。后续他又让模型写出 LaTeX、做了几道变体题、Isaac Rajagopal（MIT 学生，Nathanson 论文里给过部分指数下界证明的人）独立审过证明，整套流程加起来不到两小时。
- **Gowers 没有用「PhD-level」这个词。** 科技媒体 The Decoder 5 月 9 日转载时记者用了「PhD-level math research」这个表达，知乎和量子位转载也跟着用了。但博客原文准确说法是「a perfectly reasonable chapter in a combinatorics PhD」——「组合学博士论文里完全合理的一章」。「整篇博士论文」和「论文里的一章」差着量级，引用时要分清。

这次实测的份量就此清楚。**它不是实验室自己挑的 demo，也不是博士生的兴奋追星。** 一名顶尖数学家，用一道领域内有难度判定的开放题，给当下最强的几个 LLM 设了一道独立基准。

## 二、模型到底算出了什么：从指数下界到二次上界，缺口在哪

要看懂模型「17 分 5 秒」具体值多少分，先把 Nathanson 那篇论文里的问题摆清。

Nathanson 那篇 [arXiv:2505.05329](https://www.arxiv.org/abs/2505.05329) 关心的是这样一类问题：取整数集合 $A = \{a_1, \dots, a_k\}$，看它的 $h$ 阶 sumset $hA = \{a_{i_1} + \dots + a_{i_h}: a_{i_j} \in A\}$ 能取到的全部可能大小，构成集合 $\mathcal{R}_{\mathbb{Z}}(h,k)$。给定 $h \geq 3$、$k \geq 3$，到底有哪些 sumset 大小是可达的，构造这种集合最少需要多大的 diameter（最大元减最小元）。Isaac Rajagopal 此前证明 diameter 必须随 $k$ 指数增长——也就是要构造出全部可达的 sumset 大小，集合范围会爆炸到 $2^k$ 量级的整数区间里。

GPT-5.5 Pro 17 分 5 秒后给出的构造，把这个 diameter 上界从指数压到了**二次**（$O(k^2)$ 量级）。这中间砍掉的不是常数，是整个数量级。更关键的是 Gowers 在博客里的判断：「**a quadratic upper bound, which is clearly best possible**」（一个二次上界，显然是最优可能的）。在加性数论里，「clearly best possible」不是客气话，意思是已经匹配下界，这个问题在这个量纲上结案了。

Gowers 给质量定调那一段需要逐字看：

> I would judge the level of the result that ChatGPT found in under two hours to be that of a perfectly reasonable chapter in a combinatorics PhD.

直译：「我会把 ChatGPT 在两小时内找到的这个结果的水平，判为组合学博士论文里完全合理的一章。」他后面又补了一句更具体的限制：「Had the result been produced by a human mathematician, it would definitely have been publishable... but it seems pointless even to think about putting it in a journal.」——如果这个结果是人写的，绝对够得上学术期刊发表标准；但既然它是 LLM 几分钟跑出来的，再投期刊就没意义了。这句话本身就是对模型水准的一次冷静校准。

![Nathanson 问题与 GPT-5.5 Pro 给出的二次上界对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/gowers-gpt-5-5-pro-phd-math-2026-05-10/gowers-nathanson-problem-solved.png)

值得对比的另一组锚点是同一份配套论文里的整体规模。这次实测案例其实是论文 [arXiv:2511.16072 「Early science acceleration experiments with GPT-5」](https://arxiv.org/abs/2511.16072) 里的一章。那篇 89 页的论文 2025 年 11 月 20 日上线，14 位作者署名，包括 Sébastien Bubeck（OpenAI 副总裁，前微软研究院高级研究员）、Christian Coester、Ronen Eldan、Timothy Gowers、Mehtaab Sawhney（MIT 数学系）、Mark Sellke、Yin Tat Lee 等。摘要里关键句：

> Of note, this paper includes four new results in mathematics (carefully verified by the human authors), underscoring how GPT-5 can help human mathematicians settle previously unsolved problems. These contributions are modest in scope but profound in implication, given the rate at which frontier AI is progressing.

「四个新数学结果，由人工作者认真核验过——这些贡献范围温和，但意涵深远。」整篇论文跨了数学、物理、天文、计算机、生物、材料六个学科，给出的是「**模型作为合作者加速正在做的研究**」这条主线，而不是「模型独立做出诺奖级发现」。这里的措辞和 Gowers 那篇个人博文是一致的：把 LLM 描述为加速器和共同作者，不是替代品。

| 维度 | 数据 / 出处 |
|---|---|
| 模型 | ChatGPT 5.5 Pro（OpenAI 闭源） |
| 思考时间 | 17 分 5 秒（关键构造），全流程 < 2 小时 |
| 问题来源 | Mel Nathanson, arXiv:2505.05329 (2025-05) |
| 改进 | diameter 上界 指数 → 二次 $O(k^2)$ |
| 复核 | MIT Isaac Rajagopal，独立判证「looked correct」 |
| 配套论文 | arXiv:2511.16072 (2025-11-20)，89 页 14 作者 |
| 出处 | gowers.wordpress.com (2026-05-08) |

## 三、HN 热议怎么吵的：吵的不是模型本身，是「未来五年怎么做研究」

帖子在 Hacker News 上 24 小时冲到 540+ 分、近 400 条评论。难得的是，争论密度集中在「这件事意味着什么」，而不是「Gowers 是不是被忽悠了」。顶赞几条 verbatim 摘出来，能看清这一拨工程师在想什么。

最高赞之一来自用户 **Jweb_Guru**：「This jives with what I've experienced in the brief time I had access to 5.5 Pro. It's the very first LLM that I feel like I can wrangle into solving tedious, but straightforward, problems correctly.」直译大意：「这和我用过 5.5 Pro 那段短时间里的体验对得上。它是第一个我感觉真的能驾驭着、把那些枯燥但思路清晰的问题正确解出来的 LLM。」请注意他用的是「tedious but straightforward」——枯燥但思路清楚，这正是 Gowers 那道 Nathanson 题的特征。HN 老网友 **y1n0** 顺手给他纠了个用法：「Just as an fyi, the word you are looking for is jibes. Jive is something else entirely.」（顺便提醒一下，你想用的那个词是 jibes 不是 jive，jive 是另一回事）这种细节的存在，本身就说明 HN 顶贴这一波是真在认真读。

物理学者 **ziotom78** 留下了一段很扎实的实战陈述：「I am a physics professor and often use Gemini to check my papers. It is a formidable tool: it was able to find a clerical error I was not able to find for days.」（我是物理教授，经常用 Gemini 检查论文。这是非常强的工具：它能找出我自己几天都找不到的笔误。）他后面补了一刀冷静的话——一旦涉及他自己专长的「3D Clifford 代数」这种小众子领域，模型就会犯概念错误。这两条加起来基本就是数理学者圈对 LLM 的一致体感：**整理、查漏补缺、把粗草稿打磨干净——是真的强；越往专精方向走，越要看人。**

哲学层面争论最激烈的一条来自 **bambax**：「Yes but it's not just that if you solved a problem yourself, you're better at solving other problems; it's also that you actually understand the problem that you solved.」（自己解一道题不只是会让你更擅长解其他题，关键是这样你才真的理解了你解的这道题。）随后用户 **doginasuit** 补了一句：「If it is challenging enough to benefit from assistance and you end up playing a minor role in the solution, it seems like you are putting yourself in the worst position possible.」（如果一道题难到需要 AI 协助，但你最后只在解决方案里担任小配角，那你处的可能是最糟的位置。）这两条是 HN 这一拨讨论里最有分量的工程伦理追问。

加速派也不缺人。**robot-wrangler** 引用了数学物理学家 John Baez 的话给出一句重磅设问：「Where does the value of thinking and having deep ideas come from? We need to think about this now.」——「思考、产生深刻想法本身的价值从哪里来？我们现在就得想这个问题。」**FrojoS** 给了不站队的均衡判断：「Every advancement has followed a s curve. Where we are on that curve is anyones guess. Or maybe 'this time its different'.」（每一波进步都走 S 曲线，我们现在在曲线哪一段没人说得准——也或许『这一次真不一样』。）**civvv** 提醒说：「There are many indications that model progress is slowing down, so that is not entirely accurate.」（很多迹象表明模型进步在放缓，不能这么乐观。）

最有意思的一条比较，来自专门跑模型的 **Davidzheng**：「Deep think still makes many many many more mistakes than gpt 5.5 pro on math.」（深度思考模式在数学上还是比 gpt 5.5 pro 错得多得多得多。）这和量子位、机器之心从 2025 年底开始多次报道的趋势对得上——**当下数学推理的天花板由 GPT-5.5 Pro 一档闭源模型把着，但开源阵营的差距在按月缩小。**

## 四、这件事的关键不是「替代数学家」，是「证明过剩时代」的来临

放进更长的时间轴看，2025 年下半年开始，全球顶级数学家圈对 AI 的判断悄悄换了关键词。

陶哲轩在 [量子位 2025 年 9-10 月报道](https://www.qbitai.com/2025/10/340468.html) 和他自己的 Mastodon 账号上多次说过近似的话：当前 AI 模型最适合 Erdős 问题列表的「**长尾**」——大约只有一两成的开放题足够简单，AI 能在最少人工介入下独立解出。陶哲轩观察到一个重要现象：「**数学正在从证明稀缺时代进入证明过剩时代**」。AI 对 Erdős 问题贡献的 GitHub 页面上，「待评估」那一栏堆着 20 多份 AI 提交的全部或部分解，而此前这个分类常年只有一两份。他还坦率承认，自己用上 AI 之后，核心数学工作其实并没有快多少——加速的是整理和点子推送，最后的难关仍然只能人去啃。

![Erdős 问题待评估提交激增示意](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/gowers-gpt-5-5-pro-phd-math-2026-05-10/gowers-erdos-queue-surge.png)

Gowers 5 月 8 日博文里有一句和陶哲轩这一观察非常合拍的发言：

> The lower bound for contributing to mathematics will now be to prove something that LLMs can't prove.

「数学贡献的下限，现在要变成『证明 LLM 也证不出的东西』。」配合他另一句对学界招生的提醒——「It is no longer enough that somebody asks a problem: it needs to be hard enough for an LLM not to be able to solve it」（现在仅仅有人问出一个问题已经不够了，问题要难到 LLM 解不出才行）——你会看到顶级研究者的注意力正在从「**完成更多结果**」转向「**找到更值得做的问题**」。这是 AI 协作给学术界带来的最实在的红利之一。

![顶级数学家对 AI 协作的态度光谱](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/gowers-gpt-5-5-pro-phd-math-2026-05-10/gowers-mathematicians-stance.png)

国内数学界对此的接受度其实比舆论想象的要高。2026 年初澎湃新闻、量子位多次报道过陶哲轩在播客和访谈里给出的判断：「**广度归于 AI，深度留给人类**」。中国科学院数学所、北大、清华、复旦的不少研究小组已经把 GPT-5.x、DeepSeek-Math-V2、GLM-5 当作日常的「证明助手」——整理粗草稿、检验文献遗漏、跑大量计算实验。颁奖与定理署名仍归人类研究者，这条共识并没有动摇。可以说：**国内数学家圈对这件事的态度，与 Gowers / 陶哲轩这一档欧美同行高度同步——温暖坚毅地把 AI 当成加速器，而不是焦虑地把它当作威胁。**

## 五、国产模型对比：厂商自报数据汇总

Gowers 用的是 GPT-5.5 Pro。国内开发者做数学/物理/化学/生物科研，手头能直接用的国产模型在公开口径下报到什么数字？下面这一节把厂商自报与论文声称的数据按出处汇总，**所有数字均为厂商或论文方公开口径，独立第三方复测尚在进行中**，请按"厂商口径"读，不要当作中立 benchmark。

![国内外大模型数学能力厂商自报数据汇总](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/gowers-gpt-5-5-pro-phd-math-2026-05-10/gowers-frontier-math-benchmarks.png)

| 模型 | 厂商声称数据 | 来源（均为厂商自报，未独立验证） |
|---|---|---|
| DeepSeek-Math-V2 | 声称 IMO 2025 全 6 题完整证明 83.3%；Putnam 2024 118/120 | 论文 [arXiv:2511.22570](https://arxiv.org/html/2511.22570v1) 作者声称 |
| GLM-5 | 声称 AIME 2026 92.7%；GPQA Diamond 86.0% | 智谱官方技术报告自报 |
| DeepSeek V4 Pro | 声称 HMMT 95.2%；IMOAnswerBench 89.8% | 深度求索官方榜单 |
| 千问深度思考 | 声称 USAMO 2026 唯一纯合成路线参赛开源模型 | [MathArena 公开榜单](https://matharena.ai/?comp=usamo--usamo_2026) |

**DeepSeek-Math-V2**（深度求索 2025 年 11 月开源）：模型核心创新是「**生成器与验证器分离**」的双组件架构——生成器提构造，验证器像数学家一样审证明，从「结果导向」转到「过程导向」。这个思路和 GPT-5.5 Pro 内部的 self-verification 路径接近，是开源阵营少数能在数学严谨性上对位闭源旗舰的工作。

**GLM-5**（智谱 2026 年 2 月开源，744B 总参 / 40B 激活 MoE，200K 上下文）：智谱这次的关键工程亮点是「**完全适配华为等国产芯片**」——国内研究小组在没有 H100 / H200 的物理条件下，能在国产推理卡上跑起来一个数学能力按厂商自报接近闭源旗舰的模型。

**DeepSeek V4 Pro**（深度求索通用旗舰，2026 年 4 月）：按官方榜单数字看，**与 GPT-5.5 在数学测试上的厂商口径差距已大部分被弥合**。最有杀伤力的是定价——deepseek.com 直连 API，单价大约是 OpenAI 同档位的三十分之一。

**千问深度思考**（阿里通义 2026 年开源，Qwen3.5-397B 量级）：MathArena 的 USAMO 2026 公开榜单显示，它是唯一采用纯合成路线（synthetic-only，不调外部工具）参赛的开源大模型。在国内开发者侧，千问系列的核心优势是阿里云直接托管 + 自部署都成熟，加上最近放出的代码模型 Qwen2.5-Coder-Plus 跟它配合做完整的「证明 + 工程」闭环很顺。

**注意读法**：这些数字都是厂商或论文方公开口径，不是独立第三方实测。要走 Gowers 那条「把开放问题喂给模型独立思考」的路线，第一步永远是自己把题贴进去看看，再决定信不信。

## 六、按场景选型：国内做基础科研的四种用法

![国内做基础科研的四档场景与模型组合](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/gowers-gpt-5-5-pro-phd-math-2026-05-10/gowers-scenario-matrix.png)

做数学、物理、化学、生物方向的科研，把 LLM 用起来，国内开发者手头有这几种现实路径：

**做组合数学、加性数论、表示论这种「构造或反例驱动」的开放问题。** 优先选 GPT-5.5 Pro / DeepSeek-Math-V2。Gowers 那条路径可以照搬：把开放问题原文贴给模型，给 15-25 分钟思考时间；模型若给出构造，自己花 30 分钟独立审证（或交给信得过的研究生），再让模型自己写 LaTeX。**模型给出的「最优」「下界匹配」「显然」三个词，一句都不能直接信，必须你来确认**。Gowers 的博文里也强调了，他亲自核了构造，Isaac Rajagopal 又独立审了一遍。

**做大体量文献综述、定理引用整理、推导细节补足。** GLM-5（200K 上下文）+ DeepSeek V4 Pro 的组合最划算。把 200 页 survey 一次性扔进 GLM-5，让它把所有定理依赖关系画清楚；再用 DeepSeek V4 Pro 帮你补 5-15 行省略的代数细节。陶哲轩在多次访谈里说自己用 AI 的核心场景就是「证明消化」（proof digestion）——把别人的工作读懂、讲透、提炼方法论。这种活国产模型已经能扛。

**做物理/化学/生物的实验数据清洗、误差分析、单位换算审核。** ziotom78 那位 HN 物理教授给的模板很实用：把你的草稿喂给模型，让它指出笔误、量纲错误、文献引用错配。GPT-5.5 Pro 好用，但 DeepSeek V4 Pro / 千问深度思考都达标且便宜得多。**子领域陷阱要小心**：越偏门的领域（3D Clifford 代数、稀有同位素物理这种），越要交叉验证，最好让模型把它依赖的定理和文献页码都列出来你逐条核。

**做工程实现、代码联调、把数学理论翻译成数值实验。** 千问深度思考 + Qwen2.5-Coder-Plus 的本土组合性价比最高——一个负责想清楚理论侧约束，一个负责落地到 PyTorch / Julia / Mathematica 代码。DeepSeek V4 Pro 也够用，API 价格在同档闭源里最友好。

底线只有一句：**模型负责加速，证明仍归你。** 在 Gowers 自己用这台模型的体验里，他都强调「Had the result been produced by a human mathematician, it would definitely have been publishable—but it seems pointless even to think about putting it in a journal」——如果这个结果是人写的肯定能发期刊，但既然是 LLM 做的，投期刊毫无意义。LLM 给的结果可以做研究输入，但不能直接拿去发表。署名权、定理命名、贡献认定，仍然只能归人类。

## 七、研究工作流的真实变化：从单线到多线并行

![单线作坊到多线并行的研究工作流](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/gowers-gpt-5-5-pro-phd-math-2026-05-10/gowers-workflow-transformation.png)

把 Gowers 这次实测、陶哲轩的 Erdős 列表观察、国产模型的 benchmark 数据合到一起看，国内一线研究小组的工作流已经在变化。一年前的典型场景是：一个博士生抱着一个开放题啃半年，导师每周组会拍一次进度。现在很多组的现状是：博士生上午把昨晚生成的多份 LaTeX 草稿跟模型一起核一遍，下午用 GLM-5 跑长综述把方法论摘清楚，晚上让 DeepSeek V4 Pro 复跑数值实验。一个人的有效产出从「一条线」变成「三条线并行」，深度判断和最终把关仍归人。

这正是「博士论文一章」这个评价让数学家圈集体振奋而不是恐慌的根本原因。它意味着 AI 协作此刻最舒服的位置是**事务性中段**——把研究者从「写出博士论文那一章」级别的整理、构造、文献核对里解放出来，让人把宝贵的精力投入到更难、更深、更值得思考的问题。

## 八、回到最开始那一句：博士论文一章的真实含义

回到 Gowers 博客那句被全网传颂的「相当于组合学博士论文里完全合理的一章」。它的**精确分量**要再强调一次：不是「整篇博士论文」，是「一章」；不是「人类巅峰水准」，是「一名合格组合学博士生 1-2 年的工作量」。

Gowers 在博文末尾留下那段判断——

> There is still a great deal of value in struggling with a mathematics problem, but... the era where you could enjoy the thrill of having your name forever associated with a particular theorem... may well be close to its end.

「跟一道数学题死磕仍然有巨大价值——但那种『靠某个定理把自己的名字永久镶进数学史』的兴奋感，可能离结束不远了。」这句话不是悲观，是在重新定义「值得做的事情」。从开源阵营 DeepSeek-Math-V2、GLM-5 厂商自报数字看，开源阵营在数学专项上的口径已经接近闭源旗舰，国内研究者和 Gowers / 陶哲轩这一档其实在同一个时代相位上——**手里有一样的工具，需要回答的是一样的元问题：我们这一代的智识价值，到底要建立在哪些 LLM 还做不到的事情上**。

这件事最让人安心的地方在于：从 Gowers 博文到 HN 顶贴，全球数学家圈的主流回应是「这是好事、加速器到位了、我们该想想下一步」。没有人在喊「数学家要失业了」。

这种坚定而温暖的基调，恰好是国内做基础科研的同行应该接住的——把工具用起来、把门槛抬起来、把更值得做的问题问出来，仍然是属于人类的工作。

---

**关键链接：**

- Gowers 博客原文：[gowers.wordpress.com/2026/05/08/a-recent-experience-with-chatgpt-5-5-pro/](https://gowers.wordpress.com/2026/05/08/a-recent-experience-with-chatgpt-5-5-pro/)
- HN 热帖（540+ pts，397 评论）：[news.ycombinator.com/item?id=48071262](https://news.ycombinator.com/item?id=48071262)
- 配套论文（14 作者）：[arXiv:2511.16072 「Early science acceleration experiments with GPT-5」](https://arxiv.org/abs/2511.16072)
- Nathanson 原问题论文：[arXiv:2505.05329 「Explicit sumset sizes in additive number theory」](https://www.arxiv.org/abs/2505.05329)
- DeepSeek-Math-V2 论文：[arXiv:2511.22570](https://arxiv.org/html/2511.22570v1)
- GLM-5 技术报告：[datalearner.com/glm-5/analysis](https://www.datalearner.com/en/ai-models/pretrained-models/glm-5/analysis)
- MathArena 2026 公开榜单：[matharena.ai](https://matharena.ai/?comp=aime--aime_2026)
- 陶哲轩近期访谈合集：[量子位「广度归于 AI 深度留给人类」](https://hub.baai.ac.cn/view/53443)
