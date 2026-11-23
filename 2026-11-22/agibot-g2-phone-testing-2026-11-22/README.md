---
title: 智元 G2 进入蓝思手机测试线，换机型后能复用什么
date: 2026-11-22
weekday: 星期日
slug: agibot-g2-phone-testing-2026-11-22
category: domestic
description: 从自动上下料和设备交互出发，梳理工位接入、换型和复制时可验证的指标。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agibot-g2-phone-testing-2026-11-22.png
batch: evening
track: deep-dive
cover_style: photorealistic editorial news photography
---

# 智元 G2 进入蓝思手机测试线，换机型后能复用什么

![双臂机器人与手机测试夹具的概念画面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agibot-g2-phone-testing-2026-11-22.png)

每日经济新闻 11 月 20 日报道，智元创新与蓝思科技达成手机测试产线合作，数十台精灵 G2 在测试工位执行自动上下料和设备交互。对 3C 制造团队，这次部署值得关注的是，机器人怎样接入已有测试设备，以及换机型后有哪些工作可以复用。

## 要点速览

- **落地在测试段。** 已披露的覆盖范围包括 MMI、ANT、CAM 和 CAMCV 等工位。
- **机器人负责搬运和设备交互。** 项目披露了工位及任务范围，测试设备使用什么判定规则仍有待披露。
- **本体支持同类动作复用。** 工装、来料位置和设备接口仍要逐线核对。
- **部署数量已经公开。** 蓝思现场的单站节拍、人工介入频次和投入回收期尚无公开数据可核算。

![AGIBOT 精灵 G2 · 多台精灵 G2 面向电子测试设备](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agibot-g2-news2.jpg)

*图片来源：IT 之家智元与蓝思合作报道配图，2026-11-21。*

## 手机进了测试线，机器人具体做什么

据每日经济新闻，精灵 G2 进驻的是手机测试产线。数十台设备覆盖若干重复性测试环节，执行自动上下料并与测试设备交互，这给出了可观察的任务边界。

在一个典型测试工位，设备可以把手机送入夹具，等测试工序完成再取出，并把手机送往后续位置。这里描述的是理解工位分工的示例，具体动作顺序和信号接口由现场工艺决定。

| 工段 | 公开披露 | 现场可以记录的指标 |
| --- | --- | --- |
| MMI、ANT | 列入部署范围 | 上下料节拍、定位失败次数 |
| CAM、CAMCV | 列入部署范围 | 等待时间、异常品流转 |
| 设备交互 | 自动上下料及交互 | 握手超时、人工复位频次 |

表中最后一列是本文给出的验收观察项。若机器人因设备超时停在工位，还需知道谁能恢复任务，以及恢复后是否重复测试，避免把单次动作完成率当成整线吞吐量。

![AGIBOT 精灵 G2 · 精灵 G2 的轮式底盘与双臂本体](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agibot-g2-product.jpg)

*图片来源：智元精灵 G2 官方产品页（取阅），2026-11-22。*

![AGIBOT 精灵 G2 · 智元 G2 工业级产品规格展示](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agibot-g2-product2.jpg)

*图片来源：智元精灵 G2 官方产品页（取阅），2026-11-22。*

## 通用本体能复用什么

智元 G2 用户手册列出轮式底盘、高精度力控作业和 SDK 接口。放到测试线，轮式本体提供跨位置部署的硬件基础，力控有助于处理插入、放置等涉及接触的动作。

同一双臂本体迁到另一个工位，动作模板和设备监控逻辑或可沿用。手机尺寸、夹具开合方式和设备信号有变化，仍要重新验证末端动作、取放路径和故障恢复。

机型切换的实际成本也与夹具设计有关。若工位继续使用原来的人工作业夹具，自动取放需要检查可达性和取料容差。若同步更换夹具，验证和停线时间也应计入项目成本。

![AGIBOT 精灵 G2 · 精灵 G2 早期官方演示视频缩略图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agibot-g2-video.jpg)

*图片来源：智元官方发布视频，YouTube，2025-10-22。*

## 从一条线复制到下一条线

![AGIBOT 精灵 G2 · 运行中的 G2 电子产线工位资料图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/agibot-g2-news1.png)

*图片来源：IT 之家智元与蓝思合作报道所附产线资料图，2026-11-21。*

此前，IT 之家报道智元 G2 曾在龙旗科技南昌工厂的平板量产质检工段公开演示。这个案例给出了另一个 3C 场景的先例。蓝思手机工位的运行指标需要用该项目本身的班次记录核对。

如果要评价复制成本，可以从三项可记录的数据入手。

- **生产节拍。** 记录稳定运行时的单站周期，并把等测试设备与等机器人的时间分别记账。
- **故障恢复。** 区分来料偏移、设备超时和测试不合格，记录重试次数与人工介入时间。
- **换型耗时。** 从最后一件旧机型下线计到新机型开始稳定生产，单列重做工装和示教所需时间。

合作公告能确认一批机器人已经进入实际测试工位。下一份有解释力的项目数据，是同一机型、相近负荷下，按班次比较自动化前后产出和异常恢复时间。

蓝思的部署把具身机器人带进手机测试工序，跨线推广的成本和效果还要由节拍、恢复时间与换型数据来验证。

## 来源

- [每日经济新闻 11 月 20 日报道](https://m.nbd.com.cn/articles/2026-11-20/4653970.html)
- [IT 之家 11 月 21 日报道](https://m.ithome.com/html/1030364.htm)
- [智元精灵 G2 用户手册](https://www.agibot.com/DOCS/OS/g2-user-manual)
- [智元精灵 G2 产品页](https://www.agibot.com/products/G2)
- [智元官方发布视频](https://www.youtube.com/watch?v=PPlnLFLJWZ4)
