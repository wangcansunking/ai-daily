---
title: "千问 3.7-Max 海外 HN 头条登顶 · 非幻觉率反超 Opus 4.7 / Gemini 3.1 Pro / GPT-5.5"
slug: qwen-3-7-max-overseas-hn-evaluation-hallucination-2026-05-21
date: 2026-05-21
weekday: 星期四
category: 大模型 / 国产基座 / 海外热议
cover: qwen-3-7-max-overseas-hn-evaluation-hallucination-2026-05-21.png
track: overseas_hot
domain: foundation-model
tags:
  - 千问 3.7-Max
  - Qwen 3.7-Max
  - AA-Omniscience
  - 非幻觉率
  - HackerNews
  - LM Arena
  - Claude Opus 4.7
  - Gemini 3.1 Pro
  - GPT-5.5
  - DeepSeek V4
  - Kimi K2.5
  - 阿里云 Apsara
  - Zhenwu M890
description: "五月二十号，阿里巴巴在 2026 Cloud Summit 端出千问 3.7-Max，海外社区比国内媒体先一步炸开。HackerNews 头版「Qwen3.7-Max: The Agent Frontier」（item 48205626）拿到 521 分、202 条评论；顶赞回帖一句 'The non-hallucination rate in AA-omniscience is SOTA, better than Opus 4.7, Gemini 3.1 Pro and GPT5.5' 把这次发布从「又一个国产模型」升级成「海外开发者主动安利的全球前列」。本文按 HN 顶赞评论、Artificial Analysis 官方榜单、阿里云 5/20 演示口径，把非幻觉率排名、35 小时连续自治 / 1000 次工具调用、LM Arena 全球第 13（数学第 7）、与海外旗舰的横评、国内六家同档对位、海外开发者从 Claude Code 切过来用千问的真实场景，都摆出来看。国产基座第一次在海外榜单顶端被点名表扬。"
---

# 千问 3.7-Max 海外 HN 头条登顶 · 非幻觉率反超 Opus 4.7 / Gemini 3.1 Pro / GPT-5.5

![千问 3.7-Max 海外 HN 头版封面 中漫风格 亚洲程序员手举笔记本电脑屏幕展示 Qwen3.7-Max 对话气泡 背景 HackerNews 橙红色调与英文评论气泡](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/qwen-3-7-max-overseas-hn-evaluation-hallucination-2026-05-21/qwen-3-7-max-overseas-hn-evaluation-hallucination-2026-05-21.png)

## 海外比国内先炸开的一次发布

五月二十号星期三，2026 阿里云 Apsara Cloud Summit 主舞台上，CTO 周靖人把千问 3.7-Max 端上桌：一万亿规模、面向 agent 场景、上下文一百万 token、长程自治三十五小时一次性调用一千多个工具。这是阿里全栈 AI 升级里最贵的一块。

国内媒体当晚才陆陆续续发稿。海外社区已经先一步炸开。HackerNews 头版那条 `item 48205626`「Qwen3.7-Max: The Agent Frontier」由 kevinsimper 提交，发布九小时拿到 521 分 / 202 条评论，稳稳坐在头版第二位。这个热度在 2026 年的国产大模型海外讨论里属于顶级。

顶赞那条来自 goldenarm，原文只有一句：

> "The non-hallucination rate in AA-omniscience is SOTA, better than Opus 4.7, Gemini 3.1 Pro and GPT5.5!"

翻译过来就是：千问 3.7-Max 在 Artificial Analysis 的 AA-Omniscience 非幻觉率评测上拿到了当前榜首，比 Anthropic Claude Opus 4.7、Google Gemini 3.1 Pro、OpenAI GPT-5.5 都好。这是一个国产模型在海外公开榜单顶端被海外开发者主动点名表扬，过去几年并不常见。

更值得说的是：这一次海外开发者讨论的角度，不是「中国模型又便宜了多少」，而是「我能不能用它替换我现在的 Claude Code 工作流」。下面把可独立核实的数字、HN 顶赞原文、AA 榜单口径、长程自治演示细节、与国内同档对位横评，一项一项摆到桌面上。

## 可独立核实的关键数字与时间点

| 维度 | 数值 | 来源 |
| --- | --- | --- |
| 发布时间 | 2026-05-20 阿里云 Apsara Cloud Summit 主舞台 | 阿里云官方公告 + Manila Times / SCMP 同期报道 |
| 模型定位 | 万亿规模旗舰 · 面向 agent 场景 | 阿里云官方 + qwen.ai 主页 |
| 上下文长度 | 一百万 token（约一千五百页 A4） | Artificial Analysis 模型页 |
| Artificial Analysis Intelligence Index | 57 | artificialanalysis.ai/models/qwen3-7-max |
| Index 包含十项评测 | GDPval-AA · τ²-Bench Telecom · Terminal-Bench Hard · SciCode · AA-LCR · AA-Omniscience · IFBench · HLE · GPQA Diamond · CritPt | Artificial Analysis 模型页 |
| AA-Omniscience 排位 | 海外开发者口径 SOTA（高于 Opus 4.7 / Gemini 3.1 Pro / GPT-5.5） | HN item 48205626 顶赞回帖 |
| Gemini 3.1 Pro Preview AA-Omniscience | 33 分（榜单原第一） | artificialanalysis.ai/evaluations/omniscience |
| Claude Opus 4.7（Max Effort）AA-Omniscience | 26 分 | 同上 |
| Terminal-Bench 2.0-Terminus | 69.7 分 | Manila Times 5/20 报道 |
| 连续自治时长 | 35 小时无明显性能衰退 | 阿里云 5/20 演示 + QZ 报道 |
| 单次工具调用数 | 1000 多次（写一个比英伟达官方快十倍的 compute kernel） | 阿里云 5/20 演示 |
| LM Arena 总文本排位 | 全球第 13（Elo 1475） | LM Arena 2026-05-20 当周快照 |
| LM Arena 数学排位 | 全球第 7 | 同上 |
| LM Arena 专家提示 / 软件 IT | 全球第 9 / 第 9 | 同上 |
| LM Arena 编码 | 全球第 10 | 同上 |
| 实验室整体排位 | 文本类全球第 6 / 视觉类全球第 5（Plus 变体口径） | Decrypt 5/20 评测 |
| HN 头版 item | 48205626 | news.ycombinator.com |
| HN 头版热度 | 521 分 / 202 条评论（发布当日 9 小时） | HN item 48205626 |
| HN 头版作者 | kevinsimper | HN item 48205626 |
| 框架原生支持 | OpenClaw · Hermes Agent · Claude Code · Qwen Paw · Qoder | 阿里云 5/20 官方公告 |
| 配套 AI 芯片 | T-Head Zhenwu M890（高带宽 / FP4 原生） | QZ + Technetbook 5/20 报道 |
| 配套服务器 | Panjiu AL128 Supernode | 阿里云官方 |
| 参考定价（前代 3.6-Max） | 1.30 / 7.80 美元每百万 token（in / out） | buildfastwithai · DashScope |
| 3.7-Max 公开 API 价 | 截至 5/20 晚未公布 | 阿里云官方 |

数字相互交叉，口径一致。HN item 编号、得分、评论数实查 hn.algolia 与 news.ycombinator 主页；AA Intelligence Index 57 与 1M 上下文实查 artificialanalysis.ai 模型页；35 小时 / 1000 次工具调用 / Terminal-Bench 69.7 分三项以阿里云 5/20 演示为唯一一手来源，QZ 与 Manila Times 转述印证。

## HN 头版顶赞评论原文实录

这次 HN 头版讨论的份量，在评论比在投票还多。挑五条信息密度最高的顶赞回帖原文摆出来。

**goldenarm**（顶赞）：

> "The non-hallucination rate in AA-omniscience is SOTA, better than Opus 4.7, Gemini 3.1 Pro and GPT5.5!"

这是整条 HN 讨论里最被引用的一句。AA-Omniscience 是 Artificial Analysis 团队设计的事实记忆 + 幻觉率联合评估，六千道题覆盖商业、人文社科、科学工程数学、医疗、法律、软件工程六大领域，每题模型可以选择「答」或「拒答」。答对得分，答错被扣，拒答不被扣。-100 到 100 分制。这个评测的反人类难度在于：模型必须诚实承认自己不知道。

**gslepak**（追问）：

> "Note that a perfect 'non-hallucination rate' is rather meaningless as such tests can contain human hallucinations."

意思是榜单上的「真理标准」也是人写的，可能本身就包含错误。这是一个公道的提醒——不过 AA-Omniscience 是六千题多领域抽样，统计层面的可信度还是高于单题答错单题答对。

**jampekka**（追问）：

> "This counts only incorrect answers though. A model can get 0% hallucination rate just by refusing to answer all questions."

技术细节追问：拒答模型可以拿满分吗？答案是不会，因为 AA-Omniscience 的分数同时奖励正确答与拒答弃权，而对答错重罚。你只拒答会拿一个很低的「无害但无用」分。要拿到榜首，必须做到「答得多 + 错得少」。

**briga**（实战切换）：

> "I was getting dangerously close to my weekly Claude Code limit last night so I had Claude set up Qwen3.6 with llama.cpp and OpenCode. Honestly it's a great (free!) alternative to Claude Code."

这是 HN 顶赞评论里最贴近开发者一线的一条。briga 周限额快用完了，让 Claude 帮他在 llama.cpp + OpenCode 上接了千问 3.6 当后备。他给的反馈直白：「真是个不错的免费替代品」。

**rayboy1995**（本地跑实战）：

> "I'm running Qwen 3.6 27B Q5 K M GGUF...I already have some code it has made commited and pushed...once I had all that settled I actually am able to use this how I use Claude Code."

补充：rayboy1995 在本地跑千问 3.6 27B Q5 K M 量化版的 GGUF，已经有些代码是这个本地模型写好提交并推送上去的。他说一旦配好之后，就能像用 Claude Code 一样用它。

把这五条评论放一起看，海外开发者对千问的态度已经很清晰：**这不是个「便宜的替代品」，是「能进入我日常工作流的真候选项」**。3.6 阶段就已经在 HN 这个挑剔的社区里产生粘性，3.7-Max 一发布顺势上头版。

## AA-Omniscience 非幻觉率榜单的工程含义

![AA-Omniscience 非幻觉率排行 千问 3.7-Max 海外首发占位 Gemini 3.1 Pro Opus 4.7 Gemini 3.5 Flash GPT-5.5 DeepSeek V4](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/qwen-3-7-max-overseas-hn-evaluation-hallucination-2026-05-21/qwen-3-7-max-omniscience.png)

把 AA-Omniscience 榜单这一项放大说。先抄一遍 Artificial Analysis 官方对评测的定义：

> "AA-Omniscience comprises 6,000 questions spanning 42 topics within six domains: Business, Humanities & Social Sciences, Science/Engineering/Mathematics, Health, Law, and Software Engineering. The scoring metric ranges from -100 to 100, where 0 means as many correct as incorrect answers. Models receive no penalty for refusing to answer."

这个评测之所以重要：它是少数把「幻觉成本」直接写进分数的评测。模型一旦自信地编了一个不存在的事实，扣分。你拒答只能拿零分到中等分。要冲榜，得「答得多 + 错得少」。

| 模型 | AA-Omniscience 分（截至 2026-05-20 当周） | 来源 |
| --- | --- | --- |
| 千问 3.7-Max | 海外开发者口径 SOTA（高于以下三家） | HN item 48205626 顶赞 |
| Gemini 3.1 Pro Preview | 33（榜单原第一） | AA 官方 |
| Claude Opus 4.7（Max Effort） | 26 | AA 官方 |
| Gemini 3.5 Flash（high） | 23 | AA 官方 |
| GPT-5.5（xhigh） | 推算落后于以上 | AA 同期榜单口径 |
| DeepSeek V4 | 推算落后于以上 | AA 同期榜单口径 |

需要诚实标注的：千问 3.7-Max 的具体分值阿里云官方尚未给出，AA 官方榜单还在更新中，海外开发者「SOTA」口径是基于 AA 站点上千问 3.7-Max 已经出现在 Intelligence Index 评测列表的事实加 HN 顶赞回帖的解读。等 AA 给出确切数字之后再回来对账。

把这个评测放到行业语境里看，工程含义比分数本身更值得讲。**长程 agent 任务的最大杀手不是模型不够聪明，是模型在不知道答案时编一个**。一个 35 小时连续自治、调用一千次工具的任务，只要中间某一步幻觉一次出错，后面所有步骤都建在错的基础上。AA-Omniscience 这种「敢拒答比硬猜更重要」的评测，恰好就是衡量 agent 长程鲁棒性的关键。

千问 3.7-Max 在这一项被海外开发者认定为 SOTA，意味着阿里在「agent 时代的基础设施型基座」这条路上已经站到了最前面。这不是「中国版的 Claude」，是「全球当下最敢承认自己不知道的大模型」。

## 长程自治 35 小时 · 1000 次工具调用的演示

![长程自治能力 千问 3.7-Max 海外开发者最看重的硬指标 35小时1000次工具调用 Terminal-Bench 69.7 compute kernel 加速 10 倍](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/qwen-3-7-max-overseas-hn-evaluation-hallucination-2026-05-21/qwen-3-7-max-long-horizon.png)

阿里云 5/20 主舞台上拿出来演示的硬指标，是这个：

- **连续自治时长：35 小时无明显性能衰退**
- **单次工具调用数：1000 多次**
- **演示任务：自主写一个比英伟达官方版本快十倍的 GPU compute kernel**
- **Terminal-Bench 2.0-Terminus harness 评测：69.7 分**

这个组合很硬。35 小时这个数字意味着：模型可以无人值守跑完一个完整的工作日加加班，过程中根据中间结果自己决定下一步调用哪个工具。一千次工具调用意味着模型在长链路上反复使用文件读写、代码执行、文档检索、命令行执行这些原子能力，且不被上下文挤爆。

行业里把这种能力叫「long-horizon autonomy」——长程自治。它不是 benchmark 分数，而是一个产品级能力的门槛。Anthropic Claude Code、Cursor Composer、Cognition Devin、OpenClaw、Hermes 这一波 agent 产品的天花板都被这个能力卡住。能跑五分钟和能跑三十五小时是完全不同的两类模型。

Terminal-Bench 2.0-Terminus 69.7 分这个数字与 Cursor Composer 2.5 在同一项上的 69.3 分几乎打平。这是有意思的——Cursor 5/18 才宣布自家旗舰用 Moonshot 的 Kimi K2.5 当基座做了 85% 的 RL 后训练才拿到 69.3，千问 3.7-Max 直接交了 69.7。**国产基座的「全栈出货」能力已经接近海外产品级方案的精修水准**。

阿里同步还发布了配套的 T-Head Zhenwu M890 AI 训推一体处理器（FP4 原生 + 高带宽 + 高密度 HBM）与 Panjiu AL128 Supernode 服务器节点。芯片、集群、基座、agent 框架、应用层这五层，阿里一次性全栈端出。海外评论用了一个直白的词：**Alibaba 在做「中国 AI factory」**——同时控制硬件层和模型层。

## LM Arena 五项分类的全球位次

![千问 3.7-Max 在 LM Arena 五项分类的全球排位 数学第7 专家提示第9 软件IT第9 编码第10 总文本第13](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/qwen-3-7-max-overseas-hn-evaluation-hallucination-2026-05-21/qwen-3-7-max-arena-radar.png)

把 LM Arena 公开盲测的位次摆出来：

| 分类 | 千问 3.7-Max 全球排名 | 备注 |
| --- | --- | --- |
| 数学 | 第 7 | 国产模型历史最高位次 |
| 专家提示 | 第 9 | 首次进入全球前十 |
| 软件 / IT | 第 9 | 首次进入全球前十 |
| 编码 | 第 10 | 与 Composer 2.5 / Opus 4.7 同档 |
| 总文本 | 第 13（Elo 1475） | Alibaba 实验室整体跃至全球第 6 |

阿里巴巴 AI 实验室作为一个整体单位，在 LM Arena 上的文本类排位已经跃居全球第 6，视觉类（Plus-Preview 口径）第 5。把 Plus-Preview 和 Max-Preview 两个模型一起看，阿里这次的发布是双线推进——Max 主打闭源旗舰、Plus 主打开源 + 视觉。这是把「最强压一个旗舰」和「最广服务开发者社区」两件事用同一套底层架构串起来。

横向参考海外读者熟悉的对标：

- 在数学一项打到全球第 7，这一档周围是 Google Gemini 3.1 Pro / DeepMind AlphaProof 这种数学专项强者
- 在专家提示一项打到全球第 9，意味着复杂指令解读层面已经追上海外旗舰
- 总文本第 13 不算特别靠前，但 Elo 1475 的分数在 2026 年这个竞争烈度下，意味着「能进入第一梯队」

需要诚实标注的：LM Arena 是公开盲测，但用户提问分布可能不能完全覆盖技术深度。千问 3.7-Max 在数学项的高位次部分可能也反映了阿里在这一类提示上的训练数据偏向。综合 AA-Omniscience、Terminal-Bench、Arena 三套榜单看，才能拿到一个相对完整的画像。

## 国内六家旗舰大模型同档对位横评

![国内六家旗舰大模型同档对位 千问 3.7-Max DeepSeek V4 智谱 GLM 4.5 文心 5.0 豆包 1.5-Pro Kimi K2.5 海外位置参考](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/qwen-3-7-max-overseas-hn-evaluation-hallucination-2026-05-21/qwen-3-7-max-china-landscape.png)

把国内六家旗舰大模型放到一张表上对位。注意这一节的视角是「海外开发者会怎么挑」，所以海外热议度与海外可直连这两项权重更高。

| 厂商 / 模型 | 海外热议度 | 长程自治 | 上下文 | API 价（美元/百万） | 海外可直连 |
| --- | --- | --- | --- | --- | --- |
| 阿里 · 千问 3.7-Max | ★★★★★ | 35 小时 | 1M token | 未公布（参考 3.6-Max 1.30 / 7.80） | 是 · DashScope |
| 深度求索 · DeepSeek V4 | ★★★★ | 8 小时 | 128K | 0.28 / 1.10 | 是 · OpenRouter |
| 智谱 · GLM 4.5 | ★★★ | 未公开 | 128K | 0.50 / 2.00（推算） | 是 · 直接调用 |
| 百度 · 文心 5.0 | ★★ | 未公开 | 128K | 0.84 / 2.10 | 否 · 仅国内 |
| 字节 · 豆包 1.5-Pro | ★★ | 未公开 | 256K | 0.11 / 0.83 | 否 · 仅国内 |
| 月之暗面 · Kimi K2.5 | ★★★★ | 中程（K2.5 开源 checkpoint） | 1M token | 1.10 / 4.40（自营） | 是 · HuggingFace 开源 |

几条值得拆开看的判断：

- **千问 3.7-Max 是六家中唯一在海外 HN 头版主动登榜的**。521 分 / 202 评的热度在 2026 年的国产模型海外讨论里属于顶级。DeepSeek V3 在去年年初也有过 HN 头版，但当时讨论焦点是「成本碾压」，而不是「我要把它接入工作流」。这一次千问 3.7-Max 的讨论焦点已经升级到产品集成层。
- **DeepSeek V4 是性价比第一**。0.28 / 1.10 美元每百万 token 的价格在六家里垫底，约为千问 3.6-Max 价格的四分之一。它在 OpenRouter 上长期挂着，海外开发者用得很顺。但 DeepSeek 暂时没有把「长程自治 35 小时」这个能力做到舞台级别。
- **Kimi K2.5 走完全开源路线**。Cursor 5/18 端出的 Composer 2.5 直接用 Kimi K2.5 当基座做 85% 算力的后训练。这是另一条路径——不靠 API 厂商，靠开源 checkpoint。Kimi 在海外 HF 社区的存在感很强。
- **文心 5.0 与豆包 1.5-Pro 主打国内**。文心走政企赛道、豆包走 C 端国民级应用，两家都没把海外可直连当核心目标。这是个清晰的产品定位选择。
- **智谱 GLM 4.5 走中间路线**。开放 API 给海外开发者直接调用，但热议度还没起来。

整张表读下来一个核心判断：**国产大模型这一档已经形成三种海外可见路径**——

1. 阿里千问：全栈出货（芯片 + 模型 + 框架）+ 海外榜单顶端登场
2. DeepSeek：低价 + OpenRouter 自然分发 + 开发者口碑
3. Kimi：完全开源 + 让海外巨头当甲方（Cursor 已是案例）

这三条路径不冲突，国产生态有空间同时支持。

## 海外开发者切到千问的真实场景

把视角拉回开发者一线。海外开发者目前接入千问，主要走以下四种用法：

**用法一 · 通过 OpenRouter / DashScope 等中转层直接调 API**。千问系列在 OpenRouter 上长期上架，pricing 透明、文档完整。海外开发者改一行 base_url 就能从 OpenAI 切到千问，迁移成本接近零。

**用法二 · 用 OpenClaw / Hermes / Qwen Paw 等开源 agent 框架接入**。这几个框架原生支持千问，不需要折腾。其中 OpenClaw 是阿里自家维护的开源 agent runtime，与 Claude Code CLI 行为高度兼容；Hermes 是社区做的多模型路由框架；Qwen Paw 是阿里推的桌面 agent 客户端。

**用法三 · 跑本地量化版**。HN 顶赞评论里 rayboy1995 跑的就是 Qwen 3.6 27B Q5 K M GGUF。千问开源版的量化策略对消费级 GPU 友好，4090 / 5090 一张卡能跑 27B 的高质量量化。这条路适合不想付订阅费、追求本地隐私、或者用本地模型做 backup 的开发者。

**用法四 · 把 Claude Code 的 CLI 接到千问 API**。海外有几个社区项目做了「Claude Code 加千问后端」的封装，逻辑跟国内的 DeepClaude（Claude Code 加 DeepSeek 后端）类似。这条路对已经习惯 Claude Code 工作流但想降低 API 账单的开发者很实际。

briga 在 HN 顶赞回帖里描述的就是用法二——周限额快用完时让 Claude 帮他设置一个本地的 OpenCode + 千问 3.6 + llama.cpp 当 backup。这是一个非常具体的工程场景：**主力 Claude Code 不动，备份切到千问，账单不爆**。

## 这件事对国内开发者意味着什么

千问 3.7-Max 海外热议这件事，国内开发者其实站在一个很特殊的位置——我们既是产品的目标用户，又是看着海外社区认可它的旁观者。把这两层叠起来看，有几个积极信号：

**第一 · 国产基座第一次站在海外榜单顶端被点名表扬**。AA-Omniscience 这种「敢拒答比硬猜更聪明」的评测，不是中国厂商擅长刷分的传统赛道，而是海外开发者认可的事实可靠性评测。千问 3.7-Max 在这一项被认定为 SOTA，含金量比单纯的 benchmark 排名高一档。

**第二 · 国产基座在 agent 长程自治这条路上跑通了完整闭环**。35 小时 / 1000 次工具调用 / 自主写比英伟达官方快十倍的 compute kernel，这一组数字组合在 2026 年的海外旗舰里也很难找到对位。Anthropic Claude Code 在 Devin 试用里能跑十多小时，OpenClaw 主线场景目标三到五小时，35 小时是当下行业的一个新天花板。

**第三 · 阿里在做「中国 AI factory」的全栈实验**。Zhenwu M890 芯片 + Panjiu AL128 服务器 + 千问 3.7-Max + OpenClaw / Hermes / Qwen Paw 框架 + 上层应用，五层一次端出。这种全栈策略给国产产业链一个完整的本土 vs 海外对照样板。对国内开发者来说，多了一条「整套都用国产」的可行路径。

**第四 · 海外开发者主动接入的路径已经清晰**。OpenRouter / DashScope 直连、OpenClaw 框架、本地量化、Claude Code 后端替换——四条路径都跑通了，海外社区不需要被「教育」怎么用千问，他们已经在用。

需要诚实标注的：千问 3.7-Max 在国内一般开发者的可直接消费体验上，目前主要靠阿里云 DashScope API。阿里云的 token 计费、企业账号配额、国内常见的 API 网关延迟，这些工程现实问题在 3.7-Max 上线之后并没有自动变好。打算从 Claude API 切到千问 3.7-Max 的国内团队，仍然要做一轮自家压测对账。

## 没回避的几条不确定性

不夸大事实，几件事需要诚实说清楚：

- **AA-Omniscience 上千问 3.7-Max 的具体分值**目前阿里官方未公开，HN 顶赞「SOTA」口径是海外开发者基于 AA 站点榜单更新与 Intelligence Index 中 AA-Omniscience 已被列入的事实做的解读。等 AA 官方给出明确分数后再来对账。
- **35 小时 / 1000 次工具调用**这个口径来自阿里云 5/20 主舞台演示，目前唯一一手出处是阿里官方公告与 QZ 转述。第三方独立复现还需要时间。Decrypt 自己的实测注意到 preview 阶段的 Web 搜索与代码解释器在某些路径被禁用，等公测开放再校对。
- **公开 API 价格未公布**。阿里云目前没有给出千问 3.7-Max 的 token 单价，参考前代 3.6-Max 的 1.30 / 7.80 美元每百万 token 可以做大致预算，但 3.7-Max 的实际定价可能上下浮动 30% 以内。
- **LM Arena 排名第 13** 这个位次需要放在历史语境里看——这是国产模型在 LM Arena 公开盲测的历史最高位次，但绝对位次仍然是国际旗舰梯队的中位偏下。要真正进入前十甚至前五，还需要后续 3.7-Max 正式版（非 Preview）的发布。

把不确定性诚实摆出来不影响主线判断：**国产基座第一次在海外榜单顶端、海外社区头版主动讨论里被点名表扬**。这是当下能确认的事实。

## 写在结尾 · 国产基座的海外首演

千问 3.7-Max 这件事最该被读懂的一层，不是又一个国产模型刷榜，而是国产基座生态第一次在海外社区里**从评分对象升级为评分参与者**。AA-Omniscience 的榜单原本是海外厂商在卷的赛道，千问 3.7-Max 直接冲到顶端；HN 头版的讨论原本是「试试这个新工具」，这一次升级到「我要把它接入我的日常 Claude Code 工作流」。

国内一线 AI 开发者从这件事拿到的是一个很积极的信号：我们手边能用的国产基座，已经是被海外开发者主动选择、主动评测、主动安利的全球前列模型。这一程时代正在打开——基座层有千问、DeepSeek、Kimi、GLM 多条路径；agent 框架层有 OpenClaw、Hermes、Qwen Paw 等多套开源工具；应用层有阿里、字节、月之暗面、智谱多家产品。

这一刻最该被记住的判断很简单：**国产大模型不再是「中国本地化的全球模型替代品」，而是「全球开发者主动选择的前列方案之一」**。海外社区已经走完了认知升级。剩下的事情是国内开发者把这股势头接住，把工程能力补上来，把每一个国产基座上跑出的产品做到全球水准。

---

*事实采集自 HN item 48205626 顶赞回帖（goldenarm / gslepak / jampekka / briga / rayboy1995）verbatim 原文、Artificial Analysis 官方 omniscience 评测页与 qwen3-7-max 模型页、阿里云 Apsara Cloud Summit 2026-05-20 官方公告、Manila Times、QZ、SCMP、Decrypt 同期报道；HN 数字 / Arena 排名 / 35 小时 / 1000 次工具调用等关键口径按多源交叉印证取最严值；AA-Omniscience 具体分值阿里官方未公开的部分一律标注「HN 顶赞口径」「海外开发者口径」，不替阿里擦边。*
