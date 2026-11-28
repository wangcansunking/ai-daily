---
title: "Claude API 换成 Rust，单请求 CPU 开销少了 70%"
date: "2026-11-28"
weekday: "星期六"
slug: "anthropic-messages-api-rust"
category: "big-tech-models"
description: "Anthropic 用 Claude 重写生产接口，让两套代码处理同一份流量，缩短了回答开始前的等待。"
cover: "https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/anthropic-messages-api-rust.png"
batch: "morning"
cover_style: "minimalist-still-life"
---

# Claude API 换成 Rust，单请求 CPU 开销少了 70%

Claude API 换了一套底层，客户的请求照常发。Anthropic 11 月 25 日披露，Messages API 从 Python 迁到 Rust 后，单请求 CPU 开销约降 70%，首 token 中位延迟约降 20%。更惊人的是换挡前的一组数字：测试通过率约 98%，真实流量输入却只有约四分之一对得上。

## 要点速览

- 最初只把输出解析移到 Rust，API 的 CPU 节省约 8%–10%。
- 整体迁移后，单请求 CPU 约降 70%，内存降逾 80%，首 token 中位延迟约降 20%。
- 团队让两套 API 共用一次模型生成，用真实流量比对每个处理阶段的差异，再由 Claude 和工程师协作修复。

## 先换一截，省下的 CPU 被来回交接吃掉

Anthropic 最初只把解析模型输出的代码重写成 Rust，其他步骤留在 Python。这样动得少，却要让每个 token 多次跨过两种语言的边界。公司说，当时 CPU 开销下降约 8%–10%；面对新增的复杂度，这点收益不足以支撑长期维护两段混合代码。

Messages API 的工作从用户送来的配置和消息开始。它准备模型输入，组织工具定义，随后把模型输出整理成文本、思考内容或工具调用，逐步发回客户端，同时统计用量。把整条请求链迁走，才能消除这段热路径频繁交接的成本。

![部分重写与完整重写的请求路径](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/claude-path.png)

*图片来源：Anthropic 工程博客，2026-11-25。*

## 98% 的测试通过，真实流量先给了 24%

工程团队原有约 700 个集成测试。迁移之后它们大多通过，但检查要求容易随着代码一起变松。Anthropic 复看移植后的测试，发现有超过 100 处断言被削弱。与此同时，老接口还持续上新，Rust 团队追的是一直在移动的行为。

生产流量揭开了这层落差。举例说，一边会把工具调用中的 emoji 转成转义表示，另一边写入原字符。对应用开发者，两段字符串都能解析；在送进模型之前，两个提示已经不完全一样。再加上流量会反复经过热门路径，单元测试里只出现一次的小差别可以在生产环境中大量出现。

团队把旧 Python 服务留在前台，让 Rust 在旁边处理同一份流量。四月末真实输入匹配率约 24%，曲线一度跌至约 6.5%，随后随着修复爬到 99.6%。曲线采用抽样输入的七日中位数，先看内部流量，再转向更广的样本。这是报告所示的输入一致性曲线，API 可用性与答复质量另有各自口径。

![Claude API 真实流量输入匹配率图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/claude-match.png)

*图片来源：Anthropic 工程博客 Claude API 真实流量输入匹配率图，2026-11-25。*

## 模型只答一次，两套接口分头处理

如果让 Python 和 Rust 各自调用一次 Claude，哪怕问同一个问题，也可能拿到两种表达。API 团队需要隔离这块随机性，因此让模型生成一次，再把同一次回复交给两条输出路径。这样一来，某边少了一段换行或用量字段，就可以沿接口处理流程往回找。

工程师也没有直接阅读客户的原始请求和回答。系统记录差异出现在哪个阶段、发生在多长内容的哪个位置，然后把同类差异归组。Claude 用自己编写的代表性请求复现行为，提出补丁，再交给工程师评审。生产旁路继续观察变化，确认新版本是否减少了该类差异。

这个安排的价值，在于写迁移代码和提供真实参照由不同机制承担。代码助手可以提出一个看似自然的实现，老 API 则能持续给出客户端实际依赖的行为。开发团队仍有余地判断某个差异究竟该保留，还是应作为单独的产品改动处理。

![Anthropic 披露的工程师与 Claude 协作界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/claude-slack.png)

*图片来源：Anthropic 工程博客，2026-11-25。*

工程团队还把容易重复犯的错误写进项目说明，供后续代理读取。例如，一次评审指出代理猜测了原版的行为，随后团队将保留原行为定为明确规则，又从已有改动中找出七处类似猜测。迁移越长，这种积累越能减少同一个问题反复出现。

生产旁路的反馈要等真实流量经过，可能要几个小时甚至几天。本地的小试验则能在数秒内反复运行。将两种节奏接起来，工程师可以把精力花在有分歧的行为上，同时让重复的尝试继续推进。

## 用户最先感受到的是回答开始得更快

| 接口指标 | 迁移后变化 | 读法 |
| --- | --- | --- |
| 每请求 CPU | 约少 70% | 服务请求使用的 CPU |
| 每请求内存 | 降逾 80% | 接口侧资源消耗 |
| 首 token 中位延迟 | 约短 20% | 流式回答开始得更早 |

公司图表的 CPU 数据取自一个区域，内存取自生产全量，首 token 延迟则取自一段较短窗口内 Claude Code 的客户请求。

首 token 体现的是等待第一段输出的时间。一段 3000 字的回答，后面的生成速度、工具响应和上下文长度仍然会左右总耗时。接口层 CPU 节省与 GPU 推理、账单价格也存在各自的计量边界。

![Anthropic 公布的单请求资源与首 token 延迟图表](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/claude-speed.png)

*图片来源：Anthropic 工程博客，2026-11-25。*

还有一个很容易漏掉的收益：Anthropic 表示，这次迁移没有停掉线上服务，也没有冻结新功能开发。对一个不断加功能的 API 来说，新版本可以先接住流量、拿到反馈，再逐步承担更多工作，这比选中哪门语言更难复制。

对已经接入 Claude 的开发者，这意味着可以在原有接口上关注流式输出的起步延迟。对准备改写服务的团队，这个案例给出的参照则是流量本身：测试里的绿灯很重要，用户实际走过的分支能把迁移成本讲得更清楚。

Claude 帮忙换掉了支撑自己的接口，而真正让这次换挡跑通的，是让新旧两套服务在同一批请求面前说话。

## 来源

- [Anthropic 工程博客《Migrating our Messages API to Rust》及文内图表](https://www.anthropic.com/research/migrating-api-to-rust)
