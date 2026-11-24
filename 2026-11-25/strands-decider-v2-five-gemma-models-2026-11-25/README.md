---
title: AWS 开源 5 款 Gemma 4 决策模型，让智能体少叫几次大模型
date: '2026-11-25'
weekday: 星期三
slug: strands-decider-v2-five-gemma-models-2026-11-25
category: big-tech-models
description: Strands Decider v2 从 E2B 覆盖到 31B，单次前向即可给预设选项打分，并输出置信度。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/aws.png
batch: morning
cover_style: conceptual-3d
---

# AWS 开源 5 款 Gemma 4 决策模型，让智能体少叫几次大模型

一张客服工单写了三大段，系统真正要的只有一个答案：交给退款组、物流组，还是人工复核？AWS 的 Strands Labs 为这类选择题推出 5 款 Gemma 4 衍生模型，最大的 31B，最小的 E2B。它们交回选项概率，省掉逐字生成答案的过程。

## 要点速览

- v2 共五档，权重和代码以 Apache 2.0 开放，任务集中在分类、打分和路由。
- 官方六项文本基准中，v2 31B 平均 82.1，上一代 26B-A4B 为 70.0。
- 亚马逊工单案例的 98% 降本来自 v1。v2 另有一组大小模型路由实验。

## 大模型接长活，小模型接选择题

智能体常常把大量时间花在派活：哪条记录最像重复工单，此时要查哪个数据源，一次请求该交给便宜模型还是更强模型。只要可选项事先定得下来，就可以让一个更专门的模型完成分派。

Decider 接收任务状态、问题和选项，输出选项及概率。它支持是非题、从一组备选项中挑选、按有序等级打分。官方文档给出的选择题上限是 255 个选项。类别名称写在每次请求里，增加工单队列时可以直接把新队列加进候选项。

![Strands Decider v2 的共享上下文和问题读出](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/aws-architecture.png)

*图片来源：Strands Agents《Introducing Strands Decider v2》图 3（架构图），2026-11-23。*

其关键是把读材料和出判断放在一次模型前向里。同一份长工单可以先编码一次，再分别问「该分给谁」「是否需要升级」和「优先级多高」。问题可读取公共状态，各自的结果也不会因为换了问题顺序而改变。

双向读取还有一个实际好处。工单开头可能写着「只收到了包裹」，最后一行才解释「里面没有配件」。任务是判断是否交给缺件队列时，模型可以结合末尾的条件来理解开头，而无需用一串生成文字把这些线索重新复述出来。

换成开放问题，边界就清楚了。如果用户真正需要的是给出新的赔付方案，系统仍需要能生成答案的模型或人工。团队可以把「决定下一站」和「完成下一站的工作」拆成两个操作，并分别计算失败和重试的成本。

## 两组数字说明了两件事

| 官方文本基准 | 上一代 | v2 |
|---|---:|---:|
| 最大发布档位 | 70.0（26B-A4B） | 82.1（31B） |
| E4B 同档 | 62.7 | 74.4 |

第一行换了架构也换了规格，第二行才是按同档位观察进步。官方还公布了最高档的图像和视频任务成绩，分别为 90.1 和 70.2。视觉能力沿用 Gemma 4 的视觉编码器，v2 的决策训练数据则是文本。

![Strands Decider v2 官方文本、图像和视频基准](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/aws-bench.png)

*图片来源：Strands Agents《Introducing Strands Decider v2》图 1（基准图表），2026-11-23。*

另一组数据来自真实工单。AWS 称，内部一个大型工单系统把标注环节从 Claude Sonnet 4.6 换到 **v1**，成本下降 98%，单次调用速度提升 78%。这里的收益只对应给工单贴标签的工作，理解复杂投诉和撰写回复仍然是两类不同任务。

团队在 v2 的路由实验里也给出了约束条件：在客服和应用自动化的混合任务中，Decider 将请求分配给 Claude Haiku 5.5 和 Sonnet 5.5，以全用 Sonnet 的 44% 成本，完成了后者所能解决任务数量的 94%。成本和成功任务数量要一起看。

## 把置信度变成一条可执行的分流规则

官方六项文本基准中，31B 的 ECE，即预期校准误差，从上一代最大档的 0.062 降到 0.027。更低的误差意味着模型给出的概率更贴近实际命中频率，运营团队才更容易决定哪些请求走快线。

![两代 Decider 在文本基准的校准误差](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/aws-calibration.png)

*图片来源：Strands Agents《Introducing Strands Decider v2》图 2（校准图表），2026-11-23。*

设想你维护一个客服机器人。高信心的订单查询走自动分流，模棱两可的投诉则交给更强的模型或人工。当输入分布从成熟产品换成刚发布的新服务，原有阈值可能需要重定。类别写得太接近，也会让概率分散，带来更多转人工的请求。

可以从一个小而真实的输入开始。官方快速开始页给出了一张空购物车界面，让模型在「已付款」「重试」「购物车」「空车」里选一个。部署者可以沿用这种思路，把自己的历史工单去标识后做一份样本，再记录各个候选队列实际会把单子送到哪里。

![官方示例的空购物车测试界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/aws-ui.png)

*图片来源：Strands Agents 快速开始文档与示例仓库（产品界面测试样例，采集日期），2026-11-25。*

五档模型真正带来的选择，是把判断交给多大的模型、把多少边界样本交回人工。E2B 适合从窄分类任务起步，31B 则有更高的官方文本基准分数。整条流程的成本还包含输入编码、部署、人工接手和误分返工，可以与节省的生成开销一起计算。

还有一种容易忽略的流量：每逢上游系统新增渠道，工单会冒出训练样本里没见过的说法。可以把这类样本集中保留，月底回看有多少被错误分入老队列、有多少触发了升级。这样调出来的拒答和分流策略，比全局固定一个置信度门槛更贴近业务。

部署也有两种典型取舍。偶尔处理一批单子，可能只需离线跑小模型。每天持续高频分发，编码一次共享上下文的收益才更容易显现。如果每次判断都要去远端取材料，通信和数据准备依然会进入整条链路的响应时间。

分派任务的模型也可以只给建议。让操作人员先看到模型推荐的队列和备选项，等积累足够多的人工反馈，再决定在哪些队列逐步开启自动分流。

一句话总结：Strands Decider v2 把智能体里最常见的一批选择题独立出来，让团队能为每一次派活单独决定需要多少模型能力。

## 来源

- [Strands Agents v2 发布与实验数据](https://strandsagents.com/blog/introducing-strands-decider-v2/)
- [架构与置信度文档](https://strandsagents.com/docs/labs/strands-decider/understanding/)
- [快速开始与视觉样例](https://strandsagents.com/docs/labs/strands-decider/quickstart/)
- [Strands Decider 开源仓库](https://github.com/strands-labs/strands-decider)
