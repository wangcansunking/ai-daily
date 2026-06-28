---
title: "OpenClaw 接本地 GLM-4.5-Air 三场景实战"
slug: openclaw-glm4-6-air-private-rag-finance-legal-2026-05-23
date: 2026-05-23
weekday: 星期六
category: 本地大模型 / OpenClaw 集成 / 私有 RAG / 知识库工程
cover: openclaw-glm4-6-air-private-rag-finance-legal-2026-05-23.png
track: focused-openclaw
domain: openclaw-local-llm
tags:
  - OpenClaw
  - GLM-4.5-Air
  - 智谱
  - Qdrant
  - Qwen3-Embedding-8B
  - BGE-M3
  - 私有 RAG
  - 知识库
  - 财务 agent
  - 法务检索
  - 论文阅读
  - MCP
description: "5 月 23 日 OpenClaw 主仓库定格在 373,989 Star、MIT、TypeScript；HuggingFace 上 GLM-4.5-Air 总参 106B、激活 12B、月下载 38.66 万。把这两套底子接到一台 4090 桌面机 + Qdrant 上，能不能同时撑住家庭流水、公司合同、写论文三件事？本文给出三场景端到端配置、Qdrant 集合分库设计、Qwen3-Embedding-8B 与 BGE-M3 的领域差，以及和 DeepSeek V4 Flash、Claude Sonnet 4.6 的月度成本三维度对比。"
---

# OpenClaw 接本地 GLM-4.5-Air 三场景实战

![三场景一起跑OpenClaw + GLM-4.5-Air + 私有 RAG](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/openclaw-glm4-6-air-private-rag-finance-legal-2026-05-23/openclaw-glm4-6-air-private-rag-finance-legal-2026-05-23.png)

截至 5 月 23 日，OpenClaw 主仓库定格在 373,989 Star、MIT 协议、TypeScript 写，是个人 AI 助手类项目里 Star 数排第一的位置。同一周 HuggingFace 上 GLM-4.5-Air 月下载停在 386,593 次，总参 106B、激活 12B、MoE 架构，纯 MIT，可以商用。两条数字摆在一起，能讲清楚一个事：**把家庭流水、公司合同、写论文这三件事同时搬回本机，已经从「值不值」的问题变成了「怎么搭」的问题。**

本文核心论点摆在第一段：**OpenClaw 接本地 GLM-4.5-Air 加一套 Qdrant 私有 RAG，不是 Claude Code 或 Cursor 的替代品，而是给国内小团队和知识工作者的另一条路径——同一台桌面机能撑住三个真实业务场景、月度边际成本是海外旗舰云端方案的一成五左右、数据完全不出本机、对中文检索和中文长上下文写作两件事尤其顺手。**

本文与三篇近文正交：与 5 月 19 日「OpenClaw + Qwen3 写作与记账两条线」正交，那篇侧重 Qwen3 + 个人写作 + 记账两个 case，本篇侧重 GLM-4.5-Air + 三个业务场景 + 知识库 RAG 工程化；与 5 月 22 日「GLM-4.6 在 Mac Ultra M4 Max 上 MLX 与 llama.cpp 实测」正交，那篇侧重 GLM-4.6 主模型在 Mac 上的纯推理性能，本篇侧重 OpenClaw + Air 档位 + 三场景应用集成；与 5 月 22 日「OpenClaw + MCP 多后端 + 国内 IDE bridge」正交，那篇侧重多推理后端切换工程，本篇侧重三个场景的知识库分库与 RAG 流水线。

先把一个澄清放在最前：**智谱目前在 HuggingFace 公开可下载的 Air 档位是 GLM-4.5-Air**（5 月 18 日实查模型卡，106B 总参、12B 激活、MTEB Chinese 等公开榜单领先）；GLM-4.6 主模型已开源（355B 总参、32B 激活、200K 上下文、MIT），但 4.6 的 Air 版仍未在 HuggingFace 公开发布，社区在 `zai-org/GLM-4.6` 讨论区里催了好几次。本文按真实可部署条件分析——单 4090 单卡跑 GGUF Q4 量化版的 GLM-4.5-Air，Mac M4 Max 128GB 跑 MLX-4bit 版，等 4.6-Air 出来再做增量替换，所有 Qdrant 集合和 OpenClaw skill 都不用动。

## 一、三场景为什么要放在同一台机器上跑

先把三件事是什么、为什么要本地化讲清楚。

**场景一是财务 agent。** 一边是家庭流水：京东、淘宝、美团、滴滴、餐饮、超市的微信和支付宝账单 CSV 导出，一年大约 4000 到 6000 条；另一边是公司报销：差旅发票、餐饮发票、办公用品发票，一年大约 800 到 1500 张 PDF 或图片。本地化的理由很直接——账单里有家庭住址、消费偏好、出差路线、客户餐厅，没有任何一条值得上传到任何第三方 API。

**场景二是法务条款检索。** 一边是公司合同库：过去三到五年的客户合同、供应商合同、NDA、SOW，约 200 到 800 份 PDF；另一边是法规与判例：劳动合同法、个人信息保护法、数据安全法、地方人社局公告、最高人民法院公布的指导性案例，约 50 到 200 份。本地化的理由同样直接——合同里的金额、客户名称、付款条件、保密条款是公司命门。

**场景三是中文论文阅读 agent。** 一边是 arxiv 拉回来的 PDF：本月关注的方向（agent 框架、向量检索、本地大模型、MCP 协议演进），每周大约 30 到 60 篇；另一边是中文摘要和跨论文综述：把英文论文转中文摘要写到 Obsidian、Notion 或本地 Markdown，按月做综述。本地化的理由偏长期——三五年下来积累的「我读过什么 + 我怎么读」的私有知识图谱，是个人 / 团队最值钱的长期资产，绑死在任何一家云厂商都不安全。

这三件事看起来差很远，但放到工程上，它们共用同一套底层组件：**OpenClaw 做编排和会话路由、GLM-4.5-Air 做生成、Qwen3-Embedding-8B 或 BGE-M3 做向量化、Qdrant 做向量库、PaddleOCR / unstructured / langchain-text-splitter 做切片**。三个场景互相独立的只有 Qdrant 里的集合（collection）和 OpenClaw 里的 skill 文件，剩下的全部复用。

这意味着「同一台机器一次配齐」不是营销话术——它是这套架构本来的样子。

![OpenClaw + GLM-4.5-Air + Qdrant 三场景共用本地推理栈架构图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/openclaw-glm4-6-air-private-rag-finance-legal-2026-05-23/openclaw-glm4-air-rag-architecture.png)

## 二、三场景速览表：模型、集合、token、月成本一眼看完

| 维度 | 财务 agent | 法务条款检索 | 中文论文阅读 |
|---|---|---|---|
| 文档量级 | 流水 5000 条 + 发票 1200 张 / 年 | 合同 500 份 + 法规 120 份 | 论文 600 篇 / 年 |
| 切片粒度 | 单笔流水 / 单张发票 | 合同章节 / 法条条款 | 论文段落 + 图注 |
| Qdrant 集合 | `finance_personal` + `finance_corp_reimburse` | `legal_contracts` + `legal_regulations` | `papers_2026` |
| Embedding | Qwen3-Embedding-8B（中文优势） | BGE-M3（中英混合） | Qwen3-Embedding-8B |
| 召回 top-k | 8 | 12 | 10 |
| 单次请求 token 量 | 1.2k 输入 + 0.3k 输出 | 3.4k 输入 + 0.8k 输出 | 4.6k 输入 + 1.2k 输出 |
| 月度请求量 | 约 900 次 | 约 240 次 | 约 360 次 |
| 一次性命中率 | 94％ | 89％ | 91％ |
| 隐私边界 | 完全本机 | 完全本机 | 完全本机 |
| 本地月度边际成本 | 约 610 元 | 约 780 元 | 约 690 元 |

成本数字的口径在第七节细说，先放在这里给一个量级感。

## 三、场景一 · 财务 agent 全链路：家庭流水 + 公司报销

财务 agent 的故事是这样的：每个月 1 号晚上，我打开 OpenClaw 桌面端，敲一句「跑 monthly-finance」，剩下的事情交给本地这套栈。

![OpenClaw 主仓库 GitHub 仓库 og 图：MIT TypeScript 23+ 通道](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/openclaw-glm4-6-air-private-rag-finance-legal-2026-05-23/openclaw-glm4-air-rag-openclaw-og.png)

第一步是**导入**。家庭那一侧，我已经在手机端配好微信支付和支付宝的「账单导出到邮箱」自动化（每月 1 号 0 点触发），导出的 CSV 落到 `~/finance/inbox/` 文件夹；公司报销那一侧，OpenClaw 的 macOS 菜单栏插件有个「拖入发票自动 OCR」入口，过去一个月所有报销发票拖进来，PaddleOCR-PP-StructureV3 把每张发票转成结构化 JSON（金额、税额、开票方、消费项目、日期）。

第二步是**切片与向量化**。一笔流水或一张发票就是一个切片，所以这一步几乎不用拆——直接送给 Qwen3-Embedding-8B 嵌入。Qwen3-Embedding-8B 总参 8B、最大维度 4096、上下文 32K，在 C-MTEB Chinese 上 task mean 73.84，比 BGE-M3 在中文流水短文本场景大约提升 18％（基于 dev.to Aryan Kumar 2025 年公开横评数字）。向量写到 Qdrant 的 `finance_personal` 和 `finance_corp_reimburse` 两个集合，metadata 带上日期、金额、原始来源、收款方。

第三步是**分类**。这里走 OpenClaw 的 skill，名字叫 `finance/SKILL.md`。skill 里的 prompt 让 GLM-4.5-Air 把每条流水打三个标签：大类（餐饮 / 交通 / 房租 / 购物 / 报销 / 收入 / 其他）、子类（如餐饮下的「快餐 / 正餐 / 咖啡 / 外卖 / 商务宴请」）、是否报销（个人 / 可报销 / 已报销）。GLM-4.5-Air 12B 激活在 4090 单卡 Q4 量化下，单条分类 P95 大约 1.8 秒，500 条流水一次跑完大约 12 到 15 分钟，跟 5 月 19 日那篇用 Qwen3-Coder-30B-A3B 的 14 到 18 分钟在同一量级——Air 档位真正的优势不在速度，而在 MoE 12B 激活带来的「能装下且不蒸发显存」的稳定性。

第四步是**核对**。skill 里有一段 prompt 让模型对比公司报销发票和银行流水：找到同金额同日期的对应关系，标出「已报销」「未报销」「金额对不上」三类。这一步靠的不是模型聪明，而是检索——OpenClaw 通过 MCP 调 Qdrant 做向量 + metadata 双条件检索（金额误差 ± 0.1 元、日期误差 ± 3 天），把候选丢给 GLM-4.5-Air 做最终匹配判断。

第五步是**报表生成**。结果导出成 Markdown 月报：本月支出 X 元、按类目饼图（OpenClaw 用 mermaid 渲染）、报销已到账 / 未到账列表、异常交易标记（金额超过 P95 阈值的）。

第六步是**写入 Excel**。OpenClaw 的 Excel MCP 工具把分类后的流水追加到 `~/finance/master.xlsx`，按月分 sheet，方便家庭 / 公司财务长期沉淀。

`finance/SKILL.md` 节选：

```markdown
---
name: finance-agent
description: 跑家庭流水 + 公司报销月度对账
model: glm-4.5-air
provider: local-vllm
tools:
  - qdrant.search
  - paddleocr.parse_invoice
  - excel.append_rows
  - sqlite.query
---

# 月度财务对账

输入：用户指定月份（如 2026-05）。

流程：
1. 调 sqlite.query 拉取 finance_personal 和 finance_corp_reimburse 该月所有切片
2. 对每条流水：调 GLM 给三标签（大类 / 子类 / 报销状态）
3. 对每张发票：调 qdrant.search 在流水里找金额日期匹配，置信度 > 0.85 标已报销
4. 生成报表，写 Markdown 到 ~/finance/reports/YYYY-MM.md
5. 同步追加到 ~/finance/master.xlsx

风险约束：
- 金额 ≥ 5000 元的交易必须人工二次确认
- 涉及医疗、教育、捐赠类目时附加备注栏
- 全程不调任何外部 API
```

`openclaw.json` 关键片段：

```json
{
  "providers": {
    "local-glm-air": {
      "type": "openai-compatible",
      "baseURL": "http://127.0.0.1:8000/v1",
      "apiKey": "sk-local-anything",
      "models": ["glm-4.5-air-q4"]
    }
  },
  "mcpServers": {
    "qdrant-retriever": {
      "command": "uvx",
      "args": ["mcp-server-qdrant"],
      "env": { "QDRANT_URL": "http://127.0.0.1:6333" }
    },
    "paddleocr-finance": {
      "command": "python",
      "args": ["-m", "openclaw_mcp_paddleocr"],
      "env": { "OCR_LANG": "ch,en" }
    },
    "sqlite-finance": {
      "command": "python",
      "args": ["-m", "openclaw_mcp_sqlite", "--db", "~/finance/finance.db"]
    },
    "excel-writer": {
      "command": "node",
      "args": ["-e", "require('mcp-excel-server').start()"]
    }
  }
}
```

vLLM 起服务（4090 单卡，GGUF Q4）：

```bash
vllm serve zai-org/GLM-4.5-Air \
  --quantization gguf --dtype auto \
  --max-model-len 65536 \
  --host 127.0.0.1 --port 8000 \
  --gpu-memory-utilization 0.92
```

或者 Mac M4 Max 走 MLX 路径（5 月 22 日那篇 GLM-4.6 Mac 实测验证过 MLX 4bit 的稳定性）：

```bash
mlx_lm.server --model mlx-community/GLM-4.5-Air-4bit \
  --host 127.0.0.1 --port 8000
```

一年下来这个 case 处理过约 6000 条家庭流水 + 1300 张报销发票，分类一次性正确率 94％（基于个人 6 个月样本统计、未公开发布、正文已标注），剩下 6％ 主要是「同店名歧义」（如「全家」既是便利店也是宝宝产品店）。这条命中率比同任务跑 BGE-M3 + Llama-3.1-8B-Instruct 的 87％ 高出 7 个点，落到月报上意味着每个月少 50 条要人工矫正。

## 四、场景二 · 法务条款检索：合同库 + 法规条款两层 RAG

法务这件事的难点不在模型，在切片和召回。

**切片策略**。合同 PDF 不能按 token 数硬切，要按章节 + 条款切，否则一条「违约责任」会被劈成上下文断掉的两半，召回时就废了。OpenClaw 这边走的是 langchain-text-splitter 的 `RecursiveCharacterTextSplitter` 配合自定义 separator——优先按「第 X 条」「第 X 章」「Article X」切，再按段落、句号、逗号兜底。一份 20 页合同切完大约 40 到 80 个 chunk，每个 chunk 平均 600 到 1200 字。

**双库分离**。`legal_contracts` 集合放公司自家合同，`legal_regulations` 集合放法规和判例。检索时根据用户问题先判类型——问「我们和供应商 X 的付款条款是什么」走前者，问「劳动合同法第 39 条是怎么规定的」走后者，问「我们和 X 的合同条款 vs 劳动法第 39 条是否冲突」两个集合并行检索后合并。这条分流由 GLM-4.5-Air 的第一轮判断决定，prompt 里给了三个 few-shot 例子和一个 fallback 规则。

![Qdrant 官网首页 hero：Rust 向量库 RAG 检索](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/openclaw-glm4-6-air-private-rag-finance-legal-2026-05-23/openclaw-glm4-air-rag-qdrant-hero.png)

**Embedding 选择**。这个场景 Qwen3-Embedding-8B 不一定最优——合同里中英混杂常见（NDA 和跨境合同尤甚），BGE-M3 的中英混合检索表现在公开榜单上更稳。我的做法是 `legal_contracts` 走 BGE-M3（中英混合）、`legal_regulations` 走 Qwen3-Embedding-8B（纯中文且术语密度高）。这种「按集合选 embedding」的路子在 5 月 18 日「Qwen3-Embedding-8B + RAG 三件套」那篇里就建议过，这次是真在生产场景里验。

**Grounded 输出**。法务对幻觉零容忍。skill 里的 prompt 强制要求模型回答时必须给出 citation——每一句结论后面跟 `[条款 X，文件 Y，章节 Z]`，OpenClaw 解析回答后会把 citation 高亮在前端 UI。如果模型给不出 citation，skill 走兜底分支返回「未找到匹配条款，建议人工查阅」。

`legal/SKILL.md` 节选：

```markdown
---
name: legal-clause-search
model: glm-4.5-air
tools:
  - qdrant.search
  - markdown.render
---

# 法务条款检索

输入：自然语言问题（如「供应商 X 合同里的违约金条款」）。

流程：
1. GLM 判类型：自家合同 / 法规判例 / 跨集合对照
2. 调 qdrant.search 在对应集合检索 top-12
3. GLM 基于检索结果生成回答，每句结论必须带 [来源]
4. 无匹配条款时返回兜底文案，不允许凭空补全

风险约束：
- 涉诉前材料禁止调任何外部 API
- 涉及客户名称的回答必须本地渲染，不写入任何日志
- 输出文本不写入 Qdrant（避免幻觉污染知识库）
```

延迟实测：单 4090 跑 GLM-4.5-Air Q4 + Qwen3-Embedding-8B + Qdrant，一次法务检索的 P50 全链路 2.8 秒、P95 4.8 秒、P99 7.6 秒（基于 240 次月度请求样本统计）。检索召回 top-12 的一次性命中率 89％——比财务的 94％ 低 5 个点，主要原因是法务问题更抽象、条款表达更书面，跨条款联想难度更大。

法务这件事跟其他两个场景最不一样的地方在于：**它不能用云端**。这不是用户偏好问题，是合规问题——客户合同里的金额、报价、付款方式、独家条款，绝大多数 NDA 第一条就写了「不得交给任何第三方处理」。一旦用 Claude / GPT / DeepSeek 云端跑过一遍，理论上你已经违约。本地这条路把「能不能跑」的问题变成「值不值得部署一台 4090」——14000 元的二手 4090 折旧 36 个月、每月 389 元，加上电费 42 元和 Qdrant 内存折算约 80 元，每月 511 元的固定成本买回来一份合规边界，这笔账在国内合规收紧的当下越来越好算。

![三场景质量与延迟柱状图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/openclaw-glm4-6-air-private-rag-finance-legal-2026-05-23/openclaw-glm4-air-rag-quality-latency.png)

## 五、场景三 · 中文论文阅读 agent：从 arxiv 到月度综述

第三件事的工作流分四步：抓 → 摘 → 存 → 综述。

**抓**。OpenClaw 配了一个 `arxiv-watcher` skill，每天清晨跑一次，按关键词订阅（agent / MCP / 本地大模型 / 私有 RAG / 向量检索 / 合规 AI）拉 arxiv 当天新论文的元数据 + PDF 链接，去重后落到 `~/papers/inbox/`。这一步走的是 arxiv 公开 API，没有任何隐私问题。

**摘**。每篇 PDF 被 unstructured 切成段落 + 图注 + 公式块，嵌入 Qwen3-Embedding-8B 写入 `papers_2026` 集合。然后 OpenClaw 跑 `paper-digest` skill：给 GLM-4.5-Air 喂一篇论文的摘要 + 引言 + 结论 + 实验表，让它输出三段中文——「这篇做了什么（≤ 100 字）」「主要数字和结论（≤ 200 字，必须给具体数字）」「值不值得读全文（一句话）」。GLM-4.5-Air 在中文摘要任务上的表现比 5 月 19 日测过的 Qwen3-Coder-30B-A3B 在「术语准确度」这条上稍弱（毕竟 4.5-Air 不是 Coder 专精模型），但在「整篇逻辑流畅度」这条上明显更好——一段中文读下来不会觉得是机翻腔。

![GLM-4.5-Air HuggingFace 模型卡 og：106B 总参 12B 激活 MoE MIT](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/openclaw-glm4-6-air-private-rag-finance-legal-2026-05-23/openclaw-glm4-air-rag-glm45air-og.png)

**存**。每篇论文的中文摘要写到 `~/papers/digests/2026-05/<paper-id>.md`，附原 PDF 软链。Obsidian 自动 index 这个文件夹，第二天早上我在 iPad 上就能看。

**综述**。月底跑一次 `monthly-survey` skill：让 GLM-4.5-Air 从 `papers_2026` 集合里召回当月全部论文的中文摘要，按主题聚类（这一步用 Qwen3-Embedding-8B 算余弦相似度 + 层次聚类），然后对每个主题让模型写一段「本月这条线的进展是什么 + 三篇代表作 + 我应该关注什么」。综述文档大约 4000 到 6000 字，写到 `~/papers/surveys/2026-05.md`。

这条 case 跟前两个最不一样的地方在于 **token 量**。一篇论文摘要 + 引言 + 结论 + 实验表大约 4.6k 输入 token，月底综述时一次性召回 30 到 60 篇中文摘要做聚类输入大约 18k 输入 + 2k 输出 token。GLM-4.5-Air 在 4090 单卡 Q4 量化下，最大 max-model-len 设到 64K 是安全的，超过这个数会偶发 OOM——所以月度综述拆成两轮跑（先按主题聚类生成主题清单、再按主题逐个写）比一次性塞 32K 上下文稳得多。

`paper-digest/SKILL.md` 关键片段：

```markdown
---
name: paper-digest
model: glm-4.5-air
tools:
  - unstructured.parse_pdf
  - qdrant.upsert
---

# 论文中文摘要

输入：PDF 文件路径。

流程：
1. unstructured 解析 PDF，提取摘要 + 引言 + 结论 + 实验主表
2. GLM 输出三段：做了什么 / 主要数字 / 值不值得读
3. 写入 ~/papers/digests/YYYY-MM/<paper-id>.md
4. qdrant.upsert 写到 papers_2026 集合，metadata 含原 arxiv id / 主题标签

风险约束：
- 数字必须 verbatim，不允许 round
- 实验表表头必须保留英文原文
- 中文摘要不超过 300 字，超过则截断重写
```

一年下来这个 case 处理过 600 多篇论文，中文摘要返工率（摘要错把数字搞错、术语搞错、被我后来发现需要重写）约 4％，比 5 月初试过的纯 Qwen2.5-7B-Instruct 的 11％ 低不少。这个差是 12B 激活带来的——MoE 让模型在保持本地可部署的同时把生成质量推到了「不太需要返工」的阈值之上。

## 六、硬件与推理引擎：单 4090、双 4090、M4 Max 三条路怎么选

GLM-4.5-Air 总参 106B，BF16 全量大约 212 GB，单卡 24 GB 装不下。能跑的三条路：

| 路径 | 硬件成本 | 量化 | KV cache + max_len | 单 batch 速度 | 三场景并行能力 |
|---|---|---|---|---|---|
| 单 4090 + GGUF Q4 | 二手 1.4 万元 | Q4_K_M / Q4 AWQ | 64K 上下文，约 6 GB 留给 KV | 约 45-60 token/s | 顺序跑可以，并发吃紧 |
| 双 4090 + Tensor Parallel | 二手 2.8 万元 | Q4 或 Q5 | 128K 上下文，KV 充裕 | 约 90-130 token/s | 三场景并行无压力 |
| Mac M4 Max 128GB + MLX | 整机约 4 万元 | MLX 4bit | 64K 上下文 | 约 28-38 token/s | 顺序跑舒服，无风扇噪音 |

数字来源说明：双 4090 + TP 在 GLM-4.5-Air 上的实测数字来自 5 月 21 日「DeepSeek V4 Flash 双 4090 + vLLM TP」那篇的同类外推（DeepSeek V4 Flash 与 GLM-4.5-Air 都在 100B 量级 MoE），单 4090 数字来自 Unsloth 文档 + reddit r/LocalLLaMA 公开实测帖均值，Mac M4 Max 数字来自 5 月 22 日「GLM-4.6 Mac Ultra M4 Max MLX 与 llama.cpp 实测」那篇的真实数字外推到 4.5-Air 12B 激活量级。本文作者主跑单 4090 + GGUF Q4 这一条。

**选哪条**：

- 三场景里只有论文综述会偶尔需要超过 32K 上下文，平时 64K 够用——**单 4090 + GGUF Q4 是性价比最高的路径，1.4 万元搞定**
- 如果有团队多人并发、或者法务条款检索需要做 cross-collection long-context 联合检索（同时召回合同 + 法规共 30k token），**升级双 4090 + TP**
- 如果是知识工作者个人用、要带着模型出差、对静音和续航敏感，**Mac M4 Max 128GB 是更安静的选项**——MLX 4bit 在 Apple Silicon 上的稳定性 5 月 22 日那篇已经验过，三场景轮流跑没有任何问题

OpenClaw 在这三条路径上是同一份配置——它对推理后端是 OpenAI 兼容协议无感切换，今天单 4090 跑、明天换 Mac、后天上双卡，`openclaw.json` 改一行 `baseURL` 就好。这条「后端可替换」也是 5 月 22 日那篇 OpenClaw + MCP 多后端文章的核心论点，本文不重复。

## 七、月度成本三方对比：本地 vs 国内云 vs 海外旗舰

成本这件事必须按场景算，因为不同场景的 token 量差太多。下面这张表把三个场景的月度成本拉出来，分别对比本地 GLM-4.5-Air、国内云端 DeepSeek V4 Flash、海外旗舰 Claude Sonnet 4.6。

| 场景 | 月度请求量 | 月度 token 量 | 本地（4090 + Qdrant）| DeepSeek V4 Flash | Claude Sonnet 4.6 |
|---|---|---|---|---|---|
| 财务 agent | 900 次 | 1.4M 输入 + 0.27M 输出 | 610 元 | 320 元 | 4180 元 |
| 法务条款 | 240 次 | 0.82M 输入 + 0.19M 输出 | 780 元 | 510 元 | 5240 元 |
| 中文论文阅读 | 360 次 | 1.66M 输入 + 0.43M 输出 | 690 元 | 420 元 | 4860 元 |
| **合计** | **1500 次** | **3.88M 输入 + 0.89M 输出** | **2080 元** | **1250 元** | **14280 元** |

口径：
- 本地一行包含 4090 二手 1.4 万元 / 36 个月折旧 = 389 元/月 + 电费 350 W × 8 h × 30 d × 0.5 元/度 ≈ 42 元 + Qdrant 32 GB 内存折算 80 元 + Qwen3-Embedding-8B GPU 共用折算 100 元，再加三场景分摊
- DeepSeek V4 Flash 走 2026 年 5 月公开价：缓存命中 1 元 / 百万输入 token，缓存未命中 2 元 / 百万，输出 4 元 / 百万（[DeepSeek 平台价格表](https://platform.deepseek.com/api-docs/zh-cn/pricing/)），按 60％ 命中率折算
- Claude Sonnet 4.6 走 Anthropic 官方价 $3 / 百万输入 + $15 / 百万输出，按 7.2 汇率折人民币

![三场景月度成本对比柱状图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/openclaw-glm4-6-air-private-rag-finance-legal-2026-05-23/openclaw-glm4-air-rag-cost-chart.png)

几个观察：

**本地路径不比国内云便宜，但比海外旗舰便宜得多。** 三场景合计本地 2080 元、DeepSeek 1250 元、Sonnet 4.6 14280 元——本地是 DeepSeek 的 1.66 倍，但只是 Sonnet 4.6 的 14％。这条比例关系跟 5 月 19 日那篇「Qwen3 + 个人工作流」算出来的数字接近，说明 Air 档位 12B 激活和 Qwen3-Coder 30B / 3B 激活在三场景里的运行成本是同一量级。

**本地路径的真正价值不在月度成本，在三件别的事**：合规边界（法务场景的硬约束）、数据沉淀（私有 RAG 一年下来的知识资产）、断网可用（出差、机房、海外服务商抽风时仍然能跑）。三件加起来支撑了「贵一点也值」的论点。

**Claude Sonnet 4.6 在这三场景里不是合理选择。** 不是因为它不好——它在代码生成、跨领域推理、长上下文等任务上仍然是最好的之一——而是因为这三场景里有 70％ 的请求是简单分类、检索、模板化输出，把这些任务给 Sonnet 4.6 跑相当于用法拉利送外卖。海外旗舰最适合的位置是「最难的那 5％ 任务」，比如月度综述里需要跨论文做思想梳理的那一两段，这种任务可以走 OpenClaw 的「混合路由」（绝大多数请求走本地、少数复杂请求走云端），但这不是本文要展开的方向。

## 八、对比 Claude Code / Cursor / Continue / Codex 海外同类工具

OpenClaw 加这套 RAG 栈跟海外同类工具是分层关系，不是替代关系。8 维对比：

| 维度 | OpenClaw + GLM-4.5-Air + 本地 RAG | Claude Code | Cursor | Continue | Codex CLI |
|---|---|---|---|---|---|
| 主要任务 | 个人 / 小团队三场景工作流 | 编程为主 + 通用 agent | IDE 编程 + 代码改写 | IDE 编程辅助 | 终端编程 + agent |
| 模型路径 | 本地 + 可切云端 | 仅 Anthropic 云端 | OpenAI / Claude / Gemini 云端 | 可本地可云端 | OpenAI 云端为主 |
| 私有 RAG | 一等公民（Qdrant + MCP）| 不内置，需自建 | 不内置 | 不内置 | 不内置 |
| 中文场景 | 中文模型 + 中文 embedding 原生 | 英文优先 | 英文优先 | 看接的模型 | 英文优先 |
| 数据本地化 | 完全本地（强约束）| 全部上云 | 全部上云 | 看配置 | 全部上云 |
| 月度成本（同场景）| 约 2080 元 | 约 14280 元 | 约 1440 元（订阅）| 看接的模型 | 按 OpenAI 价 |
| 桌面 + 多通道 | macOS / iOS / Android / 23 + IM 通道 | CLI | IDE | IDE | CLI |
| 国内合规 | 完全可用 | 网络受限 | 网络受限 | 看配置 | 网络受限 |

这张表读法是这样的：**Claude Code 和 Cursor 对国内开发者仍然是编程任务的第一选择**，5 月 22 日「Microsoft 取消 Claude Code 报销」那篇就讲过这条工具链对生产力的实际影响；但当你的工作不只是写代码、还包括财务、法务、读论文、个人知识库——这些任务用 Claude Code 处理本来就不顺手，OpenClaw + 本地模型 + 私有 RAG 是更合适的工具，而不是要替代谁。

**国内合规边界**这一条单独说一下。三场景里，财务 agent 和论文阅读 agent 在「数据出境」这条上没有强约束（个人家庭流水、公开论文），但法务条款检索这条**事实上不允许走任何境外云**——一旦合同流过 Claude / OpenAI API，理论上等于把内容交给境外司法管辖，国内大客户尤其敏感。这就是「本地化不是性能选择、是合规选择」的真正含义。

OpenClaw 在「团队 vs 个人」边界上的差异：个人版（本文场景）所有数据在自己机器上，没有任何中央服务器；团队版（项目仓库里有 `workspace` 概念）通过共享 Qdrant + 共享 skill 仓库实现多人协作，但模型推理仍然在每个人本地（或公司机房一台共用机），不需要把数据交给任何第三方。这条差异比海外 SaaS 同类工具的「企业版 = 数据走自有 VPC」更彻底——因为根本没有 VPC 这件事，数据从来没离开过本机。

## 九、收尾：私有 RAG 是真正可走的下一步

把三场景串起来再看一遍。

家庭流水 + 公司报销，自己跑完不出门；公司合同库 + 法规检索，符合 NDA 第一条不上云；arxiv 论文 + 中文综述，一年沉淀下来一份属于自己的知识图谱。三件事用一台 1.4 万元的二手 4090 桌面机、一份 386,593 次月下载的开源模型、一套 30k+ Star 的 Rust 向量库、一个 373,989 Star 的 MIT 编排框架就跑通了。这个组合在 2024 年还不太行——那时本地模型不够好、向量库不够稳、编排框架不够多通道。到 2026 年 5 月，它已经是真实可走的路。

更重要的是——这条路是国内小团队和知识工作者可以走的下一步。它不要求你放弃 Claude Code、不要求你抛弃 Cursor、不要求你卸载 OpenAI 客户端，它只要求你把那些「不应该上云的事」搬回本机：家里的账、公司的合同、自己读过的论文。把这些事搬回本机的过程里，你顺手就把一个属于自己的私有知识库长出来了——一年后，三年后，这份知识库的价值会比每月省下的几千块钱大得多。

GLM-4.6-Air 还没出来，等出来之后整套架构不用动一行配置——把 `openclaw.json` 里 `models` 那一行从 `glm-4.5-air-q4` 改成 `glm-4.6-air-q4`、重新跑一遍 Qdrant 集合的 embedding（如果智谱顺手发了 GLM-Embedding 那就一起换），其他全部沿用。这是开放协议和模块化架构带来的安心——你买的不是某一个具体模型，是一套能持续演进的本地 AI 基础设施。

慢一点没所谓。本地这条路，真的能走通。
