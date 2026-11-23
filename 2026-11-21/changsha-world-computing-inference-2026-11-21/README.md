---
title: 长沙世界计算大会上，推理需求如何走到台前
date: '2026-11-21'
weekday: 星期六
slug: changsha-world-computing-inference-2026-11-21
category: domestic
description: 从推理需求预测、液冷设备、国产全栈和互连标准看智算交付。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/changsha-world-computing-inference-2026-11-21.png
batch: morning
track: deep-dive
cover_style: cinematic sci-fi concept
---

# 长沙世界计算大会上，推理需求如何走到台前

![机柜旁的工业机械臂概念画面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/changsha-world-computing-inference-2026-11-21.png)

2026 世界计算大会于 11 月 20 日在长沙开幕，会上发布 CLink 系列标准，并启动一项机器人赛事。工信领域研究机构在同一场会上给出了到 2030 年的智算与推理需求展望，场馆里则展示了液冷超节点和国产软硬件。

## 要点速览

- 湖南省政府披露，大会共设 21 场活动，包括开幕式暨主旨论坛、各类论坛、博览会及同期赛事。
- 据新华社，中国电子信息产业发展研究院人士预计，到 2030 年智算占总算力将超九成，推理需求占智能算力结构或达 85%。
- 展会现场可看到液冷超节点、国产软硬件全栈展示和机器人应用。

![新华网（Xinhua）拍摄的 2026 世界计算大会开幕式现场](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/xinhua-world-computing-opening.jpg)

*图片来源：新华社记者陈思汗摄，新华网，2026-11-20。*

## 两个百分比各有分母

大会上的第一个比例，是智能算力在总算力中的占比。第二个比例说的是推理需求在智能算力结构中的占比。它们来自同一次展望，含义不同，均指向 2030 年。新华社同时报道，研究院人士称国内智能算力规模届时有望冲击 10000 EFLOPS。

| 大会披露的口径 | 指向 2030 年 |
| --- | --- |
| 智能算力占总算力 | 超过 90% |
| 推理需求占智能算力结构 | 85% |
| 智能算力规模 | 有望冲击 10000 EFLOPS |

这些数字描述的是预期。采购一套推理系统还得回答一组不同的问题，比如每天有多少短请求、峰值排队时间有多长、长上下文占几成。只按峰值浮点运算能力做采购，容易漏掉内存和网络的瓶颈。

![华为展出的 Atlas 950 SuperPoD 液冷超节点](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/changsha-atlas-superpod.jpg)

*图片来源：长沙晚报全媒体记者余劭劼摄，2026-11-20。*

## 从展台问到交付条件

据长沙晚报，华为在博览会上展出 Atlas 950 SuperPoD 液冷超节点。液冷展示把算力故事拉到了机房现场：集群要跑起来，除了卡和互连，还要把供配电、散热、安装及后续运维一起安排。

机房评估可以从设备和设施的交界处开始。问清单柜功耗准备到什么水平，原机房能不能提供对应的供电和回水条件，测试条件与上架条件是否一致。展品出现说明厂商在展示方案，采购和部署仍要看项目文件。

![中国电子（China Electronics）国产软硬件全栈展位](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/changsha-china-electronics.jpg)

*图片来源：长沙晚报全媒体记者余劭劼摄，2026-11-20。*

中国电子在现场合展了飞腾芯片、麒麟操作系统、达梦数据库和长城整机。四层一起看，迁移工作就会落到编译依赖、驱动、数据库适配和实际业务验证这些具体步骤。

![蓝思科技（Lens Technology）展出的机器人](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/changsha-lens-technology-robot.jpg)

*图片来源：长沙晚报全媒体记者余劭劼摄，2026-11-20。*

## 互连标准要靠现场验证

省政府披露，开幕式发布了 CLink 系列标准。对于要组合不同来源设备的团队，后续可以关注公开规范、实现情况和互操作测试结果。具体到项目，拿已有业务负载验证，能更早发现兼容与运维问题。

从大型模型服务转到机器人，验收口径也会变。云端可以先测并发和排队时间，现场机器人还涉及联网状况、突发断连和人机安全边界。大会同期赛事和展品有助于观察这些应用，但持续运行能力要靠实际工况验证。

![新华网（Xinhua）拍摄的参展机器人运动演示](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/xinhua-robot-demonstration.jpg)

*图片来源：新华社记者陈思汗摄，新华网，2026-11-20。*

## 哪些数字可以跟踪

新华社转述的十大趋势还提到边缘与终端算力增长，以及全国一体化算力网建设。需求在哪里产生、算力部署在哪里，两者之间的延迟和数据传输成本，会改变每个现场的选择。

比较各地项目时，可以逐项记录已经交付的机房容量、能运行的真实业务、可公开核对的服务时延。三个数字分开写，比把各类算力建设公告加在一起更能说明使用进度。

![新华网（Xinhua）镜头下了解展出设备的观众](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/xinhua-computing-exhibition.jpg)

*图片来源：新华社记者陈思汗摄，新华网，2026-11-20。*

同一套服务也可以用两种负载交叉验证。第一种是工作日的交互请求，关心首字何时回来。第二种是夜间批处理，关心吞吐和单位任务的资源占用。两种结果分开记，才能判断容量短缺究竟出现在高峰，还是整天都不够用。

这也解释了为什么同时看现场设备与标准发布。设备展示能说明厂商在解决哪些工程问题，测试规范则给不同团队一套共同的验收语言。两条线都走到实际业务，才能看清这批投入能支撑什么。

从长沙这场会出发，智算的实际进展要看标准能否互通、机房能否交付、推理负载能否持续跑起来。

## 🔗 引用链接

- [湖南省人民政府｜2026 世界计算大会开幕](https://hunan.gov.cn/hnszf/szf/zfld/cj1/cjhdyjh/202611/t20261120_34107787.html)
- [新华网｜智算与推理需求展望](https://www3.xinhuanet.com/tech/20261120/8bf8f8fdba4c45b2b63720f6a71b9119/c.html)
- [长沙晚报｜世界计算大会现场组图](https://www.icswb.com/h/160/20261120/1004952.html)
