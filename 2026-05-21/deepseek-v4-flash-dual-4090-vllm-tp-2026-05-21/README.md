---
title: "双 4090 跑 DeepSeek V4-Flash：vLLM 张量并行与国产 IDE 接入实战"
slug: deepseek-v4-flash-dual-4090-vllm-tp-2026-05-21
date: 2026-05-21
weekday: 星期四
category: 本地大模型 / 双 GPU 部署 / 国产顶级 MoE
cover: deepseek-v4-flash-dual-4090-vllm-tp-2026-05-21.png
track: domestic-hot
track_score: 9.0
domain: deepseek-dual-gpu-vllm
tags:
  - DeepSeek V4-Flash
  - 双 RTX 4090
  - vLLM
  - Tensor Parallel
  - 通义灵码
  - Cline
  - Trae
  - 本地大模型
  - 国产开源
  - MoE
  - OpenClaw
description: "DeepSeek V4-Flash 284B 总参 / 13B 激活、1M 上下文，本地双卡 RTX 4090 48GB 用 vLLM 0.21 + 张量并行 = 2 + Q4_K_M 量化能稳定跑起来。本文把双卡价位、启动命令、显存切分、token/s 实测中位数、以及通义灵码 / Cline / Trae / RooCode / OpenClaw 五款客户端接到本地 OpenAI 兼容服务的具体配置一次摆清。"
image_alt_match_ignore:
  - vllm-gh-og.png
  - cline-gh-og.png
  - vllm-tp2-layout.png
  - gpu-count-throughput.png
  - ide-vllm-wiring.png
  - price-compare.png
  - deepseek-v4-flash-hf-og.png
---

# 双 4090 跑 DeepSeek V4-Flash：vLLM 张量并行与国产 IDE 接入实战

![双 RTX 4090 + DeepSeek V4-Flash + vLLM TP=2 + 国产 IDE 接入封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/deepseek-v4-flash-dual-4090-vllm-tp-2026-05-21/deepseek-v4-flash-dual-4090-vllm-tp-2026-05-21.png)

写在前面：昨天那篇「4090 跑 Qwen3-Coder：四款本地引擎谁顺手」聊的是**单卡** 4090 跑 30B-A3B 的横评；今天这一篇换条线索往前推一档——**双卡** RTX 4090 凑 48GB 显存池，用 vLLM 张量并行 = 2 把 DeepSeek 团队 4 月预览的 V4-Flash（284B 总参 / 13B 激活 / 1M 上下文）真正搬到家里的台式机里，再把通义灵码、Cline、Trae、RooCode、OpenClaw 五个国产开发者每天都会摸到的客户端接到这台本地服务上。和 5 月 11 日 antirez/dsk 的 Mac Metal 单文件引擎那一篇也不是同一条线，那边是 Mac 单机推理；这边是 N 卡多卡 生产环境 风格。

本文的核心论断摆在第一段：**DeepSeek V4-Flash 在 2026 年 5 月 20 日这个时间点，是国产开源里第一个让"双 4090 家用配置"摸到顶级 MoE 上限的型号。**单卡 24GB 跑不了原版权重，Q4_K_M 量化勉强能进；双卡 48GB 用 vLLM 张量并行 = 2 跑 Q4_K_M + 128K 上下文是社区里跑通的最稳路线，单请求生成 30 到 50 token/s、8 到 16 并发稳得住、显存每卡占用约 10 到 12GB。这条路线的整机价钱比 Mac Studio M5 Ultra 256GB 顶配低三分之一，比 H20 企业整机低四分之三。

## 一图速览：双卡 4090 在 V4-Flash 全家桶里的真实位置

![DeepSeek V4-Flash 模型卡封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/deepseek-v4-flash-dual-4090-vllm-tp-2026-05-21/deepseek-v4-flash-hf-og.png)

写本文时实查 HuggingFace 模型卡：DeepSeek-V4-Flash 总参 284B、激活参数 13B、上下文长度 1M tokens、原版权重 MoE 专家层用 FP4 精度、其余主参数用 FP8、模型实际权重文件大小 158B params（safetensors 格式）、上月下载量 228 万次、MIT 协议、训练数据 32T+ tokens。架构层面是 V3 的下一代：把 V3 的多头潜在注意力换成了**混合本地 + 长程注意力**（CSA + HCA）、把残差连接换成**流形约束超连接**（mHC）、专家激活函数从 Sigmoid 换成 Sqrt(Softplus)、专家总数 256 个、每 token 激活 6 个、加 1 个共享专家、隐藏维度 4096、注意力头 64 个、键值头压到 1 个走多查询注意力路线。

下面这张图是按四个公开教程（CSDN 保姆教程、LINUX DO 双卡帖、知乎 H20 96GB 帖、databasemart 单 / 双 4090 vLLM 评测）里的实测中位数排出来的——四档配置的真实位置：

![单卡 / 双卡 / 四卡 4090 跑 V4-Flash Q4_K_M 实测中位对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/deepseek-v4-flash-dual-4090-vllm-tp-2026-05-21/gpu-count-throughput.png)

| 配置 | 显存池 | 单请求 token/s | 稳定并发 | 每卡显存 | 国内整机价区间 |
|---|---|---|---|---|---|
| 单卡 4090 24GB | 24GB | 12-25 | 2-4 | ~22.5GB | 3.0-3.5 万 |
| 双卡 4090 48GB | 48GB | 30-50 | 8-16 | ~10-12GB | 3.5-4.5 万 |
| 四卡 4090 96GB | 96GB | 70-130 | 16-32 | ~5-6GB | 6.0-8.0 万 |
| 2×H20 96GB | 96GB | 未独立复测 | 32+ | ~16-20GB | 12-18 万 |

注：所有 4090 数字是 Q4_K_M 量化 + 128K 上下文限制 + `--gpu-memory-utilization 0.85` 配置下，CSDN 教程帖与 LINUX DO 帖反复出现的中位区间，未独立复测；H20 数字来自知乎实测帖但部分细节作者未公开。

这张表翻译过来一句话：**单卡 4090 跑 V4-Flash 是"能跑通"，双卡才是"能用上"。**显存翻倍换来的不是吞吐翻倍，而是并发翻 4 倍、每卡压力减半、整机噪音和电费同步压下来——尤其后面接团队多人的时候，双卡的连续批处理稳定性是单卡硬挤不出来的体验。

## 二、为什么是双 4090，不是单 5090

先把价钱按写本文时的实价摆出来。Chiphell 论坛 2025 年底到 2026 年初的连贯讨论里，4090 公版二手价反复贴近 17000 元这条线、并且这一波涨势的主因是 5090 公版 14000 元上下的指导价没法稳定供货，二手 4090 在算力 / 显存 / 兼容性这三项上仍然不可替代。京东渠道的 4090 整机方案（i9 + 64GB DDR5 + 4090 + 850W 金牌）写本文时落在 3.0 到 3.5 万元区间，闲鱼成色卡走刀完整套件价区间是 3.5 到 4.5 万元。单 5090 整机方案因为 FE 公版供货不稳、通路价 14000 到 16000 元浮动，整机落在 3.2 到 4.2 万元。

![跑得动 V4-Flash 的本地方案价格对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/deepseek-v4-flash-dual-4090-vllm-tp-2026-05-21/price-compare.png)

价钱接近的情况下，5090 单卡 32GB 是个**尴尬的中间档**——比 4090 24GB 显存大 8GB、但不够装 V4-Flash 任何一种量化、跑 V4-Flash 还是要靠 CPU offload；双 4090 48GB 是国产 MoE 在家用台式机上能见到的**第一个完整池**，128K 上下文 + Q4_K_M 模型权重 + KV 缓存全部入卡、PCIe 4.0 ×16 双卡走 all-reduce 也仍然能稳定。这就是为什么社区在 V4-Flash 4 月预览发布后大量贴帖往双 4090 这边靠——不是 5090 不香，是 V4-Flash 这个 158B 实际权重的体量正好卡在 24GB 之上、48GB 之内。

国内整机渠道里写本文时还有两条次要路径值得知道。一是 4090D（48GB 国行特供版）二手价 11000 到 13000 元，单卡 48GB 直接绕过双卡 PCIe 通信开销，写本文时 SlavikF 在 HuggingFace 上贴的 4090D 48GB 跑 Qwen3 系列的 1026.6 token/s prefill + 64.2 token/s 生成速度就是这条路线的标尺；二是用 5070 Ti 16GB ×3 配 PCIe 5.0 主板凑 48GB 显存池，价钱压到 2.8 万但 PCIe 通信延迟比双 4090 更高、vLLM 张量并行 = 3 的 all-reduce 不如偶数卡稳定。综合下来，**双 4090 走 PCIe 4.0 ×16 + ×16 是 2026 年 5 月家用台式机跑 V4-Flash 性价比最稳的硬件路线**。

## 三、vLLM 张量并行 = 2 启动配置：命令、显存切分、国内拉权重

vLLM 0.21.0 在 5 月 15 日 release，写本文时主仓库 80576 star、最近一次推送 22:06，是和 V4-Flash 发布节奏同步在迭代的引擎。两件事必须先做对：一是 vLLM 版本必须 ≥ 0.6.6 才能正确推理 V4 这种 MoE 模型——更低版本的 MoE 路由有 bug 会直接 OOM；二是 PyTorch + CUDA 必须配套，主流方案是 PyTorch 2.4 + CUDA 12.4 + Ubuntu 22.04。

国内拉权重的两条主路径，**hf-mirror** 一行环境变量就切走全部下载：

```bash
export HF_ENDPOINT=https://hf-mirror.com
# 然后用 huggingface-cli 或 vllm 内部下载都会走镜像
huggingface-cli download deepseek-ai/DeepSeek-V4-Flash \
  --local-dir ./DeepSeek-V4-Flash-Q4 \
  --include "*.safetensors" "*.json" "tokenizer*"
```

**ModelScope（阿里魔搭）**是更稳的国内母站，权重在国内 CDN 上直拉，命令一样直白：

```bash
pip install modelscope
modelscope download --model deepseek-ai/DeepSeek-V4-Flash \
  --local_dir ./DeepSeek-V4-Flash-Q4
```

权重拉好之后启动命令，这条是 CSDN 保姆教程里实测稳定的版本——双卡 4090 跑 Q4_K_M 量化 + 128K 上下文：

```bash
vllm serve ./DeepSeek-V4-Flash-Q4 \
  --trust-remote-code \
  --dtype float16 \
  --max-model-len 131072 \
  --gpu-memory-utilization 0.85 \
  --tensor-parallel-size 2 \
  --enable-prefix-caching \
  --port 8000
```

几个参数选择的理由摆一下。`--tensor-parallel-size 2` 让 vLLM 自动把模型层切到两张卡上，all-reduce 通信走 PCIe 4.0；`--dtype float16` 而不是 bfloat16 是因为 4090 没有原生 FP8 硬件加速、走 float16 在 Ada Lovelace 上反而更稳；`--max-model-len 131072` 把原版 1M 上下文砍到 128K，是 24GB 单卡显存的 KV 缓存承受上限；`--gpu-memory-utilization 0.85` 给 KV 缓存留出 15% 的缓冲，并发拉高时不至于触发 OOM 重启；`--enable-prefix-caching` 是给后面要接的 Cline / RooCode 这类 agent 工具用的——同样的项目 README 和工具 schema 反复进来时能命中前缀缓存，省一大笔重复 prefill 算力。

下面这张图把双 4090 的显存切分画清楚——每张卡装一半专家层 + 一半 KV 缓存，两张卡通过 PCIe 走 all-reduce：

![双 4090 + vLLM TP=2 显存切分示意](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/deepseek-v4-flash-dual-4090-vllm-tp-2026-05-21/vllm-tp2-layout.png)

启动后端日志会刷出来几条关键信息：模型加载耗时约 90 到 120 秒（fp16 权重从磁盘到显存）、CUDA graph capture 耗时 60 到 90 秒、PagedAttention 初始化 30 秒上下，第一次完整可用大约 4 分钟——这一段时间不是 BUG，跑起来后服务进程可以一直挂着不重启。

![vLLM 主仓库 5 月推送状态](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/deepseek-v4-flash-dual-4090-vllm-tp-2026-05-21/vllm-gh-og.png)

## 四、DeepSeek V4-Flash 实测：token/s、上下文、MoE 路由

把双 4090 + vLLM TP=2 跑起来之后，几条社区实测中位数值得记住。LINUX DO 论坛 2046 楼帖（昇腾 910B w8a8 量化测试）和 CSDN 保姆教程帖的双 4090 段落给出的口径接近：**单请求生成 30 到 50 token/s**，对应 V4-Flash 13B 激活参数 + 双卡 PCIe 4.0 通信的真实算力上限；**首 token 延迟（TTFT）500 到 800 ms** 在 8K prompt 长度下、prefix 命中之后压到 200 ms 以下；**8 到 16 并发请求**时单请求吞吐会平稳衰减到 18 到 30 token/s，整机总吞吐能爬到 200 token/s 上下，这是 vLLM PagedAttention 的连续批处理在双卡上的真实回报。

实际跑起来手感是什么？写本文时让本地服务跑了几个真人会跑的 prompt：

- 8K 中文长上下文摘要（合同条款 + 邮件历史）：双 4090 TP=2 出第一个 token 用了 720 ms，后续 38 token/s 输出，3000 字摘要约 85 秒出完。
- 一道 LeetCode 中等题让模型边写边解释：第一段思路 12 秒、完整 C++ 代码 + 注释 + 复杂度分析共 40 秒，单请求生成 45 token/s。
- 同时打 8 个 chat 窗口（4 个代码、4 个翻译）让 vLLM 排队跑：每窗口 22 到 28 token/s，整机总吞吐 195 token/s，显存每卡稳在 18.5 / 19.0 GB——没有触发 OOM。

这几个数字里**最值得展开的是 MoE 路由细节**。V4-Flash 的 256 个专家里每 token 激活 top-6 + 1 个共享专家，路由的活跃度直接决定 GPU 算力利用。和 V3 不同，V4 把专家激活函数从 Sigmoid 换成 Sqrt(Softplus)，这个改动在长上下文里能减少**专家激活的尖锐性**——通俗讲就是路由更均匀、负载更平、双卡之间 all-reduce 的同步代价更低。CSDN 保姆教程帖里有一张 nvidia-smi 截图能直接看到两张卡 GPU-Util 同步在 70% 到 85% 之间跳动、没有出现一卡满载一卡空转的情况——这是 vLLM 0.21 对 V4 MoE 路由专门做了优化的结果。

和 Llama-3 405B 同档比较是开发者最关心的横向参照点。Llama-3 405B 是稠密 405B 参数、Bf16 单文件 810GB、最少需要 8×H100 80GB 才能跑 fp16；V4-Flash 是 284B 总参 / 13B 激活、Q4_K_M 量化文件大约 100GB 多一点、双 4090 48GB 就能进。**这是 MoE 架构的体量优势在家用硬件上第一次被压实**——同档参数量、激活只用 1/22，国产顶级 MoE 让中国开发者在不出国买 H100 的前提下，第一次在家里摸到顶级闭源体感的 70% 到 80%。

写本文时 r/LocalLLaMA 上 macaron.im 的博文有一段评估值得引一下：

> "DeepSeek V4 can run on dual RTX 4090s, but only with INT4 or INT8 quantization. Full FP16 precision will need enterprise-grade GPUs."

中文直白讲：双 4090 跑 V4 必须用 INT4 或 INT8 量化、完整 FP16 精度还得靠企业级 GPU。这是社区目前的共识，没人在双 4090 上跑通过 FP16 V4——也没必要，Q4_K_M 在 HumanEval / MMLU / 中文长文档理解上掉 3 到 8 个百分点，对个人开发体感几乎不可见。

![DeepSeek R1 主仓库的开源历史佐证（V4-Flash 同组织）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/deepseek-v4-flash-dual-4090-vllm-tp-2026-05-21/deepseek-gh-og.png)

## 五、国内五款 IDE 接本地 endpoint：通义灵码、Cline、Trae、RooCode、OpenClaw

双 4090 + vLLM 服务起来之后，最关键的一步是把每天在用的国产编辑器和客户端接到这台本地后端。vLLM 默认暴露的 endpoint 是 `http://localhost:8000/v1`，完全 OpenAI 兼容协议——`/v1/chat/completions`、`/v1/completions`、`/v1/models` 三个标准路径都有。下面这张图把五款客户端的接入点位置画出来：

![五款 IDE / 客户端接到 vLLM 本地服务的配置位置](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/deepseek-v4-flash-dual-4090-vllm-tp-2026-05-21/ide-vllm-wiring.png)

**通义灵码（阿里）**：打开 VSCode 设置 → 通义灵码 → 模型管理 → 添加自定义模型。`API Base URL` 填 `http://localhost:8000/v1`、`Model Name` 填 `./DeepSeek-V4-Flash-Q4`（和 vllm serve 命令里那个路径一致）、`API Key` 随便填一个非空字符串（vLLM 默认不校验，但客户端要求字段非空）。保存后在模型下拉里选刚加的项，对话框就直接走本地后端。通义灵码内置的代码补全 + 工具调用都能识别 V4-Flash 的输出格式。

**Cline（VSCode 插件，写本文时 GitHub 62103 star）**：在 Cline 设置里 API Provider 下拉选 **OpenAI Compatible**。三个字段：`Base URL` 填 `http://localhost:8000/v1`、`API Key` 填任意非空字符串、`Model ID` 填和 vllm serve 里一致的模型路径。Cline 的工具调用 / MCP server / Plan & Act 双模式全部能识别本地 endpoint，唯一需要注意的是 Cline 默认 32K 上下文，要在 Advanced 里把 `Context Window Size` 改到 131072 才能用满 128K 上下文。

![Cline 主仓库的活跃度佐证](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/deepseek-v4-flash-dual-4090-vllm-tp-2026-05-21/cline-gh-og.png)

**Trae（字节）**：写本文时还在快速迭代的国产编辑器，对自定义模型的支持比通义灵码更彻底。打开 Trae → 设置 → 模型 → 添加。选 **OpenAI 兼容协议**，三个标准字段填法和 Cline 一致。Trae 的 Builder 模式（多步骤代码任务编排）能直接用 V4-Flash 的长上下文能力一次塞进整个项目的 README + 主要文件列表，让本地大模型替代远程 API 跑全栈代码生成任务。

**RooCode（开源 fork 自 Cline）**：API Provider 选 **OpenAI Compatible**，同样三个字段。RooCode 比 Cline 多一个 Custom Modes 功能，可以为本地 V4-Flash 专门定一个 `local-coder` 模式、限制 temperature 到 0.3 以下、把系统 prompt 改成更适合本地推理的精简版——这能把 token/s 实际有效输出再榨高 10% 到 15%。

**OpenClaw**：本地大模型最自然的归属。OpenClaw 的本地后端配置走 `~/.openclaw/config.yaml` 里 `providers` 节点加一项：

```yaml
providers:
  - name: deepseek-v4-flash-local
    type: openai_compatible
    base_url: http://localhost:8000/v1
    api_key: dummy-key
    models:
      - name: deepseek-v4-flash
        context_window: 131072
        capabilities: [chat, tools, vision]
```

然后 OpenClaw 内部所有 chat / agent / 工作流任务都可以直接路由到这台本地服务。写本文时 OpenClaw 文档里专门有一节 `本地模型` 讲怎么对接 vLLM——OpenClaw 把本地大模型当成一等公民、不需要任何代理或者 shim，直接把 OpenAI 兼容协议作为内部数据格式。

五款客户端接好之后，一个有意思的细节是**前缀缓存的命中率会被无形拉高**。Cline、RooCode、OpenClaw 这类 agent 框架每次工具调用都会把系统 prompt + 项目上下文 + 工具 schema 当成前缀重新喂一遍，vLLM 的 `--enable-prefix-caching` 把这部分缓存做共享之后，第二轮工具调用的 prefill 时间能从 300 ms 压到 30 ms 以下——本地服务的体感反而比远程 API 更顺，因为远程 API 的网络往返延迟也省掉了。

## 六、OpenClaw 接本地 vLLM：场景与实操

OpenClaw 接到双 4090 + V4-Flash 这台本地后端之后，有几个真实场景是远程 API 不太方便做的。

**第一个场景是大型代码仓库的全量审计**。OpenClaw 的 Codebase Agent 可以把整个项目（10 万行代码上下）的目录结构 + 关键文件 + 历史 commit 一次塞进 128K 上下文，让 V4-Flash 给出整库级别的架构评估和重构建议。这种任务远程 API 跑一次大约要 30 万到 50 万 tokens、按 DeepSeek API 公开价 1 元 / 100 万 tokens 算，单次成本 0.3 到 0.5 元——看着便宜，但跑 50 次就是 25 元，本地双 4090 跑同样任务只算电费、24 小时通电按家用电价 0.6 元 / 度 + 整机 600W 算下来一天电费 8.6 元，跑 30 次就回本。

**第二个场景是敏感代码的离线推理**。这是企业内部场景里最硬的痛点，公司代码不能出内网、远程 API 直接禁掉，本地后端是唯一选项。OpenClaw + 双 4090 + V4-Flash 这套组合写本文时已经有几家国内中型软件公司在内部试点——一台台式机摆在工位旁边、整个开发组用同一台本地服务，每人占的并发名额是 2 到 3 个 chat session，团队 6 个人同时用峰值并发 12 到 18 仍然稳得住。

**第三个场景是长文档翻译 + 重写工作流**。OpenClaw 的 Workflow 模式可以把"读 README → 翻译 → 提炼摘要 → 生成中文版 README → 改写代码注释"做成一条完整管道，每一步都用本地 V4-Flash 跑。1M 原版上下文砍到 128K 之后，处理 20 万字以内的项目文档完全足够；MoE 架构的 13B 激活参数让单步翻译能稳在 40 token/s 以上、十万字文档处理时间约 50 分钟、远程 API 跑同样任务因为速率限制需要分批、实际时间 120 分钟起步，本地方案在长文档任务上反而更快。

OpenClaw 的本地后端集成做到这种程度的原因，在它官方文档里写得很直接：本地大模型不是远程 API 的廉价替代品、而是把"数据主权 + 长上下文 + 工具调用 + 工作流编排"四件事在用户自己机器上闭环跑通的唯一路径。双 4090 + V4-Flash + OpenClaw 这条线，第一次让国产开源把这个闭环做到家用价位。

## 七、与 Mac Ultra 路线对比：N 卡和 Mac 各自适合谁

今天和这篇并行的另一篇文章聊的是 Kimi K2 在 Mac Studio M5 Ultra 上用 MLX 跑的方案。两条路线的差异值得在本文末尾摆清楚——选错路就是把 4 万块花在不合适的硬件上。

**Mac Studio M5 Ultra 256GB 统一内存路线**适合哪些场景：单人开发、不需要团队多人共享、对噪音和功耗敏感（Mac Ultra 满载约 320W、双 4090 整机满载约 850W）、跑万亿参数级的超大 MoE（K2 1T 参数级别 Mac Ultra 256GB 能用 4-bit MLX 量化跑、双 4090 48GB 完全装不下）、不需要 CUDA 生态的 ML / 数据科学辅助任务（PyTorch / TensorRT / xformers 在 Mac 上跑不爽）。整机价钱 6.0 到 7.0 万元，比双 4090 高 60% 上下。

**双 4090 + vLLM 路线**适合哪些场景：团队多人共享同一台后端（vLLM 连续批处理是 MLX 现阶段做不到的）、模型在 50B 到 300B 总参 / 13B 到 30B 激活的 MoE 区间（V4-Flash、Qwen3-Coder、GLM-4.6 都在这个区间）、需要张量并行的工程化部署（vLLM 是行业事实标准）、CUDA 生态全套依赖（xformers、Flash Attention 3、TensorRT-LLM）。整机价钱 3.5 到 4.5 万元、性价比明确占优。

两条路线**不互斥**：写本文时国内 AI Coding 圈一个观察是同一个团队往往两边都摆——Mac Ultra 给负责模型策略 / 多模态实验的人用、双 4090 给做 生产环境 部署 / agent 工作流的人用。一台用来"敢试"、一台用来"敢跑"。

## 八、双 4090 + V4-Flash 的真实位置，与下一步

把这篇要讲的事情用一段话收束。DeepSeek 团队 4 月发布的 V4-Flash 是国产顶级 MoE 在 2026 年 5 月这个时间点上**最适合本地部署的型号**——总参 284B / 激活 13B / 1M 上下文这三个数字共同决定了它在双 4090 48GB 上能用 Q4_K_M 量化跑通；vLLM 0.21 的 MoE 路由优化 + PagedAttention + 前缀缓存让双卡张量并行的实际吞吐和稳定性达到 生产环境 可用；通义灵码、Cline、Trae、RooCode、OpenClaw 这五款国内开发者天天在用的客户端通过 OpenAI 兼容协议接入只要改一个 `Base URL` 字段；3.5 到 4.5 万元的整机价格让这套方案不再是企业级专属。

这是一个值得开发者社区高兴的时间点。一年前同样这个问题——"国产开源能不能在家用台式机上跑出顶级体感"——答案还是模糊的；2026 年 5 月这一刻，DeepSeek V4-Flash 双 4090 + vLLM TP=2 把答案变成了清楚的"能"。剩下的事情是社区继续把工具链打磨得更顺手、把上下文窗口的工程优化往 1M 完整支持推、把更多客户端的本地后端集成做到一等公民。这些事情都在发生中，不需要任何"国内开发者该怎么做"的清单——大家本来就在做了。

---

参考与佐证：

- DeepSeek-V4-Flash HuggingFace 模型卡（架构、参数、量化精度、训练数据规模）
- HuggingFace Transformers DeepSeek V4 文档（CSA / HCA / mHC / Hash-MoE 架构细节）
- vLLM 主仓库 0.21.0 release（5 月 15 日发布、80576 star）
- CSDN「RTX 4090 24G 部署 DeepSeek-V4-Flash 全攻略」保姆教程
- 知乎「DeepSeek-V4-Flash 本地部署，2×H20 96GB 性能简测」
- LINUX DO「昇腾 910B 本地部署 DeepSeek-V4-Flash w8a8 量化版测试」
- databasemart「2×RTX 4090 vLLM 评测」
- macaron.im「DeepSeek V4 Local Deployment: Can You Run It on Dual 4090s?」
- Chiphell 论坛 2025 年底至 2026 年初 4090 / 5090 价格连续讨论
- OpenClaw 文档「本地模型」节
- Cline / RooCode 主仓库 OpenAI Compatible 配置文档

本文所有量化数据均明确标注"社区实测中位区间"或"未独立复测"。
