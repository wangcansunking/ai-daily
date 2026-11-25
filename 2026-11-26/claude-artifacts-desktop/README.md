---
title: Claude 给生成应用开放 3 档权限，Mac 和 Windows 都能装
date: '2026-11-26'
weekday: 星期四
slug: claude-artifacts-desktop
category: big-tech-models
description: Anthropic 让 Claude 生成的交互作品拥有独立的应用图标和窗口，桌面权限分三档。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/claude-cover.png
batch: morning
cover_style: minimalist-still-life
---

# Claude 给生成应用开放 3 档权限，Mac 和 Windows 都能装

在 Claude 里做了一个轨道可视化工具，关掉对话后，第二天还能从应用列表里把它打开。Anthropic 在 11 月 24 日公布的 Install as app 把这段路缩成一个菜单动作：Claude 生成的 Artifact 可以安装成 Mac 或 Windows 的桌面应用，自带图标与独立窗口。

## 要点速览

- Install as app 在 Mac 和 Windows 的 Claude 客户端面向各套餐逐步推出测试版。
- 安装后的应用随原 Artifact 更新，能复用 Claude 账户和已连接的服务。
- 本机能力分三档，从浏览器级功能到能启动程序、读写文件的完整访问。
- 应用打开时需要联网并登录 Claude，模型请求仍按套餐计入用量。

## 一句话做出来的工具，有了单独的启动入口

Claude 的 Artifact 是对话中生成、可以在侧栏查看或操作的作品。它可能是一张信息面板、一份交互文档，或一个能点能拖的小工具。人可以对着它继续提要求，Claude 再把改动写回去。

以前这类交互工具常跟着对话走。好用的东西想留着，就得记得当初在哪个会话里，或把原型交给开发者重新包装。Anthropic 现在把安装入口加到了分享菜单，安装后能像桌面上其他应用一样单独启动。

![Claude 官方展示的多款桌面 Artifact 应用](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/claude-desktop.png)

*图片来源：Anthropic《Turn artifacts into Mac and Windows apps》，2026-11-24。*

官方演示把地球轨道可视化工具装进独立窗口。这个例子选得很准：画面要占够空间，操作要重复做，作品本身还可以继续改。聊天记录保留了创作过程，桌面窗口则给最终工具留了一个常驻位置。

安装需要 Claude Desktop 6.28899.0 或更高版本。确认安装后，Mac 可以从 Dock 或 Spotlight 打开，Windows 可以从开始菜单启动。支持菜单栏或系统托盘的作品还可留在后台。

这项更新尤其适合每天重复、需求又太窄的小活。比如值班工程师想看固定几条曲线，现成的软件带着一长串用不上的选项。Artifact 可以先做最小版本，留下三条线，再按使用习惯改变排列和刷新节奏。

![Claude 在官方演示中生成并安装轨道可视化应用](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/claude-install.png)

*图片来源：Anthropic 官方 Install as app 演示，经 ClaudeTechHub 转载，2026-11-24。*

## 从独立窗口，到能跟电脑交互

独立窗口之外，桌面版把一部分系统能力给了作品。一个运行状态小工具可以请求通知或常驻置顶。文件整理器如果真要读写文件，就进入完整访问这一档。官方示例中的 Vitals 把性能数据放在菜单栏或托盘，Phosphor 则展示了文件管理器形态。

| 权限档位 | 能做的事 | 适合的例子 |
| --- | --- | --- |
| 默认 | 在浏览器同等边界内运行 | 文本对照板、交互图表 |
| 应用权限 | 请求通知、置顶窗口或全局快捷键等桌面能力 | 计时器、状态监视器 |
| 完整电脑访问 | 请求运行程序、读取、修改或删除本机文件 | 自己制作的文件管理或整理工具 |

完整访问只对自己制作的应用开放，Team 和 Enterprise 用户还可以对组织内其他成员制作的应用授权。权限绑定当前版本，Artifact 更新后 Claude 会再次询问。官方还提醒，应用引用的外部网站内容可能变化，网站改动未必触发新的授权弹窗。

这里最值得留意的是更新和授权分开走。创作者修了一个按钮，安装版能跟着更新。要是某个版本要继续操作文件，用户仍有一轮独立决定。对同事共享的工具而言，这比把后续版本都默认为可信更符合桌面使用习惯。

![Claude 桌面 Artifact 产品演示中的独立应用画面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/claude-window.png)

*图片来源：Anthropic 官方 Install as app 演示视频 00:10 截帧，2026-11-24。*

## 它把原型做轻了，运行依然依赖 Claude

Anthropic 说，应用可调用 Claude，也能使用账户已连接的服务，无需单独申请 API 密钥。调用 Claude 会消耗对应套餐用量，打开应用时也得在线并登录。分享的是由 Claude 托管和更新的作品，接收者同样在这套运行条件下使用。

这决定了它最先替代哪一类工作。团队想把固定格式的记录做成输入面板，可以当天做出一个能反复开的版本。若工具要放到隔离网络长时间运行，在线和登录依赖就会直接限制用法。

推出范围也有边界。功能正在所有套餐分批测试，部分组织部署形态暂不支持。Team 默认开放安装功能，完整访问默认关闭。Enterprise 需要所有者开启。Claude Docs、Slides、Sheets 等部分作品类型也无法安装。

更有意思的是分发方式。过去，一个人把内部原型用熟后，想让十个人也用，往往得有人打包、发新版本、接认证。这次最直接的收益发生在第一次分享：同事装完就有同一个入口，改动可以沿原作品更新。团队是否愿意把正式流程放进去，则取决于可用性、权限和持续使用成本。

对大多数人来说，起点可以很小：先把每周都会重新打开的一张对照表做成面板，等大家确认用得上，再决定是否要接连接器。把工具留在默认权限下也能复用，真正需要频繁读写文件的场景才值得承担更大的权限。

Install as app 给生成式应用带来的变化，是让一次对话里做出的东西有了下一次使用时找得到的入口。

## 🔗 引用链接

- [Anthropic：Turn artifacts into Mac and Windows apps](https://claude.com/resources/articles/install-as-app)
- [Anthropic：Claude 发布说明](https://support.claude.com/en/articles/12138966-release-notes)
- [Anthropic：Artifacts 使用说明](https://support.claude.com/en/articles/17153992-what-are-artifacts-and-how-do-i-use-them)
- [Anthropic：安装与运行帮助](https://support.claude.com/en/articles/18516354-install-an-artifact-as-an-app)
- [Anthropic：Install as app 演示视频](https://www.youtube.com/watch?v=vBDP--QD_Og)
- [ClaudeTechHub：产品演示转载](https://cthcommunity.com/en/news/claude-artifacts-install-as-app/)
