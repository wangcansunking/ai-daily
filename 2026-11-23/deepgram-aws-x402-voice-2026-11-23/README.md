---
title: Deepgram 接入 AWS 按次付款，一笔语音请求怎样对账
date: '2026-11-23'
weekday: 星期一
slug: deepgram-aws-x402-voice-2026-11-23
category: top-news
description: 拆解语音 API 从逐笔报价到 AWS 账单的业务链路。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/deepgram-aws-x402-voice-2026-11-23.png
batch: morning
track: deep-dive
cover_style: minimalist editorial still-life photography
top_five_rank: 4
---

# Deepgram 接入 AWS 按次付款，一笔语音请求怎样对账

![银色麦克风与立体声波静物](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/deepgram-aws-x402-voice-2026-11-23.png)

Deepgram 11 月 20 日宣布，机器调用方可以通过 AWS Marketplace 按次购买转录和语音合成。每笔请求先获得美元报价，再用 Bedrock AgentCore 获取支付令牌。团队可以把付款额度与业务执行放在同一条审计链上。

## 要点速览

- 首批覆盖 Nova-3 转录，以及 Flux TTS、Aura-2 合成。
- 三条接口均为批处理，实时 WebSocket 当前未列入该接入路径。
- 报价随 HTTP 402 返回，AgentCore 支付会话用于限制开支。
- 支付完成的业务响应带有 Marketplace 支付编号，费用进 AWS 账单。

## 语音调用怎样完成

![Deepgram 公告列出的首批语音模型](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/dg-launch.png)

*图片来源：Deepgram 更新日志原文摘录，2026-11-20。*

普通语音 API 通常先配置供应商账户和密钥。这次接入使用 AWS 账户结算，Deepgram 端无需另配 API 密钥。AWS 侧仍要设置带 AWS Billing 连接器的 Payment Manager，并创建带支出限额的支付会话。

![AgentCore 与 AWS Billing 的对接关系](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/aws-architecture.jpg)

*图片来源：AWS 技术博客 · AgentCore 支付架构图，2026-11-19。*

请求进入 Deepgram 后，服务通过 HTTP 402 返回这次调用的美元报价。调用端将匹配 AWS 结算的报价交给 AgentCore，取得支付令牌，再用令牌重发同一项语音请求。处理成功后返回业务结果与支付编号。

![收到报价后获取令牌、携令牌重发与响应的流程](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/aws-flow.jpg)

*图片来源：AWS 技术博客 · x402 支付流程图，2026-11-19。*

| 环节 | 调用方自建记录 | 业务状态 |
| --- | --- | --- |
| 获得报价 | 报价、任务 ID 和音频或文本规格 | 待确认 |
| 取得支付令牌 | 支付会话和授权结果 | 待执行 |
| 收到语音结果 | 响应状态、支付编号和产物 ID | 可核账 |

报价、支付授权和业务结果分别发生。业务响应超时后，可以先核对本系统的任务记录和能取得的 AWS 交易记录。执行或扣费状态未明时，应暂缓自动重试。

![Deepgram 文档中报价（quote）、令牌和成功响应说明](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/dg-quote.png)

*图片来源：Deepgram 接入文档 · 请求与响应原文摘录（取阅），2026-11-23。*

## 批处理接口适合什么场景

Nova-3 用于对已经有的音频做转录；Flux TTS 和 Aura-2 把文字合成为语音。Deepgram 文档列出三个批处理端点，并说明准确美元价格随每笔报价返回。预先录好的语音素材和离线配音任务可以用这条链路做验证。

![Deepgram 文档中按请求计价的语音产品（products）和批处理说明](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/dg-products.png)

*图片来源：Deepgram 接入文档 · 接口说明原文摘录（取阅），2026-11-23。*

实时语音对话还要处理流式传输、首包与打断。Deepgram 当前文档说明，这条 Marketplace 路径暂时没有 WebSocket 端点。若一个系统同时有离线复盘和在线对话，两类流量可以分别估价和验收。

一段长音频可以作为单笔请求，也可以先拆成多个作业。团队可按素材长度、失败时重做的成本和回传速度比较两种做法，并保留输入与返回结果之间的映射。

若工作流会同时触发多笔调用，还可以在测试中用同一支付会话连续提交，确认限额何时生效，以及预算用完时上游任务能否等待人工处理。

## 如何把额度与对账接进现有系统

AWS 说明，功能适用于 AgentCore payments 已可用的区域。客户可以在交易前设定支出限额、卖家范围和其他规则，Private Marketplace 策略仍适用。AWS 侧交易也可在 CloudTrail 和 Cost Explorer 中查看。团队可用成本中心、任务 ID 与支付编号关联用量。

![Deepgram 文档的支付安全（safety）与令牌绑定说明](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/dg-safety.png)

*图片来源：Deepgram 接入文档 · 可选令牌绑定原文摘录（取阅），2026-11-23。*

在本地试验时，可以给测试任务单独建立额度较小的会话，分别运行一条转录和一条合成请求，随后检查业务记录与支付记录能否对应。加入失败场景时，再测预算不足、报价不能使用和业务响应超时怎样呈现。

应当按完整业务任务汇总报价和花费，记录不同接口在该任务中的开支。这个口径便于比较按次采购和固定供应商合约的实际成本，也便于在超出预期时定位究竟是哪种任务增长。

验收时还可按转录和合成分别设置单笔预算，用真实录音长度和文本字符数跑一次预估，再对比用量报表与最终账单。

这次发布把批处理语音 API 接入了 AWS 的逐笔结算，落地时需要一并验证服务结果、预算边界和交易记录。

## 🔗 引用链接

- [Deepgram 11 月 20 日更新日志](https://developers.deepgram.com/changelog/2026/11/20)
- [Deepgram 的 AWS Marketplace 接入文档](https://developers.deepgram.com/docs/aws-marketplace-x402)
- [AWS AgentCore 与 Marketplace x402 技术博客](https://aws.amazon.com/blogs/machine-learning/enable-ai-agents-to-pay-for-aws-marketplace-x402-products-with-amazon-bedrock-agentcore-payments/)
- [AWS Marketplace 按次支付公告](https://aws.amazon.com/about-aws/whats-new/2026/11/marketplace-agentic-payments-agentcore/)
