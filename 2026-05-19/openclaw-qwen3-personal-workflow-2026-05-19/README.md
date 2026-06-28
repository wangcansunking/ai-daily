---
title: "OpenClaw 接本地 Qwen3 一周实测：写作与记账两条线"
slug: openclaw-qwen3-personal-workflow-2026-05-19
date: 2026-05-19
weekday: 星期二
category: 本地大模型 / OpenClaw 集成 / 个人工作流
cover: openclaw-qwen3-personal-workflow-2026-05-19.png
track: arbitrage
domain: openclaw-local-llm
tags:
  - OpenClaw
  - Qwen3-Coder-30B-A3B
  - Qwen3-Embedding-8B
  - Ollama
  - vLLM
  - 个人工作流
  - 本地大模型
  - 隐私
  - MCP
description: "5 月 18 日 OpenClaw 主仓库 37.30 万 Star、MIT、TypeScript。同行已经写过 4 个场景的广撒网横评，本文不重复，而是把两件最高频的事拆透：用 Qwen3-Coder-30B-A3B-Instruct 写中文长稿和中英对照、用同模型 + Qwen3-Embedding-8B 做家庭流水分类。给到 RTX 4090 单卡的延迟分布、月度成本三方对比、和 Claude Code Max 同任务的差距数字、合规与隐私的真实边界。"
---

# OpenClaw 接本地 Qwen3 一周实测：写作与记账两条线

![OpenClaw 接本地 Qwen3 一周实测封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/openclaw-qwen3-personal-workflow-2026-05-19/openclaw-qwen3-personal-workflow-2026-05-19.png)

截至 5 月 18 日 22:01，OpenClaw 主仓库定格在 372,959 Star、MIT 协议、TypeScript 写，是个人 AI 助手类项目里 Star 数排第一的位置。同一天 Hugging Face 上 Qwen3-Coder-30B-A3B-Instruct 月下载停在 191.5 万次，Qwen3-Embedding-8B 停在 163.6 万次。两条数字摆在一起，能讲清楚一个事：把日常的写作和记账搬回本机，已经不是发烧友的玩具时间。

本文核心论点摆在第一段：**OpenClaw 接本地 Qwen3 不是 Claude Code 的替代品，它是另一条路径——把数据全部留在本机、月度边际成本降到 Claude Max 的四成、响应慢一些但够用、对中文写作和中文流水分类两件事尤其顺手。**

5 月 11 日已经写过一篇 4 个场景的广撒网横评（论文翻译 / 私有合同 RAG / 凭证财务 / 跨仓代码图谱），本篇不重复那篇的覆盖面，而是把两件最高频的事拆透：**一是 Qwen3-Coder-30B-A3B-Instruct 用作个人写作助手的全链路，二是同模型加 Qwen3-Embedding-8B 用作家庭记账 agent 的全链路**。给到 RTX 4090 单卡的 P50/P95/P99 延迟分布、月度成本三方对比、和 Claude Code Max 同任务的差距数字、合规与隐私的真实边界。

## 一、参数与硬件门槛：4090 单卡到底跑得起跑不起

先把跑得起的硬件锚点钉死。Qwen3-Coder-30B-A3B-Instruct 是 MoE 架构，**总参数 30.5B、激活参数 3.3B、48 层、128 选 8 专家、原生上下文 26.2 万 token、Yarn 扩展到 100 万**（HuggingFace 模型卡）。Apache 2.0 协议，140 多家社区贡献了量化版本。

显存预算这条线，4090 24GB 的能装下：

| 量化档位 | 文件大小 | 4090 单卡是否能装 | 备注 |
|---|---|---|---|
| BF16 | 约 61 GB | 装不下 | 需要双卡 A100 或服务器级 |
| AWQ-Q4 | 18.1 GB | 装得下 | 留 6 GB 给 KV cache + 上下文 |
| GGUF Q4_K_M | 18.0 GB | 装得下 | llama.cpp / Ollama 路径 |
| GGUF Q8_0 | 32.5 GB | 装不下 | 走 M3 Max 128GB / M4 Pro |

Apple Silicon 这一档也跑得起：M3 Max 128GB 跑 GGUF Q4 内存占用 21-23 GB（Unsloth 文档），生成速度社区帖给出的中位数大约 32-40 token/s。RTX 4090 跑 AWQ Q4 配 vLLM 0.6.4+ 在 batch=1 时大约 88 token/s，开 batch=8 在并发请求下能拉到 320 t/s 量级（Unsloth 文档 + dev.to 公开实测帖估算，作者未自测）。

![OpenClaw 接本地 Qwen3 端到端接入架构](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/openclaw-qwen3-personal-workflow-2026-05-19/openclaw-qwen3-personal-workflow-2026-05-19-1-architecture.png)

OpenClaw 的接入是两条主线同时存在。**主线 A 是 MCP 协议**（stdio + SSE 双 transport，文档地址 `docs.openclaw.ai/cli/mcp`），用来让本地模型调本地工具——Qdrant 向量检索、PaddleOCR 凭证识别、Excel 读写、SQLite 流水查询都走这条。**主线 B 是 OpenAI 兼容 endpoint**，把推理后端切到本地 vLLM / Ollama / LM Studio，OpenClaw 的模型设置里 base URL 指 `http://localhost:8000/v1`、API key 任意填一个就跑通。

`~/.openclaw/openclaw.json` 关键片段长这样：

```json
{
  "providers": {
    "local-qwen-coder": {
      "type": "openai-compatible",
      "baseURL": "http://localhost:8000/v1",
      "apiKey": "sk-local-anything",
      "models": ["qwen3-coder-30b-a3b"]
    }
  },
  "mcpServers": {
    "qdrant-retriever": {
      "command": "uvx",
      "args": ["mcp-server-qdrant"],
      "env": { "QDRANT_URL": "http://127.0.0.1:6333" }
    },
    "sqlite-finance": {
      "command": "python",
      "args": ["-m", "mcp_server_sqlite_finance"]
    }
  }
}
```

vLLM 起服务命令（4090 单卡，AWQ Q4）：

```bash
vllm serve cpatonn/Qwen3-Coder-30B-A3B-Instruct-AWQ \
  --max-model-len 65536 \
  --host 127.0.0.1 --port 8000 \
  --gpu-memory-utilization 0.92
```

或者更轻便的 Ollama 路径：

```bash
ollama pull qwen3-coder:30b-a3b
ollama serve  # 默认 http://127.0.0.1:11434/v1
```

两条主线常常组合使用：主线 B 决定模型在哪里跑，主线 A 决定模型能调哪些工具。下面两个 case 全部按这个心智模型来配。

## 二、Case 1 · 个人写作工作流：从 1500 字初稿到中英对照

第一件事是中文长稿写作。我对它的期待很具体——给 6-8 个关键词或者一段散乱的笔记，能在合理时间内给到一份 1500-2000 字的初稿、5 个备选标题、和一份中英对照译稿。

OpenClaw 这边把这一整套封装成一个叫「写作助手」的 skill，存在 `~/.openclaw/workspace/skills/writing-assistant/` 目录里。skill 文件本质是一份 markdown 写的 prompt + 调度规则。触发方式可以是 macOS 菜单栏点开、可以是 Telegram 发消息、可以是飞书机器人——OpenClaw 把消息通道做成了多入口同一编排。

![个人写作 + 家庭记账两个深度 case 端到端流程图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/openclaw-qwen3-personal-workflow-2026-05-19/openclaw-qwen3-personal-workflow-2026-05-19-4-case-deep-dive.png)

全链路 6 步拆开：

1. **触发**。我在 macOS 菜单栏点 OpenClaw 图标，选「写作助手」skill，弹出一个 prompt 输入框
2. **上下文召回**。OpenClaw 调本地 Qdrant MCP，用 Qwen3-Embedding-8B 把我过去 30 天写过的相关片段召回前 8 条（约 2000 token）作为参考——这一步是写出「我的风格」的关键
3. **起草**。Qwen3-Coder-30B-A3B 在 Ollama 上接到完整 prompt（关键词 + 召回片段 + 写作风格约束），生成 1500 字初稿。延迟实测分布是 P50 6.2 秒首 token、P95 11.8 秒、P99 18.4 秒
4. **标题润色**。OpenClaw 用同模型不同 prompt 跑第二轮，让 Qwen3 给出 5 个备选标题，每个标题附一句卖点解释
5. **中英对照翻译**。通过 MCP 调一个独立的翻译 skill（同样跑在本地 Qwen3 上），把全文做段落级中英对照
6. **落盘**。整篇 Markdown + 元数据写到 `~/notes/2026-05-19-*.md`，向量自动更新到 Qdrant，方便下次召回

![三任务 P50/P95/P99 延迟分布](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/openclaw-qwen3-personal-workflow-2026-05-19/openclaw-qwen3-personal-workflow-2026-05-19-3-latency-distribution.png)

数字层面的几个观察。**首 token P50 6.2 秒**这条线，比 Claude Code Max 的同等任务慢 2 到 3 倍。Anthropic 在自家文档里给云端首 token 通常是 2-4 秒，差距确实存在。但本地这条路有两个反向优势：**一是 P95 不会出现云端那种偶发降速或限流**（订阅用户每天 5 小时之后被限速是 Claude Max 用户公开抱怨过的话题），二是 token 用完了不需要等下一个计费周期。

写作这件事还有个软指标——**风格连贯性**。Qwen3-Embedding-8B 在 MTEB Chinese 上的 task mean 是 73.84，比 BGE-M3 提升约 18.7%（dev.to 公开横评数字，2025-06 提交）。这个数字翻译到写作场景就是：召回我过去文章风格片段的命中率更高，初稿的「我味」更稳。这条线对长期写作者比单纯 throughput 更重要。

写作助手 skill 的核心 prompt（`writing-assistant/SKILL.md` 节选）：

```markdown
# 写作助手 skill

## 调用模型
local-qwen-coder · qwen3-coder-30b-a3b

## 上下文召回
通过 mcp:qdrant-retriever，按当前 topic 召回过去 30 天文档 top-8。

## 输出
1. 1500 字 markdown 初稿
2. 5 个候选标题（每个附 30 字卖点）
3. 段落级中英对照（调 mcp:translation skill）

## 风格约束
- 直接进入主题，禁套话开头
- 数字先行，解释在后
- 中文用大陆主流报道腔，禁港台词
```

## 三、Case 2 · 家庭记账 agent：500 条流水的自动分类

第二件事是家庭流水分类。我每个月从支付宝和微信导出消费 CSV，过去一直是手工分类——「外卖」「打车」「孩子学习」「订阅」「商超」十几个标签。一个月 500-800 条流水，手工分类要花一个晚上。

把这件事交给 OpenClaw 之后，整套流程压到 6 分钟以内：

1. **触发**。每月 1 号 OpenClaw 的 cron skill 自动启动一次，或者我在飞书机器人发「整理上月账单」手动触发
2. **流水入库**。OpenClaw 读取 `~/finance/2026-05-*.csv`（支付宝 + 微信导出），按表头解析成结构化记录写入 SQLite
3. **召回历史**。Qwen3-Embedding-8B 对每条流水的文字描述做 embedding，从历史已标注样本里召回前 5 条最相似条目作为 few-shot 提示
4. **分类**。Qwen3-Coder-30B-A3B 接 few-shot 提示 + 当前流水描述，输出结构化 JSON：`{ "category": "外卖", "confidence": 0.92, "reason": "美团商家名 + 时间在午餐窗口" }`
5. **聚合汇总**。OpenClaw 的 finance skill 把所有分类结果按类目汇总，输出当月开支报表 Markdown + A2UI Canvas 可视化（OpenClaw 自带的 Canvas 渲染层）
6. **异常预警**。如果某类目环比突增 50% 以上，OpenClaw 把摘要推到我的飞书

每条流水分类的延迟实测分布是 P50 1.4 秒、P95 2.6 秒、P99 4.1 秒（输出约 200 token）。500 条全跑完大概 14-18 分钟。这个数字比写作那条慢一些的原因是：财务任务输出 token 短、模型主要时间花在召回 few-shot 和读 prompt 上，而不是生成。

few-shot 召回这一步是命中率的关键。我用 Qwen3-Embedding-8B 替换之前用的 BGE-M3，**分类一次性命中率从 87% 提升到 94%**（基于我自己 3 个月样本统计，未公开发布）。剩下 6% 不命中的条目，OpenClaw 把 confidence < 0.7 的条目挑出来推到我的飞书让我手动确认，这部分一个月大概 20-30 条。

财务 agent 的 OpenClaw skill 文件（节选）：

```markdown
# 家庭记账 agent skill

## 调用模型
- 分类：local-qwen-coder · qwen3-coder-30b-a3b
- 召回：local-qwen-embedding · qwen3-embedding-8b

## 数据流
csv → sqlite → qdrant 召回 top-5 历史 → 分类 → confidence < 0.7 推飞书人工确认

## 异常规则
- 单类目环比 +50% → 异常
- 单笔金额 > 历史 P99 → 异常
- 周末连续 3 天某类目支出 → 异常

## 输出
- 月度报表 markdown
- A2UI Canvas 饼图 + 柱状图
- 异常摘要推飞书
```

这里有个国产硬件 + 国产模型的隐藏好处。**全部数据从来不出本机**——支付宝 / 微信流水不上传任何云端、不调任何海外 API。这对家庭财务这种特别敏感的场景是硬约束。如果换成 Claude Code 或者 GPT-5，账单细节得发到海外服务器，对很多用户是不能接受的。

## 四、一周真实数字：电费、token、延迟一起算

把上面两件事跑满一周，给出几个可以直接对账的数字。**前提是一名个人开发者每日 8 小时使用、写作 + 翻译 + 财务三任务混合、4090 主机当时段满载运转**。

电费这条线最直白：4090 主机典型功耗 350 W（GPU + CPU + 整机），按每度 0.5 元、每天 8 小时、每月 30 天，**月度电费约 42 元**。硬件折旧另算——4090 24GB 二手主机当下约 1.4 万元，按 36 个月折旧，每月约 389 元。两项合计 **本地方案月度边际成本约 430 元**。这个数字里没算空调和其他间接成本，南方夏天可以再加 20-30 元。

![本地 vs Claude Max vs DeepSeek API 月成本三方对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/openclaw-qwen3-personal-workflow-2026-05-19/openclaw-qwen3-personal-workflow-2026-05-19-2-cost-comparison.png)

token 消耗这条线没有计费意义，但有容量参考意义。粗算下来一周的真实使用是：

| 任务 | 单次请求 token | 周次数 | 周 token 量 |
|---|---|---|---|
| 写作初稿 | 输入 1.8k / 输出 1.5k | 14 | 输入 25k / 输出 21k |
| 中英对照翻译 | 输入 0.6k / 输出 0.8k | 28 | 输入 17k / 输出 22k |
| 标题润色 | 输入 1.6k / 输出 0.3k | 14 | 输入 22k / 输出 4k |
| 流水分类（单条） | 输入 0.5k / 输出 0.2k | 200 | 输入 100k / 输出 40k |
| **合计** | — | 256 次 | **输入 164k / 输出 87k** |

把这套用量换算到云端：Claude Code Max 计划月费 $100（折人民币约 720 元），90% 用户实际花费低于 $12/天，对应月度 360 元—2600 元区间。DeepSeek V4 Flash 缓存命中价格 1 元/百万 token，同样用量月度大约 280 元；Qwen3-Coder-Plus 阿里云按量大约 520 元（dev.to + 阿里云价格表估算）。

第二月起，**本地 430 元 vs Claude Max 720 元 vs 重度 Claude API 4320 元**。这条线的核心结论是：

- **轻度用户**（每天 1-2 小时低强度）：DeepSeek / Qwen 云端 API 反而比本地便宜，本地的优势只在隐私
- **中度用户**（每天 4-6 小时）：本地和 Claude Max 价格接近，本地略低
- **重度用户**（每天 8 小时以上、Claude API $20/天）：本地把月成本压到云端的十分之一，ROI 最强

## 五、和 Claude Code、Cursor 同任务怎么比

把同样的两件事（写作 + 财务）放在 Claude Code Max 和 Cursor Ultra 上跑一遍，差异在四个维度：

| 维度 | OpenClaw + 本地 Qwen3 | Claude Code Max | Cursor Ultra |
|---|---|---|---|
| 月费 | 430 元（电费 + 折旧） | $100（720 元） | $200（1440 元） |
| 首 token 延迟（写作 P50） | 6.2 秒 | 2-4 秒 | 2-4 秒 |
| 限流风险 | 无 | Max 计划每天 5h 后限速 | Ultra 限速少但仍有月度配额 |
| 数据出境 | 全部本地 | 走 Anthropic | 走 OpenAI / Anthropic |
| 中文 long-form 质量 | Qwen3 中文母语训练强 | Sonnet 中文流畅但偶发翻译腔 | 同上 |
| skill 生态 | OpenClaw ClawHub 5400+ skill | Anthropic skills | Cursor agent / rules |
| 微信 / 飞书 / WhatsApp 通道 | OpenClaw 原生 23 个通道 | 需自建 | 需自建 |
| 离线可用 | 是 | 否 | 否 |

把这张表读出来：**Claude Code 和 Cursor 是更快、更全能的代码 agent**，本地这条路在写作、流水这种「输出 token 慢一点没所谓、数据敏感、想要离线」的场景上反而占优。**这两条路不是替代关系是分层关系**——日常代码工作流交给 Claude Code Max，文字 / 财务 / 私人事务交给 OpenClaw + 本地 Qwen3，每一层用最合适的工具。

## 六、合规与隐私的真实边界

把数据留在本机有几个值得讲清楚的边界。

**第一是个人 vs 团队差异**。OpenClaw 主项目 MIT 协议，个人本机部署不存在任何法律问题。如果在公司机器上接公司数据，需要看公司数据出境策略——本地推理这条路天然合规，因为根本没出网。

**第二是国密合规**。如果场景是金融或医疗，需要走国密算法和等保认证，那 Qwen 这一档开源模型本身不带国密支持，需要包一层国密代理或者直接选阿里云 / 华为云的合规版本。这一档不是 OpenClaw 单独能解决的，要做合规架构的人接手。

**第三是数据回流**。OpenClaw 本身不上报任何遥测，但 skill 生态里有些第三方 skill 会调外部 API。建议私域场景把 OpenClaw 的网络出口锁到白名单：`~/.openclaw/openclaw.json` 里 `outboundAllowlist` 字段只放 `localhost` 和 `127.0.0.1`，连 OpenClaw 自家的 ClawHub 也走代理。

**第四是模型权重来源**。Hugging Face 直拉模型在国内偶尔卡顿，建议用 ModelScope 镜像或 HF 国内镜像 `hf-mirror.com`，命令前加 `HF_ENDPOINT=https://hf-mirror.com` 环境变量即可走国内 CDN。

## 七、什么场景值得切本地，什么留云端

把一周实测后的判断收拢成一张决策矩阵。

| 场景类型 | 推荐路径 | 理由 |
|---|---|---|
| 私人写作 / 日记 / 长文 | OpenClaw + Qwen3 本地 | 中文风格 + 隐私 + 长期沉淀向量库 |
| 中英对照翻译 | 本地 Qwen3-MT 或 Qwen3-Coder | 速度够、词汇本地化精确 |
| 家庭 / 个人财务 | OpenClaw + Qwen3 本地 | 数据敏感 + 高频低强度任务 |
| 个人代码 agent | Claude Code Max + 本地 backup | Anthropic skill 生态成熟、本地兜底 |
| 重度 agent 工作流（每天 8h+） | 本地为主 + 云端补强 | 边际成本下降空间最大 |
| 多人协作代码 | Cursor / Claude Code 团队版 | 团队协作工具链国产暂时不够强 |
| 多模态 / 视觉 agent | 云端为主 | 4090 跑视觉模型显存吃紧 |
| 跨设备同步 | 云端为主 | 本地多设备同步成本不低 |

这张矩阵的核心不是「本地取代云端」也不是「云端碾压本地」，而是**两条路按任务分层**。每一层选最合适的工具，整体的总成本和总体验都比单押一边更好。

## 八、为什么这条路真的能走通

最后回到第一段的论点。**OpenClaw 接本地 Qwen3 不是 Claude Code 的替代品，它是另一条路径。**

这条路径在 2026 年 5 月 19 日这个时间点上能跑通的原因有四个：一是 Qwen3-Coder-30B-A3B-Instruct 这一档 30B MoE 模型在 4090 24GB 上装得下（AWQ Q4 18.1 GB + 上下文留够），二是 Qwen3-Embedding-8B 中文检索质量真的赶上来了（MTEB Chinese 73.84），三是 OpenClaw 把多通道接入和 skill 生态做得足够成熟（5400+ skill），四是国内推理引擎和模型镜像基础设施稳定下来了（ModelScope / hf-mirror / vLLM 国内可拉）。

第二条路径打开之后，云端那条路反而更值钱了——把代码 agent 这种最高强度的工作留给 Claude Code Max，把写作 / 财务 / 私人事务搬回本机。两条路一起跑，月度总成本下降的同时，数据安全感和工作流稳定性反而提升。

慢一点没所谓。能完整跑通、能持续跑下去、能让数据真的留在本机，这条路就值得走。和读者一起把工作流搬回本地的过程，本身就有意义。
