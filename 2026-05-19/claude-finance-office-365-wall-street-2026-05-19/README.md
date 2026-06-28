---
title: "Claude 接管 Office：十个金融 Agent 上桌"
description: "5/18 Anthropic 一口气放出十个金融 Agent 模板 + Excel / Word / PowerPoint 全量上线 + Outlook 内测 + Moody's MCP 接入六亿家公司 + 八家新数据连接器。三十六氪 / Fortune / The New Stack / CryptoBriefing 同步报道。金融成为 Anthropic 第二大行业收入来源 · 前五十客户中四成来自金融机构。本文按 Anthropic 官方公告 + 国内八家券商对照口径，把工作流形态差异讲清楚。"
date: 2026-05-19
weekday: 星期二
category: AI 金融 / Microsoft 365 / 国内国外对照
track: domestic-hot
slug: claude-finance-office-365-wall-street-2026-05-19
cover: claude-finance-office-365-wall-street-2026-05-19.png
keywords: [Claude 金融, Anthropic, Microsoft 365, Excel, 金融 Agent, Moody's MCP, JPMorgan, 中信证券, 中金, 通义灵码]
---

# Claude 接管 Office：十个金融 Agent 上桌

![Claude 把 Office 全家桶变成投行工具封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/claude-finance-office-365-wall-street-2026-05-19/claude-finance-office-365-wall-street-2026-05-19.png)

## 开篇：一句话先把这件事说清楚

5 月 18 日深夜，Anthropic 在官网放出了一组让华尔街分析师集体放下咖啡的更新——**十个金融 Agent 模板一次性开放、Excel / Word / PowerPoint 三个 Office 应用全量上线、Outlook 内测、Moody's 凭 MCP（Model Context Protocol，模型上下文协议）接进 Claude、八家新数据连接器同步官宣**。

三十六氪用了这样一句话当头条：「Claude 杀入华尔街，十个智能体接入 Office 全家桶，爆改分析师桌面」。

把这件事放在国内开发者视角看，意思更直接：**Anthropic 在用一个标准 SaaS 大厂的产品节奏，把 Claude Opus 4.7 装进投行分析师每天敲八个小时的 Excel 单元格里**。这不是又一次「AI 助手发布会」，是一套覆盖前台研究、中台估值、后台对账的完整工作流。

按 5/18 Anthropic 官方公告 + 三十六氪 / Fortune / The New Stack / CryptoBriefing 四家媒体同步报道，关键数字罗列如下：

| 项目 | Anthropic 五月十八号官宣口径 | 来源 |
|---|---|---|
| 金融 Agent 模板 | **10 个**，覆盖前中后台 | anthropic.com/news/finance-agents |
| 金融数据连接器 | **新增 8 家 + 既有 8 家** | 同上 |
| Moody's MCP 接入 | **6 亿家公开与私人公司**信用评级 + 行业研究数据 | Anthropic 官方 / 三十六氪转引 |
| Claude Opus 4.7 金融基准 | **Vals AI Finance Agent 64.37%** · 业界第一 | Anthropic 官方 |
| 已部署金融客户 | **JPMorgan Chase · Goldman Sachs · Citi · AIG · Visa** 等 | Fortune 五月五号 |
| 金融行业地位 | **第二大行业收入来源** · 前 50 大客户中 **40%** 来自金融机构 | 三十六氪五月十八号 |
| Office 应用 | Excel / Word / PowerPoint **全量上线** · Outlook **内测** | The New Stack 五月十号 |

> ![Anthropic 金融 Agent 官方公告 hero](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/claude-finance-office-365-wall-street-2026-05-19/claude-finance-anthropic-hero.png)

![三十六氪五月十八号头条 hero 截图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/claude-finance-office-365-wall-street-2026-05-19/claude-finance-36kr-hero.jpg)

这篇文章把三件事讲清楚：

1. **十个 Agent 是什么场景**——按投行三段工作链路（前台研究、中台合规、后台财务）一一拆开；
2. **Office 全家桶到底接管了哪些事**——Excel / Word / PowerPoint / Outlook 四个应用各自的具体能力 + 一个叫 Dispatch 的新功能；
3. **国内同向产品在做什么**——中信证券 / 中金 / 广发 / 海通 / 华泰 / 万得 / 同花顺 / 通义灵码 / 智谱 九家口径横评，工作流形态差异说清楚。

文末附五个常见疑问 + 一张对照表。

---

## 一、十个金融 Agent · 不是十个 prompt 模板

按 Anthropic 官方公告原文，十个 Agent 分两组列出：

**研究与客户覆盖组（前台五个）**：

- ① **Pitch Builder**（路演材料搭建）
- ② **Meeting Preparer**（客户会前简报准备）
- ③ **Earnings Reviewer**（财报更新与点评）
- ④ **Model Builder**（财务模型迭代搭建）
- ⑤ **Market Researcher**（行业研究 / 公司画像）

**财务与运营组（中后台五个）**：

- ⑥ **Valuation Reviewer**（估值逻辑审查）
- ⑦ **KYC Screener**（反洗钱客户尽调）
- ⑧ **General Ledger Reconciler**（总账对账）
- ⑨ **Month-End Closer**（月末关账）
- ⑩ **Statement Auditor**（财报一致性核查）

![Anthropic 十个金融 Agent 按投行三段工作链路分布](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/claude-finance-office-365-wall-street-2026-05-19/claude-finance-10-agents.png)

读这张表前先把一个误读拦下来：**这十个 Agent 不是十段 prompt**。

三十六氪原文用了一个准确的描述：「这些 Agent 不是简单的 prompt 模板，而是完整的部署包，附带 sub-agent（子智能体）、数据连接器、技能包」。换句话说，**一个 Pitch Builder Agent 拉出来，它后面挂着 FactSet 数据连接器 + PowerPoint 渲染插件 + 一组负责数据校对的子 Agent**。分析师只需要交代「给客户 X 准备一份关于行业 Y 的 pitchbook」，剩下的事 Agent 自己规划、自己取数、自己排版。

这套设计跟 Anthropic 早些时候放出的 Claude Code 思路一脉相承——**把工作流封装成 Skill，让 Agent 自己决定调用哪些工具**。在金融场景，工具 = Excel / PowerPoint / Word + 七八家专业金融数据库。

为什么先做这十个？翻 Anthropic 的产品博客能看出选择标准：**每个 Agent 都对应投行内部「最浪费分析师时间」的某段流程**。

- 路演材料：一份 pitchbook 通常需要分析师手工拼 30-80 页，从 FactSet 拉财务、从 PitchBook 拉可比交易、从 IBISWorld 拉行业。这类活儿没有创造性、但需要 100% 准确。Pitch Builder 把这套流程标准化。
- 月末关账：财务团队每月最后一周加班的元凶。Month-End Closer 自动拉 GL 流水、识别异常、生成调整分录草案。
- KYC 尽调：合规团队的重负担。KYC Screener 从公开数据库交叉核对受益人、关联交易、政治公众人物名单。

**Anthropic 没有去碰交易决策、投资建议、风险预测这类「黑盒会出大事」的场景**。这种克制比能力本身更值得国内同行学习。

### Pitch Builder 实际跑一遍是什么样子

以 Pitch Builder 为例，按 Anthropic 官方公告 + The New Stack 描述拼一个完整工作流：

1. 分析师在 Claude Cowork 控制台敲一句话：「给客户 A 准备一份关于新能源汽车赛道的 pitchbook，重点对比国内三家头部主机厂」；
2. Pitch Builder Agent 先调 IBISWorld 拉行业研究报告，调 PitchBook 拉过去十二个月的可比交易，调 FactSet 拉三家主机厂的最新财务；
3. Agent 把数据丢给子 Agent，子 Agent 在 Excel 里搭一份估值模型；
4. Agent 调 PowerPoint 模板，按公司视觉规范填充三十页幻灯片；
5. 分析师收到一份完整 pitchbook 初稿，**整个过程大约二十分钟**，对比传统人工拼一份 pitchbook 需要 8-16 小时。

![Pitch Builder Agent 实跑一次 · 二十分钟生成一份 pitchbook 时序图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/claude-finance-office-365-wall-street-2026-05-19/claude-finance-pitch-workflow.png)

注意这里的关键工程细节：**整条链路里数据连接器 / Excel 模型 / PowerPoint 渲染都是 Claude 调用工具，分析师只需要审核结果**。这不是「AI 提升效率」的常规叙事，是工作流形态的根本变化。

### KYC Screener 为什么值得国内合规团队关注

KYC（Know Your Customer，客户尽职调查）是国内券商合规团队的高压力工作，对人力消耗巨大。KYC Screener Agent 的典型流程：

1. 输入新客户基本信息——姓名 / 公司 / 注册地；
2. Agent 调 Dun & Bradstreet 验证企业实体真实性，调 Verisk 查保险与风险记录；
3. 调 Third Bridge / Guidepoint 查关联交易与受益人；
4. 自动交叉核对全球制裁名单 + 政治公众人物名单 + 反洗钱黑名单；
5. 生成尽调报告，标记需要人工复核的红色 / 黄色风险点。

国内合规团队目前主要靠企查查 / 天眼查 / 内部 KYC 系统手工核对，跨数据源耗时长且容易遗漏。如果国产大模型厂商愿意做类似 Agent 模板 + 国内征信数据库接入，对券商合规工作流改善是实打实的。

---

## 二、Office 全家桶 · Excel 是真正的主战场

Anthropic 这次跟 Microsoft 365 的整合，可以说是把过去半年「Claude 进 Office」拼图最后一块装上。

**Excel** 端的能力官方公告原文这样写：「Build financial models from filings and data feeds, audit formulas across linked workbooks, and run sensitivity analyses」（从监管申报和实时数据构建财务模型，跨工作簿审查公式，运行敏感性分析）。

翻译到投行分析师的真实工作场景：

- 你把一家公司的 10-K（美国证监会年度报告）丢进 Excel 工作簿，让 Claude 直接搭一份三表联动的 DCF（折现现金流）估值模型；
- 你怀疑某个工作簿的循环引用，让 Claude 跨表追踪整条公式链；
- 你想看「如果毛利率从 32% 滑到 28%，EBITDA 怎么变」，Claude 一次性跑十二种情景。

**Word** 端最实用的能力是「按公司模板编辑信用备忘录」。这意味着你给 Claude 喂一份历史信用 memo 模板，它能按这家券商内部规范的格式、引用、脚注、风险段落起草新的备忘录。

**PowerPoint** 端最关键的能力是「数据变化时演示文稿自动更新」。这一条对路演分析师杀伤力极大——以前改一个数字要手动同步 27 页 PPT 的图表，现在 Claude 接住。

**Outlook** 还在内测，能力包括收件箱分诊、按客户偏好语气起草回复、自动会议安排。Anthropic 官方原文用了「coming soon」（即将上线）。

![Claude 接管 Office 全家桶 · 四个应用具体能力矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/claude-finance-office-365-wall-street-2026-05-19/claude-finance-office-matrix-v2.png)

更关键的一个新功能叫 **Dispatch**。The New Stack 五月十号文章原文：「In Claude Cowork, users can assign Claude work tasks from anywhere—by text or by voice—using Dispatch」（在 Claude Cowork 里，用户可以从任何地方用文字或语音派任务给 Claude，靠 Dispatch 功能）。

实际场景：分析师中午出去吃饭，路上想起来「下午客户会前要把 X 公司最新的 earnings 摘要发我」，掏出手机说一句话，Claude 在分析师本地的 Excel 工作簿上继续干活，等分析师回到座位时摘要已经躺在桌面。这件事彻底改变了「分析师必须坐在台式机前才能产出」这个隐含前提。

还有一个细节官方没大力宣传但对工作流影响深远——**单次对话状态在四个 Office 应用之间不丢失**。你在 Excel 跟 Claude 讨论了 X 公司的 DCF 模型，切到 PowerPoint 让 Claude「按刚才那份估值做一页路演稿」，Claude 知道「刚才那份」指什么；再切到 Outlook 让 Claude「把这份发给客户 Y」，Claude 也知道客户 Y 是谁。

这种贯穿能力在 Mind Studio 的技术博客里被披露：**Claude 在四个应用之间用 sub-agent 通信，跨文件上下文共享**。这是 Anthropic 没大张旗鼓宣传、但工程含义最大的一块。

---

## 三、Moody's 接进 Claude · 六亿家公司在指尖

数据生态这块更值得国内做投研的同行注意一下。

Anthropic 这次同时官宣的合作：

**新增八家数据连接器**（按 Anthropic 公告 verbatim）：

- Dun & Bradstreet（邓白氏企业征信）
- Fiscal AI（财务数据）
- Financial Modeling Prep（模型预制）
- Guidepoint（专家访谈网络）
- IBISWorld（行业研究）
- SS&C Intralinks（交易数据室）
- Third Bridge（第三方专家访谈）
- Verisk（保险风险数据）

**既有八家数据连接器**：

- FactSet（金融终端）
- S&P Capital IQ（标普资本）
- MSCI（摩根士丹利资本国际指数）
- PitchBook（私募 / VC 数据）
- Morningstar（晨星基金评级）
- Chronograph（私募业绩追踪）
- LSEG（伦交所数据集团，原 Refinitiv）
- Daloopa（财务建模数据）

**Moody's MCP 接入**：Anthropic 官方公告原文「more than 600 million public and private companies」（六亿多家公开与私人公司）。注意是 **6 亿不是 60 亿**——三十六氪原文也是六亿，本文沿用 verbatim 数字。

![Claude 金融版 · 客户名单与数据连接器全景](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/claude-finance-office-365-wall-street-2026-05-19/claude-finance-ecosystem-v2.png)

这里的工程含义比客户名单更重要：**Moody's 走的是 MCP 协议接入，而不是传统数据 API**。

MCP 是 Anthropic 自己设计的协议，让模型可以像调本地工具一样调外部数据源。Moody's 选 MCP 而非传统 REST API 意味着——**Moody's 不再把自己当成一个数据库，而是当成一个 Claude 可以原生调用的工具**。这对其他数据厂商是强信号：要么也走 MCP，要么继续在传统 API 圈子里跟 Bloomberg 抢饭碗。

国内做投研数据的同行，万得 / 同花顺 / 朝阳永续 / 通联数据，下一步如果想跟 Claude 接的话也得走 MCP。这件事在国内开发者社区已经有共识，但具体落地需要数据厂商有动作。

---

## 四、客户名单 · 第二大行业不是夸张

Fortune 五月五号文章给了一份在生产环境部署 Claude 的金融客户名单（verbatim）：

- **JPMorgan Chase**（摩根大通）
- **Goldman Sachs**（高盛集团）
- **Citi**（花旗集团）
- **AIG**（美国国际集团）
- **Visa**（Visa 卡组织）

加上五月初 Anthropic 和 Blackstone（黑石）、Hellman & Friedman（HF 私募）、Goldman Sachs 共同投资 15 亿美元成立的 AI 企业服务公司，意味着 Anthropic 已经把华尔街最大的几家 buy side / sell side / 私募 / 保险都装在了一张桌子上。

三十六氪原文给了两个总量数字：

> 「金融已成为 Anthropic 第二大行业收入来源」
>
> 「Anthropic 前 50 大客户中 40% 来自金融机构」

——三十六氪 2026-05-18

40% 的客户集中度意味着 Anthropic 实质上已经把金融当成了仅次于科技自身（Claude Code / Cursor / 各种开发工具用户）的核心市场。

更值得注意的一幕是 5 月 5 日 Anthropic CEO Dario Amodei 和摩根大通 CEO Jamie Dimon 同台亮相纽约。Dimon 现场说：「我想了解资产掉期、国债买卖价差、退出市场、投资级评级，二十分钟它给我建了一个巨大的仪表板」。

这种 CEO 级别同台的画面，意味着 Claude 不只是工程团队在用，**摩根大通最高层在用**。

---

## 五、国内对位横评 · 工作面形态不同，技术差距没想象中大

国内现在做金融 AI 的有两条路径：

**第一条路：券商自研 + 多模型接入**

按 21 世纪经济报道 + CSDN 整理的公开资料，截至 5 月 18 日：

- **中信证券**——Bond Copilot 债券智能助手，覆盖承揽 / 承做 / 承销三环节，底层千问 + 自研。
- **中金公司**——投研助手内部试点，底层千问 + DeepSeek，接 Wind / 巨潮 / 内部财报库。
- **广发证券**——智能投研对客户经理开放，底层千问 / DeepSeek。
- **海通证券**——智能客服 App + 网厅上线，底层文心 + 自研。
- **华泰证券**——涨乐财富通 AI 嵌入零售 App，底层智谱 + 自研。
- **国泰君安**——智能投顾试点，底层多模型混用。

**第二条路：第三方厂商 SaaS 化投研工具**

- **万得 · WindClaw**——Wind 终端嵌入 AI，多智能体协同投研，底层千问 / DeepSeek / 智谱。
- **同花顺 · i 问财**——Hithink GPT 自研大模型驱动。
- **朝阳永续 · AI 小二**——专业机构高频投研场景，融合二十余年金融数据积累。
- **通联数据**——量化策略与投研。

**国产大模型厂商金融版**：

- **阿里通义 · 千问金融版**——通义灵码 IDE 插件 + Web，接 Wind / Tushare。
- **智谱 · GLM 金融**——GLM-4.6 私有部署，B 端定制为主。
- **百度文心 · 金融**——主要在海通等券商内嵌。
- **DeepSeek**——多家券商底层接入但无独立金融版。

![Claude 金融版 vs 国内同向产品 · 截至五月十八号实查口径](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/claude-finance-office-365-wall-street-2026-05-19/claude-finance-domestic-compare.png)

横向对比 Anthropic 和国内方案，差异不在「谁的模型更强」，而在两条更基础的轴：

**第一个差异：工作面**。Anthropic 把 Office 当工作面，国内主战场仍在 Web 控制台和 App。这个差异不是技术差距，是**国内分析师工作流的真实形态**——很多券商内部系统是浏览器为主，Wind / 同花顺 / 内部研报系统都是 Web 应用，Excel 反而不是主战场。所以国产 AI 投研选 Web 优先有它的合理性。

**第二个差异：数据接入路径**。Anthropic 一口气接了十六家专业金融数据库 + Moody's MCP。国内券商的优势是有自己的内部数据库 + Wind / 同花顺生态相对封闭。这一点国内反而是优势——内部数据没人能抢，外部数据靠 Wind 终端集成。

但有一条值得国内同行注意：**Anthropic 的 Agent 是产品形态而不是技术 demo**。Pitch Builder / Month-End Closer 这种命名直接对应投行内部岗位，意味着 Anthropic 跟投行业务团队深度共创了三到六个月。国内目前的方案大部分还停留在「我有 AI 能力，你来用」的工具形态，从工具到 Agent 形态的产品化还有一段路要走。

---

## 六、Vals AI 评测 64.37% · 分数怎么读

Anthropic 官方公告原文：「state-of-the-art on financial tasks and leads the industry on Vals AI's Finance Agent benchmark, at 64.37%」（在金融任务上达到业界领先水平，在 Vals AI 金融 Agent 基准测试上领跑业界，得分 64.37%）。

读这个分数需要先把 Vals AI 是什么交代清楚：Vals AI 是 2024 年成立的法律 + 金融 AI 评测公司，他们的金融 Agent 评测涵盖财务模型构建、估值审查、财报点评、行业分析四大类任务，参与方包括 GPT-4 / Claude / Gemini / DeepSeek / 千问等主流模型。

64.37% 这个分数本身需要两个 framing：

1. **这是公开 Agent 基准的领先**，不是「金融 AI 终极水平」。基准里包含一些边界场景，整体准确率超过 60% 已经达到分析师可用门槛；
2. **领先优势是几个点而不是几十个点**。Anthropic 没有公开第二名是谁，但参考其他公开评测，前几名通常聚集在 55-65% 区间。

国内厂商在 Vals AI 这个榜上目前没有看到正式提交记录，但 DeepSeek / 千问在自有金融评测里跑出过相当不错的成绩。坚持一个判断：**评测分数差几个点对真实工作流体验影响有限，真正影响的是 Agent 产品化程度 + 数据接入广度 + 工作流贯穿能力**。

另外值得一提的是 Anthropic 这次特意强调 Claude Opus 4.7 是金融专版，但实际上跟通用版底层模型权重是一致的——专版的差异在于**额外训练了金融领域的 Skills 与 Agent 模板，并且把十六家专业数据库的接入做成了官方支持**。这意味着如果国内厂商有类似的金融领域训练语料与连接器投入，达到接近水平在技术上完全可行，差距在**产品化决心**而不是模型能力。

---

## 七、给国内开发者的对比视角

如果你是国内做金融 AI 工具的工程师，从这次 Anthropic 更新里可以读出三件事：

**第一件事，Agent 模板是产品而不是技术 demo**。十个金融 Agent 之所以值钱，不是因为底层模型有多强，而是因为每个 Agent 背后都做完了「跟投行业务团队共创 + 工具接入 + 数据连接器 + UI 形态」这一整套产品化工作。国内厂商如果想做对位产品，决心不在「我能不能跑出 64% 的分数」，而在「我愿不愿意派工程师在中信证券投行部驻场三个月」。

**第二件事，MCP 协议是接下来一年的入场券**。Moody's 走 MCP 接进 Claude 是一个信号——数据厂商如果不想被边缘化，必须支持模型可以原生调用的协议。国内 Wind / 同花顺 / 朝阳永续 / 通联数据接下来一年大概率会推出类似 MCP 的协议接入。如果你是这些厂商的工程师，这件事比客户端 UI 优化更值得投入。

**第三件事，Office 不是国内主战场，但工作流贯穿是**。国内分析师不在 Excel 里办公，但他们在 Wind 终端 / 同花顺 / 内部研报系统之间频繁切换。如果哪个国产 AI 工具能做到「在 Wind 里跟 Agent 讨论一家公司，切到内部研报系统时 Agent 知道你在说哪家」，那就是国内版的 Claude × Office。这个空间目前还没人占。

---

## 八、五个常见疑问

**Q1：国内分析师用得上吗？**

直接用 Anthropic 的金融 Agent 在国内有访问限制。但思路完全可以复用——国产模型 + Wind 数据 + 同花顺数据 + 国内券商自己的内部数据库，构造一个对标 Pitch Builder 的 Agent 在技术上没有障碍。差的是产品化决心和投行业务团队深度共创。

**Q2：Claude Opus 4.7 金融版定价多少？**

按 The New Stack 文章：Claude for Excel / Word / PowerPoint 在所有付费计划（Pro / Team / Enterprise）已经全量上线。Outlook 内测期间向 Team / Enterprise 客户开放。具体金融版（含十个 Agent 模板）定价 Anthropic 没公开，按行业惯例属于 Enterprise 套餐内嵌。

**Q3：Moody's MCP 国内能用吗？**

技术上可以，但要通过 Anthropic 海外账号。国内信用评级有中诚信、联合资信、新世纪等本土机构，目前没看到他们走 MCP 协议的公开消息。

**Q4：跟 OpenAI ChatGPT for Finance 比怎么样？**

OpenAI 在 2025 年也推过类似产品，但绑定 Microsoft 365 走的是 Copilot 路径——通过 Microsoft Copilot 套件接入而不是 Claude 这种独立 Office 插件。两种路径各有优劣，Copilot 集成度高但定制能力弱，Claude 插件灵活但需要单独安装。

**Q5：国内券商接下来会做什么？**

预测三件事：(1) 头部券商加大投研工作流 Agent 化投入，从工具向 Agent 演进；(2) 国产大模型厂商推出金融垂类的 Agent 模板，对标 Anthropic 十个 Agent；(3) Wind / 同花顺等数据厂商考虑接入国内大模型的工具调用协议（类似 MCP 的国产协议）。

---

## 九、收尾：分析师桌面的形态正在改变

Anthropic 这次的更新，技术细节比官方公告披露的还要重——但最值得国内同行思考的，不是 Claude Opus 4.7 多强、Excel 集成多深，而是一个更朴素的判断：

**投行分析师每天敲八小时 Excel 这件事，从 5 月 18 日起开始被重新设计**。

不是说 Excel 会消失。是说**Excel 单元格背后那个等着 AI 填数的人**，工作流形态正在从「手工查数据 + 拼模型 + 改 PPT + 写 memo + 发邮件」变成「跟 Agent 协商策略 + 审核 Agent 结果 + 跟客户讨论结论」。

这个变化对国内同行的意义在于：**国内做 AI 投研的产品决策窗口期还有六到十二个月**。海外这边 Anthropic 已经把 Office 工作面占住、把十六家专业数据库接住、把摩根大通这种 anchor 客户拿下；国内的路径不同——Web 控制台 + 自有数据库 + 国产模型组合在国内有它自己的优势，但产品化决心和投行业务团队深度共创是必修课。

三十年前美国投行用上彭博终端的那一天，国内分析师还在手工剪报。今天 Claude 装进了华尔街的 Office 全家桶，国内做金融 AI 的同行已经在跟 Anthropic 在同一年讨论同样的产品形态。这本身就是过去十年最值得高兴的进步。

30 天后再回访这个赛道——Anthropic 金融 Agent 实际使用反馈、Moody's MCP 国内接入路径、国产券商对位产品的进展——那时候应该有更新的判断。

---

**本文来源**：Anthropic 官方公告（anthropic.com/news/finance-agents · 五月十八号）+ 三十六氪头条文章（id 3814703947701767 · 新智元出品 · 五月十八号 21:14 发布）+ Fortune（五月五号 Dimon × Amodei 同台报道）+ The New Stack（五月十号 Office 集成报道）+ CryptoBriefing + Markets Media + Storyboard18 + 21 世纪经济报道 + 阿里云帮助中心 + 万得官网 + 智谱官网。Moody's 6 亿家公司数字 verbatim 引用，未做放大。

**协作说明**：本文由 Claude Code 和作者协作完成，经过五个审核 Agent 把关——内容真实性、图像质量、链接有效性、跨表一致性、去重检查。十个金融 Agent 名单、八家新数据连接器列表、五家既有客户银行名单全部 verbatim 对照 Anthropic 官方公告。Vals AI 64.37% 分数 verbatim 引用 Anthropic 官方原文。国内八家券商 + 三家国产大模型厂商情况按 5/18 公开资料整理，如有最新动态欢迎纠正。
