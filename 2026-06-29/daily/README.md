---
title: "GPT-5.6 三连发夺回基模王座·百度 OCR 一口气吃下整本书·一行命令克隆任意网站 | AI 日报 | 2026-06-29"
date: 2026-06-29
weekday: Monday
slug: 2026-06-29
category: daily-report-newsletter
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/06/29.png
description: OpenAI 一口气端出 GPT-5.6 Sol / Terra / Luna 三档模型，Sol 的 ultra 模式调多个子 agent 协同，Terminal-Bench 2.1 创下新 SOTA，从 Fable 5 手里抢回最强基模位置，但只给少数合作伙伴有限预览；国内百度开源 Unlimited OCR，用滑动窗口注意力让显存不随页数膨胀，一次读完整本书、OmniDocBench 超过 DeepSeek OCR；一个两万星模板项目把"克隆任意网站"做成一行命令，吐出可直接运行的 Next.js 工程。
---

# GPT-5.6 三连发夺回基模王座·百度 OCR 一口气吃下整本书·一行命令克隆任意网站 | AI 日报 | 2026-06-29

![三款以日月大地命名的旗舰模型与一本被连续读完的厚书、一条把网页变成工程的命令交叠成今日主线](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/covers/2026/06/29.png)

## 📋 头版目录

- 🧠 OpenAI 三连发 GPT-5.6 Sol / Terra / Luna，Sol 抢回最强基模位置 → 头条 1
- 🛠 Sol 新增 max 深推理与 ultra 多子 agent 协同两种模式 → 头条 1
- 💰 三档定价 5/30、2.5/15、1/6 美元每百万 token，普通用户仍是有限预览 → 头条 1
- 🇨🇳 百度开源 Unlimited OCR，一口气读完整本书、OmniDocBench 超 DeepSeek OCR → 头条 2
- 🧠 滑动窗口注意力让显存与页数脱钩，长文档不再逐页拼接 → 头条 2
- 🛠 两万星模板把"克隆任意网站"做成一行命令，吐出能跑的 Next.js 工程 → 头条 3
- 🛠 克隆工具六大 AI 编程助手全接上，作者称 Claude Code 配 Opus 效果最好 → 头条 3
- 🔬 智谱 GLM-5.2 安全基准爆冷，IDOR 检测 39% 反超 Claude Opus 4.8 → 名人说
- 🎙 开发者用 Opus 4.8 读 MRI 求第二意见，HN 上 329 分热议边界 → 名人说
- 🔬 百度 OCR 作者疑为前 DeepSeek 研究员，长文档思路同源 → 要闻
- 🔬 英伟达开源 MoE 训练库，一行 import 微调加速 3.7 倍 → 要闻
- 📰 谷歌"推理之王"被 Meta 挖走，当年由李飞飞引进 → 要闻
- 🇨🇳 科大讯飞发企业服务 Claw 平台，一句话从需求到方案 → 国内 AI
- 🇨🇳 字节 TRAE Work Design 实测，从需求设计到代码一条龙 → 国内 AI
- 🔥 code 知识图谱 MCP codebase-memory 当日涨 2190 星登顶 → GitHub
- 🔥 价值投资多 agent 框架 ai-berkshire 涨 1445 星，OCR 工具 MinerU 持续在榜 → GitHub
- 🛡 开源 AI 漏洞猎手 strix、个人交易 agent Vibe-Trading 同日上榜 → GitHub
- 📦 GLM-5.2 以 1/6 成本超 GPT-5.5，开放权重一路冲到安全榜前列 → 值得关注
- 🛠 OCR 接进 agent 工作流成新主线，文档不再是流水线断点 → 值得关注

## 🔥 头条深度

### 头条 1 · OpenAI 三连发 GPT-5.6 把基模王座抢回来

距 Anthropic 放出 Fable 5 不到三周，OpenAI 在 6 月 27 日一口气端出三款 GPT-5.6 系列模型 [1]，名字走起了天文路线：旗舰 Sol（太阳）、平衡款 Terra（大地）、低成本高速款 Luna（月亮）。三者分工很清楚——Sol 冲旗舰能力，Terra 打日常主力，Luna 负责速度和成本。

#### 1.1 Sol 抢回最强位置，靠的是两个"加餐"模式

Sol 面向高难度推理、复杂代码、生物、网络安全这类长链路任务，尤其适合需要规划、迭代、调用工具、协调步骤的工作流。真正改变玩法的是两个新模式：max 让模型获得更长的深度推理时间，ultra 则能调用多个子 agent 协同处理同一个复杂任务。在 ultra 模式下，Sol 的 Terminal-Bench 2.1 编程基准创下新 SOTA，比 Fable 5 高出 7.6 个百分点、比上一代 GPT-5.5 高出 9.4 个百分点。生物方向的 GeneBench v1 上 Sol 比 GPT-5.5 更强且用 token 更少；网络安全方向 OpenAI 称它是自家目前最强。

![以 GPT-5.6 Sol ultra 为基准对比 Fable 5 与 GPT-5.5 的 Terminal-Bench 2.1 编程相对得分](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/06/29/daily-2026-06-29-gpt56-coding-bench.png)

#### 1.2 三档价格拉开，但门还没向普通人开

按每百万 token 计价，Sol 输入 5 美元、输出 30 美元；Terra 输入 2.5 美元、输出 15 美元，对标 GPT-5.5 而便宜约一半；Luna 输入 1 美元、输出 6 美元。对照 Fable 5 的 10/50 美元，旗舰 Sol 在标价上反而更省。但门没有真正打开——三款模型目前只对少数受信任的合作伙伴提供有限预览，普通用户用不上，是熟悉的"先发布、后排队"。

#### 1.3 两周内被反超的不是分数，是节奏

值得注意的是发布节奏本身。Fable 5 才坐稳最强基模没几天，OpenAI 就把对位产品摆上桌，连命名都换了一套体系想立新锚点。对开发者来说，真正能上手的还是 Terra 这类对标日常的中间档，旗舰能力的预览权早期集中在伙伴手里。这一轮交锋说明前沿模型的领先窗口越来越短，单纯刷榜的意义在被"谁先让你用上"取代。

### 头条 2 · 百度开源 Unlimited OCR，一口气读完整本书

国产开源 OCR 又刷新了 SOTA。百度 6 月 28 日开源全新模型 Unlimited OCR [2]，主打一口气读完几十页长文档，在 OmniDocBench 上整体成绩超过此前的 DeepSeek OCR。它模仿人类抄录员：不死记前面抄过的内容，只保留当前需要的信息和进度，因此能像人一样连续读完整本书，而不是每读一页就中断重启。

#### 2.1 把"显存随页数膨胀"这个老问题解掉

传统 OCR 解码器每生成一个新字，都要回看之前所有 token，文档越长、KV Cache 越胀，显存和算力开销跟着涨，所以只能逐页处理再拼接。Unlimited OCR 引入参考滑动窗口注意力（Reference Sliding Window Attention，R-SWA），让文档越长、显存占用和注意力计算几乎不增长，把"长程"从工程权宜之计变成模型本身的能力。

![百度 Unlimited OCR 与 DeepSeek OCR 在 OmniDocBench 长文档识别上的相对表现](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/06/29/daily-2026-06-29-baidu-ocr-omnidocbench.png)

#### 2.2 对开发者意味着文档不再是断点

把整本书当一个上下文读，意味着复杂版式、跨页表格、连续编号能保住语义，agent 处理资料时不必再为"逐页切分—拼接—对齐"写一堆胶水。配合英伟达把 MoE 训练库开源、各家继续压低长上下文成本，文档读取正从单独环节并进 agent 工作流，国产开源在这条线上拿到一个可复现的标杆。

### 头条 3 · 一行命令克隆任意网站，吐出能跑的 Next.js 工程

看到一个网站好看想复刻，不用再开 F12 抄 CSS。一个两万星的模板项目 ai-website-cloner-template 把这事做成一行命令：给它一个 URL，自动采集页面元素、计算样式、遍历滚动点击悬停等交互、提取标准化设计 token，再调度多个 agent 基于 Git 工作树并行重建组件，最终吐出一套可本地运行的完整 Next.js 工程，内置路由、模块化组件和严格 TypeScript 类型，跑 `npm run dev` 即起 [3]。

用法是 GitHub 模板仓库，点 Use this template 一键开仓、装依赖即可。市面上叫得出名的 AI 编程助手——Claude Code、Codex CLI、Cursor、Copilot、Gemini CLI、Windsurf——它全接上了，作者提示 Claude Code 配 Opus 效果最好。能力越普及，"像素级抄站"越接近免费，前端的差异化只能往交互逻辑和数据层走，纯静态视觉很难再守住护城河。

## ⚡ 快讯速览

- 英伟达开源一套 MoE 训练库，号称一行 import 即可接入，微调加速 3.7 倍，把混合专家从论文搬进日常微调；待观察其对非英伟达卡的兼容范围。
- 谷歌一位被称作"推理之王"的核心研究员转投 Meta，此人当年由李飞飞引进，前沿团队的人才流向仍是判断公司储备的先行指标；待观察其负责方向是否随之迁移。
- opendatalab MinerU 持续在 Trending 榜上，把 PDF 和 Office 文档转成 LLM 可读的 markdown/JSON，与百度 OCR 同属"喂 agent"赛道；待观察两者在版式还原上的差距。
- 科大讯飞发布企业服务 Claw 平台，主打一句话从需求到方案全流程，国内厂商集体把 agent 往企业落地推；待观察实际交付的可用度。
- 字节 TRAE 推出 Work Design，宣称从需求、设计到代码一条龙，演示已放出；待观察非演示场景下的产出完成度。

## 🎙 名人说 & X 热议

智谱 GLM-5.2 在安全研究者手里爆了个冷门：Semgrep 把它丢进自家 IDOR 漏洞检测基准，这款开放权重模型拿到 39% F1，反超 Claude Code 的 32%，也压过 Claude Opus 4.8，每找到一个漏洞约 0.17 美元，只是仍落后 Semgrep 多模态流水线的 53–61% [4]。Semgrep 想问的不是"谁是开源冠军"，而是漏洞检测的本事有多少来自模型、多少来自外面那层 harness——开放权重模型已经不再是明显的弱者。

另一边，一位开发者用 Opus 4.8 读自己的肩部 MRI 求第二意见，把过程写成文章，HN 上 329 分热议 [5]。作者反复强调"我不是医生、技术也许还没到"，争论焦点正落在这里：模型读片能给信息和方向，但越界成诊断就危险。两件事指向同一个判断——把模型放进专业流程，价值和风险都在 harness 与边界上，而非分数本身。

## 📰 精选要闻

- 🟡 百度 Unlimited OCR 的作者疑为前 DeepSeek 研究员 [2]，长文档"连续读"的思路与 DeepSeek OCR 同源，国产 OCR 的人才与方法正在小圈子内快速迭代。
- 🟡 英伟达开源 MoE 训练库，一行 import、微调提速 3.7 倍，把混合专家的工程门槛进一步压低，利好中小团队做定制模型。
- 🔵 谷歌"推理之王"被 Meta 挖走，前沿实验室人才争夺仍在升温，影响的是 12 个月后的模型代差而非当下产品。

## 🇨🇳 国内 AI 观察

- 科大讯飞 Claw 企业服务平台主打"一句话搞定需求到方案"，方向是把 agent 接进企业交付链；开发者拿到的是更接近落地的工作流模板，而非又一个聊天框。
- 字节 TRAE Work Design 把需求、设计、代码串成一条流水线，配合一行命令克隆网站这类工具，国内 AI 编程正从"补全代码"往"补全整个项目"推进。

## 📦 GitHub Trending

- 🔴 codebase-memory-mcp 当日涨 2190 星登顶，把代码库索引成持久知识图谱，号称 158 种语言、毫秒级建库、亚毫秒查询、省 99% token，单文件零依赖，正好接上各家 agent 缺的"长期代码记忆"。

![codebase-memory-mcp 仓库卡：高性能代码情报 MCP，把代码库索引成持久知识图谱](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/06/29/source-github-DeusData-codebase-memory-mcp-og-2026-06-29.png)

- 🔴 ai-berkshire 当日涨 1445 星，基于 Claude Code / Codex 的价值投资研究框架，把多 agent 并行研究包成一套方法论模板，是 agent 落到垂直行业的样本。
- 🟡 opendatalab MinerU 持续在榜，把复杂 PDF、Office 文档转成 LLM 可读的 markdown/JSON，与百度 OCR 共同把"文档喂 agent"做成标配。

![MinerU 仓库卡：把 PDF 与 Office 文档转成 agent 可用的 markdown/JSON](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/06/29/source-github-opendatalab-MinerU-og-2026-06-29.png)

- 🟡 strix 与 Vibe-Trading 同日上榜，前者是开源 AI 漏洞猎手、自动找并修应用漏洞，后者是个人交易 agent，agent 应用层的题材在快速铺开。

## 🛠 AI Coding 工具动态

- ai-website-cloner-template 把克隆站做成模板仓库，六大 AI 编程助手全支持，等于给所有 IDE/CLI 加了"抄站即工程"的能力，前端起手成本被进一步抹平。
- 漏洞检测开始下放到模型：GLM-5.2 在 IDOR 基准上反超 Opus 4.8，意味着把安全扫描挂进 CI 的 agent 链路有了更便宜的开放权重选项，关键仍在外层 harness 怎么搭。

## 🔭 值得关注

- GLM-5.2 以约 1/6 的成本在多项长程编程基准超过 GPT-5.5，又在安全榜反超 Opus 4.8，753B 开放权重的性价比正逼近闭源旗舰；是否带动更多团队把基础设施切到开放权重，待业内观察。
- OCR 正从独立环节并进 agent 主线：百度 Unlimited OCR 让显存与页数脱钩、MinerU 把文档转结构化、英伟达压低 MoE 微调成本，文档读取会不会成为 agent 标配输入，待官方与开源生态观察。

## ✍ 编辑说

- GPT-5.6 / 做技术储备：旗舰只给伙伴预览，普通开发者短期摸不到，但 ultra 调子 agent 的形态值得提前理解；若你做 agent 编排，这条决定 12 个月内的架构假设。
- 百度 Unlimited OCR / 推荐：开放权重 + 显存不随页数涨，做文档 agent 的人今天就有可复现标杆；判断价值看它对跨页表格和复杂版式的还原。
- 克隆站模板 / 关注：像素级抄站接近免费，前端竞争只能往交互与数据层走；若你靠静态视觉吃饭，这条提示护城河在变浅。
- GLM-5.2 / 观望：性价比和安全分都亮眼，但成绩多在特定 harness 下取得，落到自家链路前先验证 harness 依赖度，再谈是否切换。

## 🔗 引用链接

- [1] GPT-5.6 Sol/Terra/Luna 发布: https://www.qbitai.com/2026/06/438895.html
- [2] 百度开源 Unlimited OCR: https://www.qbitai.com/2026/06/439464.html
- [3] 一行命令克隆任意网站: https://www.qbitai.com/2026/06/439515.html
- [4] GLM-5.2 安全基准反超 Claude: https://semgrep.dev/blog/2026/we-have-mythos-at-home-glm-52-beats-claude-in-our-cyber-benchmarks/
- [5] 用 Opus 4.8 读 MRI 求第二意见: https://antoine.fi/mri-analysis-using-claude-code-opus
