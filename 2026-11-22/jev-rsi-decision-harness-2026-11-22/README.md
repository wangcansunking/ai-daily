---
title: 轻量模型怎样复用大模型的经验，JevRSI 做了三组实验
date: 2026-11-22
weekday: 星期日
slug: jev-rsi-decision-harness-2026-11-22
category: domestic
description: 对照执行模块带来的成功率变化、推理时间和开发成本。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/jev-rsi-decision-harness-2026-11-22.png
batch: morning
track: deep-dive
cover_style: magazine photo collage
top_five_rank: 3
---

# 轻量模型怎样复用大模型的经验，JevRSI 做了三组实验

![纸张和视觉素材的编辑拼贴](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/jev-rsi-decision-harness-2026-11-22.png)

港科大（广州）与 LIGHTSPEED 研究人员在 JevRSI 预印本里，让轻量模型负责一步步执行，让大模型根据历史轨迹改进执行所需的上下文。作者在三类交互任务上报告成功率和成本对照，并给出了表现下滑的例子。

## 要点速览

- JevRSI 将较重的分析放到异步开发流程，由轻量模型执行当前动作。
- 可复用上下文包括操作步骤、状态记忆和世界模型。
- StartLux-27B 配合 Qwen 开发模块时，TextCraft 分布外成功率从 62.66% 升至 70.66%。
- 换用 Jev 执行时，Qwen 开发的模块让 TextCraft 分布外成功率由 22.00% 降至 20.00%。

## 把哪一段工作留给大模型

交互任务要连续做决定。每走一步都请求大模型，可以获得更多分析，同时要支付延迟与词元开销。轻量模型执行更快，遇到陌生状态时却可能重复试错。

JevRSI 先让轻量模型执行，把动作序列、反馈和结局保存为证据。异步运行的大模型检查这些证据，修改之后可以重复使用的辅助模块。后续运行时，轻量模型根据检索到的上下文选择动作。

![JevRSI JevRSI 的快速执行和异步开发框架](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/jevRSI-framework.png)

*图片来源：JevRSI 预印本图 1，2026-11-20。*

模块有三类。操作步骤记录遇到某类条件时如何行动。状态记忆保存观察与历史变化。世界模型预测某个动作可能带来的后果。检索还参考模块在过去任务中的效用，避免仅凭文字相似度挑选经验。

## 同一个模块开发模型，两种执行结果

| 执行模型与评测设置 | 基础模型 | 加入 Qwen 开发的模块 |
| --- | ---: | ---: |
| StartLux-27B，TextCraft 分布外 | 62.66% | 70.66% |
| Jev，TextCraft 分布外 | 22.00% | 20.00% |

![JevRSI 三套任务的单次尝试成功率](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/jevRSI-scores.png)

*图片来源：JevRSI 预印本表 2，2026-11-20。*

两组数字出自论文表 2，指标为单次尝试成功率。分布内测试考查训练分布中的未见任务，分布外测试考查遇到分布变化后的泛化。同一项分布外评测更换执行模型，结果从增加 8.00 个百分点变为下降 2.00 个百分点。

训练轨迹用于开发模块，测试任务用于判断积累的经验能否迁移。若一个步骤在旧任务里表现很好，却只在特定目标和环境组合下成立，换任务后反而可能干扰轻量模型。

![JevRSI 三类模块的分布内和分布外消融结果](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/jevRSI-ablation.png)

*图片来源：JevRSI 预印本图 5，2026-11-20。*

## 成本要把运行和开发都带上

论文将词元开销与推理耗时分别作图。在线执行省下来的时间是一笔账，离线分析轨迹、修订模块与检查回退又是另一笔账。两笔开销能否摊薄，取决于模块在多少后续任务中可以复用。

![JevRSI 三套任务的成本和成功率比较](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/jevRSI-cost.png)

*图片来源：JevRSI 预印本图 2，2026-11-20。*

![JevRSI 不同设置的推理耗时对照](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/jevRSI-latency.png)

*图片来源：JevRSI 预印本图 3，2026-11-20。*

作者还展示了训练过程中开发模块的干预频次。它能帮助读者判断实验里何时集中修改、何时较少修改。对长周期业务的维护成本，仍需用实际运行日志估算。

![JevRSI 训练阶段模块开发的干预频次](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/jevRSI-development.png)

*图片来源：JevRSI 预印本图 4，2026-11-20。*

## 把表里的失败留在复盘中

如果要在自己的工作流试验，先固定动作权限、任务时限与重试次数，再比较原轻量模型、加入辅助上下文、以及每步调用大模型三条路径。训练阶段可以用旧日志，最后保留全新任务做测试。

除成功率外，记录每次动作的延迟、当批开发成本和重复失败次数。特别保留关闭一个模块后反而好转的任务，检查是否因为步骤过时、状态记录不完整，或者预测模块给出了不可靠的建议。

这三套交互基准仍与实际业务有差距。把一次任务从完成到失败的轨迹完整存下来，再按场景拆分复盘，通常比只看汇总成功率更能说明问题。

若流程依赖外部工具，还应记录每次调用失败，以及过期上下文被重新检索的次数。

## 一句话总结

JevRSI 把大模型推理变成可复用的执行上下文，值不值得采用要同时看迁移后的得失和开发成本。

## 🔗 引用链接

- [JevRSI 预印本](https://arxiv.org/abs/2611.22432)
- [JevRSI 论文原文及图片](https://arxiv.org/pdf/2611.22432)
- [JevRSI 官方仓库](https://github.com/usail-hkust/JevRSI)
