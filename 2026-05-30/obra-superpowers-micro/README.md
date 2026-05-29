# 兼容Claude Code与Cursor等8大代理：Superpowers如何强制执行TDD开发？

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/obra-superpowers-micro/head.png)

“先写测试，再写代码”这句话，过去二十年里被无数技术博主写烂了。但 2026 年的开发者打开 Claude Code 或 Cursor，面对一个空项目时，99% 的人依然选择从 `main.py` 开始敲，而不是从 `test_main.py`。

Jesse Vincent 显然被这 99% 气得不轻。

![obra/superpowers — An agentic skills framework & software development methodolog](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/obra-superpowers-micro/content-1.png)

他放出的 **Superpowers**，表面上是个给 coding agent 用的技能框架，骨子里是一套强制执行 TDD 的软件方法论——而且它的目标用户不是人，是 AI。你不需要自己记住“先写测试”，你的 agent 会自己查 skill 列表，然后拒绝在测试失败之前写任何一行做到代码。

**这不是建议，是硬约束。agent 如果不照做，skill 会直接把它拉回正轨。**



![Superpowers 核心循环——brainstorming → design → plan → subagent-task → TDD-red-green-refactor → review → finish，每一环都标注了对应的 skill 名称](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/obra-superpowers-micro/schematic-1.png)



---

## 一个 200 行 README，凭什么同时兼容 8 个 coding agent？

先看一组数字：Superpowers 的 Quickstart 章节列出了 8 个受支持的 harness——**Claude Code、Codex CLI、Codex App、Factory Droid、Gemini CLI、OpenCode、Cursor、GitHub Copilot CLI**。从 Anthropic 旗舰到 OpenAI 新贵，从 Google 实验品到微软老牌工具，一网打尽。

但更有意思的是安装方式的高度碎片化。Claude Code 走的是 Anthropic 官方插件市场，一条 `/plugin install superpowers@claude-plugins-official` 就完事。OpenCode 用户却得手动 fetch 一个 raw GitHub URL 里的 INSTALL.md。Gemini CLI 用 `gemini extensions install`，Cursor 用 `/add-plugin`，GitHub Copilot CLI 用 `copilot plugin` 前缀。每一套 CLI 的工具链哲学都不一样，Superpowers 硬是给每一条都写了一份安装指南。

这暴露了一个尴尬的现实：**2026 年的 coding agent 生态，比 2016 年的 JavaScript 框架战国时代还混乱。** 每个 agent 都有自己的插件系统、自己的 marketplace 规则、自己的 CLI 动词。Superpowers 与其说是一个“跨平台框架”，不如说是一次对产业碎片化的无声抗议——Jesse 得为 8 个不同的入口维护同一套 skill 定义，而社区贡献的新 skill 还必须“在所有支持的 coding agent 上都能跑”（原文说的很直白：`any updates to skills must work across all of the coding agents we support`）。



![8 个 coding agent 的安装命令对照表，突出每个工具的 CLI 动词差异——/plugin install / gemini extensions install / copilot plugin / droid plugin / fetch raw URL](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/obra-superpowers-micro/schematic-2.png)



---

## TDD 执行到什么程度？连“看到测试失败”都是强制步骤

Superpowers 里最让我愣神的是 `test-driven-development` 这个 skill 的描述。它不是泛泛地说“请使用 TDD”，而是把 RED-GREEN-REFACTOR 拆成了**可验证的动作序列**：

1. 写一个会失败的测试
2. **亲眼看着它失败**（原文：watch it fail）
3. 写刚好够用的最小代码
4. **亲眼看着它用**（watch it pass）
5. commit

第五条是真正的杀招——**“删除在测试之前写的任何代码”**。这不是建议，这是 agent 执行计划时会主动执行的清理动作。如果你让 agent 先写了做到再补测试，它会在 review 阶段被 `requesting-code-review` skill 标记为 critical issue，然后阻塞进度。

Vincent 在设计这套系统时显然对人性（或者说 AI 的“惰性”）有极深的认知。agent 跟人一样，面对模糊指令时会走捷径。Superpowers 的对策是把捷径全部堵死：`systematic-debugging` 要求四阶段根因分析（root-cause-tracing → defense-in-depth → condition-based-waiting → verification-before-completion），`finishing-a-development-branch` 要求在合并前验证所有测试用，`subagent-driven-development` 甚至给每个子任务分配了**两阶段 review**——先检查 spec 合规性，再检查代码质量。



![subagent-driven-development 的两阶段 review 流程——spec compliance check → code quality check，critical issue 会 block 进度](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/obra-superpowers-micro/schematic-3.png)



值得一提的是，`subagent-driven-development` 和 `executing-plans` 是两个并列的选项。前者是“每个任务派一个全新子 agent，独立 review”，后者是“批量执行，人工 checkpoint”。这说明 Vincent 很清楚不同场景下的取舍——子 agent 模式隔离性好但 token 消耗大，批量模式快但需要人盯着。这个设计细节让我觉得 Superpowers 不是实验室里的白皮书，是真有人在生产环境里踩过坑之后才打磨出来的。

至于 `using-git-worktrees`，它会在 design 批准后自动创建隔离工作区、切新分支、跑项目初始化、验证测试基线。这一步看起来像基础设施，但放在 Superpowers 的流程里意义重大：**它确保了每个 feature 的起点是干净的。** 没有什么比 agent 在前一个 feature 留下的全局状态污染更让人头疼了。

---

Jesse Vincent 是谁？Prime Radiant 这家公司又是什么来头？GitHub 上的信息很有限——我们知道 Vincent 是 Superpowers 的主要作者，Prime Radiant 是他和团队所在的组织，项目以 MIT 协议开源，Discord 社区已经建立起来了。但更让我好奇的是这个项目的商业逻辑：README 里只有一段赞助请求（“如果 Superpowers 帮你赚了钱，考虑赞助我的开源工作”），没有任何 SaaS 定价页或 enterprise tier 的暗示。

一个覆盖 8 个主流 coding agent、强制执行 TDD 和结构化开发流程的框架，居然是完全免费的。这要么是纯粹的理想主义，要么是 Prime Radiant 在用 Superpowers 当某种更大战略的入口——比如卖咨询服务，或者为后续的付费 skill marketplace 铺路。目前没有任何证据指向后者，但我会持续盯着他们的 Discord 公告。

**对了，如果你准备让 Claude 自己装 Superpowers，记得先确认它能不能看到插件市场——别像某个 Reddit 用户那样，折腾了一下午才发现用的是企业版，插件市场被 IT 关了。**

---

## 参考来源
- https://github.com/obra/superpowers
- https://github.com/obra/superpowers#quickstart
- https://github.com/obra/superpowers#installation
- https://github.com/obra/superpowers#how-it-works
- https://github.com/obra/superpowers#the-basic-workflow
- https://github.com/obra/superpowers#whats-inside

#An #AI #科技
