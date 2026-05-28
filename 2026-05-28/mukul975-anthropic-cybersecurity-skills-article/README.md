# /GitHub项目754个安全技能：Claude、Copilot等26+平台AI Agent直接调用

> ai-daily · 2026 年 5 月 28 日 02:43 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/mukul975-anthropic-cybersecurity-skills-article/head.png)

2026 年 3 月 11 日凌晨，一个名叫 Mahipal Jangra 的开发者在 GitHub 上按下了发布键。仓库名字很直白——Anthropic Cybersecurity Skills。734 个结构化网络安全技能，26 个安全域，两套框架映射。他大概没指望这件事能炸出多大的水花。

两个月后，这个仓库的技能数量涨到了 754 个，框架映射从 2 套变成了 5 套，覆盖的 AI 平台超过 26 个。Claude Code 能跑，GitHub Copilot 能跑，OpenAI Codex CLI、Cursor、Gemini CLI 全都能跑。Hasan Toor 在 X 上转发的时候写了句话："一个真实的、组织好的安全技能数据库，任何 AI agent 都能插上就用。不是教程，不是博客文章。"

![mukul975/Anthropic-Cybersecurity-Skills — 754 structured cybersecurity skills fo](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/mukul975-anthropic-cybersecurity-skills-article/content-1.png)

我盯着这句评价看了三遍。不是教程。不是博客文章。这才是重点。

**安全行业缺的不是知识，是让机器也能读懂知识的格式。**

## 一个 Junior 分析师知道的事，你的 AI agent 不知道

我们得先聊清楚一个问题：现在的 AI agent 做安全，到底卡在哪。

原文里有一段描述精准得让人难受——一个初级分析师知道用哪个 Volatility3 插件去分析可疑的内存转储，知道用哪些 Sigma 规则抓 Kerberoasting 攻击，知道怎么在三个云服务商之间划定一次云入侵的范围。但你的 AI agent 不知道。除非你把这些技能"喂"给它。

这不是能力问题，是知识结构问题。GPT-4 和 Claude 可以写代码、搜网页、读文档，但它们脑子里没有一个"安全分析师决策树"。遇到一个内存转储文件，人类分析师脑子里自动弹出的是"先跑 imageinfo，再看 pslist，然后 netscan，最后 malfind"——这是一个按优先级排好的工作流。LLM 如果不给这个工作流，它可能会给你一篇关于 Volatility 发展史的综述，外加五个你不需要的插件建议。

这个仓库做的事情，就是把 754 个这样的工作流全部结构化。每一个技能都写成 YAML 前置元数据加 Markdown 正文，前置元数据让 agent 能在 30 个 token 内完成扫描和匹配，正文则是完整的步骤级执行指南。发现一个技能只需要 30 token，完整加载需要 500 到 2000 token。这种"渐进式披露"架构意味着 agent 可以一次性扫描全部 754 个技能，而不会炸掉上下文窗口。

**这本质上是一次知识编码运动——把人类安全专家的隐性知识，翻译成机器可执行的显式指令。**



![754 个技能从 YAML 前置元数据到 Markdown 正文的"渐进式披露"架构示意，左侧是 30 token 扫描层，右侧是 500-2000 token 完整加载层](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/mukul975-anthropic-cybersecurity-skills-article/schematic-1.png)



举个例子。用户输入："分析这个内存转储，看看有没有凭据盗窃的痕迹。"

Agent 的内部流程是这样的：先扫描全部 754 个技能的前置元数据，匹配标签、描述和域，找到 12 个相关技能；然后加载前 3 个最匹配的——`performing-memory-forensics-with-volatility3`、`hunting-for-credential-dumping-lsass`、`analyzing-windows-event-logs-for-credential-access`；接着按照 Workflow 章节一步步执行，跑 Volatility3 插件、检查 LSASS 访问模式、关联 Windows 事件日志证据；最后用 Verification 章节验证结果，确认 IOC，把发现映射到 ATT&CK T1003（凭据转储）。

没有这些技能，agent 就是瞎猜命令。有了这些技能，它走的是一条高级 DFIR 分析师会走的同款路径。

## 五套框架一套技能库：合规的"一键五连"

这件事最让我觉得有意思的，不是技能数量，是框架映射的设计。

这个仓库把每一个技能都映射到了五套框架：MITRE ATT&CK v18、NIST CSF 2.0、MITRE ATLAS v5.4、MITRE D3FEND v1.3、NIST AI RMF 1.0。一个技能，五个合规复选框。原文给了一个实例——`analyzing-network-traffic-of-malware` 这个技能，同时命中 ATT&CK T1071、NIST CSF DE.CM、ATLAS AML.T0047、D3FEND D3-NTA、AI RMF MEASURE-2.6。

**这在安全合规领域是一个"一键五连"的操作——一次分析，五份报告的基础数据都有了。**



![单个技能映射到五套框架的雷达图，五个顶点分别是 ATT&CK、NIST CSF、ATLAS、D3FEND、AI RMF](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/mukul975-anthropic-cybersecurity-skills-article/schematic-2.png)



我们来拆一下这五套框架各自在仓库里的分量。

MITRE ATT&CK 覆盖了全部 14 个战术。侦察（TA0043）、初始访问（TA0001）、执行（TA0002）、持久化（TA0003）、提权（TA0004）、防御规避（TA0005）、凭据访问（TA0006）、横向移动（TA0008）、C2（TA0011）、数据渗出（TA0010）、影响（TA0040）都是 Strong 覆盖。资源开发（TA0042）和发现（TA0007）是 Moderate。顺便说一下，ATT&CK v19 在 2026 年 4 月 28 日已经落地了，把原来的防御规避（TA0005）拆成了两个新战术——Stealth 和 Impair Defenses。原文说技能映射会在后续版本更新，这个工作量可不小。

NIST CSF 2.0 是 2024 年 2 月发布的版本，新增了 Govern 职能，把适用范围从关键基础设施扩展到了所有组织。仓库里 Govern 相关技能 30+，Identify 120+，Protect 150+，Detect 200+，Respond 160+，Recover 40+。全部 22 个类别、106 个子类别都有对应映射。

MITRE ATLAS v5.4 覆盖了 16 个战术、84 个技术，包括 2025 年底新增的 agentic AI 攻击向量——AI agent 上下文污染、工具调用滥用、MCP 服务器入侵、恶意 agent 部署。这些是专门对 AI/ML 系统的对抗性威胁框架，和传统 ATT&CK 的视角完全不同。

MITRE D3FEND v1.3 则是 NSA 资助的防御对策知识图谱，267 个防御技术分布在 7 个战术类别里：Model、Harden、Detect、Isolate、Deceive、Evict、Restore。它用 OWL 2 本体构建，用共享的 Digital Artifact 层把防御对策和 ATT&CK 的攻击技术双向映射。

NIST AI RMF 1.0 加上 GenAI Profile（AI 600-1，2024 年 7 月发布），定义了 4 个核心职能——Govern、Map、Measure、Manage，72 个子类别。GenAI Profile 额外增加了 12 个生成式 AI 专属风险类别，从幻觉和数据隐私到提示注入和供应链风险。更关键的是，科罗拉多州的 AI 法案 2026 年 2 月生效，遵守 NIST AI RMF 的组织可以享受法律安全港保护。

**这意味着这些技能映射不只是"最佳实践参考"，而是直接关联到法律合规的硬需求。**

## 26 个安全域，从云安全到欺骗技术

754 个技能被分进 26 个安全域。我翻了一遍域列表，有几个数字值得单独拎出来说。

Cloud Security（云安全）拥有 60 个技能，涵盖 AWS、Azure、GCP 的加固、CSPM、云取证。这是最大的单一域。Threat Hunting（威胁狩猎）55 个，主打假设驱动的狩猎、LOTL 检测、行为分析。Threat Intelligence（威胁情报）50 个，覆盖 STIX/TAXII、MISP、feed 集成、攻击者画像。Web Application Security（Web 应用安全）42 个，OWASP Top 10、SQLi、XSS、SSRF、反序列化全在里头。Malware Analysis（恶意软件分析）39 个，静态/动态分析、逆向工程、沙箱分析。



![26 个安全域按技能数量排序的横向柱状图，Cloud Security 60 个排第一，Deception Technology 2 个排最后](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/mukul975-anthropic-cybersecurity-skills-article/schematic-3.png)



但真正让我愣神的是尾巴上的两个域：Compliance & Governance（合规与治理）只有 5 个技能，Deception Technology（欺骗技术）只有 2 个技能——蜜标和入侵检测金丝雀。

这不是疏忽，这是优先级信号。社区贡献的力量集中在攻击面最宽、日常运营最需要的域上。合规和欺骗技术的薄弱，恰好说明这个仓库的基因是实战操作，不是审计文档生成器。原文在贡献指南里也明确写了："Domains like Deception Technology (2 skills) and Compliance & Governance (5 skills) need the most help." 这是公开喊话，等社区来填坑。

OT/ICS Security（工控安全）28 个技能，覆盖 Modbus、DNP3、IEC 62443、历史数据防御、SCADA。这个域在传统安全培训里往往是盲区，但在真实世界里——想想 2021 年 Colonial Pipeline 事件——工控安全一旦出事就是基础设施级灾难。28 个技能不算多，但至少让 AI agent 有了一个可以起步的知识锚点。

Zero Trust Architecture（零信任架构）13 个技能，覆盖 BeyondCorp、CISA 成熟度模型、微隔离。Ransomware Defense（勒索软件防御）只有 7 个技能，但覆盖了前兆检测、响应、恢复和加密分析全流程。数量少，但链路完整。

## 平台兼容性这张牌，打得极其聪明

这个项目最巧妙的设计决策之一，是它选择了 agentskills.io 标准，而不是绑定任何单一平台。

> All platforms that support the agentskills.io standard can load these skills with zero configuration.

零配置。这句话的商业含义是：不管你是 Anthropic 的死忠，还是微软 GitHub Copilot 的用户，还是 OpenAI Codex CLI 的尝鲜者，还是 Google Gemini CLI 阵营的，这个技能库都能直接用。AI 代码助手列表拉出来——Claude Code、GitHub Copilot、Cursor、Windsurf、Cline、Aider、Continue、Roo Code、Amazon Q Developer、Tabnine、Sourcegraph Cody、JetBrains AI。CLI agent 有 OpenAI Codex CLI 和 Gemini CLI。自主 agent 有 Devin、Replit Agent、SWE-agent、OpenHands。Agent 框架和 SDK 有 LangChain、CrewAI、AutoGen、Semantic Kernel、Haystack、Vercel AI SDK，以及任何兼容 MCP 的 agent。



**这不是一个"为某个平台优化"的项目，这是一个"让所有平台都能用"的项目。生态位选得极其精准。**

而且安装方式只有两种：npx 一行命令，或者 git clone。npx skills add mukul975/Anthropic-Cybersecurity-Skills，回车，完事。这种极简的接入成本，在开发者社区里就是传播的燃料。

（值得一提的是，这个项目被多个 awesome list 收录——awesome-agent-skills、awesome-ai-security、awesome-codex-cli——还被 SkillsLLM 和 Openflows 索引。社区认可度在快速累积。）

## 网络安全人力缺口 480 万 vs. 754 个 AI 可读技能

ISC2 的数据显示，2024 年全球网络安全未填补职位达到 480 万个。480 万。这不是一个"行业缺人"的温和表述，这是一个"防御方正在系统性溃败"的数字。

AI agent 能不能填补这个缺口？原文的回答是：能，但前提是给它们结构化的领域知识。今天的 agent 能写代码、能搜网页，但它们缺的是"从业者手册"——那种把一个通用 LLM 变成一个能干活的安全分析师的东西。现有的安全工具仓库给你的是字典、payload、漏洞利用代码。没有一个仓库给 AI agent 提供结构化决策工作流：什么时候用哪个技术、前置条件是什么、怎么一步步执行、怎么验证结果。

这个项目填的就是这个坑。而且它强调了一句话：这些技能"编码了真实从业者的工作流，不是生成的摘要"。不是让 GPT 总结出来的，是人写的，人验证的。

> Existing security tool repos give you wordlists, payloads, or exploit code. None of them give an AI agent the structured decision-making workflow a senior analyst follows.

每一个技能遵循统一的目录结构：`SKILL.md`（YAML 前置元数据 + Markdown 正文）、`references/`（标准映射和深度技术流程）、`scripts/`（辅助脚本）、`assets/`（模板和检查清单）。前置元数据的字段包括 name（kebab-case，1-64 字符）、description（关键词丰富，供 agent 发现）、domain、subdomain、tags、atlas_techniques、d3fend_techniques、nist_ai_rmf、nist_csf。ATT&CK 技术映射记录在每个技能的 `references/standards.md` 文件中，也在发布版本的 ATT&CK Navigator 图层里。



![单个技能的目录结构树状图，SKILL.md 在顶层，references/、scripts/、assets/ 三个子目录展开显示内容](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/mukul975-anthropic-cybersecurity-skills-article/schematic-4.png)



社区贡献流程也设计好了：加新技能的 PR 标题格式是 `Add skill: your-skill-name`，48 小时内审核技术准确性和 agentskills.io 标准合规性。仓库遵循 Contributor Covenant 行为准则。

还有一个细节：作者 Mahipal Jangra 同时在跑一个全球学术调研——GARS-2026（Global Agentic AI Readiness Survey），60 个问题，匿名，由 SRH Berlin 监督。调研目标是测量安全专业人员、开发者和企业团队对 agentic AI 的实际准备程度——MCP 服务器、工具调用、治理、人在回路工作流。完成调研的人拿到 50 个 Casky Tokens，可以提前访问 casky.ai 的 Playground。

这个 Playground 也值得提一嘴：无需安装，浏览器打开就能跑真实的网络安全技能练习，看 AI agent 实时执行结构化技能，交互式探索 MITRE ATT&CK 映射的工作流，测试威胁狩猎、DFIR 和渗透测试场景。这是一个"先试后买"的体验层，把仓库的静态技能变成了动态演示。

**480 万缺口对 754 个技能——这不是一个"技术替代人力"的叙事，而是一个"让人力乘以 AI"的杠杆故事。**

## 戏谑收尾

我最后想说一件事。这个仓库的名字叫"Anthropic Cybersecurity Skills"，但它的 README 第一行就写着"Community Project — Not affiliated with Anthropic PBC"。这是一个独立社区项目，和 Anthropic 公司没有任何关系。

但它选择了 agentskills.io 标准——这个标准和 Anthropic 的 Claude Code 深度兼容。它的框架映射覆盖了 MITRE ATLAS——这个框架是 MITRE 在 Anthropic 和其他 AI 公司的安全研究基础上发展出来的。它的社区讨论区在 GitHub 上，但它的体验层在 casky.ai 上——这又是另一个独立产品。

三家关系奇奇怪怪的公司、一堆互相引用的开源标准、一个社区开发者、754 个技能文件，居然就这么纠缠在了一起，构成了 2026 年 AI 安全自动化最值得关注的基础设施之一。Mahipal Jangra 按下发布键的那一刻，大概也没想到自己正在给 480 万人的缺口打上一个可编程的补丁。

这件事最妙的地方在于：它既不是 Anthropic 做的，又处处都有 Anthropic 的影子。开源社区的逻辑，永远比公司战略有趣一百倍。

---

## 参考来源
- https://github.com/mukul975/Anthropic-Cybersecurity-Skills
- https://agentskills.io
- https://casky.ai
- https://www.isc2.org（2024 年全球网络安全人力缺口 480 万数据原始出处）
- https://attack.mitre.org/versions/v18/
- https://atlas.mitre.org/versions/v5.4/
- https://d3fend.mitre.org/versions/v1.3/
- https://www.nist.gov/cyberframework/csf-2-0
- https://www.nist.gov/itl/ai-risk-management-framework
- https://github.com/VoltAgent/awesome-agent-skills
- https://github.com/ottosulin/awesome-ai-security
- https://github.com/RoggeOhta/awesome-codex-cli
- https://skillsllm.com/skill/anthropic-cybersecurity-skills

#Anthropic #CybersecuritySkills #AI #Mapped #MITRE
