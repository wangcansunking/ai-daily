---
title: "OpenAI 把 Codex 搬上 AWS · 微软用自研模型接管 Copilot · 国产个人助手开始能替你干活 | AI 日报 | 2026-06-04"
date: 2026-06-04
weekday: Thursday
slug: 2026-06-04
category: daily-report-newsletter
cover: "04.png"
cover_style: isometric
description: "今天三条主线指向同一件事：编程模型的供应正在松绑。OpenAI 6/1 把 GPT-5.5、GPT-5.4 和 Codex 正式上线 AWS Bedrock，这是它第一次让最新一代闭源模型上微软之外的大型云，价格和自营 API 持平、没有加价；微软则在 Build 把自研的 MAI-Code-1-Flash 推进所有 GitHub Copilot 计划，5B 激活的小模型在 SWE-Bench Pro 上 51.2%、把同等难题的词元省掉最多 60%。另一头，开源个人助手 OpenClaw 冲到 37 万星，国产四款个人 AI 助手已经能照着它的能力清单一条条对上。三件事叠在一起是一个让开发者更有底气的方向：你能选的编程模型，从没这么多过。"
---

# OpenAI 把 Codex 搬上 AWS · 微软用自研模型接管 Copilot · 国产个人助手开始能替你干活 | AI 日报 | 2026-06-04

![OpenAI Codex 上 AWS、微软自研模型进 Copilot、国产个人助手三线并进](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/daily/04.png)

## 📋 头版目录

- 🚀 OpenAI 6/1 把 GPT-5.5、GPT-5.4 和 Codex 正式上线 AWS Bedrock，第一次让最新闭源模型上微软之外的大型云 → 头条 1
- 💰 GPT-5.5 在 Bedrock 上每百万词元输入 5 美元、输出 30 美元，与 OpenAI 自营 API 同价、不加价 → 头条 1
- 🛠 Codex 的 App、CLI 与 VS Code / JetBrains / Xcode 插件原样跑，换一朵云不换工作流 → 头条 1
- 🏭 这步背后是 4 月底微软与 OpenAI 修订条款、结束 Azure 独家第三方云的地位 → 头条 1
- 🇨🇳 开源个人助手 OpenClaw 冲到 37 万星，国产四款个人 AI 助手已能照着它的能力清单逐条对上 → 头条 2
- 🇨🇳 国产助手在"装机即用、定时任务、本地隐私"上补齐，差距主要落在长任务稳定性与生态插件 → 头条 2
- 🛠 微软把自研 MAI-Code-1-Flash 推进所有 GitHub Copilot 计划，5B 激活、SWE-Bench Pro 51.2% → 头条 3
- 🧠 MAI-Thinking-1 约 1T 总 / 35B 激活、256K 上下文，SWE-Bench Pro 追平 Claude Opus 4.6 → 头条 3
- 🔬 微软称两款 MAI 核心模型从零训练、不蒸馏第三方、用授权合规数据，Simon Willison 最关心这条 → 头条 3
- 💸 Snowflake 与 Anthropic 在 Summit 上宣布 2 亿美元扩展合作，把 Claude 接入企业数据体系 → 精选要闻
- 🛡 HTTP/2 内存炸弹 CVE-2026-49975 公开，一根家用宽带几秒打满服务器内存，波及全球 88 万站点 → 精选要闻
- 🛠 Snowflake 的 CoCo 智能体（原 Cortex Code）打通 Slack、Excel、VS Code 与 Claude Code → AI Coding
- 🔬 Snowflake Cortex Training 让企业在仓内用自有数据微调 Qwen、Mistral，不必自管 GPU 集群 → 精选要闻
- 🔥 给智能体省词元的 headroom 升到约 9600 星，真实负载上把工具输出压掉九成 → GitHub Trending
- 🔥 OpenClaw 37 万星、ComfyUI 越过 11.5 万星，本地优先与节点式工作流仍是开源主力 → GitHub Trending
- 🇨🇳 国产编码套餐从智谱包月到 MiniMax 全模态按量，今日一篇专题把计费口径拉齐对照 → 国内 AI
- 🖥 AMD 128GB 迷你主机跑开源模型，混合专家比同级密集模型快约十倍，今日专题给出实测 → 快讯
- 🎙 Simon Willison 点评 MAI：在意 35B 激活能追平 Opus 的反差，更在意"授权数据、不蒸馏"能否兑现 → 名人说
- ⚖️ 苹果 WWDC 6/8 开幕在即，新版 Siri 与端侧模型是开发者本周最大悬念 → 值得关注

## 🔥 头条深度

### 头条 1 · OpenAI 把 Codex 和前沿模型搬上 AWS，第一次走出微软的云

![OpenAI 的 GPT-5.5、GPT-5.4 与 Codex 正式上线 AWS Bedrock 的官方公告](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/daily/source-aws-openai-announcement-hero-2026-06-04.png)

> 来源：OpenAI 官网公告页 openai.com [1]，AWS Bedrock OpenAI 产品页 [3]

6 月 1 日，OpenAI 把 GPT-5.5、GPT-5.4 和 Codex 正式上线了亚马逊云的 Bedrock 平台 [1][2]。单看"又多了一个托管入口"似乎平平无奇，但这件事的分量在一句话里：这是 OpenAI 第一次让自己最新一代的闭源模型，上微软之外的大型云。过去几年，Azure 是唯一能跑 OpenAI 前沿模型的第三方云；现在这道门被推开了。

对国内开发者来说，最实在的落点是 Codex 这套编码工作流——它现在能在已经接了 AWS 的团队里原样跑起来，账也算进现成的云预算里。

#### 头条 1.1 · 同一套 Codex，换一朵云照样跑

这次上 Bedrock 的不只是模型权重，还有 Codex 的整套开发界面。OpenAI 明确：Codex 的桌面 App、命令行 CLI，以及 Visual Studio Code、JetBrains、Xcode 三家 IDE 插件，都能直接对接 Bedrock 上的模型 [1][3]。换句话说，团队不需要为"换到 AWS"重学一套工具，原来怎么写代码、怎么让智能体跑长任务，现在照旧。

模型通过 Bedrock 的 Responses API 接入，这是亚马逊新一代推理引擎的统一入口。配套的还有一整套企业控制：身份管理 IAM、私有网络 PrivateLink、安全护栏 Guardrails、静态与传输加密、操作审计 CloudTrail，以及数据驻留——推理过程停留在你选定的区域里，不出境 [3]。对合规要求高的团队，这套东西比"模型本身强不强"更决定能不能上生产。

#### 头条 1.2 · 价格和自营 API 持平，按词元计费、没有人头授权

定价是这次最容易被低估的一条。GPT-5.5 在 Bedrock 上是每百万词元输入 5 美元、输出 30 美元；GPT-5.4 是输入 2.5 美元、输出 15 美元 [2][15]。关键不在绝对数字，而在它和 OpenAI 自营 API 完全同价——亚马逊没有借托管之名加价。

![GPT-5.5 与 GPT-5.4 在 AWS Bedrock 上按词元计费的价格对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/daily/chart-codex-aws-price-2026-06-04.png)

计费模式也对企业友好：按词元计费，没有席位、没有按人头的授权费，花的钱直接算进已有的 AWS 承诺额度里 [2]。对一个已经把账跑在亚马逊云上的团队，这意味着接入 OpenAI 几乎是"加一行配置"的事，不用再单独走一遍采购和对账。值得一提的是，Bedrock 上今年已经排着 DeepSeek V3.2、Qwen3 Coder、GLM 4.7、Kimi K2.5、MiniMax M2.1 这批国产开源模型——OpenAI 这次是挤进了一份国产模型已经在场的可选清单。

#### 头条 1.3 · 背后是微软和 OpenAI 互相松绑

把这条新闻放回更大的背景，它其实是另一根线的另一头。今年 4 月底，微软和 OpenAI 修订了合作条款，结束了 Azure 作为唯一第三方云的独家地位 [1]。一个多月后，OpenAI 就把前沿模型铺到了 AWS——这一步等于把那纸条款落到了产品上。

有意思的是，松绑是双向的：OpenAI 在往微软之外走，微软也在往 OpenAI 之外走（见头条 3 的 MAI 自研模型）。两家曾经深度绑定的公司，正在各自给自己留后路。对开发者而言，这种"互相松绑"是好事——它意味着同一个能力，未来能从更多家、更多云拿到，议价权慢慢往用这一侧挪。

### 头条 2 · 国产个人 AI 助手开始能替你干活了：对标 OpenClaw 的一次能力横评

![开源个人 AI 助手 OpenClaw 的 GitHub 仓库，星标已超过 37 万](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/daily/source-openclaw-github-2026-06-04.png)

> 来源：OpenClaw GitHub 仓库 github.com/openclaw/openclaw [4]

个人 AI 助手这条线今天有个清晰的参照点：开源助手 OpenClaw 的星标实查已经到 37.6 万、分叉 7.8 万 [4]，是过去半年开源世界里增长最猛的项目之一。它定义了一个朴素但难做的标准——跨任意操作系统、任意平台，帮你把日常事务真正办完，而不是只陪你聊天。今天值得说的是：国产的几款个人助手，已经能照着这张能力清单一条条对上了。

#### 头条 2.1 · 装机即用、定时任务、本地隐私，国产这三块补齐了

![国产四款个人 AI 助手对照 OpenClaw 的能力横评矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/daily/chart-cn-agent-matrix-2026-06-04.png)

今日一篇专题把四款国产个人 AI 助手放在一起，逐项对照 OpenClaw 的能力。结论比预期乐观：在"开箱即用"上，国产几款的安装门槛已经降到普通用户能自己搞定；在"定时与常驻任务"上，能挂着后台帮你盯邮件、抓信息、按点执行；在"本地隐私"上，数据不必出本机、敏感信息留在自己电脑里这条，国产做得不比海外差，甚至更贴合国内对数据落地的偏好。

这三块恰恰是个人助手从"玩具"走向"能用"的门槛。一个助手如果装都装不上、关掉编辑器就不干活、用一次就要把你的资料传到云端，它再聪明也进不了日常。国产这一轮把这三块补齐，意味着普通人第一次能在国内环境里，找到一个真的能托付杂活的助手。

#### 头条 2.2 · 差距还在长任务稳定性和插件生态

把话说全：差距仍然存在，主要落在两处。一是长任务的稳定性——让助手连续跑一两个小时的复合任务，国产几款在中途纠错、自我恢复上还不如 OpenClaw 那套成熟，容易在某一步卡住或跑偏。二是插件生态——OpenClaw 背后有一个庞大的社区在持续往里塞工具，能接的外部服务更广；国产几款的可扩展面还窄，遇到冷门需求时自己接工具的成本更高。

但这两处都是"时间能解决"的差距，不是路线问题。对今天的国内用户来说，结论可以很直接：如果你想要一个能办日常杂活、又把数据留在本机的助手，国产已经到了"可以认真用一用"的阶段，不必非等海外项目。完整四款对照见今日「国产个人 AI 助手能替你干完活了吗：四款对照 OpenClaw 横评」专题。

### 头条 3 · 微软把自研 MAI-Code 推进所有 Copilot 计划，给"减少依赖 OpenAI"落了地［跟进］

![微软自研 MAI-Code-1-Flash 进入 GitHub Copilot 模型选择器](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/daily/source-mai-code-hero-2026-06-04.jpg)

> 来源：微软 AI 官网 MAI-Code-1-Flash 介绍页 [5]，Build 2026 主题演讲实录 [7]

昨天的日报已经报过微软在 Build 2026 上一次端出七款自研 MAI 模型；今天这条的跟进点很具体——两款核心模型的完整参数和落地范围都清楚了，而且编码那一款已经从"进选择器"变成"推给所有 Copilot 计划" [5][9]。这给微软"减少依赖 OpenAI"那句话，补上了第一块能上手验证的实证。

#### 头条 3.1 · MAI-Code-1-Flash：5B 激活的小模型，SWE-Bench Pro 51.2% 压过 Haiku

![MAI-Code-1-Flash 与 MAI-Thinking-1 的参数规模与编码基准对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/daily/chart-mai-two-models-compare-2026-06-04.png)

MAI-Code-1-Flash 是一款稀疏混合专家模型，137B 总参数、只激活 5B [5]。它直接在 GitHub Copilot 的真实生产环境上训过，现在向所有 Copilot 计划的个人用户推送，既进了模型选择器，也进了默认的自动选择器——也就是说很多人不主动选，也可能已经在用它。

成绩上，它在 SWE-Bench Pro 上拿到 51.2%，对照 Claude Haiku 4.5 的 35.2%，高出 16 个百分点 [5][9]。更值得开发者注意的是另一条：微软称它在同等难度的问题上，最多能省 60% 的词元。编码模型的真实成本越来越不看单价、而看"干完一个任务烧多少词元"——一个 5B 激活的小模型如果能用更少词元把活干完，对批量铺 Copilot 的企业账单是直接的减法。

#### 头条 3.2 · MAI-Thinking-1：约 1T 总 / 35B 激活，编码追平 Claude Opus 4.6

另一款 MAI-Thinking-1 是微软的首款推理模型，约 1T 总参数、激活 35B，256K 上下文（约 600 页文档）[6]。微软给的成绩：AIME 2025 数学竞赛 97.0%、AIME 2026 94.5%；在 SWE-Bench Pro 上与能力更强的 Claude Opus 4.6 持平；在 1276 项任务的内部盲测里，被用户更偏好于 Claude Sonnet 4.6（这是微软自评口径）。它目前只在 Microsoft Foundry 上对早期合作方私有预览，后续才会在 MAI Playground 公开。

#### 头条 3.3 · 最该盯的不是分数，是"不蒸馏第三方"这句话

把两款放在一起，最该盯的其实不是基准分。微软这次反复强调的是数据来源：两款核心模型从零训练、未蒸馏第三方模型、用商业授权数据，MAI-Thinking-1 还说预训练排除了 AI 生成内容 [5][6]。LLM 工具开发者 Simon Willison 在点评里最在意的也是这条 [8]——35B 激活能追平 Opus 的反差固然抓眼球，但"授权数据、不蒸馏"这句话能不能经得起推敲，才决定这批模型是不是真的"干净自研"。这一点眼下只有微软的说法，外部还无法独立核验，值得继续盯。

## ⚡ 快讯速览

- **AMD 128GB 迷你主机跑开源模型，混合专家比密集快十倍**：AMD Strix Halo（Ryzen AI Max+ 395，128GB 统一内存）这类两千多美元的迷你主机，跑开源模型时混合专家架构的优势被放大——gpt-oss-120b 约 55 词元/秒，而同一档位的 Llama-70B 密集模型只有约 5 词元/秒，差出约十倍。瓶颈在 215GB/s 的内存带宽：激活参数越少、读的权重越少、出字越快。待观察的是这类统一内存迷你主机在更大上下文下的稳定性，今日专题给了四款机器的实测对照。

- **headroom 把智能体的词元开销压掉九成**：开源项目 headroom（实查约 9634 星、Apache-2.0）专门在内容进大模型前先压缩工具输出、日志和检索片段。它公布的真实负载上，代码搜索 100 条结果从 17765 词元压到 1408（省 92%）、SRE 事故日志从 65694 压到 5118（省 92%）。全程本地跑、原文留在本机可还原。待观察的是高压缩率下复杂智能体任务的答案一致性，官方给的是标准基准而非端到端评测。

- **Snowflake 的 CoCo 智能体打通 Claude Code**：Snowflake 把原来的 Cortex Code 智能体改名 CoCo，现在支持桌面与移动端，并打通了 Slack、Excel、VS Code 和 Anthropic 的 Claude Code。对在用 Snowflake 的数据团队，这意味着从仓库到编码助手之间少了几道手动搬运。待观察的是 CoCo 与 Claude Code 之间的上下文如何对齐、权限怎么分。

- **Snowflake Cortex 又添 SpaceXAI 模型**：在 Anthropic、OpenAI、谷歌、Meta、Mistral、DeepSeek 之外，Snowflake Cortex 的可选模型清单又新增了 SpaceXAI。对企业用户，这进一步把"在数据仓内换模型"做成了选项题。待观察的是新模型在受治理的企业场景里的实际调用占比。

## 🎙 名人说 & X 热议

LLM 工具开发者 Simon Willison 在 6 月 2 日点评了微软两款新模型 [8]。他记录得克制：MAI-Thinking-1 是约 1T 参数、35B 激活、只对受邀早期合作方开放的推理模型；MAI-Code-1-Flash 是 137B 参数、5B 激活、专为 GitHub Copilot 打造的编码模型。比起基准成绩，他更把注意力放在微软那句"从零训练、不蒸馏第三方、用授权合规数据"上——这恰恰是一款大厂模型最难自证、外部又最难核验的部分。对开发者，这是一个值得借用的判断角度：当一家公司主动强调数据来源时，真正该追问的不是分数高低，而是这套说法将来能不能拿出可验证的证据。

这背后还有一层行业张力值得记一笔：微软在 Build 上强调自研、减少对 OpenAI 的依赖，而 OpenAI 同期把前沿模型铺上 AWS、走出 Azure 独家。两家昔日深绑的公司各自松绑，谁也不愿把命脉只押在对方身上——这种结构性的"互相留后路"，往往比任何一次模型发布更能说明大厂的真实判断。

## 📰 精选要闻

- 🔴 **Snowflake 与 Anthropic 宣布 2 亿美元扩展合作，把 Claude 接入企业数据体系**：在 Snowflake Summit 2026 上，两家把战略合作扩展到 2 亿美元级别，核心是让企业在受治理的环境里直接调用 Claude，回应越来越强的"可治理、可上生产"的 AI 需求 [10][11]。配套的 Cortex Training 提供托管 GPU，让企业用自有数据在仓内微调 Qwen、Mistral 等开源模型，不必自管大型 GPU 集群。这条对企业 AI 的意义在于：模型、数据和治理被收拢到同一个平面，少了把数据搬来搬去的合规风险。

![Snowflake 与 Anthropic 在 Summit 2026 宣布 2 亿美元扩展合作](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/daily/source-anthropic-snowflake-partnership-2026-06-04.png)

- 🔴 **HTTP/2 内存炸弹 CVE-2026-49975 公开，一根家用宽带几秒打满服务器**：这枚 CVSS 9.8 的拒绝服务漏洞（6 月 2 日公开）波及 nginx、Apache httpd、微软 IIS、Envoy 与 Cloudflare Pingora，全球约 88 万台公网服务器受影响 [12][13]。攻击手法是先往 HTTP/2 的头部压缩表里塞一条大条目，再用成千上万个单字节引用反复指回去，逼服务器分配巨量内存还原头部——在脆弱的 Envoy 部署上约 10 秒就能吃掉 32GB 内存，且只需一根住宅宽带。nginx 在收到通报后 24 小时内出 1.29.8 修复、Apache 5 月 27 日当天就发了补丁，运维今天该做的第一件事是确认自家版本已经更新。

- 🟡 **Snowflake Cortex Training 让企业在仓内微调 Qwen、Mistral**：作为 Summit 的工程侧亮点，Cortex Training 提供托管 GPU 基础设施，企业能用自有数据微调 Qwen、Mistral 这类开源模型，全程不离开 Snowflake 环境 [11]。对国产开源模型是个利好信号——它们正越来越多地出现在国际企业级平台的默认可选清单上。这条值得关注的是：受治理环境下的微调，会不会成为企业落地国产开源模型的主路径。

## 🇨🇳 国内 AI 观察

- **国产编码套餐怎么选，从智谱包月到 MiniMax 全模态按量**：国产几家的编码套餐计费口径差异不小——有的按月打包、有的按量计费、有的把全模态额度也算进来。今日一篇专题把它们放在同一张表里，把"一个月实际花多少、能跑多少任务"的口径拉齐对照，帮开发者按自己的用量挑套餐。对国内开发者，这类横评的价值在于：把藏在不同计费名词背后的真实单位成本摊开看。

- **DeepSeek V4 预览版的规格已经落定**：4 月开源的 V4 预览版里，V4-Pro 是 1.6 万亿总参数、V4-Flash 是 2840 亿参数，都带原生百万上下文与 DSA 稀疏注意力。这套规格目前以预览身份对外，国内开发者已经能拿到权重上手；正式版的确切日期和多模态升级仍待官方进一步披露。

## 📦 GitHub Trending

- 🔴 **openclaw/openclaw**：跨任意操作系统、任意平台的开源个人 AI 助手，实查 37.6 万星、7.8 万分叉，TypeScript 编写 [4]。它定义了个人助手"真的帮你办完事"的标准，也是今天国产几款助手对照的标杆。连续多日高居榜首，本地优先 + 个人智能体这条线的热度还在往上走。

- 🔴 **chopratejas/headroom**：给智能体省词元的压缩工具，实查约 9634 星、Apache-2.0、Python 编写 [14]。它在内容进大模型前先压缩工具输出、日志和检索片段，真实负载上能把代码搜索结果、事故日志压掉约九成，且全程本地跑、原文可还原。智能体记忆与上下文省钱这个题材最近一周持续上榜。

![headroom 在真实负载上把工具输出词元压掉约九成的对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/daily/chart-headroom-token-savings-2026-06-04.png)

- 🟡 **comfyanonymous/ComfyUI**：节点式的图像 / 视频生成工作流仍越过 11.5 万星，是开源生成式工具里最稳的一档。它给用户对生成流程每一步的精细控制，今天仍在趋势榜前列。

## 🛠 AI Coding 工具动态

- **Snowflake CoCo 打通 Claude Code，把数据栈和编码助手接上**：原 Cortex Code 智能体改名 CoCo 后，支持桌面与移动端，并打通了 Slack、Excel、VS Code 与 Anthropic 的 Claude Code。对在用 Snowflake 的团队，从数据仓到编码助手之间的搬运被省掉一截——这是企业数据平台往开发者日常工具里渗透的又一个例子。

- **headroom 给所有编码智能体省词元**：headroom 不绑定某一家助手，它能覆盖 Claude、Codex、Cursor、Aider、Copilot、OpenClaw 等一众工具，在请求送进模型前先压缩上下文。对每天烧大量词元跑智能体的开发者，这类"前置压缩"工具正在成为一个独立且实用的工具层。

## 🔭 值得关注

- **苹果 WWDC 6/8 开幕**：本周最大的悬念在端侧。新版 Siri 会接入什么模型、端侧能力做到哪一步，直接关系到个人 AI 助手这条线的竞争格局。是否会有面向开发者的端侧模型接口开放，待大会现场观察。

- **微软"自研减依赖"能走多远**：MAI-Code 已经推给所有 Copilot 计划，但 MAI-Thinking 还在私有预览。微软到底会把多大比例的 Copilot 流量从 OpenAI 切到自研，是接下来几个月最值得盯的指标。是否会出现"默认就是自研、OpenAI 变成可选项"的格局，待后续版本观察。

- **OpenAI 多云之后的议价格局**：前沿模型上了 AWS，下一步会不会上谷歌云、甚至更多平台，决定了开发者拿同一个能力的渠道有多宽。是否会带动 API 价格的进一步松动，待业内观察。

- **国产开源模型进国际企业可选清单的占比**：Qwen、Mistral 能在 Snowflake 仓内微调，DeepSeek、Qwen、Kimi、MiniMax 已上 AWS Bedrock。这些模型在受治理企业场景里的真实调用占比能涨到多少，是衡量国产开源"出海落地"的硬指标，待平台侧数据进一步披露。

## ✍ 编辑说

- **OpenAI 上 AWS / 关注**：如果你是企业架构或采购角色，这条值得你重新评估"模型供应"这件事——前沿闭源模型不再只能从一朵云拿，意味着选型时多了一层议价和容灾的空间。这对未来 12 个月的云与模型采购决策有实际意义。

- **国产个人助手成熟 / 推荐**：如果你是想给自己或团队找一个能托付日常杂活、又把数据留在本机的助手，今天是一个值得认真试一试的节点。国产几款在装机即用、定时任务、本地隐私上已经够用，差距在长任务稳定性——这决定了你该把它用在哪类任务上。

- **微软 MAI 自研 / 做技术储备**：如果你重度依赖 GitHub Copilot，MAI-Code-1-Flash 可能已经在默认选择器里替你干活了，值得留意它在你实际项目上的表现和词元开销。但"不蒸馏第三方"这类说法目前只有微软口径，外部还无法核验，对它的能力判断建议留一份余地。

- **HTTP/2 内存炸弹 / 关注**：如果你负责线上服务的运维或安全，这枚 CVSS 9.8 的漏洞够格让你今天就去核对一遍 nginx、Apache、Envoy 的版本。它的攻击门槛低到一根家用宽带就能打——这类"低成本高破坏"的拒绝服务，往往比复杂漏洞更值得优先处理。

## 🔗 引用链接

- [1] OpenAI 官宣前沿模型与 Codex 上线 AWS: https://openai.com/index/openai-frontier-models-and-codex-are-now-available-on-aws/
- [2] AWS Bedrock 上手 GPT-5.5 / GPT-5.4 / Codex（AWS 博客）: https://aws.amazon.com/blogs/aws/get-started-with-openai-gpt-5-5-gpt-5-4-models-and-codex-on-amazon-bedrock/
- [3] AWS Bedrock OpenAI 产品页: https://aws.amazon.com/bedrock/openai/
- [4] OpenClaw 开源个人 AI 助手仓库: https://github.com/openclaw/openclaw
- [5] 微软 MAI-Code-1-Flash 介绍页: https://microsoft.ai/news/introducingmai-code-1-flash/
- [6] 微软 MAI-Thinking-1 介绍页: https://microsoft.ai/news/introducing-mai-thinking-1/
- [7] 微软 Build 2026 MAI 主题演讲实录: https://microsoft.ai/news/microsoft-build-2026-mai-keynote-transcript/
- [8] Simon Willison 点评微软新模型: https://simonwillison.net/2026/Jun/2/microsofts-new-models/
- [9] CNBC 报道微软自研模型: https://www.cnbc.com/2026/06/02/microsoft-unveils-new-ai-models-lessen-reliance-on-openai-lower-costs.html
- [10] Snowflake × Anthropic 2 亿美元扩展合作（Anthropic）: https://www.anthropic.com/news/snowflake-anthropic-expanded-partnership
- [11] Snowflake 官方新闻稿: https://www.snowflake.com/en/news/press-releases/snowflake-and-anthropic-accelerate-enterprise-ai-adoption-driven-by-rising-demand-for-governed-ai/
- [12] CVE-2026-49975 漏洞页（Tenable）: https://www.tenable.com/cve/CVE-2026-49975
- [13] CERT/CC 公告 VU#767506: https://kb.cert.org/vuls/id/767506
- [14] headroom 智能体词元压缩工具仓库: https://github.com/chopratejas/headroom
- [15] OpenAI 模型与 Codex 在 Bedrock 正式可用（AWS 机器学习博客）: https://aws.amazon.com/blogs/machine-learning/openai-models-and-codex-on-amazon-bedrock-are-now-generally-available/
