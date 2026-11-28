---
title: "OpenSandbox 日榜第 7：给 AI 发一次性工作机"
date: "2026-11-28"
weekday: "星期六"
slug: "opensandbox"
category: "github-trending"
description: "从本地 Docker 起步，用一套 API 给代码智能体分配、使用和回收隔离环境。"
cover: "https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/opensandbox.png"
batch: "noon"
cover_style: "conceptual-3d"
---

# OpenSandbox 日榜第 7：给 AI 发一次性工作机

一个 Agent 在装依赖，另一个刚好开始跑测试，两边碰巧用了同一个目录。十分钟后报告全绿，你却说不清测的是谁的代码。OpenSandbox 给出的办法很直接：每个任务领取自己的运行环境，用完回收。

## 要点速览

- 11 月 28 日午班抓取的 GitHub Trending Go 日榜，OpenSandbox 排第 7，累计 **17,984 星**，榜单标注今日 **+36 星**。
- 本地可从 Docker 起步，集群可接 Kubernetes。创建环境、执行命令、上传和读取文件，都能走统一接口。
- 项目公布的 Firecracker 预热串行基准中，沙箱就绪中位数为 **64 毫秒**。资源池接近满载，成功率会比延迟更显眼。

## 从「给 AI 一把终端」到「给任务一台机器」

最早写工具调用时，一个 shell 往往就够了。等产品要同时服务十个人，问题立刻变样。模型可能改工作目录、下载依赖、留下后台进程。下一次任务继承这一切时，即使两次提示词一字不差，结果也可能漂移。

OpenSandbox 把环境变成调用方能主动管理的资源。程序先选镜像或模板，申请一个沙箱，再把输入文件放进去，执行命令并取回结果。任务结束后由程序销毁环境。任务编号、输入版本和产物都可以留在应用自己的记录里。

![OpenSandbox 架构总览](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/opensandbox-architecture.png)

*图片来源：OpenSandbox 官方仓库架构图，2026-11-28 取图。*

控制面负责生命周期，沙箱内的执行服务承接文件和命令操作。同一批作业可以共用一份环境定义，各自写入自己的目录。对代码评测来说，这让「哪一版代码在什么环境里跑过」有了可追溯的答案。

它也能装下一整个桌面。官方示例展示了在沙箱里打开图形界面。如果你在做桌面应用的回归演练，可以把应用和样例数据装进镜像，每次从相同初始状态打开，再把截图带出环境。

![官方沙箱中的桌面界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/opensandbox-desktop.jpg)

*图片来源：OpenSandbox 官方 Desktop 示例，2026-11-28 取图。*

## 64 毫秒背后是一池预热资源

官方性能页给了两个容易被混在一起的数字。早期串行基线的中位数是 64.0 毫秒。同一份文档用另一组客户端配置复跑时，串行中位数为 77.9 毫秒。并发 10 路那组跑了 100 次，全部就绪，尾部 P99 为 125.4 毫秒。

| SDK 创建场景 | 就绪结果 | 中位延迟 |
| --- | --- | --- |
| 预热后的初始串行基线 | 100/100 | 64.0 毫秒 |
| 同批串行复跑 | 100/100 | 77.9 毫秒 |
| 10 路并发 | 100/100 | 102.7 毫秒 |
| 20 路并发 | 51/100 | 136.7 毫秒 |

计时从 SDK 发起创建开始，到经网关探测到环境就绪结束，销毁和批间等待另外计时。模板已缓存，测试环境是 Kubernetes 上的 Firecracker 运行时。表中的 20 路数据尤其扎眼，近半请求没成功，136.7 毫秒描述的是成功样本，读秒表时要把分母带上。

![OpenSandbox 快速沙箱暂停与唤醒流程](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/opensandbox-lifecycle.png)

*图片来源：OpenSandbox 官方暂停与唤醒时序图，2026-11-28 取图。*

如果在做自己的任务队列，可以把排队时间、创建到就绪时间、实际执行时间拆开记录。创建很快但等待很久，优先调并发与容量。沙箱秒开、依赖却装了三分钟，第一笔优化预算该花在预构建镜像上。

## 用 Alpine 跑第一单，先别搬集群

找一台已经装好 Docker 的开发机，准备 Python 3.10+ 和 uv。如果已有 ~/.sandbox.toml，用独立测试用户或先保留原配置。在一个终端执行官方 Docker 示例的两条命令。

```bash
uvx opensandbox-server init-config ~/.sandbox.toml --example docker
uvx opensandbox-server
```

在另一个终端创建 Python 虚拟环境，安装 opensandbox SDK。按仓库 Getting Started 示例，在异步函数里用 `await Sandbox.create("alpine")` 创建环境，调用 `await sandbox.commands.run("echo hello")` 取回标准输出。把 `await sandbox.destroy()` 放在 `finally`，让异常分支也会清理。

![OpenSandbox CLI 创建沙箱的界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/28/opensandbox-create.png)

*图片来源：OpenSandbox 官方 CLI 演示，2026-11-28 取图。*

第二轮可以准备一个十行的小脚本，把它写到沙箱的 /tmp，只读一份虚构输入，输出一份结果文件。销毁后创建第二个环境，看看旧文件是否随环境消失。走通这一步，再考虑接入真实代码任务或把产物存到对象存储。

你还可以用同一份脚本开两个环境，分别读取不同的样例数据，却都把文件写成 result.json。调用方用各自的任务编号收产物，脚本依然只写自己的本地文件。以后要换镜像或加重试，脚本就少一个需要跟着修改的地方。

## 沙箱的边界取决于你给了什么

给任务挂上宿主机目录、开放外网、注入长期密钥，隔离收益就会缩水。初次落地时用临时目录和样例数据足够。等确实需要联网拉包，再针对那类任务单独放开网络和短期凭据。

如果只在自己电脑里顺序执行几段可信脚本，普通容器就能解决很多问题。OpenSandbox 更适合做共享的 AI 执行服务，尤其是环境反复创建、结果要带出、资源还得明确回收的团队。

还有一个常被忽略的成本是冷启动。第一次拉大镜像、创建缓存和恢复环境的等待会落到用户身上。把依赖分成常用底座与任务专属两部分，底座随镜像走，业务输入在启动后再传，能减少重复准备。

**一句话总结**：OpenSandbox 把 Agent 的工作环境做成按任务分配的资源，先用 Docker 跑通一次创建到回收，再决定是否上集群。

## 来源

- [GitHub Trending Go 日榜](https://github.com/trending/go?since=daily)
- [OpenSandbox 官方仓库与快速开始](https://github.com/opensandbox-group/OpenSandbox)
- [Fast Sandbox 性能报告](https://github.com/opensandbox-group/OpenSandbox/blob/main/docs/architecture/fast-sandbox/performance.md)
- [OpenSandbox Desktop 示例](https://github.com/opensandbox-group/OpenSandbox/tree/main/examples/desktop)
