---
title: "Claude Code 失守 · OpenAI 语音 GA | AI 日报 | 2026-05-09"
date: 2026-05-09
weekday: 星期六
category: newsletter
slug: 2026-05-09
description: "Adversa AI 公开 TrustFall：Claude Code v2.1.129、Cursor CLI、Gemini CLI、GitHub Copilot CLI 一个回车交出 SSH key、同根因第 4 起；OpenAI 5/8 一次发三个实时语音模型 GPT-Realtime-2 / Realtime-Translate / Realtime-Whisper、Realtime API 转 GA、ASR+MT+TTS 三段流水线开始走向终结；中国移动苏州发布全球首款 AI-eSIM 与 1+3+9 体系，把通信凭证、数字身份、大模型账号写进一颗硅片；HN 顶贴 561 pts 立论『agents need control flow, not more prompts』；vllm-mlx 把 vLLM 服务范式搬到 Apple Silicon、M4 Max 单机 463 tok/s；anthropics/financial-services 5 天冲到 15,079⭐ 登 GitHub Trending 榜首。"
cover: 09.png
---

# Claude Code 失守 · OpenAI 语音 GA | AI 日报 | 2026-05-09

![AI 日报封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-09/daily/09.png)

**今日关键词：Adversa AI 5/7 公开 TrustFall · Claude Code v2.1.129 / Cursor CLI / Gemini CLI / GitHub Copilot CLI 一个回车交出 SSH key · 同根因 7 个月内第 4 起 · OpenAI 5/8 一次发 GPT-Realtime-2 / Realtime-Translate / Realtime-Whisper 三模型 + Realtime API 转 GA · 中国移动苏州 5/8 全球首发 AI-eSIM 与 1+3+9 多生态体系 · HN 顶贴 561 pts 立论「agents need control flow, not more prompts」· vllm-mlx 把 vLLM 服务范式搬到 Apple Silicon · M4 Max 单机 463 tok/s · anthropics/financial-services 5 天冲到 15,079⭐ · xAI Grok 4.3 Image Quality API 5/7 全量开放**

---

## 📋 头版目录（一屏扫完今日）

- 🛡Adversa AI 公开 TrustFall · Claude Code 一个回车交出 SSH key · Cursor / Gemini CLI / Copilot 同坑 → 头条
- 🛡同根因 7 个月内第 4 起 · CVE-2025-59536 / 21852 / 33068 之后 Anthropic 这次拒认 → 头条
- 🚀OpenAI 5/8 一次发三个实时语音模型 · GPT-Realtime-2 / Translate / Whisper · API 转 GA → 精选要闻
- 🧠GPT-Realtime-2 上下文 32K → 128K · 推理强度 5 档可调 · Big Bench Audio 81.4% → 96.6% → 精选要闻
- 🇨🇳中国移动苏州 5/8 全球首发 AI-eSIM · 1+3+9 体系 · 「运营商码号即大模型账号」 → 国内 AI
- 🇨🇳国产语音四家同档铺底 · 字节 Seed LiveInterpret 2.0 / 豆包 / 通义 / 讯飞星火 / Kimi 语音 → 国内 AI
- 🎙HN 顶贴 561 pts · bsuh「agents need control flow, not more prompts」· 范式之争开打 → 名人说
- 🛠Anthropic Code with Claude 大会 5/7 · Claude Code Routines / 多 agent 编排 / 新记忆系统 → AI Coding
- 🛠Claude Code 5/7 更新 · /color 随机会话色 · /mcp 显示工具数 · --plugin-dir 接 .zip → AI Coding
- 🛠Cursor 3.3 上线 · 上下文使用面板 · Composer 2 节奏稳定 → AI Coding
- 🍎vllm-mlx 把 vLLM 搬到 MLX · M4 Max 单机 463 tok/s · 接 Claude Code 一行环境变量 → 国内 AI
- 📦anthropics/financial-services · Python · 15,079⭐（5 天 +14,672）· 登顶 GitHub Trending → GitHub Trending
- 📦addyosmani/agent-skills · Shell · 35,330⭐（当日 +1,893）· AI Coding 工程手册跟进 → GitHub Trending
- 📦Hmbown/DeepSeek-TUI · Rust · 21,714⭐（当日 +3,731）· DeepSeek 终端跑 → GitHub Trending
- 📦datawhalechina/hello-agents · Python · 44,536⭐（当日 +667）· 国产《从零构建智能体》教程 → GitHub Trending
- 📦HKUDS/AI-Trader · Python · 14,598⭐ · 港大开源全自动 agent 量化交易 → GitHub Trending
- 📦waybarrios/vllm-mlx · Python · 1,128⭐ · M 系 Mac 跑 Claude Code 后端 → GitHub Trending
- 🚀xAI Grok 4.3 上线 · 1M 上下文 · Image Quality API 5/7 开放 · 8 个旧模型 5/15 退役 → 快讯
- ⚖️Pennsylvania 起诉 Character.AI · 测试中 Emilie 伪造执业心理医师 → 快讯
- 🔬Stanford AI Index 2026 · 65% 美国成年人预期 AI 减少就业岗位 → 快讯
- 🔭TrustFall 之后 managed scope 会不会成为企业部署默认 → 值得关注
- 🔭OpenAI 实时语音 GA 之后 · 国产同传四家追平节奏 → 值得关注
- 🔭HN 顶贴范式之争 · Dify / 扣子 / LangGraph / MetaGPT 工程边界重画 → 值得关注

---

## ⏱ 公众号版 30 秒速览

> 今天最值得花时间的三件事：

- **Claude Code 一个回车交 key**：Adversa AI 5/7 公开 TrustFall——克隆一个仓库、按一次回车，Claude Code v2.1.129 / Cursor CLI / Gemini CLI / GitHub Copilot CLI 全部以登录用户全权限静默执行任意代码。同根因 7 个月内第 4 起，Anthropic 这次拒绝认定为漏洞。任何自动跑 PR review 的 OSS 仓库都该把 `.mcp.json` / `.claude/` / `.cursor/` 拉进 PR 黑名单。
- **OpenAI 三发实时语音模型**：5/8 凌晨 OpenAI 一次发了 GPT-Realtime-2、Realtime-Translate、Realtime-Whisper 三个模型，Realtime API 正式 GA。GPT-5 级推理 + 128K 上下文 + 70 语种听 13 语种说，Translate 0.034 美元/分钟、Whisper 0.017 美元/分钟，把「ASR 厂 + MT 厂 + TTS 厂」三段流水线压成单 API。国产语音四家——字节豆包、阿里通义、讯飞星火、月之暗面——基础设施已具同档身位。
- **中国移动 AI-eSIM 全球首发**：5/8 苏州 2026 移动云大会，副总经理陈怀达发布全球首款 AI-eSIM 与「1+3+9」多生态体系。一颗焊在主板的硅片同时承载通信凭证、数字身份和大模型账号，MoMA 平台预接 DeepSeek / 豆包 / Qwen / Kimi / GLM。可穿戴用 Cat.1 通信芯片重量降 82%，AI 玩具 / AI 眼镜 2026 年内首批量产。

---

## 🔥 头条：4 大 AI Coding CLI 同坑——Claude Code 一个回车，SSH key 就走了

当地时间 2026 年 5 月 7 日，Adversa AI 在官方博客挂出名为 **TrustFall** 的研究——题图直接放了四把 logo：Claude Code、Cursor CLI、Gemini CLI、GitHub Copilot CLI。研究员 Rony Utevsky 与 CTO Alex Polyakov 把同一个攻击模板分别打到 4 个 CLI 上，结果一致：**克隆仓库、回车一次、本地以登录用户全权限跑攻击者命令**。The Register 同日跟进，Dark Reading、SecurityWeek、Help Net Security 跟出 4 篇报道；Hacker News 上榜，评论区集中讨论两件事——Anthropic 这次为什么不修，以及 GitHub Action 的 CI 模式连「回车」这一下都不需要。

![4 大 CLI 信任对话框横评](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-09/daily/cli-comparison-trustfall.png)

把 7 个月时间轴拉出来，问题更清晰：

| 事件 | 公开日期 | 攻击面 | 处置 |
|---|---|---|---|
| **CVE-2025-59536** | 2025-10 | MCP 钩子在信任对话框之前抢跑执行 | 已修复 + 分配 CVE |
| **CVE-2026-21852** | 2026-01 | `ANTHROPIC_BASE_URL` 重定向劫持 API key | 已修复 + 分配 CVE |
| **CVE-2026-33068** | 2026-03 | `bypassPermissions` 直接绕过信任对话框 | 已修复 + 分配 CVE |
| **TrustFall** | 2026-05-07 | 4 大 CLI 项目级 JSON 隐式信任 | Anthropic 拒认 + 未分配 CVE |

四起事件根因都是同一件事——**项目目录里的 JSON 文件可以让 CLI 在用户阅读告警之前调度本地权限**。这一次研究员把范围从 Claude Code 扩到了 4 个工具，结论很硬：**这不是某家厂商的实现 bug，而是这一代 agentic CLI 共用的隐式信任模型**。

### 一、三条路径：JSON 文件就能调度本地命令

Adversa AI 给出 3 条互不依赖的攻击路径，全部命中 Claude Code v2.1.129。3 条都不需要预装恶意二进制、不需要漏洞利用代码、只需要 Git 仓库根目录两个文本文件。

![三条攻击路径示意](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-09/daily/attack-paths-trustfall.png)

**路径 ① · `enableAllProjectMcpServers`**：仓库根放 `.mcp.json`，里面写一台指向攻击者 server 的 MCP 配置；同目录 `.claude/settings.json` 里写 `enableAllProjectMcpServers: true`。受害开发者 `git clone` 后在该目录敲 `claude` 进入交互；信任对话框默认按钮是「Yes, I trust this folder」，回车——Claude Code 会**先**按 settings.json 调度全部 MCP server，然后才进入 reasoning。MCP server 运行环境是用户登录态的无沙箱 OS 进程，等价于把 shell 交出去。

**路径 ② · `enabledMcpjsonServers`**：上面是「全量自动批准」，路径 ② 是命名子集自动批准。`.claude/settings.json` 写 `enabledMcpjsonServers: ["<恶意-server-name>"]`，利用价值在隐蔽性——配置文件读起来像「我只信任 N 个里的 1 个」，但那 1 个就是攻击者放进 `.mcp.json` 的恶意 server。

**路径 ③ · `permissions.allow`**：这条不依赖 MCP，依赖项目级预授权工具调用清单。`.claude/settings.json` 写 `permissions.allow: ["Bash", "Read", "Write", ...]`，配合仓库 README / 代码注释里的提示注入，模型 reasoning 调用工具时不再二次确认。触发节奏比路径 ①② 慢一拍——必须等 Claude reasoning 走到工具调用——但执行时机一旦到位，效果同样是无沙箱本地命令。

3 条路径在 Adversa AI 的 PoC 视频里跑完一轮的总耗时是数秒。**重点不在 RCE 本身，重点在执行时机：MCP server 启动早于 Claude reasoning，意味着模型本身没有任何机会拦截**。

### 二、4 大 CLI 横评：差异在文案，执行后果一致

Adversa AI 把同一份恶意仓库分别交给 Claude Code v2.1.129、Gemini CLI、Cursor CLI、GitHub Copilot CLI，4 家在「告警措辞」上差异明显，但**回车之后的执行结果完全相同**。

Polyakov 在 The Register 的采访里给出一句关键判断：「They all have different approaches to configs and trust. But Cursor and Copilot / VS Code agent mode are clear analogs. Both read project-scoped MCP configuration. We tested it, and it's the same behaviour but with different user approval messages.」翻译过来——4 家在配置和信任模型上各走各的，但 Cursor 与 Copilot / VS Code agent mode 是明显的同构体——读取项目级 MCP 配置，行为一致，只是用户批准对话框写得不同。

值得展开的是 Claude Code 自己的「设计回退」：

- **2.0 之前**：信任对话框明确告知 `.mcp.json` 可执行代码，并提供「在禁用 MCP 的情况下信任此文件夹」选项
- **2.1 起**：替换为更宽泛的「Quick safety check: Is this a project you created or one you trust?」对话框，**完全不提 MCP**

Adversa AI 没说这是回归 bug，他们说的是：**Anthropic 主动收窄了信息密度**——从「明确告知 MCP 风险 + 三选项」收成「一句通用问候 + 默认 yes」。这是产品决策，不是疏忽。

### 三、CI 默认全自动：headless 模式连「回车」都没有

整组研究里读起来最让 OSS 维护者紧张的，是 CI 行为。Anthropic 自己发的 GitHub Action `claude-code-action` 跑在 GitHub Actions 上时是 headless 模式——**没有终端，信任对话框根本不渲染**。换句话说：

- 一个被攻击者打了 PR 的开源仓库，PR 里偷偷加进 `.mcp.json` + `.claude/settings.json`
- 维护者配置了「每个 PR 自动跑 Claude review」的 Action
- PR 一推，Action 在维护者的 CI 凭据上以无沙箱进程跑攻击者的 MCP server
- 拿到的是 `GITHUB_TOKEN`、SSH key、deploy key、secrets 全套

Help Net Security 把这一段翻成一句更直白的话：「One keypress is all it takes to compromise four AI coding tools——除非根本不需要按。」

### 四、Anthropic 的立场：design intent 还是 informed-consent gap？

这次最值得展开的不是技术细节，是双方的工程哲学分歧。

**Anthropic 的立场**——根据 The Register 与 Adversa AI 博客都引用的同一份回应，Anthropic 安全团队评估了报告，归类为「outside threat model」：接受「Yes, I trust this folder」即视为对项目全部配置（包括 MCP 定义）的整体同意，信任对话框之后的执行属于设计意图（design intent）；与此前自动触发类漏洞（CVE-2025-59536 等）不同，本次必须经用户回车才执行，因此不在威胁模型范围内。

**Adversa AI 的反驳**——他们不挑战边界本身，挑战边界的告知质量。Polyakov 的原话：「We haven't seen that managed scope secure configuration often, rather, we've seen the opposite. And it's not that obvious to understand all configuration nuances, especially for vibe coders.」Utevsky 给的更技术化的一句：「Managed scope cannot be overridden by any other scope.」——意思是，企业 IT 通过 managed scope 锁死的配置，是这套信任体系里**唯一**的硬约束。但他们实测发现企业用户大量没启用 managed scope，普通开发者的 vibe-coding 路径里，managed scope 形同虚设。

### 五、给私有化部署的工程窗口

国内 AI Coding CLI——通义灵码、Cursor 国行版、字节 MarsCode、智谱 Code、月之暗面 Coder、千问 CLI、文心快码——基本都在跟 Claude Code / Cursor 同款架构上长。TrustFall 暴露的不是某家公司的实现 bug，而是这一代工具的设计假设。三条路径国产工具同样适用：

| 治理动作 | 国内现状 | 借鉴方向 |
|---|---|---|
| 项目级 MCP / 工具配置默认信任 | 通义灵码、字节 MarsCode、智谱 Code 都已跟进项目级配置 | 默认拒绝 + 显式启用 |
| 配置变更可审计 | 大多数还是单文件 JSON | 引入签名 / 哈希校验 + 团队 managed scope |
| CI 模式独立路径 | headless 模式正在补齐 | 与交互模式分开权限边界，不复用 dev token |
| 私有化部署默认权限 | 私有化场景占比高，给了治理窗口 | 在企业镜像里直接关闭项目级覆盖 |

私有化部署是国产 AI Coding 工具的天然护城河——企业镜像可以默认关闭项目级覆盖、强制 managed scope、所有 MCP 注册走平台审批，这一层在公有 SaaS 里根本做不到。这次事件给了国产工具一次「把治理写进默认配置」的工程窗口。

### 六、写给开发者的三件事

把这次事件落到具体动作上，是三条可立即执行的工程纪律：

1. **个人开发**：拉到陌生仓库先看 `.mcp.json` / `.claude/` / `.cursor/`；把 Claude Code 升到能显示 MCP 风险的最新版；不熟悉的项目永远在 `--dangerously-skip-permissions=false` 下跑。
2. **团队仓库**：CODEOWNERS 把 `.mcp.json` / `.claude/settings.json` 列为强制 review 文件；任何 MCP 配置变更走 PR 双签字。
3. **OSS 维护者**：Claude Code Action / Cursor PR Bot 的 GitHub Actions workflow 显式忽略 PR 里对 `.claude/`、`.mcp.json`、`.cursor/`、`.copilot/` 的修改；CI token 与本地开发 token 分离。

> **头条小结**：TrustFall 不是一个漏洞披露，是一份工程哲学陈词。AI Coding 工具的「项目级隐式信任」走到了尽头，治理层接管原本由用户单点决定的安全边界——这件事完整展开见今日 arbitrage 专题深度文。

---

## ⚡ 快讯速览

- **xAI Grok 4.3 全量上线 API · Image Quality API 5/7 开放**：Grok 4.3 提供 1M token 上下文，定价 1.25/2.50 美元（输入/输出）/M token，三档可调推理强度；Grok 的 Image Generation Quality Mode 在 5/7 开放给 API 开发者，文字渲染与创作控制能力提升明显。grok-4-fast / grok-4-0709 / grok-3 / grok-code-fast-1 / grok-imagine-image-pro 等 8 款旧模型将于 5/15 12:00 PT 退役，迁移名单已公开。来源：xAI Release Notes、apiyi 迁移指南。
- **AI Coding 工具三发 [跟进]**：Anthropic Code with Claude 5/7 大会公开「Claude Code Routines」+ leader-worker / swarm / pipeline 多 agent 编排骨架（5/7 头条延伸）；Claude Code 5/7 patch 更新 `/mcp` 工具数显示、`--plugin-dir` 接 zip、`/color` 随机会话色；Cursor 3.3 (5/6) 上线上下文使用可视化面板。详见下文 AI Coding 工具动态。
- **Frontier Model Forum 反对抗蒸馏机制启动**：OpenAI / Anthropic / Google 通过 Frontier Model Forum 启动对抗性蒸馏检测协作，识别中国厂商基于他们模型反向推出新模型的尝试。Bloomberg 4/6 早期报道，5 月初协议正式启动。具体检测方法与公开标准官方未披露。来源：Bloomberg。
- **OpenAI Codex + Managed Agents 计划登 AWS Bedrock**：OpenAI 5/8 释放信号，年化收入 250 亿美元、IPO 时间表传 2026 末，Codex 与 Managed Agents 计划登陆 AWS。云厂商分发渠道扩张说明 OpenAI 在加快 to-B 触达节奏。来源：llm-stats.com 整理。
- **Google × Anthropic 400 亿美元投资升级**：Google 4/24 公告对 Anthropic 投资额上调到 400 亿美元（含现金 + 算力），Google Cloud 将提供未来 5 年 5GW 容量。本周 5/8 Anthropic 官博披露与 Broadcom 三方合作落地。来源：CNBC、TechCrunch。
- **Anthropic 拓展 Google × Broadcom 算力合作**：Anthropic 5/8 官方博客披露，已与 Google 和 Broadcom 签下迄今最大算力承诺，覆盖未来五年 5GW 容量；同期数据 Anthropic 运营率收入已突破 300 亿美元（去年底约 90 亿美元）。来源：anthropic.com/news。
- **Pennsylvania 起诉 Character.AI**：宾州 5/5 起诉 Character.AI——州方测试中聊天角色 Emilie 自称执业心理医师，并伪造一组医师执照编号。这是美国州一级第一起把聊天角色伪造执业资格写进民事诉讼的案件。来源：Hacker News 整理。具体诉求与赔偿数额尚未公开。
- **Stanford AI Index 2026 报告发布**：65% 美国成年人预期 AI 在未来 20 年减少可获得的就业岗位；专家与公众在 AI 医疗影响上的乐观度差异显著（专家组 84% 看正面 vs 公众组 44%）。完整数据集还未公开，机构维度切片待补。来源：Stanford HAI。
- **OpenAI Managed Agents 计划登 AWS**：OpenAI 5/8 释放信号，Codex 与 Managed Agents 计划登陆 AWS Bedrock，年化收入已到 250 亿美元，IPO 时间表待官方确认。来源：llm-stats.com 整理。
- **白宫拟起草 AI 模型审查行政令**：5/7 国家经济委员会主任 Kevin Hassett 表示白宫正在起草 AI 模型审查行政令，类比 FDA 药品审批；Google / Microsoft / xAI 已与 NIST 签发布前预评测协议，Anthropic 暂未签署同样协议（与 2024 年首批协议不同）。具体审查范围与时间表官方未披露。来源：CNBC、CNN Business。
- **Anthropic-Pentagon 法律纠纷继续**：Pentagon 5 月初与 8 家科技公司（含 OpenAI / Google / Microsoft / xAI）签 AI 合同，独漏 Anthropic；后续诉讼进度待法庭文件公开。来源：CNN Business。
- **PyTorch Lightning 2.6.2-2.6.3 包含凭证窃取恶意代码**：5 月初安全社区披露的供应链事件，受影响版本已下架，已升级用户请检查本地 token；具体感染面与受害项目数尚未由 Linux Foundation 正式公布。来源：The Hacker News。

---

## 🎙 名人说 & X 热议

**bsuh：可靠的 agent 不来自更长 prompt，而来自把控制流写进代码**

5/7 下午，用户名 bsuh 的工程师在 bearblog.dev 抛出一篇 600 字短文——`agents need control flow, not more prompts`。当晚提交到 Hacker News，24 小时内冲到 561 pts、280 条评论。核心论断一句话：「reliable agents tackling complex tasks need deterministic control flow encoded in software, not increasingly elaborate prompt chains」。判定方法非常具体：「If you've ever resorted to MANDATORY or DO NOT SKIP, you've hit the ceiling of prompting」。任何用过 Claude / GPT-5 / 千问做 agent 的人，看到这句话都会会心一笑——那种全大写的提示词，就是「我已经放弃了，但还在挣扎」的标志。bsuh 给 prompt 范式判了三条死刑：non-deterministic（同一段 prompt 模型可以走出完全不同路径）、weakly specified（自然语言无法精确表达「绝对不要做 X」）、difficult to verify（没法静态分析触发哪些行为）。解药是「deterministic scaffolds: explicit state transitions and validation checkpoints that treat the LLM as a component, not the system」——把 LLM 当成系统的一个组件，控制流写在代码里、prompt 只剩叶节点的局部判断。完整范式之争见今日 HN 顶贴专题。

**HN 顶赞 827a 的实战补刀**：有团队在 30 个文件之后崩了大写 prompt 工厂，转去把 QA 流程写成显式状态机；改写之后稳定性问题从「全员盯屏」变成「仅看告警」。这个故事在 HN 评论区被多个工程师确认是「自家代码库故事」，可见这条工程教训普遍性极强。

**Simon Willison 5/7 实时博客**：把 Anthropic Code with Claude 大会 + Adversa AI TrustFall 报告放在同一篇里讨论，给出一句判断——「2026 上半年 AI Coding 的关键词从 capability 变成了 trust boundary」。Simon 同时点了 anthropics/financial-services 仓库一周登顶 GitHub Trending，称其为「2026 第一份能照着抄的 vertical agent 工程范本」。

**Karpathy 5/7 转推**：5/7 凌晨他转了 Simon Willison 早前那条 prompt injection 警示推——「prompt injection 之于 LLM 像 1980s 计算机病毒之于 PC，没有成熟的杀毒和内核隔离」。这条转推在 TrustFall 公开当天的语境里，被 HN 评论区拿去当作 AI Coding 安全边界讨论的引子。

**判断**：5/7 这一天 X 上 AI 圈讨论密度最高的是「TrustFall + agent 工程范式」两条主线一起爆，技术 KOL 的判断都收敛到「治理层接管」这一意向上。这是个值得记住的转折信号。

---

## 📰 精选要闻

### 🔴 [OpenAI 三发实时语音 · GPT-Realtime-2 / Translate / Whisper · API 转 GA](https://openai.com/index/introducing-gpt-realtime-2/)

当地时间 5/8 凌晨，OpenAI 在官方博客一次性放出了三个实时语音模型——GPT-Realtime-2、GPT-Realtime-Translate、GPT-Realtime-Whisper——并宣布 Realtime API 正式结束 beta 进入 GA。北京时间当天 04:35，量子位抢出首发稿，36氪、新浪科技、IT 之家、网易科技、B 站 AI 早报全天跟进。

![三款模型规格](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-09/daily/gpt-realtime-2-three-models-spec.png)

**规格速览**：

- **GPT-Realtime-2**：旗舰对话模型，搭载 GPT-5 级推理，128K 上下文（上代 32K），并行工具调用，五档推理强度（minimal / low / medium / high / xhigh）和 preamble 前置语；定价 32 美元 / 1M token 输入、64 美元 / 1M token 输出、缓存输入 0.40 美元 / 1M token
- **GPT-Realtime-Translate**：同声传译专用，70+ 语种听 → 13 语种说，边说边译跟讲者语速；定价 0.034 美元 / 分钟（约 0.25 元 / 分钟），8 小时不到 120 元人民币
- **GPT-Realtime-Whisper**：低延迟流式转写，模型层 streaming，第一字延时亚秒级；定价 0.017 美元 / 分钟（约 0.10 元 / 分钟）

**工程层关键变化**：上下文从 32K 拉到 128K——Zillow 实测通话成功率从 69% 拉到 95%，长会议从此不再「金鱼记忆」；Big Bench Audio 从 81.4% 拉到 96.6%；Audio MultiChallenge 在 xhigh 推理档下从 34.7% 拉到 48.5%。

![三段流水线压成一根 WebSocket](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-09/daily/realtime-pipeline-old-vs-new.png)

一根 WebSocket 把「听 → 推理 → 译 → 说」端到端压成单 API，对工程团队意味着系统集成代码量减少 60-70%、故障定位面积缩小到一家厂商、整体延时压到 800ms 级。**这是「ASR 厂 + MT 厂 + TTS 厂」三段流水线开始走向终结的工程信号。** 完整对位国产语音四家见今日 OpenAI 实时语音专题。

### 🔴 [anthropics/financial-services 一周冲到 15,079⭐ · 行业范本仓库登顶 GitHub Trending](https://github.com/anthropics/financial-services) [跟进]

5/5 Anthropic 把 Claude Cowork 第一次做成了完整行业范本仓库，5 天内 14,672⭐ 暴涨——昨日报告还是 11,617⭐，今日实测 15,079⭐ / 1,821 fork / Apache-2.0。10 个具名 Agent（Pitch / Meeting Prep / Market Researcher / Earnings Reviewer / Model Builder / Valuation Reviewer / GL Reconciler / Month-End Closer / Statement Auditor / KYC Screener）+ 9 个 Skill Bundle（financial-analysis / investment-banking / private-markets 等）+ 11 个 MCP 数据连接器（FactSet / S&P / LSEG / Plaid 等）。Star/Fork 比 8:1 偏「真用」型——金融业内大量私仓克隆。这是 2026 年第一份能照着抄的 vertical agent 工程范本。完整对位国内金融 AI 工程基础见今日 Anthropic 金融 Agent 套件专题。

### 🟡 [waybarrios/vllm-mlx · M 系 Mac 跑 Claude Code 本地后端](https://github.com/waybarrios/vllm-mlx)

vllm-mlx 把 vLLM 的整套服务范式搬到了 MLX 上——PagedAttention、continuous batching、Anthropic Messages API、MCP 工具调用全保留。M4 Max 上单请求 463.6 tok/s，5 路并发吞吐 1111.8 tok/s（项目官方 benchmark 数据，截至 2026-05-09）。意义在于 Apple Silicon **第一次有了能扛住多并行 Claude Code agent 的本地后端**，Qwen3-Coder-Next、Kimi K2.6、GLM-5.1 等国产模型可以直接在上面跑。

接 Claude Code 一行环境变量：`ANTHROPIC_BASE_URL=http://localhost:8000` + `ANTHROPIC_API_KEY=not-needed`，IDE / MCP 工具调用 / 流式输出全部走本机，数据不出本机一步。仓库 1,128 ★ / 162 fork / 459 commit / Apache-2.0 / 平均 2-3 周一个 minor release。完整 M4 Pro / M4 Max / M3 Ultra 三档 Mac 的模型选型与 benchmark 数据见今日 vllm-mlx 专题。

### 🟡 [HKUDS/AI-Trader · 港大开源全自动 agent 量化交易](https://github.com/HKUDS/AI-Trader)

香港大学数据智能实验室开源的 100% Fully-Automated Agent-Native Trading 框架，14,598 ★ / 2,432 fork。Python 实现，支持多 agent 协同（Researcher / Strategist / Trader / Risk Manager 分角色）、回测与实盘双模式、可插拔 LLM 后端。需要明确说明：项目本身是研究框架与示例，并非可直接投产的策略，国内任何场内交易都需走合规接入与监管报备，框架对国内开发者更适合作为多 agent 编排架构的工程参考。后续合规改造路径取决于私有部署侧的接入方式。

### 🔵 [datawhalechina/hello-agents · 国产《从零构建智能体》教程](https://github.com/datawhalechina/hello-agents)

44,536 ★ / 5,416 fork，国内 Datawhale 社区维护的开源教程仓库——《从零开始构建智能体》。覆盖 agent 原理、工具调用、记忆系统、多 agent 编排、生产化部署，配套代码全 Python。在 LangChain / LangGraph 大量国产生态化讨论之外，给国内开发者一条「先理论再选框架」的入门路径。当日新增 +667 ★，在 HN 顶贴范式之争的语境里被多次引用为「国产 agent 教程基础设施」。

### 🔵 [decolua/9router · 多家免费 LLM 路由器](https://github.com/decolua/9router)

5,549 ★ / 1,066 fork，JavaScript 项目，自我描述「Connect Claude Code, Codex, Cursor, Cline, Copilot, Antigravity to FREE Claude/GPT/Gemini via 40+ providers. Auto-fallback, RTK -40% tokens, never hit limits.」——把 40+ 家免费 / 试用渠道串成统一 API。注意事项：项目合规边界尚未公开评估，许多 free provider 的 ToS 是否允许通过此类路由聚合调用、`-40% tokens` 优化的实测口径都需要使用前自行核实。当日新增 +1,052 ★，可见社区对「免费 LLM 入口」的需求强度，但工程上不建议做生产代理用。

### 🔵 [Anthropic 拓展 Google × Broadcom 算力合作](https://www.anthropic.com/news/google-broadcom-partnership-compute)

Anthropic 5/8 官博公布与 Google + Broadcom 的迄今最大算力承诺：未来 5 年 5GW 容量。同步披露运营率收入已突破 300 亿美元（2025 年底约 90 亿美元），增速 3.3 倍。这是 Anthropic 在 OpenAI / Microsoft / Google / Oracle / Stargate 的算力供给链之外，给自己锁定的第二条路径——Google TPU + Broadcom 自研芯片组合。

---

## 🇨🇳 国内 AI 观察

### 中国移动苏州 5/8 全球首发 AI-eSIM 与「1+3+9」体系

苏州金鸡湖国际会议中心，2026 移动云大会 AI-eSIM 分论坛，中国移动副总经理陈怀达发布全球首款 AI-eSIM 产品和「1+3+9」多生态智能服务体系。一张焊在主板上的硅片，第一次同时承载通信凭证、数字身份和大模型账号——eSIM 走出 IoT 圈、第一次正式成为 AI 终端基础设施。虎嗅 5/8 把这条新闻挂上头条第 4 位、新浪科技给了长篇深度，IT 之家 / 网易订阅 / 搜狐 / 财经头条 / ITBear / 腾讯新闻同步跟进，国内主流科技媒体一天之内 6 家以上集中发声。

![中国移动 AI-eSIM 1+3+9 体系架构](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-09/daily/aiesim-1-3-9-system.png)

**1+3+9 速览**：

- **1 个入口**：AI-eSIM 芯片入口，焊在终端主板、支持 OTA 远程写入，跨过上一代「必须线下营业厅开卡」门槛；陈怀达原话「全球首创『运营商码号即大模型账号』」
- **3 大引擎**：AIoT 平台（设备入网与远程管理）+ 智能体服务（云端模型调度，MoMA 平台预接 DeepSeek / 豆包 / Qwen / Kimi / GLM 与自研九天）+ Byte+Token 融合运营（流量套餐升级为「连接 + 算力 + 模型」订阅）
- **9 大场景**：玩具、家电、可穿戴、办公、金融、交通、无人机、机器人、能源

**最值得关注的工程数字**：可穿戴专用 Cat.1 通信芯片**重量降低 82%**——AI 眼镜可以做到接近普通眼镜重量、AI 手表续航能多撑半天到一天。**2026 年内首批量产**预计落在 AI 玩具（移动 × 苏超 IP / 南京文旅 / 雷鸟创新）、智能眼镜（移动 × 慧明眼镜 / 雷鸟创新）、智能支付（移动 × 银联）三类。中期（1-2 年）落在智能家电（美的智能空调样机）、办公场景（移动 × 京东 / 腾讯 / 火山引擎 AI-eSIM 实验室）。完整 9 大场景的近期 / 中期 / 远期落地节奏与商业模式分析见今日 中国移动 AI-eSIM 专题。

### 国产语音四家同档铺底 · 与海外三发对比

OpenAI 5/8 三发实时语音不是一次国产语音的降维打击，更像一份工程蓝图被对标方画清楚。国产同档玩家的实力地图：

![国产语音四家能力对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-09/daily/domestic-voice-models-comparison.png)


- **字节 Seed LiveInterpret 2.0**（2025-07）：端到端同传旗舰，中英互译延时 2-3 秒，多人会议中英双向准确率 70%+，单人演讲 80%+；最差异化的能力是 0 样本声音克隆——直接用讲者本人音色输出译文，OpenAI 这次没有覆盖
- **字节豆包实时语音**（2026-01）：端到端语音理解 + 生成一体化，已在豆包 App 全量上线
- **阿里 Qwen3-Omni**：端到端多模态，能力对位 GPT-Realtime-2，主打 Cantonese / Hokkien 等汉语方言识别
- **科大讯飞星火**（V3.0 + 同传 V2）：60+ 语种实时翻译，行业积累深，金融 / 法律 / 医疗垂直场景适配最好
- **月之暗面 Kimi 语音**：长上下文 + 语音问答深度集成，128K 长会议是 Kimi 一直的主场

整体判断：国产语音四家手上各自有同档基础设施，30-90 天内追平 OpenAI 三发的工程蓝图不是难事，且各家在「方言 / 声音克隆 / 长会议 / 垂直行业」四个差异化方向上各有底牌。

### 海外金融 Agent 范本横评 · 给国产同行的工程参考

5/5 Anthropic 把 Claude Cowork 第一次做成了完整行业范本——`anthropics/financial-services` 仓库 5 天冲到 15,079 ★、1,821 fork、Apache-2.0 授权。10 个具名 Agent（Pitch Agent / Meeting Prep Agent / Market Researcher / Earnings Reviewer / Model Builder / Valuation Reviewer / GL Reconciler / Month-End Closer / Statement Auditor / KYC Screener）、9 个 Skill Bundle（financial-analysis / investment-banking / private-markets 等）、11 个 MCP 数据连接器（FactSet / S&P / LSEG / Plaid 等）。

国内金融 AI 同行的工程基础同样完整——华泰、招商证券、中信建投在卖方研究 LLM 上有公开成果；阿里通义、字节豆包、百度文心、讯飞星火都有金融垂类版本；蚂蚁、平安在保险 / 银行内部已有大规模部署。这套海外范本对国产是工程参考，不是降维打击。最值得借鉴的工程纪律是「一岗一 Agent」——名字直接对得上一个真实岗位的真实交付物（Pitch Deck、IC Memo、月末账、KYC 报告），而不是又一个「金融大模型助手」。完整对位见今日 Anthropic 金融 Agent 套件专题。

---

## 📦 GitHub Trending（截至 2026-05-09）

### 1. [anthropics/financial-services](https://github.com/anthropics/financial-services) — Python · 15,079⭐（5 天 +14,672）

5/5 Anthropic 开源的金融 Agent 套件。10 个具名 Agent + 9 个 Skill Bundle + 11 个 MCP 数据连接器，Apache-2.0 授权可商用。一周内 14,672 ★ 暴涨，登 GitHub Trending 全站第一。Star/Fork 比 8:1 偏「真用」型——金融业内大量私仓克隆。这是 2026 年第一份能照着抄的 vertical agent 工程范本，国产金融 LLM 团队上手成本极低。

### 2. [Hmbown/DeepSeek-TUI](https://github.com/Hmbown/DeepSeek-TUI) — Rust · 21,714⭐（当日 +3,731）[跟进]

DeepSeek 模型的终端 coding agent，Rust 实现。从昨天 18,701 ★ 一日内继续涨 3,000+，热度延续。可见国产模型 + 端侧 TUI 这条路径在 r/LocalLLaMA 和 HN 上的工程关注度。

### 3. [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) — Shell · 35,330⭐（当日 +1,893）[跟进]

Google 工程总监 Addy Osmani 维护的 AI Coding 工程手册，从昨日 32,000 ★ 继续涨。国内开发者读这份仓库的姿势已经从「翻译过来读」升级为「直接 git pull」，CONTRIBUTING.md 里中文 issue 比例稳步上升。

### 4. [datawhalechina/hello-agents](https://github.com/datawhalechina/hello-agents) — Python · 44,536⭐（当日 +667）

国内 Datawhale 社区维护的《从零开始构建智能体》开源教程。在 HN 顶贴「agents need control flow」范式之争的语境里，这套国产教程提供了一条「先把原理写清楚再选框架」的入门路径。Python 代码全开源，含 agent 原理、工具调用、记忆系统、多 agent 编排、生产化部署。

### 5. [HKUDS/AI-Trader](https://github.com/HKUDS/AI-Trader) — Python · 14,598⭐（当日 +202）

香港大学数据智能实验室开源的全自动 agent 量化交易框架，多 agent 协同（Researcher / Strategist / Trader / Risk Manager）+ 回测实盘双模式。注意：是研究框架与示例，不是可直接投产的策略；国内合规接入与监管报备需另走流程。架构本身可作为多 agent 编排的工程参考。

### 6. [waybarrios/vllm-mlx](https://github.com/waybarrios/vllm-mlx) — Python · 1,128⭐（5 个 minor release / 459 commit）

把 vLLM 的整套服务范式搬到 Apple Silicon——PagedAttention、continuous batching、Anthropic Messages API、MCP 全保留。M4 Max 单请求 463 tok/s，5 路并发 1111 tok/s。国内 M 系 Mac 玩家在 Ollama 之外多了第二条认真的本地路径，Apache-2.0 协议、双 API（OpenAI + Anthropic）兼容、原生 MLX 后端。

---

## 🛠 AI Coding 工具动态

### Anthropic Code with Claude 2026 · Claude Code Routines + 多 agent 编排

5/7 旧金山大会，**Claude Code Routines** 是这次最实用的新原语——按时间表 / webhook 触发的定时任务，给 agent 编排补上调度层；同时上线 leader-worker / swarm / pipeline 三套多 agent 编排骨架、长记忆系统 SDK 接口、outcome-based agents（用交付物作为成功标准而不是工具调用次数）。Pro/Max 用户的 Claude Code 5 小时窗口翻倍放开延续到 5/9。

### Claude Code 5/7 patch 更新

终端与会话环境变量支持完善；`/color` 不带参数随机选会话色、`/mcp` 显示已连服务器的工具数（含 0 工具警告）、`--plugin-dir` 接 `.zip` 插件包、`--channels` 在 console 鉴权下也可用；修复 VS Code Windows 激活和 Mantle 端点鉴权头问题。这一组改动里 `--plugin-dir` 接 zip 是工程上变化最大的一笔——团队插件分发不再需要 git 仓库。

### Cursor 3.3 上线 5/6 · 上下文使用面板

3.3 主打上下文使用可视化（多少 token 用在了哪类文件、多少在系统 prompt、多少在工具调用），细节体验改进偏多。Composer 2 节奏稳定 200+ tok/s。Cursor / Windsurf / Cline 三家在 Q2 持续迭代节奏。

### vllm-mlx 把 Apple Silicon 推进到生产级 agent 后端

waybarrios/vllm-mlx 5/8 push，5 个 minor release（v0.2.5 → v0.2.9）平均 2-3 周一个版本。M4 Pro 36GB 跑 Qwen3-Coder-Next 14B 4bit + 3-5 路并发就能扛单人 Claude Code 后端；M4 Max 64GB 上 32B + 5-10 路并发是小团队甜点档；M3 Ultra 256GB 跑 Kimi K2.6 1T-MoE Q4 是单机 inference 服务器。

---

## 🔭 值得关注

### TrustFall 之后 managed scope 会不会成为企业部署默认（7 天追踪）

Adversa AI 实测发现企业用户大量没启用 managed scope，普通开发者 vibe-coding 路径里 managed scope 形同虚设。后续值得追踪两条线：Anthropic 会不会在 Claude Code 默认启用 managed scope（哪怕只是企业版），以及国内 AI Coding 工具（通义灵码 / 字节 MarsCode / 智谱 Code）的私有化镜像里会不会把项目级配置默认关闭。这一层如果跑通，国产 AI Coding 在企业内的护城河会比公有云时代更深。

### 实时语音 GA 之后 · 国产同传四家追平节奏对比（7 天追踪）

字节 Seed LiveInterpret 2.0 / 豆包 / 阿里 Qwen3-Omni / 讯飞星火 / 月之暗面 Kimi 在 OpenAI 三发同档已知能力上的追平节奏值得记录。差异化方向已经清晰——0 样本声音克隆、汉语方言识别、长会议 128K 上下文、垂直行业适配——四家各有底牌，30-90 天内的产品迭代值得逐家追踪。

### HN 顶贴范式之争 · LangGraph / Dify / 扣子 / MetaGPT 工程边界重画（永久追踪）

bsuh 的 561 pts 顶贴把「prompt 派 vs 控制流派」的工程范式之争公开化。国产 agent 框架 Dify / 扣子 / LangGraph 中文版 / MetaGPT 在控制流可视化、状态机原语、程序化校验三件事上各有路径。国产框架的下一个 6 个月，工程纪律会比模型能力更重要。这件事值得加进永久追踪——agent 框架范式如何沉淀为社区共识。

### 行业范本仓库带起的 vertical agent 工程化潮（7 天追踪）

Anthropic 用 5 天把这个仓库做到 15,079 ★，验证了「行业 LLM 范本仓库」这条工程路径的需求强度。后续 7 天追踪点：OpenAI / Google DeepMind 会不会跟进各自的行业范本仓库（金融 / 法律 / 医疗 / 教育）；国产团队（蚂蚁 / 平安 / 招商证券）会不会公开各自的 vertical agent 工程结构。

---

## ✍ 编辑说

- **TrustFall 推荐**：所有 AI Coding 工具用户**必读**今日头条与 arbitrage 专题深度文。Claude Code / Cursor CLI / Gemini CLI / Copilot 用户立刻检查 `.mcp.json` 与 `.claude/settings.json` 在不熟悉仓库里的存在性，OSS 维护者把这两份配置加进 PR 强 review 名单。
- **OpenAI 实时语音推荐**：做语音 agent / 同传 / 实时字幕 / 客服记录的团队**值得上手**。Translate 0.034 美元/分钟把 8 小时国际会议 AI 同传成本压到 120 元人民币以内，是一次成本结构上的代际下沉。国内同档基础设施齐备，可以把 OpenAI 这次的 API shape 当作产品对标。
- **vllm-mlx 推荐**：M4 Pro / M4 Max / M3 Ultra Mac 用户**值得安装**。一行 `ANTHROPIC_BASE_URL` 接 Claude Code，本地后端跑 Qwen3-Coder-Next / Kimi K2.6 / GLM-5.1，数据不出本机一步。国内端侧 AI 第一次有了能扛多并发 agent 的工程级方案。
- **AI-Trader 观望**：研究框架可以读、架构可以借鉴，但国内场内交易合规边界明确，**不建议直接接入实盘**。多 agent 协同的代码骨架值得拆开看，更适合作为编排架构的工程参考。
- **HN 顶贴范式之争 推荐阅读**：所有写过 agent 的开发者**值得读 600 字原帖** + 今日 HN 顶贴专题。把全大写 prompt 当成「我已经放弃了」的信号，重新审视手里的 agent 工程结构——这件事比任何一次模型升级都更值得花一个周末。

---

**今日完整专题清单**：

1. TrustFall：Claude Code 一个回车交出 SSH key，Cursor / Gemini CLI / Copilot 同坑
2. OpenAI 三发实时语音 GPT-Realtime-2：把 GPT-5 推理塞进同传
3. 中国移动 AI-eSIM 把 Token 写进 SIM 卡：1+3+9 体系全球首发
4. Agents 需要控制流，不是更多 prompt：HN 561 顶帖立论与国产框架对位
5. Anthropic 一周登顶 GitHub：金融 Agent 套件给国内同行的工程参考
6. vllm-mlx · M 系 Mac 跑 Claude Code 本地后端：单机 463 tok/s + 10 路并行 agent 不抖
