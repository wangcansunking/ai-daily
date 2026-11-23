---
title: 百炼 Token Plan 加入 Qwen4-Max，个人和团队怎样算额度
date: 2026-11-22
weekday: 星期日
slug: aliyun-token-plan-2026-11-22
category: domestic
description: 用预览时段、额度归属和交互使用限制厘清订阅成本。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/aliyun-token-plan-2026-11-22.png
batch: noon
track: deep-dive
cover_style: abstract data visualization
---

# 百炼 Token Plan 加入 Qwen4-Max，个人和团队怎样算额度

![汇聚成彩色条带的流动点线](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/aliyun-token-plan-2026-11-22.png)

阿里云百炼 Token Plan 在 11 月 16 日向个人版和团队版加入 Qwen4-Max，预览版则有独立优惠窗口。对每天在编辑器里用 AI 的开发者，订阅的实际变化涉及三件事：可以接入哪些工具，任务如何扣 Credits，哪些流量需要走其他产品。

## 要点速览

- 百炼的 Token Plan 以 Credits 计量，支持在兼容工具中交互使用。
- 正式版 Qwen4-Max 已列入两种版本，Qwen4-Max-Preview 有独立的截止时间和优惠条件。
- 个人按套餐订阅，团队按席位订阅，两套额度与 API Key 独立。
- 官方 FAQ 将个人版限定为交互开发，生产服务、批量脚本和计划后台任务需要另选接入方式。

## 同一个模型名，先分清两条产品更新

产品更新日志在 11 月 16 日记录了 Qwen4-Max 正式版的支持，个人、团队版均可用。11 月 11 日的另一条纪录说的是带 Preview 后缀的预览版。预览窗口从 11 月 11 日 10:00 到 11 月 25 日 24:00，采用 UTC+8。

![Token Plan · 正式版与预览版更新条款](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/token-source-update.png)

*图片来源：阿里云 Token Plan 产品更新原文摘录图（取阅），2026-11-22。*

日志写的 Credits 消耗最低为标准费率的 20%，并列出个人版在 22:00 至次日 08:00 的额外折扣条件。做成本估算时，应先把当前 API 使用的模型标识和适用时段写清楚，再从控制台记录实际扣减。预览期间的账单适合验证该期工作量，续用成本仍需按届时的正式规则算。

例如，同一项代码整理既可在交互会话里分三轮提问，也可以给助手一次长任务。计量组件、上文长度和工具调用次数不同，结果会出现差异。以一次通过人工验收的任务为单位记录扣减，比只比较发了多少次请求更接近日常使用。

## 个人与团队版本如何分账

| 比较项 | 个人版 | 团队版 |
| --- | --- | --- |
| 订阅对象 | 单一开发者按套餐订阅 | 团队按席位订阅 |
| API Key | 个人版独立生成 | 团队版独立生成 |
| 管理 | 个人管理自己的用量 | 管理员有席位和成员用量管理 |

![Token Plan · 个人版与团队版差异](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/token-source-editions.png)

*图片来源：阿里云 Token Plan FAQ 版本说明原文摘录图（取阅），2026-11-22。*

两种产品可在同一个阿里云账号下分别订阅，官方 FAQ 说明它们独立计费、额度不共享，API Key 不能混用。团队版还提供单点登录等管理能力。需要给后来加入的同事开权限时，可以先核对席位和账号配置，再配置编辑器。

密钥归属和项目归属也适合分开管理。比如个人在练习仓库里试工具，团队可以在另一套配置里做评审，记录哪一个账号和版本承担该任务。这样遇到账单异常或密钥过期时，排查从订阅侧开始就有明确对象。

## 怎样在编辑器里试用

- 从 Model Studio 的 Token Plan 入口选计划并获取对应凭据，打开文档中所用工具的配置指南。
- 用公开或测试仓库的一个交互任务检查连接，记录模型、任务和 Credits 扣减。
- 用相同材料跑几次，记下任务成功率、等待时间、额度变化和人工修订。

![Token Plan · 个人版生产自动化限制](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/token-source-usage.png)

*图片来源：阿里云 Token Plan FAQ 使用限制原文摘录图（取阅），2026-11-22。*

产品总览列有 Claude Code、Codex 和 Qwen Code 等接入指引。个人版 FAQ 允许的是指定工具中的交互使用，明确列出的限制包括生产自动服务、批量脚本和定时后台任务。把编辑器里开发者发起的调用和上线后的服务流量分开，是订阅试用最早需要确定的边界。

如果团队同一周开始使用多个助手，可以让成员各选一种常见任务，先记录现有流程耗时，再在相同约束下试用。把 Credits 和人工审核放在同一份表里，能看出哪些任务适合继续采用交互订阅。

一句话总结：Token Plan 新增模型之后，选套餐要把交互任务的实际扣减、密钥归属和允许使用的场景一起算进去。

## 来源

- [阿里云 Token Plan 产品更新](https://www.alibabacloud.com/help/en/model-studio/token-plan-product-updates)
- [阿里云 Token Plan 总览](https://www.alibabacloud.com/help/en/model-studio/token-plan-overview)
- [阿里云 Token Plan FAQ](https://www.alibabacloud.com/help/en/model-studio/token-plan-faq)
