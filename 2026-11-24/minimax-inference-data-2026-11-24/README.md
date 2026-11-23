---
title: MiniMax 请求涉潜在访问，推理链怎样审计
date: '2026-11-24'
weekday: 星期二
slug: minimax-inference-data-2026-11-24
category: domestic
description: 区分客户通知、供应商披露和平台处置，解释零留存如何核对到请求。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/minimax-inference-data-2026-11-24.png
batch: morning
track: deep-dive
cover_style: data-visual
top_five_rank: 3
---

# MiniMax 请求涉潜在访问，推理链怎样审计

![MiniMax 主题概念图，蓝色和琥珀色多分支数据路径](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/minimax-inference-data-2026-11-24.png)

据 Axios 11 月 23 日报道，OpenRouter 通知部分客户，经由 MiniMax 模型处理的提示与响应可能曾被第三方访问。OpenRouter 的公开公告同时披露了对整个推理供应商网络采取的措施。两份信息分别回答了「用户可能受到什么影响」和「平台接下来怎样验证供应链」。

## 要点速览

- 媒体援引客户通知称，可能涉及 7 月至 10 月间发送的部分请求。
- OpenRouter 公布移除 3 家供应商，并取消 125 个端点的零数据留存资格。
- 平台数字覆盖整轮审查，含预防性调整，无法直接推算 MiniMax 相关用户规模。
- 审计需要追到具体服务商、实际端点、转发路径与留存条款。

![OpenRouter · 供应商和端点处置](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/openrouter-rt-actions.png)

*图片来源：OpenRouter · 推理供应商事件公告 · 供应商和端点处置（网页原文摘录），2026-11-22。*

## 为什么模型名没有列出处理链

模型聚合 API 允许应用通过一个接口调用多家模型。开发者提交的是模型名和请求参数，平台还要选择实际托管推理的一方。由谁执行，往往会随可用性、延迟、价格和用户允许的路由条件变动。

供应链里还有一层区别：展示给用户的供应商可能自己运算，也可能把部分请求转发出去。「选了哪款模型」是产品问题，「哪几家机构接触了数据」是处理链问题，二者要分别记录。

Axios 引述通知报道了可能发生的第三方访问，不能据此识别访问者身份，也没有可据以计算总体泄露规模的公开数据。本文把当事方描述的可能访问与已确认的平台处置分别表述。

![OpenRouter · 供应商公告与用户通知](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/openrouter-rt-disclosure.png)

*图片来源：OpenRouter · 推理供应商事件公告 · 供应商公告与用户通知（网页原文摘录），2026-11-22。*

## 三家与一百二十五的边界

OpenRouter 在 11 月 22 日发布公告，称已移除 3 家供应商、取消 125 个端点的零留存资格并通知可能受影响的客户。公告明确，端点调整包括研究人员标记的对象与平台预防性排除的其他对象。

所以这两个数字回答的是平台治理范围。若要了解某一家供应商、某一产品或某位客户受到的影响，还需要对照个别通知中列出的时间区间、请求和端点。

公告列出若干供应商的说明，并表示有的供应商尚未公开发布事件说明。平台通知与供应商声明讲的是不同责任范围，用户可以先按各自收到的请求记录划定后续排查边界。

| 记录 | 能回答的问题 | 谁可以提供 |
| --- | --- | --- |
| 客户侧请求记录 | 发了什么应用请求 | 应用运营方 |
| 路由与端点日志 | 请求交给谁执行 | 聚合平台与供应商 |
| 留存与删除凭证 | 数据保留多久、如何清理 | 实际处理方 |

![OpenRouter · 后续供应链核验](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/openrouter-rt-next.png)

*图片来源：OpenRouter · 推理供应商事件公告 · 后续供应链核验（网页原文摘录），2026-11-22。*

## 零留存要落到一条请求

零数据留存通常描述服务在完成处理以后怎样对待请求数据。配置项可以限制有资格接单的端点，但配置本身只能说明应用当时要求什么。还需要供应商实际处理记录与平台路由证据，才能核实要求有没有执行。

同一模型带有多个推理端点，其中部分端点可能支持不同的隐私选项。平台撤回某个端点资格时，企业还要判断它是否会造成可用性变化，特别是它原先承担了多少流量以及故障时有无合规候补。

看配置时也要单独核对日志与监控服务。调用方自建的应用日志、网关记录和供应商排障记录分别有不同的存取周期。承诺覆盖至哪一层，最好能在采购条款和技术文档中找到对应佐证。

![OpenRouter · 零留存配置说明](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/openrouter-rt-zdr.png)

*图片来源：OpenRouter · 零数据留存文档（取阅） · 零留存配置说明（网页原文摘录），2026-11-24。*

有些留存条件会把安全处置需要单列。审计时可以问这类例外是否对不同账号一视同仁、谁能启动例外、会留多久。把例外写进架构审查记录，日常排障时就有明确可查的范围。

## 收到通知后先界定受影响面

企业可先建立请求清单，收集应用、时间、模型、实际端点及可获得的事件编号。把客服对话、程序日志与测试数据标注清楚，可以区分个人信息、商业机密和不含敏感信息的内容，减少不必要的扩大排查。

第二步是用应用配置和供应商书面确认核对备用路径。请求在首选端点过载时是否转给其他处理方，需要具体条件与审批约束。涉及不同地区和组织的访问时，合同约定和现实路径都应纳入核查。

第三步是让后续审计可复用。可保存当时生效的策略版本、退出供应商的时间与相关例外，避免几周后只能看见现行设置。业务需要保持可用时，也可以准备符合相同数据要求的备用端点。

采购环节可以把月度报告分成流量分布、路由变化和数据事件三部分。即使模型名称始终一样，参与处理的服务商也可能改变，账单上的累计调用量无法揭示这层变化。

对要求强审计的部门，测试环境也值得单列。开发阶段常会把真实故障内容复制到测试工具中，沿用生产研发账号容易使上下游接触范围变宽。使用经脱敏的任务材料，可以保留调试价值并缩小后续排查范围。

Novita 公开确认部分请求转到 MiniMax 官方接口，并称已暂停受影响模型。它披露的可能受影响日期覆盖 5 月 28 日至 11 月 11 日，适用于该供应商自己的路径。这个区间与媒体转述的其他通知各有范围。

![OpenRouter · Novita 对客户的说明](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/openrouter-rt-customer.png)

*图片来源：Novita · MiniMax 请求隐私通知 · Novita 对客户的说明（网页原文摘录），2026-11-22。*

## 平台要拿什么恢复信任

OpenRouter 说将持续检测供应商，验证网络路径，并在恢复零留存资格前要求适当的保证与核验。此后可以观察恢复资格的标准会不会公开，用户能否查询具体请求被转给了谁，以及行使退出权需要多久生效。

对供应商而言，能证实的承诺比统一标签更具体：可列明数据处理位置、接触权限、删除周期、哪些第三方可以接收请求，以及发生意外时谁负责先行告知客户。

对开发者而言，路由切换仍有工程价值。对价格、吞吐和延迟敏感的通用任务可以用更宽的候选范围，严格受限的数据用能够提供端到端证据的路径，两类任务可各自监控失败率和成本。

由这则报道引出的采购问题很具体：每次推理究竟经过谁的系统，发生变更时由谁提供能对上这条请求的证据。

## 🔗 引用链接

- [Axios · OpenRouter 客户通知报道](https://www.axios.com/2026/11/23/openrouter-minimax-china-customer-data)
- [OpenRouter · 推理供应商事件公告](https://openrouter.ai/blog/announcements/data-processing-incidents-on-inference-providers/)
- [Novita · MiniMax 请求隐私通知](https://novita.ai/blog/minimax-requests-privacy-notice/)
- [OpenRouter · 零数据留存文档（取阅）](https://openrouter.ai/docs/guides/features/zdr)
