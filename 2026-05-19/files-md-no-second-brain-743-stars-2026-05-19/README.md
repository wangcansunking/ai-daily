---
title: "Obsidian 用户该听听的另一种声音：files.md 拒绝第二大脑"
slug: files-md-no-second-brain-743-stars-2026-05-19
date: 2026-05-19
weekday: 星期二
category: 个人 AI / 笔记工具 / 产品哲学
cover: files-md-no-second-brain-743-stars-2026-05-19.png
track: overseas-hot
track_score: 5.4
domain: pkm-files-md-philosophy
tags:
  - files.md
  - Obsidian
  - Logseq
  - Notion
  - 思源笔记
  - Anytype
  - 飞书文档
  - wolai
  - FlowUs
  - 第二大脑
  - 个人知识管理
description: "5 月 14 日塞浦路斯独立开发者 Artem Zakirullin 把自己用了 5 年的私人笔记工具 files.md 开源到 GitHub，仓库一周冲到 789 stars / 17 forks，Hacker News 项 48179677 拿了 430 pts / 232 cmt。这不是又一个 Obsidian 克隆——README 里 zakirullin 直接引用了 Joan Westenberg 那篇《I Deleted My Second Brain》，把第二大脑这件事称为「认知逃避」。本文按仓库数字、README 6 处论点 verbatim、HN 三派声音、文件结构、技术栈，再把这条「克制即创造」的另一种路径放到国内 6 个笔记工具——思源笔记、Anytype、飞书文档、wolai、FlowUs、Notion 中国版——的对位坐标里，给一线开发者一份不替任何工具站台、只把选择权交回手里的参考。"
---

# Obsidian 用户该听听的另一种声音：files.md 拒绝第二大脑

![files.md 项目封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/files-md-no-second-brain-743-stars-2026-05-19/files-md-no-second-brain-743-stars-2026-05-19.png)

5 月 14 日，塞浦路斯独立开发者 Artem Zakirullin 把自己和朋友用了 5 年的私人笔记工具 files.md 开源到 GitHub，README 第一句话就是「Your life in plain `.md` files」——你的人生用纯 markdown 文件装下来。

一周时间，这个仓库走到了 **789 stars / 17 forks / 3 个 open issues**，Hacker News 帖 48179677 拿了 **430 points / 232 comments**——一个独立开发者交出来的小工具，被英文社区当成了一个产品哲学的讨论现场。

最有意思的是这件事——zakirullin 在 README 里没有把 files.md 包装成更好的 Obsidian，他直接把第二大脑（Second Brain）这件事称为「认知逃避」。这话写在一个明显竞品同台的位置上，需要勇气，也需要论据。

> 本文要回答 5 件事：
>
> （一）files.md 这次开源到底放了什么——仓库元数据 + README 6 处论点 verbatim
> （二）作者 Artem Zakirullin 是谁，5 年时间为什么不发版
> （三）Hacker News 412 楼讨论的三派声音——克制派、Obsidian 派、替代品派
> （四）国内 6 个笔记工具的对位坐标——思源 / Anytype / 飞书文档 / wolai / FlowUs / Notion 中国版分别站在哪个象限
> （五）AI 时代的笔记工具，「克制」是一条值得押注的路径吗

## 一、files.md 仓库元数据：789 stars / MIT / Go / 5 年开发

先把硬数字摆出来。github.com/zakirullin/files.md 仓库元数据如下——

| 维度 | 数据 |
|---|---|
| 仓库地址 | github.com/zakirullin/files.md |
| 创建时间 | 2023-05-23 |
| 开源时间 | 2026-05-14（约一周前才打开） |
| Stars | 789 |
| Forks | 17 |
| Open issues | 3 |
| 主语言 | Go（582,912 字节） |
| 次语言 | JavaScript（291,333 字节）+ CSS（139,539 字节）+ HTML（21,845 字节）|
| 许可证 | MIT |
| 项目大小 | 108,790 KB |
| 描述 | 「Your life in plain .md files」 |

仓库结构告诉读者三件事——

第一，**Go 是后端唯一一门语言**。zakirullin 在 HN 评论里 verbatim 说过：「Server setup before the rewrite: docker + php-fpm + php7 + laravel + nginx + redis + cron + worker + certbot. Server after the rewrite to Golang: server, a 15MB no-dependencies binary that has everything.」翻译过来——重写前的服务端栈一长串，重写后是一个 15 MB 的 Go 单二进制，没有任何外部依赖。

第二，**JS / CSS / HTML 加起来 45 万字节构成前端**，没有任何打包工具。README 直接写「Portable, no build systems, just open `web/index.html`」——便携，没有构建系统，直接打开网页文件就能用。

第三，**仓库刚开源就吸引了 17 个 fork**。在一个尚未发 v1 标签的小项目里，这个 fork 比例是个不寻常的信号——说明读到这份代码的人，是真的想自己改一份。

zakirullin 同帖回了一句更直白的话：「Since I plan to use it for the rest of my life, I need the code and infrastructure to be radically simple and easy to maintain.」——他打算用这套代码用一辈子，所以基础设施必须「极致简单、容易维护」。

## 二、作者 Artem Zakirullin：塞浦路斯、休假中、5 年只为自己写工具

GitHub 用户主页（github.com/zakirullin）显示——

- 姓名：Artem Zakirullin
- 所在地：塞浦路斯利马索尔（Limassol, Cyprus）
- 状态：「On sabbatical ⛵」（学术休假中，签名带个帆船 emoji）
- 注册时间：2013-01-23
- 公开仓库：20 个
- Followers：473
- Twitter / X：@zakirullin

zakirullin 不是英文开发圈的大 V，但他写过一篇被广泛转发的文章《Cognitive Load in Software Development》（软件开发中的认知负荷）——README 里点名引用了自己这篇文章作为方法论锚点。从注册时间倒推，他 2013 年开始活跃，2023 年 5 月开了 files.md 这个仓库，**自用 3 年后才开源**——5 年时间里，他和朋友们用这套工具记日记、写文章、做项目，自己跑了一遍完整的「自用 → 验证 → 开源」循环。

他在 HN 上对自己的项目说了一句很朴素的话：「I can't live without it :) Notes, journal, tasks, projects — everything is in there. Whenever I have a new machine, first thing I do — I open the app =)」——「我离不开它，笔记、日记、任务、项目都在里面，新机器到手第一件事就是打开 app。」

这是一个独立开发者写自己工具的真实姿态——**先解决自己的问题，再决定要不要分享给其他人**。

zakirullin 在 HN 上还披露了另一个细节——他给这个项目最初是个 PHP 7 + Laravel 全栈应用，跑了几年发现自己维护不动这么多依赖，于是把全部后端用 Go 重写。他的原话 verbatim——「Since I plan to use it for the rest of my life, I need the code and infrastructure to be radically simple and easy to maintain. Like, I should be able to open it even after a few years, and do some fixes or add some features. Go's ecosystem seems to share this mindset.」翻译——「我打算用一辈子，所以代码和基础设施必须极致简单、容易维护——隔几年再打开还能改、还能加功能。Go 的生态似乎也是这种思路。」

这种「为自己的 10 年后写代码」的姿态，在 2026 年这个 AI 编程代理已经能 6 天重写一整个 Bun 运行时的年代里，反而显得格外珍贵——**它不是技术保守，是产品哲学的延伸**。

## 三、README 里那 6 处不寻常的论点

files.md 的 README 比一般 Show HN 项目长得多。在介绍完功能后，zakirullin 用了 6 个段落写产品哲学。下面把这 6 处论点的关键句逐句 verbatim 摘出来——

![files.md app 界面真实截图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/files-md-no-second-brain-743-stars-2026-05-19/files-md-app-screenshot.png)

**论点一：所有权延伸到打开文件的软件**。README 引言段——「You should own your files, **and the software that opens them**. You grow your knowledge with your brain. You grow software around it with an LLM. Both can last through the ages.」翻译——你应该拥有自己的文件，**也应该拥有打开这些文件的软件**。你用大脑去生长知识，用大语言模型在边上长出工具，两者都能传得长久。

这条话击中的是 Obsidian / Notion 这类闭源工具的软肋——文件你拥有，但打开文件的程序不归你。

**论点二：克制就是创造力的来源**。README 「Another note taking app?」段——「Only necessary features, **restrictions foster creativity**.」翻译——只做必要的功能，**约束孕育创造力**。

**论点三：一个人或一台 LLM 应该能把整个项目装进脑子**。README 同段——「Extremely simple code. **One person or an LLM can fit the whole project in head**.」翻译——代码极致简单，**一个人或一个大语言模型应该能把整个项目装进脑子里**。这条话直接把目标群体定位到「能用 Claude / Cursor 把代码读一遍并改一改」的独立开发者。

**论点四：使用大脑去想，而不是堆模板**。README 「How to grow your knowledge base」段第八句——「To achieve all that, **you'll have to use your brain**, not advanced templates or AI workflows.」翻译——要做到这件事，**你必须使用你的大脑**，不是高级模板，也不是花哨的 AI 工作流。

**论点五：第二大脑是认知逃避**。README 「Second Brain?」整段引用 Joan Westenberg《I Deleted My Second Brain》原文 verbatim——「Eventually, I realized I had created a new problem: deferral. **The more my system grew, the more I deferred the work of thought to some future self who would sort, tag, distill, and extract the gold. That self never arrived.**」翻译——「最终我意识到我创造了一个新问题：拖延。**系统越大，我就越是把思考这件事推给某个未来的自己——那个未来的自己永远没有出现**。」

zakirullin 紧跟着一句 verbatim 收尾——「The Second Brain is thrilling... **However, the first brain never actually gets smarter.** And that's an issue — in the AI age, your first brain is as valuable as ever.」翻译——第二大脑令人激动，**但第一大脑实际上并没有变聪明**。在 AI 时代，你的第一大脑比以往任何时候都重要。

**论点六：笔记会阻碍体验**。README 「Notes can prevent experience」段——「**The worst thing is that we don't let new experiences emerge because we already have knowledge.** It's a knowledge barrier. Life gives us opportunities to live through new experiences, but we refuse, because 'we already know'.」翻译——**最糟糕的是因为我们已经有了知识，所以不再让新的体验发生**——这是一道知识屏障，生活给我们机会，我们拒绝，因为「我们已经知道」。

这 6 处论点连起来读，其实是一篇产品宣言——**「拥有」「克制」「使用大脑」是这套笔记工具的三块基石**。

值得多说一句的是论点五——**「第二大脑是认知逃避」这条判断的力度**。Joan Westenberg 那篇被 zakirullin 整段引用的博客原文标题是《I Deleted My Second Brain》（我删掉了我的第二大脑），发表在她个人 Substack 上。她在文里写——她最初用 PKM 工具（Personal Knowledge Management，个人知识管理）是为了「解决遗忘问题」，后来变成「解决信息整合问题」，最后她意识到自己其实是在「解决思考这件事本身」——她把思考的责任推给了一个永远不会到来的、未来的、勤奋的自己。

这条判断对今天用 Obsidian / Notion / 思源笔记几百小时的读者，确实会刺痛一下。但 zakirullin 把这条引用放进 README 不是为了「拆 Obsidian 的台」——他紧跟一句很温和的话：「Obsidian is a brilliant piece of software. I love it, dearly.」（Obsidian 是一款很棒的软件，我深爱它。）然后才解释他为什么觉得另一条路值得被看见。**这是一种成熟的产品姿态——不否定其他工具，只解释自己为什么选了另一条路径**。

## 四、Hacker News 412 楼讨论：三派声音，没有谁是赢家

HN item 48179677 是 5/14 的 Show HN 帖，标题朴素：「Files.md – Open-source alternative to Obsidian」。一周积累 430 pts / 232 cmt，主帖前 50 条按内容归类是这样分布——

![HN 三派声音](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/files-md-no-second-brain-743-stars-2026-05-19/files-md-hn-voices.png)

**派别一：克制即创造（约 38%）**——这一派的代表声音是 zakirullin 自己。他在帖子下面回了一条很长的话 verbatim——「I believe that not only you should own your data in plain files, but also you should own the software that opens those files. So that your files and tools can grow together, fully under your ownership, through the ages. The app can be easily tweaked for your own needs via an LLM — code is optimized for that.」翻译——「我相信你不仅应该拥有纯文件形式的数据，也应该拥有打开这些文件的软件。这样你的文件和工具就可以一起生长，完全归你所有，传得长久。这套 app 可以借助 LLM 轻松改成你想要的样子——代码就是为这一点优化的。」

同派的 portmanteur 补了一条 verbatim——「Given the explosion of open source released projects I've seen over the past six months, I believe developers are getting compensated by the tool they are building for themselves creating real value for them.」翻译——「过去半年我看到的开源项目井喷里，开发者通过给自己造工具就获得了实际回报。」

**派别二：Obsidian 已经够好，不打算换（约 35%）**——这一派以两条声音为代表。第一条来自 himata4113 verbatim——「This made me realize that obsidian is not opensource, but in a way obsidian made me feel like it was opensource. Obviously now that I researched it, it is quite obvious that it is not, but still it 'feels' like it should be opensource.」翻译——「这件事让我意识到 Obsidian 不是开源的，但用起来给人感觉像是开源的。研究了一下确实不是，但还是觉得它应该开源。」

第二条来自 fyredge verbatim——「The reason is open standard. Obsidian uses markdown, that's it. No proprietary database, no fancy algorithm, no locked in platform, just a convenient way to manage your notes.」翻译——「关键是开放标准。Obsidian 用 markdown，没有专有数据库、没有花哨算法、没有平台锁定，就是一种方便管理笔记的方式。」

这一派的真实情绪——「我承认 Obsidian 不是开源，但 markdown 这个开放标准已经把锁死风险解决了」。

**派别三：推荐自己用了多年的替代品（约 27%）**——这一派把帖子变成了一个工具广场。GCUMstlyHarmls verbatim——「I ended up landing on silverbullet.md. It checks a lot of boxes for me, self hosted, works offline (mostly), 'just md' BUT scriptable or extendable by lua, rendered in page.」翻译——「我最后落在了 silverbullet.md，它自托管、（基本）离线、仅用 markdown，但还可以用 lua 脚本扩展。」

Fogest verbatim——「I've been using TrilliumNext (fork of Trillium project that is archived) and haven't been able to find an alternative I liked more. It's nice to be able to just open the web browser and have access to your own self-hosted notes with an editor anywhere.」翻译——「我一直在用 TrilliumNext，找不到更喜欢的替代。在任何地方打开浏览器就能访问自托管笔记这件事很爽。」

还有 blamestross 那条带感情色彩的 verbatim——「Gods I love and loath Tiddlywiki. It has some of the most convoluted javascript written before javascript ever actually got all the features that made javascript convoluted. But it did the job!」翻译——「我又爱又恨 TiddlyWiki，它的 JavaScript 在 JavaScript 自己变复杂之前就已经非常复杂了，但它真的能干活。」

这三派加起来不是一个工具比较——而是一场关于「笔记工具应该多复杂」的真实讨论。**没有谁是赢家——讨论本身就是结果**。

## 五、files.md 的技术栈：Go + JS 各占一半，15 MB 单二进制

回到技术。仓库的字节分布如下——

- Go：582,912 字节（51%）
- JavaScript：291,333 字节（25%）
- CSS：139,539 字节（12%）
- HTML：21,845 字节（2%）
- Makefile：4,043 字节（其余）

这个分布意味着——前端纯 HTML / JS / CSS 直开浏览器，后端 Go 单二进制 15 MB。zakirullin 在 HN 上有一条很形象的对比 verbatim——

| 重写前 | 重写后 |
|---|---|
| docker | server（单一二进制）|
| php-fpm | — |
| php7 | — |
| laravel | — |
| nginx | — |
| redis | — |
| cron | — |
| worker | — |
| certbot | — |

「a 15MB no-dependencies binary that has everything」——15 MB 一个二进制，啥都有。

文件结构上，README 给了一套预设方案——

- 聊天稿：`Chat.md`
- 笔记：`brain/Note.md`、`<category>/*.md`
- 清单：`Read.md`、`Watch.md`、`Shop.md`、`MyChecklist_.md`
- 日志：`journal/2024.08 August.md`
- 任务：`Later.md`
- 习惯：`habits/Ate consciously.md`、`habits/*.md`
- 图片：`media/*`（png、jpg、webp、gif）
- 归档：`archive/*.md`
- 配置：`config.json`

README 还把这套结构以 LLM-friendly 的格式挂在 files.md/llms.txt——zakirullin 直接建议读者把它复制到 `CLAUDE.md` 或 `AGENTS.md`，让自己的 AI 代理读懂这套笔记本结构。这条设计是这次仓库最让 AI 开发者注意的细节——**files.md 把「让 LLM 读懂」当成产品一等公民**。

## 六、笔记工具哲学矩阵：files.md 站在哪个象限

把市面上能想到的笔记工具放到二维坐标里，可以看清 files.md 的真实位置——

![笔记工具哲学矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/files-md-no-second-brain-743-stars-2026-05-19/files-md-philosophy-matrix.png)

横轴是**功能复杂度**，从左到右是「仅必要功能 → 数据库 / 插件 / 协作齐全」。

纵轴是**拥抱 AI 深度**，从下到上是「AI 边界清晰（用户自己接 LLM） → 深度集成（产品自带 AI 摘要 / 标签 / 问答）」。

files.md 站在**左下角**——极简 + AI 边界清晰，让位置最远的还是 files.md。它没有内置 AI——README 里 zakirullin 留下的入口是「llms.txt + 复制到 CLAUDE.md」，让用户自己去用 Claude Code / Cursor / 自己的本地 LLM 去操作这套文件。

Obsidian 在**右下偏中**——功能丰富（插件系统 / 图谱 / 数据库），但 AI 是插件可选，不是内置。

Notion 在**右上**——数据库 + 协作功能丰富，Notion AI 是内置入口。

Logseq 与 Anytype 在**右下**——图谱与本地优先的取舍接近 Obsidian，但 AI 集成更保守。

国内 6 款工具的位置在矩阵右半部分铺开——

- **思源笔记**站在 **右中**：数据库 + 图谱 + 块引用都齐全，AI 是插件可选；开源 AGPLv3，国内开发者社区最活跃的一款。
- **wolai** 站在 **右上**：数据库丰富 + 内置 AI 助手；国内类 Notion 路线代表之一。
- **FlowUs** 站在 **右上偏中**：与 wolai 同档定位，文档协作 + 内置 AI。
- **飞书文档** 站在 **最右上**：团队协作权重最高，妙记 / 多维表格 / 飞书 AI 内嵌。
- **Notion 中国版**（专为大陆区合规调整的 Notion 服务）站在 **右中偏上**：数据库 + 一定 AI，但延迟与功能裁剪。
- **钉钉知识** 站在 **右上**：企业内协作场景。

看到这张图，第一反应不应该是「谁是冠军」。这张图给出的真实答案是——**files.md 这个左下角的孤点，是过去 3 年没人愿意去占的位置**。

## 七、files.md vs Obsidian vs 思源 vs 飞书：一张实操对照表

把 6 个候选工具按 9 个真实场景维度铺开——

![files.md tool 工具对照实操表](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-19/files-md-no-second-brain-743-stars-2026-05-19/files-md-tool-comparison.png)

| 维度 | files.md | Obsidian | Notion | 思源笔记 | wolai | 飞书文档 |
|---|---|---|---|---|---|---|
| 文件存放 | 本地 .md | 本地 .md | Notion 云 | 本地 SQLite | wolai 云 | 飞书云 |
| 是否开源 | MIT | 闭源 | 闭源 | AGPLv3 | 闭源 | 闭源 |
| 二进制大小 | 约 15 MB | 约 200 MB | 约 350 MB | 约 180 MB | 网页版 | 网页版 |
| 功能取舍 | 仅必要 | 丰富 + 插件 | 丰富 + 数据库 | 数据库 + 图谱 | 数据库 | 团队协作 |
| AI 集成深度 | 不内置 | 插件可选 | 内置 AI | 插件可选 | 内置 AI | 内置妙记 |
| LLM 对接友好度 | 极友好 | 友好 | 中等 | 中等 | 中等 | 需 API |
| 跨端同步 | 云盘 / 自建 | Sync 订阅 | 云 | S3 / WebDAV | 云 | 云 |
| 移动端形态 | 浏览器 / TG bot | 原生 | 原生 | 原生 | 原生 | 原生 |
| 入门门槛 | 极低 | 中等 | 中等 | 中等 | 低 | 低 |

这张表里**没有任何一行写「这是最佳工具」**。每一列代表一种取舍——

**如果你今天的需求是**「自己掌控所有文件，未来 10 年代码层面都看得懂改得动」——files.md 是这个方向上目前唯一一个把克制做到极致的工具。

**如果你今天的需求是**「已经攒了 1000 条笔记 + 200 个图谱链接 + 30 个插件」——Obsidian 还会继续是答案，files.md 没有插件系统，迁移成本不低。

**如果你今天的需求是**「团队 30 个人协作 + 实时编辑 + 数据库视图」——飞书文档、Notion、wolai 各有各的优势。

**如果你今天的需求是**「本地优先 + 数据库 + 图谱 + 中文界面 + 国内服务器」——思源笔记是国产派最熟的选择。

**如果你今天的需求是**「想试试一个完全没有插件、没有 AI、只有 markdown 文件的工具一周看看自己实际用什么功能」——files.md 拿来试一周成本几乎为零。

工具是用来匹配场景的，不是用来定义身份的。zakirullin 这次开源 files.md 给市场提供的不是又一个 Obsidian 替代——他在矩阵上插了一面旗，告诉读者**「极简 + AI 边界清晰」这个象限值得有人占着**。

## 八、AI 时代的笔记工具，「克制」是值得押注的路径吗

最后回答最难的一个问题——AI 时代笔记工具到底该往哪走？

**第一条观察 · 大语言模型把「笔记复杂度」反向重估**。过去用 Notion 数据库 + Obsidian 图谱去组织笔记，本质是为了「让自己未来某一天能找到信息」。但今天 Claude Code / Cursor / DeepSeek-V4 这些工具已经可以在 30 秒内把一个 5,000 条笔记的文件夹检索出关键 3 条——**「让未来的自己能找到」这个需求被 LLM 接走了**。所以图谱、数据库、复杂标签的相对价值在下降。

**第二条观察 · 纯 markdown 文件成了 LLM 时代的「真正一等公民」**。Notion 的导出格式有损，飞书文档需要走 API，wolai 的数据格式偏向自家。**任何 LLM 直接读 markdown 都不会失真**。files.md 这种「就是文件夹 + .md」的形态，恰好对位 LLM 的输入格式偏好。

**第三条观察 · 「克制」不是反 AI——是把 AI 当外挂**。zakirullin 不是反对 AI——他在 README 里写得很清楚：「You grow software around it with an LLM」（你用 LLM 在边上长出工具）。区别在于——**Notion AI、飞书妙记是「AI 内嵌产品里」，files.md 是「AI 站在用户这边读你的文件」**。这是两种完全不同的位置关系，没有谁对谁错——但在数据所有权敏感的场景下，第二种位置更让人放心。

**第四条观察 · 国内开发者的真实场景，「场景化分仓」可能比单押一款更稳**。一线开发者今天的笔记需求大概是这样分布——

- 公司团队协作 + 知识库——飞书文档 / 钉钉知识
- 个人长期知识沉淀 + 跨设备 + AI 检索——思源笔记 / Obsidian + 自接 LLM
- 写作 / 日记 / 习惯打卡 / 想法快速捕获——files.md / Bear / 备忘录
- 项目 README / 代码 + 文档共存——直接 git + markdown 文件

**没有一款笔记工具能同时通杀以上 4 个场景**——也不应该有。

**第五条观察 · 「克制」是一条可被持续维护的路径**。Obsidian 的插件生态今天看是优势，3 年后可能就是负债——插件作者放弃维护、API 升级失败、跨设备同步出问题。Notion 的数据库视图很强，但厂商一旦改产品策略，用户调起来很被动。files.md 这条路最大的吸引力是——**代码量小到任何一个独立开发者愿意接手维护**，文件是纯 markdown 任何文本编辑器都能打开。10 年后即使原作者退休，文件仍然在那里，能继续打开、继续读。

**第六条观察 · 国内笔记工具圈值得跟进的方向**。思源笔记开源 AGPLv3、Anytype 国内适配在做、wolai / FlowUs 都还在快速迭代——这条赛道远没有结束。**但是 files.md 这种「左下角孤点」位置上的国产工具今天还几乎没有**。如果有哪位国内独立开发者愿意写一个「中文版 files.md」——纯 markdown、单文件夹、自己负责跨端同步、给 Claude / 通义千问 / DeepSeek 留 llms.txt 接口——市场上确实有这块需求。

## 九、写在最后

files.md 这件事的真正意义不是又一个 Obsidian 克隆冲上 HN 头版——是一个塞浦路斯独立开发者用 5 年时间自己用、再开源的工具，把一个被市场冷落的位置占住了。

zakirullin 在 README 最后一段写——「My friends and I have been using this simple setup for five years, and it works well.」翻译——「我和朋友们用这套简单方案 5 年，它一直管用。」

这句话比任何 benchmark、任何 stars 数字都更有说服力。**一个工具能在 5 年时间里被一群人持续高频使用，比一周拿 789 stars 重要得多**。

国内开发者社区今天还在讨论「思源笔记和 Obsidian 哪个更好」、「飞书文档和钉钉知识哪个适合团队」——这些讨论会继续。files.md 这次开源给市场提供的，是另一条路径选项——**如果你已经被插件 / 模板 / 数据库 / 图谱搞得有些累，可以试试一周完全用一个文件夹 + .md 文件去过，看看自己真正需要的功能到底是哪几个**。

矩阵上的 17 个点没有谁是冠军——但每个象限值得有人占着。zakirullin 这次给出的，是左下角那个孤点的一种可能性。

最后一句——这次 HN 412 楼讨论里 zakirullin 自己最后那条 verbatim——「For the past week alone, I made 500+ commits, fixing all sorts of UI/UX fixes to perfect things out. I believe I put too much time into it during all those years, but I don't regret it. Because I use the project on daily basis.」翻译——「光上周我就提了 500 多个 commit 去打磨 UI 细节。我可能花了太多时间，但我不后悔，因为我每天都在用这个项目。」

**为自己造工具的开发者，永远值得多一份目光**。
