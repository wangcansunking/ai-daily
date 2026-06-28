---
title: "SkyClaw：把百万上下文 Agent 模型的调用价压到一半"
slug: skyclaw-million-token-agent-model-2026-06-01
date: 2026-06-01
cover: skyclaw-million-token-agent-model-2026-06-01.png
tags: [智能体, 百万上下文, 昆仑万维, 天工, AI Coding, OpenAI兼容接口, 国产模型]
description: "昆仑万维天工 5/26 发布 SkyClaw-v1.0 与轻量版 lite，主打百万词元上下文 + 深度适配 OpenClaw / Claude Code / Codex 等智能体框架。六项智能体基准上稳压 MiniMax 2.7、DeepSeek V4-Flash、千问 3.6 27B，在 OpenClaw 任务上逼近 DeepSeek V4-Pro、Claude Opus 4.6；旗舰版输出 4 元/百万词元，约为 MiniMax 2.7 的一半、DeepSeek V4-Pro 的 1/6。接口走 OpenAI 兼容格式，换个 base_url 就能接进自己的 Agent 工作流。"
weekday: "星期一"
category: "智能体模型 / AI Coding / 国产开源"
track: domestic-hot
---
# SkyClaw：把百万上下文 Agent 模型的调用价压到一半

![SkyClaw-v1.0：昆仑万维天工发布的百万上下文智能体模型，一卷百万词元长卷接到各家智能体客户端](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/skyclaw-million-token-agent-model-2026-06-01/skyclaw-million-token-agent-model-2026-06-01.png)

输出每百万词元 4 元。这是昆仑万维天工 5 月 26 日发布的 SkyClaw-v1.0 给出的价格——同样跑智能体任务，MiniMax-M2.7 输出价 8.4 元，DeepSeek V4-Pro 是 24 元。也就是说，一个性能在多数智能体基准上稳压 MiniMax 2.7、在 OpenClaw 任务上逼近 DeepSeek V4-Pro 和 Claude Opus 4.6 的国产模型，把调用成本直接打到了竞品的一半甚至更低。

这件事对正在搭智能体（Agent，能自己规划、调工具、多轮完成任务的 AI 系统）的国内开发者来说，分量不在“又一个新模型”，而在一个具体的算式变了：原来因为单次调用太贵、只能拿来做演示的高频智能体场景，现在的单价低到可以放进真实业务里反复跑。

本文想讲清楚三件事：**SkyClaw 这个百万上下文的智能体模型到底强在哪、和 DeepSeek V4 / 千问 3.6 / Claude Opus 摆在一起怎么比、以及国内开发者怎么用一个 OpenAI 兼容端点把它接进自己已经在用的 Claude Code、Codex 或自研工作流。** 结论先放这儿：如果你做的是要高频调用、对单价敏感的智能体或 AI 编程类应用，SkyClaw 现在是国产里一个性价比很突出的选项；接入几乎零成本，换个 base_url 和模型名就能试。

## SkyClaw 是什么：一个为“真实工作流”调出来的智能体底座

一句话：**SkyClaw-v1.0 是昆仑万维（300418.SZ）旗下天工团队（Skywork）发布的高性能智能体模型，支持百万词元上下文，专门为复杂工具调用、多轮任务执行、代码生成、文件编辑这类真实智能体场景优化。** 它同时有一个轻量版 SkyClaw-v1.0-lite，主打更快、更省。

先把几个事实摆清楚：

- **发布方**是昆仑万维天工，开源仓库在 `SkyworkAI/skyclaw`，6 月 1 日凌晨核对约 83 stars、MIT 许可证，仓库 5 月 19 日建立
- **上下文长度**是 100 万词元（1M tokens），这是它放进真实工作流的底气——多轮智能体任务里堆积的工具返回、文件内容、对话历史很容易撑爆窗口，百万级别意味着一个复杂任务可以从头跑到尾不丢上下文
- **训练路线**官方写得很清楚：先做大规模中期训练（mid-train），再用高质量合成任务做监督微调（SFT），最后端到端强化学习（RL）打磨。其中强化学习直接在 OpenClaw 风格的智能体环境里做，筛选轨迹时既看最终答案对不对，也看中间每一步动作的质量
- **接口**走 OpenAI 兼容格式，支持流式输出、工具调用、多轮对话，模型名是 `skywork-ai/skyclaw-v1`（旗舰）和 `skywork-ai/skyclaw-v1-lite`（轻量）
- **可直接用**：模型 5 月 22 日已接入天工 Skywork，登录 tiangong.cn 打开即用，无需自己配智能体环境；即日起开放 2 至 4 周免费试用

![SkyClaw 生成的交互式机票旅行应用界面截图，展示模型一次性产出可运行前端应用的能力](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/skyclaw-million-token-agent-model-2026-06-01/source-skyclaw-flight-travel-demo-2026-06-01.png)
<small>来源：天工 SkyClaw 官方发布页交互式应用样例（机票旅行）</small>

官方发布页放了一组它直接生成的可运行界面——机票预订、社交信息流、小游戏（2048、俄罗斯方块、象棋），从产品级前端到交互逻辑一次成型，这正是“交互式应用构建”这项能力的直观样子。

这里值得点一句它的产品定位。官方原话是，SkyClaw 承担的是底层智能体模型能力升级——通过百万上下文、智能体强化学习（Agentic RL）、复杂工具调用优化和高性价比接口，把智能体从“能演示”推进到“能高频调用、能真实交付”。这句话的潜台词，正是过去一年很多团队的真实痛点：智能体在演示里跑得漂亮，一旦上量，要么贵到用不起，要么长任务跑着跑着上下文崩了。SkyClaw 想解的就是这两个问题。

## 六项基准横评：稳压同价位，逼近大模型

光说“强”没意义，看官方放出的基准对位。SkyClaw 选的六项评测都是智能体方向的硬指标，对照组是 DeepSeek V4-Flash、MiniMax-M2.7、千问 3.6 27B（同价位段）以及 DeepSeek V4-Pro（更大规模闭源段）。

![SkyClaw-v1.0 与 lite 版在六项智能体基准上对比 DeepSeek V4-Flash、MiniMax 2.7、千问 3.6 27B、DeepSeek V4-Pro 的官方成绩图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/skyclaw-million-token-agent-model-2026-06-01/source-skyclaw-benchmark-chart-2026-06-01.png)
<small>来源：天工 SkyClaw 官方发布页基准对比图</small>

把官方图里的关键分数整理成表（分数越高越好）：

| 基准 | 测什么 | SkyClaw-v1.0 | DeepSeek V4-Flash | MiniMax-M2.7 | 千问 3.6 27B | DeepSeek V4-Pro |
| --- | --- | :---: | :---: | :---: | :---: | :---: |
| PinchBench-v2 | OpenClaw 智能体任务 | **87.2** | 84.6 | 85.9 | 86.4 | 87.7 |
| Claw-Eval Pass³ | 智能体工作流稳定性 | **59.7** | 55.6 | 48.7 | 56.5 | 59.8 |
| Claw-Eval Avg | 智能体工作流均分 | **74.2** | 73.2 | 72.6 | 73.5 | 77.2 |
| Skywork-Claw-Bench | 自建智能体评测 | **62.9** | 57.1 | 55.6 | 60.5 | 63.4 |
| TAU3-Bench | 交互式工具调用 | **71.5** | 69.1 | 68.1 | 69.5 | 71.7 |
| Terminal-Bench 2.0 | 终端编程智能体 | **51.7** | 36.4 | 46.1 | 46.5 | 56.0 |

几个能直接读出来的结论：

- **在同价位段（对比 V4-Flash / MiniMax 2.7 / 千问 3.6 27B），SkyClaw-v1.0 六项全部领先**。尤其 Claw-Eval Pass³（连测三次都得过，考的是稳定性，不是侥幸一次过）上 59.7，把 MiniMax 2.7 的 48.7 甩开一大截；Terminal-Bench 2.0（终端里的编程智能体）51.7 对 DeepSeek V4-Flash 的 36.4，差距也很明显
- **对比规模更大的 DeepSeek V4-Pro，SkyClaw 不是被碾压，而是贴得很近**。PinchBench 87.2 对 87.7、Claw-Eval Pass³ 59.7 对 59.8、TAU3-Bench 71.5 对 71.7，几项几乎咬住；只在 Claw-Eval 均分（74.2 对 77.2）和 Terminal-Bench（51.7 对 56.0）上还有一段
- **轻量版 lite 也没掉队**，官方口径是 lite 在多数基准上仍优于 MiniMax 2.7，这意味着哪怕你为了省成本选轻量版，拿到的也不是阉割玩具

这里要给个客观提醒：PinchBench、Claw-Eval、Skywork-Claw-Bench 这套评测里，Skywork-Claw-Bench 是天工自建的，主场作战难免占一点便宜；真实选型还得拿自己的任务实测。但即便把自建那一项的分数打个折扣，剩下五项里 TAU3-Bench、Terminal-Bench 2.0 都是业界通用的智能体 / 编程评测，SkyClaw 在同价位段的领先是成立的。

## 定价才是杀手锏：旗舰版只要竞品一半

如果说基准是“够用”，那定价就是 SkyClaw 真正的差异点。把官方价目表摆出来（单位：元 / 百万词元）：

![SkyClaw API 定价与 DeepSeek V4、MiniMax 2.7 的输入输出价对位柱状图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/skyclaw-million-token-agent-model-2026-06-01/skyclaw-pricing-compare-2026-06-01.png)

| 模型 | 输入 | 输出 | 缓存读 | 缓存写 |
| --- | :---: | :---: | :---: | :---: |
| **SkyClaw-v1.0** | 0.5 | 4 | 0.2 | 1.5 |
| **SkyClaw-v1.0-lite** | 0.3 | 2 | 0.12 | 0.9 |
| DeepSeek V4-Flash | 1 | 2 | 0.02 | — |
| DeepSeek V4-Pro | 12 | 24 | 0.1 | — |
| MiniMax-M2.7 | 2.1 | 8.4 | 0.42 | 2.625 |

读这张表，差距是数量级的：

- **SkyClaw-v1.0 输出 4 元，是 MiniMax-M2.7（8.4 元）的约一半，是 DeepSeek V4-Pro（24 元）的 1/6**。考虑到前面基准里 SkyClaw 和 V4-Pro 在多项上几乎咬住，这个单价对比相当有冲击力
- **输入价 0.5 元更狠**——智能体任务里输入往往是大头（工具返回、长文件、多轮历史一遍遍喂进去），输入便宜对真实账单的影响比输出更直接
- **lite 版输入 0.3 元、输出 2 元**，再低一档，专为高吞吐、成本敏感的批量场景准备
- **缓存价也单列了**：缓存读 0.2 元，对那种系统提示词长期固定、反复命中的智能体应用，实际成本还能再压

为什么定价对智能体这么关键？因为智能体和聊天不一样。一次聊天问答可能就几千词元，一个真实的智能体任务——比如让它读完一个代码仓库、规划改动、逐个文件编辑、跑测试、根据报错再改——一来一回几十轮，轻松吃掉几十万词元。单价差一倍，月底账单就差一倍。这也是官方反复强调“从可演示推进到可高频调用”的底层逻辑：成本不降下来，智能体就只能停在演示阶段。

需要说明的是，发布初期 SkyClaw-v1.0 与 lite 都开放了 2 至 4 周限时免费试用，开发者注册账号拿到 Key 就能零成本先跑一轮真实任务，再决定要不要长期用。

## 怎么接进自己的工作流：一个 OpenAI 兼容端点的事

对国内开发者最有价值的部分在这里。SkyClaw 本身不是一个带界面的产品，它是个模型——真正的用法是把它接到你已经在用的智能体客户端里。官方明确列了适配范围：OpenClaw、Hermes、Nanobot 这类通用智能体环境，以及 Claude Code、Codex 这类代码智能体框架。

![SkyClaw 通过 OpenAI 兼容端点接入 OpenClaw、Hermes、Nanobot、Claude Code、Codex 与自研工作流的接入路线图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/skyclaw-million-token-agent-model-2026-06-01/skyclaw-agent-routing-2026-06-01.png)

接入的关键，是它走 **OpenAI 兼容格式的接口**。这意味着任何已经支持配置 OpenAI 风格 base_url 的工具，理论上都能把后端模型换成 SkyClaw，改动只有三处：接口地址、API Key、模型名。

- **接口地址**：`https://api.apifree.ai/v1/chat/completions`
- **模型名**：旗舰用 `skywork-ai/skyclaw-v1`，轻量用 `skywork-ai/skyclaw-v1-lite`
- **能力**：支持流式输出、工具调用（function/tool calling）、多轮对话，这三样正是智能体框架运转的硬性前提

落到几种典型场景，接法各有差别：

- **接 Claude Code / Codex 这类代码智能体**：这两家的核心是让模型规划文件改动、编辑、跑测试、迭代。SkyClaw 在 Terminal-Bench 2.0（终端编程智能体）上 51.7 的成绩说明它能干这类活；接入后把它当作后端推理模型，让客户端负责工具编排即可。官方对应用构建、代码任务建议优先用旗舰版
- **接 OpenClaw / Hermes / Nanobot 这类通用智能体**：这是 SkyClaw 训练时的主场（强化学习就在 OpenClaw 风格环境里做），适配度天然高，规划、文件编辑、工具调用、交互式应用构建都在能力范围内
- **接自研工作流 / 企业内部工具 / 任务平台**：只要你的系统是按 OpenAI 接口写的，换 base_url 即可；流式 + 工具调用 + 多轮这套齐全，足够撑起自研智能体的调度

选旗舰还是 lite，给个简单判断：**跑应用构建、代码这类对质量敏感的任务用旗舰版；批量、高吞吐、对单价敏感的任务切到 lite 版。** 也可以混用——主流程用旗舰保质量，外围的分类、摘要、批处理交给 lite 省钱，这是智能体系统里很常见的分级调用思路。

## 放进国产模型坐标系横评：它补的是哪个位置

把 SkyClaw 放回当下国产智能体模型的格局里看，会更清楚它的价值。这一年国产在智能体方向已经很热闹：DeepSeek V4 系列分了 Flash 和 Pro 两档、千问 3.6 铺了从 27B 到 Plus 的全尺寸、MiniMax 把 M2.7 推到智能体场景。SkyClaw 进来，补的不是“最强”那个位置——最强还得看 DeepSeek V4-Pro 这类大模型——而是**“同样能打、但便宜一半”**这个性价比身位。

- **和 DeepSeek 比**：V4-Pro 性能更全面，但贵；V4-Flash 便宜，但智能体基准上明显被 SkyClaw 压一头。SkyClaw 正好卡在两者之间——性能贴近 Pro、价格贴近甚至低于 Flash
- **和千问 3.6 比**：千问胜在尺寸齐全、生态成熟；SkyClaw 在 27B 同价位段六项基准全面领先，且定价不到千问 3.6 系列的一半
- **和 MiniMax 2.7 比**：这是 SkyClaw 直接对标的对象，基准全面超越、价格只要一半，对位关系最清楚
- **和 Claude Opus 比**：Opus 仍是闭源智能体的标杆之一，SkyClaw 的说法是在 OpenClaw 相关任务上“逼近”，而不是“超越”——这个分寸官方拿捏得克制，也更可信

对正在做个人 AI 工具、AI 编程助手、自研智能体的国内团队，SkyClaw 的实际意义是多了一个高性价比的国产后端选项：接入零门槛（标准兼容接口）、单价低到能放进高频场景、百万上下文撑得住长任务、还有免费试用期可以先实测再决定。这套组合拳，恰好打在“智能体想上量但被成本卡住”这个最普遍的痛点上。

## 写在最后

SkyClaw-v1.0 没有用“最强模型”这种姿态出场，它讲的是另一个更务实的故事：当智能体已经在基准上证明能干活，下一步的竞争就从“能不能做到”转向“能不能用得起、跑得稳、接得进”。百万上下文解决了长任务跑不完的问题，OpenAI 兼容接口解决了接入门槛的问题，而把输出价压到竞品一半，解决的是最现实的账单问题。

对国内开发者来说，最值得做的一件事很简单：趁免费试用期，拿自己真实的智能体任务接上去跑一轮，用自己的数据而不是官方基准来判断它够不够用。国产智能体模型这一年走得很快，从堆参数、刷榜，到现在开始认真比性价比和工程接入体验——这种转向，对每天在搭智能体、写 AI 编程工作流的人是实打实的好消息。

---

## 参考链接

- [1] 天工 SkyClaw 官方发布页：https://skyworkai.github.io/skyclaw/
- [2] SkyClaw 开源仓库（含定价、接入说明、Demo）：https://github.com/SkyworkAI/skyclaw
- [3] 天工 AI 发布 SkyClaw-v1.0：面向真实工作流的百万上下文 Agent 模型（腾讯云开发者社区）：https://cloud.tencent.com/developer/news/3976243
- [4] 国产 Agent 模型 SkyClaw-v1.0 登场：百万上下文逼近 Opus 4.6（腾讯云开发者社区）：https://cloud.tencent.com/developer/news/3982272
- [5] 昆仑万维：天工 AI 重磅官宣 SkyClaw-v1.0 Agent 模型正式上线（中国证券网）：https://www.cs.com.cn/ssgs/07/2026/05/26/detail_2026052610014261.html
- [6] 天工 AI 发布 SkyClaw-v1.0：百万上下文 Agent 模型（新浪科技）：https://finance.sina.com.cn/tech/it/2026-05-26/doc-inhzfshk8938918.shtml
