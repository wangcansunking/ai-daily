# GitHub Trending 项目 Taste-Skill：让 AI 告别视觉“泔水”，拥有好品味

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/leonxlnx-taste-skill-micro/head.png)

有件事我一直没想通：为什么 2026 年的 AI 能写出 800 行没有 bug 的 Rust 代码，却做不出一个看着不难受的登录页？

不是"丑"——丑是主观的。我说的是那种一眼就认出来的"AI 味"：居中大标题配灰底卡片，Inter 字体 16px 行高 1.5，蓝色渐变按钮圆角 8px，三列 feature grid 下面接一个永远画不对的曲线分割线。你打开 Cursor 让它"做个漂亮的 landing page"，它给你吐出来的东西跟三年前 GPT-4 刚出时一模一样。

![Leonxlnx/taste-skill — Taste-Skill - gives your AI good taste. stops the AI from](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/leonxlnx-taste-skill-micro/content-1.jpg)

这个问题折磨前端开发者至少两年了。2026 年 6 月的 GitHub Trending 上，一个叫 **Taste-Skill** 的项目冲了上来，star 数还在涨。它的 README 第一句话就定了调：

> The Anti-Slop Frontend Framework for AI Agents.

"Slop"——这个词选得精准。不是 buggy，不是 ugly，是 slop：那种工业化流水线倒出来的、没有经过任何人脑判断的视觉泔水。

**AI 不缺算力，不缺 token，不缺代码能力——它缺的是 taste。而 taste 终于被人打包成了一个可安装的 skill 文件。**

## 把"审美"拆成三个旋钮

Taste-Skill 的核心设计思路相当粗暴：既然 AI 生成 UI 的通病是"太安全"，那就强制给它加三个可调参数——`DESIGN_VARIANCE`、`MOTION_INTENSITY`、`VISUAL_DENSITY`，每个都是 1 到 10 的刻度。



![三个旋钮的 UI 示意，VARIANCE 控制布局激进程度，MOTION 控制动效深度，DENSITY 控制信息密度，旁边标注"低=安全/干净"vs"高=不对称/现代"](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/leonxlnx-taste-skill-micro/schematic-1.png)



`VARIANCE` 管的是布局的"实验性"。调低了，模型给你老老实实的居中对称布局；调高了，它开始用不对称网格、重叠元素、打破标准 12 列栅格。`MOTION` 从 hover 级别的微动效一路拉到 scroll-triggered 的全页面叙事动画。`DENSITY` 则从 Apple 官网那种大面积留白跨越到 Stripe Dashboard 级别的数据密度。

这三个旋钮背后是一整套设计系统映射规则。作者 Leonxlnx 没有去训模型，而是写了一个 `SKILL.md` 文件——这是 2025 年底开始流行的一种可移植指令格式，Anthropic 的 Claude Code 和 OpenAI 的 Codex CLI 都能自动识别并加载。你跑一句 `npx skills add https://github.com/Leonxlnx/taste-skill`，所有 skill 就进了项目目录，接下来的每一次 `claude` 或 `codex` 调用都会带上这些规则。

有意思的是 v2 版本的一个硬性规定：**禁止 em-dash**（也就是 `—`）。做过 AI UI 生成的人会心一笑——默认模型对 em-dash 有种病态的迷恋，每段 hero text 都要塞一个。Taste-Skill 直接把它写进禁令，跟防 SQL 注入似的防排版陋习。

## 不是一套规则，是一套规则矩阵

Taste-Skill 最让我愣神的地方，是它不是一个 skill，而是 **9 个代码 skill + 3 个图片生成 skill** 的矩阵。



![12 个 skill 的分类对照表，左侧"Implementation skills"列出 taste-skill / gpt-taste / image-to-code 等 9 个，右侧"Image generation skills"列出 imagegen-frontend-web / imagegen-frontend-mobile / brandkit 三个](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/leonxlnx-taste-skill-micro/schematic-2.png)



这种拆分暴露出作者对真实工作流的理解。做 SaaS landing page 的人不需要 brutalist 的设计规则；做品牌升级的人不需要 image-to-code 的 pipeline。所以每个 skill 只做一件事：`gpt-taste` 是给 ChatGPT/Codex 用的更严格版本，强制更高的 layout variance 和 GSAP 动效；`minimalist-skill` 走 Notion/Linear 那种编辑产品风格；`brutalist-skill` 直接上瑞士字体、硬对比度、实验性布局；`stitch-skill` 甚至兼容了 Google 的 Stitch 设计系统，可以导出 `DESIGN.md` 格式。

图片生成侧的设计也值得细说。`imagegen-frontend-web` 生成的是整页 comps——hero、landing、多段式布局，带排版和间距的艺术指导。`imagegen-frontend-mobile` 专门处理 iOS/Android 的移动端 mockup。`brandkit` 做的是品牌板：logo 方向、色板、字体、跨品类的 identity 应用。这三个 skill 不生成代码，只出参考图——然后你把图丢给 Codex、Cursor 或 Claude Code，让它们照着做到。

这个"image-first" pipeline 在 `image-to-code-skill` 里被做成了一个完整的自动化流程：生成参考图 → 分析视觉语言 → 做到前端代码。你可以直接跟模型说一句 `follow the skill: generate images, then analyze, then code`，它就按这个管线跑。

**这本质上是在用 prompt engineering 的方式做 design system enforcement。没有训模型，没有 fine-tune，全靠指令层硬约束。**



![image-to-code 管线流程：用户 prompt → 图片生成（imagegen skill）→ 视觉分析 → 前端代码做到，三阶段线性流转](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/leonxlnx-taste-skill-micro/schematic-3.png)



回到那个让我困惑了两年的问题：为什么 AI 代码能力爆炸但 UI 审美原地踏步？Taste-Skill 给出的答案很直白——因为模型被训练成"取悦大多数"，而大多数 prompt 没有设计约束。你让它"做个好看的页面"，它取的是训练数据里出现频率最高的视觉模式，那就是那些蓝按钮白卡片。只有当你把"好看"翻译成"VARIANCE=7, MOTION=6, DENSITY=4, 禁止 em-dash, 用 GSAP scroll trigger 做叙事动效, 字体用 DM Sans 配 JetBrains Mono"，模型才会输出不 slop 的东西。

Leonxlnx 在 `research/` 目录里放了背景文章，REPO 的 FAQ 里还专门强调：所有规则都是 framework-agnostic 的，React/Vue/Svelte 通吃，因为规则对的是设计意图而非特定框架 API。MIT 协议开源，没有 token，没有 coin，作者在 README 加了一条 disclaimer 声明任何用他名字或项目名发的币都跟他无关——在 2026 年这个 AI 项目人均发币的环境里，这个声明本身就是一个 taste 声明。

不过话说回来，把审美压缩成三个旋钮这件事，细想是有点荒谬的。设计本应该是对语境的回应——用户是谁、品牌在说什么、信息层级怎么组织——而不是三个 1-10 的 slider 能穷尽的。但实用主义者的反驳也很简单：在 AI 输出全是 5 分的世界里，你能调到 7 分就已经赢了 99% 的生成结果。至于那 10 分的人类设计师作品？他们本来就不跟 AI 在同一个池子里竞争。

Taste-Skill 目前还在 v2 experimental 阶段，作者说"正在积极迭代向 v2.0.0 stable"。我好奇的是，当这些 skill 规则被足够多的项目采纳后，会不会反过来成为新的"slop"——毕竟如果所有人的 VARIANCE 都调到 7，那不对称网格也会变成另一种流水线审美。但在那一天到来之前，先让 AI 学会不用 em-dash 再说。

## 参考来源
- https://github.com/Leonxlnx/taste-skill

#Leonxlnx #TasteSkill #AI #科技
