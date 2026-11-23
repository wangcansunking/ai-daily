---
title: Mooncake 上榜，Kimi 怎样跨机器复用推理缓存
date: '2026-11-23'
weekday: 星期一
slug: mooncake-shared-kv-cache-2026-11-23
category: trending
description: 解读 Mooncake 共享键值缓存、跨节点传输及小规模压测方法。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/mooncake-shared-kv-cache-2026-11-23.png
batch: noon
track: deep-dive
cover_style: abstract data visualization
top_five_rank: 4
---

# Mooncake 上榜，Kimi 怎样跨机器复用推理缓存

![数据可视化抽象封面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/mooncake-shared-kv-cache-2026-11-23.png)

11 月 23 日上午，GitHub Trending 的 C++ 日榜显示，月之暗面 Kimi 使用的推理服务项目 Mooncake 累计 7,108 颗星、今日新增 6 颗。它把大模型推理中可复用的前缀状态放进跨实例缓存，并提供高效搬运数据的引擎。对需要反复读长文档的服务，复用这些状态可以释放算力，但前提是读缓存真的更省时。

## 要点速览

- GitHub C++ 日榜快照为 7,108 stars，+6 today，统计口径来自同一个榜单页面。
- 项目以键值缓存为中心，可结合推理框架分开部署预填充和逐词元解码。
- Mooncake Store 管缓存对象，Transfer Engine 管数据搬运；部署时两层可以有不同的优化目标。
- 上手应从相同流量的单机基线开始，随后才排查跨实例复用、带宽和尾延迟。

## 长前缀怎样被算了很多遍

一套问答服务可能反复输入同一份产品手册，每次只替换最后一个问题。模型先处理输入，逐层建立注意力计算所需的键和值，生成时继续使用。只要模型与共享前缀的约束匹配，就有机会复用这些键值缓存，省去重复预填充。

![Mooncake 官方文档架构图（取阅）](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/moon-arch.png)

*图片来源：Mooncake 官方文档架构图（取阅），2026-11-23。*

普通实例的缓存通常优先照顾当前工作进程。扩到多机后，请求可能落在另一节点，之前算出的状态拿不到，或者当热点变了又很快被淘汰。Mooncake 的做法是为缓存设计共享层，使推理框架能够用同一入口查找、分配和传输这些对象。

| 部件 | 负责的事 | 上线先测 |
| --- | --- | --- |
| 预填充与解码实例 | 处理输入与生成输出 | 首词延迟、逐词延迟 |
| Mooncake Store | 分配与查找共享对象 | 命中率、淘汰、容量 |
| Transfer Engine | 搬运已定位的数据 | 尾延迟、有效带宽 |

![Mooncake 官方 README 组件图（取阅）](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/moon-parts.png)

*图片来源：Mooncake 官方 README 组件图（取阅），2026-11-23。*

拆分推理工作会带来通信成本。预填充阶段计算长输入，解码阶段反复读历史状态并输出新词元。如果两个阶段放在不同工作池，调度可以各自扩容，但创建的缓存必须按时送到下一池。链路拥堵时，即使两边都有闲置算力，也可能在队列里等数据。

## 命中后，读缓存仍可能比重算慢

决定收益的是命中后节省的预填充时间，与查找、复制和跨节点传输的耗时。短前缀重算很快，走远端缓存反而可能多一次等待。前缀很长并且被反复请求时，复用一次通常更有意义，尤其是工作集能在共享内存层保持一段时间。

![Mooncake 官方 README 传输引擎性能图（取阅）](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/moon-transfer.png)

*图片来源：Mooncake 官方 README 传输引擎性能图（取阅），2026-11-23。*

项目 README 报告，Kimi 在真实工作负载下遵守服务等级目标时能处理多 75% 的请求。这是项目方的工作负载结果。团队要复核相似场景，可以先看自家前缀分布、冷热周期、硬件互联与当前排队情况，再设计可比较的压测。

![Mooncake 文档所列缓存命中率示意图（取阅）](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/moon-hit.png)

*图片来源：Mooncake 文档所列缓存命中率示意图（取阅），2026-11-23。*

公共前缀可以来自重复的规范、常驻知识库或长工具说明，缓存也可能包含隔离域内的客户信息。容量之外，还要和业务定义复用范围、版本变化和保留时间。否则为追求高命中率做出的缓存键安排，可能打破原有的运行边界。

## 社区集成告诉我们什么

仓库跟踪了 vLLM、SGLang 等推理框架的集成，并列出 AMD ATOM 合作案例。这表明团队可以从已有服务端接入，工程初期通常集中在连接器、配置和测量。接入说明所写的模型、设备和缓存方式各有条件，连接器的版本需要与部署版本对齐。

![Mooncake 文档所列 HiCache 与 Store 架构图（取阅）](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/moon-store.png)

*图片来源：Mooncake 文档所列 HiCache 与 Store 架构图（取阅），2026-11-23。*

对于有明显冷热差异的工作集，项目资料还给出通过 SSD 扩容的方案。使用较慢的存储之前，先明确读旧状态能省下多少重新计算，再观察并发读取时尾延迟是否上升。只统计总体命中率，往往看不出最慢的那批用户发生了什么。

![Mooncake 文档所列 SSD 卸载架构图（取阅）](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/moon-ssd.png)

*图片来源：Mooncake 文档所列 SSD 卸载架构图（取阅），2026-11-23。*

## 流量形态决定容量和回退策略

预填充与解码拆分后，容量预测也需要分开。长输入突然增多时，预填充池容易先排队。长输出显著变多时，解码池可能持续占住设备。共享缓存可以改变两边的负载，但团队仍需按实际的输入长度和输出长度分别建模。

缓存怎样失效同样重要。模型升级、前缀模板变化或节点被迁移，都可能让同一个业务在短时间内失去许多命中。测试时可以主动减少可用缓存容量，观察流量是否回到计算路径，以及回退是否挤压原本无需远端缓存的请求。

观测也应给工程师一个能定位问题的链条。一次首词延迟过高，可能是没有命中，也可能是找到对象之后读取排队。把查找耗时和传输耗时分别记录，才能判断是调整调度、扩大热层，还是换网络通道。

共享缓存还涉及排期上的取舍。将所有推理实例一次迁移到新方案，会让指标波动难以归因。可以先接入一类请求和一小组副本，观察命中、重算和异常回退，再扩大到下一类工作负载。每一步都保留同一套对比指标。

## 从一组可重复的请求开始

- 按官方快速入门选择硬件包。常规 Linux CUDA 起步可用 `pip install mooncake-transfer-engine`，再按框架文档配置连接器，并记录模型版本和上下文配置。
- 先用现有服务测基线。准备一组共享长前缀的请求和一组没有公共前缀的请求，保持并发、输入长度与输出上限稳定。
- 用单机或小规模测试启用共享缓存，分别查看首次请求和前缀复用请求。记录首词时间、每秒输出、传输量和长尾。
- 然后扩大到跨节点，增加网络压力测试。观察缓存缺失或组件重启后，服务是否能返回到重算路径。
- 若长前缀和热点负载达到预定收益，再验证连续运行和版本更换时的淘汰策略。

把压测结果提交给同事时，可以并列展示高频前缀和普通请求的分位数。若热门请求变快而其他请求变慢，要进一步比较回退、连接池与后台淘汰带来的资源占用，才能决定是否扩大适用范围。

日榜新增 6 颗星说明这是一次小幅升温。Mooncake 本身的实用性需要从部署形态判断：已有成熟单机推理的小团队可以先保留简洁架构，需要分离算力池或共享大型前缀的团队则有更明确的评估对象。

Mooncake 值得试用的条件，是共享缓存能在自家的流量与网络上持续降低请求时延，同时维持可预期的服务质量。

## 🔗 引用链接

- [GitHub Trending C++ 日榜](https://github.com/%74rending/cpp?since=daily)
- [Mooncake 项目 GitHub README](https://github.com/kvcache-ai/Mooncake)
- [Mooncake 官方文档](https://kvcache-ai.github.io/Mooncake/)
- [Mooncake 性能文档](https://kvcache-ai.github.io/Mooncake/performance/mooncake/index.html)
- [Mooncake 官方快速入门](https://kvcache-ai.github.io/Mooncake/getting_started/quick-start.html)
