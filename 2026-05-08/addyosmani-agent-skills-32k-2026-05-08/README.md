---
title: "Google Director 开源 AI Coding 工程手册：32k⭐ 一周封神"
date: 2026-05-08
slug: addyosmani-agent-skills-32k-2026-05-08
type: deep-dive
track: arbitrage
arbitrage_score: 8.0
domain: ai-coding
cover: addyosmani-agent-skills-32k-2026-05-08.png
description: "Addy Osmani 把一份带 20 个工程 skill、7 条斜杠命令、3 个评审角色的 AI Coding 工作流手册开源，2 月 15 日建仓，到 5 月 8 日 32,728⭐、当日 +3,058，国内尚无深度报道——这次 arbitrage 套利点是把 Google 内部工程文化打包给 Claude Code / Cursor / Gemini CLI 用。"
tags: [ai-coding, agent-skills, claude-code, cursor, addy-osmani, google, open-source, workflow, arbitrage]
---

# Google Director 开源 AI Coding 工程手册：32k⭐ 一周封神

![封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/addyosmani-agent-skills-32k-2026-05-08/addyosmani-agent-skills-32k-2026-05-08.png)

5 月 8 日盘前，`addyosmani/agent-skills` 在 GitHub 累计 32,728⭐、3,791 fork、70 个 open issue，过去 24 小时净增 3,058⭐。仓库 2026 年 2 月 15 日才建立，还不到三个月，速度排在今年 GitHub Trending 总榜前列。它不是又一个新模型、新 IDE 或新 Agent 框架——它是一份把"资深工程师怎么写代码"塞进 markdown、让 AI Coding agent 照着做事的工程手册。20 个 skill、7 条斜杠命令、3 个评审 persona、4 份参考清单，全部 MIT，可以直接在 Claude Code、Cursor、Gemini CLI、Antigravity、Windsurf、OpenCode、Kiro 上跑。这一篇要拆的不是营销话术，而是这份手册的工程意义：在所有人都在卷模型卷 IDE 的 2026 年春，谷歌一位 Director 选择开源他过去十几年攒下的代码评审纪律——这件事比仓库⭐数本身更值得国内 AI 开发者读一遍。

![README 截图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/addyosmani-agent-skills-32k-2026-05-08/addyosmani-agent-skills-readme.png)

## 32,728⭐ 不是热度，是资深工程师对 AI 失控写法的集体共识

数据先摆出来。GitHub API 在 2026-05-08 早晨返回 `stargazers_count: 32728`、`forks_count: 3791`、`open_issues_count: 70`、`subscribers_count: 225`，仓库大小 358 KB，主语言标签 Shell（实际正文 99% 是 markdown）。建仓日期 2026-02-15，最近一次 push 在 2026-05-07 09:33（北京时间），合并的是社区 PR #114——把 plugin.json 里的 skills 字段从字符串改成目录形式，让 Claude Code 能自动发现 skill。

| 指标 | 数值 | 说明 |
|---|---|---|
| Stars | 32,728 | 当日 +3,058 |
| Forks | 3,791 | fork/star 比 ~11.6%，参考线 5% 偏高，社区在真用 |
| Watchers | 225（subscribers）| 长期跟踪者，订阅密度健康 |
| Open Issues | 70 | 维护活跃 |
| Repo size | 358 KB | 全是 markdown，没有 binary |
| Created | 2026-02-15 | 不到 3 个月 |
| License | MIT | 商用无障碍 |

HackerNews 那一头同样能交叉验证。Addy 在自己博客挂了一篇 `Agent Skills` 介绍稿，5 月 4 日发上 HN 后 375 points、211 条评论，前排讨论里有大量自建路由器（`/do` 之类）的 indie 开发者拿出自家方案逐条对比，结论几乎一致："anti-rationalization、progressive context discovery、verification gate 这些点我自己也走到了，他这一版打磨得更干净。"

这是一个值得停一下的信号。不是粉丝在刷 star——是同样在 2025-2026 年自己摸出 AI Coding 工作流的开发者，看到这套手册之后决定换掉自己手写的版本。AI 编码代理在 2024-2025 年的最大问题不是模型不够强，而是默认行为太"省事"——跳 spec、跳测试、把"看起来对"当成"已经对"。`agent-skills` 这套手册的工程价值，就是把 Google 内部那套写代码的纪律——`Software Engineering at Google` 一书里描述的 trunk-based development、code-as-liability、change sizing、Beyoncé Rule、Hyrum's Law、Chesterton's Fence——拆成 agent 能机械执行的 markdown 工作流，强行让模型走完每一步。

![Stars 增长曲线](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/addyosmani-agent-skills-32k-2026-05-08/addyosmani-agent-skills-stars-trend.png)

## 7 条斜杠命令把开发生命周期切成 6 段，每段都有出口

整个手册的入口是 7 条 slash command，对应 `Define → Plan → Build → Verify → Review → Ship` 六个阶段。每条命令进入之后会自动激活若干个 skill，agent 必须按 skill 里写好的 step 推进，跑不完不能进下一阶段。这是 README 顶部那张 ASCII 流程图想表达的核心。

| 阶段 | 命令 | 关键原则 | 典型 skill 触发链 |
|---|---|---|---|
| Define | `/spec` | Spec before code | idea-refine → spec-driven-development |
| Plan | `/plan` | Small, atomic tasks | planning-and-task-breakdown |
| Build | `/build` | One slice at a time | incremental-implementation + context-engineering + source-driven-development |
| Verify | `/test` | Tests are proof | test-driven-development + browser-testing-with-devtools |
| Review | `/review` | Improve code health | code-review-and-quality + security-and-hardening + performance-optimization |
| Simplify | `/code-simplify` | Clarity over cleverness | code-simplification |
| Ship | `/ship` | Faster is safer | shipping-and-launch + git-workflow-and-versioning + ci-cd-and-automation |

把一条命令翻译成业务感受是这样的：你在 Claude Code 里敲 `/spec`，agent 不会上来就写代码，而是把 idea-refine 这一步铺开做发散收敛思考，再让你确认 PRD 的目标、命令、结构、代码风格、测试和边界。你说"行了，开干"，它进 `/plan`，把 PRD 拆成多个有验收标准、有依赖顺序的小任务。然后才允许 `/build`，build 阶段每写一个垂直切片就跑一次测试再 commit。Verify、Review、Ship 都有同样的 hard exit。

中国一线 AI 开发者读到这里大概率会会心一笑——这不就是早就在自己 `.cursorrules` 或 `claude.md` 里手写过几遍、删了又加的那套规矩吗。区别在于：以前每个项目都得自己写一遍，写到一半还会漏掉一两个 verification 步骤；这一份是 Google 这个量级团队的工程师把自己习惯打包出来的，覆盖度和措辞都打磨过。

## 20 个 skill 是一份能直接抄的"资深工程师 checklist 库"

20 个 skill 按六个生命周期阶段分组，每一个都是独立的 markdown 文件，都遵循同一套结构：frontmatter 写名字和触发条件，然后是 Overview / When to Use / Process / Rationalizations / Red Flags / Verification 六段。Rationalizations 这一节最有意思——它是一张"agent 常用的偷懒借口 + 反驳话术"的表，把"我后面再补测试"、"这次改动很小不用拆 PR"、"先跑通再说"这些常见 excuse 提前列好对应的 rebuttal，强行掐掉模型的偷懒路径。

下面把 20 个 skill 按阶段拆开看：

**Define 阶段（2 个）**
- `idea-refine`：发散收敛思考，把模糊概念磨成可执行 proposal
- `spec-driven-development`：写 PRD，覆盖目标、命令、结构、代码风格、测试、边界

**Plan 阶段（1 个）**
- `planning-and-task-breakdown`：把 spec 拆成有验收标准、有依赖顺序的小任务

**Build 阶段（6 个）**
- `incremental-implementation`：垂直切片、feature flag、rollback-friendly
- `test-driven-development`：红绿重构、测试金字塔（80/15/5）、DAMP > DRY、Beyoncé Rule、浏览器测试
- `context-engineering`：rule 文件、上下文打包、MCP 集成——session 起、切任务、输出降质都该过一遍
- `source-driven-development`：每一个框架决策必须基于官方文档，引用、标注未验证项
- `frontend-ui-engineering`：组件架构、设计系统、状态管理、响应式、WCAG 2.1 AA 可访问性
- `api-and-interface-design`：契约优先、Hyrum's Law、One-Version Rule、错误语义、边界校验

**Verify 阶段（2 个）**
- `browser-testing-with-devtools`：Chrome DevTools MCP 取实时运行数据——DOM、console、网络、性能
- `debugging-and-error-recovery`：复现-定位-缩小-修复-加防护五步走，stop-the-line 规则、安全 fallback

**Review 阶段（4 个）**
- `code-review-and-quality`：五轴评审、~100 行 change sizing、Nit/Optional/FYI 严重度、评审速度规范
- `code-simplification`：Chesterton's Fence、Rule of 500，行为不变前提下降复杂度
- `security-and-hardening`：OWASP Top 10、auth 模式、密钥管理、依赖审计、三层边界
- `performance-optimization`：Measure-first、Core Web Vitals 目标、bundle 分析、反模式识别

**Ship 阶段（5 个）**
- `git-workflow-and-versioning`：trunk-based、原子 commit、~100 行 change sizing、commit-as-savepoint
- `ci-cd-and-automation`：Shift Left、Faster is Safer、feature flag、质量门
- `deprecation-and-migration`：code-as-liability、强制/建议下线、migration 模式、僵尸代码清理
- `documentation-and-adrs`：架构决策记录、API 文档、内联文档——文档"why"
- `shipping-and-launch`：上线 checklist、feature flag 生命周期、灰度、回滚、监控

**Meta（1 个，不计入 20 个工程 skill 总数）**
- `using-agent-skills`：怎么用这一整包（meta 类，不计入 20 个工程 skill 总数）

![20 个 skill 一览](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/addyosmani-agent-skills-32k-2026-05-08/addyosmani-agent-skills-skill-list.png)

读完会发现：这一份不是泛用 prompt 集合，而是 opinionated 工作流。比如 `git-workflow-and-versioning` 直接把 change size 卡在 100 行左右，`code-review-and-quality` 把评审速度写成规范，`test-driven-development` 强制 80/15/5 的 unit/integration/E2E 比例。这种程度的具体度，才是这份手册被 AI Coding 圈集体认可的原因。模型默认的"看起来对"在这里没空间——每条 skill 收尾都有 Verification 段，要求拿出测试通过、构建产物、运行时数据这种证据。"Seems right is never sufficient" 是 README 里直接挑明的一句话。

## Addy Osmani 是谁，为什么他的开源会值得读

GitHub 给出的 bio 当前是"Director at Google working on Gemini and Google Cloud"，followers 47,802，公开仓库 355 个。在这之前他在 Chrome 团队带 DevRel 与 Web 性能方向多年，写过 *Learning JavaScript Design Patterns*、*Image Optimization*、长年维护 web.dev 上 Core Web Vitals 那条线。前端开发者读过他的文章是大概率事件——`Yeoman`、`TodoMVC`、`Critical CSS` 这些早期项目都和他有关。2025 年他重心转到 Gemini 与 Google Cloud 的 AI 开发者体验。

这次开源的 `agent-skills`，可以理解成他把过去十几年在 Google 内部走过的代码评审、上线流程、性能 review 那一套实战打包成 markdown 给外部用。README 里他直接点了来源：*Software Engineering at Google* 这本书，加上 Google 公开的 [eng-practices](https://google.github.io/eng-practices/) 指南。Hyrum's Law 写进了 API 设计 skill，Beyoncé Rule（"if you liked it, you should have put a test on it"）写进了测试 skill，Chesterton's Fence 写进了简化 skill，Shift Left 与 feature flag 写进了 CI/CD skill——这些不是抽象原则，全部嵌进 step-by-step 工作流里。

![Addy Osmani](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/addyosmani-agent-skills-32k-2026-05-08/addyosmani-portrait.png)

这件事的份量在哪——一个 Director 级别 Google 工程师把自己团队工作方式开源，意味着两层含义。第一层，谷歌内部对 AI Coding 工程纪律的理解被外部直接看到，国内开发者不需要再隔着翻译过来的 SWE@Google 单行书摘去揣摩。第二层，这种纪律可以机器执行——skill 是给 agent 读的，不是给人读的；放进 Claude Code 之后，模型每写一段代码都被这套手册兜底。AI Coding 这件事从"个人 prompt 黑话"走向"团队工程标准"，这一份是当下最具代表性的尝试。

## skill 与 .cursorrules、context、rule 的本质区别

社区里有一个常见困惑：这跟 `.cursorrules`、`CLAUDE.md`、Cursor 的 rules、Claude Code 的 plugin 到底什么关系。直接对比一张表：

| 维度 | .cursorrules / CLAUDE.md | rule（Cursor / Claude Code） | skill（agent-skills） |
|---|---|---|---|
| 形态 | 单文件全局规则 | 触发式短规则集 | 结构化工作流 markdown |
| 颗粒度 | 项目级 | 场景级（如"写 React 组件时"） | 任务阶段级（spec / plan / build…） |
| 触发方式 | session 全程持续 | 关键词或文件类型触发 | 命令或上下文自动激活 |
| 验证机制 | 无强制 | 弱（靠模型自觉） | 强（每个 skill 收尾有 Verification 段） |
| Anti-rationalization | 无 | 无 | 有（Rationalizations 表） |
| 适配工具 | 单工具 | 单工具 | 跨工具：Claude Code / Cursor / Gemini CLI / Windsurf / OpenCode / Kiro / Copilot |

![skill / rule / context 对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/addyosmani-agent-skills-32k-2026-05-08/skill-vs-rule-vs-context.png)

最关键差异在于"过程式"而不是"陈述式"。`.cursorrules` 写的是"项目用 TypeScript、不要写 any、commit 用 conventional commits"——一组陈述。skill 写的是"现在你要进入 Build 阶段，第一步先写测试，第二步实现最小切片，第三步跑测试，跑过才能 commit；如果模型想跳第一步，对照 Rationalizations 表给出反驳"——一段过程。陈述式规则模型容易"知道但不做"，过程式工作流模型必须按 step 走完。

这一点对国产 AI IDE 也有现实意义。腾讯 CodeBuddy、字节 MarsCode、阿里灵犀、字节 Trae、各家自研 Cursor 类产品 2025-2026 年都在补 rule 系统——大多数还停在"陈述式"阶段。`agent-skills` 把"过程式工作流"这一层证明可以跑通，国产工具要不要补，怎么补，怎么和已经存在的中文工程文化（比如阿里 P7 标准、华为 IPD）结合，是接下来六个月的真问题。

## 跨工具适配矩阵：Claude Code 是 first-class，其余都能接

README 里专门列了一节 Quick Start，覆盖 7 种 agent 客户端的安装方式，能力覆盖度差异不小。

| 客户端 | 安装方式 | 自动激活 skill | 斜杠命令 | 上手难度 |
|---|---|---|---|---|
| Claude Code | `/plugin marketplace add` 一行装 | 是 | 7 条全有 | 极低 |
| Cursor | 复制 SKILL.md 进 `.cursor/rules/` | 否（手动引用） | 否 | 中 |
| Gemini CLI | `gemini skills install` 或 `GEMINI.md` | 是 | 部分 | 低 |
| Windsurf | 加进 Windsurf rules 配置 | 否 | 否 | 中 |
| OpenCode | 通过 AGENTS.md + skill 工具 agent-driven | 是 | 否 | 中 |
| GitHub Copilot | 用 `agents/` 当 persona + `.github/copilot-instructions.md` | 否 | 否 | 中 |
| Kiro IDE & CLI | `.kiro/skills/` 目录，project / global 都行 | 是 | 否 | 低 |
| Codex / 其他 | 把 markdown 当 system prompt | 视客户端 | 否 | 高 |

![不同客户端覆盖矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/addyosmani-agent-skills-32k-2026-05-08/claude-code-vs-cursor-vs-antigravity-coverage.png)

仓库 topic 里挂了 `antigravity` 和 `antigravity-ide`——Antigravity 是 Google 自家在内部测试的下一代 AI IDE，Addy 提前埋了适配点。Claude Code 是 first-class，因为 Anthropic 自己 2025 年 10 月发布的 Skills 协议是这套手册技术原型的源头之一。Cursor 和 Windsurf 因为没有原生 skill 概念，只能"复制粘贴 SKILL.md 当 rule"，把过程式手册降级为陈述式规则——能用但损失了 anti-rationalization 这一层。

中国开发者最常用的几个国产工具里，**腾讯 CodeBuddy / 字节 Trae** 已经原生支持类似 rule 系统，理论上可以直接吃这一份手册的 markdown，但需要自己补一层"工作流推进"的状态机。**通义灵码（Lingma）** 目前规则系统更轻，吃的是 prompt template；**华为 CodeArts** 走的是企业级 IDE 路线，规则定制走的是配置而非 markdown。这三家如果想跟上这一波，最低成本是直接 fork `addyosmani/agent-skills`，把 SKILL.md 翻译成中文之后做产品内置——MIT license 不卡这件事。

## 国内能立刻吃到的部分：直接复制工作流，按需汉化

中国 AI 开发者眼下能立刻吃到的有三层。

- **工具层**：跑 Claude Code、Cursor、Gemini CLI 的本地 dev 环境拿到斜杠命令和 20 个 skill，立刻有产能提升
- **抽象层**：20 个 skill 那张分类表本身就是一份"AI Coding 工程清单"，不用 agent 也能当人脑 checklist 贴到团队 wiki
- **工程文化层**：Anti-rationalization 表、Verification 强制、change sizing 100 行这些规则本身就是一线工程团队用得上的纪律，和模型无关

| 层 | 立刻可用 | 需要二次开发 | 中长期 |
|---|---|---|---|
| 工具层 | Claude Code / Gemini CLI 直装 | Cursor / Windsurf 转 rule | 国产 IDE 内置 |
| 工作流层 | 斜杠命令 + skill 全流程 | 20 个 skill 中文化 | 适配 P7/IPD 等本土工程文化 |
| 文化层 | anti-rationalization 表 | Verification gate 落地 | 团队评审标准 |

值得点出来的是：这份手册没有任何中国开发者天然受限的部分。MIT 协议、纯 markdown、不依赖任何特定模型、不调外部 API、不绑死单一 IDE。这种"纯文本工程资产"的传播速度在国内只会比海外快——只要有人做完整中文翻译、补一份针对国内主流 IDE（CodeBuddy / Trae / 通义灵码 / CodeArts）的适配指南，就能在一两周内成为国内 AI Coding 圈的下一个标准引用。

## 真正的工程意义：让"过程"重新比"prompt"值钱

把视角拉远一点。2024 全年 AI Coding 圈最热的资产是"prompt 模板"，2025 年是"rule / context 文件"，到 2026 年这一波 `agent-skills` 已经把答案推到了第三层——**过程式工作流**。模型能力进入边际收益期之后，差异化不再来自"我写了个更妙的 prompt"，而来自"我让模型按工程纪律走完每一步"。这是一份非常工程的开源——它不炫技、不堆 demo，全部内容是给 AI 写的 step-by-step 操作手册，每一段都假设模型会偷懒并提前堵死偷懒路径。

国内一线 AI 开发者其实早就走到了这一步。过去半年身边的工程团队都在"自己写 rule、自己写 context、自己写流程"，每家方案都长得像但又不完全一样。`addyosmani/agent-skills` 的价值不在于"全新发明"，而在于把这件事用 Google 工程文化的标尺统一了一遍——读一遍 20 个 skill 的目录，自己手写的那一版哪里漏、哪里偏、哪里多余，立刻能看出来。

32,728⭐ 不会停在这里。一份能被 Claude Code 直接 `/plugin install`、能被 Cursor `.cursor/rules/` 直接引用、能被 Gemini CLI `skills install` 装进去的工程纪律包，正在变成这一代 AI 开发者的共享底座。下一个值得追的问题是：国产 AI IDE 谁先把这一层做成 first-class，谁就能在 2026 年的开发者心智里抢到一席。
