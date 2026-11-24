---
title: Splash 登上 C++ 日榜，Mac 推理该测哪些速度
date: '2026-11-24'
weekday: 星期二
slug: splash-apple-silicon-inference-2026-11-24
category: trending
description: 拆解针对 Apple Silicon 的推测解码与基准口径。
batch: noon
track: deep-dive
cover_style: data-visual
top_five_rank: 5
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/splash-apple-silicon-inference-2026-11-24.png
---

# Splash 登上 C++ 日榜，Mac 推理该测哪些速度

![Splash 登上 C++ 日榜，Mac 推理该测哪些速度](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/splash-apple-silicon-inference-2026-11-24.png)

incoai/splash 在这次 GitHub Trending C++ 日榜快照里有 2,683 颗星，当日增加 47 颗。它为 Apple Silicon 提供本地模型推理，支持桌面聊天和兼容 API。对打算让一台 Mac 带动编辑器或编码助手的人，Splash 的公开数据正好展示了该怎样区分解码速度和整项任务速度。

## 要点速览

- 日榜累计 2,683 星、当日 +47，来源为同一张 GitHub C++ 日榜快照。
- 官方入门要求 Apple M3 或更新、macOS 26.4 或更新。
- Splash 结合 DFlash 2 推测解码、Metal 内核、前缀缓存和并发批处理。
- 评测数据来自项目方测试。实测应分开记录冷启动、长输入、缓存重放和多路并发。

![C++ 日榜星标快照](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/noon-splash1.png)

*图片来源：GitHub Trending C++ 日榜 · C++ 日榜星标快照（原网页局部截图），2026-11-24。*

## 为什么围绕模型做引擎

Inco 把 Splash 定位为一台 Mac 上的推理服务，除了聊天页面，还能为兼容 OpenAI 或 Anthropic 接口的本地应用提供请求入口。官方列出多支 Qwen 权重及其他适配模型，也给出不同量化格式的示例。

![Inco 发布演示画面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/noon-splash2.png)

*图片来源：Inco 官方 Splash 技术博客 · Inco 发布演示画面（原图），2026-11-24。*

项目采用 DFlash 2 推测解码。可以把流程理解为先让配套的轻量模型提出一小段候选，再由目标模型验证。如候选经常被接受，相同目标模型需要串行走的步数就可能减少。匹配的草稿模型、候选接受情况和硬件决定能节约多少时间。

![推测解码与 Metal 简介](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/noon-splash5.png)

*图片来源：Splash 官方 GitHub 仓库 · 推测解码与 Metal 简介（原网页局部截图），2026-11-24。*

Metal 内核和内存规划针对苹果硬件优化。推理时仍要容纳权重、草稿模型、键值缓存和运行时缓冲。README 指出某些量化布局不受支持，也分别列出可用的量化和内存说明。能加载某家族的一种权重，不代表仓库里每个衍生版本都适配。

## 74 token/s 在什么条件下成立

README 把不同阶段拆得很清楚。以 Qwen3.8-27B 为例，官方在 16 核 GPU、48 GB 的 M5 Pro 上运行 Splash 1.0，通过 HTTP 使用选定的 SPEED-Bench 编程提示。下表四项均来自这组设备、模型与测试条件。

![公开基准的设备与测试条件](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/noon-splash4.png)

*图片来源：Splash 官方 GitHub 仓库 · 公开基准的设备与测试条件（原网页局部截图），2026-11-24。*

| 指标 | 官方数据 | 此数字观察的环节 |
| --- | ---: | --- |
| 短提示解码 | 74 token/s | 已开始输出后生成速度 |
| 32K 输入预填充 | 363 token/s | 推理前处理输入 |
| 32K 缓存重放首字 | 282 ms | 已命中前缀缓存的等待 |
| 四路短提示解码合计 | 170 token/s | 四个请求合计吞吐 |

![公开基准首字延迟行](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/noon-splash6.png)

*图片来源：Splash 官方 GitHub 仓库 · 公开基准首字延迟行（原网页局部截图），2026-11-24。*

当一个编程助手反复带着相同仓库说明和工具列表发请求，前缀能重复利用。若每轮都重排前缀、改写系统提示或替换前面的文件，原先的重放条件就变了。压测要成对保存冷、热两组记录，并固定请求的共有前缀。

官方表对别的模型还用过不同的设备和对比对象。例如 Qwen3.8-Flash-Next 一列来自 20 核 GPU、64 GB 的 M5 Pro。设备和权重都变了，跨列比较适合用于提出试验假设，最终还要回到同一台机器和同一批输入。

## 在一台合适的 Mac 上起服务

先查系统版本，再看可用磁盘和统一内存。桌面版可从官方 Releases 的 DMG 开始。命令行方式需要 Homebrew，README 的示例如下。

```bash
brew install incoai/tap/splash
splash serve --model unsloth/Qwen3.8-27B-GGUF:UD-Q4_K_M
```

![官方安装示例](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/noon-splash3.png)

*图片来源：Splash 官方 GitHub 仓库 · 官方安装示例（原网页局部截图），2026-11-24。*

首次运行会下载模型权重及匹配的草稿，后续启动复用下载。终端出现 Ready 后，可打开本机的 127.0.0.1:8000，也可先用兼容的客户端发一条短请求。首次验收宜采用无敏感内容，把监听范围保留在本机。

从两组真实任务入手通常比追单项峰值更快。第一组是几十到几百字的问答，记单请求首字时间和最终完成时间。第二组是很长的说明和一段反复使用的公共前缀，先测模型冷加载，再测已加载时首次遇到前缀的延迟，最后换尾部内容测缓存命中。

然后可以走四路并发，另记每条请求的等待和系统峰值内存。合计吞吐上升时，个别请求有可能等待更久。若使用者是一位经常交互式调试的人，他主要在意第一条输出何时出现；若背后有一批自动化任务，合计完成时间才更重要。

最后把接口兼容当成单独的验收项目。聊天能回字只验证了最基本路径，工具调用、结构化输出、长文本和中断恢复还要按客户端需要逐个试。工作中途改了模型或量化，要同时记到结果表，免得把模型差异归给引擎。

## 推理能跑起来以后，瓶颈会迁移

一条编码请求通常还会等客户端整理上下文、工具返回、模型判断下一步，再等最终结果。服务端解码加速一倍，对某次单轮问答可能影响明显。若任务的大部分时间花在等编译和测试，整条链路的改善就需要重新测量。

首字延迟也可以拆为排队和计算。并发一多，硬件利用率可能上升，交互请求却可能在队列里等更久。记录每条任务进入队列、首字出现和最后一字的时间，才能说明用户看见的卡顿究竟发生在哪一步。

模型选择可先按场景定两档。较小权重用于高频问题和结果可快速检查的任务，较大权重用于需要读取长材料的任务。比较时固定量化格式和输入，确保耗时差异来自所关注的因素。两档都要保留余量给系统与其他应用。

硬盘空间也应该单列。首次下载安装与日常请求的成本不同，后续模型或草稿升级也可能重复占用。对多人复用的机器，可以记下常用模型、首次常驻大小和可回收的旧版本，再决定是否维持两套权重。

若要给同组同事分享评测，除了平均速度，最好公布测试机器的内存、系统版本、模型标识、提示长度和并发数。这样其他人可以在相近环境复查，也能判断某个数字适合当作起点，还是只对当前机器和任务成立。

工具调用应先用可人工复查的任务，比如在公开文本里提取几个字段。对照输出与原材料，再比较生成速度。这个顺序能避免把格式处理失败误认成模型慢，也能发现某个客户端的重试放大了服务器实际承受的负载。

例如，固定一份公开代码样例，让两个客户端依次生成同一种结构化摘要。先比较服务日志里的提示长度、输出长度和重试次数，再比较最终耗时。如果客户端一直重新发送长前缀，应用层的配置调整可能比更换权重更有效。

等能够稳定复现后，可以把一轮正常使用的功耗和温度也记下来。台式机、外接电源的笔记本和电池供电有不同的限制。对通勤或经常移动的用户，持续十分钟的表现比一条短输出更接近日常。

Splash 的上榜给 Apple Silicon 用户带来了一套可复现的本地推理入口，选型最好用冷、热和并发三组自己的任务数据来决定。

## 🔗 引用链接

- [GitHub Trending C++ 日榜](https://github.com/%74rending/cpp?since=daily)
- [Splash 官方 GitHub 仓库](https://github.com/incoai/splash)
- [Inco 官方 Splash 技术博客](https://inco.ai/blog/splash/)
