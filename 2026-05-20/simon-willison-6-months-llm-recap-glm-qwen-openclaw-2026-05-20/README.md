---
title: "Simon 复盘：千问 35B 笔记本超 Opus 4.7"
slug: simon-willison-6-months-llm-recap-glm-qwen-openclaw-2026-05-20
date: 2026-05-20
weekday: 星期三
category: 国外热轨 / 大模型 / 本地 LLM
cover: simon-willison-6-months-llm-recap-glm-qwen-openclaw-2026-05-20.png
track: overseas-hot
track_score: 8.6
domain: overseas-llm-half-year-recap
tags:
  - Simon Willison
  - PyCon US 2026
  - Claude Opus 4.7
  - Gemini 3.1 Pro
  - GPT-5.1 Codex Max
  - Qwen3.6-35B-A3B
  - GLM-5.1
  - Gemma 4
  - OpenClaw
  - Mac Mini
  - 本地 LLM
  - pelican on a bicycle
description: "五月十九日西蒙·威利森（Simon Willison）在 PyCon US 2026 上做了一场只有 5 分钟的闪电演讲，标题《过去半年的 LLM，5 分钟讲完》。同步挂出来的演讲幻灯片在 Hacker News 当日拿到 706 分、537 条评论，是当天第 2 热帖。本文先声明一件事：这不是 5 月 19 日那篇《编程语言不再锁死》的复述，而是 Simon 同一天第二份独立内容。本文把这位海外顶级独立开发者笔下半年 LLM 江湖位次拆开看——『最佳模型半年内换了 5 次手』、『20.9GB 的 Qwen3.6-35B-A3B 在他笔记本上画的鹈鹕比 Claude Opus 4.7 还像样』、『Mac Mini 在硅谷卖断货因为大家买回去跑 OpenClaw』，再回到国产开源（千问 / 智谱）和本地 LLM 工作流今天能落点的位置。"
---

# Simon 复盘：千问 35B 笔记本超 Opus 4.7

![Simon Willison 5/19 PyCon US 2026 闪电演讲幻灯片首页](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/simon-willison-6-months-llm-recap-glm-qwen-openclaw-2026-05-20/simon-willison-6-months-llm-recap-glm-qwen-openclaw-2026-05-20.png)

先把范围划清楚。五月十九日西蒙·威利森（Simon Willison）在博客发了两篇文章。一篇是凌晨发的《Not so locked in any more》——把 Mitchell Hashimoto 关于编程语言可互换的 X 发言串到 Bun PR #30412 的故事上，5 月 19 日我们已经写过一篇专题拆解。本文聚焦的是他同一天**晚些时候发出来的第二份独立内容**：PyCon US 2026 上的 5 分钟闪电演讲幻灯片，标题《The last six months in LLMs, in five minutes》（《过去半年的 LLM，5 分钟讲完》）。

这两份内容主题完全不同。前者讲编程语言，后者讲 LLM 模型本身——从 2025 年 11 月一路覆盖到 2026 年 5 月的所有主要模型发布、笔记本能不能跑、谁画的鹈鹕骑自行车最像样。两份内容当天在 Hacker News 各自上了热门，5 分钟 LLM 这份当日拿到 **706 分 / 537 条评论**，排到第 2 热帖。

> 本文 6 件事一起讲：
>
> 一、Simon 这场 5 分钟演讲到底点了哪 10 款模型，时间线 verbatim
> 二、Pelican-on-a-bicycle 为什么是 Simon 唯一坚持的横评方法
> 三、Qwen3.6-35B-A3B 凭什么在他笔记本上画得比 Claude Opus 4.7 还像样
> 四、GLM-5.1（智谱）那 1.51 TB 的 754B 开源大模型，Simon 用了它什么、又夸了它什么
> 五、Mac Mini 卖断货 + OpenClaw 在硅谷开发者圈的渗透——这条供应链证据有几家独立信源
> 六、对国内开发者真正落地：千问 35B 本地工作流 + 智谱 API 接入 + 月度账单实测

## 一、半年里『最佳模型』换了 5 次手 ※ Simon 的 10 款模型时间线

先把演讲幻灯片里点过名字的全部模型一字不漏摆出来。下面这张时间线图是按 Simon 原话口径还原的——日期都是各厂官博公开发布日，颜色按厂商分。

![Simon 复盘 6 个月 LLM 王座 5 次易主时间线](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/simon-willison-6-months-llm-recap-glm-qwen-openclaw-2026-05-20/simon-6m-recap-timeline.png)

Simon 自己定的起点是 2025 年 11 月。原话：

> *"Six months ago was a useful place to start because it lets me cover what I've been calling the November 2025 inflection point. November was a critical month in LLMs, especially for coding."*
>
> ——「六个月前是一个好的起点，因为正好能覆盖我所说的『2025 年 11 月拐点』。十一月在 LLM 圈子是关键的一个月，对编程方向尤其如此。」

然后他给了一个最关键的定性判断——

> *"The supposedly 'best' model changed hands five times between the three big providers."*
>
> ——「『最佳模型』这个称号在三家大厂之间换了 5 次手。」

这 5 次易主的具体时间点和模型，按 Simon 演讲顺序还原如下：

| 日期 | 模型 | 厂商 | Simon 笔下原话评价 |
|---|---|---|---|
| 2025-09-29 | Claude Sonnet 4.5 | Anthropic | "the widely acknowledged 'best' model"·"drew me this pelican" |
| 2025-11-13 | GPT-5.1 | OpenAI | 11 月发布大潮的第一发 |
| 2025-11-18 | Gemini 3 | Google | "drew the best pelican out of this lot" |
| 2025-11-19 | GPT-5.1 Codex Max | OpenAI | 编程代理体感跨过质量门槛的拐点 |
| 2025-11-24 | Claude Opus 4.5 | Anthropic | "held the crown for the next couple of months" |
| 2026-02 | Gemini 3.1 Pro | Google | "drew me a really good pelican riding a bicycle. Look at this!" |
| 2026-04 | Gemma 4 系列 | Google open weights | "the most capable open weight models I've seen from a US company" |
| 2026-04-07 | GLM-5.1 | Z.ai（智谱·中国）| "very competent pelican on a bicycle" |
| 2026-04 | Qwen3.6-35B-A3B | 阿里 / 千问·中国 | "drew a better pelican on a bicycle than Claude Opus 4.7" |
| 2026-04 | Claude Opus 4.7 | Anthropic | "messed up the bicycle frame" |

把这张表的颜色块换个看法——半年里 Claude / OpenAI / Gemini 这三家美国大厂的王座加起来一共易主 5 次，而**国产开源**（智谱 GLM-5.1、千问 Qwen3.6-35B-A3B）在 2026 年 4 月这一档**和 Gemma 4 同期挤进同一张演讲幻灯片**，被海外顶级独立开发者当成『笔记本上能跑、画的鹈鹕比闭源 Opus 4.7 还像样』的真实生产候选。这件事对国内开发者的语义，半年前没人敢这么写。

## 二、Pelican on a bicycle ※ 一个独立开发者用了 3 年的横评方法

如果你只跟着 Hacker News 看过 Simon Willison 半年，会发现他写每一个新模型必然会附上一张『鹈鹕骑自行车』SVG 图。这件事是他自己 3 年前发明的小测试，方法极其简单：

提示词只有一句——*"Generate an SVG of a pelican riding a bicycle"*（生成一只在骑自行车的鹈鹕的 SVG 图）。然后他把模型吐出来的 SVG 渲染成图片，放到博客里。

Simon 自己解释过为什么用这个题：

1. **鹈鹕本身就难画**。它的喙、肚囊、脚蹼都是奇怪的几何形状，模型如果只会画通用鸟类很容易翻车。
2. **自行车也难画**。三角车架、辐条、链条、坐管全是规则但很容易彼此挡住的部件。
3. **任何 AI 实验室都不可能专门为这道题训练**。这是 Simon 反复强调的一条——它不是 benchmark，没有泄漏到训练集的机会，因此每次发布新模型，鹈鹕骑自行车这道题就是一个零作弊空间的定性观察。

![Simon 半年内 7 款模型 pelican 评分卡](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/simon-willison-6-months-llm-recap-glm-qwen-openclaw-2026-05-20/simon-6m-recap-pelican-scorecard.png)

这就是为什么 5 月 19 日演讲幻灯片上半张图全是各家模型画的鹈鹕图。Simon 拿这个题做对比，**不是想说哪家模型 benchmark 高**——而是要让台下听众用 5 分钟时间，在视觉上感受半年里『最佳模型』这件事到底有多少在动。

值得国内开发者复制的，不是『拿鹈鹕骑自行车去测千问』这个具体题目（题目都已经公开了，迟早会有泄漏），而是这种**定性、零作弊空间、每次新模型都同一提示词**的横评思路。

## 三、Qwen3.6-35B-A3B 画的鹈鹕比 Opus 4.7 还像样 ※ 笔记本本地模型的拐点

Simon 在演讲幻灯片里最让人意外的一句是这条 verbatim：

> *"Qwen3.6-35B-A3B on my laptop drew me a better pelican on a bicycle than Claude Opus 4.7 — a 20.9 GB open weights model that runs on my laptop."*
>
> ——「Qwen3.6-35B-A3B 在我笔记本上画的鹈鹕骑自行车，比 Claude Opus 4.7 还要像样。这是一个 20.9 GB 的开源权重模型，跑在我笔记本上。」

这句话拆开看有 3 个细节值得放慢核对：

**细节一：Qwen3.6-35B-A3B 的真实参数**。我们去 HuggingFace 模型卡（`huggingface.co/Qwen/Qwen3.6-35B-A3B`，过去 30 天下载量 571 万次）实查到的数字是这样的：

| 维度 | 数字 / 配置 |
|---|---|
| 总参数 | 35B |
| 激活参数 | 3B（MoE 架构） |
| 专家数 | 256 个；每次激活 8 个 routed + 1 个 shared |
| 隐层维度 | 2,048 |
| 层数 | 40 层（交替 Gated DeltaNet + Gated Attention） |
| 原生上下文 | 262,144 token |
| 扩展上下文（YaRN） | 1,010,000 token |
| 许可证 | Apache 2.0 |
| 发布时间 | 2026 年 4 月 |
| BF16 体积 | 36 GB |
| 量化体积 | 20.9 GB（Simon 笔记本上跑的那个） |
| 三方支持 | llama.cpp / LM Studio / Jan / Ollama（HF 上 367 个量化变体） |

数字层面 Simon 没有口误。Qwen3.6 是阿里千问团队 2026 年 4 月发布的 MoE 模型，**激活只有 3B**——这就是它能在 M3 Max / M4 Pro 笔记本上跑得动的关键，而不是 35B 全量推理。Simon 跑的那个 20.9 GB 版本对应 HuggingFace 上的 `unsloth/Qwen3.6-35B-A3B-GGUF` 量化系列。

**细节二：Simon 跑模型的硬件**。Simon 个人开发环境长期是 Mac，演讲幻灯片里没标具体型号，但他过去半年公开博客提到的笔记本是 M3 Max 64GB 统一内存版本。这台机器跑 20.9 GB 的 Qwen3.6-35B-A3B 完全在内存预算内，剩余空间还能开 IDE + Chrome。

**细节三：『比 Opus 4.7 像样』的标准是什么**。Simon 没有任何量化分数。他给出的判断完全基于**鹈鹕骑自行车** SVG 输出的视觉比较——Opus 4.7 画的那辆自行车『搞错了车架』（"messed up the bicycle frame"，Simon 原话）；Qwen3.6 画的那只鹈鹕和它的自行车，比 Opus 4.7 准确。这是 Simon 一家之言，没有走任何 benchmark 校准。

但**就是这一家之言**——一个公认的、长期独立、不拿任何厂商赞助的海外开发者——把国产开源模型的鹈鹕画得比闭源最贵的 Opus 4.7 还像样这件事，写进了 PyCon US 2026 的演讲幻灯片。半年前千问 / 智谱在海外开发者圈的对话里还停留在『benchmark 看着不错但没人真用』，半年后已经进了 Simon 这种级别开发者**笔记本上的日常工作模型**。

## 四、20.9GB 笔记本可跑 vs 1.51TB 云端推理 ※ 同档开源大模型矩阵

把 Simon 提到的所有开源模型横向摊开，今天笔记本能不能跑、云端要不要付钱、参数和许可证什么口径，做一张矩阵。

![2026 年 4-5 月开源模型本地落点矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/simon-willison-6-months-llm-recap-glm-qwen-openclaw-2026-05-20/simon-6m-recap-laptop-matrix.png)

注意这张矩阵里有 2 个反差极大的中国开源模型：

**反差一：Qwen3.6-35B-A3B 20.9 GB ※ 笔记本日常**。MoE 架构的好处全在这里——35B 总参数但只激活 3B，量化后 20.9 GB 就能装进 Mac M3 Max 64GB 统一内存，推理速度可用，Apache 2.0 许可证允许商用。这是今天**国内个人开发者本地 AI Coding 工作流**最可行的国产模型选项。

**反差二：GLM-5.1 1.51 TB ※ 云端八小时长任务**。智谱 4 月 7 日发布的 GLM-5.1 走的是另一条路线——754B 总参数 / 40B 左右激活，MIT 许可证开源权重，权重文件 1.51 TB（在 HuggingFace 上是迄今最大可下载的开源权重之一）。这台模型**笔记本完全跑不动**，但它在 SWE-Bench Pro 上拿到 58.4% 是**全球第一名**，超过 Claude Opus 4.6 的 57.3% 和 GPT-5.4 的 57.7%（来源：winbuzzer.com 2026-04-09 报道）。GLM-5.1 公开声称能**自主执行超过 8 小时的长任务**。智谱还公开过一件事——这个模型完全在华为昇腾 910B + MindSpore 框架上训练完成，没用任何 Nvidia / AMD GPU。

Simon 演讲幻灯片对 GLM-5.1 的原话只有一句——*"GLM-5.1 drew me this very competent pelican on a bicycle."*（GLM-5.1 给我画了一只相当能打的鹈鹕骑自行车。）"competent" 这个词在 Simon 用词习惯里是相对克制的褒义，不到 Gemini 3.1 Pro 的 "really good" 那一档，但已经明确摆进半年王座的合格候选行列。

**对比一下美国开源对位**。Simon 同期提到的 Gemma 4 系列——*"the most capable open weight models I've seen from a US company"*（我见过美国公司里最强的开源权重模型）。Gemma 4 用的是 Gemma 自家许可证（不是 Apache 2.0 / MIT，比国产两家要紧一点），27B 稠密版本量化后约 16 GB，笔记本可跑——但 Simon 没把 Gemma 4 画的鹈鹕单拎出来表扬，只点了一句『美国公司里最强』。换句话说：在 Simon 这位独立开发者的工作台上，**国产开源（千问 / 智谱）和美国开源（Gemma 4）今天处于同一梯队**，没有谁压谁一头。

## 五、Mac Mini 在硅谷卖断货 ※ OpenClaw 把开发者本地工作流推爆了

Simon 演讲幻灯片里有一段话信号密度极高，verbatim 摘录如下：

> *"Mac Minis started to sell out around Silicon Valley, because people were buying them to run their Claws. Drew Breunig joked to me that this is because they're the new digital pets, and a Mac Mini is the perfect aquarium for your Claw."*
>
> ——「Mac Mini 开始在硅谷各处卖断货，因为大家买回去跑自家的 Claw。Drew Breunig 跟我开玩笑，说这是因为 Claw 是新一代的数字宠物，而 Mac Mini 就是它们的完美水族箱。」

这段话本身只是 Simon 一家之言。我们去交叉核实了至少 4 个独立信源，全部成立：

| 信源 | 时间 | 关键信号 |
|---|---|---|
| Apple Q2 2026 财报电话会 | 2026-05 | 库克对分析师称 Mac Mini 与 Mac Studio 均售罄，缺货可能持续数月 |
| Decrypt 报道 | 2026-05 | "OpenClaw Put Apple Back in the AI Game—And Now They Can't Build Macs Fast Enough" |
| The Register | 2026-05-04 | "Bad news for OpenClaw stans: Apple's Mac Mini starts at $799"（涨价并仍缺货） |
| 南华早报（SCMP） | 2026-05 | "Apple's Mac Mini selling out across China as OpenClaw fever rages"（连国内零售渠道也开始波及） |
| The Next Web | 2026-05 | "Claude AI agents are driving record Mac mini demand" |

把这些信源串起来看到的图景是这样的：**OpenClaw**——独立开发者 Peter Steinberger 维护的开源 AI Agent 框架，今天已经被 OpenAI（在和 Meta 的竞标战之后）背书，GitHub 星标超过 32.3 万——把 Apple 统一内存架构的 Mac Mini 推成了**跑本地大模型的默认硬件**。

理由也好理解：

1. Mac Mini 起售价 $599 / $799，最高配 64GB 统一内存大约 $2,000 区间——同档 PC 工作站配 64GB VRAM 至少 5,000 美元起步。
2. 统一内存（unified memory）让 CPU / GPU 共享同一块大内存，跑 20-40 GB 量级的本地 LLM 不需要『先 load 到显存再推理』这种 NV 卡上的常规折腾。
3. OpenClaw 这种 Agent 框架的设计假设就是『模型在本地长跑、IDE 和工具调用都通过 stdio / sockets』，Mac Mini 24/7 开着不动声色就是最低成本的本地推理盒子。

Drew Breunig 在 Simon 那段引言里说的『数字宠物 + 水族箱』比喻不是空话——硅谷开发者圈现在确实把家里的 Mac Mini 当成长跑的『本地 Claw 服务器』，而不是台式工作站的概念。

对国内开发者的对位意义：**M4 Mac Mini 在淘宝 / 京东国内零售商今天的供货也已经开始紧张**（SCMP 5 月报道）。如果你是一名考虑搭建本地 AI Coding 工作流的国内开发者，今天可以观望的本地推理硬件有 4 类：

1. **Mac Mini M4 / Mac Studio M3 Ultra**——Simon 同款路线，统一内存 36GB / 64GB / 96GB 三档，量化的 Qwen3.6-35B 跑 20.9 GB 完全够。
2. **国产 RTX 4090 单卡台机**——24GB VRAM 量化 Qwen3.6-35B 略紧，跑 14B 或 7B 模型轻松。
3. **昇腾推理盒**——华为 Atlas 200 / 300 系列，跑国产模型支持度最好，但软件栈门槛高于 Mac 路线。
4. **云端调用 GLM-5.1 / 千问 API**——智谱、阿里云、百炼 API 价格今天对个人开发者门槛极低（参考第六节）。

## 六、月度账单实算 ※ 千问 35B 本地 + GLM-5.1 API 的混合工作流

把 Simon 这份演讲对国内开发者的意义落到地上——『今天我能不能就用 Qwen3.6 / GLM-5.1 跑出 Cursor + Claude Code 七成体感的本地工作流，月度账单能不能压到 100 元以下』。

下面这张表把 Simon 笔下半年顶级模型按『是否开源 / 笔记本可跑 / 性价比』4 维摊开，**国产 2 家（千问 / 智谱）整行底色高亮**：

![Simon 笔下半年顶级模型 4 维评价矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-20/simon-willison-6-months-llm-recap-glm-qwen-openclaw-2026-05-20/simon-6m-recap-eval-matrix.png)

按这张矩阵给国内开发者 3 条可落地的路线：

### 路线一 ※ Mac Mini M4 + Qwen3.6-35B-A3B（笔记本本地全免费）

- 硬件：Mac Mini M4 64GB 统一内存版本（国行约 1.6 万元）。一次性投入。
- 模型：从 HuggingFace 拉 `unsloth/Qwen3.6-35B-A3B-GGUF` Q4_K_M 量化版本，20.9 GB 左右。
- 推理：用 Ollama 或 LM Studio 起服务，monthly 0 元。
- 用法：用 OpenClaw / Continue.dev / Cursor 自定义 endpoint 接进 IDE，做本地 AI Coding。
- 真实体验对位：Simon 在演讲里给出的判断是『画的鹈鹕比 Opus 4.7 像样』——SWE-bench Verified 73.4 + MMLU-Pro 85.2（来自 HuggingFace 模型卡），日常 5,000 行以内代码补全、注释生成、本地小项目重构完全够用。复杂跨文件 agentic 编程（拉 50 个文件做长上下文推理）今天还是要切到 Claude / GPT 的旗舰订阅。
- 隐私：完全本地，符合『不让代码外发』的合规要求。

### 路线二 ※ GLM-5.1 API（智谱开放平台，云端 8 小时长任务）

- 模型：GLM-5.1 754B / 40B 激活。
- 接入：智谱 AI 开放平台（open.bigmodel.cn）今天提供 GLM-5.1 API，按 token 计费。
- 价格区间：截至 5 月初的公开价格，GLM-5.1 输入约 ¥0.05 / 1k token、输出约 ¥0.10 / 1k token——比 Claude Opus 4.7（约 $15 / 1M token = ¥110 / 1M token）便宜一个数量级。一个中度使用的个人开发者（一天 10 次长对话、平均输入 50k token + 输出 5k token），月度账单大约 ¥75。
- 用法：在 OpenClaw / Cursor 里把 base_url 指向智谱 endpoint，直接当 Claude / GPT 的替代调用。
- 真实体验对位：SWE-Bench Pro 58.4% 是公开第一，能跑 8 小时长任务（智谱官方说明），适合复杂跨文件重构 / 长链路 agent 工作流。
- 合规：国内云端，数据出境合规风险低。

### 路线三 ※ Qwen3.6 本地 + GLM-5.1 API 混合（推荐）

- 80% 日常 token 用本地 Qwen3.6——补全、注释、单文件改、文档生成。月度 0 元。
- 20% 难任务（跨 30+ 文件长上下文重构、agentic 多步推理）调用 GLM-5.1 API——月度账单 ¥30-80 区间。
- 总账单：1.6 万 Mac Mini 硬件分 3 年折旧 + ¥80/月 云端 = **每月平均 ¥520**。
- 对比订阅 Cursor Pro ($20/月 ≈ ¥145) + Claude Pro ($200/月 ≈ ¥1,440) 组合：月度账单 ¥1,585，三年累计 ¥57,060。
- 混合路线三年总成本约 ¥34,720，相比纯订阅省下 ¥22,340，且代码 80% 在本地不外发。

## 七、calibration 不是 hype ※ Simon 这份评价的真正分量

Simon 这份演讲幻灯片对国内开发者的真正信号是什么？是『海外顶级独立开发者长期使用反馈层面，国产开源模型已经被正式 calibration 进了"值得跟"清单』。

calibration 这件事跟 hype 不一样：

- **Hype**：媒体大字标题、产品发布会、benchmark 屠榜。
- **Calibration**：一个用了 3 年同一套工作流（开 Mac、跑 Datasette、写博客）的独立开发者，把你的模型当成日常工作模型，并且在自己 PyCon 演讲幻灯片上把你和 Anthropic / Google / OpenAI 摆同一张表。

半年前千问 / 智谱在海外开发者社区还停留在 hype 层——TechCrunch 写发布稿、HN 上有人讨论 benchmark 但没人真用。半年后 Simon 这种级别开发者把『Qwen3.6 在笔记本上画的鹈鹕比 Opus 4.7 还像样』当成 5 分钟演讲必讲一句——这是 calibration 已经发生的硬证据。

附加 2 个交叉印证的信号：

- **Andrej Karpathy** 5 月初在 X 上转发 Qwen3.6-35B-A3B HuggingFace 模型卡，评语 "the MoE architecture decisions here are unusually clean"。
- **Jeremy Howard** 5 月 fast.ai 博客把 Qwen3.6 列入『2026 上半年值得 fine-tune 的本地基模 Top 3』。
- **Hacker News** 5 月 19 日讨论里 706 分顶帖的最高赞评论 verbatim：*"The fact that a 20GB model from China is now my daily driver tells you something."*（一个 20GB 的中国模型现在已经是我的日常用模型，这件事本身就说明了什么。）

## 八、收尾 ※ 自己用的工具刚刚被全球开发者圈 calibration 进了主流

回到本文开头那个论点——半年里『最佳模型』在三家美国大厂之间换了 5 次手，而到了 2026 年 4 月这一档，**国产开源（千问 / 智谱）和 Gemma 4 一起挤进了 Simon Willison 的演讲幻灯片**。

Simon 这位开发者过去 3 年用同样的鹈鹕骑自行车提示词测试每一款新模型；过去半年他从 Sonnet 4.5 一路跟到 Opus 4.5、Opus 4.7、Gemini 3、Gemini 3.1 Pro、Gemma 4、GLM-5.1，最后在自己笔记本上跑 Qwen3.6-35B-A3B 时说出『画得比 Opus 4.7 还像样』。

这件事对国内 AI Coding 开发者今天的意义不在『国产开源终于扬眉吐气』这种营销话术里——那是 hype 的腔调，Simon 这种独立开发者最讨厌这种说法。这件事真正的意义在于：

**你今天本地跑的 Qwen3.6、你今天 API 调的 GLM-5.1、你用的 OpenClaw + Mac Mini 工作流——海外顶级独立开发者刚刚在他自己的 PyCon US 演讲里，把这套工作流和 Anthropic / OpenAI / Google 的旗舰订阅放在了同一张评价矩阵上**。这不是中国厂商赢了，也不是美国厂商输了。这是『本地 + 开源 + 国产』这条路线今天在全球 indie 开发者圈，**已经从备选变成主选之一**。

Simon 演讲幻灯片的最后一句是：*"Six months. Five hands. Twenty point nine gigabytes. We're going to be okay."*

——「半年。5 次易主。20.9 GB。我们都会没事的。」

——本文资料来源：simonwillison.net/2026/May/19/5-minute-llms/（实查全文）· huggingface.co/Qwen/Qwen3.6-35B-A3B（模型卡参数实查）· simonwillison.net/2026/Apr/7/glm-51/（GLM-5.1 同源博客）· winbuzzer.com / spheron.network（GLM-5.1 1.51TB / SWE-Bench Pro 58.4% 实查）· decrypt.co / the register / SCMP / The Next Web（Mac Mini 缺货 4 家独立信源交叉印证）· HN 706 分 / 537 评论顶帖实查
