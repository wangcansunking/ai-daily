---
title: "ChatGPT 和 Codex 合并：照着 Claude 重排"
slug: openai-chatgpt-codex-merge-agentic-2026-06-03
date: 2026-06-03
weekday: 星期三
category: OpenAI / 智能体平台 / 行业观察
cover: openai-chatgpt-codex-merge-agentic-2026-06-03.png
description: "5 月 16 日，OpenAI 内部备忘录宣布重组：把 ChatGPT、Codex、开发者 API 并进一个产品组织，联合创始人格雷格·布罗克曼统管，目标是『一个 agentic 平台』，把 ChatGPT 和 Codex 合并成统一的智能体体验，内部叫 super app。这篇的判断是：OpenAI 这步本质是在照着 Anthropic 的 Claude 打法重排——Claude 早就是『一个模型族 + 对话助手 + Claude Code 编程智能体』的统一系统，ChatGPT 现在补上这一课。"
---
# ChatGPT 和 Codex 合并：照着 Claude 重排

![OpenAI 把 ChatGPT 与 Codex 合并成统一的 agentic 平台，由布罗克曼统管产品战略](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-03/openai-chatgpt-codex-merge-agentic-2026-06-03/openai-chatgpt-codex-merge-agentic-2026-06-03.png)

5 月 16 日，OpenAI 给员工发了一份内部备忘录：联合创始人、总裁格雷格·布罗克曼（Greg Brockman）永久接管全部产品战略，把 ChatGPT、Codex 和开发者 API 并进同一个产品组织。备忘录里的原话是，OpenAI 要「投入一个统一的 agentic 平台，把 ChatGPT 和 Codex 合并成面向所有人的统一智能体体验」。

这件事被不少人当成一次普通的内部架构调整。但放进 AI 这两年的竞争里看，它的含义要重得多。

**OpenAI 把对话产品（ChatGPT）和编程智能体（Codex）合并成一个 agentic 平台，本质是在照着 Anthropic 的 Claude 打法重排自己——Claude 早就是『一个模型族，既是对话助手、又是 Claude Code 编程智能体』的统一系统。OpenAI 过去把这两摊分开做，现在要把它们合回一个『会干活』的平台。换句话说，ChatGPT 正在被改造成第二个 Claude。** 这篇拆开讲：合并了什么、为什么说像 Claude、以及对开发者意味着什么。

先看这次到底合并了什么。

## 合并了什么：三摊并一摊，布罗克曼统管

![OpenAI 把 ChatGPT、Codex、开发者 API 并进一个产品组织，目标是单一 agentic 平台](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-03/openai-chatgpt-codex-merge-agentic-2026-06-03/source-openai-reorg-techtimes.png)

*来源：TechTimes 关于 OpenAI 重组、合并 ChatGPT 与 Codex 的报道 techtimes.com*

这次重组的动作很直接，列出来是三件事：

- **三摊并一摊**：过去 ChatGPT（面向大众的对话产品）、Codex（面向开发者的编程智能体）、开发者 API 是相对独立的三块，现在并进同一个产品组织。
- **布罗克曼统管**：由联合创始人布罗克曼永久执掌全部产品战略。这次重组把今年 4 月初就开始的一个临时安排正式定下来——当时负责 AGI 落地的高管菲吉·西莫（Fidji Simo）因病休假，产品线需要一个稳定的统一负责人。
- **目标是一个平台**：内部把这个合并后的产品称作 agentic「super app」（超级应用），强调它不是聊天框，而是一个能替你把任务办完的智能体平台。

合并不会一夜完成。按 OpenAI 的说法，它会**分步走**：先让 Codex 从单纯写代码，扩展到编程之外的更多生产力任务；再把 ChatGPT 和 OpenAI 的研究工具 Atlas 逐步并进来。

把这套动作连起来，方向就一个：OpenAI 要从「卖一个聊天模型」转向「做一个会干活的 agentic 平台」。而这条路，另一家公司两年前就在走了。

## 为什么说像 Claude：统一 agentic 系统，Anthropic 先做了

![Codex 是 OpenAI 的编程智能体，合并后将与 ChatGPT 并入同一个 agentic 平台](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-03/openai-chatgpt-codex-merge-agentic-2026-06-03/source-openai-codex-repo.png)

*来源：OpenAI Codex 官方仓库 github.com/openai/codex*

要看懂 OpenAI 这步，得先看 Claude 长什么样。

过去两年，Anthropic 把 Claude 做成了一个**统一的智能体系统**，而不是几个割裂的产品：

- 同一个 Claude 模型族，既是你打开就能聊的对话助手，又是终端里能自己读代码、改文件、跑命令的 Claude Code 编程智能体；
- 对话和编程不是两个 app，而是同一套模型能力在不同场景下的两张面孔；
- 整个产品的设计重心，是「让模型去干活」，不是「让模型陪你聊」。

把 OpenAI 这次合并对照着看，几乎是同一张图：

| 维度 | Anthropic 的 Claude | OpenAI 合并后的方向 |
|---|---|---|
| 对话 + 编程 | 同一模型族，Claude 助手 + Claude Code 一体 | ChatGPT + Codex 合并成一个平台 |
| 产品重心 | 让模型干活（agentic） | 单一 agentic 平台 / super app |
| 模型路线 | 围绕 agentic 能力训练 | GPT-5.5 首次按「智能体优先」重训基座 |

最后一行尤其关键：当前 Codex 跑的是 **GPT-5.5**——OpenAI 自 GPT-4.5 以来第一个完全重训的基座模型，4 月 23 日发布，训练时就明确按「智能体优先」来做。模型层、产品层一起朝 agentic 转，**这正是 Anthropic 用 Claude 趟出来的路**。所以说 ChatGPT 在变成「第二个 Claude」，不是调侃，是对路线的准确描述。

那么这套重排，对天天用这些工具的开发者意味着什么？

## 对开发者：一个账号，一个会干活的入口

![Codex 已进入 ChatGPT 移动 app，当前跑在 GPT-5.5、每周约 400 万开发者活跃](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-03/openai-chatgpt-codex-merge-agentic-2026-06-03/source-openai-codex-app.png)

*来源：TechCrunch 关于 Codex 进入 ChatGPT 移动 app 的报道 techcrunch.com*

合并对开发者的影响，落到实处是几条：

- **一个账号打通多个入口**：Codex 现在已经能在多个场景里用，背后用同一个 ChatGPT 账号串起来。5 月 14 日，Codex 进了 ChatGPT 的移动 app（iOS 和安卓都有），手机上也能让它干活。
- **从「写代码」扩到「办事情」**：合并后 Codex 会先长出编程之外的生产力能力，意味着同一个智能体入口能办的活会越来越多。
- **规模已经不小**：当前每周约有 400 万开发者在用 Codex。这个体量决定了 OpenAI 的产品决策会实打实影响很多人的日常工作流。

需要说清楚的是，这次重组背后也有商业节奏——OpenAI 正准备在今年第四季度上市。但驱动这次产品合并的，是「做一个统一 agentic 平台」这个技术与产品判断本身，估值和上市只是背景。对开发者来说，真正要关注的不是估值数字，而是手里这个工具正在从「聊天框」变成「会干活的平台」。

## 收尾：两家都走向同一个形态

把这件事看完，可以收成一句：**OpenAI 把 ChatGPT 和 Codex 合并成一个 agentic 平台，是在照着 Anthropic 用 Claude 趟出来的统一智能体路线重排自己——对话与编程合一、模型按智能体优先重训、一个入口替你把活干完。**

两家最强的 AI 公司，从不同起点出发，正走向同一个产品形态：不再是陪你聊天的模型，而是替你干活的智能体平台。这对用工具的人是好事——它意味着无论你站在哪一边，手里的工具都在朝「更能办事」的方向走。看清这个共同方向，比纠结谁学谁更有用；接下来值得做的，是把这些越来越能干活的智能体，真正用进自己每天的活里。
