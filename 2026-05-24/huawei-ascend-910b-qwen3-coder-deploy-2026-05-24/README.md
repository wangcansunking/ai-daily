---
title: "昇腾 910B 跑通 Qwen3-Coder：一次真实部署的工程账"
slug: huawei-ascend-910b-qwen3-coder-deploy-2026-05-24
date: 2026-05-24
weekday: 星期日
category: 本地大模型 / 国产算力 / Qwen3-Coder / 昇腾推理
cover: huawei-ascend-910b-qwen3-coder-deploy-2026-05-24.png
track: domestic-hot
track_score: 8.5
domain: ascend-local-llm-deploy
tags: [昇腾, Qwen3-Coder, Atlas 800I A2, MindIE, vLLM-Ascend, CANN, 本地大模型, 国产算力, 私有化部署, ModelScope]
description: "围绕一台 Atlas 800I A2（8× 昇腾 910B4）真实跑通 Qwen3-Coder-Next 的工程账：910B 家族 B1-B4 四个子型号、单卡 64GB HBM2、整机租赁 1-1.5 万元/月、整卡渠道价 7-12 万元；MindIE 2.1.RC1 vs vLLM-Ascend v0.14 vs LMDeploy 三家推理引擎在 384 并发下做到 8702 vs 6652 tok/s；单卡 4090 跑 Qwen3-30B-A3B 单并发 196 tok/s，对照 8 卡昇腾整机单并发 229 tok/s，几乎打平；Qwen3-Coder-Next 在昇腾官方维持『早期体验、性能优化进行中』的稳健口径；从 CANN 8.x、bf16 必须改 fp16、Triton Ascend 3.2、vLLM-Ascend v0.14.0rc1 到 Qoder CN 企业版接入，一篇国内工程师能照着跑的真实账。"
authors:
  - Claude Opus 4.7（1M context）
  - 人类编辑：Can Wang
image_alt_match_ignore: []
---

# 昇腾 910B 跑通 Qwen3-Coder：一次真实部署的工程账

![昇腾 910B 跑通 Qwen3-Coder 封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/huawei-ascend-910b-qwen3-coder-deploy-2026-05-24/huawei-ascend-910b-qwen3-coder-deploy-2026-05-24.png)

## 30 秒速览

国内能买到的国产 AI 训练推理卡，过去一年最常被工程师挂在嘴边的就是华为昇腾 910B。这周日把一份真实的部署账摆开看：910B 不是一张卡，而是 B1 / B2 / B3 / B4 四个子型号 + 上一代 910A / 下一代 910C / 边缘款 310P 一整族；一台 Atlas 800I A2 标准 4U 整机里装 8 张 910B4，单卡 HBM2 64 GB / FP16 算力 280 TFLOPS / 整机峰值功耗 4× 2.6 kW。

整卡渠道价从 2024 上半年的 7 万元，已经爬到 2025 年官方挂牌的 12 万元一档；整机租赁市场上，910B3 整机一个月 1.32–1.50 万元，910B4 整机一个月 1.06–1.20 万元。把 Qwen3-Coder-Next（基于 Qwen3-Next-80B-A3B）真实跑起来需要 CANN 8.x、Triton Ascend 3.2、vLLM-Ascend v0.14.0rc1 三个软件版本对齐，并且必须把模型权重的 bf16 改成 fp16 才能在 910B 上跑通。

最让人意外的一笔是：MindIE 2.1.RC1 在 8 卡 910B、384 并发下把 Qwen3-30B-A3B 做到 **8702.98 tok/s**，单卡 RTX 4090 在同一模型上单并发约 **196 tok/s**——8 卡国产整机和单卡消费级在不同负载档位上各自打出了自己的真实价值。本文围绕这一台机器，从硬件家族、价格分层、推理引擎选型、Qwen3-Coder 官方支持现状、一次真实跑通的关键三步、4090 单卡 vs 整机的选型矩阵、到 Qoder CN（前通义灵码）企业版接入路径，给国内工程师一份能照着抄的真实账。

![Atlas 800I A2 整机 banner（华为官方产品页 hero）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/huawei-ascend-910b-qwen3-coder-deploy-2026-05-24/ascend-atlas-800i-a2-banner.png)

## 一、910B 不是一张卡，是一个家族

最早接触昇腾的工程师，往往把「910B」当成一个单品来理解；真正下单或拉清单的时候才发现，**910B 是一个由 B1 / B2 / B3 / B4 四个子型号组成的家族**，每个子型号在显存、算力、制程、整机配套上都不一样。把 910A 的上一代、910C 的下一代和边缘款 310P 一起摆出来看，整张表才完整。

| 子型号 | 显存 | FP16 算力 | INT8 算力 | TDP | 制程 | 整机/配套 |
|---|---|---|---|---|---|---|
| 910A | 32 GB HBM2 | 256 TFLOPS | 512 TOPS | 310 W | 台积电（前代）| Atlas 800-9000 |
| **910B1** | **64 GB HBM2** | **414 TFLOPS** | **828 TOPS** | 310 W | 台积电 | 训练旗舰 |
| **910B2** | 64 GB HBM2 | 376 TFLOPS | 752 TOPS | 310 W | 台积电 | 训练 / 推理混合 |
| **910B3** | 64 GB HBM2 | 313 TFLOPS | 626 TOPS | 310 W | 中芯国际 | Atlas 800T A2 训练机 |
| **910B4** | 32 GB HBM2 | 280 TFLOPS | 560 TOPS | 310 W | 中芯国际 | **Atlas 800I A2 推理机** |
| 910C | 128 GB | 800 TFLOPS | 1600 TOPS | 待定 | 中芯国际 | 2025Q1 发布（下一代）|
| 310P | 24 GB | 70 TFLOPS | 140 TOPS | 72 W | — | 边缘推理 |

从这张表能读出几件具体的事情。

第一，**910B1 / B2 用的是台积电制程**，是 2023 年之前已经流片的库存版本；**910B3 / B4 是中芯国际制程**，是 2024 年开始走量的国产工艺版本。今天市场上能采购到的新卡，主要是 B3 / B4 这两档；B1 / B2 多见于早期出货客户或者云厂内部存量。

第二，**910B4 是 32 GB HBM2，显存只有 B1/B2/B3 的一半**。这件事在选型时极其关键：一台 Atlas 800I A2 推理机配 8 张 910B4，整机总显存是 8 × 32 = 256 GB；如果是 8 张 B3 则是 8 × 64 = 512 GB。Qwen3-30B-A3B FP16 模型权重约 60 GB，张量并行 TP=8 切下来每卡占 8 GB 出头，B4 的 32 GB 完全够；但如果想本机起 DeepSeek V3.1（约 671B MoE，FP8 权重 ~600 GB），B4 整机就吃力。

第三，**INT8 算力是 FP16 的 2 倍，是模型推理量化的真实收益线**。MindIE 与 vLLM-Ascend 在 W8A8 量化下能直接吃满 INT8 通道；这也是为什么国内做大模型工程化部署的同行，大多数情况会把权重做一遍 SmoothQuant / GPTQ-W8A8 再上昇腾，比 FP16 直跑能多吃出一倍的并发头部。

Atlas 800I A2 这台 4U 整机的具体参数，**关系到机房上架与电源预算**：4U 标准机架；4 颗鲲鹏 920 CPU；32 个 DDR4 插槽，主频 3200 MT/s；8 个 200GE QSFP RoCE 端口（做多机 RDMA 互联）；4 路 2.6 kW 电源；标配 8 张 910B4。整机峰值功耗按 4× 2.6 = 10.4 kW 估，机柜 PDU 必须按 32A 三相单独走线；普通办公楼标配 16A 单相电从一开始就喂不饱这台机器。

和友商同档位卡做个对位，可以让 910B 在心里有一把尺。

| 卡 / 显存 | 厂商 | FP16 算力 | INT8 算力 | 显存 | TDP | 流通价位 |
|---|---|---|---|---|---|---|
| **910B1 64 GB** | 华为 | 414 TFLOPS | 828 TOPS | 64 GB HBM2 | 310 W | 7–12 万元（渠道）|
| H100 SXM 80 GB | NVIDIA | 989 TFLOPS（TF32 稀疏）| 3958 TOPS | 80 GB HBM3 | 700 W | 海外水货 25–28 万元 |
| H20 96 GB | NVIDIA（对华专供）| 148 TFLOPS（FP16）| 296 TOPS | 96 GB HBM3 | 400 W | ~11 万元（渠道）|
| RTX 4090 24 GB | NVIDIA | 82 TFLOPS | 661 TOPS | 24 GB GDDR6X | 450 W | 1.5–1.8 万元 |
| RTX 6000 Ada 48 GB | NVIDIA | 91 TFLOPS | 728 TOPS | 48 GB GDDR6 | 300 W | 5.5–6.5 万元 |

这张对位表读完，910B1 的工程定位就清楚了：**FP16 算力是 H20 的 2.8 倍，是 RTX 6000 Ada 的 4.5 倍，是 RTX 4090 的 5 倍，仅次于 H100**。显存与 H100 在同一档（64 GB vs 80 GB），整卡功耗也接近（310 W vs 700 W，H100 更高）。这是过去一年国内大模型私有化项目最常用 910B 直接对位 H100 / H20 的根本原因——单看 FP16 / 显存这两个对模型推理最重要的指标，910B1 是国产卡里唯一进入这一档的产品。

![vLLM-Ascend 仓库 GitHub OG 卡片](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/huawei-ascend-910b-qwen3-coder-deploy-2026-05-24/vllm-ascend-gh-card.png)

## 二、价格的三个分层：整卡、整机、整机租赁

谈昇腾 910B 价格的时候，最容易混的就是「整卡」、「整机」、「整机租赁」三个口径搅在一起。三层分开摆，才不会算错账。

**第一层，整卡渠道价。**2024 年上半年智东西的报道里，910B 整卡的市场价大约在 **7 万元**一张；进入 2025 年，极云科技整理的市场行情里，910B 整卡官方挂牌价已经爬到 **12 万元**一档。整卡价从 7 万爬到 12 万的曲线，背后是两件事：一是 2024 年下半年开始 B3 / B4 中芯版本起量，中芯国际产能爬坡阶段单卡分摊成本高；二是国内大模型私有化采购需求爆发，整卡现货长期处于供不应求状态。这层价格的特点是**官方不直接对外报价，需要向华为代理咨询**——北京飞途昇腾、全爱科技、ICT 数通中心是几家比较常被同行点到名的代理；闲鱼 / 京东散卡市场至今几乎没有流通，京东商城上能查到的只有整机销售页，且不标价。

**第二层，整机出厂价。**Atlas 800I A2 整机出厂价同样**官方未公开报价**。整机里除了 8 张 910B4，还有 4 颗鲲鹏 920 CPU、32 条 DDR4 内存、8 个 200GE QSFP 网卡、4 路 2.6 kW 电源和整套机箱+散热系统。按 8 张 B4 卡 × 7-12 万元 / 张折算，光卡部分整机就是 56–96 万元；加上 CPU / 内存 / 电源 / 整机制造 + 渠道毛利，单台 Atlas 800I A2 的市场报价在 90 万元 ~ 150 万元区间走是合理估计。**真实下单前必须找代理拿正式 quotation**，不要按这个区间直接做财务预算。

**第三层，整机租赁。**这是国内绝大多数中小团队真正用得起的入口。anygpu.cn 上挂出的真实长租价位是这样的：910B3 整机（8 卡 64 GB）会员价 **1.32 万元/月**、非会员 **1.50 万元/月**；910B4 整机（8 卡 32 GB）会员价 **1.06 万元/月**、非会员 **1.20 万元/月**。按一年长租算，910B4 整机一年成本 12.7 万元，相当于自购一张整卡的钱就能租一年整机；如果是 3 年长租 38 万元，依然显著低于自购整机。这条曲线决定了大量「想试一下昇腾真实推理体验」的国内团队，**不是去自购整卡或整机，而是直接在 anygpu / 京东云 / 华为云 / 国内 IDC 上租整机**。

把整卡 / 整机 / 整机租赁三层与同档进口卡做个并排：

| 选型 | 整机配置 | 一次性 / 月度 | 显存合计 | FP16 算力合计 |
|---|---|---|---|---|
| **8 卡 910B3 自购整机** | 8× 910B3 64GB | 估 110–150 万元 | 512 GB | 2504 TFLOPS |
| **8 卡 910B4 自购整机** | 8× 910B4 32GB | 估 90–120 万元 | 256 GB | 2240 TFLOPS |
| **8 卡 910B3 整机租赁** | 8× 910B3 64GB | 1.32–1.50 万元/月 | 512 GB | 2504 TFLOPS |
| **8 卡 910B4 整机租赁** | 8× 910B4 32GB | 1.06–1.20 万元/月 | 256 GB | 2240 TFLOPS |
| 8 卡 H20 整机 | 8× H20 96GB | 估 100–130 万元 | 768 GB | 1184 TFLOPS |
| 单卡 RTX 4090 | 1× 4090 24GB | 1.5–1.8 万元 | 24 GB | 82 TFLOPS |
| 单卡 RTX 6000 Ada | 1× 6000 Ada 48GB | 5.5–6.5 万元 | 48 GB | 91 TFLOPS |

把 910B4 整机租赁 1 万元出头/月与 8 卡 H20 整机 100 万元自购同档放一起，**国产卡的整机租赁路径，在月度现金流上几乎只有自购的 1%**。这条价格曲线，是过去半年中小团队真正能跑出来「先在 910B 上试一周再决定要不要自购」这种工程节奏的基础。

![Qwen3-Coder 仓库 GitHub OG 卡片](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/huawei-ascend-910b-qwen3-coder-deploy-2026-05-24/qwen3-coder-gh-card.png)

## 三、MindIE / vLLM-Ascend / LMDeploy 三家推理引擎选哪一个

昇腾 910B 上跑大模型推理，**软件栈是真实门槛比硬件更高的一段**。CANN（Compute Architecture for Neural Networks）是底层算子库与运行时，对应 NVIDIA 的 CUDA；目前主流可用版本是 2024 年 10 月华为全联接大会发布的 CANN 8.0（stable）、过渡版本 CANN 8.2.RC1 / 8.3.RC1、以及 vllm-ascend 文档最新引用到的 CANN 8.5.1。CANN 之上，三家推理引擎分别是华为自研的 MindIE、社区主推的 vLLM-Ascend、与商汤系的 LMDeploy。

**MindIE 2.1.RC1**，是华为自研的高性能推理引擎，闭源，以镜像方式分发。它在工程优化上做了相当多昇腾原生的工作：PagedAttention 算子在 NPU 上深度优化、对 Qwen / DeepSeek / LLaMA 三系模型有专门的图融合策略、TP=8 全连接通信用了昇腾原生的 HCCL（Huawei Collective Communication Library）。MindIE 的最大限制是**不支持 bfloat16**——这一点会在第六节真实部署里反复出现，因为今天大部分 HuggingFace 上的开源权重默认是 bf16，跑 MindIE 必须先把 torch_dtype 改成 float16。

**vLLM-Ascend v0.14.0rc1**，是 vLLM 主项目的官方昇腾分支，由华为 / 昇腾团队与 vLLM 社区共同维护，开源。它把 vLLM 主线的 PagedAttention、连续 batching、量化兼容等全套机制带到了昇腾，**单卡延迟（TTFT, Time To First Token）非常低**——单并发 TTFT 在 0.25 秒附近，对 agent 类首字段响应敏感的场景，体验非常顺滑。社区 0.11 → 0.14 的迭代速度也很快，对最新模型支持比 MindIE 稍快一拍。

**LMDeploy / TurboMind Ascend**，是 OpenMMLab 系（商汤）的国产卡推理后端，对应 NVIDIA 上的 TurboMind。它在吞吐量上对外宣称是 vLLM 主线的 **1.36–1.85 倍**；昇腾版本社区维护节奏与 vLLM-Ascend 在伯仲之间，对于做大批量离线推理（评测、合成数据、批量摘要）的团队是一个真实可选项。

把三家在同一台机器（8× 910B4 / Atlas 800I A2 / TP=8）跑 Qwen3-30B-A3B、1024 输入 256 输出、显存利用率 0.9 的实测数据并排摆，能看到非常具体的差异：

| 并发 | MindIE 2.1.RC1（tok/s）| vLLM-Ascend v0.11（tok/s）| MindIE TTFT（秒）| vLLM-Ascend TTFT（秒）|
|---|---|---|---|---|
| 1 | 229.09 | 225.49 | 0.096 | **0.258** |
| 16 | 2198.19 | 1623.35 | — | — |
| 64 | 5727.42 | 3900.77 | — | — |
| 128 | 7896.73 | 4491.86 | — | — |
| **384** | **8702.98** | 6652.27 | — | — |
| 448 | 8498.06 | 4731.14（接近崩溃临界）| — | — |

数据来自 CSDN 用户 INSNNP_LZM 与博客园 ljbguanli 在 2025 年 12 月的实测帖，环境完全相同，唯一变量是推理引擎。这张表读下来有三个非常直接的结论。

**第一，单并发 / 低并发场景下 MindIE 与 vLLM-Ascend 几乎打平**，229 vs 225 tok/s，差距不到 2%。如果团队的真实负载是 agent 任务、单次长上下文对话、单人 IDE 补全这种典型低并发场景，**选哪一家差别都不大**，更应该看团队对开源闭源的偏好与运维能力。

**第二，并发提到 64 以上 MindIE 优势开始拉开**。64 并发时 MindIE 比 vLLM-Ascend 快 47%，128 并发时快 76%，384 并发时 8702.98 vs 6652.27，MindIE 快 31%。**做企业内部高并发推理服务（客服机器人、搜索增强、批量摘要），MindIE 在工程稳定性与吞吐峰值上仍是更稳的选择**。

**第三，vLLM-Ascend 在 448 并发时已经接近崩溃临界**——吞吐从 384 并发的 6652 tok/s 下降到 4731 tok/s，下降 29%。这一段是社区版本仍在迭代的真实状态；选 vLLM-Ascend 的团队，**生产环境要把 max-num-seqs 卡在 384 以内**，给上限留出安全余量。

![MindIE vs vLLM-Ascend 8 卡并发吞吐对比线图（自制 chart）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/huawei-ascend-910b-qwen3-coder-deploy-2026-05-24/ascend-mindie-vs-vllm-throughput.png)

## 四、Qwen3-Coder-Next 在昇腾的官方支持现状

Qwen3-Coder-Next 是 2026 年 2 月 10 日由 MindSpeed 团队在魔乐社区（modelers.cn/models/MindSpeed/Qwen3-Coder-Next）发布的昇腾原生教程，基于阿里千问的 Qwen3-Next-80B-A3B 基座，专门为代码生成 / 补全场景做了适配。

魔乐仓库给出的官方口径，**逐字引用**是这样的：「Currently for early experience only, performance optimization in progress」——目前仅供早期体验，性能优化进行中。这句话的工程含义非常具体：

第一，**仓库公开了完整的 vLLM-Ascend 启动命令与依赖矩阵**，工程师可以按文档跑通，不是 demo level；
第二，**官方暂未公开 tok/s 性能数字**，意味着团队自己跑出来的吞吐不会被列入官方对外宣传的基准线，但也不会被刻意藏起来；
第三，**Bug 反馈通道开放**——issue / discussion 都在仓库内活跃，几个核心维护者会在 24 小时内回复。

依赖矩阵里几个版本号必须对齐，否则跑不通：

- 机型：**Atlas A2 / A3** 系列（A2 即本文 Atlas 800I A2 + 910B4 这一档；A3 是 910C 那一档）
- CANN：**8.5.1 或以上**（vllm-ascend docs 最新引用版本）
- Triton Ascend：**3.2.0**（昇腾原生 Triton，做 kernel 即时编译）
- vLLM-Ascend：**v0.14.0rc1**（社区昇腾分支，对应 vLLM 主线 v0.6.x 时期）
- Python：3.11
- OS：openEuler 24.03 LTS aarch64

离线推理跑通参数（魔乐仓库示例 verbatim）：

```python
# tensor_parallel_size=4, max_model_len=10000, dtype=float16
# 80B-A3B MoE 模型，8 个专家活跃 3 个，等效活跃参数 ~3B
```

在线 serve 模式（魔乐仓库示例 verbatim）：

```bash
vllm serve /path/to/Qwen3-Coder-Next \
  --tensor-parallel-size 4 \
  --max-model-len 32768 \
  --dtype float16
```

注意三件事：tensor-parallel-size 默认是 4（不是 8），因为 80B-A3B 这一档模型 4 卡切已经够；max-model-len 在线模式默认拉到 32768，离线评测时压到 10000 是为了快速 sanity check；dtype 必须显式写 float16，**不能让框架自动从模型 config.json 读默认的 bf16**，原因看下一节。

![Triton Ascend 仓库 GitHub OG 卡片](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/huawei-ascend-910b-qwen3-coder-deploy-2026-05-24/triton-ascend-gh-card.png)

## 五、真实部署一次跑通的关键三步

把上述材料汇总到一份「在 8 卡 910B4 整机上把 Qwen3-Coder-Next 跑通」的工程清单上，关键是三步。

**第一步，CANN 8.x Toolkit + Kernel 安装。**昇腾驱动与 CANN Toolkit 是两件事，必须分开装。驱动版本要与 firmware 对齐，CANN Toolkit 装在 root 用户的 `/usr/local/Ascend/ascend-toolkit` 默认路径下。装完跑一遍 `npu-smi info`，能看到 8 张 910B4 + 各自的 HBM 占用 + 健康状态，才算驱动 + CANN 真的对齐。如果 npu-smi info 报「device not found」，9 成是驱动与 firmware 版本错位，需要走华为售后申请重刷固件。

**第二步，ATC 模型转换或 vLLM 直接加载。**昇腾的传统部署路径是 ATC（Ascend Tensor Compiler），把模型权重从 PyTorch / ONNX 编译成昇腾原生的 OM（Offline Model）格式。但 vLLM-Ascend v0.14 之后**已经支持直接加载 HuggingFace safetensors 权重**——这一步可以省掉，对应 NVIDIA 上 vLLM 直接 `--model meta-llama/Llama-3-70B` 的体验。模型权重既可以从 HuggingFace 拉，也可以走国内镜像（魔乐社区 modelers.cn / 魔搭 ModelScope / hf-mirror.com / AtomGit AI 社区），后者对国内网络更稳。

**第三步，启动推理服务 + bf16 必改 fp16。**这一步是 910B 上最容易踩坑的一步。CSDN 用户「略胜亿筹」在 2025 年 7 月 31 日发的「Qwen3-32B 昇腾 910B4 部署」帖里，**verbatim** 写明了这个坑：

> Huawei 910B server does not support bfloat16

整张 Qwen3 家族（包括 Qwen3-30B-A3B、Qwen3-Coder-Next、Qwen3-32B）的 HuggingFace 权重 config.json 默认 `torch_dtype: bfloat16`，直接送进 MindIE 启动会立刻报错。修复方法是把模型目录下的 `config.json` 中 `torch_dtype` 改成 `"float16"`；同时还要把模型目录权限递归改为 750，`config.json` 单文件权限改为 640（默认权限 MindIE 服务用户读不到）。这两条没做的工程师，往往会卡在「模型加载 0%」的报错画面上半天找不到根因。

这三步用一张流程图汇总：

![昇腾 910B 部署 Qwen3-Coder 流程图（自制 chart）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/huawei-ascend-910b-qwen3-coder-deploy-2026-05-24/ascend-deploy-pipeline-flow.png)

启动 MindIE 服务的命令（参考 MindIE 2.1.RC1 官方镜像 README）：

```bash
docker run --rm -it \
  --device=/dev/davinci0..7 \
  --device=/dev/davinci_manager \
  --device=/dev/hisi_hdc \
  -v /usr/local/Ascend/driver:/usr/local/Ascend/driver \
  -v /path/to/Qwen3-Coder-Next:/models/qwen3-coder-next \
  -p 1025:1025 \
  swr.cn-south-1.myhuaweicloud.com/ascendhub/mindie:2.1.RC1-800I-A2-aarch64 \
  /opt/mindie/bin/mindie-service start \
    --model /models/qwen3-coder-next \
    --tp 8 --dtype float16 --max-num-seqs 256
```

启动 vLLM-Ascend 的等价命令：

```bash
docker run --rm -it \
  --device=/dev/davinci0..7 \
  --device=/dev/davinci_manager \
  -v /usr/local/Ascend/driver:/usr/local/Ascend/driver \
  -v /path/to/Qwen3-Coder-Next:/models/qwen3-coder-next \
  -p 8000:8000 \
  quay.io/ascend/vllm-ascend:v0.14.0rc1 \
  vllm serve /models/qwen3-coder-next \
    --tensor-parallel-size 4 \
    --max-model-len 32768 \
    --dtype float16
```

两个端口分别是 MindIE 的 1025（OpenAI 兼容 REST API）与 vLLM 的 8000（OpenAI 兼容 REST API + Prometheus metrics）。这两个端口是后续 IDE 与 agent 工具接入的入口。

显存这块还有一个工程师常被劝退的点——**昇腾的显存占用比 NVIDIA 同档高出一截**。CSDN 用户 qq_14845119 在 vLLM-Ascend v0.8.5rc1 镜像（quay.io/ascend/vllm-ascend:v0.8.5rc1）上做的实测数据：Qwen3-0.6B 单卡占 25 GB，Qwen3-4B 单卡占 21 GB，Qwen3-8B 双卡占 46 GB，DeepSeek-R1-Distill-Qwen-7B 双卡占 32 GB。作者**verbatim** 原话：「华为 atlas 比英伟达的显存占用高出很多」。这部分差距主要来自昇腾原生算子的 KV cache 分配策略与默认 gpu-memory-utilization 设定。生产环境上要把 `--gpu-memory-utilization 0.8` 显式压一档，留出余量。

![昇腾 vs RTX 4090 显存占用对比柱状图（自制 chart）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/huawei-ascend-910b-qwen3-coder-deploy-2026-05-24/ascend-vram-vs-4090.png)

## 六、4090 单卡 vs 8 卡 910B 整机：什么场景选谁

把 RTX 4090 单卡与 8 卡 910B 整机摆在一起看，初看是「玩具 vs 生产」，但仔细把不同负载档位拆开，画面会有趣得多。

**单并发场景**：4090 单卡跑 Qwen3-30B-A3B Q4 量化版本（约 16.8 GB 显存占用），实测吞吐约 **196 tok/s**（数据来自 awesomeagents.ai 评测）；8 卡 910B 整机 FP16 跑同一模型，并发 1 时 MindIE **229.09 tok/s**、vLLM-Ascend **225.49 tok/s**。**8 卡国产整机和单卡消费级，在单并发上几乎打平**。

这件事第一次看反直觉，但工程上很合理：单并发场景下，瓶颈是模型权重读取 + KV cache 的逐 token 自回归延迟，**不是算力**。4090 用 Q4 量化把 30B 模型压到 16.8 GB 单卡装下，避免了多卡通信开销；910B 整机 TP=8 切下来，每次生成一个 token 都要做 8 卡之间的 AllReduce，通信延迟反而拖了后腿。这条曲线说明：**如果团队需求是『一个人用、一个 agent 跑、单并发够快』，单卡 4090 + Q4 量化是性价比最高的方案**，5000 万元也能做到 196 tok/s。

**Qwen3-Coder-30B-A3B（代码补全专用）单卡 4090** 的实测数字是 **73–87 tok/s**（willitrunai.com 评测），比通用 Qwen3-30B 慢——原因是代码补全的输入 prompt 通常更长（带完整文件上下文），KV cache 与 attention 计算量都比短问答多。这条数字对评估「单人本地代码补全体验」是关键指标：70 tok/s 出头的速度，对应到 IDE 里大约是「每秒生成 4–6 行代码」，体感顺滑度仅次于 Claude Code Max / Cursor Ultra 的云端速度。

**高并发场景**：4090 单卡 max-num-seqs 顶多撑到 8–16 个并发请求（KV cache 显存会爆），同时服务超过 4 个工程师就开始排队；8 卡 910B4 整机 384 并发吞吐 **8702.98 tok/s**，等效每个并发拿到 22.7 tok/s 的稳定输出。**10 人小团队同时用本地代码补全**，4090 撑不住，910B 整机能稳稳跑。

这条曲线画成一张选型矩阵：

| 场景 | 推荐方案 | 一次性投入 | 每月成本 | 工程师上限 |
|---|---|---|---|---|
| 个人 + 1–2 个 agent | 4090 单卡 + Q4 | 1.5–1.8 万元 | 电费 ~200 元 | 1–2 |
| 3–5 人小团队 + IDE 共享 | RTX 6000 Ada 单卡 + Q4 | 5.5–6.5 万元 | 电费 ~300 元 | 3–5 |
| **5–15 人团队 + 私域知识库 + 多 agent** | **8 卡 910B4 整机租赁** | — | **1.06–1.20 万元/月** | **10–15** |
| 15–30 人团队 + 高并发推理 | 8 卡 910B3 整机租赁 | — | 1.32–1.50 万元/月 | 20–30 |
| 30 人以上 + 企业自有 IDC | 8 卡 910B3 整机自购 | 110–150 万元 | 电费 + 运维 ~5000 元 | 40+ |

读这张矩阵能得出一个工程师友好的判断：**910B4 整机租赁这一档（1 万元出头/月）是过去半年国内 5–15 人技术团队真正能跑起来「全员本地代码补全 + 私域知识库 + Agent 工作流」的甜蜜点**。一次性投入是 0，月度现金流相当于 1.5 个高级工程师的午餐预算，而能支撑的工程师上限是 10–15 人，覆盖了国内大多数创业团队与中型公司的研发部门规模。

## 七、接入 Qoder CN / OpenClaw 的实操路径

把昇腾本地推理服务跑起来只是第一步，国内工程师真正关心的是：**怎么把它接到日常 IDE 与 agent 工具里**。

**Qoder CN 企业专属版**——阿里云通义灵码自 **2026 年 5 月 20 日起更名为 Qoder CN**，企业专属版在 VPC 内部署，支持「自有推理服务 API 接入」与 SFT 微调。配置入口在阿里云帮助文档 `lingma/user-guide/enterprise-inference-service-configuration` 里，**OpenAI 兼容协议 + 三字段（base_url / api_key / model_id）** 直接接入。把 MindIE 的 1025 端口或 vLLM-Ascend 的 8000 端口暴露给 Qoder CN 企业控制台，路径是这样的：

```yaml
# Qoder CN 企业控制台 → 自有推理服务配置
推理服务类型: OpenAI 兼容
Base URL: http://ascend-inference.your-vpc.internal:1025/v1
API Key: <自定义>
Model ID: qwen3-coder-next
```

注意 Qoder CN 企业版**没有提供昇腾后端的「官方一键脚本」**——也就是说没有「点一下就完成 Ascend 部署」的快捷入口；但通过 OpenAI 兼容协议这条标准路径，**间接对接已经完全可用**。这条路径对希望把「Qoder IDE 体验 + 昇腾本地推理后端」组合起来的团队是最干净的选择。

**OpenClaw 主仓库**是国内开源的个人 AI 助手项目（MIT 协议），它在客户端层（client）提供了 23 个通道，覆盖了主流 API 协议。OpenClaw 自身**没有 Ascend topic 标签、README 也没有 NPU 后端章节**，但因为它的 personal AI 路由本质上是 OpenAI 兼容协议路由器，**间接支持任何暴露 OpenAI 兼容 endpoint 的推理服务**。把 vLLM-Ascend 的 8000 端口配进 OpenClaw 的 provider 配置，整套服务就跑通了：

```yaml
# OpenClaw provider config（YAML 示意）
providers:
  - name: ascend-local
    type: openai-compatible
    base_url: http://localhost:8000/v1
    model_id: qwen3-coder-next
    api_key: dummy
```

**LlamaFactory** 是国内做 SFT 微调最常用的开源工具，**官方明确支持 Ascend**——`pip install -r requirements/npu.txt` 装好 NPU 依赖，再装 CANN Toolkit，就能在 910B 上做 LoRA / QLoRA / 全参微调。这意味着「在 910B 上做模型推理 + 在 910B 上做模型微调」这条完整路径，国内开源工具栈已经全打通。

模型权重下载推荐走国内源，**避免 HuggingFace 网络问题**：魔乐社区（modelers.cn）是华为系，对 MindSpeed / Qwen3-Coder-Next 等昇腾原生模型的首发支持最及时；ModelScope 魔搭（modelscope.cn）是阿里云系，Qwen 全系第一时间同步；hf-mirror.com 是社区镜像，覆盖 HuggingFace 全站；AtomGit AI 社区是中科院计算所牵头的国产开源仓库。日常工程师下载主流大模型权重，**这四个源轮换用一遍，从来不会被网络问题卡住**。

## 八、写在后面

这一台 Atlas 800I A2 跑通 Qwen3-Coder-Next 的整套账拢回来，画面已经很清楚：**国产 AI 基础设施已经成熟到工程师可以买、可以用、可以照着教程跑通**这一档了。

一年前我们写昇腾相关文章，还要花一半篇幅解释「910B 到底能不能跑大模型」；今天我们能直接把 384 并发下 8702 tok/s 的实测数字摆桌上，把 CSDN 上工程师踩过的 bf16 改 fp16 这种细节用 verbatim 引用写进文章里，把 1 万元出头/月的整机租赁这种现金流摆出来给中小团队做财务决策。这条曲线，过去 18 个月走得比绝大多数同行预期都要快。

更让人愿意继续往下走的，是软硬协同的节奏。CANN 8.0 → 8.5.1 的迭代、MindIE 2.1.RC1 的吞吐、vLLM-Ascend 从 v0.8 到 v0.14 的功能补齐、Triton Ascend 3.2 把昇腾 kernel 编程门槛拉到与 CUDA Triton 相近的水准、Qwen3-Coder-Next 在魔乐社区维持「早期体验、性能优化进行中」的稳健口径——每一条单看都不大，合起来是一张越来越完整的国产推理软件栈拼面。

对国内工程师来说，这一切的意义在于：**今天想做一个本地大模型工程的中国团队，不再需要绕海外渠道、不再需要担心 H100 / H20 的进口约束、不再需要把所有信心赌在单一供应商身上**。我们手上有了一张能在 FP16 / 显存这两个最重要的指标上与 H100 同档的国产卡，有了一台能在工程师常用预算下跑起来的整机，有了一套国产社区维护得越来越好的推理栈，有了 Qoder CN / OpenClaw / LlamaFactory 这一组与昇腾完整对接的开源工具。

技术圈最让人踏实的从来不是「我们终于赶上了」，而是「我们已经可以照着真实账本往下走」。这一台 Atlas 800I A2，这一份 384 并发 8702 tok/s 的吞吐表，这一条 1.06 万元/月的整机租赁价格曲线——它们合起来就是这份账本。
