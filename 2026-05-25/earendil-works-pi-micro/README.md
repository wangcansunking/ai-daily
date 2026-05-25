# AI代码工具pi的激进策略：默认关闭新贡献，加固供应链防漏洞

> ai-daily · 2026 年 5 月 25 日 02:41 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/earendil-works-pi-micro/head.png)

2026 年 5 月 7 日，凌晨两点。一个叫 Mario 的开发者（GitHub ID: badlogic）正在给 pi-mono 仓库提交一份新的 coding session 记录。他的终端上跑着 `pi-share-hf`，一键把刚才三小时的 agent 对话推到了 Hugging Face 上——包括每一次 tool call、每一次失败、每一次自我修正。

这不是什么内部测试，这就是 pi 项目的日常。整个 repo 的 README 里，最显眼的不是架构图，而是一句请求：

![earendil-works/pi — AI agent toolkit: coding agent CLI, unified LLM API, TUI & w](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/earendil-works-pi-micro/content-1.png)

> If you use pi or other coding agents for open source work, please share your sessions.

![Exy mascot](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/earendil-works-pi-micro/content-2.png)

**在 AI 圈人人都在造 benchmark 的时候，这项目在收集真实的失败日志。**

## 一个拒绝玩具基准的 coding agent

pi 是一个 monorepo，核心只有四个 package：`pi-ai`（统一 LLM API）、`pi-agent-core`（agent 运行时）、`pi-coding-agent`（交互式 CLI）、`pi-tui`（终端 UI 库）。外加一个独立的 `pi-chat` 仓库处理 Slack 集成，以及 vLLM pods 做自托管推理。

技术栈看起来中规中矩。但让我愣神的是项目的贡献规则：

> New issues and PRs from new contributors are auto-closed by default. Maintainers review auto-closed issues daily.

新贡献者的 issue 和 PR 默认自动关闭，维护者每天批量审查被关掉的内容。这种反向门控机制在开源项目里极其罕见——绝大多数项目都在求爷爷告奶奶拉贡献，pi 却先把门焊死，再手动挑拣。



![pi 贡献流程示意，新 issue 进入后自动关闭，维护者从关闭池中审查](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/earendil-works-pi-micro/schematic-1.png)



配套的还有一套我见过的最激进的供应链加固策略。`.npmrc` 里设置了 `save-exact=true` 和 `min-release-age=2`，意思是所有外部依赖钉死精确版本，且 npm 上发布不到两天的包直接拒绝解析。`package-lock.json` 是"依赖的 ground truth"，pre-commit hook 甚至会拦截意外的 lockfile 变更，除非你显式设置环境变量 `PI_ALLOW_LOCKFILE_CHANGE=1`。

更狠的是 shrinkwrap 生成流程：发布出去的 CLI 包里包含 `npm-shrinkwrap.json`，把传递依赖也钉死。如果某个依赖突然引入了 lifecycle script，CI 会直接失败，直到维护者审核并加入显式白名单。

**依赖即代码，npm 的下一个 left-pad 事件别想从 pi 的管道里钻进去。**

## 数据飞轮：把用户变成训练集

pi 项目最不寻常的设计不在代码里，在分发策略上。它鼓励用户公开分享 coding agent 的完整会话记录，理由是：

> Public OSS session data helps improve coding agents with real-world tasks, tool use, failures, and fixes instead of toy benchmarks.

翻译成人话：你帮我跑 agent，顺便把翻车现场也给我，我拿这些真料喂模型。

为此项目专门做了两个工具：`badlogic/pi-share-hf` 负责把会话推送到 Hugging Face，只需要一个 HF 账号和 CLI 就能搞定。项目作者 badlogic 自己定期发布 `pi-mono` 的开发 session 到 Hugging Face 上，以身作则。



![pi 数据飞轮流程，用户 CLI → pi-share-hf → Hugging Face 数据集 → 模型改进 → 更聪明的 agent](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/earendil-works-pi-micro/schematic-2.png)



这个飞轮的精妙之处在于：它绕过了 AI 圈最虚伪的环节——benchmark 竞赛。HumanEval 的分数已经通货膨胀到失去参考意义，SWE-bench 被各家 agent 刷到 40% 以上之后也快变成另一个玩具指标。pi 的策略是：别给我看你在人造考试里的成绩，给我看你修一个真实 bug 时 tool call 的顺序对不对。

而且数据收集的门槛被压到了极致。一个 Hugging Face 账号加一条命令，零配置。对于 OSS 开发者来说，分享 coding session 几乎没有额外成本——反正代码本来就是公开的，agent 怎么写的也是过程透明。

**这是开源社区的终极套娃：用开源代码训练 AI，让 AI 写更多开源代码，顺便把所有过程记录下来喂给下一代模型。**

（值得一提的是，pi.dev 这个域名是 exe.dev 捐赠的，两个域名的所有者显然知道自己在赞助什么——一个可能吃掉自己饭碗的工具。）



![pi monorepo 包结构图，pi-ai / pi-agent-core / pi-coding-agent / pi-tui 四层 + pi-chat 外部集成](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/earendil-works-pi-micro/schematic-3.png)



不过这个飞轮有一个隐藏假设：用户真的愿意分享失败记录。对个人开发者来说，把自己的 agent 会话公开，等于暴露了所有试错过程——包括愚蠢的 prompt、跑偏的 tool call、被 agent 搞坏的本地文件。这需要一种在开发者圈子里并不普遍的心理素质：对"不完美过程"的坦然。

badlogic 显然有这种素质。他不仅公开 session，还录了视频教别人怎么公开 session。这种"先把自己裸奔了再邀请大家一起裸奔"的真诚，在 AI 工具赛道里算是一股泥石流。

---

pi 的 README 最后一句话是 `License: MIT`。一个把供应链锁到牙齿的项目，选了最宽松的协议。你可以 fork 它、改它、拿它赚钱，但如果你用 npm 装它，依赖树里的每一个字节都已经被审查过两遍。

这大概就是 2026 年的开源精神：代码是你的，但信任必须由我来保证。

#AI #CLI #LLM #API #TUI
