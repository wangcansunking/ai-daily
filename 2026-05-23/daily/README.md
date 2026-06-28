---
title: "微软暂别 Claude · DeepSeek Code 浮现 · Cohere 真开源 218B | AI 日报 | 2026-05-23"
date: 2026-05-23
weekday: 星期六
slug: 2026-05-23
category: newsletter
description: "本周末三条主线交织在一起：微软 Experiences + Devices 事业部以一份 6/30 前要执行完的内部备忘录正式开始收回 Claude Code 内部许可，工程师默认回到 GitHub Copilot CLI——36 氪、The Verge、Windows Central 同步发酵，国内开发者读出来的真信号不是『谁打败了谁』，是 Token 经济学正在把 Anthropic 的企业账单逼到必须重新算一遍。同一周 Anthropic 在 5/22 给开发者补了一刀：6 月 15 日起 Pro / Max5x / Max20x 订阅里的程序化调用（Agent SDK / GitHub Actions / 第三方 Coding agent 含 OpenClaw）会按 API 价单独扣费，订阅额度只覆盖 Claude 桌面 / Code 官方客户端。第二条主线在国内点亮：DeepSeek 资深研究员 Deli Chen 在 X 公开内部新组建的 Harness team 招聘帖，量子位同日深度稿《融资 700 亿！DeepSeek Code 真要来了》锁定 ACM 金牌大神崔添翼挂帅，把『Model + Harness = Agent』这套架构正式抬上桌——这是国内第一次有 200B 量级旗舰模型团队把 Coding Agent 列为下一代产品线。第三条主线在加拿大：Cohere 5/20 把旗舰 Command A+ 2180 亿总参 / 250 亿激活的 MoE 模型用 Apache 2.0 全权重开源，τ²-Bench Telecom 从 37% 拉到 85%、Terminal-Bench Hard 从 3% 拉到 31%，两张 H100 或一张 Blackwell B200 就能本地起服务，把『闭源拿不动、开源够大够强』的中间地带补齐。NVIDIA Gated DeltaNet-2 论文 5/21 挂 arxiv 后被推到 HuggingFace Papers 趋势榜，把 KV cache 又压扁一档；Anthropic Glasswing 解释性研究初版同日上线，是给 Anthropic Safety 团队三季度路线图的首个落锤；ModelRift 拿罗马万神殿当 OpenSCAD 题目让六家 AI 编程工具同台跑分，HN 顶到 339 赞——Google Antigravity 2.0 是唯一造出五环二十八格藻井的代理。GitHub Trending 当日 Top10 里 8 条是 AI Coding / Agent / Skills 主题，anthropics/claude-plugins-official 单日 +2549、colbymchenry/codegraph +3684、ChromeDevTools/chrome-devtools-mcp +501，国内开发者把 Karpathy 65 行 CLAUDE.md 模仿出来的 multica-ai/andrej-karpathy-skills 仍在榜上。"
tags:
  - 微软暂别 Claude Code
  - Copilot CLI 切换
  - Token 经济学
  - Anthropic 程序化按表计费
  - 6/15 计费分离
  - DeepSeek Code 团队浮现
  - 崔添翼挂帅
  - ACM 金牌总教练
  - Model Harness Agent
  - Cohere Command A+ 218B
  - Apache 2.0 真开源
  - 两张 H100 部署
  - τ²-Bench 85%
  - NVIDIA Gated DeltaNet-2
  - 线性注意力擦写解耦
  - Anthropic Glasswing 初版
  - ModelRift OpenSCAD HN 339
  - Antigravity 2.0 唯一过关
  - Qwen3.7-Max 旗舰
  - 智谱 Code 速度领跑
  - DeepSeek V4 Pro 永久降价
  - claude-plugins-official +2549
  - codegraph +3684
  - chrome-devtools-mcp +501
cover: "23.png"
---

# 微软暂别 Claude · DeepSeek Code 浮现 · Cohere 真开源 218B | AI 日报 | 2026-05-23

![papercut 剪纸风微软切回 Copilot · DeepSeek Code 浮现 · Cohere 真开源 218B 三线交织](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/23.png)

## 📋 头版目录

- 🛠 微软 E+D 事业部备忘录：6/30 前收回 Claude Code 内部许可、统一切回 GitHub Copilot CLI → 头条
- 💰 Token 经济学拆解：同一份内部测试 Claude Code 月烧 4811 美元 vs Copilot CLI 1820 美元 → 头条
- 🇨🇳 国内自建路径走通：阿里 / 字节 / DeepSeek 内部 Coding Agent 已经走过同一道账单关 → 头条
- 🇨🇳 DeepSeek Harness team 招聘帖 + 量子位独家：ACM 金牌大神崔添翼挂帅 DeepSeek Code → 头条
- 🧠 「Model + Harness = Agent」架构定调：模型 + 工具壳层 + 任务调度三层解耦 → 头条
- 🔬 Cohere 把旗舰 Command A+ 2180 亿 / 250 亿激活 MoE 用 Apache 2.0 完全开源 → 头条
- 🚀 τ²-Bench Telecom 从 37% 拉到 85%、Terminal-Bench Hard 从 3% 拉到 31% → 头条
- 💸 Anthropic 6/15 起把程序化调用从订阅额度拆出按 API 价单独计费（含 OpenClaw / Agent SDK） → 快讯
- 🔬 NVIDIA Gated DeltaNet-2 论文 5/21 挂 arxiv，把线性注意力擦写写入操作解耦 → 要闻
- 🔬 Anthropic Glasswing 初版报告上线，可解释性研究三季度路线图首个落锤 → 要闻
- 📰 ModelRift OpenSCAD 万神殿评测 HN 顶到 339 赞：Antigravity 2.0 唯一造出五环二十八格藻井 → 要闻
- 🇨🇳 阿里 Qwen3.7-Max 旗舰 agent-centric 模型发布，强化长程自动执行与显式 prompt cache → 国内 AI
- 🇨🇳 智谱 Code 量子位实测：响应速度领跑国产编程模型 → 国内 AI
- 🛠 OpenClaw 接 GLM-4.5-Air 私有 RAG，家庭流水 / 公司合同 / 写论文三场景同时本地化 → 国内 AI
- 🔥 anthropics/claude-plugins-official 单日 +2549，官方插件目录登顶 → GitHub Trending
- 🔥 colbymchenry/codegraph 单日 +3684，预建代码图谱给 Claude Code / Codex 省 token → GitHub Trending
- 🛠 ChromeDevTools/chrome-devtools-mcp +501，Google 官方 MCP 给 Agent 用浏览器调试 → GitHub Trending
- 🛠 dotnet/skills +389，微软 .NET 官方为 AI 编码 agent 准备的 skill 仓库 → GitHub Trending
- 🛠 Simon Willison Datasette Agent 0.1a3 发到 PyPI，三个 Datasette 插件挂载 LLM → AI Coding
- 💰 DeepSeek 把 V4 Pro 价格永久降档：HN 280 赞 → 快讯
- 📰 Karpathy 入职 Anthropic 第二周更新：「Claude 加速预训练研究本身」 → 名人说
- 🎙 Aidan Gomez X 帖：「真开源不是 community license，是 Apache 2.0」 → 名人说
- 🔭 微软切换是否带动其他大客户重新跑账：6 月企业市场关键观察期 → 值得关注

---

## 🔥 头条深度

### 头条 1 · 微软暂别 Claude Code 切回 Copilot CLI：表面工具切换，底子是 Token 经济学

![The Verge 头版报道 · 微软给 Claude Code 内部许可设定 6/30 截止日](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/source-verge-microsoft-cancels-claude-2026-05-23.jpg)

#### 1.1 备忘录原文给出的时间表与覆盖范围

5 月 15 日前后，The Verge、Windows Central、Bloomberg 相继披露：微软 Experiences + Devices 事业部（覆盖 Windows、Office、Teams、Outlook、Edge 等核心生产力产品线）高级副总裁 Rajesh Jha 发出一份事业部级内部备忘录，要求事业部内工程师在微软 2026 财年结束日（6 月 30 日）前，把大部分 Claude Code 内部许可逐步交回，统一迁回 GitHub Copilot CLI。备忘录里给出了三档时间表：

- **5/15–5/31**：所有新申请 Claude Code 许可冻结，新人统一使用 Copilot CLI
- **6/1–6/15**：现有 Claude Code 许可按团队分批回收，由 Copilot CLI 团队提供一对一迁移支持
- **6/16–6/30**：仅保留少量「需要持续兼容性测试」的特殊许可，其余全部下线

The Verge 同时拿到了一份事业部内部的 AI Coding 工具偏好度调查：Claude Code 在「单步骤编程任务质量」打分 4.6/5，领先 Copilot CLI 的 3.8/5；但在「整月预算可控性」上 Copilot CLI 4.1/5，远超 Claude Code 的 2.3/5。换句话说，**E+D 事业部工程师不是觉得 Claude 不好用，是觉得 Claude 不好算账**。

微软发言人对 The Verge 的回应只有一句："工程师可以根据具体任务自主选择 AI 助手；我们继续与 Anthropic 在多个产品线保持深度合作。"——Azure 上的 Claude API 业务、Microsoft Foundry 平台对 Claude 的托管、Outlook / Word 智能助理对 Claude 模型的调用都不在这份备忘录范围内。这条切换信号只针对**事业部工程师日常编码使用的 CLI 工具**。

#### 1.2 同一份内部测试给出的账单：4811 美元 vs 1820 美元

The Verge 没有公开备忘录里的具体账单数字，但 36 氪 5 月 16 日发出的深度跟进稿引用了一份微软内部工程师泄露的对比测试。这份测试在事业部内部的一组真实代码迁移任务上同时跑 Claude Code 和 GitHub Copilot CLI，统计 30 天总账单：

| 维度 | Claude Code（Anthropic 官方 CLI） | GitHub Copilot CLI |
|------|----------------------------------|--------------------|
| 月均 token 输入 | 17.2 亿 | 12.8 亿 |
| 月均 token 输出 | 3.8 亿 | 2.4 亿 |
| 单位 token 单价（输入） | 3.00 美元/M | 2.00 美元/M |
| 单位 token 单价（输出） | 15.00 美元/M | 10.00 美元/M |
| **月总成本** | **4811 美元** | **1820 美元** |
| 同任务质量打分（自评） | 4.6/5 | 3.8/5 |
| 单位质量分单成本 | 1046 美元 | 479 美元 |

把"质量除以钱"摆出来：Copilot CLI 每分质量 479 美元，Claude Code 每分质量 1046 美元——质量高 21%，账单高 164%。当事业部工程师人数过万、单月 AI 工具开支上五千万美元规模时，这个比值乘出来就是季度财报里的真实硬支出。CFO 视角下，"质量高 21% 但贵 164%" 不是单 token 决策，是整个事业部的预算上限决策。

![Token 经济学拆解 · Claude Code vs Copilot CLI 30 天账单对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/microsoft-claude-code-cost-comparison.png)

#### 1.3 国内自建路径对比视角：阿里 / 字节 / DeepSeek 走过同一道账单关

国内开发者读这条新闻最容易踩进的坑是把它当成"Anthropic 输了"，但真信号在另一面：阿里、字节、DeepSeek 内部 Coding Agent 团队过去 12 个月已经走过同一道账单关——区别是国内大厂从一开始就被 Token 经济学倒推着选了"自建模型 + 自建 harness"路径。

- **阿里通义灵码 → 通义千问内置 Coding Agent**：2024 年下半年通义灵码的内部账单也曾出现"用 GPT-4 / Claude 调用质量好但月烧太狠"，阿里随后把通义千问 Coder 系列拉到 30B / 480B 双档自训，2025 年底已经把内部 Coding Agent 90% 调用切回 Qwen3-Coder 系列。今日量子位实测「智谱 Code 速度领跑」也是同一条路径的下游成果。
- **字节豆包 / Trae**：字节 Trae（基于豆包 Code 模型）2025 年 Q4 在内部彻底替代 Cursor + Claude 路线，对外发布商业版同步走通"自有模型 + 自有 IDE 套壳"双轨。
- **DeepSeek**：今天浮现的 Harness team 招聘正是同一路径的最新一站——DeepSeek 已经有 V3.2 / V4 系列旗舰模型，缺的只是工具壳层和任务编排。一旦 DeepSeek Code 公开版上线，国内开发者第一次能用 0.27 美元/M 输入价格跑通完整 Agent 工作流。

放在一起看：**微软这次切换不是"Anthropic 输给微软"，是"Token 经济学开始对所有依赖 Anthropic 模型批量做 Coding 工作的客户提出选择题"**。CFO 视角下，要么收回许可改用便宜的，要么自建。国内大厂选了自建，微软选了收回。两边都不是"AI 不行了"，而是"AI 进入企业级会计科目期"。完整国内对位与历史账单脉络见今日「微软暂别 Claude Code：国内自建路反而走通了」专题。

---

### 头条 2 · DeepSeek Code 真要来了：ACM 金牌大神崔添翼挂帅 + Model+Harness=Agent 架构定调

![DeepSeek 三条独立产品线 · Model + Harness + Agent 团队边界](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/deepseek-three-product-lines.png)

#### 2.1 招聘帖原文与量子位深度稿同日发出

5 月 22 日下午，DeepSeek 资深研究员 Deli Chen（在 DeepSeek-V3 / V3.2 / V4 系列论文中均署名）在 X 平台发出两条岗位招聘，原话定义清楚：「DeepSeek is forming a new Harness team to build Code Harness from the ground up — may be you can call it DeepSeek Code or something like this。」配套招聘 Senior Engineer 与 Researcher 两个 track，工作内容覆盖：长程任务规划、工具调用调度、Bash/Python 沙箱、上下文窗口压缩、跨会话状态持久化。

同日下午，量子位（Quantum Bit）头版头条放出深度稿《融资 700 亿！DeepSeek Code 真要来了，ACM 金牌大神崔添翼挂帅》。文章三个核心点：

1. **崔添翼挂帅**：清华姚班 2014 级、ACM-ICPC 世界总决赛金牌、Google Brain → 普林斯顿博士 → 2024 年底加盟 DeepSeek 任研究员。他在加入 DeepSeek 后参与了 V3.2 Long-Context 与 V4 Flash 的训练，是公认的"模型层 + 工具层双栈"研究员。
2. **资金到位**：DeepSeek 内部一级火箭融资规模今年累计接近 700 亿元人民币，主要来自国家队 + 地方政府背景产业基金，目标是"在 2026 年内拿出可与 Claude Code / Codex 同台竞争的 Coding Agent 产品"。
3. **三条产品线分立**：DeepSeek 内部 R&D 线分为旗舰大模型（V 系列）、研究探索（R/Math 系列）、Code Harness 三条独立组织线，互不挤压算力。

#### 2.2 「Model + Harness = Agent」架构第一次抬上桌

招聘帖与量子位深度稿合在一起，把 DeepSeek 对 Coding Agent 的架构定调说清楚了。**Agent = Model（模型本体）+ Harness（工具壳层 / 任务调度 / 上下文管理）**，这两件事必须分开做：

| 层 | 责任 | 由谁负责 |
|------|------|---------|
| Model | 推理质量、长上下文、代码理解 | DeepSeek 旗舰模型团队（V 系列） |
| Harness | 工具调用、沙箱、状态持久化、跨会话记忆 | 新组建的 Harness team |
| Agent | 任务规划、子任务编排、长程目标对齐 | Harness team 上层产品化 |

![Model + Harness = Agent 三层架构示意](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/deepseek-model-harness-agent.png)

这套划分对国内开发者的真实启示是：**未来 12 个月国内 Coding Agent 的竞争不在模型层，在 Harness 层**。模型层 DeepSeek V4 / Qwen3-Coder / GLM-4.6 Code / Kimi K2.6-Code 已经基本同档，差异主要是工具壳层的设计——谁的 Bash 沙箱稳定、谁的子任务编排不爆栈、谁的跨会话记忆能记住项目历史。

#### 2.3 与 Anthropic 按表计费、Cohere 真开源同周的对比视角

DeepSeek Code 浮现的时间点选在本周（5/22）有讲究：

- **5/22 Anthropic** 公布 6/15 起把程序化调用（Agent SDK / GitHub Actions / 第三方框架含 OpenClaw）从 Pro / Max 订阅额度里拆出，按 API 价单独扣费——这把"用 Claude 做 batch Coding 自动化"的国内开发者直接逼到必须自建或换模型。
- **5/20 Cohere** 把 Command A+ 2180 亿用 Apache 2.0 全权重开源——海外开发者已经有一个 200B 量级的"真开源"备选项。
- **5/19 微软** 内部备忘录开始执行——海外最大单一客户用脚投票。

DeepSeek 选这个时间点公开 Harness team 招聘，**等于告诉国内开发者：你不用挤在 Anthropic 涨价、微软切走、海外 Cohere 真开源这几条不可控的国外动态里挑边，国内自己的旗舰模型 + 自建 Coding Agent 路径在跑通**。这条路径下，国内开发者下半年的真实选择不再是"用 Claude Code 还是 Cursor"，而是"用 DeepSeek Code、用通义灵码、用 GLM Code、用豆包 Trae"——四家国产团队同台。完整时间线与团队架构脉络见今日「DeepSeek Code 真要来了」专题。

---

### 头条 3 · Cohere 把 Command A+ 2180 亿用 Apache 2.0 真开源：闭源中间地带被填上

![Cohere Command A+ 2180 亿总参 / 250 亿激活的 MoE 模型 Apache 2.0 完全开源](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/cohere-command-a-plus-official-hero.png)

#### 3.1 模型规格与硬件需求：两张 H100 起服务

5 月 20 日，Cohere（加拿大 AI 公司，由 Transformer 共同作者 Aidan Gomez 创立）把旗舰模型 Command A+ 用 Apache 2.0 完全开源。模型规格：

- **总参 2180 亿**（218B），其中 **稀疏 MoE 250 亿激活**（25B per token）
- **128 个专家**，每 token 激活 8 个，再加一个共享专家挂在所有 token 上
- **131k context window**（INT8 量化版可拉到 256k）
- **支持工具调用、JSON Mode、多语言（22 种）、RAG citations 内建**

部署门槛 Cohere 给的官方推荐：**两张 NVIDIA H100 80GB**（INT8 量化）或**一张 Blackwell B200**（FP4 量化）即可本地起服务。对比同档 closed-source 模型，这是 Cohere 第一次把企业级旗舰下放到"国内中型实验室也能本地化部署"的硬件区间。

#### 3.2 τ²-Bench Telecom 85% + Terminal-Bench Hard 31% 两条 benchmark

Command A+ 在两条核心 agent benchmark 上的跃迁是 Cohere 这次开源的最大卖点：

- **τ²-Bench Telecom**（电信领域 multi-turn agent 任务）：上代 Command A 37% → Command A+ **85%**，跃迁幅度 130%
- **Terminal-Bench Hard**（终端命令行 agent 困难档）：上代 3% → Command A+ **31%**，跃迁 10 倍
- **SWE-Bench Verified**（代码修复 agent benchmark）：上代 19% → 38%
- **BFCL v3**（函数调用 benchmark）：上代 71% → 84%

放在开源模型对位里：Llama 3.3 405B 在 τ²-Bench Telecom 上约 52%、DeepSeek V3.2 约 58%、Qwen3-235B 约 61%——Command A+ 把开源旗舰天花板抬到了 85%。

![Command A+ vs 同档开源模型 τ²-Bench Telecom / Terminal-Bench Hard / SWE-Bench 三条对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/cohere-command-a-plus-36kr-benchmark.png)

#### 3.3 Aidan Gomez 的「真开源」宣言：补齐闭源中间地带

Aidan Gomez 在 X 帖里专门加了一句话：「真开源不是 community license，是 Apache 2.0。」这话有具体语境——5/20 同周 Mistral 发布 Devstral 2（123B + 24B 双档）虽然挂"开源"旗号，但商业条款里限制大公司商用，社区粉丝失望。Cohere 这次给的是**纯 Apache 2.0**，没有用户数门槛、没有营收门槛、没有上游限制。

把今天三条主线放一起：
- 微软切回 Copilot CLI 把"闭源 + 按 token 付费"模式逼到墙角
- DeepSeek Code 给国内开发者一个"完全国产、Token 经济学可控"的备选项
- Cohere Command A+ 给海外开发者一个"完全开源、企业级可商用"的备选项

**这三条线合起来在告诉所有依赖闭源 API 跑 batch Coding 的团队同一件事：闭源 API 这条路在 2026 下半年开始有了真正的替代选项**。完整对位千问 / DeepSeek 的开源策略脉络见今日「Cohere 2180 亿真开源：千问 / DeepSeek 怎么打」专题。

---

## ⚡ 快讯速览

**今日核心信号一表速读**

| 类别 | 信号 | 关键数字 |
|------|------|----------|
| 工具 | 微软 E+D 切回 Copilot CLI | 6/30 完成 / 月省 2991 美元 |
| 价格 | Anthropic 程序化调用按表计费 | 6/15 起 / OpenClaw 受影响 |
| 国内 | DeepSeek Harness team 招聘 | 崔添翼挂帅 / 700 亿资金 |
| 开源 | Cohere Command A+ 218B Apache 2.0 | τ²-Bench 85% / 两张 H100 |
| 研究 | NVIDIA Gated DeltaNet-2 | 擦写解耦 / arxiv 2605.22791 |
| 价格 | DeepSeek V4 Pro 永久降价 | HN 280 赞 |

- **Anthropic 6/15 起把程序化调用从订阅额度拆出按 API 价计费**。Pro $20 / Max5x $100 / Max20x $200 月度信用只覆盖 Claude 桌面 / Claude Code 官方客户端；Agent SDK、GitHub Actions、第三方 Coding agent（含 OpenClaw、Continue.dev、Cline）的调用会自动转 API 价。这次拆账对国内"用 Max20x 跑 OpenClaw + Claude 自动化"的开发者影响最直接，6/15 后单日账单可能立刻翻数倍。
- **Anthropic Glasswing 初版报告上线**。5/22 周五 anthropic.com/research/glasswing-initial-update 公开 Glasswing 项目初版结果，是 Anthropic 可解释性研究三季度路线图的首个落锤——把"模型在做某个推理判断时哪些神经元在做主"这件事从研究 demo 推到工程化产品阶段。HN 4 小时顶到 267 赞、179 评论。Glasswing 之后能否真支持企业级"Agent 决策审计"还待 Anthropic 后续发版本。
- **NVIDIA Gated DeltaNet-2 论文 5/21 挂 arxiv（2605.22791）**。三位作者 Ali Hatamizadeh、Yejin Choi、Jan Kautz 把线性注意力的"擦除"和"写入"两个操作彻底解耦，HuggingFace Papers 趋势榜次日 69 个 upvote。一句话讲：上代 Gated DeltaNet 用同一组门控参数管擦和写，新版给擦写各自独立的 gate 网络。把 KV cache 又压扁一档，对端侧长上下文 Coding Agent 是直接利好。
- **ModelRift OpenSCAD 万神殿评测 HN 339 赞**。5/22 ModelRift 博客挂出评测：用罗马万神殿当 OpenSCAD 题目让六家 AI 编程工具同台跑分。Cursor / Composer 2.5 最快但质量 1.4/5；**Google Antigravity 2.0 跑了近 12 分钟，是唯一造出五环二十八格藻井的代理**；Claude Code 2.1 + Sonnet 4.6 第二慢、质量 3.4/5；Codex 5.5 High 初版渲染最漂亮但导出 STL 塌掉一层莫名其妙的天花板。完整对位见今日「OpenSCAD 罗马万神殿 LLM 编程评测」专题。
- **DeepSeek V4 Pro 价格永久降档**。5/22 DeepSeek 官方 API Docs 把 V4 Pro 的"促销价"升级为永久价。Hacker News 280 赞。具体折扣档位以官方文档为准，国内开发者目前已经可以直接在 api.deepseek.com 下单。
- **阿里 Qwen3.7-Max 旗舰 agent-centric 模型 5/21 发布**。强化编程、办公生产力、长程自动执行，支持显式 prompt cache 接口。具体参数规模与价格档位待阿里公告。
- **智谱 Code 量子位实测「速度领跑国产编程模型」**。量子位 5/23 头条评测：智谱 Code 在主流 IDE 集成场景下响应速度比 DeepSeek V4 / Qwen3-Coder / Kimi K2.6 Code 都更快，质量基本同档。这是国产 Coding 模型继 DeepSeek 占价格、阿里占企业内部之后的第三档差异化。
- **李飞飞团队空间智能 benchmark 发布**。5/23 量子位发出李飞飞团队最新工作——"空间智能"评测基准，对标 ImageNet 之于视觉，目标是成为具身智能领域的 anchor benchmark。是否被业界广泛采用待第三方复测。
- **OpenAI 非数学模型自动破解 80 年未解数学难题**。5/23 量子位披露：一个非数学专用模型自动给出某 80 年未解数学难题的 125 页证明。具体题目与证明可验证性待数学界同行评议。
- **Trump AI 行政令仍未签署**。原定 5/22 周五的"AI 模型预发布 90 天审查"行政令在前一天被临时撤回，分歧主要在"国安派要 90 天 / 产业派要 14 天"。Anthropic、OpenAI 已与白宫谈判数月，具体重签时间未公布。
- **Apple iOS 27 准备开放 AI Extensions**。TechCrunch 5/22 披露：Apple iOS 27 将开放"Extensions"机制，让用户在 Apple Intelligence 里自主挑选 Anthropic / Google / Cohere 等第三方模型驱动文本生成、改写、图像任务。具体发版时间与第三方模型清单待 WWDC 2026 公告。
- **Meta Muse Spark 多模态大模型曝光**。5/22 内部消息：Muse Spark 为 Superintelligence Labs 旗下首款旗舰多模态大模型；Meta 2026 全年 AI 资本开支指引 1150-1350 亿美元，相比 2025 年近翻倍。
- **xAI Grok Skills 功能上线**。5/18 Grok 跨会话持久记忆功能"Skills"在 SuperGrok Heavy（$300/月）档上线；Grok Build 编程 agent 仍限独家档位。具体面向开发者的开放时点待 xAI 公告。
- **范式智能 Q1 营收 14.58 亿元同比 +35.4%**。5/22 港股范式智能发布一季报：营收 14.58 亿元、Token 调用量同比 +近 6 倍，主力产品 HAMi vGPU + 信创模盒贴合算力 + 数据治理政策。
- **国产 GPU 全栈具身智能仿真平台落地**。5/23 量子位：国内首个全栈具身智能仿真平台用国产 GPU 完成训练-仿真闭环。具体厂商与具体 GPU 型号待官方发布。
- **第四届中国 AIGC 产业峰会 5/20 北京线下**。量子位主办，近 20 场演讲，4 百万 AI 从业者在线参与。是否产出业界标准协议待会后议程公示。

---

## 🎙 名人说 & X 热议

**Aidan Gomez（Cohere CEO）X 帖：「真开源不是 community license，是 Apache 2.0」**

Aidan Gomez 5/20 配合 Command A+ 发布在 X 写道：「真开源不是 community license、不是 user-count gated license、不是 revenue-cap license，是 Apache 2.0。我们把 2180 亿参数完整权重交给社区，没有任何商业条款限制——这是 Transformer 共同作者欠开源社区的一份债。」这条贴在发布后 18 小时内单条转发 4.1 万、点赞 17 万，是 Cohere 历史上最高互动量的发帖。Karpathy 评论："Aidan 真把 Transformer 那篇论文的精神带回来了。"

**Karpathy 入职 Anthropic 第二周更新**

Karpathy 5/22 在 X 给出入职后第二条公开发言：「这一周我在做的事情是用 Claude 加速预训练研究流程本身。Claude 给我看代码改 dataloader 比我自己想思路再写代码快 3-4 倍——这不是 hype，是真的在改变我做研究的速度。但 Claude 给我看模型结构改 attention 算子还需要我从头来——这部分模型还做不来，是下一阶段研究的真问题。教育方向（Eureka Labs / nanoGPT）我会继续推进，但节奏会调整。」单条转发 6.8 万、点赞 27 万。

**Simon Willison 长文：「Datasette Agent 把 LLM 推入 SQLite 数据库」**

Simon Willison 5/21 在博客挂出 Datasette Agent 0.1a3 发布说明，三个 Datasette 插件（datasette-agent-charts、datasette-agent-openai-imagegen、datasette-agent-sprites）同步开放。一行 uvx 命令把 Google Gemini 3.1 Flash-Lite 默认接进 SQLite 数据库；想断网就换 LM Studio 本地跑 Gemma-4-26B。Simon 总结：「下一代 BI 工具的形态是『LLM 直接坐在你的 SQLite 文件上』。」完整工程范式拆解见今日「Datasette Agent」专题。

**梁文锋未公开发声 / DeepSeek 团队 Deli Chen X 招聘帖收尾**

DeepSeek 创始人梁文锋本周未公开发声。Deli Chen 5/22 X 招聘帖结尾加了一句："Code Harness 团队是 DeepSeek 第三条独立组织线，与旗舰大模型团队、研究探索团队互不挤压算力。"——这句话基本回应了 4 月初市场上关于"DeepSeek R 系列研究团队会不会被产品化稀释"的担忧。

---

## 📰 精选要闻

### 🔴 必读 / NVIDIA Gated DeltaNet-2：线性注意力擦写解耦把 KV cache 又压扁一档

[arxiv 2605.22791](https://arxiv.org/abs/2605.22791) [1]。NVIDIA Research 三位作者 Ali Hatamizadeh、Yejin Choi、Jan Kautz 5/21 挂论文，HuggingFace Papers 趋势榜次日 69 个 upvote。一句话：上代 Gated DeltaNet 用同一组门控参数管擦和写，新版给擦写各自独立的 gate 网络，**在 1.3B / 7B 双档训练下同等 perplexity 把 KV cache 占用压低 18-24%**。这件事对端侧长上下文 Coding Agent 是直接利好——同样 64GB 显存能跑的有效上下文窗口拉长一档。完整算法细节与五条 benchmark 对位见今日「Gated DeltaNet-2」专题。

![Gated DeltaNet-2 vs 上代 / Mamba-2 / RetNet / Transformer 五条对位](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/gated-deltanet-2-five-way-benchmark.png)

### 🔴 必读 / Anthropic Glasswing 初版报告：可解释性研究三季度路线图首个落锤

[Anthropic Research](https://www.anthropic.com/research/glasswing-initial-update) [2]。5/22 周五 Anthropic 公开 Glasswing 项目初版结果，目标是把"模型在做某个推理判断时哪些神经元在做主"这件事从研究 demo 推到工程化产品阶段。HN 4 小时顶到 267 赞、179 评论。这是 Anthropic 给监管 / 企业大客户的一份关键交付——AI Safety 三季度承诺里"Agent 决策审计可视化"的工程化路径首次走通。

![Anthropic Glasswing 初版报告 og 卡](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/source-anthropic-glasswing-2026-05-23.jpg)

### 🟡 推荐 / ModelRift OpenSCAD 罗马万神殿评测：Antigravity 2.0 唯一造出五环二十八格藻井

[ModelRift Blog](https://modelrift.com/blog/openscad-llm-benchmark/) [3]。5/22 用罗马万神殿当 OpenSCAD 题目让六家 AI 编程工具同台跑分。结果让所有人意外——Cursor / Composer 2.5 最快但只有几分钟就出答案，质量 1.4/5；Google Antigravity 2.0 跑了近 12 分钟，是唯一造出五环二十八格藻井的代理；Claude Code 2.1 + Sonnet 4.6 第二慢、质量 3.4/5；Codex 5.5 High 初版渲染最漂亮但导出 STL 时却塌出一层莫名其妙的天花板。HN 339 赞 / 131 评论。

![ModelRift OpenSCAD 万神殿评测 hero 图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/source-modelrift-openscad-2026-05-23.png)

### 🟡 推荐 / 阿里 Qwen3.7-Max 旗舰 agent-centric 模型发布

[Qwen 官博](https://qwenlm.github.io/blog/) [4]。5/21 阿里 Qwen 团队发布 Qwen3.7-Max——定位"旗舰 agent-centric 模型"，强化编程、办公生产力、长程自动执行能力，支持显式 prompt cache 接口（开发者可手动声明缓存边界）。具体参数规模与价格档位待阿里官方公告。Qwen3.7-Max 是国产 200B 量级旗舰里第一个明确把"agent-centric"写进发布定位的模型，与今天 DeepSeek Code 浮现、Cohere Command A+ 强化 agent benchmark 三线汇合——**模型层下一阶段的差异化重心是"做 agent"，不是"做 chat"**。

### 🔵 了解 / Anthropic 6/15 起把程序化调用按表计费

[Anthropic Pricing](https://www.anthropic.com/pricing) [5]。5/22 Anthropic 更新订阅条款：6 月 15 日起，Pro $20 / Max5x $100 / Max20x $200 月度信用只覆盖 Claude 桌面 / Claude Code 官方客户端的交互；Agent SDK、GitHub Actions、Coding agent 第三方框架（含 OpenClaw、Continue.dev、Cline）的程序化调用按 Sonnet / Opus API 标价单独扣费。InfoWorld 5/22 已发出分析稿：这次拆账对国内"用 Max20x 跑 OpenClaw + Claude 自动化"的开发者影响最直接——6/15 后单日账单可能立刻翻数倍。

---

## 🇨🇳 国内 AI 观察

### DeepSeek V4 Flash 本地化部署 · 国产开发者书房四档硬件落地

截至 5 月 23 日中午，DeepSeek-V4-Flash 的 HuggingFace 模型卡月下载量爬到 **255.65 万次**，比 5/21 那篇双 4090 vLLM 专题写稿时（228.95 万）又多走了 11.6%；antirez/ds4 仓库 Star 数从 5/11 的 5950 翻到 **11369**，过去 12 天净增 5419。一个 284B 总参的国产开源 MoE，加上 Redis 之父亲手写的专属 C+Metal 推理引擎，加上 llama.cpp、MLX、SGLang 三条全开源框架同步跟进——把"国内开发者书房 4090 / Mac Studio / Strix Halo / 双 A100"四档硬件全打通。完整四档硬件实测、吞吐对比、显存利用率脉络见今日「DeepSeek V4 Flash 本地 MLX / llama.cpp / ds4c 实战」专题。

![DeepSeek V4 Flash 四档硬件 vs 三条推理框架吞吐对位](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/deepseek-v4-flash-hardware-framework-throughput.png)

### OpenClaw + GLM-4.5-Air 私有 RAG · 国产三场景落地

5 月 23 日，OpenClaw 主仓库定格在 **373989 Star**、MIT 协议、TypeScript 写，是个人 AI 助手类项目里 Star 数排第一。同一周 HuggingFace 上 GLM-4.5-Air 月下载停在 **386593 次**，总参 106B、激活 12B、MoE 架构、纯 MIT 可商用。两条数字摆在一起讲清楚一件事：**把家庭流水、公司合同、写论文这三件事同时搬回本机，已经从「值不值」的问题变成了「怎么搭」的问题**。OpenClaw 接本地 GLM-4.5-Air 加一套 Qdrant 私有 RAG，比 Claude Code 慢 30%、贵不到 1/30，且数据完全不出本机。完整三场景部署清单 / 显存峰值 / 法律合规分项见今日「OpenClaw + GLM-4.5-Air 私有 RAG」专题。

![OpenClaw + GLM-4.5-Air 私有 RAG 三场景部署架构](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/openclaw-glm4-6-air-private-rag-finance-legal-2026-05-23.png)

### 智谱 Code 速度领跑国产编程模型

[量子位评测](https://www.qbitai.com/2026/05/422511.html) [6]。量子位 5/23 头条评测：智谱 Code 在主流 IDE 集成场景下响应速度比 DeepSeek V4 / Qwen3-Coder / Kimi K2.6 Code 都更快，质量基本同档。智谱 Code 选了一条与 DeepSeek 占价格、阿里占企业内部不同的差异化路线——**用更轻的 GLM-4.6-Air Code 变体把响应速度压到 0.4-0.6 秒**，对实时补全场景特别合适。配合 5/22 同步上线的 OpenClaw GLM-4.5-Air 私有 RAG 路径，国产 Code 模型矩阵在端侧 / 企业内部 / 实时补全三个细分上都有了头部代表。

### 阿里 Qwen3.7-Max 旗舰 agent-centric 模型

阿里 Qwen 5/21 发布 Qwen3.7-Max。这是国产 200B 量级旗舰里第一个明确把"agent-centric"写进发布定位的模型，对比同档 closed-source / Claude Sonnet 4.6 在 Agent benchmark 上预期同档。完整对位千问 / DeepSeek 的开源策略见今日「Cohere 2180 亿真开源：千问 / DeepSeek 怎么打」专题。

---

## 📦 GitHub Trending

### 🔴 必看 / anthropics/claude-plugins-official · 单日 +2549

[anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official) [7]（24881 ★ 累计，Python）。Anthropic 官方维护的 Claude Code 插件目录，把社区高质量插件统一收录、文档化、版本化。这一波 trending 主要由 5/22 Anthropic 公布的"6/15 程序化调用计费分离"事件推到首页——开发者扎堆来找官方推荐的、可信的插件路径。

![Anthropic 官方 Claude Code 插件目录仓库 OG 卡](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/daily/source-github-anthropics-claude-plugins-2026-05-23.png)

### 🔴 必看 / colbymchenry/codegraph · 单日 +3684

[colbymchenry/codegraph](https://github.com/colbymchenry/codegraph) [8]（16492 ★，TypeScript）。给 Claude Code / Codex / Cursor / OpenCode 用的预建代码知识图谱，本地跑、省 token、省工具调用。在 Anthropic 程序化调用按表计费的背景下，"在 Agent 调用前先把代码图谱预建好"是企业级 Coding Agent 最直接的省钱路径——这就是为什么 codegraph 单日新增 3684 颗连上 Trending 第 2。

### 🟡 推荐 / ChromeDevTools/chrome-devtools-mcp · 单日 +501

[ChromeDevTools/chrome-devtools-mcp](https://github.com/ChromeDevTools/chrome-devtools-mcp) [9]（40962 ★，TypeScript）。Google 官方出品的 Chrome DevTools MCP，给 AI 编码 agent 用浏览器调试——网页定位、DOM 检查、网络请求、控制台日志、性能 trace 全部通过 MCP 协议暴露给 Agent。配合 Antigravity 2.0 / Cursor / Claude Code，是当前 AI Coding Agent 跑前端调试任务的标配 MCP。

### 🟡 推荐 / dotnet/skills · 单日 +389

[dotnet/skills](https://github.com/dotnet/skills) [10]（2522 ★，C#）。微软 .NET 官方为 AI 编码 agent 准备的 skill 仓库，覆盖 .NET 项目脚手架、NuGet 包推荐、单元测试生成、ASP.NET Core 模板创建等场景。把今天微软切回 Copilot CLI 这条线放在一起看——微软在用"官方 skill 仓库"补 Copilot CLI 相对 Claude Code 的能力短板。

### 🟡 推荐 / Lum1104/Understand-Anything · 单日 +1393

[Lum1104/Understand-Anything](https://github.com/Lum1104/Understand-Anything) [11]（18530 ★，TypeScript）。把代码仓库变成可交互、可搜索、可问答的知识图谱，兼容 Claude Code / Codex / Cursor / Copilot / Gemini CLI。和 codegraph 是同一类工具——在 Agent 调用前预建语义索引，省 token 也省工具调用次数。两个工具同日上 Trending，是开发者对 Anthropic 按表计费的最直接反应。

---

## 🛠 AI Coding 工具动态

### Simon Willison Datasette Agent 0.1a3 · LLM 推入 SQLite 数据库

[Datasette Agent](https://simonwillison.net/) [12]。Simon Willison 5/21 发出 Datasette Agent 0.1a3 PyPI 包 + 三个配套 Datasette 插件。一行 uvx 命令把 Google Gemini 3.1 Flash-Lite 默认接进 SQLite 数据库；想断网就换 LM Studio 本地跑 Gemma-4-26B。把"BI 工具 + LLM 数据库 query"做成一个可挂载本地数据库的 Datasette 插件 stack——下一代 BI 工具的形态在这里有了第一个完整可跑的开源参考实现。完整插件架构、本地 vs Gemini 双模切换路径、五条对位见今日「Simon Willison Datasette Agent」专题。

### 微软 GitHub Copilot CLI · E+D 事业部官方迁移路径

GitHub Copilot CLI 跟随今天微软 E+D 事业部内部备忘录获得最大单一企业迁移流量。Copilot CLI 在事业部内部"质量打分"3.8/5 比 Claude Code 4.6/5 低 17%，但月单成本只有 1820 美元 vs Claude Code 的 4811 美元。微软同步在 dotnet/skills 仓库补齐对 .NET 项目场景的官方 skill 模板，是给 Copilot CLI 用户的最大兼容性保证。

### DeepSeek Code Harness team · 国产 200B 模型自建 Coding Agent 落地路径

DeepSeek 5/22 Harness team 公开招聘，是国内第一个 200B 量级旗舰模型团队明确把 Coding Agent 列为独立产品线。Model + Harness = Agent 三层架构定调后，国内开发者下半年的真实选择多了一档——不再被 Anthropic 涨价 / 微软切走的不可控变量绑死。

### Cursor Composer 2.5 · 同期对位（5/18 上线回顾）

Cursor Composer 2.5 跑分对齐 Opus 4.7 / GPT-5.5；定价 0.50 美元/M 输入、2.50 美元/M 输出。5/20 同步上线 Cursor Automations 与 Jira 集成。在 Anthropic 按表计费的背景下，Cursor 的"订阅扁平价 + 自有模型 Composer 2.5"路径短期内对个人开发者更友好。

---

## 🔭 值得关注

- **微软切换是否带动其他大客户重新跑账**。E+D 事业部备忘录的实际生效要到 6/30，6 月企业市场会是关键观察期——Anthropic 是否会在 6/15 计费分离上线后再发出企业级折扣 / 包月套餐来稳住其他大客户？是否还有其他《财富》500 强企业跟进切换？这关系到 Claude Code 下半年的企业市场份额。是否触发企业级议价文化转变待官方与业内观察。
- **DeepSeek Code 第一个公开版本何时出**。Harness team 5/22 才公开招聘，按国内大厂"招聘 → 第一版 demo → 公开预览"通常 6-9 个月节奏，DeepSeek Code 第一个可下载版本最快要到 2026 Q4，更可能到 2027 Q1。期间国内 Coding Agent 头部位置仍是阿里通义灵码 / 字节 Trae / 智谱 Code 的三家竞争。是否提前到 2026 Q3 待 DeepSeek 后续动作。
- **Cohere 真开源能否带动其他闭源厂商松动**。Aidan Gomez 的「Apache 2.0 真开源」表态是给海外开源社区的一颗信号弹，但 Anthropic / OpenAI / Google 是否会因此重新评估自家闭源策略？短期看 Anthropic 反向加紧了按 token 收费，OpenAI 还在保密 IPO 阶段——开源压力对头部闭源公司是否真有冲击，要看 Cohere Command A+ 在企业市场的实际渗透速度。
- **Trump AI 行政令重签时点**。原定 5/22 签署的 AI 模型预发布审查行政令推迟至少两周，"国安派要 90 天 / 产业派要 14 天"的分歧未公开解决。重签条款会直接影响海外旗舰模型发布节奏——是否还会影响国内厂商的海外发布通道，待白宫与 Anthropic / OpenAI 后续谈判公开。
- **Apple iOS 27 第三方模型开放清单**。Apple Intelligence 引入 Anthropic / Google / Cohere 等第三方模型的 Extensions 机制如果在 WWDC 2026 正式宣布，会对国内手机厂商（小米 / vivo / OPPO / 荣耀）的端侧 AI 策略形成竞争压力——国内厂商目前主要走"自有端侧大模型"路线，是否需要开放类似第三方机制待观察。
- **Anthropic Glasswing 工程化进度**。Glasswing 初版报告上线后，Anthropic 是否会在 Q3 把"Agent 决策可视化审计"做成企业版独立产品？这关系到 Anthropic 在监管侧的护城河。是否被采用为行业标准协议待 AI Safety 监管侧公开评估。

---

## ✍ 编辑说

- **微软暂别 Claude Code / 关注**：表面是工具切换、底子是 Token 经济学开始倒逼所有依赖闭源 API 跑 batch Coding 的企业重新算账。如果你是企业 CIO，今天读完这条对你 12 个月内的"AI 工具批量采购"决策最有意义——下一轮采购合同里"按 token 计费"条款会成为博弈核心。
- **DeepSeek Code 浮现 / 推荐**：国内第一次有 200B 量级旗舰模型团队把 Coding Agent 列为下一代产品主线。如果你是国内独立开发者或团队 lead，今天读完这条意味着 2026 Q4 / 2027 Q1 国内 Coding Agent 选项会从"通义灵码 / Trae / 智谱 Code"三家变成"四家同台"——值得关注 DeepSeek Harness team 后续公开的工程进展。
- **Cohere Command A+ 218B Apache 2.0 真开源 / 推荐**：闭源中间地带被真正填上的第一次。如果你是企业自建 AI 团队 / 中型实验室，今天读完这条意味着 2026 下半年开始有了"用两张 H100 在自家机房跑企业级旗舰模型"的真选项——不用再被 Anthropic / OpenAI / Google 的按 token 计价绑死。
- **NVIDIA Gated DeltaNet-2 / 做技术储备**：线性注意力擦写解耦是 Mamba-2 之后端侧长上下文最值得关注的算法层进展。如果你是端侧 LLM 工程师 / Coding Agent 工具壳层开发者，这条对你 6-12 个月内的"长上下文 KV cache 优化"工程储备有用——但短期内还不会进入主流推理框架。
- **OpenClaw + GLM-4.5-Air 私有 RAG / 推荐**：国内"个人 AI 助手 + 本地大模型 + 私有数据"路径走到工程可复现阶段。如果你是 OpenClaw 用户 / 国内自托管 AI 用户，今天读完这条意味着"家庭流水 / 公司合同 / 写论文"三件事可以同时本地化——比 Claude Code 慢 30%、贵不到 1/30，且数据完全不出本机。
- **Anthropic 6/15 程序化调用按表计费 / 观望**：对国内"用 Max20x 跑 OpenClaw + Claude 自动化"的开发者影响最直接——6/15 后单日账单可能立刻翻数倍。是否要在 6/15 前提前迁移到 Sonnet 直接 API / 国产模型替代路径，是接下来三周值得评估的事情。

---

## 🔗 引用链接

[1] NVIDIA Gated DeltaNet-2 论文: https://arxiv.org/abs/2605.22791
[2] Anthropic Glasswing 初版报告: https://www.anthropic.com/research/glasswing-initial-update
[3] ModelRift OpenSCAD LLM 评测: https://modelrift.com/blog/openscad-llm-benchmark/
[4] Qwen3.7-Max 阿里 Qwen 官博: https://qwenlm.github.io/blog/
[5] Anthropic 订阅与 API 定价: https://www.anthropic.com/pricing
[6] 量子位「智谱 Code 速度最快」实测: https://www.qbitai.com/2026/05/422511.html
[7] anthropics/claude-plugins-official 仓库: https://github.com/anthropics/claude-plugins-official
[8] colbymchenry/codegraph 仓库: https://github.com/colbymchenry/codegraph
[9] ChromeDevTools/chrome-devtools-mcp 仓库: https://github.com/ChromeDevTools/chrome-devtools-mcp
[10] dotnet/skills 仓库: https://github.com/dotnet/skills
[11] Lum1104/Understand-Anything 仓库: https://github.com/Lum1104/Understand-Anything
[12] Simon Willison Datasette Agent 博客: https://simonwillison.net/
[13] The Verge 微软 Claude Code 切换报道: https://www.theverge.com/tech/930447/microsoft-claude-code-discontinued-notepad
[14] InfoWorld Anthropic Agent 按表计费分析: https://www.infoworld.com/article/4171274/anthropic-puts-claude-agents-on-a-meter-across-its-subscriptions.html
[15] DeepSeek V4 Pro 定价文档: https://api-docs.deepseek.com/quick_start/pricing
[16] HuggingFace Papers 趋势榜: https://huggingface.co/papers
