---
title: Claude Code Desktop 近两个月，五项更新怎么用
date: '2026-11-24'
weekday: 星期二
type: deep-dive
slug: claude-code-desktop-recent-features-2026-11-24
category: tool
description: 从终端转桌面到远控流式回复，梳理 Claude Code Desktop 五项更新的适用范围与上手方法。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/claude-code-desktop-recent-features-2026-11-24.png
cover_style: cinematic-scifi
---

# Claude Code Desktop 近两个月，五项更新怎么用

从 2026 年 9 月 24 日到 11 月 24 日，Claude Code Desktop 有五项更新值得放进日常编码流程：长会话空闲后能自动整理上下文，云端审查结果更好读，手机也能更早看到本地任务的回复。

Claude Desktop 的 Code 标签页还有自己的一份 Claude Code，排查功能是否到位时，要把应用版本和会话版本分开看。

## 要点速览

- **终端转桌面** · 9 月 29 日，新增 `claude --desktop`，可直接打开目录或续接已有会话。
- **长会话** · 10 月 30 日，Desktop Code 和 VS Code 新增长会话空闲压缩。
- **代码审查** · 10 月 31 日，云端及相关远控场景的 `/code-review` 改用发现卡片呈现。
- **远程接手** · GitHub 的 11 月 16 日版本开始让指定的 Remote Control 会话把回复流式传到手机或网页。
- **桌面更新** · 11 月 17 日，Claude Desktop 官方发布说明列出 Stable 和 Latest 两个更新通道。

| 官方记录 | 适用范围 | 最值得用的场景 |
| --- | --- | --- |
| 9 月 29 日 · Claude Code 2.1.285 | CLI 到 Desktop | 接着用桌面检查变更 |
| 10 月 30 日 · Claude Code 2.1.314 | Desktop Code、VS Code | 大型重构暂停后续接 |
| 10 月 31 日 · Claude Code 2.1.316 | 桌面与网页的相关云端、远控审查 | 提交前逐条核对问题 |
| 11 月 16 日 · Claude Code 2.1.326 | Desktop、VS Code、命令行远控服务 | 离开工位时查看回复 |
| 11 月 17 日 · Claude Desktop 4.29.0 起 | 符合条件的 Mac、Windows 客户端 | 提前验证新功能或修复 |

表中 Claude Code 日期按 GitHub 发布记录的 UTC 日期。2.1.285 和 2.1.326 分别对应北京时间 9 月 30 日和 11 月 17 日。版本号标识的是更新记录，桌面端实际可用版本以 Code 会话里查到的结果为准。

![Claude Code 桌面版官方演示封面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/claude-code-desktop-demo.jpg)

*图片来源：Anthropic 桌面版改版公告所嵌官方演示封面，2026-04-14。*

## 从终端把手头的工作带进桌面

9 月 29 日发布的 Claude Code 2.1.285 增加了 `claude --desktop`。在仓库目录执行会直接打开 Desktop；加 `--continue` 能接上该目录最近的会话，指定旧会话则用 `--resume <会话 ID>`。

这项入口适合在终端完成初步排错后，把含有日志、约束和改动背景的会话带进桌面，再打开变更面板核对改动。要交接指定会话，先在终端用 `/status` 找到 ID，再结束原会话后打开。

按官方文档，这个命令要求已安装 Claude Desktop，并通过 Claude 订阅登录，适用于 macOS 和 x64 Windows。正在另一个终端打开或仍在后台运行的会话，等原任务结束后再转接。

![Claude Code 的命令行会话](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/claude-code-terminal.jpg)

*图片来源：Anthropic Claude Code 产品页，终端界面示意，2026-11-24 取阅。*

## 长会话暂停前，先留一份可复查的交接

10 月 30 日的 2.1.314 给 Desktop 的 Code 标签页和 VS Code 加入空闲压缩。会话超过 20 万 token 且处于空闲状态时，Claude Code 会生成摘要。若提示缓存已过期，下次发消息时会使用摘要。

适合把横跨多个目录的重构留在同一会话里。比如上午改了接口和调用方，下午回来补失败用例，恢复时可以先请 Claude 列出它理解的改动边界和待验证项，再对照实际 diff 与测试结果往下做。

这项升级由会话自动触发。想知道当前上下文用了多少，可以看 Code 的用量信息；需要自己决定何时压缩时，仍可在输入框发 `/compact`。对于涉及多个调用方的任务，可先把关键路径和失败测试写进交接消息。

摘要适合保留工作脉络。测试命令、关键失败日志和必须维持的行为，最好留在可重新打开的文件或任务记录里。续接后先核对这些依据，再让模型扩大改动范围。

若前后两段工作由不同开发者负责，还可以请接手者先标记需人确认的约束。

![Claude Code 的 VS Code 扩展界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/claude-code-vscode.jpg)

*图片来源：Anthropic Claude Code 产品页，VS Code 扩展界面，2026-11-24 取阅。*

## 提交前跑一次审查，把发现变成复测清单

2.1.316 调整的是审查结果的呈现。官方更新日志写明，从 Claude Desktop 的 Code 标签页或网页端发起 `/code-review`，相关云端会话和 `claude remote-control` 会话现在显示发现卡片。

命令本身是 Claude Code 共用能力。

在当前分支改动基本齐备后，直接在会话输入框运行 `/code-review`。把审查放在提交之前，可以先处理会改变运行结果的发现，再评估风格或整理建议。需要时也可以把审查目标缩到一个路径或指定改动范围。

例如重写分页逻辑后，可以逐条核对发现有没有指出触发条件，再决定是否补空页、边界页或顺序变化的测试。请 Claude 修复时，先选定要处理的问题，改完重看 diff 并重跑相应用例。

本地桌面会话的官方操作说明也值得对照。文档列出本地、SSH、WSL 会话的按文件分组卡片，以及进入变更逐条查看的入口。上面的 10 月更新对应的是云端和远控结果的呈现范围。

![Claude Code 网页版会话界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/claude-code-web.jpg)

*图片来源：Anthropic Claude Code 产品页，网页版会话界面，2026-11-24 取阅。*

## 离开工位，用手机看正在生成的回复

2.1.326 改进了 Remote Control：`claude remote-control`、Claude Desktop 和 VS Code 会话可以把正在生成的回复流式传到手机或浏览器。它适合编译、测试或重构还在进行、开发者暂时离开电脑的场景。

在 Desktop 的本地 Code 会话中，打开会话详情里的 **Remote Control** 开关，或输入 `/remote-control`。

连接后，在同一账号的 Claude 应用或 `claude.ai/code` 会话列表中打开它。团队与企业账号由 Owner 开启组织侧权限。

例如让 Claude 跑回归测试时，可以要求它先报告第一条有区分度的失败，再给修改方案。手机端看到回复开头后，就能较早判断是等整轮测试结束，还是补上复现条件让下一步更精确。

远程接入时任务仍在原来的计算机上执行。测试依赖本地服务、设备或仓库文件时，应让原会话和机器保持在线。回到工位后，用原环境检查测试日志和工作树，再处理提交。

跨设备看同一会话时，最好先确认仓库与分支，再发新指令，尤其是项目有多个并行任务时。

## 要跟进修复，先看桌面通道和会话版本

11 月 17 日的官方发布说明列出 **Stable** 和 **Latest**。Stable 是默认通道，测试周期更长；Latest 大约每天推送一个版本。官方安装指南列出的门槛为 Claude Desktop 4.29.0。

在 **Settings → This computer → System** 找 **Release channel**；旧布局可查看 **Settings → Desktop app**。

选择 Latest 后，下次检查更新时生效。Mac 从 Claude 菜单检查更新，Windows 从 Help 菜单检查。

团队如果正在验证一项 Code 修复，可以先用一台机器试 Latest，复现同一个任务，再到本地 Code 会话输入 `/status` 确认实际版本。这样在记录结果时，可以同时写下桌面应用版本、会话版本和复现步骤。

官方说明列出 Linux、Microsoft Store 安装和受 IT 策略管理的设备等限制。切回 Stable 也不会立即降级，应用会等更高版本的 Stable 到来。评估是否切换时，把这个更新节奏算进测试计划。

若团队要判断修复是否来自一次更新，可以保留稳定通道的对照环境，在两个版本上用相同的仓库和复现步骤验证。

日常可先试终端转桌面和提交前的审查卡片，再按任务需要用空闲压缩续接长会话、用远控流式回复照看运行中的工作，并用会话版本确认更新是否到位。

## 信源

- [Anthropic GitHub · Claude Code 2.1.285](https://github.com/anthropics/claude-code/releases/tag/v2.1.285)
- [Anthropic GitHub · Claude Code 2.1.314](https://github.com/anthropics/claude-code/releases/tag/v2.1.314)
- [Anthropic GitHub · Claude Code 2.1.316](https://github.com/anthropics/claude-code/releases/tag/v2.1.316)
- [Anthropic GitHub · Claude Code 2.1.326](https://github.com/anthropics/claude-code/releases/tag/v2.1.326)
- [Anthropic 帮助中心 · 发布说明](https://support.claude.com/en/articles/12138966-release-notes)
- [Anthropic 帮助中心 · 安装 Claude Desktop](https://support.claude.com/en/articles/10065433-install-claude-desktop)
- [Claude Code 文档 · Desktop](https://code.claude.com/docs/en/desktop)
- [Claude Code 文档 · 本地代码审查](https://code.claude.com/docs/en/code-review-local)
- [Claude Code 文档 · Remote Control](https://code.claude.com/docs/en/remote-control)
- [Anthropic · Claude Code 桌面改版与演示](https://claude.com/resources/articles/claude-code-desktop-redesign)
- [Anthropic · Claude Code 产品页](https://claude.com/product/claude-code)
