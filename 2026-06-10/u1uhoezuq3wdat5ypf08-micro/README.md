# Snowflake发布CoCo等矩阵，为何说它开始“致敬”Anthropic？

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-10/u1uhoezuq3wdat5ypf08-micro/head.png)

Snowflake 的股价在过去一个月翻了一倍。这家云数据仓库公司市值重回 600 亿美元区间，但它的 CEO Sridhar Ramaswamy 在 2026 年 6 月的 Summit 上几乎没有花时间庆祝这件事。他把整场 Platform Keynote 的基调定在了一个更激进的位置——不是“数据平台又多了几个 AI 功能”，而是“你的 AI 原生企业从这里开始”。

这句话出自 Snowflake 产品 VP Christian Kleinerman 的口中，原文更直接：

![图片](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-10/u1uhoezuq3wdat5ypf08-micro/content-1.webp)

> Your AI-native enterprise starts here.

![图片](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-10/u1uhoezuq3wdat5ypf08-micro/content-2.webp)

如果这是一家 AI 原生公司的发布会，这句话只是及格线。但 Snowflake 过去十年建立的认知太牢固了：存算分离、弹性扩展、Zero Copy Clone、Data Sharing、Snowpark。它是一家数据基础设施公司，不是 AI 公司。当这样一家企业开始用 AI 重新组织自己的产品体系时，它不是在追热点，它是在换赛道。

InfoQ 中国 AI 在 6 月 10 日的报道里完整记录了这场 Summit 的实感。作者郭炜以 Snowflake 雪花大使身份参会，他是 Apache 基金会成员、白鲸开源 CEO，做了二十年数据工程。他的现场判断很干脆：**Snowflake 已经不满足于继续被定义为一家 Data Warehouse 公司，它要把自己变成企业里的 AI + Data 平台，甚至和 Anthropic 开始竞争。**

**Goodbye Data, Hello AI——不是数据不重要了，而是数据平台的表达方式彻底变了。**

## CoCo、CoWork、Desktop：一场对 Anthropic 的“致敬”暴露了新野心

郭炜在文章里反复提到一个词：“致敬”。他说 Snowflake 这次发布的产品矩阵——CoCo、CoWork、Desktop、Skill Catalog、MCP、ACP、Cloud Agents——让他觉得 Snowflake 在“致敬” Anthropic。

这个判断需要拆解。Anthropic 过去两年做的事情，本质上是把 Claude 从一个聊天机器人扩展为一整套 AI 工作系统：Claude Code 面向开发者，Desktop 进入用户桌面环境，MCP 协议连接外部工具和数据源，Artifacts 承载生成结果，Skills 封装可复用的能力模块。它们瞄准的不是一个问答入口，而是未来人和软件协作的主界面。

Snowflake 这次发布的组件，几乎可以一一对应。CoCo 的前身是 Cortex Code，一个帮数据工程师写 SQL 和修 Pipeline 的代码助手。但在 Summit 现场，Christian Kleinerman 正式宣布改名：

> From here on, no more Cortex Code. It is officially Snowflake CoCo.

改名本身不是新闻。但 CoCo 在改名同时扩展到了 MCP、ACP、SDK、Agent Teams、Cloud Agents、自动化能力和 Skill Catalog。Skill Catalog 尤其关键——它允许用户分享、发现、复用 Skills，本质上是在把 Snowflake 的平台能力模块化、工具化、Agent 化。这已经不是“帮数据团队写代码”的产品逻辑了，这是“让 Agent 在数据平台上自由调用工具”的逻辑。



![Snowflake CoCo 产品架构示意，从 CLI/Web 扩展到 MCP/ACP/SDK/Skill Catalog 的放射状能力图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-10/u1uhoezuq3wdat5ypf08-micro/schematic-1.png)



更让我愣神的是 CoWork 这个产品。郭炜说他一开始听到 CoWork 时是困惑的——CoCo 帮数据工程师写 SQL、修 Pipeline，这很好理解；但 CoWork 和数据仓库有什么关系？听完介绍他才理解：CoWork 是给业务人员用的。CEO、销售、运营、市场人员直接用自然语言与企业数据对话，像拥有自己的 Jarvis 一样获得洞察。Samsung 在现场给出了一个案例：CoCo 是数据工程师和开发者的 AI 操作入口，CoWork 是业务人员的 AI 工作入口。

这就是 Anthropic 的打法——不是让用户迁移到你的界面，而是让你的 Agent 进入用户的工作流。Snowflake 还宣布将推出 CoCo 的 Excel 插件和 VS Code 扩展。Excel 是业务人员最熟悉的数据工作台，VS Code 是开发者最熟悉的工作台。CoCo 不需要所有人进入 SnowSight 控制台，它进入用户原本工作的地方。



![CoCo vs CoWork 双入口对比表，左侧开发者/数据工程角色，右侧业务人员角色，中间共享 Snowflake 统一数据底座](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-10/u1uhoezuq3wdat5ypf08-micro/schematic-2.png)



这让我想起郭炜文章里那句总结：**CoCo 重构后台的数据工程，CoWork 重构前台的业务决策。两者结合，Snowflake 才能从数据平台变成企业 AI 工作平台。**

## AI 把所有软件公司重新拉回同一起跑线

郭炜在 Summit 现场有一个瞬间印象很深。Snowflake 发布了 Agentic Control Plane，简称 ACP。他当时心里一惊——白鲸开源上个月刚发布了同名的 ACP 产品。他的第一反应是：“这不撞车了么？大厂直接下场，我岂不是完蛋了？”

后来仔细听完，发现两者并不完全一样。Snowflake 的 ACP 更偏向内部的数据建模、Text-to-SQL、Semantic Layer，以及 Agent 对 Snowflake 数据的理解和调用。白鲸开源做的更多是 ETL、Orchestration、Pipeline、数据同步、任务调度，以及异构数据系统之间的执行与治理。他赶紧把产品名字前面加上了“Data Engineering”，然后才是 Agent Control Plane。

但重点不在于两者是否完全一样，而在于它说明了一件事：**大家都看到了同一个方向——未来的软件系统必须变成 Agent 可以理解、调用、编排和治理的系统。** 过去不同软件公司的差距来自品牌、客户、渠道、工程规模、产品成熟度。但 AI 到来以后，一个非常有趣的变化出现了：所有软件都要重新适配 AI。过去的交互方式是人打开界面、人点击按钮、人写 SQL、人看报表。未来的交互方式可能是：人提出目标，Agent 理解上下文，调用工具，生成方案，执行任务，反馈结果。

面对 AI，每个软件都要重新做一遍。大厂有历史包袱——复杂的旧系统、旧客户、旧架构、旧组织流程。创业公司如果认知足够快，反而可以从一开始就按照 Agent-native 的方式设计产品。郭炜说这是他这次看 Snowflake 最受鼓舞的地方：Snowflake 这样的大公司在这个月发布的东西，他们上个月也在类似方向上做了自己的探索。虽然规模不同、场景不同、产品深度不同，但对趋势的判断几乎是同时的。



![大厂 vs 创业公司在 AI 时代的产品重构起跑线对比，左侧历史包袱（旧系统/旧客户/旧架构），右侧 Agent-native 新设计](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-10/u1uhoezuq3wdat5ypf08-micro/schematic-3.png)



这个视角反过来看 Snowflake 的转身，就更有意思了。Snowflake 把自己的竞争对手瞄向 Anthropic，从 Data Warehouse 走向 AI Data Platform。但郭炜抛出了一个值得琢磨的问题：**未来 Data+AI，到底是掌握 Data 的人是入口，还是掌握 AI 的人是入口？**

他自己的判断是：数据是难以搬走的，AI 平台反而是很容易切换的。Snowflake 的数据底座已经在全球几千家企业里跑了十年，那些 PB 级的结构化数据、半结构化数据、Iceberg 表、共享数据和治理策略，不是 Anthropic 一纸合同就能迁移的。但 Claude、CoCo、CoWork 这些 AI 入口，用户切换起来几乎没有物理成本——只要一个 API Key 和一个新界面。

所以 Snowflake 的赌注就变得很清晰了：它赌的是“AI 必须长在数据上”，而不是“数据可以搬到 AI 上”。如果这个赌注成立，那它和 Anthropic 的竞争就不是同维度的——Anthropic 要从 AI 往下沉到数据层，Snowflake 要从数据往上长到 AI 层。谁的成本更低、谁的路径更短，还要看接下来两年的落地速度。

郭炜在文章最后给了一个个人判断：Snowflake 竞争的是 AI 的入口，如果这个 AI 入口模式成功，未来它的股价不止过去一个月翻一倍。他说这话时加了一句“纯属个人看法，不是投资建议”，但判断的底层逻辑是清楚的——Snowflake 正在把自己从一家数据仓库公司，重新讲述成一家 Enterprise AI Platform 公司。而市场给数据仓库公司的估值倍数，和给 AI 平台公司的估值倍数，是完全不同的。

这场 Summit 最好的注脚，可能是郭炜那篇报道的标题：**Goodbye Data, Hello AI。** 不是告别数据，而是告别旧的数据叙事方式。过去我们谈数据平台，谈的是怎么存、怎么算、怎么共享、怎么治理。现在 Snowflake 谈的是 AI 如何理解企业数据，Agent 如何使用企业数据，业务人员如何用自然语言直接获得洞察。数据还是那些数据，但入口从 SQL 变成了 Agent，用户从数据工程师变成了全公司的每一个人。

Snowflake 把 Slogan 定成了“Make AI Real for Business”。它要做的不是又一个聊天机器人，而是把过去十年积累的数据底座，变成企业 AI Agent 唯一可信的上下文引擎。这个转身如果成功，它竞争的不只是 Databricks 或者 Google BigQuery——它竞争的是未来企业软件的主界面。那个界面今天还握在 OpenAI 和 Anthropic 手里，但 Snowflake 显然不打算让他们赢得太轻松。

#Snowflake #Summit #Goodbye #Data #Hello

![图片](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-10/u1uhoezuq3wdat5ypf08-micro/content-3.webp)
