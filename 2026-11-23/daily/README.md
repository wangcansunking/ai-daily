---
title: 亚马逊据报洽购 Decart · 混元开放权重 | AI 日报 | 2026-11-23
date: '2026-11-23'
weekday: 星期一
slug: '2026-11-23'
category: daily-report-newsletter
description: 跟进 Decart 谈判、深圳 APEC 就业声明、混元开放权重与语音按次结算。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/23.png
batch: morning
track: newsletter
cover_style: magazine photo collage
---

# 亚马逊据报洽购 Decart · 混元开放权重 | AI 日报 | 2026-11-23

![深圳天际线与算力设备的照片拼贴](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/23.png)

《金融时报》报道，亚马逊收购实时视频公司 Decart 的谈判已进入后期。深圳 APEC 文件把数字技术与就业放进同一份合作声明，腾讯混元则公开了 Hy4 flash 的不同权重版本。

## 要点速览

- 媒体报道亚马逊与 Decart 的谈判金额约 75 亿美元，仍待双方公告。
- APEC 就数字技术与就业发表声明，落实进度将纳入年度高官经济技术合作报告。
- Hy4 flash 提供指令版、FP8 版和预训练底座，模型卡披露约 2200 亿总参数。
- Deepgram 三条批处理语音接口可按次报价，由 AWS 账户结算。

## 头条｜亚马逊据报接近收购 Decart

《金融时报》当地时间 11 月 21 日援引知情人士报道称，亚马逊与 Decart 的收购谈判已进入后期，讨论金额约 75 亿美元。截至截稿，公开依据仍是媒体报道，最终价格和条款待确认。

![Trainium3 芯片产品照片](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/trainium-chip.jpg)

*图片来源：Amazon 的 Trainium3 UltraServer 公告，2025-12-02。*

Amazon 此前把 Decart 列为 Trainium3 的早期用户，并披露过实时视频案例。Decart 开发 Lucy 实时视频模型，并从事推理优化。若交易推进，值得跟踪的是这套优化能否被其他团队复用，以及模型迁移需要多少工作。

![Lucy 2.6 对提示与参考图的说明](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/lucy-release.png)

*图片来源：Decart Lucy 2.6 发布说明原文摘录，2026-11-05。*

## 要闻｜声明把数字化落到就业

11 月 18 至 19 日，APEC 领导人会议在深圳举行。配套声明把数字技能进修、职业培训和中小微企业数字化列为合作方向，也提到劳动者权益与职业安全。

![深圳 APEC 领导人会议合影](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/apec-group.jpg)

*图片来源：APEC 领导人会议官方合影，2026-11-19。*

人力资源开发工作组将与相关机制推进落实，执行情况进入年度高官经济技术合作报告。下一步可据此跟踪有哪些项目、覆盖哪些群体，以及是否披露就业结果。

## 🇨🇳 国内 AI 观察

混元公开了 Hy4 flash、FP8 指令版与预训练底座。模型卡列出约 2200 亿总参数、每词元约 120 亿活跃参数，支持文本和图像输入，输出文本，上下文上限约 100 万词元。

![DFly2 草稿模块复用键值缓存的架构图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/hy-dfly2.jpg)

*图片来源：腾讯混元 Hy4 flash 模型卡 · DFly2 草稿模块复用键值缓存的架构图（取阅），2026-11-23。*

对自部署团队，活跃规模主要说明每步动用多少计算，权重存储、缓存和多并发开销还需要单算。混元的 WorkIndex 成本来自团队自己的办公任务口径，适合用来选择复测任务。

## 📦 GitHub Trending

以下为北京时间 11 月 23 日清晨抓取的 GitHub 语言日榜。增量沿用页面的 stars today 口径。

| 仓库与语言榜 | 累计星标 | 榜单新增 |
| --- | ---: | ---: |
| FlashML-org/FreeVideo（Python） | 4,347 | +209 |
| Fission-AI/OpenSpec（TypeScript） | 75,466 | +50 |

FreeVideo 围绕本地视频生成提供界面；OpenSpec 把编码助手的变更计划、规范和任务落在项目文件里。一个看本机资源能否承载视频模型，一个看改动在动手前能否审阅。

![OpenSpec 官方 README 中的进度面板](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/openspec-dashboard.jpg)

*图片来源：OpenSpec 官方仓库 README 演示图（取阅），2026-11-23。*

![FreeVideo 官方仓库项目介绍](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/freevideo-readme.png)

*图片来源：FreeVideo 官方仓库 README 原文摘录（取阅），2026-11-23。*

## AI Coding｜语音请求可以按次购买

Deepgram 11 月 20 日宣布，智能体可通过 AWS Marketplace 为 Nova-3 转录和 Flux TTS、Aura-2 合成逐笔付费。服务先给出美元报价，通过 AgentCore 获取支付令牌后，再重发业务请求。该功能适用于 AgentCore payments 已可用的 AWS 区域。

![收到报价后获取令牌、携令牌重发与响应的流程](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/aws-flow.jpg)

*图片来源：AWS 技术博客 · x402 支付流程图，2026-11-19。*

试接时可以先用固定批量录音，检查报价、余额和到账记录能否串起来。Deepgram 当前文档覆盖三个批处理接口，实时 WebSocket 仍需另选接入路径。

## 值得关注｜从披露走向可比数据

- 实时视频选型同时记录首帧、尾部延迟和每分钟成本，固定输入分辨率与输出质量后再做对照。
- 自部署先用日常图文任务验证权重格式和工具链，再把上下文逐档拉长，测并发下降幅度。
- 观察后续 APEC 年度报告能否给出培训覆盖、企业采用及岗位转换的数据。

## 编辑说｜先用可复现的口径比成本

这批披露使用的单位各不相同：视频按画面，办公按任务，语音按请求。做横向选型时，先把计量单位换成团队实际验收的交付物。

今天的四条主线都落在具体的实施条件上：算力怎样兑现为可用视频，数字合作怎样跟踪就业，开放权重怎样部署，语音调用怎样对账。

## 🔗 引用链接

- [《金融时报》亚马逊与 Decart 谈判报道](https://www.ft.com/content/dc7749dc-0f40-44a6-80bc-7f747d383616)
- [The Information 对谈判报道的转述](https://www.theinformation.com/briefings/amazon-nearing-7-5-billion-purchase-decart)
- [Amazon 的 Trainium3 UltraServer 公告](https://www.aboutamazon.com/news/aws/trainium-3-ultraserver-faster-ai-training-lower-cost)
- [Decart 的 Lucy 2.6 发布说明](https://decart.ai/publications/introducing-lucy-2-6)
- [APEC 领导人会议文件及合影](https://apec.org/meeting-papers/leaders-declarations/2026/2026-apec-leaders-declaration)
- [APEC 数字与新兴技术就业声明原文](https://apec.org/meeting-papers/leaders-declarations/2026/2026-apec-leaders-declaration/apec-statement-on-harnessing-digital-and-emerging-technologies-for-employment-and-prosperity-in-the-asia-pacific-region)
- [腾讯混元 Hy4 flash 模型卡](https://huggingface.co/tencent/Hy4-flash)
- [GitHub Trending Python 日榜](https://github.com/trending/python?since=daily)
- [GitHub Trending TypeScript 日榜](https://github.com/trending/typescript?since=daily)
- [FreeVideo 仓库及 README](https://github.com/FlashML-org/FreeVideo)
- [OpenSpec 仓库及 README](https://github.com/Fission-AI/OpenSpec)
- [Deepgram 11 月 20 日更新日志](https://developers.deepgram.com/changelog/2026/11/20)
- [Deepgram 的 AWS Marketplace 接入文档](https://developers.deepgram.com/docs/aws-marketplace-x402)
- [AWS AgentCore 与 Marketplace x402 技术博客](https://aws.amazon.com/blogs/machine-learning/enable-ai-agents-to-pay-for-aws-marketplace-x402-products-with-amazon-bedrock-agentcore-payments/)
