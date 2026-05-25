# GitHub 爆火：这份 20 阶段 AI 课程如何用 435 节课“焊死”知识断层？

> ai-daily · 2026 年 5 月 25 日 11:40 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/rohitg00-ai-engineering-from-scratch-micro/head.png)

2026 年 5 月 7 日凌晨，一个 GitHub 仓库的 star 数正在以每小时 300+ 的速度疯涨。仓库名叫 `ai-engineering-from-scratch`，作者 Rohit Ghosh 在 README 开头甩了一组数字：84% 的学生已经在用 AI 工具，但只有 18% 觉得自己能在专业场景下驾驭它们。

这个 gap，他打算用 435 节课、20 个阶段、约 320 小时的学习路径来填平。

我看到这个项目的第一个念头不是“又一个 AI 教程”，而是“终于有人把 AI 教育从拼图模式拉回了脊椎模式”。Ghosh 在文档里写了句很狠的话：大多数 AI 教学材料是散装的——一篇论文解读在这里，一篇 fine-tuning 教程在那里，一个花里胡哨的 agent demo 在另一处。你最后能部署出一个 chatbot，但解释不了它的 loss curve；你能把函数挂到 agent 上，但说不出模型内部的 attention 到底在干什么。

**这不是课程。这是一条从手搓反向传播到多智能体集群的完整产线。**



![20 个阶段从数学基础到多智能体集群的流程塔图，底部是线性代数，顶部是自主系统](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/rohitg00-ai-engineering-from-scratch-micro/schematic-1.png)



## 从“Build It”到“Ship It”：每一节课都是一条微型生产线

这个课程最有意思的设计，是它把每一节课都变成了一个六拍循环。Ghosh 在文档里用了一张流程图：MOTTO（一句话核心思想）→ PROBLEM（具体痛点）→ CONCEPT（图解和直觉）→ BUILD IT（裸写数学，不用框架）→ USE IT（同一件事在 PyTorch / sklearn 里跑一遍）→ SHIP IT（产出 prompt、skill、agent 或 MCP server）。

BUILD IT 和 USE IT 的分割是整个体系的脊柱。你先从 raw math 做到算法，然后再用生产库跑同一个东西。你知道框架在做什么，因为你亲手写过小号版本。

举个例子：Phase 14 的第一节课，agent loop。约 120 行纯 Python，零依赖。你写完之后，这节课的 outputs 文件夹里会多出两个东西——一个可复用的 `skill-agent-loop.md`，可以直接丢进 Claude、Cursor、Codex 或任何能读 SKILL.md 的 agent；另一个是 `prompt-debug-agent.md`，一个 agent 调试器，专门用来分析 agent 运行轨迹里哪一步出了问题。

435 节课，每节课都这么干。到课程结束时，你手里不是一个结业证书，而是一个 435 件可复用制品的工具箱——prompts、skills、agents、MCP servers，全部是你自己从零构建的，你完全理解它们内部的每一层逻辑。



![六拍循环流程图，MOTTO → PROBLEM → CONCEPT → BUILD IT → USE IT → SHIP IT，右侧标注每步产出物](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/rohitg00-ai-engineering-from-scratch-micro/schematic-2.png)



## 四门语言、20 个阶段、一个野心：把 AI 教育的“断层”焊死

这个课程的阶段列表读起来像一份 AI 工程师的全基因组测序。Phase 0 是环境搭建和工具链（12 节课），Phase 1 是数学基础（22 节课，从线性代数直觉一路干到随机过程），Phase 2 是经典机器学习（18 节课），Phase 3 是深度学习核心（13 节课，包括从零写反向传播和自己搭一个 mini 框架）。

然后课程在这里分叉。Phase 4 是计算机视觉（28 节课，从卷积写到 3D Gaussian Splatting 和世界模型），Phase 5 是 NLP（29 节课，从 Word2Vec 写到 chunking 策略和 LLM 评估框架），Phase 6 是语音与音频（17 节课，从波形采样写到 Moshi 流式语音到语音和音频水印），Phase 9 是强化学习（12 节课，从 MDP 写到 RLHF 和 PPO）。

这些分叉最后全部汇入 Phase 7——Transformers 深度解剖（14 节课，包括从零写 self-attention、multi-head attention、RoPE 位置编码、MoE、Flash Attention、KV cache 和 scaling laws）。然后是 Phase 8 生成式 AI（14 节课，从 VAE 写到 flow matching），Phase 10 从零构建 LLM（22 节课，包括 BPE tokenizer、124M 参数 mini GPT 预训练、分布式训练、RLHF、DPO、量化、DeepSeek-V3 架构走读和 NSA 稀疏注意力）。

再往上走，课程进入工程深水区：Phase 11 LLM 工程化（17 节课，从 prompt engineering 到 LangGraph 和 agent 框架 tradeoff），Phase 12 多模态 AI（25 节课，从 ViT patch-token 原理到多模态 agent 和 computer-use），Phase 13 工具与协议（23 节课，MCP 从入门到生产级 OAuth 2.1 鉴权，A2A 协议，OpenTelemetry GenAI），Phase 14 Agent 工程（42 节课，从 agent loop 到生产级 agent workbench，包括 Mem0 混合记忆、Voyager 技能库、SWE-bench 基准、Claude Computer Use 和 prompt injection 防御）。

Phase 15 进入自主系统（22 节课，从 METR 长时域 agent 到 Anthropic RSP v3.0 和 OpenAI 预备框架），Phase 16 多智能体与集群（25 节课，从 FIPA-ACL 通信协议到 agent 经济和声誉系统），Phase 17 基础设施与生产（28 节课，从 vLLM 服务内部机制到多区域 LLM 服务和 FinOps 单位经济学），Phase 18 伦理、安全与对齐（30 节课，从奖励黑客到红队自动化工具 Garak 和前沿安全框架），Phase 19 是综合项目。

四门语言贯穿其中：Python 是主力，TypeScript 出现在多智能体通信和全栈工程部分，Rust 出现在实时音频和边缘推理场景，Julia 出现在线性代数和矩阵运算的高性能做到中。



![20 个阶段的依赖关系有向无环图，标注关键分叉点和汇合点](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/rohitg00-ai-engineering-from-scratch-micro/schematic-3.png)



## 这套课程的出现，踩中了 AI 从业者最深层的焦虑

2025-2026 年的 AI 就业市场正在经历一个奇怪的扭曲：一方面，基础 prompt engineering 岗位的薪资在快速贬值，因为任何一个能读文档的人都可以在两周内上手；另一方面，真正能理解模型内部机制、能设计 agent 架构、能在生产环境解决 KV cache 瓶颈的工程师，薪资溢价反而在拉大。

Ghosh 在 README 里没有明说，但这套课程本质上是在回应一个尖锐的问题：当 AI 工具本身变得越来越易用，人类的竞争力究竟应该锚定在哪里？他的答案是：锚定在“你亲手写过一遍”的那个深度上。

课程内置的 agent skills 也很有意思。它支持 Claude、Cursor、Codex、OpenClaw、Hermes 等 agent 直接读取课程技能文件。两个关键指令：`/find-your-level`（十道题的定位测试，映射你的知识水平到起始阶段，生成个性化路径和时间估算）和 `/check-understanding <phase>`（每个阶段八道题，给出反馈和需要复习的具体课程）。这不是传统的“看完视频点下一集”，而是一个自适应诊断系统。

让我愣神的是 Phase 14 的 workbench 系列（第 31-42 课）。每一课都附带一个 `mission.md`，在 agent 打开完整课程文档之前就给它一个任务简报。这相当于把课程本身变成了一个 agent-native 的体验——你在学 agent 工程的同时，课程内容也在用 agent 的方式和你交互。

Ghosh 在文档末尾写了一句很克制的话：“Free, open source, MIT. Built to run on your own laptop.”没有推销，没有付费墙，没有“加入我们的社区获取完整内容”。435 节课全部开源，MIT 协议，本地可跑。

**AI 教育领域最稀缺的不是知识，是一根能把散装知识点串成脊椎的钢缆。**

这个仓库做到了。至于你能不能坚持 320 小时——那是另一个问题了。

## 参考来源
- https://github.com/rohitg00/ai-engineering-from-scratch

#Learn #Build #Ship #AI #科技
