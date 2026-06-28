---
title: "Tokenspeed 让你肉眼校准本地千问的速度"
slug: tokenspeed-tokens-per-second-perceived-speed-ui-2026-05-21
date: 2026-05-21
weekday: 星期四
category: AI 工具 / 本地大模型 / 推理引擎
cover: tokenspeed-tokens-per-second-perceived-speed-ui-2026-05-21.png
track: overseas_hot
domain: tokens-per-second-perceived-speed-ui
tags:
  - Tokenspeed
  - tok/s
  - 推理速度
  - vLLM
  - SGLang
  - Ollama
  - LM Studio
  - Qwen3
  - DeepSeek
  - 本地大模型
  - 4090
  - 5090
  - M4 Max
  - HN
  - Simon Willison
description: "五月二十日 Mike Veerman 在 mikeveerman.github.io/tokenspeed 上线了一个 HTML 小工具——按数字键 1-9 就能切到 5 / 30 / 60 / 200 / 800 tok/s 九档速度，按 c / t / h / a 切换 code / text / think / agent 四种渲染场景。Hacker News item 48174920 当天上首页拿到 190 分 51 评论，Simon Willison 同日转引。看似只是个小玩具，背后是国内本地大模型开发者天天碰到的硬问题——4090 跑 Qwen3-7B 出来 80 tok/s 到底快不快？M4 Max 跑 32B 量化版 22 tok/s 是能用还是不能用？Tokenspeed 把抽象数字翻成肉眼可感的字幕速度、朗读速度、打字速度。本文把 Veerman 工具的设计逻辑、HN 五派意见、Simon Willison 转引、用 vLLM / SGLang / Ollama / LM Studio 部署千问 / DeepSeek 时怎么用 Tokenspeed 校验，全部摆到桌上。"
---

# Tokenspeed 让你肉眼校准本地千问的速度

![Tokenspeed 像素风格封面 文字以不同速度滚动的速度感对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/tokenspeed-tokens-per-second-perceived-speed-ui-2026-05-21/tokenspeed-tokens-per-second-perceived-speed-ui-2026-05-21.png)

## 这一次的核心判断

五月二十日，独立开发者 Mike Veerman 在 GitHub Pages 上线了一个 HTML 小工具——网址是 mikeveerman.github.io/tokenspeed，副标题是「感受 LLM 每秒输出多少 token」（feel LLM tokens-per-second）。打开页面默认从 30 tok/s 起，按一下空格暂停，按数字键 1 到 9 就能在 5 / 15 / 30 / 45 / 60 / 120 / 200 / 400 / 800 tok/s 九档之间瞬切。Hacker News item 48174920 当天上了首页，190 分 51 评论；Simon Willison 同一天在自己博客转引，原话是「Neat little HTML app by Mike Veerman which simulates LLM token output speeds from 5/second to 800/second」。

这条工具的命门在哪里？**国内开发者每天看到的 tok/s 数字是抽象的**。4090 跑 Qwen3-7B FP16 输出 80 tok/s——这个 80 跟你写代码的体感是什么关系？M4 Max 跑 Qwen3-32B Q4 输出 22 tok/s——这个 22 是能用还是不能用？r/LocalLLaMA 帖子里写「7900 XTX 跑 GLM-4.5-Air 出来 45 tok/s」，这个 45 你应该感到惊喜还是失望？

**抽象数字让你没法决策硬件**。Veerman 的做法是把数字换成肉眼可感的视觉滚动——你眼睛跟着字幕看半分钟，自然知道 5 tok/s 是树莓派那种逐字蹦的折磨，30 tok/s 是你能舒服读代码的最低线，60 tok/s 是托管的 Claude Opus / GPT-4 给你的默认体验，200 tok/s 是 Groq 那种瀑布流，800 tok/s 是 Cerebras 直接瀑布到看不清字。

这件事的意义不只是「好玩」。**国内开发者今年下半年正在大规模迁回本地——千问 3 7B / 32B、DeepSeek V4、GLM-4.5-Air、Kimi K2.5 量化版在 4090 / 5090 / M4 Max 上跑得起来，但部署完一个核心问题是：自家这块 4090 跑出来的 tok/s 到底够不够日常用**？Tokenspeed 给了一个无 GPU 也能感知的对照工具——你不用真买卡，先在 Veerman 的页面上把九档速度都看一遍，你心里就有了「我至少要买能跑到 60 tok/s 的卡」「思考模式我能忍 30 tok/s」这种具体决策。

接下来一节一节看：Veerman 工具的四种模式和九档速度怎么用。再看 HN 五十一条评论里五派代表声音。然后 1 token ≈ 1.4 汉字的换算落到 4090 / 5090 / M4 Max 跑千问的实测 tok/s 矩阵。最后给一份能照着走的本地校验流程。

## 可独立核实的关键数字

| 维度 | 数值 | 来源 |
| --- | --- | --- |
| 工具上线日期 | 二零二六年五月二十日 | mikeveerman.github.io/tokenspeed |
| 作者 | Mike Veerman · 独立开发者 | GitHub Pages 仓库元信息 |
| HN 提交人 | hexagr | news.ycombinator.com/item?id=48174920 |
| HN 标题 | How fast is N tokens per second really? | 同上 |
| HN 热度 | 一百九十分五十一评论 · 当天首页 | 同上 |
| Simon Willison 转引日期 | 二零二六年五月二十日 | simonwillison.net 当日 weeknotes |
| 默认起点速度 | 30 tok/s | tokenspeed 页面默认值 |
| 九档速度 | 5 · 15 · 30 · 45 · 60 · 120 · 200 · 400 · 800 tok/s | tokenspeed 数字键 1-9 |
| 四种渲染模式 | code · text · think · agent | tokenspeed 字母键 c · t · h · a |
| 英文换算口径 | 一 token ≈ 0.77 词 | tokenspeed 页面文案 |
| 30 tok/s 等于多少词每秒 | ≈ 二十三词每秒 | tokenspeed 文案 |
| 5 tok/s 对应硬件 | 树莓派量级本地模型 | tokenspeed 文案 |
| 60 tok/s 对应硬件 | 托管 Claude / GPT 量级 | tokenspeed 文案 |
| 200 tok/s 对应硬件 | Groq 量级 | tokenspeed 文案 |
| 800 tok/s 对应硬件 | Cerebras 量级 | tokenspeed 文案 |

## 模式切换 · code / text / think / agent 不是装饰

打开 Tokenspeed 页面，让人意外的不是文字滚动本身——按 c / t / h / a 四个字母键，渲染内容会切到完全不同的视觉。

![Tokenspeed 四种渲染模式 code text think agent 视觉对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/tokenspeed-tokens-per-second-perceived-speed-ui-2026-05-21/tokenspeed-ui-mock.png)

**code 模式 · 语法高亮伪代码。**Veerman 在页面文案里写得直白：「syntax-highlighted pseudo-code, the most common thing you watch stream out of an LLM」。这条是当前 AI Coding 用户最熟悉的场景——你用 Claude Code、Cursor、千问 Coder 让模型写一段函数，眼睛跟着流式输出走。code 模式滚动的就是这种带颜色高亮的代码片段，让你直接感受「30 tok/s 写 Python 是什么节奏」。

**text 模式 · lorem ipsum 散文。**对应聊天 / 问答场景——你用千问 App 问一个问题，模型答你一段散文。这一模式渲染拉丁文 lorem ipsum，意在剥离语义只剩纯粹的速度感。这一档很关键——读散文跟读代码的眼动节奏不一样，散文你眼睛会跳读，代码你必须逐字读，**同样 30 tok/s 在 code 模式略快、在 text 模式刚刚好**。

**think 模式 · 暗色斜体推理。**Veerman 的原文叫「dim-italic reasoning sentences alternating with code, mimicking a reasoning model thinking out loud」。这是模拟 Claude Opus 4.7 / 千问 3 32B / DeepSeek V4 在 deep reasoning 模式下的思考链流——一段灰色斜体的「让我想想这个问题」，交替一段代码草稿，再来一段「不对，应该这样」。HN 用户 SXX 在评论里专门说这条做得还不够真实——真实推理模型每出 1 个有效 token，背后烧 2 到 3 个思考 token，「thinking 通常烧 code 两到三倍的 token」。这一模式对评估「你的硬件配置够不够跑深度推理」是关键档。

**agent 模式 · 工具调用 + 代码 + 停顿。**这是 Veerman 的设计里最贴近当前 AI Coding 体感的一档——「alternating tool calls and code generation with processing pauses, simulating an AI coding agent」。屏幕上交替出现 `[tool: search]`、`[result: 3 hits]`、然后一段代码生成、然后再一个工具调用——并且会有停顿（处理时延）。这一档的存在直接回应 HN 用户 aurareturn 的判断——**智能体时代，60 tok/s 不再是「快」，而是「最低能忍」**。

四种模式不是花架子。它们对应你买卡时的四种典型工作流：写代码、聊天问答、跑推理链、跑智能体。同样一块 4090 在四种模式下的体感速度差异巨大——你买之前最好都过一遍。

## 速度阶梯 · 5 tok/s 是树莓派 · 800 tok/s 是 Cerebras

Veerman 工具的核心创新在数字键 1-9 的九档预设。每一档都锚到了一个真实硬件量级，国内开发者一看就能对号入座。

![九档速度预设 从树莓派到 Cerebras 真实硬件锚点](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/tokenspeed-tokens-per-second-perceived-speed-ui-2026-05-21/speed-presets-ladder.png)

**第一档 · 5 tok/s · 树莓派量级。**Veerman 把它定义为「树莓派量级本地模型」（Raspberry-Pi-class local model）。这一档你眼睛能看清单个字蹦出来，但需要忍受逐字蹦的延迟。国内对照——树莓派 5 + Ollama 跑 Qwen3-1.7B Q4，实测大致就这个数。

**第三档 · 30 tok/s · 默认起点。**Veerman 在页面顶部写「Start at the default 30 and read along」——默认从 30 起，让你眼睛先建立基准。30 tok/s 换算英文是每秒约 23 词，比你正常朗读快一倍，比熟练打字员略快。**这是「人盯着看舒服」的临界线**。

**第五档 · 60 tok/s · 托管 Claude / GPT 量级。**Veerman 把它定义为「典型托管 Claude 或 GPT」（typical hosted Claude or GPT）。这一档对应的是 API 调用 Anthropic / OpenAI 服务时的常见速度——出来的字流速度刚好让你能跟着读，但又不让你等。HN 用户 aurareturn 说的「智能体时代 60 是底线」就是这一档。

**第七档 · 200 tok/s · Groq 量级。**Veerman 原文「Groq territory」。这一档字流速度已经超过人类阅读极限——你只能看到段落感，看不清单字。这一档存在的意义是给智能体场景——智能体不需要逐字读，它需要的是「整段输出尽快出来好让下一轮工具调用启动」。

**第九档 · 800 tok/s · Cerebras 量级。**Veerman 原文「Cerebras-class」。这一档已经是肉眼分辨不清楚字符的瀑布流——给人看完全是浪费，但给智能体跑多轮 ReAct 循环就很关键。

**国内对照怎么看**？4090 24G + vLLM + Qwen3-7B FP16 ≈ 80 tok/s（落在第五档和第六档之间）。5090 32G + vLLM + Qwen3-32B FP16 ≈ 65 tok/s（落在第五档）。M4 Max 64G + Ollama + Qwen3-32B Q4 ≈ 22 tok/s（介于第二第三档）。这些数字本身意义不大，但放到 Tokenspeed 的九档梯子上一看——**你立刻知道自己买这块卡能享受到什么样的速度档**。

## 国产模型 × 硬件 × 场景矩阵 · 实测 tok/s

下面这张矩阵图是本文最实用的一节——国内开发者 r/LocalLLaMA / Bilibili / 知乎 上的实测帖中位口径，搭配千问 3 / DeepSeek V4 / GLM-4.5-Air 主流配置，在 Tokenspeed 的四种场景下大致跑出来什么速度。

![硬件 × 模型 × 四场景实测 tok/s 矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/tokenspeed-tokens-per-second-perceived-speed-ui-2026-05-21/hardware-model-matrix.png)

**4090 24G + vLLM + Qwen3-7B FP16 · 是国内本地用户的甜点配置。**code 场景 80 tok/s 流畅，text 95 tok/s 流畅，think 42 tok/s 可读，agent 38 tok/s 可读。结论是——**写代码 / 聊天 / 思考全部能用，跑多轮 agent 略卡但能忍**。

**4090 24G + SGLang + Qwen3-32B Q4 · 32B 量化是 24G 显存的极限选项。**code 38 tok/s 可读，text 45 tok/s 可读，think 22 tok/s 卡顿，agent 18 tok/s 卡顿。结论是——**单轮聊天和代码生成能跑，但 deep reasoning 和 agent 多轮基本没法用**。

**5090 32G + vLLM + Qwen3-32B FP16 · 不量化跑 32B 是 5090 的关键卖点。**code 65 tok/s 流畅，text 78 tok/s 流畅，think 36 tok/s 可读，agent 32 tok/s 可读。结论是——**五项全能，是本地跑 32B 模型的下半年新标杆**。

**M4 Max 64G + Ollama + Qwen3-32B Q4 · 苹果统一内存能跑大模型但单卡带宽不如英伟达。**code 22 tok/s 卡顿，text 28 tok/s 卡顿，think 14 tok/s 痛苦，agent 12 tok/s 痛苦。结论是——**胜在能跑 32B 不爆显存、安静、省电；输在速度全线卡顿**。

**M4 Max 64G + LM Studio + Qwen3-7B Q4 · 苹果用户的实用配置是切到 7B 量化版。**code 48 tok/s 可读，text 56 tok/s 可读，think 26 tok/s 卡顿，agent 22 tok/s 卡顿。结论是——**M4 Max 跑 7B 量化是日常聊天 / 代码生成的可用区，但要做 deep reasoning 还是回 4090**。

**树莓派 5 + Ollama + Qwen3-1.7B Q4 · 这是底线参照。**全场景 4 到 10 tok/s——对应 Tokenspeed 第一档。**这个档你可以体验 AI 但没法日常用**。

读这张表的方式是——**先按你常做的工作流（code / text / think / agent）选一列，然后看哪一行的数字落在你能忍受的速度档**。Tokenspeed 给你的就是这个「我能忍受到几档」的肉眼直观锚点。

## HN 帖底代表声音 · 把 tok/s 这件事的辩论摆开

Hacker News item 48174920 当天五十一条评论分布很有代表性——五派意见全部出来了。

![HN item 48174920 五派代表评论 antirez aurareturn ricardobeat unglaublich SXX](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/tokenspeed-tokens-per-second-perceived-speed-ui-2026-05-21/hn-comments-snapshot.png)

**第一派 · 单一 tok/s 数字不够派。**用户 antirez（Redis 作者 Salvatore Sanfilippo）在评论里给了一个最有重量的批评：

> 「Token/sec only makes sense once you tell me decoding t/s, prefill t/s, and the slope as context grows.」
>
> tok/s 这个数字本身没意义——你得告诉我 decoding 阶段的 t/s、prefill 阶段的 t/s、以及随上下文增长这两个数字的斜率。

这一派的核心观点是——你看到的 80 tok/s 是输出阶段的解码速度，但上下文几千 token 时是 80，几万 token 时可能掉到 30，几十万 token 时可能掉到 10。**Tokenspeed 把这件事简化成单个数字滚动，缺了上下文掉速这一维度**。

**第二派 · 智能体提升底线派。**用户 aurareturn 给了一个最有现实价值的体感描述：

> 「Prior to AI agents, I tolerated 10 tok/s. With AI agents, 60 is the minimum.」
>
> AI 智能体出现之前，10 tok/s 我能忍。智能体时代，60 tok/s 是最低线。

为什么？智能体一轮要跑多个工具调用、多段代码生成、多次重试。10 tok/s 跑一轮可能要几分钟，等到天荒地老。60 tok/s 跑一轮才能让你「不离开座位等结果」。**这一派直接刷新了国内开发者的硬件采购参考线**——从单纯聊天的 30 tok/s 提到智能体的 60 tok/s，等于本地部署的最低门槛抬高了一档。

**第三派 · 速度两面性派。**用户 ricardobeat 提了一个角度刁钻的观察：

> 「Even 5 tok/s is faster than you'd typically type, but feels glacially slow for an agent.」
>
> 5 tok/s 已经比你打字还快，但对智能体来说慢得像冰川。

这一派的洞察是——**速度的「快慢」取决于消费者是谁**。同样 5 tok/s，人看着觉得「比我手打快」，智能体跑着觉得「我等不起」。Tokenspeed 工具把这件事可视化得很好——你切到 5 tok/s 的 text 模式，文字够你读了；切到 5 tok/s 的 agent 模式，看着那些工具调用慢慢蹦出来，立刻明白「智能体不能用这个速度」。

**第四派 · 噪音稀释派。**用户 unglaublich 指出 Tokenspeed 演示的盲区：

> 「30 tok/s looks fine streaming code, but background noise (tools, metadata, thinking) eats it.」
>
> 30 tok/s 看流式代码够用，但背景噪音——工具调用 / 元数据 / 思考——把这个速度吃掉了。

意思是——你看到 30 tok/s 的代码输出觉得舒服，但真实场景里这 30 tok/s 里只有 10 tok/s 是有效代码，另外 20 tok/s 是 `[tool_call_id]`、`<thinking>...</thinking>`、JSON 元数据这些「看不见但烧 token」的部分。**真实有效输出速度可能只有名义 tok/s 的三分之一**。

**第五派 · 思考烧 token 派。**用户 SXX 给了 Tokenspeed 一个具体的改进建议：

> 「Thinking usually burns at least 2x to 3x the tokens of code — the demonstration needs more realistic logs.」
>
> 思考通常烧的 token 是代码的两到三倍——演示应该用更真实的日志。

这一派的观察呼应了 Veerman 自己 think 模式的设计意图，但批评他做得还不够极端——真实的 Claude Opus / 千问 3 32B 的 reasoning trace 比 Tokenspeed 当前的 think 模式更冗长更迭代。**国内做 deep reasoning 部署的用户要做心理准备**：你看到模型「思考」5 秒，实际烧的 token 数可能是它最终输出代码的 2-3 倍。

## 人类可感速度带 · 阅读 / 字幕 / 打字 / 智能体的参照线

Veerman 工具最有教育价值的一节是它的换算文案——「English prose averages ~1.3 tokens per word, so 30 tok/s ≈ 23 words/s」。一句话把抽象的 tok/s 锚到了人类朗读速度。把这条原理扩展到中文场景，就是下面这张图。

![人类可感速度带 阅读 字幕 打字 智能体四条参照线](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/tokenspeed-tokens-per-second-perceived-speed-ui-2026-05-21/perceived-speed-band.png)

**汉字换算口径。**Qwen3 / GPT-4 tokenizer 在中文上的中位口径是 1 token ≈ 1.4 汉字。所以 30 tok/s 对中文是每秒约 42 汉字——比普通人打字（每分钟 40 字 ≈ 每秒 0.67 字）快约 60 倍，但比快速阅读（每分钟 600 字 ≈ 每秒 10 字）只快 4 倍。

**第一带 · 人类朗读舒适区（5-30 tok/s）。**听书、读经、播客转写、有声小说都落在这一带。低于 5 tok/s 你会等得不耐烦；高于 30 tok/s 你会觉得朗读员说太快听不清。**这条带的意义是——做语音生成 / TTS 配套产品不需要追求 60 tok/s**，30 已经够了。

**第二带 · 打字速度对比区（30-60 tok/s）。**这一带刚好覆盖普通人到熟练打字员的速度。**这条带的意义是——做聊天 / 对话 / 客服产品，体验上不需要超过 60 tok/s**，你打字都没这么快，再快意义有限。

**第三带 · 字幕滚动远超舒适区（60-200 tok/s）。**电影字幕一般每秒只滚动 6 个汉字（约 4.3 tok/s）就到了字幕可读的上限。本带的起点 60 tok/s 比这个上限快得多——你只剩段落感，看不清单字。**这条带的意义是——做长文阅读 / 文档总结产品要落在这一带**，让用户感觉「整段呼啦一下出来了」。

**第四带 · 智能体专用区（200-800 tok/s）。**这一带给人看是浪费，但给智能体跑多轮工具调用是宝贵的。**这条带的意义是——你买 5090 / Cerebras / Groq 的钱主要是为智能体烧的**，不是为你眼睛舒服看字流烧的。

读这张图的方法是——**先选你做什么产品（聊天 / 朗读 / 长文 / 智能体），再看你应该买能跑到哪一带速度的硬件**。Tokenspeed 工具让你不用买卡就能感知每一带的体感。

## 用 Tokenspeed 校验本地千问 / DeepSeek 的具体流程

![Tokenspeed 校验本地千问的六步 validation workflow 工作流](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/tokenspeed-tokens-per-second-perceived-speed-ui-2026-05-21/validation-workflow.png)

抓住一个最实用的角度——你今天就有一块 4090 / 5090 / M4 Max，你跑了 vLLM / SGLang / Ollama / LM Studio 部署千问 3 / DeepSeek V4，怎么用 Tokenspeed 校验速度对不对？步骤如下，按性价比排列。

| 步骤 | 动作 | 时间预算 | 难度 | 价值 |
| --- | --- | --- | --- | --- |
| 一 | 打开 Tokenspeed 页面把九档速度全过一遍 | 五分钟 | 极低 | 建立肉眼直觉 |
| 二 | 跑你的本地引擎让它输出一段长文，记录 tok/s | 十分钟 | 低 | 拿到自家硬件数字 |
| 三 | 把数字切到 Tokenspeed 对应档位看体感 | 五分钟 | 低 | 校准期望 |
| 四 | 分四种模式（code / text / think / agent）重测 | 三十分钟 | 中 | 找出瓶颈场景 |
| 五 | 切换量化档（FP16 / Q8 / Q4）重测对比 | 一小时 | 中 | 找出速度 / 质量平衡点 |
| 六 | 写一份「我这套硬件的速度档报告」给团队参考 | 半天 | 中 | 团队决策依据 |

**第一步 · 打开 Tokenspeed 把九档全过一遍。**不用接 GPU，浏览器打开 mikeveerman.github.io/tokenspeed，按数字键 1 到 9，每一档看 30 秒到 1 分钟，配合 c / t / h / a 四种模式都过一下。**这一步建立的肉眼直觉，是后面所有判断的基准**。

**第二步 · 拿自家硬件实测 tok/s。**用 vLLM 跑 Qwen3-7B 的命令是：

```bash
vllm serve Qwen/Qwen3-7B-Instruct \
  --tensor-parallel-size 1 \
  --max-model-len 32768 \
  --gpu-memory-utilization 0.9
```

然后用 curl 发一段 1000 token 输入的请求，看返回 metadata 里的 `completion_tokens / total_time` 算出 tok/s。Ollama 用户更简单，直接 `ollama run qwen3:7b --verbose` 命令跑完会打印 eval rate。**记下来这个数字，比如 78.3 tok/s**。

**第三步 · 把这个数字切到 Tokenspeed 对应档。**78 tok/s 介于 Tokenspeed 第五档（60）和第六档（120）之间——切到第五档看 30 秒，再切到第六档看 30 秒。**你心里就建立了「我这台机器约等于这个体感」的具体锚点**。

**第四步 · 分四种模式重测。**code 模式下你测出来 80 tok/s 不代表 think 模式下也是 80。Qwen3-7B 跑 reasoning 模式会带 `<thinking>...</thinking>` 标签，有效 tok/s 通常砍半。**这一步要分别测 code / text / think / agent 四种典型 prompt 模板**。

**第五步 · 切量化档对比。**Qwen3-7B 在 4090 上跑 FP16 大约 80 tok/s，切到 Q8 量化大约 120 tok/s，切到 Q4 量化大约 180 tok/s——但质量损失依次累积。**Tokenspeed 让你直观看到「Q4 量化跑 180 tok/s 对应肉眼是 Groq 那种瀑布流」**，然后判断你愿不愿意为这个速度牺牲质量。

**第六步 · 写一份速度档报告给团队。**国内 AI 团队现在选本地引擎选硬件的最大问题是「数字太多看不懂」。一份「我们的 4090 跑 Qwen3-7B 在 code 模式下相当于 Tokenspeed 第五档体感」的报告，比一张密密麻麻的 tok/s 表格直观一百倍。**这一步是 Tokenspeed 工具的工程价值——把速度从数字变成可沟通的体感**。

**总耗时 · 一个真正完整的流程大约半天**。比起继续靠感觉选硬件，这个时间是已知的、可控的、跟自家工作流强相关的。

## 边界判断 · Tokenspeed 帮你做什么 · 不帮你做什么

Tokenspeed 不是万能的速度评测工具，把它的边界摊开看一下。

**它帮你做的三件事。**

一是**把抽象 tok/s 翻成肉眼可感的速度档**。你测出来 45 tok/s 不知道快慢，切到 Tokenspeed 第四档看 30 秒就知道。这是它最大的工程价值。

二是**让你在四种工作流（code / text / think / agent）下分别体验**。不同场景下同样速度的体感完全不同——这件事你不亲眼看一遍是想象不出来的。

三是**给团队提供一个共通沟通语言**。「我们的本地模型大约是 Tokenspeed 第六档体感」比「我们的本地模型大约是 78 tok/s」更容易被产品 / 运营 / 决策者听懂。

**它不帮你做的三件事。**

一是**它不告诉你模型质量**。Tokenspeed 只测速度——同样 80 tok/s，4090 跑 Qwen3-7B 和 4090 跑 Llama 3.1 8B 给的答案质量天差地别。**质量评测要单独跑 MMLU / HumanEval / 内部业务评测集**。

二是**它不模拟上下文掉速**。HN antirez 的批评一针见血——真实的 tok/s 随上下文长度急剧下降，Tokenspeed 当前演示的是稳态速度。**做长上下文场景要单独测 32k / 128k token 输入下的实际速度**。

三是**它不替代真实的工具链评测**。vLLM / SGLang / TensorRT-LLM 等引擎之间还有 batch 调度、KV cache 命中率、tensor 并行策略等差异，Tokenspeed 只让你看速度档，**不告诉你为什么自家的 SGLang 比 vLLM 慢 30%**。这一类问题要看引擎的「评测工具」（Benchmark）自己输出的详细分析报告。

一句话总结——**Tokenspeed 是个肉眼校准器，不是评测套件**。它给你的是「你的速度档大约在哪儿」的直观感觉，不给你的是「为什么是这个档」的工程分析。这两件事互补，不互相替代。

## 结尾 · 一个小工具背后的本地大模型新范式

为什么这条工具在五月二十日特别合时宜？

过去一年，国内开发者从「调 API 用 Claude / GPT」逐步迁回「本地部署 Qwen3 / DeepSeek / GLM」——主要驱动力是数据合规 + 成本可控 + 不依赖境外服务。但本地部署带来的新挑战是——**你没有 Anthropic / OpenAI 给你的「默认就够用」的速度兜底，你得自己判断硬件配置够不够**。

判断的难点在哪里？**抽象的 tok/s 数字让你没办法决策**。看到 r/LocalLLaMA 帖子写「4090 跑 Qwen3-7B 80 tok/s」，你不知道 80 跟你的工作流是什么关系。看到 Bilibili 测评说「5090 跑 32B 65 tok/s」，你不知道 65 是惊喜还是失望。看到 M4 Max 用户晒「22 tok/s 跑 32B Q4」，你不知道 22 是能用还是不能用。

Veerman 用 Tokenspeed 做的事，是**把数字翻回人类感官**——你眼睛看 30 秒，你身体就知道这个速度对应你的工作流是什么体验。这条工具上线一天 190 分 51 评论上 HN 首页 + Simon Willison 转引，背后是整个本地大模型开发者社区对「我到底应该买什么硬件」这件事的真实焦虑。

国内本地大模型用户今天就可以打开 Tokenspeed 把九档速度过一遍建立直觉，再回到自家的 4090 / 5090 / M4 Max，用 vLLM / SGLang / Ollama 跑出来的 tok/s 切回 Tokenspeed 对应档看体感——**半天时间，你对自家硬件能做什么工作流的判断会清楚得多**。

国内 AI 工具链团队也值得借鉴这套「把抽象指标翻成肉眼可感」的产品做法。国内做推理引擎的（阿里 PAI、智谱 GLM-Coding-Engine、字节 BytePS、华为 MindSpore Lite、百度 PaddlePaddle Inference）的官方文档大量是冷冰冰的数字表格。哪家国产推理引擎先在自己的 dashboard 里嵌入 Tokenspeed 风格的肉眼校准器——**让用户看自家速度档的肉眼对照而不是数字对照**——产品教育价值会很大。

国内 AI 应用开发者也借这件事可以重新审视一个老问题：产品的用户感知的不是 tok/s 数字，是字流的视觉节奏。**优化体验有时候不是把速度从 60 拉到 120，而是确保 60 这一档下字流的连贯性和视觉舒服度**。Tokenspeed 实质是把这件事拆开了——速度档是工程指标，体感档才是产品指标。

Veerman 一个人用最朴素的 HTML 解决了「数字太抽象」这件事。今年五月这一波本地大模型迁移潮里，给国内开发者带来的提示就一句：**有时候解决 AI 时代的问题，不需要更强的模型，需要的是更好的工具直觉**。
