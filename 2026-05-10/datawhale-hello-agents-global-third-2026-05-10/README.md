---
title: "Datawhale 4.5 万星 Agent 教程冲全球第二"
date: 2026-05-10
weekday: 星期日
category: 开源
slug: datawhale-hello-agents-global-third-2026-05-10
description: "国内开源 AI 学习社区 Datawhale（南瓜书、苹果书、蘑菇书的出品方）写的中文 Agent 教程书 Hello-Agents 拿到 45,621 颗星，登上 GitHub Python Trending 全球第二，仅次于 Anthropic 当周开源的金融 Agent 套件，比 Hugging Face 官方 Agents Course 多 1.6 万颗星。本文带你看 16 章目录、和微软 GenAI for Beginners / OpenAI Cookbook 这些海外同类的 Star 横评、以及 6 类参与路径。"
track: overseas-hot
domain: ai-education-open-source
cover: datawhale-hello-agents-global-third-2026-05-10.png
tags:
  - Datawhale
  - Hello-Agents
  - Agent 教程
  - 国内开源出海
  - GitHub Trending
---

# Datawhale 4.5 万星 Agent 教程冲全球第二

> Anthropic 上周开源的金融 Agent 套件排第一，第二位是一本中文书：Datawhale 的 `hello-agents`，45,621 颗星，2025 年 9 月开仓到现在 8 个月。比 Hugging Face 官方 Agents Course 多 1.6 万颗星。第 3 到 15 名一水的英文项目，唯一的中文项目就是它。

![Datawhale 的 Hello-Agents 冲到 GitHub 全球第二](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/datawhale-hello-agents-global-third-2026-05-10/datawhale-hello-agents-global-third-2026-05-10.png)

打开 GitHub 趋势榜 Python 类目，2026 年 5 月 10 日全球第一是 Anthropic 上周才开源的 `anthropics/financial-services` 金融 Agent 套件，第二位就是 `datawhalechina/hello-agents`——一本由国内开源社区 Datawhale 发起、纯中文、从零讲到 16 章的智能体入门书。当天前 15 名里只有这一个中国教育类项目，紧贴在 Anthropic 之后。

Datawhale 这个名字很多国内 AI 开发者都熟，过去 8 年它出过《南瓜书》（周志华西瓜书公式详解，25,800 星）、《苹果书》（李宏毅深度学习课程笔记，16,537 星）、《蘑菇书》（中文强化学习教程，14,123 星）、吴恩达 LLM 中文课（23,979 星）这几本两万星级别的代表作，但都没把单仓 Star 推过 4 万。Hello-Agents 在 2025 年 9 月开仓、2026 年 5 月已经累到 45,621 星 / 5,523 fork / 773 个提交 / 至少 20 位公开贡献者，超越南瓜书成为 Datawhale 整个组织最受欢迎的项目。

四块内容：

- Datawhale 是谁、过去拿出过什么代表作、凭什么这一本书能突围；
- Hello-Agents 这本书 16 章 5 部分到底讲了什么；
- 跟 Hugging Face Agents Course、微软 GenAI for Beginners、OpenAI Cookbook 几本英文 Agent 教程横评，差距和优势分别在哪；
- 国内开发者怎么进这个项目、能贡献什么。

## 一、Datawhale 是谁：一家长出 200 多个开源项目的中文 AI 学习社区

![Datawhale 社区组织画像](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/datawhale-hello-agents-global-third-2026-05-10/datawhale-community-profile.png)

Datawhale 的底子是这样的：2018 年 12 月在国内成立的开源 AI 学习社区，GitHub 组织名 `datawhalechina`，到 2026 年 5 月公开仓库 201 个、组织级 followers 26,114、长期跟着做学习者贡献的核心成员上百人。它的运行模式是「读书会 + 教程书 + 学习路径」三件套：每个学习方向选一本经典教材或一个工程主题，组一支社区作者团，把内容拆到章节、配上代码和习题，再用读书会形式带学员一起跑完。

过去 8 年，Datawhale 跑出过几本代表作，每一本都对应了一拨学习者的核心刚需：

| 项目 | 创建年 | 当前 Star | 主题 |
|---|---|---|---|
| pumpkin-book（南瓜书） | 2018 | 25,800 | 周志华《机器学习》西瓜书公式详解 |
| leedl-tutorial（苹果书） | 2019 | 16,537 | 李宏毅深度学习课程笔记 |
| easy-rl（蘑菇书） | 2020 | 14,123 | 中文强化学习教程 |
| llm-cookbook | 2023 | 23,979 | 面向开发者的吴恩达 LLM 中文课 |
| llm-universe | 2023 | 12,941 | LLM 应用开发入门教程 |
| self-llm | 2023 | 30,341 | 国内开源大模型部署微调指南 |
| happy-llm | 2024 | 30,067 | 从零开始构建大模型 |
| **hello-agents** | **2025** | **45,621** | **从零开始构建智能体** |

这张表的关键不是绝对数字，而是**节奏**：南瓜书做了 7 年到 25,800 星，hello-agents 做了 8 个月到 45,621 星，是过去整个组织内增速最快、终点最高的单仓。它正面映出一件事——Agent 这个学习方向今年的真实需求量级，比当年机器学习入门书还要更密。

![Datawhale 旗舰开源项目 Star 排行](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/datawhale-hello-agents-global-third-2026-05-10/datawhale-flagship-projects.png)

为什么是 Datawhale 而不是其他社区先做出来？看一下 Hello-Agents 仓库 README 里的「核心贡献者」名单，能感受到这家社区的人才结构：

- 项目负责人陈思州（Datawhale 成员，全文写作和校对，已合并 464 个提交）；
- 联合发起者孙韬（Datawhale 成员、CAMEL-AI 贡献者）；
- 联合发起者姜舒凡（章节习题设计）；
- 第十四章案例由牛客科技 Agent 工程师曾鑫民贡献；
- 浙江师范大学杭州人工智能研究院教授朱信忠挂指导专家。

Extra-Chapter 名单还能看到帝国理工、西安交通、深圳大学、南京信息工程、浙大的在读学生贡献章节。「教师 + 工业界工程师 + 在校学生」三层贡献者结构，每一层都给社区注入不同的内容密度。

## 二、Hello-Agents 16 章拆讲：从智能体定义讲到赛博小镇

![Hello-Agents 16 章 5 部分内容地图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/datawhale-hello-agents-global-third-2026-05-10/hello-agents-16-chapters.png)

仓库 README 把全书拆成 5 大部分 16 章：

**第一部分 · 智能体与语言模型基础（第 1-3 章）**

- 第 1 章 **初识智能体**——智能体定义、类型、范式与应用；
- 第 2 章 **智能体发展史**——从符号主义到 LLM 驱动的智能体演进；
- 第 3 章 **大语言模型基础**——Transformer、提示、主流 LLM 及其局限。

第一部分是地基，给完全没有 Agent 概念的读者补理论。这种「先讲历史再讲技术」的章节顺序是中文教材的传统打法，方便读者理解为什么 Agent 概念在 LLM 时代前后含义会有差别。

**第二部分 · 构建你的大语言模型智能体（第 4-7 章）**

- 第 4 章 **智能体经典范式构建**——手把手实现 ReAct、Plan-and-Solve、Reflection；
- 第 5 章 **基于低代码平台的智能体搭建**——Coze、Dify、n8n；
- 第 6 章 **框架开发实践**——AutoGen、AgentScope、LangGraph；
- 第 7 章 **构建你的 Agent 框架**——从 0 开始构建自研框架。

第二部分是这本书最像「实战手册」的一段，编排逻辑是「先用经典范式 → 再用现成低代码平台 → 再用主流开源框架 → 最后自己造一个轮子」。第 7 章对应到一个独立仓库 `jjyaoao/helloagents`，已经发布到 V1.0.0 版本，是教程 + 自研框架双仓联动的典型工程做法。

**第三部分 · 高级知识扩展（第 8-12 章）**

- 第 8 章 **记忆与检索**——记忆系统、RAG、存储；
- 第 9 章 **上下文工程**——持续交互的「情境理解」；
- 第 10 章 **智能体通信协议**——MCP、A2A、ANP；
- 第 11 章 **Agentic-RL**——从 SFT 到 GRPO 的 LLM 训练实战；
- 第 12 章 **智能体性能评估**——核心指标、基准测试与评估框架。

第三部分密度最高。第 10 章把 2025 年最重要的几个 Agent 互通协议放到一个章节里同框讲，这种结构在英文 Agent 教程里目前还很少见——MCP 通常一篇博文讲，A2A 通常 Google 自家文档讲，ANP 在中文社区也才刚起来。第 11 章直接讲 Agentic-RL（SFT → GRPO 全流程），是教程类项目里少见的把强化学习训练章节摆进 Agent 实战书的尝试。

**第四部分 · 综合案例进阶（第 13-15 章）**

- 第 13 章 **智能旅行助手**——MCP 与多智能体协作；
- 第 14 章 **自动化深度研究智能体**——DeepResearch Agent 复现与解析；
- 第 15 章 **构建赛博小镇**——Agent 与游戏的结合，模拟社会动态。

第四部分给的是三个完整可跑的案例：一个面向真实 to-C 场景（旅行助手），一个面向科研 / 写作场景（DeepResearch 复现），一个面向研究场景（赛博小镇模拟社会）。第 14 章对国内做 RAG / 知识工程的开发者特别有借鉴价值，因为它直接对标 OpenAI / Perplexity 那条 DeepResearch 路线，但用国产模型 + 国内可获取的 API 接得起来。

**第五部分 · 毕业设计（第 16 章）**

- 第 16 章 **毕业设计**——构建属于你的完整多智能体应用，挂在 `Co-creation-projects` 目录里收社区共创作品。

第 16 章不是收尾彩蛋，是真的在收作品。Datawhale 的传统是每个大项目最后必有「毕设」环节，所有学员的最终作品反过来变成教程的一部分回流到社区，这是社区生态闭环最关键的一段。

加上正文之外的 11 篇 Extra-Chapter（含 Agent 面试题总结 + 参考答案、上下文工程补充、Dify 保姆教程、GUI Agent 实战、Skill 写作最佳实践、Agent Self-Evolution 自进化等），整本书的内容总量已经远远超过传统教科书的厚度。Extra 章是社区共创的承载位，对投稿门槛比较低、节奏比正章快得多，对一线开发者很友好。

## 三、跟 Hugging Face / 微软 / OpenAI 横评：差距和优势分别在哪

![全球 Agent 教程类开源项目 Star 横评](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/datawhale-hello-agents-global-third-2026-05-10/global-agent-tutorials.png)

把 Hello-Agents 放在全球 Agent 教程类项目里横评，最直接对比的有这么几个（截至 2026-05-09 公开 Star 数）：

- **`langchain-ai/langchain` · 136,252 星**——其实算框架不算教程，但很多入门读者会把它的 docs 当 cookbook 读，参与人数巨大；
- **`microsoft/generative-ai-for-beginners` · 110,525 星**——21 课 GenAI 入门，最早一批英文 GenAI 教程，受众面最广；
- **`openai/openai-cookbook` · 73,372 星**——OpenAI 官方 API 范例库，覆盖最快但定位是范例不是教材；
- **`microsoft/AI-For-Beginners` · 47,295 星**——微软 24 课 AI 入门，更偏经典 ML / DL；
- **`datawhalechina/hello-agents` · 45,621 星**——本文主角，纯中文，专攻 Agent；
- **`huggingface/agents-course` · 28,498 星**——Hugging Face 官方 Agents Course，跟 Hello-Agents 同年（2025）创建，是定位最接近的英文同类；
- **`NirDiamant/GenAI_Agents` · 21,876 星**——50+ 实战教程合集；
- **`NirDiamant/agents-towards-production` · 19,076 星**——更面向生产级 Agent 的端到端教程。

把这八个项目摆在一起，能看到几个清晰的事实：

**第一，Hello-Agents 已经稳稳进了全球 Agent 教程类项目第一梯队**。比 Hugging Face 官方 Agents Course 多约 1.6 万颗星，比 NirDiamant 两本英文实战合集都更高，是非英文社区第一个进到 4 万星俱乐部的 Agent 教程书。

**第二，跟微软 21 课和 OpenAI Cookbook 还有一个量级差距**。微软的 GenAI for Beginners 是 11 万星，OpenAI Cookbook 是 7.3 万星，这两个项目的优势是站在英文受众的入口位 + 大公司官方背书。Hello-Agents 想在这条线继续往上突破，难度比从 0 到 4 万更大，需要更多英文版传播 + 海外社区参与。

**第三，定位差异比 Star 数差异更值得看**。LangChain docs 实际是工具文档，不能算严格意义的教程；OpenAI Cookbook 是 API 范例，不教概念；微软两本「Beginners」更偏 GenAI / AI 通识。在「真正系统讲 Agent 是什么、怎么从零写、怎么训」这条狭义路线上，Hugging Face Agents Course、NirDiamant 两本、Hello-Agents 才是真正的同行三人组。这三本里 Hello-Agents 是中文，目录密度最高，覆盖到 Agentic-RL / 自研框架 / 评估这些其他两本不展开的硬骨头章节，是它能冲到第一梯队的核心原因。

但说实话，**Hello-Agents 的英文版 README_EN.md 已经放出，README 默认还是中文，海外读者打开第一眼是中文目录**。这意味着 4.5 万星里很大一部分还是中文社区贡献，全球榜单第二的位置主要是因为 GitHub Trending 算的是当日 Star 增速，不直接代表全球受众规模。中文社区的一次集中释放，不等于中文教程已经在英文世界完成了渗透。

## 四、参与路径横评：从读 Issue 到写 Extra-Chapter 都开放

![Hello-Agents 国内开发者参与路径](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-10/datawhale-hello-agents-global-third-2026-05-10/contribute-paths.png)

冲到全球第二是结果。比结果更值得看的是**这本书还在收贡献**。仓库 README 直接把贡献路径写在文末，门槛梯度做得非常细：

- **🐛 报 Issue**——目前 110 个 open issue，全部都是社区在反馈错别字、链接失效、概念表达问题。门槛最低，是新人第一次接触开源项目最舒服的入口；
- **📝 提 PR 改正文**——直接改章节内容、补图、补代码、补习题答案，用 GitHub PR 流程合并；
- **✍️ 投稿 Extra-Chapter**——独立小专题不挤进正文（已有 10 篇上线，包含面试题总结 + 参考答案、上下文工程补充、Dify 保姆教程、Skill 写作实践、Agent 自进化等），是社区贡献吞吐量最大的一个口子；
- **🧪 共创毕业设计**——跟着第十六章框架做一个完整多智能体应用，合入 `Co-creation-projects` 目录；
- **🔧 维护 HelloAgents 框架**——向 `jjyaoao/helloagents` 自研框架仓提 PR，目前 V1.0.0 已发布、官方明确说会继续完善工具和特性，跟这本教程双仓联动；
- **📣 内容传播 + 答疑**——读者群答疑、写学习笔记、做配套视频课。视频课部分仓库 README 明确说「将会陆续放出，更加细致，授人以鱼也授人以渔」，对内容创作者也是窗口。

这种贡献模型有一点值得国内其他开源教程参考——**门槛是分层的**。报错别字和写一章 Extra 是完全两个量级的工作，但都被 README 平等列为贡献方式。这种设计让任何水平的学习者都能找到适合自己的进入点，而不是被「先做 PR 才算贡献」这种英文社区默认的隐形门槛挡在外面。

## 五、为什么这件事重要：从「下载者」到「教程出海者」的范式转换

国内开源 AI 教育内容过去 8 年的主要叙事是「翻译 + 解释」——把 Andrew Ng 的课翻成中文（llm-cookbook）、把李宏毅的课整理成笔记（leedl-tutorial）、把周志华的西瓜书做习题详解（pumpkin-book）。这条路线的本质是**学习者社区**，把全球最好的内容搬到中文世界。这不是劣势，是任何后发地区在生态早期都必经的一步——印度社区做 AI 翻译、东南亚社区做本地化，都是同样的逻辑。

Hello-Agents 跨出去的一步是**自己写一本原创教程**。它没有等待哪本英文经典 Agent 书出现再来翻译，而是直接由社区作者团基于 2025 年的 Agent 现状从零写 16 章。它的目录里既能看到 ReAct / Plan-and-Solve 这些从英文社区学来的范式，也能看到 MCP / A2A / ANP 这种英文社区还没系统讲过的协议横评，还能看到 Agentic-RL 从 SFT 到 GRPO 这种偏研究的训练章节。这是「从下载者到原创者」的工程范式转换的一个清晰信号。

类似的信号其实今年已经在多个方向出现：DeepSeek、Qwen、GLM、Kimi 在模型侧已经把节奏从「跟跑」推到「并跑甚至领跑」；vLLM / SGLang 这些推理引擎接受了大量国内贡献者的 Day-0 适配 PR；OpenClaw 这种国产客户端拿到 36 万星量级。模型、引擎、客户端三条线都在出海，教程这条线晚了一拍——Hello-Agents 是它的代表作。

具体到 Hello-Agents 这个项目，国内开发者最容易切入的几件事：

- 英文版 README_EN.md 既然已经放出，章节英文版的协同翻译就是最直接、影响最大的一类贡献；
- Extra-Chapter 投稿门槛低、节奏快、影响面广，特别适合一线开发者把自己工程实践沉淀成一个独立小专题；
- 第十六章毕业设计的共创空间，对在读学生和刚转 AI 工程方向的开发者是非常友好的练手项目入口。

从 2018 年第一本《南瓜书》到 2026 年的 Hello-Agents，Datawhale 这家社区花了 8 年时间把中文 AI 教育从「学习者下载者」做到「全球教程出海者」。45,621 颗星只是这条路上一个清晰的里程碑——下一阶段更值得期待的是中文 AI 教程能不能真的进到英文受众的入口位，把 Hugging Face Agents Course、微软 AI for Beginners 这条线变成「中英双轨教学」的常态。

榜单第二的位置不会停在那。不管是被新冲上来的英文项目挤下去、还是被同社区的下一本《从零开始训练智能体》（README 已经预告）继续往上推，这本书都已经把一件事做成了：中文 AI 教育第一次有了一本可以拿到全球榜单上跟英文项目并列展示的原创 Agent 书。这件事本身比第几名更值得记下来。

---

**仓库公开数据**（截至北京时间 2026-05-09 20:15）：

- Star 45,621；Fork 5,523；Watcher 145；Open Issues 110；过去 1 年总提交 773；
- 创建于 2025 年 9 月 7 日；License NOASSERTION；Topics: agent / llm / rag / tutorial；
- 在线阅读：[国内](https://hello-agents.datawhale.cc) / [GitHub Pages](https://datawhalechina.github.io/hello-agents/)；
- PDF 下载：[GitHub Releases](https://github.com/datawhalechina/hello-agents/releases/latest/) / [Datawhale 学习平台](https://www.datawhale.cn/learn/summary/239)；
- 当日榜单位置：GitHub Python Trending 全球第 2（2026-05-10，第 1 名为 `anthropics/financial-services`）；
- 数据来源：仓库 README、GitHub Trending 公开页面、各项目 GitHub 页面 Star 计数。
