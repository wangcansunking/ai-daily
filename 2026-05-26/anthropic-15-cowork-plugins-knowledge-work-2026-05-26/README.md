---
title: "Anthropic 一次开源 17 个工种 plugin：212 个 skill 全家桶，财务、销售、市场也能挂进 Claude Code"
slug: anthropic-15-cowork-plugins-knowledge-work-2026-05-26
date: 2026-05-26
weekday: 星期二
category: Claude Code Plugin / Anthropic Cowork / 知识工作
track: overseas_hot
track_score: 8.6
cover: anthropic-15-cowork-plugins-knowledge-work-2026-05-26.png
description: Anthropic 把自家 17 个工种的 plugin 全部开源到 anthropics/knowledge-work-plugins，截至 2026-05-25 marketplace 已收录 49 个 plugin，第三方合作伙伴在持续往里加。每个 plugin 就是 markdown 文件 + 一份 MCP 配置，挂进 Claude Code 不到一分钟。本文逐一核对 17 个一方 plugin 的内容、看一个 plugin 的内部结构、给国内开发者两条直接可用的二开路径。
---
# Anthropic 一次开源 17 个工种 plugin：212 个 skill 全家桶，财务、销售、市场也能挂进 Claude Code

![Anthropic 17 工种 plugin 全家桶封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/anthropic-15-cowork-plugins-knowledge-work-2026-05-26/anthropic-15-cowork-plugins-knowledge-work-2026-05-26.png)

## 30 秒速览

- **仓库**：`anthropics/knowledge-work-plugins`，2026-01-29 首次提交，截至 2026-05-25 14:48（仓库内最新 commit 时间）已积累 15,153 stars / 1,840 forks，Apache-2.0 协议
- **一方 plugin 17 个**：productivity、sales、customer-support、product-management、marketing、legal、finance、data、engineering、human-resources、operations、design、bio-research、small-business、enterprise-search、cowork-plugin-management、pdf-viewer
- **总盘 49 个**：marketplace.json 里登记的 49 项 = 17 一方 + 5 合作伙伴随仓打包 + 27 外部独立仓引用（Vanta、Miro、PlanetScale、Prisma、Figma、Adobe、CockroachDB、Mintlify、Box、LSEG、S&P Global 等都已经进来）
- **skill 总数 212 个**：所有 plugin 内部累计 212 个 SKILL.md，small-business 一家就有 31 个；commands 共 15 个，主要集中在 pdf-viewer（4 个）+ partner-built/slack（5 个）+ partner-built/brand-voice（3 个）+ product-management/brainstorm 1 个
- **每个 plugin 三件套**：`skills/` 目录（Claude 自动调用的领域知识）+ `commands/` 目录（用户敲 `/` 触发的显式动作）+ `.mcp.json`（接外部数据库 / SaaS 的连接器）
- **安装一行命令**：`claude plugin marketplace add anthropics/knowledge-work-plugins && claude plugin install finance@knowledge-work-plugins`
- **历史节奏**：1 月 30 日首发 11 个 plugin，2 月 24 日企业版扩展，5 月 12 日加 small-business，5 月 18-21 日批量修 MCP 死链 + 给每个 marketplace 项目补 displayName

![Cowork plugin 总盘 49 个 · 一方 17 + 合作伙伴打包 5 + 外部独立仓 27](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/anthropic-15-cowork-plugins-knowledge-work-2026-05-26/anthropic-cowork-marketplace-49.png)

## 一、Cowork 这个产品，和它为什么要把 17 个 plugin 全部开源

Anthropic 在 2026-01-12 推出 Claude Cowork，定位是「面向不会写代码的知识工作者的 Claude Code」。Claude Code 让工程师把整个仓库交给 Claude，Cowork 想让会计、产品经理、销售把整个工作流程交给 Claude——核心范式从「问答」变成「布置任务、Claude 自己跑完、交一份成品回来」。

但通用助手有个天花板：一个销售经理打开 Cowork，输入「帮我准备明天和客户的电话」，Cowork 默认状态下不知道你们公司用的 CRM 是 HubSpot 还是 Salesforce、不知道你们的销售流程几个阶段、不知道你们公司怎么定义「合格线索」。每次都要从零讲清楚太累。

所以 Anthropic 在 2026-01-30 同步开源了 11 个 plugin。每个 plugin 把一个工种的「领域知识 + 工具连接 + 工作流模板」打包成一个目录，挂进 Cowork 之后 Claude 立刻进入「角色状态」——你说「准备和 Acme 公司的电话」，sales plugin 里 `call-prep` skill 自动接管，按它的工作流先去 CRM 抓客户历史、再去 web 搜最近新闻、再去日历找会议、再去 Slack 翻同事讨论，最后吐出一份可直接打印的备忘录。

到 2026-05-25，这套打法已经实质性走出了「Anthropic 自家 demo」的范围：

- **一方 plugin 从 11 个增加到 17 个**：陆续补了 engineering、design、operations、human-resources、small-business、pdf-viewer
- **合作伙伴随仓打包 5 个**：Slack（Salesforce 出品）、Apollo.io、Common Room、Brand Voice、Zoom
- **外部独立仓 27 个**进 marketplace：Vanta、Miro、PlanetScale、Sanity、Zoominfo、Mintlify、Daloopa、Zapier、Intercom、CockroachDB、Prisma、Fastly、Cloudinary、Nimble、BrightData、SearchFit、Atlan、Postiz、Figma、Adobe Creative Cloud、Box、LSEG（伦敦证券交易所）、S&P Global 等

49 个 plugin 的总盘说明这套架构已经被合作伙伴生态接住——给 LSEG / S&P Global 这种金融数据商，做一个 plugin 把自家的市场数据 MCP 接口 + 用法指南打包发布到 Anthropic 的官方 marketplace，比维护一个独立 SaaS 产品成本低得多。

值得国内开发者关注的是：Anthropic 把这套配方完全开源，意味着「工种助手」从此不再是 OpenAI GPTs 那种「每家做自己的封闭花园」的状态。任何一家公司都可以 fork 这个仓库，把 17 个一方 plugin 当模板，改成自家公司的「财务团队 plugin」「市场团队 plugin」，挂进 Claude Code 或 Cowork 给团队用。

## 二、17 个一方 plugin 全清单

下面这张表把 17 个一方 plugin 全部列出，skill 数 + 主要连接器都按仓库实际目录核对：

| 工种 plugin | skill 数 | 代表 skill | 自带 MCP 连接器 |
|---|---|---|---|
| **productivity 个人效率** | 4 | start、task-management、memory-management、update | Slack、Notion、Asana、Linear、Jira、Monday、ClickUp、MS 365 |
| **sales 销售** | 9 | call-prep、account-research、draft-outreach、pipeline-review、forecast | Slack、HubSpot、Close、Clay、ZoomInfo、Notion、Jira、Fireflies、MS 365 |
| **customer-support 客服** | 5 | ticket-triage、draft-response、customer-escalation、kb-article | Slack、Intercom、HubSpot、Guru、Jira、Notion、MS 365 |
| **product-management 产品** | 8 | write-spec、roadmap-update、synthesize-research、competitive-brief、metrics-review | Slack、Linear、Asana、Monday、ClickUp、Jira、Notion、Figma、Amplitude、Pendo、Intercom、Fireflies |
| **marketing 市场** | 8 | brand-review、campaign-plan、competitive-brief、email-sequence、seo-audit、performance-report | Slack、Canva、Figma、HubSpot、Amplitude、Notion、Ahrefs、SimilarWeb、Klaviyo |
| **legal 法务** | 9 | review-contract、triage-nda、compliance-check、legal-risk-assessment、vendor-check | Slack、Box、Egnyte、Jira、MS 365 |
| **finance 财务** | 8 | journal-entry、reconciliation、financial-statements、variance-analysis、close-management、sox-testing、audit-support | Snowflake、Databricks、BigQuery、Slack、MS 365 |
| **data 数据分析** | 10 | write-query、sql-queries、explore-data、build-dashboard、statistical-analysis、validate-data | Snowflake、Databricks、BigQuery、Definite、Hex、Amplitude、Jira |
| **engineering 工程** | 10 | architecture、code-review、debug、deploy-checklist、incident-response、system-design、tech-debt、testing-strategy | （以 GitHub / Linear / Jira / Slack 系为主） |
| **human-resources 人事** | 9 | recruiting-pipeline、interview-prep、onboarding、performance-review、comp-analysis、policy-lookup | （HRIS + Slack + MS 365 系） |
| **operations 运营** | 9 | runbook、process-doc、capacity-plan、risk-assessment、change-request、vendor-review | （Slack + Jira + Notion 系） |
| **design 设计** | 7 | design-critique、design-system、accessibility-review、design-handoff、ux-copy、research-synthesis | （Figma + Slack + Notion 系） |
| **bio-research 生命科学** | 6 | scientific-problem-selection、single-cell-rna-qc、nextflow-development、scvi-tools | PubMed、BioRender、bioRxiv、ClinicalTrials.gov、ChEMBL、Synapse、Wiley、Owkin、Open Targets、Benchling |
| **small-business 小企业** | 31 | smb-onboard、smb-router、cash-flow-snapshot、month-end-prep、tax-prep、invoice-chase、margin-analyzer、close-month | QuickBooks、PayPal、Gmail、Google Calendar、Google Drive |
| **enterprise-search 企业搜索** | 5 | search、search-strategy、knowledge-synthesis、digest、source-management | Slack、Notion、Guru、Jira、Asana、MS 365 |
| **cowork-plugin-management 元 plugin** | 2 | （帮你创建新 plugin / 改现有 plugin） | 无 |
| **pdf-viewer PDF 助手** | 1 | view-pdf | 无（但带 4 个 commands：/open、/annotate、/fill-form、/sign） |
| **合计 17 个一方 plugin** | **141 个 skill** | — | — |
| **+ 合作伙伴随仓 5 个**（apollo / brand-voice / common-room / slack / zoom-plugin） | **71 个**（其中 zoom-plugin 57 个） | — | — |
| **= 仓库内总计** | **212 个 SKILL.md** | — | — |

![17 个一方 plugin 的 skill 数对比 · skills 是 Claude 自动调用的领域知识](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/anthropic-15-cowork-plugins-knowledge-work-2026-05-26/anthropic-cowork-skills-per-plugin.png)

几个细节值得展开：

**1. small-business 是隐藏冠军，31 个 skill 一个人就比其他 plugin 加起来还多**

小企业老板要兼任会计、销售、运营、客服、HR、市场——所以这个 plugin 把所有工种的核心动作浓缩进一个包：周一晨会简报、周五总结、月末关账、季度回顾、税务整理、发票催收、毛利分析、营销活动、价格检查、岗位招聘、客诉处理、合同审查……31 个 skill 基本对应「一个老板一天会遇到的 31 种典型场景」。这是 Anthropic 给单干创业者 / 夫妻店 / 5-10 人小公司的 all-in-one 答案。

**2. data 和 engineering 各 10 个 skill，是工种最重的两个**

data plugin 的 10 个 skill 几乎覆盖了一个数据分析师的完整一周工作：写 SQL、跑可视化、做统计、搭看板、验证数据。`validate-data` skill 专门处理「在分享给老板之前自己先把数对一遍」这个环节——大部分数据事故来自这一步缺失，Anthropic 把它单独拎出来做 skill 比简单加在 SQL 后面更靠谱。

engineering 的 10 个 skill 也很全：架构设计、代码评审、调试、部署清单、文档、事故响应、技术债、测试策略。和 Claude Code 本体的「写代码」能力互补，更偏「工程师的非编码工作」。

**3. bio-research 是垂直行业试水**

6 个 skill 全围绕早期生物医药研发：文献检索、单细胞 RNA 质控、基因组分析、靶点优先级。连接器接 PubMed、bioRxiv、ChEMBL（药物化学数据库）、Benchling（生物医药 ELN）。Anthropic 在用这个 plugin 测试一个假设——把通用 Claude 用领域 plugin 包成行业专家是否可行。如果 bio-research 跑得通，后面大概率会出 medical、materials-science、quantitative-finance 等更多垂直 plugin。

## 三、一个 plugin 内部长什么样：以 finance 为例

挑 finance 来拆，因为它是最典型的「领域知识 + 外部数据库 + 工作流」三件套。

![一个 plugin 的三件套：skills + commands + MCP 接口](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/anthropic-15-cowork-plugins-knowledge-work-2026-05-26/anthropic-cowork-anatomy.png)

`finance/` 目录下的结构：

```
finance/
├── .claude-plugin/plugin.json   # 清单 · 写明名字、版本、作者、描述
├── .mcp.json                    # 7 个外部数据连接：Snowflake、Databricks、BigQuery、Slack、MS 365、Gmail、Google Calendar
├── CONNECTORS.md                # 给 LLM 看的连接器说明
├── README.md                    # 给人看的 plugin 说明
└── skills/
    ├── journal-entry/SKILL.md          # 月末会计分录准备
    ├── journal-entry-prep/             # 配套预处理
    ├── reconciliation/SKILL.md         # 账户调节
    ├── financial-statements/SKILL.md   # 三表生成
    ├── variance-analysis/SKILL.md      # 预算差异分析
    ├── close-management/SKILL.md       # 月末关账流程
    ├── audit-support/SKILL.md          # 审计支持
    └── sox-testing/SKILL.md            # SOX 内控测试
```

### skill 的内部长这样

打开 `finance/skills/journal-entry/SKILL.md`，前 25 行的 frontmatter + 简介长这样（直接照抄仓库原文翻译）：

> **name**: journal-entry
> **description**: Prepare journal entries with proper debits, credits, and supporting detail. Use when booking month-end accruals (AP, payroll, prepaid), recording depreciation or amortization, posting revenue recognition or deferred revenue adjustments, or documenting an entry for audit review.
> **argument-hint**: "<entry type> [period]"
>
> # 月末会计分录准备
>
> **重要**：本 skill 协助会计分录工作流，但不提供财务建议。所有分录应由合格的财务专业人员审核后再过账。
>
> ## 工作流
> ### 1. 收集源数据
> 如果连了 ~~erp 或 ~~data warehouse：拉指定期间的试算表 / 子账明细 / 历史分录 / 当前总账余额
> 如果没连：提示用户连 Snowflake / BigQuery，或贴试算表数据 / 上传电子表格

这就是一个 skill 的完整骨架：YAML frontmatter（告诉 Claude 什么时候触发）+ 分步骤的工作流说明（告诉 Claude 怎么做）。**没有代码、没有 prompt 模板黑魔法，就是 markdown**。

Anthropic 在 README 里的原话：「Every component is file-based — markdown and JSON, no code, no infrastructure, no build steps.」每个组件都是文件，markdown + JSON，没有代码、没有基础设施、没有构建步骤。

这个设计哲学是这套架构能扩张到 49 个 plugin / 212 个 skill 的关键——会写 markdown 的会计、律师、市场经理都能自己改 plugin，不用懂代码。

### .mcp.json 长这样

```json
{
  "mcpServers": {
    "snowflake":  {"type": "http", "url": ""},
    "databricks": {"type": "http", "url": ""},
    "bigquery":   {"type": "http", "url": "https://bigquery.googleapis.com/mcp"},
    "slack":      {"type": "http", "url": "https://mcp.slack.com/mcp",
                   "oauth": {"clientId": "...", "callbackPort": 3118}},
    "ms365":      {"type": "http", "url": "https://microsoft365.mcp.claude.com/mcp"},
    "gmail":      {"type": "http", "url": ""},
    "google calendar": {"type": "http", "url": ""}
  }
}
```

注意三个细节：

1. **空 URL = 占位**：Snowflake、Databricks、Gmail、Google Calendar 都留空 URL，意思是「这家公司可能用 Snowflake 也可能用 Databricks，自己填」
2. **OAuth 内置**：Slack 自带 clientId 和回调端口，安装完 plugin 后第一次用 Slack 数据时会自动弹 OAuth 授权页
3. **claude.com 自家 MCP gateway**：MS 365 走 `microsoft365.mcp.claude.com/mcp`——Anthropic 自己跑了一套 MCP 代理服务给 Microsoft 365 用，避免每家公司都要自己跑 MCP server

### commands 哪儿去了

`finance/` 下面没有 `commands/` 目录。仓库一共 22 个 plugin.json，但全部 15 个 commands 集中在 4 个 plugin 里：

- `pdf-viewer/commands/` 4 个：open、annotate、fill-form、sign
- `partner-built/slack/commands/` 5 个：standup、summarize-channel、channel-digest、draft-announcement、find-discussions
- `partner-built/brand-voice/commands/` 3 个：discover-brand、enforce-voice、generate-guidelines
- `partner-built/common-room/commands/` 2 个：weekly-brief、generate-account-plan
- `product-management/commands/` 1 个：brainstorm

绝大多数 plugin **不带 commands，纯靠 skills 自动触发**。这是 Anthropic 设计的核心选择：

- **skill** = Claude 看你说了什么自动判断要不要用（隐性触发）
- **command** = 用户敲 `/legal:triage-nda` 这种命令显式调用（显性触发）

举个对比：用户输入「帮我把这份 NDA 看一下」，legal plugin 里 `triage-nda` skill 自动识别意图、自动接管，不需要敲 `/`。而 pdf-viewer 的 `/open` 这种「精确动作」才用 command——因为「打开 PDF 这个动作不会从对话语境里自然涌出来」。

Anthropic 显然认为隐性触发是未来。212 个 skill / 15 个 command 这个比例就是答案。

## 四、装一个 plugin 到 Claude Code，从零跑通的命令清单

按 README 的官方说明，整个流程三步：

```bash
# 第 1 步：加 marketplace
claude plugin marketplace add anthropics/knowledge-work-plugins

# 第 2 步：装具体 plugin
claude plugin install finance@knowledge-work-plugins

# 第 3 步：直接用，不需要重启 Claude Code
# 在对话里说：「准备 12 月份的应付账款分录」
# Claude 自动识别 finance plugin 里 journal-entry skill 触发条件，自动接管
```

如果想用上外部数据源（Snowflake / BigQuery / Slack），第 4 步是改 `.mcp.json`——但这需要装完 plugin 之后到 `~/.claude/plugins/` 下找到对应目录改 JSON。这一步对没装过 MCP server 的人有门槛，是目前 plugin 体系的最大摩擦点。

Anthropic 提供的兜底方案：所有 skill 都设计成「不连数据源也能跑」。打开 finance/skills/journal-entry/SKILL.md 看到这一段：

> If no data source is connected:
> Connect ~~erp or ~~data warehouse to pull GL data automatically. You can also paste trial balance data or upload a spreadsheet.

没接数据库就提示用户「粘贴试算表 / 上传电子表格也行」。每个 skill 都按「standalone 模式 + supercharged 模式」分两档写——这一招让 plugin 在 0 配置下也能用，是真心给「不会写代码的会计 / 销售」做的设计。

## 五、国内对照：飞书智能伙伴、钉钉 AI 助理、千问 App 知识助手

国内有三家在做类似的事：

![Anthropic Cowork plugins vs 国内办公 AI 助手 · 6 维对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/anthropic-15-cowork-plugins-knowledge-work-2026-05-26/anthropic-cowork-cn-alternatives.png)

**飞书智能伙伴**：飞书把 AI 助手做成「场景包」——文档润色、表格函数、会议纪要、人事审批。优势是和飞书生态深度耦合：你的多维表、群消息、会议录音 Claude 都直接调得到，不需要装 MCP。劣势是这些场景包闭源，你想加一个「合规自查」助手只能走飞书的低代码平台搭，不能 fork 一份改 markdown 就上。

**钉钉 AI 助理 + 宜搭**：钉钉给企业的方案是 AI 助理 + 宜搭低代码——AI 助理负责对话能力，宜搭负责把企业的业务表单 / 审批流挂上去。优势同样是和钉钉生态打通；劣势是宜搭学习曲线比 markdown 高，且整套方案不开源。

**千问 App 知识助手**：阿里把通义千问 App 做成通用助手，不分工种。优势是对国内用户最低门槛——下载 App 就能用，不需要注册海外服务、不需要 API key。劣势是它没有 plugin 这一层抽象，每次对话都要从零讲清楚自己是销售还是会计、公司用什么系统、流程几步。

| 维度 | Anthropic Cowork plugins | 飞书智能伙伴 | 钉钉 AI 助理 | 千问 App |
|---|---|---|---|---|
| 开源协议 | Apache 2.0 全开放 | 闭源 SaaS | 闭源 SaaS | 模型开源 · App 闭源 |
| 工种覆盖 | 17 + 32 外部 = 49 | 飞书场景包 | 钉钉场景包 | 通用聊天 · 不分工种 |
| 可改源代码 | fork 改 markdown 即可 | 需走飞书低代码 | 需走宜搭 | 不能改 |
| 接公司数据 | MCP 协议 · 接自家库 | 飞书多维表 + OpenAPI | 钉钉宜搭 + 钉钉表格 | 无原生接公司库 |
| 二开门槛 | 写 markdown 即可 | 需懂飞书低代码 | 需懂宜搭 | 无法二开 |
| 国内可用性 | 需自备海外 API | 原生 | 原生 | 原生 |

国内三家走的是「平台一体化 + 闭源」路线——优势是和自家办公套件无缝衔接、对纯业务用户最低门槛；劣势是不能 fork、不能跨平台、不能商业转售。Anthropic 走的是「开源标准 + 生态市场」路线——优势是 49 个 plugin 一周加一个，劣势是国内开发者要自备海外模型 API。

## 六、国内开发者两条直接可用的二开路径

**路径一：把 17 个 plugin fork 一份，改成接国内 SaaS 的版本**

flow 是这样：

```bash
# 第 1 步：fork anthropics/knowledge-work-plugins 到自己 GitHub
gh repo fork anthropics/knowledge-work-plugins

# 第 2 步：clone 下来挑一个改
cd knowledge-work-plugins
# 把 finance plugin 的 .mcp.json 改成接金山办公 / 用友 / 金蝶
# 把 productivity plugin 的 .mcp.json 改成接飞书 OpenAPI / 钉钉 OpenAPI
```

具体怎么改 `.mcp.json`：飞书自家有 `lark-openapi-mcp`（飞书官方开源 MCP server）已经放出来，钉钉有官方 OpenAPI 但需要自己包一层 MCP server。每个 plugin 改 3-5 个 MCP URL，外加修一下 CONNECTORS.md 说明书里的「Slack」改成「飞书 / 企业微信」，剩下的 skill 文件几乎不用改——因为 skill 里写的都是「拉群讨论、找日历会议、查 CRM」这种语义动作，跟具体是 Slack 还是飞书无关。

**路径二：照 17 个 plugin 的目录结构写自家公司的内部 plugin**

把 17 个 plugin 当模板，给自家公司写一个「公司专属 plugin」：

```
my-company-finance/
├── .claude-plugin/plugin.json
├── .mcp.json                 # 改成接自家用友 NC / 金蝶 K3 / 浪潮 ERP
├── skills/
│   ├── quarter-close/SKILL.md  # 改成自家公司的季末关账流程
│   ├── invoice-fapiao/SKILL.md # 改成增值税专票审核流程
│   └── ...
```

完成后挂自家 GitLab 内网，让团队 `claude plugin marketplace add http://gitlab.company.com/ai/my-company-finance` 就能装。

这条路径的价值是把「公司知识」沉淀进 markdown——每个新员工入职第一天，Claude 已经知道公司的财务流程、销售话术、产品定义、客服 SOP，不需要老员工口口相传。Anthropic 自己在 README 里也强调这一点：plugin 真正的价值不是 Anthropic 写的这 17 个通用版，而是被改造成「公司专属版」之后的样子。

## 七、Apache 2.0 + 公司内部 plugin = 商业风险有多大

合规这条线值得单独说，因为 Apache 2.0 在国内企业落地的接受度比 MIT 更高。

Apache 2.0 的关键条款：

- 可商用、可改、可闭源衍生（公司内部 plugin 不需要开源）
- 必须保留版权声明 + 协议文件 + NOTICE（如果有）
- 如果改了代码必须在改动文件里注明
- 含专利授权条款：使用者获得用 plugin 内涉及专利的免费许可

对国内企业的实务影响：

1. **改完不用回馈上游**：fork 一份给公司内部用，不需要把改动公开提交回 anthropic 仓库
2. **没有「传染性」**：改完的内部 plugin 是闭源资产，不影响公司其他闭源软件
3. **得保留 LICENSE 文件**：把 anthropics/knowledge-work-plugins 的 LICENSE 文件复制进 fork，加自己的「本版本由 XX 公司 modify 自上游 anthropic」一行说明即可

唯一的潜在风险是 plugin 里有个别 skill 引用了第三方专有 SDK（比如 Snowflake 的 Python connector）——但这是 SDK 的协议问题，不是 plugin 的协议问题。markdown 描述「用 Snowflake」不构成专利侵权。

## 八、Claude Code 装 plugin vs Cowork 装 plugin

最后一个常被混淆的点：这 49 个 plugin 同时支持 Claude Code 和 Cowork 两个产品，但触发逻辑不同。

| 触发方式 | Cowork | Claude Code |
|---|---|---|
| 装 plugin | 在 [claude.com/plugins](https://claude.com/plugins/) 网页点装 | 跑 `claude plugin install` CLI |
| 触发 skill | 用户讲自然语言、Claude 自动判断 | 用户讲自然语言、Claude 自动判断 |
| 触发 command | 在对话框输入 `/finance:reconciliation` | 在 Claude Code 命令行输入 `/finance:reconciliation` |
| 连数据源 | Cowork 网页里点 OAuth 授权 | 改本地 `~/.claude/plugins/*/.mcp.json` |
| 适合谁 | 不会写代码的知识工作者 | 工程师 + 想 fork 改 plugin 的人 |

国内开发者大概率走 Claude Code 这条路：装好 plugin、用国内 MCP server 替换连接器、提交回自家公司内部 git。Cowork 目前需要 Anthropic 海外账号 + 信用卡，对国内非企业用户不友好。

但更重要的是：**Claude Code 这条路本身就是「让国内工程师把 Anthropic 的工种模板搬过来 + 接国内系统」的标准路径**。一个上海的财务 SaaS 公司团队，可以把 finance plugin 的 8 个 skill 翻译成中文 + 接金蝶 K3 + 加 4 个中国会计准则特有的 skill（增值税专票审核 / 出口退税申报 / 营改增过渡 / 进项税转出），重新打包成「中国版 finance plugin」给客户用。整个过程不需要等 Anthropic 出中文版、不需要等飞书 / 钉钉做类似产品。

## 九、值得继续观察的三件事

到这里 17 个 plugin / 49 个总盘 / 212 个 skill 的全貌讲完。后面值得继续观察的：

**1. 中国 ISV 会不会跟进做自家 plugin 仓库**

阿里、字节、腾讯、华为、百度都有完整办公套件。如果其中一家也搞一个 `aliyun/cowork-cn-plugins` 仓库，把中文 finance plugin + 国内 SaaS 连接器全部打包，会是一次有意思的对位。技术上完全可行，问题是商业模式——开源生态 vs 自家 SaaS 闭环之间他们怎么选。

**2. 合作伙伴 27 个外部仓后续会扩到多少**

按现在 5 月底每周新增 2-3 个的速度，到 9 月底应该到 80-100 个 plugin。如果某个垂直行业（医疗、法律、金融）有 10+ 个 plugin 都聚集进来，会形成「Anthropic plugins as marketplace」的局面——Anthropic 没做应用商店，但 49 个一起聚集已经形同应用商店。

**3. skill 数 vs 实际有用度**

small-business plugin 31 个 skill 听起来很猛，但 31 个里有多少真被用到？Anthropic 没出过 plugin 使用频次数据。后面值得跟踪 GitHub issue 区——如果某个 skill 频繁被吐槽 / 频繁被改，说明它真在被用；如果某个 skill 一直安静，说明只是充数。

## 编辑说

读到这里你会发现 Anthropic 这次开源的真正卖点不是「17 个工种 plugin」这个数字，而是「skill = markdown 文件」这套抽象。markdown 当 prompt 模板的工作流定义这件事，过去三年大家都在做（Custom GPTs、Cursor Rules、Cline 系统提示、各家 IDE 的 .clinerules），但 Anthropic 第一次把它配套上 commands + MCP connectors 做成完整三件套，还把 17 个工种的实战版全部开源、配了 marketplace 协议、招了 32 个外部仓接进来。

这件事的真正价值会在未来 6-12 个月显现——当某家公司的财务 plugin、销售 plugin、合规 plugin 都在自家 GitLab 上跑了半年、积累了几百个 skill，新员工入职第一天 Claude 已经懂公司每一道流程，这时候 plugin 才不只是「一种新功能」，而变成「公司知识资产的载体」。Apache 2.0 协议保证了这些 plugin 不会被锁死在某家供应商。

国内开发者这次有两个好机会：一是直接 fork 17 个 plugin 改造成接国内 SaaS 的版本（飞书 / 钉钉 / 用友 / 金蝶 / 金山办公），一是给自家公司写一个「公司专属 plugin 仓库」。两条路都不需要等任何海外厂商出中文版——markdown 谁都能写，MCP 协议谁都能实现。

仓库的 stars 还在涨。仓库的 issue 区已经有人在认真讨论「能不能加一个 medical plugin」「能不能加一个 K-12 教育 plugin」。49 个 plugin 这个数字下周会变 50、变 60、变 100，但每个新 plugin 都是同样的 markdown + JSON 配方——这才是这次开源最值得国内开发者认真研究的事。
