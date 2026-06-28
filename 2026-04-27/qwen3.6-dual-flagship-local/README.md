---
title: "Qwen3.6 双旗舰本地部署 · 27B Dense + 35B-A3B MoE 全攻略"
date: 2026-04-27
tags: [Qwen3.6, 35B-A3B, MoE, 本地 LLM, MLX, Ollama, llama.cpp, vLLM, RTX 5090, Apple Silicon]
cover: 2026-04-27-qwen3.6-dual-flagship-local.png
description: "Qwen3.6 一周内连发两个旗舰：35B-A3B MoE（4-15）+ 27B Dense（4-21）。本地部署完整指南——4 条主流路径（MLX / Ollama / llama.cpp / vLLM）+ 18 条真人 r/LocalLLaMA / HN / 博客实测体验 + 失败案例 + 不同设备选型。社区已用脚投票：35B-A3B 月下载 135 万 vs 27B 40 万。"
---
# Qwen3.6 双旗舰本地部署 · 27B Dense + 35B-A3B MoE 全攻略

![Qwen3.6 双旗舰本地部署：27B Dense vs 35B-A3B MoE](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-27/qwen3.6-dual-flagship-local/2026-04-27-qwen3.6-dual-flagship-local.png)

> **2026-04-15 阿里开源 Qwen3.6-35B-A3B（MoE，3B 激活）；6 天后 4-21 又开源 Qwen3.6-27B（Dense，全激活 + 多模态）**。同期 Apache-2.0、262K 原生上下文、vision encoder 都齐。社区用脚投票——HF 月下载 35B-A3B **135 万** vs 27B Dense **40 万**，3.4 倍差距。
>
> 本文不堆 benchmark 数字，全是 r/LocalLLaMA / HN / 个人博客 / B 站 / CSDN 上 18 条真人实测——**3090 单卡跑 35B-A3B 120 tok/s 是真的，DGX Spark 上跑 FP8 四连暴翻车也是真的**。下面把"双旗舰怎么选 + 怎么装 + 装完什么体感"讲全。

---

## 双旗舰怎么选：10 秒看懂

![Qwen3.6 双旗舰参数对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-27/qwen3.6-dual-flagship-local/qwen3.6-dual-flagship-spec.png)

两个模型同期发布、协议相同、原生上下文一样、都支持视觉，差别在**激活参数和定位**：

- **27B Dense**：27.78B 全激活。质量更稳，深度任务（多步推理、复杂 codebase）优势明显
- **35B-A3B MoE**：35.95B 总参 / 3B 激活。**3B 激活意味着推理成本只跟 3B dense 一档**，但参数知识量是 35B——速度翻好几倍，消费级硬件友好

社区下载量已经给了答案：**35B-A3B 月下载 135 万 vs 27B 40 万**。背后逻辑很简单——M4 32GB / RTX 4090 / RTX 3090 这些"主流硬件"能流畅跑 35B-A3B，但跑 27B Dense 就紧。MoE 是真的"消费级旗舰"，27B Dense 是"质量党选择"。

下面所有部署教程和真人体验都按这个分工讲——**默认推荐 35B-A3B，装得起 27B Dense 再聊"质量首选"**。

---

## 装哪个：8 档硬件实测对照

![硬件分档](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-27/qwen3.6-dual-flagship-local/qwen3.6-dual-device-tiers.png)

*量化档位依据 Unsloth 官方 GGUF 文件大小 + 真人实测反馈整理*

几个反直觉的事实，全部来自社区真人帖子（详见 §四真人体验）：

1. **MacBook M4 32GB 跑 27B Dense 5 tok/s（不可用），跑 35B-A3B 25 tok/s（流畅）**——同设备同 Q4_K_M，5 倍速度差。reddit 用户 benob 在 HN 47863217 直接报数字
2. **RTX 3090 老卡才是 35B-A3B 性价比甜点**——HF 用户 Kukedlc 单卡 3090 跑 Q3_K_M 量化拿到 120 tok/s
3. **AMD RX 7900 XTX 也能塞 81k context**——HN 用户 skirmish llama.cpp + Q5_K_M + 8-bit KV cache 实测
4. **DGX Spark Blackwell + FP8 早期是工程地狱**——CSDN 上"java人才G"四连暴：nvidia runtime → 废弃参数 → tool parser 改名 → Tensor Core 罢工

具体哪档硬件跑哪个量化，下面部署教程会逐条对应。

---

## 部署教程：4 条主流路径

### 路径 A · Mac 用户：MLX（最快、最省心）

Apple Silicon 跑这两个模型**必上 MLX**——比 GGUF + llama.cpp 快 30-50%，比 Ollama 快 2-3 倍。社区共识无争议。

**装 35B-A3B（消费级 Mac 首选）**：

```bash
# 1. 装 mlx-lm
pip install mlx-lm

# 2. 起 OpenAI 兼容 server（端口 8080）
mlx_lm.server \
  --model mlx-community/Qwen3.6-35B-A3B-4bit \
  --port 8080 \
  --host 127.0.0.1
```

第一次拉模型要 5-15 分钟（取决于带宽），文件 ~20 GB。装完 Cursor / Continue.dev / aider 配 `http://127.0.0.1:8080/v1` 当 OpenAI endpoint 就能用。

**装 27B Dense（质量优先）**：

```bash
mlx_lm.server \
  --model mlx-community/Qwen3.6-27B-4bit \
  --port 8080
```

**Mac 内存档位建议**（依据真人反馈）：

- **16 GB**：跑不动，别折腾
- **32 GB**：35B-A3B Q4_K_M 流畅、Dense 5 tok/s 不可用 → **只装 35B-A3B**
- **64 GB**（M3 Pro / M4 Pro 顶配）：两个都流畅
- **128 GB**（M4 Max / M5 Max）：两个都装，长 ctx 跑得开
- **256-512 GB**（M3 Ultra / Mac Studio）：FP8 跑得动，质量首选

**Simon Willison 在 M5 Pro 128GB 上的实测**（`simonwillison.net/2026/Apr/16/qwen-beats-opus/`）：

> "The pelican is excellent for a 16.8GB quantized local model... If the thing you need is an SVG illustration of a pelican riding a bicycle, right now Qwen3.6-35B-A3B running on a laptop is a better bet than Opus 4.7!"

### 路径 B · NVIDIA 消费级：Ollama（开箱即用）

如果你只想"装一行就跑"，Ollama 是首选。

**装 35B-A3B**：

```bash
ollama pull qwen3.6:35b-a3b
ollama run qwen3.6:35b-a3b
```

**装 27B Dense**：

```bash
ollama pull qwen3.6:27b
ollama run qwen3.6:27b
```

Ollama 官方仓库 4 月已挂 `qwen3.6:27b`、`qwen3.6:27b-coding-mxfp8`、`qwen3.6:27b-coding-nvfp4`、`qwen3.6:35b-a3b` 等多个量化标签。视觉版的 mmproj 文件已打包好，开箱支持图像输入。

**配 Open WebUI（浏览器界面）**：

```bash
docker run -d -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  ghcr.io/open-webui/open-webui:main
```

打开 `http://localhost:3000`，模型选 `qwen3.6:35b-a3b` 或 `qwen3.6:27b`，几乎和 ChatGPT 网页版一个体感。

**Ollama 的一个坑**：Unsloth 文档里说 *"Currently no Qwen3.6 GGUF works in Ollama due to separate mmproj vision files"*——这是指**自己拉的 unsloth GGUF** 在 ollama run 起不来，**不是 Ollama 整个挂了**。Ollama 官方仓库的 `qwen3.6:27b` / `qwen3.6:35b-a3b` 标签（已打包 mmproj）正常可用。装不上先 `ollama --version` 升到 0.6.0+。

### 路径 C · NVIDIA / AMD：llama.cpp（性能榨干派）

要把 5090 / 4090 / 3090 / 7900 XTX 的速度压榨到极限，走 llama.cpp。

**拉 Unsloth GGUF**：

```bash
# 35B-A3B Q4_K_M 推荐（或 Q5_K_M 质量更高）
huggingface-cli download \
  unsloth/Qwen3.6-35B-A3B-GGUF \
  Qwen3.6-35B-A3B-Q4_K_M.gguf \
  --local-dir ./models

# 27B Dense Q4_K_M
huggingface-cli download \
  bartowski/Qwen_Qwen3.6-27B-GGUF \
  Qwen_Qwen3.6-27B-Q4_K_M.gguf \
  --local-dir ./models
```

**起 llama-server**：

```bash
./llama-server \
  -m models/Qwen3.6-35B-A3B-Q4_K_M.gguf \
  --port 8080 \
  --ctx-size 65536 \
  --n-gpu-layers 99 \
  --flash-attn \
  --cache-type-k q8_0 \
  --cache-type-v q8_0
```

`--cache-type-k` / `--cache-type-v` 量化 KV cache 是关键技巧。HN 上 gfosco 在 5090 32GB 上的实测原话：

> "I set this up today on my 5090 at Q6_K quantization and Q4_0 KV, got 50 tokens/s consistently at 123k context, using ~28/32gb vram through LM Studio."

5090 + 27B Dense Q6_K + Q4_0 KV cache + 12.3 万 tokens 上下文 = 50 tok/s 稳定——**当下消费级单卡跑 Qwen3.6 的最甜组合**。

**AMD 用户照样能跑**——HN 用户 skirmish 在 RX 7900 XTX 24GB 上的实测：

> "I squeezed it into 24 GiB VRAM (since I have RX7900XTX): Q5_K_M Unsloth quantization on Linux llama.cpp, context 81k, flash attention on, 8-bit K/V caches."

### 路径 D · 服务器：vLLM + FP8（多并发 / 团队部署）

如果是企业部署、要给 50 个同事共用一个本地 LLM 服务，vLLM + FP8 量化是路。

```bash
# 拉官方 FP8 镜像（避免 nightly！）
docker pull vllm/vllm-openai:v0.7.2

# 起 35B-A3B FP8（A100 80GB 单卡）
docker run --runtime nvidia --gpus all \
  -v ~/.cache/huggingface:/root/.cache/huggingface \
  -p 8000:8000 \
  --ipc=host \
  vllm/vllm-openai:v0.7.2 \
  --model Qwen/Qwen3.6-35B-A3B-FP8 \
  --tool-call-parser qwen3_xml \
  --enable-auto-tool-choice
```

**关键避坑**（CSDN "java人才G" 在 DGX Spark Blackwell + FP8 上踩过 4 次）：

> "硬件架构太新了……标准的 vLLM 官方镜像无法正确处理这种特定的 A3B 格式，硬塞进去直接导致底层 Tensor Core 报错罢工。"

具体 4 个坑：

1. **nvidia runtime 缺失**——容器报"unknown runtime"，先 `apt install nvidia-container-toolkit`
2. **`--disable-log-requests` 已废弃**——新版 vLLM 不接受，从启动命令删掉
3. **tool parser 改名**——从 `qwen` 改成 `qwen3_xml`，老版本配置直接挂
4. **Blackwell + FP8 Tensor Core 罢工**——必须升级到 `cu130-nightly-aarch64` 镜像

DGX Spark / 5090 / Blackwell 这些 2025 末新硬件 + 新模型组合，准备好踩坑 1-2 周再上生产。

CSDN 上 fzuim 用双 4090 + nightly 镜像也翻车："两块 4090 折腾一天的真实记录"。教训一致——**vLLM 不要用 nightly**，等稳定 release。

---

## 真人体验：18 条来自一线的实测

下面按设备分组讲。每条都附 source URL（实测过 200）+ 真人原话——这是文章的真正含金量，不是 benchmark 表格。

先一张图把 6 条最有代表性的成功体验摆出来：

![Qwen3.6 双旗舰真人成功体验 6 条](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-27/qwen3.6-dual-flagship-local/qwen3.6-dual-success-quotes.png)

下面按设备深入讲。

### Apple 阵营（统一内存党）

#### Simon Willison · M5 Pro 128GB · 35B-A3B Q4

> "The pelican is excellent for a 16.8GB quantized local model. I ran it on an M5 Pro with 128GB of RAM, but it only needs ~20GB of that. If the thing you need is an SVG illustration of a pelican riding a bicycle, right now Qwen3.6-35B-A3B running on a laptop is a better bet than Opus 4.7!"

来源：`simonwillison.net/2026/Apr/16/qwen-beats-opus/`

体感：**强成功**——本地 21GB 量化模型在特定任务（SVG 创意输出）上击败 Anthropic 闭源旗舰 Opus 4.7。Simon 不轻易给好评。

Simon 让两个旗舰都画了"骑自行车的鹈鹕"——这是他每发新模型必做的 SVG 绘图测试。两张实测原图：

![Qwen3.6-27B Q4_K_M 输出的 pelican on bicycle SVG](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-27/qwen3.6-dual-flagship-local/qwen3.6-27b-simon-pelican.png)

*图片来源：Simon Willison 个人博客 `simonwillison.net/2026/Apr/22/qwen36-27b/`，27B Dense Q4_K_M GGUF 实测，CC-BY 授权*

![Qwen3.6-35B-A3B 输出的 opossum on e-scooter（霓虹赛博朋克）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-27/qwen3.6-dual-flagship-local/qwen3.6-35b-a3b-simon-opossum.jpg)

*图片来源：同博客，35B-A3B 实测，CC-BY 授权*

#### benob · MacBook M4 32GB · 双旗舰对比（关键反差）

> "I get ~5 tokens/s on an M4 with 32G of RAM, using llama-server. The 35B-A3B model is at ~25 t/s." 又："35B-A3B model goes off rails after 15k-20k tokens."

来源：HN 47863217 评论区

体感：**Mac 用户必看**——同设备 + 同 Q4_K_M，27B Dense 5 tok/s（不可用）vs 35B-A3B 25 tok/s（流畅）。**5 倍速度差**完全颠覆"参数大就慢"的直觉。但 35B-A3B 在 15-20k token 后开始退化（"goes off rails"），long context 别全信标称 262K。

> ⚠️ **Mac < 64GB → 直接劝退 27B Dense，只装 35B-A3B**

#### datadrivenangel · M5 MBP · 跨模型对比

> "Gemma4 feels the most claude-like."

来源：HN 47863217 评论

体感：**chat 体验未必 Qwen 第一**——日常闲聊式对话，Gemma 4 给人的"Claude 既视感"更强。Qwen3.6 真正强的是 agentic + coding。

### NVIDIA 消费级（3090 / 4090 / 5090）

#### gfosco · RTX 5090 32GB · 27B Dense Q6_K + Q4_0 KV

> "I set this up today on my 5090 at Q6_K quantization and Q4_0 KV, got 50 tokens/s consistently at 123k context, using ~28/32gb vram through LM Studio."

来源：HN 47863217

体感：**强成功 + 当下最甜组合**——5090 + 27B Dense Q6_K + KV cache 量化到 Q4_0，跑 12.3 万 tokens 上下文还能稳定 50 tok/s。这是消费级单卡跑 Qwen3.6-27B 的极限配置。

#### finnjohnsen2 · RTX 5090 + Ollama + OpenCode ACP

> "I keep an Opus close of course, because these local models still wander off in the wrong direction and fails. Something Opus almost never does for me anymore."

来源：HN 47863217

体感：**部分成功 + 真实坦诚**——5090 + Qwen3.6 日常够用，但 agentic 场景里仍会"跑偏"。建议保留 Opus 作为兜底。这是当下重度开发者的真实工作流：本地 + 线上 双备。

#### Kukedlc · 单卡 RTX 3090 24GB · 35B-A3B Q3_K_M

> "the first one that saturated my benchmark... It's the first time with an open source model where I feel like the bottleneck is me writing better prompts and not the model failing to understand what I'm asking."

来源：HF Qwen3.6-35B-A3B Discussion #37

体感：**爆款金句**——3090 旧卡（2020 年发布）+ 35B-A3B Q3 量化达 120 tok/s，主观体验"瓶颈第一次不在模型而在我自己写 prompt"。这条话直接定义了开源本地大模型 2026 年的拐点。

#### ghostwithahat · RTX 3090 · 35B-A3B IQ4_NL · agentic 场景

> "For normal 'chatting', I like the big dense gemma 4 better. But Qwen3.6 seems to work better for agentic use."

来源：HF Qwen3.6-35B-A3B Discussion #37

体感：**双备分工**——chat 用 Gemma 4，agent / 多步任务用 Qwen3.6。同时报告了 hermes-agent 死锁问题，需要手动调 `memory.nudge_interval=0` 才不卡。

#### overgard · RTX 5080 16GB · 27B Dense Q4_0

> "For an RTX 5080 16GB of RAM (desktop), I get about 6 tokens/sec after some tweaking (f16->q4_0). Kind of on the borderline of usable."

来源：HN 47863217

体感：**翻车边缘**——16GB 显卡跑 27B Dense 即使 Q4_0 也只有 6 tok/s。**显存 < 24GB 不要折腾 27B Dense**，直接上 35B-A3B。

#### skirmish · AMD RX 7900 XTX 24GB · 27B Dense

> "I squeezed it into 24 GiB VRAM (since I have RX7900XTX): Q5_K_M Unsloth quantization on Linux llama.cpp, context 81k, flash attention on, 8-bit K/V caches."

实测：prefill 625 t/s，generation 30 t/s。

来源：HN 47863217

体感：**成功**——AMD 卡也能塞 81k context 跑 27B Dense。证明 Qwen3.6 不是 NVIDIA 专属。

#### 苏米 · RTX 4090 + Win + LM Studio · 27B Dense Q4_K_M

> "Qwen3.6-27B 是目前测试过的本地模型中，综合能力最接近 GPT-4 的一个。这种解释质量，在本地模型里算比较顶尖的。"

来源：`xmsumi.com/detail/3046`

体感：**强成功**——4090 + 27B Dense + Windows + LM Studio 是最简单的"开箱即用"路径。中文体验直追 GPT-4。

#### Wasif Basharat · 单卡 RTX 3090 230W 限功耗 · 27B Dense + 视觉 + 125k ctx

> "85 TPS sustained — on a card with 25% less VRAM and ~40% less raw compute — at 230W (quiet and cool)."

来源：Medium 长文

体感：**成功但血泪**——3090 限功耗 230W（比原版省 1/3）跑 27B Dense + 视觉 + 12.5 万 tokens 上下文，85 tok/s 稳定。但作者完整列了 4 个失败堆栈：MTP n=4 接受率塌到 8%、SGLang+EAGLE 张量打包不兼容、Qwen 官方 FP8 25-31GB 单卡塞不下、groxaxo GPTQ 变体 0% MTP 接受率。**堆栈选择决定生死**。

### 服务器 / 工作站

#### serapis + cosinus · DGX Spark · FP8 + flashinfer

数据：prompt 8496 t/s / generation 76-78 t/s；ToolCall-15 拿到 97% 成功率。

来源：NVIDIA Developer Forums

体感：**专业级成功**——DGX Spark + FP8 + flashinfer 是当下"小型工作站"跑 Qwen3.6 的最快配置。tool calling 准确率 97%。

#### java人才G · DGX Spark Blackwell + FP8 A3B（翻车 → 救活）

> "硬件架构太新了……标准的 vLLM 官方镜像无法正确处理这种特定的 A3B 格式，硬塞进去直接导致底层 Tensor Core 报错罢工。"

来源：CSDN

失败链：nvidia runtime 缺失 → `--disable-log-requests` 已废弃 → tool parser 从 `qwen` 改名 `qwen3_xml` → Blackwell + FP8 Tensor Core 罢工。最终用 `cu130-nightly-aarch64` 镜像才跑通。

体感：**翻车四连暴**——这条故事写得最好的实战警告：**新硬件 + 新模型 = 1-2 周工程地狱**。早期 adopter 准备好踩坑。

#### fzuim · 双 RTX 4090 48GB + Docker 多实例（翻车）

体感：**翻车**——选 vllm-openai:nightly 镜像直接挂掉。"两块 4090 折腾一天的真实记录" 是文章标题原话。

来源：CSDN

#### SlavikCA · Intel Xeon W5 + 256GB DDR5 + 72GB VRAM · UD-IQ4_NL

> "Getting 13 t/s. Using it with thinking disabled."

来源：HN 47792764

体感：**反直觉**——重型工作站只跑 13 tok/s，反而不如 3090 单卡的 120 tok/s。**MoE 性能上限受 router 而非显存约束**——堆显存堆 RAM 没用，吞吐还是被路由策略卡住。

### 其他

#### anonym29 · Beelink GTR 9 Pro（Strix Halo APU）

> "You absolutely do NOT need a $3000 Strix Halo rig... to run this model at extremely high accuracy."

来源：HN 47863217

体感：**便宜 APU 也能上车**——AMD Strix Halo 这种 unified memory mini PC 也能跑 Qwen3.6，不需要 3000 美元的高配工作站。

#### B 站 碳基生物退役指南 · 12GB 显卡 + MoE offload

> "llama.cpp 本地部署 Qwen3.6-35B-A3B，12G 显存 20 token 每秒。"

来源：B 站视频

体感：**惊喜**——12GB 显卡（GTX 1660 Ti / RTX 3060 Q4 等老中端卡）+ llama.cpp 智能 offload = 20 tok/s。**入门门槛低到 2500 元二手机**。

---

## 三类典型坑（必须提前知道）

先把 5 条翻车实录摆出来——**占总反馈约 30%，比成功故事更有警示价值**：

![Qwen3.6 双旗舰真人翻车实录 5 条](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-27/qwen3.6-dual-flagship-local/qwen3.6-dual-failure-quotes.png)

下面逐条展开。

### 坑 1：Q4_K_M 重复循环 bug（已修复，但要重拉）

bwv848 在 HN 35B-A3B 帖（47792764）抱怨：

> "I've been trying the Q4_K_M version, and sometimes it gets stuck in a loop."

mobiuscog 跟帖确认：

> "I'm having the same issues, the more I use it... the repetition penalty doesn't seem to help."

后续 Unsloth 创始人 danielhanchen 在同贴**承认 22% 误差并已重新上传** Q4_K_M 量化。

**教训**：4 月 20 日之前下载的 Unsloth Q4_K_M 量化版本要重拉新版。怎么验证？看文件 SHA256 是否对得上 HF 上当前的版本——`huggingface-cli download` 会自动校验。

### 坑 2：长上下文 prefill 跳水

benob 在 M4 32GB 上的另一句话：

> "35B-A3B model goes off rails after 15k-20k tokens."

35B-A3B 在 15-20k tokens 后开始退化——**标称 262K，实际能稳定使用的窗口大约只有 16K**。

27B Dense 反而更稳定，gfosco 的 5090 实测能稳跑 12.3 万 tokens。

**教训**：超长上下文工作流（整本 PDF 阅读、几十万行代码索引）选 27B Dense；agentic 多步对话（每轮上下文 < 16K）选 35B-A3B。

### 坑 3：新硬件 + 新模型 = 工程地狱

DGX Spark Blackwell（2025 末）+ Qwen3.6（2026-04）这种组合是**早期 adopter 雷区**。java人才G 的"四连暴"已经讲过——nvidia runtime → 废弃参数 → tool parser 改名 → Tensor Core 罢工。fzuim 双 4090 + nightly 镜像也翻车。

**教训**：

- vLLM 用稳定 release（v0.7.x），不要 nightly
- Blackwell（5090 / DGX Spark）跑 FP8 等社区镜像更新一两周
- 早期 adopter 心态：踩坑 1-2 周是正常的，不要赶生产时间表

---

## 不同用途选哪个：5 个场景的真实分工

按真人反馈而不是模型卡数据，下面 5 个场景各自的最佳组合：

### 场景 1：IDE 副驾（Continue.dev / aider）→ 35B-A3B MoE

速度优势压倒一切。每秒 25-50 tok 才能在编辑器里"边查边问"不打断思路。27B Dense 在普通设备上太慢，只适合"丢长任务回头看"。

finnjohnsen2 / 苏米 的真实工作流都是 Ollama + 35B-A3B + Continue.dev / OpenCode。

### 场景 2：agent 多步任务 → 27B Dense（如果硬件够）

ghostwithahat 在 3090 上的反馈：

> "Qwen3.6 seems to work better for agentic use."

但同时 finnjohnsen2 在 5090 上提醒：

> "These local models still wander off in the wrong direction and fails. Something Opus almost never does for me anymore."

**结论**：本地 27B Dense agent 比 35B-A3B 更准，但仍比 Opus 4.6 / 4.7 弱。重度 agentic 工作流保留 Opus 兜底。

### 场景 3：长文档 / RAG → 看显存

- **5090 32GB / M3 Ultra / A100** + 27B Dense Q6 + KV 量化 → gfosco 实测 50 tok/s @ 123k ctx
- **普通 4090 / 3090** + 35B-A3B Q4 → benob 的警告：避开超过 16k context

### 场景 4：视觉 / OCR / PDF 阅读 → 都支持

两个模型都自带 Vision Encoder，原生支持图像输入。Wasif Basharat 用 3090 跑 27B Dense + 12.5 万 tokens + 视觉理解，证明消费级硬件能上。

27B Dense 在视觉任务上略强（MMMU 模型卡数据），但日常用途差距不大。

### 场景 5：中文 chat / 闲聊 → 用户反馈说 Gemma 4 更顺

ghostwithahat 和 datadrivenangel 都报告 Gemma 4 的"Claude 既视感"在闲聊场景里更顺。Qwen3.6 真正擅长的不是 chat，是**agentic + coding + 视觉**。

**结论**：纯 chat 用户用 Gemma 4 体验更稳；要 coding / agent / 视觉这类复杂任务再上 Qwen3.6。

---

## 跟其他本地 LLM 的真实对比（用户故事）

不堆 benchmark 表格，看用户实际怎么说。

### vs Llama 3.1 70B

一年前的开源旗舰（2024 中），70B 参数 / Q4 ≈ 40 GB / SWE-Verified ~50 分。**社区共识：Qwen3.6-27B Dense 在 1/2.5 参数量下已经追平甚至超过**。HN / r/LocalLLaMA 上几乎没有人再为 Llama 3.1 70B 牺牲速度和量化空间。

### vs Gemma 4

- **chat 偏文学 / 闲聊**：用户偏好 Gemma 4（datadrivenangel、ghostwithahat 都这么说）
- **agent / 代码 / 视觉**：用户偏好 Qwen3.6（同上）
- **结论**：双备最稳，分场景用

### vs DeepSeek V3.2 / V3-Plus

DeepSeek V3 系列开源版本是 **671B-A37B MoE**，Q4 量化 ~320 GB——**家用部署不实际**，需要专业服务器（M3 Ultra 512GB 或 8xA100 集群）。社区反馈一致：DeepSeek 走 API 路线（V4-Pro ¥0.75/M 促销价），本地不打 Qwen3.6 的牌。

### vs 闭源 API（Claude / Kimi / DeepSeek API）

不直接比 benchmark，看用户的"切换成本"反馈：

- **finnjohnsen2 / 苏米**：装本地后没退订 Opus / Claude，**双备使用**
- **r/LocalLLaMA 共识**：本地兜底 + 线上按需是 2026 Q2 的主流配置
- **Kukedlc**："the bottleneck is me, not the model"——开源第一次有了这种用户原话

**简单说**：开源本地不会"完全替代"线上闭源，但**在 70% 日常场景里够用**——这是从用户实际使用模式（不是 benchmark）能看到的真实信号。

---

## 一句话推荐：你应该装哪个

| 你是 | 装哪个 + 怎么装 |
|---|---|
| **MacBook 32 GB** | 35B-A3B Q4_K_M / MLX 路线（`mlx_lm.server --model mlx-community/Qwen3.6-35B-A3B-4bit`） |
| **MacBook 64-128 GB** | 两个都装，MLX 跑 35B-A3B 日常 + 27B Dense 当深度任务 backend |
| **M3 Ultra 256-512 GB** | 27B Dense FP8 / MLX，质量优先 |
| **RTX 3090 24 GB** | 35B-A3B Q3-Q4 + KV 量化，**性价比甜点**（120 tok/s） |
| **RTX 4090 / 5090 / 7900 XTX** | 两个都装，5090 + 27B Q6 + Q4_0 KV 是当下最甜 |
| **5080 / 16GB 显卡** | 只装 35B-A3B；27B Dense 不要折腾 |
| **A100 / H100 / DGX Spark** | FP8 + vLLM（避免 nightly + Blackwell 早期版本） |
| **12GB 老中端卡 / Strix Halo APU** | 35B-A3B + llama.cpp + offload，2500 元也能上车 |

**纯 chat / 闲聊** → 用 Gemma 4 体验更顺。
**agent / coding / 视觉 / RAG / 长文档** → Qwen3.6 双旗舰其中一个。

---

## 收官：Kukedlc 的那句话

> "It's the first time with an open source model where I feel like the bottleneck is me writing better prompts and not the model failing to understand what I'm asking."

这是 HF Discussion #37 里一个普通用户在单卡 3090 上跑 Q3 量化后写的话。**没有 Anthropic、没有 OpenAI、没有阿里 PR 部门改过——这是开源本地大模型 2026 年第一次拿到这种用户原话**。

留 4 月 15 日（35B-A3B 发布）和 4 月 21 日（27B Dense 发布）这两个时间戳，半年后回头看本地 LLM 圈的拐点。

---

**本文数据来源**：18 条真人实测帖，全部 URL 2026-04-27 实测可访问：

- HN 47863217（27B Dense, 988 pts / 444 comments，2026-04-21 上线）
- HN 47792764（35B-A3B, 1274 pts / 532 comments，2026-04-15 上线）
- HF Discussion `huggingface.co/Qwen/Qwen3.6-35B-A3B/discussions/37`
- `simonwillison.net/2026/Apr/16/qwen-beats-opus/`
- Medium 长文 `medium.com/@fzbcwvv/an-overnight-stack-for-qwen3-6-27b-85-tps-125k-context-vision-on-one-rtx-3090-...`
- CSDN `blog.csdn.net/fzuim/article/details/160462593`、`blog.csdn.net/weixin_44407716/article/details/160495951`
- `xmsumi.com/detail/3046`、`forums.developer.nvidia.com/t/.../366822`、`bilibili.com/video/BV1cBd2BuEaj/`

模型卡：HF `Qwen/Qwen3.6-27B`（下载 39.9 万/月）、`Qwen/Qwen3.6-35B-A3B`（下载 135 万/月）。Unsloth GGUF 量化档位 + 已修复的 Q4_K_M 22% 误差由 danielhanchen 在 HF Discussion 公开承认。所有英文引语字对字保留原文。
