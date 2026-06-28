---
title: "Zig 把 LLM 写进 Code of Conduct"
date: 2026-05-01
weekday: 星期五
tags: [开源治理, AI Coding, OSS, Zig, LLM]
slug: zig-anti-ai-contribution-policy
description: "2026-04-30 02:15 UTC，Simon Willison 在个人博客发出长文，把 Zig 项目刚刚白纸黑字写进 Code of Conduct 的反 LLM 政策称为「the best articulation I've seen yet for a blanket ban」。该转载帖在 Hacker News 拿到 612 pts、397 条评论（item 47957294），冲到当日热门第二。Zig 三条政策原文：「No LLMs for issues. No LLMs for pull requests. No LLMs for comments on the bug tracker, including translation.」起草人 Loris Cro（Zig Software Foundation VP of Community）2026-04-29 在个人博客 kristoff.it 给出了完整论证——「contributor poker」：开源项目押注的是贡献者本人这张「人牌」，不是这次 PR 的「内容牌」。截至 2026-05-01 国内 36 氪 / 量子位 / 机器之心 / 虎嗅 / CSDN 主流媒体未深度报道，auto-research arbitrage 选题。文章覆盖：(1) 政策原文 + Loris Cro 完整论证 + Simon Willison 的反问；(2) HN 397 条评论里 5 条 verbatim 反对 + 支持引语；(3) 把 Zig 放进 OSS 反 LLM 脉络（Gentoo 2024-04 / NetBSD 2024-05 / Curl 2026-01 砍 bug bounty）；(4) 国内 OSS 开发者投稿海外项目时如何把握 LLM 边界、Qwen Code / DeepSeek Code 用户该怎么办的具体建议。"
cover: zig-anti-ai.png
---

# Zig 把 LLM 写进 Code of Conduct

![Zig 反 LLM 贡献政策主视觉](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-01/zig-anti-ai-contribution-policy/zig-anti-ai.png)

> 2026 年 4 月 30 日 02:15 UTC，[Simon Willison](https://simonwillison.net/2026/Apr/30/zig-anti-ai/) 把 Zig 项目刚写进 Code of Conduct 的三条反 LLM 规则推到他的博客头条。同一时刻，Hacker News [item 47957294](https://news.ycombinator.com/item?id=47957294)（截稿时 612 pts / 397 评论）冲到当日热门第二。这是过去一年 OSS 圈里关于 LLM 贡献最系统的一次划线。

Zig 把界线写得没有任何模糊空间：

> **No LLMs for issues.**
> **No LLMs for pull requests.**
> **No LLMs for comments on the bug tracker, including translation.**

这三条直接出自 [ziglang.org/code-of-conduct](https://ziglang.org/code-of-conduct/)，不是某个 maintainer 在 GitHub issue 里发的牢骚，是写进项目章程的硬规则。Simon Willison 在博客原文里的判断是：

> "the best articulation I've seen yet for a blanket ban"

这是他第一次给出这种程度的明确认可。看完 Zig Software Foundation VP of Community Loris Cro 在个人博客 [kristoff.it](https://kristoff.it/blog/contributor-poker-and-ai/)（发表于 2026-04-29）的完整论证之后，很多原本反对一刀切的开发者也开始重新打分。

国内 AI 开发者今天最该关心的不是「赞成 / 反对」二选一，而是**这条线是怎么划出来的，以及当我们用国产模型给海外项目提 PR 时，边界在哪里**。

## 一、Loris Cro 的论证：贡献者扑克，押人不押牌

![贡献者扑克牌——押人不押牌](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-01/zig-anti-ai-contribution-policy/zig-anti-ai-poker.png)

Loris Cro 把它叫做 **contributor poker**。一句话讲完：成熟的 OSS 项目处理新贡献者的方式，跟扑克牌局更像，跟阅卷打分不像。

> "you play the person, not the cards. In contributor poker, you bet on the contributor, not on the contents of their first PR."

也就是说，maintainer 花两个小时去 review 一个新人 200 行的 PR，**真正的赌注不是这次能不能合进主干**。是判断这个人会不会留下来——三个月后还在不在 issue 区出现，半年后能不能独立把模块扛起来，一年后会不会变成核心 review 力量。Loris 在博客里说得很直白：在 Zig 这种规模和复杂度的项目上，新贡献者第一次发的 PR 几乎一定有问题，maintainer 押的从来不是牌面。

LLM 把这个赌局直接掀了。Loris 列了几条可观察的现象：

- **drive-by PR 噪声**：「an increase in background noise due to worthless drive-by PRs full of hallucinations」——一批人用 LLM 一键生成 PR 扔进 ziglang 仓库刷资历，hallucination 比比皆是；
- **隐藏式滥用**：声称没用 LLM 但实际偷偷用，对 maintainer 信任体系是定时炸弹；
- **review 逻辑失效**：「reviewing LLM-generated work doesn't develop new contributors」——把时间花在 review LLM 写的代码上，等于把贡献者养成这件事完全空转。

Loris 顺手回了一句对所有"为什么不一案一议"的反问：

> "from the perspective of contributor poker it's simply irrational for us to bet on LLM users while there's a huge pool of other contributors that don't present this risk factor."

不是 LLM 写的代码不能用，是 ZSF 的资源池就这么大，**当还有大量不带这个风险因子的候选贡献者排队时，先押他们**。这是一个非常理性的资源分配判断，跟"反对 AI"的口号关系不大。

Loris 在博客里专门用了一段把这套思路放进 ZSF 的商业模型来讲：

> "Instead of building Zed like a web page, we built it like a video game"——这是 Andrew Kelley 多年前给 Zig 编译器定的工程哲学；Loris 这次给的是一句平行的：「we try our best to help new contributors to get their work in, even if they need some help」。

意思是 Zig 团队明知道在 OSS 项目长到一定规模后，"完美 PR 只合，不完美 PR 拒"才是 ROI 最高的选择，但他们偏要花时间把不完美的 PR 帮新人改顺，因为那条路上才会长出下一代 maintainer。Loris 把这件事直接称作 ZSF 战略的一部分：「Contributor poker is a key part of their strategy and it's in the project's best interest to push back against anything that hinders their ability to play the game effectively.」LLM 在他眼里不是工具，是一个把这盘棋盘掀了的事件。

## 二、Simon Willison 的反问：那 maintainer 为什么不也用自己的 LLM？

Simon Willison 在博客里转引了 HN 用户 jart（Justine Tunney，cosmopolitan 作者）的一句话——这条留言后续在 HN 拿到大量票数：

> "if a PR was mostly written by an LLM, why should a project maintainer spend time reviewing and discussing that PR as opposed to firing up their own LLM to solve the same problem?"

这是过去一年关于 LLM PR 最锋利的一句反问。如果 PR 的主体由模型生成，maintainer 为什么不直接打开自己的 Claude / Codex / Gemini，让模型去解同一个问题？最起码对方写的 prompt 跟我能写的 prompt 大概率是一个水平。

Simon 自己跟着补了一段更精准的总结：

> "the thing I value most in software now isn't robust tests or thorough documentation—an LLM can spit those out in a few minutes. It's usage. I want to use software which other people have used before me. I want them to have encountered the bugs and sharp edges and sanded them down."

他把 OSS 的核心价值锚到 **usage**——别人真的把它用过、把锐边磨平了——而不是测试覆盖率或文档完整度这些 LLM 几分钟就能批量产出的东西。Loris 押贡献者，Simon 押用户。两套论证最后指向的是同一个点：**OSS 项目的护城河是真人累积的判断密度，不是代码堆积的体积**。

## 三、为什么是这个时间点：Bun 那个 3000 行 PR 把火点起来了

很多读者会问：Zig 项目 2016 年到现在 9 年多，为什么偏偏 2026 年 4 月把这条规则正式写进 CoC？

线索在 HN 47957294 里有一位老 Zig 关注者 hitekker（id=47958209）写得很清楚：

> "the noise around the AI policy came from Bun's developers saying that policy blocks upstreaming their performance PR. But the real reason seems to be that PR's code itself isn't in great shape, and introduces unhealthy complexity"

Bun 是基于 Zig 编译器构建的 JavaScript / TypeScript 运行时（项目地址 [bun.sh](https://bun.sh)，2025 年底被 Anthropic 收购）。Bun 团队最近向 ziglang 上游提交了一个把语义分析做并行化、一举把编译速度提升 4 倍的 PR——3000 行代码、按介绍主要由 LLM 辅助生成。这个 PR 一方面体量大，一方面踩在 Zig 自己规划已久的 "parallel semantic analysis" 路径上，但 Zig 核心团队回复里说得很直接（参考 [ziggit.dev 上 Andrew Kelley 的回复](https://ziggit.dev/t/contributor-poker-and-zigs-ai-ban/15232)）：

> "Parallel semantic analysis has been an explicitly planned feature of the Zig compiler for a long time, and it has heavily influenced the design of the self-hosted Zig compiler. However, implementing this feature correctly has implications not only for the compiler implementation, but for the Zig language itself! Therefore, to implement this feature without an avalanche of bugs and inconsistencies, we need to make language changes."

也就是说，**这个改动不是工程层面 review 一下能合的**，它需要先确定 Zig 语言层面要不要为了它做出对应调整。即便没有 LLM 政策这道闸，PR 本身也是个深水区议题。但 Bun 那边的发声把整个对话推到了"政策 vs 个案"的层面，反而把 Zig 团队推到了必须公开讲清楚立场的位置。

HN 用户 baq（id=47959875）把矛盾点总结得很到位：

> "the question is, would it have been accepted had it met all the quality and engineering standards and full disclosure that it was 90%+ LLM generated?"

如果质量到位、披露到位、又是 90%+ 由 LLM 生成的 3000 行 PR，到底合不合？Loris Cro 那篇博客就是回这个问题的——按 contributor poker 的 ROI 算，**就算这次合了，下一次还是要从头建立信任，因为 maintainer 没有从 review 里学到任何关于这位贡献者本人的判断信息**。

## 四、HN 397 条评论里的真实声音（verbatim）

![OSS 反 LLM 简史 · 三家项目陆续划线](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-01/zig-anti-ai-contribution-policy/zig-anti-ai-mentor.png)

HN 这个帖子有一个特点：反对方的声音不少，但不脏。把几条票数明显较高的留言原文摘出来，正反两面都看一遍。

**🟢 支持方·em-bee（id=47964279）**：

> "in my projects i will reject any contribution that i do not understand. even if the contribution is handwritten by an expert developer. that developer will have to earn my trust like anyone else, like you would have too. LLM contributions are non-deterministic, which means they can never be trusted."

em-bee 把 Loris 的逻辑往个人项目层面又推了一层——核心不是反 AI，是 **review 经济学**：我必须看懂才合，看不懂的概不合，LLM 不可重现就拿不到信任票。

**🟢 支持方·tovej（id=47958815）**：

> "An LLM most definitely cannot spit out robust tests or thorough documentation. It can spit out some tests or some documentation, but they will not cover the user perspective or edge cases unless those are already documented somewhere."

tovej 直接顶了 Simon 那句"LLM can spit those out in a few minutes"——LLM 能产出的是文字形态的测试，覆盖不到真实用户视角和边界情况，前提知识不在训练语料里就抓不到。

**🟢 支持方·jillesvangurp（id=47958776，独立观察）**：

> "I've been seeing a drop in PRs against my repositories. ... My theory is that LLMs prefer sticking to mainstream projects. And since lots of developers are now leaning heavily on LLMs, they are biased to ignoring most of what I provide."

这是另一条角度——一位 100 stars 量级仓库的作者实测今年 PR 数明显下滑。LLM 倾向把开发者往 mainstream 项目上引导，长尾项目反而失血。

**🟡 反对方·vga1（id=47960142）**：

> "Blanket banning all of these seems like a bad idea to me. It actively gates people like myself from contributing, because I respect these people and projects that much."

vga1 是一个有代表性的反对声——他不是反对 Zig 自己定规矩（"every project is allowed to make their own rules"），而是反对 blanket ban 这种形态把"我用 LLM 校对一遍"和"我让 agent 全自动生成"放在同一类里处理。

**🟡 反对方·SuperV1234（id=47966870）**：

> "LLM contributions can be reviewed, tested, and understood like any other contribution. There's nothing 'special' about LLM contributions. Contributions authored by human brains are also non-deterministic"

SuperV1234 直接顶 em-bee 的「非确定性」论证——人写的代码也是非确定性的，作者今天心情不一样，写出来就不一样。

**🟢 中间派·wiseowise（id=47959096）**：

> "Why use someone's project when you can just have the robot write your own? Because it is incredibly expensive to write a replacement for semi-complex software? Good luck asking frontier models to write a replacement for Zig, Docker, VSCode, etc."

把 jart 那句"为什么不让机器人自己写一个"打了回去——前沿模型替你重写 Zig / Docker / VSCode，成本远比你想的高。

支持方主线是「review 经济学 + 信任不可建立」，反对方主线是「政策粒度太粗 + 一刀切伤友军」。两边都讲得过去，Zig 选了第一条。

## 五、Zig 不是第一个：OSS 反 LLM 的脉络

把 Zig 这次划线放到时间线上，2024 到 2026 这两年，OSS 圈陆续有几个项目把界线划了出来：

- **Gentoo（2024-04）**：第一个明确禁止 AI 生成代码进入主仓的主流 Linux 发行版，理由是版权和许可证不可追溯；
- **NetBSD（2024-05）**：commit guidelines 直接把 LLM 生成代码定性为 "tainted code"——「code generated by a large language model or similar technology, such as GitHub/Microsoft's Copilot, OpenAI's ChatGPT, or Facebook/Meta's Code Llama, is presumed to be tainted code, and must not be committed without prior written approval by core」。视角是版权清洁度；
- **Curl（2026-01）**：[Daniel Stenberg 在 daniel.haxx.se](https://daniel.haxx.se/blog/2026/01/26/the-end-of-the-curl-bug-bounty/) 宣布关闭运行 5 年的 HackerOne bug bounty 项目，原因是 AI slop 报告把维护者完全淹没——按 Daniel 自己的实测，2024 年六分之一的安全报告是真的，2025 年这个比例掉到二十分之一到三十分之一。注意 Daniel 的姿态没那么硬：他强调「AI is a tool」，对真正用模型辅助挖出来的真实漏洞依然欢迎。问题在 bug bounty 这个奖励结构在 LLM 时代被滥用穿了。

Zig 走得比这三家都更彻底——Gentoo / NetBSD 是版权角度，Curl 是机制问题，Zig 这次给出的是 **OSS 治理本质**层面的论证：贡献者养成机制本身不能被空转。这也是 Simon Willison 给出 "best articulation I've seen yet" 这个评价的原因。

## 六、国内 OSS 开发者怎么办：Qwen Code / DeepSeek 用户的边界感

国内开发者今天其实有大把投稿海外开源项目的机会。阿里千问 Qwen Code、DeepSeek 的 coding 系列、智谱 [CodeGeeX](https://codegeex.cn) 都是日常会用到的工具。Zig 这次的划线，其实给了几条非常实用的判断标准：

**1. 看项目章程，不看你是不是用了 AI**

很多人误以为只要"我自己懂这段代码"就行。Zig / NetBSD 这种明确写进 CoC 的，是不能用「我看懂了」当抗辩的——项目要的是不可重复实验保证。投稿前先打开仓库根目录的 `CODE_OF_CONDUCT.md` 或 `CONTRIBUTING.md`，搜 "LLM" / "AI" / "generative" 关键词。零容忍的项目（目前明确的：Zig、NetBSD、Gentoo、部分 BSD 系），不要去碰。

**2. 中间地带，建议主动 disclose**

更多项目目前没有明确章程，靠 maintainer 个案处理。这种情况投稿时建议在 PR description 里诚实写一段："This PR was scaffolded with Qwen Code 3.5 / DeepSeek-Coder, then I reviewed and rewrote modules X / Y by hand. Please let me know if this conflicts with project policy." 这是 jart / SuperV1234 那一派也认可的做法——不是 LLM 用了不能用，是不告诉 maintainer 让 review 经济学失效。

**3. 用模型写 issue / 报 bug 之前，先把现象自己复现一遍**

Curl 那边被 AI slop 淹没的核心痛点不是模型本身，是有人没复现就把模型生成的猜测当 bug 报上去。如果你真的用国产模型帮你写 bug report 草稿，**至少把脚本跑一遍 / 把 traceback 贴全 / 把版本号核一遍**——这是 maintainer 信你这条线索值不值得花时间的最低门槛。

**4. 翻译场景：Zig 把翻译也禁了，这条值得注意**

Zig 的 CoC 原文里有 "including translation" 这一句——在它的频道里，连用 LLM 翻译你母语写的 issue 都不行。Zig 反过来明确鼓励大家用母语发，让其他人用自己的工具翻：

> "English is encouraged, but not required. You are welcome to post in your native language and rely on others to have their own translation tools of choice."

这条对中文开发者其实是个好消息——你直接用中文开 issue，让 maintainer 那边自己翻就行。

**5. 投稿前的工作流建议：先沉浸再投稿**

Loris Cro 博客里的另一段值得国内同行多读两遍：

> "the ability to provide contributors with an engaging ecosystem where they can improve their systems thinking and interact with other competent, trusted and prolific engineers"

他把"和靠谱前辈互动"这件事称为 ZSF 商业模式的一部分。换到我们国内开发者视角，**这其实是个机会而不是门槛**——海外严肃 OSS 项目（Linux 内核、PostgreSQL、Rust、Zig 这一档）都还在用师徒制带新人，你只要肯花时间在 issue 区潜水半年，把 maintainer 反复重申的设计哲学吃透，第一条 PR 就比"用 LLM 一键生成"的人有 10 倍以上的合入概率。

具体做法：先 watch 仓库的 issue 区两到三个月，挑几个 "good first issue" 标签下手，PR description 写清楚自己测试过的边界情况，遇到 review 评论别把 LLM 当二传手——直接自己回复、自己改。这条路走通一次，后面 contributor poker 那套就开始为你工作。

## 七、给同行的话：押人不押牌也是国产 OSS 的好底盘

读完 Loris Cro 整篇博客，最有价值的不是"反 LLM"那个结论，是**他把 OSS 治理拆到了"贡献者养成"这个最底层维度**。这套思路对国产开源项目同样成立。

国内现在围绕 Qwen / DeepSeek / GLM 的 OSS 生态已经在快速长出来。Zig 这次给的提醒是：**当你的项目从 100 stars 冲到 10k stars 那段路上，最该想清楚的不是"我们要不要拥抱 LLM 贡献"，而是"我们的 maintainer 时间预算够不够支撑 contributor poker"**。这两件事不是非此即彼。Zig 选了一刀切，是因为他们押的人才池就那么深，不容浪费；如果国内项目有更宽的 maintainer 池，可以选一条更细粒度的中间路线——明确披露、分模块标注、给纯 LLM PR 一个 "AI-generated" 标签做单独 lane。

国内已经在这条路上走的项目其实不少。阿里千问的 Qwen Code 客户端把自己定位为开发者助手，既鼓励开发者用模型加速日常工作，又在仓库里把 maintainer 评审流程写得格外清晰；DeepSeek 的几个 coding 仓库对外贡献者的入口也保留得很完整。这些项目和 Zig 不在同一个争论象限——他们既不是"封闭排斥 LLM"，也不是"放任 LLM PR"，而是在 maintainer 工作流和 contributor 养成机制之间找平衡。这种姿态对国内开源生态长期发展更有空间。

**12 年前 Linus 写过一句话：「Talk is cheap. Show me the code.」12 年后我们这一代国内 OSS 开发者要补上的是：「Show me the contributor.」**

模型会越来越强。但 OSS 项目能跑十年、二十年的那条命脉，永远是真人之间一次次累积起来的判断密度。Loris Cro 把这件事讲清楚了，Zig 用一条三行政策把它写下来了。

下一程，看我们怎么写。

---

**主要来源**：

- Simon Willison · [The Zig project's rationale for their firm anti-AI contribution policy](https://simonwillison.net/2026/Apr/30/zig-anti-ai/) · 2026-04-30
- Loris Cro · [Contributor Poker and Zig's AI Ban](https://kristoff.it/blog/contributor-poker-and-ai/) · 2026-04-29
- Zig 项目 · [Code of Conduct](https://ziglang.org/code-of-conduct/)
- Hacker News · [item 47957294](https://news.ycombinator.com/item?id=47957294)（612 pts / 397 评论 / 2026-04-30 02:15 UTC）
- Daniel Stenberg · [The end of the curl bug-bounty](https://daniel.haxx.se/blog/2026/01/26/the-end-of-the-curl-bug-bounty/) · 2026-01-26
- The Register · [Gentoo and NetBSD ban 'AI' code](https://www.theregister.com/2024/05/18/distros_ai_code/) · 2024-05-18
