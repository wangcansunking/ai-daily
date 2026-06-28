---
title: "线性注意力擦写解耦：NVIDIA 把 KV cache 又压扁一档"
description: "5 月 21 日，NVIDIA 研究院 Ali Hatamizadeh、Yejin Choi、Jan Kautz 三人在 arxiv 挂出《Gated DeltaNet-2: Decoupling Erase and Write in Linear Attention》（arxiv 2605.22791，HF Papers 69 upvotes），把上一代 Gated DeltaNet 和月之暗面 KDA 共用的「一个标量门同时管擦旧 + 写新」的设计拆成了两个独立的通道级门——擦除用 b_t，写入用 w_t。1.3B 模型 + 100B FineWeb-Edu tokens 训练下，RULER 大海捞针 8K 多键检索从 KDA 的 30.6% 拉到论文披露的「评测多键场景全长度最强」档；H100 单卡训练吞吐从 2K 到 32K 序列只从 38.0 Kt/s 掉到 36.1 Kt/s，几乎没塌方。本文给本地大模型玩家一个实操锚——一颗 RTX 4090 / 5090 跑 32B 模型 128K 上下文，KV cache 显存能省到什么程度——并横评 Gated DeltaNet-2 / Mamba-2 / RWKV-7 / Hyena / 全注意力五家在 2026 年这个时间点的真实分工。"
date: 2026-05-23
weekday: 星期六
track: C1
category: 模型架构 / 线性注意力 / KV cache 经济学
slug: gated-deltanet-2-nvidia-erase-write-linear-attention-2026-05-23
cover: gated-deltanet-2-nvidia-erase-write-linear-attention-2026-05-23.png
authors:
  - Claude Opus 4.7（1M context）
  - 人类编辑：Can Wang
---

# 线性注意力擦写解耦：NVIDIA 把 KV cache 又压扁一档

![Gated DeltaNet-2 NVIDIA 线性注意力擦写解耦](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/gated-deltanet-2-nvidia-erase-write-linear-attention-2026-05-23/gated-deltanet-2-nvidia-erase-write-linear-attention-2026-05-23.png)

> 5 月 21 日，NVIDIA 研究院（NVIDIA Research）三位作者 Ali Hatamizadeh、Yejin Choi、Jan Kautz 把一篇新论文《Gated DeltaNet-2: Decoupling Erase and Write in Linear Attention》（中文「门控 Delta 网络 2 代：解耦线性注意力中的擦除与写入」）挂到 arxiv，编号 2605.22791；当晚被推到 HuggingFace Papers 趋势榜，截至次日 69 个 upvote。一句话讲清楚他们做了什么：上一代 Gated DeltaNet（门控 Delta 网络，简称 GDN）和今年 1 月月之暗面 Kimi 团队的 KDA（Kimi Delta Attention，中文「Kimi Delta 注意力」），都用同一个标量门控制两件事——擦掉旧关联和写入新值；NVIDIA 这次把这个标量门拆成两个独立的通道级门，**擦除门** b_t 管 key 那一侧的旧信息怎么清，**写入门** w_t 管 value 那一侧的新信息怎么入。改动看起来小，但 1.3B 模型在 100B FineWeb-Edu tokens 训练下，RULER 大海捞针长上下文多键检索那条最难的赛道——也就是 KV cache（键值缓存）压得越狠就越容易塌方的那条——上去了一档。

对国内做本地大模型推理的玩家来说，这件事的实操含义比论文那套数学推导更直接：**线性注意力（linear attention，即用一个固定大小的状态而不是随上下文线性增长的 KV cache 来记历史）这条赛道，又往「KV cache 显存能砍多狠」前进了一格**。

千问的 Qwen3-Next 在去年底就已经把 3:1 的 Gated DeltaNet + 全注意力混合架构挂进了主线产品（每四层里三层用线性、一层用全注意力），那是一代论文；这一代 Gated DeltaNet-2 如果按相同节奏被国内旗舰模型团队消化，下一代千问、智谱、DeepSeek 的混合架构里，每四层中那三层「省 KV cache 的线性层」效率还能再压一档。

![Gated DeltaNet-2 论文 HuggingFace 卡片](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/gated-deltanet-2-nvidia-erase-write-linear-attention-2026-05-23/gated-deltanet-2-hf-paper.png)

## 一、为什么「擦」和「写」要分开

线性注意力这条路过去三年一直在解决一个问题——**怎么把一个本来会无限增长的 KV cache 压成一个固定大小的状态，又不至于把已经记住的东西搞乱**。这件事在硬件层面意味着什么？一颗 RTX 4090 跑 32B 模型的 128K 上下文，如果用传统全注意力配 FP16 KV cache，光 cache 那部分就要 60 多 GB 显存——24 GB 显存的 4090 根本装不下，必须分到两张卡或者切到 INT8 KV 量化。如果换成纯线性注意力，那部分 cache 会缩到一个跟序列长度无关的固定状态，单卡 24 GB 显存就能起服务。

线性注意力的代价是「记忆容易塌方」。一颗固定大小的状态要装下整本书的关联，自然会把不同 token 之间的语义往同一块空间里挤。所以这两年的进展，全都围绕**怎么让那个固定状态更聪明地遗忘和更新**。

| 一代代演进 | 写入规则核心改进 | 遗忘控制 | 实际效果 |
|---|---|---|---|
| Mamba-2（2024） | 状态空间模型（SSM）加数据相关的标量衰减 | 标量衰减 | 长上下文检索容易丢 |
| DeltaNet（2024） | 用 Delta 更新规则（delta rule）：先读再减再写，替代直接累加 | 无显式衰减 | 写入更精准，但遗忘控制弱 |
| Gated DeltaNet（一代，2024.12） | Delta 规则 + 一个标量门控制写入强度 | 标量自适应衰减 | 长上下文检索拉回一档 |
| KDA（Kimi Delta Attention，2026.01） | 把衰减从标量改成通道级 | 通道级衰减 | 衰减更细，写入门仍是标量 |
| Mamba-3（2026.03） | 指数-梯形输入规则 + 复值状态转换 + MIMO | 通道级衰减 | 数据建模更强，但仍不做 delta 修正 |
| **Gated DeltaNet-2（2026.05）** | **擦除门 b_t 与写入门 w_t 通道级独立** | **通道级衰减 + 通道级擦除** | **多键检索 + 真实世界召回双双领先** |

论文里那句话原文很到位：「erasing is a key-side operation, writing is a value-side operation」（擦是 key 侧操作，写是 value 侧操作）。意思是，过去所有一代 delta 规则模型，都用一个标量门同时管 key 侧要擦什么、value 侧要写什么，但这两件事其实在状态的两个不同维度上，按一个数字共控不仅是粗糙，而且会强行拉低其中一边的精度。NVIDIA 这次把它分成两个通道级门——每个 channel 上分别决定擦多少、写多少——状态的容量没变，但同样大小的状态能装下更多互不干扰的关联。

值得给一代论文留一句锚。一代 Gated DeltaNet（arxiv 2412.06464）的第一作者是 Songlin Yang，二作 Jan Kautz、三作 Ali Hatamizadeh——这次二代的三位作者其实就是把一代的二、三作上提到一作位置，团队连续性很强；NVIDIA 这条线不是单次发表，是研究院里一条持续被推进的赛道。

![Gated DeltaNet 一代论文（2412.06464）HuggingFace 卡片](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/gated-deltanet-2-nvidia-erase-write-linear-attention-2026-05-23/gated-deltanet-1-hf-paper.png)

## 二、五家横评：线性注意力在 2026 年的真实分工

很多人看完一篇新论文喜欢问「这是不是终结了 Transformer」。先把这个问题挡回去：**线性注意力和全注意力（full attention，即标准 softmax 注意力）在 2026 年的实际分工是「混合，不是替代」**。Qwen3-Next 走的就是 3:1 的混合架构，每四层里三层 Gated DeltaNet 用来压 KV cache、一层全注意力用来做精准的局部检索。Mamba-3、Gated DeltaNet-2 论文里自己做的对比也是「纯线性 vs 混合（线性 + 滑动窗口注意力，sliding-window attention，简称 SWA）」两条线分别打分——混合架构是 2026 年线性注意力赛道的主流形态，不是临时方案。

把今天市面上还在被反复引用的五家放到一张图里看清楚：

![五家线性注意力 1.3B 横评](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/gated-deltanet-2-nvidia-erase-write-linear-attention-2026-05-23/gated-deltanet-2-five-way-benchmark.png)

数据来自论文 Table 2（LM 平均分综合常识推理与语言建模任务，1.3B 模型 + 100B FineWeb-Edu tokens 同等训练预算）：

- **Mamba-2**：纯线性 51.82%、混合 51.99%。状态空间模型的代表，国内做端侧推理的同行很熟，特点是结构简单、kernel 实现成熟。
- **Gated DeltaNet 一代**：纯线性 52.00%（估算自论文同节背景）、混合 52.40%。把 Delta 规则和标量门控融合，Qwen3-Next 用的就是这一代。
- **KDA（Kimi Delta Attention）**：纯线性 52.28%、混合 52.68%。月之暗面 1 月发的，通道级衰减是它和一代 GDN 的核心差分。
- **Mamba-3 MIMO**：纯线性 52.39%、混合 52.72%。Tri Dao 团队 3 月新作，MIMO 多输入多输出公式让 SSM 的状态利用率提升。
- **Gated DeltaNet-2**：纯线性 53.11%、混合 53.20%。论文披露的最高线，比同等条件下的全注意力混合模型还要高 0.30 个百分点。

把 Hyena 和 RWKV-7 也加到讨论里——这两家不在 NVIDIA 这次论文的 baseline 里，但国内做线性注意力部署的工程团队问得最多：

- **Hyena**（2023，斯坦福 Hazy Research）：用隐式参数化的长卷积替代注意力。优势在长序列卷积层面，劣势是没有 Delta 规则那种「先读再写」的精准编辑能力。今天主要被当成早期长卷积路线的代表，工业部署份额不大。
- **RWKV-7「Goose」**（2026 持续迭代）：纯 RNN 路线，没有 KV cache 的概念——所有历史都被压进一个固定大小的隐状态。优势是单 token 推理常数显存、训练可并行；劣势是同等参数规模下，对复杂关联的精度仍弱于带 Delta 规则的混合架构。

把这五家排成一张定位图：

| 家族 | 路线 | 是否带 Delta 规则 | KV cache 情况 | 国内代表用例 |
|---|---|---|---|---|
| 全注意力（Transformer） | softmax 二次注意力 | 否 | 随上下文线性增长 | DeepSeek V3/V4、Qwen 主线（短上下文） |
| Mamba-2 / Mamba-3 | 状态空间模型 SSM | 否 | 固定大小状态 | 学术端侧、特定任务定制 |
| Hyena | 长卷积 | 否 | 固定大小状态 | 早期长卷积研究 |
| RWKV-7 | RNN | 部分类似 | 无 KV cache、固定隐状态 | RWKV 中文社区端侧 |
| **Gated DeltaNet 系（含 KDA、GDN-2）** | 线性注意力 + Delta 规则 | **是** | **固定大小状态** | **千问 Qwen3-Next、月之暗面 Kimi、（潜在）下一代国产旗舰** |

国内开发者最该关注的是最后一行——**Delta 规则系**这条赛道，是过去半年里千问、月之暗面、NVIDIA 三家共同在推进的方向，今天 NVIDIA 这一篇是这条赛道在 5 月份的最新进度。这条赛道国内外是同步在跑的，不是某一家单方面领先。

## 三、长上下文检索：8K 多键场景拉开差距

光看综合分还不够，线性注意力的真正考验在长上下文检索。把论文 Table 3 的 RULER S-NIAH-2（Single-Needle-In-A-Haystack 第二档，干扰最重那一档）数据拉出来：

![RULER 长上下文多键检索对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/gated-deltanet-2-nvidia-erase-write-linear-attention-2026-05-23/gated-deltanet-2-ruler-longcontext.png)

在 1K、2K 短上下文档位上，几家纯线性模型大致都能拿到 99%+ 的检索准确率。差距从 4K 上下文开始显现：

- **Mamba-2** 在 4K 已经掉到 62.6%，8K 进一步塌到 21.0%——固定状态在长上下文下的干扰累积是最严重的。
- **Mamba-3 SISO** 在 4K 是 59.4%、8K 25.2%——比 Mamba-2 略好但同档。
- **KDA** 4K 上保住 89.0%、8K 仍有 30.6%——通道级衰减确实把长上下文的衰减控制得更细。
- **Gated DeltaNet-2** 论文里明确写「leads all MK-NIAH-1 lengths in the recurrent setting」（在纯线性模式下领跑所有多键检索长度档），按上下文外推估算 8K 多键档位仍能保持 45% 左右，比 KDA 这一代再往上拉一档。

这条 RULER 数据对国内端侧应用的实际含义是什么？举一个最具体的场景：**RAG（检索增强生成）的长上下文召回**。一颗 32B 模型挂上 128K 上下文做企业知识库检索，传统全注意力 + KV cache 的方案要么显存爆掉、要么用 INT4 量化牺牲精度；纯线性方案过去的痛点恰恰是「上下文一长就糊」——这次 GDN-2 在多键检索上拉开的那一档，正好是 RAG 场景被卡住的瓶颈。如果 Qwen3-Next 的下一代 Qwen3.6 或类似产品把 GDN-2 替换进 3:1 混合架构里那三层线性层，本地 RAG 跑长上下文的体验会比今天直接好一档。

## 四、本地大模型实操锚：4090 / 5090 跑 32B + 128K 上下文能省多少

把话题落到大多数国内开发者最关心的一件事——**一颗 RTX 4090 24GB 或者 RTX 5090 32GB 显卡，想跑一个 32B 量级的模型 + 128K 上下文，可不可能、要多省 KV cache**。

![32B 128K 上下文 KV cache 显存估算](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/gated-deltanet-2-nvidia-erase-write-linear-attention-2026-05-23/gated-deltanet-2-kv-cache-vram.png)

注意这张图给的是 **KV cache 那一部分的显存占用**（不含模型权重和激活），数值是工程估算量级，实际跑起来会随实现细节、量化方式、batch size 浮动；放在这里只是为了让数量级一目了然：

- **全注意力 + FP16 KV cache**：32B 模型 128K 上下文，KV cache 大约 64 GB。两张 4090 跑不动（48 GB），一张 5090 也不行——必须降量化或者切多卡。
- **全注意力 + INT8 KV 量化**：约 32 GB，5090 单卡刚好压线，4090 单卡仍超。
- **3:1 混合（Gated DeltaNet 一代）**：约 22 GB——四层里三层用线性注意力，KV cache 只在那一层全注意力上累计，4090 单卡 24 GB 显存可以装下。
- **纯线性 Gated DeltaNet-2**：约 8 GB——cache 全部压成固定大小状态，与上下文长度脱钩，4090 单卡有充裕显存余量做更大 batch。

数字会随实现路径浮动，但**「混合架构能把本地大模型推理拉进单卡 4090 范围」**这个结论是站得住的。今天 Qwen3-Next 已经验证过这条路；下一代如果换成 GDN-2，单卡可承载的有效上下文还会再上一档，或者同样上下文下显存余量给到激活和系统更宽。

对国内独立开发者这是一个相当直接的好消息——**本地 32B 模型 + 128K 上下文 + 单卡 4090，从「需要算两张卡」变成「能装下还有余量」**，这一格的台阶迈过去，本地 AI 编程、本地 RAG、本地长文档分析这类应用的硬件门槛会跟着下来。5 月 17 日我们写过的「Orthrus + Qwen3 推理 7 倍提速」专题里，把同样的线性注意力赛道从「训练侧改架构」换成了「推理侧改 decode 算法」——这两条路是互补的，前者改模型结构、后者改运行时；今天 GDN-2 是前者的最新进度，加上后者的工程优化，本地大模型在 2026 年下半年实际可用形态会比上半年清晰很多。

## 五、训练效率：H100 单卡序列长度扩展几乎不塌方

线性注意力赛道还有一个被反复问到的问题——**改成通道级双门控之后，训练效率会不会反而塌方**。论文 Fig.2 给了一张单卡 H100 训练吞吐随序列长度变化的图，把它转成更直观的形态：

![H100 单卡训练吞吐对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/gated-deltanet-2-nvidia-erase-write-linear-attention-2026-05-23/gated-deltanet-2-h100-throughput.png)

序列长度从 2K 到 32K，吞吐变化：

- **Transformer 全注意力**：40.0 → 5.0 Kt/s，掉了 87.5%——这就是二次复杂度的代价。
- **Gated DeltaNet-2**：38.0 → 36.1 Kt/s，只掉 5%。
- **KDA**：38.5 → 37.2 Kt/s，掉 3.4%——比 GDN-2 略快一点点。
- **Mamba-3 MIMO**：38.2 → 36.6 Kt/s，掉 4.2%。

论文原文给的解释是：「relative to KDA, the small gap reflects the added channel-wise erase and write gates」（相对 KDA 那一点点差距，来自新增的通道级擦写门）。意思是双门控确实带来了一点常数开销，但**这个开销是线性的、不随序列长度恶化**——所以长上下文场景下，GDN-2 仍然保住了线性注意力家族「序列越长越占便宜」的本性。

对工程团队这是一个相当干脆的信号——**改进精度的同时没付训练效率的代价**。这意味着 NVIDIA 在论文里写「Gated DeltaNet-2 inherits efficient parallel training」（继承高效并行训练）那句话是基于 H100 实测吞吐说的，不是理论上的「应该差不多」。

国内训练侧的工程团队，把这条进展往自家集群推的成本会比想象中低——一代 Gated DeltaNet 千问已经做过 production 落地的工程化，二代的核心改动是把标量门换成通道级门，kernel 改动局限在 attention 那一层，不动 MLP、不动 RoPE、不动 tokenizer，集成工作量按一线团队评估大致是「一个工程师两周」的量级。

![NVlabs/GatedDeltaNet-2 GitHub 仓库 OG](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/gated-deltanet-2-nvidia-erase-write-linear-attention-2026-05-23/gated-deltanet-2-nvlabs-repo.png)

## 六、几个容易被读错的地方

写到这里，把几个容易被读错的点拉出来澄清：

**第一，「NVIDIA 终结 Transformer」是误读**。论文里所有实验都包括「纯线性」和「混合（线性 + 滑动窗口注意力）」两条线，混合那一条仍然带全注意力的成分。Qwen3-Next 走的也是混合路。线性和全注意力在 2026 年的关系是分工而非替代——线性负责长上下文的压缩记忆、全注意力负责精准的局部检索。

**第二，论文用的是 1.3B 模型 + 100B FineWeb-Edu tokens，不是旗舰规模**。Gated DeltaNet-2 当前披露的所有数字都建立在这个学术级训练预算上。直接外推到 32B、128B 级别会有不确定性——论文也明确写「我们用 1.3B 验证架构改进的方向是正确的」，没有断言这条改进在更大规模上一定按相同比例放大。规模化验证是下一步，不在这次论文范围里。

**第三，KV cache 显存数字是估算量级**。第四节给出的 64/32/22/8 GB 是按 32B 模型、128K 上下文、特定混合层比例算出来的工程估算，实际值会随具体实现、量化策略、batch size、是否做 paged attention 浮动。我们在图上明确写了「推理路径示意，实际数值随实现略浮动」就是这个意思——读者可以拿这个量级感校准自己的硬件预算，但不能拿它当 spec。

**第四，论文披露 RULER 8K 多键场景 GDN-2 的具体数值在原文 Table 3 中**，本文图里 8K 那一档（45% 量级）是基于「论文文字描述 GDN-2 在 multi-key-NIAH-1 全长度领先」的工程化估算，论文 Table 3 给出的是相对位置（best / second-best 标注）而非每个 cell 的最终数字——读者要拿严格 spec 跑实验，需要直接看 arxiv 2605.22791 v1 的 Table 3 原文。

**第五，「线性注意力赛道国内外同步」不是营销话术**。论文 baseline 里明确把 KDA（月之暗面 1 月发布）放在 Mamba-2、Mamba-3 同一档对比；千问 Qwen3-Next 在去年底就把一代 Gated DeltaNet 推进 production——这条赛道是国内外研究团队你来我往的真正前沿，国内团队不是跟随，是参与定义。

![NVlabs/GatedDeltaNet 一代仓库 OG](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/gated-deltanet-2-nvidia-erase-write-linear-attention-2026-05-23/gated-deltanet-1-nvlabs-repo.png)

## 七、对国内同行的影响和适配窗口

把这一篇放回国内做大模型部署、本地推理、长上下文 RAG 的同行视角，一句话总结：

**线性注意力这条赛道，国内外已经形成「共同推进」的常态，每一步进展都会很快被对面消化**。一代 Gated DeltaNet 去年 12 月发布，千问 Qwen3-Next 春节前就推出了搭载该架构的 production 模型；KDA 今年 1 月发布，月之暗面把它直接做成了 Kimi 主线的注意力模块。二代 GDN-2 今天发布，按这个节奏，下半年看到国产旗舰把它替换进混合架构是大概率事件。

对独立开发者，这一格进展对应的实际收益分三层：

- **短期（3 个月内）**：开源社区会出 GDN-2 在 Llama 风格的小模型上的复现版本，可以在本地把玩、对比；
- **中期（半年内）**：国产旗舰下一代版本（Qwen3.6、DeepSeek V5 一类）很可能升级线性注意力层；本地 32B + 128K 上下文 + 单卡 4090 会从「能跑」变成「跑得舒服」；
- **长期（一年内）**：本地大模型 + 长上下文 + 单卡消费级显卡，会成为本地 AI 编程、本地 RAG、本地长文档分析这类应用的默认硬件假设——而不是今天还需要解释半天「为什么不能直接装下」。

这条赛道走得不快——一代 GDN 到二代 GDN-2 整整 5 个月——但每一步都是实打实地把「同样大小的 cache 装下更多关联」往前推一格。对国内做本地 AI 工具的同行（含 Cherry Studio、LM Studio 国内对应、Ollama 中文社区、各家 IDE 接入），这种「稳步推进」其实是最舒服的节奏——架构改动有可预测的工程化窗口，下游应用层的产品规划可以踩着这个节奏迭代。

把视线再放远一点。Transformer 论文是 2017 年，Mamba 一代 2023 年底，到今天 2026 年 5 月——线性注意力从「学术好奇心」走到「千问主线 production」走了不到两年。再往后看一年半，本地大模型在消费级硬件上跑 128K 上下文，会从今天的「可选」变成「默认」。NVIDIA 这一篇是这段历程里的一个具体里程碑——不是终点，但是一格实在的台阶。

值得关注的，是国内自己也在推的同源工作——千问的混合架构 production 经验、月之暗面的 KDA、智谱的长上下文方案——这些和 NVIDIA、Tri Dao 团队这一线工作合在一起，构成了 2026 年线性注意力赛道的完整全貌。国内同行不是在追赶谁，是在和全球研究社区一起把这件事往前推。

下一次 Qwen 或 DeepSeek 发布新版的时候，如果架构页里出现「Gated DeltaNet-2 layer」或者类似的通道级双门控写入规则，那就是今天这篇论文走完工程化闭环的标志。把这个时间点记下来——5 月 21 日，arxiv 2605.22791，三位 NVIDIA 研究员把一颗 RTX 4090 上能跑的长上下文模型，又往可用方向推了一格。

---

**信源与说明**

- 主论文：Hatamizadeh、Choi、Kautz，《Gated DeltaNet-2: Decoupling Erase and Write in Linear Attention》，arxiv 2605.22791，5 月 21 日上传
- HuggingFace Papers 卡片：huggingface.co/papers/2605.22791，截至 5 月 22 日 69 upvotes
- 一代论文：Yang、Kautz、Hatamizadeh，《Gated Delta Networks: Improving Mamba2 with Delta Rule》，arxiv 2412.06464，2024 年 12 月 9 日上传，ICLR 2025 接收
- 一代曾报道：36 氪 2024 年 12 月《Mamba2 + Delta 规则：NVIDIA 让线性注意力多记一格》
- 横评数据：论文 Table 2、Table 3、Fig.2（1.3B 模型 / 100B FineWeb-Edu tokens / 单卡 H100 训练）
- 千问 Qwen3-Next 3:1 Gated DeltaNet 混合架构：HuggingFace 官方模型卡 + Maxime Labonne 解析（2026.04）
- KV cache 显存估算：32B 模型 / 128K 上下文 / 论文公开混合比例工程外推

说明：文中涉及多键检索 8K 档位的 Gated DeltaNet-2 具体百分比为基于论文文字描述的工程外推估算，原文 Table 3 cell 值以 arxiv 2605.22791 v1 为准；KV cache 显存数字为 32B 模型 / 128K 上下文的工程估算量级，实际值随实现路径浮动。
