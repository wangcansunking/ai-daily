---
title: 凤凰城算力账 · OpenAI 语音待机 · 长沙智算 | AI 日报 | 2026-11-21
date: '2026-11-21'
weekday: 星期六
slug: '2026-11-21'
category: daily-report-newsletter
description: 产业、国内智算、语音 API、推理路由及开源榜单。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/21.png
batch: morning
track: newsletter
cover_style: magazine photo collage
---

# 凤凰城算力账 · OpenAI 语音待机 · 长沙智算 | AI 日报 | 2026-11-21

![凤凰城、语音与工业算力的编辑拼贴](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/11/21.png)

长沙世界计算大会公布了智算与推理需求展望。美国亚利桑那州凤凰城在讨论下一批数据中心怎样接电，OpenAI 和 Perplexity 则把竞争延伸到语音待机和模型服务的可靠性。

## 要点速览

- **凤凰城**市长称，若所有申报项目兑现，数据中心所需电力或占凤凰城都会区需求约三分之二。
- **长沙**发布 CLink 系列标准，研究机构给出 2030 年推理需求展望。
- **GPT-Live-1.1** 开放 API，WebRTC 待机语音费率为每分钟 0.02 美元。
- **Perplexity** 把同一模型的不同部署接入统一 API，并预览按任务选模型。

## 头条｜凤凰城给机房算电水账

![Axios 刊载的凤凰城市长凯特·加列戈数据中心主题配图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/axios-gallego.jpg)

*图片来源：Axios 摄影拼贴（Aïda Amer，照片 Pablo Barrera／Anadolu via Getty Images），2026-11-20。*

Axios 11 月 20 日刊出的采访中，凤凰城市长凯特·加列戈说，她希望争取常设岗位，并让社区能查到项目用水用电。她提到的三分之二是全部申报项目兑现时的情景。现行许可制度源于 2025 年的分区条例更新。完整讨论见今日《凤凰城的数据中心，先过电网和选址两关》。

## 要闻｜语音待机与推理路由

![GPT-Live-1.1 API 公告及待机费率](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/gpt-live-api-announcement.png)

*图片来源：OpenAI 开发者社区 API 公告原文摘录，2026-11-20。*

OpenAI 宣布两代 GPT-Live 均支持 WebRTC 待机，闲置阈值可设为 5 至 60 秒。配置了待机的会话在推理暂停时保持连接，用户再开口时恢复。后台模型和工具另计。完整讨论见今日《GPT-Live-1.1 开放 API，一通电话如何按待机计费》。

![Perplexity API 控制台的模型用量与支出](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/perplexity-api-console.png)

*图片来源：Perplexity 官方博客 API Console 原图（取阅），2026-11-21。*

Perplexity Orchestrator 可以在同一模型的可用部署之间重试。自动选模型属于另一项可选预览功能，平台按任务描述匹配预设路线。完整讨论见今日《Perplexity 推理路由，如何切换模型与供应商》。

## 🇨🇳 国内 AI 观察｜长沙发布 CLink

![新华网（Xinhua）拍摄的 2026 世界计算大会开幕式现场](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/xinhua-world-computing-opening.jpg)

*图片来源：新华社记者陈思汗摄，新华网，2026-11-20。*

11 月 20 日开幕的世界计算大会发布 CLink 系列标准。新华社报道，中国电子信息产业发展研究院人士预计，到 2030 年，智算占总算力将超九成，推理需求占智算结构或达 85%。完整讨论见今日《长沙世界计算大会上，推理需求如何走到台前》。

![华为展出的 Atlas 950 SuperPoD 液冷超节点](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/changsha-atlas-superpod.jpg)

*图片来源：长沙晚报全媒体记者余劭劼摄，2026-11-20。*

## 📦 GitHub Trending｜看榜单原始数字

以下是北京时间早班抓取的 GitHub Trending 日榜快照。日增是页面标示的 stars today，项目说明来自各自仓库。

| 仓库 | 当前星标 | 榜单日增 |
| --- | ---: | ---: |
| Tracer-Cloud/opensre | 13,562 | +465 |
| zhouxiaoka/autoclip | 12,122 | +983 |

![GitHub Trending 日榜的 OpenSRE 与 AutoClip 条目](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/trending.png)

*图片来源：GitHub Trending 日榜条目原文摘录（取阅），2026-11-21。*

OpenSRE 面向 CI/CD 可靠性，AutoClip 面向视频高光识别、裁切和字幕。榜单反映近期关注，选型时可以用一次失败流水线或一段自有视频检验项目能否完成目标。

## AI Coding｜把失败也放进测试

试用推理路由时，同一批请求可以分别覆盖固定模型和按任务分流，记录实际模型、尾部延迟和输出是否达标。在排查构建失败的场景里，也可先用历史记录做回放，确认工具能给出可复现的定位。

## 快讯与后续

以下三条可作为跟进大会与平台后续公告的起点。

- 湖南省政府披露，世界计算大会安排了 21 场活动，开幕式启动 RCAP 亚太机器人世界杯长沙国际邀请赛。
- 凤凰城已在 2025 年设立数据中心特别许可程序，审查涉及电网、消防、应急通行和噪声。
- Perplexity 的公开对比只覆盖 10 月 4 日至 11 月 2 日。评估其他业务时，可重新用自己的请求测一遍成功率和响应时间。

## 编辑说

今早最值得跟踪的是承诺如何兑现：机房有没有已落实的并网容量，语音模型真的待机了多久，多模型入口能否说清每次请求由谁完成。

## 🔗 引用链接

- [Axios｜与凤凰城市长谈数据中心](https://www.axios.com/2026/11/20/phoenix-mayor-data-centers-ai-boom)
- [凤凰城市议会｜7 月 2 日会议纪要](https://apps-secure.phoenix.gov/PublicRecordsSearch/Home/RenderPDF/?id=vYDgzBsu7TFueXyeIzlrUbtE%2FPnL9WtMNOJl+Oc8jYE%3D)
- [OpenAI 开发者社区｜GPT-Live-1.1 API 公告](https://community.openai.com/t/gpt-live-1-1-has-arrived-in-the-api/1418537)
- [Perplexity 官方社区｜Orchestrator API 公告](https://community.perplexity.ai/t/introducing-the-perplexity-orchestrator-api/6688)
- [Perplexity 官方博客｜可靠推理与自动路由](https://www.perplexity.ai/tr/hub/blog/orchestrator-one-api-for-reliable-inference)
- [湖南省人民政府｜2026 世界计算大会开幕](https://hunan.gov.cn/hnszf/szf/zfld/cj1/cjhdyjh/202611/t20261120_34107787.html)
- [新华网｜智算与推理需求展望](https://www3.xinhuanet.com/tech/20261120/8bf8f8fdba4c45b2b63720f6a71b9119/c.html)
- [长沙晚报｜世界计算大会现场组图](https://www.icswb.com/h/160/20261120/1004952.html)
- [GitHub｜Trending 日榜](https://github.com/trending?since=daily)
- [GitHub｜Tracer-Cloud/opensre](https://github.com/Tracer-Cloud/opensre)
- [GitHub｜zhouxiaoka/autoclip](https://github.com/zhouxiaoka/autoclip)
