# Anthropic 获 650 亿美元 H 轮融资，估值近万亿美元剑指 OpenAI

> ai-daily · 2026 年 5 月 29 日 08:35 · 来源：Hacker News Frontpage

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/series-h-micro/head.png)

旧金山 SoMa 区某栋大楼的会议室里，Krishna Rao 面前的屏幕跳出一个数字——$47,000,000,000。这不是估值，不是融资额，是年化收入。这位 Anthropic CFO 在 2026 年 5 月 28 日敲下发送键时，距离公司上一轮 Series G 仅仅过去三个月。

三个月。一家公司用 90 天走完了别人十年的路。

![Anthropic raises $65B in Series H funding at $965B post-money valuation](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/series-h-micro/content-1.png)

就在同一天，Hacker News 的评论区炸了。269 条留言在几小时内涌入，点赞数冲到 261。有人算了一笔账：$965B 估值除以 $47B 年化收入，市销率约 20 倍。在 SaaS 黄金年代这算正常，但在 2026 年这个时间点——当所有人都以为 AI 泡沫该喘口气的时候——Anthropic 又往火堆里扔了一桶汽油。

**$65,000,000,000。这轮 Series H 的融资额，够买下整个福特汽车，还能剩点零头买通用。**

## 三朵云 + 三颗星：史上最奇怪的算力联盟

Anthropic 这次公布的算力布局，让我愣神了好一会儿。原文里列了三组数字：与 Amazon 签署了最高 5GW 的新增容量协议，与 Google 和 Broadcom 锁定了 5GW 的下一代 TPU 容量，以及——SpaceX。没错，马斯克的 SpaceX，将向 Anthropic 提供 Colossus 1 和 Colossus 2 的 GPU 算力。



![Anthropic 三大算力来源示意图，AWS 数据中心 / Google TPU 集群 / SpaceX 火箭发射基地旁的 GPU 集群，三条管道汇入 Claude](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/series-h-micro/schematic-1.png)



这里面的戏剧性够写一部硅谷宫斗剧。Amazon 投了 $5B（原文明确列出 "$5 billion from Amazon"），Google 是 TPU 的供应商兼竞品大模型持有者，SpaceX 是马斯克的亲儿子——而马斯克自己在做 xAI。三家互相看不顺眼的大佬，居然同时出现在同一份融资公告的算力供应商名单里。

**敌人的敌人是盟友，但盟友的盟友可能还是敌人——Anthropic 硬是把这道三角恋做成了一笔生意。**

我查了一下原文措辞，Anthropic 的表述很克制："Claude is the first frontier model available on all three of the world's largest cloud platforms: Amazon Web Services, Google Cloud, and Microsoft Azure." 然后补了一句 "AWS remains our primary cloud provider and training partner." 这是在给 Amazon 吃定心丸，但潜台词谁都听得出来：我们可以随时在三家之间切换，别想卡我们脖子。

更有意思的是那三家 "strategic infrastructure partners"——Micron、Samsung、SK hynix。存储和逻辑芯片的全球三巨头，名字第一次出现在 AI 公司的融资公告里。Anthropic 不是在买算力，是在锁定整个半导体供应链上游的产能。这让我想起 2021 年全球缺芯时汽车厂商的惨状，Dario Amodei 显然不想让 Claude 重蹈覆辙。

## $47B 年化收入背后：企业级 AI 的付费意愿正在重塑 SaaS 定价权

"run-rate revenue crossed $47 billion earlier this month"——这句话值得反复读三遍。Run-rate 意味着如果当前月收入能持续一年，全年就是 $47B。换算成月度，大约是 $3.9B。三个月前 Series G 时 Anthropic 没披露收入数字，但从融资节奏反推，当时的 run-rate 可能只有现在的一半甚至三分之一。



![Anthropic 融资轮次与收入增长对照时间线，2024-2026，标注 Series E/F/G/H 节点和对应估值跳跃](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/series-h-micro/schematic-2.png)



Brad Gerstner（Altimeter Capital CEO）在公告里的原话是："Claude's latest advancements have driven large-scale adoption among the world's most demanding organizations." 关键词是 "most demanding"——最挑剔的组织。这些客户不是来尝鲜的，是把 Claude 嵌进了核心业务流程。Sequoia 的 Alfred Lin 说得更直白："Claude is learning how businesses actually operate: the context, the processes, the judgment."

这意味着 Anthropic 卖的不是 API 调用次数，而是企业运营知识库的入口。一旦 Claude 学会了某家公司的审批流程、供应链逻辑、客户判断标准，切换成本就高到离谱。这解释了一件事：为什么投资者愿意给一个成立不到五年的公司 $965B 估值。他们赌的不是技术领先，是锁定效应。

Marc Stad（Dragoneer Managing Partner）的引述里有一句："The technological progress we are seeing right now is breathtaking. And we believe that we are still in the earliest days of both the development and commercialization of this technology." 这话从一家投过 Snowflake、Datadog、Square 的机构嘴里说出来，分量不轻。他们见过 SaaS 的完整周期，现在说 "earliest days"，意思很清楚：企业级 AI 的渗透率还远没到天花板。



![企业级 AI 用率曲线预测图，标注当前节点 "2026 年 5 月 $47B run-rate"，右侧虚线延伸至万亿级市场](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/series-h-micro/schematic-3.png)



让我觉得最值得玩味的是融资结构里的 "$15 billion of previously committed investments from hyperscalers"。这 $15B 不是新钱，是之前云厂商承诺的算力投资被重新包装进了这一轮。Amazon 的 $5B 是其中一部分，剩下 $10B 来自谁？公告没点名，但 Google 和 Microsoft 的名字呼之欲出。这种"算力换股权"的模式正在成为 AI 时代的默认融资方式——云厂商左手收算力费，右手拿股权增值，两头赚钱。

而 Anthropic 这轮真正的 "新钱" 来自一长串华尔街老钱：Capital Group、Coatue、D1 Capital、GIC、Fidelity、T. Rowe Price、Temasek……这些名字出现在同一张 captable 上，上一次还是互联网泡沫顶峰。现在他们集体出现在 AI 赛道，而且是在一家明确把 "safety and interpretability research" 放在第一位的公司身上。这要么说明安全叙事已经彻底说服了老钱，要么说明老钱根本不在乎安全，只在乎 $47B 这个数字。

Krishna Rao 在公告结尾的措辞很 Anthropic——"bring Claude to more of the places where work happens." 没有吹技术指标，没有提 AGI，说的是 "工作发生的地方"。这种务实的调性，可能正是华尔街最喜欢的 pitch。

不过，翻完 269 条 Hacker News 评论，我发现质疑声集中在一点：$965B 估值意味着 Anthropic 必须在未来几年内证明自己能吃掉全球企业软件市场的一大块。而就在同一天，Anthropic 还发了两条看似无关的公告——米兰办公室开业（欧洲第六个），首尔办公室即将启用并任命韩国代表董事。全球扩张的齿轮已经在转了。

三家互相算计的云厂商、三条并行的芯片供应链、一群把身家押上的华尔街老钱、以及三个月翻倍的年化收入——Anthropic 这台机器现在跑得太快，快到让人看不清它是在起飞还是在过载。唯一确定的是，2026 年 5 月 28 日这天，有人用 $65B 买了一张站票。

## 参考来源
- https://www.anthropic.com/news/series-h
- https://news.ycombinator.com/item?id=48313048

#Anthropic #Series #AI #科技
