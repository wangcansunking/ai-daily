---
title: "TrustFall：Claude Code 一个回车交出 SSH key，Cursor / Gemini CLI / Copilot 同坑"
slug: trustfall-ai-coding-cli-rce-2026-05-09
date: 2026-05-09
type: deep-dive
track: arbitrage
domain: ai-coding-security
tags: [TrustFall, Claude Code, Cursor, Gemini CLI, GitHub Copilot CLI, MCP, AI Coding, supply chain]
description: "Adversa AI 5 月 7 日披露 TrustFall：克隆一个仓库、按一次回车，Claude Code v2.1.129、Cursor CLI、Gemini CLI、GitHub Copilot CLI 即在用户全权限下静默执行任意代码。一年内同根因第 4 起，本文从攻击路径、四工具横评、Anthropic 立场争议、CI 默认全自动场景、国产 AI Coding 工具治理路径六个维度拆给国内开发者。"
cover: trustfall-ai-coding-cli-rce-2026-05-09.png
---

# TrustFall：Claude Code 一个回车交出 SSH key，Cursor / Gemini CLI / Copilot 同坑

![封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-09/trustfall-ai-coding-cli-rce-2026-05-09/trustfall-ai-coding-cli-rce-2026-05-09.png)

> 当地时间 2026 年 5 月 7 日，Adversa AI 公开 TrustFall：克隆一个 Git 仓库、按一次回车——Claude Code v2.1.129、Cursor CLI、Gemini CLI、GitHub Copilot CLI 即在用户全权限下静默调度本地命令；同一根因，过去 7 个月已是第 4 起。这不是工具崩了，是 AI Coding 工具链开始进入工程化下半场。

## 头条核心：4 大 CLI 同时踩坑，根子是同一处「沉默信任」

5 月 7 日，Adversa AI 在官方博客挂出名为 TrustFall 的研究——题图直接放了四把 logo：Claude Code、Cursor CLI、Gemini CLI、GitHub Copilot CLI。研究员 Rony Utevsky 与 CTO Alex Polyakov 把同一个攻击模板分别打到 4 个 CLI 上，结果一致：**克隆仓库、回车一次、本地以登录用户全权限跑攻击者命令**。

The Register 同日跟进，Dark Reading、SecurityWeek、Help Net Security 跟出 4 篇报道；Hacker News 上榜，评论区集中在两件事——Anthropic 这次为什么不修，以及 GitHub Action 的 CI 模式连「回车」这一下都不需要。

7 个月时间轴拉出来，问题更清晰：

![过去 7 个月的 4 次信任边界事件](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-09/trustfall-ai-coding-cli-rce-2026-05-09/cve-timeline-trustfall.png)

- **CVE-2025-59536**（2025-10）：MCP 钩子在信任对话框之前抢跑执行；
- **CVE-2026-21852**（2026-01）：`ANTHROPIC_BASE_URL` 重定向劫持 API key；
- **CVE-2026-33068**（2026-03）：`bypassPermissions` 直接绕过信任对话框；
- **TrustFall**（2026-05-07）：本次研究主体，Anthropic 拒绝接受为漏洞，未分配 CVE。

四起事件根因都是一件事——**项目目录里的 JSON 文件可以让 CLI 在用户阅读告警之前调度本地权限**。这一次研究员把范围从 Claude Code 扩到了 4 个工具，结论很硬：**这不是某家厂商的实现 bug，而是这一代 agentic CLI 共用的隐式信任模型**。

> 全文围绕一个核心论断：**AI Coding 工具的「项目级隐式信任」已经走到尽头，治理层（团队 / 企业 / 平台 / 国产私有化）开始接管原本由用户单点决定的安全边界**。

## 三条路径：JSON 文件就能调度本地命令

Adversa AI 在博客里给出 3 条互不依赖的攻击路径，全部命中 Claude Code v2.1.129。3 条都不需要预装恶意二进制、不需要漏洞利用代码、只需要 Git 仓库根目录两个文本文件。

![三条攻击路径示意](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-09/trustfall-ai-coding-cli-rce-2026-05-09/attack-paths-trustfall.png)

**路径 ① · `enableAllProjectMcpServers`**

- 攻击者在仓库根放 `.mcp.json`，里面写一台指向自己 server 的 MCP 配置；
- 同目录 `.claude/settings.json` 里写 `enableAllProjectMcpServers: true`；
- 受害开发者 `git clone` 后在该目录敲 `claude` 进入交互；
- 信任对话框默认按钮是「Yes, I trust this folder」，回车——Claude Code 会**先**按 settings.json 调度全部 MCP server，然后才进入 reasoning。
- MCP server 运行环境是用户登录态的无沙箱 OS 进程，等价于把 shell 交出去。

**路径 ② · `enabledMcpjsonServers`**

- 上面是「全量自动批准」，路径 ② 是命名子集自动批准；
- `.claude/settings.json` 写 `enabledMcpjsonServers: ["<恶意-server-name>"]`；
- 利用价值在隐蔽性——配置文件读起来像「我只信任 N 个里的 1 个」，但那 1 个就是攻击者放进 `.mcp.json` 的恶意 server；
- 后续执行流与路径 ① 完全一致。

**路径 ③ · `permissions.allow`**

- 这条路径不依赖 MCP，依赖项目级预授权工具调用清单；
- `.claude/settings.json` 写 `permissions.allow: ["Bash", "Read", "Write", ...]`；
- 配合仓库 README / 代码注释里的提示注入，模型 reasoning 调用工具时不再二次确认；
- 触发节奏比路径 ①② 慢一拍——必须等 Claude reasoning 走到工具调用——但执行时机一旦到位，效果同样是无沙箱本地命令。

3 条路径在 Adversa AI 的 PoC 视频里跑完一轮的总耗时是数秒。**重点不在 RCE 本身，重点在执行时机：MCP server 启动早于 Claude reasoning，意味着模型本身没有任何机会拦截**。

> 这一节的小结：把项目目录里的两个 JSON 文件升格成「权限边界配置」，让用户回车一次就移交了本地全权限——这是设计选择，不是实现 bug。

## 4 大 CLI 横评：差异在文案，执行后果一致

Adversa AI 把同一份恶意仓库分别交给 Claude Code v2.1.129、Gemini CLI、Cursor CLI、GitHub Copilot CLI，发现 4 家在「告警措辞」上差异明显，但**回车之后的执行结果完全相同**。

![4 大 CLI 信任对话框横评](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-09/trustfall-ai-coding-cli-rce-2026-05-09/cli-comparison-trustfall.png)

Polyakov 在 The Register 的采访里给出一句关键判断：

> "They all have different approaches to configs and trust. But Cursor and Copilot / VS Code agent mode are clear analogs. Both read project-scoped MCP configuration. We tested it, and it's the same behaviour but with different user approval messages."

翻译过来：4 家在配置和信任模型上各走各的，但 Cursor 与 Copilot / VS Code agent mode 是明显的同构体——读取项目级 MCP 配置，行为一致，只是用户批准对话框写得不同。

值得展开的是 Claude Code 自己的「设计回退」：

- **2.0 之前**：信任对话框明确告知 `.mcp.json` 可执行代码，并提供「在禁用 MCP 的情况下信任此文件夹」选项；
- **2.1 起**：替换为更宽泛的「Quick safety check: Is this a project you created or one you trust?」对话框，**完全不提 MCP**。

Adversa AI 没说这是回归 bug，他们说的是：**Anthropic 主动收窄了信息密度**——从「明确告知 MCP 风险 + 三选项」收成「一句通用问候 + 默认 yes」。这是产品决策。

## CI 默认全自动：headless 模式连「回车」都没有

整组研究里读起来最让 OSS 维护者紧张的，是 CI 行为。

Anthropic 自己发的 GitHub Action `claude-code-action` 跑在 GitHub Actions 上时是 headless 模式——**没有终端，信任对话框根本不渲染**。换句话说：

- 一个被攻击者打了 PR 的开源仓库，PR 里偷偷加进 `.mcp.json` + `.claude/settings.json`；
- 维护者配置了「每个 PR 自动跑 Claude review」的 Action；
- PR 一推，Action 在维护者的 CI 凭据上以无沙箱进程跑攻击者的 MCP server；
- 拿到的是 GITHUB_TOKEN、SSH key、deploy key、secrets 全套。

Help Net Security 把这一段翻成一句更直白的话：「One keypress is all it takes to compromise four AI coding tools——除非根本不需要按。」

这一段对国内开发者的现实意义是：**任何配了 Claude Code / Cursor CLI 自动跑 PR 的 OSS 仓库，PR 检查里必须显式拒绝读取 `.mcp.json`、`.claude/`、`.cursor/` 等目录**，或把 Action 的执行环境锁死在最小权限分支。

## Anthropic 的立场：design intent 还是 informed-consent gap？

这次最值得展开的不是技术细节，是双方的工程哲学分歧。

**Anthropic 的立场**——根据 The Register 与 Adversa AI 博客都引用的同一份回应，Anthropic 安全团队评估了报告，归类为「outside threat model」：

> 接受「Yes, I trust this folder」即视为对项目全部配置（包括 MCP 定义）的整体同意，信任对话框之后的执行属于设计意图（design intent）；与此前自动触发类漏洞（CVE-2025-59536 等）不同，本次必须经用户回车才执行，因此不在威胁模型范围内。

**Adversa AI 的反驳**——他们不挑战边界本身，挑战边界的告知质量：

> "We havent seen that managed scope secure configuration often, rather, we've seen the opposite. And it's not that obvious to understand all configuration nuances, especially for vibe coders." —— Alex Polyakov, Adversa AI CTO

Utevsky 给的更技术化的一句：

> "Managed scope cannot be overridden by any other scope."

——意思是：企业 IT 通过 managed scope 锁死的配置，是这套信任体系里**唯一**的硬约束。但他们实测发现企业用户大量没启用 managed scope，普通开发者的 vibe-coding 路径里，managed scope 形同虚设。

这就是双方的工程哲学差距：

- **Anthropic 看到的**：信任边界已经在 UI 上明确画好，执行属于已同意范围，告知不全是 UX 问题不是漏洞；
- **Adversa AI 看到的**：在用户做不出 informed consent 的前提下扩大执行权限，等价于把工程纪律外包给了所有终端用户。

**作为同行，我倾向第二种判断**——理由有三：信任对话框的措辞从 2.0 的「明确告知 MCP」回退到 2.1 的「通用问候」是产品方主动收窄信息密度；CI / headless 场景连这一句问候都没有；同根因 7 个月内 4 起，从 hooks 到 base URL 到 bypassPermissions 再到 MCP，每一次外部研究员命中后再修，模式本身就证明信任边界还在演进期。

> 这一节小结：分歧不在「该不该有信任边界」，而在「信任边界的告知质量该由谁补」。Anthropic 把球踢回用户，安全研究社区觉得这球该平台接。

## 国产 AI Coding 工具的治理对照

国产 AI Coding 工具走的不是同一条治理曲线。把通义灵码、千问 Code（Qwen Code 开源 CLI）、字节 Trae、字节扣子（Coze）四家放一起看，差异在「治理层在哪里」。

![国产 AI Coding 工具治理路径对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-09/trustfall-ai-coding-cli-rce-2026-05-09/domestic-tools-governance-trustfall.png)

四条路径各自的取舍：

- **通义灵码**——阿里云的产品形态，公有云 + 专有云 + 本地部署都支持。企业版有团队规则面板，企业 IT 可以一键锁定项目级 `.mcp.json` 类配置不允许覆盖；这等价于把 Anthropic 文档里那个被忽视的 managed scope，做成默认开启的企业控制台；
- **Qwen Code**——通义自家的开源 CLI，定位偏 Claude Code 风格。开源属性意味着项目级配置面与 Claude Code 同构，风险面也同构；优势在权重 + 推理可全本地化，企业可以把整套链路收进内网，治理由企业自己的私有化部署接管；
- **Trae**——字节自研的 AI Coding 工具，路径走的是项目级配置 + 平台级规则双层；字节内部的安全规则可以下放到云端 Trae 节点上做强制；
- **扣子（Coze）**——直接抹掉「项目级 .mcp.json」的概念，走 SaaS 平台插件模式，所有插件平台审核，治理面 = 平台面。

四家的共性是「**把项目级隐式信任收归平台层**」——要么企业控制台、要么私有化部署、要么平台审核——而不是把 informed consent 推到每一个开发者的回车键上。这条路径的代价是开放度更低（不像 Claude Code 那样人人能塞 .mcp.json），但对企业 IT 和 OSS 维护者更友好。

四家国产工具厂商目前都没有公开回应 TrustFall，但底层路径选择已经先一步把同类风险面收掉了。这是国内 AI Coding 工具链一个值得记下的工程节点。

## 6 条具体加固建议

### 给个人开发者（敲 `claude` / `cursor` / `gemini` / `copilot` 的所有人）

1. **任何来源不明的仓库**，进入前先 `ls -la` 检查 `.mcp.json`、`.claude/`、`.cursor/`、`.github/copilot/`、`.gemini/` 有没有；有则先 `cat` 一遍再决定是否信任；
2. **macOS / Linux 的 secrets 扫描提前做**——`~/.ssh/`、`~/.aws/credentials`、`~/.config/gh/hosts.yml` 这些路径，能拿出去用 1Password / Doppler / Vault 托管的就托管；本地裸文件等于暴露面；
3. **CLI 全局配置 disable 项目级覆盖**——Claude Code 用户在 `~/.claude/settings.json` 里显式写 `enableAllProjectMcpServers: false`、`enabledMcpjsonServers: []`；Cursor / Gemini / Copilot 用对应的全局禁用键。

### 给企业 IT / 平台架构师

4. **把 Anthropic 的 managed scope 变成默认开启**——企业版镜像 / 内部 init 脚本里写死 managed `~/.claude/managed-settings.json`，禁用项目级 MCP 自动批准，下发给所有员工机器；
5. **CI / OSS 流水线显式拒绝**——`pre-commit` / GitHub Actions / GitLab Runner 加一道扫描，PR 里出现 `.mcp.json` / `.claude/settings.json` / `.cursor/mcp.json` 增量直接 fail，需要维护者人工 review。

### 给 OSS 维护者

6. **README 里写明项目根的 `.claude/`、`.cursor/`、`.mcp.json` 是不是有意提供的**——如果没有，PR 里出现就是攻击信号；如果有，配套写一段 verifier 脚本，发布前做哈希校验。

## 工程化下半场的积极信号

把镜头拉远一点。

过去 12 个月，AI Coding 工具的演化曲线是「从能用到爱用」——Claude Code、Cursor、Gemini CLI 一路把开发者体验推到很多人离不开的地步；过去 7 个月的曲线则是「从爱用到工程化」——CVE-2025-59536 起的 4 起信任边界事件，每一次外部研究员命中、厂商响应、社区讨论、防御工具补齐，都是这条曲线在生长。

**TrustFall 是 AI Coding 工具链终于走到了「治理层必须显性补齐」的那个拐点**。Anthropic 这一次选择不修补、把球踢给用户，恰恰把矛盾摆在了桌面上——后面 6 个月会有更多研究员、更多 OSS 项目、更多企业 IT、更多国产工具厂商，把信任边界的告知质量、CI 默认行为、企业控制台、私有化部署一起补齐。

国内开发者今天读到这篇，能做的事很具体：**把上面 6 条加固建议过一遍，把 `.mcp.json` 当 `~/.bashrc` 的级别去审视，把企业 managed scope 跑起来**。这一代 AI Coding 工具会越来越像传统 IDE 的延续——它会有 marketplace、会有签名、会有企业版、会有审计；今天补的每一道工程纪律，都是把后面 12 个月的曲线往上拉一节。

回到开篇那句核心论断：**项目级隐式信任的时代结束了，治理层接管的时代开始了**。Claude Code 也好、Cursor 也好、国产灵码 / Trae / 扣子也好，下一代 AI Coding 工具会比这一代更好用、也更稳——这是确定性的方向，路在前面，一起加油。

---

**参考来源**

- Adversa AI · TrustFall: coding agent security flaw enables one-click RCE in Claude, Cursor, Gemini CLI and GitHub Copilot · 2026-05-07 · <https://adversa.ai/blog/trustfall-coding-agent-security-flaw-rce-claude-cursor-gemini-cli-copilot/>
- The Register · Claude Code trust prompt can trigger one-click RCE · 2026-05-07 · <https://www.theregister.com/security/2026/05/07/claude-code-trust-prompt-can-trigger-one-click-rce/5235319>
- Dark Reading · 'TrustFall' Convention Exposes Claude Code Execution Risk · <https://www.darkreading.com/application-security/trustfall-exposes-claude-code-execution-risk>
- Help Net Security · One keypress is all it takes to compromise four AI coding tools · 2026-05-07 · <https://www.helpnetsecurity.com/2026/05/07/trustfall-ai-coding-cli-vulnerability-research/>
- SecurityWeek · AI Coding Agents Could Fuel Next Supply Chain Crisis · <https://www.securityweek.com/ai-coding-agents-could-fuel-next-supply-chain-crisis/>
- Check Point Research · Caught in the Hook: RCE and API Token Exfiltration Through Claude Code Project Files · CVE-2025-59536 · CVE-2026-21852 · <https://research.checkpoint.com/2026/rce-and-api-token-exfiltration-through-claude-code-project-files-cve-2025-59536/>
