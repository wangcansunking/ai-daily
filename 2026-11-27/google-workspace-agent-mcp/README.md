---
title: "谷歌给 8 个办公应用开 Agent 通道，旧接法 2 月起换挡"
date: "2026-11-27"
weekday: "星期五"
slug: "google-workspace-agent-mcp"
category: "big-tech-models"
description: "从 Gmail、Drive 到 Calendar，谷歌为云端和本地 Agent 提供两条正式接入路线。"
cover: "https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/google-workspace-agent-mcp.png"
batch: "morning"
cover_style: "minimalist-still-life"
---

# 谷歌给 8 个办公应用开 Agent 通道，旧接法 2 月起换挡

让 AI 翻邮件、找附件，再约一场会，过去往往要给它分别接三套接口。谷歌现在把 Gmail、Drive、Calendar 等 8 个应用接进 Workspace 的 Agent 工具层，还给旧接法定了迁移时间：2027 年 2 月 1 日。

## 要点速览

- Google Workspace 在 11 月 24 日宣布远程 MCP 与官方 CLI 正式可用，向 Workspace 客户和开发者推出。
- 托管 MCP 面向云端和网页 Agent，官方 CLI 面向本地、虚拟机和容器里的终端 Agent。
- 新要求针对 Agent 对 Workspace 数据的访问，技术执行从 2027 年 2 月 1 日开始。
- 从读取材料到对外共享，可以在同一次任务里分别处理权限和人工确认。

## 一个助手，两条官方接入路线

假设你想让助手准备客户会议。它需要读 Gmail 里的最近邮件、从 Drive 找到提案，再用 Calendar 找出空档。每个应用的动作和权限都不同，过去集成者要把许多底层请求拼成模型能理解的工具。

MCP 是模型上下文协议。客户端可以发现服务提供的工具及其参数，再把某项工作交给对应工具。谷歌托管的远程服务把 Workspace 的具体动作包装好，网页或云端 Agent 可以使用统一协议来接入这些服务。

| 路线 | 适合谁 | 工具入口 |
| --- | --- | --- |
| 远程 MCP | 网页、云端 Agent | Google 托管服务和预定义工具 |
| 官方 CLI | 本地终端、虚拟机或容器里的 Agent | 命令行、打包技能与动态命令发现 |
| 传统 REST | 非 Agent 的传统应用功能 | 按应用调用原有 API |

首发覆盖 Gmail、Drive、Docs、Sheets、Slides、Calendar、Chat 和 Tasks。Keep 仍列为后续支持。公告列的是有对应服务器的应用范围，具体有哪些读写动作，还取决于各服务公开的工具清单。

## 从找到提案，到把提案发出去

先让助手在已有权限内查找邮件和文件，把会议背景整理进草稿。这一步即使做得很快，也只完成了内容准备。下一步如果要把共享链接发给外部客户，动作会改变谁能看到文件，用户就需要知道即将发生什么。

谷歌的 MCP 可以在敏感动作前发回一个表单，由客户端展示给用户。管理员可以设置外部 Agent 的人工复核规则，已有的资料防泄露策略也能随请求生效。体验上的差别在于，用户可以先看见动作、对象和目标，再决定要不要继续。

![Google Workspace 文档中的 用于说明确认机制的 Gmail 界面示意](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/workspace-confirm.png)

*图片来源：Google Workspace 开发者文档，查阅于 2026-11-27。*

Gmail 图展示通用确认交互，当前 Gmail MCP 工具清单列有读取和创建草稿，发送仍应以实际工具清单为准。

这一环也给客户端提出了具体要求。假如它只会把工具结果塞回模型，却没有地方展示确认表单，读邮件或许能跑通，到共享步骤就可能停住。此时产品要给用户保留草稿和下一步状态，而不是让用户重新描述整个任务。

![MCP 客户端与 Workspace 服务之间的人工确认时序](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/workspace-flow.png)

*图片来源：Google Workspace 开发者文档，查阅于 2026-11-27。*

确认环节也会改变对话界面的设计。一次任务可以在邮件草稿和会议邀请之间切换，页面最好保留任务位置，把当前等待确认的动作单独展示。拒绝创建日程之后，用户仍能回到邮件草稿继续改文字。

![Google Calendar 创建会议前的确认界面示意](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/workspace-calendar.png)

*图片来源：Google Workspace 开发者文档，查阅于 2026-11-27。*

## 网关管去向，办公应用管动作

Workspace 公布接入的同时，Google Cloud 也推出了 Agent Gateway。它服务于模型、MCP 和 Agent2Agent 流量，可以结合 Agent Registry 管理批准的目标，并按云端策略处理请求。Workspace 自己仍负责办公数据和应用动作。

![Google Cloud 多 Agent 参考架构，展示网关与多个代理及其工具的关系](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/workspace-arch.png)

*图片来源：Google Cloud Architecture Center，查阅于 2026-11-27。*

两者出现在同一份发布里，开发者容易以为每个 Workspace 助手都得搭一套 Cloud 网关。实际上，一个给办公室找材料的本地工具可以先走 Workspace CLI。需要管多条模型和工具流量的云端团队，才有理由考虑集中入口。

对团队而言，更值得提前想好的是哪些动作应当设计成可撤回。搜索没有写入成本，覆盖共享表格却可能影响同事，发出邀请还会给所有参会人送通知。即使它们都是模型的一个工具调用，界面也应给出不同反馈。

## 2 月 1 日临近，旧集成怎样迁移

谷歌公布的两周推出窗口覆盖远程 MCP、CLI 和相关管理员功能。2 月 1 日指的是技术执行开始的时间。工程上，迁移首先要把团队现在靠旧接口完成的动作，映射到官方工具里已经存在的能力。

- 把现有产品按用途拆开。固定表单同步仍可以作为传统应用功能，按用户指令动态找资料、串动作的能力走 Agent 路线。
- 在远程 MCP 路线里，为 Agent 使用标记为 Agent 用途的 OAuth 客户端。若同一产品也有传统非 Agent 功能，谷歌指引建议使用单独的客户端。
- 用一个低风险任务跑通读取和草稿，再为需要确认的动作加上可见的等待、拒绝与恢复状态。假如目前缺少某项工具，就让流程停在一份可继续处理的草稿。

旧应用如果把好几种写操作包装成一个按钮，迁移时也可以借机缩小每一步的范围。先让 Agent 把候选会议时间列出来，再让用户选定时间和收件人。出了意外，也容易从任务记录里找到卡在哪一步。

对最终用户来说，这次变化最容易感知的地方，可能是助手开始真正跨应用完成差事。找邮件、找附件、填草稿可以连续完成，到了共享或日程变更，用户还能看清准备执行的动作。

谷歌给八个办公应用补上的，是一套让 Agent 能接工具、能执行、也能在需要人作决定时停下来的入口。

## 来源

- [Google Workspace 官方发布](https://workspaceupdates.googleblog.com/2026/11/mcp-servers-cli-standardized-usage-tiers-now-available.html)
- [Google Workspace 产品博客](https://workspace.google.com/blog/product-announcements/built-for-trust-and-control-new-ai-agent-protections-and-developer-tools-in-google-workspace)
- [Google · 远程 MCP 配置](https://developers.google.com/workspace/guides/configure-mcp-servers)
- [Google · 官方 Workspace CLI](https://developers.google.com/workspace/guides/workspace-cli)
- [Google · 人工确认界面与时序](https://developers.google.com/workspace/guides/mcp-elicitation)
- [Google Cloud · Agent Gateway 官方发布](https://cloud.google.com/blog/products/identity-security/deploying-agents-safely-in-your-enterprise)
- [Google Cloud · 多 Agent 参考架构](https://docs.cloud.google.com/architecture/multiagent-ai-system)
