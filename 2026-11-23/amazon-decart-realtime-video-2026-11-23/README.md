---
title: 亚马逊据报洽购 Decart，实时视频该算哪笔账
date: '2026-11-23'
weekday: 星期一
slug: amazon-decart-realtime-video-2026-11-23
category: industry
description: 对照媒体报道、Trainium3 案例和端到端视频指标，厘清交易进度与技术价值。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/amazon-decart-realtime-video-2026-11-23.png
batch: morning
track: deep-dive
cover_style: cinematic sci-fi concept
top_five_rank: 1
---

# 亚马逊据报洽购 Decart，实时视频该算哪笔账

![带有光学走线的服务器机房概念图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/amazon-decart-realtime-video-2026-11-23.png)

《金融时报》当地时间 11 月 21 日报道称，亚马逊收购实时视频公司 Decart 的谈判进入后期，讨论金额约 75 亿美元。截至发稿，交易仍待双方确认。此前公开的 Trainium3 案例，提供了观察双方技术关系的起点。

## 要点速览

- 75 亿美元出自媒体对谈判的报道，最终金额和交易条件仍待披露。
- Decart 已公开展示 Lucy 的实时视频编辑能力。
- Amazon 此前披露过 Decart 在 Trainium3 上的实时生成案例。
- 判断这类优化能否规模化，需要同时测首帧延迟、持续帧率和每分钟成本。

## 先分清已经发生的事

交易报道援引知情人士描述谈判，《The Information》随后转述。现有公开材料支持两件事：媒体报道了潜在交易，Amazon 此前公开把 Decart 列为 Trainium3 用户。有关交易金额、支付方式和交割条件仍待双方文件。

![Trainium3 芯片产品照片](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/trainium-chip.jpg)

*图片来源：Amazon 的 Trainium3 UltraServer 公告，2025-12-02。*

实时视频面向连续画面，输入每次变化都可能影响接下来的输出。对直播编辑场景来说，观众一边看画面一边互动。处理超时会体现在画面与声音错位、提示变化反应慢，或者持续帧率下降。

![Lucy 2.6 对提示与参考图的说明](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/lucy-release.png)

*图片来源：Decart Lucy 2.6 发布说明原文摘录，2026-11-05。*

Decart 11 月 5 日介绍 Lucy 2.6，发布材料展示把提示或参考图应用到实时流的方式。对直播客户，可用性的一个检验是，换商品、换背景或切换动作时，画面是否还能保留同一个主体的关键细节。

## 四倍和一半该怎样读

Amazon 在 Trainium3 发布材料里称，Decart 实时视频案例相对其 GPU 对照实现了四倍的帧生成速度和一半的成本。该页面没有公开足以完整重现对照的配置、质量指标和测试条件。数字应随案例一起引用。

![Amazon 公告所述 Decart 的帧生成案例](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/trainium-decart.png)

*图片来源：About Amazon Trainium3 公告原文摘录，2025-12-02。*

| 指标 | 可比较的记录 | 需要固定的条件 |
| --- | --- | --- |
| 响应速度 | 首帧时间、端到端延迟 | 地区、网络和客户端 |
| 输出稳定性 | 持续帧率和长尾卡顿 | 输入、时长、分辨率 |
| 生产成本 | 每分钟合格视频的花费 | 画质门槛、并发和失败率 |

首帧与持续帧率回答不同问题。首帧看建立连接到接收画面的时间，稳定输出则取决于系统在持续压力下能否维持节奏。按分钟核算时，还要把失败重试和高峰期预留容量放在同一口径里。

![Decart Lucy 的直播编辑使用场景](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/lucy-use.png)

*图片来源：Decart Lucy 2.6 直播应用说明原文摘录，2026-11-05。*

Decart 另有面向 Lucy 2.1 的浏览器探针页面，解释首帧、端到端延迟和网络往返时间怎样取得。这个页面可用来理解指标，但产品示例和监测对象是两个版本，不能把监测结果直接套给 2.6。

![Decart Lucy 浏览器探针覆盖的模型版本](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/lucy-status.png)

*图片来源：Decart Lucy 2.1 浏览器探针监测页原文摘录（取阅），2026-11-23。*

在比较不同芯片时，还应记录模型版本、精度、输入分辨率和并发。即使两套系统的标称帧率相同，连续播放时的丢帧和尾部抖动也可能有明显差异。

## 为什么云厂商会关心视频软件

从公开合作推断，潜在价值在于把芯片适配、模型执行和实时产品串起来。客户如果保留同样的画面质量，还能在固定预算下接入更多并发，优化才会变成可售卖的服务能力。

对 AWS，下一步观察是这套能力能否沉淀为更多客户可用的组件。对视频开发团队，则要记录编译和部署改动、迁移回退耗时、运行故障归属。单回演示可以很快完成，维护通常跨多个版本。

![Lucy 浏览器探针的运行方法与数据聚合](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/lucy-method.png)

*图片来源：Decart Lucy 2.1 浏览器探针方法原文摘录（取阅），2026-11-23。*

实测时可以准备两类输入：一类是机位固定的直播样本，另一类包含移动、遮挡和快速切换。两组都保留原始录屏及运行日志，以便出现成本下降时确认画质、同步和可用性有没有一起变化。

谈判结果尚未得到双方确认，现阶段最可检验的产业问题，是联合优化能否在同等输出质量下稳定降低每分钟视频的成本。

## 🔗 引用链接

- [《金融时报》亚马逊与 Decart 谈判报道](https://www.ft.com/content/dc7749dc-0f40-44a6-80bc-7f747d383616)
- [The Information 对谈判报道的转述](https://www.theinformation.com/briefings/amazon-nearing-7-5-billion-purchase-decart)
- [Amazon 的 Trainium3 UltraServer 公告](https://www.aboutamazon.com/news/aws/trainium-3-ultraserver-faster-ai-training-lower-cost)
- [Decart 的 Lucy 2.6 发布说明](https://decart.ai/publications/introducing-lucy-2-6)
- [Decart 的 Lucy 浏览器端监测与指标说明](https://lucy-status.decart.ai/)
