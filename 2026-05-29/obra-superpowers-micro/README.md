# Superpowers框架揭秘：如何让Claude Code等Agent自主工作90分钟？

> ai-daily · 2026 年 5 月 28 日 21:42 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/obra-superpowers-micro/head.png)

旧金山某个凌晨，Jesse Vincent 盯着终端。Claude Code 刚跑完一次完整的功能迭代——不是他写的代码，是 AI agent 自己读 spec、写测试、提交、review、再来一轮，整整 90 分钟没向他提一个问题。他截了张图，发到 Discord。配文就一句话：“This thing just worked for an hour and a half without me.”

后来他把这套东西打包开源，名字起得毫不谦虚：**Superpowers**。

![obra/superpowers — An agentic skills framework & software development methodolog](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/obra-superpowers-micro/content-1.png)

**不是让人类变超人，是让 coding agent 终于有了工作纪律。**

## 一个被 8 个主流 coding agent 同时接住的技能框架

Superpowers 在 2026 年 5 月初登上 GitHub Trending，一周之内被 Claude Code、Cursor、Gemini CLI、GitHub Copilot CLI、Codex CLI、Codex App、Factory Droid、OpenCode 八个平台原生接入。这个适配列表本身就是一个信号——它解决的已经不是“某个 agent 怎么用”的问题，而是“所有 agent 在写代码这件事上缺了什么”的问题。

缺的东西说来也简单：**一套强制性的工作流**。Jesse 在设计里明确写了“Mandatory workflows, not suggestions”。agent 在动手写代码之前，必须先退一步问开发者“你到底想干什么”，然后吐出一份可分块阅读的 spec，等人类点头，再生成一份细到文件路径和验证步骤的实施计划，最后才进入执行环节。执行也不是一次性梭哈，而是拆成 2-5 分钟一个的 bite-sized task，逐个派给独立的 subagent，每个任务过两道 review——先对 spec，再看代码质量。



![Superpowers 核心工作流示意，从 brainstorming 到 finishing a development branch 的七个环节形成闭环](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/obra-superpowers-micro/schematic-1.png)



让我愣神的是那道“两道 review”的设计：第一道叫 spec compliance review，第二道叫 code quality review。在传统软件工程里，这两件事通常是同一个人或同一个 PR review 过程里混着做的。但 Jesse 把它们拆开了，因为他假设 subagent 是“enthusiastic junior engineer with poor taste, no judgement, no project context, and an aversion to testing”——一个热情高涨但品味堪忧、没有判断力、不了解项目上下文、还不爱写测试的初级工程师。让这种角色先过 spec 合规性检查，再过代码质量关，等于给每个 subagent 配了两个自动化的 code reviewer。

这套假设的残酷性在于，它几乎精准描述了当前所有 coding agent 的真实水平。

**Agent 不缺智商，缺的是“先停一下，想清楚再动手”的纪律。**

## 藏在 YAGNI 和 RED-GREEN-REFACTOR 里的产品哲学

Superpowers 的核心技能库只有十几个文件，但每个文件都是一条强制规则。`test-driven-development` 技能明确要求 RED-GREEN-REFACTOR 循环：先写一个会失败的测试，确保它真的失败，然后写最小量的代码让测试用，确认用后提交。如果 agent 在写测试之前就写了做到代码，这套规则会要求它删掉重来。

另一条规则是 YAGNI（You Aren't Gonna Need It），压在 `writing-plans` 技能里。计划阶段 agent 必须确认每一项功能都是当前 spec 需要的，而不是“以后可能用到”。这和 DRY（Don't Repeat Yourself）一起构成了 Superpowers 的设计约束三角：TDD 保证正确性，YAGNI 防止过度工程，DRY 控制复杂度。



![RED-GREEN-REFACTOR 循环的三个阶段，红阶段写失败测试，绿阶段写最小做到，重构阶段优化代码结构](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/obra-superpowers-micro/schematic-2.png)



Jesse 在技能库里还放了一个容易被忽略的东西：`systematic-debugging`。这是一个四阶段根因分析流程，包含 root-cause-tracing、defense-in-depth 和 condition-based-waiting 三种技术。绝大多数 agent 在遇到 bug 时的第一反应是“改一下试试”，Superpowers 强制它先定位根因，再做纵深防御，最后只在条件满足时才继续。这相当于把 SRE 的事故处理手册塞进了 agent 的 prompt 里。

还有一个细节值得展开：Superpowers 的 `using-git-worktrees` 技能会在设计被批准后自动创建隔离的 worktree 和分支，跑一遍项目 setup，验证测试基线是干净的。这个步骤在传统开发里通常靠 CI 流水线来做，但 Superpowers 把它前置到了 agent 开始写代码之前。如果基线测试就过不了，agent 不会继续往下走。Jesse 的设计逻辑很清楚：在一个不干净的基线上跑 TDD，等于在流沙上盖房子。



![Superpowers 技能库分类，Testing / Debugging / Collaboration / Meta 四大类，每个技能之间的触发依赖关系](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/obra-superpowers-micro/schematic-3.png)



从产品哲学的角度看，Superpowers 实际上在做一件反直觉的事：它不是在给 agent 增加能力，而是在给 agent 增加约束。每一条技能规则都在限制 agent 的自由度——不能直接写代码，不能跳过测试，不能忽略 review，不能在主分支上直接操作。但恰恰是这些约束，让 Claude 能够“autonomously work for a couple hours at a time without deviating from the plan”。

Jesse Vincent 是谁？他是 Perl 6 项目早期的核心贡献者，后来做了 task management 工具 Hiveminder，再后来创办了键盘公司 Keyboardio。这个人有一个贯穿职业生涯的特点：他总在别人忙着堆功能的时候，花时间设计“怎么把事情做对”的系统。Superpowers 是这种思维方式的延续——当整个行业都在卷谁的 agent 代码生成更快、上下文窗口更大时，他扔出来的是一套纪律手册。

这套纪律手册目前以 MIT 协议开源，托管在 `obra/superpowers` 仓库。Jesse 在 README 里留了一行：“If Superpowers has helped you do stuff that makes money and you are so inclined, I'd greatly appreciate it if you'd consider sponsoring my opensource work.”没有 SaaS 定价页，没有企业版，就是一个 PayPal 式的赞助链接。Prime Radiant 这家公司（Jesse 是其中一员）似乎也没打算把它包装成商业产品，至少在 2026 年 5 月这个时间点还没看到任何商业化动作。

但这件事的行业意义不在商业化。它回答了一个 2025-2026 年所有 AI coding tool 用户都在问的问题：为什么我的 agent 一开始跑得挺快，三五个任务之后就偏离方向、写出没人要的功能、或者开始编造不存在的 API？答案不是模型不够强，而是没有工作流约束。Superpowers 的出现等于给整个行业提供了一个“agent 自律”的参考做到。

更妙的是，因为它只是一个技能框架加上一套初始指令，安装成本极低。Claude Code 用户一条 `/plugin install superpowers@claude-plugins-official` 就能用，Cursor 用户搜一下 marketplace 就能装，Gemini CLI 一条 extension install 命令搞定。这个极低的接入门槛意味着 Superpowers 的扩散速度可能比它本身的设计迭代还要快。

Jesse 在 Discord 里说，社区贡献的新技能“一般不接受”，而且任何更新都必须跨所有八个 agent 平台正常工作。这条限制看似保守，实际上是在保护 Superpowers 的核心价值——它不是一个随意拼插的 skill 超市，而是一套经过验证的、强制性的开发纪律。一旦放开社区技能贡献，很容易变成“什么都能装，什么都不保证”的松散集合，那和 agent 原生自带的那堆时灵时不灵的 prompt 模板就没什么区别了。

所以 Superpowers 真正的野心可能藏在它的名字里：不是给 agent 一堆花哨的超能力，而是给 agent 一个像人类资深工程师那样的工作习惯——动手之前先想清楚，写完代码先让它红着失败一次，改完 bug 先验证再宣布胜利。这些习惯在人类工程师身上需要数年时间培养，而 Superpowers 试图把它们压缩成一堆 `.md` 技能文件，塞进 agent 的 system prompt。

至于效果如何，Jesse 那张“90 分钟无人干预”的截图已经是一个相当不错的广告了。

---

## 参考来源
- https://github.com/obra/superpowers

#An #AI #科技
