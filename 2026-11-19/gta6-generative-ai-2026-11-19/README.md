---
title: "为什么 GTA6 偏偏不靠生成式 AI"
date: 2026-11-19
weekday: 星期四
category: 产业
slug: gta6-generative-ai-2026-11-19
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/gta6-generative-ai-2026-11-19.png
description: "从 GTA6 的发售与 Take-Two 的公开表态出发，看生成式 AI 在美术、配音、关卡、NPC 和测试中的实际分工。"
tags: ["GTA6","Rockstar Games","生成式 AI","游戏开发"]
track: overseas-hot
batch: special
---

# 为什么 GTA6 偏偏不靠生成式 AI

![海滨街道、录音话筒与手工城市模型的杂志照片拼贴](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/gta6-generative-ai-2026-11-19.png)

*图片来源：编辑部 AI 生成封面概念图，2026-11-19。*

**《侠盗猎车手 VI》（GTA6）于 2026 年 11 月 19 日登陆 PS5 和 Xbox Series X|S。** Take-Two 的财报列明了这个日期。Rockstar 在发售当天宣布游戏已全球上市。据路透社报道，游戏从新西兰开始，随各市场进入当地午夜陆续解锁。PlayStation 支持页也明确了 PS5 的当地午夜解锁安排。

据 GameSpot 转述，Take-Two 首席执行官 Strauss Zelnick 在 2026 年 2 月接受 GamesIndustry.biz 采访时说，生成式 AI 没有参与 Rockstar 当时为 GTA6 构建的内容。他形容团队逐栋建筑、逐条街道、逐个街区搭建世界。同月，他在接受 The Game Business 采访时表示，Take-Two 集团有数百项 AI 试验和应用。两次表态的范围分别是 GTA6 的内容制作和整个集团的工具实践。

这两句话放在一起，恰好提供了观察游戏生产的切口。一张场景草图可以在几分钟里多出十种方案。一座城市要交付给玩家，还得确定角色在哪条街见面、玩家绕路时任务要怎样继续，以及这一路能否稳稳跑在目标主机上。生成候选稿的速度，只会改变其中一部分工期。

## 先弄清楚省下的是哪段时间

Rockstar 早在 2022 年 7 月就公开说，过去几年一直在向下一部《侠盗猎车手》转移更多开发资源，并因此调整《荒野大镖客 Online》的支持方式。2026 年 8 月，Rockstar 发布较长的 GTA6 展示片，称素材全部取自 PS5 的游戏内画面。从公开谈到资源调整到正式上市，相隔四年多。这四年多衡量的是公开里程碑之间的间隔。

行业确实有局部提速的案例。EA 在《2024 影响力报告》中称，EA Sports 团队结合生成式 AI、机器学习和大型数据集，将细致呈现一座体育场的时间从六个月缩短到六周。这个数字来自 EA 自述，衡量的是一项制作任务，且涉及多种工具。

在体育场项目里，如果团队已有明确的场馆参考、镜头范围和验收规则，提早交付模型可以让灯光和演出更早开始。开放世界也有类似机会，但需要看节省的时间能否传给下游。假如一条街还在等任务路线确定，美术提前交出十版街景，团队依然得等路线拍板才能决定哪些细节值得做。

对 Rockstar 这样的项目，一项工具能否缩短总工期，取决于它有没有缩短那条决定交付日期的工作链。美术、角色表演、任务与测试互相牵制时，某个环节出稿越快，团队越需要明确谁来选稿、谁来整合、修改后由谁重测。

## 美术和关卡共用同一条街

据 TechRadar 转述《Game Informer》对 Rockstar 开发团队的采访，Rockstar North 联合负责人 Aaron Garbut 说，团队反复打磨屋顶、小巷和建筑内部。从远处能不能一眼认出一条街，到走进建筑后还能看到什么，这些细节决定同一块地图能支撑怎样的探索体验。

在美术前期，生成工具可以给同一间便利店提供不同的招牌、色调和室内陈设。只用于讨论的草图，要求相对简单。到了正式资产阶段，团队还要处理网格、材质、碰撞体、不同观看距离下的细节层级，以及贴图和素材来源。验收对象是运行中的场景。在纸上成立的方案，还得在夜景、雨天、近看和高速经过时都说得通。

关卡设计会给同一家店提出另一套要求。设想一个约定在店门口见面的虚构任务。设计师要先决定玩家从哪儿能看见入口，再考虑逆向开来、提前走进后门或中途离开会发生什么。生成工具可以帮助快速比较街区布局，程序化工具也可以按规则批量放置路边道具。关键路口的视线、触发器的位置、对话播完前玩家会走多远，都需要和任务一起调。

这种耦合会让一个不起眼的改动传到好几个岗位。商店门口往里缩几米，可能需要重调车流、导航和角色站位。改完镜头发现玩家看不到出口，关卡还要回头改。这里的分工可以按改动的影响来定。可自由替换的纹理和摆件容易批量试做，会改变玩家行动的建筑布局需要更早进入灰盒试玩。

## 声音可以合成，表演需要授权和取舍

配音团队可以在读本阶段用获得许可的临时声音检查对白时长，看看一行字幕是否太长，或者一段台词能否在角色走到门口前说完。到了主角对话，声音还要和呼吸、停顿、视线、动作配合。角色同一句「我知道」可以是安慰，也可以是回避。导演和演员决定观众听见的是哪一种，然后让其他镜头里的表演接得上。

声音克隆把同一问题带到合同桌上。美国演员工会 SAG-AFTRA 在 2025 年 7 月 9 日公布，会员以 95.04% 的赞成票通过新《互动媒体协议》，游戏演员罢工由此结束。对协议涵盖的制作，协议为 AI 数字替身规定了同意和披露义务，并允许演员在罢工期间暂停同意生成新素材。条款还为适用的数字替身使用规定了报酬。工会争取的是对可辨识表演复制的知情同意、用途控制和补偿。

合同对声音数字替身有具体定义，并对常规降噪、调整速度和音高等处理留出空间。制作团队若计划让已有声音生成新台词，需要先厘清适用合同、授权用途和报酬。把录音中的底噪清干净，与让同一声音念出演员从未录过的句子，业务上是两件事。

## 路人更会聊天，任务也更难验收

游戏里的 AI 还包括寻路、行为树等既有技术。巡逻角色可以按规则判断何时绕行或等待。这些规则让设计师有办法解释某次行为，也方便测试人员再次触发。同一角色若能依玩家提问即时说话，交互会更丰富，测试的范围也会跟着变大。

育碧在 2024 年游戏开发者大会展示了 NEO NPCs 原型。玩家可以和角色语音交谈，角色能按上下文反馈。育碧的演示团队仍要设定人物的性格、过去、动机和情绪。育碧将它描述为研发原型。它提供了一个实际样本，说明实时对话会给叙事作者增加新的工作。

以开放世界的任务情报为例。一个路人可以用几种说法指向同一个地点，但他能透露的情报要随着任务进度变化。若台词允许临场生成，团队还需约束角色能查哪些资料、能触发哪些动作，以及等待时间过长时怎样回到预录台词。常规 NPC 的行为可以继续由确定性规则控制。模型可以部署在选择明确、出错后容易回退的交谈里。

据 TechRadar 转述同一篇《Game Informer》采访，Rockstar 动态美术副总裁 Michael Kane 把 GTA6 的路人制作描述为由美术人员主导的工作。团队考虑了服饰、文身和社区之间的关系。角色穿什么、怎样融入街区，和他能说出多少句话是两项不同的设计任务。

## 测试提速要看问题有没有更快解决

EA 的研究团队把语言模型用在故障归因上。2024 年的研究采用微调后的 BERT，根据故障和变更信息，从四条候选提交里找出可能引入问题的一条。论文报告，模型在该测试集上的准确率为 71%。研究中的用户评估还发现，排查单个问题原因所用的时间有所减少。这里的测量对象是故障归因，范围比整款游戏的质检小得多。

测试团队可以借此减少翻日志、对照提交和分派工单的时间。判断能否复现，则需要保留版本、存档、输入顺序和预期结果。一个故障如果只发生在读档后，随机跑地图或总结错误日志都不能代替那次有明确断言的回归。模型提出的候选原因越容易验证，它在排查中的价值就越容易兑现。

对项目负责人，比较实在的衡量方式是跟踪一项改动从提出到验收花了多久。美术看可用资产的返工时间，配音看从拿到授权到完成剪辑的排期，关卡看必经分支和绕行路线能否通过试玩，测试看故障能否更快稳定复现。Zelnick 对 GTA6 的描述，展示了大项目保留人工控制的一种取舍。创作决定会跨岗位传导，值得采用的自动化，是能让下一道工序更早接手的那一种。

## 参考资料

- [Take-Two 2027 财年第二季度业绩及发售表（SEC）](https://www.sec.gov/Archives/edgar/data/946581/000162828026072175/ttwo2q27earningsrelease.htm)
- [路透社关于 GTA VI 分区上线的报道](https://www.reuters.com/world/europe/gamers-get-ready-enter-vice-city-grand-theft-auto-vi-begins-global-rollout-2026-11-18/)
- [PlayStation 的 GTA VI 支持页](https://www.playstation.com/en-us/support/games/gta-vi-support/)
- [GameSpot 对 Zelnick 的 GamesIndustry.biz 采访的转述](https://www.gamespot.com/articles/generative-ai-has-zero-part-in-gta-6-despite-widespread-use-across-take-two/1100-6537900/)
- [The Game Business 对 Zelnick 的采访](https://www.thegamebusiness.com/p/take-two-ceo-googles-project-genie)
- [Rockstar 2022 年 7 月社区更新](https://www.rockstargames.com/newswire/article/9k74k52737912k/grand-theft-auto-and-red-dead-online-community-update)
- [Rockstar 的 GTA VI 加长展示片说明](https://www.rockstargames.com/newswire/article/4k138k8okkk483/grand-theft-auto-vi-an-extended-look-now-playing)
- [EA《2024 影响力报告》第 36 页](https://media.contentapi.ea.com/content/dam/eacom/common/ea-impact-report-24.pdf)
- [SAG-AFTRA 2025 年游戏协议批准公告](https://www.sagaftra.org/sag-aftra-members-approve-2025-video-game-agreement)
- [SAG-AFTRA 协议表决文本，第 18 条](https://www.sagaftra.org/sites/default/files/2025-06/2025%20Interactive%20Media%20%28Video%20Game%29%20Agreement%20Referendum%20Booklet.pdf)
- [育碧 NEO NPCs 原型发布稿](https://staticctf.ubisoft.com/8aefmxkxpxwl/Mw2s4KjssknqHHh1VIf8V/720296810ecc3deae51778a219732c7f/PRESS_RELEASE_GDC_UbisoftUnveilsNEONPC_190324.pdf)
- [EA 关于用语言模型辅助故障归因的论文](https://media.contentapi.ea.com/content/dam/ea/seed/presentations/seed-cog2024-leveraging-llms-efficient-failure-analysis-paper.pdf)
- [Rockstar 官方发售页](https://www.rockstargames.com/VI/out-now)
- [TechRadar 对《Game Informer》开发团队采访的转述](https://www.techradar.com/gaming/five-big-new-details-about-grand-theft-auto-6-map-size-confirmed-170-animal-species-revealed-a-wild-new-dynamic-weather-system-and-more)
