---
title: LLM 推理缓存实战：5 类缓存 + 7 个开源项目 + 4 个国内场景，把 token 账单砍 9 成
slug: llm-inference-caching-guide-2026-05-08
date: 2026-05-08
weekday: 星期五
cover: llm-inference-caching-guide-2026-05-08.png
tags:
  - LLM 推理
  - 缓存
  - vLLM
  - SGLang
  - Claude Code
  - DeepSeek
  - 千问
  - GPTCache
summary: 5 月 8 日打开 ML Mastery 头条这篇 inference caching guide，作者把推理缓存收成 KV / Prefix / Semantic 三层。我们把它扩成 5 类缓存 + 7 个开源项目 + 4 个国内开发者落地场景的速查手册——Anthropic / OpenAI / DeepSeek / 千问的 prompt cache 怎么开、vLLM v1 prefix cache 一行参数怎么打、GPTCache 67% 命中率怎么调阈值、Claude Code 用户怎么吃满 cache_control 的 0.1× 折扣，全部给到可复制配置和实测数字。
---

# LLM 推理缓存实战：5 类缓存 + 7 个开源项目 + 4 个国内场景，把 token 账单砍 9 成

![LLM 推理缓存实战封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/llm-inference-caching-guide-2026-05-08/llm-inference-caching-guide-2026-05-08.png)

5 月 8 日打开 [Machine Learning Mastery 今天的头条](https://machinelearningmastery.com/the-complete-guide-to-inference-caching-in-llms/)，标题是《The Complete Guide to Inference Caching in LLMs》。作者把整个推理缓存世界收成三层：KV cache、prefix cache、semantic cache。读完很顺，但作为国内开发者我们得多走两步——加上 exact-match 这一层、加上厂商内置 prompt cache 这一层、把每一层落到 Anthropic / OpenAI / DeepSeek / 千问的真实开通方式上、再把 vLLM / SGLang / GPTCache / LiteLLM / LangChain / llama.cpp / Langfuse 这 7 个开源实现的 star 数和最新更新查清楚。

**这篇文章只想回答一件事**：作为一个每天用 Claude Code、Cursor、Cline 写代码，本地跑过千问 / DeepSeek、自建过 RAG 的中国 AI 开发者，怎么把推理缓存这一套系统性地装到自己的项目里，把 token 账单砍 5 到 9 成、把 TTFT 压到一半以下，同时不踩"缓存把答案污染了"那种坑。

下面是 5 类缓存的全景图，我们按"从模型内部到应用最外层"的顺序铺开。

![LLM 推理缓存五层全景对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/llm-inference-caching-guide-2026-05-08/llm-inference-caching-overview-table.png)

## 一、把 5 类缓存按层拆清楚，先理解再选型

**核心论断**：推理缓存不是"二选一"，是"五层叠加"——KV cache 永远在跑，prefix cache 是 ROI 最高的一层，semantic cache 在高重复率场景再加一刀，exact-match 兜底应付完全相同的 query，厂商内置的 prompt cache 帮你把网关层的工作量省下来。

ML Mastery 原文把推理缓存压成三层：KV / prefix / semantic。但实战里至少要再补两层才完整：

- **Exact-match cache**：完整 prompt+参数 hash 命中，应用层最便宜的一招；典型命中率 10-25%，命中后近乎 0 模型耗时；代表实现 Redis / LangChain InMemoryCache / SQLAlchemyCache
- **KV cache**：单请求内部 attention K/V 内存复用；对所有现代推理引擎都是默认行为，每个 decode step 节省约一半算力；vLLM、SGLang、llama.cpp 都默认开
- **Prefix cache**：跨请求共享前缀的 KV 复用；RAG / Agent / 多轮对话场景命中率 70-95%，TTFT 降 50-90%；vLLM v1 默认开、SGLang 用 RadixCache、llama.cpp 用 `--prompt-cache`
- **Semantic cache**：向量相似度匹配整段答案；FAQ / 客服 / 知识库类应用 50-70% 命中率，命中后**完全跳过模型调用**；代表 GPTCache、Redis Vector、pgvector
- **厂商内置 Prompt Cache**：API 端做的"自动 / 半自动 prefix cache"；Anthropic、OpenAI、DeepSeek、千问全部上线，对长 prompt 场景输入价格直接打 1 折

每一层的成本回收路径不一样：KV cache 回收的是显存，prefix cache 回收的是计算，semantic cache 回收的是 API 调用本身，exact-match 兜底的是重复请求，厂商内置 prompt cache 把后两层的工程化成本转移给厂商。

**这一节的判断**：先把 KV + prefix 这两层吃满，再决定要不要往应用层加 semantic / exact-match。先压重头戏，再补长尾。

## 二、Prefix cache：vLLM v1 默认开 + Anthropic 1 折读 token

**核心论断**：prefix cache 是这一波缓存里 ROI 最高的一层——尤其当你的应用满足"长系统提示 + 短动态输入"这一条结构时。vLLM v1 已经把它做成默认行为，Anthropic 做成 cache_control 显式标记，DeepSeek 做成 disk-level 透明命中，三种姿势都能拿到 5 倍以上的成本降幅。

![主流缓存开源/商业实现速查表](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/llm-inference-caching-guide-2026-05-08/llm-inference-caching-repo-table.png)

vLLM 这一侧给的数字相当扎实。[vLLM 官方文档](https://docs.vllm.ai/en/stable/design/prefix_caching/)和 [llm-d 团队的 KV-Cache 博客](https://llm-d.ai/blog/kvcache-wins-you-can-see)一致显示：在共享前缀比例从 0.1 拉到 0.9 的合成基准下，vLLM 吞吐提升 32%、TensorRT-LLM 提升 49%；分布式调度配合 prefix cache 时，特定场景能拿到 57 倍的响应时间提升和双倍吞吐。更关键的一条来自 vLLM v1 的发布说明——**v1 的 prefix cache 在 0% 命中率下吞吐损失不到 1%，于是直接做成默认开启**。

国内开发者最常见的本地部署方式：

```bash
# vLLM v1 启动千问 3.6 27B（24GB 显存可跑 4-bit 量化版）
# v1 的 prefix cache 是默认开启的，下面这条命令开箱就吃 prefix cache
vllm serve Qwen/Qwen3.6-27B-Instruct-AWQ \
  --max-model-len 16384 \
  --gpu-memory-utilization 0.92 \
  --enforce-eager  # 仅调试期；正式跑去掉

# SGLang 启动 DeepSeek-V4 蒸馏版，显式启用 RadixCache
python -m sglang.launch_server \
  --model-path deepseek-ai/DeepSeek-V4-Distill-Qwen-32B \
  --port 30000 \
  --enable-radix-cache  # SGLang 的 prefix cache 实现，默认开
```

**踩坑提醒**：prefix cache 命中的硬条件是"前缀逐字节相同"。你的系统提示里如果用了 `f"当前时间是 {datetime.now()}"` 这种动态字段，命中率会被打到 0。把所有动态变量挪到 prompt 末尾，前缀做成纯静态文本，是吃满 prefix cache 的第一条工程纪律。

更细一层的纪律是**JSON 序列化要稳定**：Python 的 `json.dumps()` 默认是无序键序列化，同样一个 dict 多次序列化可能产生不同的 byte 顺序，等于把前缀 cache miss 掉。生产环境固定 `json.dumps(obj, sort_keys=True, ensure_ascii=False, separators=(",", ":"))` 这一组参数，几乎是 prefix cache 的隐藏护城河——这条不写进文档但每个吃过亏的工程师都会反复强调。

Anthropic 这一侧的玩法不一样——不是"自动命中"，是"你显式标记一段做 cache breakpoint"。[Claude API 文档](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)给的定价口径很清楚：5 分钟 cache 写是基础输入价的 1.25×，1 小时 cache 写是 2×，cache 读统一 0.1×。换算下来：5 分钟 cache 第 1 次读就回本，1 小时 cache 第 2 次读回本。**这套定价的含义是只要复用一次以上，cache_control 永远赚。**

```python
# Anthropic prompt caching 最小可用片段（Python SDK）
import anthropic

client = anthropic.Anthropic()

resp = client.messages.create(
    model="claude-sonnet-4-7-20260201",
    max_tokens=1024,
    system=[
        {
            "type": "text",
            "text": LONG_SYSTEM_PROMPT,  # 比如 5000 token 的工程规范
            "cache_control": {"type": "ephemeral", "ttl": "1h"},  # 1 小时 cache，2× 写、0.1× 读
        }
    ],
    messages=[
        {"role": "user", "content": user_question},
    ],
)
print(resp.usage)  # 看 cache_creation_input_tokens / cache_read_input_tokens
```

**踩坑提醒**：`cache_control` 必须放在你想 cache 那一段的"末尾"——它是一个 breakpoint，意思是"截止到这里的 prompt 都进 cache"。一份请求最多能放 4 个 cache_control breakpoint，复杂 agent prompt 可以分段缓存（系统提示 + 工具定义 + 文档上下文 + few-shot 示例各一个）。

DeepSeek 这一侧最舒服——**完全自动**。[DeepSeek API 文档](https://api-docs.deepseek.com/guides/kv_cache)说得直接：context caching 默认开启，请求和最近请求共享前缀就从分布式磁盘自动命中，不用改代码、不用声明 cache key、不用设 TTL。2026-04-26 起命中价砍到 cache miss 的 1/10，V4 Pro 命中价 $0.015/M tokens、V4 Flash $0.003/M tokens。**这条线对国内开发者意义最大——什么都不做就能拿到 9 折。**

**这一节的判断**：自建推理跑 vLLM v1 / SGLang，prefix cache 是"开了它别动"的事；走 DeepSeek API 是"什么都不用做"；走 Claude API 是"花 30 行代码挂 cache_control"。三种姿势都能在长 prompt 场景拿到 5 倍以上的成本降幅，没有不开的理由。

## 三、Semantic cache：GPTCache 67% 命中率怎么调出来

**核心论断**：semantic cache 不是"装上就有效"，是"装上 + 调阈值 + 防误命中"三件事一起做才有效。GPTCache 论文和生产复盘给的命中率区间是 50-70%，**但前提是查询本身有语义重复结构**——FAQ / 客服 / 知识库可以，创意生成不行。

GPTCache 这个项目本身的背景：[zilliztech/GPTCache](https://github.com/zilliztech/GPTCache) 8020 星，最近一次 commit 在 2025-07-11，社区维护节奏明显放缓——但作为最经典的 semantic cache 参考实现，它的代码结构、阈值调优经验仍然是这条路的最好教材。论文 *GPT Semantic Cache: Reducing LLM Costs and Latency via Semantic Embedding Caching*（[arXiv:2411.05276](https://arxiv.org/abs/2411.05276)）给的数字是：跨多种 query 类别，cache hit rate 在 61.6% 到 68.8% 之间，最高把 API 调用减少 68.8%。

最小可用配置：

```python
# GPTCache + LangChain ChatOpenAI 接 DeepSeek API（OpenAI 兼容端点）
from gptcache import cache
from gptcache.adapter.langchain_models import LangChainChat
from gptcache.embedding import Onnx
from gptcache.manager import CacheBase, VectorBase, get_data_manager
from gptcache.similarity_evaluation.distance import SearchDistanceEvaluation
from langchain.chat_models import ChatOpenAI

onnx = Onnx()  # 默认 paraphrase-mpnet-base-v2，384 维
data_manager = get_data_manager(
    CacheBase("sqlite"),
    VectorBase("chromadb", dimension=onnx.dimension),
)
cache.init(
    embedding_func=onnx.to_embeddings,
    data_manager=data_manager,
    similarity_evaluation=SearchDistanceEvaluation(),
)
cache.set_openai_key()  # 兼容层会自动接走 OPENAI_API_KEY

llm = LangChainChat(chat=ChatOpenAI(
    model="deepseek-chat",
    base_url="https://api.deepseek.com/v1",
))
```

**踩坑提醒**：默认相似度阈值偏松，FAQ 场景把阈值从默认 0.7 调到 0.85 是常规操作；评估方式优先选 `SearchDistanceEvaluation`（cosine 距离）+ 二次 LLM 校验（让小模型判一句"语义等价吗"），把 false-positive 命中率压到 3% 以下。GPT Semantic Cache 论文报告的 positive hit rate（确实命中等价 query 的比例）超过 97%，前提就是阈值 + 二次校验都做到位。

LiteLLM 这条网关路线也值得讲——[BerriAI/litellm](https://github.com/BerriAI/litellm) 46125 星、2026-05-08 当天还在更新，是国内开发者把 Cursor / Cline / Aider 等多个 IDE 收到统一缓存层的最干净路径：

```python
# LiteLLM Proxy 配置 semantic cache（config.yaml 片段）
litellm_settings:
  cache: True
  cache_params:
    type: redis-semantic
    similarity_threshold: 0.85
    redis_url: redis://localhost:6379
    ttl: 3600
    embedding_model: text-embedding-3-small
```

启动 `litellm --config config.yaml --port 4000`，然后让 Cursor / Cline 把 base URL 指到 `http://localhost:4000`。所有经过这条网关的请求都会先做 semantic 匹配；命中直接返回，不走模型；未命中转发到上游。这是把 semantic cache 从"嵌进每个应用"变成"嵌进网关一次性吃满"的最低成本姿势。

LangChain 在缓存这件事上的角色介于 GPTCache 和 LiteLLM 之间：[langchain-ai/langchain](https://github.com/langchain-ai/langchain) 136112 星，把 SQLAlchemyCache / RedisCache / GPTCache / RedisSemanticCache 全封进 `set_llm_cache(...)` 一行调用。**RAG agent 类项目直接挂 RedisSemanticCache 是 5 行代码起步的最省事写法。**

观测层这一支也值得专门讲一句。Langfuse 26803 星、2026-05-08 当天还在 push commit，是国内开发者做 LLM 应用线上监控时最常见的开源选择之一；它本身做的是 trace + 评估，但 v3.x 起内置了 prompt 复用统计，可以直接告诉你"哪些 prompt 在过去 7 天被请求 N 次、它们的实际命中率分布是怎样的"。把 Langfuse 装上当 cache 选型的"侦察兵"用，比凭直觉决定要不要加 semantic cache 靠谱得多。

**这一节的判断**：semantic cache 的回报曲线是"前 30% 命中率最容易拿，后 30% 越来越难"。把目标锁定在 50% 命中、阈值 0.85、TTL 1 小时这一组保守参数上，先跑两周看真实命中分布再调，比一上来追求 70% 命中更靠谱。

## 四、四个国内开发者落地场景，照着抄就能跑

**核心论断**：缓存这件事最容易卡在"知道有用但不知道我自己的项目从哪开"。下面 4 个场景按"今天就能动手 → 一周内能见效 → 一个月内能压成本"排序，每个场景给可复制配置 + 一句踩坑提醒。

![国内 + 海外 LLM 厂商 Prompt Cache 一览](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/llm-inference-caching-guide-2026-05-08/llm-inference-caching-vendor-table.png)

### 场景 1：Claude Code 用户开 Anthropic prompt caching

如果你已经在用 Claude Code 跑 agent 流程，最直接的一步是把工具定义 + 大段系统提示挂上 cache_control。Claude Code 内部对 cache_control 有自动支持，但很多用户没意识到自己写的 sub-agent skill 里那个 7000 字的指令本身就是 cache 候选。

```python
# 自定义 sub-agent 调用 Anthropic SDK 时主动挂 cache_control
messages = [
    {
        "role": "system",
        "content": [
            {"type": "text", "text": SKILL_INSTRUCTIONS},  # 7000 token 的 skill 规范
            {"type": "text", "text": TOOL_SCHEMAS, "cache_control": {"type": "ephemeral", "ttl": "1h"}},
        ],
    },
    {"role": "user", "content": current_task},
]
```

**踩坑提醒**：5 分钟 vs 1 小时 cache 怎么选？rule of thumb——agent 内部循环（每次都几秒内打回来）用 5 分钟省成本，需要跨用户共享或者人在思考的场景用 1 小时省 cache miss 重写。

### 场景 2：Cursor / Cline / Aider 套 LiteLLM gateway 拿统一 cache

Cursor 自己做了 prompt caching，但 Cline 和 Aider 默认没做应用级 cache。在三者前面架一个 LiteLLM Proxy，配 redis-semantic 和上游 prompt caching，等于把"应用级 semantic cache + 厂商级 prompt cache"两层一起吃满。

```yaml
# litellm config.yaml — 把 Claude / DeepSeek / 千问 收成统一端点 + 双层缓存
model_list:
  - model_name: claude-sonnet
    litellm_params:
      model: anthropic/claude-sonnet-4-7-20260201
      cache_control_injection_points:
        - location: message
          role: system  # 每次自动给 system 挂 cache_control，无需改 IDE 配置
  - model_name: deepseek-chat
    litellm_params:
      model: deepseek/deepseek-chat
litellm_settings:
  cache: True
  cache_params:
    type: redis-semantic
    similarity_threshold: 0.92  # IDE 上下文敏感，阈值要严
    ttl: 1800
```

**踩坑提醒**：IDE 类应用对"同一问题不同上下文"的辨识极敏感，semantic 阈值不要低于 0.9，否则会把 A 文件的代码答案错配给 B 文件。

### 场景 3：本地千问 / DeepSeek 跑 vLLM / SGLang 吃 prefix cache

这是国内开发者最容易拿到收益的一条线——不花一分 API 费、不出本机、纯吃工程优化。一台 24GB 显存的 RTX 3090 / 4090 跑千问 27B AWQ 量化版，配 vLLM v1 默认 prefix cache，长 RAG 场景的 TTFT 能从 8 秒压到 1 秒以内。

```bash
# vLLM v1 + 千问 3.6 27B 4-bit 量化 + 默认 prefix cache
docker run --gpus all -p 8000:8000 \
  -v ~/.cache/huggingface:/root/.cache/huggingface \
  vllm/vllm-openai:latest \
  --model Qwen/Qwen3.6-27B-Instruct-AWQ \
  --max-model-len 32768 \
  --enable-prefix-caching  # v1 默认开，写出来更安心

# 验证 prefix cache 命中：连续两次相同前缀的请求，第二次 TTFT 应降至原来 1/5 以内
curl http://localhost:8000/v1/completions -d '{
  "model": "Qwen/Qwen3.6-27B-Instruct-AWQ",
  "prompt": "你是一个资深 Rust 工程师，下面是你正在 review 的代码……",
  "max_tokens": 256
}'
```

![缓存命中实测：四个真实场景的成本与延迟变化](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/llm-inference-caching-guide-2026-05-08/llm-inference-caching-real-numbers.png)

**踩坑提醒**：本地跑的好处之一是 prefix cache 命中数据可以直接观测——访问 `http://localhost:8000/metrics` 能看到 `vllm:gpu_prefix_cache_hit_rate` 这个 gauge，调一周看真实分布再决定要不要扩显存。

### 场景 3.5：本地 llama.cpp 跑量化模型 + 持久化 prompt cache

这是给国内开发者里硬件预算最紧的那批人留的。一台 16GB 内存的 Mac 或者 12GB 显存的 RTX 3060 跑 llama.cpp 加载千问 4-bit 量化，服务个人 + 小团队完全够用——而且 llama.cpp 有一个 vLLM 没有的特性：**prompt cache 可以持久化到磁盘**。

```bash
# llama.cpp 启动 + 把 7000 token 的系统提示预热进磁盘 cache
./llama-server -m qwen3.6-27b-instruct-q4_k_m.gguf \
  --port 8080 \
  --prompt-cache /var/cache/llama/system-prompt.cache \
  --prompt-cache-all \
  -p "$(cat system_prompt.txt)" \
  -n 1  # 只生成 1 token，触发 cache 写入

# 后续请求只要带相同系统前缀，从磁盘 cache 秒级加载，绕过整段 prefill
```

**踩坑提醒**：llama.cpp 的 prompt cache 是按文件路径持久化的，部署多副本时每个进程独立维护一份；NFS 共享会有锁冲突，多副本场景换 vLLM 更省心。

### 场景 4：自建 RAG / agent 套 GPTCache + Milvus / Chroma

垂类 RAG（医疗 / 法律 / 客服）类应用是 semantic cache 的甜蜜点——同一类问题不同人会用 30 种问法，但答案池高度收敛。GPTCache + Milvus（云）或 ChromaDB（单机）是最经典的搭配。

```python
# GPTCache + ChromaDB 单机版（国产 RAG 项目最低成本起步姿势）
from gptcache import cache
from gptcache.embedding import Huggingface
from gptcache.manager import CacheBase, VectorBase, get_data_manager
from gptcache.similarity_evaluation.distance import SearchDistanceEvaluation

embed = Huggingface(model="BAAI/bge-small-zh-v1.5")  # 中文场景必换中文 embedding
data_manager = get_data_manager(
    CacheBase("sqlite"),
    VectorBase("chromadb", dimension=embed.dimension, collection_name="rag_cache"),
)
cache.init(
    pre_embedding_func=lambda data, **_: data["messages"][-1]["content"],  # 只对最后一条 user message 做 embed
    embedding_func=embed.to_embeddings,
    data_manager=data_manager,
    similarity_evaluation=SearchDistanceEvaluation(max_distance=0.15),  # cosine 距离阈值
)
```

**踩坑提醒**：中文 embedding 一定要换成 `bge-small-zh-v1.5` 或 `text2vec-base-chinese`，不然默认英文模型在中文 query 上的相似度判断会非常飘——这是 GPTCache 在中文场景下命中率上不去的最常见原因。

### 场景 5：智谱 / 文心快码 / 通义灵码这类国内 IDE 内置缓存

国内的 AI Coding IDE 这一年陆续上线了内置缓存层。通义灵码、文心快码、CodeGeeX 这几个主流国产工具，对话主线默认走自家 API，背后的缓存机制由厂商决定——开发者侧的事就是把"对话保留 / 重置"按钮玩明白：每次重置等于强制清掉前缀 cache，长会话保留则是吃满 prefix cache 的最佳姿势。

智谱 GLM-4.6 的 context cache 走显式 create_cache 接口，需要先调用 `create_cache(model, content, ttl)` 拿到 `cache_id`，后续请求把 `cache_id` 塞进 system 字段引用。这个设计的好处是 cache 生命周期由调用方控制，不会因为厂商侧自动逐出策略丢失；坏处是必须改代码、不像 DeepSeek 那么透明。**国内开发者选型口径**：图省事走 DeepSeek，要可控走智谱，要 5 类都吃满走自建 LiteLLM + 千问 Dashscope cache_control 标记。

## 五、命中率 / 成本节省的真实数据：把每一笔账算清楚

**核心论断**：缓存最迷人的不是"听起来便宜"，是"账面真的便宜"。把 4 个独立来源的实测数字拉成一张表，能看出每一层的回收路径。

来自 [vLLM blog](https://llm-d.ai/blog/kvcache-wins-you-can-see) 的合成基准：共享前缀比例从 0.1 拉到 0.9，vLLM 吞吐 +32%、TensorRT-LLM +49%。**这是引擎层不写一行业务代码就能拿到的纯工程收益。**

来自 [OpenAI 官方公告](https://openai.com/index/api-prompt-caching/) 的实测：1024 token 的 prompt 命中后请求快 7%，150k token 长 prompt 命中后 TTFT 快 67%；输入价格直接打 5 折。这是网关层的真实数字。

来自 [DeepSeek 8 月公告](https://api-docs.deepseek.com/news/news0802) 的实测：cache hit 输入价是 cache miss 的 1/10，2026-04-26 起 V4 全系再降一档。这是 API 层的纯透明收益。

来自 GPT Semantic Cache 论文（[arXiv:2411.05276](https://arxiv.org/abs/2411.05276)）的多场景实测：cache hit rate 61.6%-68.8%，positive hit rate（命中正确）超过 97%。这是应用层的端到端数字。

把 4 组数字翻译成一句判断：**RAG 长文档场景的输入 token 账单能砍到原来的 10%，多轮 agent 场景的 TTFT 能砍到原来的 1/3，FAQ 场景的总账（含调用次数）能砍 40-60%——三件事一起做，整体推理预算压一半到一个数量级是常态。**

更细一点的成本结构是这样的：假设你有一个日 100 万次调用的 chatbot，平均每次输入 5000 tokens、输出 500 tokens，未优化时每天烧 $1500（按 DeepSeek V4 Pro 价格估算）。开 prefix cache 后输入侧打 1 折，每天降到约 $300；再叠 semantic cache 60% 命中率，每天进一步降到约 $120——**单这一项工程优化每年省下 $50 万**。这条账给国内 AI 创业团队的意义在于：你不需要等模型再降价，缓存这一刀比降价更先到、且收益更稳定。

![5 层缓存决策流：请求进来怎么过](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/llm-inference-caching-guide-2026-05-08/llm-inference-caching-decision-flow.png)

## 六、什么时候不要用缓存：3 类场景请避开

**核心论断**：缓存不是免费午餐——有 3 类场景开了缓存反而是负优化，工程上必须事先识别清楚。

**第一类是创意生成 / 高温度采样场景**——温度 ≥0.7、top-p 0.9 这种参数下，每次输出本来就要不一样，semantic cache 命中等价于"用户拿到的是别人的答案"。这种场景关掉应用层缓存、只保留 prefix cache 就行（因为 prefix cache 缓存的是 K/V 状态，不影响采样多样性）。

**第二类是多轮对话上下文敏感场景**——用户在对话第 5 轮问的"这段代码怎么改"和第 50 轮问的"这段代码怎么改"指向完全不同的代码上下文。semantic cache 看不出来这种"语义相同但上下文不同"，必须把对话 ID / 用户 ID / session ID 进 cache key，或者干脆只在 stateless 端点上开 cache。

**第三类是合规 / 安全要求 audit log 的场景**——金融 / 医疗 / 政务类应用要求每一次模型调用都有完整审计链路，semantic cache 命中跳过模型这件事会让审计日志缺失。这类场景通常只允许开 prefix cache（不影响 audit）+ 厂商内置 prompt cache（厂商侧自己有日志），应用层 semantic cache 默认关闭。

**还有一种"半禁用"场景值得提一笔**——多租户 SaaS 应用。如果 cache key 不加 tenant_id 隔离，A 租户的问题可能命中到 B 租户的答案，这是合规事故。多租户场景的 cache key 一律是 `f"{tenant_id}::{user_id}::{semantic_hash}"` 三层组合，少一层都不行——这一条是 GPTCache 论文专门讨论过的"跨租户漏切"问题，工程上靠 cache key 命名规范兜底。

知乎和 r/LocalLLaMA 上反复出现的实战教训汇总成一句：缓存的边界是"哪里有确定性，哪里就能缓存"。**有确定性 → 开；半确定性 → 设阈值；无确定性 → 别开**。

## 六半、cache 失效 / 污染的几个真实坑

工程上吃过亏的开发者会列出几个 cache 失效场景：

**第一是模型版本切换导致的隐性失效**。`claude-sonnet-4-7-20260201` 和 `claude-sonnet-4-7-20260301` 表面看是同一系列，但前缀 cache 的 hash key 包含了 model 名，切版本等于全部 cache miss。生产环境换模型版本要预留一段"冷启动期"重新预热 cache，否则首日成本会瞬间翻倍。

**第二是 system prompt 微调导致的雪崩**。运营同事在系统提示里加一个标点、改一个字，整套 prefix cache 就全部 miss——这种"无感修改"在快速迭代的产品里非常常见。建议把 system prompt 走配置中心管理，每次变更走 PR review，明确标注"会触发 cache 重建"。

**第三是 semantic cache 的"近似漂移"**。同一类问题用了 3 个月以后，用户的问法会慢慢漂移；初期阈值 0.85 命中率 65%，3 个月后降到 45%。定期跑 cache 命中率分布分析、按月调阈值或者重训 embedding，是 semantic cache 长期健康度的关键。

## 七、国内开发者额外要注意的几件事

**核心论断**：把上面这套搬到国内环境，有 4 件事得提前看一眼，不然会被基础设施细节卡半天。

- **国内代理 / 中转 API 是否透传 prompt-caching header**：Anthropic / OpenAI 的 cache_control 是 HTTP header + JSON 字段双层走的，部分国内中转方案为了简化 schema 只透传顶层字段、丢掉了 cache_control。**实测办法**：发一笔带 cache_control 的请求，看返回的 `usage.cache_creation_input_tokens` 是不是 > 0；如果是 0，cache 没透传，找你的中转商问
- **千问 / DeepSeek 国内 API 的 prompt cache 现状**（截至 2026-05-08）：DeepSeek 全系列 V4 默认开 disk cache 不用改代码；千问 Dashscope 通过 cache_control 标记开启最多 4 个 cache 块，cache 写 1.25× / 读 0.1× / 最小命中长度 1024 tokens；智谱 GLM-4.6 提供显式 context cache 接口，需要先 create_cache 拿到 cache_id 再传
- **数据出境合规边界**：如果 cache key 包含敏感数据（用户姓名 / 手机号 / 病历号），semantic cache 的 embedding 落在境外向量库（Pinecone / Weaviate Cloud）等于敏感信息出境。国内场景优先选 Milvus 自托管 / 阿里云 OpenSearch 向量版 / 腾讯云 VectorDB，向量库不出境
- **Claude Code 国内可用方案的 cache 状态**：直连 Anthropic 走 cache_control 完全可用；通过国内代理（智谱 BigModel 镜像 / OpenRouter / 各 Cursor 中转）要分别 case-by-case 测——OpenRouter 文档明确说 [它会透传 prompt caching](https://openrouter.ai/docs/guides/best-practices/prompt-caching)，国内中转商建议在文档里直接搜"prompt cache 透传"四个字确认

**这一节的判断**：把这 4 件事当成上线 checklist 走一遍，可以避开"以为开了 cache、实际全是 cache miss"那种最痛的坑。

## 八、把推理缓存装进 2026 年的国内 AI 工程

回到开头那句核心论断：**5 类缓存 + 7 个开源项目 + 4 个国内开发者场景，组装出来就是一套属于国内 AI 开发者的成本曲线压缩工具。**

这套工具的工程纪律可以收成 5 句：

- KV cache 是免费的，选对推理引擎就行
- prefix cache 是 ROI 最高的一层，把动态变量挪到 prompt 末尾是吃满它的第一条规则
- semantic cache 不是"装上就有效"，是"装上 + 调阈值 + 防误命中"三件事一起做才有效
- 厂商内置 prompt cache 已经把工程化成本转移给厂商了，能用就用、能省就省
- 缓存的边界是"哪里有确定性，哪里就能缓存"

国内 AI 开发者过去两年最大的红利是模型本身——千问、DeepSeek、智谱把可用性顶起来了。下一程的红利会从"模型能用"切到"模型用得起"——推理成本、TTFT、并发上限三件事谁先压下来，谁的产品就能在 C 端跑出更高的留存。

把这套缓存工具装上，2026 年的 AI 应用账单会比 2025 年好看很多。一个具体的实施节奏建议按 4 周拆解：

![国内开发者 30 天落地缓存路线图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/llm-inference-caching-guide-2026-05-08/llm-inference-caching-rollout-plan.png)

- **第 1 周**：开 vLLM v1 prefix cache 和 DeepSeek 自动 cache，吃免费午餐
- **第 2 周**：给 Claude / 千问 API 挂 cache_control，把长 prompt 输入价砍 1 折
- **第 3 周**：给 RAG / FAQ 接 GPTCache 或 LiteLLM gateway，看真实命中率分布
- **第 4 周**：做"什么时候不该开 cache"的 audit，把高温度采样和合规链路单独切出去

一个月走完，后面只需要按月调阈值就能持续受益。

我们这一代赶上了一个国产模型先出来、又一起做工程优化的好窗口期，路在前面，慢慢加油。

---

**参考资料**

- Machine Learning Mastery：[The Complete Guide to Inference Caching in LLMs](https://machinelearningmastery.com/the-complete-guide-to-inference-caching-in-llms/)
- vLLM 官方文档：[Automatic Prefix Caching](https://docs.vllm.ai/en/stable/design/prefix_caching/)
- llm-d 团队博客：[KV-Cache Wins You Can See](https://llm-d.ai/blog/kvcache-wins-you-can-see)
- Anthropic 官方文档：[Prompt caching](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)
- OpenAI 官方公告：[Prompt Caching in the API](https://openai.com/index/api-prompt-caching/)
- DeepSeek API 文档：[Context Caching](https://api-docs.deepseek.com/guides/kv_cache)
- 阿里云 Model Studio：[Context Cache feature for Qwen models](https://www.alibabacloud.com/help/en/model-studio/context-cache)
- GPT Semantic Cache 论文：[arXiv:2411.05276](https://arxiv.org/abs/2411.05276)
- GPTCache 仓库：[zilliztech/GPTCache](https://github.com/zilliztech/GPTCache)
- LiteLLM 仓库：[BerriAI/litellm](https://github.com/BerriAI/litellm)
- SGLang 仓库：[sgl-project/sglang](https://github.com/sgl-project/sglang)
- llama.cpp 仓库：[ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)
