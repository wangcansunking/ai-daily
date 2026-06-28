---
title: "Specsmaxxing：用 YAML 把 vibe coding 拉回工程化"
slug: specsmaxxing-yaml-spec-driven-vibe-coding-cure
date: 2026-05-04
weekday: 星期一
category: AI 编码协作 · 工程方法论
tags: [Specsmaxxing, Spec-Driven Development, acai.sh, feature.yaml, Claude Code, Cursor, Vibe Coding, 通义灵码, GitHub Spec Kit, AWS Kiro]
description: "2026-05-03 凌晨，acai.sh 创始人 Brendan McCarthy（HN 用户名 brendanmc6）在博客上发了一篇《Specsmaxxing》，自承曾陷入 AI psychosis、变成 spec maxxer，最后用一份 feature.yaml 找到「自然语言」与「正式需求语法」之间的甜蜜点，并开源了同名工具链 acai.sh。文章当天上 Hacker News 47994012 顶帖，280 分、289 评论；hansmayer 一句「ARR 是多少」拿到 251 分顶赞；jacquesm 评论「你重新发明了 90 年代初的软件分析师岗位」。文章与 5-3 Apple Support 误打包 Claude.md 事件互为镜像——一边是大公司的工程治理失守，一边是个人开发者的工程化反思。本文复盘 Specsmaxxing 的 4 阶段进化、feature.yaml 真实模板、HN 顶帖正反方辩论，以及国内开发者已经在用的 GitHub Spec Kit / AWS Kiro / 通义灵码 SPEC 模式 / 阿里渐进式 Spec / Comate SPEC 的对位实践。"
cover: specsmaxxing-yaml-spec-driven-vibe-coding-cure.png
---

# Specsmaxxing：用 YAML 把 vibe coding 拉回工程化

![用 YAML 把 vibe coding 拉回工程化](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-04/specsmaxxing-yaml-spec-driven-vibe-coding-cure/specsmaxxing-yaml-spec-driven-vibe-coding-cure.png)

> HN 顶帖 280 分 / 289 评论。acai.sh 作者 Brendan McCarthy 自承「我陷进了 AI psychosis、花数小时写最漂亮的 PRD/TRD、像组建一个 mini dark factory 把 spec 转成代码」，写完之后他给出的解药是一份 feature.yaml——介于自然语言与正式需求语法之间的甜蜜点。这篇文章 2026-05-03 凌晨 06:33 出现在 Hacker News，到当天傍晚就把"spec 派"和"vibe 派"两边的开发者拉进了一场公开辩论。

## 一、48 小时回放：一篇博客如何冲到 HN 顶帖

把节奏摆出来看。

- **05-03 06:33 UTC**：HN 用户 brendanmc6 提交博客《Specsmaxxing – On overcoming AI psychosis, and why I write specs in YAML》到 HN 47994012。
- **05-03 上午（HN 7-9 小时）**：累计 hansmayer 的「ARR is multo？」质疑顶赞 251 分；slopinthebag 留言「居然没有 AI 写作味，我把全文读完了」。
- **05-03 中午**：作者本人 brendanmc6 在评论区与 hansmayer 直接对话，强调"spec 描述目标态而非实现路径"。
- **05-03 傍晚（11 小时）**：jacquesm 的「你重新发明了 90 年代初的软件分析师岗位」拿到关注。
- **05-03 23:00 前后**：帖子稳定在 HN 首页前 5，最终定格在 280 分、289 条评论。

文章和热度本身都不算特别——HN 上每天都有人写"我用 AI 写了 X 个产品"，但《Specsmaxxing》的特别之处是它**用一种很坦诚的方式承认了 AI 编码协作的副作用**，再给出一个能动手用的小工具。这两件事放在一起，正好命中了过去半年开发者社区的两股焦虑：一股是"我写得越来越快但越来越没自信"，一股是"vibe coding 该被工程化收编了"。

而它出现在 5-3 的时间也很巧。前一天晚上 Apple Support v5.13 误把内部 Claude.md 文件打包进 App，HN 47973378 那条帖子 376 分；两件事在同一周冲上首页，一个是大厂治理事件，一个是个人反思 + 解药。读者把它们放在一起讨论，并不奇怪。

![vibe coding 到 spec coding 的 4 个阶段](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-04/specsmaxxing-yaml-spec-driven-vibe-coding-cure/specsmaxxing-vibe-to-spec-4stages.png)

## 二、AI psychosis 是什么：作者亲历的 4 个阶段

文章结构其实很简单——作者把自己用 AI 编码这一年的轨迹拆成 4 段。

**阶段 1：随心写 vibe coding。** 一句话需求扔给 Claude / Cursor，让模型生成、跑、修。小工具、玩具项目、个人脚本——这一档够用。但代码库长到几十个文件、几条业务线交叉、生产环境有真用户之后，"描述一句话"开始失效。

**阶段 2：AI psychosis 上头期。** 作者的原话——

> I fell into an AI psychosis, I became a 'spec maxxer', and I spent hours and hours writing the most beautiful PRDs and TRDs you've ever seen.

这段写得很坦诚。他把症状归纳成一句更具体的描述——"using AI to build AI harnesses for building products, rather than just using AI to build the damn product"。翻译过来：用 AI 建 AI 工具，去做产品，而不是用 AI 直接做产品。文档、模板、agent 角色定义、prompt 库越攒越多，好像生产力工具齐了，但产品没动。

**阶段 3：spec maxxer 重度期。** 作者用了一个比喻——

> I assembled an army, working together like a mini dark factory, to turn my specs into reality.

"暗工厂"是英语社区里一种半玩笑半批判的说法，指那种没人值班、机器自己跑的车间。作者形容自己组了一支 agent 部队，把 spec 转成代码，最重的一次是"agent 跑了 1.5 小时无人监督"。结果不是更高效，而是更焦虑——agent 的输出他得回来逐行审；spec 写得越细，agent 跑得越慢；最严重的是他注意到自己**在打磨工具，没在做产品**。

**阶段 4：feature.yaml 甜蜜点。** 走出来之后他没有完全放弃 spec。继续写，但只写一种格式——feature.yaml。一份文件、几十行、严格但不繁琐。比 markdown 严格（agent 能 grep 编号），比正式需求语法轻（人能 5 分钟读完）。这一份就是文章题目里那个"specsmaxxing"——他自嘲式地保留了这个词，但内涵从"我写最漂亮的 PRD"变成"我写一份够用的 YAML"。

这 4 个阶段不是每个人都会经历完，但很多读者在评论区表示"阶段 2 和阶段 3 我太熟了"。Hacker News 用户 K0balt 说自己独立摸索出了几乎一样的方法，原话是 "Correct, coherent, consistent, commented"——四个 C 的纪律加上 firmware manual + implementation plan + product manual 三件套。这种平行收敛说明 spec-first 不是 acai.sh 一家的发明，而是一年来大量个人开发者被 AI 编码工作流"硌"出来的结论。

## 三、feature.yaml 长什么样：一份真实模板

acai.sh 的核心产出物就是这份 YAML。它由几个段组成——

![feature.yaml 模板：spec 介于自然语言与正式需求语法之间](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-04/specsmaxxing-yaml-spec-driven-vibe-coding-cure/specsmaxxing-feature-yaml-template.png)

```yaml
# acai.sh feature spec 真实模板
feature:
  name: subscription-billing
  product: billing-api
  description: [一句话功能概述，3-5 行内]

components:
  AUTH:
    name: auth-component
    requirements:
      1: 用户必须用 OAuth 2.0 登录后才能订阅
      1-1: OAuth provider 仅支持 Google / GitHub
      1-2: Token 失效后必须强制重定向到登录页
      2: 未登录用户访问订阅页面返回 401

  ENG:
    description: [跨切面工程约束]
    requirements:
      1: 所有金额字段必须用 Decimal 不能用 float
      2: 数据库写入必须走幂等键防重复扣费
      3: API 错误码遵循 RFC 7807 Problem Details

acceptance:
  - AUTH:1-1 通过
  - ENG:2 通过
  - 集成测试覆盖 ≥ 80%
```

四个设计选择值得展开。

**第一，编号 1 / 1-1 / 1-2 是稳定 ID，可以被 grep。** 作者把这套编号叫 ACID，全称 **Acceptance Criteria IDs**（acai.sh 原文术语；不是 "for AI"）。代码里写注释 `// ACID AUTH:1-1`，spec 里改了某条要求时，agent 能反向定位到所有引用这条 ACID 的代码。这是它跟随手 markdown 最关键的差距——markdown 没有稳定锚点，改一个字所有依赖它的代码都要靠人类记忆去找。

**第二，requirements 而不是 implementation。** 文章里反复强调一句——specifications doesn't tell you what to do, they say what the end state should be。spec 描述"做完之后系统是什么样"，至于"该怎么做"留给 agent + 代码库分析。这一条直接回应了 HN 评论区的灵魂质疑（chrisldgk 那条）："why not just write the code yourself?"——作者的回答是：因为难的不是写代码，是把"目标态"定清楚。

**第三，components 是垂直能力切面。** AUTH / ENG / DATA / UI 这种维度不是按用户故事切，而是按系统边界切。比传统 PRD 的"作为用户我希望……"更靠近代码组织方式，agent 拿到这一段能直接定位到对应的代码模块。其中 ENG 是个例外——它不是垂直切面，而是横向的工程约束（比如金额必须 Decimal、必须幂等），跨所有 components 生效。

**第四，acceptance 段是合并前的验收清单。** 作者要求 agent 提交 PR 时必须附一份"acceptance JSON"，记录这次 PR 跑过了哪些 ACID 编号。spec.acai.sh 这个 dashboard 就是用来汇总这份 JSON 的——三种状态（Assigned → Completed → Accepted）让 spec 进度可见。

这四件事加起来，feature.yaml 跟"随手 markdown"的核心差异不在格式，而在**它把 spec 当成代码的同级公民来管**——能 grep、能 ref、能 diff、能进 git。这一点和 GitHub Spec Kit 的设计取向是一致的，下面会讲到。

## 四、HN 顶帖里 280 分的真讨论

HN 47994012 那条帖子的评论区比文章本身更值得读。289 条评论里能看到三派——挺 spec 的、嫌 spec 重的、回到历史维度看的。

![HN 47994012 顶帖正反方辩论](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-04/specsmaxxing-yaml-spec-driven-vibe-coding-cure/specsmaxxing-hn-debate.png)

最尖锐的一条来自 hansmayer，280 分顶赞——

> Let's cut through the noise — what did you build with this very elaborate process and how much ARR is it generating?

翻译过来：撇开热闹不谈，你用这套精致流程到底做出了什么、年化收入是多少？这条质疑很犀利，因为它点中了 spec 党最容易被攻击的地方——流程做得漂亮不等于产品成功。回复链里 rrgok 跟了一句"我也想知道 AI 到底带来了多少 MRR/ARR"。这不是单纯抬杠，是 2026 年开发者社区里一个真实的疲倦——大家看了一年的 AI 工具论文、流程图、最佳实践，但拿出来过实战的成功产品没有那么多。

作者本人 brendanmc6 的回应是把"做什么"和"怎么做"分开。Spec 描述的是商业意图，不是实现路径；ARR 应该问产品本身、产品策略、市场，不是问 spec 流程。这个回应技术上说得通，但社区里有人不买账——chrisldgk 紧跟着一条——

> At this point, why not just write the code yourself? Defining exactly what the product is supposed to do is the hard part, writing code is the easy part.

这一条把矛盾推到极端：如果定清楚做什么是难的部分，那定清楚的过程本身就是大部分工作量，AI 介入的边际价值就小。这条评论的回复有 9 楼之多，说明很多人都在想同一个问题。

历史视角的一条来自 jacquesm——

> You have rediscovered the job of Software Analyst, which until the early 90's was a thing.

这一条很有趣。90 年代之前软件公司里有专门的"软件分析师"岗位，负责把业务需求翻译成形式化文档，再交给程序员去实现。后来需求工程被敏捷拍碎、分析师岗位被产品经理 + 高级工程师吸收。jacquesm 的潜台词是——AI 编码协作的中间层，可能正是把这个被裁掉的分析师岗位以新形态请回来。这跟阿里 "渐进式 Spec" 实战指南里那句 "Spec is Truth、Code is Cheap、Context is Expensive" 是同一个判断的两个版本。

支持派里最让人会心一笑的是 slopinthebag——

> I actually read it all since it did not contain any hints of being AI generated. Thank you for that.

这条评论本身没什么观点，但它点出了 2026 年技术写作的一个新现实：博客文章太多了 AI 味，太少有人愿意完整读完。作者用第一人称写自己的踩坑、不藏脏数据、不假装客观，反而拿到了"我读完了"的最高肯定。这一点对国内技术写作也有借鉴——AI 能写流畅的标准化解读，但读者越来越倾向于用更慢的速度读"有人味的复盘"。

## 五、对位 Apple Claude.md 事件：双幕互补不重叠

再把镜头拉远一点。这周 Hacker News 首页其实出现了两件 AI 编码协作的标志性事件——

5-1 是 Apple Support 应用 v5.13 误把内部 Claude.md 文件打包进 App，11 小时内回收到 v5.13.1，HN 47973378 顶帖 376 分。这是一桩**大厂治理事件**：Apple 内部用 Claude Code 工作流写客服后端代码，但发版流水线没把上下文文件挡在门外。媒体讨论的焦点是"Apple 也 vibe coding 了""CLAUDE.md 该不该进 git"。

5-3 是 brendanmc6 的《Specsmaxxing》，HN 47994012 顶帖 280 分。这是一桩**个人开发者反思事件**：作者承认自己在 AI 编码工作流里走过一段无效路径，用一份 YAML 找到了平衡。

两件事方向相反，但指向同一个判断——**vibe coding 在 2026 年正进入工程化下半场**。Apple 那件事说明大厂内部已经全面切到 AI 辅助开发，但工程纪律没跟上；Specsmaxxing 这件事说明个人开发者正在主动寻找工程化的轻量方案。前者是被动暴露，后者是主动收敛。

这跟过去一年讨论 vibe coding 的氛围有微妙的差别。2025 年大家在问"AI 能不能写出生产级代码"；2026 年大家在问"AI 写出代码之后，工程纪律怎么补回来"。Specsmaxxing 给出的答案是：spec 不是 PRD 那种重型文档，而是一份能进 git、能被 grep、能跟 agent 对齐的轻量 YAML。这个答案不一定是终极答案，但它是一个现实可上手的中间形态。

## 六、国内开发者的对位：Spec Kit / Kiro / 通义灵码 / 渐进式 Spec / Comate

看到这里很多人会想——这套东西国内有没有？答案是有，而且密度不小。**spec-first 工程化是国内开发者本来就擅长的题目**——大厂内部多年用 PRD / 需求评审 / 模板规范，只是过去半年这些工具被 AI 编码协作的潮流推到了更前台。把现在能用的几个工具摆出来对比——

**GitHub Spec Kit**。GitHub 官方的开源 Spec-Driven Development 工具包，支持 24 种以上 coding agent，**截至 2026-05-05 已过 92,000 stars**（gh api repos/github/spec-kit 实测）。它的设计哲学跟 acai.sh 很接近——用结构化文档驱动 agent 实现，spec 与代码同 git 仓库，模板覆盖 GitHub Copilot / Claude Code / Gemini CLI 等主流编码客户端。Spec Kit 的优势是生态广，国内开发者用 GitHub Copilot Pro 或 Claude Code 都能直接接上。

**AWS Kiro**。AWS 在 2025 年中期发布的 agentic IDE，把 spec-driven development 做成 IDE 内的原生工作流，分 Requirements → Design → Tasks 三段式。Kiro 比 Spec Kit 重，但流程更端到端。国内合规层面 Kiro 跑在 AWS Global，如果客户是出海项目可以直接用；如果业务在境内，类似形态的国产替代是百度 Comate 的 SPEC 模式。

**百度 Comate SPEC 模式**。Comate 在 2025 年下半年加了 SPEC 模式：用户提需求时强制 AI 先生成一份 plan 文档，确认后再写代码。文档主要用 markdown，结构上比 acai.sh 更接近 PRD。Comate SPEC 模式的定位偏严格代码质量管控的工程团队，比如核心业务逻辑或 C++ 底层开发——这跟阿里渐进式 Spec 那条"复杂任务才用完整 spec"的判断是同一类思路。

**通义灵码 + 阿里"渐进式 Spec"实战指南**。通义灵码（Lingma）是阿里云 2024 年底发布的 IDE 编码插件，跑在千问（Qwen）系列大模型上。2026-04-13 一篇被广泛转载的《2026 年 AI 编码的"渐进式 Spec"实战指南》（出处带阿里实践背景）给出了三层方案——

- **70% 简单改动**：lightweight rules + 直接对话，不写完整 spec
- **25% 中等复杂度**：加一份 spec.md 文档
- **5% 重度需求**：完整 spec.md + tasks.md + knowledge/ 三层

核心三句口号 "No Spec, No Code"、"Spec is Truth"、"Reverse Sync"（发现 bug 先修文档再修代码）跟 acai.sh 的"spec 是真理"完全同向。差别是阿里这一版用 markdown + 目录结构组织，acai.sh 用单文件 YAML + 编号 ID 组织。两个方案都有自己的取向，但都把 spec 抬到了和代码同级的工程资产。

**飞书项目 / PingCode 这类项目协作工具的角色**。这一档不是 spec-driven 工具，但承担了"团队 spec 同步"的职责。PingCode 的 SPEC 模板、飞书项目的需求文档，能跟 GitHub Spec Kit 或 acai.sh 这种工程层 spec 形成上下游——产品经理在飞书写商业需求、工程团队在 git 里维护 feature.yaml、agent 跑在 IDE 里执行。这种分层组合在国内大厂里已经在跑。

把这些对位摆完一个判断很清楚——**国内开发者不缺 spec-driven 的工具盘子，缺的是统一的格式约定和团队习惯**。GitHub Spec Kit 给了一份开源标准，acai.sh 给了一份更轻的 YAML 模板，阿里的渐进式 Spec 给了一份分级方法论，通义灵码 / Comate / Trae 给了 IDE 内的入口。这些拼起来比海外工具链拼图更全——区别只在于谁先把"团队级标准"推开。

## 七、值得周末花 30 分钟动手的人

文章读完真正实用的问题是——我要不要试？给三类读者三种不同的判断。

**如果你是个人开发者 + 写中等规模副业项目**：值得试。30 分钟成本，建一个 `feature.yaml`，把"下一个要做的小功能"按 components 切面写一份。下一次 agent 跑出来不对的时候，对比 spec 找差距比对比对话历史快。github.com/github/spec-kit 给的模板比 acai.sh 更成熟，可以直接用。

**如果你是国内大厂开发者 + 团队已有 PRD 流程**：值得读，但不一定要切工具。团队已有的 PRD / 需求评审已经承担了 spec 的职责，缺的是 spec → agent 的对齐通道。这种情况建议在现有 PRD 之外加一个简短的 `agent-context.md`（参考阿里渐进式 Spec 的 rules/ 层），把"我的 agent 该怎么读这个 PRD"写清楚，而不是另起一份 feature.yaml 跟 PRD 双轨。

**如果你已经装备了完整 spec maxxing 工具链**：这篇文章不是给你的。作者本人的态度是——别再加文档，把已有的 spec 砍到 30 行，留 ACID 编号和 acceptance 段就够了。这个建议跟阿里渐进式 Spec 那条"70% 任务不需要完整 spec"是同一个意思。

工程化是国内开发者本来就擅长的事——从 SVN 到 git、从 waterfall 到敏捷、从 PRD 到 OKR、从手写 SQL 到 ORM——每一波都是先收一段无序、再立一份共识。Specsmaxxing 这套东西的真意义不是新发明，是给 AI 编码工作流也加上同一份纪律。这件事在国内有 GitHub Spec Kit、有阿里渐进式 Spec、有通义灵码 SPEC 模式撑着，工具不缺；缺的只是有一个周末，挑一个真实小项目，把第一份 feature.yaml 写出来。时代正在打开，一起加油。

---

参考链接：

- 原文：[acai.sh/blog/specsmaxxing](https://acai.sh/blog/specsmaxxing)
- HN 顶帖讨论：[news.ycombinator.com/item?id=47994012](https://news.ycombinator.com/item?id=47994012)
- GitHub Spec Kit 开源仓库：[github.com/github/spec-kit](https://github.com/github/spec-kit)
- 阿里渐进式 Spec 实战指南：[cnblogs.com/running-future/p/19861681](https://www.cnblogs.com/running-future/p/19861681)
- 上一篇互补阅读：本站 2026-05-03 的《Apple Support v5.13 误打包 Claude.md：24 小时撤回》
