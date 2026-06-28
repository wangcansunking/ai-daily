---
title: Qwen3-Coder 本地跑：4090 装不下，5090 才够
slug: qwen3-coder-next-consumer-gpu-runtest-2026-06-08
date: 2026-06-08
weekday: 星期一
category: 本地大模型
track: A
cover: qwen3-coder-next-consumer-gpu-runtest-2026-06-08.png
tags:
  - Qwen3-Coder-Next
  - 本地部署
  - 显存
  - 量化
  - 编程模型
description: 通义千问 2 月开源的 Qwen3-Coder-Next 是个 80B 总参数、每 token 只激活 3B 的 MoE 编程模型，号称消费级硬件能跑。但显存账没那么便宜：MoE 省的是算力不是显存，800 亿参数得全部驻留显存待命，连最省的 4-bit 都要 35-40GB——RTX 4090 的 24GB 装不下，5090 的 32GB 才是入场券。这篇把各量化档显存阶梯、三类消费级硬件实测的 25-60 tok/s、以及怎么接进 OpenCode/Cursor/Aider 一次讲清，帮你先想明白该配什么卡、上哪档量化。
---
# Qwen3-Coder 本地跑：4090 装不下，5090 才够

![一块消费级显卡前摆着 Qwen3-Coder-Next 的 80B 参数与 3B 激活标识，显存条几乎拉满](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-08/qwen3-coder-next-consumer-gpu-runtest-2026-06-08/qwen3-coder-next-consumer-gpu-runtest-2026-06-08.png)

通义千问今年 2 月开源的 Qwen3-Coder-Next，是个 800 亿（80B）总参数的编程模型，但每生成一个 token 只激活 30 亿（3B）参数。Apache 2.0 许可证，可商用。官方称它的编程能力接近 Claude Sonnet 4.5 级别，SWE-Bench Pro 得分 44.3%。

「只激活 3B」这个数字很容易让人产生一个误会：以为它对显卡的要求也就 3B 那么轻，手里一块 RTX 4090（24GB 显存）应该绰绰有余。

这篇文章的核心判断只有一句：**Qwen3-Coder-Next 激活只有 3B，但显存吃的是 80B 的总体重——4090 连最省的 4-bit 都装不下，5090 的 32GB 才是入场券；只要塞得进，靠激活 3B 它能跑出 25-60 tok/s 的可用速度。** 选硬件之前，你要先想清楚自己能上哪一档量化。

先看它到底有多大。

## 它到底多大：3B 是激活量，80B 才是显存账

![HuggingFace 上 Qwen3-Coder-Next 模型页，标注 80B 总参数、3B 激活、512 专家、256K 原生上下文](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-08/qwen3-coder-next-consumer-gpu-runtest-2026-06-08/qwen-source-hf-modelpage.png)

*来源：HuggingFace 上的 Qwen3-Coder-Next 模型页*

先把这个模型的几个关键数字摆清楚：

- **总参数 80B，激活 3B**：这是一个混合专家（MoE）模型，内部一共 512 个专家，每个 token 只把计算路由到其中 10 个专家身上，算下来每次激活约 3B 参数。
- **原生 256K 上下文**：放整个代码仓库当上下文绰绰有余。
- **SWE-Bench Pro 得分 44.3%**：这是一个偏真实工程任务的编程评测。官方称其能力接近 Claude Sonnet 4.5 级别——这句是厂商表述，能不能在你自己的项目上对得上，得自己跑了才知道。

关键在于 MoE 这个结构省的是什么。每个 token 只激活 3B，省下来的是**算力**：本来要把 80B 参数全乘一遍，现在只乘其中的 3B，计算量大幅下降，这也是 MoE 推理快的原因。

但显存省不了。原因是这样：推理时，下一个 token 会被路由到哪 10 个专家，是模型现算出来的，事先无法预知。这意味着 512 个专家、800 亿参数，任何一个都可能在下一步被点名，所以它们必须全部驻留在显存里待命。换句话说，你没法只把那 3B 装进显存、把剩下的 77B 放在硬盘上等召唤——专家是动态选的，谁都不能提前下场。

所以显存账要按 80B 的总体重来算，不是按 3B 的激活量算。这就是「只激活 3B」最容易踩的坑——它快，但它不小。

## 各量化档显存阶梯：连 4-bit 都要 35GB 起

![自制阶梯图：各量化档显存需求与 RTX 4090 的 24GB、5090 的 32GB 上限对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-08/qwen3-coder-next-consumer-gpu-runtest-2026-06-08/qwen-chart-vram-ladder.png)

80B 参数全驻显存，具体要多少，取决于你压到几位（量化档）。把各档的显存需求摆出来——下面的数字来自 Unsloth 官方运行文档和 DEV 社区本地实测整理两类来源交叉，不同来源略有差异，所以给的是区间：

| 量化档 | 显存需求 | 备注 |
|---|---|---|
| IQ3_XXS（极限 3-bit 压缩） | 约 30-34GB | Unsloth 称接近 BF16 质量 |
| Q4_K_XL（常用 4-bit） | 约 35-40GB | 见下方多源说明 |
| Q6_K（6-bit） | 约 50-55GB | |
| Q8_0（8-bit 高保真） | 约 65-70GB | Unsloth 表述为约 85GB |

4-bit 这一档值得单独说一句，因为不同来源给的数不一样：DEV 社区的本地实测整理是约 35-40GB，而 Unsloth 官方文档的表述是 4-bit 约需 46GB 的内存/显存/统一内存。两个数指的是同一档，差异来自上下文长度、KV 缓存预留、推理框架开销这些口径不同。把它们放一起看，4-bit 这一档的实际占用大致落在 35GB 到 46GB 这个区间——无论取哪个数，结论不变。

把这些数字往消费级显卡上一套，分界线就清楚了：

- **RTX 4090（24GB）**：连最省的 4-bit 都装不下。哪怕是 30-34GB 的极限 3-bit 压缩档，单卡 24GB 也塞不进。4090 单卡这一关，Qwen3-Coder-Next 直接出局。
- **RTX 5090（32GB）**：刚好够 4-bit 一档（取 35-40GB 区间下沿时贴边，实际多半要靠 offload 兜一点）。这是单卡跑这个模型的现实入场券。
- **想上更高保真档（6-bit / 8-bit）**：单张消费级卡都不够，得靠统一内存（unified memory，Mac 那种把内存当显存用的架构）或者多卡。

这就是开头那句话的由来：选硬件先看量化档。你能接受哪一档的精度，直接决定了你要配哪一档的硬件。

## 三类硬件实测速度：塞得进就有 25-60 tok/s

![自制柱状图：RTX 5090、Mac Studio M3 Ultra、AMD 7900 XTX 三类硬件单请求 tok/s 对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-08/qwen3-coder-next-consumer-gpu-runtest-2026-06-08/qwen-chart-tps-bench.png)

显存这关过了之后，速度怎么样？看三类消费级硬件第三方实测的单请求生成速度（来自 DEV 社区指南整理）：

- **RTX 5090（32GB）**：25-40 tok/s，跑的是 Q4_K_XL（4-bit）。
- **Mac Studio M3 Ultra（统一内存）**：40-60 tok/s，跑的是 Q8_0（8-bit）。
- **AMD Radeon 7900 XTX**：35-39 tok/s（社区实测整理）。

这里要先说清一件事：**这三组数字不是同条件横评**——量化档不一样（5090 跑 4-bit、M3 Ultra 跑 8-bit）、配置也各不相同，所以不能直接横着比谁快谁慢。它们各自说明的是「这台机器在它自己合适的档位上能跑多快」，而不是「同一份模型在三台机器上谁快」。

为什么 M3 Ultra 跑着更高保真的 8-bit，速度反而最快？因为它的统一内存容量大，能把 65-70GB 的 8-bit 档**完全塞进**去。而 Unsloth 的通用经验正是：量化档完全塞进设备时，能稳定跑出 20+ tok/s；一旦塞不下、需要 offload（把放不下的部分卸载到内存或磁盘），速度就会再打折。

![DEV 社区本地实测指南，整理 RTX 5090、Mac Studio、AMD 7900 XTX 三家硬件的运行速度](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-08/qwen3-coder-next-consumer-gpu-runtest-2026-06-08/qwen-source-dev-hardware-table.png)

*来源：DEV 社区 Qwen3-Coder-Next 本地实测指南*

所以速度这件事，其实和显存是同一个故事的两面：

- 塞得进（量化档完全驻留显存/统一内存）→ 20+ tok/s，编程 agent 用着不卡。
- 塞不下（要 offload）→ 速度打折，体验明显变差。

![Unsloth 官方《如何本地运行 Qwen3-Coder-Next》文档页，列出各量化档内存需求与运行命令](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-08/qwen3-coder-next-consumer-gpu-runtest-2026-06-08/qwen-source-unsloth-runlocal.png)

*来源：Unsloth 官方《如何本地运行 Qwen3-Coder-Next》文档页*

换句话说，前面那张量化档显存阶梯图，不光决定你能不能跑起来，也直接决定你跑得卡不卡。5090 跑 4-bit 能贴线塞进，所以能拿到 25-40 tok/s 这个可用区间；M3 Ultra 凭大容量统一内存把 8-bit 整个塞进，所以能上到 40-60 tok/s。

## 怎么接进编程 agent：一个 OpenAI 兼容接口的事

模型在本地跑起来之后，怎么把它接到你日常用的编程 agent 上？答案比想象中简单：起一个 OpenAI 兼容接口就行。

用 vLLM 部署是常见做法，两步：

```bash
pip install vllm>=0.19.0
vllm serve <模型路径>
```

启动之后，本地会有一个 OpenAI 兼容的接口（形如 `localhost:8000/v1`）。市面上主流的编程 agent 大多支持自定义这个接口地址，把它指过去就能用本地模型干活：

- **OpenCode**
- **Cursor**
- **Continue.dev**
- **Aider**

这几个工具都支持填一个 OpenAI 兼容的 base URL，把它换成你本地的 `localhost:8000/v1`，模型名填 Qwen3-Coder-Next，就能让这些 agent 走本地推理，而不是调云端 API。对于在意代码不出本机、或者想省 API 费用的人，这一步是把本地模型真正用起来的关键。

![vLLM 官方 Qwen3-Next 部署文档页，展示 pip 安装与 vllm serve 启动命令](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-08/qwen3-coder-next-consumer-gpu-runtest-2026-06-08/qwen-source-vllm-recipe.png)

*来源：vLLM 官方 Qwen3-Next 部署文档页*

接进来之后能不能好用，还是回到前面那条线：你的硬件能塞进哪一档量化、跑出多少 tok/s。塞得进、跑得动，本地编程 agent 的体验就立得住；塞不进、要 offload，agent 每次等回复都会肉眼可见地慢。

## 选硬件先看量化档：什么配置值得跑

说到底，Qwen3-Coder-Next 这个国产 80B 编程模型，对消费级硬件友好的是它的速度（激活 3B 算力省），不友好的是它的显存（80B 总参数全驻留）。所以决定你能不能跑、跑得好不好的，不是显卡型号本身，而是「你能上哪一档量化」。

按硬件给个明确的判断：

- **RTX 4090（24GB）单卡**：跑不了 Qwen3-Coder-Next，连 3-bit 极限压缩档都装不下。想本地跑这个模型，4090 这一关过不去，不必勉强。
- **RTX 5090（32GB）单卡**：能跑，但上限就是 4-bit 一档，实测 25-40 tok/s。适合「想本地试这个模型、能接受 4-bit 精度、单卡预算」的人——这是目前单张消费级卡的现实入场券。
- **Mac Studio M3 Ultra（大容量统一内存）**：能把 8-bit 高保真档整个塞进，实测 40-60 tok/s。适合在意输出质量、又想本地化的人——大内存统一架构在这种 80B 级 MoE 上反而占便宜。
- **AMD Radeon 7900 XTX**：社区实测 35-39 tok/s，是 N 卡之外的一条可行路线。

什么场景值得本地跑它？代码不想出本机、想省云端 API 费用、或者就是想把一个接近 Sonnet 4.5 级别（官方称）的开源编程模型攥在自己手里——这些场景下，只要你的硬件能塞进合适的量化档，Qwen3-Coder-Next 在本地是真能干活的。先量好自己显卡的尺寸，再决定上哪一档，这事就不会踩坑。
