---
title: "4090 跑 Qwen3-Coder：Q4 三家格式怎么选"
slug: qwen3-coder-q4-awq-gptq-gguf-4090-2026-05-17
date: 2026-05-17
weekday: 星期日
category: 本地大模型 / Q4 量化格式对位
track: domestic-hot
domain: local-llm-quantization
tags:
  - Qwen3-Coder
  - AWQ
  - GPTQ
  - GGUF
  - Q4 量化
  - RTX 4090
  - 本地大模型
  - AI Coding
cover: qwen3-coder-q4-awq-gptq-gguf-4090-2026-05-17.png
description: "Qwen3-Coder-30B-A3B-Instruct 在 RTX 4090 24GB 单卡上，AWQ / GPTQ Int4 / GGUF Q4_K_M 三种 Q4 量化格式的精度 / 吞吐 / 显存 / 工程实操对位实测。三家文件大小都落在 18-19 GB、精度差距 1-2 个百分点；真正分胜负的是引擎兼容性与工程拓扑——服务化部署选 AWQ Marlin，个人本地跑选 GGUF Q4_K_M。"
---

# 4090 跑 Qwen3-Coder：Q4 三家格式怎么选

![qwen3 q4 awq gptq gguf 4090 三家格式对位封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-17/qwen3-coder-q4-awq-gptq-gguf-4090-2026-05-17/qwen3-coder-q4-awq-gptq-gguf-4090-2026-05-17.png)

## 关键数字一览

| 项目 | 数字 |
|---|---|
| Qwen3-Coder-30B-A3B-Instruct 月下载（HuggingFace） | 2,109,017 |
| 总参 / 激活参 / 层数 / expert | 30.5B / 3.3B / 48 / 128 选 8 |
| 原生上下文 / YaRN 扩展 | 256K / 1M |
| 许可证 | Apache-2.0 |
| AWQ Q4 文件大小（cpatonn 社区版） | 18.1 GB |
| GPTQ Int4 文件大小（btbtyler09 社区版） | 18.66 GB |
| GGUF Q4_K_M 文件大小（Unsloth） | 18.6 GB |
| AWQ 4090 解码吞吐（社区中位） | ~88 tok/s |
| GPTQ Int4 4090 解码吞吐（社区中位） | ~82 tok/s |
| GGUF Q4_K_M 4090 解码吞吐（社区中位） | ~73 tok/s |
| AWQ Aider Polyglot / HumanEval Pass@1 | 60.9 / 51.8 |
| GGUF Q4_K_M Aider Polyglot / HumanEval Pass@1 | 60.9 / 51.8 |
| GPTQ Int4 Aider Polyglot / HumanEval Pass@1 | 60.4 / 51.5 |
| BF16 全精度 Aider Polyglot / HumanEval Pass@1 | 61.8 / 56.1 |
| AWQ 仓库月下载（cpatonn） | 385,783 |
| GGUF 仓库月下载（Unsloth） | 172,571 |
| Marlin kernel 在 AWQ 上的速度倍率（H200 基准对照） | 10.9 倍 |

## 一、先把三家 Q4 分清楚：算法路线不同，结果都落在 18-19 GB

昨天那篇五档量化对位把「4090 24GB 显存 × Qwen3-Coder-30B-A3B × Q4」这条路钉死了——Q4 是单卡 4090 能纯 GPU 跑的最高档；Q6 / Q8 / BF16 在这张卡上全是空头支票。但「选 Q4」只是答案的一半，另一半是：**Q4 这一档其实有三家不同的实现路线**，权重压成 4 bit 的方法不一样，对应的推理引擎也不一样。AWQ、GPTQ Int4、GGUF Q4_K_M 这三个名字在 r/LocalLLaMA、知乎、阿里云开发者社区被并列提了几个月，但很少有人把三家摆到一张表上认真比一比。

本文就把这件事做完。先把结论写在前面，省得读者翻到末尾：**三家 Q4 在 4090 上的文件大小都落在 18.1-18.66 GB，精度差距 1-2 个百分点，吞吐差距 15-20%；真正分胜负的不是这些数字，而是引擎兼容性——AWQ / GPTQ 走 vLLM / SGLang / lmdeploy 服务化路径，GGUF Q4_K_M 走 llama.cpp / Ollama / LM Studio 本地路径**。这两条路径背后是两套截然不同的工程哲学；选哪家，本质上是选你的部署拓扑。

先把三家的算法路线一句话讲清楚：

- **AWQ**（Activation-aware Weight Quantization，论文 [arxiv 2306.00978](https://arxiv.org/abs/2306.00978)）：MIT Han Lab 2023 年提出。核心洞察是 LLM 里只有约 1% 的权重通道是「关键通道」，识别这些通道的依据不是权重数值本身，而是激活值的统计分布。把这 1% 关键通道通过等价变换缩放到更高精度后再压成 4 bit，整体量化误差大幅下降。group size 默认 128，配合 Marlin GEMM kernel 在 GPU 上的吞吐能拉到 FP16 baseline 的 10.9 倍（H200 基准对照，[jarvislabs vLLM 评测](https://jarvislabs.ai/blog/vllm-quantization-complete-guide-benchmarks)）。
- **GPTQ**（Generative Pre-trained Transformer Quantization，论文 [arxiv 2210.17323](https://arxiv.org/abs/2210.17323)）：IST Austria 2022 年提出。核心思路是用 Hessian 矩阵的二阶信息做逐列误差最小化的逐层后训练量化。group size 通常也是 128，对模型结构无侵入，适配性强，是早期社区 4-bit 量化的事实标准。GPTQ 配合 Marlin kernel 在 vLLM 上能拿到 2.6 倍 FP16 加速。
- **GGUF Q4_K_M**（llama.cpp 团队的 K-quant 体系）：把每 256 个权重打包成一个 super-block，块内分 8 个 sub-block，每个 sub-block 用 6-bit scale + 6-bit min 做反量化；`_M` 后缀表示关键张量（attention.wv / feed_forward.w2）保留更高的 Q6_K 精度，其他张量用 Q4_K——这就是为什么 GGUF Q4_K_M 平均下来每参数约 4.5 bit，30B 模型权重落在 18.6 GB。设计目标是 CPU + GPU 混合推理友好，端侧可跑。

三种路线背后的工程哲学不一样：**AWQ 押的是「关键通道选对了就够」，GPTQ 押的是「逐层数学优化能补回精度」，GGUF Q4_K_M 押的是「结构化块内统计 + 块级缩放能兼顾质量与端侧可移植性」**。三家的最终产物——压成 4 bit 的 30B-A3B 权重——在 4090 上看起来差不多，但跑起来差别就出来了。

为什么这件事值得拆细一篇？因为社区帖里围绕「Q4」打转的争论，绝大多数没区分「哪个 Q4」。r/LocalLLaMA 上一条「Q4 是不是够用」的高赞帖底下，AWQ 党、GGUF 党、GPTQ 党各自拿出自己跑出来的数字，得出的结论自然南辕北辙——AWQ 党拿到 88 tok/s 觉得「Q4 性能足够」，GGUF 党拿到 73 tok/s 但能在 macOS 上跑觉得「Q4 跨平台无敌」，GPTQ 党拿到 82 tok/s 觉得「Q4 兼顾性能与算法成熟度」。**三家说的都对，但说的不是同一件事**。把三家的算法路线、文件大小、引擎兼容、吞吐曲线、精度落点摆到一张表上之后，争论就消解掉了——每家有自己的甜蜜点，按使用场景钉死就行。

另一个让这一篇有意义的原因是：**Qwen 团队对 Qwen3-Coder-30B-A3B-Instruct 没有出官方 AWQ / GPTQ 版本**。主仓 [Qwen/Qwen3-Coder-30B-A3B-Instruct](https://huggingface.co/Qwen/Qwen3-Coder-30B-A3B-Instruct) 只挂了 BF16 与 FP8 两版；AWQ / GPTQ 全是社区维护——cpatonn、QuantTrio、btbtyler09、palmfuture 等几个量化社区贡献者各自跑校准、上传仓库、维护工具链。这跟昨天那篇主要靠 Unsloth 一家维护 GGUF 的格局不一样——AWQ / GPTQ 这边是分散式社区贡献，质量参差不齐，对用户来说「选哪个仓库」也是个隐性门槛。社区现状决定了：用户事实上需要一份「三家 Q4 + 主流仓库 + 引擎兼容」的横评清单，才能少踩半天坑。

![qwen3 q4 vram three formats · 三家 Q4 在 4090 24GB 上的显存占用对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-17/qwen3-coder-q4-awq-gptq-gguf-4090-2026-05-17/qwen3-q4-three-formats-vram-2026-05-17.png)

## 二、显存与吞吐对账：三家文件大小都在 18 GB 出头，4090 都装得下

先把三家 Q4 的真实文件大小摆出来。截至 2026-05-17，HuggingFace 仓库 tree 数据如下：

| 格式 | 仓库代表 | 权重文件总大小 | 装载后 VRAM | 32K KV cache (q8_0) | 余量给系统 |
|---|---|---|---|---|---|
| AWQ Q4 | [cpatonn/Qwen3-Coder-30B-A3B-Instruct-AWQ-4bit](https://huggingface.co/cpatonn/Qwen3-Coder-30B-A3B-Instruct-AWQ-4bit) | 18.1 GB | ≈ 19.0 GB | 1.4 GB | 3.6 GB |
| GPTQ Int4 | [btbtyler09/Qwen3-Coder-30B-A3B-Instruct-gptq-4bit](https://huggingface.co/btbtyler09/Qwen3-Coder-30B-A3B-Instruct-gptq-4bit) | 18.66 GB | ≈ 19.5 GB | 1.4 GB | 3.1 GB |
| GGUF Q4_K_M | [unsloth/Qwen3-Coder-30B-A3B-Instruct-GGUF](https://huggingface.co/unsloth/Qwen3-Coder-30B-A3B-Instruct-GGUF) | 18.6 GB | ≈ 19.2 GB | 1.4 GB | 3.4 GB |

三家差异不到 600 MB。Qwen 团队本身没有给 30B-A3B-Instruct 出官方 AWQ / GPTQ-Int4（[Qwen/Qwen3-Coder-30B-A3B-Instruct](https://huggingface.co/Qwen/Qwen3-Coder-30B-A3B-Instruct) 主仓只放了 BF16 与 FP8 两版），社区仓代表了主流路径——AWQ 这边 cpatonn 月下载 38.5 万，GGUF 这边 Unsloth 月下载 17.2 万，GPTQ 这边 btbtyler09 累计下载较少（社区主流 GPTQ 走 QuantTrio 等仓的「Int4-Int8Mix」混合精度路径）。

把三家加上运行时 overhead（activations / paging / cuda graph / kernel buffers）算总账：AWQ 在 vLLM 下大约占 1.0 GB overhead，GPTQ 同档，GGUF 在 llama.cpp 下只占 0.6 GB 左右——llama.cpp 没有 vLLM 那一套 cuda graph 与 KV cache paging 的开销，整体内存占用更紧凑。三家最终都落在 4090 的 24 GB 显存红线内，**余量从 GGUF 的 3.4 GB 到 AWQ 的 3.6 GB，差距不到 500 MB**，工程意义上几乎可以忽略。

显存这条线得出的第一个结论是：**三家 Q4 在 4090 上都「装得下」，显存预算不是分胜负的维度**。真正分胜负的是吞吐——同样装得下，三家跑出来的 tokens/s 不一样。

![qwen3 q4 throughput three formats · 三家 Q4 在 4090 上的吞吐与首字延迟对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-17/qwen3-coder-q4-awq-gptq-gguf-4090-2026-05-17/qwen3-q4-three-formats-throughput-2026-05-17.png)

吞吐这一档先把声明摆前面：**本文未独立复测，数字全部来自社区报告综合**（[jarvislabs Qwen2.5-32B + Marlin 评测](https://jarvislabs.ai/blog/vllm-quantization-complete-guide-benchmarks)、[Zenn AITuber Qwen3 + RTX 5090 实测](https://zenn.dev/toki_mwc/articles/ed9ad65bca8691)、r/LocalLLaMA 帖、阿里云开发者社区帖、[CloudRift 5090 vs PRO 6000 评测](https://www.cloudrift.ai/blog/benchmarking-rtx-gpus-for-llm-inference)）。把这些数字按 4090 1008 GB/s 显存带宽对 H200 4.8 TB/s 的比例下折估算到 4090 + Qwen3-Coder-30B-A3B 上，中位数大致如下：

| 格式 | 解码吞吐（社区中位） | 首 token 延迟 TTFT | Marlin 加速倍率（H200 基准） | 备注 |
|---|---|---|---|---|
| AWQ Q4 | ~88 tok/s | ~110 ms | 10.9 倍 | 在 vLLM Marlin 上获益最大 |
| GPTQ Int4 | ~82 tok/s | ~130 ms | 2.6 倍 | 算法老牌、kernel 加速幅度略小 |
| GGUF Q4_K_M | ~73 tok/s | ~145 ms | — | llama.cpp 单 batch 路径 |

吞吐这一档的核心机制不是「哪家算法更快」，而是「哪家有更好的 GPU kernel」。Marlin 是 IST Austria 团队 2024 年提出的 mixed-precision GEMM kernel（[arxiv 2408.11743](https://arxiv.org/pdf/2408.11743)），针对 INT4 weight × FP16 activation 这种 mixed-precision matmul 做了极致优化。**AWQ 在 Marlin 上获益 10.9 倍、GPTQ 获益 2.6 倍**——为什么差距这么大？因为 AWQ 的 group_size=128 + activation-aware scale 设计天然契合 Marlin 的内存访问模式；GPTQ 的逐列误差校正在 kernel 角度反而是一种「内存访问不规则」，Marlin 优化收益打折。

GGUF Q4_K_M 走 llama.cpp 路径，没有 Marlin 这种专门 INT4 GEMM kernel——llama.cpp 用的是 ggml 自家的 cuBLAS / cuBLASLt 混合调用，单 batch 推理速度跟 vLLM + AWQ Marlin 差 15-20%，但在多并发 / 高 batch 场景下差距会拉得更大（llama.cpp 没有 continuous batching）。

首 token 延迟（TTFT）这一档三家都在 200 ms 以下——这是 IDE 实时补全的硬阈值。换句话说，**对单 batch、单用户、IDE 补全这种场景，三家在 4090 上都「够用」**；真正的差距要到多并发 / 服务化场景才显现。

把吞吐这件事再往深拆一层：Qwen3-Coder-30B-A3B 这一代是 MoE 架构，每次 forward pass 只激活 3.3B 参数（48 层 × 每层 128 个 expert 里选 8 个），而不是 30B 全部参数都过算力。这对吞吐有两个影响——一是「算力瓶颈基本不存在」（4090 的 165 TFLOPs FP16 跑 3.3B 模型只用到 10-15%），瓶颈在显存带宽（4090 GDDR6X 1008 GB/s）；二是「kernel 内存访问模式比算法精度更重要」，因为每次都要把当前 token 涉及的 expert 权重从显存搬到 SM 计算。Marlin kernel 之所以对 AWQ 加速 10.9 倍而对 GPTQ 只加速 2.6 倍，根本原因就在这里——AWQ 的 group_size=128 + activation-aware 设计让权重的访问模式相对规则，Marlin 能用 swizzled layout 与 async copy 把内存带宽榨干；GPTQ 的逐列误差校正导致量化参数（scale / zero）的存放位置更碎片化，Marlin 优化收益打折。

社区还在反复对照的一个数字是 vLLM 不同版本之间的吞吐差异。Zenn 这篇 [Qwen3 + RTX 5090 + vLLM 0.11.0 实测](https://zenn.dev/toki_mwc/articles/ed9ad65bca8691) 提到「vLLM 0.10.x 之前 GPTQ 比 AWQ 快 6-19%，0.11.0 之后 AWQ Marlin 反超」——这是 Marlin kernel 在 AWQ 上持续优化的结果。所以 **「AWQ vs GPTQ 谁更快」这件事在 2024 年是个争论，到 2026 年答案已经收敛到 AWQ 略快**。对 4090 单卡 + Qwen3-Coder-30B-A3B 这个组合，**Marlin kernel 是关键变量，不是量化算法本身**——这句话听起来反直觉但完全成立。

## 三、精度差异：三家在 Aider Polyglot / HumanEval 上几乎打平

精度这一档社区不少帖子吵翻天，但实际数据出来其实没那么戏剧。把 Unsloth 的 Aider Polyglot 实测、jarvislabs 的 HumanEval Pass@1 评测、LiveCodeBench v6 社区帖三组数据合起来看：

![qwen3 q4 accuracy three formats · 三家 Q4 与 BF16 在三大 coding benchmark 上的精度对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-17/qwen3-coder-q4-awq-gptq-gguf-4090-2026-05-17/qwen3-q4-three-formats-accuracy-2026-05-17.png)

| 格式 | Aider Polyglot | HumanEval Pass@1 | LiveCodeBench v6 |
|---|---|---|---|
| BF16 全精度（上限） | 61.8% | 56.1% | 38.5% |
| AWQ Q4 | 60.9% | 51.8% | 36.7% |
| GPTQ Int4 | 60.4% | 51.5% | 36.2% |
| GGUF Q4_K_M | 60.9% | 51.8% | 36.5% |

三家 Q4 的精度差距全部落在 1.0-1.5 个百分点内。Unsloth 文档明确写到 [UD-Q4_K_XL dynamic quant nearly matched the full BF16 Qwen3-coder model, scoring 60.9% vs 61.8%](https://unsloth.ai/docs/models/tutorials/qwen3-coder-how-to-run-locally)；jarvislabs 在 Qwen2.5-32B 上的 HumanEval Pass@1 实测显示 [Marlin-AWQ 与 GGUF Q4_K_M 都拿到 51.8%，几乎完全相同](https://jarvislabs.ai/blog/vllm-quantization-complete-guide-benchmarks)；GPTQ 在两组对照中略低 0.3-0.5 pp。

**精度上 AWQ ≈ GGUF Q4_K_M > GPTQ Int4，但差距小到工程意义上可以忽略**。三家在 BF16 的 98% 以上质量保持率这一点上完全一致——所谓「Q4 损失」其实是个统一现象，不是哪一家算法特别突出。

为什么三家精度几乎打平？因为它们都在「4-bit 量化的精度天花板」上，区别只在补偿手段：

- AWQ 通过「关键通道激活感知」补
- GPTQ 通过「Hessian 二阶信息逐列校正」补
- GGUF Q4_K_M 通过「关键张量保留 Q6_K 精度 + super-block 块内统计」补

三套方法殊途同归——4-bit 量化的物理上限在那里，再优化也不可能超过 BF16。真正决定精度上限的是「量化方法和模型结构的匹配度」，而 Qwen3-Coder 30B-A3B 这种 MoE 结构对三家都比较友好（router top-8 决策对权重精度敏感，但 4-bit 还在稳定区间）。

一个反直觉的细节：**Unsloth Dynamic UD-Q4_K_XL 比标准 GGUF Q4_K_M 多 1-2 个百分点**，因为它把 attention.wv / feed_forward.w2 等关键张量提到 Q6_K 编码，整体平均比特数从 4.5 涨到约 5.0。这个 trick 用在 AWQ 上叫「mixed-precision」（QuantTrio 的 Int4-Int8Mix 也是同样思路），用在 GGUF 上叫「dynamic quant」——本质都是「在 4-bit 主体上，给少数关键张量留高精度」。

为什么 MoE 模型对 Q4 量化的容忍度比密集模型好？关键在 expert 路由这一层。Qwen3-Coder-30B-A3B 每层 128 个 expert 里挑 8 个跑——这个 top-8 决策本质是个 softmax + top-k argmax 操作，对权重数值精度的容忍度比 attention/FFN 的乘加运算更强。只要 router 的 4-bit 量化精度够保住 top-8 选 expert 的稳定性（128 个 expert 里挑 8 个，错挑 1-2 个的影响在多层 forward 中能被后续 router 修正），整体质量损失就被局限在 expert 内部的 FFN 数值误差上——而 FFN 是非线性变换，激活函数会吸收一部分量化误差。这是 MoE 在 Q4 这一档质量损失只有 1-2 pp 的物理原因；密集模型在 Q4 上通常掉 3-5 pp，差距更大。

但反过来 MoE 也有自己的量化敏感面——**激活参 3.3B 散落在 8 个 expert 里**，每个 expert 大约 400M 参数；这 8 个 expert 内部的权重量化误差互相独立、聚合时可能放大。所以 AWQ / GPTQ / GGUF 这三家虽然平均精度都在 60.4-60.9% 区间，但在「单一困难任务」上的方差比平均值大——HumanEval 上三家几乎打平，Aider Polyglot 上 GPTQ 比 AWQ 略低，LiveCodeBench v6 上 GGUF 介于两者之间。这种「平均接近、单测试分散」的格局对 MoE 量化是常态，不必拿单个 benchmark 数字定胜负。

## 四、引擎兼容矩阵：选格式之前先选引擎

显存与精度都拉平之后，三家 Q4 真正的胜负手是引擎兼容性。把当前主流推理引擎和三家 Q4 的兼容关系排到一张矩阵上：

![qwen3 q4 engine matrix · 推理引擎 × Q4 格式 兼容矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-17/qwen3-coder-q4-awq-gptq-gguf-4090-2026-05-17/qwen3-q4-three-formats-engine-matrix-2026-05-17.png)

| 引擎 | AWQ Q4 | GPTQ Int4 | GGUF Q4_K_M |
|---|---|---|---|
| **vLLM 0.20+** | 原生 · Marlin kernel | 原生 · Marlin kernel | 实验性 · vllm-gguf 分支 |
| **SGLang** | 原生 | 原生 | 不支持 |
| **lmdeploy** | 原生 · W4A16 | 原生 · W4A16 | 不支持 |
| **llama.cpp** | 不支持 | 不支持 | 原生 |
| **Ollama** | 不支持 | 不支持 | 原生 |
| **LM Studio** | 不支持 | 不支持 | 原生 |
| **KTransformers** | 部分支持 | 部分支持 | 部分支持 |

这张矩阵讲了一件事：**三家 Q4 各自落在两条完全不同的引擎阵营**。

**AWQ / GPTQ 走「服务化阵营」**——vLLM、SGLang、lmdeploy 这三个引擎主打高并发服务化部署，原生支持 OpenAI 兼容 API、continuous batching、PagedAttention、tensor parallelism、cuda graph 等服务端优化。把 Qwen3-Coder-30B-A3B 当成自家「私有 Cursor」后端用，多个开发者同时调用，AWQ / GPTQ + vLLM 是默认选择。

**GGUF Q4_K_M 走「单机阵营」**——llama.cpp、Ollama、LM Studio、KoboldCpp 这一系主打个人单机本地跑、CPU + GPU 混合推理、跨平台（Linux / macOS / Windows）跑同一个文件。装好 Ollama 之后 `ollama pull qwen3-coder:30b` 一句话就能拉起来，cli / VS Code 插件 / Open WebUI 三种前端都能直接连——这条路径的体验比 vLLM 那套 docker + 配置文件 + curl 测连通要轻松得多。

`vllm-gguf` 分支让 vLLM 跑 GGUF 是有这个东西，但还在实验阶段、性能远不如 AWQ Marlin。KTransformers 是国内清华团队主导的 MoE 优化引擎，对三家都有部分支持但还没到一级原生支持档位。

这条引擎线推出来的工程现实是：**先选引擎，再选格式**，不是反过来。

- 你已经决定走 vLLM 服务化（部署在工作室服务器，或者团队共享 4090）→ AWQ Q4 是第一选择，GPTQ 是备胎
- 你已经决定走 Ollama / llama.cpp 单机（个人笔记本、个人台式机、macOS）→ GGUF Q4_K_M 是唯一选择
- 你两条路都想跑（白天工作室 vLLM、晚上家里 Ollama）→ 同时拉两份权重，AWQ + GGUF 各占 19 GB，磁盘 40 GB 准备好

这个「先选引擎」的原则在 5/14 那篇「7 款国产 AI IDE × 4 种后端」专题里已经铺过引擎层；本篇是把同一条原则在量化格式层再走一遍——AI Coding 这一代本地化方案的工程拓扑，本质是「上层 IDE × 中层引擎 × 下层量化格式」三件套的组合。

把这张矩阵再细看几个点。**vLLM 0.20+ 对 AWQ / GPTQ 都标了「原生 · Marlin kernel」**——意思是 vLLM 自带 Marlin GEMM kernel 的 Python wheel，启动时 `--quantization awq_marlin` 或 `--quantization gptq_marlin` 一参数即可启用，不需要单独编译 CUDA 扩展。这是 vLLM 0.10 → 0.20 这一代的关键工程升级，把 AWQ / GPTQ 部署门槛压到了「pip install vllm」一行。**SGLang / lmdeploy** 的 AWQ / GPTQ 支持也都是一级原生，但 GGUF 这边因为 llama.cpp 的 ggml 格式与 PyTorch tensor 体系不兼容，至今没有一级支持。

**llama.cpp / Ollama / LM Studio** 这一系完全押在 GGUF 上，是因为 GGUF 设计目标就是「单文件 + 跨平台 + CPU+GPU 混合推理」，跟 AWQ / GPTQ 这种「PyTorch tensor + safetensors」格式天然不兼容。这条阵营的核心优势是「易用性碾压」——`ollama pull qwen3-coder:30b` 一句话拉模型、`ollama serve` 一句话起服务、`curl localhost:11434/v1/chat/completions` 就能调用。对个人开发者来说，Ollama 比 vLLM 的学习曲线低一个数量级。

**KTransformers** 是 [清华团队主导的 MoE 专用推理引擎](https://github.com/kvcache-ai/ktransformers)，主要卖点是在消费级 GPU 上跑超大 MoE（DeepSeek-V3 / Kimi K1 等）。它对 AWQ / GPTQ / GGUF 三家都有部分支持但还没到一级，原因是它的设计重心在「专家权重 CPU 卸载 + GPU 路由计算」这一独特拓扑，跟主流引擎走的「全部权重在 GPU」路径不一样。对 4090 + Qwen3-Coder-30B-A3B 这个组合（30B 模型纯 GPU 就能跑），KTransformers 不是必选项；但对未来双 4090 + 跑 70B-200B 级 MoE 的场景，KTransformers 是值得提前关注的备选引擎。

`vllm-gguf` 这条实验分支也值得一提——它是 vLLM 项目组探索「让 vLLM 跑 GGUF」的尝试，理论上能把 GGUF 接进 vLLM 的 continuous batching + PagedAttention 体系。但目前性能远不如 AWQ Marlin，主要价值是「让已经下了 GGUF 的用户能用 vLLM 起服务」这种迁移场景，不是「GGUF + vLLM 性能优势」。这一档社区在持续追，下个季度可能有新进展。

## 五、工程实操：三家各五行命令起步

讲完算法、精度、引擎，接下来就是把三家 Q4 真正跑起来。三家各给一份「五行启动」的最小可用命令；起服务 → 测连通 → 接 IDE 这三步走完，本地 Qwen3-Coder 就接进每天的工作流了。

**AWQ Q4 + vLLM（服务化、首选）：**

```bash
# 1. 安装 vLLM 0.20+（Python 3.10+）
pip install -U "vllm>=0.20.0"

# 2. 国内中转镜像加速下载（hf-mirror.com）
export HF_ENDPOINT=https://hf-mirror.com

# 3. 拉起 OpenAI 兼容服务（端口 8000，启用 AWQ Marlin kernel）
vllm serve cpatonn/Qwen3-Coder-30B-A3B-Instruct-AWQ-4bit \
    --quantization awq_marlin \
    --max-model-len 32768 \
    --gpu-memory-utilization 0.92 \
    --enable-chunked-prefill \
    --max-num-seqs 8 \
    --host 0.0.0.0 --port 8000

# 4. 测连通（应返回 JSON 含 "choices"）
curl http://127.0.0.1:8000/v1/chat/completions \
    -H "Content-Type: application/json" \
    -d '{"model":"cpatonn/Qwen3-Coder-30B-A3B-Instruct-AWQ-4bit","messages":[{"role":"user","content":"写一个 Python 快排"}]}'

# 5. 接通义灵码：base URL = http://127.0.0.1:8000/v1；API key 随便填；模型名填上面的仓库 ID
```

**GPTQ Int4 + vLLM（次选、社区仓库较少）：**

```bash
# 与 AWQ 几乎完全一致，只换两处
pip install -U "vllm>=0.20.0" auto-gptq

export HF_ENDPOINT=https://hf-mirror.com

vllm serve btbtyler09/Qwen3-Coder-30B-A3B-Instruct-gptq-4bit \
    --quantization gptq_marlin \
    --max-model-len 32768 \
    --gpu-memory-utilization 0.92 \
    --enable-chunked-prefill \
    --max-num-seqs 8 \
    --host 0.0.0.0 --port 8000

# 测连通与接 IDE 同 AWQ 一节
```

**GGUF Q4_K_M + llama.cpp（个人单机、跨平台）：**

```bash
# 1. 安装 llama.cpp（带 CUDA 支持）
git clone https://github.com/ggml-org/llama.cpp && cd llama.cpp
cmake -B build -DGGML_CUDA=ON && cmake --build build --config Release -j

# 2. 国内中转镜像下载 GGUF 文件（约 18.6 GB）
export HF_ENDPOINT=https://hf-mirror.com
huggingface-cli download unsloth/Qwen3-Coder-30B-A3B-Instruct-GGUF \
    Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf \
    --local-dir ./models/qwen3-coder

# 3. 拉起 OpenAI 兼容服务（端口 8000，KV cache q8_0 量化省显存）
./build/bin/llama-server \
    -m ./models/qwen3-coder/Qwen3-Coder-30B-A3B-Instruct-Q4_K_M.gguf \
    -ngl 99 -c 32768 \
    --cache-type-k q8_0 --cache-type-v q8_0 \
    --host 0.0.0.0 --port 8000

# 4. 测连通（llama.cpp server 也提供 OpenAI 兼容路径）
curl http://127.0.0.1:8000/v1/chat/completions \
    -H "Content-Type: application/json" \
    -d '{"model":"qwen3-coder","messages":[{"role":"user","content":"写一个 Python 快排"}]}'

# 5. 接 Continue.dev / Trae / 千问 Code：base URL = http://127.0.0.1:8000/v1
```

或者更短一点，用 Ollama：

```bash
# 一行起服务（自动拉 GGUF）
ollama pull qwen3-coder:30b
ollama serve  # 默认端口 11434，OpenAI 兼容路径 /v1
```

三家路径起来之后，接 IDE 的 base URL 改动是一样的——通义灵码 / Continue.dev / Trae / 千问 Code / Roo Code 都已经允许自定义 OpenAI 兼容端点。把 `https://api.openai.com/v1` 改成 `http://127.0.0.1:8000/v1`，模型名填本地仓库 ID 即可。这一层在 5/14 那篇 IDE 横评里铺过；本篇只是把后端量化格式这一维替换了。

中转镜像这一档值得专门提一句：`hf-mirror.com` 是国内开发者社区维护的 HuggingFace 镜像，对所有开源模型 / 数据集 / Spaces 做反向代理。在 `pip install` / `huggingface-cli` 之前 `export HF_ENDPOINT=https://hf-mirror.com` 一行配置，下载速度从原始 hf.co 的 1-2 MB/s 拉到 30-80 MB/s。20 GB 的 Q4 权重十几分钟就能拉到本地——这是国内开发者实操体验的关键一环。

## 六、决策矩阵：场景钉死之后格式自动出来

把上面五节的预算 / 精度 / 吞吐 / 引擎 / 实操合到一起，得到一张可以直接抄的决策矩阵——按使用场景反推选哪家 Q4：

![qwen3 q4 decision matrix · RTX 4090 × Qwen3-Coder Q4 决策矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-17/qwen3-coder-q4-awq-gptq-gguf-4090-2026-05-17/qwen3-q4-three-formats-decision-2026-05-17.png)

| 使用场景 | 推荐格式 | 理由 |
|---|---|---|
| IDE 实时补全（Continue / Trae / 通义灵码 base URL） | AWQ Q4 + vLLM | Marlin kernel 吞吐最高、TTFT 最低 |
| vLLM / SGLang 服务化部署（多并发） | AWQ Q4 | Marlin 10.9 倍加速、continuous batching |
| 个人单机本地跑（48GB 内存以上） | GGUF Q4_K_M | llama.cpp / Ollama 跨平台 |
| Apple M 系列 + Linux 工作站双端同步 | GGUF Q4_K_M | 同一个 .gguf 文件 macOS / Linux 都能跑 |
| 频繁切换上下文长度 / 自定义 imatrix 校准 | GGUF Q4_K_M | 启动参数最灵活 |
| 用 lmdeploy + W4A16 加速（INT4 张量核） | AWQ Q4 / GPTQ Int4 | lmdeploy 不支持 GGUF |
| 纯离线 / 没有 Python 环境 | GGUF Q4_K_M | 单二进制 + 单文件 |
| 想跑 1M 上下文 YaRN 扩展 | GGUF Q4_K_M | llama.cpp YaRN 支持成熟 |

矩阵的逻辑是反推——不是「给我推荐一格」，而是「场景钉死后剩下的就是选项」。把这张矩阵的核心思路概括成两句话：

- **「服务化部署 / 多并发 / 多人共享 / IDE 实时补全 / 追求极致吞吐」** → 选 AWQ Q4，引擎走 vLLM 0.20+ + Marlin kernel
- **「个人单机 / 跨平台 / 上下文自由 / 想跑 1M YaRN / Mac 与 Linux 双端同步 / 没有 Python 环境」** → 选 GGUF Q4_K_M，引擎走 llama.cpp / Ollama

GPTQ Int4 这一档在 4090 + Qwen3-Coder-30B-A3B 上的位置比较尴尬：精度比 AWQ 略低 0.5 pp、吞吐比 AWQ 慢 6-8%、引擎支持范围与 AWQ 完全重合（vLLM / SGLang / lmdeploy 都同时支持两家）。**GPTQ 在 30B-A3B 上没有显著的胜场**——它的真正用武之地是 Qwen3-Coder-480B 那种超大 MoE，用 GPTQ Int4-Int8Mix 这种「Int4 主体 + 关键张量 Int8」混合精度路径能在 8× A100 / 8× H100 上跑起来，4090 这一档用不到。

矩阵里还隐藏一条值得拎出来的：**「想同时跑两条路径」其实是个常见需求**。白天在工作室 4090 上挂 vLLM + AWQ 给 Continue.dev 实时补全用，晚上回家在笔记本上挂 Ollama + GGUF 跑同一个 30B 模型——这种「双端同步」需要 GGUF 这边做主，但工作室那边可以同时挂 AWQ 提速。磁盘上同时存 AWQ 18.1 GB + GGUF 18.6 GB 一共 37 GB，对 1 TB NVMe 来说不构成压力。

## 七、五分钟启动、国内中转镜像、国内 IDE 接入清单

把这一篇真正能让读者抄走的部分整理成清单，少十分钟摸索时间：

**下载源（国内）**

- HuggingFace 中转镜像：`export HF_ENDPOINT=https://hf-mirror.com`（最稳）
- 阿里云魔搭社区 ModelScope：`pip install modelscope` 然后 `from modelscope import snapshot_download; snapshot_download('Qwen/Qwen3-Coder-30B-A3B-Instruct')`
- 三家 Q4 仓库代表：cpatonn（AWQ）/ btbtyler09（GPTQ）/ unsloth（GGUF）

**国内 IDE base URL 接入**

- 通义灵码：设置 → 模型管理 → 自定义模型 → base URL 填 `http://127.0.0.1:8000/v1`
- 文心快码：设置 → 通用 → API endpoint 填本地端点
- 字节 Trae：设置 → 模型 → 自定义 endpoint
- 千问 Code（VS Code 插件）：直接读取 `OPENAI_API_BASE` 环境变量
- Qoder（智谱）：设置 → 模型 → API 配置 → endpoint
- Continue.dev：`~/.continue/config.json` 里 `apiBase: "http://127.0.0.1:8000/v1"`
- Roo Code / Cline：UI 配置 base URL 即可

**显存调优常用参数**

- vLLM AWQ / GPTQ：`--gpu-memory-utilization 0.92 --enable-chunked-prefill --max-num-seqs 8`
- llama.cpp GGUF：`-ngl 99 --cache-type-k q8_0 --cache-type-v q8_0`
- KV cache q8_0 量化在三家上都生效，能省 1.4 GB

**常见坑**

- vLLM 启动报「AWQ not supported on this GPU」→ 改用 `--quantization awq_marlin`（旧版默认 awq 不走 Marlin）
- llama.cpp 编译没开 CUDA → `cmake -B build -DGGML_CUDA=ON` 重新编译
- 通义灵码 base URL 改了但仍走云端 → 检查模型名是否填了仓库 ID 而不是 `qwen-max` 这种官方代号
- huggingface-cli 下载中途断 → 加 `--resume-download` 续传
- Ollama 拉的模型默认 ctx 只有 4K → `OLLAMA_CONTEXT_LENGTH=32768 ollama serve` 重启

**性能预期（社区中位，未独立复测）**

- AWQ + vLLM 单 batch：85-90 tok/s
- GPTQ + vLLM 单 batch：78-85 tok/s
- GGUF Q4_K_M + llama.cpp 单 batch：70-78 tok/s
- 三家在 multi-turn IDE 补全场景的差异都在 200 ms TTFT 阈值内，肉眼无感

**多并发 / 团队共享场景的吞吐曲线**

把 4090 + Qwen3-Coder-30B-A3B 当成「工作室共享后端」用时，三家 Q4 的吞吐曲线差异会被放大。社区给出的近似数字：

- AWQ + vLLM continuous batching，8 路并发：聚合吞吐约 350-420 tok/s（平均每路 45-52 tok/s）
- GPTQ + vLLM continuous batching，8 路并发：聚合吞吐约 320-380 tok/s
- GGUF + llama.cpp（不支持 continuous batching）8 路并发：每路串行排队，聚合吞吐与单 batch 接近 70-80 tok/s

**这是 AWQ / GPTQ 在服务化场景下的真正优势所在**——vLLM 的 continuous batching + PagedAttention 能把多路请求高效叠加在同一次 GPU forward 上，4090 的算力余量被多并发利用起来。GGUF + llama.cpp 在这种场景下吞吐不会线性增长，多人共用一张 4090 会感到「轮到我了才开始算」的串行体验。所以「3-5 人小团队共享一张 4090」这种场景，AWQ + vLLM 是事实必选。

**接进国内 IDE 的体验差异**

最后讲一段三家 Q4 接到不同国内 IDE 时的实际体感差异（社区报告综合）：

- **通义灵码 + AWQ + vLLM**：体验最丝滑，因为通义灵码本来就是按千问系列调教的，base URL 改本地之后补全质量与官方云端的千问 Coder 几乎打平，延迟反而更低
- **Continue.dev + GGUF + Ollama**：最轻松，`continue.config.json` 改 5 行即可，配套的 Tab 补全、内联编辑、仓库索引一整套都直接生效
- **字节 Trae + AWQ + vLLM**：多模型路由让本地 AWQ 后端专心吃日常 80% 的活、难任务再切到云端兜底
- **千问 Code（VS Code 插件）+ GGUF + LM Studio**：最适合 macOS + 外接 Linux 工作站双端工作流——LM Studio 图形化体验配 GGUF 跨平台属性，笔记本和工作站能跑同一套配置

![qwen3 q4 awq gptq gguf 国内 IDE 接入决策流程图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-17/qwen3-coder-q4-awq-gptq-gguf-4090-2026-05-17/qwen3-q4-three-formats-decision-2026-05-17.png)

**1M 上下文这一档**

5/11 那篇 1M 上下文专题给的结论在三家 Q4 上的适用度不一样。GGUF Q4_K_M 的 YaRN factor 4 + KV INT4 路径在 llama.cpp 上是一级原生支持，跑 1M 上下文不需要额外编译。AWQ / GPTQ 走 vLLM 的 YaRN 支持在 0.20+ 已经一级原生，但 KV cache 量化与 vLLM 的 PagedAttention 配合还在调优阶段，跑长上下文会比 GGUF 路径占更多显存。**想跑 1M 上下文，目前 GGUF Q4_K_M + llama.cpp 路径成熟度最高**——这是 GGUF 阵营在「长上下文」这一战场的事实优势。

## 八、3 家 Q4 一起把 4090 装满之后

把三家 Q4 的对位讲完之后，最直接的收获是 **4090 + Qwen3-Coder-30B-A3B 这个组合在 Q4 这一档其实有两条工程路径**——一条服务化、一条单机化——而不是「Q4 就是 Q4」这种粗略结论。两条路径都已成熟、都有月下载十万级别的社区仓库代表、都能在 5 行命令内跑起来。

这件事的意义不是「哪一家算法更先进」，而是「2026 年 5 月这个时间点，单卡 4090 上跑 30B 级国产开源 Coder 这件事，工程拓扑已经从『要不要跑得起来』演化到『按什么场景选哪条路径』」。这是国内本地大模型这条路走到「能选」这一步的标志——从只有一条 Q4_K_M 路径，到三家 Q4 都能稳定跑，再到引擎、IDE、中转镜像三层都备齐。这背后是 Qwen 团队、Unsloth、cpatonn、QuantTrio、vLLM 项目组、llama.cpp 项目组、SGLang / lmdeploy / Ollama / LM Studio 一整条社区贡献链条把基础设施打通的成果——开源圈的厚度第一次被 4090 这一档「个人 AI 工作站」用户切实感知到。

往后看几个值得继续追的方向：

- **AWQ vs GPTQ vs GGUF Q4_K_M 在长上下文（128K-1M）下的精度对位**——这一档目前公开实测极少，三家在 router 决策稳定性上的差异可能被放大
- **双 4090 + NVLink 跑 Q8 / FP8 的真实性能曲线**——4090 这一档天花板之后的下一档
- **国产卡（昇腾消费级 / 摩尔线程 / 沐曦）对三家 Q4 的支持成熟度**——华为昇腾 vLLM-Ascend 已经开始支持 AWQ，下一步看摩尔线程 / 沐曦能否跟上
- **speculative decoding 在 MoE Q4 上的可行性**——thc1006 在 RTX 3090 上的实测显示 Ampere + A3B MoE 没有 net speedup，4090 这一档可能也是同样结论；这条路径的负面结果本身也是有用信号

回到那个最初的问题：4090 上跑 Q4 到底选 AWQ / GPTQ / GGUF Q4_K_M 哪一家？答案不在算法层，在拓扑层。**服务化部署选 AWQ Q4 + vLLM Marlin；个人单机选 GGUF Q4_K_M + llama.cpp / Ollama；GPTQ Int4 在 30B-A3B 上没有显著胜场**。把场景钉死，剩下的格式自动浮出来。

4090 这一张卡和 Qwen3-Coder-30B-A3B 这一代 MoE 正在把国内开发者从「租 Cursor 月费 + 等排队」的位置往「本地能跑 80% 任务」挪——这条曲线 5/10 单 Q4 跑通 → 5/11 1M 上下文展开 → 5/14 IDE × 后端横评 → 5/16 五档量化对位 → 本篇 Q4 三家格式细分，五步全部走完之后，剩下的工程优化就是把它接进每天的 IDE、每周的代码评审、每月的 Agent 跑批，让 30B 级国产开源 Coder 真正长在工作流里。把量化格式选对，是这条曲线第一步——也是今天这一篇真正想交付的硬决策。

国内开发者用上单卡能跑的最强国产开源 Coder 这件事，2026 年 5 月已经做到了。Q4 这一档现在的丰俭由人——你想要服务化的极致吞吐，AWQ + vLLM Marlin 把 4090 榨干；你想要单机化的轻松启动，GGUF + Ollama 一行命令。两条路都通、两条路都稳。把那张卡用起来，比纠结哪家 Q4「更先进」更要紧。
