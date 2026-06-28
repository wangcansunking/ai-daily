---
title: "Matt Pocock 的 .claude 目录 11 天涨到 6.5 万⭐：从 21 个散文件变成插件"
date: 2026-05-08
slug: mattpocock-skills-update-2026-05-08
type: deep-dive
track: arbitrage
arbitrage_score: 7.6
domain: ai-coding
cover: mattpocock-skills-update-2026-05-08.png
description: "4 月 26 日 mattpocock/skills 是 1.96 万⭐、21 个并列 skill 目录的散文件仓库；5 月 8 日凌晨 GitHub API 返回 65,192⭐、5,616 fork、24 个 open issue，11 天里星标 3.3 倍、新增 .claude-plugin/plugin.json 装成 Claude Code 插件、skill 重排进 6 个分类目录、加了 diagnose / prototype / handoff 三个新 skill、把 design-an-interface / qa / request-refactor-plan / ubiquitous-language 挪进 deprecated/——这是一份英国 TypeScript 老师把私人 .claude 目录运营成产品的 11 天复盘。"
tags: [ai-coding, claude-code, agent-skills, mattpocock, plugin, tdd, diagnose, open-source]
---

# Matt Pocock 的 .claude 目录 11 天涨到 6.5 万⭐：从 21 个散文件变成插件

![cover](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/mattpocock-skills-update-2026-05-08/mattpocock-skills-update-2026-05-08.png)

5 月 8 日凌晨 4 点，把 `gh api repos/mattpocock/skills` 跑一遍，返回的字段比 11 天前那次刺眼很多：`stargazers_count: 65192`、`forks_count: 5616`、`open_issues_count: 24`、`subscribers_count: 489`，仓库大小 93 KB，最后一次 push 是 5 月 7 日 06:34 UTC——也就是不到一天前。4 月 26 日我们写第一篇时，这个仓库是 19,605⭐、1,641 fork、21 个并列在根目录的 skill 文件夹。11 天里，星标 3.3 倍、fork 3.4 倍、subscribers 多了 4 倍——这种增长曲线在 GitHub Trending 总榜上不算夸张，夸张的是 Matt 这 11 天里**没有把它当作一个躺平的明星仓库**：他重排了目录、加了 `.claude-plugin/plugin.json` 把整个 repo 装成了 Claude Code 插件、加了 3 个新 skill、把 4 个旧 skill 挪进 deprecated。这是一篇资深 TypeScript 教师把"我个人的 .claude 笔记"运营成"产品"的 11 天复盘。

![README 顶部新加的 skills.sh badge + newsletter banner](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/mattpocock-skills-update-2026-05-08/mattpocock-skills-update-newsletter.png)

## 11 天数字对账：星标 3.3 倍、fork 3.4 倍、订阅者 4 倍

先把数字摆齐。下面这张表是 4 月 26 日和 5 月 8 日两次 GitHub API 实查的字段对比，不是热度感觉。

![mattpocock/skills 11 天增长曲线](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/mattpocock-skills-update-2026-05-08/mattpocock-skills-update-growth.png)

| 指标 | 4 月 26 日 | 5 月 8 日凌晨 | 变化 |
|---|---|---|---|
| Stargazers | 19,605 | 65,192 | +45,587（3.3 倍）|
| Forks | 1,641 | 5,616 | +3,975（3.4 倍）|
| Watchers / subscribers | ≈100 量级 | 489 | 长期跟踪盘加深 |
| Open issues | 15 | 24 | 维护活跃，未失控 |
| 仓库大小 | 45 KB | 93 KB | 翻倍——文档/资源都在加 |
| 最近 push | 当日 | 5/7 06:34 UTC | 24 小时内 |
| GitHub Trending | Daily #2（4/25）| 长期流量盘 | 已不靠"昨天上热门" |

11 天里凡是和"长期跟踪 + 真用度"挂钩的指标都在涨，而且涨幅是同方向的。fork/star 比从 4/26 的 8.4% 微涨到 8.6%——比 addyosmani/agent-skills 那 11.6% 略低，但远高于纯刷 star 的 1-3% 区间。这说明拿到 65,192⭐ 里至少有 5,616 个开发者愿意 fork 一份回去自用或改造，不是一闪而过的收藏。Matt 自己的 GitHub followers 同期也从 9,239 涨到了一个量级以上——这次没单独查，但 README 顶部新挂的 newsletter 模块自报 *约 6 万订阅者*，跟 4/26 还在数 X 粉丝的状态完全不在一个分发体量上。

更细一点的曲线在 commit 频次。`gh api repos/mattpocock/skills/stats/participation` 返回最近 12 周的提交分布，前 9 周一共加起来不到 20 次，后 3 周里第 10 周突然蹦到 8、第 11 周飙到 32、第 12 周仍有 10——对应的就是从 4 月 25 日 GitHub Trending 上榜那一天起，这个仓库进入"每天都在改"的节奏。这个提交曲线比 star 曲线更能说明问题：star 是被动结果，commit 是主动节奏，主动节奏稳定才是可持续运营的标志。

判断很简单：这条曲线已经从"GitHub Trending 蹭一波"切换成"自带分发"。Matt 的 [aihero.dev](https://www.aihero.dev/s/skills-newsletter) newsletter 在 README 第二屏就开始引流，每篇 issue 都把这个 repo 推一遍——这是个"开源代码 + 付费课程教师 + 邮件订阅"的三段式飞轮，不是单纯靠 GitHub 算法。配合自家课程 *Claude Code for Real Engineers*（4 月新开的 cohort），整条价值链是闭环的：repo 拉新、newsletter 转化、cohort 变现，再把 cohort 学员的实战反馈倒灌回 SKILL.md。这种结构国内做技术内容的同行不太陌生——36 氪、机器之心、量子位走的是"媒体 → 流量 → 广告 / 课程"的路；Matt 走的是"GitHub repo → 邮件订阅 → 付费课程"的路，对个人开发者来说更轻、更可复制。

## .claude-plugin/plugin.json：从散文件升级成 Claude Code 插件

11 天里最实质的工程改动只有一行：4 月 26 日仓库根目录只有 README、LICENSE 和 21 个 skill 文件夹；5 月 8 日多了一个 `.claude-plugin/` 目录，里面塞着 plugin.json。这个文件长这样——是 Anthropic 在 Claude Code 1.x 里规定的*插件清单*格式：

```json
{
  "name": "mattpocock-skills",
  "skills": [
    "./skills/engineering/diagnose",
    "./skills/engineering/grill-with-docs",
    "./skills/engineering/triage",
    "./skills/engineering/improve-codebase-architecture",
    "./skills/engineering/setup-matt-pocock-skills",
    "./skills/engineering/tdd",
    "./skills/engineering/to-issues",
    "./skills/engineering/to-prd",
    "./skills/engineering/zoom-out",
    "./skills/engineering/prototype",
    "./skills/productivity/caveman",
    "./skills/productivity/grill-me",
    "./skills/productivity/write-a-skill"
  ]
}
```

意义有两层。第一层是技术意义：装这个仓库不再需要"手动 cp 到 .claude/skills/"，Claude Code 看到 plugin.json 会把列出来的 13 个 skill 当成一组**统一启用 / 统一禁用**的资产；新仓库的 README 把 4/26 那一长串 `npx skills@latest add mattpocock/skills/<name>` 单条命令简化成了一句 `npx skills@latest add mattpocock/skills`——一次性把整个插件挂上。第二层是产品意义：4/26 的 mattpocock/skills 是"我把私人 .claude 公开了"；5/8 的 mattpocock/skills 是"我维护了一个 Claude Code 插件"。这中间是有运营动作的——挑哪 13 个 skill 放进 plugin.json、哪些先不进、出 bug 时怎么 cut release，都是产品决策。

这条改动的发起人是社区——5 月 6 日合并的一组 PR（`b27a1a4 Add handoff skill documentation`、`ff3ee1d Add prototype skill`）就是把 plugin 化和新 skill 一起推上去的。Matt 的 commit log 里有大量 `Merge pull request #XX` 字样，意味着这 11 天的进化是 Matt + 60k 邮件订阅 + 社区 PR 三方推动的，不是他一个人加班加的——这种结构在 6.5 万⭐ 量级开源项目里已经不奇怪。

更值得国内开发者抄一下的是 plugin.json 里的*取舍*。20 个 skill 不是全进，挑了 13 个；剩下 7 个里 2 个进 personal、4 个进 in-progress、4 个进 deprecated（4+4+2 多余的那一个是 misc 区的 4 个工具型 skill 全部不进 default plugin）。换句话说，Matt 在用 plugin.json 表达一个判断：**装我这个插件你应该立刻就能跑的 skill 有 13 个**，剩下的要么是我的私人偏好（personal）、要么还在 beta（in-progress）、要么我不再推荐了（deprecated）。这种"清单管理"的思路比"目录里有就装"严格得多，是国内 .cursorrules / claude.md 仓的常见盲区——很多国产 .claude/ 仓现在的状态是"扔进 skills/ 就默认全部加载"，加载一多 LLM 触发判断就开始出错乱。

## 重排目录：21 个并列 → engineering/productivity/misc/personal/in-progress/deprecated

旁边那张目录树图是这次重排的全貌——21 个并列目录被切成了 6 个语义分组。

![20 个 skill 按 4 大类归位](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/mattpocock-skills-update-2026-05-08/mattpocock-skills-update-tree.png)

新结构是这样的：

- `skills/engineering/`——10 个，每天写代码会触发的核心 skill：`diagnose`、`grill-with-docs`、`triage`、`improve-codebase-architecture`、`setup-matt-pocock-skills`、`tdd`、`to-issues`、`to-prd`、`zoom-out`、`prototype`
- `skills/productivity/`——3 个，跨语言场景的沟通护栏：`caveman`、`grill-me`、`write-a-skill`
- `skills/misc/`——4 个，偶尔用一次的工具：`git-guardrails-claude-code`、`migrate-to-shoehorn`、`scaffold-exercises`、`setup-pre-commit`
- `skills/personal/`——2 个，Matt 自己生活流：`edit-article`、`obsidian-vault`
- `skills/in-progress/`——4 个，公开打磨中：`handoff`、`writing-beats`、`writing-fragments`、`writing-shape`
- `skills/deprecated/`——4 个，留在 repo 但不再被 plugin.json 引用：`design-an-interface`、`qa`、`request-refactor-plan`、`ubiquitous-language`

这套切分有三个用意要看穿。首先是把"engineering 必跑"和"misc 偶尔用"分开，能让插件加载顺序更可控——`plugin.json` 里只挂了 engineering + productivity 的 13 个，misc 那 4 个不进默认 plugin，避免污染日常 session 的 skill 触发列表。其次是 `in-progress/` 这个目录——Matt 把"还没写完但已经能用"的 skill 公开放出来，等于在告诉社区"这是 beta，欢迎 PR 也欢迎抄"，把开发动作变成 stream-on-trunk。最后是 `deprecated/`——这 4 个 skill 不删除而是搬走，社区用过它们的人能继续 reference 历史链接，但不会再误导新人。

国内做开源的工程师读到这一段大概率会点头——这就是把 `archive` 概念引入 Claude Code skill 仓的早期实验。我们自己 daily-report 也踩过同样的坑：4 月初 17 个零散 skill 散在 .claude/ 下，命名冲突、加载顺序、版本回滚都没法管。Matt 这次重排的实质是把"我每天怎么写代码"从 *个人偏好* 升级成 *可分发的工程包*——前提是你愿意承担"开始用 deprecated 标签 + 在 plugin.json 里挑名单 + 给 in-progress 接受 PR"这一摊运营动作。

值得单拎出来夸一下的是 `in-progress/`。仓库根目录敢有这个文件夹的开源项目并不多——大多数维护者的本能是"等成熟再 push"，怕被 issue 区追问"这个怎么用"。Matt 反过来，把 4 个还在打磨的 skill（`handoff`、`writing-beats`、`writing-fragments`、`writing-shape`）原样公开，让社区能直接对着 SKILL.md 改、提 PR、提 issue。这不是技术决策，是**社区运营策略**——把"还没写好的东西"暴露出来，等于把"我下一步会做什么"也公开了，社区能围绕这个方向贡献。国内做 awesome-list 或 .cursorrules 仓的同行可以学一下：与其攒一年憋个大版本，不如把 in-progress 摆出来跑日活。

`scripts/` 目录里多了 `link-skills.sh` 和 `list-skills.sh` 两个 shell 脚本——前者把 plugin.json 里列的 skill 软链到 `~/.claude/skills/mattpocock-*/`，后者列出当前仓内全部 skill 名 + 简介供查询。两个脚本都是社区 PR 加进来的，加一起不到 80 行 bash，但解决了"装多个 skill 包之后路径冲突"这个具体问题。这件事跟 plugin.json 配合起来看，能感觉到一个隐形的小生态在成型——*Claude Code skill 包管理*，跟 npm 包管理是同一回事，只是粒度更小、协议更轻。

## 三个新 skill：diagnose、prototype、handoff

11 天里最实质的内容增量在 3 个新 skill。下面这张表是这 11 天里所有"新加 / 改名 / 弃用" 的清单。

![新加 / 改名 / 弃用 11 天清单](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/mattpocock-skills-update-2026-05-08/mattpocock-skills-update-changes.png)

### diagnose——给 bug 一份诊断纪律

`skills/engineering/diagnose/SKILL.md` 是 4 月底新加的。它解决的问题非常具体：Claude Code 上一波"碰到 bug 直接乱猜原因 → 改一行代码 → 跑测试 → 失败 → 再乱猜"的常见失败模式。Matt 把这套流程切成 6 个 phase：reproduce → minimise → hypothesise → instrument → fix → regression-test。最有意思的是 Phase 1 的副标题——*Build a feedback loop*。skill 里这一段写得挺锐：

> This is the skill. Everything else is mechanical. If you have a fast, deterministic, agent-runnable pass/fail signal for the bug, you will find the cause. If you don't have one, no amount of staring at code will save you.

下面给了 10 种构造 feedback loop 的方法：失败测试 → curl 脚本 → CLI fixture diff → 无头浏览器 → 重放 trace → throwaway harness → 属性 / fuzz 循环 → bisection harness → differential loop → 人工兜底脚本。这个清单本身就值得任何在国内带一只 Claude Code agent 的工程师抄一遍——不是因为里面方法新，而是因为它把 *"先建 feedback loop 再 debug"* 的纪律写成了一份 skill 触发词。读者上一次看到这种纪律应该还是 *Pragmatic Programmer* 那本书，不是 AI Coding 教程。

### prototype——抛弃式原型，UI 或终端二选一

`skills/engineering/prototype/SKILL.md` 解决的是"agent 拿到一个模糊需求时该写什么"的问题。它直接把原型分成两个分支：

- *"这个状态 / 业务逻辑感觉对吗"* → 终端跑一个交互式小程序，把 state machine 推过那些纸上推不动的 case
- *"这看起来该长什么样"* → 同一个路由下生成几个**截然不同**的 UI 变体，URL 加个 query param 就能切换

skill 里 6 条规则全是"省事"的反向校准：第一条就是"throwaway from day one, and clearly marked as such"，第二条是"one command to run"，第三条"no persistence by default"。把"原型不能写成产品 v0"这件事从口头规矩沉淀进了 skill。

这件事跟 5/8 同期的 [Addy Osmani agent-skills](https://github.com/addyosmani/agent-skills) 那个 32k⭐ 仓的 `incremental-implementation` skill 是同一脉的——*把工程经验里"老程序员都知道但新工程师老踩坑的部分"批量上写进触发词*。Matt 这版 prototype skill 写得更窄、更利落；Addy 那版更全、更系统。两者结合起来读，是当下"AI Coding 工程文化包"最完整的两份样本。

skill 末尾还有一段被多数人会跳过但其实很关键的话——*Delete or absorb when done*：原型回答完它的问题之后，要么删掉、要么把验证过的决策吸收进真代码，不能让它烂在 repo 里。配套要求是把"这个原型回答了什么问题"显式写在 commit message / ADR / NOTES.md 里。这是把"原型有结论"和"代码改一行就忘"两个常见漏项一次按住——前者是工程纪律的事，后者是上下文管理的事，国内大多数团队的 .cursorrules 都没把这两条写进 prompt。

### handoff——给下一个 agent 写一份交接文档

`skills/in-progress/handoff/SKILL.md` 是 5 月 6 日新加的，目标是把当前对话压缩成下一段 agent session 的接班文档。skill 里有一段挺关键的反话：

> Do not duplicate content already captured in other artifacts (PRDs, plans, ADRs, issues, commits, diffs). Reference them by path or URL instead.

这是把"上下文就是 token、token 就是钱"这件事正面对着 LLM 喊出来。handoff 不是写一份大段总结——它是写一份只列指针的索引，让下一段 session 自己用工具去拉。在动辄 30 万 token 起步的 Claude / Sonnet / Gemini 长上下文时代，这种做法看起来反直觉，但实际更省钱、更准——因为长上下文的"在场不等于被关注"，明确指向反而能让下一段 agent 把注意力留给真正重要的几页 ADR。

handoff skill 的实现细节也巧妙：让 agent 把交接文档写进 `mktemp -t handoff-XXXXXX.md` 的临时路径，再让下一段 session 显式读它。这绕开了一个常见反模式——把整段对话直接 paste 进新 session 的第一条 prompt——临时文件让交接 artifact 可被多人 review、可被 git ignore、可被反复迭代。这是"agent 间协作"的低成本工程方案，没有引入新协议，没有起新服务，只用了 `mktemp` 这个 1971 年就存在的 Unix 工具。

## 改名：github-triage → triage，四个旧 skill 退役

剩下的改动都是收口动作。

`github-triage` 改名成了 `triage`，不再硬绑 GitHub。新的 `setup-matt-pocock-skills` skill 在初始化阶段会问你三个问题——*issue tracker 是 GitHub 还是 GitLab 还是本地 markdown？triage 标签用啥词？CONTEXT.md 和 ADR 放哪？*——把 11 天前那种"默认 GitHub、其他自己改"的硬假设松开了。这是社区压力的产物：4/29 那次 commit `Add GitLab as a first-class issue-tracker option` 就是合并的 PR。

这次 setup 流程的设计也值得注意：skill description 里写明 `disable-model-invocation: true`，意思是这个 skill **不能被 LLM 自己加载**，只能由用户显式 `/setup-matt-pocock-skills` 触发。这是给"配置类 skill"加的一道护栏——配置类操作如果让 LLM 自动判断是否触发，会出现"我刚开始写 PRD 它先把项目 setup 改了一遍"这种尴尬。把它锁成纯人工触发，是 11 天前那批 skill 里没有的一个新模式，国内做配置向导类 skill 的同行可以直接抄这个 frontmatter 字段。

`design-an-interface`、`qa`、`request-refactor-plan`、`ubiquitous-language` 这 4 个 skill 4/26 还在主目录，5/8 全挪进了 `deprecated/`。Matt 在 README 里没专门解释为什么——但只看 commit log 能拼出来：`a32ebfb Remove deprecated triage-issue skill`、`7afa86d migrate engineering skills to vague prose`这两条提示出，他在做一次集中减法，把"和别的 skill 触发条件重叠"或"实际没人用"的几个砍下来。`ubiquitous-language` 这个名字一看就懂——它的功能在新版 `grill-with-docs` 里以"shared CONTEXT.md + ADR"的形态吸收了，独立 skill 没必要再留。`design-an-interface` 跟 `prototype` 的 UI 分支高度重合；`qa` 跟 `triage` 在工作流上撞车；`request-refactor-plan` 被 `to-prd + to-issues` 这条链替代——四个 skill 退役都有具体的合并对象，不是一刀切。

不删除而是挪去 deprecated 这个动作，对国内还在管自家 .claude/ 仓的开发者来说是一个值得抄的细节。删了是 commit history 才能找到；挪进 deprecated/ 是当前 tree 就能看到，老用户能继续按旧路径 `git submodule update --remote` 拿到，新用户在 README 里又看不到，互不影响。`deprecated/` 目录内还有一份独立 README.md，挂着 4 个旧 skill 的迁移指引——告诉读者"这个 skill 改用什么"。这就是软件工程里 *deprecation pathway* 的最小可行版本，比 npm 的 deprecation warning 还轻。

## 4/26 那批选 5 个，还能不能照抄

4 月 26 日那篇我们推荐了 5 个先抄的 skill：`tdd` / `git-guardrails-claude-code` / `grill-me` / `to-prd` / `write-a-skill`。11 天后的状态怎么样？

- **`tdd`**——主路径没变，仍然是 vertical-slice 红绿循环。位置从根目录 `tdd/` 挪到了 `skills/engineering/tdd/`，npx 命令也跟着改成 `npx skills@latest add mattpocock/skills/skills/engineering/tdd`，更长但对应得上目录树。继续推荐。
- **`git-guardrails-claude-code`**——位置挪到 `skills/misc/`，被踢出了 default plugin.json 加载列表。Matt 的判断大概是"它太好替代了，每个团队 PreToolUse 黑名单都不一样，没必要默认装"。我倾向同意，但国内开发者刚开始用 Claude Code 的话还是值得手装一次——它是"Claude 偷偷 git push --force"这件事最便宜的一道墙。
- **`grill-me`**——保留，挪到 `skills/productivity/`。同时拆了一个 `grill-with-docs` 兄弟出来——专门用来 grill 代码改动、并把对话沉淀进 CONTEXT.md + ADR。grill-me 走非代码场景，grill-with-docs 走代码场景，分流之后比 4/26 那种"什么都用 grill-me"更准。
- **`to-prd`**——保留，挪到 `skills/engineering/`。语义没变：把当前对话的内容综合成 PRD 提 GitHub issue（或 GitLab、或本地 markdown），不再额外 grill 你。配合 `to-issues` 做后续 PRD → issue 拆解，仍然是 Matt 这套 skill 的重头戏。
- **`write-a-skill`**——保留，挪到 `skills/productivity/`。这次重排之后特别值得抄一遍——它本身就是"如何写出能像 mattpocock/skills 这样可分发的 skill"的元 skill，11 天前抄它得到一份基础结构，今天抄它能学到 plugin.json 怎么配。

如果时间紧只挑 3 个加进自己 `.claude/skills/`，我会换一组：`tdd` + `diagnose` + `grill-with-docs`。理由是这三个的触发条件覆盖了"写新功能 / 修旧 bug / 不知道该写啥"三个最高频场景，而且互相之间的 description 触发词错开得不严重，不会一个 prompt 里加载 5 个 skill 把 context window 吃满。

具体说，`tdd` 触发词集中在 "TDD" / "red-green-refactor" / "test-first"；`diagnose` 集中在 "debug" / "bug" / "broken" / "throwing" / "performance regression"；`grill-with-docs` 集中在 "before I start" / "is this design right" / "challenge my plan"。三个 skill 把"开发会话"的三种典型起点切干净——你说"我想做个 X"它进 grill-with-docs；你说"X 出错了"它进 diagnose；你说"我要写测试"它进 tdd。这种触发条件设计对国内做 .cursorrules 的同行特别值得抄一遍——很多国产 rule 仓的失败原因不是规则不够好，是触发条件太模糊，LLM 拿不准什么时候该用、就一律不用。

## 国内开发者怎么搬，3 条路径仍然可用

11 天前总结的 3 条搬运路径基本仍然成立，加几条 5/8 的注脚。

第一条路径：`npx skills@latest add mattpocock/skills`。简化最大——不需要再挑哪几个 skill，整个插件按 plugin.json 一次性挂上。`skills` 这条 CLI 是 Anthropic 生态的第三方安装器，国内 npm 走淘宝镜像 / cnpm 都没问题。装完之后跑 `/setup-matt-pocock-skills`，按提示选 issue tracker（GitHub / GitLab / 本地 markdown）、triage 标签、CONTEXT.md 路径——这三个交互回答会被写进项目的 `AGENTS.md` 或 `CLAUDE.md`，之后的 to-prd / to-issues / triage 调用都从这里读。

第二条路径：`git clone` 整套。`cd ~/.claude/skills/ && git clone https://github.com/mattpocock/skills.git mattpocock`。国内连不通时换 `gitclone.com` 镜像。clone 之后插件不会自动启用——Claude Code 仍按 description 字段触发，但少了 plugin.json 那一组 enable/disable 的整体开关，灵活度高一点、收敛性低一点。

第三条路径：`git submodule add`。最适合"想跟 Matt 走"的开发者——`git submodule update --remote` 一次拿到这次重排 + 新的 3 个 skill；下次 Matt 再调，再 update 一次就行。注意 4/26 之后他在 1 周内合并了 90+ 号 PR，submodule 更新频次比 4 月那一阵明显高，建议每周 `update --remote` 一次而不是日级别。

补一条 5/8 才有的细节：如果你已经把 4/26 那版 21 个 skill 的旧路径写进了 `.claude/settings.json` 或别的脚本（例如 `.claude/skills/mattpocock/tdd/`），这次重排之后老路径全部失效——`tdd` 现在是 `mattpocock/skills/engineering/tdd/`。submodule 更新前先 `grep -r 'mattpocock/<old-name>' ~/.claude/` 一遍，把硬路径换成新路径，免得插件加载时整个 mattpocock 段全 silent fail。

## 跟 5/8 同期的 addyosmani/agent-skills 怎么对位

5/8 同一天，[addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) 32,728⭐——Google Director Addy Osmani 也在做"把工程文化打包成 skill"这件事。我们另外写了一篇专题。这两个仓库放一起对比能看出非常清晰的差异。

| 维度 | mattpocock/skills | addyosmani/agent-skills |
|---|---|---|
| 作者背景 | TypeScript 教师，独立开发者 | Google Director（Gemini + Cloud） |
| 首发日期 | 2026-02-03 | 2026-02-15 |
| 当前⭐ | 65,192 | 32,728 |
| skill 数量 | 20 + 4 in-progress + 4 deprecated | 20 |
| 哲学定位 | 个人最佳实践仓 | 工程文化手册 |
| 触发风格 | description 触发词，按 LLM 自由判断 | 7 条斜杠命令切六段生命周期 |
| 写作笔调 | 轻松、个性化、book quote 多 | 严肃、过程式、Beyoncé Rule |
| in-progress 公开 | 有（4 个）| 无，等成熟才放出来 |
| 配套分发 | aihero.dev newsletter（约 6 万订）| addyosmani.com 博客 + HN |
| 适合什么场景 | 一个人 / 小团队，自己挑工作流 | 大团队，统一约定 |

不要二选一——大多数国内一线 AI Coding 开发者两个都该装。Matt 这套适合给*个体*装，Addy 那套适合给*团队 .cursorrules / claude.md*装。前者管"我自己每天怎么写"，后者管"我们这群人协作时怎么不踩同一个坑"。

## 11 天 commit log 当作运营日志读

把 4 月 26 日到 5 月 7 日 这段 commit log 拉一遍，能看到一条挺典型的开源项目"破圈后忙碌期"曲线。4 月 26 日当天 0 commit；4 月 27 日恢复后开始密集 push；4 月 28 日单日 12 个 commit，包含 `Add setup-matt-pocock-skills`、`Rename github-triage to triage`、`Migrate engineering skills to vague prose` 三件大事；4 月 29-30 日 GitLab 支持 + 出范围声明；5 月 1-5 日相对安静；5 月 6-7 日又一次冲刺，handoff、prototype、新 README badge 一起上。

读这段 log 你能感觉到 Matt 用了一种很常见的运营节奏——*破圈后立刻整顿、然后稳住、再上新东西*。4/28 那天的密集 commit 是把"原来私人风格"系统化，确保 65k⭐ 涌进来的人看到的不是临时的乱仓；5/6-5/7 那次冲刺是把 in-progress skill 公开引流，给社区一个"这个项目还在动"的信号。中间那几天的安静期不是放假，是 cohort 课程 *Claude Code for Real Engineers* 在跑，他在拿付费课的反馈调整。

PR 流量也值得记一下。4/28 一天合并的 PR 编号已经到 #90，5/8 issue 编号到 #153——11 天里大约 60 个 PR + issue 流过这个仓库。Matt 的处理风格是 *快合并 + 轻 review*：很多 PR 看 commit message 是"Merge pull request #XX from someuser/branch-name"——没有改动 review、没有 CI 检查（仓库根目录看不到 .github/workflows）、没有冗长的反馈。这种节奏适合 markdown-only 的 skill 仓——文档冲突很容易看明白，不需要重 CI；但如果国内同行做的是带 shell 脚本的 skill 仓，这种"直接合"的节奏就要打折，建议加个最小 lint。

## 看点：未来一周

- **Matt 会不会出 release**——4/26 仓库还没有任何 GitHub Release，5/8 issue #147 已经在讨论"为重大变更打 release"。如果他这周开始用 GitHub release 标记里程碑，意味着他承认这个 repo 已经是一个产品而不是私人笔记本。这件事 1 个月内会发生，建议 watch 一下。
- **国内会不会出中文 fork 插件**——MIT 协议、纯 markdown，翻译加适配 OpenClaw / Codex CLI / Cursor 的本地化版本，是一个 7-10 天就能落地的项目。这种"海外热、国内尚无中文版"的窗口对个人开发者特别友好——不需要做新东西，把现成的好东西本地化一下，就有现成读者群。
- **Anthropic 会不会出官方 skill marketplace**——4/26 我们就猜过；5/8 看 mattpocock + addyosmani 的两个 6.5 万⭐ / 3.2 万⭐ 邻居，加上 ComposioHQ/awesome-claude-skills、VoltAgent/awesome-agent-skills 这两个 awesome-list 已经突破 1k⭐，Anthropic 把这事正式做成有官方 directory 的可能性继续上升。`/setup-matt-pocock-skills` 这种"装好就要做配置向导"的 skill 越多，对官方做 marketplace 的需求就越实——因为目前 plugin.json 还没有 metadata schema 标准。

- **国内一线大厂的 .cursorrules 仓会不会跟进 plugin 化**——飞书、Trae、CodeBuddy、灵犀这几家如果做内部 .claude/ 仓，这种"plugin.json 选清单"的做法是最直接能抄的。社区 awesome-cursorrules 已经积累上百份内部 rule，下一步的瓶颈正是分发——抄 plugin.json 模型能让多份 rule 互不干扰地共存。

## 一句收尾

11 天前 mattpocock/skills 是"我把私人 .claude 笔记本开源了"；11 天后它是"我维护了一个 6.5 万⭐ 的 Claude Code 插件"。中间发生的事情没有什么神秘配方——重排目录、加 plugin.json、合 90 几个 PR、把"还没写完的 skill"也敢公开放出来。

这件事对国内一线 AI Coding 同行的现实意义是：你不需要建一个新模型、新 IDE、新框架——把自己每天用 Claude Code 的私房经验整理一遍、按"engineering / productivity / misc / personal / in-progress / deprecated"切清楚、配一份 plugin.json 就能挂，已经是一个能进 GitHub Trending 的可分发产品。Matt 这套 skill 的内容并不特殊——TDD、grill、prototype、diagnose 都是任何资深工程师每天都在做的事；特殊的是他把这些"嘴上功夫"沉淀成 markdown 触发词、敢公开、敢迭代、敢挪 deprecated。这种"把工程纪律打包成 agent 能机械执行的 skill"的做法，是 2026 年这一波 AI Coding 工具竞争里最便宜的护城河——不靠模型、不靠 IDE，靠的是把自己写代码的肌肉记忆原样搬出来。

国内做 AI Coding 工具的同行如果想做出有人愿意 fork 的 skill 仓，这 11 天的 commit log 比任何运营手册都值得抄一遍。

---

*所有数字来自 2026-05-08 凌晨 04:00 北京时间的 GitHub API 实查——`gh api repos/mattpocock/skills` 返回 `stargazers_count: 65192 / forks_count: 5616 / open_issues_count: 24 / subscribers_count: 489 / size: 93 KB / pushed_at: 2026-05-07T06:34:14Z`。4/26 那次基线数字来自当日早 20:03 UTC 的同一个 endpoint。*
