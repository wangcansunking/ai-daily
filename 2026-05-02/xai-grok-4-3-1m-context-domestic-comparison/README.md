---
title: "Grok 4.3 抢下速度第一 国产同档照样上"
slug: xai-grok-4-3-1m-context-domestic-comparison
date: 2026-05-02
weekday: 星期六
category: 海外大模型 · 国产对位
tags: [xAI, Grok, Grok-4.3, 1M 上下文, 千问, DeepSeek, GLM, Kimi]
description: "xAI 4-30 把 Grok 4.3 推上 API：1M 上下文 / Artificial Analysis Intelligence Index 53 分 / 速度榜 #1（艺术分析口径 196 tokens/秒）/ 输入 1.25 美元 + 输出 2.50 美元每百万 token。HN 47972447 上线一晚冲到 350 多 pts、近 500 楼讨论。这一档对国内开发者有多少新意？千问 Qwen3.6 Plus、DeepSeek V4-Pro、GLM-5.1、Kimi K2.6 的同期成绩单已经摆在桌上。"
cover: "xai-grok-4-3.png"
---

# Grok 4.3 抢下速度第一 国产同档照样上

> xAI 4 月 30 日把 Grok 4.3 推上 API：1M 上下文、速度榜 #1、输入价砍到 1.25 美元 / 百万 token。HN 顶贴一晚 350 多 pts。对国内 AI 工程师而言，更值得关注的不是 Grok 4.3 本身，而是它把同档位的价格基线往哪儿拉、千问 / DeepSeek / GLM / Kimi 这一档怎么对位。

![封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-02/xai-grok-4-3-1m-context-domestic-comparison/xai-grok-4-3.png)

## 一、Grok 4.3 这一代到底变了什么

xAI 这次发布走的是「悄悄上 API、文档连夜更新、官方推文一行字」的路线，没有以往那种发布会和长推文连珠炮。HN 47972447 这个帖子是从 Artificial Analysis 的同步页面引出来的，一晚上点进 353 pts、471 条评论，绝大多数讨论围绕三件事：价格、速度、和「这 leaderboard 还能不能信」。

先把硬指标摆出来：

- **上下文**：1M tokens，OpenRouter 列表里同步标注「无输出 token 上限」，超过 200K 总 token 走更高阶价格档
- **价格**：输入 1.25 美元 / 百万 token，输出 2.50 美元 / 百万 token，相比上代 Grok 4.20 输入便宜约 37.5%、输出便宜约 58.3%
- **综合分**：Artificial Analysis Intelligence Index 53 分，全球 154 个模型里排第 10，刚好压在 Muse Spark 和 Claude Sonnet 4.6 之上
- **速度**：196.1 tokens/秒，艺术分析速度榜 #1
- **延迟**：首 token 13.44 秒（推理模型典型水平，思考时间花在前面）
- **类型**：reasoning 模型，extended thinking 默认常开，OpenRouter 文档明确「reasoning 不能关、不能调档」
- **模态**：text + image 输入，text 输出
- **权重**：闭源，参数量 xAI 没公开

最值得说一句的进步项是 GDPval-AA（一个偏「真实工作场景」的 agentic 评测），Grok 4.3 拿到 1500 ELO，比上代 Grok 4.20 0309 v2 的 1179 高 321 分。τ²-Bench Telecom 客服任务做到 98%、IFBench 指令遵循 81%、AA-Omniscience 比上代涨 8 分。Artificial Analysis 自己的总结是：「这是一次以 agentic 表现和成本效率为主轴的迭代」。

但同样要把 caveat 摆出来——速度数字三家口径不一致：

- **Artificial Analysis**：196.1 tokens/秒
- **datalearner 抓包**：206.9 tokens/秒
- **HN 顶赞 mythz 实测**：202.7 tokens/秒（"Ok speed (202.7 tok/s) and value (1.25 -> 2.50) look great"）

差异主要来自不同 prompt 长度、是否计入推理 token、首 token 延迟是否算进吞吐。三个数据都不算错，但写技术决策时不能只挑一个最大的写。HN 上 progbits 也直接吐槽过：「图上写 #1 速度，但旁边的 chart 里又是第 2，到底哪个对？」——这是真实的 leaderboard 数据一致性问题，不是 xAI 单家的锅。

另一条更值得记下的 caveat 来自 BoredPositron：「Grok 总是在免费 token 阶段又快又好，过了那段就降级（"Grok always seems good fast in the free token phase and after that degrades"）。」mythz 也补了一句类似观察：「速度问题在于刚发布前几周很快、之后就慢下来。」对真要把 Grok 接进生产链路的国内跨境团队，这条经验值得在 SLA 里留余量。

## 二、HN 楼里大家在聊什么

挑几条真实 verbatim 引用，看看海外开发者关心的点：

- **artdigital**：「Grok is my favorite model for chatting, and my favorite voice mode. It seems to be the only voice mode that isn't routing to a extremely cheap model.」（语音模式是 Grok 的隐形优势，他认为别家语音背后偷偷路由到 Haiku 那种便宜底座）
- **sundarurfriend**（英语二语者视角）：「As an English-as-second-language speaker, Grok shines at capturing tone and formality in ways other providers don't.」（语气和正式度的把握上 Grok 更细）
- **ragchronos**：「这次 benchmark 看起来 Grok 4.3 跟 Kimi K2.6 在智能和价格上都很接近，正好卡在甜蜜点上。AA-Omniscience 这一项 Grok 比 Kimi 这类开源模型高一些。」
- **simianwords**（对榜单的批判）：「这游戏挺有意思——最有名的 leaderboard 完全是编出来的，但赌注是万亿美金。」
- **netdur**（一句辛辣）：「Musk 在和 OpenAI 对簿公堂时承认 Grok 部分用 OpenAI 模型训练，所以它在性能和成本上跟中国模型应该差不多。」

把这些声音合在一起，能看出海外开发者对 Grok 4.3 的评价是「价格惊喜 + 语音和聊天体验加分 + 但 leaderboard 数字别太当真 + 跨境同质化已经是默认事实」。这跟国内开发者拿到这一档同价位国产模型时的心理状态高度相似。

## 三、国产同档位摆在哪：千问 / DeepSeek / GLM / Kimi 各占一格

把视线拉回国内。Grok 4.3 卡在「1M 上下文 + 1.25/2.50 美元 + reasoning」这一档时，国产同期已经齐了。

![5 家旗舰：1M 上下文同档对位](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-02/xai-grok-4-3-1m-context-domestic-comparison/grok-4-3-vs-domestic-table.png)

**通义千问 Qwen3.6 Plus**——4 月 2 日发布，原生 1M 上下文、最高 65K 输出 token、reasoning 默认常开。SWE-Bench Verified 78.8、Terminal-Bench 2.0 拿到 61.6，agentic coding 这一项压过 Claude 4.5 Opus。阿里官方价格 0.50 / 3.00 美元；OpenRouter 跨境 0.325 / 1.95 美元；TokenMix 给到的 1M 长上下文档位是 0.28 / 1.66 美元——输入价是 Grok 4.3 的四分之一到一半。

**DeepSeek V4-Pro**——4 月 24 日发布，1.6T 参数、1M 上下文、开源权重直接挂在 HuggingFace。价格 1.74 / 3.48 美元，在 1M 上下文长度下单 token 推理 FLOP 只有 V3.2 的 27%、KV cache 内存只占 10%，把 1M 真正做成了生产可部署形态。MRCR 1M 大海捞针 83.5%，超过 Gemini-3.1-Pro。还有一只更便宜的 V4-Flash，0.28 美元 / 百万 token，适合代价敏感的批处理场景。

**智谱 GLM-5.1**——203K 上下文（不及 1M 但够大多数场景），价格 1.40 / 4.40 美元，开源权重。AIME 95.3%、GPQA Diamond 86.2%、SWE-Bench Pro 58.4，最后一项的对位是 GPT-5.4 / Claude Opus 4.6 / Gemini 3.1 Pro 这一档。

**月之暗面 Kimi K2.6**——4 月 20 日发布，1T 参数、256K 上下文、开源权重。SWE-Bench Pro 58.6 跟 GPT-5.5 同分、Humanity's Last Exam（带工具）54.0% 领先；官方 API 0.60 / 2.50 美元，输出价跟 Grok 4.3 完全一致、输入价比 Grok 4.3 还便宜一半。HN 上 ragchronos 那一句「跟 Kimi K2.6 在智能和价格上都很接近」，正是这种横向对比下的自然结论。

把跨家 benchmark 拢一拢看：

![5 家旗舰：核心 benchmark 对位](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-02/xai-grok-4-3-1m-context-domestic-comparison/grok-4-3-benchmark-cross.png)

不同评测口径不能直接画等号，但可以看出几条线：**SWE-Bench / Terminal-Bench 这条 coding 线，Qwen3.6 Plus 反而是国内里最猛的一只**；reasoning（AIME / GPQA）这条线 GLM-5.1 强；超长上下文实战（MRCR）DeepSeek V4-Pro 强；agentic 工具使用 Kimi K2.6 强。Grok 4.3 在 AA-Omniscience 这种「世界知识广度」上有相对优势，但在 SWE-Bench / AIME 这些更被国内重视的硬指标上，目前没有公开数据能压过国产前四。

## 四、国内能不能用 Grok 4.3

xAI 官方 grok.com 和 X 平台在国内大陆都不直接开放，这是公开的客观事实。对真有需要的跨境开发者、出海团队、海外业务线，路径只有几条：

1. **OpenRouter 中转**：OpenRouter 已经上 Grok 4.3，标价就是 1.25 / 2.50 美元，跨境账号 + 海外支付即可走。这是目前最干净的接入方式
2. **xAI 官方 API**：注册门槛在跨境账号和支付，没有 region block 的明确公开声明，但实际可达性视网络条件而定
3. **第三方代理**：风险自负、不建议放生产
4. **Claude Code 等工具内的模型路由**：少数 IDE 工具把 Grok 加进可选项，但要看具体厂商策略

问题是：值不值得为 Grok 4.3 单独打通跨境链路？

如果是**纯中文场景 + 国内业务**，没必要——Qwen3.6 Plus 国内云原生、价格更低、合规零摩擦。如果是**跨境出海 + 英文 / 多语对话 + 客服 agent**，Grok 4.3 在 τ²-Bench Telecom 98% 和 sundarurfriend 那条「英语二语者实测语气好」的反馈，是值得测一轮的差异化能力。如果是**长文档处理（合同 / 论文 / 大代码库）**，DeepSeek V4-Pro 的 1M 上下文 + MRCR 83.5% + 开源权重三件套，在国内能直接落本地，比绕路 Grok 4.3 划算得多。

## 五、把这一代放回更长的镜头里

去年这时候，国内开发者讨论一款海外旗舰发布的口径是「我们什么时候能用上」、「国产差几个月」。今年再看 Grok 4.3 这场发布，氛围已经全变了：

- 4 月一个月，国内连发了 Qwen3.6 Plus（4-2）、Kimi K2.6（4-20）、DeepSeek V4-Pro（4-24）、GLM-5.1（4 月内）四只重量级模型
- 每一只都对应了 Grok 4.3 的某一项专长，价格普遍更低，开源权重的占三只
- 1M 上下文不再是稀缺规格，是 4 月发布的国产旗舰里近一半的标配

xAI 这次把价格往下砍 37%-58%，被 HN 解读为「他们囤的算力比想象中多得多」。同样的判断放到国内，就是 DeepSeek V4-Flash 把 1M 上下文价格砸到 0.28 美元的同样逻辑——算力供给一旦堆上来，价格曲线一定向下走。

对国内 AI 工程师，这意味着两件事。第一，模型选型不再是「找一个最强的用」，而是按场景挑：中文走千问、长文档走 DeepSeek、reasoning 走 GLM、coding agent 看 Kimi 和 Qwen Coder、跨境英文聊天才考虑 Grok。第二，价格预算可以更激进——同一档智能水平，国产价格普遍是海外的三分之一到一半，预算省下来的部分可以买更多并发或更大上下文。

国内 AI 工程师并不缺 1M 上下文模型、不缺反应 200 tps 的推理模型，DeepSeek + 千问 + GLM + Kimi 的组合早就在自己机器或国内云上跑通了。Grok 4.3 这一发，更像是给行业再加一个参考系：选项更多了，议价空间更大了，做工程的人手里牌就多了一张。

---

**资料来源**

- HN 47972447（Grok 4.3 帖子，353 pts / 471 comments，2026-05-01）
- Artificial Analysis：[Grok 4.3 模型页](https://artificialanalysis.ai/models/grok-4-3) + [发布分析文章](https://artificialanalysis.ai/articles/xai-launches-grok-4-3-with-improved-agentic-performance-and-lower-pricing)
- OpenRouter：[Grok 4.3 listing](https://openrouter.ai/x-ai/grok-4.3)
- 通义千问 Qwen3.6 Plus、DeepSeek V4-Pro、智谱 GLM-5.1、月之暗面 Kimi K2.6 各家官方文档与 Artificial Analysis / llm-stats 同期对位
