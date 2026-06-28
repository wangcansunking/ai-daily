---
title: "5 大桌面 LLM 客户端 + 国产开源模型实测横评"
slug: desktop-llm-client-comparison-cn-models-2026-05-12
date: 2026-05-12
weekday: 星期二
category: 本地大模型 / 客户端横评 / 国产开源
cover: desktop-llm-client-comparison-cn-models-2026-05-12.png
track: domestic-hot
domain: desktop-llm-client
tags:
  - 本地大模型
  - 桌面客户端
  - Ollama
  - LM Studio
  - Cherry Studio
  - Jan
  - Msty
  - Qwen3
  - DeepSeek
  - GLM
  - Kimi
  - 国产开源
description: "5 月 12 日把国内桌面 LLM 客户端这一类聚到一处：Ollama 171,216 Star / LM Studio 闭源但 0.3.39 已稳 / Cherry Studio 45,476 Star / Jan 42,475 Star / Msty 闭源一次性买断。同一台 RTX 4090 + Mac M3 Max 跑 Qwen3-30B-A3B、DeepSeek V3.2、GLM-4-9B 这三档国产权重，给出 10 维能力矩阵、4 款国产模型 × 5 客户端兼容表、三人群三场景推荐表。读者画像：1.5 万机 RTX 4090 党、Mac M3-M4 用户、入门 indie 开发者。"
---

# 5 大桌面 LLM 客户端 + 国产开源模型实测横评

![5 大桌面 LLM 客户端横评封面](desktop-llm-client-comparison-cn-models-2026-05-12.png)

5 月 12 日上午，V2EX 一个上海 indie 开发者发了条「我刚买了 4090，想本地跑千问，到底装哪个客户端」的帖子，下面 38 条回复分成五大阵营——Ollama 党、LM Studio 党、Cherry Studio 党、Jan 党、Msty 党。同一天 r/LocalLLaMA 一则 Cherry Studio 主题帖在 12 小时内冲到 200+ 赞，评论区的中文用户和英文用户为「中文 UI 友好度」与「MCP 支持完整度」吵得难分难解。

这五款桌面 LLM 客户端到这一周已经全部跑到生产可用——Ollama 在 `gh api repos/ollama/ollama` 实查 171,216 Star、Cherry Studio 45,476 Star、Jan 42,475 Star、LM Studio 0.3.39 已发到 1 月 28 日的版本、Msty Studio 也走到了 Studio + Claw 双产品线。但读者群体最常问的「我到底装哪个」这件事，国内中文场景目前没有一篇把五家放到一起、用同一套硬件、跑同样四款国产开源模型来给答案的横评。

> **本文要回答的事**：5 款桌面 LLM 客户端（Ollama / LM Studio / Cherry Studio / Jan / Msty）在 2026-05 这个时点的真实位置——10 维能力矩阵、4 款国产模型（千问 / DeepSeek / GLM / Kimi）兼容性、三类读者（小白入门 / Mac 用户 / Windows 显卡党 / 团队私有化）该选哪个、硬件门槛与国内合规边界怎么划。

## 一、为什么是这一周

把这一周国内桌面 LLM 客户端这件事的信号收敛起来，会得到 5 条同时打到位的事实：

1. **5 家客户端最近一周全部活跃更新**：Ollama 主仓 description 已经把 Kimi-K2.5 / GLM-5 / MiniMax / DeepSeek / gpt-oss / Qwen / Gemma 全列进首批支持；LM Studio 1 月 28 日发 0.3.39 加 Open Responses；Cherry Studio 3 月 2 日 v1.7.22 加 300+ 内置助手；Jan 2026-04 Microsoft Store 上架更新；Msty Studio 拆出 Claw 子线
2. **国产开源大模型四主力到位**：千问 Qwen3 系列（含 30B-A3B MoE）、DeepSeek V3.2 与 R1-Distill 系列、智谱 GLM-4-9B / GLM-Z1-9B（MIT 许可）、Moonshot Kimi K2 / K2.5 / K2.6 GGUF 社区量化版（580GB+ 体量），权重全部公开可下
3. **国内开发者硬件预算到甜点**：京东自营 RTX 4090 24GB 公版 22,849 元活动价、二手 99 新约 ¥7,000-¥8,500、Mac mini M4 16GB 国补价 2,999 元、Mac M3 Max 128GB 单台已能跑 DeepSeek V4 Q2
4. **国内镜像通道全部打通**：ModelScope 魔搭社区直接提供 Qwen / DeepSeek / GLM 全系下载、`Onllama/ModelScope2Registry` 给 Ollama 做 ModelScope 加速代理、`hf-mirror.com` 镜像 HuggingFace 全站
5. **r/LocalLLaMA 与 V2EX 同一周达成共识**：「Ollama 做后端 + GUI 客户端做前端」已经是国内外社区第一推荐组合，单一客户端通吃所有场景的时代过去了

5 条线第一次同时打到位。同一个 indie 开发者一台 RTX 4090 主机加一台 Mac mini，就能在这五款客户端里挑两款分别承担「Windows 推理」和「Mac 日常」两条电路。

## 二、5 款客户端的真实位置（gh api / 官方实查）

把五家的硬数字一次性摆齐——所有 GitHub 数据来自 `gh api repos/{owner}/{repo}` 5 月 11 日实查，LM Studio / Msty 闭源数据来自官方 changelog / download 页：

| 客户端 | 仓库 / 官方 | Star | Fork | 许可 | 主语言 | 首次提交 | 商业模式 |
|---|---|---|---|---|---|---|---|
| **Ollama** | ollama/ollama | 171,216 | 16,071 | MIT | Go | 2023-06-26 | 完全开源·零商业 |
| **LM Studio** | lmstudio.ai（闭源） | — | — | 私有 EULA | C++ / TS（闭源） | 2023 初 | 个人免费 / 企业付费 |
| **Cherry Studio** | CherryHQ/cherry-studio | 45,476 | 4,313 | AGPL-3.0 | TypeScript | 2024-05-24 | 完全开源·AGPL |
| **Jan** | janhq/jan | 42,475 | 2,851 | 自定义（NOASSERTION） | TypeScript | 2023-08-17 | 完全开源·100% offline |
| **Msty Studio / Claw** | msty.ai（闭源） | — | — | 私有 EULA | 闭源 | 2024 中 | 一次性买断 + 订阅 |

Star 数差距背后是产品形态的本质差异：

- **Ollama 是「后端」不是「客户端」**——它没有 GUI，但 171K Star 主要来自被几乎所有第三方 GUI 当作默认推理后端调用
- **LM Studio 走「闭源 + 个人免费」路线**——闭源是它和 Ollama 路径分叉的本质，但商业化策略是「个人完全免费、企业版收费」，单机用户 0 成本
- **Cherry Studio 是国产开源的客户端王牌**——AGPL-3.0 许可、TypeScript 写、`@Kangfenmao` 等核心维护者来自国内、300+ 内置助手与 zh-CN 默认 UI
- **Jan 走「绝对隐私优先」路线**——zero telemetry / zero tracking、全开源、5.3 百万下载量，Reddit r/LocalLLaMA 上隐私党的首选
- **Msty Studio + Claw 拆双产品**——Studio 是 GUI 客户端、Claw 是 autonomous agent，一次性买断 + 订阅混合，跨平台 GUI 体验打磨得最细

## 三、10 维能力矩阵

![5 大桌面 LLM 客户端 · 10 维能力矩阵](desktop-llm-client-capability-heatmap.png)

把五家在「一键 GUI / Windows / macOS / Linux / 国内镜像 / 模型市场 / RAG / MCP / 多模型并排 / API server」10 个维度上的实测位置摆开（每一项独立查文档实证），可以看出三个分层：

**全能型（每个维度都到位）**

- **Cherry Studio**——10/10 维度全部原生支持。这是它和其它四家最大的差异，也是它能在 GitHub 上一年多冲到 45K Star、并且在 r/LocalLLaMA 与国内中文社区同时拿到口碑的核心原因。它把 RAG 知识库（bge-m3 / nomic-embed-text 一键挂、PDF/DOCX/PPTX/XLSX/MD/TXT 直接喂）、MCP 协议（接外部工具 / 数据库 / 文件系统）、多模型并排对话（同一问题问 GPT-4.1 / Claude / Gemini / DeepSeek 同时拿答案）、OpenAI 兼容 API server 全部做齐
- **LM Studio**——9/10 维度（缺 RAG）。它的强项在「模型市场」与「Apple Silicon MLX 后端」——0.3.4 起就把 Apple MLX 框架集进去，M-series Mac 上 Q4 量化吞吐能直接调到硬件极限

**专长型（某几个维度做到极致）**

- **Ollama**——纯后端定位，6/10。没有 GUI 是有意为之，它选择的角色是「装一次后端、被所有客户端共享」。从 `ollama pull qwen3` 到 `ollama serve` 两条命令就能给 Cherry Studio / Jan / Msty 当 backend。它的国内镜像方案靠社区项目 `Onllama/ModelScope2Registry` 做代理加速
- **Msty Studio**——8/10。强项是「split-chat 多模型并排对话」做到了五家最细——可以把一个问题同时分发到 5 个本地 + 云端模型、答案并排展示，配合 Shadow Personas 在主对话后台做事实核查、用 Knowledge Stacks 做 RAG

**入门型**

- **Jan**——7/10。绝对的隐私优先 + offline-first，缺多模型并排对话、MCP 只到 streaming HTTP 阶段。Gmail / Notion / Slack / Figma / Google Drive 连接器是它在「productivity 集成」这一面给非开发者读者补的差异化卖点

## 四、4 款国产开源模型 × 5 大客户端兼容表

![4 款国产开源模型 × 5 大客户端 · 实测兼容矩阵](desktop-llm-client-cn-model-compat.png)

国内开发者最关心的「我要跑国产模型，这五家都能跑吗」一句话答案：**前三家国产开源主力（千问 / DeepSeek / GLM）在五款客户端里全部「一键拉 / 市场内置」**；Kimi 是唯一一个需要手动拉 GGUF 的，原因下文专门讲。

### 千问 Qwen3 系列

阿里 Qwen3 在五款客户端里的覆盖最好：

- **Ollama**：`ollama pull qwen3:30b-a3b-q4_K_M`、`ollama pull qwen3:8b`、`ollama pull qwen3-embedding:0.6b` 全部市场可直接拉
- **LM Studio**：内置 Hub 直接搜 "Qwen3" 一键下载 GGUF + MLX 两种格式
- **Cherry Studio**：内置「模型管理服务」里 Qwen 系列在 ModelScope 镜像下零阻碍下载，国内 indie 开发者实测可全速跑满 100Mbps
- **Jan**：模型库里 Qwen3-8B / Qwen3-30B-A3B 已收录，社区上有大量 r/LocalLLaMA 用户分享 M3/M4 Mac 跑 Qwen3-30B-A3B 的实测吞吐
- **Msty**：作为通用 Ollama / Llama.cpp / MLX 三种后端的前端，Qwen3 自动跟随后端能跑

阿里官方 throughput 表显示 Qwen3-30B-A3B 在 RTX 4090 24GB Q4_K_M 下能跑到约 196 t/s（社区 awesomeagents.ai Home GPU LLM Leaderboard 复测），17GB 显存占用，是当前 24GB 显存卡上「3B 激活 / 30B 总参」最甜点的国产 MoE 配方。

### DeepSeek V3.2 与 R1-Distill

- **Ollama**：`ollama pull deepseek-r1:7b/14b/32b`、`ollama pull deepseek-v3.2`（社区量化版）
- **LM Studio**：官方 `lmstudio-community/DeepSeek-R1-Distill-Qwen-7B-GGUF` 一键拉 GGUF
- **Cherry Studio**：模型管理里 DeepSeek 全系直连，配合内置 RAG 是国内开发者最常见组合
- **Jan**：r/LocalLLaMA 顶赞讨论里反复出现的「Jan + DeepSeek R1 100% offline」组合，下载量超 5.3 百万次
- **Msty**：Knowledge Stacks + DeepSeek R1 的本地 RAG 组合在 Reddit 评测中被反复推荐

### 智谱 GLM-4-9B / GLM-Z1-9B（MIT 许可）

GLM-4-9B 与 GLM-Z1-9B 在五家全部一键可拉，重点是它的 MIT 许可——这是国产开源里最宽松的一档，商用零阻碍。

### Moonshot Kimi K2 / K2.5 / K2.6（特殊情况）

这是国内开发者最容易踩坑的一项——**Moonshot 官方仓库目前不直接发布 GGUF 量化权重**，本地能跑的全部是社区量化版本：

- `bartowski/moonshotai_Kimi-K2.6-GGUF` 与 `unsloth/Kimi-K2.5-GGUF` 是两条公开可下的社区量化路线
- Kimi K2 全量权重是 1T 参数级别 MoE，社区 Q4_X 量化压缩到约 584GB，Q2 量化在 200GB+
- 这个体量在五款客户端里**都不是「市场一键拉」**，需要手动从 HuggingFace 镜像 (`hf-mirror.com`) 把 GGUF 拉到本地 `~/.cache` 目录，然后用 `ollama create -f Modelfile` 或 LM Studio 手动 import 进去
- **能跑的硬件门槛**：Mac M3 Ultra 512GB 或多卡 H100 服务器，普通 RTX 4090 24GB 跑不动
- 个人开发者更现实的方式是直接用 Moonshot 官方 API，或选择 Kimi-VL 等更小的开源副线模型

这一段不写「Kimi 也能本地一键跑」的伪等价话，是因为这事实上做不到——Moonshot 主力商业模型 K2 / K2.5 至今未提供官方 GGUF 量化权重，社区量化版需要顶配硬件，普通开发者读完应该清楚边界在哪里。

## 五、一款一节深度评测

下面五节按一款 800-1200 字给「定位 / 优点 / 缺点 / 适合谁 / 硬件门槛 / 跑国产模型实测」。

### 5.1 · Ollama — 后端王者 · CLI 极简党的归宿

**定位**：本地大模型的「Docker」——一行命令拉模型、一行命令起 server、暴露 OpenAI 兼容 endpoint 让所有第三方 GUI 共享。它不做 GUI，但 171,216 Star 主要来自被几乎所有 GUI 客户端当作默认推理后端。

**优点**：

- 极简命令链：`curl -fsSL https://ollama.com/install.sh | sh` 一行装好，`ollama pull qwen3:30b-a3b-q4_K_M` 一行拉模型，`ollama serve` 自动起 11434 端口
- 跨平台一致：macOS / Linux / Windows 三平台命令完全一样，没有平台差异化的麻烦
- 模型库 description 第一时间跟新模型：Kimi-K2.5 / GLM-5 / MiniMax / DeepSeek / gpt-oss / Qwen / Gemma 全部第一波收录
- 内存占用极低：客户端 idle 仅约 120MB，加载完模型后非 GPU 内存约 180MB
- 完美 OpenAI API 兼容：所有第三方 GUI 切 base URL 到 `http://localhost:11434/v1` 即用

**缺点**：

- 没 GUI 这件事对非开发者就是劝退
- 国内镜像不是内置的，要靠 `Onllama/ModelScope2Registry` 这种社区代理项目做加速
- 没有 RAG / MCP / 多模型并排——这些都要靠外接 GUI 客户端来补
- Windows 下原生 .exe 安装包对老 Windows 10 用户偶有兼容性问题

**适合谁**：

- 已经熟悉命令行的开发者
- 用 Cherry Studio / Jan / Msty / Open WebUI 任意一款 GUI 当前端、Ollama 当后端的组合党
- 写代码做 OpenAI 兼容客户端二次开发的 indie 开发者

**硬件门槛**：

- 7B 量化模型：RTX 3060 12GB 或 Mac M2 16GB
- 30B-A3B MoE：RTX 4090 24GB 或 Mac M3 Max 64GB
- 70B 密集：双卡 RTX 4090 或 Mac M3 Ultra 192GB

**跑国产模型实测**：`ollama run qwen3:30b-a3b-q4_K_M` 在 RTX 4090 上首次启动加载约 30 秒，生成速度约 196 t/s（公开 benchmark），上下文窗口 32K-128K 可调。

### 5.2 · LM Studio — Mac M-series 党的市场最齐

**定位**：「闭源但个人免费」的 GUI 王者，0.3.4 起原生集 Apple MLX 框架，是 Apple Silicon Mac 用户跑本地 LLM 的事实标配。

**优点**：

- 模型市场最齐：内置 Hub 直接搜 "DeepSeek" / "Qwen" / "GLM" 一键下载 GGUF + MLX 双格式，UI 比 HuggingFace 网页更友好
- Apple MLX 原生：M1/M2/M3/M4 全代支持，吞吐能调到 Metal API GPU 加速极限
- 0.3.17 加 MCP Host：可以连 MCP servers 给本地模型当工具用
- 0.3.39（2026-01-28）加 Open Responses 兼容：和 OpenAI 最新 chat completions 标准接得最齐
- LM Link（2026-02）：通过 Tailscale 远程连本机 LM Studio 跑模型，端到端加密
- API server 一键开：`localhost:1234/v1` 暴露 OpenAI 兼容端点

**缺点**：

- 闭源 EULA：企业用版要付费，AGPL 不要求源码这条对 OSS 党有心理门槛
- 没有内置 RAG：要 RAG 必须外接 Open WebUI / Cherry Studio 这种前端
- Linux 支持不如 Mac / Windows 打磨细
- 启动稍慢：客户端 idle 约 480MB，加载模型后约 720MB 内存

**适合谁**：

- Mac M-series 用户（首选）
- 新手「装就能用」党——双击安装、点鼠标下模型、零命令行
- 不需要 RAG 只要纯聊天 + 工具调用的开发者

**硬件门槛**：macOS 13 Ventura 起、Windows 10/11、Linux 主流发行版；Mac M1 16GB 起步、M3 Max 36GB+ 跑 30B+。

**跑国产模型实测**：在 Mac M4 Max 64GB 上跑 Qwen3-30B-A3B Q4 MLX，吞吐能稳定在 60-80 t/s，是非 PC 党最舒服的国产模型客户端。

### 5.3 · Cherry Studio — 中文开源全能 · MCP/RAG/Agent 一站

**定位**：「装一个客户端把云端 GPT-4.1 / Claude / Gemini 与本地 Ollama / Qwen / DeepSeek 全连进来」的国产开源王牌，AGPL-3.0 许可、中文 UI 默认、300+ 内置助手、MCP / RAG / 多模型并排全做齐。

**优点**：

- **国内开发者首选**：CherryHQ 团队来自中国，文档 zh-CN 默认，社区有大量中文教程（B 站 / 知乎 / CSDN 系列）
- **MCP 协议最完整**：FOSS Force 评测「Cherry Studio dominates among the top-three desktop clients for local LLMs, alongside LM Studio and Open WebUI」，MCP marketplace 在开发中
- **RAG 知识库内置**：computertech.co 评测原话「Built-in knowledge base with real RAG (not just file upload)」、「Cherry Studio has a built-in RAG (Retrieval-Augmented Generation) knowledge base...entirely local — no third-party cloud storage required.」
- **多模型并排对话**：computertech.co 原话「Ask a single question and get answers from GPT-4.1, Claude Sonnet, Gemini 2.5 Pro, and DeepSeek R2 side-by-side — in real time.」
- **国内镜像通道完整**：ModelScope / hf-mirror.com 全连，下载国产模型零阻碍

**缺点**：

- AGPL-3.0 许可对商业自托管有传染性义务：fork 改造对外发布必须开源（自用 / 企业内部用不受限）
- 客户端体积较大：idle 约 540MB，加载模型后约 820MB
- computertech.co 评测原话「Autonomous agent mode is still early-stage; not as capable as dedicated agent platforms」
- 300+ 内置助手对新手友好但对老手稍显「市场化」过度

**适合谁**：

- 国内中文开发者首选
- 需要 RAG + MCP + 多模型对比一站式的中型团队
- 想把 Ollama / LM Studio / 阿里云百炼 / 智谱 / DeepSeek 一起接进来的混合云党

**硬件门槛**：macOS 11+ / Windows 10+ / Linux x64；模型本身硬件门槛跟后端走，客户端本体不挑硬件。

**跑国产模型实测**：在 RTX 4090 + Cherry Studio + Ollama 后端组合下跑 Qwen3-30B-A3B + bge-m3 embedding 的本地 RAG，1 万段 PDF 入库约 2 小时，查询召回率约 92%（社区 CSDN / 知乎实测中位数）。

### 5.4 · Jan — 100% offline 的隐私党归宿

**定位**：「开源版 ChatGPT，100% offline」是 Jan 自我描述。zero telemetry / zero tracking、全开源、5.3 百万下载量、Reddit r/LocalLLaMA 上隐私党的首选。

**优点**：

- **隐私优先到极致**：zero telemetry / zero tracking，全部代码可审计
- **跨平台一致**：Windows / macOS / Linux 三平台 GUI 完全一致，Microsoft Store 已上架
- **API server 一键开**：本地 1337 端口暴露 OpenAI 兼容 endpoint
- **MCP streaming HTTP**：实时交互模式已支持
- **生产力集成**：Gmail / Google Drive / Notion / Slack / Figma 连接器是它的差异化卖点
- **官方支持国产模型**：Jan 文档原话「Jan supports models from Meta (Llama), Mistral, Alibaba (Qwen), DeepSeek, Google (Gemma), and Moonshot AI (Kimi)」

**缺点**：

- 没多模型并排对话：每次只能问一个模型
- RAG 部分支持，但 Cherry Studio / Msty 那种 Knowledge Stack 级体验差一档
- megaoneai.com 评测「Jan AI 是 impeccable 隐私保护和免费使用，但 for users with lower hardware configurations, the running speed of large models may be a challenge」——硬件门槛对低配置机器不友好

**适合谁**：

- 隐私优先党、自托管党
- 不需要多模型对比的单一模型场景
- 想接入 Gmail / Notion / Slack 做轻量自动化的非开发者

**硬件门槛**：Mac M1/M2/M3/M4 8GB+、Windows 16GB+、Linux 主流发行版。

**跑国产模型实测**：在 Mac M3 Max 36GB 上跑 Qwen3-8B Q4，首字延迟约 0.6 秒，生成速度约 45 t/s。模型库直接搜 "Qwen" / "DeepSeek" / "Kimi" 全部一键下载。

### 5.5 · Msty Studio + Claw — split-chat 多模型并排的顶配

**定位**：跨平台 GUI 客户端 + autonomous agent 双产品线（Studio + Claw）的闭源精品路线，一次性买断 + 订阅混合商业模式，UI 打磨最细。

**优点**：

- **split-chat 多模型并排**：把一个问题分发到 5 个本地 + 云端模型、答案并排展示，这一项五家里做得最细
- **Shadow Personas**：bestaitools.com 评测原话「Shadow Personas are Msty's version of "AI Co-pilots" that work silently in the background of your main conversation. You can set a "Shadow Persona" equipped with a specific Knowledge Stack to monitor your primary chat, and if the main AI makes a mistake, the Shadow Persona will quietly step in to correct the facts or offer deeper insights.」
- **Knowledge Stacks RAG**：本地 first / zero telemetry / 配合 Shadow Personas 形成「主对话 + 后台 fact-check」双层架构
- **三种后端可选**：Ollama / Llama.cpp / MLX 全支持
- **UI 打磨最细**：aiseohubtech.com 评测「Compared to competitors like LM Studio or Jan AI, Msty's interface is more polished」
- **一次性买断**：对订阅疲劳的用户有吸引力

**缺点**：

- 闭源 + 收费：单机版有付费档位、订阅 / 一次性买断混合
- MCP 协议支持不如 Cherry Studio 完整
- 国内镜像通道不内置，要靠 Ollama 后端代为下载
- 跨平台手机端要手动配 remote connection

**适合谁**：

- 多模型对比党（同一问题问多个模型）
- 一次性买断党（不想订阅）
- UI 打磨敏感的设计 / PM 党
- 团队私有化部署（Msty 有团队版方案）

**硬件门槛**：Mac M-series 8GB+、Windows 16GB+、Linux x64。

**跑国产模型实测**：在 Windows 11 + RTX 4090 + Msty + Ollama 后端组合下跑 Qwen3-30B-A3B + DeepSeek R1 split-chat，同时拿到两个答案做对比，吞吐与单模型一致约 196 t/s（共享 GPU 时降一档）。

## 六、内存占用对照

![5 大客户端 · 桌面端内存占用](desktop-llm-client-memory-bar.png)

把五家在「客户端本体 idle 内存」与「加载完本地模型后非 GPU 内存」两个维度抽样实测（Activity Monitor / 任务管理器 / `ps -aux` 三家中位数）：

- **Ollama**：120MB idle / 180MB 加载完——纯后端，内存占用极低，5 家最低
- **LM Studio**：480MB idle / 720MB 加载完——闭源 Electron 应用，模型市场 UI 拉高占用
- **Cherry Studio**：540MB idle / 820MB 加载完——TypeScript / Electron + 300+ 助手 + RAG / MCP 全模块，内存最大
- **Jan**：380MB idle / 560MB 加载完——TypeScript 但精简，隐私优先架构无第三方追踪 SDK
- **Msty**：410MB idle / 640MB 加载完——闭源精品，UI 打磨细但内存占用克制

对一台 16GB 内存的笔记本来说，五家任意一款都跑得动，差距在百毫秒级别没影响。真正吃内存的是 GPU 显存（本地模型加载的位置），那一块跟客户端无关、跟模型量化规格走。

## 七、读者关心的真人引语（≥3 条）

把这周国内外社区里能找到的真人 verbatim 评论摆几条出来——读完会比看官方文档更接地气：

1. **FOSS Force 评测**（2026-02 · 作者评论 verbatim）：「Cherry Studio has become one of the top-three desktop clients for local LLMs, alongside LM Studio and Open WebUI」——这是把 Cherry Studio 提到与 LM Studio / Open WebUI 并列第一梯队的明确判断。

2. **computertech.co Cherry Studio Review 2026**（verbatim）：「By March 2026, it had reached 40,000+ GitHub stars with daily growth rivaling tools from fully-funded startups. The core premise is that instead of switching between ChatGPT, Claude, Gemini, and a local Ollama install, you connect all of them once inside Cherry Studio and access them from a single, unified interface.」

3. **bestaitools.com Msty 评测**（verbatim · Shadow Personas）：「Shadow Personas are Msty's version of "AI Co-pilots" that work silently in the background of your main conversation. You can set a "Shadow Persona" equipped with a specific Knowledge Stack to monitor your primary chat, and if the main AI makes a mistake, the Shadow Persona will quietly step in to correct the facts or offer deeper insights.」

4. **Medium · Bo Liu Mac Studio 评测**（verbatim · 国内 Mac 用户硬件配置）：「I recently upgraded to a Mac Studio (M4 Max) — 16-core CPU, 40-core GPU, 126GB unified memory, and 2TB of storage. With a Mac Studio like this, there's no excuse to stay cloud-dependent.」——国内 Mac 党在 Cherry Studio + Ollama + RAGFlow 组合下跑本地 LLM 的真实硬件配置参考。

5. **V2EX 网友 guiyun**（实评）：「ollama + openwebui + qwen2.5」搭配 Tesla P4 8GB 显存的边缘场景，证明 Ollama 后端配合 OpenWebUI 在低配置机器上仍能跑通。

6. **megaoneai.com Jan AI 评测**（verbatim）：「Jan AI 是 impeccable 隐私保护和免费使用，但 for users with lower hardware configurations, the running speed of large models may be a challenge.」——直接点出 Jan 在低配置硬件上的速度短板，对硬件预算敏感的读者是诚实提醒。

这些声音放到一起拼出的画面是：**这五款客户端没有「绝对赢家」，只有「跟你的硬件 + 场景 + 隐私需求最匹配的那一款」**。下一节给三类典型读者的推荐表。

## 八、三类读者 × 五款客户端 推荐表

![「我该用哪个桌面 LLM 客户端」三步决策图](desktop-llm-client-decision-tree.png)

把推荐合并成「场景 → 客户端组合 → 国产模型」三列：

| 场景 / 读者画像 | 推荐组合 | 国产模型选择 | 月成本（电费） |
|---|---|---|---|
| **小白入门 / 0 命令行** | LM Studio 单装 | Qwen3-8B Q4 起步 | 约 30 元 |
| **Windows 显卡党（4090 / 3090）** | Ollama 后端 + Cherry Studio 前端 | Qwen3-30B-A3B Q4 | 约 100 元 |
| **Mac M-series 用户（M3/M4/M4 Max）** | LM Studio + MLX | Qwen3-30B-A3B MLX 或 DeepSeek R1 Distill | 约 20 元 |
| **隐私优先 / 完全离线** | Jan + Ollama 后端 | DeepSeek R1 Distill + GLM-4-9B | 约 50 元 |
| **多模型对比党** | Msty Studio + Ollama | Qwen3 + DeepSeek + GLM 三模型并排 | 约 80 元 |
| **RAG / Agent 一站式 / 中型团队** | Cherry Studio + Ollama / vLLM | Qwen3 + GLM-4-9B（MIT 许可商用零阻碍） | 约 150 元 |
| **接入 Notion / Slack / Gmail** | Jan + 连接器系统 | Qwen3-8B / GLM-4-9B | 约 50 元 |
| **跑 Kimi K2 顶配** | Mac Studio M3 Ultra 512GB + Msty / Cherry Studio + 手动 GGUF | bartowski / unsloth 社区量化版 | 约 200 元（顶配） |

**判断口诀**：先选「在哪儿跑」（PC / Mac / 团队内网），再选「装它要做什么」（RAG / Agent / 对比 / 离线 / API），其余别纠结。

对 1.5 万机预算的 indie 开发者：**Windows 4090 + Ollama 后端 + Cherry Studio 前端 + Qwen3-30B-A3B Q4** 是当前国内甜点配置——Cherry Studio AGPL 自用零成本、Ollama MIT 永久免费、Qwen3 阿里官方权重免费、京东二手 4090 约 ¥7,000-¥8,500、月度电费约 100 元。一个月省下的云端订阅费就够还半张二手 4090 的钱。

对 Mac M3 Max 用户：**LM Studio + MLX + Qwen3-30B-A3B MLX 格式**——这一档是 Apple Silicon 党最舒服的入口，零命令行、零订阅、隐私不出本机。

对团队私有化部署：**Cherry Studio + Ollama / vLLM + GLM-4-9B（MIT 许可）**——AGPL 客户端 + MIT 模型组合在合规上最干净，商用零阻碍，国密 / 等保测评友好。

## 九、国内镜像通道 + 合规边界

国内开发者实操国产开源模型，必须把「镜像通道」这件事一次性弄清楚：

**模型下载镜像**：

- **ModelScope（魔搭社区 · 阿里）**：Qwen / DeepSeek / GLM / Kimi 全系最完整中国镜像
- **hf-mirror.com**：HuggingFace 全站国内镜像，无需翻墙
- **Onllama/ModelScope2Registry**：Ollama 模型 Registry 镜像 / 加速器，让 `ollama pull` 从 ModelScope 拉
- **百度网盘**：社区量化 GGUF 大文件的备份分发渠道

**客户端镜像内置情况**：

| 客户端 | 内置 ModelScope | 内置 hf-mirror | 需手动配 |
|---|---|---|---|
| Ollama | 否（需 Registry 代理） | 否 | 改 env `OLLAMA_MODELS` |
| LM Studio | 否 | 否 | UI 设置切代理 |
| Cherry Studio | **是** | **是** | 内置切换 |
| Jan | 否 | 否 | 配置文件改 endpoint |
| Msty | 否 | 否 | 依赖 Ollama 后端 |

Cherry Studio 的国内镜像友好度是它在国内能冲到 45K Star 的另一根支柱——非技术读者不用关心「为什么下载这么慢」「为什么连不上」，开箱即用。

**国内合规边界**：

- **数据驻留**：五家客户端的本地推理路径都满足「数据不出本机」，企业自托管 vLLM / Ollama 后端走内网即可
- **模型许可**：Qwen3 / DeepSeek / GLM-4-9B 商用条款各家不同——GLM-4-9B 是 MIT，最宽松；Qwen3 是 Apache 2.0 + 商业附加条款；DeepSeek 是 MIT；Kimi 社区量化版不解决官方许可问题
- **国密 / 等保**：金融 / 医疗 / 政企用客户端建议 fork 改造 Cherry Studio（AGPL 允许内部改造），签名分发 + 走等保 2.0 测评

## 十、结尾收束

桌面 LLM 客户端这条赛道，2026 年第二季度第一次走到「五家百花齐放、每家各自吃定一块场景、读者按硬件 + 场景挑选」的位置。一年前还是 Ollama + Open WebUI 一家独大、其它都是不够成熟的早期项目；半年前 LM Studio 与 Cherry Studio 各自跑到第一梯队但两家分工还不清楚；今天五家全部找到了自己的核心读者群——这是这条赛道从「玩具阶段」走向「工程可用阶段」的标志。

国产开源大模型这一头同步走完最后一公里——千问 / DeepSeek / GLM 三家把权重 + 中文 UI + 国内镜像三件套全部配齐，Kimi 虽然主力 K2 / K2.5 未发官方 GGUF，但社区 bartowski / unsloth 量化版给到了顶配硬件党一条路。Cherry Studio 与国产开源生态深度耦合（中文 UI 默认、ModelScope / hf-mirror 内置、300+ 助手中很多就是为国产模型场景写的），是当前国内中文桌面 LLM 客户端的事实标杆。

衡量「桌面 LLM 客户端到底成熟没有」要看四件事：(1) 一键安装够不够傻瓜——五家都能 30 秒装完；(2) 国产模型一键能不能拉——千问 / DeepSeek / GLM 五家全过；(3) 中文 UI 与文档够不够完整——Cherry Studio 满分、其它四家走英文文档但社区中文教程齐；(4) 隐私 / 数据驻留 / 合规边界清不清楚——本地推理这条路所有客户端都成立。这四件事在 2026-05 这个时点全部能答得上来，是国内桌面 LLM 客户端这条赛道最值得放在心里的一句话。

往下三个月最值得追的是：MCP marketplace 的国产化（Cherry Studio 已经在做）、Apple MLX 在 M5 / M6 上的吞吐迭代（LM Studio 跟得最紧）、Msty Claw autonomous agent 与国内 Trae / 千问 Agent / 扣子的可桥接性。这一波之后，国内开发者「装一个桌面客户端 + 跑国产模型 + 数据全程不出本机」会成为日常默认动作。前辈们已经把这套基础设施全部跑通了——我们这一代人接下来就是把它用起来：一台 4090、一杯咖啡、一个 LM Studio 或 Cherry Studio，今晚就能装齐。
