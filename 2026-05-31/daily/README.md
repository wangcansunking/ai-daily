---
title: "智元 τ0 开源真机数据刷世界纪录 · Cursor 3.6 与 Claude Code 各自加码 · Anthropic 估值反超 OpenAI | AI 日报 | 2026-05-31"
date: 2026-05-31
weekday: Sunday
slug: 2026-05-31
category: daily-report-newsletter
cover: "2026-05-31.png"
cover_style: dramatic
description: "5/31 国内具身智能开源、AI Coding 三线撕裂、Anthropic 反超 OpenAI 三件大事齐爆。"
back_run: true
back_run_reason: "5/31 凌晨 timer 跑时 Claude API 用量超限，6/1 补跑"
---

# 智元 τ0 开源真机数据刷世界纪录 · Cursor 3.6 与 Claude Code 各自加码 · Anthropic 估值反超 OpenAI | AI 日报 | 2026-05-31

![智元具身世界模型开源 AI Coding 三线撕裂 Anthropic 反超 OpenAI](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/daily/2026-05-31.png)

> 本期补发：5/31 凌晨调度触 API 上限，3 次重试全失败，6/1 补跑。下文事件锚定 5/30-5/31 窗口。

## 📋 头版目录

- 🧠 智元 τ0-WM 5B 具身世界模型 5/31 开源，真机遥操作数据 1.78 万小时刷开源纪录 → 头条 1
- 🛠 Cursor 3.6 上 Auto-review、Claude Code 1000 子 Agent 复盘、HN "MCP is dead" 325 分同周炸 → 头条 2
- 💸 Anthropic 估值 9650 亿美元反超 OpenAI 9000 亿，Karpathy 等 10 人加盟 → 头条 3
- 🇨🇳 字节自研 CPU 应对 x86 季度涨价 10-35%，Coze 平台开始本地化 → 国内 AI
- 🇨🇳 宇树科技 6/1 接受科创板上市审核，国内人形机器人 IPO 窗口开启 → 国内 AI
- 🇨🇳 面壁 MiniCPM5-1B 端侧基座 + ForgeTrain（AI 写的训练框架）昇腾 10% 加速 → 国内 AI
- 🇨🇳 智谱港股盘中破 7000 亿港元 · DeepSeek 国家大基金洽谈 450-500 亿美元估值 · 阶跃星辰 25 亿美元融资 → 国内 AI 一周钱与价
- 💰 Codex Pro 2x 促销 5/31 到期 · GitHub Copilot 6/1 切按量计费 · Cursor 2.5 首周 2x 5/25 已结束 → 要闻（AI Coding 集体涨价窗口）
- 📦 OpenBMB VoxCPM2 端侧 TTS 上 GitHub Trending，2.36 万 stars → GitHub Trending
- 📦 EveryInc compound-engineering-plugin 跨 Claude Code / Codex / Cursor 三端 1.87 万 stars → GitHub Trending
- 📦 microsoft/markitdown 13.5 万 stars 持续登顶，Agent 上下文准备工具 → GitHub Trending
- 🎙 Sam Altman CBA Sydney：承认 ChatGPT 上线后对"AI 替代初级白领"预期错了 → 名人说
- 🎙 Quandri "MCP is dead" HN 头版 325 分，OpenAI 与 Anthropic 两阵营下场对线 → 名人说
- ⚖️ CNN 起诉 Perplexity 抄袭 17,000+ 条新闻图视频，首家电视网告 AI 公司 → 要闻
- ⚖️ xAI 起诉科罗拉多 AI Act、联邦司法部介入，州法首次被告 → 要闻
- 🔬 talkie-1930-13b：Alec Radford 用 1931 年前 2600 亿英文 token 训 13B，HumanEval 居然写出 Python → 值得关注
- 🚀 微软 Build 2026 大会 6/2-3 开幕，Windows Agent Framework + WSL 3 NPU 直通 → 值得关注

## 🔥 头条深度

### 头条 1 · 智元 τ0-WM 把"真机数据"做成开源主力

![智元 τ0 具身世界模型 GitHub 仓库 og 卡](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/daily/source-github-tau0wm-og-2026-05-31.png)

#### 头条 1.1 · 5B 参数 / 3 万小时数据，真机遥操作 1.78 万小时

5/31 上午，智元机器人和上海创智学院（罗剑岚团队）开源 τ0-WM——一个 5B 参数的具身世界模型。预训练数据约 3 万小时，**其中真机遥操作数据 1.78 万小时**，规模在公开的具身基座模型里排第一。仓库地址 `sii-research/tau-0-wm` [1]，5/30 建仓，6/1 凌晨 64 stars、3 forks，Python 项目、size 7.3 MB；HuggingFace 同步发了权重 [2]。

#### 头条 1.2 · "数据金字塔"被改写

过去机器人基座的训练数据金字塔，底层是仿真、中层是视频脱困、上层才是真机。τ0-WM 反过来：真机遥操作占主力。量子位 [3] 当日的解读是，**仿真到真实的鸿沟（sim2real gap）让仿真数据"性价比"在大模型规模下持续下滑**——能拿到 1.78 万小时真机数据这件事本身，就是国内具身公司过去两年遥操作平台搭起来之后的产业红利。

#### 头条 1.3 · 为什么是 5/31 这个时间点

智元、银河通用、宇树过去半年密集出货遥操作平台，1.78 万小时是把所有部署机型回传数据合并的结果。模型权重 Apache 化之后，下游公司——尤其是没有自研真机数据采集的国内机器人初创——理论上可以直接拿来做 fine-tune。宇树科技 6/1 接受科创板上市审核（详见国内 AI 区块），整个国内人形机器人产业链 6 月初是密集动作的窗口期，τ0-WM 这一步抢在前面。

---

### 头条 2 · AI Coding 一周三件事，全部指向"Agent 自治边界"

![Cursor 3.6 changelog hero](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/daily/source-cursor-3-6-changelog-2026-05-31.png)

#### 头条 2.1 · Cursor 3.6 上 Auto-review，三层路由放进默认运行模式

5/29 Cursor 推 3.6 [4]，把 Auto-review 模式做成默认开关：对 Shell、MCP、Fetch 三类工具调用走"允许列表 → 沙箱 → 分类器子 Agent"三层路由，长任务里不再每一步打断用户。changelog 同时坦白一句："非确定性，不是安全边界"——分类器子 Agent 兜底但不保证拦截恶意调用。同日 Cursor 发的 Developer Habits 报告里有两个数：过去 2 个月工具调用次数涨 30%、AI 一次性改动行数涨 5 倍。

#### 头条 2.2 · Claude Code Dynamic Workflows 复盘：1000 子 Agent / 16 并发

![Claude Code GitHub 仓库 og 卡](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/daily/source-github-claude-code-og-2026-05-31.png)

5/28 和 Opus 4.8 同时放出的 Dynamic Workflows 预览版（5/29 日报已写），5/31 在 HN 和 The New Stack 引发集中复盘：Claude 写 JS 编排脚本，由独立 runtime 执行，单次任务最多拉 1000 个子 Agent，16 个并发。Anthropic 拿"配合 Bun 75 万行重写"做案例佐证（5/29 报道）。HN 头版讨论的焦点是两个：(1) 编排（orchestration）从框架（LangGraph、LangChain）挪进模型厂之后，框架方向何去何从；(2) 1000 子 Agent 的 token 成本失控警告——已经有开发者报告单任务跑爆月度额度。Claude Code 主仓 `anthropics/claude-code` 6/1 凌晨 128,961 stars，5/28 以来上涨明显。

#### 头条 2.3 · "MCP is dead" 上 HN 325 分

Quandri Engineering 5/31 的一篇 "MCP is dead" [5] 当天就上了 HN 头版（同标题两版本累积 325 分 / 第一版 195 分 174 评）。文章核心：MCP 协议在 Claude / Codex / Cursor 三端都有产品级集成，但**吃 context、可靠性低、重复造 CLI/API 轮子**这三个问题在 5/28 后被新一轮 Agent 工具放大。OpenAI 内部 ChatGPT App Store、Codex plugins、MCP 团队负责人下场对线；Anthropic 5/14 收购 Stainless（5/14-5/15 日报已写）继续在 MCP 工具链上加码作反证。**一周的三件事——Cursor 上 Auto-review、Claude Code 推 Dynamic Workflows、社区炸 MCP——都在同一根命题轴上：Agent 自治度该到哪里、安全和成本谁兜底**。

---

### 头条 3 · Anthropic 估值首次反超 OpenAI

#### 头条 3.1 · 9650 亿 vs 9000 亿，差距来自企业市场

国内媒体 5/31 集中复盘了 AI 前线汇总 [6] 的数据：Anthropic 5/28 完成 65B 系列 H 轮（5/29 日报头条），投后估值 9650 亿美元；OpenAI 上一轮估值 9000 亿美元——**这是 Anthropic 自成立以来首次反超 OpenAI**。Ramp 的企业采购数据进一步显示：截至 5 月，企业 AI API 支出里 Anthropic 占比 60%，OpenAI 占 32%。Cursor 5/29 那份 Developer Habits 报告里也披露过类似口径——开发者付费工具的 backend 大头是 Claude 系列。

#### 头条 3.2 · 人才虹吸效应叠加

5/19 Karpathy 公开宣布加入 Anthropic 做 pretraining 研究的推文 24 小时内 620 万次浏览。5/31 Humphrey Theodore [7] 把 Karpathy、Eric Boyd（前 Microsoft Azure AI 总裁）、Ross Nordeen（ex-xAI）等 10 人列成 "Anthropic 2026 头号引进名单"。这是过去半年第一次"加盟 Anthropic"成了 AI 圈刷屏的主话题，反过来强化了估值反超的叙事。

#### 头条 3.3 · 国内视角的对照

国内媒体的角度更直接：Anthropic 估值反超 OpenAI 的同一周，**智谱港股盘中市值一度破 7000 亿港元**（详见国内 AI 区块），DeepSeek 在洽谈国家大基金 450-500 亿美元估值，**国内大模型公司开始第一次有"国家级估值锚"**。Anthropic 反超的故事在国外是企业市场逻辑，在国内被读成"基础模型公司的估值天花板被打开"。这两件事叠加之后，6 月下半月起密集的国内 AI 公司 IPO/融资节奏值得跟。

## ⚡ 快讯速览

- **OpenAI 发布 Rosalind Biodefense 计划 / GPT-Rosalind 受限模型**（5/30）[8]。仅向 CAISI、UK AISI、洛斯阿拉莫斯国家实验室、Frontier Model Forum 等受审核机构开放，命名致敬 Rosalind Franklin。聚焦病原检测和疫情早期预警。国内开发者基本拿不到，待观察后续是否开放 API。
- **微软 Build 2026 大会 6/2-3 开幕**（5/31 预告 [9]）。Nadella 开场 keynote，预告 Windows Agent Framework（WAR runtime）、WSL 3 NPU 直通、Azure Agent Mesh、Windows Agent Store。Insider 6 月可拿 preview，初期仅支持文本类 Agent，视觉类推迟到 2027。WSL 3 NPU 仅支持 Snapdragon X Elite + Intel Meteor/Lunar Lake，AMD 后续补。
- **CNN 起诉 Perplexity**（5/28 立案，5/31 媒体发酵）[10]。纽约南区联邦法院，指控抄袭 17,000+ 条新闻、图、视频。**首家电视网起诉 AI 搜索公司**。CNN 称去年谈版权许可未谈成。Perplexity 回应"事实不享受版权"。后续庭审节奏 6 月公布。
- **xAI 起诉科罗拉多 AI Act**（5/28-5/31）[11]。联邦司法部介入支持 xAI，**这是州级 AI 法案首次被联邦层面挑战**。
- **OpenAI Frontier Governance Framework**（5/28 发布）[11]。把自家 Preparedness Framework 对齐欧盟 AI Act 的 GPAI Code of Practice 和加州 Transparency in Frontier AI Act。欧盟 8/2/2026 全面强制。
- **Cursor Composer 2.5 首周 2x 5/25 已结束**，Codex Pro 2x 促销 5/31 到期，GitHub Copilot 6/1 切按量计费。**AI Coding 集体进入"促销退潮 → 涨价"窗口**，6 月底成本观察重点。
- **昆仑万维 SkyClaw-v1.0 Agent 模型 5/26 发布**（窗口边缘）[12]。百万 token 上下文 Agent 模型，适配 OpenClaw / Hermes / Nanobot / Claude Code / Codex；benchmark 超 MiniMax 2.7、接近 DeepSeek V4 Pro / Claude Opus 4.6，定价低于竞品一半。
- **小米 MiMo-V2.5 API 永久降价最高 99%**（5 月下旬）。配合智谱港股市值破 7000 亿港元、阶跃星辰 25 亿美元融资、零一万物 15 亿元订单，国内大模型一周钱与价齐动。

## 🎙 名人说 & X 热议

**Sam Altman 在悉尼承认对"AI 替代初级白领"的预期错了**——5/31 在 CBA Sydney 大会的虚拟演讲 [13]，与 CBA CEO Matt Comyn 对话。Altman 原话："ChatGPT 2022 上线后我对初级白领被替代的预期，被证明是显著错误的，人的成分不能外包。" 同时补一句："仍然可能是真风险。" 这是 OpenAI CEO 第一次正面承认就业冲击叙事走过了——背景是 FT 6/1 头版那篇"远程办公而非 AI 导致初级开发者招聘崩盘"的讨论（HN 同步 90 分）刚好砸下来。

**Quandri Engineering 的 "MCP is dead" 把 HN 整周搅热**——5/31 HN 头版 325 分 [5]，OpenAI 内部 ChatGPT App Store、Codex plugins、MCP 团队负责人下场长评回应。Anthropic 则用 5/14 收购 Stainless 做反向举证。社区分裂之处不在协议本身，而在**编排责任的归属**——Quandri 主张"协议层薄、agent 自己写代码调 API 更可控"，Anthropic 阵营主张"协议层做工具发现、模型层做编排"。这场争论 6 月还会继续。

## 📰 精选要闻

- 🔴 **AI Coding 工具集体涨价窗口（5/25-6/1）**。Cursor Composer 2.5 首周 2x 5/25 结束、Codex Pro 2x 5/31 到期、GitHub Copilot 6/1 切按量计费 [14]。背景是 Opus 4.8、Cursor 3.6、Codex Workspace 都加了 Auto-review / Dynamic Workflows / 子 Agent，单任务 token 量级提升。建议有重度使用的团队 6 月底前结一笔月度账单观察实际成本。
- 🔴 **Anthropic Opus 4.8 蒸馏中国模型争议**[6][7]。5/29 网易 / AI 前线复盘 Opus 4.8 上线后被指"蒸馏自国内开源模型"，Anthropic 未公开回应。这是 2/23 旧指控的余波。技术层面没有公开证据，争议主要在国内社区。建议把"指控" vs "事实"分开读。
- 🟡 **CNN 起诉 Perplexity**（5/28 立案）[10]。纽约南区联邦法院，**首家电视网告 AI 公司**，索赔 17,000+ 条新闻图视频。CNN 此前与 Perplexity 版权谈判未达成。
- 🟡 **xAI 起诉科罗拉多 AI Act**（5/28-5/31）[11]。联邦司法部介入支持 xAI，**州级 AI 法案首次被联邦挑战**。
- 🔵 **OpenAI Frontier Governance Framework**（5/28 发布）[11]。对齐欧盟 AI Act 的 GPAI Code of Practice 和加州 Transparency in Frontier AI Act。欧盟 8/2/2026 强制。

## 🇨🇳 国内 AI 观察

### 字节自研 CPU 应对 x86 季度涨价 10-35%

5/28 路透三名知情人士透露 [15]，字节因英特尔 / AMD CPU 季度涨价 10-35% + 交付周期最长 6 个月，加速自研 CPU 用于自有服务器，部署 Coze 智能体平台。同时推进 Arm + RISC-V 两条路线，已接触外部代工伙伴。5/30-5/31 媒体持续发酵。**国内云厂商芯片自主化叠加 Agent 推理时代算力转移**，是 6 月观察重点。

### 宇树科技 6/1 接受科创板上市审核

5/31 媒体确认 [6]，宇树科技 6/1 上科创板上市审核，国内人形机器人独角兽。**国内机器人公司开始密集冲击 IPO 窗口**——和 5/31 智元 τ0-WM 开源呼应，整个产业链 6 月初密集动作。

### 面壁 MiniCPM5-1B + ForgeTrain：AI 写的训练框架昇腾上 10% 加速

![ForgeTrain GitHub 仓库 og 卡](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/daily/source-github-forgetrain-og-2026-05-31.png)

5/26 面壁智能、清华、OpenBMB 发的 MiniCPM5-1B [6] 是端侧文本基座，1B 参数在 OpenLM-Index 上超越所有 3 个月前 2B 模型。亮点是训练框架 ForgeTrain [16]——**国产 AI 模型自己写的预训练框架，性能超英伟达 Megatron，在华为昇腾上 10% 加速**。仓库 `OpenBMB/ForgeTrain`，6/1 凌晨 199 stars（5/26 上线后一周内）。MiniCPM5-1B + ForgeTrain 一起放出，是国产 AI Coding 闭环（AI 写训 AI 的代码、训国产模型、跑国产 NPU）第一次完整展示。

### 国内大模型一周钱与价（5/22-5/31）[6]

- **智谱**港股盘中市值一度破 7000 亿港元
- **DeepSeek** 国家大基金洽谈 450-500 亿美元估值（梁文锋拟出 200 亿元自掏腰包 40%）
- **阶跃星辰** 25 亿美元融资完成
- **零一万物** 订单超 15 亿元，李开复称 2026 盈利
- **昆仑万维** SkyClaw-v1.0 + lite 发布（5/26）[12]，Agent 模型，benchmark 接近国内闭源旗舰
- **小米 MiMo-V2.5** API 永久降价最高 99%（5/28 日报头条）

## 📦 GitHub Trending

![harry0703/MoneyPrinterTurbo GitHub og 卡](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/daily/source-github-harry0703-moneyprinterturbo-og-2026-05-31.png)

- 🔴 **`harry0703/MoneyPrinterTurbo`** [17] — 国内开源短视频生成工具，6/1 凌晨 74,522 stars（5/31 当日 +1,937），单日榜首。利用 AI 大模型一键生成高清短视频，国内创作者圈持续推。
- 🔴 **`microsoft/markitdown`** [18] — Python 工具把文件 / Office 文档转 Markdown，**Agent 上下文准备工具**。6/1 凌晨 135,200 stars，单日 +2,798。AI Coding / RAG 流水线必备。
- 🔴 **`anthropics/claude-code`** [19] — Claude Code CLI 主仓，6/1 凌晨 128,961 stars，单日 +489。5/28 Dynamic Workflows 上线后持续上涨。
- 🟡 **`OpenBMB/VoxCPM`** [20] — VoxCPM2 Tokenizer-Free TTS，多语种语音生成 + 创意声音设计 + 真人级声音克隆。6/1 凌晨 23,603 stars，单日 +635。国内开源 TTS 抢眼。
- 🟡 **`EveryInc/compound-engineering-plugin`** [21] — Compound Engineering 插件，**跨 Claude Code / Codex / Cursor 三大主流 AI Coding 工具**。6/1 凌晨 18,741 stars，单日 +251。TypeScript 编写，反映"跨工具复用"是开发者本周关注点。
- 🟡 **`supermemoryai/supermemory`** [22] — AI 时代 Memory API 引擎，配合 Agent 持久记忆主题。6/1 凌晨 23,401 stars，单日 +264。

## 🛠 AI Coding 工具动态

- **Cursor 3.6**（5/29）[4] — Auto-review 三层路由（详见头条 2.1）；Developer Habits 报告显示 2 个月工具调用涨 30%、AI 改动量涨 5 倍。
- **Claude Code Dynamic Workflows 复盘**（5/28 发布、5/31 集中讨论）— 1000 子 Agent / 16 并发，HN 头版讨论 token 成本控制问题（详见头条 2.2）。
- **GitHub Copilot 6/1 切按量计费**（5/31 是最后一天）[14] — 截至 5/31 未公布单 credit 美元价，6 月计费透明度待验证。
- **OpenClaw v2026.5.31-beta.1**（5/31 发布）— 用户自家本地 AI 助手 [23] 5/31 出 beta 版。

## 🔭 值得关注

- **Microsoft Build 2026 大会 6/2-3 开幕** — Nadella keynote 预告 Windows Agent Framework + WSL 3 NPU 直通 + Azure Agent Mesh + Windows Agent Store。Insider 6 月可拿 preview。是否会改变 Windows 作为 Agent 客户端的地位，待官方实际发布观察。
- **talkie-1930-13b：用 1931 年前 2600 亿英文 token 训 13B 模型** [24] — Alec Radford + David Duvenaud + Nick Levine 项目，HumanEval 测试中**居然写出 Python**（语言诞生 1991 年）。RLAIF 阶段用 Claude Sonnet 4.6 当裁判，SFT 用 Claude Opus 4.6 生对话。路线图：今夏发 GPT-3 级别复古模型。哲学价值大于商业价值，反常识。
- **国内人形机器人 IPO 密集窗口（6 月起）** — 宇树 6/1 科创板审核 + 智元 5/31 开源 τ0-WM，国内具身智能产业链进入 IPO + 开源 + 数据三线并进阶段。是否能形成对海外 Tesla Optimus / Figure AI 的差异化优势，6-9 月观察。
- **Anthropic 反超 OpenAI 之后** — 估值反超 + 人才虹吸 + 企业采购份额都已经过线，下一步是产品差异化能否守住。Opus 4.8 + Dynamic Workflows 是当前杀手锏，待 OpenAI GPT-5.6 / Codex 后续动作。

## ✍ 编辑说

- **τ0-WM / 推荐**：如果你做具身机器人研究或者下游应用，5B Apache 权重值得 fine-tune 一遍；真机遥操作 1.78 万小时这个数据规模 6 个月内不会被打破，是国内开源具身基座的事实参照。
- **AI Coding 三线撕裂 / 做技术储备**：如果你的团队重度依赖 Cursor / Claude Code / Codex，6 月需要重新评估单任务 token 成本——Auto-review / Dynamic Workflows 把 token 用量推高 1-2 个量级，叠加 GitHub Copilot 按量计费、Cursor / Codex 促销退潮，月度账单结构会重排。
- **Anthropic vs OpenAI / 关注**：企业开发者 12 个月内的模型选型逻辑，从"看 benchmark 谁高"转向"看企业采购份额谁稳"。Anthropic 60% / OpenAI 32% 这个数据点如果在 7-8 月还能保持，意味着 AI 基础设施的市场格局开始真正分化。
- **国内大模型估值锚 / 关注**：智谱破 7000 亿港元、DeepSeek 国家大基金 450-500 亿美元这两件事如果都落地，国内大模型公司的估值参照系会被重置。下半年 IPO / 融资节奏会更密集。

## 🔗 引用链接

- [1] sii-research/tau-0-wm GitHub: https://github.com/sii-research/tau-0-wm
- [2] sii-research/tau-0-wm HuggingFace: https://huggingface.co/sii-research/tau-0-wm
- [3] 网易科技 5/31 τ0-WM 报道: https://www.163.com/news/a/KU93L4TR0511DSSR.html
- [4] Cursor 3.6 changelog: https://cursor.com/changelog
- [5] HN "MCP is dead" 讨论: https://news.ycombinator.com/item?id=48330436
- [6] AI 前线 5/31 国内 AI 一周复盘: https://www.163.com/dy/article/KU8SON8V05566ZHB.html
- [7] Humphrey Theodore Anthropic 招聘 list: https://humphreytheodore.com/writing/anthropic-talent-magnet-karpathy-boyd-2026
- [8] OpenAI Rosalind Biodefense 报道: https://www.newsbytesapp.com/news/science/openai-launches-gpt-rosalind-to-spot-and-halt-biological-threats/tldr
- [9] Microsoft Build 2026 预告: https://windowsnews.ai/article/build-2026-makes-windows-an-agent-platform-for-ai-developers.420496
- [10] Reuters CNN 起诉 Perplexity: https://www.reuters.com/legal/litigation/cnn-files-suit-against-perplexity-alleging-unlawful-content-distribution-2026-05-28/
- [11] TechTimes 5/31 AI 监管三战线: https://www.techtimes.com/articles/317461/20260531/ai-regulation-2026-opens-three-fronts-cnn-sues-perplexity-openai-aligns-eu-rules.htm
- [12] 昆仑万维 SkyClaw-v1.0: https://cloud.tencent.com/developer/news/3983870
- [13] Cyprus Mail Sam Altman CBA Sydney: https://cyprus-mail.com/2026/05/31/openais-altman-says-ai-unlikely-to-lead-to-jobs-apocalypse
- [14] AI Tools Recap 5/31 行业资讯: https://aitoolsrecap.com/Blog/ai-news-may-31-2026
- [15] IT之家 5/28 字节自研 CPU: https://www.ithome.com/0/956/718.htm
- [16] OpenBMB/ForgeTrain GitHub: https://github.com/OpenBMB/ForgeTrain
- [17] harry0703/MoneyPrinterTurbo GitHub: https://github.com/harry0703/MoneyPrinterTurbo
- [18] microsoft/markitdown GitHub: https://github.com/microsoft/markitdown
- [19] anthropics/claude-code GitHub: https://github.com/anthropics/claude-code
- [20] OpenBMB/VoxCPM GitHub: https://github.com/OpenBMB/VoxCPM
- [21] EveryInc/compound-engineering-plugin GitHub: https://github.com/EveryInc/compound-engineering-plugin
- [22] supermemoryai/supermemory GitHub: https://github.com/supermemoryai/supermemory
- [23] openclaw v2026.5.31-beta.1 release: https://github.com/openclaw/openclaw/releases/tag/v2026.5.31-beta.1
- [24] talkie-1930-13b 36氪报道: https://36kr.com/p/3789105218362369
