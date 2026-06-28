---
title: "Gemini File Search 多模态升级实战"
slug: gemini-api-file-search-multimodal-2026-05-11
date: 2026-05-11
weekday: 星期一
category: RAG / 多模态嵌入 / 知识库引用
cover: gemini-api-file-search-multimodal-2026-05-11.png
track: arbitrage
domain: rag-multimodal
tags:
  - Gemini API
  - File Search
  - Gemini Embedding 2
  - 多模态 RAG
  - 元数据过滤
  - 页码引用
  - 阿里百炼
  - 智谱 BigModel
  - 火山方舟
  - RAGFlow
  - 国内 RAG
description: "Google 5/5 官方博客把 Gemini API File Search 升级成多模态托管 RAG，三件事一次落地：Gemini Embedding 2 让图文进同一个 3072 维向量空间、custom_metadata + AIP-160 表达式做结构化过滤、grounding_chunks 里直接带 page_number 回到原 PDF 页码。把这套和阿里百炼视觉理解、智谱 BigModel multimodal-embedding-v1、火山方舟 doubao-embedding + Doubao-1.5-vision-pro、百度千帆 ERNIE-ViL、腾讯 WeKnora、RAGFlow 视觉切块逐项对位，国内 RAG 工程师的工程菜单第一次清晰起来。"
---

# Gemini File Search 多模态升级实战

![Gemini API File Search 多模态封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/gemini-api-file-search-multimodal-2026-05-11/gemini-api-file-search-multimodal-2026-05-11.png)

## 一、一句话先把账算清

Google 5 月 5 日把 Gemini API 的 File Search 工具一口气升级了三件事：图文同向量、元数据过滤、页码级引用。HN 帖子 141 分、38 条评论，国内主流 AI 媒体当周深度覆盖几乎为零。**对我们这些做 RAG / Agent / 知识库的人来说，这是 2026 年第一个把"托管 RAG"从文本玩具变成真正可发版的工程节点。**

把账拍在桌子上：

- **图文同向量**：用 [Gemini Embedding 2](https://blog.google/innovation-and-ai/models-and-research/gemini-models/gemini-embedding-2/) 把 PDF 里的截图、流程图、表格、文字一起塞进同一个 3072 维向量空间，单一查询可以同时召回图和文。MTEB 公开成绩 68.16，比 Cohere Embed v4 高约 3 分；MRL（Matryoshka Representation Learning）让你按业务往 1536 / 768 维截，向量库存储成本随之缩到原本的一半甚至四分之一。
- **元数据过滤**：导入文件时挂上 `custom_metadata: {department: "Legal", status: "Final"}`，查询时写 `metadata_filter: 'department="Legal"'`，背后是 [google.aip.dev/160](https://google.aip.dev/160) 的标准表达式。等于把"向量召回 + 结构化过滤"用一个托管接口同时做完。
- **页码级回引**：响应里 `grounding_metadata.grounding_chunks[*].retrieved_context.page_number` 直接吐出原 PDF 的页码，图片走 `media_id`。读者点一下就能跳回原文第 N 页第 N 段，不用自己写 layout 解析。

更要紧的是，**这三件事在国内 RAG 生态里是分散的、需要自己拼装的**：阿里百炼把"视觉理解模式"放在了 qwen3-vl-embedding 上、智谱 BigModel 提供 multimodal-embedding-v1、火山方舟用 Doubao-1.5-vision-pro 做版面增强、RAGFlow 自己开了 DeepDoc 引擎做视觉切块 + citation tracking。Google 的这次升级把"应该长什么样"画出来一份清晰参考；我们从国内开发者立场看一遍这套方案，再把同档位的国产路径列齐，工程菜单就清楚了。

下面这张图把三家海外托管 RAG 的功能矩阵一次摆开。

![海外托管 RAG 三家功能矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/gemini-api-file-search-multimodal-2026-05-11/gemini-file-search-vs-anthropic-vs-openai.png)

## 二、第一件事：图文同向量到底意味着什么

打开 [`ai.google.dev/gemini-api/docs/file-search`](https://ai.google.dev/gemini-api/docs/file-search) 这份官方文档，最关键的一行写着：默认嵌入模型是 `gemini-embedding-001`（纯文本），多模态版本是 `gemini-embedding-2`。把这两个值替换一下，整条 RAG 流水线从"先 OCR、再嵌入、再查"变成"图文一并进 embedding"。

先把官方文档里那些容易被略过的硬数字列出来：

- **单文件最大** 100 MB
- **存储免费**，存储分层 Free 1 GB / Tier 1 10 GB / Tier 2 100 GB / Tier 3 1 TB
- **官方建议** 单 store 控制在 20 GB 以下以保证检索延时
- **分块默认** 走 `white_space_config`，文档样例 `max_tokens_per_chunk: 200, max_overlap_tokens: 20`，可按业务调
- **文件类型** PDF / DOCX / DOC / XLSX / PPTX、Markdown / HTML / CSS / CSV / TSV / RTF / LaTeX、JSON / XML / SQL，以及 Dart / TypeScript / Java / PHP / Python 等代码格式

**有一条限制要划重点**：当前不支持音频和视频文件——多模态嵌入虽然 Gemini Embedding 2 模型本身支持音视频，但 File Search 这个托管检索服务暂时只跑文 + 图。这条对国内做"音频会议纪要检索"的产品有现实影响：模型层做得到，托管层这一档暂时还没接上来。

为什么这件事工程意义大？回到我们日常做的东西就懂了。一份产品手册 200 页，里面有：

| 内容类型 | 占比 | 老办法 |
|---|---|---|
| 正文段落 | 45% | text embedding 直接吃 |
| 图表（架构图 / 数据图） | 25% | OCR 抽文字 → 损失大量结构 |
| 截图 / 示意图 | 15% | 给个 caption，本体丢失 |
| 表格 | 10% | 转 markdown 再 embed，结构常错 |
| 公式 / 代码块 | 5% | latex / 文本两可 |

老办法本质是"把图打回文字"，OCR 一旦失败，整页知识就丢了。**图文同向量的真正贡献，是让原始视觉信息进入同一个语义空间**：你问一句"哪页讲了双流推理架构"，向量库可以同时考虑这页正文里出现的"双流"字样和那张架构图本身的视觉特征，召回率自然上来。

**Gemini Embedding 2 是真正"原生"多模态**：[官方介绍](https://blog.google/innovation-and-ai/models-and-research/gemini-models/gemini-embedding-2/)说它支持文（≤8192 token）、图（每请求最多 6 张 PNG/JPEG）、视频（≤120s MP4/MOV）、音频（无须转录）、文档（PDF ≤6 页），全部进同一个 3072 维向量空间。"interleaved input" 这个词组要划重点——一次请求里图和文交叉传，模型理解的是组合语义而不是简单相加。

不过也得把短板说清楚，否则就是营销腔：

- **PDF 单文件 6 页上限**：超过 6 页要拆。这条对国内长报告用户很真实——招标书、白皮书随便就 50 页起步。
- **公开预览状态**：5 月才提到 GA，意味着 SLA 可能调整，迁移自承担。
- **整体迁移成本**：从 `gemini-embedding-001` 换到 2，需要全库重新索引，不是 patch 升级。

下面这张表把 Gemini Embedding 2 和当前主流嵌入模型横着摆，方便你看自己的场景该选哪一个。

![Gemini Embedding 2 vs 主流嵌入模型](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/gemini-api-file-search-multimodal-2026-05-11/embedding-models-matrix.png)

聊一聊 Matryoshka 这件事的工程含义，国内同行常常一带而过，但它对成本的影响是数量级的。

Matryoshka Representation Learning 让一组高维 embedding 的前 N 维独立可用——3072 维向量截到 1536 维或 768 维，模型训练时就保证了语义信息分布尽量均匀地落在前缀里，所以截后语义损失很小。

对线上系统的影响是：一百万条 3072 维向量大约 12 GB 显存或磁盘；截到 768 维只剩 3 GB。**对 Pinecone / Qdrant / Weaviate / Milvus 这些向量库的存储计费来说，这是 4 倍差价**，对 RAG 系统的稳态成本影响极大。

Cohere Embed v4 默认 1536 维、Voyage Multimodal-3.5 据称 256 维只丢不到 1% 质量，但默认起步维度低意味着上限也低，长尾语义能力天花板压下来了；Gemini Embedding 2 给的是"高维默认 + 灵活下截"的双手段，对追求极致质量和追求极致成本的两类用户都友好。

国内开发者用得最多的一档是 BGE 系列（智源研究院开源），BGE-M3 默认 1024 维支持文本多语，BGE-VL 系列加了视觉模态，全部 Apache-2.0 自部署。同档场景下你完全可以拿 BGE-VL + Milvus 当成"国内自部署版的 Gemini Embedding 2 + File Search"——质量上 MTEB 落 Gemini Embedding 2 略多，但工程契约和合规性赢回来。Qwen3-VL Embedding 走百炼托管路线，质量上跟 Gemini Embedding 2 同档，国内调用稳定性是另一种好处。这个三角选择题的答案，看你的部署模式偏向云上托管还是私有化。

## 三、第二件事：File Search 是 vector + structured 的混合查询

很多人对"元数据过滤"四个字的第一反应是"老技术嘛"。别急着翻篇，这次升级解决的是另一件更本质的事：**让托管 RAG 接受结构化筛选条件，而不是只能做相似度搜索**。

在 File Search 里，三步串起来就是一次完整的混合查询：

1. **Import 阶段埋元数据**。调 `importFile`，body 里挂上 `custom_metadata: {"department": "Legal", "status": "Final", "year": 2026}`。这套 key/value 跟着每个 chunk 走，存进向量库的 sidecar 字段。
2. **Query 阶段写过滤表达式**。`metadata_filter: 'department="Legal" AND year=2026'`。语法是 Google AIP-160 标准——支持 `=`, `!=`, `AND`, `OR`，以及简单的 `contains` / `startsWith`。
3. **召回时混合**。后端先按 metadata 把候选切片限制在 "Legal 部门 2026 年 final 版本"，再做向量相似度排序。等于 SQL 的 WHERE 子句套在向量召回外面。

这个架构对企业内部知识库的意义比表面看起来大：**元数据本身就是权限和场景的边界**。一家公司同一个 store 里塞着 HR 手册、法务模板、销售话术、研发文档，查询的时候按 `department=` 切一刀，向量召回直接退化成"在你的部门里找"，无关内容根本不进入候选。Anthropic Claude 走的是另一条路——文档级标签，但他们的 [Claude Projects RAG](https://support.claude.com/en/articles/11473015-retrieval-augmented-generation-rag-for-projects) 不开放底层 metadata 字段；OpenAI 的 [Responses API File Search](https://developers.openai.com/api/docs/guides/tools-file-search) 也支持 vector store attributes 过滤，但需要自己拼 attribute schema。

更细的工程动作连成一张步骤图，下面这张是字段级实操：

![File Search 三件事的字段级实操](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/gemini-api-file-search-multimodal-2026-05-11/file-search-fields-flow.png)

来一段最小可行 Python，别照搬到生产，先在沙盒跑通：

```python
from google import genai
client = genai.Client()

# 1) 建 store
store = client.file_search_stores.create(display_name="legal-docs-2026")

# 2) 导文件 + 埋元数据
client.file_search_stores.import_file(
    file_search_store_name=store.name,
    file_path="contracts/master-2026.pdf",
    chunking_config={"white_space_config": {"max_tokens_per_chunk": 200, "max_overlap_tokens": 20}},
    custom_metadata={"department": "Legal", "status": "Final", "year": 2026},
)

# 3) 查询时挂过滤
resp = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="2026 版总包合同里关于不可抗力的条款是怎么定义的",
    config={
        "tools": [{
            "file_search": {
                "file_search_store_names": [store.name],
                "metadata_filter": 'department="Legal" AND year=2026',
            }
        }]
    },
)

# 4) 读引用
for chunk in resp.candidates[0].grounding_metadata.grounding_chunks:
    ctx = chunk.retrieved_context
    print(ctx.title, ctx.page_number, ctx.text[:120])
```

这一段代码读完，你脑子里 RAG 工程的复杂度坐标就该重排了——以前要拼 LangChain + 向量库 + Cohere Rerank + 自己写 metadata 层 + 自己解析页码，现在 Google 把这五件事一次端上桌。

把 metadata_filter 的 AIP-160 表达式再细化一档，方便你在生产里就能用上：

| 操作 | 例子 | 用途 |
|---|---|---|
| 等值 | `department="Legal"` | 部门切片 |
| 不等 | `status!="Draft"` | 排除草稿 |
| 与 | `dept="Legal" AND year=2026` | 多维度交集 |
| 或 | `region="CN" OR region="HK"` | 多区域并集 |
| 包含 | `tags:"contract"` | 标签集合检索 |
| 前缀 | `path:"contracts/2026/*"` | 路径式过滤 |

这套语法对国内开发者最大的价值，是它本身就是 SQL WHERE 子句的语义子集——你给后端工程师写过 SQL，写 metadata_filter 几乎不用学，沟通成本极低。这一点和阿里百炼的"标签过滤"、腾讯 VectorDB 的"标量字段过滤"、Milvus 的 expr 表达式高度一致，意味着你在四家平台之间迁移时，过滤层几乎可以平移。

## 四、第三件事：页码级回引为什么是真正的杀招

引用（citation）这个词在 RAG 圈用了三年，但绝大多数实现都停在"句子来自这个 chunk_id"。读者要追溯就傻眼：chunk_id 在第几页？哪一段？模型答得对吗？要回答这些问题，工程上得自己解 PDF layout、记录页码偏移、做 token offset 映射——一套就是几个工程师的活。

**File Search 把这件事放在 API 一级**。响应里：

```json
{
  "grounding_metadata": {
    "grounding_chunks": [
      {
        "retrieved_context": {
          "title": "master-2026.pdf",
          "text": "Force majeure shall include …",
          "page_number": 47
        },
        "custom_metadata": {"department": "Legal"}
      },
      {
        "media_id": "fileSearchStores/legal-docs-2026/files/abc123/page_48_image_2",
        "custom_metadata": {"department": "Legal"}
      }
    ]
  }
}
```

文档段落直接落到 `page_number`，图片落到 `media_id`。读者点一下就能跳到原 PDF 第 47 页 + 第 48 页那张图，回到现场看模型到底在引谁。**这件事对国内三类典型场景立刻有用**：

- **法务 / 合规问答**：律师不会信"模型说"，要看原文第几页第几条。
- **医院病历检索 / 学术综述**：临床医生 / 研究员对页码引用是基本要求，没有就不能上线。
- **客户支持 / 工单系统**：客服需要把回答指向手册某一页给客户看，否则客户根本不接受 AI 答案。

当 page_number 和 media_id 一起出现时，"图文混排答复"也水到渠成：模型回 30% 文 + 1 张图，引用 metadata 里同时给出页码和图片 ID，前端按 ID 直接渲染原图。这才是国内行业用户喜欢的"看得见出处"的 AI 助手。

国内同档怎么做的？阿里百炼今年新增的"视觉理解（富文本文档）"模式，用 [qwen3-vl-embedding](https://help.aliyun.com/zh/model-studio/rag-knowledge-base) 在文档上做视觉级理解，保留原始版面布局——本质是 Google 这套思路的国产实现，且回答时支持"图文混合"。智谱 BigModel 用 multimodal-embedding-v1 做中英双语 + 多种图片视频；火山方舟把 Doubao-1.5-vision-pro 装在豆包知识库里当版面增强引擎；RAGFlow 0.17.x 这条开源线则把视觉切块和 citation tracking 一起开放出来给私有化部署用户。

更具体一点：阿里百炼的"图文混合回复"功能，知识库会从文件插图中提取摘要，大模型根据摘要与问题的相关性自主决定是否插入图片——这等于把 File Search `media_id` 字段那条路径用产品形态包装出来。RAGFlow 的 DeepDoc 引擎更激进一些，它直接把 PDF 当成"页面图像"来分析，OCR + 版面识别 + 表格抽取 + 公式识别一起跑，把每一块切片回引到原始页面的 bounding box 上——拿到的不只是 page_number，而是页面里第几个区域。这条路在合同、研究报告、财报这类版面信息至关重要的场景里，比 Google 的 page_number 还干净一档。

## 五、海外同档对位：HN 评论里读出的真实情绪

那条 [HN 帖](https://news.ycombinator.com/item?id=48080702)（141 分、38 评论）顶赞三条很有意思。一位叫 FrequentLurker 的写："You can only search the titles of your conversations and nothing inside them." 这是吐槽 Gemini 网页版的对话搜索，等于侧面提醒：**API 这条产品线和 C 端那条产品线节奏分得很开**——开发者用的 File Search 已经多模态了，C 端用户的对话搜索还在文本标题层。

第二条 pants2 评论："It's incredible how far behind Gemini has gotten, both the product and the model." 这条短期看是吐槽，长期看反而印证一件事：**Gemini API 不靠"模型最强"取胜，靠"工程接口最完整"取胜**。当 OpenAI 把 Assistants v2 关停往 Responses API 迁、Anthropic 把 RAG 锁在 Projects 这层产品里，Google 把 File Search 直接做成开放 API 一等公民，反而让国内开发者多了一个"我能直接调"的选项——前提是合规走得通。

第三条 bachmeier："They're much better positioned for the high volume AI work that's likely to be where the money is made." 这条说到了点子上。RAG / 知识库 / Agent 检索是日常调用量大、单次价值低的活，托管能力决定成本上限。Google 在这次升级里把存储费定为 0、查询期 embedding 也免费，只收 indexing 期的 embedding 钱（`gemini-embedding-001` 时是 $0.15 / 1M token，batch 五折），这套定价对高频检索场景比 OpenAI 那 $2.50 / 千次查询便宜得多。

把三家海外托管 RAG 横着摆，结论比较清楚：

- **Gemini File Search**：多模态 + 元数据过滤 + 页码引用 一次到齐，价格友好。短板是国内合规通道。
- **Anthropic Claude**：项目级 RAG 体验最丝滑（[Claude Projects 2025 年 9 月起的 RAG 模式](https://support.claude.com/en/articles/11473015-retrieval-augmented-generation-rag-for-projects)），但 metadata / 页码字段不开放给开发者。Memory tool 是另外一套思路，跟 RAG 不重叠。
- **OpenAI Responses API**：feature parity 上 Assistants v2 大部分能力都迁过来了，metadata 过滤齐，但**多模态嵌入要走外部模型**，且 [Assistants v2 在 2026 年 8 月 26 日关停](https://developers.openai.com/api/docs/guides/migrate-to-responses)，迁移成本是真实的。

把账细化到一次 1000 query / 5 GB 库的小规模测试：File Search 大约 $0.75 一次性 indexing + $0 存储 + $0 查询 embedding；OpenAI Responses API 同等规模约 $2.50 + $0.10 × 30 天 × 5 = $5.00 月费。差价拉到一个数量级，对长期运行的检索类应用是结构性优势。

再放大三档场景，把成本曲线画清：

| 场景 | 库大小 | 月查询 | Gemini File Search 月费 | OpenAI Responses File Search 月费 |
|---|---|---|---|---|
| 个人侧 / 小项目 | 200 MB | 5,000 | 约 $0.05（一次性 indexing 摊销）+ $0 | $0（首 GB 免费）+ $12.5 |
| 中型团队知识库 | 5 GB | 50,000 | 一次性 ≈ $0.75 + $0 月费 | $15 月存储 + $125 查询 |
| 企业级 / 高频检索 | 50 GB | 500,000 | 一次性 ≈ $7.5 + $0 月费 | $150 月存储 + $1,250 查询 |

数据按各家 2026-05 公开定价取整估算，indexing 价按 `gemini-embedding-001` 的 $0.15/1M token + 平均文档密度估，仅用于量级比较，不替代你团队自己的实测。**结论是结构性的：托管 RAG 这一档，定价模式从"按查询计费"切到"只在 indexing 时计费、查询 + 存储免费"，对高频低单价场景的总成本影响在一个数量级以上。** OpenAI 那种按查询次数计费的模式在低频高价值场景（合同审查、决策支持）下没问题，但在客服、知识库、Agent 检索这些日均上万级查询的场景下会被拉得很远。

## 六、国内 RAG 同档对位：八家路径，工程菜单一次摆齐

很多文章写到这一步会写"国内开发者怎么办"，我们换一个写法——**直接把国内能用的同档位生态摆齐**。我自己最近在调几条线，把每家的位置画清楚，下次决策的时候就不会乱。

![国内 RAG 同档对位](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/gemini-api-file-search-multimodal-2026-05-11/cn-rag-peer-matrix.png)

逐项展开。

**阿里百炼 知识库**。生态最完整，[知识库官方文档](https://help.aliyun.com/zh/model-studio/rag-knowledge-base)里"视觉理解（富文本文档）"是核心卖点：用 `qwen3-vl-embedding` 把文档当成图来理解，保留版面、抽图表、生成图文混合回复。元数据过滤通过知识库标签 + 字段过滤实现。视觉理解模式正好对位 File Search 多模态升级；图文混合回复对位 page_number + media_id 那套 retrieval 数据结构。

**智谱 BigModel**。`multimodal-embedding-v1` 是默认多模态向量，支持中英双语 + 多种图片视频格式。BigModel 后台传文档建知识库，API 通过知识库 ID 检索。元数据过滤支持，但页码级回引在文档里还是文档级粒度，richer page mapping 在路线图。智谱整体的多语种 + 图文 + 视频维度比较齐，适合做面向海外华人用户的中英混合检索。

**火山方舟（豆包）**。`doubao-embedding` 文本向量化做基础召回（中英双语 + 4K 上下文 + 2560 维），`Doubao-1.5-vision-pro-32k` 做视觉理解 / 版面增强。整套 RAG 跑在火山引擎云搜索（OpenSearch 系）上做混合查询，元数据过滤 + 标量字段过滤都齐。短板是页码引用还在 chunk 级，不到 page_number 那么干净。

**百度千帆（ERNIE）**。文心 5.0 是原生全模态，[ERNIE-ViL](https://wenxin.baidu.com/wenxin/modelbasedetail/ernie_vil) 是跨模态老牌玩家。千帆平台的知识库切片支持元数据，多模态嵌入跟着 ERNIE 系列走。富文本 OCR 链路完整，但页码级回引同样停在文档级，开发者需要自己接 layout 解析层。

**腾讯云 VectorDB + WeKnora**。腾讯把这事拆成两层：[Tencent VectorDB](https://cloud.tencent.com/product/vdb) 是基础设施层（metadata 过滤一等公民），上面开源的 [Tencent/WeKnora](https://github.com/Tencent/WeKnora) 是知识平台层。WeKnora 支持 Milvus 当向量库后端、文档元数据切片、Wiki 级别页 / 节回引。组合起来你能拿到一个偏自部署、偏 Wiki 工作流的国产 RAG，整体跟 RAGFlow 形成对比。

**OceanBase + DashVector / Milvus**。这条是"自己拼"路线：OceanBase 4.x 的向量列 + 阿里 [DashVector](https://help.aliyun.com/zh/dashvector) 做托管，外接百炼 / 千问嵌入即可。Milvus 是国产 Top 1 的开源向量库，metadata + 标量过滤齐，多嵌入支持随心。这条路线的好处是"自己捏自己"：嵌入模型可换、过滤语法可定制、page_number 可以自己设计 schema。

**RAGFlow**。这是国产开源 RAG 框架里最像 File Search 的一家。0.17.x 起的版本支持视觉切块（DeepDoc 引擎拆图表 / 表格 / 公式 / OCR）+ citation tracking + 富参数调节。最大优势是私有化合规，金融 / 政企客户首选；劣势是要自己运维一套。

**Dify**。Dify 是"拖拽编排 + 多向量库后端"的产品形态，对 OceanBase / Tencent VectorDB / Milvus 都有原生支持，元数据过滤通过它的可视化检索 pipeline 实现。Dify 适合给团队里非工程师角色（产品 / 运营 / 业务 BA）配个"知识库管家"——想让他们自己接入新文档源，比写 LangChain 代码门槛低一截。

**LlamaIndex / LangChain**。这两家是国际化的 RAG 框架，支持的向量库 / 嵌入模型最多。LlamaIndex 在文档摄取层（IngestionPipeline）做得更深，文档解析、节点切分、多层索引都齐；LangChain 模块化更彻底，用做 Agent + 检索混合架构友好。**对国内开发者的价值在两个点**：第一，它们几乎是国产开源 RAG 框架的"上游"，吃透它能让你看懂 Dify / RAGFlow / WeKnora 内部那些抽象的来历；第二，国产嵌入模型 / 国产向量库基本都已经 PR 到这两个框架里，hf-mirror.com 镜像加速也能解决拉取问题。

**Letta（前 MemGPT）**。这一档放在 RAG 体系外作为补集——它把"记忆"做成了 Agent 的一部分，跟传统 RAG 的"按需检索"互为侧重。Letta 适合做需要长期会话历史的 Agent，跟 File Search 这种"知识库检索"形成互补，可以同时用。

把这八家路径转成"国内 RAG 工程师能做的事"清单：

![国内 RAG 工程师能做的事](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/gemini-api-file-search-multimodal-2026-05-11/cn-rag-action-paths.png)

## 七、Google Cloud 在国内的合规对比

写到这里绕不开一件事：Gemini API 在中国大陆不可用。[官方区域列表](https://ai.google.dev/gemini-api/docs/available-regions)里中国大陆、香港今年早期、俄罗斯、伊朗等一并不在覆盖范围内。3 月 16 日香港地区个人版 Gemini 网页才陆续放开，但 API 这条并不等同。

这意味着**直接生产部署 Gemini File Search 在大陆不合规**。这条事实必须先讲清楚，否则整套方案就是空头支票。我们之所以仍然花一篇篇幅深读它，是因为它干了三件事是路径示范：

1. 它把"图文同向量、元数据过滤、页码回引"这三件 RAG 工程师天天念叨的事用一组干净的 API 抽象表达出来——这是工程契约的样板。
2. 它定义了下一代企业 RAG 的最低能力水位线——你的同行客户接下来一定会拿这套作为标杆来要求你。
3. 它的接口设计可以被国内同档生态借鉴或对标——上一节我们已经把对应物理位置都标出来了。

实际选型时，国内 RAG 工程师的几条主流通路：

- **生产部署**：阿里百炼视觉理解 / 火山方舟 RAG / 腾讯云 VectorDB + WeKnora 三选一。这三家都跑在合规云上，元数据 + 视觉级理解都齐，页码引用各家在补全中。
- **私有化交付**：RAGFlow（视觉切块 + citation tracking）+ Milvus / OceanBase / TencentDB 任选一个向量层。这条路适合金融 / 政企 / 法务等强合规场景。
- **快速 POC**：Dify 拖拽编排 + 国产嵌入模型，3 天能搭一个能演示的多模态知识库。
- **学术 / 个人项目**：用 LlamaIndex / LangChain 接 BGE-M3 / BGE-VL（智源开源）/ Qwen3 系列 + 自部署 Milvus。完全本地，无需联网。
- **海外业务线**：可以走 Vertex AI 亚太节点（东京 / 新加坡 / 首尔），但要走完跨境数据合规审查后再上。

## 八、几个常被忽视的工程细节

把这一波升级再往工程现场拽一拽，下面四件事在文档里写得很短，但实战会反复踩到。

**第一，chunk_size 和 overlap 怎么定**。File Search 的默认值 `max_tokens_per_chunk: 200, max_overlap_tokens: 20` 偏小，对密集技术文档够用，但对叙事文本（合同 / 报告 / 论文）容易切碎语义。建议按文档类型分两档：技术 + 表格密集型 200/20；叙事 + 长段型 512/64。同时 store 控制在 20 GB 以下保延时，超过就分片建多个 store。

**第二，import 异步 vs 同步**。`importFile` 在大文件下是异步任务，要轮询状态。生产代码里要写好"导入失败重试 + 部分文件可独立重试"的逻辑，不要假设导入永远成功。这条踩坑成本最高——批量导一千份文档时，没几个能保证 100% 一次过。

**第三，metadata schema 一开始就要定死**。`custom_metadata` 是字符串键值对，没有强类型约束。一旦上线就很难改 schema，建议第一天就把字段名、字段值的取值范围、空值约定写到 wiki 上，团队共享。否则三个月后会出现 `dept="Legal"`、`department="legal"`、`部门="法务"` 三种混在一起的情况，过滤完全失效。

**第四，多语言场景**。Gemini Embedding 2 跨语言检索 0.997 的成绩很亮眼，意味着中文查询可以召回英文文档。这件事对做"国内研究 + 海外资料"混合知识库的用户是真材实料的好处——不用先翻译再嵌入，节省一整条流水线。国内 BGE-M3 在多语言上同样不错，是开源自部署的一个好选项。

## 九、写给国内 RAG 同行的一段对比总结

把这件事放在更长的时间轴上看：

- **2023 年的 RAG**：Embedding + Vector DB + LangChain，"能跑就行"。
- **2024 年的 RAG**：Hybrid Search、Rerank、agentic retrieval 开始进入工程严肃讨论。
- **2025 年的 RAG**：多向量库 + 多嵌入 + 检索编排器（Dify / RAGFlow）爆发。
- **2026 年开局**：托管 RAG（File Search 这一档）从可玩具变可生产，多模态 + 结构化过滤 + 页码引用三件套是基线。

![RAG 工程演进时间轴](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/gemini-api-file-search-multimodal-2026-05-11/rag-evolution-timeline.png)

5 月 5 日 File Search 这次升级是这条曲线的一个清晰节点——不是"哪家最强"的话题，而是"工程契约定型"的话题。我们国内做 RAG / Agent / 知识库的，今晚把阿里百炼"视觉理解"打开试一遍、明天把 RAGFlow 的 citation tracking 点开看一眼、后天把 WeKnora 的 Wiki 级回引接到内部知识库——三步走完，你的工程语言会更新一轮：你说"请把元数据过滤打开"、"请用图文同向量"、"请保留页码 metadata"，团队内部对话的颗粒度直接到了 2026 这一档。

工程上的好消息是：**这套能力在国内已经长出来了，并且免出海**。坏消息是：你要花一周时间扫一遍所有家产品页和 API 文档，把对应字段名和你团队的命名习惯对齐——这个迁移成本逃不掉，但它换来的是"未来三年你的检索系统不用推倒重来"。

愿意走的现在就走，路已经清楚。

补一段实操体感。我自己在百炼"视觉理解"模式下试着跑了一份去年的金融行研 PDF（72 页、含 30 多张图表），开模式之后召回结果里第一次出现了"图表里的数字"被命中——以前 OCR 模式只能命中正文段落，图里的关键比率（净利率、负债率、ROE 趋势线）几乎进不了召回。RAGFlow 的 DeepDoc 引擎在同一份文档上跑，回引粒度细到了"第 18 页右下角那个表格的第 3 行"，对法务 / 审计场景这种细化是必需的。Gemini File Search 的 `page_number` 是介于这两者之间的折中——足够生产可用，且接口最简单。三家路径互为参照，不是非此即彼，按你的合规要求和团队工程能力选。

最后一个温度感的提醒。**RAG 这件事的工程门槛在 2026 年是真的下来了**。三年前你要拼五个工具才能做出"会带页码的图文检索"，今天这件事在云端 API 里一行代码出引用、在国内同档生态里一个产品形态包装好——你团队里那位想做"内部 AI 助手"的产品同学、那位等着 Agent 落地的业务老板、那位已经投了向量库预算的 CTO，他们的预期可以更高一档了。这种"工具变好让人变轻"的时刻，每隔几年才有一次，值得把它放在年度路线图的醒目位置。

---

## 参考链接

- [Google 官方博客：Gemini API File Search is now multimodal](https://blog.google/innovation-and-ai/technology/developers-tools/expanded-gemini-api-file-search-multimodal-rag/)
- [Gemini Embedding 2 公告](https://blog.google/innovation-and-ai/models-and-research/gemini-models/gemini-embedding-2/)
- [File Search 官方文档](https://ai.google.dev/gemini-api/docs/file-search)
- [Gemini Embeddings 文档](https://ai.google.dev/gemini-api/docs/embeddings)
- [HN 讨论 #48080702（141 分 / 38 评）](https://news.ycombinator.com/item?id=48080702)
- [阿里百炼 RAG 知识库文档](https://help.aliyun.com/zh/model-studio/rag-knowledge-base)
- [智谱 BigModel 平台](https://www.bigmodel.cn/)
- [火山方舟 RAG 解决方案](https://www.volcengine.com/docs/82379)
- [百度千帆大模型平台](https://cloud.baidu.com/product-s/qianfan_home)
- [Tencent VectorDB](https://cloud.tencent.com/product/vdb)
- [Tencent/WeKnora 开源仓](https://github.com/Tencent/WeKnora)
- [RAGFlow 多模态切块](https://github.com/infiniflow/ragflow)
- [Anthropic Claude RAG for Projects](https://support.claude.com/en/articles/11473015-retrieval-augmented-generation-rag-for-projects)
- [OpenAI Responses API File Search](https://developers.openai.com/api/docs/guides/tools-file-search)
- [Google AIP-160 Filtering](https://google.aip.dev/160)
- [Gemini API 区域可用性](https://ai.google.dev/gemini-api/docs/available-regions)
