---
title: "16.8 GB 装下 SWE 77.2：本地能否顶替 Claude"
date: 2026-04-27
tags: [Qwen3.6, 本地 LLM, AI Coding, SWE-bench, Apple Silicon, Apache 2.0]
cover: 2026-04-27-qwen3.6-27b-local-coding.png
description: "Qwen3.6-27B（dense, Apache 2.0, 2026-04-22 开源）SWE-bench Verified 77.2，距 Claude Opus 4.6 只差 3.6 分；Q4_K_M 量化 16.8 GB，单 RTX 4090 / 24 GB Mac 就能跑。本文测它够不够顶替 Claude — coding 能力、本地速度、能用在哪些方面、和线上前沿差多少。"
---
# 16.8 GB 装下 SWE 77.2：本地能否顶替 Claude

![16.8 GB 装下 SWE 77.2：本地能否顶替 Claude](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-27/qwen3.6-27b-local-coding/2026-04-27-qwen3.6-27b-local-coding.png)

> **77.2 vs 80.8——SWE-bench Verified 上，本地 27B 距 Claude Opus 4.6 只差 3.6 分。** 16.8 GB 量化体积，单 RTX 4090 / 24 GB Mac 就能跑。Simon Willison 在普通笔记本上跑出 25.57 tok/s，原话是 *"an outstanding result for a 16.8GB local model"*。
>
> 2026-04-22 阿里开源 Qwen3.6-27B，dense 27B + Apache 2.0。下面把"本地够不够用"拆成五个问题：能不能跑、跑多快、干什么活、coding 上和线上差多少、谁该装谁别折腾。

---

## 27B dense + 多模态 + Apache 2.0：先把基础事实摆出来

下载量 39.9 万/月，HF 模型卡 `Qwen/Qwen3.6-27B` 2026-04-27 抓取数据。这次不是去年那批"看着大、跑不动"的玩具开源。

模型卡上的硬指标：

- **架构**：Dense 27B（每个 token 全激活，不是 MoE 那种"参数大、激活小"），64 层，hidden 5120，混合 Gated DeltaNet + Gated Attention
- **模态**：Causal LM + Vision Encoder（文 + 图 + 视频，多模态原生支持）
- **上下文**：原生 **262,144 tokens**，可扩展到 ~1,010,000 tokens
- **协议**：**Apache-2.0**——商用免授权、可二次发布、可微调商业化（比 Llama 自定义社区协议干净）
- **配套发布**：`Qwen3.6-27B-FP8`（官方 FP8 版）、`Qwen3.6-35B-A3B`（同期 MoE 旗舰）

发布当晚 HN 帖子 ID 47863217（"Qwen3.6-27B: Flagship-Level Coding in a 27B Dense Model"）截至 2026-04-22 当晚冲到 691 赞、339 条评论（截稿前实时已涨到 988 / 444）。Simon Willison 当天写长博客亲测，定调四个字——*flagship-level*。

**开源 dense 模型在 2026 年第一次同时占齐"小尺寸 + SWE 77+ + 商用 license + 笔记本能跑"四件事。**

---

## SWE-bench Verified 77.2 是什么概念：和线上前沿差几分

直接看表：

![SWE-bench Verified 对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-27/qwen3.6-27b-local-coding/qwen3.6-27b-swe-comparison.png)

*数据来源：HF Qwen3.6-27B 模型卡 / Anthropic 官方 / DeepSeek 官方文档 / Moonshot 官方博客 / OpenRouter，全部 2026-04-27 抓取*

四个数字关系：

1. **77.2 vs 闭源 Qwen 3.6 Plus 78.8**——差 1.6 分。阿里自家闭源旗舰几乎被 27B 开源版追平
2. **77.2 vs Sonnet 4.6 79.6**——差 2.4 分。距 Anthropic 中等档不到 3 分
3. **77.2 vs Opus 4.6 80.8**——差 3.6 分。距 Anthropic 旗舰 3-4 分
4. **77.2 vs DeepSeek V4-Pro 80.6 / Kimi K2.6 80.2**——差 3 分。和国内"价格屠夫"两强基本持平

**SWE-Verified 每差 5 分，相当于 100 个 GitHub issue 里多/少修对 5 个。** Qwen3.6-27B 本地版相对 Claude Opus 4.6，每跑 100 个 issue 平均少修对 4 个。

这只是 SWE-Verified。同模型在更难的 **SWE-bench Pro**（多文件 / 多步工具调用）上 27B 报的是 53.5——下面 §五 单独展开。

### 一年时间，开源追上了大部分线上模型

12 个月前，2025-04 的开源旗舰还是 Qwen2.5-72B、DeepSeek V3 这种 600B+ MoE，SWE-Verified 50 分上下。一年时间：

- 参数从 72B / 671B（MoE）压到 **27B dense**——小一个数量级
- SWE-Verified 从 50 多分涨到 **77 分**——涨 25 分以上
- 量化体积从 40 GB+ 压到 **16.8 GB**——笔记本能跑

**2025 年开源在追"看着像有用"，2026 年开始追"真的够用"。** 这是 4 月 22 日 Qwen3.6-27B 发布最大的信号。

---

## 16.8 GB 能不能跑起来：硬件门槛 + Simon Willison 实测

![硬件门槛](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-27/qwen3.6-27b-local-coding/qwen3.6-27b-hardware-tiers.png)

*量化档位来源：bartowski/Qwen_Qwen3.6-27B-GGUF（HF 实测）+ Unsloth 官方文档*

### Apple Silicon

- **24 GB 统一内存**（M3 / M4 air、M3 Pro 顶配 mini）：Q3_K_M 14.4 GB 能跑，Q4_K_M 17.5 GB 给 OS 余地不大
- **32-48 GB**（M3 Pro、M4 Pro 顶配）：Q4_K_M 流畅，可叠加长上下文
- **64-128 GB**（M3 Max / M4 Max / Studio）：Q5/Q6 跑得动，可同时开 Cursor + Claude Code 当多 backend
- **MLX 加速**：mlx-community 已发 `mlx-community/Qwen3.6-27B-4bit`，原生 Apple Silicon 优化，比 GGUF + llama.cpp 快 30-50%（按 Qwen3.5 同档位类推，27B 实测社区暂未公布）

### NVIDIA

- **RTX 4090（24 GB VRAM）**：Q4_K_M 17.5 GB 单卡跑得开，留 6 GB 给 KV cache 和上下文
- **RTX 5090（32 GB VRAM）**：Q5_K_M 20.5 GB，留更多 buffer，单卡 27B 跑得最舒服
- **RTX 4060 Ti / 4070 Ti（16 GB）**：只能跑 IQ2_M 10.6 GB 激进量化，质量损失明显，不建议

### 实测速度：Simon Willison 的 25.57 tok/s

25.57 tok/s——2026-04-22 Simon Willison 博客（`simonwillison.net/2026/Apr/22/qwen36-27b/`）实测数据：

- **量化版本**：Q4_K_M GGUF (16.8 GB)，Unsloth 出的版本
- **运行方式**：本地 `llama-server`，65,536 tokens 上下文，4 GB cache
- **Prompt 处理**：54.32 tok/s
- **生成速度**：**25.57 tok/s**（4,444-token 输出耗时 2 分 53 秒）；另一次 6,575 tokens 跑出 24.74 tok/s

他的原话：*"an outstanding result for a 16.8GB local model"*。

### 25 tok/s 是什么体感

每秒 25 token ≈ 每秒 12-15 个汉字 ≈ 每秒 1 行代码。一段 50 行 Python 函数大概 **30-40 秒**。

对比线上：Claude Sonnet 4.6 ~80-120 tok/s，DeepSeek V4 ~150-200 tok/s——**线上是本地的 3-8 倍**。

**速度差距是真实存在的"本地税"。** 异步代码生成、agentic 长任务这点慢可以接受；聊天式快速 iterate 会折磨。

---

## 实测：5090 / A100 / M5 Max / 普通笔记本——四档硬件谁跑得最香

Simon Willison 的 25.57 tok/s 是基线。再往上换档位、再往下退档位，体感差几倍？

![Qwen3.6-27B 类 · 本地硬件实测速度对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-27/qwen3.6-27b-local-coding/qwen3.6-27b-hardware-realworld.png)

*数据来源详见各档位段落末尾。所有数字 2026-04 实测或基于同尺寸模型推算。*

### 普通笔记本（MacBook Pro 32GB · M3-M4 系列）

**25.57 tok/s @ Q4_K_M / 65,536 ctx**——Simon Willison 锚定的基线。

体感：每秒约 1 行代码，写一段 50 行函数 30-40 秒。后台跑 task 没问题；同时开 Cursor 边查边问就开始等。32 GB 内存里 17 GB 给模型 + 4 GB KV cache + OS 浏览器还要 8 GB——余地很紧。

**适合**：先用免费档摸 Qwen 27B 的天花板。能跑通整套流程，但日常用建议换档。

来源：`simonwillison.net/2026/Apr/22/qwen36-27b/`（2026-04-27 实测可访问）。

### Apple M5 Max 128GB / M4 Max 128GB（"安静派"赢家）

**Qwen3.6-35B-A3B Q4_K_M 实测 ~55 tok/s**（M5 Max 单点社区数据，`llmcheck.net/benchmarks`）。
**Qwen3.5-35B-A3B MoE 4-bit @ M4 Max：MLX 126-132 / llama.cpp ~71 / Ollama 41-48 tok/s**（10 次实测区间，`antekapetanovic.com/blog/qwen3.5-apple-silicon-benchmark/`）。

**dense 27B 推算 30-50 tok/s**（MoE 只激活 3B 所以快得离谱，dense 全激活带宽消耗更猛——别拿 MoE 数字推 dense）。

体感差异：
- **0 风扇噪音**：满载推理时 M4/M5 Max 噪音和待机几乎一样
- **~50W 持续功耗**：插电用一晚上不到 1 度电，电费 ¥0.5；同期 5090 同时长 ~5 度
- **MLX 比 llama.cpp 快 30-50%**：M 系列上跑 27B 一定要用 MLX，不要用 GGUF

**适合**：每天 4 小时以上重度用本地模型 + 不想忍风扇噪音的人。M3 Pro / M4 Pro 64 GB 也够，但跑长 ctx 时内存压力大。

### Mac Studio M3 Ultra 512GB（极端档位 · "本地服务器派"）

**Qwen3-235B-A22B FP8 MLX 25-35 tok/s**（`macstories.net` 早期 Mac Studio AI benchmarks，文中重点测的是 235B-A22B + Qwen2.5-VL-72B 这类大模型）；27B 类 dense 在 819 GB/s 带宽下推算约 35-50 tok/s（按 M4 Max 同档位线性外推，社区 27B 直测数据未公布）。

512 GB 统一内存的意义：**能装下任何当前开源模型**——包括 Llama 3 405B Q4（~200 GB）、Qwen3-235B-A22B FP8（~250 GB）、DeepSeek V3 Q4（~340 GB）。整机价 ¥7-8 万一次性投入，4 年使用周期下每月 ~¥1500，比租 A100 便宜得多。

**适合**：小团队 / 工作室 / 需要本地跑 200B+ 模型的研究者。普通个人用是溢出。

### RTX 4090 24GB（主流游戏卡 · 性价比首选）

**Qwen 2.5 32B Q6_K：8K context 39.4 tok/s，32K context 18.7 tok/s**（同型号实测，`vipinpg.com`）。
**Llama 3.1 70B Q4_K_M：2K 28.3 tok/s，32K 11.2 tok/s**。
**DeepSeek 33B Q5_K_M：2K 42.8 tok/s，16K 24.3 tok/s**。

体感：
- 短 ctx 速度比 M4 Max llama.cpp 路线快一档
- 功耗 ~420W，玩游戏的水冷散热够压
- 24 GB VRAM 跑 Q4_K_M 17.5 GB 留 6 GB KV cache——长 ctx（>32k）开始紧
- 单卡价 ¥12,000-14,000，二手市场 ¥8,000

**适合**：原本就在玩游戏 / 已经买 4090 的人，本地模型当副业用。

### RTX 5090 32GB（个人单卡极速王）

**Qwen3-32B dense Q4：4K context 61 tok/s（生成）+ 793 tok/s prefill**（`hardware-corner.net/rtx-5090-llm-benchmarks/`）。
**Qwen 2.5 32B Q6_K：8K 51.8 tok/s，32K 26.3 tok/s**（vipinpg 同实测）。
**Qwen3.5-35B-A3B MoE Q4_K_XL：194 tok/s 生成 / 7026 tok/s prefill**（512 ctx，`github.com/ggml-org/llama.cpp/discussions/19890`）。

是 27B 用户体验的 **2.4 倍跃迁**——从 25 tok/s 跨到 61 tok/s，"等代码"的体感从可见变成几乎无感。

体感：
- 持续满载 ~510W，比 4090 高 90W；同散热下温度高 3-4°C
- 风扇响——大型机箱 + 360 水冷压得住，普通中塔机箱风扇会很吵
- 32 GB VRAM 留出更多 ctx buffer，跑 Q5_K_M 20.5 GB 比 4090 舒服
- 价格 ¥18,000-22,000（2026-04）

**适合**：每天写代码 8 小时以上 + 想要 token 速度逼近线上 API + 不在乎噪音的桌面用户。

### NVIDIA A100 80GB（数据中心 · 不是给个人的）

单流 batch=1：**40-55 tok/s**（业内典型值，跟 5090 接近，A100 带宽 1.94 TB/s 略高但 SM 算力老）。
**vLLM 50 并发：DeepSeek-Distill-Qwen-32B 577 tok/s 总吞吐 / TTFT 1.3s；QwQ-32B 615 tok/s**（`databasemart.com/blog/vllm-gpu-benchmark-a100-80gb`）。
**300 并发**：TTFT 飙到 67-94 秒——直接崩溃，不能用。

A100 的优势在**并发吞吐**，不是单用户速度。云上租赁 RunPod $1.19-1.89/hr、Lambda Labs $2.06/hr、Spheron $1.07/hr（`runpod.io/pricing`）。

折算"包月本地推理" ≈ $1,100/月，**比买 RTX 5090 不划算**——除非你需要的是同时给 50 个同事跑。

**适合**：企业 / 工作室 / 给团队 50 人共享一个本地 LLM 服务。

### 长 context 是隐藏杀手

所有 GPU 在 32k+ context 下显著掉速：

- **5090 从 4K 61 → 32K 26 tok/s**（-57%）
- **4090 从 8K 39 → 32K 19 tok/s**（-51%）
- **A100 也会退**，只是 vLLM batching 摊平了

Qwen3.6-27B 原生支持 262K context——**实际跑到 200k+ 时 tok/s 可能掉到 5-15**。"长上下文很美好"和"长上下文跑得动"是两回事。短 ctx 测出来的 tok/s 永远是上限，不是日常体感。

### 一句话选档位

| 你是 | 选哪档 | 理由 |
|---|---|---|
| 偶尔玩玩、笔记本党 | MacBook Pro 32GB | 25 tok/s 够 prototype；能用就行 |
| 重度本地用 + 静音控 | M4 Max / M5 Max 128GB | MLX 30-50 tok/s + 0 噪音 + 50W 功耗 |
| 跑 200B+ 大模型 | M3 Ultra 512GB | 整机 ¥7-8 万，能装下当前所有开源旗舰 |
| 已有 4090 / 老玩家 | RTX 4090 | 39 tok/s 够用，二手 ¥8000 性价比顶 |
| 想要逼近线上速度 | RTX 5090 | 61 tok/s + 2.4× 4090，¥18000 起 |
| 团队 / 公司多用户 | A100 80GB（云上） | 50 并发 600 tok/s 总吞吐 |

---

## 装得起还要看装哪条工具链：Ollama vs llama.cpp vs LMStudio vs MLX

| 工具 | 适合 | 装 Qwen3.6-27B | 备注 |
|---|---|---|---|
| **Ollama** | 入门、装一行就跑 | `ollama run qwen3.6:27b`（17 GB，文+图） | 官方仓库已挂 `qwen3.6:27b`、`qwen3.6:27b-coding-mxfp8`、`qwen3.6:27b-coding-nvfp4` 等多个标签；视觉版的 mmproj 已打包 |
| **LMStudio** | GUI 派、点按钮 | 在 model 搜 "Qwen3.6"，下载 GGUF | 基于 llama.cpp，UI 直观，没 CLI 心智负担 |
| **llama.cpp** | 性能榨干派 | clone + build + 拉 GGUF | Simon Willison 的 25 tok/s 就是这条路；可调 batch size / KV cache 等所有参数 |
| **MLX**（Apple 专属）| Mac M 系列重度用户 | `pip install mlx-lm` + 拉 `mlx-community/Qwen3.6-27B-4bit` | 比 llama.cpp 在 Apple Silicon 上快一档；可起 OpenAI 兼容 server，端口 8080 |
| **vLLM / sglang** | 服务化、多并发 | 需要 Hopper / Ada GPU + Python 环境 | 个人桌面意义不大，企业部署再考虑 |

> **Ollama 一个坑**：Unsloth 文档说"Currently no Qwen3.6 GGUF works in Ollama due to separate mmproj vision files"——这是指"自己拉 unsloth 的 GGUF 用 ollama run 跑不起来"，不是 Ollama 整个挂了。**Ollama 官方仓库 `qwen3.6:27b` 标签（已打包 mmproj）正常可用。** 跑不起来先 `ollama --version` 升到 0.6.0+。

完整安装步骤在 [Qwen3.6 本地部署指南 · Mac + Windows 实战版](../2026-04-22/qwen3.6-local-guide.md)（针对 35B-A3B MoE，Ollama / LMStudio / llama.cpp 那一节对 27B dense 完全适用），不再重复。

---

## 5 个有 ROI 的本地场景

跑起来只是第一步，能干活才是关键。下面 5 个场景在 2026-04 已经能上桌。

### 场景 1：IDE 里的本地副驾（0 月费）

Claude Code 4-21 对 2% Pro 用户测试性下架（几天后回滚），加上 4-23 那周 Anthropic postmortem 后用户对默认配置的信任明显动摇——很多重度用户开始把"本地兜底 + 线上 API 按需"作为主流配置。

本地 Qwen3.6-27B + Continue.dev / aider，月费 0、调用费 0。下面这些场景是合理决策：

- **写 boilerplate**：JSDoc、单测脚手架、CRUD 接口骨架——27B 和 Sonnet 4.6 体感几乎一样
- **修明确 bug**：错误信息已知、定位文件已知、改动 ≤ 50 行——SWE-Verified 77.2 分就是这种任务的打分
- **轻 refactor**：重命名、提函数、抽常量——单文件场景 27B 完全够用

### 场景 2：离线 RAG（公司内部代码库 / 私密文档）

**数据不出本机——本地模型不可替代的场景。**

- 把公司内部 Git 仓库（Java 后端、运维脚本、产品 PRD）embed 进本地 Milvus / Qdrant
- 跑 `claude-context` 或 `mcp-rag` 这种 MCP 工具把 Qwen 27B 接进去
- IDE 里问"我们公司怎么实现支付重试的"，模型先 RAG 内部代码库再回答

金融、医疗、军工、央国企——代码不能出墙的场景，本地 27B 是当前唯一能跑到 SWE 77+ 的选择。

### 场景 3：长文档理解（262K 原生上下文）

262,144 tokens 原生上下文 ≈ 30-40 万汉字，可扩展到 1M。本地化意义：

- **整本技术书**塞进单次对话——《Designing Data-Intensive Applications》25 万词，能塞两遍
- **整个开源项目代码**当上下文——Linux kernel 子模块、Vue 全仓、Vite 全仓都塞得下
- **长会议纪要 / 多年合同 / 整份招标文件**做问答

**长 context 本地比线上更划算。** DeepSeek V4 标价 ¥3/M、Sonnet ¥21/M，跑 200k context 一次问答几毛到几块。本地是固定成本（电费 + 折旧），跑无数次不烧钱。

### 场景 4：多模态实战（视觉 + 文 + 视频）

27B 自带 Vision Encoder，原生支持图像和视频理解。本地能干这些：

- **截图 OCR + 解释**：界面截图扔给它，解释 UI 状态、找按钮位置、生成自动化测试代码
- **PDF 整本阅读**：扫描件、合同、研究报告——本地一次过完
- **视频内容索引**：会议录像、教学视频抽关键帧 + 转字幕

**模型卡数据：MMMU 82.9 / VideoMME 87.7——接近闭源前沿。**

### 场景 5：本地 agent（OpenClaw、Continue.dev agent 模式）

这是 2026 年 Q2 最热的方向。Qwen3.6-27B 接进 OpenClaw 这种本地 agent 框架后：

- **离线办公自动化**：邮件分类、Excel 处理、文件批量改名——agent 自己规划 + 调工具
- **本地代码 PR 生成**：给需求描述，agent 自己 grep 代码、改文件、跑测试、生成 git diff
- **Linux 运维 agent**：内网堡垒机本地部署，agent 调 SSH + kubectl 排查故障

**Terminal-Bench 2.0 59.3——和 Claude 4.5 Opus 持平**（per the-decoder.com 报道）。本地 agent 这条路在 2026-04 第一次有了开源底座。

---

## 5 个本地干不过线上的地方

77.2 SWE-Verified 不等于"日常体感只差 3.6 分"。下面 5 个差距实测能感觉到。

### 短板 1：速度差 3-8 倍

本地 25 tok/s vs 线上 80-200 tok/s。下面三类场景被放大：

- **聊天式快速迭代**：改一行问一句，每次等 5 秒 vs 等 1.5 秒——半小时下来体感差几倍
- **agentic 多轮**：一个 task 跑 30 步工具调用，每步等 10 秒 vs 等 2 秒——总耗时 4 分钟 vs 1 分钟
- **长 context 推理**：context 越长 TTFT 涨得越凶，本地 27B 在 100k+ context 下首 token 可能 30+ 秒

**"快速 prompt-iterate"会被抓狂。"丢长任务回头看"差别不大。**

### 短板 2：SWE-bench Pro 上差距拉到 15-20 分

Qwen3.6-27B SWE-Pro 53.5。完整对比：

- DeepSeek V4-Pro SWE-Pro 55.4（差 1.9）
- Kimi K2.6 SWE-Pro 58.6（差 5.1）
- GPT-5.5 xhigh SWE-Pro 58.6（差 5.1）
- Claude Opus 4.6 SWE-Pro 53.4（per vellum.ai 公开评测，27B 反高 0.1——SWE-Pro 是更难的多文件 / agent 多步任务，闭源模型在这块未必占绝对优势）

SWE-Verified 差 3-4 分，到 SWE-Pro 拉到 4-6 分。再往复杂场景走（multi-repo、跨语言、外部 web search），gap 会继续拉到 **15-20 分**。

**体感翻译：让 27B 跑"React + 后端 API + 数据库 migration 同时改的 bug"，多轮工具调用 + 长 context 后，错误率显著高于 Sonnet 4.6 / Opus 4.6。**

### 短板 3：tool use / 工具调用的稳定性

开源整体落后线上半代——27B 也不例外：

- **JSON schema 严格遵守**：线上几乎 0 失误，27B 长 prompt 下偶尔多/少字段
- **多工具并行**：线上一次调 3-5 个工具，27B 通常退化成单工具串行
- **工具结果二次推理**：拿到返回再综合，27B 常忽略部分关键字段

agent 链路重度依赖 function calling（比如 mcp 工具栈很大）的话，本地 27B 会有"模型听不懂工具说话"的体验。这是开源整体短板，不是 Qwen 一家的事。

### 短板 4：复杂 codebase 索引能力弱

线上默认接 `claude-context`、`context-mode` 做语义代码搜索（Anthropic 4 月 23 postmortem 那一周三个工具一起爆火，社区版本冲到 9.5k 星）。本地接这些工具理论上行——但：

- 国内开发者要自己跑 Milvus / Qdrant 向量库
- embedding 模型也要本地版（调云端 embedding 就破坏了"完全离线"）
- IDE 集成度不如 Cursor / Claude Code 那种开箱即用

**本地 RAG 链路要自己搭，不是装一行命令的事。** 普通开发者用 Cursor + 线上模型省心 10 倍。

### 短板 5：中文场景的"奇怪退化"

反直觉——Qwen 是阿里出的中文模型，但 27B dense 在某些中文场景上**反而不如 Kimi / DeepSeek 的同等线上版本**：

- 中文 long-form 写作（5000+ 字技术文档）：本地 27B 容易"重复同义句"，Kimi K2.6 不会
- 中文 instruction following（多步指令）：DeepSeek V4 更准
- 中英混合代码注释（国内常见场景）：差距小但本地略弱

可能原因：开源 27B 的 instruction tuning 数据规模和 RLHF 强度不如闭源版本。Qwen 自家 Plus / Max 闭源版（同期发布）在中文上明显更稳。

---

## 装 / 不装：六类人怎么选

| 你是什么人 | 装 Qwen3.6-27B 本地？ | 理由 |
|---|---|---|
| **隐私敏感行业**（金融 / 医疗 / 军工 / 央国企）| ✅ 必装 | 数据不出本机；77.2 分是开源里 first-class，没有同尺寸更强的选择 |
| **Mac M3+ Max / Studio 重度开发者** | ✅ 装 | 离线兜底 + 长 context 私人文档；MLX 加速跑得舒服 |
| **轻度个人开发者，原本订 Claude Code $20 档** | ⚠️ 看场景 | 如果工作流是"丢长任务回头看"，本地够用；如果要"快速 prompt iterate"，DeepSeek V4 ¥0.75/M 促销价更划算 |
| **企业内部 RAG / 知识库 / agent 平台** | ✅ 装 | Apache 2.0 商用免授权，比 Llama 协议干净；27B 在企业 GPU 池里很经济 |
| **多步 agentic coding 重度用户**（每天跑 50+ 多文件 agent 任务）| ❌ 别装 | SWE-Pro / agent 链路差距明显，等 Sonnet 4.6 / Opus 4.7 更省事 |
| **预算 ¥0、流量小、只是好奇** | ⚠️ 中等 | DashScope / 硅基流动 / OpenRouter 上有免费 / 低价 Qwen 调用，未必要本地折腾 |

---

## 替代 Claude 吗？分场景看

**不能完全替代，但 70% 场景够用。** 三档划分：

- **够用**（体感差距小）：写 boilerplate、修明确 bug、快速 prototype、单文件 refactor、长文档理解
- **还得 Claude**：50k 行祖传项目、跨文件 agent、调微妙性能 bug、复杂 RAG 链路
- **27B 是真正的本地平替**：隐私场景、断网场景、低频用户、企业内网——SWE-Verified 数字摆在那

更准的措辞——**Qwen3.6-27B 是 Claude Sonnet 4.6 在本地能跑起来的最近平替**：同样 SWE-Verified 80 分量级、同样 multi-modal 支持、同样 agentic 底座。差的是速度、tool use 稳定性、复杂 codebase 处理。

**和 Opus 4.6 对比没意义。** 线上旗舰永远更快、tool use 更稳、有人不停在调。本地的意义是"无须依赖外部基础设施"。

---

## 编辑说：4 月 22 日是开源本地大模型的拐点

回到 Simon Willison 那句 *"an outstanding result for a 16.8GB local model"*。

12 个月前的"本地大模型"是 Llama 3 70B 跑 4-bit 量化（40 GB+）、出来的代码勉强能用。一年后，**16.8 GB + SWE-Verified 77.2 + Apache 2.0** 第一次同时成立。

**MacBook + 24 GB + Q4_K_M 就是 SWE 77 分的入场券。** 这是 4 月 22 日 Qwen3.6-27B 发布里最被低估的那个事实。

具体怎么动手：

- **隐私敏感行业 / 企业内网**：今天就拉 `mlx-community/Qwen3.6-27B-4bit` 或 Ollama `qwen3.6:27b`，先把 SWE 77 分的本地底座装好
- **Mac M3 Max / Studio 重度开发者**：MLX 路径最舒服；Continue.dev 接进去当离线兜底
- **原本订 Claude Code Pro 的个人开发者**：先看自己工作流——丢长任务回头看就装本地兜底，快速 prompt iterate 仍续 Pro 或转 DeepSeek V4 ¥0.75/M 促销价
- **多步 agent 重度用户**：再等一个版本，或继续 Sonnet 4.6 / Opus 4.7

留 4 月 22 日这个时间戳，半年后回头看本地 LLM 圈的拐点。

---

**本文数据**：Qwen/Qwen3.6-27B HF 模型卡（2026-04-27 抓取，下载量 39.9 万/月、SWE-Verified 77.2、SWE-Pro 53.5、LiveCodeBench v6 83.9、AIME 26 94.1、MMLU-Pro 86.2、Terminal-Bench 2.0 59.3、MMMU 82.9、VideoMME 87.7、原生 262K context）。Simon Willison 实测数据（`simonwillison.net/2026/Apr/22/qwen36-27b/`，Q4_K_M 16.8 GB、25.57 tok/s 生成、54.32 tok/s prompt 处理）。bartowski/Qwen_Qwen3.6-27B-GGUF 量化文件大小（HF 实测）。Unsloth 官方文档硬件门槛表。HN item 47863217（691 pts / 339 comments）。Qwen 官方博客（`qwen.ai/blog?id=qwen3.6-27b`）。线上模型 SWE 数据：Anthropic 官方公告（Sonnet 4.6 79.6、Opus 4.6 80.8）、DeepSeek 官方文档（V4-Pro 80.6 / SWE-Pro 55.4）、Moonshot 官方博客（Kimi K2.6 80.2 / SWE-Pro 58.6）、OpenRouter（Qwen 3.6 Plus 78.8）、vellum.ai 评测（Opus 4.6 SWE-Pro 53.4）、the-decoder.com 报道（Terminal-Bench 2.0 59.3）。GitHub `QwenLM/Qwen3.6` 仓库 3,177 stars、2026-04-27 仍在更新。
