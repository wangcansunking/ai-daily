---
title: Pro² Agent 怎样用试验找出机器学习瓶颈
date: 2026-11-22
weekday: 星期日
slug: pro2-agent-problem-state-2026-11-22
category: top-news
description: 拆解问题状态如何组织实验，以及两档算力预算下的获牌率。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/pro2-agent-problem-state-2026-11-22.png
batch: morning
track: deep-dive
cover_style: minimalist editorial still-life photography
top_five_rank: 4
---

# Pro² Agent 怎样用试验找出机器学习瓶颈

![黑板与粉笔的研究主题静物](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/pro2-agent-problem-state-2026-11-22.png)

Google 与约翰斯·霍普金斯大学研究人员提出 Pro² Agent，让机器学习智能体在改代码的同时，维护一份关于任务瓶颈的工作记录。论文报告完整 MLE-Bench 获牌率在 24 小时为 70.7%，48 小时为 76.0%。读懂两行结果，需要先明白智能体怎样决定下一次实验。

## 要点速览

- 系统维护显式的问题状态，将瓶颈、候选办法和验证证据保存在一起。
- 作者用受控实验更新判断，再把结果用于后续方案设计与工程优化。
- 论文报告在完整 75 项 MLE-Bench 上，两个时限的获牌率相差 5.3 个百分点。
- 两档实验均按每任务一张 A100 配置，获牌率仍受时限和基准任务影响。

## 让下一次实验回答一个问题

许多自动机器学习流程从可运行的代码出发，提交修改后再根据验证分数保留或放弃方案。这容易留下结果，却难以解释一次改动为什么有效，也难以判断它在另一个任务中是否值得重试。

Pro² Agent 在代码状态之外保存问题状态。候选瓶颈可能是数据表示、训练策略或验证信号。系统把待验证的假设和依据挂在对应问题下，让下一次实验能补充具体证据。

![Pro² Agent 代码状态与问题状态的对比](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agent-state.png)

*图片来源：Pro² Agent 预印本图 1，2026-11-19。*

同样一份验证分数，可能对应完全不同的情形。比如修改预处理流程后得分下降，原因可能是输入变形，也可能是同期更换了切分方式。把变更拆到可回溯的实验记录里，才能知道该复查哪一个环节。

## 从瓶颈出发安排算力

论文把问题探查与方案工程连成两段。探查阶段提出问题并尝试设计能区分候选解释的实验。后续工程阶段接着实现和优化，前一阶段发现的证据会参与选择。

![Pro² Agent 问题探查与工程优化框架](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agent-framework.png)

*图片来源：Pro² Agent 预印本图 2，2026-11-19。*

工程循环也让规划、实现和审阅承担不同工作。检查环节会关注实现是否漏掉要求、提交是否有效。论文的架构图解释了各环节如何传递结果，实际约束仍取决于评测实现。

![Pro² Agent 规划实现和审阅循环](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agent-loop.png)

*图片来源：Pro² Agent 预印本图 3，2026-11-19。*

这种分工的成本是，进入工程环节前要花时间做探查。它最可能节省的是重复走错方向的试验。对于一个简单、短时的调参任务，是否有同样回报，需要另做对照。

## 两个时限摆在一起

| MLE-Bench 时限 | 获牌率 | 有效提交率 |
| --- | ---: | ---: |
| A100，24 小时 | 70.7% | 100.0% |
| A100，48 小时 | 76.0% | 100.0% |

![Pro² Agent 完整 MLE-Bench 获牌率与提交率](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agent-score.png)

*图片来源：Pro² Agent 预印本表 1，2026-11-19。*

两档都报告全部任务有有效提交。按论文表格，金牌率分别是 24.0% 和 25.3%，银牌率均为 21.3%，总获牌率的差异来自铜牌与金牌。有效提交只表示一次提交符合基准要求，成绩要看是否越过对应门槛。

作者还在 22 项 MLE-Bench Lite 上分析初始方案与后续优化轨迹。它能解释某些轨迹为什么更早到达阈值，完整 75 项总体结果仍应使用完整榜单的数据。

![Pro² Agent MLE-Bench Lite 验证轨迹](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agent-trajectory.png)

*图片来源：Pro² Agent 预印本图 4，2026-11-19。*

## 怎样用同样的思路做内部对照

可以先拿团队已有的一组任务做对照，每组锁定模型、硬件、时间、随机种子与验证切分。第一组保留原流程，第二组额外记录瓶颈、假设、区分实验和已见结果。

记录首次有效提交用时、规定预算内的最佳验证分数，以及一次失败实验之后是否重复同样的尝试。还可以保留逐任务结果，检查平均提升是否只由少数任务贡献。

![Pro² Agent 问题状态各部分的消融结果](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agent-ablation.png)

*图片来源：Pro² Agent 预印本表 4，2026-11-19。*

研发团队还可把测试集与调试用验证集隔离，要求每次修改留下对应日志。这样即使某次实验没能提升得分，也能判断它是否缩小了排查范围，并为下一轮选择留下理由。

## 一句话总结

Pro² Agent 的可借鉴之处是把实验要回答的问题保存下来，再用固定预算核对这种做法能带来多少收益。

## 🔗 引用链接

- [Pro² Agent 预印本](https://arxiv.org/abs/2611.25357)
- [Pro² Agent 论文原文及图片](https://arxiv.org/pdf/2611.25357)
