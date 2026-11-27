---
title: EdgeEver 日涨31星，三栏笔记给 AI 开一格书架
date: '2026-11-27'
weekday: 星期五
slug: edgeever
category: github-trending
description: 把 GitHub 剪藏、三栏编辑和按笔记本授权的 MCP 连成一条线。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/edgeever.png
batch: noon
cover_style: magazine-collage
---

# EdgeEver 日涨31星，三栏笔记给 AI 开一格书架

一页技术资料从浏览器进收藏夹，再进群聊，最后常常输给搜索框。EdgeEver 把网页剪藏、三栏笔记和 AI 接口放进同一套可自托管工作区。11 月 27 日午班抓取的 GitHub Trending TypeScript 日榜上，它排第 8，累计 3,614 星，页面显示今日新增 31 星。

## 要点速览

- 编辑区保留左侧目录、中间笔记列表、右侧正文，可切换富文本和 Markdown。
- Web Clipper 能把网页整理进自己的笔记库，部分网站还会按页面类型提取结构。
- 可用 Cloudflare 托管，也可在 NAS 或 VPS 走 Docker。原库可以先用少量样本试迁移。
- 内置的远程 MCP 可以按笔记本给权限，桌面 ACP 则把支持的本地 Agent 带进笔记界面。

## 三栏工作区，把资料和结论摊在一屏

设想你要给团队写一篇「三个 CSV 导入库怎么选」。目录栏放「开发调研」，中栏把三个仓库和一份结论并排，编辑栏随时切进源码链接、表格或 Markdown。边查边写时，目录、进度和正文都在同一屏，读完一条不用先决定它属于哪个永久分类。

![EdgeEver 的目录、笔记列表和编辑区](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/edgeever-panes.png)

*图片来源：EdgeEver 官方最佳实践文档，三栏编辑器界面，2026-11-27 查阅。*

它的吸引力是给习惯传统笔记布局的人保留原来的手感，同时把剪藏、同步和 AI 接口整在一起。对那些同时开着浏览器收藏夹、桌面笔记和编码 Agent 的人，少一次复制粘贴，就少一次弄丢出处的机会。

项目提供 Web、桌面和移动客户端。若你主要在两台电脑之间来回切换，可以先从网页版体验工作区，再决定要不要装桌面客户端。官方演示会在每天北京时间凌晨 3 点恢复样例，适合放公开文档跑一轮，别把它当作正式库。

## 把仓库剪成能重新打开的卡片

GitHub 示例里，官方剪藏器把仓库信息收进一条笔记。调研时可以让原始链接留在笔记里，再加两行自己的判断，例如「能按流处理」和「文档里的浏览器示例需再试」。等一个月后回头，来源和当时的选择依据还在同一处。

![GitHub 仓库剪藏后的卡片界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/edgeever-clip.png)

*图片来源：EdgeEver 官方最佳实践文档，GitHub 剪藏效果，2026-11-27 查阅。*

| 资料 | 收进来之后 | 首批样本 |
| --- | --- | --- |
| GitHub 仓库 | 留出处，再记接口或兼容性 | 找到的 3 个候选库 |
| 长文与文档 | 摘关键段落，留页面标题和链接 | 2 篇教程 |
| 附件 | 保留跟正文的引用关系 | 1 张图和 1 份 PDF |

官方支持从 Evernote、Obsidian、Notion 等来源导入，也有 Markdown 文件夹入口。迁移前可以先抽出带附件、层级和互链的几条笔记。尤其是旧库用了 Wiki 链接、自定义属性或插件块时，先打开导入结果，看自己的常用结构读起来是否顺手。

## 给 AI 一格书架

Remote MCP 是给外部 Agent 连接笔记服务的接口。项目文档的入口是个人资料里的 API / MCP，可以创建只读、可写或仅对应指定笔记本的令牌。一个有价值的第一轮任务是让 Agent 把三篇公开文档的链接和共同结论整理成一份摘要，先在对话里交稿。

桌面端还接 ACP，可以把支持的本地 Agent 放进工作区侧边栏。两种入口面对不同的工作方向。已经在编码工具里工作的，可以让它读选定的笔记本。如果主要在笔记里写提纲或追问资料，桌面侧边栏少来回切几次应用。

![EdgeEver 桌面端 ACP 侧边栏会话](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/edgeever-acp.png)

*图片来源：EdgeEver 官方最佳实践文档，ACP 侧边栏会话，2026-11-27 查阅。*

用一枚单独的可写令牌，白名单只选专用的「AI 草稿」笔记本。查资料用另一枚只读令牌。自定义令牌的访问级别作用于整份白名单，选中父笔记本也会包含下级。提示词也可以具体一点，比如「从这三条笔记提取库名、支持的运行时和出处。资料没说的一律写未找到，先交一页 Markdown」。拿到初稿就能继续编辑，原始剪藏仍能作为复查入口。

积累一周以后，再把剪藏按用途分成「待读」和「已形成判断」。前者留原链接和一行为什么收藏，后者补上决定和日期。Agent 下次整理时可以同时读到原材料与人的结论，也更容易指出哪些判断随着库的版本更新已经过期。

## 云端和家里的 NAS，都可以先从小库起步

- **先打开公开演示。** 建一个「本周调研」，试三栏切换、标签搜索和 Markdown 编辑。用公开素材走一次从摘录到整理的流程。
- **需要浏览器到处可用，就看 Cloudflare。** 官方路线使用 Workers、D1 和 R2。先为 Worker 设置管理员密码 Secret，再连接构建。项目说可在免费额度内运行，R2 仍要开通订阅并绑定付款方式。
- **已有 NAS 或 VPS，就看 Docker。** 参照官方部署文档安排持久化目录和访问地址。容器路线用 SQLite，附件可随本地文件保存，也支持兼容 S3 的存储。把备份位置和部署位置分开，会更方便恢复。
- **导出一次再搬大库。** 利用原生 ZIP 导出，确认附件和 Markdown 都在。文档说明归档同时保存内部结构元数据，手动解包则可直接读到纯文本资料。

接模型时还要留意数据流向。服务自托管决定的是笔记放在哪里。用自己的模型密钥调用外部接口时，传给模型的内容由对应配置和实际调用决定。先拿公开笔记练流程，回到工作资料时再按笔记本和用途分配权限。

EdgeEver 的第一份收益可以很朴素，让一周的公开调研从剪藏、可追溯整理到 AI 摘要，全程待在一个可导出的工作区里。

## 来源

- [GitHub Trending TypeScript 日榜](https://github.com/trending/typescript)
- [EdgeEver 官方仓库及部署说明](https://github.com/tianma-if/edgeever)
- [EdgeEver 最佳实践与界面截图](https://github.com/tianma-if/edgeever/blob/main/docs/best-practices.md)
- [EdgeEver MCP 配置](https://github.com/tianma-if/edgeever/blob/main/docs/mcp-guide.md)
- [EdgeEver Docker 部署](https://github.com/tianma-if/edgeever/blob/main/docs/deploy-docker.md)
