# 解决 Claude Code 会话痛点：claude-mem 让 AI Agent 拥有持久记忆

> ai-daily · 2026 年 5 月 27 日 02:47 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/thedotmack-claude-mem-micro/head.png)

2026 年 5 月某个周三凌晨两点，纽约布鲁克林一间公寓里灯还亮着。一个独立开发者刚让 Claude Code 跑完第 47 轮 debug，token 消耗已经冲到 120 万，窗口濒临崩溃。他盯着终端里那条熟悉的红色提示——"会话即将结束，建议你总结当前进度以便下次继续"——深吸一口气，开始手动把过去四小时的上下文一条条粘贴进笔记软件。

这种事，做过 AI 辅助编程的人都懂。Agent 跑得正欢，突然因为会话断开，之前建立的所有"默契"归零。你得像教实习生一样，从"我们昨天做到哪儿了"重新开始。

![thedotmack/claude-mem — Persistent Context Across Sessions for Every Agent – Cap](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/thedotmack-claude-mem-micro/content-1.png)

三天后，这个开发者在 GitHub Trending 上看到了一个叫 claude-mem 的项目。Star 数不算爆炸，但 README 第一句话就让他瞳孔放大——"Captures everything your agent does during sessions, compresses it with AI, and injects relevant context back into future sessions."

**上下文是 AI Agent 时代的石油，而 claude-mem 在修输油管道。**

## 一个插件，七把钩子：claude-mem 到底在做什么

claude-mem 的架构设计透露着一股"老子受够了"的实用主义气息。作者 Alex Newman（GitHub ID: @thedotmack）没有搞什么花哨的 AGI 叙事，而是直接抛出了五条生命周期钩子：SessionStart、UserPromptSubmit、PostToolUse、Stop、SessionEnd——六段脚本，像手术钳一样精准嵌在 Claude Code 的每个关键节点上。



![claude-mem 五条生命周期钩子在 Claude Code 会话中的触发时序图，从 SessionStart 到 SessionEnd 的完整流程](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/thedotmack-claude-mem-micro/schematic-1.png)



这意味着什么？Agent 的每一次工具调用、每一次用户提示、每一次会话结束，都会被自动捕获。不是"请总结本次会话"这种被动机制，而是静默的全量记录。更关键的是，这些记录会经过 AI 压缩生成语义摘要，存进本地 SQLite 数据库，下一次新会话启动时自动注入相关上下文。

我注意到一个细节：claude-mem 的安装方式不是传统的 `npm install -g`。文档里专门强调，`npm install -g claude-mem` 只会安装 SDK 库，不会注册插件钩子或启动 worker 服务。正确姿势是 `npx claude-mem install`，或者在 Claude Code 内部用 `/plugin marketplace add thedotmack/claude-mem` 安装。

这个设计选择很有意思。插件系统本质上是"寄生"在宿主 IDE 的运行时里，需要深度访问 Claude Code 的内部事件总线。npm 全局安装做不到这点，必须用 npx 或插件市场触发完整的安装脚本。这透露出 Claude Code 的插件架构正在从"玩具级"往"生产级"演化——它开始允许第三方在生命周期层面介入，而不是只开放几个 API 端点。



![claude-mem 安装方式对比，npm install -g vs npx claude-mem install vs 插件市场安装，三种路径的能力差异](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/thedotmack-claude-mem-micro/schematic-2.png)



claude-mem 支持的宿主不止 Claude Code 一个。根据 README，它兼容 OpenClaw、Codex、Gemini、Hermes、Copilot、OpenCode 等一系列 Agent 框架。对 Gemini CLI 的安装甚至做了自动检测（`--ide gemini-cli` 会扫描 `~/.gemini` 目录）。这种跨平台野心，让我想起当年 VS Code 插件生态的早期阶段——谁先吃下最多的宿主，谁就能定义"Agent 记忆"这个品类的标准。

## 三层检索 + Chroma 向量库：不止是"记住"，更是"找得到"

如果说自动捕获上下文是 claude-mem 的肌肉，那它的检索系统就是大脑皮层。项目提供了四个 MCP 工具，遵循一套"渐进式披露"的三层工作流：

第一层 `search`，返回紧凑索引，每条结果只消耗约 50-100 token；第二层 `timeline`，围绕感兴趣的观测点拉出时间线上下文；第三层 `get_observations`，只对筛选后的 ID 拉取完整详情，每条消耗 500-1000 token。

官方文档用了一个数字来描述这个设计的效果：**约 10 倍的 token 节省**。在 AI Agent 的世界里，token 就是钱。Claude 3.5 Sonnet 的 API 价格是每百万输入 token 3 美元，输出 15 美元。一个重度用户一天跑 500 万 token 并不稀奇，如果能省下 90% 的记忆检索成本，一年下来能省出一台 MacBook Pro。



![三层检索工作流示意图，search → timeline → get_observations 的漏斗结构，标注每层 token 消耗](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/thedotmack-claude-mem-micro/schematic-3.png)



这套系统的底层依赖两个存储引擎：SQLite（用 FTS5 做全文搜索）和 Chroma 向量数据库（做语义搜索）。Chroma 的加入让 claude-mem 从"关键词匹配"升级到了"语义理解"——你可以用自然语言问"上个月那个认证 bug 怎么修的"，而不是记着具体的函数名或行号。

项目还内置了一个 Web Viewer UI，运行在 `http://localhost:37777`。这个端口号 37777，跟 Anthropic 官方 Claude Code 的默认端口 37776 只差一位数，不知道是巧合还是某种"官方-社区"的镜像暗示。更有趣的是，claude-mem 还提供了一个 `mem-search` 技能，可以从 Claude Desktop 直接查询记忆库，相当于把 Agent 的"大脑"外挂到了桌面端。

我特别注意到一个隐私控制的设计：用户可以在内容中插入 `<private>` 标签，标记不想被存储的敏感信息。这个功能说明 Newman 考虑到了企业场景——没有哪个 CTO 会允许所有 Agent 操作日志都被无差别记录，尤其是在处理密钥、用户数据或商业机密的时候。

## 一个 Solana 代币、一个 Beta 模式、一个社区生态的雏形

claude-mem 的 README 底部有一段让人猝不及防的内容：`$CMEM` 代币。这是一个 Solana 链上的 token，合约地址 `2TsmuYUrsctE57VLckZBYEEzdokUF8j8e1GavekWBAGS`。文档明确说，这个代币是第三方在未经 claude-mem 同意的情况下创建的，但作者 Alex Newman 事后"官方拥抱"了它，将其定位为"社区增长催化剂"和"实时 Agent 数据的分发载体"。

这种操作在开源工具类项目里极为罕见。通常开发者会对未经授权的代币发律师函，Newman 却反向操作，把它变成了项目的社区货币。是实用主义还是营销嗅觉？从 README 的措辞来看，Newman 的意图是把 $CMEM 跟"real-time agent data"绑定——如果 claude-mem 未来成为一个 Agent 记忆的事实标准，那么它产生的数据流确实有价值，而代币可以成为数据访问的结算层。



![claude-mem 生态构成图，核心插件 + MCP 工具 + Chroma 向量库 + Web Viewer + $CMEM 代币的关系](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/thedotmack-claude-mem-micro/schematic-4.png)



另一个让人浮想联翩的功能是 Beta 频道里的"Endless Mode"。文档描述它是"仿生记忆架构，用于延长会话"。虽然目前没有更多技术细节，但"Endless"这个词暗示 claude-mem 的野心不止于"跨会话记忆"——它在试图突破 Agent 的上下文窗口限制本身。如果 Claude Code 原生的 200K token 窗口是物理极限，那 claude-mem 的 Endless Mode 就是在尝试用存储换时间，把短期记忆和长期记忆打通。

项目用 Apache 2.0 许可证，README 里专门解释了选择这个协议的原因："durable agentic memory should be easy to embed in developer tools, local agents, MCP servers, enterprise systems, robotics stacks, and production agent harnesses." 这句话几乎把 AI Agent 的所有落地场景都列了一遍。Newman 不是在做一个 Claude Code 的小插件，他在赌一个假设：未来的 AI 代理都需要持久记忆层，而 claude-mem 想做那层基础设施。

从技术栈看，claude-mem 用 TypeScript 编写，依赖 Bun 做进程管理，uv 做 Python 包管理（用于向量搜索），SQLite 做持久化。整个技术选型透着一股"轻量但完整"的气质——没有引入 Postgres 或 Redis 这种重型依赖，一个本地单机就能跑完整套系统。这对于独立开发者和中小团队来说，意味着零运维成本。

目前项目的 GitHub Issues 区和 Discord 社区还处在早期阶段，但已经有人在讨论"怎么把 claude-mem 的记忆导出到 Notion""能不能给团队共享记忆库"这类问题。一个工具类项目如果开始催生用户自发的二次需求，通常意味着它切中了真实的痛点。

claude-mem 的故事让我想起一个悖论：AI Agent 越来越聪明，但它们的"健忘症"也越来越致命。每次会话断开就清零，像极了《记忆碎片》里的主角——能力超群，却记不住十分钟前发生的事。Newman 给这种状态装上了一个外挂硬盘，至于这个硬盘未来是变成 Agent 操作系统的标准组件，还是停留在"一个挺好用的插件"，取决于社区愿意为"记住"这件事付多少钱——或者买多少 $CMEM。

## 参考来源
- https://github.com/thedotmack/claude-mem

#Persistent #Context #Across #Sessions #Every
