---
title: Claude 把聊天作品装进桌面，DeepMind 用 544 道基础题考模型 | AI 日报 | 2026-11-26
date: '2026-11-26'
weekday: 星期四
slug: ai-daily-2026-11-26
category: ai-daily
description: Claude 桌面应用、DeepMind 日常可靠性基准、英伟达供应链调查与三条国内动态，附五个 GitHub 日榜项目。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/daily-cover.png
batch: morning
cover_style: magazine-collage
---

# Claude 把聊天作品装进桌面，DeepMind 用 544 道基础题考模型 | AI 日报 | 2026-11-26

一个 Claude 对话里做好的小工具，现在能出现在 Mac 或 Windows 的应用列表里。DeepMind 同期给前沿模型出了套基础题，图里数东西、照格式答题，换个问法还要答对。11 月 26 日早报从这两件事说起，再看英伟达的芯片客户和国产开源模型。

## 要点速览

- Claude Artifacts 开始支持安装成独立窗口的桌面应用，调用 Claude 仍计入套餐用量。
- DeepMind 的 544 道基础题专门看答题稳定性，最高分模型四个变体全对的比例为 92.4%。
- 百度公开文心 5.5 Flash Preview 权重，腾讯翻译模型从 38 种语言扩到 102 种。
- GitHub 日榜捞出 5 个此前未收录项目，覆盖前端、远程桌面、邮件、CRM 与智能体技能。

## 大厂与前沿

本期收录 11 月 25 日的新进展，以及过去两天发布的重点产品与模型。

### Claude 的作品，从聊天侧栏搬进 Mac 和 Windows

Anthropic 在 11 月 24 日公告中推出 Install as app 测试功能。用户先在 Claude 里生成交互式作品，再从分享菜单安装。作品从此有自己的图标和窗口，还可以分享给别人。

官方样例从菜单栏性能监视器做到文件管理器。能在桌面打开，改变的是复用成本：用惯了的小工具可以像普通应用一样启动。上线版本仍要求登录并联网，每次调用 Claude 也会消耗账户额度。

![Claude 的桌面应用演示](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/claude-desktop.png)

*图片来源：Anthropic《Turn artifacts into Mac and Windows apps》，2026-11-24。*

### DeepMind 给前沿模型出了 544 道基础题

Google DeepMind 公布 Humanity’s First Exam，把 544 道图文提示分在 20 类基础能力里。同一题换成四个变体，四次都答对才算那组通过。论文评测里，前五名的单次通过率为 96% 至 98%，四个变体全对的比例平均 85.8%。

有道题给出一张霓虹灯照片，要求按顺序报出每个单词第三个字母的颜色，并指定分隔符。认出文字只是第一步，还要数对位置、辨对颜色、照格式输出。把这些简单动作连起来，才像日常自动化遇到的工作。

![DeepMind HFE 霓虹灯颜色题](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/hfe-neon.png)

*图片来源：Google DeepMind，Humanity’s First Exam 官方样题照片，2026-11-24。*

### 英伟达客户 Megaspeed，被追问算力去了哪

《时代》11 月 25 日追查英伟达客户 Megaspeed 的股权和业务关系，援引公司记录及新披露的美国国会材料。报道关注的是，先进 GPU 在经由云和数据中心合作交付后，最终由谁控制、用在哪里。

这对算力市场意味着，同一批卡的交付风险会沿客户、运营方和终端用户一路传递。报道披露的许可材料涉及申请，实际批准和最终去向需要分别看待。

![TIME 关于英伟达客户 Megaspeed 调查的报道配图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/nvidia-time.png)

*图片来源：Taryn Ng／AFP／Getty Images，Megaspeed 新加坡办公室，摄于 2026-11-10，TIME 于 2026-11-25 刊发。*

### MIT 给软件装上可以摸到的旋钮

MIT News 介绍了 TangiMap，一套把软件功能映射到实体旋钮、滑杆和按钮的研究原型。演示覆盖修图、音乐和 3D 建模，研究团队还计划开展用户研究。

它瞄准的是数字界面里反复发生的小动作。调音量、改图像参数或转动模型时，手留在同一个控件上，屏幕可以专心显示结果。原型展现了这种交互的可能性，实际效率仍等用户实验回答。

## 🇨🇳 国内 AI 观察

### 百度放出 280B 文心权重，每步激活 14B

百度于 11 月 23 日上传文心 5.5 Flash Preview 权重，24 日更新模型卡。模型卡列出 280B 总参数、14B 激活参数和百万 token 上下文，采用 Apache 2.0 许可。这是文心 5.5 Flash Base 上做后训练的预览模型。

激活参数说明每个 token 使用的参数规模，部署时还得给整套专家权重和长上下文缓存安排空间。对想把模型接进自家工具链的团队，开放权重给了它们调整推理方式的余地。

### 腾讯把翻译模型的语言覆盖从 38 种扩到 102 种

腾讯混元释出 Hy-MT3 的 1.8B、7B、30B-A3B 三档权重。团队技术报告称，最大档在其真实场景数据上，相比同档 Hy-MT2，出现漏译或增译的样本比例相对下降 74%。

游戏本地化很能说明这个差别。一条对白可能夹着玩家名占位符、富文本标签和固定译名。译文再顺口，变量一旦被改坏，游戏里就可能显示错误。新版模型同时把这些约束纳入对比。

![Hy-MT3 翻译错误类别对照图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/inline/tencent-errors.png)

*图片来源：腾讯混元 Hy-MT3 技术报告 v2 图 5，2026-11-23。*

### 字节「小豆」开启邀请制公测，把盯开票变成后台任务

据 36 氪转载的智东西报道，豆包旗下个人助理「小豆」开启邀请制公测，场景包括跟进演出开票、订餐、出行和整理文档。它的卖点是长期记忆、后台执行和主动推送。

用聊天窗口查一次活动，问完即走。把盯开票交给个人助理，用户关注的是它能否在下次打开手机前把变化送过来。对现有入口来说，这是一种从问答频次转向任务持续性的竞争。

## 📦 GitHub Trending

11 月 26 日北京时间 05:51 左右抓取 GitHub 语言日榜。星数和新增均为页面快照，榜名与名次随更新变化。

| 仓库与榜位 | 总星／日榜新增 | 用途 |
| --- | --- | --- |
| [Svelte](https://github.com/sveltejs/svelte)，JavaScript 第 2 | 89,920／+139 | 编译式前端框架，构建时把组件转成运行代码 |
| [RustDesk](https://github.com/rustdesk/rustdesk)，Rust 第 10 | 131,463／+246 | 开源远程桌面，支持自托管 |
| [Vercel Agent Skills](https://github.com/vercel-labs/agent-skills)，JavaScript 第 4 | 34,920／+56 | 为编码助手提供可复用的前端开发指引 |
| [Twenty](https://github.com/twentyhq/twenty)，TypeScript 第 9 | 61,476／+54 | 可自托管的开源 CRM，能按业务自定义数据对象 |
| [Mailflare](https://github.com/hieunc229/mailflare)，TypeScript 第 10 | 7,672／+89 | 可自托管的团队邮箱、日历和网盘，内置 AI 智能体 |

今天的变化落在三个具体问题上：临时生成的工具能否反复用，简单指令能否稳定做对，专业输出能否守住格式和术语。

## 🔗 引用链接

- [Anthropic：Install as app](https://claude.com/resources/articles/install-as-app)
- [Google DeepMind：Humanity’s First Exam](https://blog.google/innovation-and-ai/models-and-research/google-deepmind/humanitys-first-exam/)
- [Google DeepMind：HFE 技术报告](https://storage.googleapis.com/deepmind-media/papers/hfe_tech_paper.pdf)
- [TIME：Megaspeed 调查](https://time.com/article/2026/11/25/nvidia-megaspeed-china-investment/)
- [MIT News：TangiMap](https://news.mit.edu/2026/tangimap-agentic-system-personalizes-physical-controls-help-users-dj-their-art-1124)
- [百度：文心 5.5 Flash Preview 模型卡](https://huggingface.co/baidu/ERNIE-5.5-Flash-Preview)
- [腾讯混元：Hy-MT3 仓库](https://github.com/Tencent-Hunyuan/Hy-MT3)
- [36 氪／智东西：小豆公测](https://36kr.com/p/4083578123184773)
- [GitHub Trending JavaScript](https://github.com/trending/javascript?since=daily)
- [GitHub Trending TypeScript](https://github.com/trending/typescript?since=daily)
- [GitHub Trending Rust](https://github.com/trending/rust?since=daily)
