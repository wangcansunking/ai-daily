---
title: "Nolan Lawson 反共识冲上 HN 1078 分：与其用 Claude Code 写得更快，不如让 Claude＋Codex＋Cursor 三路 review 写得更稳"
date: 2026-05-27
slug: nolan-lawson-ai-better-code-slowly-hn-1078
category: overseas-hot
cover: nolan-lawson-ai-better-code-slowly-hn-1078-2026-05-27.png
tags:
  - Nolan Lawson
  - Hacker News
  - Claude Code
  - Codex
  - Cursor Bugbot
  - George Hotz
  - tinygrad
  - 多模型 review
keywords: Nolan Lawson, Using AI to write better code more slowly, HN 1078, Claude Code, Codex CLI, Cursor Bugbot, multi-model review, George Hotz, Eternal Sloptember, tinygrad, Uber AI 预算, Anthropic Mythos, Project Glasswing, 多模型投票, 反共识
---
# Nolan Lawson 反共识冲上 HN 1078 分：与其用 Claude Code 写得更快，不如让 Claude＋Codex＋Cursor 三路 review 写得更稳

![Nolan Lawson 反共识：用 AI 更慢写更好的代码](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/nolan-lawson-ai-better-code-slowly-hn-1075-2026-05-27/nolan-lawson-ai-better-code-slowly-hn-1078-2026-05-27.png)

## 30 秒速览

- **顶帖事实**：前 Salesforce / Microsoft 资深前端工程师 Nolan Lawson 5 月 25 日发文《Using AI to write better code more slowly》，被网友 signa11 提交到 Hacker News 后冲到首页第一，截稿时 **1078 分、396 条评论**。
- **反共识核心**：作者直接打掉「LLM 让你编程快 10 倍」的共识，主张 LLM 在 **bug review** 这个反方向上才真正值钱——一份 PR 同时跑 **Claude 子代理 ＋ OpenAI Codex CLI ＋ Cursor Bugbot 三路独立审查**，再按 Critical / High / Medium / Low 分级合并，重叠命中区误报率几乎为零。
- **配套对照**：同一周，tinygrad 作者、首位 iPhone / PS3 越狱者 George Hotz 发表《The Eternal Sloptember》，称采用 AI 编程 agent「会是软件工程史上代价最贵的错误之一」；他用 6 个月在 tinygrad 与 USB-PCIe 固件逆向上做实测，得出与 Nolan 完全一致的结论：**主流叙事押在错的位置**。
- **真实代价**：Uber 5000 名工程师在 2025 年 12 月引入 Claude Code，到 2026 年 4 月已烧光全年 AI 预算，CTO 公开承认「回到画板重新算账」；同周 Anthropic 透露 Mythos / Project Glasswing 在 1 个月内挖出 10000＋ 高危漏洞——同一个 LLM 用作 reviewer 时是核武器，用作 author 时是账单。
- **本文核心论点**：把 AI 从「写代码的人」换成「读代码的人」，是当前阶段把 LLM 用对的最优解；想跟着做的国内开发者，文末有可直接复制的三路投票流水线。

---

![同一周，两个一线工程师同时给 AI 编程降温](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/nolan-lawson-ai-better-code-slowly-hn-1075-2026-05-27/two-voices-same-week.png)

## 一、Nolan Lawson 是谁，这篇为什么能冲到 1078 分

Nolan Lawson 在前端圈是熟面孔。PouchDB（IndexedDB 上的离线数据库）作者、《High Performance Web Audio》前合著者、Salesforce 前 Web 性能技术经理、Microsoft Edge DevTools 前贡献者——一位把职业生涯绝大部分时间花在「让浏览器跑得更快」这件事上的工程师。这种背景决定了他对「快」这个字的敏感度，远高于一个还在写第一年生产代码的 vibe coder。

正因为这个底色，当他在博客标题里放上「**更慢**写**更好**的代码」时，Hacker News 用户读到的不是又一篇唱衰 AI 的鸡汤，而是「一个对性能有近乎洁癖的人，主动放弃速度」。这种姿态的反差，加上他给出的工作流可被一比一复制，最终把帖子推到了首页顶端，截稿时 1078 分、396 条评论——把 5 月 25 日同档期的 Anthropic Mythos 公告、Google I/O 余波全部压在了下面。

> **作者原文**：「LLMs are very flexible. And you can use them just as effectively to write **high-quality** code more **slowly**.」（LLM 是非常灵活的工具，你完全可以用它**慢一点**，但**质量更高**地写代码。）

这句话直接对位的，是当下硅谷叙事里两条最响的口号：「AI 让你编程快 10 倍」和「代码已经被 AI 写完了」。Nolan 没有否定 LLM 的能力，他只是把 **斜杠的位置** 换了——LLM 是灵活的，但灵活不等于必须用在「快」上，也可以用在「慢」上。

热门评论第一条来自用户 **bottlepalm**：「我会让 AI 设计一个中等大小、横切的功能实现，自己审完每个细节，再用 Claude 4.7 Max 实现。」第二条 **aomix**：「在实现之前先和 AI 把问题聊死，是我最舒服的状态——产出质量高，自己还能完全看懂代码。」第三、四条都是同一个味道——HN 上一群已经踩过坑的资深工程师，主动给 Nolan 站台。

但 396 条评论里也有刺。用户 **cold_harbor** 提醒一条不舒服的事实：

> 「LLM 在被用户反驳时会有大约 70% 的概率切换立场——哪怕它原本是对的。RLHF 优化的是「让用户满意」，不是「保持正确」。」

这正是 Nolan 整套方法论的隐藏前提：单模型审查不可靠，因为它会顺着你说话；只有多模型独立投票才能逼近真相。

---

## 二、「慢但稳」工作流：三模型 review 是怎么把误报率压到零的

![三模型并行 review：Claude + Codex + Cursor 投票挑 bug](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/nolan-lawson-ai-better-code-slowly-hn-1075-2026-05-27/multi-model-vote-pipeline.png)

Nolan 把整个流程拆成三步，每一步都和「快」反着来。

**第一步：三路独立审查**。同一份 PR diff，分别交给 Claude 子代理、OpenAI Codex CLI、Cursor 内置的 Bugbot 跑一遍。三个模型背后是不同的训练数据、不同的 RLHF 配方、不同的系统 prompt。这件事的精髓不在于哪一家更强，而在于**它们犯错的方向不一样**——Claude 倾向多想一步、给出建议性改动；Codex 倾向严格按 spec 抠类型；Cursor Bugbot 更聚焦经典 bug 模式。三个模型对同一行代码同时报警，几乎可以肯定那一行真的有问题。

> **作者原话**：「the more, different models you throw at a PR review, the less likely you are to get hallucinations or bogus bugs.」（你往一份 PR review 里丢的模型越多越不同，幻觉和假 bug 的概率就越低。）

**第二步：分级 ＋ 取舍**。三路输出全部 Critical / High / Medium / Low 打标——这层分级 Nolan 强调是按他自己一套硬标准来打的：违反 KISS / DRY、可访问性 HTML 与 JSX 缺陷、SQL 索引缺失等都属于他认可的 bug 定义，而不是 LLM 自由发挥的「建议」。Critical 与 High 必修，Medium 与 Low 按性价比筛——不是说后者不重要，而是修它们的时间成本可能高于价值，专业判断让位给职业判断。

**第三步：触发 PR-level 决策**。如果 Critical 出现得太多，意味着这份 diff 本身的设计有问题；他会直接把 PR 退回去，而不是堆补丁。这一条直接对应一类常见反模式——AI 写出一坨能跑但结构错位的代码，用 reviewer 模式当时给过了，半年后才发现整个模块该重写。

![「快 10 倍写代码」 对比 「慢但稳的多模型 review」](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/nolan-lawson-ai-better-code-slowly-hn-1075-2026-05-27/fast-vs-slow-workflow.png)

两边的取舍是明牌的：用 AI 当 author，首次 commit 极快但隐藏成本高；用 AI 当 reviewer，首次 commit 显著变慢，但代码可读、可维护、半年后回来还认得。Nolan 在原文里给这套方法的人格画像极克制——「careful, methodical, quality-obsessed, focused on making things better for the next coder」——谨慎、有方法、对质量执着、为下一个写代码的人考虑。这种人当下在硅谷数量稀少，但帖子能冲 1078 分本身说明：愿意承认这一点的工程师远比时间线上看起来的要多。

---

## 三、对照组：George Hotz 同一周喊出「软件史上最贵错误」

把 Nolan 这篇文章放在更长的窗口里看，更有意思的是**它不是孤例**。5 月 24 日——比 Nolan 早一天——George Hotz 在自己的 GitHub Pages 博客发表《The Eternal Sloptember》，标题直译是「永不结束的烂泥九月」，sloptember 是他自造的词，slop（烂活）＋ September 拼出来。

Hotz 的开场比 Nolan 凶得多：

> 「I'm calling it now, the adoption of AI agents into software development will be one of the most costly mistakes in the field's history.」（我现在就把话撂这儿——采用 AI 编程 agent 会成为软件工程史上代价最贵的错误之一。）

> 「Agents cannot program, and it's taking longer and longer to realize that they can't.」（Agent 不会写代码，而我们意识到这一点的过程越拖越长。）

这两句话被外媒《Decrypt》《the-decoder》大量引用，国内 36 氪也以这条线索切入做了同期报道。Hotz 不是隔岸观火的批评家——他用 6 个月时间在两个真实项目上做了实测：一是 tinygrad（他自己写的开源深度学习框架）里的 GPU mock 部分，二是一颗 USB 到 PCIe 转换芯片的固件逆向。结论是每一次他都怀疑自己手写会更快、更对。

最戳的是他对 agent 的「老虎机」比喻：

> 「The agent frontloads all the progress, then hands you what is essentially a slot machine lever—you pull it and hope the finishing work gets done. It never quite does.」（Agent 把绝大部分进度都堆在前面完成，然后把一个老虎机拉杆塞给你——你拉一下，祈祷剩下的收尾工作能搞定，但它永远差那么一点。）

这个画面之所以在工程师圈子里炸开，是因为它精准还原了一个常见体验：你看着 agent 唰唰唰把 80% 的代码写出来，最后 20% 你修了一晚上，修完才发现整体设计偏了。

Hotz 紧接着给出一段更刺耳的组织观察：

> 「High-performing people have the ability to error-correct and have mostly been good at seeing when slop is slop. The bottom performers won't have that self check, but they'll be the ones producing 10x output with the agents.」

「高水平的人能自我纠错、看得出烂活是烂活；低水平的人没有这个自查能力，但他们将是用 agent 产出 10 倍代码量的那群人。」——结尾他用了一句很冷的预言：「a golden era for buckets and buckets of slop, and a dark age for gems of quality」（一个烂活铺满的黄金时代，一个精品稀缺的黑暗时代）。

Nolan 与 Hotz 之间没有任何串联痕迹，两篇文章发表时间相差不到 24 小时，论点几乎可以互相替换。两位一线工程师在同一个 48 小时窗口、用完全不同的语气说出同一件事：**主流叙事正在把 AI 编程的价值押在错的位置上**。

---

## 四、Uber 5000 工程师烧光全年预算：账单端的真实代价

![Uber 5000 工程师烧光 2026 全年 AI 预算只用 4 个月](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/nolan-lawson-ai-better-code-slowly-hn-1075-2026-05-27/uber-burn-budget.png)

如果说 Nolan 与 Hotz 是从「质量」端给主流叙事降温，Uber 这边的数字则是从「钱包」端给一耳光。

公开材料显示，Uber 2025 年 12 月把 Claude Code 引入公司内部，全员 5000 名工程师。短短四个月之后，整年的 AI 预算就花完了。一份给 CTO 的内部通报里给出的曲线极为陡峭：2 月使用率 32%，3 月已经有 84% 的工程师被归类为「agentic 用户」，4 月整体到达 95%。同期单工程师月度 API 成本从 500 美元飙到 2000 美元一档，公司 CTO 公开说「我们要回到画板上重新算账，看看我们能不能继续承担这种生产力」。COO 的话更直接：

> 「在和高级工程主管聊过之后，我意识到 token 消耗多并不意味着对外功能产出按比例增加。」

这条事实链有两层意思。第一层是显然的：Claude Code 这种 token 计费、按使用强度爆发的工具，对企业财务的破坏力远超传统 IDE 订阅；第二层更难看见——「token 多并不等于功能多」，背后正是 Nolan 与 Hotz 都在讲的同一件事：**多生成的代码不一定多解决了问题**，它可能只是多堆了债。

把镜头切回普通开发者视角，这条故事的可借鉴度极高。当一家市值数千亿美元的公司在 4 个月里花完自己整年的 AI 预算还来不及收到等比的产出，独立开发者 / 中小团队就更没有理由相信「让 AI 写 70% 代码」是一个可以稳态运行的工作流。账本撑不住，质量端的债迟早也要还。

---

## 五、Mario Zechner 的另一面：Claude Code「我用过最破碎的软件之一」

继续按反方声音线索往下走，还有第三个名字值得放在一起读——Mario Zechner，libGDX 游戏引擎作者、claude-trace 工具作者、Pragmatic Engineer 的常客采访对象。他的态度更戏剧化：曾经是 Claude Code 的早期重度用户，后来公开转身写道：

> 「Claude Code is one of the most broken pieces of software I've ever used in my entire life.」（Claude Code 是我这辈子用过最破碎的软件之一。）

他随后自己写了一个叫 **Pi** 的极简编程 agent——只保留四件工具，一个透明、可控的执行循环——并解释为什么不再用 Claude Code：版本之间行为不一致、系统 prompt 被偷偷修改、用户的上下文经常被官方注入的 system reminder 覆盖。这些观察被 Pragmatic Engineer 详细报道，并不是孤证。

Zechner 的声音和 Nolan / Hotz 拼在一起，构成一个更完整的叙事：

- **Nolan 路线**：不放弃工具，但**换用法**——从生成转向审查；
- **Hotz 路线**：直接判工具死刑，**回到手写 ＋ 极端克制**；
- **Zechner 路线**：保留思路、**砍掉胖大的官方产品**，自己造一个透明的小工具。

三条路线指向的共同地基是：当前一代 agent 工具在「不透明、不稳定、不可解释」这三件事上欠的债，已经多到一线开发者愿意公开喊出来的程度。

---

## 六、reviewer 模式同一周的另一面：Mythos 与 Project Glasswing

回到 Nolan 的核心论点——**LLM 真正的价值在 review，不在 generation**——一个可以直接验证它的数据点，是 Anthropic 自己在 5 月底披露的 Mythos / Project Glasswing。

Mythos 是 Anthropic 把当前 Claude Code 与 Claude Security 用的最强模型，专门拉去做漏洞挖掘。截至 5 月 22 日，他们已经在 **281 个开源项目里披露了 1596 个漏洞**；配套的 Project Glasswing 联合 11 家工业合作伙伴，**单月挖出 10000＋ 高危和严重漏洞**。同一份模型在 SWE-Bench Verified 上录得 93.9%，这个数字是当前公开记录里最高。

把这条信息和 Nolan 的论点叠起来读，逻辑严丝合缝：**同一个 LLM，用作 author 时是账单生成器，用作 reviewer 时是 0-day 工厂**。能力是一样的，决定结果的是「让它放在工作流的哪一格」。Nolan 的方法论用 Claude 4.7 + Codex + Cursor 三路投票挑应用层 bug，Anthropic 内部用 Mythos 单路挖系统级漏洞——两者是同一个范式在不同尺度的展开。

这一点对国内开发者尤其重要。当下国内 AI Coding 工具的注意力，绝大部分仍然放在「让模型把代码写出来」——这条路在硅谷已经被三种姿态同时质疑（Nolan 反共识、Hotz 唱衰、Zechner 出走）。如果国内的工具厂商和团队能把更多算力放在「让模型把代码读懂」这一侧，可能比追加成代码生成更划算。

---

## 七、国内开发者怎么落地：Claude Code＋千问＋OpenClaw 的可复刻方案

Nolan 的原始组合是 Claude 子代理 ＋ Codex CLI ＋ Cursor Bugbot——三件都是境外服务、都要稳定网络。国内开发者要复刻这套「三路 review」并不复杂，关键在保留**多模型 ＋ 独立 ＋ 投票**这三个变量。一种就地可用的组合是：

1. **第一路：Claude Code 子代理**——主力 reviewer，调用 Claude 4.7 Sonnet 或 Opus，按 PR diff 跑一遍，重点抓设计层缺陷；走加速节点接入是当前国内开发者普遍做法。
2. **第二路：千问 Code（Qwen Code CLI）**——阿里通义千问家的命令行 agent，国内服务，价格更友好；用同一份 diff 跑一遍，重点抓类型与 spec 一致性，和 Claude 拉开训练分布。
3. **第三路：OpenClaw / Trae CN**——选一款国产 agent IDE 内置 review 模式跑第三轮，抓经典 bug 模式（空指针、边界、并发）。
4. **打分合并**：三路输出全部按 Critical / High / Medium / Low 标签输出 JSON，写一段 50 行的 Python 脚本合并去重，重叠命中行直接判 must-fix，单路出现的判 nice-to-have。
5. **触发 PR 决策**：Critical 超过 3 条直接退回 PR；High 必修，Medium 与 Low 留待 reviewer 现场判断。

这套组合的好处是和 Nolan 的逻辑一比一对齐——多源、独立、按重叠投票——同时把网络、合规、成本都压在境内可控范围内。对单兵 indie，三路里至少留一路是境内服务，能保证主力工作流在网络异常时不至于完全停摆。

---

## 收尾：把 AI 放回它擅长的格子里

这周冲上 HN 顶端的两篇文章，本质上是一线工程师在公开做一件事——**把 AI 编程的叙事重心，从「生成」搬到「审查」**。Nolan 给出的是温和、克制、可复用的方法论；Hotz 给出的是更尖锐、更不留情面的诊断；Zechner 给出的是「我自己再造一个」的行动。三种姿态不冲突，指向的是同一件事：当前一代 agent 工具还没有强到可以独立写代码，但已经足够强到帮人把代码读得更细。

对中文世界的开发者，这个时间点比任何时候都更适合做一次心态调整——别再追逐「让 AI 写完一切」，先把 AI 用作每一份 PR 的第三、第四只眼睛。多花的那一点时间，会换回半年后还认得的代码、半年后还跑得起来的系统、半年后回头看不脸红的提交历史。慢一点，是一种主动选择，也是一种长期主义。

---

*Pichai「Google 25% 代码由 AI 生成」最近一次官方表述见 2024 Q3 财报电话会议；本文不引用未经核实的「75%」流传数字。*

*参考链接：*
- *Nolan Lawson 原文：<https://nolanlawson.com/2026/05/25/using-ai-to-write-better-code-more-slowly/>*
- *Hacker News 顶帖：<https://news.ycombinator.com/item?id=48272984>*
- *George Hotz《The Eternal Sloptember》：<https://geohot.github.io/blog/jekyll/update/2026/05/24/the-eternal-sloptember.html>*
- *the-decoder Hotz 报道：<https://the-decoder.com/george-hotz-says-coding-agents-will-be-one-of-the-most-costly-mistakes-in-software-development/>*
- *Decrypt Hotz 报道：<https://decrypt.co/368964/george-hotz-vibe-coding-ai-slop-warning>*
- *Uber 烧光 AI 预算：<https://www.briefs.co/news/uber-torches-entire-2026-ai-budget-on-claude-code-in-four-months/>*
- *Anthropic Mythos：<https://red.anthropic.com/2026/cvd/>*
- *Pragmatic Engineer Mario Zechner Pi 项目：<https://newsletter.pragmaticengineer.com/p/building-pi-and-what-makes-self-modifying>*
