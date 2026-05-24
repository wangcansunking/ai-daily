# GitHub项目破局AI安全：754个技能让Claude Code秒变网络安全专家

> ai-daily · 2026 年 5 月 23 日 20:14 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/mukul975-anthropic-cybersecurity-skills-article-2/head.png)

凌晨两点十七分，SOC 值班屏幕上的告警又红了。一个初级分析师揉着眼睛，把内存转储文件拖进 Volatility3，对着插件列表发呆——该跑哪个？`windows.psscan` 还是 `windows.malfind`？先查进程列表还是先扫注入代码？他翻出内部 Wiki，三页操作手册写得像大学教材，看了五分钟还没找到第一步该敲什么命令。

隔壁工位，一个接了 Claude Code 的终端正在跑完全相同的任务。它扫描了 754 个技能条目，花了大概 30 个 token 一个的速度筛出 12 个相关项，然后加载了 Top 3，按 `Workflow` 字段里的步骤一条条执行——`volatility3 -f memory.dmp windows.psscan` → `windows.malfind` → 交叉比对 LSASS 进程访问模式 → 匹配 ATT&CK T1003（Credential Dumping）。从收到告警到输出结论，不到 90 秒。

![mukul975/Anthropic-Cybersecurity-Skills — 754 structured cybersecurity skills fo](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/mukul975-anthropic-cybersecurity-skills-article-2/content-1.png)

这两个画面之间的差距，就是 2026 年网络安全行业最残酷的断层线。ISC2 的数据说，2024 年全球网络安全岗位缺口达到 480 万个。而另一边，AI 编程助手已经从「能写代码」进化到「能跑完整的安全调查工作流」——前提是你得给它一本靠谱的操作手册。

**大部分 AI agent 在安全场景下不是能力不够，是根本没拿到正确的剧本。**

这就是 mukul975/Anthropic-Cybersecurity-Skills 这个仓库试图解决的问题。2026 年 3 月 11 日，一个叫 Mahipal Jangra 的开发者把它推上了 GitHub，初始版本 734 个技能、覆盖 26 个安全域、映射到 MITRE ATT&CK 和 NIST CSF 2.0。到 5 月初，技能数涨到 754 个，框架映射从 2 个扩展到 5 个——新增了 MITRE ATLAS、MITRE D3FEND 和 NIST AI RMF。不到两个月，它被收录进至少 6 个 awesome list 和技能目录，包括 SkillsLLM 和 VoltAgent 维护的 awesome-agent-skills 索引。

（值得一提的是，这项目跟 Anthropic 公司没有任何官方关系——README 里专门用 ⚠️ 图标加粗声明了「Community Project」「Not affiliated with Anthropic PBC」。名字里的「Anthropic」只是因为最初对 Claude Code 优化，这种命名方式在开源圈不罕见，但确实容易让人误以为是官方出品。）

---

## 754 个技能，5 套框架，一个 YAML 前端搞定合规

一个安全技能在 AI agent 眼中长什么样？这个仓库给出的答案是：YAML 前端 + Markdown 正文 + 参考文件目录的三层结构。每个技能一个文件夹，里面是 `SKILL.md`（定义文件）、`references/`（标准映射）、`scripts/`（辅助脚本）和 `assets/`（模板和检查清单）。

YAML 前端大概长这样——以 `performing-memory-forensics-with-volatility3` 为例：

```yaml
name: performing-memory-forensics-with-volatility3
description: >-
  Analyze memory dumps to extract running processes, network connections,
  injected code, and malware artifacts using the Volatility3 framework.
domain: cybersecurity
subdomain: digital-forensics
tags: [forensics, memory-analysis, volatility3, incident-response, dfir]
atlas_techniques: [AML.T0047]
d3fend_techniques: [D3-MA, D3-PSMD]
nist_ai_rmf: [MEASURE-2.6]
nist_csf: [DE.CM-01, RS.AN-03]
version: "1.2"
author: mukul975
license: Apache-2.0
```

Markdown 正文分四个固定段落：`## When to Use`（触发条件）、`## Prerequisites`（前置工具和权限）、`## Workflow`（逐步执行命令和决策点）、`## Verification`（验证执行成功的方法）。这个结构本身不新鲜——安全圈的操作手册几十年前就这么写了。真正有意思的是前端里那五个框架映射字段：`atlas_techniques`、`d3fend_techniques`、`nist_ai_rmf`、`nist_csf`，再加上 `references/standards.md` 里额外记录的 MITRE ATT&CK 技术 ID。

**一个技能，五个合规复选框。这不是技术炫技，这是给安全团队省了至少三层翻译工作。**



![单个技能映射到 5 套框架的关系图，中心是 SKILL.md，五个箭头分别指向 MITRE ATT&CK、NIST CSF 2.0、MITRE ATLAS、D3FEND、NIST AI RMF](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/mukul975-anthropic-cybersecurity-skills-article-2/schematic-1.png)



具体来说，这五套框架各管一摊：

MITRE ATT&CK v18 覆盖 14 个战术和 200+ 技术，描述攻击者的行为和 TTPs。仓库给每个战术都标了覆盖强度——Initial Access、Execution、Persistence、Privilege Escalation、Defense Evasion、Credential Access、Lateral Movement、Command and Control、Exfiltration、Impact 这 10 个战术全是「Strong」，Reconnaissance 和 Discovery 是「Moderate」到「Strong」，只有 Resource Development 和 Collection 标了「Moderate」。这个覆盖率相当诚实，没有吹「全覆盖」。

NIST CSF 2.0 是 2024 年 2 月才更新的版本，新增了 Govern 函数，把适用范围从关键基础设施扩展到所有组织。仓库映射了全部 6 个函数（Govern、Identify、Protect、Detect、Respond、Recover）和 22 个类别，引用 106 个子类别。Detect 函数下的技能最多，200+；Protect 和 Respond 各有 150-160；Govern 和 Recover 相对少，分别是 30+ 和 40+。

MITRE ATLAS v5.4 专门对 AI/ML 系统的对抗威胁，覆盖 16 个战术和 84 个技术。2025 年底新增了 AI agent 上下文投毒、工具调用滥用、MCP 服务器入侵和恶意 agent 部署等攻击向量。这个映射的价值在于，它让 AI agent 不仅能检测传统攻击，还能识别对自身基础设施的威胁。

MITRE D3FEND v1.3 是 NSA 资助的防御对策知识图谱，267 个防御技术按 7 个战术类别组织（Model、Harden、Detect、Isolate、Deceive、Evict、Restore），基于 OWL 2 本体论构建，可以双向映射到 ATT&CK 的进攻技术。

NIST AI RMF 1.0 加上 2024 年 7 月发布的 GenAI Profile（AI 600-1），定义了 4 个核心功能（Govern、Map、Measure、Manage）和 72 个子类别，GenAI Profile 额外增加了 12 个生成式 AI 特有风险类别——从虚构内容、数据隐私到提示注入和供应链风险。2026 年 2 月生效的科罗拉多州 AI 法案给遵守 NIST AI RMF 的组织提供了法律安全港，这意味着这套映射直接关联到合规义务。

我看到这个五层映射结构的第一反应是：这哥们要么是安全合规咨询师出身，要么被审计折磨过很多次。后来查了仓库的 Contributing 记录和社区讨论，发现 Jangra 确实在 README 里提到自己正在 SRH Berlin 的学术监督下做一项全球 Agentic AI 准备度调查（GARS-2026），60 个问题、匿名、结果以 CC-BY 4.0 公开发布——这明显是带着学术方法论在做工程落地。

---

## 26 个域，从云安全到欺骗技术，技能分布暴露了行业的真实痛点

754 个技能分到 26 个安全域里，分布极度不均。排前三的是 Cloud Security（60 个）、Threat Hunting（55 个）和 Threat Intelligence（50 个）。Web Application Security 有 42 个，Network Security 40 个，Malware Analysis 39 个，Digital Forensics 37 个，Security Operations 36 个，IAM 35 个。

往下看就开始出现断崖：SOC Operations 33 个，Container Security 30 个，OT/ICS Security 和 API Security 各 28 个，Vulnerability Management 25 个，Incident Response 25 个，Red Teaming 24 个，Penetration Testing 23 个。

再往下是长尾：Endpoint Security 17 个，DevSecOps 17 个，Phishing Defense 16 个，Cryptography 14 个，Zero Trust Architecture 13 个，Mobile Security 12 个。最后三个域加起来才 14 个技能：Ransomware Defense 7 个，Compliance & Governance 5 个，Deception Technology 只有 2 个——一个管 Honeytokens，一个管 Breach Detection Canaries。

**技能密度图本身就是一张行业资源分配的热力图——云、威胁狩猎、情报分析是主战场，欺骗技术和合规治理几乎还是荒地。**



![26 个安全域的技能数量柱状图，Cloud Security 60 排第一，Deception Technology 2 排最后，标注 480 万人才缺口数字](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/mukul975-anthropic-cybersecurity-skills-article-2/schematic-2.png)



这个分布不是随机形成的。Jangra 在 README 里明确说 Deception Technology（2 个技能）和 Compliance & Governance（5 个技能）「最需要帮助」，欢迎社区贡献。这暴露了一个事实：开源安全工具社区天然倾向于「动手型」技能——内存分析、流量抓包、漏洞扫描——而对策略、治理、欺骗这些「软技能」域关注不足。

但有意思的是，OT/ICS Security 已经有 28 个技能，覆盖 Modbus、DNP3、IEC 62443、SCADA 和 Historian 防御。这个域两年前在开源圈几乎是空白，现在能排到第 12 位，说明工控安全的需求在快速向开源社区渗透。Container Security 的 30 个技能里包含 K8s RBAC、镜像扫描、Falco 和容器取证，也反映了云原生安全栈的成熟度在提高。

每个技能被设计成渐进式加载：只扫 YAML 前端大概 30 个 token，完整加载一个技能需要 500-2000 个 token。这意味着 agent 可以在一次推理中扫描全部 754 个技能的前端（754 × 30 ≈ 22,620 tokens），找到相关技能后再选择性深加载。这个设计很务实——它承认了当前 LLM 上下文窗口虽然大了（Claude 现在支持 200K tokens），但你把 754 个完整技能全塞进去还是会爆。

仓库的 v1.0.0 发布在 2026 年 3 月 11 日，当时是 734 个技能、2 套框架映射。到 README 最新更新时（从内容推断大概是 5 月初），技能数涨到 754，框架映射从 2 套变成 5 套，还附带了一个 ATT&CK Navigator 图层文件。ATT&CK v19 预计 2026 年 4 月 28 日发布，会把 Defense Evasion（TA0005）拆成 Stealth 和 Impair Defenses 两个新战术，Jangra 说会在后续版本更新映射——说明这项目还在活跃迭代。

---

## agentskills.io 标准：为什么这不止是又一个 awesome list

如果你在 GitHub 上搜「cybersecurity skills」或「security playbooks」，会找到几十个仓库——有的收集漏洞利用代码，有的整理检测规则，有的堆砌工具清单。但 mukul975 这个仓库做了一件不同的事：它严格遵循 agentskills.io 开放标准，从第一天起就是为 AI agent 消费而设计的，不是给人读的教程合集。

agentskills.io 是一个专门定义 AI agent 可消费技能格式的社区标准。核心逻辑是：YAML 前端做快速发现和筛选，结构化 Markdown 做逐步执行，`references/` 做深度技术上下文。这个三层架构让 agent 可以「先扫目录再精读正文」，而不是把整本百科全书吞下去再吐出来。

仓库兼容的平台列表长到让人咋舌。AI 编程助手类：Claude Code（Anthropic）、GitHub Copilot（Microsoft）、Cursor、Windsurf、Cline、Aider、Continue、Roo Code、Amazon Q Developer、Tabnine、Sourcegraph Cody、JetBrains AI。CLI 代理类：OpenAI Codex CLI、Gemini CLI（Google）。自主代理类：Devin、Replit Agent、SWE-agent、OpenHands。Agent 框架和 SDK 类：LangChain、CrewAI、AutoGen、Semantic Kernel、Haystack、Vercel AI SDK，以及任何支持 MCP 协议的 agent。

**这不是一个工具，这是一个技能文件系统。任何说 agentskills.io 语言的 agent 都能即插即用。**



![agentskills.io 三层架构示意，YAML 前端 → Markdown 工作流 → references/ 深度参考，标注 token 消耗（30 / 500-2000）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/mukul975-anthropic-cybersecurity-skills-article-2/schematic-3.png)



实际使用流程是这样的：用户输入「Analyze this memory dump for signs of credential theft」，agent 先扫全部 754 个技能的前端（约 22,620 tokens），根据 tags、description、domain 匹配出 12 个相关技能；然后加载 Top 3——比如 `performing-memory-forensics-with-volatility3`、`hunting-for-credential-dumping-lsass`、`analyzing-windows-event-logs-for-credential-access`；接着按 Workflow 章节逐步执行命令，跑 Volatility3 插件、检查 LSASS 访问模式、关联 Windows 事件日志；最后用 Verification 章节验证结果，确认 IOC，映射到 ATT&CK T1003。

整个过程 agent 不是在「生成」操作步骤——它是在「执行」一个已经写好、验证过的操作手册。用仓库自己的话说：「没有这些技能，agent 只能猜命令、漏关键步骤；有了它们，它跟一个资深 DFIR 分析师用同一套剧本。」

这种设计理念让我想起 Karpathy 之前反复强调的一个观点：LLM 的瓶颈已经不是模型能力，而是给它什么上下文。这个仓库本质上是在给 AI agent 写一本它真正能读懂、能执行的安全操作手册——格式对、粒对、映射对。

---

## 社区反应、学术野心，和一个叫 Casky.ai 的 Playground

仓库发布不到两个月，被收录的列表已经不少：VoltAgent 维护的 awesome-agent-skills（1000+ 技能索引）、ottosulin 的 awesome-ai-security、RoggeOhta 的 awesome-codex-cli、SkillsLLM 的技能目录和 marketplace、Openflows 的信号分析追踪、NeverSight 的自动化技能 feed。Hasan Toor（@hasantoxr）在社交媒体上的评价被直接引用进了 README：

> "A database of real, organized security skills that any AI agent can plug into and use. Not tutorials. Not blog posts."

Medium 上的安全作者 fazal-sec 也给了评价：

> "This is not a random collection of security scripts. It's a structured operational knowledge base designed for AI-driven security workflows."

这两句评价精准抓住了这个仓库和传统安全资源库的本质区别：不是教程、不是博客、不是脚本合集——是一个 AI 原生的、可操作的知识库。

但最让我觉得有意思的不是这些收录和评价，而是 Jangra 在 README 里埋的一个学术调查链接。GARS-2026（Global Agentic AI Readiness Survey）是一个全球性的学术研究，测量安全专业人员、开发者和企业团队对 agentic AI 的实际准备度——包括 MCP 服务器、工具调用、治理和人机协同工作流。60 个问题、匿名、SRH Berlin 学术监督、结果以 CC-BY 4.0 公开发布。填了调查的人还能拿到 50 个 Casky Tokens，用于早期访问 casky.ai。



![GARS-2026 调查 → 754 技能库 → Casky.ai Playground 的生态关系图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/mukul975-anthropic-cybersecurity-skills-article-2/schematic-4.png)



这个 casky.ai 是个 Playground，不需要安装配置，直接打开浏览器就能跑网络安全技能演练——看 AI agent 实时执行结构化技能、交互式探索 MITRE ATT&CK 映射的工作流、测试威胁狩猎和 DFIR 场景。Jangra 把学术研究（GARS-2026）、开源技能库（这个仓库）和产品化落地（Casky.ai）串成了一条线，这操作很不像典型的「扔个开源项目上去看有没有 star」的心态。

从 Contributing 指南看，Jangra 对社区贡献的管理也很规范：提交新技能用 `Add skill: your-skill-name` 格式的 PR 标题，遵循 CONTRIBUTING.md 模板，48 小时内完成技术准确性审核和 agentskills.io 标准合规检查。项目用 Contributor Covenant 行为准则，Apache 2.0 许可证——允许个人和商业用途的自由使用、修改和分发。

---

## 从 480 万人才缺口到 754 个技能：AI 能补上多少？

ISC2 的 2024 年数据说全球有 480 万个网络安全岗位空缺。这个数字已经被引用烂了，但它背后的结构性矛盾很少被认真讨论：安全行业的知识传递方式还停留在「师傅带徒弟 + 内部 Wiki + 散落各处的博客文章」阶段。一个初级分析师从入职到能独立处理内存取证，通常需要 6-12 个月；要达到跨域调查能力（比如从云日志一路追到容器逃逸再追到横向移动），三年起步。

AI agent 理论上可以把这个学习曲线压到几分钟——前提是它有正确的知识库。这就是 754 个技能试图解决的核心问题：不是替代人类分析师，而是把资深分析师的决策工作流编码成 agent 可执行的格式，让初级分析师借助 AI 达到接近资深水平的调查质量。

**安全行业缺的不是工具，是工具使用经验的标准化和可传递性。**

但这个逻辑有一个隐含前提：技能库本身的质量要足够高，覆盖要足够广，更新要足够快。目前 754 个技能在 26 个域里的分布严重不均，Deception Technology 只有 2 个技能，Compliance & Governance 只有 5 个，Ransomware Defense 只有 7 个。ATT&CK v19 马上要拆分 Defense Evasion 战术，MITRE ATLAS 和 NIST AI RMF 的映射也还需要更多实战验证。这不是一个「完成态」项目——它是一个刚打好地基的框架，真正能填满它的是社区贡献。

还有一个更大的问题：agentskills.io 标准本身能走多远？目前支持它的平台已经不少——从 Claude Code 到 Gemini CLI 到 LangChain 到任何 MCP 兼容 agent——但如果这个标准不能成为事实上的行业约定，那 754 个技能就只是一个格式漂亮但孤立的资源库。好的一面是，agentskills.io 已经被 VoltAgent 的 awesome-agent-skills 索引（1000+ 技能）用，SkillsLLM 也把它列入了 marketplace，说明标准在社区层面积累了一定的采纳度。

Jangra 在 README 里留了一句话，说这个项目「fills the gap」——现有的安全工具仓库给你的是 wordlists、payloads、exploit code，但没有一个给 AI agent 提供结构化决策工作流。这句话不夸张。GitHub 上能搜到的安全仓库成千上万，但 99% 是给人用的：人知道什么时候用 Mimikatz、什么时候用 BloodHound、什么时候该停手叫 escalation。AI agent 不知道——除非你把这些判断逻辑写成它认识的格式。

这就是 754 个 YAML 文件和它们背后的 agentskills.io 标准在做的事。不性感，不炫技，但可能是 2026 年安全 AI 落地最务实的一条路。

---

## 参考来源
- https://github.com/mukul975/Anthropic-Cybersecurity-Skills
- https://agentskills.io
- https://skillsllm.com/skill/anthropic-cybersecurity-skills
- https://github.com/VoltAgent/awesome-agent-skills
- https://github.com/ottosulin/awesome-ai-security
- https://github.com/RoggeOhta/awesome-codex-cli
- https://openflows.org
- https://github.com/NeverSight/skills_feed
- https://casky.ai
- https://attack.mitre.org
- https://www.nist.gov/cyberframework
- https://atlas.mitre.org
- https://d3fend.mitre.org
- https://www.nist.gov/itl/ai-risk-management-framework

#Anthropic #CybersecuritySkills #AI #Mapped #MITRE
