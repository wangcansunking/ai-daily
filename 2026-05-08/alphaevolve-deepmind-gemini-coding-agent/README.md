---
title: "会自己进化的代码 agent：DeepMind AlphaEvolve"
date: 2026-05-08
slug: alphaevolve-deepmind-gemini-coding-agent
type: deep-dive
track: overseas-hot
cover: alphaevolve-deepmind-gemini-coding-agent.png
description: "Google DeepMind 5 月 7 日发布 AlphaEvolve 跨域成果回顾：Gemini Flash + Pro 双模型 + 进化算法 + 自动评测器，已经在 Borg 调度、TPU 设计、4×4 矩阵乘、Erdős 数学问题、Klarna 训练加速、FM Logistic 路径优化等场景拿出真实数字。HN 5 月 7 日 197 pts / 76 评论。"
tags:
  - AlphaEvolve
  - DeepMind
  - Gemini
  - Coding Agent
  - 进化算法
  - AI for Science
---
# 会自己进化的代码 agent：DeepMind AlphaEvolve

![会自己进化的代码 agent：DeepMind AlphaEvolve](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/alphaevolve-deepmind-gemini-coding-agent/alphaevolve-deepmind-gemini-coding-agent.png)

## 一、5 月 7 日 DeepMind 在博客上发了什么

北京时间 2026 年 5 月 7 日晚，Google DeepMind（美国）在官方博客挂出 **《AlphaEvolve: Gemini-powered coding agent scaling impact across fields》**——一篇横跨 8 个学科领域的成果回顾，把过去整整一年里 AlphaEvolve 在数学、芯片、基础设施、生物、能源、广告、物流、量子计算上的真实战绩列了一长串。HN 当天 197 分、76 条评论挂在首页第二位，标题被简化成 "AlphaEvolve: scaling impact across fields"。

往前回溯一年：**2025 年 5 月 14 日**，DeepMind 第一次发布 AlphaEvolve，一篇博客 + 一份 50 多页的 white paper，随后于 6 月上 arXiv 的 [《AlphaEvolve: A coding agent for scientific and algorithmic discovery》（2506.13131）](https://arxiv.org/abs/2506.13131)。当时圈内已经震动一次——它把 1969 年 Strassen 的 4×4 复值矩阵乘法世界纪录直接刷掉，56 年来第一次有算法用更少的标量乘法（48 vs. 49）做完同一件事。

把这两个时点放一起，结论就清楚了：**编码 agent 已经不再只是「写代码」了，它在科学问题上自演化求解**。这是 agent 工程范式的一次静悄悄的跃升。

> 论点先放这里：AlphaEvolve 把"LLM 出主意 + 自动评测器打分 + 进化算法挑下一代"做成了一条工业化产线，跑出了一批跨域的真实数字（Borg 0.7%、Gemini 训练 1%、FlashAttention 32.5%、Klarna 2×、FM Logistic 10.4%、Schrödinger 4×）。和 Claude Code、OpenAI o3 这些"对话式编码 agent"是两个物种——一个解定义良好的硬问题，一个跑业务流程的软任务。国产同行（DeepSeek、千问、Kimi、智谱）目前还集中在第二类，第一类的位置空出来了。

![AlphaEvolve 进化 loop 流程图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/alphaevolve-deepmind-gemini-coding-agent/alphaevolve-evolution-loop.png)

## 二、AlphaEvolve 是什么：进化算法 + Gemini + 自动评测器

AlphaEvolve 的工程结构出奇地朴素——朴素到一篇本科算法课的论文就能讲清，但每一块拼起来威力惊人。

**三个核心组件**：

1. **LLM ensemble（双模型）**——Gemini Flash 跑广度，每轮一次性丢出一大堆候选改写，便宜、快；Gemini Pro 跑深度，挑出值得继续探索的方向，给出更精细的建议。最早一版用的是 Gemini 2.0 Flash + 2.0 Pro 的组合，2026 年这一版升到 Gemini 2.5 系列。
2. **Automated evaluator（自动评测器）**——这是关键。每个问题必须能写出一段确定性的 grader 代码，对候选方案打分。打不出 grader 的问题（写营销文案、设计 UI），AlphaEvolve 一律不接。
3. **Evolutionary database（进化数据库）**——把所有跑过的程序连同分数存进去，下一轮 prompt 时把高分样本和低分对照样本一起喂给 LLM，让它"看着前辈进化"。

**一轮 loop**：从程序数据库里取若干高分样本 + 低分样本 → 拼 prompt 给 LLM → LLM 输出代码改写 → grader 跑分 → 入库。一晚上跑几万轮，"代码"就在 fitness 曲面上爬山。

```
[Program DB] ──> [Prompt Sampler] ──> [Gemini Flash + Pro]
      ↑                                       │
      │                                       ▼
[Evaluator Pool] <────────────── [Code Candidates]
```

**和 FunSearch（DeepMind 2023）的区别**：FunSearch 只能改一个 Python 函数体；AlphaEvolve 能改一个 PR 大小的整文件甚至跨文件 diff，问题表达力翻几个数量级。

**和 Claude Code、Cursor 这种 agent 的区别**：后者是"人类提需求 → agent 一次性做完"，没有 fitness function，没有大规模并行候选。AlphaEvolve 是"人类提 grader → agent 自己跟自己进化"，回合数是几万级。

社区已经有开源对位了——**OpenEvolve**，Hugging Face 上 codelion（Asankhaya Sharma）维护，是首个公开的 AlphaEvolve 复刻。它已经被独立用户跑出真实数字：Apple Silicon 上 GPU kernel 解码速度提升 21.8%，长上下文 transformer attention 提升 36.7%。这两个数字侧面验证了 AlphaEvolve 范式的可复现性。

![AlphaEvolve 架构图：Gemini Flash + Pro + 评测器](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/alphaevolve-deepmind-gemini-coding-agent/alphaevolve-architecture-diagram.png)

## 三、跨域成果实据：每条都带数字

5 月 7 日这篇博客的密度高得反常——8 个领域、十几个具体数字。挑硬核的过一遍。

### 3.1 Google 内部基础设施（已上线一年以上）

- **Borg 集群调度**：AlphaEvolve 找出新的 task scheduling 启发式，在 Google 全球数据中心**回收 0.7% 的全球计算资源**——已在生产环境跑超过一年。这个 0.7% 听上去小，但 Google 全球计算量的 0.7% 是 N 万张 TPU 的电费和容量。
- **TPU 算术电路 Verilog 改写**：AlphaEvolve 重写了一个矩阵乘法关键电路里的 Verilog，去掉了不必要的位宽，**已经被集成进下一代 TPU**。
- **Gemini 训练 kernel 加速 23%**：AlphaEvolve 把 Gemini 自己用的一个核心 kernel 拆分方法换了，**单个 kernel 提速 23%**，Gemini 整体训练时间下降 1%。
- **FlashAttention 提速 32.5%**：在 Transformer 注意力实现上拿到 **32.5% 的最大加速**。

这一组数字的可怕之处在于"用 AI 优化 AI"形成正反馈——AlphaEvolve 让 Gemini 训得更快，更快的 Gemini 又来让 AlphaEvolve 更聪明。

### 3.2 数学：Strassen 56 年纪录被破

- **4×4 复值矩阵乘法**：48 个标量乘法（vs. Strassen 1969 的 49），**56 年首次推进**。MaplePrimes 上的独立验证已经走完。
- **14 个矩阵乘法算法**改进了 state of the art。
- **50+ 数学开放问题**测试：约 **75% 重现已知最优解**，**20% 给出新的更优结果**。
- **11 维 kissing number（亲吻数）问题**：找到 **593 个外切球**的新构型，刷新下界。
- 与陶哲轩（Terence Tao）合作攻克若干 Erdős 问题——其中一类涉及**最小重叠（Minimum Overlap）问题**。

### 3.3 2026 年新增的商业落地数字

DeepMind 5 月 7 日博客明确点名的合作方与具体战绩：

- **Klarna**（瑞典金融科技）：**Transformer 训练速度翻倍**。
- **FM Logistic**（法国物流巨头）：**路径效率提升 10.4%**，**每年节省 15,000 公里以上**驾驶里程。
- **Substrate**（美国半导体光刻软件）：光刻仿真**多倍 runtime 提速**。
- **WPP**（英国广告集团）：广告投放优化**精度提升 10%**。
- **Schrödinger**（美国计算化学）：**MLFF（机器学习力场）训练 + 推理 4× 加速**。

### 3.4 公共领域

- **基因组学（DeepConsensus + PacBio）**：变异检出错误**降低 30%**。
- **电网优化（AC Optimal Power Flow）**：可行解占比从 14% 提到 **88% 以上**。
- **Spanner LSM-tree 写放大**：**降低 20%**。
- **地球科学（灾害风险预测）**：跨 20 个类别**精度提升 5%**。
- **量子电路（Willow 处理器）**：错误率较常规基线**降低 10×**。
- **编译器存储优化**：软件存储占用**降低 9%**。
- **缓存替换策略**：人类研究月级才能找到的方案，AlphaEvolve **两天给出**。

![AlphaEvolve 跨域成果网格图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/alphaevolve-deepmind-gemini-coding-agent/alphaevolve-results-grid.png)

把这些数字罗列在一起，AlphaEvolve 干的活其实是同一件事——**把人类工程师"差不多就行"的局部最优，往后再推一格**。每一格 0.5% 到 30% 不等，加总就是巨型数字。

## 四、和 AlphaCode / AlphaProof 的关系：Alpha 系列的第三块拼图

DeepMind 的 Alpha 系列不是孤立产品，每一块都对应"代码 / 证明 / 算法发现"中的一类问题。

| 系列 | 发布时点 | 解决什么问题 | 用什么 |
|---|---|---|---|
| **AlphaGo / AlphaZero** | 2016–2018 | 棋类博弈 | MCTS + 自博弈 RL |
| **AlphaFold 1/2/3** | 2018–2024 | 蛋白质结构预测 | 注意力 + 几何深度学习 |
| **AlphaCode** | 2022 | 竞赛编程题 | LLM + 大规模采样 + 过滤 |
| **AlphaProof / AlphaGeometry** | 2024 | IMO 级数学证明 | LLM + Lean 形式验证 |
| **FunSearch** | 2023 | 单函数级数学发现 | LLM + 进化搜索 |
| **AlphaEvolve** | 2025–2026 | 跨域算法发现 + 工程优化 | LLM + 自动评测器 + 进化 |

AlphaEvolve 是 FunSearch 的"工程化大表哥"——把单函数搜索升级成大代码库 diff 搜索。它和 AlphaProof 的分工也很清楚：AlphaProof 走 Lean 形式系统，问"这个定理对不对"；AlphaEvolve 走自动评测器，问"这个候选解有多快/多优"。

跟 AlphaCode 也不重叠：AlphaCode 是"一次性写出能过样例的程序"，没进化、没迭代；AlphaEvolve 是"反复改一个程序往更优推"，没有"做完"这个状态。

![DeepMind Alpha 系列对照表](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/alphaevolve-deepmind-gemini-coding-agent/alphaevolve-vs-alphacode-vs-alphaproof.png)

## 五、和 Claude / OpenAI 编码 agent 的范式差异

HN 197 pts 帖子的高赞评论里有一条说得挺到位——大意是这一年从"AI 取代程序员"到"AI 帮程序员"，再到"AI 写代码、另一个 AI 审代码"。这句话其实没说全。AlphaEvolve 代表的是更靠后的一档——**AI 写代码，AI 给打分，AI 决定下一代往哪进化，循环几万次**。

把当下三种主流编码 agent 摆在一张表上比一比，差异就出来了：

| 维度 | AlphaEvolve（DeepMind） | Claude Code（Anthropic） | OpenAI o3 / Codex（OpenAI） |
|---|---|---|---|
| **任务输入** | 问题 + grader（评测函数） | 自然语言需求 + 代码库 | 自然语言需求 / IDE 上下文 |
| **成功标准** | grader 打分越高越好 | 跑通测试 / 满足 rubric | 跑通测试 / 用户满意 |
| **回合数** | 几万到几十万 | 几次到几十次 | 几次到几十次 |
| **并行度** | 大规模并行候选 | 串行 + 子 agent | 串行 + 工具调用 |
| **适合问题** | 优化空间清晰、可量化 | 业务代码、bug 修复、重构 | 业务代码、IDE 任务 |
| **不适合** | 需求模糊、目标主观 | 需要无监督探索的科研问题 | 同左 |
| **对应学术祖先** | FunSearch / 进化搜索 | InstructGPT / RLHF 工具调用 | 同 Claude Code |
| **是否对外开放** | 不开源、Google Cloud 早期访问 | API + Claude Code CLI | API + Codex / IDE 插件 |

HN 帖子里另一条高赞评论说得很到位：**Claude 在"实现已知算法"上很顺手、但在"发现新算法"上偶尔会偷懒走捷径**。这恰好是 AlphaEvolve 的反面——AlphaEvolve 不知道什么是"已知算法"，它只知道 grader 给的分数，所以它不会走捷径，因为捷径会被 grader 抓出来。

帖子里点赞最高的那条评论讲得也清楚：**foundation model 在"定义极清晰的问题空间"里特别能打**，矩阵优化、芯片设计、kernel 调优都是这一类。Claude Code 和 o3 那种处理"业务模糊性"的活，AlphaEvolve 反而做不来——你写不出 grader，它就转不起来。

所以这不是"谁取代谁"的事，是**两个物种**：硬问题给 AlphaEvolve，软任务给 Claude / o3。

![AlphaEvolve vs Claude Code vs o3 对照表](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/alphaevolve-deepmind-gemini-coding-agent/alphaevolve-vs-claude-vs-o3.png)

## 六、国产编码 agent 的对位：路线分叉点已现

这块得诚实讲。截至 2026 年 5 月，国内主流的"会写代码的 AI"还是集中在第二类——对话式编码助手，往 agent 方向延伸。

- **千问 Qwen3.6 + Qwen Coder（阿里）**：4 月 24 日发的 Qwen3.6-27B 把多模态 + 编码能力打到接近闭源前沿。它走的是 Claude Code / Cursor 那一档——上下文长、工具调用准、可恢复，直接服务开发者日常。没有 grader-driven 的进化 loop。
- **DeepSeek V4（深度求索）**：定位已经从 chat 模型转成 agent 模型，官方明确优化 Claude Code、OpenClaw、OpenCode 几个场景。同样是对话式 agent 路线。
- **Kimi K2.6（月之暗面）**：偏长文本 + agentic workflow，对接的也是日常开发任务。
- **智谱 AutoGLM / GLM 5.1**：多 agent 编排上有自己的产品，但同样不是 grader-driven 的搜索范式。

**那国产有没有跟 AlphaEvolve 同物种的工作？** 答：有同方向探索，但还没有公开的工业级落地。

- 学术侧，国内多家高校已经在跟 FunSearch / AlphaEvolve 范式——**清华、中科院计算所、北大**都有团队跑过类似的 LLM + 进化搜索小实验，但成果基本停在论文阶段，没有 Borg 0.7% 这种量级的内部生产数字。
- 工业侧，**字节豆包大模型团队**和**阿里达摩院**内部有类似工具用于自家 kernel 优化，但都没公开披露。
- 开源侧，OpenEvolve 已经被国内开发者拿来跑过本地实验。Hugging Face 上 codelion 的 OpenEvolve 国内 fork 数量在过去半年明显抬头。

**真实的差距在哪**？两块：第一块是**"敢不敢把 AI 接到自家最关键的基础设施上"**——Google 让 AlphaEvolve 改的是 Borg 调度器和 TPU Verilog，这是公司命脉。国内大厂内部不是技术上不行，是流程审批上还没走到这一步。第二块是**算力和持续投入**——AlphaEvolve 一晚上烧的 token 量惊人，必须有 Gemini 这种自家模型 + 自家 TPU 的全栈，外面买 API 跑这种 loop 不划算。这一点国内字节、阿里、深度求索都已经具备。

**所以这是范式扩散的窗口期，不是关门期**。FunSearch 论文 2023 年底，AlphaEvolve 第一版 2025 年 5 月——这中间一年半，是工程化的窗口。从 AlphaEvolve 第一版到今天的跨域成果，又是一年。国产团队现在追，**还在合理时间窗内**。

## 七、三个范式启发：grader、双模型分工、进化式 agent

**启发一：grader 是核心生产力**。AlphaEvolve 的全部威力来自一件小事——能给候选解打出确定性分数。这不是"再训一个奖励模型"那种事，是**回到 90 年代遗传算法那套思路**：定义清楚目标函数。在哪些问题上你能写出 grader？数据库索引选择、SQL 计划、分布式 job 调度、CDN 路由、推荐系统的召回策略——这些都是中国互联网公司每天在跑的生产问题，**每一个都有自己的 fitness function**，过去靠人调参、靠 A/B test 慢慢爬。AlphaEvolve 范式说：把这些 grader 接到 LLM 进化 loop 上，让模型一晚上替你跑十万轮。

**启发二：双模型分工值得照搬**。Gemini Flash 出量、Gemini Pro 把关，这套结构在国内完全可以用 **Qwen Flash + Qwen Max、DeepSeek V4 + DeepSeek-R1、Kimi K2.6 fast + slow** 任意组合复现。OpenEvolve 已经把这件事跑通了，国内开发者拉下来改 endpoint 就能用。

**启发三：从"对话式 agent"到"进化式 agent"是结构性升级**。今天国内 90% 的 AI 编码工具还停在"我说一句它写一段"，这是个完全没有 fitness 反馈的开环系统。**进化式 agent 是闭环**——它的每一行代码都有客观分数。这个范式如果在国内大厂内部基础设施场景跑通一次，会带来 0.5% 到 30% 不等的真金白银节约——参考 Google Borg 的 0.7%。

最后一句话讲完：5 月 7 日 DeepMind 这篇博客真正想说的，不是"AI 又破纪录了"，而是"**自动评测器 + 进化搜索 + 大模型，是一条已经被工业化验证的产线**"。窗口在这里，范式在那里，谁先把自家产线接上去，谁就拿到下一档的工程红利。中国 AI 工程师现在站的位置，恰好赶得上。
