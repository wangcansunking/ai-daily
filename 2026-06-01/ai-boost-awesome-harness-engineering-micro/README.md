# GitHub 热榜仓库：OpenAI与Anthropic为何总在Harness层翻车？

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/ai-boost-awesome-harness-engineering-micro/head.png)

AI 圈有一个持续三年的奇怪现象：所有人都在卷模型，但真正决定 agent 能不能干活的东西，一直没人系统整理。

2024 年 OpenAI 发布 Codex 时，工程师们发现让 AI 写代码的难点不是模型不够聪明，而是你给它什么上下文、怎么传工具结果、何时触发验证。Anthropic 的 Claude Code 团队后来在 2026 年 4 月的一篇 postmortem 里承认，三个看起来无关痛痒的 harness 层改动——默认推理强度下调、缓存 bug 导致思考历史丢失、一个过于激进的控制输出长度的 system prompt——叠加起来就把 Claude Code 的代码质量打崩了。模型没变，变的只是包裹模型的脚手架。

2026 年 5 月，GitHub 上一个叫 `awesome-harness-engineering` 的仓库开始被疯传。它不做模型，不卖 API，只是一个精心编排的资源列表，把 OpenAI、Anthropic、Google、Meta、Microsoft、Red Hat、LangChain 过去两年散落在博客、论文、postmortem 里的 harness 工程实践全部收拢到一个框架下。目前支持 9 种语言，覆盖从 agent loop 结构到上下文压缩、从工具设计到权限系统、从人机协同到 CI 集成验证的完整链条。

**模型是引擎，harness 才是整辆车——而过去两年大部分团队只换了引擎，车架还是那辆破三轮。**



![Harness 工程全景图，模型层 vs Harness 层分层示意，标注 agent loop / 工具接口 / 上下文管理 / 记忆系统 / 沙箱 / 验证六个模块](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/ai-boost-awesome-harness-engineering-micro/schematic-1.png)



## 一个仓库，把“模型不行”的锅重新扣回了工程师头上

这个仓库的组织方式本身就是一个宣言。它把“Harness Engineering”定义为一个独立学科：设计围绕 AI agent 的脚手架——上下文交付、工具接口、规划产物、验证循环、记忆系统和沙箱——这些东西共同决定 agent 在真实任务中是成功还是失败。仓库的第一句话就划清界限：“This list focuses on the harness, not the model.”

最让我愣神的是它收录了一篇 LangChain 的案例研究：他们的 coding agent 在 Terminal Bench 2.0 上从第 30 名跳到前 5 名，没换模型，只改了 harness——加了结构化验证循环、上下文注入（目录地图 + 时间预算警告）、循环检测中间件，以及一个叫“reasoning sandwich”的模式（在规划和验证阶段集中投入最大推理算力）。这是目前公开文献里最干净的证据：harness 设计是主要性能杠杆，模型能力反而是次要变量。

仓库里另一条更狠的数据来自 Microsoft。他们的 Azure SRE Agent 已经自主处理了 35000+ 个生产事故，把 Azure App Service 的平均缓解时间从 40.5 小时压缩到 3 分钟。但真正值得读的是他们的上下文工程复盘：团队最初给 agent 配了 100+ 个专用工具和一套冗长的 prescriptive prompt，效果拉胯。后来他们把一切——源代码、runbook、查询 schema、历史调查笔记——全部暴露为文件，让 agent 只用 `read_file`、`grep`、`find` 和 `shell` 命令自己翻。结果“Intent Met”得分从 45% 跳到 75%，尤其是在从未见过的新类型事故上。换句话说，**给 agent 一个文件系统比给它 100 个精心设计的 API 更管用**——因为模型在训练时已经学会了怎么用文件系统，而你的 API 对它来说是外语。



![Microsoft Azure SRE Agent 上下文工程改造前后对比，左：100+ 专用工具 + 长 prompt，右：文件系统暴露 + 4 个基础命令，Intent Met 从 45% → 75%](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/ai-boost-awesome-harness-engineering-micro/schematic-2.png)



## Anthropic 的 postmortem 和 Meta 的 6 小时断点续传

仓库里收录的 Anthropic 2026 年 4 月那篇 Claude Code 质量报告值得单独拿出来说。团队追踪了一次 Claude Code 代码质量退化事故，最终定位到三个独立的 harness 层改动：一个默认推理强度下调、一个缓存优化 bug 导致过期的 session 持续丢弃思考历史、一个过于激进的限制输出长度的 system prompt。三个改动各自看都不致命，叠加在一起就产生了肉眼可见的 agent 退化。这篇 postmortem 现在被仓库归在“Foundations”分类下，和 OpenAI 的 Codex agent loop 拆解、Anthropic 的 Building Effective Agents 并列——不是因为它的技术深度，而是因为它用一次真实事故证明了：**harness 层的 prompt 措辞、缓存 header、默认参数这些“小事”，会在 agent 行为上产生复合效应，而诊断这些问题需要一套与模型调试完全不同的方法论。**

另一篇让我觉得必须提的是 Meta 的 Ranking Engineer Agent（REA）。它处理的是多日 ML pipeline 自动化任务，单个 turn 可能超过模型上下文限制，但整体 pipeline 必须跨天保持连贯。Meta 的方案是“hibernate-and-wake checkpointing”——让 agent 在 6 小时任务中断后能从断点恢复而不丢失上下文。这个设计直接对应到仓库中“Agent Loop”分类下的一个核心问题：你的 agent loop 是 stateless 还是 stateful？LangChain 那篇“Agents Learn Their Runtime”论文给出了一个警告——如果你在部署时的状态持久化模式与模型训练时的语义不匹配，要么出现 80% 的变量丢失错误（模型期望持久化的状态你没存），要么产生 3.5 倍的 token 开销（模型重复计算它以为已经存在的状态）。状态持久化不是一个“可以随便选的运行时选项”，它是模型已经学会的语义，你必须在部署时遵守。



![Agent loop 状态持久化模式对比，stateless vs stateful vs hibernate-and-wake，标注 token 开销和错误率差异](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/ai-boost-awesome-harness-engineering-micro/schematic-3.png)



仓库在“Context Delivery & Compaction”分类下收了一个让我觉得特别有意思的项目：Token Savior。它不压缩上下文，而是把代码库按符号（函数、类、调用图）建索引，让 agent 用指针导航而不是读取整个文件。结果活跃 token 减少 77%，benchmark 耗时降低 76%。配合 Anthropic 官方文档里那条数据——在 100 轮 web search 评测中，server-side compaction 减少了 84% 的 token 消耗——整个上下文管理的方向已经很清楚了：**上下文压力不是压缩问题，是导航问题。** 你不需要把 20 万行代码压成 2 万行塞进窗口，你需要让 agent 知道哪 200 行是现在该看的。

仓库本身不做任何产品，但它做了一件这个行业一直缺的事：把“模型不行”的锅重新扣回了工程师头上。过去两年，每次 agent 翻车，团队的第一反应是换模型——从 GPT-4 换 Claude，从 Claude 换 Gemini。但这个仓库用 60+ 篇论文和工程报告堆出了一个结论：你的 agent 大概率不是模型不行，是你给它的脚手架是豆腐渣工程。而脚手架的问题，换模型解决不了。

## 参考来源

- https://github.com/ai-boost/awesome-harness-engineering

#Awesome #AI #MCP #科技
