---
title: "Claude Code 三件套落地：Routines / 远程控制 / Code Review，国内 AI Coding 用户该怎么看"
date: 2026-05-07
slug: claude-code-routines-remote-2026-05-07
type: deep-dive
track: overseas-hot
cover: claude-code-routines-remote-2026-05-07.png
description: "Claude Code 客户端这一线在 2026 年 5 月 6 日 Code w/ Claude 大会上集齐三件套——Routines（云端定时任务）、Remote Control（手机驱动本地 session）、Code Review（多 agent 评审 PR）。Boris Cherny 现场放话过去 7 个月没手写过一行代码、最高单日 150 PR，全部从手机出。本文拆解三大功能的真实形态、Cursor / Cline / Aider 同期对照、国内 Pro 套餐与镜像可用路径。"
tags:
  - Claude Code
  - Routines
  - Remote Control
  - Code Review
  - AI Coding
  - Cursor
  - 国内可用
---
# Claude Code 三件套落地：Routines / 远程控制 / Code Review，国内 AI Coding 用户该怎么看

![Claude Code 三件套落地：Routines / 远程控制 / Code Review](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/claude-code-routines-remote-2026-05-07/claude-code-routines-remote-2026-05-07.png)

## 一、Boris Cherny 在台上说：过去 7 个月我没手写过一行代码

5 月 6 日旧金山 Code w/ Claude 2026 大会，Claude Code 创建者 Boris Cherny 上台时说了一段话，台下做笔记的人——包括 Simon Willison——把它原样记进了 live blog：

> 我现在主力在手机的 Claude app 里干活。一个普通的工作日是「几十个」PR，最高纪录单日 150 个。我有 5 到 10 个 session 同时跑，能 spawn「几百个 agent」。从去年 10 月开始，我就再没手写过一行代码。

支撑这段话的不是一项功能，是一**整套**功能。最关键的三件落地齐了：

| 功能 | 一句话定义 | 上线节点 | 套餐 |
|---|---|---|---|
| **Routines** | 云端 Claude Code，能挂定时 / GitHub webhook / API 触发 | 2026-04-14 research preview | Pro / Max / Team / Enterprise |
| **Remote Control** | 手机或浏览器驱动你本地正在跑的 Claude Code session | 2026-02-25 research preview | 全套餐（Team/Enterprise 需管理员开） |
| **Code Review** | 一组 agent 在每个 PR 上跑深度评审，行内评论 + 总结 | 2026-03-09 research preview | Team / Enterprise |

这三件单看都不算惊天，但**叠在一起**，就是 Boris 那种「主力工具是手机、笔记本只在睡觉时跑活」的工作流的物理基础。Anthropic 这条客户端产品线，从去年 9 月「一个内部小工具」起步，到现在跑出一个完整的 AI Coding agent 操作系统骨架——只用了 8 个月。

> 论点先放这里：**Claude Code 三件套把「IDE 侧 AI 助手」推到了下一阶段——AI Coding 不再是『敲代码时身边坐个 AI』，而是『随时随地把活派出去 / 醒来看结果』**。这条路线对国内 AI Coding 用户最直接的影响有两条：（1）Cursor 早就在做的 Background Agents、Bugbot 这些功能现在 Anthropic 都自己上了，Pro 套餐 20 美金月费就能用，国内通过镜像 / API 中转能跑起来；（2）Routines 那种云端 7×24 异步运行模式，对长跑、对 GitHub workflow 触发的活路，确实是新的工作方式，国产 AI Coding 工具（通义灵码 / 字节 Trae / 智谱 GLM CODING / 腾讯 CodeBuddy）距离这个能力组合还有半代差距，下半年看谁先补齐。

![Claude Code Routines 官方介绍图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/claude-code-routines-remote-2026-05-07/claude-code-routines-blog-hero.png)

## 二、Routines：云端的 Claude Code，「拍下任务，醒来看 PR」

### 2.1 一句话规格

Routines 是一份**保存好的 Claude Code 配置**——一个 prompt、一个或多个 repo、一组 connectors（GitHub / Linear / Slack / 自定义 MCP）——可以在三种条件下触发：

- **Schedule**：cron 风格的定时任务（每小时 / 每晚 / 每周）
- **Webhook**：GitHub 事件触发（push / PR opened / comment）
- **API**：每条 routine 拿一个独立的 endpoint + token，curl 一下就能跑起来

跑在哪里？**Anthropic 自己的云**——和 Claude Code on the web 同一套 sandbox，所以不依赖你的笔记本。

### 2.2 官方放出来的真实例子

挑几个比较硬的（来自 Anthropic 官博 4-14 公告）：

- **Schedule 用法**：「每天凌晨 2 点，从 Linear 拉最高优 bug，尝试修，开 draft PR」
- **Schedule 用法**：「每周一扫文档与代码漂移，自动开补丁 PR」
- **GitHub 触发**：「任何 PR 改了 `/auth-provider` 模块就总结一遍 + 发到 #auth-changes Slack 频道」
- **API 触发**：「部署后 webhook 调一下 routine，做冒烟检查 + 扫 error log」
- **GitHub 触发**：「Python SDK 改动同步 port 到 Go SDK」

Boris 自己的版本更狠：一堆 routine 串起来，CI 出错有 sub-agent 自动修，rebase 冲突有 sub-agent 自动处理，flaky test 有 sub-agent 收集分类，反馈每 30 分钟聚类一次——他在台上说自己同时跑「几百个 agent」时，描述的就是这套结构。

### 2.3 套餐 quota（这块最容易被误读）

| 套餐 | 每天 routine 跑次配额 |
|---|---|
| Pro（20 美元/月） | 5 次/天 |
| Max（100 / 200 美元/月） | 15 次/天 |
| Team / Enterprise | 25 次/天 |

超出额度可以走 extra usage 计费。每次 routine 跑都吃和交互式 session 同一个限额池——也就是说，**routine 不是免费白嫖，本质是把你 5 小时窗口里的 token 配额挪一部分给「无人驾驶」用**。

### 2.4 HN 上的反应：上瘾派 vs 警惕派

HN [Claude Code Routines 帖（id 47768133）](https://news.ycombinator.com/item?id=47768133)的两条主线：

**上瘾派**：jann 这类用户最直接，「我从 memory 这种简单功能里就拿到了价值，长期项目规划那种活，能记住上下文太香」。Boris 那种 150 PR/天的极限场景，也确实有少数 power user 在复刻。

**警惕派**：被顶得最高的是 joshstrange——「我要的是 commodity，不是 platform」。担心一旦 Anthropic 哪天「变质」，迁出去成本极高。ElFitz 进一步给案例——「我已经离开 Claude Code 了，发现自己工作流绑定它专属功能太深，memory 又不进 git repo」。redanddead 更尖锐——「他们每天加一个 feature flag 把模型变笨」。

这两派的分歧本质是：**你愿不愿意把『让 AI 帮你写活路』这件事，交给一个跑在别人云上的黑盒**。这个判断和当年「我要不要把 CI 从 Jenkins 搬到 GitHub Actions」是同一类决策——便利极强，绑定极深。

## 三、Remote Control：手机里那个绿点，让笔记本变成你的家用服务器

### 3.1 核心动作：本地 session 让远程 client 接管

Remote Control 不在 Anthropic 云上跑——这点和 Routines 是反着的。你本地 `claude remote-control` 一跑（或者 `claude --remote-control` 起一个交互 session），它就**只发出站 HTTPS**到 Anthropic API 注册一下，然后 poll 着等远程指令。

![Simon Willison 的 Remote Control 实测截图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/claude-code-routines-remote-2026-05-07/claude-code-remote-control-simonw.jpg)

你打开手机 Claude app（iOS / Android）或浏览器开 [claude.ai/code](https://claude.ai/code)，扫一下二维码或者点 session 列表里那个带绿点的电脑图标，**手机和本地 session 就连上了**。`@` 触发本地文件系统补全，本地 MCP server 全部可用，本地 git 状态本地配置一切原样——但**输入框在你手机上**。

技术形态非常老实：

- 没有任何入站端口被打开
- 流量全走 Anthropic API over TLS
- 多个短期 credential，每个 scope 单一独立过期
- 笔记本睡眠 / 网络断 → 醒来自动重连
- 只有网络断超过 10 分钟，session 才会被收走

### 3.2 三种启动姿态

| 姿态 | 命令 | 用途 |
|---|---|---|
| Server mode | `claude remote-control` | 起一个长跑 server，`--capacity 32` 默认支持 32 个并发 session（多手机/多浏览器场景） |
| 交互式 | `claude --remote-control` 或 `--rc` | 你在本地终端正常用，同时手机也能接管 |
| 既存 session 转远程 | `/remote-control` | 已经在跑的 session，加一句命令就直接挂到云端列表里 |

VS Code 扩展里也有 `/remote-control` 命令，2.1.79 起支持，banner 直接显示连接状态。

### 3.3 一个被低估的小功能：`/mobile`

在 Claude Code 里敲 `/mobile`，会弹一个二维码——iOS 和 Android 客户端的下载入口。配合 `Push when Claude decides` 设置项，**Claude 觉得这事儿需要你拍板时，主动给你手机推一条 notification**。例如：「测试跑完了」「这个改动要不要继续」。Simon Willison 上次发的 vampire-remote 那张梗图就是这个场景——你出门遛弯，手机震一下，Claude 在等你确认是不是要 merge。

### 3.4 Boris 那「150 PR 单日」的工作流是怎么回事

把上面拼起来：

1. 出门前，本地起 `claude remote-control --spawn worktree --capacity 32`——server mode，每个 session 自动开独立 git worktree，避免文件冲突
2. 同时配几个 Routines——夜里跑长任务、bug 修复、文档同步
3. 手机 app `Code` tab 里，5 到 10 个 session 同时挂着；Routines 拉的工作进度也显示在同一个列表
4. Routines 跑完一轮、agent 卡在抉择点，手机 push 一震
5. 在地铁里、咖啡馆里、家里沙发上，回一句「ok 继续」「换个方向」「这块再加一个 case」
6. Code Review 那帮 agent 在 GitHub 那边自动跑——人不在评审环节卡瓶颈

整套下来，**「等 AI 干活」 → 「派活给 AI 然后去做别的」** 的转化是质变。Boris 一天 150 PR 的极限值不必复刻，但「主力交互界面是手机」这件事，从这套基础设施落地起，已经在小范围内成立。

## 四、Code Review：每个 PR 都派一队 agent 去抓 bug

### 4.1 不是一个 agent，是一队

Anthropic 这次的 Code Review 不是「Copilot Review 那种行内 suggestion」，是一组**专门角色分工的 agent**并行跑：逻辑错误、边界条件、API 误用、认证漏洞、项目惯例符合度——每个 agent 只盯一类。

![Claude Code Review 官方头图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/claude-code-routines-remote-2026-05-07/claude-code-review-blog-og.jpg)

跑完之后还有一个**verification 步骤**——拿候选发现去对照实际代码行为，把 false positive 滤掉，然后按严重度排序，作为 GitHub inline comment + 一条 summary 总结贴出来。

### 4.2 Anthropic 自己的真实数据

这是发布会上最让人停下来记笔记的一段：

| 指标 | 用 Code Review 之前 | 用了之后 |
|---|---|---|
| PR 拿到实质评审意见的比例 | 16% | 54% |
| 单工程师代码产出（同比） | 基线 | +200% |
| 大 PR（>1000 行）有 finding 的比例 | — | 84%，平均 7.5 条 |
| 小 PR（<50 行）有 finding 的比例 | — | 31%，平均 0.5 条 |
| finding 被 reviewer 标记为「错」的比例 | — | <1% |
| 单次 review wall time | — | 平均 20 分钟 |

Boris 在 X 上配的那条原文（[bcherny/2031089411820228645](https://x.com/bcherny/status/2031089411820228645)）说得直接——「**Anthropic 工程师今年代码产出翻倍，瓶颈就是评审**」。Code Review 这条产品线本质是给自己解决问题，顺手开放出来。

Cat Wu 也补了一句，原话是「我们听到你们说想少花点时间在评审上」——这话翻译过来就是：评审是 AI 写代码时代里最大的人类瓶颈，谁先把这条卡点松开，谁就能让团队代码产出真正吃满模型那一头。

### 4.3 钱的问题

| 工具 | 单次 review 价格 |
|---|---|
| GitHub Copilot Code Review | ~4 美分 |
| Greptile | 30 美元/月含 50 次 |
| **Claude Code Review** | **平均 15-25 美元** |

差了**两到三个数量级**。HN [Code Review 顶贴（47313787）](https://news.ycombinator.com/item?id=47313787) 上有人算过账——「2000 PR/周 × 一年，要 150-250 万美元」。但也有 raflueder 跑过两轮 review、200+ PR 共 19.5 美元的实际数据，说明**实际单价远低于公告均值**——Anthropic 那个「平均 15-25」算的是大 PR 主导的场景。

辩护派的理由也很直接：**senior 工程师小时价 100 美元起，省 1 小时评审值 $100**，AI 开 review 20 分钟跑完成 $20，账面上当然合算。但前提是 AI 找到的 8 条意见里有真东西——HN 上反对最强的论点就是「不管什么 PR 都给你刷出 8 条 finding，凑数嫌疑」。

### 4.4 接入方式

简单到几乎反高潮：

1. 管理员在 Claude Code admin 设置里打开 Code Review
2. 装 Anthropic 的 GitHub App，授权要审的 repo
3. 之后所有新 PR 自动跑

可以加一个 `CLAUDE.md` 或 `REVIEW.md` 在 repo 根目录调教 Claude 的关注点——例如「这个 repo 用 Tailwind v4，不要再让我用 @apply」「认证这块用我们封装的 auth helper，别直接写 cookie」。

只在 **Team / Enterprise 套餐** 上开放，月度有 spend cap 可设、有 repo 级开关、有 dashboard 看接受率——admin 控制做到位了，避免一夜烧光预算。

## 五、横向对照：Cursor 已经做了哪些 / Aider / Cline 在哪条路上

国内做 AI Coding 的同行经常在公司群里有个争论：「Claude Code 这套，Cursor 是不是早就有了？」

部分对。三件功能逐一对照：

### 5.1 Routines vs Cursor Background Agents

Cursor 的 Background Agents 比 Anthropic 上线更早（2025 年中即推出），架构差不多——云端 VM 跑 agent，schedule / webhook / 外部事件触发都支持，**Cursor Bugbot** 也已经做了「自动评审 + 修 PR」一条龙。

差别在**配额定价模型**和**生态完整度**：

- Cursor 用 credit 制，跑 agent 烧得快，重模型更狠
- Claude Code 用 5 小时 rolling rate limit + 周上限，Pro 200 美元/月可以**几乎按需 overflow**
- Cursor 的 Bugbot 单独订阅，Claude Code 的 Code Review 跟在 Team 套餐里
- Cursor 强在 IDE-first（你驱动，AI 跟），Claude Code 强在 agent-first（AI 驱动，你 review）

### 5.2 Remote Control：Cursor 这边没有同等品

这是 Anthropic 的差异点。Cursor 也有 Cursor Mobile，但**它跑的是云端 sandbox**，不是远程接管你本地 IDE 状态。你本地的 MCP / 本地 git 工作树 / 本地配置那一堆东西，Cursor Mobile 接不到。

也就是说——**Boris 那种「本地起 server，手机就近驱动」的玩法，Claude Code 是目前唯一能给到的**。Aider 和 Cline 这种本地 CLI 工具，从架构上更没有这个能力。

### 5.3 Code Review：CodeRabbit / Greptile / Cursor Bugbot 和 Claude Code 的对位

| 维度 | Claude Code Review | CodeRabbit | Cursor Bugbot | GitHub Copilot Review |
|---|---|---|---|---|
| 单次成本 | 15-25 美元 | 数美元 | 包含在 Cursor Pro 里 | ~4 美分 |
| 多 agent 验证 | ✓（专门 verification 步骤） | ✗ | ~ | ✗ |
| 上下文模型 | Opus 4.7 | 自有微调 | Cursor 自家 | GPT-4 系 |
| 重 PR 大小 | 越大越深越准 | 中等 | 中等 | 浅层 |
| **定位** | 高价深度评审 | 中端常规评审 | IDE 一体化 | 入门级 sanity check |

逻辑很清楚——这是个**高价深度服务**，不是「便宜替代人类」。

国产工具这条线：通义灵码、智谱 GLM CODING、字节 Trae、腾讯 CodeBuddy 现阶段都还在「IDE 内 AI 助手」阶段——智谱 4 月放出 GLM CODING PLAN 套餐（18 元/月起），更像是把 Claude Code 那种 CLI 体验包给国内开发者，**Routines / Remote Control / Code Review 这种『云端 + 异步 + 多 agent』组合还没补齐**。

下半年值得看的几个国产动作：

- 智谱有没有把 GLM CODING 升级成「云端长跑 agent」
- 字节 Trae 会不会和飞书 + Lark 平台合体做远程控制
- 阿里云通义灵码会不会借百炼 agent 平台底座补上 Routines 这一块
- 腾讯 CodeBuddy 在企业 Code Review 这条线上要不要切入

## 六、国内能用吗？三条真实可用的路径

这是国内读者最关心的部分。先说结论：**官方 + 镜像 + API 中转，三条路都通**。

### 6.1 官方订阅（Pro 20 美元 / Max 100 / Max 200）

**怎么付**：海外信用卡（Visa / Mastercard）+ 海外 IP（住宅代理或 VPS 转发）。

**怎么装**：

```bash
# 用国内 npm 镜像加速
npm config set registry https://registry.npmmirror.com
npm install -g @anthropic-ai/claude-code
claude
# /login 走 claude.ai OAuth，需要海外 IP
```

**Routines / Remote Control / Code Review 都直接可用**——这是最干净的路径。

### 6.2 镜像服务（13-130 元/月档位）

银河录像局、Code80 这一类镜像服务做的事情：把 claude.ai 的登录 / 订阅 / API 调用做成一个国内可访问的代理端点。

- 镜像试用日卡 ~14 元、月卡 ~130 元
- 不用 VPN、不用海外卡
- 适合「先试试 Claude Code，再决定是不是上官方」的开发者
- **限制**：Routines、Remote Control 这种深度依赖 claude.ai 账户体系的功能，不同镜像服务支持程度不一，需要单独咨询

### 6.3 API 中转 + 国产模型替代

智谱 4 月推出的 **GLM CODING PLAN**（18 元/月起）是国产里跑得最直接的——它把自家 GLM 模型适配成 Claude Code 兼容的 API，换一个 endpoint、把 `ANTHROPIC_BASE_URL` 指过去就能用。

```bash
export ANTHROPIC_BASE_URL=https://...glm-coding-endpoint...
export ANTHROPIC_API_KEY=glm-...
claude
```

代价：模型不是 Opus 4.7，是 GLM 系；Routines / Remote Control / Code Review 这种深度耦合 Anthropic 账户体系的功能不可用——但**核心的「CLI 里跑一个 AI 编程 agent」体验是有的**。对预算敏感、或者要在企业内网用、或者纯粹想试试感觉的开发者，是合理选择。

### 6.4 一张速查表

| 你是 | 推荐路径 |
|---|---|
| Claude Code 粉，要全部新功能 | 海外信用卡 + 官方 Pro / Max |
| 想试试不想折腾 | 国内镜像月卡 130 元 |
| 公司内网 / 国内独立合规 | 智谱 GLM CODING PLAN 套餐 |
| 完全本地、不想云依赖 | OpenCode + Qwen3-Coder / DeepSeek-V3.2 自建 |

## 七、客观对比段：Cursor / Cline / Aider / 国产工具

| 工具 | 路线 | 三件套对应 | 国内可用度 |
|---|---|---|---|
| **Claude Code** | agent-first CLI | Routines + Remote + Code Review 全套 | 中（需镜像或海外卡） |
| **Cursor** | IDE-first | Background Agents + Bugbot；无 Remote Control 同等品 | 中（同上） |
| **Cline** | VS Code 扩展 + 本地 | 无云端 routines；可接任意 OpenAI 兼容端点 | 高（接国产 API 友好） |
| **Aider** | 纯 CLI，git-native | 无；定位是「最小 AI pair programmer」 | 高 |
| **OpenCode** | 开源 CLI fork | 跟 Claude Code 形态接近，无云端绑定 | 高 |
| **通义灵码** | IDE 插件 | 无云端异步；强在阿里系生态联动 | 高（国内首选之一） |
| **字节 Trae** | IDE + agent | Trae Agent 在做异步任务方向 | 高 |
| **智谱 GLM CODING** | Claude Code 兼容套壳 | 跑 GLM 模型 + 兼容 Claude Code CLI 体验 | 高（最便宜的「类 Claude Code」体验） |
| **腾讯 CodeBuddy** | IDE 插件 + 企业评审 | 强在企业 Code Review 接入侧 | 高 |

这张表里没有「谁完胜」——每条路线服务的开发者画像不一样。**Claude Code 的护城河是 Anthropic 自家模型 × CLI agent 框架 × 完整三件套**，国产工具的护城河是**国内合规 / 价格友好 / 生态联动**——两条线短期不会重叠，但下半年国产工具补齐云端 routines 能力是大概率事件。

## 八、几个真实风险，不必回避

新功能的好话要说，问题也得讲清楚：

**1. Routines 锁定问题真实存在**——HN 上 joshstrange 的担心不是空喊。Routines 配置一旦做厚，迁出去成本很高。建议把核心 prompt 和 routine 定义本地存一份（git tracked），别完全依赖 web UI。

**2. Code Review 单价对中型团队是真贵**——15-25 美元/PR 的均值，对一周 50+ PR 的中型 startup 是月烧 3000+ 美元。先小范围试，开 spend cap，盯 dashboard。

**3. Remote Control 安全模型可信但用户要懂**——它**不是**云端跑代码，是 client 远程操作你本地 session。如果你笔记本上有敏感凭据，远程 client 操作的边界要心里有数。Anthropic 的 sandbox flag（`--sandbox`）是逃生通道。

**4. Pro 套餐 Routines 配额（5/天）确实紧**——5 次/天的 quota 适合「一天跑一两个 nightly job」的轻度用户；Boris 那种几十 routine 并发跑的极限场景，至少要 Max。

**5. 国内用户「可用 ≠ 稳定」**——镜像服务质量良莠不齐，OAuth 链路偶尔抽风，建议同时备一个 API 中转 fallback（智谱 GLM CODING 是稳定备选）。

## 九、写在最后

去年的 Claude Code 是「能用 CLI 跑一个 AI 帮你 commit」，今年的 Claude Code 是「能挂在云上 7×24 替你做活、能从手机上随时驱动、能让一队 AI 帮你 review 完所有 PR」。

8 个月里推完这三件，从产品节奏看，Anthropic 已经不只是模型公司——他们正在把「AI Coding 工程师的工作环境」整个端走。

国内这边，智谱 4 月推的 GLM CODING PLAN 已经在补 CLI 体验那一格；通义灵码和字节 Trae 也在往 agent 方向走。Boris 在台上说的「我已经 7 个月没手写代码」是单点极端，但**「派活给 AI、去做别的」**这种工作方式，能稳稳出现在 2026 年下半年的国内开发者里。

![Boris Cherny 在手机上跑 Claude Code](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-07/claude-code-routines-remote-2026-05-07/boris-150-prs-phone.jpg)

下一篇值得追的，应该是 5 月 6 日大会那一批 Anthropic 演讲人现场讲的细节——Cat Wu 那条「Sonnet 3.7 时代用户就在让 Claude 直接动生产数据库」放在 keynote 里没展开，但配合 Routines + Code Review，真实的「unsupervised AI 改生产代码」场景已经接近门口了。这个节奏下，国内 AI Coding 工具今年下半年的产品规划，估计要全部重排一遍。

## 参考资料

- Anthropic 官博：[Introducing routines in Claude Code（2026-04-14）](https://claude.com/blog/introducing-routines-in-claude-code)
- Anthropic 官博：[Code Review for Claude Code（2026-03-09）](https://claude.com/blog/code-review)
- Claude Code 官方文档：[Continue local sessions from any device with Remote Control](https://code.claude.com/docs/en/remote-control)
- Simon Willison live blog：[Code w/ Claude 2026](https://simonwillison.net/2026/May/6/code-w-claude-2026/)
- Simon Willison：[Claude Code Remote Control（2026-02-25）](https://simonwillison.net/2026/Feb/25/claude-code-remote-control/)
- Boris Cherny 在 X：[Code Review 公告原推](https://x.com/bcherny/status/2031089411820228645)
- BigGo 报道：[Anthropic Engineer Writes 150 PRs a Day From His Phone](https://finance.biggo.com/news/bd63c89c5a3716f3)
- HN 顶贴：[Claude Code Routines](https://news.ycombinator.com/item?id=47768133)
- HN 顶贴：[Code Review for Claude Code](https://news.ycombinator.com/item?id=47313787)
- TechCrunch：[Anthropic launches code review tool to check flood of AI-generated code（2026-03-09）](https://techcrunch.com/2026/03/09/anthropic-launches-code-review-tool-to-check-flood-of-ai-generated-code/)
- 知乎指南：[2026 年国内使用 Claude Code 完整指南](https://zhuanlan.zhihu.com/p/2010857535318557268)
