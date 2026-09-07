---
title: "StarVLA 公开 VLAct：机器人能力先练进主干"
date: 2026-09-07
weekday: 星期一
category: 具身智能
slug: starvla-vlact-representation-pretraining-2026-09-07
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/07/source-starvla-vlact-representation-pretraining-2026-09-07.png
description: "VLAct 不再只追求收集更多机器人轨迹，而是保护视觉语言先验、引入多动作头联合监督，并在不同机器人之间共享可对齐的动作语义。"
tags: [StarVLA, VLAct, VLA, 具身智能, 机器人]
---

# StarVLA 公开 VLAct：机器人能力先练进主干

8 月 27 日，StarVLA 团队公开 VLAct。它只用公开数据和 16 张 GPU 做持续预训练，却在 LIBERO-Plus、RoboTwin 2.0、真机与未见机器人迁移上都给出了提升。

更关键的不是某个榜单名次，而是它换了问题。机器人轨迹昂贵且有限时，怎样让每条轨迹在视觉语言模型（VLM）中留下可迁移的视觉—动作知识？**VLAct 的核心判断是，视觉—语言—动作模型（VLA）的扩展不只取决于数据量，表征质量也应成为主要设计变量。**

*下图来源：VLAct 官方论文 Figure 1，2026-08-27。*

![VLAct 从有限机器人轨迹学习跨任务跨本体表征](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/07/source-starvla-vlact-representation-pretraining-2026-09-07.png)

## 机器人数据的瓶颈不只是数量

*下图来源：VLAct 官方论文 Figure 1，2026-08-27。*

![StarVLA VLAct 表征中心持续预训练概览](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/07/starvla-source-overview.png)

网页图文可以批量收集，机器人轨迹却必须在物理世界里执行。场景、物体、任务、本体与接触动力学又形成连续组合，即使数据集很大，也只覆盖物理交互空间的一小部分。

这让传统的“继续加数据”遇到两个限制：

- 采集依赖机器人、遥操作人员与真实场地，单条轨迹成本高；
- 训练分布对场景与本体的覆盖稀疏，增加同类数据未必扩大迁移范围；
- 下游用户常会更换动作头、任务和机器人，预训练策略本身很强，不等于主干可以复用。

VLAct 从已预训练的 Qwen3-VL-4B 出发，在 DROID、InternData-A1、RoboCoin、MolmoAct 等公开多本体轨迹上持续预训练。面对具体任务时，它再装上新初始化的动作头进行微调。这里的产物不是固定策略，而是一套更懂物理任务的 VLM 主干。

这一区分很重要。若预训练只让某个动作解码器更会拟合原数据，动作头一换，收益就可能消失。只有动作信息真正进入共享表征，新任务和新本体才可能复用它。

## 同一个动作头变强，不代表主干更通用

*下图来源：VLAct 官方论文 Figure 2，2026-08-27。*

![StarVLA 不同动作监督对 VLAct 主干迁移能力的影响](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/07/starvla-source-action-head-transfer.png)

论文先做了一组受控实验：固定 Qwen3-VL-4B 主干框架，改变持续预训练和下游微调使用的动作头。结果说明，动作监督会直接改变主干里的特征组织方式。

离散 FAST 动作 token 能向连续动作头迁移一些结构，但保留 FAST 头时会受到离散化的信息损失。连续 OFT 监督在下游继续使用 OFT 时提升明显，换成 PI 或 GR00T 头后却不能稳定保留收益。

机器之心转载稿给出的 RoboTwin 例子更直观：OFT 预训练后，下游仍用 OFT，成功率从 61.7% 升到 75.8%；换 PI 头则从 60.5% 降到 55.1%；换 GR00T 头从 51.2% 降到 28.9%。

论文把这种现象称为**动作头特定的表征坍缩**。它不是说主干没学到动作，而是动作信息被组织成了某个解码器最容易读取的形状。于是，同头成绩会高估主干的通用性。

一套可迁移的 VLA 主干至少要同时满足三项要求：

- 保留细粒度动作信息，不能只剩离散动作的粗轮廓；
- 让多种动作头都能读取同一份表征；
- 换任务、换机器人后，仍能提供有效初始化。

VLAct 后续三项设计都围绕这三个要求展开。

## 关键设计把能力留在共享主干

*下图来源：VLAct 官方论文 Figure 3，2026-08-27。*

![StarVLA VLAct 的表征中心持续预训练方法](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/07/starvla-source-training-recipe.png)

VLAct 没有再造一种动作头，而是改变机器人数据塑造主干表征的方式。持续预训练阶段有三条并行约束。

### 保护原有视觉语言先验

机器人数据的视觉多样性远小于网络图文数据。若端到端更新全部参数，动作学习可能覆盖原有的物体、属性、空间关系与语言理解能力。

VLAct 冻结视觉编码器和大语言模型较浅的一半层，只更新较深层与动作头；同时混入图像描述数据。论文消融中，相比更新完整主干，保护视觉编码器与较浅一半语言层让 LIBERO-Plus 从 78.9% 升至 82.6%，RoboTwin 2.0 从 77.1% 升至 80.5%。

*下图来源：VLAct 官方论文 Figure 6，2026-08-27。*

![StarVLA VLAct 分层注意力分析支持保护浅层表征](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/07/starvla-source-layer-attention.png)

浅层更广泛关注视觉与空间信息，深层更集中于语义和任务区域。冻结策略的目标不是拒绝适配，而是避免有限机器人数据先破坏已有的广泛能力。

### 三种连续动作头共同监督

同一个主干并行连接 OFT、PI 与 GR00T 三种连续动作头。三者读取同一份隐藏表征，预测同一段真实动作，损失相加回传。

这样一来，主干不能只生成某一个解码器偏爱的特征。它必须把动作信息放在多种参数化方式都能读取的位置。持续预训练结束后，三种预训练动作头都会被丢弃；下游根据任务重新初始化动作头。

这个细节划清了 VLAct 与“预训练一套完整策略再接着微调”的差别：真正被迁移的是主干权重，不是已经熟悉任务的旧动作头。

### 只共享物理意义一致的动作维度

不同机器人自由度和运动学不同，把所有动作坐标硬塞进统一空间会制造错误对应；完全分开，又浪费了夹爪开合等共通语义。

*下图来源：VLAct 官方论文 Figure 4，2026-08-27。*

![StarVLA VLAct 部分统一的跨机器人动作空间](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/07/starvla-source-unified-action-space.png)

VLAct 采用部分统一动作空间。夹爪等物理含义可对齐的维度共享，不兼容的机械臂维度则保持本体专属。每条样本只计算当前机器人有效坐标的损失，其余位置会被遮蔽。

对周期关节角，损失还按 360 度取环绕距离。179 度与 -179 度在物理上只差 2 度，普通回归却会当成相差 358 度。这个修正很小，却体现了表征对真实动作几何的尊重。

## 基准成绩验证的是迁移，不是单点拟合

*下图来源：VLAct 官方论文 Figure 5，2026-08-27。*

![StarVLA VLAct 真机实验和未见机器人迁移结果](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/07/starvla-source-real-robot-results.png)

论文把相同主干放进多个下游协议。所有 VLAct 对照里，改变的是 VLM 主干权重；动作头重新初始化，下游数据、优化器和微调预算保持一致。作者报告由主干带来的提升为 7.6—21.4 个百分点。

| 场景 | VLAct 结果 | 证据边界 |
|---|---:|---|
| LIBERO-Plus | 82.6% 成功率 | 七类扰动下的模拟评测 |
| VLA-Arena | 54.8% 平均成功率 | 11 个任务套件的加权结果 |
| RoboTwin 2.0 | Clean 92.5%，Random 90.8% | 数据扩展设置，50 项双臂任务 |
| RoboDojo | 平均分 10.66，成功率 7.60% | 35 个策略中成功率第 6 |
| RoboCasa-GR1 | 20% 数据时 49.5% | 未见人形机器人本体迁移 |

独立的 VLA Leaderboard 页面在 8 月 31 日收录了 RoboTwin 2.0 结果，并明确标注 VLAct-OFT 的 Clean 92.5%、Random 90.8%。该页面也提醒，Clean、Random 两种设置不能与其他 Easy、Hard 设置直接横比。

RoboDojo 的绝对成功率只有 7.60%，不能被标题里的“超过 WAM”掩盖。在论文引用的 8 月 24 日榜单中，VLAct 平均分排第 8，成功率排第 6。它超过了全部明确标为世界动作模型（WAM）的参赛项，但仍落后于多个策略。Memory（记忆）类任务尤其薄弱。

因此，这组结果支持“表征迁移有收益”，并不意味着 VLAct 已经解决通用机器人控制。

## 真机结果补上了模拟之外的一块证据

*下图来源：VLAct 官方论文 Figure 7，2026-08-27。*

![StarVLA VLAct 持续预训练使用的视觉语言数据样例](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/07/starvla-source-vlm-data-mixture.png)

论文还用 Franka Research 3 七自由度机械臂做了真机评测。它覆盖单臂短任务、单臂长任务、双臂协同，以及新物体和完全替换物体等分布外设置；每个模型训练 5 万步，使用 8 张 H800，每项以 10 次试验计算结果。

相较没有持续预训练的 Qwen3VL-4B-OFT，论文报告：

- 单臂短任务平均成功率从 77.5% 升至 92.5%；
- 新物体短任务两项均为 90.0%，基线分别为 73.3% 和 65.0%；
- 长任务中的舀豆从 33.3% 升至 80.0%；
- 双臂协同平均成功率从 44.0% 升至 72.0%。

这些是真实机器人结果，但样本量仍有限。每项 10 次试验足以给出方向性证据，不足以回答长期运行、硬件磨损、安全失效与现场恢复等工业问题。论文也没有提供跨机构独立复现。

图像描述数据的混入同样不是装饰。它帮助主干继续辨认物体、属性和空间关系，避免机器人轨迹把视觉语言能力压缩成少数操作场景。VLAct 的方法由此形成完整逻辑：先守住广泛感知，再注入多头动作监督，最后只在物理语义一致处跨本体共享。

## RoboCasa-GR1 下游微调轨迹只用 20% 最说明问题

*下图来源：VLAct 官方论文 Figure 5(d)，2026-08-27。*

![StarVLA VLAct 在未见 GR-1 本体上的数据效率](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/07/starvla-source-unseen-embodiment.png)

VLAct 持续预训练只见过 Franka 单臂和 AgileX 双臂数据，GR-1 人形机器人与 ARX X5 双臂平台都被留在训练分布之外。RoboCasa-GR1 因而是最接近核心论点的一组测试：主干能否把已有本体上的动作知识迁到新身体？

使用 RoboCasa-GR1 全量微调数据时，VLAct 成功率为 54.0%；Qwen3VL-OFT、GR00T-N1.6 和 π0.5 的论文引用基线分别为 48.8%、47.6% 和 37.0%。只给 VLAct 20% 下游轨迹时，它已达到 49.5%；给到 50% 时为 51.0%。

这里不能写成“20% 数据训练出完整机器人”。20% 指 RoboCasa-GR1 下游微调轨迹占比，前面仍有多本体公开数据的持续预训练。更准确的结论是：**在新本体的适配阶段，较好的共享表征降低了对目标机器人轨迹的需求。**

这也解释了为什么 VLAct 强调主干，而不是动作头。动作头负责把共享特征翻译成当前机器人的控制量；主干若已经认识物体、空间关系、可供性和动作条件变化，新机器人需要重新学习的内容就会更少。

## 16 张 GPU 说明什么：条件可复查，成本仍待交代

VLAct 的持续预训练基于 Qwen3-VL-4B，使用公开数据与 16 张 GPU。Hugging Face 的 VLAct 合集已经列出预训练主干，以及 RoboDojo、RoboTwin、LIBERO-Plus、DOMINO、VLA-Arena 等下游模型。

这给研究团队提供了三类可复查对象：

- 预训练主干能否在自有动作头上保留收益；
- 部分统一动作空间能否适配新的机器人坐标定义；
- 数据混合、冻结层数与多头权重改变后，迁移曲线怎样变化。

但“16 张 GPU”并不等于任何团队都能轻松复现。论文没有把 GPU 型号、总训练时长、能耗与数据预处理成本浓缩成统一成本数字；真机实验本身还需要设备和遥操作数据。这个数字说明它没有依赖超大闭源集群，不代表具身智能研发已经变成低门槛任务。

## 表征质量比数据规模更关键

9 月 6 日，机器之心以 StarVLA 的 16 卡训练、RoboTwin 与 GR-1 迁移结果为主线报道该工作。国内团队给出了具身智能训练目标的明确增量，VLAct 的公开模型和训练流程也提供了复查条件。

“20% 数据超过全量基线”最容易传播，却不足以概括 VLAct 的价值。**持续预训练不应只看下游动作成绩。主干能否跨动作头、任务和本体迁移，决定了机器人数据能否被继续利用。**

这个方向对国内具身智能研发很实际。硬件本体快速增加，数据规格又不统一；若每换一台机器人都从头积累策略，数据与算力会被重复消耗。部分统一动作空间和可替换动作头提供了一种更温和的共享方式：能对齐的语义共同学习，不能对齐的维度明确隔离。

同时，VLAct 还留下了清楚的验证清单：

- 在更多未见本体上检查 20% 数据优势是否稳定；
- 用更多试验次数与更长连续运行验证真机结果；
- 测量不同动作头下表征是否真的保持可读；
- 补充训练时间、能耗与数据清洗成本；
- 对 RoboDojo 的 Memory 短板做针对性分析。

## 表征质量应与数据规模并列评估

VLAct 没有否定数据扩展。它指出的是，机器人轨迹不可能像网页文本那样近乎无限采集，因此每条轨迹怎样改变主干，不能再被当成训练细节。

VLAct 先保护 VLM 先验，让主干保留广泛的视觉语言知识。再用 OFT、PI、GR00T 多头共同监督，避免表征依赖单一解码器。最后只统一物理意义一致的动作坐标，避免错误对齐。这三项设计共同服务于一个目标：把有限轨迹变成可再次利用的知识。

现有证据仍主要来自作者团队，跨机构复现、长期真机运行和完整成本尚待补齐。但从 RoboTwin 的独立榜单收录，到 GR-1 的低数据迁移，再到真机多类任务，VLAct 已经给出一条可检验的研究路线。

**具身智能下一阶段仍会继续增加数据，但更成熟的训练体系会同时追问：这些数据究竟训练出了一次性的策略，还是可以跟着新动作头、新任务和新机器人继续前进的表征。**

## 参考资料

- VLAct 论文：<https://arxiv.org/abs/2608.27550>
- VLAct 项目页：<https://starvla.github.io/VLAct/>
- 机器之心转载稿：<https://news.qq.com/rain/a/20260906A0881800>
- VLAct Hugging Face 合集：<https://huggingface.co/collections/StarVLA/vlact>
- VLA Leaderboard · RoboTwin 2.0：<https://vlaleaderboard.com/benchmark/robotwin>
