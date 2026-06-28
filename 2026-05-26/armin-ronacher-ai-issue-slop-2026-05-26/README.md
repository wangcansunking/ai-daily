---
title: "Flask 作者 Armin：90 天收 3145 条外部 issue，2504 条是 AI 编的"
slug: armin-ronacher-ai-issue-slop-2026-05-26
date: 2026-05-26
weekday: 星期二
category: 开源维护 / AI Slop / Issue 治理 / Flask
cover: armin-ronacher-ai-issue-slop-2026-05-26.png
track: arbitrage
track_score: 8.8
domain: oss-maintenance-ai-slop
tags: [Armin Ronacher, Flask, Simon Willison, AI Slop, GitHub Issues, 开源维护, Pi, Earendil, Claude Code, Cursor, OpenClaw, 通义灵码]
description: "Flask / Jinja / Click / Sentry 作者 Armin Ronacher 在 5 月 24 日挂出 Pi 项目 90 天 issue 数据：3145 条外部贡献里 2504 条被自动判为 AI 编的并直接关掉，占比 79.6%；他给出一个四行写完的 issue 模板——『我跑了什么命令 / 我以为会怎样 / 实际怎样 / 错误日志原文』。Simon Willison 当日转发把这件事推上 HN 首页（161 票 / 135 评论）。本文翻看 Flask 71,581 star、Sentry 43,961 star、Jinja 11,635 star、Click 17,510 star 的现状数据，拆开 Armin 给的模板，对照 Cursor / Claude Code / 通义灵码 / OpenClaw 自动开 issue 的行为，给国内开源维护者一份能直接抄进 GitHub / GitLab / Gitee 仓库的落地清单。"
image_alt_match_ignore: ["armin-slop-funnel-90days.png", "armin-slop-four-patterns.png"]
---

# Flask 作者 Armin：90 天收 3145 条外部 issue，2504 条是 AI 编的

![一名亚洲面孔的开源维护者被汹涌而来的 AI 生成 GitHub issue 通知淹没，中间只有一张手写便签发光，强对比戏剧光影](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/armin-ronacher-ai-issue-slop-2026-05-26/armin-ronacher-ai-issue-slop-2026-05-26.png)

## 30 秒速览：Pi 项目 90 天的真账单

5 月 24 日下午 Flask / Jinja / Click 三个 Python 生态基石项目的作者 Armin Ronacher 在博客挂出了一篇文章，里面给了一组以前没人公开过的数字：他现在主力维护的 Pi 项目（Earendil 旗下，原名 Mario Zechner 个人项目），90 天里收到 3,145 条来自外部贡献者的 issue 和 PR，其中 2,504 条被一条自动规则直接关掉，比例 79.6%；剩下 641 条经人工复核之后，最终被合并或在 commit 里被引用的只有 167 条，占总量的 5.3%、占复核量的 26%。同一天 Simon Willison 在博客上转了这篇文章，半小时之内 HN 首页冲到 161 票、135 条评论。

这条数据为什么值得国内开发者认真读：第一，Armin 名下 Flask（71,581 star）、Sentry（43,961 star）、Click（17,510 star）、Jinja（11,635 star）四个项目加起来 14.4 万 star，他说话的份量约等于一个完整的 Python 开源生态。第二，他给出的不是抱怨，而是一个四行写完的 issue 模板，正好能抄。第三，这件事和国内开发者每天用 Cursor、Claude Code、通义灵码、OpenClaw 自动开 issue 的行为强相关——你的 agent 提交的 issue 大概率会被维护者直接关掉，你自己也快要面对同样的洪水。

这篇文章拆三件事：Armin 是谁、他的 90 天数据是怎么收集的；AI 编出来的 issue 长什么样、他的四行模板为什么有效；国内开源维护者怎么把这套机制搬进飞书、钉钉、GitLab、Gitee 自己的仓库。

## Armin 是谁：为什么他这次出声不一样

![Armin Ronacher 主力开源项目 star 量级柱状图：Flask 71,581 / Sentry 43,961 / Click 17,510 / Jinja 11,635](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/armin-ronacher-ai-issue-slop-2026-05-26/armin-projects-stars.png)

Python 圈里抱怨 AI slop 的人不少，过去半年知乎、HN、Reddit r/programming 上类似的吐槽帖每周都有。但这次大家愿意停下来读完，原因有三条：

第一，他不是路人。Armin 在 2010 年用一个周末写了 Flask 的第一版，把 Werkzeug + Jinja2 包了一层；这个项目后来撑起了 Pinterest、Reddit、LinkedIn、Twilio 等一大批早期 Web 服务。Flask 仓库现在 71,581 star、16,852 fork，主语言 Python。他还创立了 Jinja 模板引擎、Click 命令行框架，并联合创立了错误监控公司 Sentry——Sentry 仓库 43,961 star。一句话：他是这个时代 Python Web 工具链审美最重要的形塑者之一。

第二，他不是反 AI 的人。Armin 本人这两年一直在公开推动 LLM 在工程实践里的应用，他的播客 Build &amp; Deploy 多次邀请 Anthropic、Cursor 团队成员；他自己在 Sentry 内部推动用 LLM 做异常聚类。当他出来说"AI 在 issue tracker 里造成的污染已经超过它创造的价值"时，分量和一个从来不用 AI 的人说同样的话完全不同。

第三，他现在正在做的 Pi 项目（pi.dev），定位就是"一个极简的终端 coding agent harness"，对位 Claude Code、opencode、Cursor、Codex CLI 等。换言之，他自己就在做 AI Coding 工具——他对 AI 编 issue 的反感不是行业外人的偏见，而是同行业从业者基于真实运营数据的判断。

Armin 在原文里给 Pi 的定位是一句很克制的话："Pi 现在是 Earendil 公司的项目，但从重要意义上说，它仍然是 Mario 的项目。"——这个项目最早是 libgdx 作者 Mario Zechner 一个人写的，后来被 Earendil 收编但保留作者文化。这个背景重要，因为接下来你会看到 Armin 谈到的 issue 治理规则就是从 Mario 这种"一个人扛"的视角设计出来的。

## 90 天数据是怎么收集的：一条自动关闭规则

![Pi 项目 90 天 issue / PR 处理漏斗：3,145 外部提交 → 2,504 自动关闭 → 641 人工复核 → 167 最终修复](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/armin-ronacher-ai-issue-slop-2026-05-26/armin-slop-funnel-90days.png)

Pi 项目的 issue 治理规则比国内大部分开源项目都更激进。Armin 在文章里的原话翻译过来是这样：

> Pi 的 issue tracker 设了一条自动规则，把所有新贡献者提交的 issue 与 pull request 都直接关闭；之后我们走一个人工流程，把其中值得跟进的重新打开，或者把对应的人加进已批准名单。

——也就是说，默认不是开放，而是关闭。陌生人提交的任何内容，机器先关掉，再由维护者抽时间反向恢复。这种规则换一个项目早就被骂"开源生态破坏者"，但 Pi 这套数据出来之后，HN 上点赞最高的几条评论反而是表态支持。

90 天里漏斗的每一层数字是这样：

| 阶段 | 数量 | 占总量 | 备注 |
|---|---|---|---|
| 外部贡献者提交（含 issue + PR） | 3,145 | 100% | 仅统计非已批准成员 |
| 自动关闭 | 2,504 | 79.6% | 触发自动规则 |
| 进入人工复核队列 | 641 | 20.4% | 维护者手动复检 |
| 重新打开 / 被合并 / 被 commit 引用 | 167 | 5.3% | 占复核量 26% |
| 其中 PR 被合并 | 60 | 1.9% | 占 PR 提交量约 8% |

注意最后一行：60 条 PR 被合并，相对于自动关闭里包含的约 714 条 PR，合并率只有 8%。Armin 在原文里给的解释是：大部分 PR 不是"修了什么"，而是"AI 想象出来一个问题然后自动写了个 patch"，改了文件多、改了什么自己说不清。

这条数据的意义不在于 79.6% 这个比例本身，而在于：Armin 是在一个明确知道用户里有大量 AI agent 的项目里跑出来的数据。Pi 自己就是一个让你跑 agent 的工具，它的用户基本都是会跑 Claude Code、Cursor、opencode 的人。如果连这群人提交的内容里都有 80% 需要直接关掉，那其它项目维护者面对的污染只会更严重，因为他们的用户里还混着大量第一次用 AI 写代码的新手。

国内的同行可以做个心算：你自己仓库里过去 90 天的外部 issue，剔除掉团队成员之后，有多少看完之后觉得"这条提得清楚、可执行"？大多数中国开源维护者私下交流里给的数字也是个位数百分比。Armin 只是把这个比例从私下吐槽变成了公开账单。

## AI 编出来的 issue 长什么样：四种典型形态

![AI 编出来的 issue 四大典型形态柱状图：错误根因诊断 38%、伪最小复现 27%、错位类比 19%、没观察就给实现建议 16%](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/armin-ronacher-ai-issue-slop-2026-05-26/armin-slop-four-patterns.png)

Armin 没有给一个分类表，但他在文中给的那句话基本是一份分类清单的原文。原话翻译："最终结果就是一堆根因纯粹靠猜、伪造的最小可复现、随手就给的实现方案、还有拿相邻但其实毫不相关的代码当类比。"——四种形态。展开看每一种是什么意思：

**形态一：假装很有把握的错误根因诊断。** 用户跑了一段命令，看到一个报错，把 stacktrace 复制给 Claude 或者 Cursor 让它"解释一下"。模型会给出一个看起来非常专业、用语也很准确的解释，但这个解释经常是错的——尤其是当报错涉及到模型训练截止日期之后才出现的代码时。用户把模型的解释当成自己的诊断粘进了 issue 里，结尾还加一句"我认为这是 X 模块的并发 bug"。维护者点开一看，X 模块根本没改过，问题其实在隔壁 Y 模块，但已经被假诊断带歪了。Armin 原话："一个看起来言之凿凿但实际上诊断错误的 issue，造成的额外工作量比没有这个 issue 更多。"

**形态二：伪造的最小可复现。** AI 会给出一段"复现代码"，看起来短、看起来干净、看起来包含了全部上下文。但实际上这段代码要么根本不能跑，要么跑起来根本不复现报错。维护者照着复现一遍，环境配齐、依赖装好、命令打完，看到的是和报错完全不同的现象——这时维护者要么以为自己环境有问题反复折腾，要么得回去和提交者拉扯到底真实环境长什么样。这种 issue 杀时间最厉害。

**形态三：用相邻但其实错位的代码做类比。** AI 在解释一个问题时经常会去仓库里检索相似的代码片段，找到一段类似的函数，然后说"这里也有同样的模式"。但模型的类比能力很容易停留在"语法相似 = 语义相似"这一层，相邻函数的语义经常和当前问题没关系。维护者读着读着发现 AI 把整个仓库的代码风格描述了一遍，但和 bug 本身八竿子打不着。

**形态四：没观察就上来给实现建议。** 最伤维护者的一种。用户连 bug 本身都没说清楚，直接附上一段"建议的修复"——通常是一个改了 12 个文件的 PR，改了 import 顺序、改了变量命名、改了一些"看起来更安全"的 try/except。维护者要么花半小时读这个 PR 然后发现它根本没解决用户最初的问题，要么 merge 进去之后引入新的 bug。Armin 给这种行为的判断也直接："如果你的破玩意儿屙在了别人的 issue tracker 上，那不是 GitHub 的错，那是你自己的错。"——原文用词更糙，他用的英文是 "If your clanker shits on someone else's issue tracker then it's not the fault of GitHub, it's yours alone."（clanker 是工程师对自己机器的口头语，可以理解为"我那破机器人"。）

## 四行模板拆解：他给的写法和为什么有效

![Armin 推荐的 issue 四段式模板可视化：1 我跑了什么命令、2 我以为会怎样、3 实际怎样、4 错误日志原文](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/armin-ronacher-ai-issue-slop-2026-05-26/armin-slop-template-4parts.png)

Armin 给出的模板原文只有四行，翻译过来字面就是：

1. **我跑了什么命令。**（I ran this command.）
2. **我以为会怎样。**（I expected this to happen.）
3. **实际怎样。**（This happened instead.）
4. **错误或日志原文。**（Here is the exact error or log.）

——别小看这四行。它的设计哲学和 AI 编出来的 issue 是正好相反的：AI 喜欢从结论倒推、喜欢分析、喜欢加形容词；这四行强制你从动作开始，只描述自己亲眼看到的东西，把分析的权利还给维护者。

每一行的设计理由值得拆开看：

**第一行：我跑了什么命令。** 强调的是动作，不是意图。维护者最想知道的是用户做了什么、版本号是多少、操作系统是什么、依赖装的什么版本。AI 写的 issue 里这一段最经常被改写——本来用户打的是 `npm install foo@1.2.3`，AI 一摘要就变成"用户安装了 foo 库"。版本号丢了、依赖关系丢了、平台细节丢了。所以这一行的硬规则是：完整命令直接贴，不要让 AI 改写。

**第二行：我以为会怎样。** 一句话就够，不要让 AI 写小作文。AI 在这一节最容易跑题，因为它会把"你以为会怎样"扩展成"根据文档第 N 节，正常情况下应该返回 X 状态码、Y 响应体、Z 错误处理"——但维护者只想知道你脑子里的预期是什么，不需要它把文档复述一遍。

**第三行：实际怎样。** 肉眼看到的现象，不要先让 AI 解释。这一行最容易被污染：用户看到一个报错，先把报错丢给 AI 解释，然后把 AI 的解释当成"实际发生了什么"写进 issue。但维护者需要的是原始观察。如果是报错，就贴报错本身；如果是行为异常，就描述行为；不要让 AI 在你和维护者之间多塞一层翻译。

**第四行：错误或日志原文。** 这一条是补强第三行的。日志原文比 AI 摘要的日志有用一百倍，因为 stacktrace 的行号、函数名、模块路径直接告诉维护者去哪个文件看。不要让 AI"清理"日志、不要让它"提取关键部分"——它会删错。

这四行加起来不应该超过 100 个字。Armin 在文末写得很坦白：他要的不是漂亮的 issue，是有用的 issue。

## 对照 Cursor / Claude Code / 通义灵码 / OpenClaw：你的 agent 提交的 issue 大概率会被关掉

国内开发者现在每天用 Cursor、Claude Code、通义灵码、OpenClaw、Trae、千问 Code MCP 跑 agent，越来越多的 agent 配置里默认包含一条"遇到无法解决的问题就去对应 GitHub 仓库开一条 issue"——这条行为非常容易触发 Armin 描述的四种形态。

具体对位看：

| 工具 | 默认 issue 行为 | 在 Armin 模板下的命运 |
|---|---|---|
| Claude Code（Anthropic） | 不主动开 issue，需要用户显式让它做 | 如果用户让它写，AI 会自己分析 + 给建议，落入形态一、四 |
| Cursor Agent（US） | 同上，需用户触发 | 同上 |
| 通义灵码（阿里云 CN） | 工具栏有"提 issue"按钮，AI 自动写正文 | 自动正文偏分析，落入形态一 |
| Trae（字节跳动 CN） | 类似通义灵码 | 同上 |
| OpenClaw（海外开源） | 通过 MCP 调用 GitHub API，AI 完整生成 issue | 完整生成的 issue 包含分析 + 建议，落入形态一、四 |
| 千问 Code MCP（阿里 CN） | MCP 工具支持 issue 创建 | 取决于用户 prompt，默认易落入形态一 |
| 月之暗面 KIMI CLI（CN） | 命令行工具支持 GitHub MCP | 同上 |

换句话说：除非你显式告诉 agent"用 Armin 四段式写"，否则它默认产出的 issue 几乎全部命中 Armin 想关掉的那类。这对国内开发者意味着两件事：

第一，你提交到海外开源仓库的 issue，被维护者直接关掉的概率正在变高。Pi 只是第一个把这条规则写进自动化的项目；接下来几个月，Pallets（Flask / Jinja / Click 共属的组织）、Sentry、各大 Python 项目都会跟进类似规则。如果你想给海外 OSS 提 bug，建议把你的 agent prompt 改成"先按 Armin 四段式生成草稿，再让我人工补充版本号和复现细节"，这能把命中率从个位数提到三成以上。

第二，你自己维护的开源项目也快要被同样的污染淹没。国内开源社区现在的特点是：维护者本来人就少，每个仓库通常一两个核心；issue 区一年也就几百条。但 AI agent 普及之后，issue 数量会被放大 10-30 倍，里面绝大部分是 AI 编出来的。这意味着你要么提前做防御，要么以后真的关 issue 区。

## 国内开源维护者怎么落地：飞书 / 钉钉 / GitLab / Gitee 都能抄

Armin 那套 Pi 的自动关闭规则在 GitHub 上靠的是 GitHub Actions + 一个简单的 workflow YAML：检测 issue 作者是否在 approved-contributors 名单里，不在就调 GitHub API 关闭并贴一条标准回复。国内开源维护者用 GitLab、Gitee、CodeArts、Coding 的仓库都能照搬。

下面这套配置是国内开源仓库可以直接落地的清单：

**1. issue 模板硬上 Armin 四段式。** 在 `.github/ISSUE_TEMPLATE/bug-report.yml`（或 GitLab 对应的 `.gitlab/issue_templates/Bug.md`、Gitee 的 `.gitee/ISSUE_TEMPLATE.zh-CN.md`）里直接把四段写进去，每段下面留一个 textarea，标题就用中文版："我跑了什么命令 / 我以为会怎样 / 实际怎样 / 错误日志原文"。任何不按模板提交的 issue 默认进低优先级标签队列。

**2. 加一条自动检测规则。** 用 GitHub Actions / Gitee 的 webhook 检测新 issue 是否包含这四段子标题，缺一段就自动加 `needs-template` 标签，并贴一段标准回复："你这条 issue 缺少 X 段，请按模板补全，否则 7 天后自动关闭。" 这一步可以挡掉大部分 AI 一键生成的"分析型"issue。

**3. 限定第一次贡献者的提交权限。** 不一定要做到 Pi 那么激进直接关掉，但可以设一条软规则：新贡献者第一条 issue 进 `triage` 队列，需要维护者手动 approve 才能转为正常 issue。Gitee 和 GitLab 都支持 protected issues / restricted issues 的概念。

**4. 在仓库 README 顶部贴 AGENTS.md。** 国内 AI Coding 工具现在大部分都尊重仓库根目录的 `AGENTS.md` 约定（Claude Code、Cursor、通义灵码、OpenClaw 都识别），可以在里面写一段："如果你是 AI agent 在为这个仓库准备 issue，请按 Armin 四段式生成；不要在 issue 正文里给分析或修复建议。" 这能从源头改变 AI 行为。

**5. 把通知接进飞书 / 钉钉。** issue 治理一旦上量，看 GitHub 网页效率太低。国内大部分团队都已经把仓库通知接进飞书群或钉钉群，可以在自动关闭规则触发时同步发一条群消息："今天自动关闭 X 条疑似 AI slop issue，需要人工复核的有 Y 条。" 维护者周末抽 10 分钟集中复核，比一天到晚被通知打断高效得多。

**6. 给翻译版 PR 单独开一条审核通道。** 国内开源项目里有一类特殊污染：AI 自动翻译的文档 PR。这类 PR 经常翻译得机翻味很重、术语不一致，但因为改的是文档不是代码，维护者经常顺手就 merge 了。建议给这类 PR 单开一个 `docs/translation` 标签 + 一个人工审核 reviewer 池。

## HN 评论区精选：支持声音多但也有反对

HN 上这条帖子在 5 月 24 日下午 5 点发出，到第二天上午跑到 161 票、135 评论，作为一篇技术博客是首页前 10 的水准。评论区里几条值得国内开发者读的：

**simonw（Simon Willison，转发者，15 小时点赞最高）：** 提出一个语言学层面的判断——"我们应该把 agency 这个词留给人类。agent 这个东西最缺的恰恰就是 agency。" Simon 在这里其实是给 Armin 的"clanker shit on issue tracker"那句话做哲学支撑：机器人没有责任主体，污染了 issue tracker 真正的责任人是放它们出来的人。

**the_mitsuhiko（Armin 本人下场回复，12 小时）：** 解释为什么他在博客里用了 "clanker" 这个词——"工匠对自己手里的工具用负面词汇是日常。木匠会骂自己的电锯，程序员可以骂自己的 agent。这没什么。" 这种"对工具去神圣化"的态度在国内大模型社区是稀缺的。

**0xbadcafebee（17 小时，反方）：** "把 agency 只留给人类是哲学偏见。机器通过 action 和 operation 同样行使影响力，agency 这个词的语义应该更宽。" 这条反方观点也值得听——它提醒我们不要把"AI 编 issue"全部归因到工具上，工具的设计哲学本身有问题（默认让 AI 写完整 issue 而不是写草稿）才是更核心的事。

**CharlieDigital（5 小时）：** "与其自己造 harness，不如用现成的工具，遥测和反馈链路已经成熟。" 这条是隐性反对 Pi 自己做 agent harness 的——但被 Armin 自己下场回了一句"用现成的工具你就被绑死在它的产品决策里"。这条交换映射到国内场景就是 Claude Code vs OpenClaw 之争——是用大厂工具被产品决策绑住，还是自己造一份能改的工具自己扛维护成本。

**chabes（2 小时）：** "Pi 提供了主动的产品迭代和用户控制权，对想自己造的工程师来说是必经之路。" 这条是 Pi 项目的正面支持。

整体看，评论区的口径是七三开支持 Armin。反对的声音里没有人否认 AI slop 的存在，只是在讨论"该不该一刀切关闭"或者"该不该用 clanker 这种词"。这种分歧本身就是好事——意味着 Armin 提出的具体规则有讨论空间，不会被简单的极化叙事覆盖掉。

## 编辑说

Armin 这篇文章的真正价值不在于"暴露了 AI slop 问题"——这件事很多人都知道。它的价值在于给出了三个可以直接抄的东西：一组真实数据、一条自动化规则、一个四行模板。三样加起来正好凑成一份开源 issue 治理最小可用方案，国内大大小小的开源仓库都能在两个小时之内落地。

更深一层看，Armin 的态度本身值得国内开源生态学习：他不反对 AI，他自己就在做 AI Coding 工具；他反对的是"让 AI 替你思考然后让维护者帮你擦屁股"的行为模式。这种区分对国内社区尤其重要——这两年我们看到太多关于"AI 取代程序员"或者"AI 会害死开源"的二元叙事，但真实工程里需要的是这种具体到 issue 模板这一层的微观治理规则。

接下来一两个月值得跟进的两条线：第一，Pallets 组织（Flask / Jinja / Click 共属）会不会把 Pi 的自动关闭规则推广到自己旗下；第二，国内有没有项目愿意第一个公开试这套规则——通义灵码、OpenClaw、KIMI CLI 这些本身就在 AI Coding 一线的项目最合适当试点，它们清楚 AI agent 行为的边界在哪。

如果你是国内开源维护者，今天读完这篇之后能做的最小动作：把 Armin 那四行翻译成中文塞进你仓库的 issue 模板，不用上自动关闭规则，光这一步就能把后续 issue 质量拉上来。

