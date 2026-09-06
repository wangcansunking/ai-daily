---
title: "RTX 5090 跑千问：短任务选 NInfer，长任务选 vLLM"
date: 2026-09-06
weekday: 星期日
category: 硬件实测
slug: qwen38-27b-rtx5090-engine-choice-2026-09-06
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/qwen38-27b-rtx5090-engine-choice-2026-09-06.png
description: "同一张 RTX 5090、同一个千问模型，短上下文 NInfer 快，长上下文与工具调用 vLLM 反超。真正的选型分水岭不是单个速度峰值，而是上下文、并发和输出格式。"
tags: [RTX 5090, 千问, Qwen3.8, NInfer, vLLM, llama.cpp, 本地大模型]
track: A
---

# RTX 5090 跑千问：短任务选 NInfer，长任务选 vLLM

9 月 5 日，一位把千问（Qwen）3.8-27B 用在生产内容处理中的 RTX 5090 用户，公开了 NInfer、vLLM 和 llama.cpp 的对照结果。

短上下文里，NInfer 的 NVFP4 版本一度跑到 **127.1 token/s**，快过 vLLM 的 **117.3 token/s**。上下文拉到 16K，次序反过来：vLLM **56.0 token/s**，NInfer **44.3 token/s**。

**这次实测最有用的结论不是“谁最快”，而是：短任务看推测解码，长任务看预填充与缓存，agent 还要把工具调用算进去。**

*下图来源：r/LocalLLaMA 实测帖索引图，2026-09-05。首图保留原帖的速度对照。*

![RTX 5090 上千问三引擎实测速度对照](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/qwen38-engine-source-reddit-speed-large.png)

## 这不是一张只看峰值的速度表

*下图来源：实测者公开的完整 Gist，最后修订于 2026-08-26；9 月 5 日 r/LocalLLaMA 新帖补充 llama.cpp 与生产样本。*

![千问 RTX 5090 实测的公开配置与数据页](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/qwen38-engine-source-gist-card.png)

参与对照的是一张 NVIDIA RTX 5090 32GB、千问 3.8-27B 和三套不同推理路径。测试者不是把官方模型卡抄成一张表，而是跑了两类工作：

- 0、4K、8K、16K、32K 不同上下文深度的单路与并发吞吐；
- 69 个确定性、多轮工具调用场景，覆盖 15 类行为；
- 另有 250 多个生产内容样本，用来检查不同引擎的结果差异；
- 所有关键启动参数、模板和测量脚本均公开。

这几类负载不能混为一个“每秒多少 token”。读入 32K 文档，和在零上下文下连续写代码，瓶颈不同；四个请求共用前缀，和四个完全无关的请求，也不是一回事。

| 变量 | 为什么会改变结论 |
|---|---|
| 权重量化 | NVFP4、Q8_0 与自定义 groupwise-int 的精度和显存不同 |
| KV cache | FP8、INT8、Q8_0 的容量与算力开销不同 |
| MTP | 多 token 预测能直接抬高解码速度，但看接受率 |
| 前缀缓存 | 多轮 agent 会复用系统提示词、工具列表和历史代码 |
| 并发上限 | 服务端排队时，“总吞吐”与“单用户速度”会分离 |

所以，这是一份**选择工作负载**的实测，不是一场统一条件下的内核冠军赛。

## NInfer 的短任务领先，主要来自 MTP

*下图来源：r/LocalLLaMA 实测帖质量与速度对照图，2026-09-05。*

![NInfer 短上下文速度与多 token 预测对照](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/qwen38-engine-source-reddit-quality-large.png)

零上下文、单路请求中，NInfer NVFP4 配合 3 个草稿 token 的多 token 预测（MTP3），达到 **127.1 token/s**。同样开 MTP3 的 vLLM 是 **117.3 token/s**，NInfer 领先约 8%。

4K 上下文时，两者几乎打平：NInfer **87.1 token/s**，vLLM **87.5 token/s**。到了 8K，vLLM 以 **76.7 对 73.1** 开始领先；16K 是 **56.0 对 44.3**；32K 是 **32.7 对 24.2**。

| 上下文深度 | NInfer NVFP4 · 单路 | vLLM MTP3 · 单路 | 当场结论 |
|---:|---:|---:|---|
| 0 | 127.1 token/s | 117.3 token/s | NInfer 快约 8% |
| 4K | 87.1 token/s | 87.5 token/s | 基本持平 |
| 8K | 73.1 token/s | 76.7 token/s | vLLM 开始领先 |
| 16K | 44.3 token/s | 56.0 token/s | vLLM 快约 26% |
| 32K | 24.2 token/s | 32.7 token/s | vLLM 快约 35% |

原帖进一步给了一个关键拆分：不计 MTP 的原始 NVFP4 解码，两套引擎都在约 **66—70 token/s**。NInfer 之所以能到 **158—213 token/s** 的混合生产速度，主要靠 MTP3 约 **76%** 的接受率。

这不是坏消息。它恰恰说明 NInfer 的优势是真正可解释的：模型容易预测后续 token 时，草稿头一次猜中多个，GPU 少跑几轮；任务变难、上下文变长或输出结构受约束，接受率和预填充会重新主导。

**短任务选 NInfer，买的是高接受率下的少跑几轮，不是一块神秘的“更快内核”。**

## 16K 之后，读上下文成了主战场

*下图来源：公开 Gist 的深度扫测与复现说明。*

![千问不同上下文深度下的 NInfer 与 vLLM 实测配置](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/qwen38-engine-source-reddit-config-large.png)

长上下文会把预填充成本放大。实测中，NInfer 的冷预填充约 **3.0—3.3K token/s**；vLLM 约 **6—18K token/s**。即使纯解码接近，先读完 16K 或 32K 文档所花的时间，也会把端到端速度拉开。

这对真实 agent 很重要。一个 Coding Agent 请求里，常见输入包括：

- 系统提示词和工具定义；
- 仓库说明、计划与历史命令；
- 多个源文件或长日志；
- 上一轮失败原因与测试输出。

输入越长，解码峰值越不能单独代表体感。零上下文快 10 token/s，可能抵不过预填充慢半秒；更何况 agent 往往不会只问一次。

vLLM 的自动前缀缓存（APC）需要显式打开。测试者在 32K 深度复用相同前缀时，双并发有效吞吐从 **28.3** 升到 **124.5 token/s**，四并发从 **31.3** 升到 **223.2 token/s**，分别约 **4.4 倍**和 **7.1 倍**。

这个数字不能拿去代表四个完全不同的用户。它测的是多轮请求共享系统提示词和前文时，缓存是否真的复用。对同一个 agent 反复读同一仓库、同一工具清单的服务，它比冷启动峰值更接近真实工作。

## 工具调用把“快”改写成“能不能交付”

*下图为本文依据公开数据整理的决策表。*

![RTX 5090 运行千问时三种本地引擎的场景取舍](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/qwen38-engine-comparison-table.png)

69 个工具调用场景的满分是 138。NInfer 得 **123/138**，折算 **89/100**；vLLM 使用默认模板时，30 个基础场景过了 29 个。换成针对千问 3.8 修过的 `froggeric-v22` 模板后，vLLM 得 **134/138**，调整一个已复现的评分器误判后为 **97/100**。

差距最集中在结构化输出：NInfer 是 **5/12**，修过模板的 vLLM 是 **12/12**。这说明同一组权重换引擎后，模型“聪明程度”并没有凭空变化，解析器、聊天模板和结构化输出实现却会决定工具参数能否被正确交给下游。

同一批 250 多个生产内容样本里，最大质量差约 **2 个百分点**。样本量为 50 的子项标准误约 **5.6 个百分点**，因此原帖把它视为统计噪声，而不是某个引擎让模型突然变聪明。

这里要把两个判断分开：

- 自由文本质量接近，不足以证明所有任务完全等价；
- 工具调用的格式正确率确有差异，而且可由模板修正；
- `json_mode` 仍是 NInfer 当前明确缺口；
- 生产 agent 失败一次的代价，常常高过快几十 token/s。

**本地 agent 的速度单位不该只是 token/s，还应包括“成功交出可执行参数的请求数”。**

## llama.cpp 没赢吞吐，却保住了通用性

*下图来源：Truespar 的千问 3.8-27B 公开对照板，2026-08-22/23。它使用 RTX PRO 6000，不与 RTX 5090 数字横向相减，只用于观察引擎随并发变化的趋势。*

![千问三种服务引擎在单路和并发负载下的公开对照板](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/qwen38-engine-source-paddock-board.png)

llama.cpp 在这次 RTX 5090 生产横评里没有拿到吞吐冠军。另一组 RTX PRO 6000 公开测试也显示，同一个 Q8_0 文件下，专用 CUDA 引擎在 13 个场景都更快，差距从 **1.5 倍到 37.5 倍**。

但这不等于 llama.cpp 没有位置。它保住的是另外四件事：

- GGUF 量化选择最丰富，社区权重获取容易；
- CPU、CUDA、Vulkan、Metal 等硬件范围广；
- 单用户、本地桌面和离线批处理设置直观；
- 模型或显存不在专用引擎支持表时，通常仍能先跑起来。

专用引擎把支持范围换成极致速度。NInfer 当前针对 NVIDIA 和少数模型做深度优化；vLLM 更适合 Linux 服务端与并发；llama.cpp 则像一把覆盖面很广的工具。三者不是同一层面的产品。

还有一个公平性问题。NInfer、vLLM 用 NVFP4，llama.cpp 常用 GGUF Q8_0 或其他量化；KV cache 也可能是 FP8、INT8 或 Q8_0。不同权重和缓存精度混在一起时，速度差不能单独归因给引擎。

## 三套可复现配置怎么起步

以下命令保留实测所需的关键开关。模型路径和模板文件要换成你本机实际位置。

### vLLM：长上下文与多轮 agent

```bash
vllm serve gittensor-model-hub/Qwen3.8-27B-NVFP4-RTX5090 \
  --kv-cache-dtype fp8 \
  --max-model-len 262144 \
  --max-num-seqs 16 \
  --enable-prefix-caching \
  --reasoning-parser qwen3 \
  --enable-auto-tool-choice \
  --tool-call-parser qwen3_xml \
  --chat-template ./qwen38-froggeric-v22.jinja
```

先观察 `vllm:prefix_cache_hits_total`。如果多轮请求的命中一直为零，长上下文优势不会自动出现。

### NInfer：短上下文与高 MTP 接受率

```bash
ninfer-serve \
  --model ./qwen3_8_27b_nvfp4.ninfer \
  --max-context 131072 \
  --max-concurrency 2
```

公开实测中，NVFP4 + MTP3 的单请求上限是 131072。需要完整 262K 时，要关闭 MTP，或换用该测试里的 groupwise-int 权重。**不能同时拿 128K 的速度和 262K 的容量当成一套配置。**

### llama.cpp：跨硬件与 GGUF

```bash
llama-server \
  -m ./Qwen3.8-27B-Q8_0.gguf \
  -ngl 99 \
  -fa on \
  -c 262144 \
  -np 4 \
  -ctk q8_0 \
  -ctv q8_0 \
  --jinja \
  --top-k 20 \
  --top-p 0.95
```

不要一上来就把 `-np` 设到 32。单人桌面服务先从 1—4 路测起；总吞吐升高时，还要看每个用户的首 token 等待有没有恶化。

## 显存与上下文，不能只看“装得下”

RTX 5090 有 32GB 显存。27B 模型做 NVFP4 后，权重能留出较大空间给 KV cache，但上下文上限仍受四项共同约束：权重、缓存精度、并发数和 MTP 草稿状态。

这也是“支持 262K”最容易误导的地方。支持不代表在 262K 时仍有零上下文速度；单路装得下，也不代表四路并发仍不排队。

建议用三组真实请求验收：

1. **短代码问答**：0—4K 输入、单路，测解码和工具参数正确率；
2. **仓库长任务**：16K—32K 输入、多轮复用前缀，测首 token 和完整任务时间；
3. **并发服务**：2—8 个真实用户，测总吞吐、每用户速度和 95 分位等待。

每组至少同时记四个数字：首 token 时间、生成 token/s、完整任务耗时、成功率。只记其中一个，结论很容易被优化项带偏。

## 选择建议：按负载，不按名气

| 你的主要负载 | 首选 | 理由 |
|---|---|---|
| 单人、短上下文、自由文本生成 | NInfer | MTP 接受率高时，短任务速度领先 |
| Coding Agent、长仓库、多轮工具调用 | vLLM | 预填充、前缀缓存与结构化输出更稳 |
| 多用户 OpenAI 兼容服务 | vLLM | 并发调度与可观测性更成熟 |
| Windows / macOS / AMD / CPU / 多种 GGUF | llama.cpp | 硬件和量化覆盖面更广 |
| 需要 262K 且仍想开 MTP | 先实测 | 不同引擎的容量与 MTP 上限不能拼接 |

我的建议很明确：**RTX 5090 上跑千问 3.8-27B，个人短任务先试 NInfer；把它当长期运行的 Coding Agent 或多人服务，就从 vLLM + MTP3 + 前缀缓存起步；硬件或量化不在支持表，再回到 llama.cpp。**

这张 5090 没有唯一“正确引擎”。它有三种不同的好用方式。把短任务、长上下文、工具调用和并发拆开测，才不会为了一个漂亮峰值，把真正的工作负载放错地方。

## 参考资料

- r/LocalLLaMA 实测讨论：<https://www.reddit.com/r/LocalLLaMA/comments/1w821fg/ninfer_vs_llamacpp_vs_vllm_quality_speed/>
- RTX 5090 上 vLLM 与 NInfer 的完整实测 Gist：<https://gist.github.com/PierpaoloPernici/f1d1382f8e357b4faffb1a9f584cc1df>
- Truespar Paddock 千问 3.8-27B 对照板：<https://truespar.com/paddock/benchmarks/qwen38-27b>
- vLLM 0.28.0 发布说明：<https://github.com/vllm-project/vllm/releases/tag/v0.28.0>
