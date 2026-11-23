---
title: "Gemini 4 Neon：中间档模型把前沿能力的单价压下来"
date: 2026-11-19
weekday: 星期四
category: 模型
slug: gemini-4-neon-price-performance-2026-11-19
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/gemini-4-neon-price-performance-2026-11-19.png
description: "Google 发布 Gemini 4 Neon，定价每百万 token 输入 3 美元、输出 15 美元，支持 100 万 token 上下文和四档思考。Google 援引 Artificial Analysis 的榜单称，Neon 在智能指数总榜排第 3、编码智能体指数排第 1。"
tags: ["Google","Gemini 4 Neon","大模型","智能体"]
track: overseas-hot
batch: noon
---

# Gemini 4 Neon：中间档模型把前沿能力的单价压下来
![Neon 霓虹招牌与旋钮旁，机器人指挥小机器人搬运代码积木，背景是一架天平](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/gemini-4-neon-price-performance-2026-11-19.png)
*图片来源：Sunky 中文 AI 日报专题配图《Gemini 4 Neon：中间档模型把前沿能力的单价压下来》，2026-11-19。*

**Gemini 4 Neon 把接近顶级的能力、中间档的价格和四档思考放进了同一款模型，团队可以按任务调节推理深度和花费。** 美国 Google 于北京时间 11 月 19 日凌晨 5 点发布 Neon，API 每百万 token 输入 3 美元、输出 15 美元，支持 100 万 token 上下文。Google 援引 Artificial Analysis 的榜单称，Neon 在 Artificial Analysis 智能指数总榜排第 3，编码智能体指数排第 1。[1]

![黑色背景上霓虹灯效果的 Gemini 4 Neon 字样](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-google-gemini-4-neon-header.png)
*图片来源：Google 官方博客《Introducing Gemini 4 Neon》头图，北京时间 2026-11-19。*

## Neon 常规定价居中，Carbon 促销改变了眼下的价差

![Gemini 4 Carbon、Neon、Hydrogen 三款模型的定位、价格、思考档位与可用渠道对比表](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-google-gemini-4-family-comparison.png)
*图片来源：Google 官方博客《Introducing Gemini 4 Neon》家族对比图，图表标注日期 2026-11-18。*

Google 的家族对比图把 Carbon 用于最难的长程项目和深度研究，把 Hydrogen 用于强调速度和效率的日常编码与智能体工作流。Neon 的位置介于两者之间。下表使用家族图公布的 Artificial Analysis 智能指数及输入和输出价格，订阅一列对应图中的订阅权益，各模型的 API 开放条件另列在原图中。[1]

| 模型 | Artificial Analysis 智能指数 | 输入 / 输出（美元／百万 token） | 思考档位 | 订阅范围 |
| --- | ---: | --- | --- | --- |
| Gemini 4 Carbon | 60 | 4 / 20，促销价截至 11 月 30 日 2 / 10 | low、medium、high | Google AI Ultra |
| Gemini 4 Neon | 61 | 3 / 15 | low、medium、high、xhigh | Google AI Pro 和 Ultra |
| Gemini 4 Hydrogen | 54 | 1.50 / 7.50 | low、medium、high、xhigh | 面向所有人 |

按表中常规定价，Neon 的输入和输出单价都介于 Hydrogen 与 Carbon 之间。按促销单价，Carbon 的两项价格都暂时低于 Neon。Neon 的指数分取自 xhigh 档。若团队用促销期间的试验结果编预算，账单应同时保留实付和常规定价两种口径，并把评测档位记在任务旁，方便判断活动结束后，同一批任务是否仍适合原来的分配。[1]

Google AI Studio 的付费 Gemini API 提供 Neon，Gemini for business 可通过 API 与模型下拉菜单使用。Google AI Pro 和 Ultra 用户可在 Google Antigravity 和 Gemini 应用中使用 Neon。试用对话界面，有助于观察用户中途补充条件时任务如何继续。接入 API 的团队还要看应用实际传入的资料、生成的输出和重试次数，订阅权益与程序调用成本各自对应不同的采购问题。[1]

家族表里的上下文能力也有具体的产品含义。Neon 可以在同一轮任务中接收较长的资料，但应用仍要决定把哪些内容交给模型。例如，做代码迁移时，可以先给模型接口约定、相关调用方和验收测试，再按需补充依赖模块。做知识工作时，可以让模型在交付物里标出依据对应的原文位置。这样，评估长上下文的价值就有了可以观察的结果，例如能否减少反复补材料的次数，或者让审阅者更快找到判断依据。

## 编码综合排名靠前，终端和图表仍要按任务选型

![Gemini 4 Neon 与 Claude Opus 5.5、GPT-6.2 Sol 在编码、科学和图表理解等基准上的得分对照表](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-google-gemini-4-neon-benchmark-table.png)
*图片来源：Google 官方博客《Introducing Gemini 4 Neon》基准对照表，北京时间 2026-11-19。*

Google 在发布文中列出了各模型在这些项目上的得分。Artificial Analysis 与 Vals 是第三方指数，表中百分比沿用各基准的评分口径。[1]

| Google 博客所列评测及口径 | Gemini 4 Neon | Claude Opus 5.5 | GPT-6.2 Sol |
| --- | ---: | ---: | ---: |
| Artificial Analysis 智能指数 v5.0.1（分） | 61 | 59 | 57 |
| Vals Index v2.2 | 70.9% | 70.1% | 66.1% |
| Terminal-bench 4.1（作弊调整后的任务准确率） | 65.2% | 65.8% | 61.5% |
| FrontierCode 2.0（主分数，Neon 为默认 high 档） | 46.2% | 47.3% | 43.8% |
| SWE-sweep（各厂商运行框架，软件工程任务通过率） | 39.0% | 10.1% | 8.3% |
| SWE-sweep（mini-SWE 运行框架，软件工程任务通过率） | 12.8% | 3.6% | 11.6% |
| AutomationBench | 56.3% | 42.5% | 41.3% |
| Terminal-Bench-Science 0.1 | 70.5% | 63.3% | 68.0% |
| Chartography | 73.5% | 66.3% | 75.1% |

Claude Opus 5.5 在 Terminal-bench 4.1 和 FrontierCode 2.0 上得分更高，GPT-6.2 Sol 在 Chartography 上得分更高。Neon 在表中的自动化与科学任务上领先。对团队而言，综合指数适合用来筛候选模型，终端操作占比高的编码产品和以图表为主要交付物的产品，则有各自需要优先验证的能力。[1]

评估代码改动时，可以把测试通过、改动范围和审阅负担放在同一条工单上看。例如，修改跨模块接口的任务，除了检查调用方是否更新，还要看补丁是否保留了仓库现有约定。图表任务则可以要求审阅者回到原始资料，检查单位、坐标和结论对应的证据。测试条件与用户实际看到的结果越接近，榜单上的差距越容易转化成一次选型决定。

对于同一个产品，试验过程中也可以把任务失败的原因记下来。如果模型遗漏了工单里写明的约束，增加思考深度是一个可比较的变量。如果模型缺少判断所需的业务规则，就应先补齐资料，再重新比较。还可以给需要反复确认的任务记录人工接手时间。这样即使两个模型交付的补丁都通过测试，负责人也能判断哪一种更适合团队的审阅节奏。

## 思考档位改变每次任务的成本，运行框架也改变通过率

![Artificial Analysis 智能指数与单任务成本的曲线图，比较 Gemini 4 Neon、Carbon、Claude Opus 5.5 与 GPT-6.2 Sol](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-google-gemini-4-neon-aa-index-cost.png)
*图片来源：Google 官方博客《Introducing Gemini 4 Neon》，北京时间 2026-11-19。*

Google 称，Neon 在默认 high 档下，Artificial Analysis 评测的单任务成本比 Carbon 低 32%，以 Carbon 的单任务成本为基数。Google 在讨论 Vals 与 Agent Arena 的能力和成本表现时，还称 high 档的单任务成本比顶级竞品低约 50%–80%，xhigh 档比竞品最高档低 36% 以上，两处均以相应竞品档位的单任务成本为基数。这些是指定评测条件下的任务账单，给团队提供了一个可测试的起点：先用 high 承担需要推理的任务，再观察哪些卡住的任务值得升档。[1]

缓存是另一项单独计费的变量。Neon 缓存输入每百万 token 计 0.15 美元，较普通输入单价低 95%，基数是每百万普通输入 token 的 3 美元。重复引用同一批资料时，能按缓存价计费的输入量决定这一项节省多少。对于长会话，团队可以把账单上的普通输入、缓存输入和输出分别记下来，再结合完成任务所需的调用次数，判断多轮交互是否仍符合预算。[1]

同一款 Neon 在 SWE-sweep 的 mini-SWE 框架下通过了 12.8% 的任务，在 Google Antigravity 下通过了 39.0% 的任务，分母都是该项评测的任务数。厂商框架一行反映各厂商整套运行环境的成绩，mini-SWE 一行给出共同框架下的对照。采购模型 API 和采用完整编码工具，对应的是表里不同的试验条件，前者尤其需要把自己的工具配置带进自测。[1]

Google 还给出一种分工，让 Neon 担任编排器，在 Google Antigravity 中指挥并行运行的 Hydrogen 子智能体。以跨模块改动为例，编排层可以分派互相独立的检查，汇总各处的证据，再由维护者按原有标准审阅。并行能否缩短交付时间，取决于任务能不能独立完成，以及合并阶段是否产生额外返工。复测编排方案时，可以保持任务和验收标准一致，把主模型与子智能体的用量合并计账，并留下汇总修改的记录。[1]

应用里的用户等待时间也值得单独记一列。交互页面上，用户要等到可审阅的结果出来，才知道是否需要补充条件。后台任务可以把验收留到结果返回之后。这两种场景即使用同一模型和档位，能够接受的等待时间也不同。把各步骤的耗时和账单对应起来，团队就能看到推理、工具等待和失败后的重新执行分别耗时多久，以及重试增加了多少费用。

## 安全代码、漏洞识别与智能体行为各有指标

![AutoBaxBench 安全代码生成柱状图，Gemini 4 Neon 以 76% 居首](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-google-gemini-4-neon-autobaxbench.png)
*图片来源：Google 官方博客《Introducing Gemini 4 Neon》，北京时间 2026-11-19。*

Google 公布的 AutoBaxBench 困难档中，面向网络安全的 Gemini 4 Neon 在安全代码生成评测中得分 76%。对需要生成业务代码的团队，这一项对应的是产出的实现能否满足安全要求，可以用已有的代码审阅标准和回归测试来验收。[1]

![WebExploitBench v1.2 柱状图，比较四款模型的平均漏洞召回率](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-google-gemini-4-neon-webexploitbench.png)
*图片来源：Google 官方博客《Introducing Gemini 4 Neon》，北京时间 2026-11-19。*

Google 报告 WebExploitBench v1.2 的平均漏洞召回率为 51.26%，召回率以评测中的已知漏洞为基数。这一结果说明，即使在评测里，识别结果也只覆盖已知问题的一部分，防御团队仍需要结合原有检查机制确认覆盖范围。[1]

![SEC-bench Pro 柱状图，比较六款模型的单次尝试通过率](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-google-gemini-4-neon-secbench-pro.png)
*图片来源：Google 官方博客《Introducing Gemini 4 Neon》，北京时间 2026-11-19。*

在 SEC-bench Pro 中，Neon 单次尝试通过率为 90.5%，以该基准的题目为分母。Collinear 的 CWE-bench v2 上，Neon 的漏洞修补得分为 70.7%。Google 在发布时表示，面向网络安全的 Neon 将在随后几天通过 Fairwind 计划向可信防御者陆续推出。做工具选型时，可把这类受限渠道的可用性和已有 API 的接入条件分别确认。[1]

![Gray Swan 间接提示注入攻击成功率柱状图，数值越低越好](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-google-gemini-4-neon-gray-swan-ipi.png)
*图片来源：Google 官方博客《Introducing Gemini 4 Neon》，北京时间 2026-11-19。*

行为指标给出了另一组约束。Google 报告，Neon 在 Terminal-Bench 4.0 上拒答率为 1.98%、作弊率为 0.0%，两项均以该测试任务为分母。在 Terminal-Bench-Science 上，拒答率为该项任务的 0%。Google 称，Neon 在最高思考档且启用实时防护时，Gray Swan 间接提示注入基准中的攻击成功率为 0.0%。图中柱顶标注的成功率对应 15 次尝试的评测条件。对于会读取外部文档的应用，这些结果可用来设定验收项，部署时仍要检查工具权限和用户授权是否匹配。[1]

Neon 的成本优势取决于比较口径。常规定价下，Neon 位于 Hydrogen 和 Carbon 之间。Google 公布的单任务成本则随思考档位变化，同一款 Neon 在不同运行框架下的通过率也有明显差别。日常编码与复杂工程任务可以共享同一款模型，同时保留按任务调整推理预算的空间。[1]

## 🔗 引用链接

- [1] 美国 Google 官方博客《Introducing Gemini 4 Neon》及文内图表 · https://blog.google/innovation-and-ai/models-and-research/gemini-models/introducing-gemini-4-neon/
