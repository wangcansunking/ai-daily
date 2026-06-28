---
title: "Claude 进 Excel：子智能体互通，账单记你头上"
date: "2026-06-09"
weekday: "星期二"
slug: "claude-in-excel-office-agent-subagents-2026-06-09"
cover: "claude-in-excel-office-agent-subagents-2026-06-09.png"
description: "Claude 进了 Word、Excel、PowerPoint（已正式上线）和 Outlook（公测），背后不是一个助手，而是每个应用一个专属子智能体、跨文件互相通信——Word 的智能体能主动喊 Excel 的智能体取数打包回传。但多数人没注意：在 Office 里用 Claude，用量计入你自己的 Claude 账号额度，需要 Pro 或 Max 付费档。同一张表，选 Claude 还是 Copilot，成本账完全不同。"
category: "AI 工具 / Office 智能体 / 计费"
tags: ["Claude", "Excel", "Microsoft Office", "子智能体", "Agent Mode", "GitHub Copilot", "Sonnet 4.6", "跨文件上下文", "计费"]
---

# Claude 进 Excel：子智能体互通，账单记你头上

![Claude 进入 Microsoft Office，每个应用一个专属子智能体并跨文件通信](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/claude-in-excel-office-agent-subagents-2026-06-09/claude-in-excel-office-agent-subagents-2026-06-09.png)

Claude 现在能在 Excel 里帮你写公式、清数据、搭分析了。这件事本身不算意外，真正有意思的是背后的结构：它不是一个统一的助手挂在侧边栏，而是 Word 一个智能体、Excel 一个智能体、PowerPoint 一个智能体，彼此还能对话——你在 Word 里让它把一份财务模型的数据拉进文档，Word 的智能体会主动去找 Excel 的智能体，让对方读表、打包、回传。

但这一篇真正想让你记住的，是另一个多数人没留意的反差：在 Office 里用 Claude，用量算在你自己的 Claude 账号额度上，要 Pro 或 Max 付费档才用得了，按官方说法和你直接在 claude.ai 上聊天共用同一套额度限制。这和 GitHub Copilot 自 6 月 1 日改成按用量扣 AI Credits（AI 额度）的算法是两套完全不同的成本账——同一张表，选谁，月底账单长得不一样。

结构、计费、怎么选，挨个说。

## 先把能查到的几个事实摆清楚

![The New Stack 对 Claude 进入 Microsoft Office 的报道](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/claude-in-excel-office-agent-subagents-2026-06-09/claude-office-source-thenewstack.png)

时间线分两条，别混在一起：

- **Claude 的 Office 插件（add-in）**：Word、Excel、PowerPoint 三个已经正式上线（GA），Outlook 还在公测。这是 Anthropic 自己做的插件，挂进 Microsoft 365 里用。
- **Excel 的智能体模式（Agent Mode）选 Claude**：6 月 8 日 Microsoft Build 2026 上，微软宣布 Excel 自家的智能体模式可以选 Claude 作为底层模型——这条走的是微软的计费通道，按 token 用量在 Azure 那边结算。

两者都让 Claude 出现在 Excel 里，但入口、计费、归属完全不同。本文主角是前者：Anthropic 的官方 Office 插件。

几个关键数字：

- Excel 的全球用户量，按报道口径是约 7.5 亿人。Claude 这次是进到「地球上用得最多的分析工具」里。
- 四个插件——Word、Excel、PowerPoint、Outlook——对每个 Microsoft 365 用户共享同一条对话线索。
- 推荐默认模型是 Sonnet 4.6，重任务才换更重的 Opus。

这三条意味着：Claude 这次不是又多一个聊天框，而是钻进了几亿人每天打开的那张表里，且四个应用之间是连着的。

## 每个应用一个子智能体，它们会互相喊话

![Office 各应用子智能体跨文件通信示意](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/claude-in-excel-office-agent-subagents-2026-06-09/claude-office-chart-subagent-flow.png)

这套结构最值得讲的一点：**不是一个大模型在所有应用里来回切，而是每个应用有自己的智能体，它们之间能直接通信。**

最直白的例子，是你在 Word 里要一份带数据的文档。按 MindStudio 的拆解，过程是这样的：「Word 智能体去找 Excel 智能体。Excel 智能体读取这张电子表格，把数据打包，再发回来。」换句话说，一次请求里其实是「一个 Word 智能体和一个 Excel 智能体互相通信、传完数据，再返回一个合并好的结果」。

这和「一个模型自己同时翻 Word 和 Excel」有本质区别：

- 每个智能体可以针对自己那个应用做专门优化——Excel 的懂公式和敏感性分析，PowerPoint 的懂母版、版式、字体配色，Word 的懂把改动落成修订痕迹。
- 专门化在能协作时才放大价值：各管一摊，又能互相取数，比一个通才模型样样都会一点更顺手。

Anthropic 没有把这套多智能体结构当成主打卖点大讲。但对开发者来说，理解它有实际意义：你在侧边栏里对话的，其实是一组会彼此协调的智能体，而不是单一助手。

这也解释了为什么各应用里的行为会带各自的脾气。Word 智能体的改动一律落成修订痕迹，还会在批注里回应你的问题、解释自己改了什么、为什么这么改。PowerPoint 智能体在你现有模板里建页面，尊重母版、版式、字体和配色，不会自作主张换一套风格。Excel 智能体则擅长搭财务模型、审公式、跑敏感性分析，并且在更新假设时尽量不破坏已有公式。每个智能体都按它那个应用的工作习惯来，合在一起才不别扭。

## 跨文件上下文：那个叫「Work across files」的开关

![MindStudio 对 Claude 进 Office 子智能体跨文件架构的报道页](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/claude-in-excel-office-agent-subagents-2026-06-09/claude-office-source-subagents.png)

让这组智能体真正连起来的，是一个叫「Work across files」（跨文件协作）的开关。

按 MindStudio 的说法：「打开这个开关，Claude 就能同时读取其他已打开 Office 应用里的上下文。」效果是上下文在你整个 Office 会话期间保持——「上下文在你的整个 Office 会话里持续……这些智能体共享状态。」

对照一个常见场景就懂它省了什么：

- 你在 Outlook 里收到一封带要求的邮件，想据此起草一份 Word 文档。开了跨文件，Word 这边「已经知道那封邮件说了什么」，不用你再复制粘贴一遍背景。
- 你在 Excel 里改了财务模型的几个假设，再打开引用这套模型的 PowerPoint，Claude 知道你刚改了哪些假设。

把上下文在 Outlook、Word、Excel 之间端着走，不用每换一个应用就重新交代一遍——这正是「一个 AI 助手」和「一个真能融进你工作方式的 AI」之间的差别。

## 计费账：用量记你自己的 Claude 头上

**在 Office 里用 Claude，账记在你自己的 Claude 账号上。** Anthropic 官方帮助文档写得很直接：「你使用 Claude for Excel 的活动，关联到你已有的 Claude 账号，并受同一套用量限制约束。」也就是说，你在 Excel 侧边栏里跑掉的量，和你直接在 claude.ai 上聊天，吃的是同一个额度池。

付费门槛也明确：要 Pro、Max、Team 或 Enterprise 档。免费账号可以装上插件，但激活不了。MindStudio 给个人用户的总结更干脆：「你需要付费档——Pro 或 Max——免费档用不了。」

把它和 GitHub Copilot 摆一起，差别就出来了：

| 维度 | Office 里用 Claude | GitHub Copilot |
|---|---|---|
| 计费归属 | 计入你自己的 Claude 账号额度 | 走 Copilot 套餐里的 AI Credits（AI 额度） |
| 付费门槛 | Claude Pro / Max / Team / Enterprise | Copilot Pro+ / Business / Enterprise |
| 算量方式 | 与 claude.ai 共用同一套用量限制 | 按 token 用量（含输入、输出、缓存）扣额度 |
| 额度形态 | 现有 Claude 订阅额度 | 每月含一笔 AI 额度，超出可加购 |
| 关键日期 | Word/Excel/PowerPoint 已正式上线，Outlook 公测 | 6 月 1 日起所有 Copilot 套餐改按用量计费 |

关键在第一行的计费归属。Copilot 自 6 月 1 日起，每个套餐含一笔每月 AI 额度，按 token 用量（含输入、输出、缓存 token）扣，扣完可加购；代码补全和下一步编辑建议仍然包含在套餐里、不扣额度。而 Claude 这边，不另起一个计费层，直接吃你订阅本来的那份量。

一句话定性：**Copilot 是「你这个月写代码、聊天、跑智能体烧了多少 token，就从这笔 AI 额度里扣多少」；Claude in Office 是「你在 Excel 里干的活，和你在 claude.ai 上干的活，共用同一个额度天花板」。** 哪种更划算，取决于你本来就是哪一边的重度用户。

## 怎么在 Excel 里把 Claude 开起来

![Claude 进入 Microsoft Office 的官方公告页](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/claude-in-excel-office-agent-subagents-2026-06-09/claude-office-source-announcement.png)

按 Anthropic 帮助文档，个人用户启用 Claude for Excel 的步骤很短：

1. 到 Microsoft Marketplace（微软应用市场）上找到 Claude for Microsoft 365 的条目；
2. 点「立即获取」（Get it now）安装；
3. 打开 Excel，激活这个插件，用你的 Claude 账号登录授权。

几个前提条件值得先确认：

- **账号档位**：Pro、Max、Team 或 Enterprise 才能正常激活，免费账号装得上但用不了。
- **运行环境**：网页版、Windows、Mac 或 iPad 版 Excel 都支持，跑在 Microsoft 365 订阅上，且 Excel 版本号要够新。
- **登录归属**：首次用要用 Claude 账号登录——这一步也正好对应上面那条「用量记你自己头上」。

装好之后，Claude 在侧边栏里能做的，按官方和报道口径，是写公式和解释公式、清洗和转换乱数据、生成文字分析、搭工作流，而且改动尽量不破坏已有公式。

## 什么时候用 Claude，什么时候用 Copilot

把账算到这一步，「选哪个」就不再是模型谁更强的问题，而是你的额度本来记在哪边：

- **本来就是 Claude Pro / Max 重度用户** → 在 Office 里用 Claude 顺理成章。反正吃的是你已经在付的那份额度，跨文件那套子智能体协作还顺手，不用再为 Office 单独开一笔预算。
- **团队已经在 GitHub 上吃 Copilot、工作重心在代码仓库** → Copilot 的 AI 额度走的是另一套账，写代码、跑智能体编程、做代码审查这些 token 大户场景，量都从 Copilot 那边扣，和你的 Claude 额度互不打架。
- **既要在 Excel 里做重度分析、又要在 IDE 里写代码** → 这才是要认真算账的人。两套额度池分开记，意味着你可以让分析的量走 Claude、代码的量走 Copilot，把两边的免费额度都用足，而不是把所有压力压在一个池子里。
- **只是偶尔让 Excel 帮忙写个公式** → 哪个顺手用哪个，月度量根本碰不到任何一边的天花板，不用纠结。

补一句实操层面的提醒：因为 Office 里的量和 claude.ai 共用额度，如果你平时已经把 Claude 用得很满，再叠上一整天在 Excel 里跑分析，可能会更快撞到限速，这时候反而值得把一部分重活分给走独立计费的 Copilot。两套额度分开记，本身就留出了错峰的余地。

回到开头那个反差：Claude 进 Office，表面是「又多一个能用的助手」，往里看是「每个应用一个会互相喊话的子智能体」，再往里看是「这笔用量到底记在谁头上」。结构决定了它好不好用，计费决定了它对你划不划算——这两件事，比「Claude 比 Copilot 强多少」更值得你先想清楚。

对国内开发者来说，这也是个好观察窗口：Office 这种几亿人共用的工作场景，正在变成多个 AI 模型同台、各自带自己计费逻辑的赛场。把每一家的额度怎么算、归属在哪摸清楚，本身就是这一代开发者越来越值钱的一项基本功。
