---
title: 完全本地跑的 Deep Research agent：千问 27B 在 SimpleQA 拿 95.7%
slug: local-deep-research-learningcircuit-2026-05-07
date: 2026-05-07
weekday: 星期四
cover: local-deep-research-learningcircuit-2026-05-07.png
tags:
  - 本地大模型
  - AI Agent
  - Ollama
  - 千问
  - Claude Code
  - MCP
summary: GitHub 5988 星的 LearningCircuit/local-deep-research 把"OpenAI Deep Research"那一套搬到本地——千问 3.6 27B 在 SimpleQA 上跑出 95.7%，比 gpt-oss-120b 还高，一台 24GB 显存的 3090 就能跑全私有版。
---
# 完全本地跑的 Deep Research agent：千问 27B 在 SimpleQA 拿 95.7%

![完全本地跑的 Deep Research agent](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/local-deep-research-learningcircuit-2026-05-07/local-deep-research-learningcircuit-2026-05-07.png)

2026 年 5 月 7 日今天，海外开源 Deep Research 项目 `LearningCircuit/local-deep-research`（下文简称 LDR）的 GitHub 星数冲到 5988，社区跑分榜单第一名是国产千问 3.6 27B——不是 gpt-oss，不是 Llama，是千问。这条赛道国内开发者等了大半年的"不烧 OpenAI token、研究问题不发去 Tavily"的替代品，现在有了一份干净的成绩单可以参照。

LDR 项目 2026-02 上线，HN 顶贴 190+，过去 7 天 push 38 个 commit，主作者累计 5144 commits 的活跃度。社区在 Hugging Face 维护的真实跑分 dataset 里，**Qwen 3.6 27B + Ollama + LDR 的 langgraph_agent 策略，在 SimpleQA 上做到 287/300（95.7%）**——这个成绩不仅追平 OpenAI Deep Research 的官方公开口径，还把同测试中的 gpt-oss-120b（86.7%）甩开 9 个百分点。

更值得注意的是这条路线的政治性：**它是国产模型在海外社区的真实成绩单**——不是厂商自评，是社区贡献者用同一脚本、同一测试集、同一个搜索后端跑出来的横评。本文要回答的就是这一件事：**这套 5988 星的本地化 Deep Research agent 在国内开发者手里到底能跑成什么样，硬件门槛在哪，跟我们已经熟悉的 gpt-researcher / Khoj 比强在哪。**

![LDR 真实跑分 - 千问 3.6 27B 在 SimpleQA 拿 95.7%](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/local-deep-research-learningcircuit-2026-05-07/local-deep-research-benchmark-table.png)

## 一、这个 5988 星的项目到底干什么

**核心论断**：LDR 是一个把"问问题 → 自动搜索 → 多轮迭代 → 输出带引用的研究报告"完整流程跑在本地的 agent 框架，最大的价值不是 SimpleQA 那个分数，而是**整套搜索 + LLM + 加密知识库都不出本机**这件事。

它的工作流可以一句话讲完：

- 你输入一个研究问题（"DeepSeek-V4 跟 Claude 4.5 Sonnet 的 RLHF 路线差异"）
- LDR 让本地 LLM 拆题、决定先搜 arXiv 还是先搜 Wikipedia
- LangGraph agent 策略循环跑：搜 → 读 → 评估 → 再搜，直到信息足够
- 输出 markdown 研究报告，每条结论挂引用、可导出 PDF
- 顺手把搜到的源文件下到本地 SQLCipher 加密库里，下次研究复用

它的差异化在三件事上：

1. **本地 LLM 是一等公民**——Ollama、LM Studio、llama.cpp 三种方式都内置，连 LangChain 那种"装 OpenAI 套壳"的味道都没有
2. **20+ 研究策略**——从 30 秒的 quick_summary 到 30 分钟的 detailed_research，按问题深度选；新加的 `langgraph-agent` 策略让 LLM 自己决定调哪个引擎
3. **加密知识库 + MCP 服务器**——SQLCipher AES-256 加密；自带 MCP server 让 Claude Code / Claude Desktop 直接调用 LDR 跑深度研究

后两件事是 gpt-researcher 不做、Khoj 部分做的——而 MCP 这条线对国内 Claude Code 用户特别关键，下文专门展开。

## 二、千问 27B 拿 95.7% 是怎么回事

**核心论断**：这不是厂商自评，是社区贡献者用同一套测试脚本跑出来的横评——而且**国产模型在这套测试里第一名**。

数据来源是 `huggingface.co/datasets/local-deep-research/ldr-benchmarks`，社区 CI 校验过的 10 条提交记录里：

- **qwen3.6:27b（Ollama）+ langgraph_agent + Serper**：SimpleQA 287/300 = 95.7%
- **qwen3.6:latest（Ollama）+ langgraph_agent + Serper**：SimpleQA 287/300 = 95.7%
- **qwen3.5:9b（Ollama）+ langgraph_agent + Serper**：SimpleQA 182/200 = 91.2%
- **gemma4:31b（Ollama）+ langgraph_agent + Serper**：SimpleQA 271/300 = 90.3%
- **gpt-oss-120b（OpenAI 端点）+ langgraph_agent + Serper**：SimpleQA 26/30 = 86.7%
- **gpt-oss:20b（Ollama）+ langgraph_agent + Serper**：SimpleQA 295/346 = 85.4%

把数字翻译成一句判断：**这套测试里，27B 的国产模型把 120B 的海外开源模型甩开 9 个百分点，胜负不在参数规模这一栏。**

几个角度可以读这张表：

**第一**，27B 的千问比 120B 的 gpt-oss 高 9 个百分点。这不是参数规模决定一切——LDR 这种 agent 任务里，模型对"决定搜什么、什么时候停止搜"的判断能力比参数量更重要。千问 3.6 在中文工具调用 + 多步推理这两条上的训练显然下了功夫。

**第二**，9B 的千问 3.5 也能拿 91.2%，比 31B 的 Gemma 4 高。**这意味着一台 RTX 3090 / 4090 24GB 显存的家用机器，跑 9B 全精度或者 27B 4-bit 量化，就能复现接近 SOTA 的 Deep Research 体验**——不需要 H100，不需要云端 API 账户，不需要把研究问题发给硅谷服务器。

**第三**，这是社区提交，不是厂商自评。LearningCircuit（项目作者）跑了 8 条，djpetti（也是 BSAIL Lab 学术评测人）跑了 1 条，kwhyte7 跑了 1 条 —— **谁都可以提自己的跑分 PR，CI 会复跑校验**。这套机制本身就是对"厂商发布会跑分"那种营销叙事的反向校准。

xbench-DeepSearch 这个更难的多步检索测试上，千问 3.6 27B 也拿到 77/100 = 77.0%——9B 的版本掉到 59%，说明 deep-search 任务对模型容量更敏感，27B 是分水岭。

## 三、跟 gpt-researcher / Khoj 比差在哪、强在哪

国内开发者圈早就熟悉 `assafelovic/gpt-researcher`（21k 星）和 `khoj-ai/khoj`（30k 星）。LDR 5988 星，从绝对量看不算最大——但定位差异非常明确。

![三条本地化 Deep Research 路线对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/local-deep-research-learningcircuit-2026-05-07/local-deep-research-vs-competitors.png)

**核心论断**：LDR 不是来抢 gpt-researcher 用户的——它服务的是"我连云端 API 都不想调"的私有化场景。

三个项目的真实差异：

- **gpt-researcher**：默认 OpenAI / Anthropic + Tavily / SerpAPI，云端 API 是一等公民。本地化是补丁，不是主线。21k 星里大部分用户是云端 API 跑研究的，国内用户走它需要至少一个海外 API 账户
- **Khoj**：定位是"个人 AI 助手 + 知识库"，研究只是一个能力。本地化通过 Ollama 支持，但搜索引擎那一头不如 LDR 全（10+ 学术源）
- **LDR**：默认就是本地——Ollama + SearXNG + SQLCipher 是"开箱主角"，云端 API 反而是"如果你想用也行"的备选

对国内开发者意味着：

- 如果你已经有 OpenAI 信用卡 + Tavily 账户，gpt-researcher 工程成熟度更好，没必要换
- 如果你要的是"个人助手 + 偶尔深度研究"，Khoj 体验更轻
- 如果你的诉求是"完全私有 + 公司合规要数据不出本机 + 要跑学术研究"——LDR 是目前最贴合的选择

特别说一下 SearXNG 这条线。LDR 默认推荐 docker run SearXNG 在本机做元搜索后端——SearXNG 自己就是个隐私优先的开源搜索元引擎，10+ 学术源（arXiv、PubMed、Semantic Scholar、Wikipedia、Wayback、Guardian、GitHub、StackExchange、OpenLibrary、Gutenberg）默认接好。**整条研究链上没有一个云端商业 API 出现**——这种纯净度在 2026 年的 deep research 项目里非常少见。

## 四、硬件门槛：3090 / 24GB 显存能不能跑

**核心论断**：跑得动，而且能跑出 SOTA 段位——但要选对模型 + 量化方案。

![LDR 国内开发者硬件配置参考](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/local-deep-research-learningcircuit-2026-05-07/local-deep-research-hardware-guide.png)

按硬件预算给具体推荐：

- **RTX 3060 12GB**（学生 / 入门家用）：跑 qwen3:4b 量化版，走 quick_research 策略——SimpleQA 段位 74%，问简单问题没问题，复杂多步推理勉强
- **RTX 3090 / 4090 24GB**（个人开发者主力机）：两个甜蜜点
  - qwen3.5:9b 全精度 + langgraph_agent → SimpleQA 91% 段位
  - qwen3.6:27b 4bit 量化 + langgraph_agent → SimpleQA 95.7% 段位
- **M1/M2 Max 32GB Mac**（Mac 用户）：gpt-oss:20b 是社区跑得最熟的一条，SimpleQA 85% 段位，跑得稳，发热可控
- **纯 CPU 32GB 内存**（无独显）：gemma4:26b 量化能跑，SimpleQA 73% 段位，但单次研究 15-30 分钟得忍——quick_research 模式更适合
- **云端零硬件**（不想买卡）：DeepSeek API / 千问云 + LDR 的 langgraph_agent 也走得通——准确率接近 95% 段位，但你就回到了"研究问题发给云端"的状态

显存怎么估算？粗略口径：参数量 × 2 = 半精度显存 GB；4bit 量化版本约 = 参数量 × 0.6。所以 27B 全精度要 ~54GB，4bit 量化压到 ~17GB——24GB 显存的 3090 / 4090 装得下，但留给上下文的余地不大，跑长研究要分批。

**Ollama 默认拉模型这条体验非常关键**——LDR 1.7 之后取消了"自动拉 gemma3:12b"的默认（避免悄悄下载 GB 级权重），改成必须在 Settings → LLM 里手动选模型。这是个对的设计——但意味着第一次跑要花 10-30 分钟先 `ollama pull qwen3.6:27b`，做好心理准备。

## 五、Claude Code 用户的隐藏价值：MCP server

**核心论断**：LDR 自带的 MCP server 让 Claude Code 直接调用本地 LDR 跑深度研究——这是 gpt-researcher / Khoj 都没有的、对 Claude Code 重度用户特别关键的能力。

![LDR 接入 Claude Code 的 MCP 工具清单](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/local-deep-research-learningcircuit-2026-05-07/local-deep-research-mcp-tools.png)

接入方式简单到一行 JSON。在 `~/.claude/mcp.json` 写：

```json
{
  "mcpServers": {
    "local-deep-research": {
      "command": "ldr-mcp",
      "env": {
        "LDR_LLM_PROVIDER": "ollama",
        "LDR_LLM_OLLAMA_URL": "http://localhost:11434"
      }
    }
  }
}
```

然后 Claude Code 里就能直接说"用 quick_research 帮我查 DeepSeek-V4 的 RLHF 路线"——Claude Code 会调 `ldr-mcp`，LDR 用本地 Ollama + SearXNG 跑完，把带引用的报告还回来。

7 个 MCP 工具的分工很清楚：

- `search`：单引擎原始结果（arxiv / pubmed / wikipedia 等），秒级，不烧 LLM token——**特别适合做日常监控**（每周扫一次某个领域的新论文）
- `quick_research`：1-5 分钟出汇总，适合临时查问题
- `detailed_research`：5-15 分钟深度分析
- `generate_report`：10-30 分钟出完整 markdown 报告
- `analyze_documents`：搜你本地 SQLCipher 库里的文档（学术 PDF / 公司内部资料）
- `list_search_engines` / `list_strategies` / `get_configuration`：元工具，秒回

**这套 MCP 设计对国内 Claude Code 用户的特殊意义在于**：你日常用 Claude Code 写代码已经在烧 Anthropic API token 了，研究环节如果再走 OpenAI Deep Research 又是一笔。LDR 把研究环节切到本地——Claude Code 还是 Claude Code，但 Deep Research 这一段变成本地 Ollama + SearXNG，token 账单只剩 Claude Code 自己那一份。

需要注意的安全提醒：LDR 的 MCP server 设计是**纯本地 STDIO 传输**，没有内置鉴权和速率限制。不要把它暴露到网络上——这是 LDR 文档里明确写的红线。

## 六、真实读者的 5 条吐槽（HN 顶贴）

**核心论断**：这个项目还在活跃迭代，不是"装上就能用"的状态——HN 顶贴 190+ 评论里，社区给出的 5 条具体吐槽值得你先有心理准备。

直接引用 HN 顶贴评论（`news.ycombinator.com/item?id=43330164`）：

- **mentalgear**：「输出文档结构有点散」——希望接图数据库做结论之间的关系图谱
- **sinenomore**：「需要做正经的自动化 eval」——LLM-as-judge 跟 perplexity / openai / deepseek 横评
- **CGamesPlay**：「跑长研究遇到很多错误，没法生成完整报告，最关键的是失败了不能 resume」——这是真实痛点，长 research 单 session 跑 30 分钟，中间挂掉就全没
- **throwaway24681**：「内部检索按问题搜不优，应该按关键词搜」——同时希望日志再详细些
- **HashedViking**（已是项目第 5 大贡献者）：「很多号称开源的 deep research 都是 OpenAI API 的套壳」——这条对照来看反而是 LDR 的卖点：它**真的**是本地的

GitHub issue tracker 也有非常具体的当前痛点：

- Issue #3826：「巨大内存泄漏，主机内存被吃满」——昨天 6 个评论在跟进
- Issue #3800：「LM Studio 模型识别不到」——一个 LM Studio 用户报的
- Issue #3827：下载管理器下不了

这些不是项目质量差的信号——5144 commit、过去 7 天 38 个 commit 的项目里，issue 跟 PR 是同一个量级的。这是项目在以高速度迭代的副产品。**国内开发者上手时建议先用 docker compose 走最稳的一条路，并且把 quick_research（5 分钟内完成）作为常用模式，detailed_research 只在确定不会被打断时再跑**。

## 七、国内开发者的真实落地路径

**核心论断**：先 docker compose + 千问 9B + SearXNG 跑通三件套，再决定要不要升级到 27B。

对国内开发者，三步上路：

1. **基础设施一次配齐**（一台 24GB 显存的家用机或公司开发机）
   - 装 Docker Desktop
   - `docker run -d -p 11434:11434 --name ollama ollama/ollama`
   - `docker exec ollama ollama pull qwen3.5:9b`（先从 9B 起步，成本低）
   - `docker run -d -p 8080:8080 --name searxng searxng/searxng`
   - `docker compose up -d` 起 LDR 主服务
2. **Settings 里选模型 + 选策略**
   - LLM provider 选 Ollama，model 填 `qwen3.5:9b`
   - 研究策略默认 `langgraph-agent`（社区跑分最高的那条）
   - 搜索引擎勾选 SearXNG + arXiv + Wikipedia + PubMed
3. **第一次研究测水温**
   - 先跑 quick_research 模式，问一个你已经熟悉的领域（比如"Qwen 3.6 跟 DeepSeek V4 的对比"）
   - 看 5 分钟内能否拿到带引用的合理汇总
   - 通了再升级到 27B 4bit 量化版本，跑 detailed_research

国内可能踩的两个坑：

- **SearXNG 部分上游搜索引擎在国内访问慢或被墙**——可以在 SearXNG 配置里挑国内可达的源（DuckDuckGo、Bing、StackExchange 这些都还行，Google / Yahoo 不稳）
- **千问 / DeepSeek 之外的本地模型拉权重慢**——Ollama 默认从国外 CDN 拉，可以走 modelscope 镜像或者公司内网代理

## 八、收尾：这是国产模型在海外社区拿到的一张干净成绩单

回到开头那个论点。LDR 的真正价值不在 5988 星这个数字，也不在"OpenAI Deep Research 平替"这个 slogan——它的价值在于：**在一个由海外开发者主导的开源项目里，由社区贡献者用统一脚本横评出来的真实跑分，第一名是国产的千问 3.6 27B**。

这件事的两层含义都值得国内开发者记住：

第一层是技术层面——**完全本地的 Deep Research agent 路线已经走通了**。一台 24GB 显存的家用机器，跑千问量化版，配 SearXNG + LDR，准确率追平甚至超过云端商业服务。如果你的工作场景对数据隐私 / 公司合规有硬要求，这条路线现在就能落地。

第二层是生态层面——**国产模型在海外开源社区的真实接纳度，远比我们日常感受到的要高**。LearningCircuit 这个海外项目的 SimpleQA 跑分页面，前两名都是千问，社区 CI 跟着复跑校验。这不是营销，是开发者用 PR 投出来的票。

这一代 AI 开发者特别幸运的地方在于：基础设施已经搭得很完整。本地有 Ollama + LM Studio + llama.cpp，模型有千问 / DeepSeek / Gemma 全谱系，研究框架有 LDR / gpt-researcher / Khoj 三条路线可选，编辑器里有 Claude Code + MCP 这套粘合层。**剩下要做的就是把它们组装起来跑通自己的工作流——前辈已经把路趟出来了，路在前面，一起加油。**

---

**项目地址**：`github.com/LearningCircuit/local-deep-research`（5988 星 · MIT License · 截至 2026-05-07）

**社区跑分数据集**：`huggingface.co/datasets/local-deep-research/ldr-benchmarks`

**HN 顶贴讨论**：`news.ycombinator.com/item?id=43330164`（190+ 评论）
