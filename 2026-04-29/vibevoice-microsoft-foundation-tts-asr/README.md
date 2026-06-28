---
title: "微软 VibeVoice：去年因滥用被撤下、今年又复出，这套开源前沿语音 AI 到底什么水平"
date: 2026-04-29
weekday: 星期三
tags: [多模态, 语音 AI, 开源, Microsoft, TTS, ASR]
description: "Microsoft VibeVoice 2026-04-29 实测：44,575 ⭐ / 当日 +1,523 / HN 47933236 277 pts、161 评论、ASR 9B 参数 + TTS 1.5B（实 3B，基座千问 2.5）+ Realtime 0.5B 三档 MIT 许可、HF 月下载 ASR 737,428 / TTS 215,324、2025-09-05 因滥用风险被微软撤下后又复出。截至 2026-04-29，国内 36氪 / 量子位 / 机器之心 / 虎嗅 / CSDN 主流媒体未检索到中文报道，auto-research arbitrage 选题。文章覆盖三档模型差异、撤下事件原文、HN 顶赞批判（含 open-source vs open-weight 争议）、与国内开源语音 AI（CosyVoice / Qwen3-ASR）的横向比较。"
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/04/29/vibevoice-microsoft-foundation-tts-asr.png
---

# 微软 VibeVoice：去年因滥用被撤下、今年又复出，这套开源前沿语音 AI 到底什么水平

> microsoft/VibeVoice 这两天的数据：44,585 ⭐（仍在攀升）、4,961 forks、Python、MIT 许可、4 月 24 日有最新 push。HN 47933236 帖子拿到 285 pts、162 条评论。仓库自报家门"开源前沿语音 AI"。但翻它的 README 你会看到一段很奇怪的话——2025 年 9 月 5 日，"出于负责任 AI 的考量"微软自己把 VibeVoice-TTS 的代码从仓库里删了，今年才陆续放回来。

中文世界目前 VibeVoice 的深度评测很稀缺，大多数报道还停留在"开源前沿语音 AI 发布"的标题党层面。但这条仓库的故事比"又一个开源 TTS"复杂得多：

- 它是**完整的三档语音模型族**，不是单点 TTS
- 它的 TTS 基座是**阿里千问 Qwen2.5-1.5B**（微软的语音 AI 用国产 LLM 做底座）
- 它去年因滥用被微软自己撤下、今年又复出，README 上撤下声明还在
- HN 顶赞把"开源"两个字喷了——"这不是开源，是开权重"

## 一、三档模型，差距比想象中大

打开 [microsoft/VibeVoice](https://github.com/microsoft/VibeVoice) 的 README，你会看到三个模型变体——很多英文 blog 翻译成中文时把它们混成一个，其实是三件不同的事：

| 模型 | 参数 | 用途 | HF 月下载 | 论文 |
|---|---|---|---|---|
| **VibeVoice-ASR** | 9B | 60 分钟一遍过的语音识别，自带说话人分离 | 737,428 | arxiv 2601.18184 |
| **VibeVoice-TTS**（命名 1.5B） | 实际 3B（1.5B 基座 LLM + ~123M diffusion head） | 90 分钟长音频生成，最多 4 说话人 | 215,324 | arxiv 2508.19205 |
| **VibeVoice-Realtime** | 0.5B | ~300ms 首音延迟，流式 TTS | （HF 单列模型卡） | — |

数据来自 [VibeVoice-ASR 模型卡](https://huggingface.co/microsoft/VibeVoice-ASR) 和 [VibeVoice-1.5B 模型卡](https://huggingface.co/microsoft/VibeVoice-1.5B)。

注意几个事实：

1. **9B vs 1.5B 不是同一种压缩规格——是两个独立模型**。HN 上有评论说"7B variant"，那是社区 archive 的旧 TTS，不是当前的 ASR 9B
2. **TTS 命名"1.5B"指的只是基座 LLM 部分**。模型卡上写的总参数是 3B（1.5B Qwen2.5 + σ-VAE 声学 tokenizer + 4 层 ~123M diffusion head + 各种适配层）。这是个标注容易让人误判的地方
3. **737k 月下载量** 是 ASR 9B 的，远大于 TTS 1.5B 的 215k——意味着用户更认 ASR

![VibeVoice 主架构（来自仓库 Figures/VibeVoice.jpg）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-29/vibevoice-microsoft-foundation-tts-asr/vibevoice-VibeVoice.jpg)

## 二、技术核心：7.5 Hz 帧率 + 80 倍压缩 + Qwen2.5 基座

VibeVoice 论文（[arxiv 2508.19205](https://arxiv.org/abs/2508.19205)，2025-08-26 发表，13 位微软作者，包含资深研究员 Furu Wei）的 abstract 原文：

> "we introduce a novel continuous speech tokenizer that, when compared to the popular Encodec model, improves data compression by 80 times while maintaining comparable performance."

把它拆开：

- **声学 tokenizer**：σ-VAE 变体，7 层 Transformer block，从 24 kHz 下采样 3200 倍。结果是只用 7.5 Hz 帧率就能编码语音
- **语义 tokenizer**：用 ASR 代理任务训
- **基座 LLM**：[Qwen2.5-1.5B](https://huggingface.co/Qwen/Qwen2.5-1.5B)。**这是微软主导的语音 AI，但底座是阿里开源的中文模型**——一个值得国内开发者注意的事实
- **Diffusion head**：4 层 ~123M 参数，用 DDPM + Classifier-Free Guidance
- **Curriculum learning**：上下文长度从 4K → 16K → 32K → 64K 逐步扩张
- **训练时冻结 tokenizer**，只训 LLM + diffusion head

64K 的 context window 是它能做 90 分钟长音频的关键。一般 TTS 模型能撑 30 秒已经很好，VibeVoice 把它推到 90 分钟、4 个说话人对话。论文里测下来在长对话生成上"surpasses open-source and proprietary dialogue models"——但具体击败了哪个 proprietary 模型，论文 abstract 没点名。

ASR 这条线的论文是另一篇（[arxiv 2601.18184](https://huggingface.co/microsoft/VibeVoice-ASR)，2026-01 发布），跟 TTS 论文不是同一组实验。两条线的评测维度也不同：

| 评测维度 | TTS（arxiv 2508） | ASR（arxiv 2601） |
|---|---|---|
| 主要指标 | MOS preference、长音频流畅性 | DER（Diarization Error Rate）、cpWER、tcpWER |
| 测试集 | 论文 abstract 未列 | 模型卡未给具体测试集名称 |
| 语言 | 主要英文 + 中文 | 50+ 语言、code-switching 内/外 |

ASR 9B 的真正卖点是**自带说话人分离**——HN 用户 mberg 在帖子下评论原文是这么说的：

> "I've been using VibeVoice's ASR (speech to text) model quite intensively for the past month and have found it to be a lot more reliable and out-of-the box functional then Whisper, parakeet and other models. The fact that is has diarization built into to the model is a huge win in my book. Without that you have to run a different model just for that which adds significantly to the overall processing time vs VibeVoice which gives you reliably great results. Big fan."

一般 ASR pipeline 是 Whisper 转写 + pyannote 做 speaker diarization，两个模型分开调，错误叠加。VibeVoice-ASR 把这两件事合到一个模型里，端到端少一层。mberg 这条原文里还提到了 NVIDIA Parakeet（NeMo 系 ASR），意思是他自己实测对比过几个开源 ASR，VibeVoice 综合最稳。

![VibeVoice 在跨语言 TTS 上的 MOS 偏好对比（来自仓库 Figures/MOS-preference.png）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-29/vibevoice-microsoft-foundation-tts-asr/vibevoice-MOS-preference.png)

## 三、2025-09-05 的撤下事件，README 上声明还在

仓库 README 现在还能看到这一段（VibeVoice 1.5B 模型卡的"Out-of-Scope/Prohibited Uses"小节附近）：

> "Since responsible use of AI is one of Microsoft's guiding principles, we have removed the VibeVoice-TTS code from this repository."

发生时间是 **2025-09-05**。微软项目页（microsoft.github.io/VibeVoice）当时还有更激烈的声明：

> "have disabled the repo until we are confident that out-of-scope use is no longer possible."

这件事的背景：VibeVoice 当时支持的 TTS 7B 变体被发现做出来的合成语音几乎听不出 AI 痕迹，社区有人开始用它做声音克隆——克隆名人声音做诈骗、政治深度伪造、骚扰电话。微软第一时间下架代码，要求"直到我们确信不能再被滥用为止"。

到了 2025 年底 / 2026 年初，仓库慢慢恢复——但**永远没把原本最强的 TTS 7B 放回来**。当前 README 里 TTS 一档只有 1.5B 命名（3B 总参）的版本，加上一系列硬性限制：

- 输入音频自动嵌入**可听见的 AI disclaimer**
- 同时打**不可感知的水印**用于事后溯源
- 推理日志做哈希记录用于滥用检测
- 微软声明每季度公开聚合统计

HN 用户 Stagnant 评论里提到：被撤下的 TTS 7B 在社区里有 archive。Stagnant 原文："The 7B parameter Vibevoice TTS model is still the most impressive local TTS model i've tried"——他自己测过的本地 TTS 里，被撤下的 7B 还是最好的。这是个灰色地带——微软撤下了官方分发，但模型 weight 已在外。

技术上看，3B 的当前版与 7B 旧版差距不会小——参数量差一倍多，subjective quality 通常差感知很明显。微软的做法本质是用**降级模型 + 强制水印 + 强制 disclaimer**做安全权衡：知道还会被滥用，但底线先建起来。

## 四、HN 顶赞把"开源"两个字喷了

HN 47933236 这帖最尖锐的评论是 maxloh（7 小时前）那条原文：

> "I think we should stop calling this type of models open source. They are indeed 'open weight.' The training code is proprietary and never revealed."
> （配的链接是 [github.com/microsoft/VibeVoice/issues/102](https://github.com/microsoft/VibeVoice/issues/102)）

这是 OSI（开源促进会）2024-2025 一直推的争论。OSI 在 2024 年 10 月发布的 [OSAID 1.0（Open Source AI Definition）](https://opensource.org/ai/open-source-ai-definition) 认为，一个模型要叫"开源 AI"，必须同时满足三大要素的"足够细节"：

1. **Data Information**：训练数据的足够细节信息（不一定原始数据集开放，但得能让人独立复现训练数据集）
2. **Code**：训练 / 推理代码、数据处理脚本
3. **Weights and Parameters**：完整的模型权重和参数

且要支持四类自由（study / use / modify / share）。

VibeVoice 真正公开的只有第 3 条 + 部分推理代码。**训练数据信息完全没公开**——模型卡里只笼统写"inherits biases from Qwen2.5-1.5B base model"，没说预训练用了什么音频数据集、版权状态、地域分布；issue #102 里 maxloh 链的是社区追问"训练代码什么时候放出来"——微软至今没回。

按 OSAID 1.0 这是 open-weight，不是 open-source AI。但微软在 README 里坚持用 "open-source frontier voice AI" 这个词。HN 上 maxloh 这种较真的人就在喷。

其他几条顶赞评论也值得逐字看（来自 [HN 47933236](https://news.ycombinator.com/item?id=47933236)）：

**steinvakt2（7 小时前）**：

> "This is not a new model. Also, it hallucinates a lot. Also, it's very heavy and slow in inference. It's also bad in multilingual.
>
> Edit: I'm talking purely about speech to text (STT). Not sure about the other things this can do."

—— 一开始没限定上下文导致语气很冲，加上 Edit 后才说自己只是在评 STT，对 TTS / Realtime 没意见。这条评论的本身就反映了"前沿"两个字的定义争论。

**gagan2020（5 小时前）**：

> "It is not good for text to speech (TTS) as well. I am trying it for few days. First of all 1.5B model documentation is not there. 0.5B realtime is shit model. I was converting text, line by line and it was randomly adding music and couldn't handle special characters like '…'. I really disappointed with this model to say the least."

—— 实测者反馈，TTS 1.5B 没文档、Realtime 0.5B 直接被骂、一行行转换文字时会随机加背景音乐、连省略号都处理不好。

**Stagnant（1 小时前）**：

> "The 7B parameter Vibevoice TTS model is still the most impressive local TTS model i've tried. It was pulled by Microsoft a few days after its release due to 'abuse potential' but it can be found in various community maintained huggingface repos."

—— 这条印证了"被撤下的 7B 版本仍在社区流通"的灰色地带。

整理下来 HN 主流声音是：**ASR 比 TTS 好用、TTS 比想象中差、Realtime 0.5B 不要试，被撤下的 7B 旧版才是真正口碑王**。

![VibeVoice 支持的语言分布（来自仓库 Figures/language_distribution_horizontal.png）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-29/vibevoice-microsoft-foundation-tts-asr/vibevoice-language_distribution_horizontal.png)

## 五、与国内开源语音 AI 横向对比

国内开源语音 AI 这两年生态密度很高，把 VibeVoice 摆进来对比一下：

| 项目 | 主体 | 类型 | 参数 / 卖点 | 许可 |
|---|---|---|---|---|
| **VibeVoice-ASR** | 微软 | ASR 自带 diarization | 9B / 50+ 语言 / 60 分钟一遍过 | MIT |
| **Qwen3-ASR**（阿里） | 阿里 | ASR + 噪声鲁棒 | 中英多语 / 量子位报道过 | Apache 2.0 |
| **CosyVoice 2**（阿里） | 阿里 | TTS + 跨语言 zero-shot | 中英日韩等多语 / 量子位 2024 报道 | Apache 2.0 |
| **VibeVoice-TTS** | 微软 | 长对话多说话人 TTS | 3B / 90 分钟 / 4 人 / 强制水印 | MIT |
| **MegaTTS-3**（字节） | 字节 | 韵律 / 个性化 | 短语音克隆见长 | Apache 2.0 |
| **VibeVoice-Realtime** | 微软 | 流式 TTS | 0.5B / 300ms 首音 | MIT |
| **GLM-Voice**（智谱） | 智谱 AI | 端到端语音对话 | 9B / 实时打断 | Apache 2.0 |

读出来的结论：

1. **TTS 领域，国内 CosyVoice 2 在多语言 zero-shot 上更稳**，但 VibeVoice-TTS 在长对话（90 分钟、4 人）这个垂直场景独占山头
2. **ASR 领域，VibeVoice-ASR 9B 自带 diarization 是真稀缺**，但 9B 推理成本不低，单 GPU（24GB）跑 60 分钟音频不便宜
3. **Realtime 流式 TTS，国内的 GLM-Voice 端到端 + 打断更现代**——VibeVoice-Realtime 0.5B 在 HN 上口碑差
4. **许可全部 Apache / MIT**，对企业商用都友好。VibeVoice 的特殊之处是强制水印 + 推理日志，这一层实际部署时要做兼容
5. **基座选择上微软选了千问**——这个 cross-pollination 在 2026 年看起来很自然，但放到 2024 是不可想象的

## 六、想跑起来怎么搞

ASR 9B 跑得起的主流方案：

```bash
# Hugging Face transformers 路径
pip install transformers torch
huggingface-cli download microsoft/VibeVoice-ASR
# 24GB GPU 单卡推荐，13GB 够 ASR 9B BF16 加载（loose）
```

TTS 1.5B 命名（实际 3B）路径：

```bash
huggingface-cli download microsoft/VibeVoice-1.5B
# 推荐 16GB GPU 起跳。微软不推荐用于商业部署
```

国内开发者要小心几件事：

1. **HF 镜像**：不能直连 huggingface.co 时，用 `HF_ENDPOINT=https://hf-mirror.com` 切镜像，或者 ModelScope 上找镜像
2. **水印不能关**：VibeVoice 的输入做了 SDK 级别的水印写入，关掉违反 Microsoft 的 use policy（`responsible_ai_use.md` 里有明文）
3. **disclaimer 不能去**：所有生成音频自动嵌入"This audio was generated by VibeVoice"语音前缀
4. **训练数据合规风险**：模型卡里没公开数据来源，下游商用如果是面向 C 端，需要法务审一下"训练数据合规链是否可追溯"
5. **不要用于声音克隆 / 实时换音 / 任何身份冒用** —— 模型卡 Out-of-Scope 列表第一条就是这个，违反触发的不只是技术问题

如果你是评估方向用：

- 想要"端到端 ASR + 说话人分离"：值得放进 PoC 候选
- 想要"长对话播客生成"：值得测，但跟内部商业模型比一下成本
- 想要"实时语音助手底座"：先看 GLM-Voice 或 CosyVoice 2 + 自己的 LLM，VibeVoice-Realtime 0.5B 还不太成熟

## 七、撤下又复出这件事，对开源 AI 的更长信号

VibeVoice 2025-09-05 撤下、半年后复出这条曲线，比模型本身更值得想：

1. **大厂出品的"前沿开源"**几乎都得带刹车系统了。不光是水印 + disclaimer，还有 hashed inference logging（哪些 prompt 触发推理）、季度统计公开。这是模型层的一种"治理 SBOM"
2. **降级 + 安全权衡**变成新常态。VibeVoice 永远不会再放 7B TTS，因为那个版本太好用、克隆太逼真。换成 1.5B（3B 总参）是质量下降，但风险下降更多
3. **community archive 的灰色市场**还会存在。Stagnant 的评论说明 7B 旧版在社区有人留底——你可以下载，但微软不再背书
4. **OSAID 标准的执行力**继续考验大厂。微软还在用"open-source"称呼一个不公开数据的模型，OSI 还在喷。这场争论 2026 年还会继续

国内观察这件事的窗口最近也开了：阿里千问 / 智谱 / 深度求索 这一波语音 AI 项目是不是要参考 VibeVoice 的水印 + disclaimer + 推理日志做"自带刹车"，是个值得看的工程问题——不只是合规问题。

## 编辑说

VibeVoice 这条仓库我读完最大的感受是：**前沿开源语音 AI 已经过了"放出来就是赢"的阶段，进了"怎么放得有底线"的阶段**。三档模型、强制水印、撤下又复出、HN 上的 open-source vs open-weight 争论——这些都不是技术问题，是工程哲学问题。

如果你只关心"能不能用"，结论是：ASR 值得用、TTS 看场景用、Realtime 暂时别用。如果你关心"开源 AI 接下来怎么走"，VibeVoice 是个比 Llama / DeepSeek 更典型的样本——它代表的是**带刹车的开源**，而不是"放出权重 + 自由竞争"。

接下来一年里，几乎每个大厂的语音 / 视频 / 图像生成模型，都会越来越像 VibeVoice，而不是越来越像 Llama 1。
