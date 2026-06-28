---
title: "Datasette Agent 把对话式 SQL 接进本地 SQLite：和 Claude Code 一样问数据，但全程不出你的硬盘"
slug: datasette-agent-sqlite-local-2026-05-26
date: 2026-05-26
weekday: 星期二
category: 数据工程 / 对话式 SQL / SQLite / Datasette / 个人 AI 助手
cover: datasette-agent-sqlite-local-2026-05-26.png
track: arbitrage
track_score: 9.0
domain: datasette-agent-conversational-sqlite
tags: [Datasette Agent, SQLite, 对话式 SQL, Simon Willison, datasette-llm, Observable Plot, Claude Code, 千问, DeepSeek Chat, Code Interpreter, 本地数据分析]
description: "围绕 Simon Willison 5 月 21 日发布的 datasette-agent 在本地 SQLite 上做对话式 SQL：13 天 5 个 alpha 版本（0.1a0 → 0.1a4）一路迭代；安装命令一条 datasette install datasette-agent；默认模型走 datasette-llm 任意 provider（gpt-5.4-mini / Gemini 3.1 Flash-Lite / Claude / 本地 LM Studio 都能换）；配套 datasette-agent-charts 自动出 Observable Plot 图；权限拆 chat / explore / background 三档；和 Claude Code 跑 duckdb-mcp / DeepSeek Chat 上传 CSV / 千问 Code Interpreter 三种主流路线相比，最大区别是数据从头到尾不离开本地 SQLite 文件。"
authors:
  - Claude Opus 4.7（1M context）
  - 人类编辑：Can Wang
---

# Datasette Agent 把对话式 SQL 接进本地 SQLite：和 Claude Code 一样问数据，但全程不出你的硬盘

![Datasette Agent 封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/datasette-agent-sqlite-local-2026-05-26/datasette-agent-sqlite-local-2026-05-26.png)

## 30 秒速览

这周二把一个国内同行很容易错过的事情摊开看一遍：**Simon Willison 在 5 月 21 日正式发了 datasette-agent，一个让你用自然语言直接问本地 SQLite 数据库的对话式 agent**。从首个 alpha 0.1a0 到最新 0.1a4，**13 天 5 个 alpha**（2026-05-12 / 05-14 / 05-15 / 05-21 / 05-24），节奏比一般周更项目密得多。Datasette 主项目本身已经持续做了 9 年，stargazers 11,099、forks 842、最新版本 1.0a30 在 5 月 24 日刚发，新加的 `makeJumpSections` JS 插件钩子是给 agent 这一层留的视图扩展位。

把这一套和国内开发者每天熟悉的工具横着对一遍：

- **Claude Code 跑 SQL**：通常挂 duckdb-mcp 或自己写 sqlite 工具，能力强但每条查询都要把 schema 加结果送上 Anthropic 服务器
- **DeepSeek Chat 上传 CSV**：5 月起内测的 Code Interpreter 版本能跑 Python + SQL，体验顺，但文件要上传到深度求索的对象存储
- **千问 Code Interpreter**：阿里云百炼跑沙箱 Python，数据走阿里云
- **Datasette Agent**：装到自己电脑上一个 `datasette install datasette-agent`，访问 `/-/agent` 就是聊天界面，**SQL 直接打到本地 `.db` 文件，对话过程的 schema、数据、查询结果**从头到尾**不出本机硬盘**——LLM 那一头你想用什么就用什么，从 OpenAI 的 gpt-5.4-mini、Google Gemini 3.1 Flash-Lite，到 LM Studio 里的 gemma-4-26b-a4b，配置文件里改一行字符串就能换

这套东西最有意思的论点不是「比 Claude Code 强」——是**把"自然语言问数据"这件事从云服务的属性，重新变回桌面软件的属性**。Datasette 本身就是 9 年前 Simon 离开 Eventbrite 开始写的"个人探索性数据分析工具"；datasette-agent 把 LLM 接进来之后，**本地数据分析这条线第一次有了和云端聊天产品同档体验的开源选项**。

下面这一篇把 datasette-agent 当前 0.1a4 的能力、配套 datasette-agent-charts 自动画图、`makeJumpSections` 插件钩子的意义、和三个主流云端竞品的真实差异、国内同行三条接入方案，拆开讲清楚。

![Simon Willison 的 datasette-agent 演示截图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/datasette-agent-sqlite-local-2026-05-26/datasette-agent-hero-demo.jpg)

## 一、Datasette 9 年项目史 + Simon Willison 三年 LLM 库准备

要看懂 datasette-agent 为什么会在 2026 年 5 月这个时间点冒出来，得先看 Simon Willison 这 9 年在数据工具这条线做了什么。

Datasette 主项目最早 2017 年 10 月在 GitHub 上线，定位很朴素：**把任何 SQLite 数据库（包括 CSV / JSON 导入后的）一行命令变成可浏览、可查询、可分享 JSON API 的网站**。Simon 给自己的目标读者写得很清楚——data journalists（数据记者）、museum curators（博物馆策展人）、archivists（档案管理员）、local governments（地方政府）、scientists、researchers。9 年里这个项目积累到 11,099 stars / 842 forks，最新版本 `1.0a30` 在 5 月 24 日刚发，是个真活的项目，**不是一时心血来潮的副业**。

围绕主项目，Datasette 周边已经有 **44 个工具 + 154 个插件**——`datasette publish` 一行命令部署到 Google Cloud Run / Heroku / Vercel；`datasette-desktop` 是个 macOS 原生应用；社区里的插件覆盖图表、地图、全文搜索、Slack 通知等大量场景。

LLM 这条线 Simon 是 2022 年 ChatGPT 出来之后开始铺的：

- **2023 年**：发了 `llm` 这个命令行工具（截至 2026-05-17，stargazers 11,931 / 主仓库 simonw/llm），一条命令调 OpenAI / Anthropic / Google / 本地 Ollama 模型，统一日志格式存到 SQLite
- **2024 年**：把 `llm` 改成插件化架构，加 logs、conversations、templates 三层概念，让命令行用户的所有对话历史都进入本地 SQLite，能用 SQL 反查
- **2026 年初**：抽出 `datasette-llm`——专门给 Datasette 插件依赖的 LLM 集成层（截至 2026-05-19，stargazers 12，主仓库 datasette/datasette-llm），描述很直白：「LLM integration plugin for other plugins to depend on」
- **2026 年 5 月**：基于 `datasette-llm` 这一层，第一个真正面向终端用户的 agent 产品 `datasette-agent` 上线

这条铺垫线读出来的第一个观点是——**datasette-agent 不是把现成的 LLM 接口缝进来这么简单**，是 Simon 把过去三年自己用 LLM 写代码、查日志、查邮件、查个人记账数据的所有实战经验，沉到一个共用的 `datasette-llm` 底层，再上面长出来的产品形态。第二个观点是：**「LLM provider 切换」从一开始就是设计契约**——配置文件里改一行 `default_model: gpt-5.4-mini` 就能换到 Anthropic Claude 4.7 或者 LM Studio 里的本地模型，**没有任何一家 vendor 锁定**。

![Datasette 主项目 GitHub 卡片](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/datasette-agent-sqlite-local-2026-05-26/datasette-main-github-card.png)

## 二、datasette-agent 0.1a4 当前到底能干什么

5 月 12 日 0.1a0 首发到 5 月 24 日 0.1a4，13 天 5 个 alpha。版本节奏密度比一般周更项目高 2-3 倍，意味着这个项目当前处在「把 demo 改成可日用工具」的冲刺期。

### 安装与启动

安装就一条 Datasette 标准命令：

```bash
datasette install datasette-agent
```

启动 Datasette 之后访问 `/-/agent` 就是聊天界面。要让 agent 工作得先在 `datasette.yml` 里给 datasette-llm 配一个默认模型：

```yaml
plugins:
  datasette-llm:
    default_model: gpt-5.4-mini
```

这里的 `gpt-5.4-mini` 是字符串占位，背后由 `datasette-llm` 解析——可以替换成 `claude-4.7-sonnet`、`gemini-3.1-flash-lite`，也可以是 LM Studio 暴露的本地 `gemma-4-26b-a4b`，本地 Ollama 跑的 `qwen3:30b`，只要 `llm` 这一层有对应 plugin 都认。

### 三档使用形态

datasette-agent 内置三档使用形态，**不是一个聊天框打天下**：

| 形态 | 入口 | 适合的场景 |
|---|---|---|
| **Chat 聊天** | 浏览器访问 `/-/agent` | 实时和 agent 你来我往，看完一条结果再问下一条 |
| **Explore 自动探索** | 数据库或表的 action 菜单里点「Explore with AI agent」 | 让 agent 自己跑一遍这张表，输出探索报告，存到 `/-/agent/explore/` |
| **Background 后台任务** | `/-/agent/background` | 给一个目标长任务，agent 自己后台跑，跑完看结果，可中途点 Stop 取消 |

权限拆得也很细，**三档独立可授权**：

- `datasette-agent` — 聊天能力
- `datasette-agent-explore` — Explore 自动探索
- `datasette-agent-background` — Background 后台任务

`--root` 用户三档全有；普通账号可以只开 chat、不开 background，团队多人版用起来不至于一个 agent 把账单跑爆。

### 内置三个 SQL 工具

每次 agent 想查数据时，调的是三个内置工具，全部走本地 SQLite：

| 工具 | 作用 |
|---|---|
| `list_databases_and_tables` | 列出当前 Datasette 实例挂载的所有数据库和表 |
| `describe_table` | 看某张表的列、类型、外键 |
| `sql_query` | 跑一条**只读**的 SQL，把结果返给 agent |

`sql_query` 是只读的——这意味着 agent 不会改你的数据。要写入数据库得装别的插件、用 `register_agent_tools` 钩子注册带 `required_permission` 的写入工具，**写入权限默认关闭，这条边界很明确**。

### 命令行也能跑

不开浏览器也行，agent 有命令行模式：

```bash
datasette agent chat mydata.db
datasette agent chat mydata.db -m gpt-5.4-mini
datasette agent chat mydata.db -m gpt-5.4-mini -p "List all tables"
```

`-m` 选模型，`-p` 走单次 prompt 退出非交互。配合 shell pipeline 直接把 agent 输出写到文件，本地数据脚本写得起来。

![datasette-agent 仓库 OG 卡片](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/datasette-agent-sqlite-local-2026-05-26/datasette-agent-github-card.png)

## 三、5 天迭代时间线：从 0.1a0 到 0.1a4

13 天 5 个 alpha 这个节奏值得单独看一眼。

![datasette-agent 13 天 5 个 alpha 时间线](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/datasette-agent-sqlite-local-2026-05-26/datasette-agent-5days-timeline.png)

| 版本 | 时间 | 主要变化（综合 changelog + repo commits） |
|---|---|---|
| 0.1a0 | 2026-05-12 | 首个 alpha：chat UI + 三个 SQL 工具 + `datasette-llm` 集成 |
| 0.1a1 | 2026-05-14 | bug 修复 + UI 细节 |
| 0.1a2 | 2026-05-15 | 引入 `register_agent_tools` 钩子，外部插件可注册工具 |
| 0.1a3 | 2026-05-21 | Explore + Background 两档独立形态拼到位；Simon 当天发博客 |
| 0.1a4 | 2026-05-24 | 自定义 HTML 渲染 (`_html` 返回字段) + `agent-sql-edit-link` 样式 + 三个插件示例 |

从这个迭代节奏读出两个信号——**第一**，Simon 自己每天都在用这个工具，不然不可能这种密度地补功能；**第二**，0.1a4 加的 `_html` 字段是给配套插件准备的——agent-charts、agent-openai-imagegen 这一批插件都能用 `_html` 把图、图表、富文本嵌进聊天界面，**插件机制比单点功能更重要**。

## 四、三个使用场景：从「本地 CSV 分析」到「内网工具挂载」

datasette-agent 实际拿来用的三个最自然的场景：

### 场景 1：本地 CSV / Excel 分析（个人开发者高频）

你周末导出一份小红书 / 京东 / 自家电商的订单 CSV，想看一下「上半年哪个 SKU 复购率最高、客单价从 3 月到 5 月的变化」。

```bash
# 一行命令把 CSV 导入 SQLite
sqlite-utils insert orders.db orders orders.csv --csv
# 启动 Datasette + agent
datasette orders.db
```

浏览器打开 `/-/agent`，直接用中文问：「先看 orders 表有哪些字段，然后帮我算一下 SKU 复购率 top 10」。agent 自己调 `describe_table` 看 schema、写 SQL、跑结果、解释结论。**整个过程订单数据不离开你的电脑**——这对于含客户手机号、地址、消费记录的数据集是个硬需求。

### 场景 2：SQLite 周报 / 月报自动化

很多团队的指标数据本来就在 SQLite 文件里——日志切片、A/B 实验结果、爬虫抓的公开数据。过去做周报得手写 SQL、整理结果、画图。现在可以接 Background agent：

```bash
datasette agent chat metrics.db -m gpt-5.4-mini \
  -p "对比本周和上周的核心指标，输出周报 markdown" \
  > weekly_report_2026-W21.md
```

跑完直接拿到一份周报草稿，再人工校对一遍发出去。这条用法和 Claude Code 跑数据分析的体验接近，**只是数据全程不离开本地，而且模型可换到 DeepSeek / 千问 / Kimi 哪一家都能跑**。

### 场景 3：内网工具挂载

数据团队把 Datasette 部署成内网服务，挂多个 SQLite 文件（或通过 `datasette-connectors` 挂 PostgreSQL / DuckDB / MySQL 子集），团队成员用 SSO 登录后能用 agent 自助查数据。这种用法在数据记者团队、博物馆档案部门、地方政府数据开放部门已经有了多年的成熟实践——datasette-agent 加上去之后，**不用每次都依赖懂 SQL 的人**，对话式就能问数据。

权限拆三档这件事在这个场景里特别有用：分析师组开 `chat + explore`，运营组只开 `chat`，CTO 开三档全部。

![datasette-agent vs 三家主流云端竞品对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/datasette-agent-sqlite-local-2026-05-26/datasette-agent-vs-3-products.png)

## 五、和 Claude Code / DeepSeek Chat / 千问 Code Interpreter 真实对比

国内开发者每天接触的「让 AI 帮我查数据」入口主要有三个：Claude Code 跑 SQL、DeepSeek Chat 5 月起内测的 Code Interpreter、千问 Code Interpreter。把 datasette-agent 加进来对照看一遍：

| 维度 | Claude Code + duckdb-mcp | DeepSeek Chat Code Interpreter | 千问 Code Interpreter | Datasette Agent |
|---|---|---|---|---|
| **数据所在** | 本地，但 SQL 和结果送到 Anthropic | 上传到深度求索对象存储 | 上传到阿里云沙箱 | **完全本地 SQLite** |
| **模型 vendor 锁定** | 锁 Anthropic（Claude 4.x） | 锁深度求索（DeepSeek V4） | 锁阿里千问 | **任意 vendor 都行**（OpenAI / Anthropic / Google / 国产 / 本地） |
| **价格** | 包月 / token 计费 | 包月 / token 计费 | 包月 / token 计费 | **开源免费**，付的是你选的 LLM 价格 |
| **隐私** | schema + 结果走云端 | 文件 + 结果走云端 | 文件 + 结果走云端 | **数据从不出本机** |
| **离线可用** | 否 | 否 | 否 | **本地 LM Studio / Ollama 时是** |
| **支持图表** | 通过工具 | 内置 matplotlib | 内置 matplotlib | datasette-agent-charts (Observable Plot) |
| **支持图像生成** | 通过工具 | 暂无 | 文生图独立产品 | datasette-agent-openai-imagegen |
| **写入数据库** | MCP 工具开关 | 临时文件能写 | 临时文件能写 | 默认只读，要写得显式装插件 |
| **多用户权限** | 单用户 | 单账号 | 单账号 | **三档权限独立可授权** |

读这张表读出来的论点很清楚——**datasette-agent 不在「单点能力」上比云端产品强，它强在三件事的乘积上：数据完全本地 × LLM 任意可换 × 多用户权限分层**。这三件事在云端聊天产品里要么没有、要么不能同时全要。

对国内同行更现实的差别是「**LLM 任意可换**」这一条：今天你用 Anthropic Claude 跑 agent，明天 Claude 涨价或者服务不稳，配置文件里把 `default_model` 改成 `deepseek-v4-pro` 或者 `qwen3-max`，整个 agent 不用动一行代码继续跑。这种「**前端不动后端随便换**」的工程性质，是 Datasette 这套架构 9 年积累出来的能力，**对长期维护数据工具的人是真红利**。

## 六、datasette-agent-charts：让 agent 自己画图

5 月 21 日 Simon 同步发了三个配套插件，最值得单挑的是 `datasette-agent-charts`——给 agent 加一个 `render_chart` 工具，**让 agent 看完 SQL 结果之后自己决定要不要画图、画什么图**。

`render_chart` 工具接受的参数：

| 参数 | 作用 |
|---|---|
| `database` | 要查询的数据库 |
| `sql` | SQL 查询语句，结果作为图表数据 |
| `chart_type` | `barX` / `barY` / `line` / `dot` / `areaY` / `waffleY` 六种 |
| `x` | x 轴的列名 |
| `y` | y 轴的列名 |
| `color` | 可选，按列上色 |
| `title` | 可选，图表标题 |
| `x_label` / `y_label` | 可选，坐标轴 |

底层用的是 Observable Plot（Mike Bostock 团队继 D3 之后维护的高层图表库），渲染在浏览器里，**SVG 图直接嵌进聊天对话**。试一下「Draw a bar chart of downloads over time」这种自然语言提问，agent 自己写 SQL group by 日期，把结果交给 `render_chart`，浏览器立刻出柱状图。

![datasette-agent-charts 自动画图演示](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/datasette-agent-sqlite-local-2026-05-26/datasette-agent-charts-plugin-demo.png)

这个插件最有工程意义的设计点是 `_html` 返回约定——0.1a4 里加的：

```python
return json.dumps({
    "_html": '<div class="my-widget">Rich content here</div>',
    "summary": "Widget rendered successfully",
})
```

带下划线前缀的 key（如 `_html`）**只渲染给用户、不送给 LLM**。意思是图表的 HTML 不会浪费 LLM 的上下文 token，但用户在聊天界面里看到的是完整 SVG 图。**这种「用户看到 / 模型看到」分离的设计**对长对话特别重要——agent 跑十轮查询，上下文里只有十段简洁文字 summary，token 成本可控，但用户屏幕上是十张高清 SVG 图。

![datasette-agent-charts 仓库 OG 卡片](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/datasette-agent-sqlite-local-2026-05-26/datasette-agent-charts-github-card.png)

## 七、Datasette 1.0a30 的 makeJumpSections 给 agent 留的位置

5 月 24 日发的 Datasette 1.0a30 主版本里有一个看起来很小、但对 agent 这一层很关键的改动：**新增 `makeJumpSections` JS 插件钩子，可以扩展 Jump to 菜单**。

`makeJumpSections` 是给前端用户的快速跳转菜单（顶栏右上的 Jump to 下拉）。在 1.0a30 之前，Jump to 里的内容是 Datasette 主程序写死的：跳到数据库、表、SQL 编辑器。1.0a30 之后，**插件可以往这个菜单注入自己的 section**——`datasette-agent` 立刻用上了这个钩子，把「Chat with agent」「Explore with agent」「Background agents」三档统一塞进 Jump to。

![Datasette 1.0a30 makeJumpSections 钩子示意](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/datasette-agent-sqlite-local-2026-05-26/datasette-agent-makejumpsections.png)

这一步看起来微小，意义却很明确：**主项目的 UI 扩展点和 agent 插件的交付节奏对齐**。两个项目同一天（5 月 24 日）发版本——主项目把 UI 扩展位开出来，agent 插件立刻用上。这种「主项目 + 插件**同步迭代**」的工程节奏，是 Datasette 9 年项目群能持续增长的核心原因，**也是 datasette-agent 不会变成又一个半成品 agent 项目的工程保证**。

## 八、国内同行三条接入路径

datasette-agent 当前是英文界面 + 默认接 OpenAI 系模型；国内开发者真正要用起来有三条最现实的接入路径：

### 路径 1：完全本地（隐私敏感数据）

最适合的场景是个人记账、客户名单、订单数据、敏感日志。

```bash
# 安装本地推理引擎（任选一）
brew install ollama          # macOS / Linux
ollama pull qwen3:30b

# Datasette + agent
pip install datasette datasette-agent datasette-agent-charts llm-ollama
echo "plugins:
  datasette-llm:
    default_model: qwen3:30b" > datasette.yml
datasette my_data.db -m datasette.yml
```

访问 `/-/agent`，**整个对话过程零网络流量**。本地千问 30B Q4 在 4090 24GB 跑 70+ tok/s 是真实数字，日常 SQL 问答完全够。

### 路径 2：本地 SQLite + 云端 LLM（成本最低）

数据敏感度不高、想用云端大模型脑力（多步推理 + 长文理解），但还是不想把数据本身交给 vendor：

```yaml
plugins:
  datasette-llm:
    default_model: deepseek-v4-pro
```

`datasette-llm` 走 `llm` 这一层的 `llm-deepseek` 插件挂 DeepSeek API key。**数据本体在本地 SQLite，SQL 查询结果以文本形式过 LLM 一遍再回到本地浏览器渲染**。这条路是当前国内同行性价比最高的——DeepSeek V4-Pro 6 月起永久价 0.75 元 / 1M 输入、1.5 元 / 1M 输出，跑个日常数据问答月成本 50 元以内。

### 路径 3：内网 Datasette + 团队 SSO

数据团队 / 研究院 / 高校实验室部署 Datasette 到内网服务器，挂多个 SQLite 数据库（或通过 `datasette-pgsql` 挂 PostgreSQL 子集），团队成员 SSO 登录后用 agent 自助查数据。

```bash
# 部署到 Linux 服务器
docker run -d --name datasette \
  -v $(pwd)/data:/data \
  -v $(pwd)/datasette.yml:/datasette.yml \
  -p 8001:8001 \
  datasetteproject/datasette \
  datasette /data/*.db -m /datasette.yml --host 0.0.0.0
```

加 `datasette-auth-passwords` 或自家 SSO 插件做用户认证，**三档 agent 权限按组独立授予**。整个团队的数据查询能力上一个台阶，但敏感数据库的写入权限依然安全。

![datasette-agent 国内三条接入路径](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/datasette-agent-sqlite-local-2026-05-26/datasette-agent-china-3-routes.png)

## 九、编辑说

本篇围绕 datasette-agent 13 天 5 个 alpha 版本的工程节奏 + 和三家主流云端对话式 SQL 产品的差异 + 国内同行三条接入路径写。核心论点不变：**「自然语言问数据」这件事的属性从云服务回到桌面软件**，而 Simon Willison 三年的 LLM 库准备让这件事第一次有了开源、可商用、vendor 无锁的选项。

值得国内同行注意的几件事：

- **不要把 datasette-agent 当成 Claude Code 的替代品**——它的定位是「本地数据分析的桌面入口」，不是通用 coding agent
- **`datasette-llm` 这一层 vendor 中立的设计是结构性优势**——配置文件改一行就能在 OpenAI / Anthropic / DeepSeek / 千问 / 本地模型之间切换，长期可维护性远超绑定单家 vendor 的产品
- **`render_chart` 的 `_html` 分离约定值得借鉴**——做 agent 类产品时把「用户看到的渲染」和「LLM 看到的文本」分开，能显著降低 token 成本
- **国内最实用的入口是路径 2**：本地 SQLite + 云端 DeepSeek V4-Pro，月成本压到 50 元内，体验和 Claude Code 同档但数据全程留本地
- 路径 3 是数据团队 / 研究院的最优解，**三档权限独立可授权**让团队多人用 agent 这件事第一次有了清晰边界

下一步值得跟的几件事：**0.1a5 alpha 什么时候发**（按当前节奏 5 月底 / 6 月初）、**Datasette Cloud 何时加 agent 入口**（Simon 在博客里提了路线图）、**国内有没有团队把 `datasette-llm` 翻译成中文文档**（这是国内同行真正用起来的关键摩擦点）。
