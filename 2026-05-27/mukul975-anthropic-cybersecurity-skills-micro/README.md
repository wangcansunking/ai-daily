# Mukul Jangra 发布 Anthropic 网络安全技能集，兼容 Claude Code 等 20+ 平台

> ai-daily · 2026 年 5 月 27 日 02:41 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/mukul975-anthropic-cybersecurity-skills-micro/head.png)

凌晨两点四十七分，一个安全分析师盯着屏幕上的内存转储文件，光标在终端里闪了整整八分钟。不是他不会查——他知道该跑 Volatility3，知道要排查 LSASS 进程，知道最后要对照 MITRE ATT&CK 的 T1003 条目写报告。但他只有一个人，而待办队列里还有 23 个告警没分类。

隔壁组的实习生倒是很闲。他刚把同一个内存文件丢给 Claude Code，Agent 扫描了 754 个技能描述，在 30 个 token 内筛出 12 个相关项，然后锁定三个最匹配的技能：内存取证、凭证转储检测、Windows 事件日志关联。整个过程不到一次深呼吸的时间。

![mukul975/Anthropic-Cybersecurity-Skills — 754 structured cybersecurity skills fo](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/mukul975-anthropic-cybersecurity-skills-micro/content-1.png)

这不是 AI 替代安全分析师的烂俗故事。这是一个叫 Mukul Jangra 的独立开发者，在 GitHub 上放了一个 repo，然后告诉所有 AI Agent：「来，这是你的安全操作手册。」

**754 个技能不是给人类看的——这是给机器写的 API 文档。**



![AI Agent 扫描 754 个技能 frontmatter 的过程示意，30 token 每条的渐进式加载架构](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/mukul975-anthropic-cybersecurity-skills-micro/schematic-1.png)



## 五套框架压进一个 YAML，合规审计从此变成 grep 操作

让我愣神的是这个数字：五个框架。

MITRE ATT&CK v18 覆盖 14 个战术、200+ 技术点。NIST CSF 2.0 六个功能、22 个类别、106 个子类。MITRE ATLAS v5.4 专门打 AI/ML 对抗威胁，16 个战术、84 个技术点。D3FEND v1.3 是 NSA 砸钱搞的防御知识图谱，267 个防御技术按七个战术类别排兵布阵。NIST AI RMF 1.0 加上 2024 年 7 月出的 GenAI Profile，72 个子类加 12 个生成式 AI 专属风险项。

五套框架，各自为政，每一套都有自己的编号体系、自己的分类逻辑、自己的更新节奏。企业安全团队每年花在「把这个告警映射到那个合规框架」上的时间，足够培养出三个 CISSP。

而这个 repo 干了一件很暴力的事：每一条技能同时打上五套框架的标签。

举个例子——`analyzing-network-traffic-of-malware` 这个技能。在 ATT&CK 里它是 T1071（应用层协议），在 NIST CSF 里是 DE.CM（持续监控），在 ATLAS 里是 AML.T0047，在 D3FEND 里是 D3-NTA，在 AI RMF 里是 MEASURE-2.6。一个技能，五个合规复选框，一次 grep 全搞定。

> 这不是安全检查表，这是给 AI 准备的跨框架 Rosetta Stone。

而且这套映射不是拍脑袋标的。ATT&CK 覆盖了全部 14 个战术，从侦察（TA0043）到影响（TA0040），每个战术下面都有强覆盖的技能支撑。Credential Access（TA0006）方向有 Mimikatz 检测、Pass-the-Hash、凭证转储分析。Defense Evasion（TA0005）方向有混淆检测、Rootkit 分析、规避行为识别。NIST CSF 2.0 那边，Detect 功能绑了 200+ 个技能，Respond 功能绑了 160+ 个，连 2024 年 2 月才新加的 Govern 功能都有 30+ 个技能顶着。



![五套框架 + 一个技能的多对多映射关系图，中心是 SKILL.md，五条射线指向 ATT&CK / NIST CSF / ATLAS / D3FEND / AI RMF](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/mukul975-anthropic-cybersecurity-skills-micro/schematic-2.png)



## 754 个技能的目录结构，比大多数企业安全团队的 wiki 还清晰

Repo 本身的设计值得单拎出来说。每一个技能是一个独立目录，里面四样东西：SKILL.md（YAML frontmatter + Markdown 正文）、references/（放标准映射和深度工作流）、scripts/（能直接跑的辅助脚本）、assets/（填好的检查清单和报告模板）。

YAML frontmatter 的设计尤其精妙。`name` 用 kebab-case，1 到 64 个字符。`description` 塞满关键词，专门给 AI Agent 做语义匹配用。`tags` 数组让 Agent 在 30 个 token 的扫描阶段就能精准命中。然后是五个框架的映射字段一字排开：`atlas_techniques`、`d3fend_techniques`、`nist_ai_rmf`、`nist_csf`，ATT&CK 映射则放在 references/standards.md 里，ATT&CK Navigator 图层文件随 release 一起发。

Markdown 正文分四块：When to Use（触发条件，告诉 Agent 什么时候该激活这个技能）、Prerequisites（需要的工具、权限、环境）、Workflow（一步步的执行指南，带具体命令和决策分支）、Verification（怎么确认技能执行成功了）。

这四块结构不是给人类培训用的 PPT 大纲——这是给 AI Agent 写的运行时决策树。人类安全分析师看 Workflow 会嫌啰嗦，但 AI Agent 需要的就是这种「如果 A 则执行 B，如果 B 失败则回退到 C」的精确指令。

26 个安全域，从 Cloud Security（60 个技能，覆盖 AWS/Azure/GCP 加固、CSPM、云取证）到 Deception Technology（只有 2 个技能，蜜罐令牌和泄露检测金丝雀）。中间夹着 Threat Hunting（55 个）、Threat Intelligence（50 个）、Web Application Security（42 个）、Malware Analysis（39 个）、Digital Forensics（37 个）——每一块都是安全运营里真实存在的工种，不是教科书里的章节标题。



![26 个安全域按技能数量排序的柱状图，Cloud Security 60 个在最左，Deception Technology 2 个在最右，中间各域依次排列](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/mukul975-anthropic-cybersecurity-skills-micro/schematic-3.png)



有意思的是这个 repo 的定位。作者在 README 里第一行就写清楚了：「⚠️ Community Project — This is an independent, community-created project. Not affiliated with Anthropic PBC.」名字里带着 Anthropic，但跟 Anthropic 这家公司没有半毛钱关系。这种命名策略在开源圈并不罕见——借大厂的名号吸引眼球，然后用实打实的工程交付把人留下来。

而且交付的确实扎实。v1.0.0 是 2026 年 3 月 11 日发的，734 个技能、26 个域、ATT&CK + NIST CSF 2.0 双框架映射。之后 main 分支持续长，长到现在的 754 个技能，ATLAS、D3FEND、NIST AI RMF 三套框架的映射都是 post-release 追加上去的。一个独立开发者维护的项目，迭代节奏比某些拿了 A 轮融资的安全创业公司还快。

兼容性列表也值得一看。Claude Code、GitHub Copilot、OpenAI Codex CLI、Cursor、Gemini CLI——这五个是原生支持 agentskills.io 标准的。往下还有 Windsurf、Cline、Aider、Continue、Roo Code、Amazon Q Developer、Tabnine、Sourcegraph Cody、JetBrains AI 这一长串 AI 编程助手。再往下是 Devin、Replit Agent、SWE-agent、OpenHands 这些自治 Agent。最底层是 LangChain、CrewAI、AutoGen、Semantic Kernel、Haystack、Vercel AI SDK 这些 Agent 框架。MCP 兼容的 Agent 也能直接用。

这意味着什么？意味着你不需要在每一个 AI 工具里重新训练安全知识。clone 这个 repo，所有工具共享同一套技能库。安全策略的一致性这个老难题，在 AI Agent 时代居然被一个 Git 仓库解决了。

ISC2 的数据说 2024 年全球网络安全岗位缺口达到 480 万。480 万。这个数字大到已经失去了实感——你没法想象 480 万个空椅子摆在一起是什么场面。但这个 repo 给出了一个具体的、可执行的答案：把高级分析师脑子里的决策流程结构化，让 AI Agent 在告警分类、初步取证、合规映射这些环节顶上。

当然，Deception Technology 只有 2 个技能，Compliance & Governance 只有 5 个，这些薄弱的域还需要社区填坑。但 754 个技能的底子已经铺好了，剩下的就是贡献者往里面加砖。

一个叫 @mukul975 的人，在 GitHub 上放了一个 repo，然后告诉全世界：你的 AI Agent 终于可以像个正经安全分析师一样干活了。不是 2027 年，不是下一轮融资之后，是现在，npx 一行命令就能装。

**安全行业缺了 480 万人，但 AI 不缺一本好说明书。**

#Anthropic #CybersecuritySkills #AI #Mapped #MITRE
