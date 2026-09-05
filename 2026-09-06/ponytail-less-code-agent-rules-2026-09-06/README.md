---
title: Ponytail 给 Coding Agent 装上删代码本能
date: 2026-09-06
slug: ponytail-less-code-agent-rules-2026-09-06
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/ponytail-less-code-agent-rules-2026-09-06.png
description: Ponytail 把 YAGNI、stdlib-first 与 native-first 写成跨 Coding Agent 的规则和技能，核心价值不是又多一套 Skills，而是让 agent 在动手前先证明新代码确有必要。
tags: [Ponytail, Coding Agent, YAGNI, 标准库, 原生能力, Agent Rules]
weekday: 星期日
category: AI Coding · 工程方法
---

# Ponytail 给 Coding Agent 装上删代码本能

![Ponytail 专题封面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/ponytail-less-code-agent-rules-2026-09-06.png)

一个日期选择器，Claude Code 在无规则组里平均加了 404 行；启用 Ponytail 后是 23 行。变化并非模型突然会“代码高尔夫”，而是它先问了一句：浏览器原生的 `<input type="date">` 是否已经够用？

**Ponytail 真正有用的地方，不是给 Skills 阵营再添一个名字，而是把“不要写”变成 Coding Agent 动手前的固定决策顺序。**

![Ponytail 官方首页展示“50 行需求只需 1 行”的核心主张。来源：ponytail.dev，2026-09-06](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/ponytail-source-home.png)

## 先证明代码需要存在

Ponytail 的核心规则只有七级，顺序比内容更重要：

1. 这项能力真的需要做吗？推测中的需求按 YAGNI（你不会需要它）处理；
2. 当前代码库已经有 helper、util 或既有模式吗？优先复用；
3. 标准库已经解决了吗？优先标准库；
4. 浏览器、数据库、操作系统已有原生能力吗？优先原生能力；
5. 已安装依赖能完成吗？不要再加包；
6. 一行能完成吗？就写一行；
7. 前六级都站不住，才写满足需求的最少代码。

它不是让 agent 一看到需求就删功能。官方规则明确要求先理解任务、阅读会受影响的代码、追踪真实调用流程，再开始逐级判断。修 bug 时也不是在报错位置加一个补丁，而是查所有调用者，在共享函数里修一次根因。

![Ponytail 官方分享图强调“最好的代码是没有写出的代码”。来源：ponytail.dev 官方素材，2026-09-06](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/ponytail-source-social.png)

这一区分很关键。少写代码可以来自三种完全不同的动作：砍掉没有证据的未来能力；改用代码库现成能力、标准库或原生控件；必须自写时，只改最少文件与最短路径。

前两种通常比“把同一套设计压成更短语法”更有价值。因为被省掉的不只是提交时的行数，还有未来的升级、测试、漏洞和认知成本。

代码库优先还解决了一个常被忽略的问题：一致性。同一个仓里若已有鉴权中间件、错误类型和表单校验器，agent 新造一套“更优雅”的实现，可能功能正确，却让调用方式、日志格式和测试方法分裂。复用现有模式通常不是最短的单文件代码，却是整个仓库最小的长期差异。

这一步需要真实搜索，而不是凭感觉说“应该没有”。agent 应查符号、调用者、相邻模块和依赖清单，再决定是否新增。Ponytail 把“先读懂”放在阶梯之前，就是为了避免小差异写在错误位置。少写代码的前提不是少看代码，而是看得足够多，知道哪一处改动能覆盖全部路径。

## stdlib-first 不是怀旧

标准库优先常被误解成“拒绝现代工具”。其实它是在问：为了一个已经稳定解决的问题，是否值得引入新的版本、供应链和维护界面。

Python 官方文档中的 `functools.cache` 和 `lru_cache` 已经提供线程安全的记忆化封装。若需求只是给纯函数结果加缓存，先用它们往往比自建 `CacheManager`、锁、淘汰逻辑和配置对象更稳。Ponytail 首页用“48 行缓存类换成一个装饰器”表达的正是这一点。

![Python 标准库 functools 官方文档，包含 cache 与 lru_cache。来源：Python Documentation，2026-09-06](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/ponytail-source-python-stdlib.png)

stdlib-first 的判断不能只看行数。至少还要核对三件事：语义是否一致；边界条件是否覆盖；运行环境的版本是否具备该能力。标准库若只覆盖八成需求，硬套进去再堆补丁，反而更长。

因此，合理的顺序是：先写出真实约束，而不是先挑库；对照标准库的行为与失败方式；只有缺口确实存在，才进入依赖或自写代码。

这不是“零依赖”信仰，而是把新增依赖从默认动作改成需要说明理由的动作。

## native-first 省掉整层维护

Ponytail 测试里最明显的缩减来自前端：日期、颜色和文件选择都存在浏览器原生控件。无规则 agent 容易先想到组件库、包装组件、样式文件和状态同步；native-first 先验证平台控件能否满足验收条件。

![Ponytail 官方预览展示原生能力替代自建组件的界面。来源：Ponytail 官方素材，2026-09-06](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/ponytail-source-preview.png)

原生优先并不等于任何场景都用原生控件。品牌视觉、跨浏览器一致性、复杂时区规则或辅助功能细节超出原生能力时，自建组件依然合理。区别在于，团队要先指出具体缺口，而不是因为“通常会装一个 picker”就直接安装。

更广的 native-first 还包括：数据库约束能保证唯一性，就不要只靠应用层先查再写；CSS 能完成的交互，不必先加 JavaScript 状态；操作系统已有计划任务，不必立即建设一套调度服务；HTTP 缓存头能解决的内容，不必先做自定义缓存协议。

每一次向下复用成熟平台，都会少一层自己负责的状态机。

## 项目自测说明了什么

Ponytail 新版 agentic benchmark（代理式基准测试）使用 Claude Code 2.1.177 和 Haiku 4.5，在固定版本的 FastAPI + React 开源模板上执行真实改动。12 个功能任务中，每个任务、每种规则各跑 4 次，代码量按最终 `git diff` 新增行统计。

结果是：Ponytail 相对无技能组平均少 54% 新增代码、少 22% token、低 20% 成本、快 27%。但这个平均值的分布很不均匀：日期选择器从 404 行降到 23 行，搜索接口等本来就短的后端任务则几乎不变。

![Ponytail agentic benchmark：12 个功能任务中代码量、token、成本与时间相对无技能组的比例。来源：Ponytail 官方 benchmark，2026-06-18](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/ponytail-source-benchmark-agentic.png)

这组数据最值得保留的结论不是“任何项目都能少 54%”，而是两点：有原生替代时，规则能阻止 agent 过度实现；没有可省空间时，各组会趋同。它测到的是特定任务与模型下的行为变化，不是普遍的软件生产率定律。

方法页还主动披露了局限：主实验只有一个模型，n=4 带有随机波动；四个代码量单元遇到 Windows 超时；安全测试只覆盖六个外科式任务。项目早期单轮生成曾宣称少 80% 至 94% 代码，作者后来承认聊天式基线放大了差距，并重建了实验。

![Ponytail 官方 benchmark 方法页，说明真实仓、四组对照与局限。来源：Ponytail benchmark 文档，2026-06-18](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/ponytail-source-benchmark-method.png)

这种修正比夸张数字更有参考价值。它说明 Ponytail 不是“少写即正确”的证明，只是一个可复现的工程假设：把复用与原生能力放在代码生成之前，能减少一类常见膨胀。

## 少写不能少掉防线

“写一行”最危险的误读，是把验证和错误处理一起删掉。Ponytail 的规则特意把信任边界校验、防止数据丢失的错误处理、安全与无障碍列为不可削减项；非平凡逻辑至少留下一个可运行检查。

官方安全组用路径穿越、SQL 注入、伪造 token、坏 CSV 行和配额耗尽等输入执行产物。Ponytail 组 20 次测试全部保住防线；只给一句“遵循 YAGNI、偏好一行”的对照组为 19/20，有一次路径拼接允许 `../../` 逃出目录。

这组样本不够证明安全，却清楚展示了规则应怎样写：约束“删什么”时，同时列出“绝不能删什么”；最短差异要在理解完整流程之后比较；触及信任边界时，正确性与防护优先于行数；有意采用简化算法时，写明上限与升级条件。

所以，Ponytail 不是 one-liner（单行代码）提示词的豪华包装。它多出来的文字，恰恰用于阻止 agent 为了短而脆。

## 跨 agent 的关键是薄适配

这个项目把核心行为放在通用规则文件与六个技能里，宿主目录只负责加载。完整插件型宿主可以注入规则、切换强度并提供命令；只支持项目说明的工具，则读取一份对齐的规则文件。

官方适配矩阵列出了 Claude Code、Codex、Gemini CLI、OpenCode、Copilot CLI、Cursor、Windsurf、Cline、Kiro、Zed、Qoder 等宿主。真正可迁移的不是某个安装命令，而是同一决策顺序在不同宿主里保持一致。

![Ponytail 官方宿主适配矩阵，区分插件型与规则文件型接入。来源：Ponytail agent portability 文档，2026-09-06](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/ponytail-source-portability.png)

[AGENTS.md](https://agents.md/) 本身是面向 Coding Agent 的开放说明格式；Anthropic 的 [Claude Code 插件文档](https://docs.anthropic.com/en/docs/claude-code/plugins) 也明确允许插件组合 skills、agents、hooks 与 MCP；[Gemini CLI 扩展文档](https://geminicli.com/docs/extensions/) 则提供扩展清单、上下文文件与命令机制。这三类一手说明共同证明：把规则主体与宿主装载方式分开，是现实可行的分发方法。

![AGENTS.md 官方站点说明开放的 agent 指令格式。来源：agents.md，2026-09-06](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/ponytail-source-agents-md.png)

但“支持很多宿主”不等于行为完全相同。插件型适配可以每轮注入、向子 agent 传播并切换 full、lite、ultra；规则文件型适配通常只有静态指令，没有生命周期钩子。评价跨 agent 方案时，应分别看规则可读性、装载可靠性和子 agent 覆盖，不能只数目录名称。

## 测试重点不是口号本身

仓库测试目录覆盖行为、正确性、命令、hooks、Windows、卸载流程，以及 Copilot、Gemini、Grok、Hermes、OpenCode、Qoder 等适配器。规则副本还有一致性检查，避免一个宿主更新、另一个宿主仍加载旧文案。

![Ponytail tests 目录覆盖规则行为、hooks 与多个宿主适配。来源：Ponytail 仓库 tests 目录，2026-09-06](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/ponytail-source-tests.png)

这给自建规则集一个直接启发：不要只测试“文件存在”，要测试规则是否真的进入宿主、命令是否可调用、关闭模式是否停止注入、子 agent 是否继承，以及不同系统上的路径处理。

可复用的测试分成三层：文本一致性，核心七级顺序在各适配文件中没有漂移；装载行为，启动、切换与关闭模式按预期生效；产物评分，同一任务对照新增行、文件数、依赖数与必要防线。

只有产物评分能回答“agent 是否真的少写了”，前两层只能说明安装没有坏。

团队还应给“少写”设置反例集。比如新增权限校验、文件覆盖保护、键盘操作和错误恢复，这些任务本就可能增加代码。规则若在这些场景仍一味追求行数下降，说明它优化错了目标。可靠的评分应该允许必要代码增长，同时检查是否避免了重复实现和无关抽象。

另一项容易漏掉的是撤销能力。Ponytail 提供 lite、full、ultra 与 off，并允许按任务调整强度。团队自建规则也应保留明确出口：实验性原型可以更激进，支付、权限和迁移任务则应收紧。规则不是凌驾于需求的总开关，而是一组默认偏好；用户明确要求的行为仍然优先。

## 把规则用在自己的仓库

照搬 Ponytail 全套插件并非唯一做法。一个团队完全可以先把七级判断写进现有的项目规则，再挑两三个过度实现高发任务做对照。

建议从以下顺序开始：选真实历史需求，不选刻意适合一行代码的玩具题；固定仓库版本、模型、验收条件和权限；记录新增行、改动文件、依赖变化与测试结果；单独检查安全、数据损失、无障碍和错误路径；只在结果稳定后，才把规则扩散到更多宿主。

判断成功也不应只看 LOC。若少 100 行却引入了隐藏行为差异，或者把清晰逻辑挤成难读表达，维护成本并没有下降。更可靠的目标是：**在行为、边界与可读性不退步的前提下，减少由团队长期负责的自定义表面积。**

Ponytail 最有价值的提醒很朴素：Coding Agent 的能力不只体现在能生成多少代码，也体现在知道何时不该生成。把 YAGNI、stdlib-first 和 native-first 固定在动手之前，agent 才更像会维护系统的人，而不是按字数交作业的人。
