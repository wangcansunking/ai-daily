# Anthropic 工程师发布 754 项安全技能：让 AI 通过 NIST、MITRE 五大合规框架

> ai-daily · 2026 年 5 月 29 日 08:26 · 来源：GitHub Trending python

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/mukul975-anthropic-cybersecurity-skills-article/head.png)

凌晨三点十七分，东京某 SOC 的值班分析师盯着三块屏幕上的告警洪流。SIEM 在尖叫，EDR 在闪烁，威胁情报平台刚推送了七条新的 IOC。他需要同时判断：这个内存 dump 里有没有 LSASS 凭证窃取的痕迹，那组 PowerShell 命令是不是 Cobalt Strike 的 beacon，以及云端的异常 API 调用究竟是一次误报还是攻击者已经进来了。他端起第四杯咖啡，手有点抖——不是因为咖啡因，而是因为他知道，全球还有 480 万个和他一样的岗位在空着，没人填。

同一时间，硅谷某间公寓里，一个叫 Mahipal Jangra 的工程师合上了笔记本电脑。他刚刚完成了一次 git push，仓库里躺着 754 个文件，每一个都精确刻画了刚才那位分析师脑子里的决策流程——只不过，这些文件不是给人看的，是给 AI 看的。

![mukul975/Anthropic-Cybersecurity-Skills — 754 structured cybersecurity skills fo](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/mukul975-anthropic-cybersecurity-skills-article/content-1.png)

**这不是一套安全工具集，这是一次对"经验"本身的工程化拆解。**

## 一张技能表，五套合规框架——AI 安全分析师的"驾照考试"

GitHub 上这个名为 `mukul975/Anthropic-Cybersecurity-Skills` 的仓库，在 2026 年 3 月 11 日发布了 v1.0.0 版本，当时包含 734 个技能。到我写这篇文章时，主分支已经膨胀到 754 个技能，横跨 26 个安全领域。每一个技能都遵循 `agentskills.io` 开放标准，用 YAML 前置元数据做索引，用结构化 Markdown 写执行流程，用 `references/` 文件夹放深度技术上下文。

让我愣神的是它的框架映射密度。这不是那种"我们参考了 NIST"的泛泛而谈——每一个技能都被精确打上了 MITRE ATT&CK v18（14 个战术、200+ 技术）、NIST CSF 2.0（6 个功能、22 个类别）、MITRE ATLAS v5.4（16 个战术、84 个技术）、MITRE D3FEND v1.3（7 个类别、267 个防御技术）和 NIST AI RMF 1.0（4 个功能、72 个子类别）的标签。五个框架，一个技能，五个合规检查项，一次搞定。



![单一技能映射到五套框架的蜘蛛网状关系图，中心是"analyzing-network-traffic-of-malware"，五条射线分别指向 ATT&CK T1071、NIST CSF DE.CM、ATLAS AML.T0047、D3FEND D3-NTA、AI RMF MEASURE-2.6](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/mukul975-anthropic-cybersecurity-skills-article/schematic-1.png)



用一个具体例子说清楚这件事的意义。仓库里有一个技能叫 `analyzing-network-traffic-of-malware`。在 ATT&CK 框架下，它映射到 T1071（应用层协议），属于 Command and Control 战术；在 NIST CSF 2.0 下，它对应 DE.CM（持续监控）；在 ATLAS 里，它关联 AML.T0047（AI 系统网络流量分析）；在 D3FEND 中，它激活 D3-NTA（网络流量分析防御技术）；在 NIST AI RMF 下，它落到 MEASURE-2.6（测量 AI 系统安全性）。一次技能执行，五份合规报告的数据点同时就位。对于正在过 SOC 2 或准备应对科罗拉多州 AI 法案（2026 年 2 月生效，值得一提的是，该法案为遵循 NIST AI RMF 的组织提供了法定安全港条款）的安全团队来说，这省掉的不是几个工时，是审计季的整条命。

**合规这件事，终于从 Excel 地狱变成了技能执行的副产品。**

## 26 个安全领域，754 个决策树——AI 终于学会了"什么时候该做什么"

这个仓库最让我觉得有意思的，不是它有多少个技能，而是它把"经验"这件事拆成了四个标准化段落：**When to Use**（触发条件）、**Prerequisites**（前置要求）、**Workflow**（分步执行）、**Verification**（结果验证）。

传统安全工具库给的是脚本和 payload。它们告诉你"怎么"做，不告诉你"什么时候"做，更不告诉你"做完之后怎么验证结果是对的"。一个初级分析师拿着 Volatility3 的插件列表，面对一个 32GB 的内存 dump，会陷入选择瘫痪。一个高级分析师拿到同一个 dump，脑子里浮现的是一条决策链：先检查进程列表有没有异常 PID → 再看网络连接有没有可疑外联 → 然后扫 LSASS 进程空间有没有 Mimikatz 痕迹 → 最后用 timeline 重建确认攻击时间线。

Jangra 做的事情，就是把这条决策链编码成 AI 能理解的结构化指令。每个技能的成本极低——扫描全部 754 个技能的前置元数据只需要大约 30 个 token 每个，完整加载一个技能的工作流需要 500 到 2000 个 token。这种渐进式加载架构意味着，AI agent 可以在一次推理过程中扫描全部 754 个技能，识别出相关的 12-15 个，然后只加载最匹配的前 3 个深度执行。



![AI agent 处理"分析这个内存 dump 有无凭证窃取痕迹"的四步流程：扫描 754 个 frontmatter → 筛选 12 个相关技能 → 加载 3 个最佳匹配 → 执行 Workflow + Verification](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/mukul975-anthropic-cybersecurity-skills-article/schematic-2.png)



仓库的 README 里给了一个精确的推演场景。用户输入一句"Analyze this memory dump for signs of credential theft"，agent 内部会做四件事：第一步，扫描 754 个技能的 YAML frontmatter，用 tag、description、domain 字段匹配出 12 个相关技能；第二步，加载前 3 个最佳匹配——`performing-memory-forensics-with-volatility3`、`hunting-for-credential-dumping-lsass`、`analyzing-windows-event-logs-for-credential-access`；第三步，按照 Workflow 章节的步骤逐一执行，运行 Volatility3 插件、检查 LSASS 访问模式、关联 Windows 事件日志证据；第四步，用 Verification 章节验证结果，确认 IOC，最终将发现映射到 ATT&CK T1003（Credential Dumping）。

没有这些技能，AI 只能瞎猜命令。有了它们，AI 走的是一条资深 DFIR 分析师踩过无数次的路。

**经验不再是师徒制的私产，它变成了可复制的配置文件。**

## 480 万 vs 754——一场用结构化知识对抗人力缺口的豪赌

ISC2 在 2024 年公布过一个让人胃疼的数字：全球网络安全岗位缺口达到 480 万。这不是说行业少了 480 万个入门级的 SOC 盯屏员，而是说少了 480 万个能独立判断、能做决策、能在压力下不出错的中高级分析师。培养一个这样的人需要 3 到 5 年，而攻击者的武器库每 6 个月迭代一次。

Jangra 的 754 个技能，本质上是在用另一种方式填补这个缺口。不是替代人，而是把那些"3 到 5 年才能积累的判断力"打包成 AI 可以即时调用的模块。云安全 60 个技能，覆盖 AWS、Azure、GCP 的加固、CSPM 和云取证；威胁狩猎 55 个技能，包含假设驱动的狩猎、LOTL（Living Off the Land）检测和行为分析；恶意软件分析 39 个技能，涵盖静态/动态分析、逆向工程和沙箱逃逸检测；数字取证 37 个技能，从磁盘镜像到内存取证到时间线重建。

但最让我在意的是两个数字：欺骗技术（Deception Technology）只有 2 个技能，合规与治理（Compliance & Governance）只有 5 个技能。这不是疏忽，这是信号。蜜罐和合规审计这类工作历史上高度依赖人工判断和定制化部署，标准化难度最大。Jangra 在 CONTRIBUTING.md 里明确写了，这两个领域"最需要社区贡献"——这等于在说："我开了头，但这两个硬骨头我啃不完，你们来。"



![26 个安全领域的技能数量柱状图，云安全 60 个高耸，欺骗技术 2 个几乎看不见，形成强烈对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/mukul975-anthropic-cybersecurity-skills-article/schematic-3.png)



从 v1.0.0 到现在的 754 个技能，增长发生在主分支上，而不是在 Release 里。v1.0.0 发布于 2026 年 3 月 11 日，当时只有 734 个技能和两套框架映射（MITRE ATT&CK + NIST CSF 2.0）。之后 ATLAS、D3FEND 和 NIST AI RMF 的映射是在主分支上陆续加进去的。这说明这个项目不是"做完就扔"的快餐式开源，而是真的在持续演进。ATT&CK v19 预计 2026 年 4 月 28 日发布，会把 Defense Evasion（TA0005）拆成 Stealth 和 Impair Defenses 两个新战术，Jangra 已经在 README 里预告了"技能映射将在后续版本中更新"。

**一个开源项目的成熟度，看它怎么对待上游框架的版本变更就知道了。**

## 跨平台兼容——从 Claude Code 到 Gemini CLI，20+ 平台的"通用语言"

这个项目最狡猾的设计，是它选择了一个叫 `agentskills.io` 的标准，而不是绑定任何一个特定的 AI 平台。结果就是，754 个技能可以在 Claude Code、GitHub Copilot、OpenAI Codex CLI、Cursor、Gemini CLI、Windsurf、Cline、Aider、Continue、Roo Code、Amazon Q Developer、Tabnine、Sourcegraph Cody、JetBrains AI 等 20 多个平台上即插即用。自主 agent 框架这边，Devin、Replit Agent、SWE-agent、OpenHands 也能直接加载。SDK 生态里，LangChain、CrewAI、AutoGen、Semantic Kernel、Haystack、Vercel AI SDK 和任何兼容 MCP 的 agent 都支持。



![754 个技能作为中心枢纽，辐射向 Claude Code、GitHub Copilot、Codex CLI、Cursor、Gemini CLI 等 20+ 平台图标](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/mukul975-anthropic-cybersecurity-skills-article/schematic-4.png)



这意味着什么？意味着一个安全团队今天用 Claude Code 做威胁狩猎，明天换到 Gemini CLI 做云安全审计，后天用 Cursor 写事件响应 playbook——同一套技能库，零迁移成本。这是典型的"写一次，到处跑"的标准化策略，和当年 Docker 容器镜像的思路如出一辙。它不是给某个平台写插件，而是给整个生态写基础设施。

> This is not a random collection of security scripts. It's a structured operational knowledge base designed for AI-driven security workflows.
> —— fazal-sec，Medium

> A database of real, organized security skills that any AI agent can plug into and use. Not tutorials. Not blog posts.
> —— Hasan Toor (@hasantoxr)，AI/tech creator

社区的反应也验证了这一点。这个仓库已经被收入 `awesome-agent-skills`（一个收录 1000+ 技能的索引）、`awesome-ai-security`（AI 安全工具集）和 `awesome-codex-cli`（Codex CLI 资源集）三个精选列表，同时在 SkillsLLM 技能市场和 Openflows 信号分析平台上被追踪。一个社区项目，没有 Anthropic 官方背书（README 里专门用 ⚠️ 标注了"Not affiliated with Anthropic PBC"），能在三个月内进入五个以上的聚合器，靠的只能是真有用。

Jangra 还在 README 底部嵌入了一个学术调查的链接——GARS-2026（Global Agentic AI Readiness Survey），60 个问题，匿名，由 SRH Berlin 监管，结果将以 CC-BY 4.0 开放获取。这操作很聪明：用开源项目吸引流量，用流量喂养学术研究，用研究反过来验证项目的价值假设。一个工程师，同时干了开源贡献者和学术研究者的活。

## 技能解剖——YAML 前置元数据如何让 AI 在 30 个 token 内做出判断

如果说 754 个技能是肌肉，那 YAML frontmatter 就是神经系统。我仔细看了 `performing-memory-forensics-with-volatility3` 这个技能的前置元数据，它长这样：

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

总共 15 行，大约 120 个词。AI 扫描这段内容只需要约 30 个 token，但已经能回答五个关键问题：这个技能是干什么的（description 里的关键词）？属于哪个安全子领域（subdomain）？需要什么前置能力（tags 暗示了 volatility3 和 dfir）？映射到哪些合规框架（五组 ID）？版本和作者是谁？



![YAML frontmatter 的字段结构图，左侧字段名（name/description/domain/tags/atlas_techniques 等），右侧对应的搜索匹配逻辑](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/mukul975-anthropic-cybersecurity-skills-article/schematic-5.png)



然后是 Markdown 正文的四段式结构。`## When to Use` 定义了触发条件——什么场景下 AI 应该激活这个技能而不是其他技能。`## Prerequisites` 列出工具、权限和环境要求。`## Workflow` 给出带具体命令和决策点的分步执行指南。`## Verification` 教 AI 怎么确认执行成功。每一个技能文件夹里还有 `references/`（标准映射和深度技术流程）、`scripts/`（辅助脚本）和 `assets/`（模板和检查清单）三个子目录。

这个目录结构的妙处在于，它同时为人类和 AI 做了优化。人类贡献者打开 `SKILL.md` 就能理解整个技能的全貌，AI agent 则可以用 frontmatter 做毫秒级筛选，然后按需加载正文和引用文件。它不是给 AI 写 prompt，而是给 AI 写"操作系统级别的能力模块"。

**prompt 是对话，skill 是能力。前者用完就忘，后者可以复用、组合、迭代。**

这件事让我想起 Karpathy 在 2025 年反复强调的一个观点：AI agent 的未来不在于更大的模型，而在于更好的工具调用和结构化知识。Jangra 的 754 个技能，恰好就是那个"更好的结构化知识"的具象化。他没有发明新的模型架构，没有训一个专有的大模型，只是把网络安全领域里散落在人脑中的隐性知识，用一套开放标准编码成了显性的、可执行的、可验证的技能单元。

至于这个项目能走多远，取决于两件事。一是社区是否真的会往 Deception Technology 和 Compliance & Governance 这些薄弱领域贡献高质量技能——2 个和 5 个实在太少了，撑不起完整的攻防覆盖。二是 `agentskills.io` 这个标准本身能不能活下来——如果它被某个大厂的私有标准吞掉，那 754 个技能就会变成一座孤岛。但至少现在，在 2026 年 5 月的这个节点上，一个独立工程师用一套开放标准撬动了全球 480 万岗位缺口的一角，这事儿本身就够摇滚的。

## 参考来源

- https://github.com/mukul975/Anthropic-Cybersecurity-Skills
- https://www.isc2.org/Research/Workforce-Study（ISC2 2024 网络安全人力缺口数据）
- https://attack.mitre.org/versions/v18/（MITRE ATT&CK v18）
- https://www.nist.gov/cyberframework（NIST CSF 2.0，2024 年 2 月发布）
- https://atlas.mitre.org/（MITRE ATLAS v5.4）
- https://d3fend.mitre.org/（MITRE D3FEND v1.3）
- https://www.nist.gov/itl/ai-risk-management-framework（NIST AI RMF 1.0 + GenAI Profile AI 600-1，2024 年 7 月）
- https://agentskills.io/（agentskills.io 开放标准）

#Anthropic #CybersecuritySkills #AI #Mapped #MITRE
