---
title: Obsidian 接上 AI，3 个状态清空剪藏收件箱
date: '2026-11-26'
weekday: 星期四
slug: obsidian-bases
category: ai-tools-practice
description: 用 Web Clipper 提取网页信息，再用 Bases 把 Markdown 笔记变成可拖拽的阅读队列。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/obsidian-bases.png
batch: noon
cover_style: minimalist-still-life
---

# Obsidian 接上 AI，3 个状态清空剪藏收件箱

早上看到一篇框架更新，手快先存了，过两天翻到它时已经想不起为什么要读。Obsidian 的网页剪藏可以让 AI 把原文压成三句话，再把条目放进 Bases 队列。摘录、出处与阅读状态都挂在同一张 Markdown 笔记上，打开待阅视图就能继续处理。

## 要点速览

- Web Clipper 的 Interpreter 用模型处理网页，支持摘要、翻译与字段提取。
- 标题、网址、剪藏日期用模板变量直接保存，摘要再交给模型。
- Bases 用同一组笔记做表格与看板。按普通笔记属性分组时，拖卡片就能改状态。
- 看板要求 Obsidian 1.14 或更新版本，表格也能独立跑完整程。

## 第一步，把原网址和 AI 摘要写进同一页

在浏览器安装 Obsidian Web Clipper，打开扩展设置，进入 Interpreter 并启用它。选择已配置的模型供应商。常用云端路线要准备对应密钥，也可以选 Ollama 等本地模型；第一次只用一篇公开网页起步。

剪藏模板的目标目录设为 `Reading/Clips`。属性可以先放三项，`source` 用 `{{url}}`，`captured` 用 `{{date}}`，`status` 填固定值 `待阅`。笔记名可用 `{{title}}`，避免让模型重新起名后找不到原文。

![Interpreter 处理后保存到 Obsidian 的笔记](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/obs-interpreter.png)

*图片来源：Obsidian 官方 Web Clipper 帮助仓库 Interpreter 演示截帧，2026-11-26 查阅。*

模板正文留三块，先写摘要，再留摘录，最后留一行给自己的想法。摘要可以用这条提示词，摘录则从原文手选最能说明问题的段落，长度试着控制在一个屏幕内。

> {{"用中文写三条短句：作者解决的问题、使用的方法、文章给出的一个具体结果。只用页面信息，缺失的写未说明。"}}

打开文章后点击扩展，再选择 Interpret 和 Add to Obsidian。保存前扫一眼摘要，特别是数字、产品版本和适用范围。比如原文只说 beta 渠道支持，笔记里就保留渠道，免得几周后把限制读丢。

日期属性记的是剪藏日。文章发布日期如果也重要，给它单独开一个 `published`。这两个时间分开后，你可以补收一篇旧文，同时让它排在今天的待阅队列前面。

![Obsidian 官方文档中的日期属性界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/obs-date-field.png)

*图片来源：Obsidian 官方《Daily notes》文档日期属性示例，2026-11-26 查阅。*

## 第二步，给同一批笔记开两个处理入口

在 Obsidian 设置里启用核心插件 Bases。用命令面板创建一个 Base，把共用过滤条件设成文件夹为 `Reading/Clips`，再建一个表格视图，展示名称、`source`、`captured` 和 `status`。按剪藏日从新到旧排，最新保存的素材马上就能找到。

![Bases 官方表格视图示例，以书单演示列属性](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/obs-bases-table.png)

*图片来源：Obsidian 官方《Table view》文档，2026-11-26 查阅。*

再加一个 Kanban 视图，用 `status` 属性分组。加上待阅、精读和归档三列，拖动 Markdown 卡片时，Obsidian 会更新原笔记的这个属性。看板显示文件名和剪藏日期即可，需要网址时再打开卡片或回表格找。

| 状态 | 留在笔记里的东西 | 下一步 |
| --- | --- | --- |
| 待阅 | 网址和简短导读 | 读原文，决定是否投入时间 |
| 精读 | 摘录和一行自己的问题 | 跑样例，或与已有资料关联 |
| 归档 | 结论与关键出处 | 留作搜索，退出日常队列 |

如果还在使用早于 1.14 的版本，在表格里编辑同一个状态属性就能做完流程。也可以复制一个「待阅」表格视图，只让待阅条目出现。过滤条件放在单个视图里，其他视图仍能访问归档内容。

## 第三步，拿手上三条链接跑通一圈

先找一篇版本公告、一篇文档和一篇个人教程，各剪藏一条。公告适合抽出版本与启用条件，文档适合留住最短复现路径，教程适合附上一句「这招能帮我做哪件事」。有些条目连这句都写不出来，当天就可以归档。

滚动处理时可以用一个小规则。能马上应用的条目拖到精读，并留一句具体动作，例如「把新缓存接口接进演示服务」。暂时用不着的直接归档。这样清空的是排队等你决定的事项，资料仍留在库里。

Interpreter 的网页上下文默认可能包含整页 HTML。遇到页面太长或夹了很多导航，可以在模板里针对页面主内容设置上下文，例如网站确实使用 `#main` 时可试 `{{selectorHtml:#main}}`。换站点后结构可能变化，先拿一页普通文章试剪。

如果同一篇文章在社交平台和原站各出现一次，先在表格里按 `source` 找原站链接，把自己的观察补回同一条笔记。转载中确有引用价值的批注可以另存并链接原笔记，下次读到它时就能顺着出处往回走。

一周之后再决定要不要加字段。比如你反复筛选版本公告，可以补一个 `kind` 区分公告、文档与教程。如果每次都在找某条原始证据，就把正文摘录放到更醒目的位置。几次真实使用比一开始设计十个字段更容易找出最省力的布局。

云端模型会收到所选上下文。团队内网、客户材料或带账号细节的页面，先按组织规则决定是否启用模型；也可以仅剪藏标题和网址。加模型的价值是让你更快决定该读哪条，每次剪藏都可以独立选择处理方式。

一套能长期用的阅读收件箱，从一条找得到出处的笔记和一个明确的下一步开始。

## 来源

- [Obsidian｜Web Clipper Interpreter](https://obsidian.md/help/web-clipper/interpreter)
- [Obsidian｜Web Clipper 模板变量](https://obsidian.md/help/web-clipper/variables)
- [Obsidian｜创建 Bases](https://obsidian.md/help/bases/create-base)
- [Obsidian｜Table view](https://obsidian.md/help/bases/views/table)
- [Obsidian｜Kanban view](https://obsidian.md/help/bases/views/kanban)
- [Obsidian｜Daily notes](https://obsidian.md/help/plugins/daily-notes)
