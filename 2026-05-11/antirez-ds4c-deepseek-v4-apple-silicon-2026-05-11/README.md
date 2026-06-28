---
title: "Redis 之父手写 DeepSeek V4 专属 Mac 引擎"
description: "Redis 作者 antirez 用 C + Metal 给 DeepSeek V4 Flash 写了一台只跑 Apple Silicon 的本地推理引擎 ds4.c。GitHub 5950 Star、5/6 上线、5/10 国内五家媒体头条。Q2 量化 81GB，M3 Max 128GB 跑出 26.68 t/s 生成 / 58.52 t/s 预填充，磁盘 KV cache 让百万上下文不再吃光统一内存。最有意思的反转是 README 第一条致谢就是 GPT 5.5——不是「我没用 AI」，是「AI 帮我把单文件写到能用」。国产顶级开源模型第一次有海外大佬反向给写底层引擎，这件事的份量比任何 benchmark 都大。"
date: 2026-05-11
weekday: 星期一
slug: antirez-ds4c-deepseek-v4-apple-silicon
category: 本地大模型 / 国产开源 / Apple Silicon
tags: [antirez, ds4.c, DeepSeek V4 Flash, Apple Silicon, Metal, MoE 量化, llama.cpp, Mac 本地推理]
cover: antirez-ds4c-deepseek-v4-apple-silicon-2026-05-11.png
---
# Redis 之父手写 DeepSeek V4 专属 Mac 引擎

![封面：antirez 给 DeepSeek V4 写 ds4.c](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/antirez-ds4c-deepseek-v4-apple-silicon-2026-05-11/antirez-ds4c-deepseek-v4-apple-silicon-2026-05-11.png)

5 月 6 日深夜，Salvatore Sanfilippo——也就是 Redis 作者 antirez——往个人仓库推了第一版 `ds4.c`。仓库描述一句话：「DeepSeek 4 Flash local inference engine for Metal」。一个用 C + Metal 写的本地推理引擎，专门跑一个模型，专门跑在一类机器上：Apple Silicon 的 Mac。到 5 月 10 日傍晚，这个仓库 5950 Star、435 Fork，量子位、36氪、ITBear、Linux.do、搜狐先后头版报道；Mac M3 / M4 用户的本地大模型群里讨论度只低于 DeepSeek V4 Flash 自己发布那一天。

国产顶级开源模型第一次出现这样一种状态：一个写过 Redis 的国际级开源系统软件作者，主动跨海给它写了一台从底层 C 代码到 Metal 着色器全部手写的专属引擎。这件事份量比任何 benchmark 都大。

## 关键参数一览

| 项目 | 数据 |
|---|---|
| 仓库 | `antirez/ds4`（MIT 许可） |
| 创建时间 | 2026-05-06 17:17 UTC |
| 截至 5/10 数据 | 5,950 Star / 435 Fork / 43 Issue |
| 代码语言 | C 55.4% / Objective-C 30.2% / Metal 13.8% |
| 目标模型 | DeepSeek V4 Flash 284B / 13B 激活 / 1M 上下文 |
| 量化方案 | Q2（81GB，128GB Mac 可跑）/ Q4（≥256GB） |
| M3 Max 128GB Q2 | 预填充 58.52 t/s，生成 26.68 t/s |
| M3 Ultra 512GB Q2 | 预填充 84.43 t/s，生成 36.86 t/s（11709 token 长上下文 27.39 t/s） |
| 兼容协议 | OpenAI `/v1/chat/completions` + Anthropic `/v1/messages` 双协议 |
| 默认搭档 | opencode、Pi、Claude Code 客户端均可直连 |
| 致谢首条 | llama.cpp / GGML（Georgi Gerganov 团队）+ GPT 5.5 |

数据来源：`gh api repos/antirez/ds4` 与官方 README 当日实查；HuggingFace `deepseek-ai/DeepSeek-V4-Flash` 模型卡 verbatim。

## 一、专属引擎到底「专」在哪里

国内媒体把 `ds4.c` 普遍写成「单文件 C 推理引擎」，这只说对了形态。真正的工程价值在「专属」两个字——它不是又一个通用 GGUF runner，而是把 DeepSeek V4 Flash 这一个模型的所有结构性假设，硬编码进了一个实现里。

README 自己讲得很直接：

> ds4.c is a small native inference engine for DeepSeek V4 Flash. It is intentionally narrow: not a generic GGUF runner, not a wrapper around another runtime, and not a framework.

翻译成工程语言，所谓「专属」体现在四件事：

**1. MoE 专家路由路径硬编码**
DeepSeek V4 Flash 是 284B 总参 / 13B 激活的稀疏 MoE。通用引擎要兼容各种 MoE 拓扑，路由查表是动态的；ds4.c 直接把 V4 Flash 的专家分配公式刻进 Metal kernel，省掉所有运行时分支。

**2. 非对称量化只压该压的部分**
README 原话：

> The 2 bit quants use a very asymmetrical quantization: only the routed MoE experts are quantized, up/gate at IQ2_XXS, down at Q2_K. The other components (shared experts, projections, routing) are left untouched to guarantee quality.

简单讲：占模型 80% 体积的「路由专家」被压到 2 bit；负责语义稳定的共享专家、投影层、路由头保持原精度。这套不对称配方拼出来的 Q2 模型 81GB，正好塞进 128GB 统一内存的 MacBook Pro，留 47GB 给 KV cache 和系统。

**3. KV cache 是磁盘公民，不是内存公民**
DeepSeek V4 Flash 自带「压缩 KV cache」（基于 Compressed Sparse Attention + Heavily Compressed Attention 的混合架构，HuggingFace 模型卡 verbatim）。压缩之后，KV 体积小到能直接落到 SSD 上。`ds4-server` 把这个特性做到底——会话切换不需要重 prefill，重启服务器之后接着昨天的对话往下打，几百毫秒恢复。README 的原话是：

> The KV cache is actually a first-class disk citizen.

对个人开发者意味着：一台 M3 Max 上同时维护十几个 Code Agent 长会话不再是天方夜谭。

**4. Tool call 协议层做精确字节对齐**
这是文章里其他媒体都没讲清楚的一段。Agent 客户端会把模型上次输出的工具调用规整成 JSON 再回传给模型；如果服务器二次渲染时少一个空格，KV cache 命中失效，下一轮就要从头 prefill。`ds4-server` 直接给每次工具调用分配 ID，把模型采样出的 DSML 原始字节存进基数树，下次客户端送回 ID 时直接拿原字节回放——KV cache 永不失效。这种「字节级精确回放」是把 Claude Code、opencode 这种 Agent 客户端跑顺的隐形主功臣。

![ds4.c 与 llama.cpp / MLX-LM / Ollama 的定位差异](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/antirez-ds4c-deepseek-v4-apple-silicon-2026-05-11/ds4c-vs-llamacpp-mlx-positioning.png)

## 二、跟 llama.cpp、MLX-LM、Ollama 同台跑分

DeepSeek V4 Flash 4 月底刚发布，社区很快把它塞进各家 Mac 推理栈。把同一台机器的不同方案放一起对比，能看清 ds4.c 真正的位置：

| 引擎 | 模型规模 / 量化 | 硬件 | 生成 t/s | 备注 |
|---|---|---|---|---|
| **ds4.c Q2 + Metal** | 284B / 13B 激活 | MacBook Pro M3 Max 128GB | **26.68** | 81GB 占用 / 上下文 32K，README 实测 |
| **ds4.c Q2 + Metal** | 同上 | Mac Studio M3 Ultra 512GB | **36.86** | 同条件，长上下文 11709 token 27.39 |
| **ds4.c Q4 + Metal** | 同上 | Mac Studio M3 Ultra 512GB | 35.50 | 256GB 以上才跑得起 |
| MLX-LM 4-bit | 同上 | Mac Studio M3 Ultra 256GB | 30–34 | 峰值统一内存 160-187GB |
| MLX-LM 8-bit | 同上 | Mac Studio M3 Ultra 256GB | 31 | 同等条件下纯 MLX 实测 |
| MLX-LM 2-bit DQ | 同上 | Mac Studio M3 Ultra 256GB | 56 | 但当前 PR 阶段，未稳定 |
| llama.cpp + Metal | 同上 | M3 Max 128GB | 待测 | antirez 自己 fork 出 `llama.cpp-deepseek-v4-flash` 实验分支，主线尚未合并 |

数据出处：ds4.c README v1.0；MLX-LM 数据出自 Ivan Fioravanti（MLX 社区核心贡献者）2026-05-08 X 帖与 InsiderLLM 5/9 测评；llama.cpp 主线 GitHub Discussion #4167 截至 5/10 仍标注 V4 Flash 为「实验性」。

读这张表别只看绝对数字。三件事更值得想：

- **成本曲线被压平了**：过去本地跑 200B 级 MoE 要 256GB+ Mac Studio（人民币起步 4 万），ds4.c 把门槛压到 128GB MacBook Pro，价格档位差一倍多。
- **专属化 vs 通用化的代价摆出来了**：MLX-LM 通用、什么模型都能跑；ds4.c 只跑这一个，但同硬件比 MLX 4-bit 高出约 10–15%。这是「单点深度优化」对「框架抽象」的胜利。
- **国产模型第一次成为生态主语**：以前是「Mac 用户能跑 Llama」「Mac 用户能跑 Qwen」；这次是「antirez 给 DeepSeek 单独写了一个引擎」。主语换了。

![ds4.c 编译 + 跑通 DeepSeek V4 Flash 的完整命令链](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/antirez-ds4c-deepseek-v4-apple-silicon-2026-05-11/ds4c-build-run-commands.png)

## 三、上手实操：从 clone 到 chat 一共多少步

国内开发者拿到一台 M3 Max 128GB 或 M4 Max 128GB 的 MacBook Pro，跑通 ds4.c 实际只需要四步。逐条对着仓库 README 复盘：

**第一步：拉代码 + 编译**

```bash
git clone https://github.com/antirez/ds4.git
cd ds4
make            # 编出两个二进制：ds4（CLI）+ ds4-server（HTTP）
```

ds4.c 没有 cmake、没有 conan、没有 vcpkg；一个 Makefile 直接编。Mac 自带 clang + Metal toolchain 就是全部依赖。

**第二步：下模型**

```bash
./download_model.sh q2          # 81GB Q2，给 128GB 机器
./download_model.sh q2 mtp      # 可选：投机解码 GGUF
```

下载来源是 antirez 自己维护的 HuggingFace 仓库 `antirez/deepseek-v4-gguf`（已经按非对称量化重打包；不是官方原版，不要拿 deepseek-ai 仓库的文件来代）。脚本支持 `curl -C -` 断点续传，国内拉不动可以走代理或镜像。

**第三步：CLI 试跑**

```bash
./ds4 -p "用三句话讲清楚 Redis Streams 的核心设计"
# 直接进交互
./ds4
ds4> /nothink                   # 关思考模式
ds4> /ctx 100000                # 上下文调到 10 万
ds4> /read main.go              # 把整文件喂进去
```

CLI 默认开思考模式（V4 Flash 的「short thinking」特性，README 里 antirez 专门表扬过：思考长度跟问题复杂度成正比，简单问题不会无端生成 5000 token 的推理）。

**第四步：起服务器，挂到 Claude Code / opencode 上**

```bash
./ds4-server \
  --ctx 100000 \
  --kv-disk-dir /tmp/ds4-kv \
  --kv-disk-space-mb 8192
```

OpenAI 兼容端口默认 `127.0.0.1:8000`，opencode 在 `~/.config/opencode/opencode.json` 里加一个 provider 就能用：

```json
{
  "provider": {
    "ds4": {
      "options": {
        "baseURL": "http://127.0.0.1:8000/v1",
        "apiKey": "dsv4-local"
      },
      "models": {
        "deepseek-v4-flash": {
          "limit": { "context": 100000, "output": 384000 }
        }
      }
    }
  }
}
```

Claude Code 用户走 `/v1/messages` 端点，配置一行环境变量就能把 ds4-server 当 Anthropic API 使。这是国内本地大模型生态今年最有用的一项工程：开源模型 + 自家 IDE 的组合，不再需要任何中转。

![ds4.c Q2 在 M3 Max 128GB 与 M3 Ultra 512GB 的实测吞吐](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/antirez-ds4c-deepseek-v4-apple-silicon-2026-05-11/ds4c-throughput-bench.png)

## 四、antirez 这个人，跟这件事的份量

把人物放在最后讲，是因为这件事的全部份量来自一个事实：他不是随便一个开发者。

Salvatore Sanfilippo 1977 年生于西西里岛卡塔尼亚，2009 年单枪匹马写出 Redis，开源至今 GitHub 7.4 万 Star，是过去十五年事实上的内存数据库标准。他 2020 年宣布「不再当 Redis 项目主理人」、转去做艺术摄影和写书，2024 年回到 Redis Labs 当 evangelist。中间这几年除了 Redis，他陆续放出 Disque（消息队列）、smallchat（极简聊天室）、HNTop（HN 命令行客户端），全部用 C 单文件写、风格统一：「写少而精的代码，做一件事做到极致，简单协议对外暴露」。

Redis 当年赢的逻辑——拒绝通用关系数据库的复杂度，用最少的数据结构 + 最简单协议把内存存储这件事做透——这次原封不动搬到了推理引擎上：拒绝通用 GGUF 框架，专挑一个值得做的模型，把 MoE 路由 + 量化 + KV cache + Agent 协议这套链路全做透。

国内媒体（特别是 36氪 5/8 那篇）抓到了一句他 5/7 在 X 上的金句：

> Modern programming is becoming complex and uninteresting, all layers to glue together.

这句话翻译成中文，是「现在的软件越来越像层与层胶水的堆叠，没意思了」。这正是他这次重新动手写 ds4.c 的内在驱动——他想要的不是再造一个框架，是再做一次 Redis 那种「一个 C 文件一个二进制就解决问题」的工程。

但有一个反差点国内报道几乎没提：README 的项目原则段第二条是这么写的：

> This software is developed with strong assistance from GPT 5.5 and with humans leading the ideas, testing, and debugging. We say this openly because it shaped how the project was built. If you are not happy with AI-developed code, this software is not for you.

中文意思是：「这个项目是 GPT 5.5 强力辅助、人类主导思路 / 测试 / 调试写出来的。我们公开说这件事，是因为它从根上塑造了项目的写作方式。如果你不接受 AI 辅助开发，这个项目不适合你。」

写出 Redis 的人，把 50 年代以来 C 语言系统软件最稳的那一脉手感，跟 GPT 5.5 写代码的工程方式合在一起，给中国开源模型做出了一件作品。这件事比任何「AI 让程序员失业」的争论都更说明问题——大佬不是不用 AI，他是在跟 AI 协作做出别人做不出的东西。

更值得记的是另一组细节：他选 DeepSeek V4 Flash 不是看分数。README 明确写出选择理由——

> 它在 thinking 模式下的思考长度跟问题复杂度成正比，简单问题不会无端生成 5000 token 的推理；它的 KV cache 压缩比让长上下文真的能在本地跑；它写英语和意大利语都「像准前沿模型」。

意大利人写一个跑 1M 中文 / 英文 / 意大利文上下文的国产模型推理引擎，落地点是「让我自己的笔记本能 24 小时跑得起一个准前沿水平的助手」。这个落点，跟国内开发者每天面对的「想本地跑 DeepSeek 但买不起 H100 集群」是同一个落点。

## 五、对国内本地大模型生态的影响

ds4.c 出现之前，国内 Mac 用户跑 DeepSeek V4 Flash 主要靠两条路：MLX-LM（苹果官方机器学习框架）和 llama.cpp + Metal（开源社区主线）。两条路各有问题：MLX-LM 通用但 V4 Flash 还在 PR 阶段；llama.cpp 主线还把 V4 Flash 标实验性，antirez 自己 fork 的实验分支也没合并回主线。

ds4.c 给出第三条路——单点深度专属。三件事会跟着发生变化：

**第一，「专属引擎」会变成国产顶级模型的标配。** DeepSeek、Qwen、智谱、MiniMax 这一档的模型，未来很可能各自有人写专属推理引擎，而不只是依赖 llama.cpp 通用支持。这意味着同一张 Mac 显卡上，国产模型本地体验会被拉平甚至反超闭源 API。

**第二，128GB 配置会成为本地 Agent 开发主力机型。** 在 ds4.c 之前，128GB Mac Pro 跑得起的 MoE 上限是 100B 总参；现在 284B 也能跑了。对国内 AI Coding 同行最直接的影响：买不起 256GB Mac Studio 的开发者，第一次有了在自己机器上 24 小时挂 Claude Code 等级 Coding Agent 的物理可能。

**第三，国产模型的国际叙事换了主语。** 过去叙事是「DeepSeek 模型很强，海外社区在帮我们做适配」；这次反过来——海外开源大佬主动把自己最贵的工程注意力，押到国产顶级开源模型上。这种主语切换的份量，比任何榜单都更说明国产开源模型已经站到一档。

## 六、现在该读什么、该试什么

如果你是 M3 / M4 系列 128GB 以上的 Mac 用户，这周值得做两件事：

第一件，把 `antirez/ds4` clone 下来跑一遍。81GB 模型下载需要几个小时，但 `make` 一次就过、CLI 直接交互；体验过单文件 C + Metal 的本地推理，再回头看任何「框架抽象层」的推理引擎都会有不一样的判断。

第二件，把 README 完整读一遍。这是一份难得的、由顶级系统软件作者亲笔写的现代推理引擎设计说明书。MoE 非对称量化的工程逻辑、KV cache 持久化的协议设计、tool call 字节精确回放的实现细节——每一段都是把一件事做透的范本。

国产顶级开源模型第一次有海外大佬反向给写底层引擎，这件事留下的痕迹不是 5950 个 Star 数字本身，是它公开演示了一种全新的国际协作姿态：模型由中国出，引擎由意大利写，Apple Silicon 跑，全球开发者本地用。开源这条路上，主语正在重新分配。

## 引用与延伸阅读

- antirez/ds4 仓库（含 README + benchmark）：直接 `git clone` 即可；5/10 实查 5950 Star
- HuggingFace `deepseek-ai/DeepSeek-V4-Flash` 模型卡（284B / 13B / 1M ctx 实查）
- HuggingFace `antirez/deepseek-v4-gguf`（antirez 自家非对称量化模型权重）
- 36氪 2026-05-08《Redis 之父下场，给 DeepSeek V4 单独造了一台推理引擎》（中文头条解读）
- 量子位 / ITBear / 搜狐 / Linux.do 5/9–5/10 多家中文报道
- llama.cpp 主线 Discussion #4167（DeepSeek V4 Flash 适配状态追踪）
- MLX 社区 Ivan Fioravanti（@ivanfioravanti）5/8 起公开的 V4 Flash on M3 Ultra 测评推

国内 AI Coding 同行手里有这台机器、想试一试的，欢迎在群里发 token/s 实测。把同一份 prompt 的 ds4.c / MLX-LM / llama.cpp 三家成绩贴在一起，是这周本地大模型圈最有意思的实操题。
