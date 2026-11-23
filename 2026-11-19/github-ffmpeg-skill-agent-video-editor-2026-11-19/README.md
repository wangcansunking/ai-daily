---
title: "ffmpeg-skill：给编程智能体配一个会自检的本地剪辑台"
date: 2026-11-19
weekday: "星期四"
category: "开源"
slug: "github-ffmpeg-skill-agent-video-editor-2026-11-19"
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/github-ffmpeg-skill-agent-video-editor-2026-11-19.png
description: "kajisho5/ffmpeg-skill 出现在 GitHub Trending Python 日榜，累计约 8,000 星，当日新增 338 星。项目提供 42 个 FFmpeg 工具，可通过 Agent Skill 或 MCP 在本机剪视频并核对输出。"
tags: ["ffmpeg-skill", "FFmpeg", "Agent Skill", "GitHub Trending"]
track: "github-trending"
batch: "noon"
repos: ["kajisho5/ffmpeg-skill", "Vincentwei1021/video-shotcraft"]
---

# ffmpeg-skill：给编程智能体配一个会自检的本地剪辑台
![毛毡风格的机器人用剪刀裁剪胶片，旁边有尺子、绿色勾号和竖屏手机](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/github-ffmpeg-skill-agent-video-editor-2026-11-19.png)
*图片来源：Sunky 中文 AI 日报专题配图，2026-11-19。*

**智能体剪视频，难在先量后剪、剪完自检。ffmpeg-skill 把这套纪律写进了工具合同，让编程智能体在本机用 FFmpeg 处理素材，并用成片的测量结果交代做了什么。** 以讲解录像为例，裁成竖屏以后，即使文件正常打开、时长也对，画面里的重点仍可能被裁掉。工具能核对可测指标，成片是否讲清楚了，则需要编辑者判断。[1]

## Python 日榜上的热度，指向能接入编辑流程的工具

![GitHub Trending Python 日榜上的 kajisho5/ffmpeg-skill 一行，显示累计 8,034 星、当日新增 338 星](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-github-trending-ffmpeg-skill-row.png)
*图片来源：GitHub Trending（Python，Today）页面截图，2026-11-19。*

| 项目 | 记录 |
| --- | --- |
| 累计星标 | 约 09:55 的日榜页面为 8,033 星，同日榜单截图显示 8,034 星，10:04 时仓库为 8,036 星 |
| 当日新增 | GitHub Trending Python 日榜 +338 星，11 月 19 日约 09:55 |
| 分叉数 | 754（Python 日榜截图） |
| 许可证 | MIT |
| 仓库创建 | 2026 年 9 月 3 日 |
| 最新发布 | v3.13.2，2026 年 11 月 19 日 07:56 |

表中时刻均为北京时间。累计星标是各页面在各自时点显示的总数，榜单的当日新增有自己的统计口径。对开发团队而言，日榜提供发现项目的入口，许可证和具体发布版本则是评估引入时可记录的依据。[1][2]

## 剪之前读取素材，交付时重新检查成片

![ffmpeg-skill 项目头图，终端示例展示探测、剪切、画幅适配、导出、规格检查和缩略图预览](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-ffmpeg-skill-readme-og.png)
*图片来源：kajisho5/ffmpeg-skill README 头图，2026-11-19。*

ffmpeg-skill 给 Claude Code、Cursor 和 Codex 提供一份 Agent Skill，也提供 MCP 服务。它用本地的 FFmpeg 和 ffprobe 工作，Python 工具本身依赖标准库，日常剪辑无需云端服务或 API 密钥。仓库附带 42 个工具，覆盖从量素材到输出成片的常见操作。对使用者来说，一个讲解视频可以在同一流程里完成截取、改画幅、加字幕和交付检查。[1][3]

- **探测（probe）**。读取输入的时长、画幅和流信息，先确认手里的文件能提供什么。
- **编辑（edit）**。在条件允许时使用流复制，保留原编码。裁画面或把字幕烧进画面时，按目标重新编码。
- **检查（check）**。按交付目标检查文件，可以核对平台规格，加上 `--content` 可检查黑帧、冻结帧或静音。
- **验证（verify）**。重新量实际输出。时长、编码以及工具承诺的指标，以写出的文件为准。画面发生变化时，README 还建议查看缩略图总览。[1]

以横屏转竖屏为例，智能体交给工具的是画幅和适配方式这样的具名参数，例如 `--aspect 9:16 --fit pad`。工具根据参数生成 FFmpeg 命令，不接受调用方直接提供的滤镜图，也不经过 shell 执行。声明的输入路径不能同时作为输出，替换已有输出还需显式给出 `--overwrite`。于是同一份素材可以先做保留全幅的版本，再试裁切版本，各自保留可复查的文件。[1]

这些限制会改变重试的方式。比如智能体把同一条剪辑请求再发一次，已有输出会触发明确的拒绝，调用方可以检查那份文件、另起文件名，或者在确认后授权替换。对团队共用的素材目录，这比把每次执行都视作一次全新的剪辑更容易追查。源文件保留在原位，改过的版本也有自己的去处，回看片段时可以找到对照。[1]

![四组左右对比演示，分别是卡拉 OK 字幕、横屏转竖屏、去除静音和响度标准化](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-ffmpeg-skill-demo-grid.png)
*图片来源：kajisho5/ffmpeg-skill README 演示动图截帧，2026-11-19。*

展示中的画幅裁切、字幕动效、去静音和响度标准化，适合用来观察一次处理到底改变了什么。演示素材大多由 FFmpeg 自带测试源合成，少数演示采用 AI 生成片段。试用时可以把这些画面当成操作样例，再用自己的素材判断裁切是否保住重点、字幕是否挡住信息，以及短暂停顿是否承担了叙事节奏。[1]

## 命令行与 MCP 从同一个解析器读取参数

![GitHub 上 kajisho5/ffmpeg-skill 仓库主页，左侧是目录，右侧是项目简介](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-github-ffmpeg-skill-repo.png)
*图片来源：GitHub 仓库页面截图，2026-11-19。*

SPEC 是项目给这套执行合同取的名字。每个工具的输入结构 `input_schema` 在运行时从 Python 的 `argparse` 解析器派生，直接读取命令行实际接受的参数、类型、默认值和可选值。MCP 服务把合同里的输入结构用于工具列表。工具用途、所需能力和输出结构等信息由项目单独维护。持续集成会检查合同与相关文档是否偏离实现。[1]

这对接入的价值体现在参数改动时。比如画幅适配工具调整了某个选项的可选值，客户端和命令行可以沿用同一份输入定义。开发者可以把精力留给更贴近任务的判断，比如保留整幅课件时选补边，主体可安全裁切时再考虑铺满竖屏。结构检查可以限制参数怎么写，素材内容决定该用哪一种参数。[1]

工具还可以用结构化 JSON 返回结果。一个批量处理录屏的任务可以据此区分缺少本机能力、导出超时和验证失败，也可以把输出重新测得的时长或编码交给后续环节。比如程序收到超时状态时，可以把这一项保留为待处理。拿到成片的检查结果后，才有依据把它交给下一步。这样，任务状态跟着文件走，编辑者能看到每一段素材处理到了哪里。[1]

输入合同也有自己的边界。一个画幅参数通过类型检查，只能说明工具理解这个请求。素材能不能正常解码、所需的滤镜是否存在、输出有没有兑现时长要求，分别要靠本机诊断、执行和复测来回答。把这些结果分别写进任务状态，技术负责人就能看出这次处理卡在环境、参数还是成片上，也能把需要看片的问题留给编辑者。[1]

## 一小时样本中，重编码比探测耗时更长

![ffmpeg-skill 项目官网首屏，标语为给编程智能体一个视频编辑器](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-ffmpeg-skill-website.png)
*图片来源：ffmpeg-skill 项目官网截图，2026-11-19。*

项目 README 记录了 Linux 上使用 FFmpeg 6.1.1 处理一小时合成测试视频的耗时。分析使用整段素材，表中的流复制剪切和两项短片导出使用十分钟片段。这组结果可以用来区分哪些操作适合先试，哪些要留出渲染时间。[1]

| 操作 | 处理范围 | README 记录 |
| --- | --- | --- |
| `probe` 读取媒体信息 | 一小时合成视频 | 0.3 秒 |
| `silence --list` 扫描静音 | 一小时合成视频 | 14 秒 |
| `loudness.py --measure-only` 测量响度 | 一小时合成视频 | 66 秒 |
| `scenes` 分析镜头 | 一小时合成视频 | 241 秒 |
| 流复制剪切 | 十分钟片段 | 2.4–4.4 秒 |
| YouTube 默认预设导出 | 十分钟片段 | 514 秒 |
| YouTube 预设加 `--fast` 导出 | 十分钟片段 | 162 秒 |
| YouTube 默认预设导出 | 一小时合成视频 | 达到默认 1,800 秒超时后终止 |

这个样本的镜头分析明显比读取文件信息耗时。十分钟片段的默认导出略快于实时，整小时重编码触发了超时。[1]

对同一批素材，流复制剪切和按平台预设导出解决的是不同的问题。前者适合先分出已有编码可用的片段，后者承担生成交付文件的工作。即便片段时长相同，两者等待时间也可相差很大。把它们分别计时，才能知道批处理慢在前面的选段还是最后的交付，以及换一种预览设置能省下哪一段等待。[1]

默认的 `--timeout` 对单次 FFmpeg 调用生效。已知是长时间重编码时，可以设更长的预算，`--timeout 0` 则取消这一时间上限。若当前只需要确认字幕位置和画幅，`--fast` 提供较快的预览路径。这里的耗时来自 README 的样本和环境，实际排期仍取决于自己的素材、机器和导出设置。[1]

## 本机试用从可用组件和交付要求入手

![kajisho5/ffmpeg-skill 的 GitHub 社交预览卡，显示贡献者、议题、星标与分叉数](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-github-ffmpeg-skill-social-preview.png)
*图片来源：GitHub 社交预览图，分叉数以表中的日榜截图为准，2026-11-19。*

npm 包提供安装和诊断入口。按 README，默认安装目标是 Claude Code，使用 Codex 时选 `--codex`。以下前两条按客户端择一执行，最后一条仅在需要本地语音转字幕时使用。[1][4]

```bash
npx ffmpeg-skill
npx ffmpeg-skill --codex
npx ffmpeg-skill doctor
npx ffmpeg-skill contract --json
pip install faster-whisper
```

`doctor` 检查本机的 FFmpeg、ffprobe 和工具需要的组件。对准备处理字幕的团队，诊断里与字幕工具相关的结果比只看总体状态更具体，因为某个组件缺失时，其他工具仍可能可用。`contract --json` 则输出机器可读的合同，可以在接入前查清哪些字段由调用方提供。语音转字幕需要另装本地引擎，`faster-whisper` 首次运行会下载模型权重，音频本身留在机器上。[1][4]

用一段已有的录屏试用时，可以把剪辑要求写成能逐项核对的交付条件，例如指定起止位置、需要保留的画面区域、字幕是否烧录。文件写出后，再检查时长和画幅，并回看字幕与画面的关系。工具可以把重复的测量交给程序，讲解是否连贯、哪个段落应当留下，仍由下达任务的人或上层智能体负责。[1]

交付检查也适合留下可读的结果。比如一份课件被裁成竖屏，规格检查可以确认格式达标，画面回看可以确认讲者指向的内容仍在画面里。团队可以据此把重复的格式核对交给程序，让看过素材的人专注于表达。对交付方来说，文件和检查结果同时可见，返工时就有了可以核对的起点。[1]

## 真实素材剪辑与逐帧渲染有不同的起点

![video-shotcraft 的六格镜头样例，画面包括发光圆环、镜头特写和倾斜的数据面板](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-video-shotcraft-readme-cover.png)
*图片来源：Vincentwei1021/video-shotcraft README 封面图，2026-11-19。*

同日 TypeScript 日榜上的 `Vincentwei1021/video-shotcraft` 显示累计 21,338 星、当日新增 237 星。它用 Remotion 把页面截图和文案变成可编辑的产品宣传片，README 列出 124 张镜头配方卡、共 150 个镜头，可交付 MP4，并可选导出剪映草稿。这里的选择是从已有的编排样例里找一种表达方式，再用代码画出每一帧。相比之下，ffmpeg-skill 从已经拍下或录好的素材出发，用探测结果安排剪切、改画幅和导出检查。有页面和文案、需要组织产品叙事时，前者提供镜头制作流程。有真实录屏、需要稳定地变换和核对文件时，后者负责具体的媒体处理。[1][5]

一条竖屏录屏的时长、画幅和编码可以写进检查结果。课件上的重点有没有留在画面里，仍要回看成片。

## 引用来源

- [1] [kajisho5/ffmpeg-skill 官方仓库与 README](https://github.com/kajisho5/ffmpeg-skill)
- [2] GitHub Trending（Python · Today）榜单页面，2026-11-19
- [3] [ffmpeg-skill 项目官网](https://kajisho5.github.io/ffmpeg-skill/)
- [4] [npm 上的 ffmpeg-skill 软件包](https://www.npmjs.com/package/ffmpeg-skill)
- [5] [Vincentwei1021/video-shotcraft 官方仓库与 README](https://github.com/Vincentwei1021/video-shotcraft)、同日 GitHub Trending TypeScript 日榜
