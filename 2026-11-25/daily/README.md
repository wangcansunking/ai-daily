---
title: Gemini 4 把量子电路开销压到 1/290，AWS 开源 5 款决策模型 | AI 日报 | 2026-11-25
date: '2026-11-25'
weekday: 星期三
slug: ai-daily-2026-11-25
category: ai-daily
description: Gemini 4 改写量子电路，AWS 给智能体配专用决策模型，中国电信公开 285B 权重，附 5 个 GitHub 日榜项目。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/daily.png
batch: morning
track: newsletter
cover_style: magazine-collage
---

# Gemini 4 把量子电路开销压到 1/290，AWS 开源 5 款决策模型 | AI 日报 | 2026-11-25

11 月 25 日早报。今天的头条是谷歌研究团队把 Gemini 4 派去搜索量子电路，AWS 则把智能体里反复出现的选择题交给 5 款开权重模型。国内两条消息也值得放在一起读：2850 亿参数的星辰公开预览权重，1450 亿参数的 Edge0 把一部分专家留在主机内存，瞄准单卡运行。

## 要点速览

- **Google DeepMind**：一段固定点反正弦电路的估算时空成本降约 290 倍。
- **AWS**：Strands Decider v2 从 E2B 到 31B，单次前向返回选项概率。
- **Anthropic**：访谈覆盖 58 家企业，很多团队先用 AI 扩大产出。
- **国内模型**：星辰公开 285B 预览权重，Edge0 的官方单卡演示也把内存开销摆上台面。

## 大厂头条

### 谷歌让 Gemini 4 改量子电路，Lean 把住证明关

Google DeepMind 11 月 23 日介绍了与 Google Quantum AI 的研究。团队用多支 Gemini 4 智能体团队搜索候选电路，再用 Lean 检查它们提交的证明。最抢眼的结果来自固定点反正弦：基线的 T 门数与量子比特数乘积超过 1.08 亿，新方案约 37 万，误差要求也比所引基线更严。

![DeepMind 量子电路优化结果](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/quantum-fig3.png)

*图片来源：Google DeepMind 与 Google Quantum AI 量子算法论文图 3（结果图表），2026-11-16。*

同一研究还把特定并行 Grover 算法的预期查询轮数最多压低 2.57 倍。两项结果的指标不同，离硬件上的运行时间又隔着纠错和编译。现在能复用的是写成规格、程序和数学证明的优化方案。

### AWS 开源 5 款 Gemma 4 决策模型

同一张工单交给谁、要不要升级、该打什么标签？Strands Decider v2 读一遍共享材料，对开发者给出的选项打分。它的五档权重以 Apache 2.0 发布。在 AWS 的六项公开文本基准中，31B 档平均 82.1，上一代最大档为 70.0。

![Strands Decider v2 文本、图像和视频基准](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/aws-bench.png)

*图片来源：Strands Agents v2 发布文图 1（基准图表），2026-11-23。*

AWS 披露的内部工单标注降本 98% 来自上一代。对团队而言，最直接的落点是把需要「写回答」的请求与只需「选去向」的请求分别处理，让大模型把预算花在前一类。

### Anthropic 访谈 58 家公司，员工开始自己写小软件

Anthropic 对 58 家企业客户做了 66 次访谈。研究团队整理出 100 个受访者用 AI 做的软件，其中 80 个接手原先的手工流程，比如更新财务表格或记会议笔记，其余 20 个替换了既有软件。

![受访团队用 AI 开发的软件与作者职能](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/anthropic-apps.png)

*图片来源：Anthropic《Working with AI》（访谈图表），2026-11-23。*

当业务同事也能把自己的表格变成工具，项目的起点就从提需求变成了直接做原型。这份研究记录的是已采用 AI 的客户样本，关于产出与岗位的观察应放在这组样本里理解。

### OpenAI 与 Anthropic 的内部模型面临公开发布争论

Axios 11 月 24 日援引二十多位知情人士称，两家实验室有人对是否适合公开发布更强的内部模型提出疑虑。报道描述的是内部讨论，公司何时推出下一款模型、会向谁开放，仍需等正式消息。

产品团队值得关注的是发布方式：同一模型可以有内部研究、受限接入和公开服务几种去向。公开用户体验取决于实际推出的版本和权限，不能从内部候选直接推定。

## 🇨🇳 国内 AI 观察

### 中国电信放出 285B 星辰预览权重

星辰 Xing4.1-Preview 采用稀疏专家混合架构，总参数 285B，每个 token 激活约 17B，模型卡标注原生 1M token 上下文和 Apache 2.0 许可。这个组合瞄准长材料与智能体编程，17B 描述每步参与计算的规模，部署仍需为整套权重留出空间。

### 145B 的 Edge0，用显存和内存一起跑

无界方舟 11 月 13 日发布的 Edge0-145B 近期获得媒体关注。官方称 RTX 5090 D 24 GB 在 NVFP4 构建下跑出 98.3 token/s 的智能体解码；启用驻留时占用 23.25 GiB 显存与 38.27 GiB 主机内存。速度来自特定任务和配置。本地跑的成本，要把内存与带宽一起算上。

![Edge0-145B 本地对话和编程操作](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/edge0-demo.png)

*图片来源：无界方舟 Edge0-145B 官方演示视频（界面截帧），2026-11-13。*

## 📦 GitHub Trending · 5 个项目

星标为 11 月 25 日北京时间晨间抓取快照。「新增」为 GitHub 日榜当时显示的数字。名次对应各自榜单。

| 项目 | 累计星标／日榜新增 | 榜位 |
|---|---:|---|
| [Pi](https://github.com/earendil-works/pi) | 123,717／+205 | TypeScript 第 17 |
| [NGINX](https://github.com/nginx/nginx) | 33,196／+158 | 全球第 7 |
| [Spec Kit](https://github.com/github/spec-kit) | 151,274／+144 | Python 第 20 |
| [dbx](https://github.com/t8y2/dbx) | 31,322／+108 | Rust 第 4 |
| [AgentScope](https://github.com/agentscope-ai/agentscope) | 36,648／+92 | Python 第 23 |

- **Pi** 把模型接口、智能体循环和终端交互放进同一套工具。想改造自己的终端编码助手，可以从它的扩展机制入手。
- **NGINX** 是 Web 服务器和反向代理的官方源码库，适合顺着配置示例摸到代理、缓存与请求转发的实现。
- **Spec Kit** 把需求、计划和实现任务写成项目资产，适合代码助手经常在同一条业务规则上返工的团队。
- **dbx** 提供跨平台数据库客户端，兼顾桌面、命令行和 MCP。经常在 SQL 与文档型数据库之间切换的人可以用它减少工具切换。
- **AgentScope** 是模块化的智能体应用框架，可以从模型、工具与对话的小示例开始，再把调用组合成业务流程。

一句话总结：今天值得留意的两种提效思路，一种让模型探索更好的答案，一种把每一步只交给足够胜任的模型。

## 来源

- [Google DeepMind 研究公告](https://deepmind.google/blog/finding-faster-quantum-algorithms-with-agents-and-formal-verification/)
- [Google 量子算法论文](https://arxiv.org/abs/2611.09822)
- [Strands Agents v2 发布](https://strandsagents.com/blog/introducing-strands-decider-v2/)
- [Anthropic 企业应用访谈](https://www.anthropic.com/research/working-with-ai)
- [Axios 内部模型报道](https://www.axios.com/2026/11/24/anthropic-openai-unreleased-ai-safety-risks)
- [星辰模型卡](https://huggingface.co/XingChen-AGI/Xing4.1-Preview)
- [无界方舟模型与演示](https://www.autoarkai.com/model-detail/llm-145b)
- [GitHub 全球日榜](https://github.com/trending?since=daily)、[Python 日榜](https://github.com/trending/python?since=daily)、[TypeScript 日榜](https://github.com/trending/typescript?since=daily)、[Rust 日榜](https://github.com/trending/rust?since=daily)
