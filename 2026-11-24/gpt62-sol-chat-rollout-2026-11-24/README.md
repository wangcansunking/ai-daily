---
title: GPT-6.2 进 Chat，看图自测涨 24.3 个百分点
date: '2026-11-24'
weekday: 星期二
slug: gpt62-sol-chat-rollout-2026-11-24
category: big-tech-models
description: GPT-6.2 Sol 开始进入普通 Chat，OpenAI 公布看图、复杂指令与网页查找四组新旧对照。
batch: evening
track: deep-dive
cover_style: magazine-collage
top_five_rank: 1
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/gpt62-sol-chat-rollout-2026-11-24.png
---

# GPT-6.2 进 Chat，看图自测涨 24.3 个百分点

![GPT-6.2 进 Chat，看图自测涨 24.3 个百分点](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/gpt62-sol-chat-rollout-2026-11-24.png)

把一张字很小的展览海报丢进 ChatGPT，问它周六最晚几点入场，这回换了一个模型读图。北京时间 11 月 24 日凌晨，OpenAI 宣布把 GPT-6.2 Sol 的聊天特调版推向普通 Chat。官方自测的图像理解成绩从 47.4% 跳到了 71.7%。

## 要点速览

- 新版面向 Plus、Pro、Business 和 Enterprise 分批推送，企业用户的可用性取决于管理员设置。
- OpenAI 内测中，看图提高 24.3 个百分点，特别困难提示的指令遵循率提高 15.6 个百分点。
- 最高一档推理设置下，内部网页查找评测从 54.4% 升到 64.5%。
- 11 月 17 日起已有 Work、Codex 和 API 用上 GPT-6.2 系列，这次轮到普通聊天入口。

## 模型发布一周后，普通聊天框接棒

OpenAI 这次把为日常对话调校的 GPT-6.2 Sol 放进 Chat。上一次 GPT-6.2 Astra 和 Sol 亮相，主场是 Work、Codex 和 API，普通聊天页当时保留了原有模型选项。两次发布相隔约一周。

这个入口差别很实际。同一个人可能用 Codex 改代码，用普通 Chat 比产品、读截图和查资料。前一次发布改变的是前一类工作，这一次最先有感觉的，可能是每天习惯把截图往聊天框里一贴的人。

如果你是付费用户，推送按账号分批到达。用公司账号的读者还要算上管理员设置这一层。OpenAI 公布的本轮名单包括四个付费档位，免费档未列入。Work 和 Codex 里的版本也维持这一轮推送前的安排。

对于用 API 搭应用的人，这两个日期尤其容易混。11 月 17 日的更新列出 `gpt-6.2-astra` 和 `gpt-6.2-sol` 两个模型名。这次公告说的是同一家产品的 Chat 页面，团队如果要更换自己应用里的模型，仍需在 API 请求里指定相应的模型 ID。

## 71.7% 的看图成绩，落到一张照片上是什么感觉？

看图最容易露馅的地方，往往藏在边角。比如一张改版稿上有三颗按钮，你关心的是底部那颗按钮到底写着「保存」还是「发布」。图像理解成绩从 47.4% 到 71.7%，说的是 OpenAI 在 Chat 内部评测里看到的变化。

![Chat 图像理解成绩对比](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/gpt62-chat-vision.png)

*图片来源：OpenAI《ChatGPT 发布日志》图像理解评测，2026-11-23。*

把这个变化带回日常需求，可以拿一张时刻表试试。先问它周末末班车是几点，再让它指出答案对应的行、列和脚注。回答给出具体位置时，你扫一眼就能确认它有没有把周五和周六混到一起。

照片反光、屏幕上的字缩得很小，或者表头被裁掉，都会让图片缺少信息。这时可以同时给一张全景和一张局部，把版面结构与小字分开交代。原图给范围，局部给细节，两张图比继续在模糊照片上追问更省时间。

## 同时记住四个条件，最后一个也别丢

另一个明显的跳升出现在指令遵循上。OpenAI 用特别困难的提示对比这两代模型，GPT-6.2 的通过率是 78.1%，GPT-6 是 62.5%。这种提升在多条件、有例外的任务里更值得关注。

![Chat 复杂指令遵循率对比](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/gpt62-chat-instructions.png)

*图片来源：OpenAI《ChatGPT 发布日志》复杂指令评测，2026-11-23。*

以选酒店为例，先卡总价，再卡步行距离，还要能免费取消，最后把住客提到隔音差的房源排后面。这个任务需要模型同时记住硬条件和偏好。最有用的输出通常是三家以内的短名单，每家一行说明取舍。

| OpenAI 的 Chat 内部评测 | GPT-6 | GPT-6.2 |
| --- | ---: | ---: |
| 图像理解 | 47.4% | 71.7% |
| 特别困难提示的指令遵循 | 62.5% | 78.1% |
| 专家级任务，最低测试推理档 | 52.3% | 65.2% |
| 网页查找，最高测试推理档 | 54.4% | 64.5% |

这四行各有题目和评分方式。专家级任务的 65.2% 来自最低测试推理档，网页查找的 64.5% 来自最高测试推理档。读表时，沿着每一行比较两代模型，才能知道这个数字对应的能力和设置。

![Chat 四档推理设置下的专家级任务对比](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/gpt62-chat-expert.png)

*图片来源：OpenAI《ChatGPT 发布日志》专家级任务评测，2026-11-23。*

专家级任务的原图还有一个有趣的细节：GPT-6.2 从 Instant 的 65.2% 升到 High 的 71.5%，Extra high 则是 71.1%。这组题里，最显眼的变化出现在两代模型的即时档对比，旧版即时档为 52.3%。

## 会搜网页，更要看它能不能找到原文

OpenAI 的内部 BrowseComp 网页查找评测用了四档推理设置，新版在四档都领先。最高档的成绩从 54.4% 升到 64.5%，差了 10.1 个百分点。这里测的是网上难找的答案，难度和随手查个天气差别很大。

![Chat 四档推理设置下的内部网页查找对比](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/gpt62-chat-search.png)

*图片来源：OpenAI《ChatGPT 发布日志》内部 BrowseComp 评测，2026-11-23。*

网页题里的差距分布也很有意思。即时档从 8.8% 到 34.2%，中档从 26.3% 到 53.5%，都是二十多个百分点。对于读者，这组分数更像一张难题地图：即便新版开到最高测试档，仍有三分之一左右的题没答对。

用在日常，一个适合交给它的问题是「两家相机厂商对二手镜头保修怎么规定」。让 ChatGPT 找到对应地区的条款原页，列出适用对象和生效日期。网页查找的价值在于把你带到正确页面，最后那两句规则仍值得自己点开读一遍。

OpenAI 还举了一个跟进的例子：如果你问某件商品有没有降价，ChatGPT 可能主动提出价格跌下来时再提醒你。从回答一次，到识别这件事会变化，是聊天入口里另一项容易感知的变化。文章里的例子用于说明用法，分数来自厂商评测。

这次更新最大的变化，是 GPT-6.2 Sol 来到了你贴截图、提条件、顺手查网页的那个聊天框。

## 来源

- [OpenAI《ChatGPT 发布日志》：GPT-6.2 进入 Chat](https://help-lb.openai.com/en/articles/6825453-chatgpt-release-notes)
- [OpenAI API 更新日志：GPT-6.2 Astra 与 Sol](https://developers.openai.com/api/docs/changelog)
- [TestingCatalog：收录 ChatGPT 官方账号的推送公告](https://www.testingcatalog.com/openai-brings-gpt-6-2-sol-to-chatgpt-chat-and-dots-to-eu/)
- [MacRumors：GPT-6.2 Sol 进入普通聊天页及报道时间](https://www.macrumors.com/2026/11/23/openai-gpt-6-2-sol-chatgpt/)
