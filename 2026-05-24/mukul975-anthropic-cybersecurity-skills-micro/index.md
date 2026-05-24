# 为AI特工配备754个网络安全技能：兼容Claude Code等20+平台

> ai-daily · 2026 年 5 月 24 日 13:19 · 来源：GitHub Trending python

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/mukul975-anthropic-cybersecurity-skills-micro/head.png)

凌晨两点十七分，一个安全分析师盯着屏幕上的内存转储，手指悬在键盘上——该跑哪个 Volatility3 插件？LSASS 凭证转储的特征是什么？云环境横跨三个供应商，从哪里开始溯源？

隔壁工位的 AI agent 也在等。但它等的不是灵感，是技能。没有结构化的工作流喂给它，再强的推理能力也只能在通用知识里打转——知道"应该分析内存"和知道"先用 windows.psscan 列出进程再用 windows.malfind 扫描注入代码"之间，隔着一条鸿沟。

![mukul975/Anthropic-Cybersecurity-Skills — 754 structured cybersecurity skills fo](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/mukul975-anthropic-cybersecurity-skills-micro/content-1.png)

现在有人往这道鸿沟里填了 754 块砖。

**这不是又一个安全工具合集。这是一本给 AI agent 写的操作手册。**

## 754 个技能，5 套合规框架——一个 skill 查完

2026 年 3 月 11 日，GitHub 用户 mukul975 发布了 Anthropic Cybersecurity Skills 的 v1.0.0 版本，初始包含 734 个结构化网络安全技能。到了 5 月，这个数字涨到了 754，覆盖 26 个安全域——从云安全（60 个技能）、威胁狩猎（55 个）、恶意软件分析（39 个）到欺骗技术（只有 2 个，急需社区贡献）——全部遵循 agentskills.io 开放标准。

但数量不是这个故事的核心。真正让我愣神的是它的映射体系：每个技能同时对齐五套框架——MITRE ATT&CK v18（覆盖全部 14 个战术，200+ 技术）、NIST CSF 2.0（6 个功能，22 个类别）、MITRE ATLAS v5.4（16 个战术，84 个 AI/ML 对抗技术）、MITRE D3FEND v1.3（7 个类别，267 个防御对策）和 NIST AI RMF 1.0（4 个功能，72 个子类别）。



![单个技能映射到五套框架的关系图，以 analyzing-network-traffic-of-malware 为例，展示 T1071→DE.CM→AML.T0047→D3-NTA→MEASURE-2.6 的链路](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/mukul975-anthropic-cybersecurity-skills-micro/schematic-1.png)



换句话说，agent 执行一次"分析恶意软件网络流量"的操作，五个合规复选框同时打勾。做安全的人都知道这意味着什么——平时为了满足不同框架的审计要求，同一件事要写五份文档。现在一个 YAML frontmatter 全包了。

这种映射密度在开源社区里是独一份。项目作者在 README 里写了一句很直白的话："No other open-source skills library maps every skill to all five frameworks. One skill, five compliance checkboxes."（值得一提的是，MITRE ATT&CK v19 在 2026 年 4 月 28 日刚刚落地，把 Defense Evasion 拆成了 Stealth 和 Impair Defenses 两个新战术，项目方承诺后续版本会更新映射。）

## 30 个 token 搜完全库——这架构设计比技能本身更有意思

技能库的技术做到藏着一些很聪明的决策。

每个技能文件遵循统一的目录结构：SKILL.md 作为主定义文件，YAML frontmatter 负责元数据，Markdown 正文分四个固定章节——When to Use（触发条件）、Prerequisites（前置要求）、Workflow（逐步执行指南）、Verification（验证步骤）。外加 references/、scripts/、assets/ 三个子目录放深度参考、辅助脚本和报告模板。



![技能目录结构示意，SKILL.md 展开显示 YAML frontmatter 和四个 Markdown 章节](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/mukul975-anthropic-cybersecurity-skills-micro/schematic-2.png)



这个结构的关键不在整齐，而在"渐进式加载"。扫描全部 754 个技能的前置元数据，每个约消耗 30 个 token——agent 可以一次过遍历整个库而不撑爆上下文窗口。匹配到相关技能后，再完整加载 500 到 2000 个 token 的详细工作流。项目文档给了一个具体例子：用户输入"分析这个内存转储，找凭证盗窃的痕迹"，agent 先是扫描 754 个 frontmatter，定位到 12 个相关技能；然后加载排名前三的——Volatility3 内存取证、LSASS 凭证转储狩猎、Windows 事件日志凭证访问分析；接着按 Workflow 章节逐步执行命令；最后用 Verification 章节验证结果，映射到 ATT&CK T1003（Credential Dumping）。

**token 是 AI agent 的算力货币。这个加载策略相当于给每块钱都标好了用途。**



![agent 处理"分析内存转储"的 4 步流程：扫描 754 frontmatter → 加载 top 3 → 执行 Workflow → 验证并映射 ATT&CK](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/mukul975-anthropic-cybersecurity-skills-micro/schematic-3.png)



兼容性方面，这个库目前对接了 20 多个 AI 平台：Claude Code、GitHub Copilot、OpenAI Codex CLI、Cursor、Gemini CLI、Windsurf、Cline、Aider、Continue、Devin、Replit Agent、LangChain、CrewAI、AutoGen 等等。任何支持 agentskills.io 标准的平台都能零配置加载。安装方式也极简——一行 `npx skills add mukul975/Anthropic-Cybersecurity-Skills` 或者直接 git clone。

但这里有一个容易被忽略的细节：项目作者在 README 顶部用黄色警告框明确标注了"Community Project — Not affiliated with Anthropic PBC."。名字里带着 Anthropic，实际跟 Anthropic 这家公司没有半毛钱关系。作者 Mahipal Jangra（@mukul975）顶着这个命名，要么是对 Anthropic 的 Claude 生态有某种执念，要么就是故意蹭一波流量——无论哪种，反正现在 awesome-agent-skills、awesome-ai-security 等好几个 Awesome List 都收录了它，SkillsLLM 的技能市场也上了架，star 数还在涨。

至于为什么需要这个库，项目方直接引用了 (ISC)² 的数据：2024 年全球网络安全职位缺口达到 480 万。AI agent 可以帮忙填坑——但前提是它手里有可执行的领域知识，而不只是会写代码和搜网页。现有的安全工具仓库给的要么是词表、payload、exploit 代码，要么是给人类看的教程和博客。没有哪个仓库告诉 AI agent：什么时候用这个技术、前置条件是什么、怎么逐步执行、怎么验证结果。Anthropic Cybersecurity Skills 填的就是这个空——它不是一个脚本合集，而是一个 AI 原生的知识库，把真实从业者的操作流程编码成了 agent 能理解的结构。

当然，754 个技能也不是均匀分布的。Deception Technology 域只有 2 个技能，Compliance & Governance 只有 5 个，Ransomware Defense 只有 7 个。威胁检测相关的域（Detect 功能下 200+ 个技能）明显比治理和恢复类丰满得多——这倒也符合安全从业者的日常重心：先看见威胁，再说其他的。

项目的下一个时间节点要看 ATT&CK v19 的映射更新什么时候落地。以及社区会不会真的把 Deception Technology 从 2 个技能推到两位数——文档里"good first issues"标签已经打好了，48 小时内审 PR 的承诺也摆在那里。至于那个跟项目绑定的 GARS-2026 全球 Agentic AI 就绪度调查，10 分钟 60 道题，做完送 50 个 Casky Token 能提前体验 casky.ai——这一套组合拳下来，mukul975 显然不只是想维护一个 GitHub 仓库，而是在搭一个生态入口。

安全行业的人均缺口 480 万，754 个技能只是开始。接下来就看 agent 是真的能照着操作手册干活，还是只会背手册里的句子。

## 参考来源
- https://github.com/mukul975/Anthropic-Cybersecurity-Skills

#Anthropic #CybersecuritySkills #AI #Mapped #MITRE
