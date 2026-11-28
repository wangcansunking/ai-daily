---
title: "It’s a Plan 上榜：2 种 AI 同事领工单"
date: "2026-11-28"
weekday: "星期六"
slug: "itsaplan"
category: "github-trending"
description: "把工单派给服务器上的模型或本机编码 CLI，让上下文和进度留在同一张项目板。"
cover: "https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/itsaplan.png"
batch: "noon"
cover_style: "magazine-collage"
---

# It’s a Plan 上榜：2 种 AI 同事领工单

一个小需求经常得写两遍。先贴到团队看板，再把描述、讨论和链接复制给编码助手。It’s a Plan 想把第二遍省掉。在这款开源项目管理工具里，AI 有自己的成员身份，也能像同事一样收工单。

## 要点速览

- 11 月 28 日午班的 GitHub Trending TypeScript 日榜上，`croffasia/itsaplan` 排 **第 16**，累计 **2,724 星**，榜单标注今日 **+29 星**。
- 社区版支持服务器内置 Agent 和外部 Runner 两条路线，可以从指派、提及或定时计划发起运行。
- 已经在用 Claude Code 或 Codex 的团队，可以从自己机器上的外部 Runner 小范围试起。

## 点一下负责人，AI 进了交付队列

一般的看板只负责告诉你「这事归谁」。It’s a Plan 给代理也分配一个身份、权限和工单负责人槽位。你仍然从原来的工单写目标，讨论留在评论，团队从项目视图看事情到了哪一步。把 AI 拉进工作流，入口就是大家熟悉的那张卡。

![It’s a Plan 的项目看板](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/itsaplan-board.png)

*图片来源：It’s a Plan 官方仓库看板截图，2026-11-28 取图。*

举个例子，团队准备把三份重复的新手指南合成一份。把路径、目标读者和保留的章节写进工单，先指派给专门处理文档的代理。代理负责形成补丁和摘要，维护者接着在同一个任务语境下决定文档能否合并。

工单可以成为人的讨论和 AI 执行之间的交接点。产品同事补充了写法或边界条件，下次运行前可以让代理从更新后的描述出发。看板的时间线、周期、关联任务和评论，也方便晚些加入的人在一处找齐背景。

## 两种代理，一次先选一种

| 运行方式 | 在哪里工作 | 适合的第一单 |
| --- | --- | --- |
| 内部 Agent | It’s a Plan 服务器上的实例 | 总结项目讨论、起草工单说明 |
| 外部 Agent | 自己机器上的 Runner 和编码 CLI | 读本地仓库、给文档出补丁 |

内部 Agent 可以配置模型、系统提示、工具和可复用技能。团队需要有可用的模型凭据。外部 Agent 通过 `@itsaplan/runner` 接收任务，把工作交给本机已有的 CLI。官方列出的适配对象包括 Claude Code、Codex 和 GitHub Copilot CLI。

![官方演示中的项目时间线](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/itsaplan-timeline.png)

*图片来源：It’s a Plan 官方产品巡览动图，2026-11-28 取图。*

外部 Runner 的优势是能沿用开发机现成的项目配置和账户。代价也在这里。Runner 是否在线、本机依赖是否齐备、CLI 以哪个用户身份执行，都会影响表现。要让它成为团队服务，先选一台权限明确、运行稳定的机器，别把任务绑在某个人合上就睡眠的笔记本上。

## 第一张卡用文档开路

如果准备自建，官方社区版路线是克隆仓库，把 .env.example 复制成 .env，填入应用与 API 的地址、数据库及对象存储相关配置，再运行 `docker compose up -d`。正式访问按官方指南配好 TLS 反向代理。

先建一个小项目，只设「待做、处理中、待合并」三个状态。给人一张卡，给代理一张卡，两张任务独立。代理的第一张卡可以直接用下面这份任务描述。

> 合并 docs 下两份重复的安装说明。保留 Windows 的特殊步骤和现有入口链接，只修改文档。完成后在工单留下改动文件、删掉的重复段落和一个待人工确认的问题。

想体验内部路径，就先用模型代理总结两份文档的内容差异。想让它动仓库，就按 Runner 文档接好外部 Agent，在测试仓库跑一轮。遇到同一张卡反复触发，先暂停定时与状态触发，从一次显式指派把行为跑顺。

![官方演示中的 AI Agent 管理界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/itsaplan-agents.png)

*图片来源：It’s a Plan 官方产品巡览动图，2026-11-28 取图。*

有了产出，在工单里让维护者接手。团队可以用关联的 Pull Request 留住代码线索。仓库支持来自五种代码托管平台的 PR 关联。试跑时先保留人工移动工单的步骤，让接手的人在看板上明确标记交付。

![官方演示中的 Agent 定时任务与工作流](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/itsaplan-automations.png)

*图片来源：It’s a Plan 官方产品巡览动图，2026-11-28 取图。*

## 看板能帮忙交接，也会放大含糊的任务

「把体验做得更好」写在工单里，人可以追问半天，代理却容易迅速产出一堆改动。开头写清楚只动哪个目录、交付物是什么、哪些行为必须保持，会比增加一个代理更早看到收益。

再留意触发条件的叠加。同一个代理可能同时收到指派和提及。第一周让每类工作只走一个明确入口，先看它是否能稳定留下结果和下一步。如果需要每周自动汇总，再引入计划任务，把频率与负责人写清楚。按工单事件触发的计划、Agent 串联工作流和 AI 预算控制属于 Pro 版。

团队还可以为工单留一个简单的交接约定。每次运行结束，评论写明产物链接、还缺哪份信息、下一步谁接手。这样即使 Runner 临时离线，后来的人也能继续工作，少花时间翻模型对话或在聊天群里追问。

还有迁移成本。官方支持从 Linear、Trello 和 Plane 导入，但看板上的字段、状态和自动化需要团队重新梳理。已有成熟流程的团队，可以先用新项目试两周，再决定要不要迁旧工单。

**一句话总结**：It’s a Plan 把 Agent 的入口放在工作发生的工单上，先让它领一张边界明确的卡。

## 来源

- [GitHub Trending TypeScript 日榜](https://github.com/trending/typescript?since=daily)
- [It’s a Plan 官方仓库与产品巡览](https://github.com/croffasia/itsaplan)
- [It’s a Plan 自建指南](https://github.com/croffasia/itsaplan/blob/main/docs/self-hosting.md)
- [It’s a Plan Pro 功能边界](https://itsaplan.dev/pro)
- [It’s a Plan 外部 Runner](https://github.com/croffasia/itsaplan/tree/main/packages/runner)
