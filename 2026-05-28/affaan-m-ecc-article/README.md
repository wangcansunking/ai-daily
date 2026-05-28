# 开源项目 ECC 如何解决 Claude Code 等 AI 编程助手的健忘问题？

> ai-daily · 2026 年 5 月 28 日 08:13 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/affaan-m-ecc-article/head.png)

凌晨 2:47，一个 GitHub 仓库的 star 数正在以肉眼可见的速度跳动。屏幕前的开发者可能刚结束一场与 Claude Code 的鏖战——模型在某个循环里反复输出同样的错误修复建议，token 燃烧速度比咖啡因代谢还快。他切到浏览器，在搜索框里敲下“claude code hooks performance”，然后看到了那个仓库：**affaan-m/ECC**。182K+ stars，28K+ forks，170+ contributors。README 第一行写着：“The harness-native operator system for agentic work.”

这不是又一个配置文件合集。这是一个人与七个 AI 编程助手搏斗 10 个月后，把每一次踩坑、每一次 token 浪费、每一次安全漏洞扫描都固化成代码的生存手册。它的作者没有把它包装成“最佳实践”或“生产力秘籍”——README 里直接说：“Not just configs. A complete system: skills, instincts, memory optimization, continuous learning, security scanning, and research-first development.”

![affaan-m/ECC — The agent harness performance optimization system. Skills, instin](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/affaan-m-ecc-article/content-1.png)

**一个人维护七个平台的插件，每周发版——这不是热爱，这是被逼出来的。**

![ECC - the harness-native operator system for agentic work](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/affaan-m-ecc-article/content-2.png)



![ECC 跨平台兼容矩阵，Claude Code / Codex / Cursor / OpenCode / Gemini / Zed / GitHub Copilot 横向排列，下方标注 61 agents / 246 skills / 76 legacy shims](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/affaan-m-ecc-article/schematic-1.png)



## 当你的编程助手开始“健忘”——记忆持久化是怎么变成一门手艺的

AI 编程助手的最大痛点不是不够聪明，而是记性太差。你花 20 分钟和 Claude Code 对齐了一整套 API 设计规范，切出去回个 Slack 消息，回来它就开始建议你“可以考虑 RESTful 风格”。ECC 的解决方案不是去改模型本身——那也不现实——而是在 harness 层做了一套完整的内存持久化系统。

这套系统的核心是 **hooks that save/load context across sessions automatically**。具体来说，它在 SessionStart 和 SessionStop 两个生命周期节点上挂了钩子：会话开始时自动加载上一次的上下文摘要（默认上限 8000 字符，可用 `ECC_SESSION_START_MAX_CHARS` 环境变量调整），会话结束时自动生成结构化摘要写入 SQLite 状态存储。v1.9.0 引入的 SQLite state store 还带了查询 CLI，你可以直接 `ecc status --markdown --write status.md` 把当前会话的就绪状态、活跃 session、skill 运行健康度、待处理的治理事件、以及来自 Linear / GitHub / handoffs 的工作项全部导出成一份可移植的交接文档。

**token 省下来的不是钱，是开发者对 AI 工具仅存的那点耐心。**

更狠的是，ECC 允许你把 SessionStart 的额外上下文完全关掉——`ECC_SESSION_START_CONTEXT=off`。为什么要有这个选项？因为如果你在用本地模型或者弱模型做低上下文任务，塞 8000 字符的背景信息反而会让输出质量断崖式下跌。ECC 的设计哲学在这里体现得很清晰：**不给开发者强加“最佳实践”，而是给一套可调的旋钮**。Hook 严格程度分三档（`ECC_HOOK_PROFILE=minimal|standard|strict`），你甚至可以单独禁用某个 hook：`ECC_DISABLED_HOOKS="pre:bash:tmux-reminder,post:edit:typecheck"`。这种细粒度的运行时控制，在我见过的所有 agent harness 工具里是独一份。



![ECC Hook 生命周期流程图，SessionStart → 加载上下文 → 会话执行 → SessionStop → 生成摘要 → 写入 SQLite state store，旁注环境变量控制点](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/affaan-m-ecc-article/schematic-2.png)



## 61 个 agent、246 个 skill、76 个 legacy shim——这不是“配置包”，这是一套操作系统

我第一次点进 ECC 的仓库目录结构时，愣了几秒。这不是一个典型的“dotfiles 合集”或者“prompt 模板库”。它有一个 `.claude-plugin/` 目录放着 marketplace 清单文件，一个 `agents/` 目录躺着 61 个专门化的子代理定义，一个 `skills/` 目录塞了 246 个工作流技能，还有一个 `commands/` 目录保留了 76 个向后兼容的 slash 命令。加上 `rules/` 目录覆盖 12 种语言生态——TypeScript、Python、Go、Java、Kotlin、C++、Rust、PHP、Perl、Swift、Django、Spring Boot、Laravel、Quarkus……这已经不是“配置包”了。**这是一个 agent harness 的操作系统。**

v2.0.0-rc.1（2026 年 4 月发布）是这套操作系统的第一个“公测版”。它带来了一个基于 Tkinter 的桌面 GUI（`ecc_dashboard.py` 或 `npm run dashboard`），支持暗色/亮色主题切换、字体自定义、以及跨 Agents / Skills / Commands / Rules / Settings 五个标签页的搜索和过滤。更重要的是，v2.0.0-rc.1 引入了一个用 Rust 写的控制平面原型——`ecc2/` 目录下的代码已经可以在本地编译运行，暴露 `dashboard`、`start`、`sessions`、`status`、`stop`、`resume`、`daemon` 七个子命令。README 的措辞很克制：“It is usable as an alpha, not yet a general release.”但 Rust 控制平面的出现本身就是一个信号：ECC 正在从“一堆 Markdown 文件加 Node.js 脚本”进化成一个有独立运行时的工程系统。

> ECC v2.0.0-rc.1 adds the public Hermes operator story on top of that reusable layer: start with the Hermes setup guide, then review the rc.1 release notes and cross-harness architecture.

v2.0.0-rc.1 还引入了“operator and outbound workflow”的概念——`brand-voice`、`social-graph-ranker`、`connections-optimizer`、`customer-billing-ops`、`ecc-tools-cost-audit`、`google-workspace-ops`、`project-flow-ops`、`workspace-surface-audit` 八个操作员级技能。这些技能不再局限于“帮开发者写代码”，而是扩展到品牌声音管理、社交图谱排序、客户计费运营、Google Workspace 操作这类更像“AI 数字员工”的领域。再加上 `manim-video` 和 `remotion-video-creation` 这两个媒体制作技能，ECC 的边界正在从“编程助手增强”滑向“通用 agent 操作系统”。



![ECC 架构分层图，底层为 Rust 控制平面（ecc2），中层为 hooks / state store / MCP 配置，上层为 61 agents / 246 skills / 76 commands / 12 语言生态规则](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/affaan-m-ecc-article/schematic-3.png)



## 10 个月、170 个贡献者、997 个测试——一个“单人维护”项目的社区悖论

ECC 的 README 里有一句话让我反复看了三遍：“a single maintainer ships weekly across 7 harnesses.”一个维护者，七个平台，每周发版。这听起来像一个 burnout 倒计时。但 ECC 的 GitHub 数据又讲述了一个完全不同的故事：170+ contributors、30+ community PRs merged（仅 v1.6.0 一个版本就合并了来自 30 个贡献者的 PR，覆盖 6 种语言）、12 种语言的 README 翻译（包括简体中文、繁体中文、日语、韩语、土耳其语、俄语、越南语、泰语、德语）、以及一个 Anthropic 黑客松的获奖记录。

**单人维护 + 170 个贡献者 = 架构设计足够好，好到陌生人愿意帮你修路。**

这个“单人维护”的悖论其实有解。ECC 的插件架构把贡献路径切得非常细：`rules/` 目录按语言分文件夹（`common/`、`typescript/`、`python/`、`golang/` 等），新增一个语言的编码规范只需要在对应目录下加 Markdown 文件；`skills/` 目录下每个技能是一个独立文件夹，有自己的 README 和 prompt 模板；`agents/` 目录下每个子代理是一个单独的 `.md` 文件。v1.9.0 引入的 selective install architecture 更是把这个模块化做到极致——`install-plan.js` 和 `install-apply.js` 可以根据 manifest 做目标组件安装，state store 会追踪哪些组件已经安装并支持增量更新。你甚至可以跑 `npx ecc consult "security reviews" --target claude` 让系统自己告诉你该装哪些组件。

但模块化本身不是护城河。ECC 真正的壁垒是那 997 个内部测试（v1.8.0 数据），覆盖了 plugins、hooks、skills、packaging 四个维度。v1.9.0 的 CI 硬化直接修复了 19 个测试失败，加了 catalog count enforcement、install manifest validation，把整个测试套件拉到全绿。一个单人维护的项目有 997 个测试，而且每次发版前必须全绿——这意味着 ECC 的代码质量不是靠代码审查保证的，是靠自动化验证保证的。这也解释了为什么 170 个贡献者的 PR 能被一个维护者高效合并：测试套件就是那个“不看脸”的审查官。



![ECC 贡献者增长曲线和测试覆盖率热力图，x 轴为版本号从 v1.2.0 到 v2.0.0-rc.1，y 轴为 contributors / tests / skills 三条折线](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/affaan-m-ecc-article/schematic-4.png)



## 安全不是功能，是基础设施——AgentShield 和那 102 条规则

2026 年 2 月，ECC v1.6.0 做了一个看似不起眼的更新：`/security-scan` 命令可以直接在 Claude Code 里运行 AgentShield。1282 个测试，102 条安全规则。这个数字放在任何一个独立的安全扫描产品里都不算大，但放在一个 agent harness 的插件系统里，它是**地基级别的存在**。

ECC 的安全架构分三层：第一层是 AgentShield 集成，做代码的静态安全扫描和 CVE 检测；第二层是 hook 层面的沙箱化和输入消毒——ECC 的 Security Guide 专门有一章讲 attack vectors、sandboxing、sanitization；第三层是 runtime 层面的成本控制——ECC Tools 的 cost controls 和 billing portal 确保 agent 不会在失控的循环里烧光你的 API 预算。三层叠加起来，ECC 实际上在 agent harness 和底层模型之间建了一个“安全网关”。

v1.8.0 引入的 observer loop prevention 是这个安全网关的一次重要升级。Observer 循环是指 agent 的输出触发了 hook，hook 又触发 agent，agent 再触发 hook，形成无限循环。ECC 的解决方案是一套 5 层防护（5-layer guard），包括 memory explosion fix（用 throttling 和 tail sampling 限制 observer 的内存膨胀）、sandbox access fix、lazy-start logic、以及 re-entrancy guard（重入防护）。这套机制在 v1.8.0 的 release notes 里被一笔带过，但熟悉 agent 工程的人都知道，observer 循环是 agent harness 领域最难解决的工程问题之一——它本质上是一个分布式系统里的活锁问题，而 ECC 用 5 层防护把它兜住了。

**安全不是“加个扫描按钮”，而是在每一次 hook 触发、每一次 session 恢复、每一次子代理调用时都多问一句：这个操作安全吗？**



![ECC 安全架构三层模型，底层 AgentShield（102 条规则 / CVE 扫描），中层 hook 沙箱 + 消毒，上层 cost controls / billing portal，右侧标注 5-layer observer loop guard](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/affaan-m-ecc-article/schematic-5.png)



## Itô 预测市场技能包和那个“非投资建议”的免责声明

v2.0.0-rc.1 最让我意外的更新不是 Rust 控制平面，也不是桌面 GUI，而是一个叫 Itô prediction-market skill pack 的东西。它包括 `ito-market-intelligence`、`ito-basket-compare`、`ito-trade-planner`、`ito-data-atlas-agent`、`prediction-market-oracle-research`、`prediction-market-risk-review` 六个技能。README 特意加了一句：“public, non-advisory market/basket workflows while keeping live Itô API access gated and separate from ECC Tools billing.”

翻译成人话：**你可以用 ECC 做预测市场研究了，但别拿它当投资顾问，而且实盘 API 访问是隔离的，不算在 ECC Tools 的计费里。** 这个技能包的出现标志着 ECC 正在从一个“编程工具”扩展成一个“通用 agent 工作流平台”。同样的趋势也体现在 v2.0.0-rc.1 新增的 optimization skill pack 里：`parallel-execution-optimizer`、`benchmark-optimization-loop`、`data-throughput-accelerator`、`latency-critical-systems`、`recursive-decision-ledger`——这些技能的名字听起来更像是一个量化交易团队的工具箱，而不是一个前端开发者的 VSCode 插件。

这种“技能包”的打包方式本身也值得一说。ECC 不是把 246 个技能平铺在一个目录里，而是按领域打包——预测市场一个包、性能优化一个包、媒体制作一个包、运营工作流一个包。每个包有独立的命名空间和依赖声明。v1.9.0 引入的 manifest-driven install pipeline 让这种打包方式变得可操作：`install-plan.js` 会根据 manifest 生成安装计划，`install-apply.js` 执行目标安装，state store 追踪已安装组件并支持增量更新。你可以只装预测市场技能包而不碰运营工作流，也可以只装性能优化包而不管品牌声音管理。这种“按需装载”的架构，让 ECC 在功能膨胀的同时保持了可用性。



![ECC 技能包生态地图，预测市场 / 性能优化 / 媒体制作 / 运营工作流 / 语言生态 / 安全扫描 六个领域各自独立，中心为 Rust 控制平面 + hooks runtime](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/affaan-m-ecc-article/schematic-6.png)



## 所以，这到底是谁的“操作系统”？

ECC 的 MIT 许可证和 GitHub Sponsors 模式（$5/月起）加上 ECC Pro 的 $19/seat/mo 定价，组合出了一个微妙的商业模式：开源代码永远免费，但私有仓库的 GitHub App 和托管服务要付费。README 里说得很直白：“Sponsors and Pro subscribers fund the work — that's why a single maintainer ships weekly across 7 harnesses.”

但 ECC 最值钱的东西其实不是代码。代码是 MIT 的，谁都可以 fork。真正有壁垒的，是那 10 个月里在 7 个平台上踩过的每一个坑、那 997 个测试覆盖的每一个边界条件、那 102 条安全规则背后的每一次漏洞复盘、以及 170 个贡献者用 PR 投票选出来的架构方向。这些隐性知识被固化在 61 个 agent 的 prompt 模板里、246 个 skill 的工作流定义里、以及那个正在从 alpha 走向 general release 的 Rust 控制平面里。

一个单人维护的项目，182K+ stars，28K+ forks，170+ contributors，Anthropic 黑客松获奖。这些数字各自独立看都足够惊人，叠在一起——怎么说呢，感觉像是一个开发者在自己的车库里造了一辆 F1 赛车，然后全世界的工程师都跑来帮他调底盘。而他现在正在把这辆赛车变成一个可以量产的平台。ECC 2.0 的 Rust 控制平面、桌面 GUI、operator workflow、Itô 技能包——这些不是功能更新，是产品化的信号。

**从“我的 Claude Code 配置”到“所有人的 agent 操作系统”，这条路他走了 10 个月。接下来的问题不是“能不能做”，而是“谁会先到”。**

---

## 参考来源
- https://github.com/affaan-m/ECC
- https://github.com/affaan-m/ECC/releases (v2.0.0-rc.1, v1.9.0, v1.8.0, v1.7.0, v1.6.0, v1.4.1, v1.4.0, v1.3.0, v1.2.0 各版本 release notes)
- https://github.com/marketplace/ecc-tools (ECC Tools GitHub App marketplace listing)

#ECC #The #Skills #Claude #Code
