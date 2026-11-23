---
title: 特朗普提海外机房税 · Claude 参与的黎曼证明获核验 | AI 日报 | 2026-11-22
date: 2026-11-22
weekday: 星期日
slug: 2026-11-22
category: daily-report-newsletter
description: 跟进数据中心、电网许可、黎曼猜想核验及开源榜单。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/22.png
batch: morning
track: newsletter
cover_style: abstract data visualization
---

# 特朗普提海外机房税 · Claude 参与的黎曼证明获核验 | AI 日报 | 2026-11-22

![抽象光带与数据曲线](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/22.png)

美国数据中心投资同时遇到两道政策问题：海外建站可能面临新增成本，国内大负荷项目的电网升级费用也在国会讨论中。另一边，Lean 与 Mathlib 社区公布了对黎曼猜想形式化证明的核验结果，开源与科研工具则继续提供可直接阅读的技术材料。

## 要点速览

- 特朗普提出对美国企业在海外建 AI 数据中心加税，目前公开信息主要来自记者会。
- 美国参议院通过包含数据中心电力成本条款的许可改革案，文本仍待众议院处理。
- Lean 与 Mathlib 社区确认 Alpöge 提交的黎曼猜想形式化命题与证明正确。
- 港科大（广州）与 LIGHTSPEED 研究人员用可复用模块为轻量决策模型提供上下文。

## 头条｜数据中心选址遇到两笔账

特朗普当地时间 11 月 20 日在白宫记者会上提出，要对美国企业在境外建设 AI 数据中心施加重税。公开报道尚无税率、适用范围和生效日期，项目预算无法据此计算应缴金额。

![ABC News 刊载的特朗普白宫记者会现场](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/abc-0.jpg)

*图片来源：美联社记者会现场照（ABC News 刊载），2026-11-20。*

国内是另外一条线。美国参议院 11 月 19 日通过经修订的 H.R. 2347，表决为 62 票赞成、33 票反对、5 人未投票。白宫次日敦促众议院继续处理。议案所涉电力成本和电网升级，与记者会提出的境外建站措施是两项不同的事项。

![谷歌在芬兰（Finland）的站点位置图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/finland-map.jpg)

*图片来源：Google Data Centers 瓦拉站点页（取阅），2026-11-22。*

## 要闻｜美国参议院讨论怎样组织 AI 监督

科罗拉多州参议员希肯卢珀 11 月 19 日向两位民主党领袖候选人致信，主张考虑成立 AI 特别委员会。他提出把安全、问责与生活成本放在同一议程上。公开信给出了政治议程，实际职权还要看参议院将来表决的文本。

![美国参议院（Senate）S.Res. 918 决议标题与转介](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/senate-intro.png)

*图片来源：美国政府出版局 S.Res. 918 文本第 1 页，2026-09-29。*

## 研究与工具｜机器核验有了公开记录

Lean 与 Mathlib 社区 11 月 21 日发布联合声明。核验人员将 Mathlib 的黎曼猜想命题单独写入文件，用两种工具核对命题，再用数个检查器检验导出的证明。Alpöge 于 11 月 20 日发布证明，社区次日公开独立检查结果。

![Lean 与 Mathlib 联合声明开头](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/lean-statement.png)

*图片来源：Lean 与 Mathlib 社区联合声明原文摘录，2026-11-21。*

Google 与约翰斯·霍普金斯大学作者的 Pro² Agent 预印本则讨论工程试验如何分配。论文在完整的 75 项 MLE-Bench 上报告，24 小时获牌率为 70.7%，48 小时为 76.0%。时限不同，适合放在各自预算下解读。

## 🇨🇳 国内 AI 观察

《人民日报》11 月 21 日梳理国产开放权重模型，提到 Kimi K3.1 Pro 和腾讯混元 Hy4。月之暗面模型卡提供可下载权重，计划自部署的团队仍需逐项核对硬件、版本与许可证。

港科大（广州）与 LIGHTSPEED 作者在 JevRSI 预印本中，把大模型的分析沉淀为步骤、状态记忆和预测模块。论文中，同由 Qwen 开发的模块给不同执行模型带来了不同结果，迁移测试需要保留失败项。

![JevRSI JevRSI 的快速执行和异步开发框架](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/jevRSI-framework.png)

*图片来源：JevRSI 预印本图 1，2026-11-20。*

## 📦 GitHub Trending

数据来自北京时间 11 月 22 日早班抓取的 GitHub Trending 日榜。日增采用页面标作 stars today 的数字。

| 仓库 | 总星数 | 日增 |
| --- | ---: | ---: |
| toeverything/AFFiNE | 76,226 | +577 |
| XiaoYouChR/Ghost-Downloader-3 | 14,386 | +774 |

AFFiNE 把文档与白板放在同一工作区。Ghost Downloader 3 是桌面下载工具。看榜单时，短期星标反映开发者关注，可以从各自仓库的文档了解实际使用场景。

![AFFiNE 工作区产品演示](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/affine-demo.png)

*图片来源：AFFiNE 官方仓库 README 演示截帧（取阅），2026-11-22。*

![Ghost Downloader 3 仓库的产品社交图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/22/ghost.jpg)

*图片来源：Ghost Downloader 3 官方仓库社交预览（取阅），2026-11-22。*

开源工具进入团队流程前，可以先确认数据保存位置与可导出的格式，再用一份非敏感样例验证协作体验。

## 一句话总结

今天最明确的进展来自可检查的材料：国会表决、公开的证明核验，以及注明实验预算的研究结果。

## 🔗 引用链接

- [美联社白宫记者会报道](https://apnews.com/article/f1ac03b73d13445400eeed8631d9d879)
- [ABC News 刊载的美联社报道与现场照片](https://abcnews.com/Technology/wireStory/trump-plans-penalize-us-tech-companies-build-ai-138323831)
- [美国参议院 H.R. 2347 唱名表决](https://www.senate.gov/legislative/LIS/roll_call_votes/vote1192/vote_119_2_00270.htm)
- [美联社国内许可改革案报道](https://apnews.com/article/b41c2017e4a9fe2e6de92ec77a4cdd0a)
- [白宫关于许可改革立法进程的说明](https://www.whitehouse.gov/releases/2026/11/what-they-are-saying-send-the-bipartisan-american-affordability-and-jobs-act-to-president-trumps-desk/)
- [希肯卢珀办公室公告](https://www.hickenlooper.senate.gov/press_releases/hickenlooper-presses-senate-leadership-candidates-to-outline-ai-safety-affordability-and-accountability-agenda/)
- [美国政府出版局 S.Res. 918 原文](https://www.govinfo.gov/content/pkg/BILLS-119sres918is/pdf/BILLS-119sres918is.pdf)
- [Google Data Centers 瓦拉站点](https://www.datacenters.google/locations/vaala/)
- [Lean 与 Mathlib 社区联合声明](https://leanprover-community.github.io/blog/posts/riemann-hypothesis/)
- [Pro² Agent 预印本](https://arxiv.org/abs/2611.25357)
- [JevRSI 预印本](https://arxiv.org/abs/2611.22432)
- [JevRSI 论文原文及图片](https://arxiv.org/pdf/2611.22432)
- [《人民日报》11 月 21 日第 5 版](https://paper.people.com.cn/rmrb/pc/attachement/202611/21/958fca0a-325d-48ae-b5dd-8ed81c247341.pdf)
- [月之暗面 Kimi K3.1 Pro 官方模型卡](https://huggingface.co/moonshotai/Kimi-K3.1-Pro)
- [GitHub Trending 日榜](https://github.com/trending?since=daily)
- [AFFiNE 官方仓库](https://github.com/toeverything/AFFiNE)
- [Ghost Downloader 3 官方仓库](https://github.com/XiaoYouChR/Ghost-Downloader-3)
