---
title: "库克最后一场 WWDC·Siri 改用谷歌 Gemini·Claude 第一次上 iPhone | AI 日报 | 2026-06-09"
date: 2026-06-09
weekday: Tuesday
slug: 2026-06-09
category: daily-report-newsletter
cover: "09.png"
cover_style: cinematic
image_alt_match_ignore: [daily-2026-06-09-chatbot-share-shuffle.png, daily-2026-06-09-wwdc-three-things.png, daily-2026-06-09-cn-cost-three-accounts.png]
description: "库克谢幕 WWDC：新 Siri 底层换用谷歌定制约 1.2 万亿参数 Gemini、运行走端侧与谷歌云分层路由（最重推理据报道落在谷歌云英伟达 B200 上），多 AI 扩展让 Claude 第一次成为 iPhone 可选助手，库克 9 月 1 日转任执行董事长由 John Ternus 接任。国产大模型靠稀疏激活、稀疏注意力、国产算力三笔技术账把 API 价格和长上下文成本一起打下来，MiniMax M3 在百万上下文下单 token 计算量约为上一代的二十分之一。6 月模型发布窗口集中开启，全球聊天助手份额洗牌：ChatGPT 降到 54.7%、Gemini 升到 27.4%、Claude 单季涨 306%。"
---

# 库克最后一场 WWDC·Siri 改用谷歌 Gemini·Claude 第一次上 iPhone | AI 日报 | 2026-06-09

![库克谢幕 WWDC·新 Siri 换用 1.2 万亿参数定制 Gemini·Claude 第一次成为 iPhone 可选助手](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/daily/09.png)

## 📋 头版目录

- 🍎 太平洋时间 6 月 8 日上午，库克在苹果园区开完最后一场 WWDC 主题演讲 → 头条 1
- 🧠 重做的 Siri 底层换成谷歌定制约 1.2 万亿参数 Gemini，运行走端侧与谷歌云分层路由 → 头条 1
- 🛠 苹果上线多 AI 扩展，用户可在 Gemini、ChatGPT、Claude 三家里挑给 Apple Intelligence 用 → 头条 1
- 🤖 Claude 第一次成为 iPhone 内置可选助手，触达约 22 亿台苹果设备 → 头条 1
- 👔 库克 9 月 1 日转任执行董事长，硬件主管 John Ternus 接任 CEO → 头条 1
- 🇨🇳 国产大模型把 API 价格和长上下文成本一起打下来，背后是三笔技术账 → 头条 2
- 💰 MiniMax M3 在 100 万上下文规模下，单 token 计算量约为上一代的二十分之一 → 头条 2
- 🔬 DeepSeek 稀疏注意力、Kimi 线性注意力、千问长文外推，路线各异目标一致 → 头条 2
- 🧠 6 月模型发布窗口集中开启：Gemini 3.5 Pro、Claude Sonnet 4.8、Grok 同期临近 → 头条 3
- 📊 全球聊天助手份额洗牌：ChatGPT 降到 54.7%，Gemini 升到 27.4%，Claude 单季涨 306% → 头条 3
- 🚀 SpaceX 定于 6 月 11 日定价、6 月 12 日上市，代码 SPCX，含 xAI 业务 → 快讯
- 🏭 微软 Foundry 模型目录扩到 1.1 万个，Claude Opus 4.8 进 Excel 智能体模式 → 精选要闻
- ⚖️ 美国科罗拉多 AI 法 6 月 30 日生效，欧盟 AI 法 8 月 2 日进入处罚期 → 精选要闻
- 🛡 民间机构点名三大聊天助手 37 处操纵性设计，集中在情感依赖与能力夸大 → 精选要闻
- 🇨🇳 第八届智源大会本周 6 月 12-13 日在北京开幕，国产 AI 一线掌舵人集中发声 → 国内 AI
- 🇨🇳 阿里千问 Qwen3.7 系列智能体编程达 72.3%，位列国产第一 → 国内 AI
- 🔥 个人助手 OpenClaw 越过 37.7 万星，仍是个人 AI 助手里最大的开源项目 → GitHub Trending
- 🔥 给智能体配技能的 superpowers 约 22.1 万星，anthropics/skills 约 14.8 万星 → GitHub Trending
- 🛠 Codex 把多天无人值守自动化做成主线，Cursor 调整团队定价并接入 Jira → AI Coding
- 🎙 苹果把模型质量绑在谷歌身上，开发者在争这步棋是务实还是认输 → 名人说

## 🔥 头条深度

### 头条 1 · 库克谢幕这天，苹果把 Siri 交给谷歌、把选择权交还用户

太平洋时间 6 月 8 日上午十点，蒂姆·库克（Tim Cook）走上苹果园区的舞台，开完了他作为苹果 CEO 的最后一场 WWDC 主题演讲 [1]。这场发布会被外界盯了一年，焦点只有一个：那个被吐槽了两年、几次跳票的 Siri，到底重做成了什么样。答案出来了——苹果没有自己硬扛前沿模型，而是把 Siri 的底层换成了谷歌的定制 Gemini，同时把"用哪家模型"的选择权交回到了用户手里。

这是一场信息量很大的发布会。它既是一次产品更新，也是一次战略表态：苹果承认自己短期内做不出第一梯队的大模型，于是选择把硬件、系统和隐私这几张牌打到极致，把模型这一层外包出去，但用一套可替换的扩展机制，避免把命门完全交到一家手里。

#### 1.1 新 Siri：底层是 1.2 万亿参数的定制 Gemini，跑在苹果自己的服务器上

重做的 Siri 拥有了系统级的上下文理解和屏幕感知能力，能读懂你正在看的邮件、照片和界面，对话也比过去自然得多 [1]。你还能调 Siri 的音色——语调、语速、音高都能自己设。形态上，新 Siri 更接近一个独立的聊天助手 App，而不是过去那个一问三不知的语音助手。

底层模型来自谷歌一套定制的约 1.2 万亿参数 Gemini，苹果为此每年向谷歌支付约 10 亿美元 [2]。运行方式上，据《The Information》等报道，苹果采用分层路由：简单请求在端侧处理，一部分走苹果自己的私有云计算（Private Cloud Compute），而这个万亿参数模型最重的推理，实测在私有云计算上跑太慢，最终路由到谷歌云上的英伟达 B200 芯片，由苹果做加密与隐私代理 [26]。也就是说，谷歌提供模型权重、最重的计算也落在谷歌云硬件上，苹果想用加密代理这层尽量把"用谷歌模型"和"数据敞开给谷歌"分开——这层隐私边界到底有多硬，要看落地后的实际细节。

![库克谢幕 WWDC·新 Siri 底层换用谷歌定制约 1.2 万亿参数 Gemini·最重推理路由到谷歌云英伟达 B200](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/daily/source-wwdc-keynote-siri-gemini-2026-06-09.png)

#### 1.2 多 AI 扩展：Claude 第一次成为 iPhone 的内置选项

比"换谷歌"更值得开发者关注的，是苹果同时上线的多 AI 扩展机制。用户可以在 Gemini（默认）、ChatGPT 和 Claude 三家之间选一个，交给 Apple Intelligence 使用 [3]。这是 Claude 第一次以内置可选项的身份进入苹果设备——此前它只能靠第三方 App 在 iPhone 上露面。

苹果全球有约 22 亿台在用设备 [3]。哪怕只有一小部分用户主动把助手切到 Claude，对 Anthropic 来说也是一个此前完全够不到的入口。结合今日「Claude 进 Excel：子智能体互通，账单记你头上」专题里 Anthropic 在微软 Foundry 与 Office 的同步推进，能看出一条清晰的路线：Anthropic 正在把 Claude 塞进用户每天已经在用的入口，而不是只守着自己的 App 和 API。

苹果这套设计也藏着自己的算盘：把模型做成可替换的扩展，意味着今天底层是 Gemini，明天若有更强的模型出现，理论上可以换。它不想重蹈"把搜索默认权一卖几十年"的旧路，而是给自己留了腾挪的余地。

#### 1.3 库克交棒：9 月 1 日起 John Ternus 接任 CEO

这场发布会还有一层告别的意味。苹果今年 4 月 20 日已经宣布，库克将于 9 月 1 日转任执行董事长，由现任硬件工程高级副总裁 John Ternus 接任 CEO [4]。WWDC 是这次交接的第一个公开节点，库克在主题演讲结尾的视频里留了一段告别话。

Ternus 2001 年加入苹果，一路在硬件部门成长，经手过 iPad、AirPods、Apple Watch 和多代 iPhone [4]。他本人没有出现在这次主题演讲里，但他将接手的，正是一家把模型层外包、把赌注押在硬件与系统体验上的苹果。从这个角度看，新 Siri 既是库克任内的收尾之作，也是 Ternus 时代的起手式。

![库克最后一场 WWDC 新 Siri 做了三件事·换底层模型为定制 Gemini·多 AI 扩展三选一·库克交棒 Ternus](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/daily/daily-2026-06-09-wwdc-three-things.png)

**产业含义**：苹果这步棋，给所有做端侧助手和应用层的团队提了个醒——在模型这一层，"自研到底"不是唯一正解。手握分发入口和用户信任的一方，完全可以把模型当成可替换的供应方，自己专注在隐私架构、系统集成和体验上。对国内做 AI 硬件和系统级助手的团队，这是一个可以直接对照的样本：你的护城河到底是模型，还是模型之上的那一层。

### 头条 2 · 国产大模型把价格打下来，靠的是三笔实打实的技术账

如果说海外这一周的主线是苹果选模型，那国内这一周的主线就是国产大模型集体把价格和长上下文成本往下压。表面上看是新一轮 API 降价，但细究起来，降价能站得住，靠的是三笔实打实的技术账，而不是单纯烧钱补贴。今日「DeepSeek 把输入价压到每百万 token 1 元：国产模型降价背后的技术账」与「长上下文的胜负手，下沉到了注意力这一层：DeepSeek NSA、MiniMax MSA 与国产稀疏注意力路线之争」两个专题把这几笔账算得很细，这里把主线提出来。

#### 2.1 第一笔账：稀疏激活，每次调用只算一小部分参数

第一笔账来自模型结构。国产主力模型普遍走混合专家（MoE）路线，总参数动辄几千亿，但每次推理只激活其中一小部分。以国内团队的做法为例，一个总参数两三千亿的模型，每个 token 真正参与计算的可能只有百分之几。参数规模撑住了能力上限，激活比例压住了单次计算成本——这是降价的第一块地基。

![国产大模型把价格和长上下文成本一起压低·MiniMax 稀疏注意力是其中一条主线](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/daily/source-github-minimax-m3-2026-06-09.png)

#### 2.2 第二笔账：稀疏注意力，长上下文不再按平方涨

第二笔账来自注意力。长上下文最烧钱的地方在于，标准注意力的计算量大致随上下文长度的平方增长，文档一长，成本就失控。国产团队这一年集中在这里下功夫，路线还各不一样：DeepSeek 把"该看哪些 token"做进训练里（NSA），上线版本则用了更克制的一手（DSA）；MiniMax 在 M2 一度退回全量注意力，到 M3 又重押稀疏；Kimi 则把线性注意力和全量注意力按大约 3:1 混着用；千问把功夫下在推理期的长度外推上。

效果是实打实的。MiniMax 在 6 月初发布的新一代通用模型 M3 上，官方给出的数字是：在 100 万上下文规模下，单 token 的计算量约为上一代的二十分之一 [5]。长上下文从"用得起但贵"变成"可以随便用"，这一笔账直接决定了长文档、长对话这类场景的定价能压到多低。

#### 2.3 第三笔账：国产推理引擎与算力，把每张卡的成本往下压

第三笔账来自工程和硬件。同样一张卡，靠更好的推理引擎把吞吐拉高一个数量级，单位 token 的折旧成本就摊薄了；再叠加国产算力把采购和折旧往下压，最后一段成本也被吃掉。三笔账叠在一起，国产大模型这一轮的低价才不是赔本赚吆喝，而是有结构支撑的。

![国产大模型把价格打下来的三笔技术账·稀疏激活只算一小部分参数·稀疏注意力压住长上下文·国产算力压采购折旧](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/daily/daily-2026-06-09-cn-cost-three-accounts.png)

**产业含义**：对国内开发者来说，这件事的意义不在"又降价了"，而在长上下文这个过去舍不得用的能力，正在变成可以放开手脚用的基础能力。RAG、长文档处理、长周期智能体这些场景，过去要精打细算地省 token，现在的成本曲线已经不一样了。这三笔技术账也解释了一个常被问到的问题：国产模型的低价到底能不能持续——只要这三层还在往前走，价格就有继续下探的空间。

### 头条 3 · 六月模型发布窗口集中开启，聊天助手份额正在重新洗牌

苹果选 Gemini 这件事，放回更大的背景里看会更清楚：6 月本就是一个模型发布密集的窗口，而支撑各家模型的用户盘子，正在悄悄换位。

#### 3.1 四条模型线挤在同一个窗口

按目前确认和高可信度的消息，6 月这一个月里挤着好几条模型发布线：谷歌的 Gemini 3.5 Pro（在 Google I/O 上由皮查伊确认"下个月给你们"）、Anthropic 的 Claude Sonnet 4.8（npm 包名泄露佐证）、以及预览中的 Claude Mythos（已向 200 多个机构、15 个以上国家开放），xAI 的新模型也在临近 [6]。Gemini 3.5 Flash 已经在 5 月底正式可用，跑出每秒约 284 token 的速度，定价为每百万 token 输入 1.5 美元、输出 9 美元 [6]。

对开发者来说，这意味着 6 月底之前，主力模型很可能要重新评测一轮——这也是为什么这个月特别值得盯着发布日历。

#### 3.2 用户盘子换位：ChatGPT 让出份额，Claude 增速最快

更有意思的是用户这一侧。最新一期统计里，全球聊天助手的份额已经和一年前大不一样：ChatGPT 占 54.7%，相比 2025 年 2 月的 76.5% 明显回落；谷歌 Gemini 升到 27.4%，半年涨了 104%；Claude 全球占 8.2%、在美国市场占 12.5%，单季增速达 306%；DeepSeek 占 4.1%，Grok 占 2.8% [7]。

![全球聊天助手份额洗牌·ChatGPT 降到 54.7%·Gemini 升到 27.4%·Claude 单季涨 306%](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/daily/daily-2026-06-09-chatbot-share-shuffle.png)

把这两件事放一起看就清楚了：苹果把默认助手交给份额已经升到第二的 Gemini，是顺着大势走；而它愿意把 Claude 也放进可选项，是因为 Claude 是增速最快的那个。份额数字背后，是各家在分发入口上的争夺——谁能进到用户每天已经在用的地方，谁就能把增速换成留存。

**产业含义**：模型发布密集叠加用户份额换位，给应用层团队的提示是：底层模型正在变成一个会持续轮换的供应层，今天最强的明天未必是。把产品架构做成"模型可替换"，比押注某一家更稳妥——这恰恰也是苹果这次的选择。

## ⚡ 快讯速览

- **SpaceX 定档 6 月 12 日上市，代码 SPCX**。据公开报道，SpaceX 定于 6 月 11 日定价、6 月 12 日开始交易，目标在 1.75 万亿美元以上估值募资约 750 亿美元，含旗下 xAI 业务 [8]。这笔交易若落地将刷新单笔 IPO 规模纪录，xAI 的模型研发投入也会随之进入公开披露视野——后续要看招股材料里对 AI 业务的口径怎么写。

- **微软 Majorana 2 量子芯片公布**。微软发布新一代拓扑量子芯片 Majorana 2，官方称可靠性较前代提升约 1000 倍 [9]。量子计算离日常 AI 工作流还很远，待观察的是这条拓扑路线能否在纠错环节真正跑通。

- **谷歌 Gemini 3.5 Pro 仍未正式上线**。皮查伊在 Google I/O 上确认"下个月给"，但截至目前正式版尚未发布 [6]。市场普遍预期 6 月底前落地，确切日期未公布——这是本月模型日历上最大的一个待办项。

- **Anthropic 推出企业合作伙伴计划**。Anthropic 上线规模约 1 亿美元的企业合作伙伴计划，面向系统集成商与咨询公司，分层评审时点定在每年 1 月、7 月等节点 [10]。对想接 Claude 做企业落地的服务商，这是一个新的合作通道，具体准入门槛还需看后续细则。

- **五大苹果系统同步进 27 代**。iOS 27、macOS 27、iPadOS 27、watchOS 27、visionOS 27 在主题演讲后同步放出 Beta 1，支持机型收紧到 iPhone 12 及之后，iPhone 11 被排除在 Apple Intelligence 之外 [1]。老机型用户能否通过云端方式用上新 Siri，官方暂未明确。

## 🎙 名人说 & X 热议

苹果把 Siri 底层交给谷歌定制 Gemini 之后，开发者社区分成了两派，争的不是技术细节，而是这步棋的性质。

一派认为这是务实。苹果在前沿模型上确实落后，与其硬扛一个二线模型砸用户体验，不如承认差距、把谷歌最强的能力借过来，再用自己的加密代理和系统集成把隐私和体验这两张牌打满。模型做成可替换的扩展，等于给未来留了换供应方的口子，并没有把命门彻底交出去。

另一派的担心也很实在：苹果等于把 Siri 的能力上限，长期绑在了谷歌模型的进步节奏上。今天能换，不代表明天换得动——一旦用户习惯、数据格式、调用链路都围着 Gemini 长起来，迁移成本会越堆越高。这是苹果第一次在如此核心的能力上，公开承认自己依赖一个直接竞争对手。

两种判断其实指向同一个真问题：在大模型时代，一家手握十亿级分发入口的公司，自研模型这条路到底该走多深。苹果给出的答案是"够用就好，重心在别处"，这个答案对错与否，要等 Ternus 时代用产品来回答。

## 📰 精选要闻

- 🔴 **微软 Foundry 模型目录扩到 1.1 万个，Claude 进 Excel 智能体模式**。微软把 Foundry 模型目录扩充到 1.1 万个以上，Claude Opus 4.8、Sonnet 4.5、Haiku 4.5 进入公开预览，Claude 还接入了 Excel 的智能体模式 [11]。这件事对每天用 Office 的人影响最直接，完整拆解见今日「Claude 进 Excel：子智能体互通，账单记你头上」专题，里面把调用与计费的细节讲透了。

- 🟡 **美国科罗拉多 AI 法 6 月 30 日生效，欧盟 AI 法 8 月 2 日进入处罚期**。科罗拉多 AI 法将于 6 月 30 日生效，覆盖招聘、医疗、金融、教育、住房、法律等高风险场景，年收入 2500 万美元以下企业有宽限期 [12]。欧盟 AI 法则在 8 月 2 日进入处罚期，最高可罚 3500 万欧元或全球年营业额的 7%，算力达到 10 的 25 次方 FLOPs 以上的通用模型要承担额外义务 [13]。做出海产品的团队需要把这两个时间点记进合规日历。

- 🔵 **民间机构点名三大聊天助手 37 处操纵性设计**。美国民主与技术中心（CDT）发布报告，在 ChatGPT、Claude、Gemini 上识别出 37 处操纵性的产品设计，集中在最大化使用时长、制造情感依赖、夸大能力三类 [14]。这份报告在多家公司临近上市的节点放出，给产品设计敲了个警钟——哪些"提升留存"的手法会被算作操纵，正在被重新划线。

- 🟡 **五角大楼测试用 OpenAI、谷歌模型替换 Claude**。据报道，美国国防部正在测试 OpenAI 和谷歌的模型，以评估是否替换其保密系统中现用的 Claude [15]。Anthropic 凭借在网络安全方向的项目积累了一定信任，但其安全优先的定位在军事场景里反而可能成为限制——这件事后续怎么走，是观察大模型与政府采购关系的一个窗口。

## 🇨🇳 国内 AI 观察

- **第八届智源大会本周开幕（6 月 12-13 日）**。第八届北京智源大会本周在北京举行，按议程设 1 场主论坛加多场平行论坛，集结一批国产 AI 企业掌舵人和多位学界代表，具身智能与世界模型是今年的重点议题 [16]。这是国产 AI 一线团队集中发声的窗口，新模型与新方向大概率会在会上释放，值得本周持续关注。

- **阿里千问 Qwen3.7 系列主攻智能体编程**。阿里通义千问 Qwen3.7 系列包含万亿参数 MoE 架构的 Qwen3.7-Max-Preview 与 35B 稠密架构的 Qwen3.7-Plus-Preview，在阿里云峰会发布，主打面向智能体时代的全能基座，把重点压在智能体编程与长周期自主执行上 [17]。结合头条二里那三笔技术账，能看出国产模型这一轮的重心很清楚：把智能体编程和长上下文这两件事做扎实。

## 📦 GitHub Trending

- 🔴 **openclaw/openclaw**｜约 37.77 万星｜TypeScript [18]。个人 AI 助手里最大的开源项目，跨操作系统、不绑定模型，能接各家大模型并在文件、浏览器、脚本和常见聊天工具之间替你跑任务。它从年初爆红一路涨到今天，仍是"个人 AI 助手"这个方向的标杆，值得长期跟踪它的版本节奏和社区采纳。

![个人助手 OpenClaw 越过 37.7 万星·跨平台不绑定模型的个人 AI 助手](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-09/daily/source-github-openclaw-2026-06-09.png)

- 🔴 **obra/superpowers**｜约 22.14 万星｜Shell [19]。给智能体配技能的框架，把一套可复用的开发方法论做成智能体能直接调用的技能集，是 Claude Code 这类工具的技能层里最大的一个。

- 🔴 **anthropics/skills**｜约 14.8 万星 [20]。Anthropic 官方的 Agent Skills 仓库，收录可复用的智能体技能。配合 superpowers 一起看，能感受到"给智能体配技能"这个方向这一个月有多热。

- 🟡 **deepseek-ai/DeepSeek-V3**｜约 10.37 万星｜Python [21]。DeepSeek V3 的开源仓库，国产开源大模型里星标最高的项目之一，长上下文与稀疏注意力的多项工作都从这里发端。

- 🟡 **modelcontextprotocol/servers**｜约 8.69 万星 [22]。MCP 官方 server 集合，随着各家工具普遍接入 MCP，这个仓库是看协议接入面扩张最直接的一个口子。

## 🛠 AI Coding 工具动态

- **OpenAI Codex 把多天无人值守自动化做成主线**。Codex 2026 年的招牌能力是跨小时、跨天的长任务：可以在无人盯着的情况下连续运行，断点续跑。截至今年 4 月，Codex 周活约 400 万，默认用 GPT-5.5、上下文窗口 40 万 token，在云端、IDE、浏览器和命令行四处都能跑，已有 90 多个一方插件 [23]。

- **Cursor 调整团队定价并接入 Jira**。Cursor 在 6 月重整了团队版定价：标准席位按年付每席每月 32 美元，新增高级席位每席每月 96 美元、用量为标准席的 5 倍；同月还上线了 Jira 集成 [24]。从形态看，编程智能体这一层的竞争确实已经从"像不像 agent"挪到了价格与协作这些更实在的地方。

- **Claude Code 持续打磨动态工作流**。Claude Code 在 5 月底加入动态工作流（Dynamic Workflows）后，6 月初继续做可靠性修复，新增 `/plugin list` 命令按条件列出已装插件，以及一个"按 c 复制并保留 markdown 格式"的小快捷键 [25]。这些更新看着不大，但都是高频使用里会真切感受到的体验改善。

## 🔭 值得关注

- **SpaceX 上市后 xAI 的账本将进入公开视野**。SpaceX 若在 6 月 12 日如期上市，旗下 xAI 的研发投入与现金消耗会随披露材料浮出水面。马斯克的 AI 业务过去靠 SpaceX 的资源腾挪，上市后这部分账目能否继续模糊处理，是否会披露 SpaceX 现金被 AI 业务大量占用——这是后续招股与财报里值得盯的点。

- **6 月模型日历仍有多个待办**。Gemini 3.5 Pro、Claude Sonnet 4.8 都在"本月内"的预期里但尚未正式上线，Claude Mythos 还在预览。这一批模型是否能在 6 月底前集中落地，会直接影响下半年应用层选型的基准线，值得逐周核对官方发布动态。

- **苹果对谷歌模型的长期依赖如何演化**。苹果这次把模型做成可替换扩展，给自己留了余地，但用户习惯与调用链路一旦围着 Gemini 长起来，实际迁移成本会怎样，是观察"分发方与模型方"关系的一个长期样本。是否会重演搜索默认权那样的深度绑定，待业内持续观察。

- **欧盟与美国 AI 监管时间点临近**。科罗拉多 AI 法 6 月 30 日生效、欧盟 AI 法 8 月 2 日进入处罚期，两个时间点都很近。对做出海产品的国内团队，高风险场景的合规义务到底覆盖到哪一层，是否需要提前调整模型与数据流程，需要在这两个节点前看清官方执行口径。

## ✍ 编辑说

- **苹果换模型 / 推荐**：如果你在做端侧助手或系统级 AI 产品，这条值得认真读。苹果用一次发布会示范了一种打法——模型层可以外包，护城河放在硬件、隐私架构和系统体验上。读完这条，对你判断"自研模型该走多深"这个问题，会有一个现成的对照样本。

- **国产价格战的三笔技术账 / 推荐**：如果你每天在和 token 成本打交道，这条能帮你把"国产模型为什么能这么便宜"想清楚。结论是它有结构支撑、不是纯补贴，长上下文正在从奢侈品变成基础能力——这会改变你做 RAG 和长周期智能体时的成本预期。

- **Claude 上 iPhone / 关注**：这件事短期内对多数人体验上变化不大，但它标志着 Anthropic 的分发策略转向——往用户已经在用的入口里钻。如果你在做应用层产品，值得关注 Claude 接下来还会进哪些日常入口。

- **聊天助手份额洗牌 / 关注**：ChatGPT 不再一家独大，Gemini 和 Claude 各自上行。这条对做模型选型的人有参考价值：底层供应层正在持续轮换，把架构做成可替换比押注单一家更稳。

- **AI 监管时间点 / 做技术储备**：科罗拉多和欧盟两个监管节点临近。对有出海打算的产品来说，高风险场景的合规义务到底覆盖到哪一层，是这两个生效日之前值得看清的事——这关系到模型与数据流程要不要提前调整。

## 🔗 引用链接

- [1] CNBC：Apple WWDC 2026 现场更新（Siri 与 Apple Intelligence）: https://www.cnbc.com/2026/06/08/apple-wwdc-2026-live-updates.html
- [2] BuildFastWithAI：June 8 2026 AI 新闻汇总（Gemini 授权与私有云计算）: https://www.buildfastwithai.com/blogs/ai-news-today-june-8-2026
- [3] BuildFastWithAI：Claude 进入 iPhone 与多 AI 扩展: https://www.buildfastwithai.com/blogs/ai-news-today-june-8-2026
- [4] AppleInsider：库克最后一场 WWDC 与 John Ternus 接任: https://appleinsider.com/articles/26/06/07/tim-cook-expected-to-head-wwdc-2026-keynote-for-the-last-time
- [5] 证券时报网：MiniMax 发布新一代通用模型 M3: https://www.stcn.com/article/detail/3936246.html
- [6] WaveSpeed：2026 年 6 月 AI 发布窗口梳理: https://wavespeed.ai/blog/posts/june-2026-ai-launch-wave/
- [7] BuildFastWithAI：2026 年 6 月聊天助手份额对比: https://www.buildfastwithai.com/blogs/ai-news-today-june-8-2026
- [8] BuildFastWithAI：SpaceX IPO 定价与上市安排: https://www.buildfastwithai.com/blogs/ai-news-today-june-8-2026
- [9] BuildFastWithAI：微软 Majorana 2 量子芯片: https://www.buildfastwithai.com/blogs/ai-news-today-june-8-2026
- [10] BuildFastWithAI：Anthropic 企业合作伙伴计划: https://www.buildfastwithai.com/blogs/ai-news-today-june-8-2026
- [11] BuildFastWithAI：微软 Foundry 目录与 Claude 进 Excel: https://www.buildfastwithai.com/blogs/ai-news-today-june-8-2026
- [12] BuildFastWithAI：科罗拉多 AI 法生效时间: https://www.buildfastwithai.com/blogs/ai-news-today-june-8-2026
- [13] BuildFastWithAI：欧盟 AI 法处罚期与门槛: https://www.buildfastwithai.com/blogs/ai-news-today-june-8-2026
- [14] BuildFastWithAI：CDT 操纵性设计报告: https://www.buildfastwithai.com/blogs/ai-news-today-june-8-2026
- [15] BuildFastWithAI：五角大楼测试替换 Claude: https://www.buildfastwithai.com/blogs/ai-news-today-june-8-2026
- [16] 智源研究院：第八届北京智源大会（6 月 12-13 日）: https://hub.baai.ac.cn/
- [17] 华尔街见闻：阿里千问旗舰模型发布报道: https://wallstreetcn.com/articles/3764197
- [18] GitHub：openclaw/openclaw: https://github.com/openclaw/openclaw
- [19] GitHub：obra/superpowers: https://github.com/obra/superpowers
- [20] GitHub：anthropics/skills: https://github.com/anthropics/skills
- [21] GitHub：deepseek-ai/DeepSeek-V3: https://github.com/deepseek-ai/DeepSeek-V3
- [22] GitHub：modelcontextprotocol/servers: https://github.com/modelcontextprotocol/servers
- [23] The New Stack：编程智能体形态趋同（Codex 多天自动化）: https://thenewstack.io/ai-coding-tool-stack/
- [24] Lushbinary：2026 AI 编程工具定价对比（Cursor 团队版）: https://lushbinary.com/blog/ai-coding-agents-comparison-cursor-windsurf-claude-copilot-kiro-2026/
- [25] Claude Code 更新日志: https://code.claude.com/docs/en/changelog
- [26] Tom's Guide / The Information 转述：新 Siri 最重推理路由到谷歌云英伟达 B200: https://www.macrumors.com/2026/06/04/apple-siri-rely-on-google-nvidia-chips/
