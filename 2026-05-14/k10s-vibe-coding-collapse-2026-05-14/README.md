---
title: "AI 写功能不写架构 · Claude Code 7 个月翻车之后"
date: 2026-05-14
weekday: 星期三
category: AI Coding
slug: k10s-vibe-coding-collapse-2026-05-14
description: "5 月 12 日独立开发者 k10s 在自家 devlog 发了一篇《I'm going back to writing code by hand》，承认自己 7 个月、234 commits、纯 Claude Code 协同写出来的 GPU 版 k9s 项目最终需要归档，单一 model.go 文件膨胀到 1690 行，Update() 跑 500 行 110 个 switch，9 处手动 nil 重置散落各处，goroutine 跨线程改 Model 字段「99% 工作正常」。这篇 HN 一天冲到 1006 pts、607 条评论，正面回应 5 月 10 日 James Shore「AI 必须降维护成本」的论断。本文从崩塌经过、god struct 内部解剖、CLAUDE.md 五条硬约束、国内开发者社群同款经验、以及一个核心论点「AI 写功能不写架构」六个角度展开——读完更知道 AI Coding 怎么用对，而不是更想放弃 AI。"
track: overseas-hot
domain: ai-coding-architecture
cover: k10s-vibe-coding-collapse-2026-05-14.png
tags:
  - Claude Code
  - AI Coding
  - vibe coding
  - 架构
  - CLAUDE.md
  - k10s
  - 通义灵码
  - Trae
  - Cursor
  - MarsCode
  - CodeBuddy
---

# AI 写功能不写架构 · Claude Code 7 个月翻车之后

> 5 月 12 日，独立开发者 k10s 在自家 devlog 写了一篇《I'm going back to writing code by hand》，承认 7 个月、234 commits、纯 Claude Code 协同跑下来的 GPU 版 k9s 项目最终需要归档。这篇文章在 Hacker News 一天内冲到 1006 pts、607 条评论，正面回应了 5 月 10 日 James Shore 给出的「AI 必须降维护成本」判断。两篇共振没有把 AI Coding 推向「不靠谱」结论，反而把行业推进到下一阶段——**AI 写功能，不写架构**。这是一个对国内每天用 Claude Code、Cursor、Trae、通义灵码、文心快码、MarsCode、CodeBuddy 的开发者反而更友好的拐点。

![AI 写功能不写架构 · Claude Code 7 个月翻车之后](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-14/k10s-vibe-coding-collapse-2026-05-14/k10s-vibe-coding-collapse-2026-05-14.png)

打开任何一个跑了半年以上的 AI Coding 项目，第一眼通常看到的不是 bug，而是单文件慢慢长大。**一个 model.go 涨到 800 行你不会注意；涨到 1200 行你开始隐约不安；涨到 1690 行你已经记不清里面有多少状态字段，只能在搜索框里靠关键字找。** k10s 作者写下这篇 devlog 的时候，他的 model.go 正好停在 1690 行。234 commits 跑了 7 个月、大约 30 个周末，每一次都觉得「这次只多加一个视图就好」，直到第 234 次。

5 月 12 日凌晨这篇博客被 HN 顶到 1006 pts，607 条评论里大概一半在共鸣（「我家项目也是这样」），另一半在补刀（「Update() 跑 500 行 110 个 switch 是怎么忍到现在的」）。**这件事重要不是因为又一个项目翻车了，而是因为它和 5 月 10 日 James Shore 的《You Need AI That Reduces Maintenance Costs》在 36 小时内连环出现——一篇给出了维护成本反比定律的理论，一篇用 234 commits 实证了反比定律不被遵守时的样子。**

更值得国内开发者关注的，是作者最后的那个判断：**AI 写功能，不写架构（AI writes features, not architecture）。给它越多自由就越糟，越早把架构骨架手画好、把 CLAUDE.md 写紧，AI Coding 才能从短跑工具变成长程工具。** 这个判断不是「AI 不行」，而是「AI 在长程里应该被怎么用」。本文围绕这个核心论点展开，逐层拆开 k10s 这次 7 个月的真实账本，再对照国内 6 款主流 AI Coding 工具用户社群里早就反复出现的同款经验。

## 一、7 个月时间轴：从神奇到崩塌只用了 4 个阶段

![k10s 7 个月 234 commits vibe-coding 时间轴](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-14/k10s-vibe-coding-collapse-2026-05-14/k10s-commit-timeline.png)

把 k10s 这 7 个月画在时间轴上，4 个阶段非常清楚。第一个月是「神奇期」，从 0 到 30 commits，k10s 在 3 周内交付了一个完整的 k9s clone——pods 视图、nodes 视图、deployments、services、命令面板，全部在单 session 里完成。技术栈是 Go + Bubble Tea（Elm Architecture TUI 框架，国内开发者熟悉的话可以类比 Vue 的 reactive 模型）。作者后来形容这阶段是 magic，「一晚上把 pod 列表写好，第二个晚上把 describe 写好」。

第二到第三个月是「速度幻觉期」，commits 从 30 涨到 90。作者继续往里塞功能——multi-context 切换、namespace 过滤、全量资源类型支持。这阶段单 model.go 还在 600 行以内，每个新功能 Claude Code 都能在 10 分钟内补完。**作者承认：「我开始觉得自己再也不需要先设计架构了，反正 AI 会帮我把它接起来。」** 这是 vibe-coding 这个词最舒服的时刻——你只需要描述要什么，不需要思考它怎么接。

第四到第六个月是「缓慢腐烂期」，commits 从 90 涨到 200。Update() 方法第一次破 300 行，然后破 500 行，最后里面长出 110 个 switch / case 分支。9 处 `m.logLines = nil` / `m.allResources = nil` / `m.resources = nil` 散落在不同 handler 里——每次 AI 看到「需要重置」就在当地写一行，没人统一管理。**「99% 工作正常」的数据竞争开始偶发**，作者承认这阶段他每加一个功能都有「隐约觉得哪里不对」的预感，但因为每次都还能跑通，他没有停下来读代码。

第七个月是「同时崩溃期」，commits 从 200 到 234。引爆点是 fleet 视图——这是作者要做多集群聚合视图时引入的，本质上要求 Model 同时持有 N 个集群的状态，跨集群查询要异步并发回来。**fleet 视图加进去的那一周，所有早期已经能跑的视图开始连环抽风——pod 列表偶尔显示上一个集群的数据，命令面板状态在切换 context 时不重置，鼠标点击坐标错位。** 作者 7 个月里第一次坐下来从头读自己的 model.go，他后来形容那一刻是「看到一个我以为我写过、但其实从来没有真正理解过的 1690 行单文件」。

到这一步他做了一个反直觉的决定：不修，全归档，用 Rust 重写。但重写之前，先手画架构。

## 二、解剖 god struct：为什么 AI 默认会写成这样

![model.go 1690 行 god struct 拆解](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-14/k10s-vibe-coding-collapse-2026-05-14/k10s-god-struct.png)

把 1690 行 model.go 切开看，能数清楚里面装了 9 类完全不同维度的状态：UI widgets（table、paginator、textinput、viewport）、k8s 客户端 + 缓存、per-view 数据（pods、nodes、deployments、services 各自的列表 + 选中行）、导航历史栈、日志 / describe 状态、fleet 视图状态、命令面板状态、鼠标事件状态，以及 9 处散落的 nil 重置语句。**这 9 类状态没有一类是「相关的」——它们只是被同一个 prompt 序列依次拽进了同一个 struct。**

Update() 方法上面更显眼。500 行，110 个 switch / case 分支，里面 20+ 次嵌套判断 `m.currentGVR.Resource == "Pods"` / `"Nodes"` / `"Deployments"`。每个判断里面又有 3-5 行直接 mutate Model 字段的代码。**这就是作者总结的一句话：「AI 会自动滑向「一个 struct 装下全部」，因为这种写法对单次 prompt 最省力——不用引入新文件、不用想模块边界、不用考虑生命周期。」**

更危险的是异步部分。作者博客里贴了一段他自己的代码：后台 goroutine watch k8s 状态变化，通过 channel 把 updateTableMsg 发回主循环；主循环 Update() 直接 `m.table = msg.rows; m.viewWidth = w`；同时 60Hz 的渲染线程 View() 在读同一组字段。**这是教科书级别的数据竞争（data race）。** Go 的 race detector 一跑就报，但作者从没在带 race detector 的环境下跑过——因为本地手测时「99% 工作正常」。

「99% 工作正常」这件事在 AI Coding 项目里特别普遍。代码读起来 fluent、跑起来通常通过、看起来像是写好的——但它通常没有经历过一次「我先想清楚这里要不要共享状态」的设计步骤，因为这个步骤被 AI 跳过了。**HN 上一条 487 赞的评论说得最直白：「AI 给你的是「能跑」的代码，不是「能扛长跑」的代码。在「能跑」和「能扛长跑」之间，差的就是架构。」**

这里有一个细节值得国内开发者注意：作者并不是新手。他自己写过两个 Bubble Tea 工具，对 Elm Architecture 的「Model + Msg + Update + View」结构非常熟悉。**也就是说，god struct 不是因为「他不会」造成的，是因为「AI 默认这么写、他没有及时拦」造成的。** 经验丰富的人也会栽，这才是 k10s 这篇博客在 HN 引爆 607 条评论的原因。

更进一步看 god struct 的成因。AI 在单次 prompt 里要最大化「完成度」——你说「加一个 fleet 视图」，最省力的实现就是在已有 Model struct 里塞 3 个字段、在 Update 里加 7 个 switch、在 View 里加一个 case。这种实现一晚上就能跑通，比「先拆 trait、再加新文件、再走 message 传递」省 3 倍时间。**AI 的目标函数是「让这一轮 prompt 看起来成功」，不是「让 12 个月后的我容易加第 11 个视图」。这两个目标短期一致，长期完全冲突——而开发者通常只看到短期那一边。** 这就是 7 个月里那种舒服感的来源：每一次都看起来在赢，只有累积起来才会被看到真正的成本。

## 三、k10s 反弹方案：先画架构，再让 AI 填实现

![k10s 架构 before / after · 让 AI 写功能而不是写架构](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-14/k10s-vibe-coding-collapse-2026-05-14/k10s-vs-architecture.png)

作者归档 Go 版本之后，没有立刻 prompt Claude Code 「帮我用 Rust 重写一遍」。他做的第一件事是关掉编辑器，拿出一张白纸，手画 5 个东西：

- `trait View`：每个视图实现统一接口（如 `fn render(&self) -> Frame`、`fn handle(&mut self, msg: Msg)`），**不允许跨视图读写字段**。Rust 的 trait 强制接口边界，比 Go 的隐式 struct 更难退化。
- `enum Msg`：所有异步事件用类型化的消息变体，比如 `Msg::PodsLoaded(Vec<Pod>)` / `Msg::ContextChanged(String)`。**禁止任何代码直接 mutate `app.pods`、`app.context` 字段。**
- `struct AppState`：只持有 `active_view`、`shared_cache`、`config`——纯粹的根节点状态，不下沉具体视图细节。每个视图自己持有自己的状态。
- `mod fleet / pods / nodes / deployments`：物理上拆成不同 mod，互相不可见。共享只走两条窄路：`Msg` 消息 + `cache` 只读接口。
- 后台 tokio 任务：**只发消息，不直接改状态。** 状态修改只有一处——主循环 `state.apply(msg)`。这就是单写者（single writer）原则。

把这五件事画好之后，他才打开 Claude Code，把这张图作为 CLAUDE.md 写进仓库根目录。Claude Code 每次 session 启动自动读 CLAUDE.md，意味着 AI 从第一行代码开始就处在一个有边界的世界里。

「先画再写」听起来像 90 年代的瀑布开发，但作者强调它不是。**他没有写详细的 spec，没有画 UML 类图，没有定义所有字段——他只画了 5 个边界。** 边界之内 AI 仍然有 90% 的自由：pods 视图怎么排版、Update 怎么处理键盘事件、cache 怎么更新——AI 都可以自由发挥。**它只是不能再跨边界。**

这是 k10s 这篇博客里最有价值的方法论：**自由 ≠ 无边界，边界 ≠ 不自由。** AI Coding 工具长期被推销成「自动驾驶」，但实际上更像「副驾驶」——副驾驶可以决定怎么开，但路线、油门上限、不能压实线这些是司机定的。CLAUDE.md 就是这张「路线 + 红线表」。

值得对比的是 5 月 10 日 James Shore 那篇《You Need AI That Reduces Maintenance Costs》。Shore 提出的「写代码速度乘 N，维护成本除以 N」反比定律，在 k10s 这次实证里得到了一个具体形态——**维护成本不是均匀分布的，它集中在「想加新视图却发现旧结构挡路」这一类时刻。** Shore 的反比定律告诉你账要平，k10s 的实证告诉你账具体平在哪里——平在架构边界上。前一篇给的是判据，后一篇给的是抓手。两篇放在一起读，AI Coding 的长程方法论才算完整。

国内不少团队在过去半年也开始把这套思路落地。一位国内 GPU 云厂商的资深工程师在公众号文章里写：「我们团队接受的内部约束是，任何超过 300 行的单文件 AI 都不再继续往里加，要么拆，要么停。」这是另一个角度的同款规则——以行数为硬阈值，简单粗暴但有效。**核心不在于具体阈值是 300 还是 600，核心在于团队提前定义了「什么时候必须停下来想结构」，而不是「等到第 7 个月再来想」。**

## 四、CLAUDE.md 五条硬约束：一份能直接抄走的模板

![CLAUDE.md 5 条硬约束 · 给 Claude Code 划好边界再让它写](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-14/k10s-vibe-coding-collapse-2026-05-14/k10s-claude-md-template.png)

作者公开了他这次 Rust 重写用的 CLAUDE.md，五条硬约束逐字翻译过来是这样的：

**01 · Ownership（所有权）**：每个视图实现统一接口，不允许任何代码跨视图读写字段。反例：`fleet_view.update_pods_table(...)` 这种写法直接被拒绝。这一条 Rust 借助 trait + 模块可见性，在编译期就能挡住——Go 版本之所以失守，是因为 Go 的 struct 字段默认对同包可见，AI 一路写下来根本没有边界提示。

**02 · Messages（异步用消息）**：所有异步数据必须通过类型化的 `Msg` 变体送达，不允许后台任务直接 mutate 渲染可见的状态。正例：`ch.send(Msg::PodsLoaded(rows))`。反例：`pods_view.rows = rows`。**这一条解决的就是 7 个月里那个「99% 工作正常」的数据竞争。**

**03 · Scope（显式禁区）**：CLAUDE.md 里直接列出「不要做的事」清单。k10s 的清单是「DO NOT add: helm / cron / RBAC」——明确告诉 Claude Code 这个项目只做 GPU 集群可视化，不要扩展到通用 k8s 管理。**显式禁区比「应该做什么」更重要，因为 AI 默认倾向于扩展，需要主动制动。**

**04 · Data（数据类型化到渲染前）**：全程用结构体表达数据，比如 `struct Pod { name: String, namespace: String, gpu_alloc: f64, ... }`。**禁止使用 `Vec<String>` 这种用位置编码字段含义的写法。** 这一条阻止了 god struct 时代那种「pod 信息打散成 7 个并列的 string 列然后靠 index 找」的反模式。

**05 · Concurrency（单写者）**：后台任务只发消息，只有主循环里 `state.apply(msg)` 这一处可以修改 render-visible 状态。**整个程序只有一个写入点。** 这是 Elm Architecture 的灵魂，但 Go 版本里因为 AI 没有这条约束，逐步把它拆成了 110 处写入点。

这五条约束加起来大概 80 行 CLAUDE.md。它们的共同点是：**全部用「不允许」「必须」「只有」这类硬性语气，而不是「建议」「考虑」「可以」这类软性语气。** AI 对软性语气的服从率不稳定，对硬性语气的服从率显著更高——这是 7 个月教训换来的另一个细节。

国内同行如果用 Cursor 或 Trae，可以把上面五条几乎一字不改地搬过去——它们和 CLAUDE.md 平级的文件分别是 `.cursorrules` 和 Trae 工作区里的 rules 设置。通义灵码用户在 IDE 设置里写自定义指令也支持类似机制。**机制是一样的：把约束沉淀到工具会自动加载的位置，让 AI 每次启动就处在边界里。**

一个常被问的问题是：「这五条会不会让 AI 变笨？」实测下来不会。AI 在被画好边界的世界里反而能更准确地完成任务——因为它不再花算力猜「我应该把状态放哪」这种本来不该问它的问题，所有算力都用在了「这个边界内的具体实现」上。**等于把 AI 从架构师降级回到工程师角色——这个降级不是 AI 的损失，是项目的胜利。** 作者在博客评论区回复一位读者时说得很清楚：「重写第一周我以为我会写更慢，结果反而更快——因为 AI 不再帮我做那些它做不好的决定。」

## 五、6 款工具横评：国内开发者社群早就在踩同样的坑

![k10s 教训对照 · 国内开发者社群里早就有人踩过同样的坑](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-14/k10s-vibe-coding-collapse-2026-05-14/k10s-cn-tools-resonance.png)

把 k10s 的教训放到国内 AI Coding 社群里看，能找到大量同款故事。知乎「AI 写代码翻车」话题下，6 款主流工具的真实复盘几乎一字不差：

**Claude Code（美国）** 在国内 CLI 工具圈被开发者用得很重，常见的 vibe-coding 场景是单 prompt 连续加功能、session 不重置。同样的 god struct 在 Rust 项目里以 `model.rs` 长到 1000+ 行的方式出现，状态字段散落、改一处崩三处。**有效的修法是仓库根加 CLAUDE.md，定义模块边界和禁词清单，每次 session 自动加载。**

**Cursor（美国）** 的 tab-complete 习惯容易滚雪球——开发者一边读一边补一边接受，组件 props 不知不觉加到 8 个，单文件里能跑 7 个 useEffect。同款修法是先手画组件接口和数据流图，AI 只补函数体，不允许新增字段或新增 prop。

**通义灵码（中国）** 是国内 Java 中台用得最多的工具之一。一个有代表性的反模式是 Service 接口膨胀——AI 给的方法补全很顺手，三个月后一个 OrderService 长出 50 个方法，单测覆盖率从 80% 跌到 20%。**这种情况下的有效修法是先按 DDD 拆子域，让 AI 只能在子域内补充实现，跨子域调用必须显式经过防腐层。**

**Trae（中国）** 在前端整页生成场景里最容易 vibe。开发者一句话「帮我做一个商品详情页」，AI 给出能跑的 800 行单组件——state 在顶层、props drill 4 层、复用零。有效的修法是先用 Trae 生成骨架 + 组件契约（props 类型 + 事件类型），然后让它在每个子组件内部实现，禁止跨组件改 state 形状。

**MarsCode（中国）** 字节系的开发者在 agent 模式里连续修改的场景很常见。一个真实复盘说：三个月之后没人记得「为什么这里 catch 之后又 throw」。**修法不复杂——团队强制 PR 描述用「AI 改了什么 / 人改了什么」二段式，让 review 视角永远区分开 AI 输出和人决策。**

**CodeBuddy（中国）** 在腾讯系小程序后端写 BFF 层时出现过 god service——1200 行单 service 把端、接口、拼装逻辑混在一起。**这种情况和 k10s 的 god struct 是同构问题。** 修法也类似：按「端 × 接口」二维拆，每一格独立文件，AI 只在单格内动。

把这 6 个故事拼起来看，结论很清楚：**god struct 不是 Claude Code 的问题，也不是 Bubble Tea 的问题，更不是 Go 的问题。它是「无边界的 AI Coding」这个工作方式的问题。** 工具换成 Cursor、Trae、通义灵码、MarsCode、CodeBuddy 都一样会出现，只是出现形态不同——god struct / god service / god component / god view。换工具不解决，换工作方式才解决。

## 六、vibe-coding vs architecture-anchored：6 维度对照

![vibe-coding vs architecture-anchored · 6 维度对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-14/k10s-vibe-coding-collapse-2026-05-14/k10s-vibe-vs-anchored.png)

把 k10s 第一次和这一次的两种工作方式做横向对照，6 个维度上的差距清晰可见。**入口结构**这一边，vibe-coding 默认是「单 god struct，字段越加越多」；architecture-anchored 用 `trait + enum`，接口与实现物理分离。**异步数据流**这一边，vibe-coding 默认让闭包直接 mutate 共享字段，数据竞争 99% 通过；architecture-anchored 强制类型化 Msg + 单写者，race-free 是编译期就保证的。

**功能边界**这一边，vibe-coding 没有显式禁区，scope 会持续扩散；architecture-anchored 在 CLAUDE.md 里写「不要做」清单，让 AI 默认不扩展。**回归定位**这一边，vibe-coding 改一处崩三处，bug 出现时定位成本很高；architecture-anchored 因为模块边界清晰，bug 大多被限制在单文件里，单测可以独立跑。

**加新视图成本**这一边的差距最直观——vibe-coding 加一个视图意味着要在 7 处不同代码里挪一遍（Model 字段、Update switch、View 渲染、message handler、nil reset、键盘绑定、命令面板），成本随项目增长线性甚至指数式上涨；architecture-anchored 加视图只需要加一个文件实现 `trait View`，成本是常数。**7 个月后产物**这一边的结果就更不用说——vibe-coding 归档重写，architecture-anchored 可上线，边界仍然清晰。

读到这一行，关心 AI Coding 长期生产力的开发者应该已经能在心里把自家项目按这 6 个维度排查一遍。如果有 3 项以上偏向左列，现在不是焦虑的时候，是打开仓库根写 CLAUDE.md（或 `.cursorrules` 或 Trae rules）的时候。**这是一个可以在一个晚上完成的动作，不需要重写任何已有代码，只需要让下一次 session 处在边界里。**

值得多说一句的是，这 6 个维度不依赖具体语言。Rust 的 trait + 模块可见性在编译期就能强制边界，写起来最舒服；Go 没有 trait 但有 interface 加包私有，加上 `go vet` 和 race detector 也能做到 90%；TypeScript 借助 type-only import 和模块边界也完全足够；Python 弱一些但靠 `__all__` 和 mypy strict 模式也能逼近。**语言只是边界的执行者，真正的边界来自 CLAUDE.md 里那五条硬约束。** 一个仍在用 Python 写 AI Coding 项目的开发者，可以把这 6 个维度全部翻到右列，前提是 rules 文件写得够紧。

另外一条容易被忽视的维度是 review 节奏。vibe-coding 项目里 PR 通常很小很频繁——AI 一次改 20 行，开发者看一眼就 merge，因为每次都看起来「就改了一小段」。**但 234 个这样的 small PR 累加起来，就是 1690 行 god struct。** architecture-anchored 项目反而 PR 颗粒度更大——一个新视图 = 一个新文件 + 一组 trait impl + 单测，开发者必须坐下来读完整个文件再决定合不合。这种 review 颗粒度的反差，是 7 个月之后两条路线分叉的另一个重要原因。

## 七、回到论点：AI 写功能不写架构 = AI Coding 进入长程时代

7 个月、234 commits、1690 行 god struct、9 处散落 nil、1006 HN pts——这些数字背后的核心信号不是「Claude Code 翻车了」，也不是「vibe-coding 不行了」。**核心信号是：AI Coding 工具链开始进入一个更成熟的阶段——开发者不再问「AI 能不能写代码」，开始问「AI 应该写什么、不应该写什么」。**

5 月 10 日 James Shore 给出了维护成本侧的判断维度——写得快必须维护降本，否则就是反向投资。5 月 12 日 k10s 给出了架构侧的判断维度——AI 写功能可以高效，但写架构必须由人负责。两个维度合在一起，是同一个核心论点的两面：**AI Coding 正在从「短跑速度战」过渡到「长程持续战」，长程的关键不在写代码这一边，在画边界这一边。**

这对国内开发者反而是好消息。在过去一年的 AI Coding 工具竞赛里，国内同行已经普遍掌握了 Trae、通义灵码、文心快码、CodeBuddy、MarsCode 这些工具的使用——「写得快」这一边的差距已经基本被抹平。下一阶段比的是「架构骨架画得好不好」「CLAUDE.md / 工作区 rules 写得严不严」「禁区列得明不明确」——**这是一片纯方法学的赛道，和工具厂商无关，和模型版本无关，每个团队都可以独立切入。**

k10s 在博客末尾贴出了他 Rust 版本仓库的第一个 commit，时间是 5 月 11 日，仓库只有 3 个文件：架构图、CLAUDE.md、`main.rs`。第一行实际代码还没写。但这一次他知道，自己写的不会是 god struct——因为 god struct 不再是 AI Coding 的默认形态。**AI 写功能不写架构，从这个月起，会成为越来越多 AI Coding 团队的工作底色。** 这不是 AI 退场，这是 AI 开始在被画好的世界里发挥它真正高效的那一面。

那 1690 行 model.go 没白写。它是行业为这个判断维度交的一笔学费。
