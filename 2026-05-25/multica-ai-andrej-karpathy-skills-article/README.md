# 卡帕西怒批AI编程：Claude Code把100行需求写成1000行？一个文件解决

> ai-daily · 2026 年 5 月 25 日 08:08 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/multica-ai-andrej-karpathy-skills-article/head.png)

凌晨 2:47，一个开发者盯着终端里 Claude Code 刚刚提交的 diff，表情从满意变成了困惑。他让 AI 修一个登录页面的密码校验逻辑——三行正则表达式的事。Claude Code 不仅改了那三行，还顺手"优化"了相邻的错误提示文案，删掉了一段它觉得"没用"的注释（那段注释恰好解释了为什么不能用 bcrypt 而要用 argon2），并且把一个单文件的 200 行工具函数重构成了三个文件、一个抽象基类和一套"可扩展的验证器架构"。1000 行代码，只为了一个本来 100 行就能解决的需求。

这不是虚构场景。2025 年以来，随着 Claude Code、Cursor、GitHub Copilot Chat 等 AI 编程工具从"自动补全"进化到"自主执行多文件重构"，开发者们发现了一个诡异的现象：AI 写代码的速度是人类的 10 倍，但制造技术债务的速度可能是 50 倍。你今天下午让它修一个 bug，明天早上醒来发现它重构了半个项目——而且重构的理由是"这样更灵活"。

![multica-ai/andrej-karpathy-skills — A single CLAUDE.md file to improve Claude Co](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/multica-ai-andrej-karpathy-skills-article/content-1.png)

Andrej Karpathy，前特斯拉 AI 总监、OpenAI 创始成员，2025 年 3 月在社交媒体上发了一条广为流传的帖子，精准点出了这个问题。他说，LLM 在编程时"会替你做出错误假设，然后直接按这个假设一路狂奔，从不检查"，它们"不管理自己的困惑，不寻求澄清，不暴露矛盾，不展示权衡，不在应该 push back 的时候 push back"。更致命的是，它们"真的非常喜欢过度复杂化代码和 API，膨胀抽象层，不清理死代码……用 1000 行做到一个 100 行就能搞定的膨胀架构"。

**AI 编程最大的敌人不是能力不够，而是它太想"帮忙"了。**

## 一个文件能治好 AI 的"过度热心"吗？multica-ai 扔出了 CLAUDE.md

2025 年 5 月初，一个名为 multica-ai 的团队在 GitHub 上发布了一个仓库：`andrej-karpathy-skills`。仓库的内容极度简洁——核心只有一个文件，`CLAUDE.md`。但这个文件在发布后迅速登上 GitHub Trending，因为它试图用一种极其轻量的方式解决上述问题：用四个原则，驯服 AI 编程助手那种"热心但毛手毛脚"的毛病。

multica-ai 本身是一家做开源编程 agent 管理平台的公司，旗下产品 Multica 是一个"可复用技能的编程 agent 运行和管理平台"。这次发布的 `andrej-karpathy-skills` 仓库，本质上是将 Karpathy 对 LLM 编程缺陷的观察，翻译成了一套可执行的指导原则，直接嵌入到 Claude Code 的 `CLAUDE.md` 系统指令中。



![CLAUDE.md 文件结构示意，四个原则以卡片形式排列——Think Before Coding / Simplicity First / Surgical Changes / Goal-Driven Execution，每个卡片下方标注其对应的 LLM 缺陷](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/multica-ai-andrej-karpathy-skills-article/schematic-1.png)



仓库提供了两种安装方式。一种是 Claude Code 插件形式（推荐方式），用户在 Claude Code 内先添加 marketplace，然后一键安装：

```
/plugin marketplace add forrestchang/andrej-karpathy-skills
/plugin install andrej-karpathy-skills@karpathy-skills
```

另一种是传统的 `CLAUDE.md` 文件方式，直接 curl 下载到项目根目录。新项目用一条命令创建，已有项目则追加到现有 `CLAUDE.md` 末尾。此外，仓库还贴心地包含了一个 Cursor 项目规则文件（`.cursor/rules/karpathy-guidelines.mdc`），让 Cursor 用户也能使用同一套指导原则。

这个仓库的 license 是 MIT，完全开源。multica-ai 的创始人 Jiayuan（据其 X 账号 `@jiayuan_jy`）在项目 README 中写道："A single CLAUDE.md file to improve Claude Code behavior, derived from Andrej Karpathy's observations on LLM coding pitfalls."

（值得一提的是，multica-ai 本身是一个 2025 年新起的开源平台，定位是"运行和管理编程 agent 并搭配可复用技能"。这次的 `karpathy-skills` 仓库既可以看作是一个独立的工具，也可以看作是 Multica 平台生态的一个"技能包"示例——它展示了一个好的 agent 指令集应该长什么样。）

## 四个原则拆解：这不是哲学，是给 AI 戴上的"紧箍咒"

multica-ai 提炼出的四个原则，每一个都精准对应 Karpathy 观察到的某类 LLM 编程缺陷。这套原则的设计逻辑不是"建议 AI 做得更好"，而是"强制 AI 在犯错之前停下来"。

**原则一：Think Before Coding（先想再写）**

这个原则要解决的是 LLM "默默做出错误假设然后一路狂奔"的问题。具体指令包括：明确陈述假设——如果不确定，宁可问也不要猜；呈现多种解释——不要在存在歧义时悄悄选一个；在必要时 push back——如果有更简单的方案，直接说出来；困惑时就停下来——说出哪里不清楚并请求澄清。

我看到这条的时候第一反应是：这不就是让 AI 学会说"我不确定"吗？在人类工程师的沟通中，"我不确定，让我确认一下"是最基本的职业素养。但 LLM 的默认行为模式是"永远给答案"——它被训练成不能说自己不知道。这套指令试图用系统 prompt 的方式打破这个训练惯性，让 AI 在编程场景下学会"管理自己的不确定"。

**原则二：Simplicity First（简单优先）**

这是对"1000 行做到 100 行功能"的直接反击。指令明确要求：不添加需求之外的功能；不为单次使用的代码创建抽象层；不添加没有被要求的"灵活性"或"可配置性"；不为不可能发生的场景写错误处理；如果 200 行可以压缩到 50 行，重写它。

判断标准也很直接："一个资深工程师会不会觉得这段代码过于复杂？如果是，简化它。"



![Simplicity First 原则的执行流程对比——左侧是 AI 默认行为的"需求→加抽象层→加配置项→加错误处理→1000 行"，右侧是施加原则后的"需求→最小做到→50 行"，中间用红色标注"被砍掉的部分"](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/multica-ai-andrej-karpathy-skills-article/schematic-2.png)



这个原则最狠的地方在于，它直接否定了 AI 最爱的"万一将来要用呢"（YAGNI 原则的反面）。LLM 在生成代码时有一种强烈的倾向：为每一种理论上的可能性预留扩展点。但实际工程中，这些预留 90% 永远不会被用到，却让代码的维护成本翻倍。

**原则三：Surgical Changes（外科手术式修改）**

这是四个原则里我最觉得"说到痛处"的一个。LLM 在编辑已有代码时，有一种难以抑制的冲动——修改相邻代码的格式、"优化"它觉得不够好的注释、重构"看起来不顺眼"的变量名。这些行为与用户的实际需求完全正交，但 LLM 就是控制不住。

Surgical Changes 原则的指令是：不要"改进"相邻的代码、注释或格式；不要重构没有坏的东西；匹配已有代码风格，即使你自己更偏好另一种写法；如果注意到不相关的死代码，提出来——但不要自己删掉。当你的修改产生了"孤儿"（orphans）——即因为你的改动而变得不再被使用的 import / 变量 / 函数——只清理你自己的修改导致的无用代码，不要碰之前就存在的死代码。

判断标准：**每一行被修改的代码，都必须能直接追溯到用户的需求。**

**AI 编程助手的 diff 应该是手术刀，不是推土机。**

**原则四：Goal-Driven Execution（目标驱动执行）**

这是 Karpathy 原帖中最核心的洞见之一——"LLM 非常擅长循环直到达成特定目标……不要告诉它做什么，给它成功标准然后看着它跑。" multica-ai 将这个洞察转化为了一套"指令→目标"的转换规则。

具体操作是：把祈使句的任务描述转化为可验证的目标。比如，"添加验证"转化成"为无效输入编写测试，然后让这些测试用"；"修复 bug"转化成"写一个能复现它的测试，然后让测试用"；"重构 X"转化成"确保重构前后测试全部用"。对于多步骤任务，要求 AI 先陈述一个简短的执行计划，每一步后面跟着验证标准。



![Goal-Driven Execution 的转换表——左侧是"祈使句指令"列（添加验证 / 修复 bug / 重构 X），右侧是"可验证目标"列，每行用箭头连接，下方展示"计划→执行→验证→循环"的流程图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/multica-ai-andrej-karpathy-skills-article/schematic-3.png)



这个原则的巧妙之处在于，它利用了 LLM 的强项（循环执行直到满足条件）来弥补 LLM 的弱项（缺乏真正的目标理解）。不给 AI 下命令，而是给它设关卡——"让这个测试变绿"比"修好这个功能"更不容易被误解。

## 这套东西真的能工作吗？判断标准和权衡

multica-ai 在 README 里给出了一个"如何判断这套指导原则在起作用"的检查清单：

- diff 里的不必要修改变少了——只有被请求的改动出现
- 因为过度复杂化而需要重写的次数变少了——代码第一次就足够简单
- 澄清性问题在做到之前就出现了——而不是在犯错之后
- PR 干净、最小化——没有顺手重构或"改进"

这四个信号本质上衡量的是同一件事：AI 是否在"做更少但更对的事"。对于每天要 review AI 生成代码的开发者来说，diff 的干净程度直接决定了他们是花 5 分钟还是 50 分钟来做 code review。

但 multica-ai 也坦诚地标注了一个权衡提示（Tradeoff Note）：这套指导原则偏向"谨慎优先于速度"。对于简单任务——比如修一个明显的拼写错误、改一个单行配置——不需要每次都走完整的严谨流程。原则的目标是"减少非琐碎工作上的代价高昂的错误，而不是拖慢简单任务"。

这个权衡提示很重要，因为它暴露了这类系统指令的一个固有限制：你无法让同一个 AI 在"修拼写错误"和"重构支付模块"时用不同的行为模式——至少目前不能。`CLAUDE.md` 里的指令对所有任务一视同仁，这意味着简单任务可能会被"过度谨慎"拖累。multica-ai 的解决方案是让开发者自己判断——对于琐碎任务，"使用你自己的判断力"。

（这其实也是 AI 编程工具的一个更大的未解决问题：上下文感知的粒度。理想的 AI 应该能自动判断当前任务的复杂度，然后动态调整自己的"谨慎程度"。但目前的技术——无论是 Claude Code、Cursor 还是 Copilot——都还做不到这一点。它们要么全局谨慎，要么全局激进。）



![谨慎度与任务复杂度的 2x2 矩阵——横轴是"任务复杂度（低→高）"，纵轴是"AI 谨慎度（低→高）"，理想状态是一条对角线（复杂度越高越谨慎），实际状态是两条水平线——全局谨慎或全局激进，都偏离了理想对角线](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/multica-ai-andrej-karpathy-skills-article/schematic-4.png)



## 这事背后更大的图景：从"写代码的 AI"到"懂分寸的 AI"

multica-ai 这个仓库看起来很小——一个文件，四个原则，MIT 协议——但它触碰到了 AI 编程工具进化路线上的一个关键节点。

2023-2024 年，AI 编程工具的竞争焦点是"能写多少代码"。GitHub Copilot 的自动补全、Cursor 的整个文件生成、Claude Code 的多文件自主重构——能力边界不断被推高。但到了 2025 年，当这些工具已经能处理整个代码库级别的任务时，新的问题出现了：不是"能不能写"，而是"该不该写"。

Karpathy 那篇帖子之所以引发广泛共鸣，是因为它描述的不是 AI 的"无能"，而是 AI 的"过度有为"。一个能自主重构整个项目的 AI，如果缺乏"什么时候不该动手"的判断力，它造成的破坏比一个只会自动补全的 AI 大得多。能力越强，越需要约束——这是工具进化史上反复出现的规律。

multica-ai 的 `CLAUDE.md` 本质上是一套约束层。它不提升 AI 的编程能力上限，而是抬高 AI 的行为下限——让 AI 在不确定时停下来，在不需要时不动手，在完成任务时用可验证的标准而非模糊的"应该好了"。这种思路和自动驾驶里的"安全约束层"有异曲同工之处：不是让车开得更快，而是让车在不确定时减速或靠边。

**AI 编程的下一个战场不是"生成更多代码"，而是"生成更少的正确代码"。**

从更广的视角看，这套原则的适用场景远不止 Claude Code。multica-ai 已经在仓库里提供了 Cursor 的适配文件，而四个原则本身的抽象程度足够高，理论上可以转化为任何 AI 编程工具的 system prompt 或项目级规则。甚至，对于使用 GitHub Copilot Chat 或 Codeium 的团队来说，手动把这些原则写进项目的 `CONTRIBUTING.md` 或 `AGENTS.md` 里，也能起到类似的效果。

不过，这套方案也有一个显见的局限：它依赖于开发者主动安装和配置。对于那些"装完即用、从不调参"的用户来说，`CLAUDE.md` 不会自己出现在项目里。multica-ai 用 Claude Code 的插件 marketplace 降低了安装门槛，但距离"开箱即用"还有距离。未来如果这类原则能成为 AI 编程工具的默认行为模式——至少作为一个可选的"谨慎模式"开关——那影响面会大得多。

让我愣神的是 multica-ai 的创始人 Jiayuan 选择的方式：他没有写一篇博客抱怨 AI 代码质量，也没有发 Twitter 长线程吐槽，而是直接写了一个文件扔到 GitHub 上，MIT 开源。这种"别废话，上代码"的做法，本身就是这套原则的最佳注脚——最小化解决问题，不添加多余的东西。

## 参考来源

- https://github.com/multica-ai/andrej-karpathy-skills
- https://x.com/jiayuan_jy （multica-ai 创始人 X 账号，项目 README 中引用）
- Andrej Karpathy 关于 LLM 编程缺陷的原始帖子（被 multica-ai README 原文引用，具体发布日期 README 未注明，仅提及"From Andrej's post"）

#CLAUDEmd #Claude #Code #Andrej #Karpathy
