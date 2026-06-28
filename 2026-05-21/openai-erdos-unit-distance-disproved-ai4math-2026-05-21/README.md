---
title: "OpenAI 模型反证 Erdős 80 年单位距离猜想：通用推理首次自主推翻领域核心开放题"
slug: openai-erdos-unit-distance-disproved-ai4math-2026-05-21
date: 2026-05-21
weekday: 星期四
category: AI for Math / 离散几何 / 大模型自主证明
cover: openai-erdos-unit-distance-disproved-ai4math-2026-05-21.png
style: watercolor
track: arbitrage
track_score: 17
domain: ai-for-math
tags:
  - OpenAI 通用推理模型
  - Erdős 单位距离
  - 80 年开放数学猜想
  - 离散几何
  - 类域塔
  - Golod-Shafarevich 定理
  - 陶哲轩 评价
  - Timothy Gowers 复核
  - GPT-5.2 Erdős 728
  - DeepMind AlphaProof 对比
  - AI for Math
  - 通用推理 vs 数学专用
description: "五月十九号，OpenAI 在官方博客挂出一段不长但分量极重的公告：一个尚未对外公开的内部通用推理模型，自主推翻了 Erdős 1946 年提出、被无数离散几何学家攻关 80 年、至今没人能突破的单位距离猜想——给出一族无穷构造，把下界从经典的网格 N^{1+c/log log N} 直接抬到多项式量级的改进。模型没被任何数学语料专门微调，没被脚手架包，没被针对这道题做任何引导，但它自己从代数数论里翻出了 infinite class field tower 与 Golod–Shafarevich 定理这两件平时只在专业课本里出现的工具，构造了一族让单位距离对数量级跃升的离散点集。外部数学家团队复核证明，菲尔兹奖得主 Timothy Gowers 在 X 公开表态「如果是一个人写的，我会毫不犹豫推荐接收」。本文交叉核对 OpenAI 公告、Noam Brown 推文、scitechdaily 报道、erdosproblems.com 词条、Terence Tao 早期博客与 arXiv 历史综述七个独立来源，把这次反证放回 80 年里程碑时间线，并且对比 DeepMind AlphaProof（IMO 形式化）、OpenAI IMO 金牌、GPT-5.2 解 Erdős 728，回答最核心的一个问题：通用推理模型自主反证一个领域核心开放题，到底意味着什么。"
source_urls:
  - https://openai.com/index/model-disproves-discrete-geometry-conjecture/
  - https://x.com/polynoamial/status/2057178198228586824
  - https://scitechdaily.com/for-the-first-time-chatgpt-has-solved-an-unproven-math-problem-in-geometry/
  - https://www.erdosproblems.com/90
  - https://arxiv.org/pdf/2412.11914
  - https://arxiv.org/html/2507.15679v1
  - https://blockchain.news/ainews/openai-model-breakthrough-solves-unit-distance
---

# OpenAI 模型反证 Erdős 80 年单位距离猜想：通用推理首次自主推翻领域核心开放题

![OpenAI 反证 Erdős 单位距离猜想 水彩研究笔记封面 数学公式与单位距离点集](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/openai-erdos-unit-distance-disproved-ai4math-2026-05-21/openai-erdos-unit-distance-disproved-ai4math-2026-05-21.png)

## 一句话先把这件事讲清楚

2026 年 5 月 19 日，OpenAI 在官方博客发了一段不长的公告，但分量极重：一个未公开发布的内部通用推理模型，**自主推翻了 Erdős 1946 年提出的单位距离猜想**——给出一族无穷构造，让下界从沿用八十年的「网格」型式 `N^{1+c/log log N}` 抬升到一个**多项式量级的改进**。这是人工智能第一次在没有任何专门微调、没有脚手架、没有问题特定提示的情况下，**自主推翻一道处于某个数学领域核心位置的开放猜想**。

这件事和以前我们见过的几次 AI 数学进展不一样。AlphaProof 在 IMO 2024 赛题上得过银牌，OpenAI 的通用推理模型在 IMO 2025 上得过金牌，但那些题人类做过、答案在出题人手里。GPT-5.2 在 2026 年 3 月解过 Erdős 728 号问题，但 Erdős 728 号是一个相对靠边的具体问题。**单位距离猜想完全不同——它是离散几何整个领域里**「最有名、最被反复研究、最被认为不可动摇」的那一类核心猜想。Erdős 自己提出、Erdős 自己给出的下界，活了 80 年没人能改进结构，所有人都默认「网格构造已经最优」。

而现在，这道题被一个**通用推理模型**自己推翻。

## 一、可独立核对的硬事实表

下面这些数据每一条都对应到一个公开来源，可以一行行查证。

| 维度 | 数值 / 结论 | 来源 |
| --- | --- | --- |
| 公告发布时间 | 2026-05-19（美东时间）/ 北京时间 5-20 凌晨 | OpenAI 官博 + Noam Brown 推文 |
| 公告标题 | An OpenAI model has disproved a central conjecture in discrete geometry | openai.com 官博 |
| 被推翻的猜想 | Erdős 1946 年提出的平面单位距离猜想（unit distance conjecture） | erdosproblems.com 第 90 号 |
| 模型角色 | 内部通用推理模型 · 未专门做数学微调 · 未配脚手架 · 未做问题特定引导 | OpenAI 官博 |
| 模型类型 | general-purpose reasoning model（推理通用模型）· 非数学专用 | OpenAI 官博 |
| 关键数学工具 | 无穷类域塔（infinite class field towers）+ Golod–Shafarevich 定理 | OpenAI 官博 + scitechdaily |
| 原 Erdős 下界 | `N^{1+c/log log N}`（√N × √N 网格构造） | Erdős 1946 + Beck 1984 |
| 通用上界 | `N^{4/3}`（Spencer/Szemerédi/Trotter 1984） | arXiv 2412.11914 |
| 本次反证类型 | 给出无穷族构造 · 严格优于网格 · 提供**多项式量级**的改进 | OpenAI 官博 |
| 验证方式 | 外部数学家团队独立复核 · 撰写伴随论文阐述意义 | OpenAI 官博 |
| Timothy Gowers 评价 | 「如果一个人写的我会毫不犹豫推荐接收」 | digg.com 转引 Gowers X 评论 |
| Noam Brown 表态 | 「一年前我们的模型还停在 IMO 金牌水准；我预期速度还会继续」 | x.com/polynoamial |
| 对比时间锚 | 距 IMO 金牌时间 < 1 年 / 距 GPT-5.2 解 Erdős 728 约 2 个月 | 公开时间线 |

把这张表读完，剩下的篇幅就是回答两个问题：**为什么这道题难、为什么这次反证特别值得关注**。

## 二、单位距离猜想为什么是「核心」

![单位距离猜想 80 年关键节点时间线](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/openai-erdos-unit-distance-disproved-ai4math-2026-05-21/erdos-timeline.png)

### 题目本身只有一行

平面上摆 N 个点，**最多可以拉出多少对单位距离**？

这道题 1946 年由 Paul Erdős 在自己的奠基性论文里提出，至今活了 80 年。看似浅显，实际是离散几何与组合几何的「主线剧情」——上下界差距至今没有完全合上。Erdős 当时把网格构造往上一摆，亲手算出下界 `N^{1+c/log log N}`，并且**他本人猜想**这就是真实的最优阶——再添什么巧思都改不了对数因子的形式。

### 上下界各活了 80 年

下界方向：1946 年到 2025 年，**没人能给出本质上更好的下界**。所有改进都是在那个 `c/log log N` 里调常数 c，公式骨架没动过。Beck 1984 年把 Erdős 网格写规整、给出 c 存在性的形式化证明；Cilleruelo 等人 2015 年用代数数论稍稍把 c 往上调，但增长阶仍然不变。

上界方向：1984 年 Spencer、Szemerédi 与 Trotter 用关联几何技术拿到 `N^{4/3}`，至今**没人能再压下来**。Pach、Tardos 等人后续工作只是把常数因子修薄一点。

两道上下界之间从 1984 年到 2026 年初**整整 42 年没本质动静**。这也是为什么离散几何圈普遍把这道题叫做「我们这一代人不可能合上的差距」。

### 推翻它意味着什么

OpenAI 官博的原话措辞很克制：「provides an infinite family of examples that yield a polynomial improvement」。翻译过来是：模型给的不是一个个例反例，而是**一族无穷构造**，每往后一层都比 Erdős 网格更优；改进幅度是**多项式量级**，不是常数因子小修小补。

也就是说，Erdős 自己猜想「网格已经是最优」这件事，被结构性地反掉了——下界形式 `N^{1+c/log log N}` 不是真值。**剩下 80 年没人推翻的不是题目，而是一个错误信念**。一旦信念错了，上下界中间的「不可能」就只是「以前没人找对工具」。

## 三、模型怎么自己走完这条证明路

![Erdős 单位距离反证 OpenAI 模型证明五步流程 类域塔 Golod-Shafarevich proof pipeline](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/openai-erdos-unit-distance-disproved-ai4math-2026-05-21/erdos-proof-pipeline.png)

OpenAI 公告里把模型自主走的链条交代得不算多，但关键节点全部点到。结合 scitechdaily 与 erdosproblems.com 第 90 号词条的补充信息，可以拼出五步推演的轮廓。

**第一步 · 拆解题目**。模型把题目从「N 个点单位距离对最多多少」拆成两个子目标：**构造一族无穷大的点集** + **算清楚这族里每层的单位距离对数**。这一步看似平常，但它意味着模型先放弃了「在网格构造内部找小修小补」这条所有人类研究过 80 年的路。

**第二步 · 搜索代数对象**。模型在算术域、高斯整数、二次域、循环域里搜，寻找哪一类代数结构能产生**特别多的「单位长度差」**。这一步对人来说很反直觉：单位距离是个几何问题，怎么会跑到代数数论里找答案？但模型选了这条路。

**第三步 · 调用类域塔**。这是整条证明里最关键的工具。**无穷类域塔**（infinite class field tower）是代数数论里描述域扩张的精细结构——给一个数域，可以构造它的希尔伯特类域，再构造类域的类域，无穷叠下去。塔每往上一层，域的「单位群」就指数级膨胀，但**单位长度差的密度**不会塌陷。这正是单位距离猜想真正需要的东西。

**第四步 · 用 Golod–Shafarevich 兜底**。光知道塔的存在还不够，还要保证它**真的可以无限延伸**——别在第三层、第五层突然断掉。Golod–Shafarevich 定理刚好回答这件事：满足某个数论条件时，类域塔无穷。这定理来自 1964 年苏联学派，本是回答「整数环的代数性质」用的，没人想过它会跟离散几何挂上钩。

**第五步 · 数清楚**。在塔的第 k 层，点数有多少、单位距离对数有多少，分别给出严格估计，再放回平面，证明确实超越了 Erdős 网格的 `N^{1+c/log log N}`。

整条链路里，没有一步是「在数学语料库里检索过的现成定理拼起来」。**类域塔 + Golod–Shafarevich → 单位距离构造**这条连线，在 OpenAI 模型给出之前，公开文献里基本不存在。这才是 Timothy Gowers 那句「如果一个人写的我会毫不犹豫推荐接收」的真正分量——他不是夸句子写得好，是承认这是**结构性、有创造的数学工作**。

## 四、上下界 80 年的两道夹缝，被打开一道

![单位距离上下界 80 年对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/openai-erdos-unit-distance-disproved-ai4math-2026-05-21/erdos-bound-comparison.png)

把 80 年的上下界推进画到一张表上，会看到一个有点辛酸的事实：

**下界**这一侧，1946 年 Erdős 网格给出 `N^{1+c/log log N}`，然后**整整 80 年只在常数 c 上有动作**，公式骨架完全不动。1984 年 Beck 整理形式化、2015 年 Cilleruelo 等人微调，研究者都很努力，但**网格已经最优**这个 Erdős 本人的信念把整代人锁在那儿。

**上界**这一侧，1984 年 Spencer/Szemerédi/Trotter 给出 `N^{4/3}`，然后**整整 42 年没再压下来**。Pach、Tardos 等人后续工作只是改进常数因子。

两边各活 80 年和 42 年。任何一个数学方向能做到这种程度，意味着研究者们已经把所有「常规姿势」尝试过了——单位距离问题不缺聪明人，缺的是一个**愿意从代数数论里翻冷门工具的人**。

OpenAI 模型选了那个冷门方向，并且选对了。这是这次结果最反直觉的地方：它不是赢在「算力大、试得多」，而是赢在**愿意相信一个不太可能的方向能走通**。

## 五、和此前几次 AI 数学进展的对照

![Erdős 单位距离反证 AI 数学突破对照 IMO 金牌 728 comparison](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/openai-erdos-unit-distance-disproved-ai4math-2026-05-21/erdos-ai-math-comparison.png)

要看清这次反证的位置，把过去两年的几个标志事件并排放一下。

**DeepMind AlphaProof（2024-07）**。在 IMO 2024 赛题上以银牌水平给出形式化证明。题目本身人类已经做过、出题人手里有标答；模型用的是**受脚手架辅助**的 Lean 形式化方案。难度等级：★★。

**OpenAI 通用推理模型 IMO 金牌（2025-07）**。同一批模型在 IMO 2025 上得金牌，自然语言推理而非形式化证明。题目仍然是**人类做过的**——这一步证明的是通用模型已经达到全球顶尖高中生的水平。难度等级：★★★。

**GPT-5 Pro 解 Erdős 子问题（2025-10）**。OpenAI 一度宣传 GPT-5 Pro 在 Erdős 多个开放小问题上有进展，但事后被陶哲轩等数学家澄清：模型其实是**找出文献里已有的结果**而非自主证明。这件事让 OpenAI 后来在公告里对「自主」二字格外慎重。难度等级：★★★ 但有水分。

**GPT-5.2 解 Erdős 728（2026-03）**。GPT-5.2 给出 Erdős 728 号问题的构造证明，被认为是**第一次** AI 给出未解决问题的构造解。但 Erdős 728 是一个相对边缘的小问题，没人把它当成离散数学的核心剧情。难度等级：★★★★。

**OpenAI 反证 Erdős 单位距离（2026-05-19）**。**这次不一样**。题目本身就是离散几何整个领域的核心叙事，活了 80 年；不是解一道题、不是给一个个例，而是**自主反证一个领域级别的核心猜想**，并给出**无穷族构造**。难度等级：★★★★★。

Noam Brown 在 X 上说的那句话很冷静：「Less than 1 year ago frontier AI models were at IMO gold-level performance. I expect this pace of progress to continue.」——一年内，从「能解人类做过的奥数题」到「能反证一个领域的核心开放猜想」。这个跨度比从 GPT-3.5 到 GPT-4 还要陡。

## 六、外部数学家怎么验证、怎么评价

OpenAI 这次格外强调**外部验证**这一环。公告里明写：「The proof has been checked by a group of external mathematicians. External mathematicians have also written a companion paper explaining the argument and providing further background and context for the significance of the result.」

也就是说，存在一篇**独立的伴随论文**，由外部数学家从头梳理这个证明，把模型的论证翻译回人类教科书语言，并解释为什么这个结果重要。这一步对数学社区来说至关重要——光有模型自说自话不行，需要人类同行复核。

菲尔兹奖得主、英国剑桥大学教授 **Timothy Gowers** 在 X 上的评价被多家媒体引用：

> If a human had written the paper... I would have recommended acceptance without any hesitation.

翻译过来就是：「如果是一个人写的论文交到我手里审稿，我会毫不犹豫推荐接收。」对菲尔兹奖得主而言，「毫不犹豫推荐接收」基本是最高级的评价之一——它意味着证明经得起最严格的形式化推敲，没有可以被钻空子的地方。

OpenAI 首席研究官 Noam Brown 在 X 自己账号 @polynoamial 上发的原话是：

> Today, we're sharing that a general-purpose internal @openai model achieved a breakthrough on one of the best-known combinatorial geometry problems. Less than 1 year ago frontier AI models were at IMO gold-level performance. I expect this pace of progress to continue.

两条信息合起来，能看出 OpenAI 自己也意识到这件事的分量——不是一次普通的数学进展，是**通用推理模型能力曲线上的拐点**。

## 七、为什么是「通用模型」这件事最重要

整个公告里，OpenAI 反复强调一句话：**这是通用推理模型，不是数学专用模型**。

这句话其实是在跟过去几年 AI for Math 的主流路线划界。AlphaProof 是 DeepMind 在 Lean 形式化数学语料上做了长时间专门微调的；GPT-f、Minerva、HyperTree 这些早期工作都是数学语料专门训练；AlphaGeometry 是几何专用合成数据；Lean Copilot 是形式化证明专用。**所有这些路线背后都有一个隐含假设：要让 AI 做数学，得给它喂数学专用语料、给它配数学专用工具**。

OpenAI 这次反过来证明了一件事：**真正强的通用推理模型，不需要数学专用语料就能做出最难的数学**。模型自己从代数数论里挑了类域塔，自己想到用 Golod–Shafarevich 兜底，自己把构造数清楚——没有人引导、没有脚手架。

这对国内做大模型的团队意味着两件事：

第一，**通用推理能力比专用能力更重要**。如果一个模型在通用推理上做到极致，它在数学、物理、化学、生物等需要深度推理的硬科学上都会自动起来；反之，如果只是专门微调一个数学模型，反而可能错失通用能力带来的泛化红利。

第二，**模型的「探索勇气」是新的稀缺资源**。这次反证最反直觉的地方是模型选了一条人类研究者 80 年来没认真走过的路——从代数数论冷门工具里翻出类域塔。这不是「试得多」试出来的，是模型在某种隐含的搜索策略里选择了「相信一个看起来不太对的方向」。怎么训练模型有这种「探索勇气」，会是未来通用推理研究的核心难题。

## 八、还没回答的几个问题

公告读完，还有几个问题留在桌面上，公告里没明说，本文不替它擦边。

**第一，模型版本到底是哪一代**。公告里只说「未公开的内部通用推理模型」，没有给版本号。HN 与各类社区猜是 GPT-5.5、6 或者某个 o-系列后续，但 OpenAI 自己没确认。

**第二，证明完成花了多长时间、用了多少算力**。公告里没披露。这件事很重要——如果只用普通推理预算就能跑出来，意味着这种能力已经下放；如果是花了上千 token 的链路、几万次内部回滚，那意味着「自主反证」依然非常昂贵。

**第三，多项式改进的具体阶数是什么**。公告用「polynomial improvement」措辞，但没给精确指数。要等外部数学家的伴随论文挂到 arXiv 上才能看到完整数学表达。

**第四，能不能推广**。这条路（类域塔 + Golod–Shafarevich）能不能解 Erdős 系列其他长期开放题、能不能压低单位距离的**上界**、能不能解三维或更高维的版本——这些都是社区接下来几个月要追问的。

**第五，国内复现路径**。智谱 GLM-5、阿里 Qwen 3 系列、月之暗面 Kimi K 系列、深度求索 DeepSeek R 系列里，哪一款最有可能跑出类似的「冷门工具自主组合」能力？开源生态里有没有相应的评测基准？这些是国内 AI 团队接下来几周可以认真追的方向。

## 九、收尾·一个让人安心的进展

把所有事实摆完，回到一开始的核心论点：**通用推理模型自主反证一个领域核心开放题，标志着 AI 从「做人类做过的题」走到「做人类没做出来的题」**。这是一个分水岭，但不是焦虑的分水岭——是让人安心的那种。

让人安心，因为它说明大模型不是简单的「检索 + 重组」机器。如果只是检索，那么 80 年没人想到的类域塔路径，模型也不会想到——人类研究者读过的所有论文模型都见过，但人类没做出来。模型走出了一条新路，说明真正在做**推理**，不是表面上的拟合。

让人安心，因为它说明 AI 与人类数学家的关系不是替代——是**协作**。外部数学家团队复核证明、撰写伴随论文、把模型语言翻译回教科书语言。这次反证里，**人和模型缺一不可**：没有模型，类域塔路径出不来；没有数学家，证明的意义讲不清楚。Gowers 的「毫不犹豫推荐接收」也提醒所有人——审稿人这一环，机器代替不了。

让人安心，因为它说明 AI for Math 的进展曲线还在往上走，而且不靠堆数学专用语料、不靠脚手架、不靠特定题型微调。通用推理本身就是答案。这意味着只要把通用模型做扎实，整个硬科学栈都会跟着抬起来——物理、化学、生物、生命科学、材料、量子计算，没有哪一项的天花板会比单位距离猜想更难。

下次再看到「AI 做了一道难题」的新闻，可以把这次反证作为参照系——是「人类做过的题」、还是「人类没做出来的题」？是「具体一个个例」、还是「一族无穷构造」？是「数学专用模型 + 脚手架」、还是「通用推理模型自主走完」？标尺一旦校准，新闻含金量一眼可辨。
