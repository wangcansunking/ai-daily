---
title: "JiuwenSwarm 开源：国产 multi-agent 第一次上桌"
description: "5/18 openJiuwen 社区放出 JiuwenSwarm，华为 2012 实验室 + 华为云 AgentArts 团队联合构建。PinchBench 94.2% / token -34.8% / LOCOMO 85% 三组对外报告口径拆解，与 Anthropic Swarm / CrewAI / AutoGen / LangGraph / 字节 DeerFlow 同台横评，国内开发者接入路径详解。"
date: 2026-05-19
weekday: 星期二
category: AI Agent / 国内开源 / 多智能体
track: domestic-hot
slug: jiuwenswarm-huawei-multi-agent-swarm-2026-05-19
cover: jiuwenswarm-huawei-multi-agent-swarm-2026-05-19.png
keywords: [JiuwenSwarm, 九闻群体智能, openJiuwen, 华为开源, multi-agent, Swarm Skills, Coordination Engineering, PinchBench, HITS, HOTS]
---

# JiuwenSwarm 开源：国产 multi-agent 第一次上桌

![九闻群体智能 JiuwenSwarm 开源封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/jiuwenswarm-huawei-multi-agent-swarm-2026-05-19/jiuwenswarm-huawei-multi-agent-swarm-2026-05-19.png)

## 开篇：一句话先把今天这件事说清楚

5 月 18 日，openJiuwen 开源社区放出了一支叫 **JiuwenSwarm** 的多智能体（multi-agent）框架，背后是**华为 2012 实验室和华为云 AgentArts 团队**。这是国产 multi-agent 第一次在和海外 Anthropic Swarm、CrewAI、AutoGen、LangGraph 同一张牌桌上端出**完整方案 + 公开评测分数**，而不是只发个 demo 视频。

按量子位、网易、中华网三家同日报道的数据：

| 指标 | JiuwenSwarm | 参照 |
|---|---|---|
| PinchBench 综合得分 | **94.2%** | OpenClaw 按报道为 91.6% |
| 平均 token 消耗 | **降低 34.8%** | 同任务量、对比单 Agent 范式 |
| LOCOMO 长期记忆准确率 | **85%** | 跨 session 跟踪能力 |
| 开源仓库 | **Apache-2.0 · Python · 已挂 697 star / 121 fork** | 截至 5/18 22:00 实查 |

> ![JiuwenSwarm 三组对外报告口径](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/jiuwenswarm-huawei-multi-agent-swarm-2026-05-19/jiuwenswarm-three-kpis.png)

![量子位 2026-05-18 JiuwenSwarm 报道 hero 截图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/jiuwenswarm-huawei-multi-agent-swarm-2026-05-19/qbitai-jiuwenswarm-hero.jpeg)

这篇文章把三件事讲清楚：

1. **这个框架到底做对了什么**，让它能跟 Anthropic Swarm、CrewAI、AutoGen 同框对比；
2. **数字怎么看**，哪些是 openJiuwen 社区自己报的、哪些是行业第三方验证、哪些得标"待复现"；
3. **国内开发者怎么接入**，把 Qwen、DeepSeek、GLM 接进来跑一支自己的 Agent Swarm。

文末附 5 个常见疑问、横评表、以及实操路径。

---

## 一、从"养一只很猛的 Agent"到"养一群会自己长本事的 Agent"

过去两年，国内外几乎所有 AI 公司都在做同一件事——把单个 Agent 做强：更长的上下文、更准的工具调用、更稳的 ReAct 循环。Anthropic Claude Code、阿里 Qoder、字节 Trae、Cursor 都属于这一波。这条路线的极限在 2026 年春天逐渐显现：上下文撑到 200K 之后边际收益递减，工具调用准确率从 92% 顶到 95% 之后再难突破一两个点，长流程任务里依然会"忘事"。

单 Agent 触到天花板的时间，比想象中早。理由很朴素：**真实工作里复杂任务从来不是一个人能干完的**。一个程序员同时写代码、做技术评审、跑测试、写文档，这件事在公司里都要分给四个角色，凭什么交给一个 Agent 就能做好？openJiuwen 在 5/18 的开源公告原话是：

> "真实世界里那些真正复杂的任务，如跨领域深度调研、大型软件项目交付、多角色协同决策、复杂业务流程编排——从来都不是'一个人'能搞定的。"
> ——网易科技 2026-05-18

JiuwenSwarm 把方向切到 **"养一群 Agent，让它们自己分工、自己协商、自己进化"**。社区给这套范式起了个新词叫 **Coordination Engineering（协同工程）**。

听起来像营销话术，但拆开看四层架构就能看到落点：

![JiuwenSwarm 四层协同工程架构](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/jiuwenswarm-huawei-multi-agent-swarm-2026-05-19/jiuwenswarm-architecture.png)

四层从下往上读：

- **L1 · Agent Swarm**：多个 Agent 自己分配子任务，遇到分歧动态协商，单个出错时其他 Agent 重新协调（不是简单的串行 pipeline）；
- **L2 · Swarm Skills**：把"这一群 Agent 怎么协作完成某类任务"这个隐式 know-how 抽出来，写成**可移植的规范文件**（不是运行时代码，是描述格式）；
- **L3 · Swarm Skills Hub**：托管在 `swarmskills.openjiuwen.com` 的开发者共享市场——别人调出来过的协作模式，你能直接 `import` 复用；
- **L4 · 自演进算法**：跑完一个任务后，系统自己看执行轨迹，反推"这里有个新协作模式值得沉淀"，把它打分、归类、丢回 Hub。

第四层是最关键的差异化。这套自演进算法在 5 月初挂到 arXiv 的论文（编号 2605.10052）里有详细说明，用 **EUF 三维评分**：

- **Effectiveness（E）**：补丁的质性影响，0-1；
- **Utilization（U）**：补丁的采用率，0-1；
- **Freshness（F）**：时间衰减因子，指数半衰期。

最终复合分 `S = wE·E + wU·U + wF·F`，让 Skill 库自我新陈代谢——没人用的旧 Skill 自动降权，效果好的新 Skill 自动顶上去。

论文里有句话定义了它的边界：

> "A description format, not a runtime. The specification dictates file structure and semantics of a multi-agent coordination protocol, entirely decoupled from the framework-specific implementation."
> ——arXiv 2605.10052v1

翻译过来：**Swarm Skills 不是又一个跑时框架，是一份大家都能读、都能写、都能搬的格式规范**。这就意味着理论上你今天在 JiuwenSwarm 上沉淀的协作模式，明天可以原样搬到 CrewAI 或者 LangGraph 上跑——只要后者愿意支持这套规范。

这个定位很有趣，因为它跳过了"框架战争"的传统打法，直接去做**协议层**。

打个不算严谨但容易理解的类比：CrewAI、AutoGen、LangGraph 这一代框架更像是**手机厂商**——每家做自己的硬件、自己的系统、自己的应用商店。JiuwenSwarm 想做的事更像 **Android 的 APK 安装包格式**——只要你支持这个格式，谁家手机都能装。能不能成不取决于发布会，取决于有没有足够多框架愿意支持这套规范。

另一个值得提的细节：openJiuwen 仓库 README 把 Skill Self-Evolution 单独列了一篇文档（`docs/en/SkillSelfEvolution.md`），TaskMemory、ContextCompression、CodingMemory 也各自独立成文。这意味着设计阶段就把"长期演化"想得比较清楚，不是临时加上去的卖点。

---

---

## 二、和 Anthropic Swarm、CrewAI、AutoGen、LangGraph、DeerFlow 同框比一下

按 5/18 各家项目主页 + 社区共识，我列了一张横评表：

![JiuwenSwarm 与海外 framework 同框 compare 横评](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/jiuwenswarm-huawei-multi-agent-swarm-2026-05-19/jiuwenswarm-framework-compare.png)

读这张表前先把 **Anthropic Swarm** 的现状交代清楚：Anthropic 24 年 10 月放出 Swarm 作为实验项目，定位明确写着 "educational"，到 2026 年 5 月仓库已停更近半年，handoff 协议本身设计巧妙，但没有自演进、没有 Skill 沉淀、没有 Hub。社区共识是：**它示范了 multi-agent 的最小可行内核，但不是产品级方案**。

CrewAI 走的是 Role-Task 模型——给每个 Agent 设角色和任务，靠 Python 简洁 API 拉群业务团队上手。AutoGen 来自微软研究院，强项是 Agent 之间的对话式协作，最早把"多 Agent conversation"做成范式。LangGraph 来自 LangChain，用有向图把多 Agent 编排表达成可视化的节点+边。这四家都没把自演进作为内置能力。

国内同行里：

- **字节 DeerFlow 2.0**：今年 2/28 开源，3 个月内 GitHub 涨到 6 万 star，强在 sub-agent + Docker sandbox + 持久 memory，定位"超级 Agent"；
- **阿里通义 Lingma IDE + Qoder**：5/15 升级到 Quest 任务工作台，重心在"国产首发任务编程范式"，没主打 multi-agent 协议层；
- **腾讯 CodeBuddy、百度 Comate**：单 Agent 路线为主，没披露 multi-agent 公开方案。

**JiuwenSwarm 在这张牌桌的差异化定位**：

1. **唯一公开把"自演进算法"作为框架核心能力之一**（其他家要么没有，要么停留在 demo）；
2. **唯一同时给出可移植规范（Swarm Skills 描述格式）+ 参考实现（JiuwenSwarm 仓库）+ 共享市场（Hub）三件套**；
3. **背后是华为 2012 + 华为云 MaaS**，对国产模型生态接入是天然路径——README 第一行就写着「Full support for Huawei Cloud MaaS and other mainstream model platforms」。

不是"超越海外"，而是**第一次把同等量级的方案端到桌面上**。这是个明显的台阶。

值得插一段历史观察：2024 年 multi-agent 这个赛道的国内代表作其实是 MetaGPT（DeepWisdom 团队），那时候概念新颖，但工程上离生产可用还有距离。2025 年中字节 DeerFlow 出场，把"超级 Agent"做成 GitHub 现象级项目，但它的重心是单一智能体编排长任务，不是多 Agent 协议层。到 2026 年 5 月，openJiuwen 选了一条 MetaGPT 没走完、DeerFlow 没下场的中间地带——**协议规范 + 参考实现 + 共享市场三件套**。

这三家代表了国内 multi-agent 三种典型路径：

- **MetaGPT 路线**：偏学术研究，强在角色模拟和软件工程任务的复现；
- **DeerFlow 路线**：偏开发者工具，强在工程化封装和开箱即用；
- **JiuwenSwarm 路线**：偏协议层和生态，强在可移植规范和自演进机制。

三条路径不互斥。一个理想的国内 multi-agent 生态最终可能是三者并存，开发者按场景选择。

---

## 三、94.2% / -34.8% / 85% 这三个数字到底硬不硬？

数据是这篇文章里最值得反复琢磨的部分。我把三组数字逐一拆开：

### 3.1 PinchBench 94.2% vs OpenClaw 91.6%

![PinchBench 对比柱状图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/jiuwenswarm-huawei-multi-agent-swarm-2026-05-19/jiuwenswarm-pinchbench-compare.png)

先把口径说透：**PinchBench 不是 openJiuwen 团队出的题**，是 kilo.ai 团队为 OpenClaw 系 Coding Agent 设计的评测套件，开源在 `pinchbench/skill` 仓库。题目内容是真实任务：安排会议、写代码、整理邮件、调研话题、管理文件。

JiuwenSwarm 借用了这份同口径的题去测自己，按量子位、网易、中华网三家口径报告：**94.2%综合得分 vs OpenClaw 91.6%，领先约 2.6 个百分点**。三家媒体表述高度一致，量子位原文是：

> "JiuwenSwarm 以 94.2% 的综合得分斩获业界 SOTA，相比 OpenClaw 提升近 3 个百分点。"
> ——量子位 2026-05-18

**判读建议**：

- 数字来自 openJiuwen 自报，未见第三方独立复现；
- PinchBench 本来设计给 Coding Agent，JiuwenSwarm 是 multi-agent 框架，跑同口径具备参考价值但不完全对位；
- 2.6 个点的领先**有意义但不悬殊**，更像"挤进同一梯队"而不是"碾压"；
- 等社区 fork、第三方实测出来再做最终判断。

### 3.2 token 消耗降低 34.8%

媒体报道一致的口径是"在同任务量下，相比单 Agent 范式平均 token 消耗降低 34.8%"。这个数字背后的机制可以从架构推断：

- multi-agent 分工后，每个子 Agent 上下文窗口里只保留自己负责那段的 context，避免单 Agent 范式里"一个超长 prompt 全塞进去"；
- Swarm Skills 把固定协作模式沉淀成可复用规范，省下重复 prompt；
- 自演进算法淘汰低效路径，减少试错轮次。

机制合理，数字本身需要复现。openJiuwen 没披露具体测试 corpus，对外严谨写法是"按 openJiuwen 社区报告"。

### 3.3 LOCOMO 长期记忆准确率 85%

LOCOMO 是 Meta 出的长期对话记忆评测基准，主要测**跨多个 session 后 Agent 还能不能准确回忆之前对话细节**。85% 在公开榜单上属于第一梯队（GPT-4 系大致 88-90%，Claude 系大致 85-88%）。

JiuwenSwarm 的解决方案是把 Memory 拆成 Conversation Memory / Task Memory / Coding Memory 三层（README 文档目录里能看到），分别走不同的检索策略。

**综合判断三组数字**：

- 都来自 openJiuwen 社区自报，**机制合理，待第三方复现**；
- 选取的评测基准（PinchBench / LOCOMO）都是真实存在的第三方榜单，不是自己出题自己打分；
- 横向看是"挤进同一梯队"，纵向看是"国产 multi-agent 第一次拿出能和海外同框比较的数据"。

不必盲目兴奋，也不该轻易否认。**最合理的读法是把这当成一个里程碑式的样本提交**。

### 3.4 数字之外，更值得关注的是基线选择

横向比一下国内同行的对外报告方式：

- 字节 DeerFlow 2.0 主打"3 个月 GitHub 6 万 star"，是社区热度信号；
- 阿里 Qoder 1.0 主打"代码留存率 +11%、token -40%、对话轮次 -33%"，是产品体验信号；
- JiuwenSwarm 主打 **PinchBench + LOCOMO** 两个独立第三方榜单，是工程严谨信号。

不同的对外口径反映不同的产品阶段判断。JiuwenSwarm 选了一条**最容易被质疑也最容易被验证**的路径——用第三方榜单说话。这条路径的好处是：**任何人都能去复现**。坏处是：**任何人都能去打脸**。

选这条路本身是一种姿态。社区版本上线第一周就把"94.2%"挂出来，意味着团队对工程稳定性有底气。后续 30 天会有人复现，到时候这个数字要么变成行业新基线，要么变成下一轮迭代的起点。两种结果都是健康的开源生态。

---

## 四、国内开发者怎么把它跑起来

这是最实用的部分。从社区披露材料和 README 看，接入路径相当清晰：

### 4.1 三行命令安装

```bash
pip install jiuwenswarm
jiuwenswarm-init    # 首次初始化
jiuwenswarm-start   # 启动服务，默认 http://localhost:5173
```

PIP 包名 `jiuwenswarm`，本机依赖 Python 3.11/3.12/3.13。带 TUI 终端模式可单独装 `jiuwenswarm-tui`。

### 4.2 接国产 + 海外模型

README 第一条特性：**「Full support for Huawei Cloud MaaS and other mainstream model platforms」**——华为云 MaaS 是默认一等公民，其他主流模型平台都支持。意味着：

国内模型：

- **Qwen 系列**（阿里通义）：通过标准 chat completions API 接入；
- **DeepSeek**：API 直连；
- **GLM-4.5 / GLM-4.6**（智谱）：API 直连；
- **Kimi-K2**（月之暗面）：API 直连；
- **盘古大模型**（华为云）：原生支持，路径最短。

四种模式可切换：**PLAN / AGENT / CODE / TEAM**，最后一种就是 multi-agent 模式。

### 4.3 配合 Skill 自演进的工作流

社区给的典型使用方式有四类，按网易科技报道整理：

1. **昇腾算子优化**：多个 Agent 分别担纲算法、编码、性能测试，互相 review；
2. **多专家会诊**：模拟 23 个 AI 医生角色，对同一病例从不同专科角度给意见；
3. **短视频生产**：从脚本到剪辑到字幕的全流程协作；
4. **狼人杀游戏**：人和 Agent 一起玩，验证 HITS / HOTS 两种人机协作模式。

**HITS（Human in the Swarm）**：人作为蜂群成员之一沉浸式参与，和其他 Agent 同等地位；
**HOTS（Human on the Swarm）**：人当指挥官，实时观察整支 Agent 团队的执行状态，可随时介入调度。

这两种模式对应**两种典型工作场景**：HITS 像创作型协作（你和团队一起头脑风暴），HOTS 像运营型监控（你坐镇调度复杂流程）。

### 4.4 渠道集成

README 列了几条原生集成路径：

- **小艺开放平台**：华为手机用户直接通过小艺助手呼叫 JiuwenSwarm；
- **Lark（飞书海外版）**：配置完直接在飞书对话框里和 Swarm 对话；
- **Discord / WhatsApp**：海外渠道也都打通；
- **Web 前端**：默认 `http://localhost:5173` 浏览器直连。

国内开发者最快上手路径：**Lark + Qwen API**，几乎零配置就能跑起来一支自己的 Agent Swarm。

### 4.5 一个具体场景：把日常调研任务交给一支 Swarm

举个能立刻动手的例子。假设你每天要看 20 篇技术博客 + 5 个 GitHub 仓库 + 3 个公众号头条，整理成一份内部周报。这事让单 Agent 干，要么 prompt 太长跑超时，要么遗漏关键信息，要么风格不稳定。

用 JiuwenSwarm 拆成三个 Agent 角色：

- **Scout Agent**：负责抓取 + 第一轮筛选（保留 30%）；
- **Analyst Agent**：对保留下来的内容做深度提炼（每篇 200 字摘要）；
- **Editor Agent**：把所有摘要按"产品 / 工程 / 行业"三栏组织，输出最终周报。

三个 Agent 并行跑，相互之间通过 E2A 协议（README 文档里有详细说明的 Gateway-Agent 请求规范）通信。跑完之后，自演进机制会观察这次执行轨迹：「Scout 的初筛规则有 3 处和 Editor 最终保留的内容冲突」——这条 patch 自动写回 Skill 库。下一次跑同类任务，Scout 的初筛就更准。

这是 Anthropic Swarm 的 handoff 协议、CrewAI 的 role-task 模型都做不到的能力：**让 Agent 团队随着使用次数自我变强**。

---

### 4.6 还有一个细节：原生支持的"小艺"渠道

国内开发者群体里讨论 multi-agent 框架，往往会忽略一个非技术信号：**这套东西要在哪里被普通用户实际用到**。

JiuwenSwarm README 里第二条 Key Features 写着「Native integration with the Xiaoyi Open Platform, enabling Huawei phone users to invoke JiuwenSwarm directly through the Xiaoyi assistant」——小艺是华为手机的语音助手，覆盖国内每年新增的几千万台 HarmonyOS 设备。

这意味着 JiuwenSwarm 的应用场景从一开始就不只是"开发者本地跑着玩"，而是有手机端 + 语音助手端的真实落地路径。社区开发者今天写的一个 Swarm Skill，理论上明天就能被一个用小艺的普通用户调用——这是一条 CrewAI / AutoGen 这种纯框架级项目目前没有的分发优势。

对国内开发者来说，多了一个值得思考的角度：**你想让你的 Agent Swarm 服务谁**。如果是企业内部流程，DeerFlow 或 Qoder 路径合适；如果是面向 C 端轻量场景且需要语音入口，JiuwenSwarm + 小艺路径很有想象空间。

---

## 五、值得开发者长期跟踪的几个点

接下来 1-3 个月这个项目要看几个关键信号：

| 看什么 | 看到什么算"过关" |
|---|---|
| **第三方实测** | 有人独立跑 PinchBench 复现 94.2%，或拿出对比结果 |
| **GitHub 社区活跃度** | star 涨势 + PR 质量 + issue 响应速度（697 star / 121 fork 是个不错的起点） |
| **Skill Hub 内容** | swarmskills.openjiuwen.com 上有多少社区贡献的 Swarm Skill |
| **跨框架移植** | 有没有人把 Swarm Skills 描述格式跑到 CrewAI 或 LangGraph 上 |
| **企业落地案例** | 华为之外的厂商有没有公开宣布生产环境用 JiuwenSwarm |

按 5/18 实查：仓库于 2026-03-05 创建，开源公告 5/18 放出，**697 star、121 fork、Apache-2.0、Python 主语言**——这是个"刚出生但已经有人在围观"的状态。

---

## 六、几个常见疑问与跨阵营答疑

**Q1：和阿里通义 Lingma、字节 Trae 不冲突吗？**
不冲突，根本不在一条赛道。Lingma / Trae 是 IDE + 单 Agent Coding 工具，JiuwenSwarm 是 multi-agent 协作框架。你可以用 Trae 写代码、用 JiuwenSwarm 编排一群 Agent 跑长流程任务，两者互补。

**Q2：华为出的，是不是绑定昇腾/盘古？**
不绑定。README 明确支持「华为云 MaaS 和其他主流模型平台」，Qwen / DeepSeek / GLM / Kimi 都接得上。盘古路径最短是因为华为云原生集成，不代表别的模型路径长。

**Q3：和 Anthropic Skill / Anthropic Swarm 是什么关系？**
JiuwenSwarm 的 Swarm Skills 论文（arXiv 2605.10052）摘要写得很清楚，是"extending Anthropic Skills to multi-agent systems"——可以理解成把 Anthropic Skill 的单 Agent 能力沉淀机制扩展到了多 Agent 协作场景。设计哲学上有承接，技术实现完全独立。

**Q4：自演进算法会不会跑飞？**
EUF 三维评分里 Freshness 的指数半衰期就是为了防止旧 Skill 永远占位，Utilization 防止"自己写自己用"的孤立 Skill。机制上有兜底，但具体表现要等大量使用数据出来。

**Q5：商业化怎么走？**
当前社区版完全开源，Apache-2.0 没有限制。商业化大概率走华为云 MaaS 托管版本+企业定制方向，但目前没公开披露具体节奏。

---

## 七、写在最后

JiuwenSwarm 这次开源最值得关注的不是"超越 OpenClaw 2.6 个点"这种数字游戏，而是**国产 multi-agent 第一次拿出一个有自己设计哲学（Coordination Engineering）、有可移植规范（Swarm Skills）、有完整参考实现（JiuwenSwarm 仓库）、有共享市场（Hub）的完整方案**。

过去两年我们一直在说"国内大模型卷起来了"——卷参数、卷上下文、卷价格。**今天 multi-agent 这条新赛道，国产社区已经能在开局就摆出完整方案，这是上一轮没有过的状态**。

对国内开发者来说，这意味着两件事：

1. 你不用等 OpenAI Swarm 或 Anthropic 出 multi-agent 产品级方案，国内已经有一份可立刻 `pip install` 的开源选择；
2. 你今天写的 Swarm Skill 有可能因为协议层独立性，未来被搬到其他框架去——这不像 LangChain 时代每个框架都要从零写一遍。

下一步看社区接得住接不住。一个开源框架的最终命运不取决于发布日的 PR 通稿，而取决于发布 3 个月后还有多少人在提 issue、提 PR、写 Skill。

把这周日历加一栏，过 30 天再看 JiuwenSwarm 的 GitHub。如果那时候 star 翻倍、Hub 上有几十个社区 Skill、有人实测复现了 PinchBench 分数——这就是一个真正立得住的项目。

如果没有，那它就是又一份"开源公告做得很漂亮"的样本。一个月时间，足够说明问题。

国产 AI 这两年最让人振奋的变化，是越来越多团队愿意把还在打磨的东西先开源出来、接受社区检验，而不是等做到 perfect 才发版。JiuwenSwarm 上线第一周就把第三方榜单分数挂出来，本身就是这种自信的体现。无论最终评测复现结果如何，这种"敢拿出来给所有人审视"的姿态，比任何 PR 通稿都更值得鼓励。

国内 multi-agent 这条赛道的故事才刚开始。看下去就行。

---

*本文来源：openJiuwen 社区官方主页、量子位 5/18 报道（qbitai.com/2026/05/419515.html）、网易科技 5/18 报道、中华网 5/18 报道、JiuwenSwarm 项目仓库 README（截至 5/18，697 star / 121 fork / Apache-2.0）、arXiv 论文 2605.10052v1《Swarm Skills: A Portable, Self-Evolving Multi-Agent System Specification for Coordination Engineering》。*
