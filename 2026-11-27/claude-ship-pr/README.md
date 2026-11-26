---
title: "Claude Ship 接手 PR 的 3 道工序，代码交上去还能接着修"
date: "2026-11-27"
weekday: "星期五"
slug: "claude-ship-pr"
category: "big-tech-models"
description: "Anthropic 把代码审查、CI 修复和按策略批准串进 GitHub 拉取请求。"
cover: "https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/claude-ship-pr.png"
batch: "morning"
cover_style: "photorealistic-editorial"
---

# Claude Ship 接手 PR 的 3 道工序，代码交上去还能接着修

AI 写完一段代码只要几分钟，拉取请求却可能在评审意见、红灯和合并冲突之间往返半天。Anthropic 的 Claude Ship 想接过这段等待：阅读改动、处理反馈，再由仓库策略决定机器能不能给出批准。

## 要点速览

- Anthropic 在 11 月 23 日推出 Claude Ship 测试版，面向连接 GitHub 仓库的 Claude Team 和 Enterprise。
- 服务由已经正式可用的 Code Review，以及测试中的 PR Steward、Claude Approvals 组成。
- Claude Code 的新命令可以从推送代码、创建拉取请求一路跟到反馈和 CI。
- Claude Code 团队称 6 月到 9 月合并量翻倍有余，回滚占比下降逾 40%，数据来自团队整体流程。

## 三个角色接力，哪一段最能省下往返？

Code Review 是第一棒。它从多个角度阅读变更，在同一个代码库里追踪相关上下文，再把发现发回拉取请求。团队可以为仓库选 Quick、Standard 或 Deep。改一张文档和改核心逻辑，所需审查深度不同。

![Code Review 在 GitHub 拉取请求里提交发现](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/ship-review.png)

*图片来源：Anthropic Claude Ship 发布页，2026-11-23。*

PR Steward 接第二棒。CI 检查亮红灯、分支出现冲突，或者评审提了意见，它可以动手修改。每次推送后会说明改了哪些地方并给出撤销线索；开发者可以从 PR 评论指挥它，也可以让它停下来。

![PR Steward 处理修复后在 PR 留下反馈](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/ship-steward.png)

*图片来源：Anthropic Claude Ship 发布页，2026-11-23。*

最后是 Claude Approvals。团队把审批策略写到仓库文件里，由它判断改动满足哪些条件。常规改动可以获得机器批准，碰到需要负责人出面的情形则等待人类签字。合并动作依然受仓库权限、分支保护和必需检查约束。

| 环节 | Claude 拿走的工作 | 团队手里的决定 |
| --- | --- | --- |
| 审查 | 阅读变更、发出评审意见 | 为仓库选择深度和触发时机 |
| 修复 | 跟进失败检查、冲突与反馈 | 接受修复、撤销或中止 |
| 批准 | 按仓库策略评估改动 | 定义放行条件和人工审批范围 |

![满足审批策略的 GitHub 拉取请求](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/ship-approval.png)

*图片来源：Anthropic Claude Ship 发布页，2026-11-23。*

这里最容易被低估的是第二棒。以前评审工具报出一个问题，开发者得重新打开已经离开一小时的上下文。机器人如果能处理机械性的修复，节约的往往是人的切换时间。遇到逻辑分歧或需求有歧义时，仍得有人摆出取舍。

## 从一条命令起跑，也可以按仓库接管

个人入口是 Claude Code 里的 `/ship`。改动准备好后，Claude 推送代码、打开 PR、查看反馈并跟进失败检查。Anthropic 说该命令正在 Claude Code 和桌面应用中推出，按常规 Claude Code 使用量计算。最后能合并什么，由用户权限与仓库规则决定。

![Claude Code 终端里的 /ship 示例](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/ship-command.png)

*图片来源：Anthropic Claude Ship 发布页，2026-11-23。*

仓库入口由管理员逐个启用 Code Review 和 PR Steward。配好默认分支上的策略文件后，可以让 Claude Approvals 先在观察模式里运行，积累一批判断记录，再决定要不要把它纳入必需检查。仓库级服务消耗量取决于 PR 大小、审查深度和修复次数。

一种自然的起点是维护文档或内部工具的仓库。文案、测试样例或简单构建问题经常要改，出错也方便回滚。可以让机器先完成来回修补，保留现有签字流程，再观察人要处理的究竟是实质分歧还是重复劳动。

两个入口也适合不同的工作节奏。临时修补可以由提交人主动发起，出了问题仍回到同一条 PR 讨论。一个每天几十次更新的仓库，则会更在意默认何时运行、一次修到什么程度，以及多少轮之后该叫人回来。

例如 CI 失败的原因可能只是漏放一份测试文件，也可能是上游服务暂时不稳定。第一种情况通常适合补丁，第二种更适合等待服务恢复或交给维护者处理。自动修复产出的每次推送，都应让接班的人能直接读懂。

当自动修复和自动批准同时启用，两个角色也需要分开对待。修复可以提出更改，批准仍要遵守仓库的边界。像依赖升级这种看似例行的动作，一旦涉及运行时兼容性，也可以继续走原来的人工审批流程。

## 合并量翻倍有余，数字还有一层上下文

Anthropic 披露，在 Claude Code 团队 6 月至 9 月的整体流程里，合并的 PR 数超过原来的两倍，同时回滚占比下降逾 40%。Code Review 是其中一环，前后两段的 PR 构成与其他流程变化未在同一数字里拆开。

更具体的是公告里的文档案例。一条客户反馈指出 Analytics API 文档容易让人误以为管理员密钥能读取特定套餐的用量。团队用 Claude Tag 开出修订 PR，后续经过代码评审、文档人员签字和审批，当晚合并。

这个例子展示了一种分工：机器把证据、修改和状态连续传下去，懂业务的人在关键节点给出判断。团队也可以用同一套机制处理有明确目标的小改动，而把产品行为或权限模型的变化留给人工讨论。

Claude Ship 最先改变的可能是开发者提交代码后的半小时：机器接着处理反馈和红灯，人可以把注意力留给下一项工作。

## 来源

- [Anthropic · Claude Ship 发布与产品截图](https://claude.com/resources/articles/introducing-claude-ship)
- [Anthropic · Claude Code 团队的工作方式](https://claude.dev/blog/solving-code-review-at-anthropic/)
