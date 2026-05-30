# ECC如何统一Claude Code等7大平台？背后是249个技能与分层设计

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/affaan-m-ecc-micro/head.png)

182K+ stars。28K+ forks。170+ 个贡献者。12 种语言生态。7 个 AI 编程 harness 同时适配。

这不是某个大厂的开源项目，这是一个单人维护者 10 个月里每周迭代出来的东西。

![affaan-m/ECC — The agent harness performance optimization system. Skills, instin](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/affaan-m-ecc-micro/content-1.png)

ECC（The agent harness performance optimization system）的 GitHub 页面打开第一眼，你可能会以为它是 Claude Code 的一个"配置包合集"——63 个 agent、249 个 skill、79 个 legacy command shim，外加一套安装脚本。但往下翻到 v2.0.0-rc.1 的 release notes，里面赫然写着一个 Rust 写的控制平面原型（`ecc2/`），已经能跑 `dashboard`、`start`、`sessions`、`status`、`stop`、`resume`、`daemon` 七个命令。一个"配置包"正在悄悄长出一个 agent 操作系统的骨架。

![ECC - the harness-native operator system for agentic work](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/affaan-m-ecc-micro/content-2.png)

**这不是 prompt engineering 的胜利。这是把 agent 当成生产系统来运维的思维胜利。**

## 不是配置包，是 agent 的操作系统

ECC 最让我愣神的地方不是它的数量——249 个 skill 确实吓人，但大厂随便一个内部工具链也能攒出这个数。真正有意思的是它的**分层设计**：skills 负责"怎么做"（coding-standards、backend-patterns、django-security），instincts 负责"从做中学"（continuous-learning-v2 带置信度评分的模式提取），memory 负责"跨 session 记住"（hooks 自动保存/加载上下文），security 负责"别出事"（AgentShield 集成，1282 个测试用例、102 条规则）。

这四个层叠在一起，形成了一个 agent 的运行时闭环：干活 → 学习 → 记忆 → 安检 → 下次干得更好。



![ECC 的四层运行时架构——Skills 层、Instincts 层、Memory 层、Security 层形成闭环](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/affaan-m-ecc-micro/schematic-1.png)



更关键的是，ECC 从一开始就不是为某个单一 harness 设计的。它的 README 第一屏就列了 Codex、Claude Code、Cursor、OpenCode、Gemini、Zed、GitHub Copilot 七个目标平台。v1.7.0 开始支持 Codex app + CLI，v1.6.0 加了 Codex CLI 的 `/codex-setup` 命令，v1.3.0 完整集成了 OpenCode 的插件系统（12 个 agent、24 个 command、16 个 skill、20+ 种 event hook）。这种跨 harness 的设计哲学在目前的 agent 工具生态里极其罕见——大部分工具要么绑死 Claude Code，要么绑死 Cursor，没人愿意花精力同时维护七套适配层。

而 ECC 的维护者不仅做了，还做到了 v1.8.0 的"cross-harness parity"——Claude Code、Cursor、OpenCode、Codex 四个平台的行为一致性被"tightened"。



![ECC 横跨 7 个 AI 编程 harness 的适配矩阵——Claude Code、Codex、Cursor、OpenCode、Gemini、Zed、GitHub Copilot](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/affaan-m-ecc-micro/schematic-2.png)



## 单人维护、每周迭代、182K stars——开源社区的异常值

ECC 的 GitHub 页面底部有一句话很容易被忽略：

> Sponsors and Pro subscribers fund the work — that's why a single maintainer ships weekly across 7 harnesses.

一个维护者。七套 harness。每周发版。

我翻了一遍 CHANGELOG，从 v1.2.0（2026 年 2 月）到 v2.0.0-rc.1（2026 年 4 月），两个月里发了 7 个大版本，每个版本都带着实质性的架构演进：v1.3.0 做 OpenCode 插件集成，v1.4.0 做多语言规则架构和 PM2 多 agent 编排，v1.6.0 上 AgentShield 和 GitHub Marketplace，v1.7.0 做 Codex 支持和 presentation builder，v1.8.0 做 hook 运行时控制和 harness 审计命令，v1.9.0 做 selective install 架构和 SQLite 状态存储。到了 v2.0.0-rc.1，直接掏出一个 Rust 控制平面、一个 Tkinter 桌面仪表盘、一套 Itô 预测市场 skill pack、一个 optimization skill pack，外加 operator workflow 的八个新 skill。

这个迭代密度放在任何一个 10 人团队身上都算高产。放在单人维护者身上——要么这个人是 AI 辅助编程的极限案例本人，要么 ECC 的架构设计确实足够模块化，让增量开发几乎零摩擦。



![ECC 从 v1.2.0 到 v2.0.0-rc.1 的版本演进时间线，标注关键架构跃迁](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/affaan-m-ecc-micro/schematic-3.png)



ECC Pro 的定价也很"单人维护者"风格：$19/seat/月，GitHub App 安装，私有仓库可用。开源侧 MIT 协议永久免费。这个定价比 GitHub Copilot（$10/月）贵，比 Cursor Pro（$20/月）便宜，卡在一个"我知道自己值多少钱但我不会漫天要价"的位置。

但 182K stars 这个数字让我有点困惑。一个 2026 年 2 月才开始密集发版的项目，两个月冲到 18 万 stars——要么是 Hacker News 连续爆了三次，要么是某个大厂的内部推荐把它推成了社区标准。原文没有解释这个增长曲线，但 28K forks 和 170+ contributors 的数字至少说明这不是刷出来的——有 170 个人愿意往一个单人项目里提 PR，本身就是质量信号。

## 一个被低估的信号：ECC 2.0 的 Rust 控制平面

v2.0.0-rc.1 的 release notes 里，ECC 2.0 alpha 只占了一小段，但这是整个项目最值得关注的转向。

> ECC 2.0 alpha is in-tree — the Rust control-plane prototype in ecc2/ now builds locally and exposes dashboard, start, sessions, status, stop, resume, and daemon commands. It is usable as an alpha, not yet a general release.

一个原本是"Claude Code 插件 + 安装脚本"的项目，现在有了自己的 daemon 进程，用 Rust 重写了控制平面。这意味着 ECC 正在从一个寄生在 Claude Code 里的配置层，变成一个独立运行的 agent 编排系统。`ecc status --markdown --write status.md` 这个命令尤其说明问题——它把本地状态存储转成可移植的 handoff 文档，覆盖 readiness、active sessions、skill-run health、install health、pending governance events，还能从 Linear/GitHub/handoffs 拉 work items。

这已经不是一个"prompt pack"了。这是一个 agent 工作流的 control plane，只是恰好目前主要跑在 Claude Code 里。

我猜 ECC 2.0 的路线图是：Rust 控制平面做进程管理和状态同步，各 harness 的适配层做"驱动"，skills 和 instincts 做"应用层"。如果这个架构跑通，ECC 就会变成 agent 时代的 Docker——不关心你底层用的是 Claude 还是 Codex，只负责把 agent 工作负载编排好、监控好、记住上下文。

至于它能不能活到那一天，取决于两件事：一是维护者能不能在"每周发版"的节奏下不 burnout，二是 182K stars 能不能转化成足够多的 Pro 订阅和 Sponsor，让这个项目从"单人奇迹"变成"可持续的开源公司"。

毕竟，一个每月 $19 的 GitHub App，要养活一个横跨 7 个 harness、12 种语言生态、249 个 skill 的系统——这笔账怎么算都紧巴巴的。但话又说回来，10 个月前这个项目可能还只是维护者自己用的脚本集合。182K stars 之后，它已经是 agent 时代基础设施的一个候选人了。

## 参考来源
- GitHub: affaan-m/ECC — https://github.com/affaan-m/ECC

#ECC #The #Skills #Claude #Code
