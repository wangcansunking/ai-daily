---
title: "用 TypeScript 写 agent，Mastra 把 Next.js 那套工程搬了过来"
slug: mastra-typescript-agent-framework-2026-06-09
date: 2026-06-09
weekday: 星期二
channel: auto-research
track: A
category: AI Coding
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/06/09/mastra-typescript-agent-framework-2026-06-09.png
tags: [Mastra, TypeScript, agent框架, Next.js, LangGraph, CrewAI, 可观测性, RAG, evals]
feishu_doc_id: null
---
# 用 TypeScript 写 agent，Mastra 把 Next.js 那套工程搬了过来

![Mastra 官网首页：面向 TypeScript 开发者的 agent 框架](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/mastra-typescript-agent-framework-2026-06-09/mastra-typescript-agent-framework-2026-06-09-img1.png)

过去一年多，写 agent 这件事几乎是 Python 的主场。LangGraph、CrewAI、AutoGen，一线能叫得出名字的框架，导入语句清一色是 `from langchain ...`。一个每天在 Next.js、React、Node 里讨生活的全栈开发者，想给自己的 Web 产品接一个能跑多步任务的 agent，标准答案往往是：再起一个 Python 服务，两套语言、两套部署、两套类型边界，中间用 HTTP 缝起来。

Mastra 想改的就是这件事。它是一个用 TypeScript 写、给 TypeScript 开发者用的开源 agent 框架，agent、workflow（工作流编排）、memory（记忆）、RAG（检索增强生成）、evals（质量评测）、observability（可观测性）这些做 agent 绕不开的部件，全部收进同一个 npm 包，和你的 Next.js 应用同一种语言、同一套类型系统、同一条部署线。

这篇文章想说清楚一件事：**agent 框架开始往 TypeScript、往前端全栈走，不是赶时髦，而是因为「做 agent」这件事的重心，正在从「调通一个模型」挪到「把一个会出错的多步流程做成能上线的工程」**——而后者，恰恰是 TypeScript 生态这些年最擅长的活。下面用可查的数据和真实的取舍，把 Mastra 解决了什么、和 Python 阵营怎么选、国内开发者现在该怎么看，一条条摆开。

## Mastra 是谁：Gatsby 原班人马，一年多攒到两万多 star

先把底细交代清楚，省得把它当成又一个周末玩具。

Mastra 由 Abhi Aiyer、Sam Bhagwat、Shane Thomas 三人在 2024 年发起，这三位是 React 静态站点框架 Gatsby 的原班团队，项目入选了 YC（Y Combinator）2025 年冬季批次。代码仓库 2024 年 8 月建立，对外公开发布在 2024 年 10 月。到撰稿时，GitHub 上的 star 数为 24,883，fork 超过 2,200，核心包 `@mastra/core` 的最新版本是 2026 年 6 月 5 日发布的 1.41.0——更新节奏相当密，最近几周几乎每天都有提交。

![Mastra 与三大 agent 框架对比：语言、范式、记忆、成熟度](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/mastra-typescript-agent-framework-2026-06-09/mastra-typescript-agent-framework-2026-06-09-img2.png)

2026 年 1 月 21 日，Mastra 发布 1.0 正式版，同时公布了几个能说明它不只是 demo 的数字：npm 周下载量超过 30 万次，GitHub star 当时为 1.94 万，以及 Replit、WorkOS 这样的公司在生产环境里用它。一个值得记下来的细节是，它不是「先有 demo 再补工程」的路子，而是从第一天就把记忆、评测、可观测性这些「上线才会用到」的部件当成框架的一等部件来设计。

许可证方面也清楚：核心框架是 Apache 2.0 开源，可以商用；只有面向企业的少数增强能力走的是 Mastra 自家的企业许可（source-available，源码可见但有使用限制）。GitHub 接口把仓库许可标成 NOASSERTION，是因为这种「核心开源 + 企业版另算」的双轨结构，并不代表协议不明。对绝大多数自己搭 agent 的开发者来说，Apache 2.0 的核心已经够用。

## 它解决的第一个真问题：别再为 agent 单养一个 Python 服务

把场景具体化。你有一个 Next.js 做的产品，现在想加一个功能：用户提个需求，agent 去查几个内部接口、读一段文档、调一次大模型、必要时停下来等人工确认，最后把结果写回数据库。

在 Mastra 出现之前，TypeScript 这边的标准做法基本是两条路。一条是只用 Vercel AI SDK 这类偏底层的工具，它把「调模型、流式输出、工具调用」这层做得很好，但 workflow 编排、记忆、评测、追踪这些得你自己一砖一瓦砌。另一条是认了，单独起一个 Python 服务跑 LangGraph，前端用 TypeScript、agent 用 Python，两种语言在一个产品里并存。

两条路的代价都很实在。前者是你得自己重新发明一套工程；后者是你的类型安全在 HTTP 边界上断掉了——前端精心定义的 TypeScript 类型，到了 Python 那头要重新声明一遍，接口一改，两边各改一次，错位就是线上 bug 的温床。

Mastra 的回答简单直接：**这一整套都在 TypeScript 里完成**。它官网把自己定位成「面向 AI 应用与 agent 的现代 TypeScript 框架」，明确说能直接嵌进 React、Next.js、Node 应用。agent 调用的工具、workflow 的每一步输入输出，都用 TypeScript 的类型（配合 Zod 做运行时校验）一路贯通，从前端的表单到 agent 的工具参数，是同一套类型定义，改一处编译器全程帮你盯着。对一个全栈开发者来说，这意味着不用再切语言、不用再维护两套部署，agent 就是你 Web 应用里的一个普通模块。

部署这条线也顺着同样的思路：Mastra 原生支持部署到 Vercel、Netlify、Cloudflare，也能跑在 Next.js、Express、Hono 里。你现在的前端部署在哪，agent 大概率就能跟到哪，不用为它单独搭一套运行环境。

## 第二个真问题：workflow 编排，把「会出错的多步流程」写成代码

agent 真正难的地方，从来不是调一次模型，而是「一连串步骤里随便哪一步都可能失败、可能要等人、可能要重试」。这正是 workflow 引擎存在的意义。

Mastra 的 workflow 是一个基于图（graph-based）的编排引擎，支持顺序执行、并行分支、条件判断和循环。它在控制流上的几个能力，恰好对应了做 agent 时最头疼的几类情况：

- **suspend / resume（挂起与恢复）**：任意一步都可以挂起执行、把控制权交给人工，等输入到了再从断点继续。做「人工审批」环节时，这是刚需——agent 跑到一半停下来等人点确认，确认完接着跑，而不是从头再来一遍。
- **time-travel 调试（时间回溯）**：可以把 workflow 挂起后随时恢复，也可以回溯到任意一步重看当时的状态。多步流程出了问题，能倒回去看哪一步出的错，比对着一堆日志硬猜省事得多。
- **持久化执行**：2026 年 5 月底，Mastra 接入了 Temporal（一个成熟的持久化工作流引擎），给 workflow 带来了断点续跑、高级重试和更细的追踪。一个跑了半小时的长流程中途崩了，能从断点接着跑而不是重来，这对真要上线的 agent 是分水岭级别的能力。

把这几条连起来看，Mastra 对「时间、记忆、失败」这三件事的处理是认真的——而业内一个被反复说起的判断是：真正能上线的 agent 框架和只能做 demo 的框架，分水岭恰恰不在集成了多少工具、可视化画布多好看，而在它怎么对待时间、记忆和失败。Mastra 把赌注押在了这条线上。

## 第三个真问题：可观测性和 evals，让 agent 出了问题查得到、改得动

agent 上线后最折磨人的，是它「黑箱」——用户说结果不对，你不知道是模型抽风、工具返回错了、还是上下文喂歪了。Mastra 把这两件事都做进了框架。

**可观测性（observability）**：内建对 agent 调用链路的追踪，记录每一次 agent 决策、token 消耗、延迟和成本，并且能对接你已有的 o11y 平台。换句话说，agent 在线上每做一个决定、烧掉多少 token、慢在哪一步，都有迹可循，而不是事后对着账单发懵。

**evals（评测）**：这是 Mastra 区别于很多「能跑就行」框架的地方。它支持用模型评判、规则判断、统计方法三类方式给 agent 的输出打分，内建的打分器（scorer）覆盖正确性、对检索内容的忠实度、语气、安全性等维度，可以挂在 agent 或 workflow 的输出上，作为 CI（持续集成）的一环跑，也可以单独当成回归测试套件。

这一条对从 Web 工程过来的人特别亲切：它把「agent 质量」变成了和「单元测试覆盖率」类似的、可以纳入 CI 流水线的工程指标。你改了一版 prompt，跑一遍 evals，分数掉了就拦在合并之前——这正是前端工程这些年沉淀下来的纪律，被原封不动搬到了 agent 上。

![Mastra Studio 本地调试界面示意：agent / workflow 的可视化追踪](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/mastra-typescript-agent-framework-2026-06-09/mastra-typescript-agent-framework-2026-06-09-img3.png)

配套的还有 Studio——一个本地的交互式调试界面。装好 Mastra 后能直接打开，在浏览器里看 agent 和 workflow 怎么一步步执行、记忆里存了什么、每步的输入输出长什么样。对习惯了浏览器开发者工具的前端来说，这种「打开就能看见内部状态」的体感，比对着终端读日志要顺手太多。

模型供应商这块也不设门槛：Mastra 有一个统一的模型路由层，号称覆盖 OpenAI、Anthropic、Google 等上千个模型，能自动从环境变量里识别各家的 API key。这意味着你换模型基本是改个配置的事，业务代码不用动。

## 和 Python 阵营怎么选：先看语言，再看场景

讲清楚 Mastra 是什么，接下来是国内开发者最关心的：它和 LangGraph、CrewAI 比，到底该怎么选。先把几个框架的底子摆成一张表。

| 维度 | Mastra | LangGraph | CrewAI |
|---|---|---|---|
| 主语言 | TypeScript | Python | Python |
| GitHub star | 约 2.5 万 | 约 3.4 万 | 约 5.3 万 |
| 编程范式 | workflow 图 + agent | 有向图（节点 / 边 / 状态） | 角色分工（researcher / writer…） |
| 内建记忆 | 有 | 仅状态检查点 | 有 |
| 内建可观测性 | 有 | 需配合 LangSmith | 部分 |
| 内建 evals | 有 | 需另接 | 部分 |
| 适配栈 | Next.js / React / Node | Python 后端 | Python 后端 |
| 上手到首个多 agent | 中等 | 偏陡 | 最快（约 10 分钟） |

> 注：star 数为撰稿时 GitHub 实时数据的约数（Mastra 24,883、LangGraph 34,188、CrewAI 53,078），会随时间变化，仅供量级参考。记忆 / 可观测性 / evals 三列综合多家公开测评整理，各框架版本迭代快，以官方最新文档为准。

这张表里有几个判断值得展开。

**第一，选框架先选语言，能砍掉一半选项。** 这是业内一个很实在的经验法则：你的团队是 TypeScript 栈，真正的候选基本就是 Vercel AI SDK 和 Mastra；是 Python 栈，才在 LangGraph、CrewAI、PydanticAI、OpenAI Agents SDK 之间挑。跨语言硬凑的代价前面说过了，多数情况不值。

**第二，LangGraph 仍是 Python 侧最能打的「重型」选择。** 它的有向图模型——节点是函数、边是状态转移、支持条件路由、循环、子图——几乎能表达任何编排模式，对执行流程、状态检查点、可恢复 workflow 有最完整的控制。Uber、Klarna、LinkedIn、摩根大通等公司在生产环境里用它，Klarna 的客服 agent 服务 8500 万用户。如果你本来就是 Python 团队、要做高复杂度的有状态生产级 agent，LangGraph 依然是稳妥答案。

**第三，CrewAI 胜在快。** 它的「角色分工」抽象（研究员、写手、编辑各司其职）特别贴合产品团队对「分工协作」的直觉，从零到第一个能跑的多 agent 流程大约十分钟。要快速验证一个「多角色接力」的点子，它最省事。

**第四，记忆是个被低估的分水岭。** 一个常被提到的观察是：真正内建了语义记忆的框架不多，CrewAI、Mastra 和 Google ADK 算是有；LangGraph 提供的是状态检查点（持久化状态，不等于语义记忆）；其余很多框架的记忆要靠你自己从头搭。对要做「长期记得用户」的个人助手类 agent，这一条权重很高。

落到一句话：**如果你在做一个 TypeScript 产品，需要的不只是裸调模型，而是长流程、记忆、工具审批、RAG、评测、上线后可追踪这一整套，Mastra 是目前 TypeScript 侧最顺手的选择；如果你是 Python 团队做高复杂度生产 agent，LangGraph 仍然更稳；要十分钟跑通一个多角色原型，CrewAI 最快。** 没有谁取代谁，是各自站在自己最合适的位置上。

## 落到国内：用 TypeScript 做 agent，现在成熟到什么程度

把视角收回国内。一个一线全栈开发者会问：这套东西，现在值不值得在真实项目里用 TypeScript 做 agent，而不是老老实实切 Python？

先说成熟度判断。Mastra 1.0 已发、周下载量过 30 万、有 Replit 这类公司在生产里用，核心能力（agent / workflow / 记忆 / RAG / evals / 可观测性）齐整且 Apache 2.0 可商用——以「能上线」为标准，它已经过了玩具阶段。对国内开发者，最直接的好处是：你那套 Next.js / React / Node 的工程经验能平移过来，不用为 agent 单独学一门语言、单养一套服务。

什么场景值得选 TypeScript 框架？给一个可操作的判断：

- **值得选 TS 的情况**：agent 是嵌在 Web 产品里的一个功能（比如站内智能助手、表单驱动的自动化、给前端用户用的 RAG 问答），团队主力是前端 / 全栈，你最看重「同一套类型从前端贯到 agent」和「同一条部署线」。这种场景，Mastra 让你不必在产品里塞进一个 Python 服务。
- **仍建议用 Python 的情况**：agent 本身是个重型独立后端，要做高复杂度的有状态编排、深度依赖 Python 那边成熟的数据科学与模型工具，或者团队主力就是 Python。硬切 TypeScript 反而是给自己添堵。

判断标准其实就一句：**agent 是「长在你 Web 产品里的一个模块」，还是「一个独立的重型后端服务」**？前者，TypeScript 框架现在足够成熟，Mastra 值得认真试；后者，Python 阵营的积累暂时还更厚。

国内生态这边也并非没有对位。字节的扣子（Coze）在 2026 年初出了 2.0，并把 Coze Studio、Coze Loop 以 Apache 2.0 开源，可商用——它的开发平台后端用 Go、前端用 React + TypeScript，配套的 Eino 是一个 Go 写的 agent 框架，Coze Loop 提供 Go / Python / Java 多语言 SDK 做可观测性与调优。和 Mastra 的差别在路线：扣子走的是「平台 + 可视化 + 全栈开发体系」，覆盖从零代码到专业开发再到企业团队；Mastra 走的是「纯代码、TypeScript 原生、给写代码的人」。一个偏平台，一个偏框架，面向的是不同习惯的开发者。对国内全栈开发者来说，二者不冲突：要可视化搭建和企业级平台能力，看扣子这类；要在自己的 TypeScript 代码里把 agent 当普通模块写、要类型一路贯通，Mastra 这一派更对路。

## 写在最后：agent 工程化，TypeScript 这一派补齐了

回到开头那个论点。agent 框架往 TypeScript、往前端全栈走，根子在于做 agent 的重心变了——从「调通一个模型」挪到了「把一个会出错的多步流程做成能上线、查得到、改得动的工程」。而「工程化」这件事，TypeScript 生态恰好有最厚的底子：类型贯通、CI 纪律、可观测性、统一部署。Mastra 做的，就是把这套底子原样接到 agent 上，让一个全栈开发者不用切语言、不用单养服务，就能在自己的产品里把 agent 写得像写任何一个业务模块一样自然。

对国内开发者，这是个踏实的好消息。它意味着「会写 TypeScript」从今往后也是「会做 agent」的一条完整路径，而不必绕道 Python。Python 阵营依然在它擅长的重型场景上稳稳站着，TypeScript 这一派则把「agent 长在 Web 产品里」这条路补齐了。两边各自更强，选择更多，这对每天在一线写代码的人，都是实打实的利好。

挑框架这件事，从来不该是站队，而是看手里的活、看团队的语言、看 agent 在你产品里到底是个模块还是个独立服务。把这几条想清楚，答案自然就出来了——而现在，TypeScript 这一栏里，终于有了一个值得认真填进去的名字。
