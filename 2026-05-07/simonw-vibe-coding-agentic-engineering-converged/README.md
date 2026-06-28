---
title: "Simon Willison：100 个 commit 我不审了"
date: 2026-05-07
slug: simonw-vibe-coding-agentic-engineering-converged
type: deep-dive
track: overseas-hot
cover: "https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/05/07/simonw-vibe-coding-agentic-engineering-converged.png"
description: "Simon Willison 长文宣告 vibe coding 和 agentic engineering 已收敛，自述生产环境 100 个 commit 不审 review。HN 同主题 417 pts 热文《The Bottleneck Was Never the Code》呼应。代码审查权分配模式正在重组。"
tags: [simon-willison, vibe-coding, agentic-engineering, ai-coding, code-review]
---
# Simon Willison：100 个 commit 我不审了

![Vibe Coding 与 Agentic Engineering 收敛时间线：从 2025 年 2 月并行到 2026 年 5 月汇合](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/simonw-vibe-coding-agentic-engineering-converged/simonw-vibe-coding-converged-timeline.png)

## 一句话事实

2026 年 5 月 6 日，[Simon Willison 在自己博客发表长文](https://simonwillison.net/2026/May/6/vibe-coding-and-agentic-engineering/)，承认一件去年自己还会公开反对的事——**vibe coding 和 agentic engineering 这两个原本被严格区分的概念，在他自己的工作流里已经分不清了**。同一天，HN 首页 417 pts、281 条评论的 [《The Bottleneck Was Never the Code》](https://www.thetypicalset.com/blog/thoughts-on-coding-agents) 从另一个方向说出了同一件事——一线工程师正在悄悄走 Simon 走的这条路。

这不是一次普通的博客打卡。Simon Willison 是 Django co-creator、Datasette 作者、过去三年最克制的 AI 评论家之一。他过去对 vibe coding 的态度是教科书式的"温和距离"——认可它的存在，但反复强调"生产代码必须逐行 review"。这一次他直接把自己生产仓库的 commit 数甩了出来，说**"那条线对我来说已经模糊得让我自己都觉得不安"**。

> "Those things have started to blur for me already, which is quite upsetting."
> ——Simon Willison，2026 年 5 月 6 日

这篇文章想讨论清楚的事：

- 这条所谓的"分水岭"在事实层面具体是什么；
- vibe coding 和 agentic engineering 各自的概念演化路径，以及它们是怎么走到同一点的；
- "代码审查权"这个词背后真正在重组的是什么——人审、agent 审、commit、outcome 这四层关系怎么重新分配；
- 国产 AI Coding 工具（通义灵码 / Qoder / Trae / 豆包 MarsCode / Kimi 编码 / 文心快码）当前的代码审查权分配模式落在哪一档；
- 接下来 6-12 个月行业可能的演化方向。

## 一、事件三段事实

### 1.1 Simon Willison 是谁，他原来怎么说

Simon Willison 这个名字在英文 AI 圈的分量，大约相当于"既是技术大佬又是写作大佬"那一类人。他和 Adrian Holovaty 一起在 2003 年做了 Django 框架，离开 Django 之后做了 Datasette（一个把 SQLite 变成 web 应用的开源工具，目前 ~10k stars 量级）。最近三年，他几乎每天都在 simonwillison.net 写一篇关于 LLM、Claude、Anthropic 工具链的札记——他既不是公司 PM，也不是大模型实验室员工，是真的"独立用、独立写"。

正因为他这个独立位置，他过去对 vibe coding 的态度被很多人当作"成年人版本的判词"。简单说就是三句话：

- 用 AI 写代码当然可以；
- prototype 阶段无所谓 review，玩起来很爽；
- 但只要这段代码进生产、有用户、和钱有关，**就必须逐行审查，没有例外**。

这条线 Simon 守了大约一年半。他过去甚至在播客里反复说"vibe coding 这个词要小心，别把它和 agentic engineering 混在一起，那是两件事"。

直到 2026 年 5 月 6 日。

### 1.2 他这次具体写了啥

Simon 的原文有几段引文值得逐字看，因为它们是这次分水岭的"原话证据"。

**第一段**，关于他自己日常的代码产出量：

> "I can knock out a git repository with a hundred commits and a beautiful readme and comprehensive tests... in half an hour! It looks identical... I can't tell from looking at it."

直译：他半小时可以拍出一个 100 commit、附带漂亮 README 和完整测试的 git 仓库，**他自己看也分不清这个仓库是 AI 一口气吐出来的还是人慢慢攒的**。

这一段其实有两个信号叠在一起：

- **产出量级**：他在文中提到，自己的代码产出从过去的"约 200 行/天"涨到了现在的"约 2000 行/天"——10 倍。这不是 demo 数字，是他在 [Heavybit High Leverage 第 9 期播客](https://simonwillison.net) 里给的私人数字。
- **可辨识度归零**：人 review 一份代码，过去靠的是"风格、命名、注释密度、提交粒度"这些信号去判断"这是不是一个认真的工程师写的"。Simon 现在亲口说，**这些信号在 AI 生成的代码里已经全部失效**——表面上和资深工程师手写的没区别。

**第二段**，关于他和 vibe coding 的边界已经溶解：

> "Those things have started to blur for me already, which is quite upsetting."

"upsetting"这个词很关键。Simon 不是在欢呼，他用的是"让我心绪不宁"。这是一个负责任的人在承认自己旧立场守不住时的措辞。

### 1.3 HN 同一天的呼应：The Bottleneck Was Never the Code

恰好同一档期，4 月 29 日发表的 [《The Bottleneck Was Never the Code》](https://www.thetypicalset.com/blog/thoughts-on-coding-agents) 在 5 月 6 日前后冲到 HN 首页，417 pts，281 条评论。这篇文章的核心论点更冷静一点：

- 写代码这件事的成本快速降低（"Jevons Paradox"——一件事变便宜，整个行业不会因此少花精力，反而会去做以前不值得做的事）；
- 但软件项目的真正瓶颈从来就不是代码本身，而是**"一群人能不能就要做什么达成共识"**；
- 当 AI 写代码越来越快，瓶颈就被推到了"写规格、写文档、把组织内的隐性知识显性化"——也就是管理层的活；
- 作者用了一句话总结：**"The new moat is organizational, not technical"**——新的护城河不是技术护城河，而是组织护城河。

文章结尾甚至举了一个具体例子：作者拖延了一年的"结构化生成算法"实验，最近用 Codex 几个小时就跑通了原型——**写代码不再是阻碍。**

HN 顶层评论的分歧也很真实：

- 有人（用户名 nayroclade）开喷："那帮去年还在说'编码心流不能被打断'的工程师，今年怎么突然开始鼓吹协作了？"
- 立刻有人（dmm）反驳："两件事可以同时成立——编码不是瓶颈 + 被打断仍然让人烦躁。"
- tikhonj 直接戳破了一种行业惯性："仪式和工单本质上是为了让工作对管理层可见可控，不是为了真正的协作。"

把 Simon 的"我自己生产环境 100 commit 不审"和 HN 这条 417 pts 的"代码本来就不是瓶颈"放在一起看，**一个分水岭就成型了**：去年还在公开战壕里的两条阵线——"严肃工程师" vs "vibe coder"——突然在 2026 年 5 月这一周握手了。

## 二、概念拆解：两条路怎么走到一起

要看清楚收敛意味着什么，得先把两个词的来龙去脉摆开。

### 2.1 Vibe Coding 的演化（2025 年 2 月起）

**起点**是 [2025 年 2 月 Andrej Karpathy 的一条推文](https://x.com/karpathy/status/1886192184808149383)：

> "There's a new kind of coding I call 'vibe coding', where you fully give in to the vibes, embrace exponentials, and forget that the code even exists."

那条推文最终被浏览了 450 万次。这条推文的核心动作其实只有一个——**"forget that the code even exists"**——把代码本身看成一种被 AI 生成的副产物，开发者只关心"vibe"层面的东西（功能、感觉、流程）。

接下来一年的演化路径是这样的：

| 时间 | 节点 | 语义 |
|---|---|---|
| 2025 年 2 月 | Karpathy 提出 | "玩票感"、prototype 范式 |
| 2025 年 3 月 | Cursor + Sonnet 接住 | 工具链跟上，门槛降到对话级 |
| 2025 年 9 月 | Collins Dictionary 选为年度词 | 进入主流大众词典 |
| 2026 年 Q1 | 严肃工程师仍然保留疑虑 | "玩具范式"标签贴在身上 |

注意这条线从头到尾的修辞底色——**"轻"**。Karpathy 自己也在反复强调这是"a fun side project / weekend hack"语境的概念。这就是为什么 Simon 这一年半一直把它和 agentic engineering 切开。

### 2.2 Agentic Engineering 的演化（2025 年 2 月起，几乎同期）

巧合的是，[Claude Code 也是 2025 年 2 月 24 日](https://www.anthropic.com/product/claude-code) 第一次以 research preview 形式发布的。Anthropic 给它的官方定位词组是"agentic coding system"——后来在工程师圈里被简化成"agentic engineering"。

它的路径完全是另一种调性：

| 时间 | 节点 | 语义 |
|---|---|---|
| 2025 年 2 月 | Claude Code preview | "agent 接代码 + 工具调用"范式 |
| 2025 年 5 月 | GA | 进入企业级使用 |
| 2025 年 11 月 | $1B 年化收入 | 大规模商业使用的门槛跨过 |
| 2026 年 1 月 | $2B 年化收入接近 | 进入"基础设施"档位 |

这条线的修辞底色是**"重"**——goal-driven、autonomy、planning、tool use、execute loop。它在工程师群体里被读为"严肃工具，企业级，可以进生产"。

### 2.3 两条路怎么收敛了

把两张图叠在一起看就明白了。

去年的"区分"是基于一个隐藏假设：**vibe coding 的代码不能进生产、agentic engineering 的代码可以进生产**。这个假设的基础是"前者代码质量低、后者代码质量高"。

但 2026 年 Q1-Q2 的事实是这个假设的两个支柱同时塌了：

- **"vibe coding 质量低"塌了**：Sonnet 4.5 / Opus 4.5 / Claude Code 这一年的能力成熟度，让 vibe coding 出来的代码在表面、测试覆盖、commit 粒度上都和工程师手写的看起来一致。Simon 自己说"I can't tell from looking at it"。
- **"严肃 review 是必备"塌了**：Simon 这条独立工程师在产线上跑了几个月之后承认，他自己也开始放弃逐行审查——因为产出量从 200 行涨到 2000 行后，**逐行审查已经在物理上不可能了**。

两个支柱塌掉之后，原本平行的两条线就只能交汇在一个新点上：**人不再是 review 的瓶颈，agent 才是 review 的执行者，人只 approve outcome**。

![代码审查权重组：从人逐行审 → 人审 outcome → agent 审 commit](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/simonw-vibe-coding-agentic-engineering-converged/simonw-code-review-authority-shift.png)

## 三、"代码审查权"在重组什么

"代码审查权"这个词是这次事件的核心。Simon 这一篇博客真正改变的不是工作方式，而是**谁拥有"approve / reject 这段代码"的最终权力**。

四层关系，逐层拆开看。

### 3.1 谁写代码

| 阶段 | 谁写 |
|---|---|
| 2010s | 人 |
| 2024-2025 | 人 + AI 提示补全 / 段落补全 |
| 2026+ | Agent，人提供目标 |

这一层的变化已经基本完成，没什么可争的。

### 3.2 谁 review 代码

这一层是这次事件真正的争议点。

| 阶段 | review 主体 | 单位 |
|---|---|---|
| 2010s | 人 | 行 / 文件 |
| 2024-2025 | 人 | PR / commit（文件级抽查） |
| 2026+ | Agent | commit / PR（agent 自检 + 自评） |

Simon 描述的 100 commit 流程就处在第三档。他不是不审，是把"审"这个动作移交给了 agent——agent 跑测试、agent 写 review 评语、agent 在 PR 里挑潜在风险。**人只在 agent 把红旗举起来时才介入**。

### 3.3 commit 这个动作的语义变了

过去的 commit 是"工程师写完一段心智完整的代码 → 主动提交"。现在的 commit 在 Simon 这种工作流里语义变了：

- **commit = agent 自己完成一个 task 的检查点**；
- 一个 PR 里可能 100 个 commit，但人不读这些 commit；
- 人读的是 PR description（agent 自动生成）+ 测试结果 + 一两个关键 file 的最终态。

这就是为什么 Simon 说"100 个 commit 我不审"——他不是不知道，是这 100 个 commit 不再是给人读的，是给 agent 自己当 work log 用的。

### 3.4 outcome 才是人最终守的东西

这一层是前三层叠加之后唯一留给人的：

- **人定 outcome**（产品 spec、用户故事、acceptance criteria）；
- **人审 outcome**（最终产物在生产环境的表现、是否解决问题）；
- **人不再审 implementation**（实现过程从 commit 到 commit 全部交给 agent）。

HN 那篇热文说得更直白："The new moat is organizational, not technical"——护城河不再是"我比你写代码快"，而是"我比你能更准确地告诉 agent 该做什么、并判断它有没有做对"。

## 四、国产 AI Coding 工具：审查权当前怎么分

Simon 这条范式落在国内是什么样？我们一线开发者每天用的几款工具——通义灵码、Qoder、Trae、豆包 MarsCode、Kimi 编码、文心快码——目前各自走到哪一档？

![六款国产 AI Coding 工具的代码审查权分配模式](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/simonw-vibe-coding-agentic-engineering-converged/simonw-china-ai-coding-review-authority.png)

按公开信息整理一份对照（数据快照：2026 年 5 月）：

### 4.1 通义灵码（阿里云）

[通义灵码 2026 年 2 月更新](https://help.aliyun.com/zh/lingma/product-overview/changelogs-of-202602) 显示，它当前的核心还是"代码补全 + 单元测试生成 + 代码解释"这一套——属于辅助型范式。代码 review 这一侧它有"分层提示"功能，会按"主要问题 → 次要问题"给出建议清单，但**最终的 approve/reject 决定权完全在人**。它在审查权层级上接近 Stage 1.5——人主导，AI 给建议。

特别提一句，通义灵码个人版从 2026 年起独占 Qwen 系列模型 + 自动路由，DeepSeek 选项已经被移除。这个调整让它在能力上更稳定，但也意味着它的演化节奏完全跟阿里自家模型走。

### 4.2 Qoder（阿里云，2025 年 8 月发布）

Qoder 的定位词组本身就在跟着 Simon 这条范式走。阿里云对它的官方描述是"从代码补全助手进化为可独立完成复杂任务的 AI 工程师"。底座模型是基于 Qwen-Coder 加大规模强化学习训练的 end-to-end 模型，公开 benchmark 上**任务完成率超过 Cursor Composer-1，Windows 终端命令准确率高 50%**。

在审查权这一侧，Qoder 走的是"agent 自循环"路线——它会自己起多步 plan、自己执行、自己 verify。落到 review 上，它接近 Stage 2.5——agent 已经开始 review 自己的产出，但人对最终 commit 还有最后一道开关。这是六款国产工具里最接近 Simon 描述范式的一档。

### 4.3 Trae 2.0 SOLO 模式（字节跳动）

Trae 是字节自己的 AI 原生 IDE，2.0 引入了"SOLO 模式"——按字节官方说法是"从需求到部署的完整工作流"。这意味着它已经在尝试把 PR / commit 自动化的部分做掉，**接近 Stage 2.8**——agent 端到端跑完，人在关键节点 approve。

Trae 也是六款里产品野心最大的——它不是把自己定位成 IDE 插件，而是直接做 IDE 本体。这条产品路径决定了它对"审查权重组"这件事最敏感，因为 IDE 本身就是审查权的物理载体。

### 4.4 豆包 MarsCode（字节跳动）

豆包 MarsCode 的特色是云端 IDE——团队所有人在统一云环境里开发，每个 preview session 对应一个独立分支。这种"分支即 PR"的形态让它在审查权上落在 Stage 2——**人审 outcome（preview 里能跑起来），agent 提供环境一致性保证**。

它和 Trae 走的是字节内部两条平行产品线，定位略有差异：MarsCode 偏团队协作 + 云端，Trae 偏单兵作战 + 本地 IDE。

### 4.5 Kimi 编码（月之暗面）

Kimi 编码是月之暗面今年逐步推开的编码线产品，目前在公开信息里相对低调，主打"长上下文 + 内联建议"。审查权层级接近 Stage 1.8——比通义灵码稍微往前一步，但还没到 agent 自循环的档位。

这个位置其实有它的产品哲学——月之暗面一向更强调"模型能力底子打牢"，对 agent 编排层的态度比阿里和字节谨慎。

### 4.6 文心快码（百度）

文心快码基于文心大模型，能力定位在"自动代码生产 + 单元测试生成 + 代码解释"，和通义灵码 2024 年中那一档非常接近。审查权层级 Stage 1.5——人主导，AI 给建议。

百度这一年在 AI Coding 这条线节奏比较稳，没有像阿里 Qoder / 字节 Trae SOLO 那样向 agent 化激进推。

### 4.7 一张表看全

| 工具 | 厂商 | 模型底座 | 审查权阶段 | 关键能力 |
|---|---|---|---|---|
| 通义灵码 | 阿里云 | Qwen 系列 | Stage 1.5 | 分层提示，人主导 |
| Qoder | 阿里云 | Qwen-Coder + RL | Stage 2.5 | Agent 自循环 |
| Trae 2.0 SOLO | 字节跳动 | 多模型支持 | Stage 2.8 | 端到端流水线 |
| 豆包 MarsCode | 字节跳动 | 豆包系列 | Stage 2 | 云端分支预览 |
| Kimi 编码 | 月之暗面 | Kimi 系列 | Stage 1.8 | 长上下文内联 |
| 文心快码 | 百度 | 文心系列 | Stage 1.5 | 分层提示 |

参照 Simon 描述的 Stage 3 范式（agent 审 commit、人只 approve outcome），国产工具里 Trae 2.0 SOLO 和 Qoder 已经走到很接近的位置。**国内 AI Coding 工具距离这条新范式不是"代差"，而是"半个产品迭代周期"——大约 3-6 个月**。

## 五、行业接下来 6-12 个月可能的演化

把 Simon 这条范式 + HN 那篇热文 + 国产六款工具的当前位置叠在一起，可以看出几条比较确定的演化方向。

### 5.1 Agent 自审会成为标配

**Stage 2.5 → Stage 3 的关键能力是"agent 给自己写 review"**。这件事 Anthropic 在 5 月 6 日 Code w/ Claude 大会上已经发布了——[Cat Wu 公布的 Claude Managed Agents](https://simonwillison.net/2026/May/6/code-w-claude-2026/) 里包括 Code Review、CI auto-fix、Security Reviews 三个能力。这条路 Qoder 和 Trae 大概率会在下半年跟进。

### 5.2 PR description 会越来越像产品文档

人审 outcome 的工作流里，PR description 是核心 artifact——它需要**告诉人这个 PR 解决了什么问题、改了哪些边界、有什么风险**。它不再是 implementation note，而是产品文档。国产工具里 Trae SOLO 和 MarsCode 的云端预览已经有这个雏形。

### 5.3 "commit 历史"语义变化

100 commit 不审 review 的前提是这些 commit 不再是给人读的。下半年大概率会出现两种新的 commit 视图：

- **agent work log 视图**：原始 commit，给 agent 自己当 step memory 用；
- **human review 视图**：从 100 个 commit 里"折叠"出 5-10 个语义节点，给人看。

GitHub 的 PR squash 功能某种程度上是这个方向的早期版本，但接下来的工具会把"折叠"自动化。

### 5.4 国产工具下一站：commit 自动化审查

如果非要给国产工具下半年的演化方向定一个最值得做的方向——**就是 commit 级别的自动化 review**。这是 Stage 2 → Stage 3 的关键跳跃点。

具体做法上有几条公开可见的路径：

- **静态分析 + LLM 双层 review**：先用静态工具（lint、type check、security scan）跑一遍，再用 LLM 跑语义层 review；
- **Agent self-loop**：Qoder 已经在这条路上，下一步是把 self-loop 的输出也作为 PR review comment 暴露给人；
- **测试驱动验收**：让 agent 写完代码后自动生成集成测试 + 跑通，人只看 test report。

### 5.5 国内一线开发者已经在悄悄走这条路

值得一提的是，国内一线开发者社区今年讨论 Cursor、Claude Code、通义灵码、Qoder 的密度，比海外想象的高。**很多团队的实际工作流里 agent 已经在跑大量的 commit，只是没有人像 Simon 这样公开把数字摆出来**。这条范式重组的窗口期，国内开发者群体并不在跟跑位置，而是在并跑位置——只是话语层面没有对外讲。

## 六、一个收尾

回到 Simon Willison 那句"upsetting"。一个独立工程师承认自己旧立场守不住，是因为他诚实——他不愿意用"我跟得上"或者"AI 还很烂"两套话术里的任何一套来回避真相。这种诚实是有传染性的。HN 那篇 417 pts 的热文里有句话被很多人引用——"Impactful software tends to be written by many humans that need to collaborate"——这句话本来是软件工程的旧真理，今天它的含义被 AI 拓宽了一档：**这"许多人"里现在有一部分是 agent**，但软件落地仍然需要人和 agent 一起达成共识。

代码审查权的重组不是某个人决定的，是行业一起走到的位置。Simon 这一篇的价值在于他第一个把数字摆出来了——100 commit、半小时、产出 10 倍。这些数字本身是事实，但事实背后那个新的工作分工——**人定 outcome、agent 审 commit、commit 不再是给人读的物体**——才是接下来一两年值得被持续观察的东西。

我们这一代国内开发者刚好赶上这个分配模式重组的窗口。Trae、Qoder 这些产品的进度说明国产工具链不在跟跑位置，而是在和这条新范式并跑——下一站是 commit 自动化 review，谁先把它做扎实，谁就拿到这个窗口期最大的红利。

---

**参考资料**

- [Simon Willison: Vibe coding and Agentic Engineering（2026-05-06）](https://simonwillison.net/2026/May/6/vibe-coding-and-agentic-engineering/)
- [Simon Willison: Code w/ Claude 2026 live blog（2026-05-06）](https://simonwillison.net/2026/May/6/code-w-claude-2026/)
- [The Bottleneck Was Never the Code（thetypicalset, 2026-04-29）](https://www.thetypicalset.com/blog/thoughts-on-coding-agents)
- [HN 讨论：The bottleneck was never the code（417 pts / 281 comments）](https://news.ycombinator.com/item?id=48006967)
- [Andrej Karpathy: vibe coding 原始推文（2025-02）](https://x.com/karpathy/status/1886192184808149383)
- [Anthropic Claude Code 产品页](https://www.anthropic.com/product/claude-code)
- [通义灵码 2026 年 2 月更新](https://help.aliyun.com/zh/lingma/product-overview/changelogs-of-202602)
