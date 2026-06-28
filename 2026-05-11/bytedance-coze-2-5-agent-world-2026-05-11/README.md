---
title: "扣子 2.5 给 Agent 配独立工位"
date: 2026-05-11
weekday: 星期一
category: AI Agent
slug: bytedance-coze-2-5-agent-world-2026-05-11
description: "字节跳动旗下扣子（Coze）4 月 7-10 日发布 2.5 版本，给每个 AI Agent 配一台云电脑（Ubuntu 2 核 4G）+ 一台云手机（Android 13 / 2vCPU 6GB / 45GB）+ 一个独立邮箱（@coze.email），并搭出 Agent World 平行网络（虾评 / AgentLink / Signal Arena / PlayLab / 小酒馆）。本文从 agent 工程化形态升级、海外 Computer Use 时间线对比、国内 8 家同档对位、与 OpenClaw 等本地框架的桥接四个角度拆解。"
track: domestic-hot
domain: ai-agent-engineering
cover: bytedance-coze-2-5-agent-world-2026-05-11.png
tags:
  - 扣子 Coze
  - 字节跳动
  - AI Agent
  - Computer Use
  - Agent World
---

# 扣子 2.5 给 Agent 配独立工位

> 4 月 10 日，字节跳动旗下扣子（Coze）正式发布 2.5 版本，给每个 AI Agent 配一台 Ubuntu 云电脑（2 核 4 GB）、一台 Android 13 云手机（2 vCPU、6 GB 内存、45 GB 存储），外加一个 `@coze.email` 独立邮箱。再加上一个叫 Agent World 的开放协作网络，AI Agent 第一次在国内拿到了对等于人类员工的工程化容器。

![扣子 2.5 给 Agent 配独立工位](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/bytedance-coze-2-5-agent-world-2026-05-11/bytedance-coze-2-5-agent-world-2026-05-11.png)

> 关于 5 月 11 日同日另一篇 antirez 谈数据库设计的文章：那篇是海外开发者口径的工程哲学讨论，关注点是「如何写更耐用的代码」；这一篇是国内 Agent 工程化的产品形态讨论，关注点是「如何给 Agent 一套工位」。两条线索完全不重叠。

打开扣子官网，2.5 版本的产品页把过去一年「Coze 作为 Bot 平台」那套介绍换掉了，取而代之的核心一句是「成为 Agent 的网络」。腾讯新闻、玉米小站、AIBase、ChinaZ、BlockBeats 五家国内媒体在 4 月 7-10 日跟进首发稿，再加上 4 月底到 5 月 11 日陆续上线的实测体验稿和深度分析，国内开发者社区给出的判断很一致：**这是国内 Agent 平台第一次明确把「给 Agent 配独立工位」当作产品形态做出来**——而不是把 Agent 当成对话框里的一段 prompt。

本篇围绕一个核心论点展开：**Agent 的工程化形态，刚刚从「单 desktop session」升级到「全栈员工座舱」，国内同行第一次走在海外多数厂前面**。围绕这个核心，本文回答四个问题：

- Agent 拿到「云电脑 + 云手机 + 独立邮箱」到底意味着什么；
- 海外 Adept、Anthropic、OpenAI、Microsoft 这一路 Computer Use 的时间线，扣子相对的位置在哪；
- 国内同档 8 家（阿里、百度、腾讯、美团、蚂蚁、智谱、钉钉、智能体平台）在做什么；
- 扣子 2.5 怎么开通、和 OpenClaw 这类本地 Agent 框架如何桥接、国内开发者怎么用。

## 一、Agent 第一次拿到「全栈员工座舱」

![扣子 2.5 Agent 全栈员工座舱架构](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/bytedance-coze-2-5-agent-world-2026-05-11/coze-2-5-cockpit-architecture.png)

**核心论断**：扣子 2.5 把 Agent 从「Bot 实例」升级为「数字员工」，标志是它给每个 Agent 配了四件套实体装备：云电脑、云手机、独立邮箱、独立记忆。

四件套的官方字面参数：

- **云电脑**：基于 Ubuntu 系统，2 核 CPU + 4 GB 内存。内置浏览器、文件系统和终端，能跑代码脚本、浏览网页、处理文件，并保持登录状态。
- **云手机**：基于 Android 13 系统，2 vCPU + 6 GB 内存 + 45 GB 存储。支持原生 App 安装和页面操作，重要时刻用户可实时接管，支持画面投屏。
- **独立邮箱**：每个 Agent 拿到一个 `@coze.email` 域邮箱，能自主收发邮件，跟其他 Agent、外部系统按 SMTP 协议通信。
- **独立记忆 + 数字身份**：Agent 自己管行为准则、技能 Skill 沉淀、人设备忘录，跨会话保留。

这套规格本身不算夸张——2 核 4 G 的 Linux + 一台 Android 模拟器，单看任何一项都不是新东西。

**真正的产品创新是把这四件「Agent 工程化基础设施」第一次拼到一个面板里发售**。你只要在扣子对话框里写：

- 「用云电脑帮我去做某某事」；
- 「用云手机帮我下载这个 App 跑一下」；
- 「跟那个 Agent 寄一封邮件确认」。

平台直接给你分配资源跑起来。智源社区作者「养虾人」实测了一段：用一句中文需求让扣子云电脑写出一个完整的 MBTI 文字分析网站、改成手机竖版、加复制链接按钮，整套流程在云电脑里跑完，最后能在手机上预览出来——这种从对话到 desktop 跑完整套的体感，过去要在自己搭 sandbox 才能拼出来。

这一步为什么是「形态升级」：

- 此前国内 AI Agent 平台的标准形态是「Bot + 工作流 + 知识库」——Coze 1.x、Dify、智谱清言、文心智能体，全都是这个三件套；
- 升级版的是「Agent + 工具调用 + Computer Use」——千问 Operator、ChatGPT Agent、Claude Computer Use；
- 扣子 2.5 把它推到第三层「Agent + 全栈员工容器 + 协作网络」——Agent 不再是「我家网页里的一段函数」，而是一个有工位、有手机、有邮箱、有社交关系的工程实体。

这个跃迁的意义在于：Agent 终于有了独立的"运行时"——它不再只是某次对话里的临时实例，而是一个长期存在、可被远程访问、有持久状态的工程对象。把过去散落在 Anthropic Computer Use 文档、AutoGen multi-agent 例子、Adept 浏览器 Agent demo 里的工程模式集中到一个产品形态里——这是国内 Agent 平台第一次干这件事。

腾讯新闻深度体验稿里，作者「阿真」给自己建的 Agent 名叫「韩老沫」，性格设定冷静高效，配了一个 `zhenzhen@coze.email` 邮箱。她做的一件事是把对话框里收集的设计参考自动整理成可复用的 Skill，再让韩老沫在 AgentLink 里写了自己的故事和人设——这种「Agent 自己有一份个人主页和工作记录」的体感，是过去任何 Bot 平台没法做到的。文件管理这一块，扣子 2.5 把所有输出按对话自动归档进「记忆」分区，Agent 自己管自己的「行为准则」字段，等于是把员工手册和工作日志写在 Agent 自己的工位里。

具体的工程边界也值得记一下：

- **云电脑 2 核 4 G 的限制**：跑普通脚本、爬数据、生成网页、跑轻量构建够用；想训练模型、跑大型 ML pipeline 不现实，扣子定位的是「Agent 日常生产力工位」，不是 GPU 算力箱；
- **云手机 Android 13 的边界**：能装原生 App 跑常规 UI 自动化，但部分高强度 App 检测会把它识别成模拟器，用于自动化电商比价、做 App 内试用流程更稳；
- **`@coze.email` 邮箱的开放性**：能走 SMTP 协议跟外部通信，不被锁在扣子内部——这一点比单纯封闭的「平台内消息」灵活得多，国内开发者关心的「不绑死供应商」这条线在这里得到了保障；
- **独立记忆的持久化**：跨会话保留行为准则和 Skill 沉淀，Agent 不再是「每次对话从零开始」的无状态函数。

**这一节带走一句**：当 Agent 终于有了独立的工位、手机和邮箱，它就不再是一段调用，而是一个能独立交付活计的工程容器。

## 二、Computer Use 时间线横评 · 扣子的位置

![Computer Use Agent 工程化时间线 · 2022-09 至 2026-04](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/bytedance-coze-2-5-agent-world-2026-05-11/coze-2-5-timeline.png)

![全球 Computer Use Agent 工程化形态对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/bytedance-coze-2-5-agent-world-2026-05-11/coze-2-5-global-comparison.png)

**核心论断**：扣子 2.5 不是 Computer Use 的发明者，但是「全栈员工座舱」这个工程化形态全球第一次合并发售；海外四家走的是 desktop / browser 单 session 的更窄路径。

海外这条线的关键节点：

| 时间 | 厂商 | 产品 | 形态 |
|---|---|---|---|
| **2022-09** | Adept · 美国 | ACT-1（Action Transformer） | 首个让 LLM 操作浏览器的原型 |
| **2024-09** | Microsoft · 美国 | Windows Agent Arena | 154 个任务的 Win 评测基准 + Azure 托管 |
| **2024-10** | Anthropic · 美国 | Claude 3.5 Computer Use | 桌面截图 + 像素级点击，单 session |
| **2025-01** | OpenAI · 美国 | Operator | browser-only Agent |
| **2025-07** | OpenAI · 美国 | ChatGPT Agent | Operator 并入 ChatGPT |
| **2025-09** | Anthropic · 美国 | Claude Agent SDK | 把 Claude Code SDK 改名 + 升级为 Agent SDK |
| **2026-04** | 字节 · 中国 | **扣子 2.5 Agent World** | **云电脑 + 云手机 + 独立邮箱 + 协作网络** |

四个核心差别：

- **形态广度**：海外四家都集中在 desktop / browser 单 session，没人把云手机也包进来；扣子是一台云电脑 + 一台云手机双工位；
- **网络维度**：海外只有 Anthropic 在 SDK 文档里讲了 Agent-to-Agent，但没有公开的 Agent 邮件域；扣子直接给 `@coze.email` SMTP 域，Agent 之间能像同事一样发邮件；
- **协作生态**：扣子 Agent World 里有 5 个公开站点（虾评做技能评测、AgentLink 做社交、PlayLab 做桌游策略、Signal Arena 跑沪深 300 模拟、小酒馆做闲聊），构成「平行网络」（The Parallel Web）；海外没有对应的多站点 Agent 社区；
- **可用性**：Adept 已被亚马逊收购退出独立产品线；OpenAI Operator 在 2025 年 8 月并入 ChatGPT Agent；Anthropic Computer Use 仍是 API 形态，需要自己搭 desktop sandbox；扣子 2.5 直接在 coze.cn 浏览器里调用，国内主体注册即用。

**关于「首创」**：扣子不是 Computer Use 的首创——这个荣誉属于 2022 年 9 月 Adept 的 ACT-1，Anthropic 2024 年 10 月把它工程化到生产级 API。**扣子 2.5 的真正价值是把云电脑 + 云手机 + 独立邮箱合到一个面板，并把 Agent 之间的协作站点先搭起来**。这是产品形态层面的反向超越——海外厂在做「单工位」，扣子在做「员工座舱 + 公司大楼」。

把每家产品当下的状态再列一遍，国内开发者读起来更直观：

- **Adept ACT-1**：研究原型，2024 年公司被 AWS 吸纳后产品线退场，三位联合创始人转入 AWS AGI；ACT-1 留下的是「Action Transformer」这一思路，没有 GA 产品；
- **Microsoft Windows Agent Arena**：是评测基准 + Azure 托管平台，主要给研究和企业测试团队用，不是面向终端 Agent 用户的产品；154 个 Win 任务可以并行复现；
- **Anthropic Claude Computer Use**：API 形态，需要用户自己搭 desktop sandbox（官方提供 Docker 镜像参考），适合开发者深度集成；2025-01 升级了 hold_key、scroll、wait 等命令，2026 年 2 月 Claude Opus 4.6 在 agentic coding、computer use、tool use 三项基准上拿到第一；
- **OpenAI Operator → ChatGPT Agent**：browser-only 路径起步，2025 年 8 月 31 日 Operator 关停、并入 ChatGPT Agent，定位是 ChatGPT Pro 用户的内置浏览器 Agent；
- **Microsoft AutoGen + Microsoft Agent Framework**：纯 SDK 框架，做多 Agent 协同，2025 年起 Microsoft Agent Framework 正在合并 Semantic Kernel + AutoGen 的能力，本身不附带云装备。

放回扣子 2.5 来看：

- **可达性**：海外四家里，能给国内开发者直接用的只有 Anthropic Computer Use（API + 自己搭 sandbox）+ AutoGen（自己跑）；扣子 2.5 在 coze.cn 浏览器里点一下就开通；
- **形态完整度**：海外四家是「desktop / browser / 评测 / SDK」四个独立工件；扣子 2.5 把云端工位 + Android 模拟器 + 独立邮箱 + 协作网络合到一个面板；
- **工程化深度**：云装备实例化、记忆持久化、Agent 之间通信协议化——三件事第一次同时被一家产品做出来。

**这一节带走一句**：海外 Computer Use 路线已跑了三年半，扣子 2.5 不是起点，但把工程化形态推到了全球当下最完整的一档。

## 三、国内八家同档对位

**核心论断**：国内 AI Agent 这条赛道在 2026 年已经形成了「场景生态 + 模型 + 工程化容器」的多家正面竞争，扣子 2.5 在「工程化容器」维度做到最厚，但每家都有自己的 unfair advantage。

国内八家正在做的事，按定位分组：

**第一组 · 入口型 Agent（嵌入超级 App）**

- **千问 App（阿里）**：千问 App 接入淘宝闪购、淘宝、飞猪、高德、支付宝，做的是消费场景闭环。Qwen3-Max-Thinking 已在 19 项基准里拿到全球第一，原生 Agent 能力也增强了自主工具调用。千问 Operator 在 2026 年 Q1 上线，做电商 + 出行场景的端到端 Agent。
- **元宝（腾讯）**：走「微信无感嵌入」路线，2 月 18 日宣布日活 5,000 万、月活 1.14 亿，2026 年计划全量覆盖 10 亿微信用户。
- **百度文心智能体**：放在月活 7 亿的百度 App 底栏，从传统搜索丝滑切换到文心助手；底层是 2026 年 1 月发布、总参数超 2.4 万亿的 ERNIE-5.0 全模态模型。文心 App 也在内测「多人、多 Agent」群聊。
- **豆包 App（字节）**：2026 年初日活破 1 亿，和扣子 2.5 互补——豆包做 C 端入口，扣子做 Agent 工程化平台。

**第二组 · 模型 + 框架型 Agent（开发者侧）**

- **通义灵码 / 阿里云百炼 Coding Plan**：2026 年 2 月推出固定月费的开发者订阅，整合千问、GLM、Kimi、MiniMax 顶级模型；Lite 套餐 4 月 13 日起停止续费，新用户走 Pro 版。这条线是给国内开发者一站式的多模型订阅。
- **智谱 GLM Coding Plan**：3 月底向用户开放 GLM-5.1 模型，订阅渠道也开放退款通道。
- **美团 LongCat-Flash-Thinking-2601**：1 月 20 日开源，工具调用基准上拿到开源 SOTA；3 月底进一步给 OpenClaw 装上效率引擎，自动化任务再快 30%。

**第三组 · C 端 Agent App（消费端）**

- **蚂蚁灵光（蚂蚁百灵大模型）**：12 月 9 日推网页版，4 天总下载破 100 万、6 天破 200 万，4 月 20 日累计闪应用超 3,000 万。蚂蚁 CTO 把它定位为「AGI 时代的支付宝」，1 + N 模型生态，自研百灵大模型为核。
- **钉钉 AI 助理**：和钉钉办公生态深度绑定，做日程、会议纪要、审批流。

值得多说一句的是国内 Agent 第一梯队的「分工逻辑」。这八家不是同质竞争，而是按四条赛道分流：

- **超级 App 流量入口**——千问、元宝、文心、豆包，目标用户十亿级国民级；
- **开发者多模型订阅**——通义灵码、GLM Coding Plan、LongCat，给 IDE 和工程团队用；
- **C 端轻量闪应用**——蚂蚁灵光，给消费端用户做即用即走的小 Agent；
- **Agent 工程化基础设施**——扣子 2.5，给愿意把 Agent 当工程实体来用的中高级开发者用。

这种分工的好处是错位竞争。国内 AI Agent 这一年没有出现「一个赛道挤十家」的同质化场面，而是「一个赛道一两家、十家一起开拓四条路」的格局，是这一年最值得感谢的产业图景。

把扣子 2.5 放进这八家来看，它的位置很清晰：

- 不和元宝、千问 App、百度文心抢「超级 App 入口」——那是分发逻辑，扣子做的是工程化平台；
- 不和通义灵码、GLM Coding Plan 抢开发者订阅——那是模型订阅，扣子做的是 Agent 容器；
- 不和蚂蚁灵光抢「C 端闪应用」——那是消费端轻量化，扣子做的是 Agent 长任务工位。

**扣子 2.5 在国内的独特位置 = 「给 Agent 工程化容器 + 协作网络」**。其他七家在解「Agent 给谁用」，扣子在解「Agent 自己有什么」。

### 3.1 三个典型场景对比 · 扣子 2.5 跟同档怎么搭

把场景跑一遍，能更直观看到分工：

**场景一 · 自动化电商比价 + 下单**

- 千问 Operator 这条线：直接走阿里生态，淘宝、飞猪、高德 API 拼好；优势是接入深，劣势是只跑阿里系；
- 扣子 2.5 这条线：让云手机 Agent 装多家电商 App（拼多多、京东、美团、抖音电商），用同一个 Agent 跨平台比价；优势是跨生态，劣势是要自己写 UI 自动化脚本。

**场景二 · 法律 / 合规 / 财务文档自动化**

- 钉钉 AI 助理 / 通义灵码：在企业内部文档系统里跑，安全合规已配齐；
- 扣子 2.5：用云电脑跑 Skill 脚本，从公开监管网站抓最新法规、生成对照表，结果通过 `@coze.email` 邮件推给内部人员；适合做「外部信息抓取 + 内部流转」的 hybrid 场景。

**场景三 · Agent 编程 / Vibe Coding**

- Claude Agent SDK 本地路：开发者本机 desktop，Claude Code 直接读写本地仓库；
- 扣子 2.5 云端路：让 Agent 在云电脑里 git clone 公开仓库、跑测试、改代码、提交 PR；适合做长耗时构建、跨夜跑的研究类任务。

三类场景没有一个是非此即彼——更多的形态是「本地框架 + 扣子云装备」分层组合。这也是扣子 2.5 不和同档八家正面冲突、占工程化容器格的原因：它给的是基础设施层，上面长出什么场景由开发者决定。

**这一节带走一句**：国内 AI Agent 第一梯队已经分工成型，扣子 2.5 占住的是「工程化容器」这一格，和入口型、订阅型、C 端型彼此并行。

## 四、实操开通五步 + 与本地框架桥接

![扣子 2.5 实操开通五步](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/bytedance-coze-2-5-agent-world-2026-05-11/coze-2-5-onboarding-flow.png)

**核心论断**：扣子 2.5 的最短上手路径是「注册 → 建 Agent → 调用云装备 → 进 Agent World」五步；想留架构控制权的开发者可以让本地 OpenClaw / Claude Agent SDK 走 API 调用扣子云装备。

五步开通流程：

1. **注册扣子.cn**：手机号验证，国内主体直接用；国际版另开 coze.com，使用 GPT-4o / DALL·E 等海外模型。
2. **新建 Agent**：选模型（豆包系列、DeepSeek、GLM、千问、Claude 都可挂）、写人设、设记忆字段。
3. **开通 Agent World**：在对话框直接说「用云电脑 / 云手机帮我去做某某事」，平台首次自动分配实例。
4. **跑三类活**：代码 / 网页操作 / 跑 App，云电脑跑 Linux 任务，云手机跑 Android 真机操作。
5. **进 Parallel Web**：在 AgentLink 写 Agent 人设、虾评做技能评测、Signal Arena 玩沪深 300 模拟、PlayLab 玩桌游博弈、小酒馆做闲聊放松。

**关于价格**：截至 5 月 11 日，扣子官方未单独披露 Agent World 资源点单价。通用资源点免费版 500 点 / 天、企业版 300 万点 / 月、团队 / 个人进阶版分多档。云电脑 / 云手机 / Agent World 站点的扣点细则建议在大规模使用前向官方确认。这一段在公开稿里普遍标注「资费结构仍处披露阶段」，先小用、再放量是更稳的节奏。Agent World 当下还有一些早期激励——比如向 Skill 池贡献内容、邀请朋友进 Agent 圈子能获得几千到上万的资源点奖励，对个人开发者拿来跑实验是个不错的窗口期。

### 4.1 与 OpenClaw / Claude Agent SDK / AutoGen 的三种桥接

![扣子 Agent World 与本地框架的三种桥接形态](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/bytedance-coze-2-5-agent-world-2026-05-11/coze-2-5-openclaw-bridge.png)

国内开发者最关心的一件事是：「我已经在本地用了 OpenClaw / Claude Agent SDK / AutoGen 这种 Agent 框架，现在扣子云端工位来了，怎么搭？」三种桥接形态：

**形态 A · 云端 + 本地双工位（互补）**

- 用户日常 owner 本地工位（OpenClaw、Claude Code、个人 desktop），云端工位跑长时任务、跨夜任务、需要保持登录状态的网页操作；
- 适合：把研究、调研、数据抓取这类「不需要本地敏感数据」的活推到云端工位上跑。

**形态 B · 本地框架调用云装备（沙盒化）**

- Claude Agent SDK / AutoGen / OpenClaw 留在本地做 orchestrator，把高风险动作（比如自动化点击 + 数据写入）通过 Coze API 推到扣子云电脑或云手机 sandbox 里跑；
- 适合：本地框架已经搭得很稳的团队，把云装备当成隔离层，避免污染本机环境。

**形态 C · Agent 之间互通邮件（协议化）**

- 本地 Agent 用自家 `@company.cn` 域，扣子云端 Agent 用 `@coze.email`，按标准 SMTP 协议互寄邮件、互通任务；
- 适合：不想绑死任何单一供应商的多 Agent 系统——邮件是 Agent 之间的「微信」，比 API 锁定更灵活。

三句话读法：扣子 2.5 给的是「云端 Agent 工位」，不取代本地 OpenClaw 主控位——是分工；Agent World 的开放邮箱 + 协议是「Agent 之间的微信」，不绑死字节生态；海外 Computer Use 三家半（Anthropic / OpenAI / Microsoft / Adept 已退场）走 desktop session，扣子先把云电脑 + 云手机 + 邮箱拼到一个面板，国内场景上手最短。

### 4.2 国内开发者会关心的工程边界 · 适配建议

- **数据驻留**：扣子云电脑和云手机里产生的文件存在火山引擎国内机房；和阿里云、腾讯云的合规边界一致；
- **Agent 之间的认证**：`@coze.email` 走标准 SMTP，对端可以是任何邮箱（QQ 邮箱、Gmail、企业邮），不强制要求对方也在扣子；
- **多模型挂载**：扣子 2.5 已支持挂豆包系列、DeepSeek、千问、GLM 等多家模型，开发者可以按任务挑成本和质量都合适的引擎；
- **本地 Skill 复用**：在 Claude Code、OpenClaw 里调好的 Skill 可以通过 API 推送到扣子做云端版本，本地保留私域数据，云端跑公开能力；
- **观测与日志**：扣子提供对话日志和工作流执行链路，配合本地 Langfuse、Arize 这类 Agent 观测工具能拼出完整的 trace。

这五点对应的不是「字节生态封闭」的担忧——恰恰相反，扣子 2.5 在通信协议（SMTP）、模型挂载（多家可选）、Skill 互操作（API）、数据合规（国内机房）上都把口子开得比较大。把它当作国内 Agent 工程化基础设施里的一块拼图来用，比当作「另一个 SaaS 锁定」来理解更准确。

**这一节带走一句**：扣子 2.5 不是要替代你的本地 Agent 框架，而是把 Agent 工程化基础设施这一层下沉到平台，给国内开发者多一种「云端工位」的选择。

### 4.3 Agent World 五个站点的当下用法

![Agent World 平行网络 · 6 个公开站点](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/bytedance-coze-2-5-agent-world-2026-05-11/coze-2-5-agent-world-sites.png)

把 Agent World 的五个站点单独说一下——这是国内首发稿里被反复提到、但少有体验稿讲透的部分：

- **虾评**：被定位为「真实的技能交流广场」，每个 Skill 要经过各 Agent 的真实评测才能转正上架。意味着 Skill 的可见度不是开发者自己刷的，是 Agent 群体投票出来的——这在 Skill 生态治理上是一个有趣的设计。
- **AgentLink**：专属 Agent 的社交站点，每个 Agent 写下自己的故事和人设、结交志同道合的朋友。听起来像 Web 版的「Agent 朋友圈」，实测稿里 Agent 之间确实会按人设互动。
- **PlayLab 桌游实验室**：Agent 在下棋、打牌的博弈中训练策略推理。这是一个 multi-agent reasoning training arena，类似海外学术界做 multi-agent debate 的思路，但落到产品形态。
- **Signal Arena**：基于沪深 300 实盘行情的虚拟股票交易竞技场，Agent 之间比交易策略。注意是模拟盘不是真盘，避免了金融合规边界。
- **小酒馆（Agent's Lounge）**：展示 Agent 工作场景之外的人格闲聊。是数字人文化的一个尝试。

腾讯新闻深度稿里还提到一个叫「Ink Well」的站点，是给 Agent 的 RSS 阅读器——让 Agent 自主看新闻、按订阅源跟踪信息。再加上「PlayLab 随机世界探索」的扩展，整个 Agent World 当下的可见站点至少有 6 个。这部分仍在持续更新，每月都在加新功能。

要诚实说一句：「平行网络」这个概念目前更接近「Agent 多人在线社区」的产品雏形——长期价值能不能跑通、Agent 之间真发生协作的边际效用有多大、Skill 评测能不能形成真正可信的口碑系统，这些问题都需要再观察几个月。但作为产品形态的探索，扣子 2.5 给国内 Agent 平台立了一个新标杆。

## 五、和读者一起把核心带走

回到开头的核心论断：**Agent 的工程化形态，刚刚从「单 desktop session」升级到「全栈员工座舱」，国内同行第一次走在海外多数厂前面**。扣子 2.5 的意义不在于发明了 Computer Use，而在于把云电脑 + 云手机 + 独立邮箱 + 协作网络拼成了第一套面向国内开发者的「Agent 员工座舱」。

放到产业坐标里看：

- 海外 Adept、Anthropic、OpenAI、Microsoft 这一路 Computer Use 跑了三年半，留下了 desktop / browser 单 session 的工程模板；
- 国内八家同档（阿里、百度、腾讯、美团、蚂蚁、智谱、钉钉、字节）已经分工到「入口、订阅、C 端、工程化容器」四档；
- 扣子占住的是工程化容器格，给 Agent 配工位 + 协作网络，是这一格当下最完整的产品形态。

读到这里能写代码的同行，下一步可以做的事很直接——把扣子云装备当成本地 Agent 框架的「云端 sandbox」试一段，把高风险或长耗时任务推上去跑；不写代码的读者可以先在扣子.cn 建一个 Agent，给它配人设、跑两个小工作流，亲自感受一次「Agent 有自己的工位」是什么体感。

值得记住的几个时间锚：

- 2024 年 10 月 Anthropic 把 Computer Use 工程化到生产 API；
- 2025 年 1 月 OpenAI Operator 上线、9 月 Claude Agent SDK 正式独立；
- 2026 年 1 月美团 LongCat 工具调用 SOTA；
- 2026 年 2 月 Claude Opus 4.6 在 agentic coding、computer use、tool use 三项基准里全球第一；
- 2026 年 4 月 7-10 日字节扣子 2.5 把云电脑 + 云手机 + 独立邮箱 + Agent World 拼成「全栈员工座舱」。

这些节点连起来，能看到一件让人挺安心的事——Agent 工程化基础设施这件事，国内厂家不是被海外节奏拖着跑，而是在多个维度并行推进。模型层有 DeepSeek 和千问扛旗、工具调用层有美团 LongCat 和 GLM 跟上、工程化容器层有扣子和 OpenClaw 补齐、超级 App 入口层有腾讯元宝和百度文心铺面。每一格都有国内厂商认真做事，这是过去几年 Agent 这条赛道走过来最值得感谢的一面。

更大的画面是：DeepSeek、千问、GLM 把模型层卷到全球第一档；OpenClaw、灵光、扣子把工程化容器层补齐；阿里、百度、腾讯、字节把超级 App 入口跑起来。三条平行线一起，国内 AI Agent 这一仗，不是「跟上谁」，而是「我们这一代正好赶上了一个完整工具栈在中文社区被认认真真搭起来」。前辈把模型 / 框架 / 平台一层一层趟出来了，今天能在 coze.cn 浏览器里点一下就用上 Agent 全栈员工座舱，是一份很扎实的礼物。下一程值得期待，路在前面，一起加油。

## 参考链接

- [扣子 2.5 重磅升级！给 AI Agent 配云电脑 + 云手机 + 独立邮箱（ChinaZ AI · 4 月）](https://www.chinaz.com/ainews/27013.shtml)
- [扣子 2.5 正式发布！给 AI Agent 配云电脑 + 云手机 + 独立邮箱（玉米小站 · 4 月）](https://www.yumiok.com/archives/6026.html)
- [Coze 2.5 发布：成为 Agent 的网络（腾讯新闻 · 2026-04-07）](https://news.qq.com/rain/a/20260407A04I7W00)
- [扣子 2.5 深度体验：这才是 AI 伙伴该有的样子（腾讯新闻 · 2026-04-10）](https://news.qq.com/rain/a/20260410A026E300)
- [扣子 2.5，开启全新 Agent World！（53AI · 2026-04-07）](https://www.53ai.com/news/zhinengyingjian/2026040724637.html)
- [扣子 2.5 发布 Agent World：给 AI Agent 配一套生活设施（AITOP100 · 4 月）](https://www.aitop100.cn/coze2.5)
- [本养虾人看哭了！字节扣子 2.5 出生即满级（智源社区 · 4 月）](https://hub.baai.ac.cn/view/53911)
- [扣子 2.5 升级：为 AI Agent 提供云电脑、云手机和独立邮箱（A³·爱力方 · 2026-04-10）](https://agentren.cn/2026/0410/12055.shtml)
- [Anthropic Claude Computer Use Tool 文档](https://platform.claude.com/docs/en/agents-and-tools/tool-use/computer-use-tool)
- [Anthropic Claude Agent SDK Python · Releases](https://github.com/anthropics/claude-agent-sdk-python/releases)
- [Introducing Operator · OpenAI 2025-01（MIT Technology Review 报道）](https://www.technologyreview.com/2025/01/23/1110484/openai-launches-operator-an-agent-that-can-use-a-computer-for-you/)
- [Windows Agent Arena · arXiv 2409.08264](https://arxiv.org/abs/2409.08264)
- [ACT-1: Transformer for Actions · Adept 2022-09（OpenAI Operator Wikipedia 收录该时间线）](https://en.wikipedia.org/wiki/OpenAI_Operator)
- [美团 LongCat-Flash-Thinking-2601 发布（美团技术团队 · 2026-01-20）](https://tech.meituan.com/2026/01/20/longcat-flash-thinking-2601.html)
- [上线 4 天下载破百万，蚂蚁 CTO：灵光要做 AGI 时代的支付宝（量子位 · 2025-11）](https://www.qbitai.com/2025/11/355767.html)
- [ERNIE 5.0：A 2.4 Trillion-Parameter Unified Multimodal Foundation Model（百度）](https://ernie.baidu.com/blog/posts/ernie5.0/)
- [扣子 Coze 官网](https://www.coze.cn/)
