---
title: "用 Claude 写 TLA+ 形式规约：分布式系统老炮工具下放给普通后端"
slug: tla-plus-llm-claude-spec-writing-2026-05-20
date: 2026-05-20
weekday: 星期三
category: 形式化验证 / 分布式系统 / AI Coding
cover: tla-plus-llm-claude-spec-writing-2026-05-20.png
track: arbitrage
domain: formal-methods-llm
tags:
  - TLA+
  - 形式化验证
  - 分布式系统
  - Claude
  - Leslie Lamport
  - Hillel Wayne
  - AWS DynamoDB
  - Microsoft Cosmos DB
  - MongoDB
  - 千问
  - Kimi
  - DeepSeek
  - 阿里云
  - TDengine
description: "五月十三日 MongoDB 老工程师 A. Jesse Jiryu Davis 发了一篇博客，标题是「Intro to TLA+ for the LLM Era」。Hacker News 八十一分二十评论上首页。三十年前 Leslie Lamport 设计的 TLA+ 形式化验证语言，过去只被 AWS S3、DynamoDB、MongoDB、Azure Cosmos DB 这种顶级分布式系统团队用。门槛在语法——LaTeX 数学符号 ∀ ∃ □ ◇ 让大多数工程师在第一周就放弃。这一次的转折是：Claude 一次提示就能写出可通过模型检查的完整规约。Hillel Wayne 在二零二六年三月统计：GitHub 上百分之四的 TLA+ 仓库出现了「Claude」这个词。这意味着国内分布式系统团队验证一致性设计的成本，从「十人月级」降到「一人周级」。本文把博客原文核心论点、Hacker News 三派评论、AWS Cosmos DB MongoDB 四家工程实践、国内中文 TLA+ 生态六处公开实查、以及国产大模型写 TLA+ 实操路径全部摆到桌上。"
---

# 用 Claude 写 TLA+ 形式规约：分布式系统老炮工具下放给普通后端

![TLA+ 形式规约语言 Claude 大模型协作 低多边形封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/tla-plus-llm-claude-spec-writing-2026-05-20/tla-plus-llm-claude-spec-writing-2026-05-20.png)

## 这一次的转折点

五月十三日星期二，MongoDB 资深工程师 A. Jesse Jiryu Davis 在自己的个人博客 emptysqua.re 发了一篇文章，标题叫「Intro to TLA+ for the LLM Era: Prompt Your Way to Victory」。文章不长，三千多个英文词，作者用一个数豆子的玩具问题，把 Claude 写出来的完整 TLA+ 规约逐行展开。Hacker News 上线当天就上了首页，八十一分二十条评论，留言里有支持有反对，也有人贴出 SIGOPS 二零二六年的最新论文做学术兜底。

事情的核心论点只有一句：**大模型已经把 TLA+ 这门语言的语法关跨过去了。**

这句话听起来平淡，但放回三十年的历史里看就是一个分水岭。一九九零年代 Leslie Lamport 设计 TLA+ 的初衷是给分布式系统设计人员一个能够数学化推导一致性的工具。三十年后，这个工具的实际用户只剩下四类公司：AWS S3 + DynamoDB 团队、Microsoft Azure Cosmos DB 团队、MongoDB 副本协议团队、再加少数几个高性能分布式数据库厂商。原因不是工具不好，是语法太硬——LaTeX 风格的 ∀ ∃ □ ◇ 把普通后端工程师挡在外面一个月就放弃。

二零二六年的转折点是：Claude、千问、Kimi、DeepSeek V4 这一代大模型，对 TLA+ 这种小众形式化语言的语法掌握度，已经够好到「一个周末上手」的程度。Hillel Wayne 在三月发的新闻信里写：「GitHub 上百分之四的 TLA+ 规约现在带『Claude』这个词。」这个数字背后是无数过去想学但被语法劝退的后端工程师，第一次写出了自己的状态机模型。

把这件事的工程意义说透：**国内分布式系统团队验证设计一致性的成本，从「需要派一个 TLA+ 老兵驻场十人月」，降到「让一个会用 Claude 的后端写一个周末」。**这不夸张。AWS 在二零一一年开始用 TLA+ 的时候，组里只有一个 Chris Newcombe 全职跟工具，整个团队学了几个月才会读规约。今天一个国内中等规模的中间件团队，可以让组里任何一个肯花周末的人上手。

下面把可独立核实的关键数字、原博客的逐句论据、Hacker News 三派声音、AWS 与 Cosmos DB 与 MongoDB 三家的工程实践、国内中文 TLA+ 生态六处公开实查、以及给国内后端工程师的具体实操路径，全部按口径摆开。

## 可独立核实的关键数字

| 维度 | 数值 | 来源 |
| --- | --- | --- |
| 原文发布时间 | 二零二六年五月十三日星期二 | emptysqua.re/blog/intro-to-tla-plus-for-the-llm-era |
| 原文作者 | A. Jesse Jiryu Davis · MongoDB 资深工程师 | emptysqua.re 站点主页 |
| Hacker News 帖子 ID | item 48170007 | hn.algolia.com 实查 |
| Hacker News 热度 | 八十一分二十评论 | news.ycombinator.com 当日热度 |
| TLA+ 创立时间 | 一九九零年代初 | Leslie Lamport 个人主页 |
| TLA+ 全称 | Temporal Logic of Actions · 行动的时序逻辑 | Lamport 官方 TLA+ Hyperbook |
| Leslie Lamport 图灵奖 | 二零一三年 | ACM Turing Award 公告 |
| TLA+ 工程界第一个大客户 | Amazon Web Services · 二零一一年起 | Newcombe 等人 ACM 通讯第五十八卷六十六至七十三页 |
| 第二个大客户 | Microsoft Azure Cosmos DB · 二零一八年起 | github.com/Azure/azure-cosmos-tla |
| 第三个大客户 | MongoDB 副本协议研究组 · 二零一九年起 | MongoDB Distributed Systems Research Group 博客 |
| GitHub TLA+ 仓库提到 Claude 的比例 | 百分之四 · 二零二六年三月统计 | Hillel Wayne 三月新闻信 |
| 大模型对 TLA+ 语法掌握度结论 | 「LLMs have mostly removed the first barrier to entry of TLA+: its syntax」 | emptysqua.re 原文逐字 |
| Hillel Wayne 反对意见 | 「If you need to know formal methods to get the LLM to do formal methods, is that really helping?」 | Hillel Wayne 三月新闻信逐字 |
| SIGOPS 二零二六年论文结论 | 「LLMs are great at producing correct TLA+ syntax but struggle to ensure conformance and appropriate invariants」 | sigops.org 二零二六年实查 |
| 国内最大 TLA+ 入门门户 | 阿里云开发者社区「程序员视角的入门之道」 | developer.aliyun.com/article/795358 |
| 国内最早成系列的中文 TLA+ 教程 | 知乎专栏「多颗糖」TLA+ 入门系列 | zhuanlan.zhihu.com 实查 |
| 国内分布式数据库厂商公开 TLA+ 实践 | TDengine 涛思数据「TLA+ 思维为分布式算法建模」技术课堂 | taosdata.com/tdengine-techtalk/8976.html |

> 截图来自原文 ※ 作者博客封面 + 数豆子规约 Claude 生成片段
>
> ![A. Jesse Jiryu Davis 原博客封面 robot square](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/tla-plus-llm-claude-spec-writing-2026-05-20/tla-plus-emptysqua-og.jpg)

## TLA+ 是什么 · 用数豆子讲清楚

要理解为什么 TLA+ 是分布式系统的「老炮工具」，先用 Davis 在博客里举的那个豆子玩具讲一遍。这个例子的精妙之处在于——它小到一个微信对话框就能解释清楚，但它需要的全部抽象能力，跟你写一个 Raft 副本协议时需要的那一套，是一模一样的。

**问题陈述。**一个罐子里有 w 颗白豆和 b 颗黑豆，刚开始至少有一颗。每次随机抽两颗：

- 如果两颗都是白豆，丢掉这两颗，加一颗白豆；
- 如果两颗都是黑豆，丢掉这两颗，加一颗白豆；
- 如果一黑一白，丢掉这两颗，加一颗黑豆。

不停玩下去。问：罐子里的豆子总数有没有可能在某一步之后等于零？

这个问题本质上是个状态机。**TLA+ 的工作就是把它形式化地写出来，然后让一个叫做 TLC 的模型检查器去穷举所有可达的状态，看会不会撞到「总数等于零」这个反例。**

把状态机的四件套拆开看：

![TLA+ 形式规约的四件套 状态机视角](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/tla-plus-llm-claude-spec-writing-2026-05-20/tla-plus-llm-state-machine.png)

第一件，**变量**。w 和 b 两个自然数。

第二件，**初始态**。w + b ≥ 1 且 w, b 都是自然数。

第三件，**转移规则**。三种动作里挑一种：

- 抽到两白：要求 w ≥ 2，下一步 w' = w − 1，b' = b（净减一颗白豆）
- 抽到两黑：要求 b ≥ 2，下一步 b' = b − 2，w' = w + 1（黑变白，白多一颗）
- 一黑一白：要求 w ≥ 1 且 b ≥ 1，下一步 w' = w − 1，b' = b（净减一颗白豆）

![数豆子规则的三种动作 状态转换示意](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/tla-plus-llm-claude-spec-writing-2026-05-20/tla-plus-llm-bean-puzzle.png)

第四件，**性质**。我们关心的是「总数永远不等于零」。用 TLA+ 的时序逻辑写出来就是一句：□ (w + b > 0)，意思是「在所有可达状态里，w + b 永远大于零」。

把这四件套放进 TLA+ 模块里写出来，整段代码大概三十行。然后跑 TLC 模型检查器——它会从初始态出发穷举所有可达状态，看有没有反例。

**关键观察。**这三种动作里，每一种都让总数严格减少一颗。所以不管罐子起始状态是什么，只要不停玩下去，总数一定会减到剩两颗或更少。剩两颗的时候，肯定还能再玩一步，玩完剩一颗。这一颗永远没法变成零，因为想抽两颗都不够。**所以 w + b > 0 在所有可达状态下都成立。**

这个推理过程，人脑可以靠数学归纳法走一遍。TLA+ 的价值是——当系统不再是数豆子，而是一个有八十个分片、每分片三副本、每副本能崩溃重启、网络可以分区的真实分布式数据库时，**人脑没法穷举状态空间，TLC 可以**。

## 它过去为什么是「老炮工具」

TLA+ 设计于一九九零年代初。Leslie Lamport 在二零一三年拿了图灵奖，奖项一半是为了表彰他在 TLA+ 上的工作。但这门语言在工程界三十年的真实用户量，几乎可以用一只手数完。原因可以分四层：

**第一层 · 语法关。**TLA+ 的语法看起来不像代码，像 LaTeX。一段规约里挤满了 ∀（任意）、∃（存在）、□（一直）、◇（最终）、≜（定义为）、∧（与）、∨（或）这种符号。Lamport 后来为了缓解，又造了一个叫 PlusCal 的算法语言，让规约写起来更像伪代码，TLC 工具会把 PlusCal 翻译成纯 TLA+。即使有 PlusCal，初学者从安装到跑通第一个完整规约，平均也要花一个月。Hillel Wayne 二零二三年的一份学员调研里，将近九成的人停在「装好 TLA+ 工具链」这一步，从来没写过自己的完整规约。

**第二层 · 思维关。**TLA+ 的真正难点其实不是语法，是「用状态机抽象问题」。你得习惯把一个真实系统拆成「变量集合 + 初始条件 + 全部可能动作 + 不变性质」。这种思维方式对写过编译器、操作系统调度器的人是自然的，对天天写业务 CRUD 的后端工程师是反直觉的。Davis 在博客里反复强调，**大模型解决不了这一关——它能帮你把语法填齐，但你得自己想清楚「正确性」到底是什么。**

**第三层 · 中文资源关。**国内分布式系统工程师之前学 TLA+，能找到的中文资料屈指可数。阿里云开发者社区有一篇「程序员视角的入门之道」，是国内 TLA+ 第一入门门户，阅读量累计破十万。知乎专栏「多颗糖」做了五十多篇连载，是国内目前唯一持续更新的中文 TLA+ 系列。涛思数据 TDengine 在「技术课堂」做过一期把 TLA+ 思维落到 TDengine 副本协议的视频。但跟英文圈相比，缺一份系统的中文教科书，也缺一门像极客时间那样规模的中文付费课程。

**第四层 · 工程文化关。**国内大厂的分布式系统团队，在过去十年里学的是「拼测试覆盖率 + 拼 chaos engineering + 拼线上观测」。这条路线是工程范式，TLA+ 是数学范式。两边对「我怎么知道我的系统是对的」这个问题给的答案不一样。工程范式的答案是「我把它跑出来的全部已知坏情况都测过」，数学范式的答案是「我证明了不可能出现坏情况」。两者并不互斥，但在团队优先级里，国内大多数厂选了前者。

![TLA+ 上手门槛 大模型出现前后对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/tla-plus-llm-claude-spec-writing-2026-05-20/tla-plus-llm-onboarding-compare.png)

## AWS · Cosmos DB · MongoDB · DynamoDB 抓到了什么 bug

要理解 TLA+ 真实的工程价值，看四家海外公司在它上面投了多少工 + 抓到了什么 bug，比看任何教程都直观。

![四家海外公司用 TLA+ 抓到的 bug 类型](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/tla-plus-llm-claude-spec-writing-2026-05-20/tla-plus-llm-aws-case-grid.png)

**Amazon S3。**二零一一年开始用 TLA+。Newcombe 等人在二零一五年发表于 ACM 通讯第五十八卷的论文「How Amazon Web Services Uses Formal Methods」里写得很清楚：S3 的对象存储一致性 + 后台垃圾回收两块逻辑，在还没上线前用 TLA+ 抓到了一类极端情况下会丢数据的 bug。这个 bug 在生产环境复现需要每个对象做几亿次操作——意思是即使上线两年也未必能在测试中撞到。

**Amazon DynamoDB。**和 S3 同期。论文里特别提到：「在 DynamoDB 已经上线，被验证为可用、能跑大流量之后，我们再用 TLA+ 回去做模型检查，仍然发现了原副本协议设计里的一处漏洞。」这句话的潜台词非常重——**就算系统已经线上跑了，工程范式抓不到的 bug，数学范式仍然能补抓。**DynamoDB 的核心副本协议至今配套完整 TLA+ 规约。

**Microsoft Azure Cosmos DB。**二零一八年起。Cosmos DB 提供五档一致性模型——strong、bounded staleness、session、consistent prefix、eventual。Azure 团队在公开博客里写：「用英文描述这五档一致性模型很容易出错，因为自然语言有歧义。我们决定用 TLA+ 来精确定义。」github.com/Azure/azure-cosmos-tla 是目前公开范围内最完整的商业系统 TLA+ 规约样本，任何中文工程师都可以打开看。

**MongoDB 副本协议。**二零一九年起。MongoDB 研究组在二零二一年发了一篇正式论文「Formal Verification of a Distributed Dynamic Reconfiguration Protocol」，把 MongoRaftReconfig 的两条核心安全性质——LeaderCompleteness 与 StateMachineSafety——用 TLAPS 证明系统给出了完整数学证明。这是把 TLA+ 从「模型检查」升级到「定理证明」的工程实例。Davis 自己就在 MongoDB 工作，他在博客里反复提到的「老兵社区」就是指这一拨人。

**四家共同的工程价值观。**他们都不指望 TLA+ 替代测试或者替代生产监控。他们把 TLA+ 当作设计阶段的一道质量闸——把设计文档从「英文段落 + 流程图」升级成「能被计算机穷举验证的状态机」。一个 bug 被工程范式抓到要花十万美元的工程小时，被数学范式抓到只要十美元的 TLC 算力。**经济账成立。**

## 大模型到底解锁了什么

回到 Davis 博客的核心论点。文章里只有两句话需要逐字读：

> 「LLMs have mostly removed the first barrier to entry of TLA+: its syntax.」
>
> 大模型已经基本上把 TLA+ 入门的第一关——它的语法——拿掉了。

> 「LLMs have transformed TLA+ from an opaque thinking tool into a translucent one.」
>
> 大模型把 TLA+ 从一个不透明的思考工具，变成了一个半透明的思考工具。

「半透明」这个词用得讲究。它的意思不是大模型替你写完了 TLA+，而是——**你想看清楚某段规约在做什么，大模型能给你打一盏侧光，让你看到形状；但要把里面的结构看清楚，还得你自己走进去。**

Davis 在博客里做了个具体实测。他把数豆子问题写成一句自然语言提示发给 Claude：「write me a TLA+ spec for the following toy example. there's a can of w white and b black beans, at least one bean initially.」Claude 一次性就给出了完整规约——包括变量声明、初始态、三种动作的转移规则、不变量。规约直接复制到 TLC 模型检查器里能跑，跑出来确认了「总数永远大于零」这个性质。

这个一次提示成功的例子，在两年前是不可能的。Davis 在二零二三年也用过 GPT-4 写 TLA+，结果是大模型把语法写得乱七八糟，引用了不存在的常量，调用了不允许的运算符。二零二六年的 Claude Opus 4.7、千问 3 32B、Kimi K2.5、DeepSeek V4 这一代，都已经踏过了那道坎。

但 Hillel Wayne 在三月新闻信里给了一份冷水。Wayne 是 TLA+ 社区最知名的几个老兵之一，他写的「Learn TLA+」书是英文圈的标准入门读本。他的核心反对意见有三条：

> 「The AI is only writing 'obvious properties', which fail for reasons like 'we missed a guard clause' or 'we forgot to update a variable'.」
>
> 大模型只写得出「显而易见的性质」——它能抓住的 bug 都是「漏了 guard 条件」「忘了更新某个变量」这种粗心错。

> 「If you need to know formal methods to get the LLM to do formal methods, is that really helping?」
>
> 如果你得先懂形式化方法才能让大模型做形式化方法，这真的算帮忙吗？

> 「Being easily able to write specifications doesn't help with correctness if the specs don't actually verify anything.」
>
> 如果规约本身验证不了任何东西，能轻松写规约也没用。

Wayne 给的具体失败例子很扎心：大模型写的 TLA+ 规约里会引用未定义的常量 NULL；写的 Alloy 规约缺 `open util/boolean` 导入因此根本编译不过；写的不变量经常是「重言式」——比如断言 `UnsignedImportMustBeDenied` 实际上只是把 `canImport` 的定义又抄了一遍，没有抓到任何真实 bug。

学术界二零二六年也给了第三方裁判。SIGOPS 二零二六年的「Can LLMs Model Real-World Systems」论文，跑了主流大模型在十一个真实分布式系统上的 TLA+ 表现，结论是：「大模型写正确的 TLA+ 语法很厉害，但要保证规约符合系统真实行为、要给出合适的不变量，这两件事它做不到。」

把支持方和反对方放在一起看，结论就清晰了——**大模型解决了 TLA+ 的「语法关」，但「思维关」「问题定义关」「不变量关」还是人的活。**对国内分布式系统团队而言，这意味着入门成本从一个月降到一个周末，但要靠 TLA+ 抓到真 bug，仍然需要一年以上的实际经验积累。

## Hacker News 八十一分二十评论里的三派声音

Hacker News 上这篇博客的讨论，恰好分成三派，把全行业对「大模型 + TLA+」这件事的判断范围画得很清楚。

![HN 八十一分二十评论的三派声音](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/tla-plus-llm-claude-spec-writing-2026-05-20/tla-plus-llm-hn-voices.png)

**第一派 · 大模型解锁了门槛派。**代表评论来自用户 baq：「就算只有一个用户，只要前后端分离，你就已经在分布式系统领域；大模型把入门门槛降下来本身就是改进——它不需要每次都完美，只要它出来的东西比人脑空想可靠就够了。」这一派把大模型当作 onboarding 工具，认为它能把过去望而却步的工程师拉进门来。

**第二派 · 不能替你思考派。**代表评论来自用户 leoqa：「TLA+ 的真正价值在于建模这个过程本身——你认真地把系统拆开来写一遍，建模就是思考。模型检查器只是确认你的猜想。把建模外包给大模型其实是把价值最高的那部分外包出去了。我经常很难看懂同事写的模型，必须自己重新走一遍他的抽象路径才理解。」这一派承认大模型写得出，但「写出来你看不懂等于没写」。

**第三派 · 学术兜底派。**代表评论来自用户 jmorse3，他在留言里贴出了 SIGOPS 二零二六年那篇论文的链接，并引用了原文结论：「大模型生成正确的 TLA+ 语法很厉害，但要保证符合系统行为、要给出合适的不变量，这两件事它做不到。」这一派用论文数据兜底，提醒大家不要被「单次提示成功」的演示吓到。

还有一个细节值得提：评论区里出现了一个叫 Specula 的新工具 github.com/specula-org/Specula，是把 TLA+ 和 LLM agent 结合起来的工程实验。用户 baq 在评论里说他用 Specula 跑了一个棘手的缓存场景，「它确实在所有主流大模型上抓到了标准 agentic review 漏掉的 bug」。这是值得国内分布式系统团队关注的工具方向。

## 国内中文 TLA+ 生态六处公开实查 · 与海外社区对比

国内分布式系统团队对 TLA+ 的真实使用情况，是这篇文章最难写的一段——**公开可查的口径很有限。**国内大厂（蚂蚁 OceanBase、字节、腾讯云、阿里中间件、美团）有没有内部用 TLA+ 验证一致性算法，业内传言肯定有，但具体仓库和具体 bug 案例几乎不公开。所以本节限定在「公开博客 + 知乎 + 公司技术站 + 学术论文有可查记录」这条严口径上。

![国内分布式系统 TLA+ 公开实践地图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/tla-plus-llm-claude-spec-writing-2026-05-20/tla-plus-llm-china-public-record.png)

**阿里云开发者社区。**「形式化验证工具 TLA+ 程序员视角的入门之道」一文是国内 TLA+ 第一入门门户，二零二一年发布，累计阅读破十万。文章把 PlusCal 的核心概念用程序员熟悉的伪代码讲清楚，是中文圈最被引用的入门资料。

**涛思数据 TDengine。**「技术课堂」做了一期「如何使用 TLA+ 思维为分布式算法建模」，是国内时序数据库厂商公开做的 TLA+ 教学内容，把抽象的状态机思维落到 TDengine 副本协议的具体例子上。这条线索说明，国内做底层分布式存储的厂商，至少有一部分团队在内部用 TLA+ 思维做设计。

**葡萄城开发者空间。**二零二五年九月发了「TLA+ 与 Python 联动的分布式共识协议建模」，这是把 TLC 的输出对接到 Python 单元测试里的工程实验。意义在于——它给出了一种「TLA+ 验证设计 + Python 实现 + 二者用相同的状态空间对照」的工程闭环。这是过去英文圈也少见的实践。

**Tony Bai 个人博客。**「使用 TLA+ 形式化验证 Go 并发程序」二零二四年八月发文。Tony Bai 是国内 Go 圈知名作者，这篇博客把 TLA+ 用法对到 Go 的 channel 与 mutex 上，给做 Go 后端的工程师提供了一条上手路径。

**高明飞个人博客。**「TLA+ 形式化验证入门指南」二零二一年八月起的一份完整中文教程，配 GitHub 示例仓库。这是国内最早一批成体系的中文 TLA+ 教程之一。

**知乎专栏「多颗糖」。**TLA+ 入门系列连载，累计五十多篇技术帖，是国内目前唯一持续更新的中文 TLA+ 系列。InfoQ 写作社区上同步发了一些精选篇。

**需要明示的盲区。**国内大厂的内部 TLA+ 实践到底覆盖了哪些系统、抓到了哪些 bug、设计阶段挡掉了多少线上事故——这些数据没有公开可查的口径。本节只能写到「公开博客 / 知乎 / 公司技术站」这一层。这个盲区本身就是国内分布式系统社区接下来值得补的一块——把内部 TLA+ 实践公开出来，对整个国内工程社区都是知识增量。

## 给国内后端工程师的实操路径 · 国产基座横评

抓住一个具体的工程价值出来谈——如果你是国内一线后端工程师，看到这篇文章想试一试，从今天开始走多久能见到第一个真实价值？路径如下，按性价比顺序排列。

| 步骤 | 动作 | 时间预算 | 难度 | 替代不替代得了人 |
| --- | --- | --- | --- | --- |
| 一 | 选一个大模型当辅助 | 半天 | 低 | 大模型替你查语法 |
| 二 | 从数豆子级问题练手 | 一个周末 | 低 | 大模型替你写 spec |
| 三 | 把你手上的小型分布式系统抽象成状态机 | 三天到一周 | 高 | 替代不了 · 是设计活 |
| 四 | 让大模型把 TLA+ 语法填齐 | 半天 | 低 | 大模型核心价值在这一步 |
| 五 | 跑 TLC 模型检查器看反例 | 一天 | 中 | TLC 给你穷举状态空间 |
| 六 | 横评几个大模型写同一个规约 | 两到三天 | 中 | 这是国产基座社区可共建项 |

**第一步 · 选一个大模型当辅助。**Claude Opus 4.7 是 Davis 博客实测的对照基线；国产基座里 Kimi K2.5、千问 3 32B、DeepSeek V4 都在二零二六年达到了「单次提示生成可通过模型检查 TLA+ 规约」的水平。建议第一周混着用——同一个提示词分别发给三家，对比生成结果。这一步本身就是国产基座的真实评测。

**第二步 · 从数豆子级问题练手。**不要一开始就上 Raft / Paxos。先把 Davis 博客里那个数豆子例子完整复现一遍——让大模型生成 TLA+ 规约，自己装 TLA+ Toolbox，跑 TLC 模型检查器，看到它把所有可达状态穷举完，输出「不变量在所有状态下成立」。这一步的目的是建立直觉——**TLC 真的会穷举状态空间，不是骗你的。**

**第三步 · 把你正在做的系统抽象成状态机。**这是最难的一步，也是大模型替代不了的一步。你需要把自己手上正在做的小型分布式系统——比如一个简单的分布式锁、一个消息队列的 ack 机制、一个限流器——拆成四件套：变量、初始态、可能动作、不变性质。这一步建议参考阿里云那篇入门文以及知乎「多颗糖」的连载。

**第四步 · 让大模型把 TLA+ 语法填齐。**当你已经能用自然语言清晰说出「我的系统有哪些变量、初始条件是什么、有哪些可能动作、我希望保持哪些不变量」的时候，把这段话发给大模型，让它生成完整 TLA+ 规约。这一步如果不顺利，问题通常出在第三步——你的自然语言描述还不够精确。

**第五步 · 跑 TLC 模型检查器看反例。**如果你的设计里有 bug，TLC 会输出一个反例——从初始态出发的某条具体执行路径，最后到达了一个违反不变量的状态。这是 TLA+ 给你的最大回报——**它不是告诉你哪里错了，是给你一条具体的反例路径，让你看到 bug 是怎么发生的。**

**第六步 · 横评几个大模型写同一个规约。**这是一个国产基座社区可以共建的事——拿同一个分布式系统的设计文档，让 Claude、Kimi、千问、DeepSeek 各写一份 TLA+ 规约，对比生成质量、对比 TLC 跑出来的覆盖率、对比抓到的 bug。这种横评在国内 TLA+ 社区还没人系统做过。

实测时间预算 · 第一步到第五步走完一个数豆子级例子，一个对分布式系统有基础的后端工程师大概一个周末。走完一个真实小系统的完整规约 + bug 抓取，大概两到三周。这个时间相比「派一个 TLA+ 老兵驻场十人月」的过去口径，是一个数量级的下降。

## 大模型能干和不能干的边界

最后回到一个核心判断——**大模型 + TLA+ 这件事的边界在哪儿？**

**能干的事 · 三件。**

- 把 TLA+ 语法关跨过去。这是 Davis 博客的核心论点，也是社区共识。
- 帮你查阅 PlusCal / TLA+ 的语义细节。过去你得查 Lamport 的 Hyperbook 或者翻 Hillel Wayne 的教程，现在大模型直接给你答案。
- 加速「设计文档转 TLA+ 规约」这个翻译过程。当你已经想清楚系统的状态机抽象，大模型能把它落成可跑的 TLA+ 几分钟内完成。

**不能干的事 · 三件。**

- 替你定义「正确性」。Davis 在博客里有一句反复强调的话：「It's still your job to define what properties your system must uphold」——定义系统必须保持哪些性质，仍然是你的活。大模型不知道你的系统该干什么。
- 替你做状态机抽象。Wayne 在新闻信里说大模型「只写得出显而易见的性质」，意思就是它给你的不变量经常是重言式——把已有定义又抄一遍，没有真正抓到设计漏洞。真正能抓 bug 的不变量需要工程师从业务理解里挖出来。
- 替你读懂同事写的规约。leoqa 在 HN 评论里说的「我经常很难看懂同事写的模型，必须自己重新走一遍他的抽象路径才理解」——这件事大模型暂时也帮不了你。规约的可读性高度依赖建模者的抽象选择。

把这个边界讲清楚的意义在于——**国内分布式系统团队不需要等到「大模型完美写 TLA+」那一天再开始用，今天的工具水平已经够把入门成本砍到一个数量级。**该补的不是大模型的能力，是国内工程师对「状态机思维」「时序逻辑直觉」「不变量识别」这三项基本功的训练。

## 结尾 · 数学范式下放给普通工程师

把这件事放进国内分布式系统社区十年发展的大背景里看，意义就清楚了。

过去十年，国内分布式数据库、消息队列、分布式锁、分布式事务这一类基础设施的发展速度世界一流——蚂蚁 OceanBase、PolarDB、TiDB、字节的 ByteHouse、腾讯的 CKafka、阿里的 RocketMQ，每一个都是国内自研撑起来的。但有一个隐形的代价——**这些系统的一致性 bug 大多数是在线上撞出来的。**业界传统讲法叫「真用户帮你测」。

TLA+ 这条路是数学范式给出的另一条路——**在你的系统还没写一行代码之前，先把设计写成状态机，让 TLC 帮你穷举出所有可能的坏情况。**这条路过去因为门槛太高，没有进入国内大多数团队的工具栈。

二零二六年的转折点是——**大模型把这条路的门槛砍到了一个周末。**国内分布式系统团队多年来吃过的一致性 bug 的亏，从此可以用 Claude + 千问 + Kimi + DeepSeek 加上 TLA+ 这一套组合，在设计阶段提前两周躲掉一大半。

这不是一个 AI Coding 工具的新闻。这是一个分布式系统设计文化的转折——**数学范式第一次以可承受的成本进入了普通后端工程师的工具栈。**国内分布式系统社区接下来一年值得做的事，是把这条路的中文文档、中文课程、国产大模型横评、企业内部实践案例补齐。当国内中等规模团队都能用 TLA+ 验证自己的一致性设计时，这十年我们靠真用户撞出来的 bug，可以让下一个十年的工程师在设计阶段就拦下来。

这是一个老工具被新技术解锁的故事，也是一个国内分布式系统社区可以走得更远的窗口期。
