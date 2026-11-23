---
title: GPT-Live-1.1 开放 API，一通电话如何按待机计费
date: '2026-11-21'
weekday: 星期六
slug: gpt-live-11-standby-api-2026-11-21
category: model
description: 拆解语音通话待机费率、唤醒延迟和后台计费。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/gpt-live-11-standby-api-2026-11-21.png
batch: morning
track: deep-dive
cover_style: minimalist editorial still-life photography
---

# GPT-Live-1.1 开放 API，一通电话如何按待机计费

![带有琥珀色指示灯的麦克风静物](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/gpt-live-11-standby-api-2026-11-21.png)

OpenAI 在 11 月 20 日宣布，GPT-Live-1.1 可通过 API 调用，同时为两代 GPT-Live 开放 WebRTC 待机。让一通电话保持连通、在必要时暂缓模型推理，给长语音会话多了一种运营方式。

## 要点速览

- 两代 GPT-Live 都可在满足条件的 WebRTC 会话中待机。
- 可设置 5 至 60 秒闲置阈值，连接保持，检测到语音时恢复推理。
- 活跃和待机的语音费率分别为每分钟 0.05 与 0.02 美元，后端模型和工具另计。

![GPT-Live-1.1 API 公告及待机费率](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/gpt-live-api-announcement.png)

*图片来源：OpenAI 开发者社区 API 公告原文摘录，2026-11-20。*

## 接通、推理、待机各有状态

官方指南将待机放在 WebRTC 语音连接里。会话可配置为先等用户开口，再启动推理。开始通话后，在指定时段内没有输入或输出转写时，可以再次进入待机。

麦克风轨道在待机时仍保持开启，连接和会话标识也保留。再次检测到语音后，系统恢复对话历史。文档提醒，唤醒后遇到很短的发言，第一句回复可能更慢。

因此，同一句短促的「在吗」应该做两次测试，一次在活跃时说，一次在待机后说。验收时分别记录首句延迟、是否遗漏开头、用户是否因等待重复发言，平均延迟会掩盖这些差异。

![GPT-Live 会话文档中的待机设置说明](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/gpt-live-standby.png)

*图片来源：OpenAI 开发者文档待机原文摘录（取阅），2026-11-21。*

## 一通五分钟电话怎样计费

按官方费率举例，五分钟全程活跃，语音费是 0.25 美元。若模型实际活跃两分钟、实际待机三分钟，语音费是 0.16 美元，差额为 0.09 美元。

| 实际状态 | 计费口径 | 五分钟示例 |
| --- | --- | --- |
| 活跃 | 0.05 美元／分钟 | 2 分钟，0.10 美元 |
| 待机 | 0.02 美元／分钟 | 3 分钟，0.06 美元 |
| 后端模型和工具 | 另外收费 | 示例未计入 |

人在沉默的时间和系统实际待机的时间有差别。若用户停下来找订单号，闲置阈值到点前仍按活跃计费。若语音模型在等待后端且推理保持活跃，这段时间也归入活跃。

费率差换算成实际节省，要先看录音里的静默分布，再看会话状态是否真的发生切换。官方列出了暂停和恢复事件，运营团队可据此统计真实待机时长，并与业务日志对照。

![GPT-Live 成本优化文档的计费说明](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/gpt-live-voice-cost.png)

*图片来源：OpenAI 开发者文档成本页原文摘录（取阅），2026-11-21。*

## 把说话和做事分开验收

GPT-Live 可以边听边说，并把需要推理或工具的任务交给另一个后端。以查订单为例，用户补充门牌号的时候，前台模型仍能对话，后台则按独立的业务流程读取结果。

可先用几通样例覆盖三个场景：

- 在查询进行中更正一个数字，检查后端最后采用了哪个值。
- 暂停几十秒再说一个短词，观察恢复速度及上下文是否延续。
- 查询失败后改变请求，检查模型能否清楚说明当前进度。

OpenAI 宣布新版提升了多语言口音一致性、数字和字母识别等表现。对实际应用，仍值得按常见语言、环境噪声和口音分别抽样，并对读错数字之类的高影响错误单独计数。

客服或车载团队还可以按不同闲置阈值做一组小样本对照。阈值设得短，更多停顿有机会落入待机；阈值设得长，用户边查资料边说话时通常更少遇到重新唤醒。比较时让每组人完成同一任务，并记录完成时间和误唤醒次数。

对于更长的空档，官方指南建议考虑彻底关闭连接，在用户再次回来时新建会话。应用要先保存对话摘要和任务状态，再决定哪些内容应在重连时带回，以免用户重报已确认的关键信息。

待机最值得测量的地方，是同一通交谈里沉默的费用、唤醒的延迟，以及后台工作能否在两者切换时保持连贯。

## 🔗 引用链接

- [OpenAI 开发者社区｜GPT-Live-1.1 API 公告](https://community.openai.com/t/gpt-live-1-1-has-arrived-in-the-api/1418537)
- [OpenAI｜GPT-Live-1.1 模型说明](https://developers.openai.com/api/docs/models/gpt-live-1.1)
- [OpenAI｜管理 GPT-Live 会话](https://developers.openai.com/api/docs/guides/live-conversations)
- [OpenAI｜语音成本优化](https://developers.openai.com/api/docs/guides/voice-latency-cost)
