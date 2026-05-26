# 混合Gemini、Claude与OpenAI，Honcho打造推理优先的记忆系统

> ai-daily · 2026 年 5 月 25 日 21:47 · 来源：GitHub Trending python

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/plastic-labs-honcho-micro/head.png)

凌晨三点，旧金山某共享办公空间里，一个开发者正对着屏幕上的 Claude Code 终端发呆。他的编码 Agent 已经连续工作了四小时，却始终记不住他十分钟前刚说过的技术栈偏好——每次新开一个 session，Agent 就像被格式化了硬盘，一切归零。

他需要的不是更长的 context window。他需要的是一个真正能"记住事情"的大脑。

![plastic-labs/honcho — Memory library for building stateful agents](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/plastic-labs-honcho-micro/content-1.png)

就在同一天，GitHub Trending Python 榜单上，一个名为 **Honcho** 的项目正在快速攀升。点开 README，第一句话就戳中了这个行业的集体痛点：*"Memory infrastructure for building stateful agents."*

**Agent 失忆症，是 2025 年最昂贵的技术债务。**

## 不是 RAG，不是向量搜索——Honcho 在玩一套完全不同的游戏

大部分人听到"Agent 记忆"，第一反应是 RAG（检索增强生成）或向量数据库。把聊天记录 embed 成向量，存进 Pinecone 或 pgvector，查询时做语义搜索——这套方案在过去两年已经被用烂了。

Honcho 做的事完全不同。它的核心不是"检索相似文本块"，而是**推理优先的记忆系统**（Reasoning-first memory）。换句话说，它不会把原始对话切片后原样吐给你，而是在后台异步地对消息推理，提取出关于每个参与者的结论（Conclusions），然后构建一个动态演化的"Peer 表示"（Peer Representation）。

让我愣神的是这个设计里的一个词：**Peer-centric model**（对等中心模型）。在 Honcho 的世界里，人类用户和 AI Agent 是平权的——都是"Peer"。系统不仅追踪"这个人类用户喜欢什么"，还会建模"Agent A 对用户 B 的理解到了什么程度"。当一个 session 里有多个 AI Agent 和多个人类用户混在一起时，Honcho 能回答这样的问题：*"Tutor Agent 目前对 Alice 的数学薄弱点了解多少？"*

这已经不是在管理对话历史了。这是在模拟社会认知。



![Honcho 的 Peer 模型示意，人类用户和 AI Agent 作为对等节点，彼此间的认知关系用连线标注](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/plastic-labs-honcho-micro/schematic-1.png)



Honcho 的架构被拆成两个服务：Storage（同步 API，管理 Workspace / Peer / Session / Message）和 Insights（异步后台队列，由 Deriver 工作进程消费）。当你调用 `session.add_messages()` 存入消息后，Honcho 不会立刻给你结论。它把推理任务扔进队列，由后台的 Deriver 慢慢消化——更新表示、生成摘要、构建 Peer Card。然后你随时可以用 Chat Endpoint 用自然语言提问，比如 *"这个用户对什么学习方式反应最好？"*，Honcho 会返回一个融合了长期事实和当前上下文的回答。

这套流水线的底层用了多 LLM 提供商的混合策略。根据 README 里的自托管配置说明，默认情况下：Google Gemini 负责 deriver、summary 和低/中级 dialectic 推理；Anthropic Claude 负责高/最高级 dialectic 和 dream 处理；OpenAI 负责 embedding（当 `EMBED_MESSAGES=true` 时）。三个大厂的模型在同一个系统里各司其职——这本身就是一种务实的工程选择。



![Honcho 推理流水线架构图，Message → Queue → Deriver Worker → Gemini/Claude/OpenAI 分流处理 → Conclusions/Representations/Peer Cards 四个输出](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/plastic-labs-honcho-micro/schematic-2.png)



Honcho 的基准测试页面上挂了一个很有野心的声明：*"Honcho has defined the Pareto Frontier of Agent Memory."* 它声称在 LongMemEval、LoCoMo 等长对话基准上达到了帕累托前沿。我没有看到具体数字（Evals 页面和博客里应该有，但原文片段没给），不过"帕累托前沿"这个措辞本身就很有意思——它暗示的不是"我们比所有竞品都好"，而是"在记忆质量和推理成本之间，我们找到了没人达到过的平衡点"。

## 从 Claude Code 到 Hermes，Honcho 正在把自己塞进每一个 Agent 的脑子里

Honcho 最激进的动作不是技术架构，而是它的集成策略。我数了一下，README 里列出的集成方式至少有 8 种：

- Claude Code 有两种接入方式：Plugin（更深度集成，推荐）和 Raw MCP（兼容所有 MCP 客户端，包括 Cursor、Cline、Windsurf）
- OpenCode 有自己的插件 `@honcho-ai/opencode-honcho`
- OpenClaw 有独立插件，甚至能无损迁移旧的 MEMORY.md / USER.md / IDENTITY.md 文件
- Hermes 直接在 `hermes memory setup` 里把 Honcho 列为可选后端
- 任何 MCP 客户端都可以用标准 HTTP transport 接入
- 还有一个叫"agentic development guide"的集成技能（`npx skills add plastic-labs/honcho`），能自动探索你的代码库、询问集成偏好、生成 SDK 配置并验证可用性

**一个记忆库，正在试图成为整个 Agent 生态的"默认海马体"。**



![Honcho 的集成生态地图，中心是 Honcho Server，周围辐射出 Claude Code / OpenCode / OpenClaw / Hermes / MCP Clients / Custom SDK 六条线](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/plastic-labs-honcho-micro/schematic-3.png)



这个策略让我想起 Vercel 当年的打法——不跟 AWS 正面竞争基础设施，而是用"部署体验"这个入口，让前端开发者再也离不开它。Honcho 也在做类似的事：不跟 Pinecone 竞争向量存储，不跟 LangChain 竞争编排框架，而是用"Agent 记忆"这个窄切口，卡进每一个编码 Agent 的 workflow 里。

更有意思的是它的商业模式。托管服务 `api.honcho.dev` 用 workspace 隔离 + 免费额度（新注册送 $100 credits），自托管则完全开源（AGPL-3.0 协议）。对于不想把用户数据交给第三方的企业，自托管只需要一个 Postgres 数据库（带 pgvector 扩展）加上三个 LLM API Key 就能跑起来。README 甚至贴心地提供了不依赖 Docker 的本地开发流程：`uv sync` → 配环境变量 → `alembic upgrade head` → 启动 FastAPI server + Deriver worker。

这种"托管优先但自托管友好"的策略，在 2025 年的开源 AI 基础设施项目里越来越常见。Plastic Labs（Honcho 背后的团队）显然想得很清楚：先用托管服务抓个人开发者和早期创业团队，等他们长大了，自然会选择 self-host 或者买企业版。$100 免费额度刚好够开发者跑通 demo 并产生依赖——这个定价心理学玩得很精准。

Honcho 的 Python SDK（`honcho-ai` on PyPI）和 TypeScript SDK（`@honcho-ai/sdk` on npm）版本独立于服务端，这意味着它们可以各自迭代而不互相阻塞。SDK 里暴露的 API 表面设计得相当干净：`peer.chat()` 用于自然语言查询，`session.context()` 返回 prompt-ready 的上下文包（直接链式调用 `.to_openai()` 或 `.to_anthropic()` 就能喂给模型），`peer.representation()` 提供低延迟的静态快照（适合对响应时间敏感的场景）。

内部存储机制也值得提一嘴。Honcho 在底层用 `(observer, observed)` peer pair 作为 key 来组织向量化的文档集合。当 observer 和 observed 是同一个 peer 时，构建的是自我认知；当它们是不同的 peer 时，构建的是交叉认知（"Tutor 眼中的 Alice"）。这些集合不直接暴露给 API，而是用 Conclusions API 作为公共接口——这层封装让底层存储引擎可以从 pgvector 平滑迁移到 turbopuffer 或 lancedb（配置文件的 `[vector_store]` 段支持这三种后端切换），而不影响上层应用。

写到这儿我突然意识到一件事：Honcho 的设计哲学里藏着一个对当前 AI Agent 范式的隐性批评。几乎所有主流 Agent 框架都在拼命往 context window 里塞东西——更长的历史消息、更多的 tool output、更详细的 system prompt。Honcho 的回应是：**别塞了，让 Agent 学会"记住"而不是"翻阅"。**

一个能记住你三个月前说过"我不喜欢太正式的回复风格"的编码 Agent，和一个每次都从零开始读对话历史的 Agent，用户信任度会是天壤之别。Honcho 在 README 里用了"data moats"这个词——给 Agent 加上记忆层，就是在构建数据护城河。用户离开你的产品越久，迁移成本越高，因为竞争对手的 Agent 没有关于这个用户的记忆。

这就回到了开头那个凌晨三点的开发者。如果他给 Claude Code 装上 Honcho 插件，他的编码 Agent 不会再问"你之前说用哪个框架来着？"——它会在后台默默消化每一条消息，更新对"这个开发者"的认知模型，然后在需要的时候，安静地把最相关的上下文注入 prompt。

当然，Honcho 还远不是完美的。异步推理意味着新消息需要"一小会儿"才能反映到查询结果里（README 自己也标注了这个延迟），对于实时性要求极高的场景，只能用 `representation` 端点拿低延迟快照。而且 AGPL-3.0 协议对 SaaS 公司来说是个需要法务审查的点——如果你的产品是对外提供 Honcho 托管服务的竞品，这个协议可能会让你不舒服。

但不管怎么说，一个把"记忆"从 RAG 的泥潭里拉出来、重新定义为"推理驱动的动态认知建模"的项目，出现在 2025 年 5 月的 GitHub Trending 上，本身就是对行业现状的一记响亮耳光。Agent 们终于有可能不再金鱼脑了——至于它们记住的东西会不会让人类更头疼，那就是另一个故事了。

## 参考来源
- https://github.com/plastic-labs/honcho
- https://api.honcho.dev
- https://app.honcho.dev

#Memory #AI #科技
