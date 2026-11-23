---
title: "GitHub Copilot 让组织团队共享默认插件"
date: 2026-11-19
weekday: 星期四
category: 开源
slug: github-copilot-team-plugin-defaults-2026-11-19
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/github-copilot-team-plugin-defaults-2026-11-19.png
description: "GitHub 宣布 Copilot Business 和 Enterprise 的组织团队可把获准的 Agent Plugins 设为默认，适用于使用 VS Code、GitHub Copilot App 或 Copilot CLI 的直属成员。企业和组织既有策略仍然优先。"
tags: ["GitHub Copilot","Agent Plugins","团队自定义","开发工具"]
track: overseas-hot
batch: evening
---

# GitHub Copilot 让组织团队共享默认插件
![桌面上的笔记本电脑旁叠放着带接口的金属模块](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/github-copilot-team-plugin-defaults-2026-11-19.png)
*图片来源：Sunky AI 日报《GitHub Copilot 让组织团队共享默认插件》主题静物图，2026-11-19。*

**使用 Copilot Business 或 Enterprise 的组织团队可以为直属成员共享一份默认插件配置。这份预设由企业和组织策略约束，策略允许时，成员可以关闭默认插件。** [1]

北京时间 11 月 19 日 00:00:13，GitHub Changelog 发布公告，宣布组织团队自定义功能正式可用。使用 Copilot Business 或 Enterprise 的组织团队可以把获准的 Agent Plugins 设为团队默认项。[1]

![GitHub Changelog 公告页，标题为 Customize GitHub Copilot for your team，页面中可见 Customize Copilot 设置界面和发布说明](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-github-copilot-changelog-page.png)
*图片来源：GitHub Changelog《Customize GitHub Copilot for your team》页面截图，2026-11-18。*

如果团队原先要求成员照着入职文档逐个启用常用插件，维护者现在可以把其中获准的插件设为团队默认项。[1]

## 维护者为直属成员选择默认插件

![GitHub Copilot 的 Customize Copilot 团队设置界面，可见添加市场、浏览插件和已为团队启用的插件](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-github-copilot-team-plugins.png)
*图片来源：GitHub Changelog《Customize GitHub Copilot for your team》界面截图，2026-11-19（北京时间）。*

团队维护者或组织所有者可以在 Customize Copilot 页面设置团队默认插件。截图里的添加市场、浏览插件和已为团队启用的插件，对应管理者眼前的不同状态。市场决定可浏览的范围，选中的获准插件才成为团队默认项。[1]

| 配置环节 | 对应的角色或范围 | 成员侧的含义 |
| --- | --- | --- |
| 设置团队默认项 | 团队维护者或组织所有者 | 团队有一套共同的插件起始配置 |
| 提供插件市场 | 内置市场、企业市场、团队添加的市场 | 添加市场后，团队能够浏览对应内容 |
| 选为默认插件 | 从市场中选取获准的 Agent Plugins | 经选择的插件才进入默认配置 |
| 确定覆盖成员 | 设置所在团队的直属成员 | 仅属于子团队的成员不会继承父团队默认项 |
| 客户端应用配置 | VS Code、GitHub Copilot App、Copilot CLI | 公告列出的这些入口均适用团队默认项 |
| 成员调整默认项 | 受企业和组织策略约束 | 策略允许时，成员可以关闭默认插件 |

添加市场后，团队可以浏览其中的插件。维护者可按团队的日常需要，从获准的插件中选出默认项。[1]

### 团队默认项只覆盖直属成员

直属成员是这项功能的配置对象。[1] 假设一个平台工程团队下面划分了多个子团队，平台工程团队里登记为直属成员的人适用该团队的预设。只属于子团队的人不会经由层级关系收到这份配置。因此，当技术负责人按团队逐步推广时，实际覆盖面要看成员直接属于哪个团队。团队树反映了组织关系，直属成员范围才回答这次预设具体发给谁。

公告列出的入口包括 VS Code、GitHub Copilot App 和 Copilot CLI。[1] 团队成员有人从编辑器打开 Copilot，也有人习惯在命令行工作。一项团队预设覆盖这些已有入口，让内部工具说明可以围绕共同的默认项来写。对负责开发者工具的人来说，客户端的实际使用方式和团队的直属成员关系，都是判断推广范围的依据。

### 客户端继续执行企业和组织策略

团队设定的是默认启用状态，客户端仍按企业和组织策略判断哪些插件可以使用、哪些必须启用、哪些必须禁用。[1] 具体到开发者看到的结果，有以下几种情形。

- **插件具备准入资格，且策略允许成员关闭。** 团队可以将它设为默认，成员也可以根据自己的工作需要关闭。
- **策略要求启用插件。** 客户端继续执行这项要求，团队预设中的可选性质不会改变该要求。
- **策略禁用插件或限制来源市场。** 客户端继续执行禁用或准入政策，团队设置须在获准范围内选择默认项。

这组规则给维护者和管理员划定了各自的职责。维护者了解团队日常使用哪些工具，可以据此减少重复配置。企业和组织管理员负责插件准入、强制或禁用要求。成员在策略允许的范围内保留选择。排查个别成员看到的插件状态时，团队页上的默认清单只回答团队选了什么，最终状态还要结合该成员所受的策略理解。[1]

![GitHub Docs 的 Plugins for GitHub Copilot 文档页，列出插件可使用的客户端和插件内容](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-github-docs-copilot-plugins.png)
*图片来源：GitHub Docs《Plugins for GitHub Copilot》页面截图，查阅于 2026-11-19。*

GitHub Docs 还列出了企业受管配置和仓库默认项。[2] 在工程团队的实际协作中，“默认”很容易被用来指代不同层面的设置。企业受管配置对应企业的管理要求，仓库默认项对应仓库，公告新增的组织团队默认项对应直属成员的可选预设。三者各有对应的管理对象。比如一个仓库由多个团队协作，仓库的默认项和其中某个团队给直属成员的预设，回答的是两个不同的配置问题。[1][2]

团队默认项是否合适，也取决于成员使用插件的频率。设想一个团队在日常开发中经常需要同一款获准插件，而另一款插件只用于少数人的专项任务。前者适合成为团队的共同起点，后者可以留在市场里供有需要的人查找。市场和默认项各有用途，团队可以把广泛的浏览需求和经常发生的配置需求分别处理。[1]

### 分批启用从现行插件政策开始

对准备在多个团队推广的技术负责人，已有的企业和组织插件政策决定了每批预设可以选什么。一个工程团队打算默认启用某项插件时，先确认它的来源市场和插件本身符合现行政策，才能确定这项选择是否具备实施条件。若某插件已经受强制要求约束，维护者说明配置时也有必要说清它为何会启用，避免把企业要求当成团队成员可以自行调整的偏好。[1]

分批启用可以先以直属成员范围清楚的团队为单位。比如平台团队希望新成员在已经使用的 Copilot 入口获得常用配置，推广范围可以直接对应到设置所在团队的直属成员。子团队也有同样需求时，其成员范围和默认配置仍需分别考虑。这样，技术负责人描述覆盖情况时可以指向具体成员关系，而无需把组织图上的整个分支都算入同一批。[1]

如果原先由成员各自照文档启用同一批插件，团队维护者现在可以统一维护这份清单。排查个别成员的配置时，可以结合直属成员关系和适用的企业及组织策略，判断团队默认项是否适用。[1]

对已有 Copilot Business 或 Enterprise 的组织，团队维护者可以用默认项统一设置直属成员经常使用的获准插件。

## 🔗 引用链接

- [1] GitHub Changelog《Customize GitHub Copilot for your team》 · https://github.blog/changelog/2026-11-18-customize-github-copilot-for-your-team/
- [2] GitHub Docs 的 Agent Plugins 文档 · https://docs.github.com/en/copilot/concepts/customizations/plugins
