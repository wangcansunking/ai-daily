---
title: "千问 agentic RL 不再训崩：浙大、美团、清华出招"
slug: sdar-meituan-zju-tsinghua-agentic-rl-2026-05-16
date: 2026-05-16
weekday: 星期六
category: AI 后训练 / Agentic RL / 国产研究
cover: sdar-meituan-2026-05-16.png
track: arbitrage
track_score: 7.2
domain: agentic-rl
tags:
  - SDAR
  - OPSD
  - GRPO
  - 自蒸馏
  - 千问 Qwen2.5
  - 千问 Qwen3
  - ALFWorld
  - WebShop
  - Search-QA
  - 浙江大学
  - 美团
  - 清华大学
  - agentic RL
description: "SDAR 是浙江大学 REAL Lab + 美团 + 清华联合提交到 arxiv 的一篇 agentic RL 训法论文，HF Daily Papers 5 月 15 日排第 3、58 个 upvote。它解决的是一件很具体的事——多轮 agent 训练里，OPSD 自蒸馏给的 token 级监督经常因为 skill 检索失败而崩。SDAR 的做法是：把 OPSD 当成 GRPO 主干上挂着的辅助目标，由 sigmoid 门控按 token 决定该不该听老师。Qwen2.5/3 系列实测，ALFWorld 平均成功率比 GRPO 涨 9.4 个点，WebShop 准确率涨 10.2 个点，全面打过朴素 GRPO+OPSD 与 RLSD。这是今年 5 月观察到的第 4 篇国内顶级团队主动公开训练复盘的工程文献——从 RL spillover、KV Cache 时序，一直走到 token 级蒸馏门控。"
---

# 千问 agentic RL 不再训崩：浙大、美团、清华出招

![SDAR 浙大美团清华 agentic RL 论文封面](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-16/sdar-meituan-zju-tsinghua-agentic-rl-2026-05-16/sdar-meituan-2026-05-16.png)

5 月 14 日，浙江大学计算机学院的陆正希、姚志远等十一位作者，把一篇标题叫《Self-Distilled Agentic Reinforcement Learning》的论文挂上了 arxiv（编号 2605.15155）。第一作者所属 1 是浙江大学，所属 2 是美团；第二组人马来自清华大学；通讯作者沈永良同样跨浙大与美团。论文页底部注脚一句很朴素的说明：「Work done during internship at Meituan」——这是一篇浙大博士生在美团实习期间，三所顶级机构联合落地出来的工程论文。

5 月 15 日早上，这篇论文出现在 HuggingFace Daily Papers 当日榜的第 3 位，58 个 upvote。HuggingFace 的搜索结果里能看到，提交的作者把代码同步开源到了 `ZJU-REAL/SDAR` 仓库，Apache-2.0 协议，2026-05-14 16:54 UTC 建仓，到本文写作时为止 59 stars / 2 forks——典型的"还没扩散开"的早期状态。

> **本文要回答四件事**：(1) SDAR 到底解决了 agentic RL 训练里的哪个真问题——是 reward 稀疏？是 KL 爆炸？还是别的；(2) 把 OPSD 当辅助目标 + sigmoid 门控这套做法，为什么比朴素 KL 罚或者 RLSD 那种重加权更稳；(3) Qwen2.5-7B 上 ALFWorld +9.4 / WebShop-Acc +10.2 的数字到底是不是工程团队该认真看的差距；(4) 国内做 agent 应用的工程团队——智谱、千问、字节、美团、阿里——能从这套训法里直接拿走什么。

## 一、agentic RL 的两个老问题，被一篇论文同时点了名

先把背景摆清楚。

近一年，大模型后训练的范式已经从「单轮推理 RL」走向了「多轮 agent RL」。区别有多大？单轮推理 RL 里，模型给一道数学题写一段思考，环境给一个对错信号，PPO 或者 GRPO 把这个 trajectory-level reward 摊到每个 token 上，更新策略就完事了。多轮 agent RL 完全不是这样——模型要在 ALFWorld 这种交互式文字游戏里走 10-30 步，每一步生成一段动作，环境返回一段观察，模型再读着观察决定下一步。整条轨迹结束才有一个 reward——成功 +1，失败 0。

这件事的工程难点 GRPO 论文（DeepSeek 那篇）已经讲过：**reward 极度稀疏**。模型走错一步就万劫不复，可能 25 步之后才知道自己第 3 步那个动作选错了，反向传播过来的信号弱到几乎没法学。

OPSD（On-Policy Self-Distillation，去年下半年由清华 Zhao 等人提出）给出过一个看起来很优雅的解法：**自己当自己的老师**。具体做：把同一个学生模型复制一份做"教师分支"，让教师比学生多看一个东西——「特权上下文」（privileged context），比如检索到的相关 skill 文档、参考答案的部分提示。然后在每个 token 上算教师和学生的概率差，把它当成密集的 token 级监督信号。轨迹级 reward 还是有，但每个 token 上都额外有了一个"你这步该往哪边偏"的细粒度提示。

听起来很美，但 SDAR 的论文 Figure 2 给出了一张冷峻的训练曲线：把朴素 OPSD 直接套到多轮 agent 训练上，**KL 散度在前 200 步直接爆掉，任务成功率从 80%+ 跌到趋近 0**。

为什么？SDAR 的作者做了两个观察：

**观察 1：多轮 OPSD 不稳定**。学生一旦走偏到教师没覆盖到的轨迹分支（这在 multi-turn 里几乎是必然），教师此前给的 token 级监督就开始失真，越走越偏，KL 越涨越高，最后崩盘。这不是教师不够强的问题，是**多轮交互天然会让学生漂离教师覆盖区**。

**观察 2：特权监督本身就不对称**。教师拿到的 skill 不一定准——检索来的 skill 可能完全不相关，可能不完整，可能教师压根没读懂怎么用。当教师对某个 token 给出"低于学生"的概率（负 gap）时，到底是这个 token 真的不该出现，还是教师被 skill 干扰判错了？论文用 Qwen2.5-3B-Instruct 跑了一遍统计：**整条轨迹里 50% 以上的 token 都是负 gap**。这不是少数边角情况，是普遍现象。

![SDAR 训法流程：GRPO 主干 + OPSD 门控辅助](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-16/sdar-meituan-zju-tsinghua-agentic-rl-2026-05-16/sdar-method-flow.png)

把两件事拼起来：**多轮场景下，教师给的 token 级监督有一半是噪声，硬学就会被带歪**。这就是 SDAR 要解决的真问题——不是 RL 不够好，也不是自蒸馏没用，是这两套范式硬拼在一起会爆。

## 二、SDAR 的工程选择：把 OPSD 关进 sigmoid 门控

SDAR 给出的方案，用一句话概括：**让 GRPO 留作主干，OPSD 当辅助 loss，每个 token 上的辅助强度由一个 sigmoid 门控按需打开或关闭**。

这套设计在论文里写成一个简单的总损失：

`L(θ) = L_GRPO(θ) + λ_SDAR · L_SDAR(θ)`

`L_SDAR = Σ g_t · (log π_T(y_t | s_t^+) - log π_θ(y_t | s_t))`

`g_t = σ(β · Δ_t)`，其中 `Δ_t` 是教师减学生的对数概率差。

直白翻译：原来的 GRPO 损失不动——这一点很关键，意味着 RL 的优势估计、importance sampling、clip 都保持原样，没有被特权信号污染。OPSD 那一项被单独拎出来当辅助 loss，每个 token 前面乘一个 0 到 1 之间的门控权重 `g_t`，权重的形状是一条 sigmoid 曲线。

![SDAR sigmoid 门控：正负 gap 不对称信号](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-16/sdar-meituan-zju-tsinghua-agentic-rl-2026-05-16/sdar-asymmetric-gate.png)

这条曲线干的事很简洁：

- **正 gap token（教师认可学生这步）**：`Δ_t > 0`，sigmoid 输出接近 1，门控全开，强蒸馏。逻辑是，教师比学生多看了 skill 文档之后给出了更高的概率，说明这个 token 是学生"已经会但还没内化"的对的方向，应该让学生重点学。
- **负 gap token（教师不认可学生这步）**：`Δ_t < 0`，sigmoid 输出接近 0，门控关闭，软衰减。逻辑是，教师对这个 token 给低概率的原因模糊——可能真不该出，也可能教师被 skill 检错误导，所以**软处理，半信半疑，不硬学**。

对比朴素 KL 惩罚（直接把 KL 散度加进 loss）会怎么样？KL 是对称的——教师和学生分布距离大，就一起拉近。问题是负 gap 那部分也被算进去，遇到 skill 检索失败的轨迹，整个 token 序列被强行拉向一个本身就错的教师方向，KL 越拉越大，最后训崩。SDAR 的 sigmoid 门控本质是把"对称的 KL 距离"换成了"不对称的 token 级权重"——同样是教师指挥学生，但学生有权利对教师说"这条我不太信，先听一半"。

这个设计还有一层好处：**梯度只通过学生的 log π_θ 流回去**，教师那一支用 `stop-gradient` detach 掉了。换句话说，门控 `g_t` 是一个 token 级的实数权重，不参与反向传播，只决定该 token 上的辅助蒸馏要不要算、算多少。这让整套训练在工程上仍然是单 forward / 单 backward 的标准 GRPO 流程，外加一个并行跑的教师 forward——计算开销可控，VRAM 占用不会翻倍。

论文还实例化了三种门控变体：

| 门控 | 公式 | 适用场景 |
|---|---|---|
| **熵门控** | `g_t = σ(β · h_t)` | 学生熵高的位置加强蒸馏（不确定时找老师） |
| **gap 门控** | `g_t = σ(β · Δ_t)` | 教师-学生差异大且正向时加强（默认） |
| **Soft-OR 门控** | `g_t = σ(β · [1 - (1-h_t)(1-Δ_t)])` | 熵高或正 gap 任一满足就加强 |

实验里 gap 门控是最稳的默认选项。这一点和工程直觉一致——熵门控会被"学生彻底糊涂"的位置欺骗（高熵不代表教师就对），Soft-OR 门控参数太多在小模型上调起来麻烦。

## 三、Qwen2.5-7B 三 benchmark 实测：差距到底大不大？

数字才是真东西。

![Qwen2.5-7B：SDAR vs 五条基线，三 benchmark 横评](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-16/sdar-meituan-zju-tsinghua-agentic-rl-2026-05-16/sdar-7b-benchmarks.png)

论文 Table 1 给的 Qwen2.5-7B 模型上、三个 benchmark 的成绩：

| 方法 | ALFWorld 平均 | Search-QA 平均 | WebShop-Score | WebShop-Acc |
|---|---|---|---|---|
| Vanilla（不训）| 12.5 | 33.9 | 5.9 | 1.6 |
| OPSD-only | 32.8 | 6.2 | 4.5 | 2.3 |
| **GRPO（最强基线）** | **81.2** | **42.0** | **80.9** | **72.6** |
| Skill-GRPO | 69.5 | 40.3 | 80.4 | 71.9 |
| GRPO+OPSD（朴素拼接）| 80.4 | 47.0 | 86.8 | 76.5 |
| Skill-SD | 85.1 | 47.8 | 86.1 | 76.5 |
| RLSD | 82.0 | 49.0 | 87.4 | 77.3 |
| **SDAR（本文）** | **85.9** | **49.0** | **89.4** | **82.8** |

几个值得拆开看的点。

**第一，OPSD-only 是真崩**。Search-QA 上 6.2、WebShop-Acc 上 2.3——纯做自蒸馏不挂 RL，结果几乎和不训一样。这印证了一件事：在多轮 agent 场景里，自蒸馏从来都不是替代 RL 的方案，它是辅助。SDAR 论文从开头就强调"RL 留作主干"，是有实测支撑的。

**第二，朴素 GRPO+OPSD 没崩，但收益不稳**。WebShop-Score 从 80.9 涨到 86.8（+5.9），WebShop-Acc 从 72.6 涨到 76.5（+3.9）——确实涨了，但论文 Figure 2 的训练曲线显示这套朴素组合在某些超参组合下会爆掉，可复现性差。这是工程团队最痛恨的——能涨但不一定能再训出来。

**第三，SDAR 在 WebShop-Acc 上抬了 GRPO 整整 10.2 个百分点**（72.6 → 82.8）。这是个很硬的数字。WebShop-Acc 衡量的是「最终买到的商品是不是完全匹配任务要求」，是个二值判定，比"分数接近就给部分分"的 Score 严格得多。10.2 个点意味着，原来 100 单里 73 单完全正确，现在能做到 100 单里 83 单完全正确。

**第四，对比 RLSD 这个上一代最强基线，SDAR 在 WebShop-Acc 上还能再涨 5.5 个点**（77.3 → 82.8）。RLSD 是用自分歧重加权 RL 的 token 级优势——逻辑接近但实现路径不同。SDAR 论文 Figure 2 右侧给出了 RLSD 在训练早期的 KL 爆炸现象，证明 RLSD 那种"直接重加权 RL loss"的做法在 teacher-student gap 大的早期阶段不稳。SDAR 把 OPSD 单独拎成辅助 loss + sigmoid 软衰减，正好绕过了这一段。

3B 模型（Qwen2.5-3B-Instruct）上的对应数字是 ALFWorld 平均从 GRPO 的 75.0 升到 SDAR 的 84.4——这就是论文摘要里那个 +9.4% 的来源（注意：摘要中 +9.4 是 3B 上的，+10.2 是 7B WebShop-Acc 上的，混着引用容易记错）。Qwen3-1.7B 上 SDAR 也能稳定打过所有基线，这一档对国内边缘部署场景特别有价值——意味着这套训法不挑参数规模，1.7B 小模型同样吃得下。

## 四、为什么这是值得连读的第四篇训法复盘

把 SDAR 放进 5 月观察到的国内大厂工程文献谱系里看，会发现它不是孤立事件。

![国内大厂主动公开 RL 后训练复盘：四篇连成系统化训法](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-16/sdar-meituan-zju-tsinghua-agentic-rl-2026-05-16/sdar-china-rl-timeline.png)

| 日期 | 出处 | 主题 | 信号意义 |
|---|---|---|---|
| 5/1 | OpenAI Goblins 中文社区译介 | RL spillover 失败模式复盘 | 训练奖励崩了之后的补救范式 |
| 5/2 | 智谱 GLM-5 训练日志 | KV Cache 时序漏洞 | Scaling 训法暴露推理侧问题 |
| 5/11 | DELEGATE-52 工程笔记 | 长 delegated workflow 上下文工程 | agentic 应用层踩坑实录 |
| **5/16** | **浙大 + 美团 + 清华 SDAR 论文** | **token 级 OPSD 门控自蒸馏** | **首篇把 skill 检索失败做成软衰减的公开方案** |

四篇连读下来能看清一件事：**国内顶级团队正在把 RL 后训练从"单点 fix"推到"系统化训法"层面**。

5 月 1 日那篇 OpenAI Goblins 译介讲的是"训崩了怎么救"，是事后补救；5 月 2 日智谱 GLM-5 的复盘是 scaling 暴露的工程漏洞，仍属于"找出来再修"；5 月 11 日 DELEGATE-52 讲的是应用层怎么把长 workflow 撑住，是 prompt engineering 范畴；到了 5 月 16 日的 SDAR，路径明显前移到了**训练目标本身**——不再被动等着出问题，而是在 loss 函数里就把"教师信号不可靠"这件事内置成可调权重。

这种递进对国内做 agent 应用的工程团队意味着什么？

**对智谱、千问、字节、阿里、美团这种自训根基的团队**：SDAR 是一份可以直接复用的工程方案。GRPO 那一支不动，只加一个并行的教师 forward + 一个 sigmoid 门控 + 一个加权辅助 loss。代码已经在 ZJU-REAL/SDAR 仓库 Apache-2.0 开放，verl 框架做基底（也是国产，volcengine 开源），vLLM 0.11.0 / Flash Attention 2.7.4 / PyTorch 2.6.0 / CUDA 12.4 全套依赖明确——A100 / H100 / 国产 910B / 寒武纪 都能跑得起来。

**对应用层的团队**——千问 Code / 通义灵码 / 智谱清言 / 豆包 这些做 coding agent 或者通用 agent 的产品线：SDAR 的核心价值是告诉你「skill 检索质量不再是死扣点」。论文的鲁棒性实验显示，**即便用随机检索给教师送 skill，SDAR 仍然能涨过 GRPO 基线**——因为门控会自动把烂 skill 引入的负 gap token 软掉。这意味着国内做 agent 的团队不需要一上来就堆顶级 RAG / 顶级 skill 库，先把 SDAR 这条训法跑起来，让训练自己适应检索的不完美。

**对学术界**：这是浙大 REAL Lab 把美团实习生项目做到 HF Daily Papers 当日第 3 的案例。三所机构联合（浙大 + 美团 + 清华），arxiv 上挂着、代码已开源、五个月内 push 仍在持续——这种"在海外学术圈先露脸、国内中文媒体随后跟进"的路径，正在成为国产 AI 研究的一种典型扩散模式。本周 HF papers 板上同样的镜像还能再找出几条，国内中文媒体翻译跟进往往滞后 1-2 周。

## 五、值得继续追的几条线

读完论文一遍，还有几件事是这一版没说透、值得国内工程团队接下来去做实测的。

**门控温度 β 的实战取值**。论文给的 β 是固定值（实验里多用 1.0），但实战上 β 应该不应该随训练进度自适应？早期 teacher-student gap 大、负 gap 比例高，β 偏小（让门控更平滑）可能更稳；中后期 gap 收敛、信号更可靠，β 加大（让门控更陡）可以提升辅助蒸馏的强度。这是个值得国内团队复现时跑 ablation 的方向。

**Qwen3-Coder / DeepSeek-V3 / GLM-5 这些更大根基上的可移植性**。论文只测到 Qwen2.5-7B + Qwen3-1.7B 两档，更大尺度上 SDAR 的稳定性怎样？教师分支需要的额外显存在 70B+ 上是不是真扛得住？这要看国内团队（特别是有自训能力的智谱、阿里、字节）接下来的实测。

**真实 agentic 场景的迁移**。ALFWorld / WebShop / Search-QA 都是学术 benchmark，真实业务里的 agentic 任务（比如美团那种带订单状态机的电商 agent、千问 Code 那种带文件树状态的 coding agent）reward 形态、轨迹长度、skill 检索来源完全不同。SDAR 论文挂着美团两位资深作者（顾琦、蔡勋良），不出意外这套训法已经在美团内部跑过一遍真实场景——下一篇值得等的，是美团接下来公开"SDAR 在真实订单 agent 上的复盘"。

**与 DEMYAgent / RLAnything 等 ICML 2026 同期方案的横评**。这两套是 ICML 2026 阶段同样在做 agentic RL 训法的工作（搜索结果里出现的 Gen-Verse/Open-AgentRL 项目）。技术路径有重叠也有差异，独立的横评对国内团队挑选训法很重要。

---

读到这里，关于"国内 agent 训法到底差在哪"的画像也清楚了一些：**不是没有训法，是训法在被一点点系统化**。从去年 GRPO 单兵作战，到今年 5 月连续四篇国内大厂公开复盘，再到 SDAR 这种把 OPSD 门控并入 RL 主干的 token 级训法——国内做 agent 的工程团队已经从"应用层调 prompt"逐步往"训练层改 loss 函数"的方向迁移。

这件事的意义可能比单一篇论文大得多。它意味着国内顶级团队开始**主动公开训练复盘**而不是把方法压在内部，意味着浙大、清华、美团这种学术 + 产业组合开始**用 HF Daily Papers 这条海外渠道做学术声誉**——回到国内中文圈、对国内开发者做技术输出，往往是这条路径的下一站。

对于每天用千问 / DeepSeek / GLM 做 agent 应用的国内开发者来说，2026 年下半年的训法节奏，大概率就是沿着 SDAR 这条线再走两到三步。这一篇论文打开了「token 级蒸馏门控」这个全新的工程开关，接下来谁先把它和 Qwen3-Coder、DeepSeek-V3、GLM-5 这些更大根基结合起来，谁就拿到了下一代 agentic 模型的早期窗口。
