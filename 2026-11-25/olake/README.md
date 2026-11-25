---
title: OLake Go 登 Go 榜第 1：把库表变更追进 Iceberg
date: '2026-11-25'
weekday: 星期三
category: github-trending
slug: olake
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/olake.png
cover_style: data-visual
description: 用订单表讲清 OLake Go 的全量回填、CDC 和 Iceberg 提交，附本地入门路径。
tags:
- GitHub Trending
- OLake Go
- Apache Iceberg
- CDC
repos:
- datazip-inc/olake-go
batch: noon
track: deep-dive
---

# OLake Go 登 Go 榜第 1：把库表变更追进 Iceberg

周一用订单库导了一份报表，周二再查，退款的订单还在，刚做完的促销却少了一截。把业务库同步进数据湖，第一遍拷贝相对容易，难的是拷贝途中仍在发生的插入、更新和删除。

## 要点速览

- 北京时间 11 月 25 日 09:47，OLake Go 位列 GitHub Trending Go 日榜 **第 1**，累计 **2,329 星**，榜单显示当日新增 **38 星**。
- 项目支持 Postgres、MySQL、MongoDB 等数据库，以及 Kafka、S3 等源，目的端包括 Apache Iceberg 和 Parquet。同步模式要看具体连接器和目标。
- 对支持 CDC 的源，OLake Go 将历史表分块回填，并从预先记下的日志位置接续增量。Iceberg 路径可用原子提交处理数据文件、删除文件和进度标记。
- 可以从 Docker Compose 的 OLake UI 试起，拿一张含新增、修改、删除的测试订单表跑完整个流程。

## 数据搬完了，订单还在变

常见的第一步是跑全量快照。从订单表读历史数据，按块分给多个工作单元，最后把数据写进选定的目的端。OLake Go 的架构文档把分块状态描述为待处理、处理中和已提交。任务中断后，进度记录能帮助后续接着处理。

![OLake Go 从数据库、Kafka 和 S3 经同步引擎写入 Iceberg 或 Parquet 的架构图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/olake-flow.png)

*图片来源：OLake 官方架构详解，2026-07-22。*

全量搬运期间，业务库照常收单。项目的 CDC 接棒流程会先记住日志位置，再开始回填。回填结束后从那个位置重放事件，于是搬运过程中发生的修改也有机会被捕获。这里的 CDC 指变更数据捕获，可从支持的源读取相应的变化记录。

想象订单 104 在快照还没扫到它所在的分块时被插入。全量读取可能已经带走这一行，稍后日志重放又遇见它。官方示例展示了 OLake 如何在重叠窗口把这类插入按 upsert 处理，让 Iceberg 目的表只保留一行。

![从预置日志位置到全量回填、重叠窗口及持续 CDC 的时序图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/olake-cdc.png)

*图片来源：OLake 官方架构详解，2026-07-22。*

## 最棘手的瞬间，发生在文件写完之后

同步到 Iceberg 时，写出一批数据文件只是完成了一半。若进程在提交表之后、保存本地进度之前退出，重启时就需要知道那批数据究竟有没有进入表。

按项目的架构说明，Iceberg 提交可以把数据文件、删除文件和用于识别已提交批次的 `olake_2pc` 表属性一起提交。恢复时读取这个标记，能跳过已经提交的工作。这个机制对应文档描述的 Iceberg 路径，换目的端时要看该连接器的说明。

![Iceberg 事务同时提交数据文件、删除文件和 OLake 进度标记的示意图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/olake-commit.png)

*图片来源：OLake 官方架构详解，2026-07-22。*

Go 进程负责读源和组织同步。架构文档还写到，Iceberg 写入会启动共享 JVM 侧进程，两个运行时经本地 gRPC 通讯。这个分工会影响容器内存和排障方式，尤其是读取正常、目标侧提交却持续重试时。

| 你要解决的事 | OLake 的对应环节 | 起步时的做法 |
| --- | --- | --- |
| 把历史订单搬进湖仓 | 分块并行回填 | 先选一张表，记录主键和数量 |
| 跟上回填期间的变化 | CDC 接棒及重叠去重 | 回填时写入一条新订单 |
| 让下游查到最新状态 | Iceberg 提交及删除记录 | 从查询引擎查看更新和删除结果 |

## 从一张订单表跑出第一条增量

1. 准备 Docker、Docker Compose、至少 4 GB 可给 Docker 使用的内存，以及空闲的 8000 端口。按官方 Quickstart 下载 Compose 文件，阅读服务和端口配置，随后用 `docker compose -f docker-compose-v1.yml up -d` 启动。
2. 打开本机的 `http://localhost:8000`，按 Quickstart 登录。保持服务仅在本机或隔离测试网络内可访问。源 Postgres 准备用于练手的库和有主键的 `orders` 表，CDC 按连接器文档配置逻辑复制、publication 和使用 pgoutput 的 replication slot。
3. 从 UI 新建任务，选 Postgres 作为源，录入可从容器访问的连接地址。配置测试 Iceberg catalog 和存储，选中订单表，再启用该连接器支持的 CDC 模式。
4. 从十来条历史订单起步，然后新增一条、把一条改成退款状态、删掉一条。等任务完成一次提交，在 Iceberg 查询端按主键检索这三条记录，顺便观察 UI 中任务日志和延迟。

![OLake UI 的任务页及数据源与目的端导航](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/olake-ui.png)

*图片来源：OLake 官方 Quickstart（更新），2026-10-06。*

这个练习把两个容易混在一起的问题拆开了。表里有没有复制进来，靠小样本就能看清。业务能等多久，要用真实变化速率继续跑一段时间，观察源端日志保留、目标提交频率和积压恢复。

## 热榜第一，适合谁先上手？

已有 Iceberg 查询环境、正把业务表送给 BI 或模型特征管道的团队最容易接上。按批跑日报的团队则可以先从单表或低频刷新开始，写清楚下游需要的时效，再决定是否开 CDC。

持续写入会涉及小文件、快照保留和计算资源。OLake 把表维护能力放在独立的 OLake Fusion 项目里。把同步作业接通后，再为查询端设定维护周期，数据才会持续好用。

**一句话总结：** 先把一张正在变化的订单表送进 Iceberg，亲眼看见新增、更新和删除接上，湖仓同步才算真正跑起来。

## 来源

- [GitHub Trending Go 日榜](https://github.com/trending/go?since=daily)
- [OLake Go 官方仓库](https://github.com/datazip-inc/olake-go)
- [OLake Go 架构详解](https://olake.io/blog/olake-architecture-deep-dive/)
- [OLake Go 官方 Quickstart](https://olake.io/docs/getting-started/quickstart/)
- [OLake PostgreSQL 本地 CDC 快速配置](https://olake.io/docs/getting-started/postgres/)
