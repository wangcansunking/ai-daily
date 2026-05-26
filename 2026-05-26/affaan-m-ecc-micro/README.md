# 开源项目 ECC：一人维护，七平台兼容，如何获182K星？

> ai-daily · 2026 年 5 月 26 日 02:43 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/affaan-m-ecc-micro/head.png)

凌晨两点，GitHub Trending 页面刷新。一个名为 `affaan-m/ECC` 的仓库悄然登顶——182K+ stars，28K+ forks，170+ contributors。点进去，README 第一行就砸过来：「The harness-native operator system for agentic work.」

不是 config pack。不是 prompt 合集。不是又一套“Claude Code 最佳实践”。

![affaan-m/ECC — The agent harness performance optimization system. Skills, instin](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/affaan-m-ecc-micro/content-1.png)

是一个在 Anthropic 黑客松上拿了奖、然后被 10 个月高强度 daily use 打磨出来的 agent harness 性能优化系统。61 个 agents，246 个 skills，76 个 legacy command shims，横跨 Claude Code、Codex、Cursor、OpenCode、Gemini、Zed、GitHub Copilot 七个平台。

![ECC - the harness-native operator system for agentic work](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/affaan-m-ecc-micro/content-2.png)

**一个人维护，每周迭代，七平台同步——这叫“个人项目”简直是对这个词的侮辱。**



![ECC 七平台兼容矩阵，从 Claude Code 到 Gemini 到 Zed 一字排开，中间标注“single repo, single maintainer, weekly ships”](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/affaan-m-ecc-micro/schematic-1.png)



## 一个黑客松项目，怎么长成了 agent 界的“操作系统”？

ECC 的故事起点相当平凡：Anthropic 黑客松，一个团队做了套 Claude Code 的配置优化方案，获奖。大多数黑客松项目到此为止——奖杯摆桌上，README 留在 GitHub，半年后无人问津。

但 ECC 没有停。

v1.2.0 开始支持 Python/Django 和 Java Spring Boot。v1.3.0 吃进了 OpenCode 的插件系统。v1.4.0 搞了多语言规则架构，把 rules 从扁平文件拆成 `common/` + `typescript/` + `python/` + `golang/` 的目录树，安装时只拉你需要的语言——不浪费 token。v1.6.0 集成了 AgentShield，一个 1282 条测试、102 条规则的安全扫描引擎。v1.7.0 直接干进了 Codex app + CLI，还把前端 slides 生成和投资材料写作塞进了 skill 列表。v1.8.0 正式宣告自己不再是“config pack”，而是“agent harness performance optimization system”。v1.9.0 引入选择性安装架构和 SQLite 状态存储。到 2026 年 4 月的 v2.0.0-rc.1，它甚至有了自己的 Rust 控制平面原型和 Tkinter 桌面仪表盘。

我看完这份 changelog 的第一个反应是：**这不是开源项目，这是一家公司在跑产品迭代。**

更让我愣神的是，这一切是一个人在维护。README 里写得清清楚楚：「Sponsors and Pro subscribers fund the work — that's why a single maintainer ships weekly across 7 harnesses.」ECC Pro 定价 $19/seat/mo，Sponsor 从 $5/mo 起步。OSS 部分 MIT 协议永久免费，私仓功能走 GitHub App 付费——这套开源商业化模型设计得比某些 VC 烧了 500 万美元的创业公司还干净。



![ECC 从 v1.2 到 v2.0-rc.1 的版本演进时间线，标注关键节点：AgentShield 集成、Codex 支持、Rust 控制平面、桌面仪表盘](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/affaan-m-ecc-micro/schematic-2.png)



## 246 个 skills 背后，是一套“本能-技能-记忆”三层架构

如果说 agents 数量（61）和 skills 数量（246）只是数字堆砌，那 ECC 真正让我觉得有意思的地方，是它把 agent harness 的性能优化拆成了三个维度：skills（技能）、instincts（本能）、memory（记忆）。

Skills 是显式的、可复用的工作流定义。比如 `continuous-learning-v2` 能从 session 里自动提取模式，变成可复用的技能；`iterative-retrieval` 解决子 agent 的上下文膨胀问题；`strategic-compact` 做手动压缩建议。这跟 LangChain 那种“给你一堆 chain 模板”的思路完全不同——ECC 的 skill 是**从实际使用中长出来的**，不是从架构图上画出来的。

Instincts 更有意思。v1.4.1 专门修了一个 bug：`parse_instinct_file()` 在 `/instinct-import` 时会静默丢弃 frontmatter 之后的所有内容（Action、Evidence、Examples 段落）。这个 bug 的存在本身就说明 ECC 有一套 instinct 系统——agent 可以从会话中提取“本能”（带置信度评分），然后用 `/evolve` 命令聚合成 skill。这相当于让 agent 有了“从经验中学习并固化成果”的闭环。

**远水解不了近渴。agent harness 的性能瓶颈不在模型，在于上下文窗口的管理和跨会话的连续性。**

Memory 这块，ECC 做了 SessionStart hook 自动加载上下文、Stop-phase hook 生成会话摘要、SQLite 状态存储追踪安装状态和会话记录。v2.0.0-rc.1 还加了 `ecc status --markdown --write status.md`，能把本地状态快照导出为可移植的交接文档——覆盖就绪状态、活跃会话、skill 运行健康度、待处理的治理事件，以及来自 Linear/GitHub/handoffs 的关联工作项。

这套三层架构的设计哲学，让我想起 Karpathy 在 2024 年说的“LLM 操作系统”概念——只不过 ECC 不是在模型层做操作系统，而是在 harness 层做。



![ECC 三层架构示意：Instincts（本能层，置信度评分+导入导出）→ Skills（技能层，246 个可复用工作流）→ Memory（记忆层，SQLite 状态存储+会话摘要+跨会话上下文加载）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/affaan-m-ecc-micro/schematic-3.png)



## 最狠的一招：把安全和经济性直接嵌进 harness

ECC 有一个让我觉得“这人真干过生产环境”的设计点：AgentShield 安全扫描是作为 skill 直接集成进来的，不是外部工具。`/security-scan` 命令在 Claude Code 里就能跑 1282 条测试、102 条规则的安全审计。v2.0.0-rc.1 还加了 `ecc-tools-cost-audit`，专门审计 agent 工具调用的成本。

再加上 `cost-aware-llm-pipeline`（LLM 成本优化、模型路由、预算追踪）和 `ECC_HOOK_PROFILE=minimal|standard|strict` 的运行时控制——ECC 对“agent 会烧钱”这件事的警惕程度，远超市面上大多数 agent 框架的“先跑起来再说”心态。

而这一切的底气来自一个数字：**997 条内部测试全部用。** v1.8.0 原话是「997 internal tests passing — full suite green after hook/runtime refactor and compatibility updates.」一个人维护的项目，测试覆盖率能到这个程度，我只能说：要么这个维护者不睡觉，要么他写的代码就是自带免疫系统。

最后说一句让我觉得这事可能真的会飞的细节：ECC v2.0.0-rc.1 已经在 tree 里放了 Rust 控制平面原型 `ecc2/`，能本地构建，暴露 `dashboard`、`start`、`sessions`、`status`、`stop`、`resume`、`daemon` 七个子命令。一个从 Claude Code 配置起家的项目，现在有了自己的守护进程。这进化路线，像极了当年 Docker 从“LXC 封装脚本”变成容器帝国的早期轨迹。

至于它能不能复制那条轨迹——182K stars 已经说明有人在认真盯着了。

## 参考来源
- https://github.com/affaan-m/ECC

#ECC #The #Skills #Claude #Code
