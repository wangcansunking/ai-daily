---
title: "Genie 4 开放 API，世界模型走进开发流程"
date: 2026-11-20
weekday: 星期五
slug: genie-4-interactions-api-2026-11-20
category: top-news
description: "谷歌把连续场景生成接入 Interactions API，交互稳定性成为原型验收项。"
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/20/genie-4-interactions-api-2026-11-20.png
batch: morning
---

# Genie 4 开放 API，世界模型走进开发流程

![雾雨穿过虚拟峡谷的世界模型概念图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/20/genie-4-interactions-api-2026-11-20.png)

11 月 19 日，谷歌将 Genie 4 开放到 Gemini Interactions API 和 Google AI Studio。开发者可先用文字、参考图或两者设定起点，再以键盘移动、用文字改变当前世界。接口把生成和控制纳入同一体验，原型能在使用者行动之后继续变化。

## 订阅体验与开发接口先后上线

谷歌先在 11 月 18 日向符合条件的 AI Ultra 订阅用户开放 Project Genie 的 Genie 4，再公布开发入口。消费产品让用户在浏览器试世界生成。新的公告面向试图把同类交互接进应用的开发团队，AI Studio 还提供交互画布和入门应用。

开发者可以把参考图当作初始条件。例如给一张有路灯和入口的街景图，让体验者先前进再转弯，最后要求下雨。生成系统要处理构图连续性，也要在新画面里响应指令。地标是否保持在合理位置，是这类原型最直观的检查项之一。

## 720p、24 帧对应什么体验

按谷歌公告，连续世界生成输出为 720p、每秒 24 帧，并带同步原生音频。声音可以来自环境，也可以是事件音效或角色对话。拿一段短流程验收时，团队可以记下发出控制输入的时间、画面首次响应的时间，以及声画变化是否对应。

每秒帧数描述输出规格。交互端到端延迟还经过输入、请求、生成和播放。相同分辨率下，响应如果落后一拍，操作者可能连续补发指令，把世界推到意料之外的位置。应同时记录控制延迟和生成帧率。

## 会话一致性要用折返来测

适合试验的场景可以限定在一条路和两个地标。第一次经过时，记录路牌、入口和光源的位置。接着改变天气，为同一路段增加可识别的视觉变化。折返后对照位置与状态，观察变化是否只覆盖了指定要素。

再重复几次同样的脚本，记录同一会话中物体是否稳定、不同会话中结果是否足以复现。对需要复盘的训练原型，这两个问题会影响能否比较不同参与者的操作。谷歌表示系统用 Gemini 推理维持世界状态，具体项目仍需要针对自己的路线和场景验证。

## 上线前核对计费与内容标识

谷歌提示开发者通过 API 文档查看计费细节。预算可以按有效体验时长计算，同时记录中途重试和废弃会话。官方也说明 Genie 4 输出带有不可感知的 SynthID 水印。演示素材和结果归档流程可据此保留生成来源信息。

## 引用链接

- [Google Genie 4 API 公告](https://blog.google/innovation-and-ai/technology/developers-tools/genie-4-in-gemini-api/)
- [Google Project Genie 公告](https://blog.google/innovation-and-ai/models-and-research/google-deepmind/genie-4-now-available-project-genie/)
