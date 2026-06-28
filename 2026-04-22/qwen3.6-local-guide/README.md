# Qwen3.6 本地部署指南 · Mac + Windows 实战版

> **适用读者**：用 MacBook 或普通 Windows PC，想在本地跑起来 Qwen3.6 开源版的人。不涉及服务器、H100、多卡推理。
> 数据截至 2026-04-22。tok/s 数字为社区实测 + 公开 benchmark 汇总，因系统配置、上下文长度、散热策略差异可能 ±20%。

![Qwen3.6 本地部署指南 · Mac + Windows 实战版](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/qwen3.6-local-guide/qwen3.6-local-cover.png)

## 一、Qwen3.6 是什么（30 秒版）

你要跑的是 **Qwen3.6-35B-A3B**，阿里通义千问 2026-04-16 开源的权重。关键三件事：

- **35B 总参数，3B 激活**（MoE 架构，256 专家每 token 激活 9 个）
- **原生 256K 上下文**
- **Apache 2.0 协议，商用也行**

和你切身相关的只有一个概念：**「35B 要全部装进内存，但每个 token 只跑 3B 的计算」**。这意味着——

> **内存决定你能不能跑；算力决定你跑多快。**

Mac 的统一内存天然适合这种模型；Windows 因为要拆 CPU/GPU 内存，反而吃亏。

其他闭源版本（Qwen3.6-Plus / Qwen3.6-Max-Preview）只能走 API，本地跑不了，本文不讨论。

![Qwen3.6-35B-A3B 评测表现](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/qwen3.6-local-guide/Qwen3.6-35B-A3B-benchmark.png)

*图：Qwen3.6-35B-A3B 综合评测得分（官方）*

---

## 二、你的机器能跑什么（一张表看懂）

| 你的机器 | 能跑吗 | 推荐量化 | 预期速度 | 体验 |
|---|---|---|---|---|
| M1 / M2 · 16GB | ❌ 不建议 | — | — | 塞不下，swap 爆炸 |
| M1 Pro / M2 Pro · 16GB | ⚠️ 勉强 | UD-IQ2_M (11.5GB) | ~6 tok/s | 慢、发热、只能短对话 |
| M1 Pro / M2 Pro · 32GB | ✅ | UD-Q3_K_M (16.6GB) | ~12 tok/s | 可用，上下文开小点 |
| M1 Max / M2 Max · 32GB | ✅ | UD-Q4_K_M (22GB) | ~18 tok/s | 舒服 |
| M1 Max / M2 Max · 64GB | ✅ | UD-Q5_K_M (26.5GB) | ~35 tok/s | 流畅主力机 |
| M2 Ultra · 128GB | ✅ | UD-Q6_K (29.3GB) | ~45 tok/s | 桌面级 |
| M3 Pro · 18/36GB | ✅ | Q3 / Q4_K_M | 15 / 25 tok/s | OK |
| M3 Max · 48GB | ✅ | UD-Q4_K_M (22GB) | ~35 tok/s | 流畅 |
| M3 Max · 64/96GB | ✅✅ | UD-Q5_K_M (26.5GB) | ~50 tok/s | 很爽 |
| M3 Ultra · 192GB | ✅✅ | BF16 (69GB) | ~55 tok/s | 原生精度 |
| M4 Pro · 24/48GB | ✅ | Q3 / Q4_K_M | 20 / 35 tok/s | 好 |
| M4 Max · 48GB | ✅✅ | UD-Q4_K_M (22GB) | ~45 tok/s | 接近旗舰 |
| M4 Max · 64/128GB | ✅✅ | UD-Q5_K_M | ~55 tok/s（llama.cpp）/ ~120 tok/s（MLX） | 本文推荐甜点 |
| **M5 Pro · 64GB** | ✅✅ | UD-Q5_K_M (26.5GB) | ~60 tok/s | **新机主流配置** |
| **M5 Max · 128GB** | ✅✅✅ | Q8_0 / BF16 | ~75 tok/s | 顶配个人机 |
| Windows 16GB RAM，无独显 | ❌ 不建议 | — | — | 跑不动 |
| Windows 32GB RAM，无独显 | ⚠️ 勉强 | UD-Q3_K_M | ~3 tok/s | 纯 CPU，慢 |
| Windows + RTX 4060 8GB | ⚠️ | UD-IQ4_XS | ~12 tok/s | 部分 offload |
| Windows + RTX 4070 Ti 12GB | ✅ | UD-Q4_K_M | ~25 tok/s | 能用 |
| Windows + RTX 4080 16GB + 64GB RAM | ✅ | UD-Q5_K_M | ~35 tok/s | 流畅 |
| Windows + RTX 4090 24GB + 64GB RAM | ✅✅ | UD-Q5_K_M | ~55 tok/s | 4000 系最佳 |
| **Windows + RTX 5060 Ti 16GB** | ✅ | UD-Q4_K_M | ~30 tok/s | 入门 50 系 |
| **Windows + RTX 5070 12GB** | ✅ | UD-Q4_K_M | ~35 tok/s | 12GB 卡性价比 |
| **Windows + RTX 5070 Ti 16GB** | ✅✅ | UD-Q5_K_M | ~50 tok/s | 50 系甜点 |
| **Windows + RTX 5080 16GB** | ✅✅ | UD-Q5_K_M | ~55 tok/s | |
| **Windows + RTX 5090 32GB** | ✅✅✅ | Q6_K (29.3GB) | ~80 tok/s | Win 顶配 |

---

## 三、为什么 MoE 对 Mac 特别友好

Qwen3.6 是 MoE（混合专家），这个架构和 Apple Silicon 的统一内存是天作之合。

![Apple MacBook Pro M5 Pro / M5 Max](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/qwen3.6-local-guide/Apple-M5-MacBook-Pro.jpg)

*图：Apple 2026 年 3 月发布的 MacBook Pro M5 Pro/Max，内存带宽最高 614 GB/s*

**传统稠密模型（如 Llama 3 70B）**：
- 70B 参数每个 token 都要参与计算 → 算力瓶颈
- CPU/GPU 内存分离 → 数据搬运开销大

**MoE 模型（Qwen3.6 35B-A3B）**：
- 只有 3B 参与计算 → 算力需求低，相当于跑 3B 模型
- 但所有 35B 必须随时可访问 → 内存需求大

**Apple Silicon 的优势**：
- **统一内存架构（UMA）**：CPU 和 GPU 共享同一块物理内存，无需拷贝
- **内存带宽大**：M4 Max 546 GB/s、**M5 Max 614 GB/s**，远超 DDR5 双通道（~100 GB/s）
- MoE 的随机专家访问模式 → UMA 零拷贝优势被放大

**实测对比**（同样 Q4_K_M 量化 Qwen3.6-35B-A3B）：

| 设备 | 后端 | tok/s | 备注 |
|---|---|---|---|
| M4 Max 64GB | llama.cpp | ~55 | 常规 |
| **M4 Max 64GB** | **MLX** | **~120** | **Apple 官方 ML 框架，性能翻倍** |
| M5 Pro 64GB | llama.cpp | ~60 | Fusion 架构带宽加成 |
| RTX 4090 24GB | llama.cpp CUDA | ~55 | 受 24GB VRAM 限制必须部分 CPU offload |
| RTX 5090 32GB | llama.cpp CUDA | ~80 | 全放 VRAM，GDDR7 带宽优势 |

这就是为什么我会推荐：**预算充足首选 M4 Max 64GB 或 M5 Pro 64GB**。一个设备顶 4090 + 64GB 系统，还省电。

---

## 四、Mac 档位详表

![Qwen3.6-Plus 能力概览](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/qwen3.6-local-guide/Qwen3.6-Plus-hero.png)

*图：Qwen3.6-Plus 定位 · 35B-A3B 是开源版本，本节主题*

### 轻量级：入门能跑（16-24GB）

| 机型 | 内存 | 推荐量化 | 文件大小 | tok/s | 可用上下文 | 备注 |
|---|---|---|---|---|---|---|
| M1 / M2 | 16GB | ❌ | — | — | — | 系统本身就占 ~10GB，跑 35B-A3B 最低 Q2 也会 swap |
| M1 Pro / M2 Pro | 16GB | UD-IQ2_M | 11.5 GB | ~6 | 4K | 能跑但慢，只适合短问答 |
| M1 Pro / M2 Pro / M3 Pro | 18GB | UD-Q3_K_M | 16.6 GB | ~10 | 8K | 建议关其他 App |
| M1/M2/M3 Pro | 24GB | UD-Q3_K_XL | 16.8 GB | ~15 | 16K | 日常够用 |
| M4 Pro | 24GB | UD-Q3_K_XL | 16.8 GB | ~18 | 16K | M4 代带宽更高 |

**关键坑**：macOS 会从物理内存切一部分给系统和 App。**16GB 机器实际可用给 LLM 的只有 ~8-10GB**。别听别人说"16GB 也能跑 35B"——是能跑 IQ1/IQ2 极限压缩，质量崩到不如 8B 模型。

### 主力级：日常流畅（32-64GB）

| 机型 | 内存 | 推荐量化 | 文件大小 | tok/s | 可用上下文 | 备注 |
|---|---|---|---|---|---|---|
| M1 Pro / M2 Pro | 32GB | UD-Q4_K_M | 22 GB | ~15 | 32K | 甜点入门 |
| M1 Max / M2 Max | 32GB | UD-Q4_K_M | 22 GB | ~18-22 | 32K | Max 带宽是 Pro 两倍 |
| M3 Pro / M4 Pro | 36/48GB | UD-Q4_K_M | 22 GB | ~25-35 | 64K | |
| M1 Max / M2 Max / M3 Max | 64GB | UD-Q5_K_M | 26.5 GB | ~30-45 | 128K | **性价比首选** |
| M4 Max | 48GB | UD-Q4_K_M | 22 GB | ~40-45 | 64K | |
| M4 Max | 64GB | UD-Q5_K_M | 26.5 GB | ~50-55 | 128K | **本文推荐甜点** |
| **M5 Pro** | **64GB** | **UD-Q5_K_M** | **26.5 GB** | **~55-65** | **128K** | **2026 新款主流** |

### 旗舰级：接近云端（96GB+）

| 机型 | 内存 | 推荐量化 | 文件大小 | tok/s | 可用上下文 | 备注 |
|---|---|---|---|---|---|---|
| M3 Max / M4 Max | 96GB | UD-Q6_K | 29.3 GB | ~50 | 256K | |
| M2 Ultra | 128GB | Q8_0 | 36.9 GB | ~50 | 256K | Mac Studio / Pro |
| M4 Max | 128GB | Q8_0 | 36.9 GB | ~55-60 | 256K | |
| **M5 Max** | **128GB** | **Q8_0 / BF16** | **37~70 GB** | **~70-80** | **256K 全开** | **614 GB/s 带宽** |
| M3 Ultra | 192GB | BF16 | 69.4 GB | ~55 | 256K | 原生精度本地跑 |

### MLX 彩蛋：Mac 性能翻倍

![MLX · Apple 官方 ML 框架](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/qwen3.6-local-guide/MLX-og.png)

*图：Apple 开源的 [MLX 框架](https://github.com/ml-explore/mlx)*

如果你愿意折腾 Apple 官方的 MLX 框架（非 Ollama/llama.cpp），**同样机器 tok/s 能翻 1.5-2 倍**：

- M4 Max 64GB：llama.cpp 55 tok/s → **MLX 120+ tok/s**
- M3 Max 64GB：llama.cpp 45 tok/s → **MLX 90 tok/s**

适合愿意敲命令、榨每一滴性能的同学。装法见本文第七节方案 C。

---

## 五、Windows 档位详表

![NVIDIA GeForce RTX 5090](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/qwen3.6-local-guide/NVIDIA-RTX-5090.jpg)

*图：NVIDIA RTX 5090（Blackwell · GDDR7 · 32GB VRAM），Windows 平台跑 Qwen3.6-35B-A3B 的顶配*

Windows 的硬伤：**没有统一内存，MoE 必须手动权衡 GPU VRAM + 系统 RAM**。而且 Ollama 在 Windows 上对 MoE 的支持一度有 bug（[issue](https://github.com/ollama/ollama/issues/10458)），**强烈推荐走 WSL2 + llama.cpp 或 LMStudio**，不要用 Windows 原生 Ollama。

### 没有独立显卡

| 配置 | 推荐量化 | tok/s | 备注 |
|---|---|---|---|
| 16GB RAM 笔记本 | ❌ | — | 根本跑不动，换模型或买独显 |
| 32GB RAM 笔记本/台式 | UD-IQ3_S (13.7 GB) | ~2-4 | 纯 CPU，勉强能问答 |
| 64GB RAM 台式机 | UD-Q4_K_M (22 GB) | ~4-6 | 纯 CPU 上限 |

### RTX 40 系（Ada，GDDR6X）

| 显卡 VRAM | 系统 RAM | 推荐量化 | 文件大小 | tok/s | 备注 |
|---|---|---|---|---|---|
| RTX 4060 8GB | 32GB | UD-IQ4_XS | 17.7 GB | ~10-15 | 大部分在 CPU，GPU 辅助 |
| RTX 4060 Ti 16GB | 32GB | UD-Q4_K_M | 22 GB | ~20-25 | |
| RTX 4070 Ti 12GB | 32GB | UD-Q4_K_M | 22 GB | ~25-30 | |
| RTX 4070 Ti Super 16GB | 32GB | UD-Q4_K_M | 22 GB | ~30-35 | |
| RTX 4080 16GB | 64GB | UD-Q5_K_M | 26.5 GB | ~35-42 | |
| RTX 4080 Super 16GB | 64GB | UD-Q5_K_M | 26.5 GB | ~40-48 | |
| **RTX 4090 24GB** | **64GB** | **UD-Q5_K_M** | **26.5 GB** | **~55-65** | **40 系最佳性价比** |

### RTX 50 系（Blackwell，GDDR7 · 带宽提升 ~50%）

**50 系最大变化**：换 GDDR7（比 GDDR6X 带宽高 ~40-50%）、CUDA 架构升级、FP4 原生支持（未来有用）。对 LLM 推理的实际增益主要来自内存带宽。

| 显卡 VRAM | 系统 RAM | 推荐量化 | 文件大小 | tok/s | 备注 |
|---|---|---|---|---|---|
| RTX 5060 8GB | 32GB | UD-IQ4_XS | 17.7 GB | ~15-20 | 比 4060 明显好，但 8GB 还是受限 |
| **RTX 5060 Ti 16GB** | 32GB | UD-Q4_K_M | 22 GB | ~28-32 | **50 系入门甜点**，16GB 够塞 |
| **RTX 5070 12GB** | 32GB | UD-Q4_K_M | 22 GB | ~32-38 | 带宽 672 GB/s（比 4070 Ti 快 ~20%） |
| **RTX 5070 Ti 16GB** | 64GB | UD-Q5_K_M | 26.5 GB | ~45-52 | **50 系性价比王者** |
| RTX 5080 16GB | 64GB | UD-Q5_K_M | 26.5 GB | ~50-58 | 比 4080 Super 快 ~25% |
| **RTX 5090 32GB** | 64GB | **Q6_K (29.3 GB)** | 29.3 GB | **~75-85** | **全放 VRAM + GDDR7**，Win 顶配 |

**50 系选型提醒**：
- **12GB vs 16GB VRAM 决定量化上限**：12GB 卡（5070）只能 Q4_K_M；16GB 卡（5070 Ti/5080）可上 Q5_K_M，智力差异明显
- **5090 的 32GB VRAM 是质变**：可以全量加载 Q6_K（29.3GB）连带 KV cache 全在 VRAM，不用 CPU offload
- **5060 8GB 别碰**：和 4060 一样 VRAM 太小，对 35B-A3B 都要 CPU offload，收益少

**Windows 上跑 MoE 的隐藏坑**：当量化文件比 VRAM 稍大（例如 Q4_K_M 22GB vs 4090 24GB），因为 KV cache 还要占几 GB，引擎会把整层 offload 到 CPU，速度直接腰斩。**宁可选小一档的量化让它全部装进 VRAM，也别卡着上限跑**。

---

## 六、工具选择：Ollama vs LMStudio vs llama.cpp

![LMStudio · GUI 派首选](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/qwen3.6-local-guide/LMStudio-og.png)

*图：LMStudio 界面 · 点按钮就能下模型、开 Chat，完全不用命令行*

| 工具 | 适合谁 | 优点 | 缺点 |
|---|---|---|---|
| **Ollama** | 90% 用户 | 一行命令装好，跨平台，生态大 | MoE Windows 有 bug；细粒度调参麻烦 |
| **LMStudio** | 不想敲命令的人 | 图形界面，内置模型下载，像装 App | 封闭，调优空间小 |
| **llama.cpp** | 爱折腾的工程师 | 最全参数、最新量化第一时间支持 | 要自己编译，命令多 |
| **MLX** | Mac 玩家 | tok/s 翻倍 | 只能 Mac，生态小 |

**给普通人的建议**：
- **Mac 用户**：先装 Ollama 试试水，熟了之后折腾 MLX 榨性能
- **Windows 用户**：装 LMStudio（点按钮就能用）或 WSL2 + Ollama，绕开 Windows 原生 Ollama 的 MoE 坑

---

## 七、Mac 完整安装步骤

![Ollama · 一行命令的 LLM 管家](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/qwen3.6-local-guide/Ollama-og.png)

*图：Ollama · Mac 用户最简首选*

### 方案 A · Ollama（10 分钟上手）

```bash
# 1. 装 Ollama
curl -fsSL https://ollama.com/install.sh | sh

# 2. 拉 4-bit 量化（22GB，视网速 5-15 分钟）
ollama pull qwen3.6:35b-a3b-q4_K_M

# 3. 开聊
ollama run qwen3.6:35b-a3b-q4_K_M
```

查看可用量化：[ollama.com/library/qwen3.6](https://ollama.com/library/qwen3.6)。64GB 机器改 `q5_K_M`；32GB 机器降 `q3_K_M`。

### 方案 B · LMStudio（点按钮派）

1. 去 [lmstudio.ai](https://lmstudio.ai) 下载 macOS 版
2. 左侧搜索 `Qwen3.6-35B-A3B`
3. 按你的机器选 GGUF 文件（旁边显示"Fully GPU Offload"绿色标签的就是你能跑的）
4. 下载完点"Chat" → 立刻用

**设置要点**：
- `Context Length`：32K（日常够）/ 128K（长文/代码）
- `GPU Offload`：拉满（Mac 会自动用 Metal）
- `Flash Attention`：开

### 方案 C · MLX（Mac 性能榨干派）

```bash
# 1. 装 MLX 和 mlx-lm
pip install mlx-lm

# 2. 拉模型（MLX 原生量化）
python -m mlx_lm.server --model mlx-community/Qwen3.6-35B-A3B-4bit

# 3. 这就是个 OpenAI 兼容 server，端口 8080
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"qwen","messages":[{"role":"user","content":"你好"}]}'
```

用过都说好——M4 Max 上 120 tok/s 不是梦。

---

## 八、Windows 完整安装步骤

### 方案 A · LMStudio（最稳）

1. [lmstudio.ai](https://lmstudio.ai) 下载 Windows 版
2. 搜 `Qwen3.6-35B-A3B`，下载 `UD-Q4_K_M` GGUF
3. 设置页 → `GPU Offload`：如果你是 RTX 4090/5090 就拉到 99（全部层 GPU）；如果是 8GB 卡拉到 12-16 层（剩下 CPU）
4. 开 Chat

### 方案 B · WSL2 + Ollama（避开 Windows 原生坑）

```powershell
# 1. 装 WSL2 + Ubuntu
wsl --install

# 2. 进 WSL2 Ubuntu
wsl
```

然后在 Ubuntu 里走 Mac 方案 A 的步骤。**显卡会被 WSL2 自动识别**（Windows 11 + NVIDIA 最新驱动）。

### 方案 C · 纯 Windows 版 llama.cpp（最灵活）

![llama.cpp · 最底层的 LLM 推理引擎](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/qwen3.6-local-guide/llamacpp-og.png)

*图：llama.cpp · 所有 GGUF 量化的源头*

```powershell
# 装 winget（Win10/11 自带）
winget install llama.cpp

# 下 GGUF
curl -L -o qwen3.6-35b-a3b.gguf `
  https://huggingface.co/unsloth/Qwen3.6-35B-A3B-GGUF/resolve/main/Qwen3.6-35B-A3B-UD-Q4_K_M.gguf

# 启动（4090/5090 示例）
llama-server -m qwen3.6-35b-a3b.gguf --port 8000 -ngl 99 -c 32768 --jinja
```

参数：`-ngl 99` 全 GPU，`-ngl 20` 部分 GPU 部分 CPU，`-c 32768` 上下文长度。

### 50 系专属提示

- **5090 32GB**：能直接全量加载 Q6_K（29.3GB）+ 32K 上下文的 KV cache，不用 offload。这是 Windows 平台目前最流畅的配置。
- **5070 Ti 16GB**：甜点之选，性价比最高。**别用 Q5_K_M XL**（26.6GB 超上限），走 Q5_K_M（26.5GB）临界但可行，或降到 Q4_K_M 更稳。
- **5060 Ti 16GB**：最低"像样"的 50 系选项，Q4_K_M 能全上 VRAM。性能比同价位 4060 Ti 16GB 高 ~30%。

---

## 九、性能调优小手册

**Mac：**
- 跑之前 `sudo purge` 清内存 cache，给 LLM 留尽可能多物理内存
- 电源选"最佳性能"（System Settings → Battery）
- **插电**：M 系列在电池上跑 LLM 降频严重，速度直接减半
- 长时间跑用底部散热架或抬起机身，防止热节流

**Windows：**
- 独显机器在 NVIDIA Control Panel → Manage 3D Settings → Power Mode → Prefer Maximum Performance
- 关掉 Windows Defender 实时扫描模型目录（扫大文件会卡）
- 32GB 以下机器跑之前把 Chrome/Slack 等关掉
- 散热差的笔记本别跑，结局是降频到 5 tok/s
- **50 系驱动**：确保用 NVIDIA 572.xx 或更新版本，早期 50 系驱动对 CUDA inference 有 bug

**通用：**
- **context 开多大有讲究**：
  - 4K-16K：日常问答、短代码，省内存
  - 32K-64K：阅读长文、项目级代码
  - 128K+：全仓库读代码。但 KV cache 吃内存厉害
  - 每 token 的 KV cache 占用：35B-A3B 大约 **0.4MB/token**。128K 上下文 = ~50GB 额外显存/内存
- `presence_penalty=1.5`：**Qwen 官方强调必须加**，不然输出循环复读
- `temperature=1.0`：别抄 GPT 的 0.7，Qwen 调教偏好高温

---

## 十、常见坑

- **"16GB Mac 也能跑"**：能跑 IQ1/IQ2，但智力掉到不如 Qwen3-7B。别自欺。
- **Ollama 默认 tag 是低量化**：`ollama pull qwen3.6:35b-a3b` 会给你 Q2 或 Q3，质量打折。显式指定 `q4_K_M` / `q5_K_M`。
- **Windows 原生 Ollama 跑 MoE 慢如龟**：见 [GitHub issue #10458](https://github.com/ollama/ollama/issues/10458)。走 LMStudio 或 WSL2。
- **量化卡着 VRAM 上限**：22GB 文件 vs 24GB 卡，加上 KV cache 撑爆。主动降一档。
- **50 系早期驱动**：Blackwell 刚上市时 CUDA 13.0 有坑，更新到 NVIDIA 572+ 驱动 + CUDA 13.1 解决。
- **忘了开思维模式**：Ollama 在 Modelfile 里默认关，手动加 `/set parameter think true`。质量会上一个台阶。
- **忘了 presence_penalty**：输出开始复读同一句话，就是这个原因。
- **温度调太低**：Qwen 偏爱 `temperature=1.0, top_p=0.95, top_k=20`。

---

## 十一、一键决策：我该选哪个量化？

回答三个问题：

**Q1. 你的机器可用内存（Mac 是总内存，Windows 是 VRAM + 系统 RAM 能给 LLM 的那部分）？**
- < 16GB → 放弃跑 35B-A3B，换 Qwen3-8B
- 16-20GB → UD-IQ3_S（13.7GB）
- 20-24GB → UD-Q3_K_M（16.6GB）或 UD-Q3_K_XL（16.8GB）
- 24-32GB → UD-Q4_K_M（22GB）⭐
- 32-48GB → UD-Q5_K_M（26.5GB）⭐⭐
- 48-64GB → UD-Q6_K（29.3GB）或保留给 context
- 64GB+ → Q8_0（36.9GB）接近无损
- 128GB+ → BF16（69.4GB）原生

**Q2. 你主要用它干嘛？**
- 闲聊 / 短问答 → 32K context，Q4_K_M 够用
- 读长文档 / 代码 → 64K-128K context，至少要 48GB+ 机器
- 重度代码辅助 → 选 Q5_K_M 起步，智力有感差异

**Q3. 速度底线？**
- **>70 tok/s**：M5 Max 128GB、M4 Max 64GB（MLX）、RTX 5090
- **50-70 tok/s**：M4 Max 64GB（llama.cpp）、M5 Pro 64GB、RTX 4090、RTX 5070 Ti、RTX 5080
- **20-50 tok/s**：M2 Max 64GB、M3 Max 48GB、RTX 4080、RTX 5060 Ti/5070
- **<20 tok/s**：32GB Mac、RTX 4060/4070、RTX 5060
- **<10 tok/s**：16GB Mac、纯 CPU Windows（别买，体验差）

---

## 十二、参考资料

- [HuggingFace · Qwen/Qwen3.6-35B-A3B](https://huggingface.co/Qwen/Qwen3.6-35B-A3B)（官方模型卡）
- [HuggingFace · unsloth Q 系列 GGUF](https://huggingface.co/unsloth/Qwen3.6-35B-A3B-GGUF)（推荐量化来源）
- [Ollama · Qwen3.6 model library](https://ollama.com/library/qwen3.6)
- [MLX · mlx-lm](https://github.com/ml-explore/mlx-lm)（Mac 性能翻倍）
- [llama.cpp · Apple Silicon 性能讨论](https://github.com/ggml-org/llama.cpp/discussions/4167)
- [LMStudio · GUI 派首选](https://lmstudio.ai)
- [Apple · MacBook Pro M5 Pro/Max 发布](https://www.apple.com/newsroom/2026/03/apple-introduces-macbook-pro-with-all-new-m5-pro-and-m5-max/)
- [NVIDIA · GeForce RTX 50 系](https://www.nvidia.com/en-us/geforce/graphics-cards/50-series/)
- [Ante Kapetanovic · Qwen3.5 Apple Silicon MLX benchmark](https://antekapetanovic.com/blog/qwen3.5-apple-silicon-benchmark/)

---

*Last updated: 2026-04-22*
