---
title: "让 LLM 从零重写 ffmpeg：Meta 新 benchmark 出炉"
date: 2026-05-08
slug: programbench-meta-si-lab-rebuild
type: deep-dive
track: overseas-hot
cover: programbench-meta-si-lab-rebuild.png
description: "Meta FAIR + Stanford + Harvard 联合发布 ProgramBench，把 200 个真实开源程序（FFmpeg / SQLite / PHP / DuckDB / ripgrep / lazygit 等）打成一道题：只给二进制和文档，让 agent 从零重建一份等价代码库。Claude Opus 4.7、GPT 5.4、Gemini 3.1 Pro 等 9 个前沿模型同台跑分，无一通关任何一题。HN 首页 125 分 69 评论。"
tags:
  - ProgramBench
  - Meta FAIR
  - Meta Superintelligence Labs
  - LLM benchmark
  - SWE-Bench
  - AI Coding
  - Claude Opus 4.7
  - Ofir Press
---
# 让 LLM 从零重写 ffmpeg：Meta 新 benchmark 出炉

![让 LLM 从零重写 ffmpeg：Meta 新 benchmark 出炉](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/programbench-meta-si-lab-rebuild/programbench-meta-si-lab-rebuild.png)

## 一、5 月 5 日，arXiv 上贴了一道新题

2026 年 5 月 5 日，arXiv 编号 2605.03546 挂上一篇短论文——**ProgramBench: Can Language Models Rebuild Programs From Scratch?** 作者署名横跨美国 Meta FAIR、Meta TBD Lab、Stanford、Harvard 四个机构，第一作者 John Yang 与最后一位 Ofir Press 都是 SWE-Bench 原班人马。

题目本身很短：**只给一个可执行二进制 + 用户文档，让 agent 从零拼出一份行为等价的代码库**。不允许反编译，不允许联网，不允许看原始源码。评测方式不再依赖人类写测试，而是用一个独立 agent 对参考二进制做 fuzzing 自动生成端到端行为测试，再拿这套测试反过来跑 agent 写出来的实现。

5 月 6 日 HN 首页冒出来这条 arXiv 链接，截至 5 月 8 日北京时间早上拿到 **125 分、69 条评论**，r/MachineLearning 当天顶贴位置也是它。讨论焦点不是"模型多强"，而是"这道题是不是太狠了"——9 个前沿模型同台跑分，**没有一个完整通关任何一道题**。最佳成绩是 Claude Opus 4.7，在 200 道题里只有 **3.0% 的题能跑过 95% 的测试**，剩下 97% 的题它连 95% 的行为测试都摸不到。

> 论点先放这里：SWE-Bench 这一代 benchmark 已经被刷得逼近天花板，前沿模型在它上面 60–70 分早就常态。下一代 benchmark 不再是"在已有 repo 里改 bug"，而是直接让 agent 从设计开始重建完整开源程序。ProgramBench 是这个新范式的第一锤。它给业界一个干净的"远未饱和"的标尺，正好接住 SWE-Bench 之后的评测真空。

![ProgramBench 任务设计：从二进制+文档到等价代码库](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/programbench-meta-si-lab-rebuild/programbench-task-design.png)

## 二、ProgramBench 是什么：任务定义与评分方式

### 2.1 输入：只给二进制和文档

每道题包含三样东西：

- 一份参考可执行二进制（reference executable）
- 一份用户向文档（README / man page / CLI usage）
- 一个空的工作目录

agent 拿到的就这些。它要在这个空目录里**架构一个完整代码库**：选语言、选模块划分、选数据结构、写构建脚本、跑通编译、跑通自己的实现。

明确禁止的项有三条：**不许反编译参考二进制、不许联网搜索、不许看上游开源仓库的源码**。这三条把"拷代码 + 改皮肤"这种作弊路径堵死了。

### 2.2 评测：agent 自己 fuzz 出来的端到端测试

ProgramBench 没有让人类来写测试集，而是用一个独立的 fuzzing agent 对参考二进制做行为采样，自动生成端到端测试。每道题中位 **770 条测试**，整个 benchmark 总测试函数数 **248,853**。

得分有两档：

- **% Resolved**——所有测试 100% 通过的题占比
- **% Almost**——至少 95% 测试通过的题占比

这两个数字才是论文摘要里那句"none fully resolve any task, with the best model passing 95% of tests on only 3% of tasks"的真正出处。9 个模型在 % Resolved 这一栏全部挂零；% Almost 这栏只有 Claude 三兄弟和 Opus 4.6 露头。

![ProgramBench 评测流程：fuzzing agent 出题 + 模型答题](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/programbench-meta-si-lab-rebuild/programbench-eval-flow.png)

### 2.3 任务规模：从 212 行到 270 万行

200 道题的代码库规模跨度极大：**最小 212 行，最大 2,701,283 行**。这意味着 agent 既要能拼一个 200 行的 toy CLI，也要能架构一个 270 万行级别的工业软件。论文明确写了："Models favor monolithic, single-file implementations that diverge sharply from human-written code."——大模型倾向写**单文件、巨型文件**的实现，跟人类工程师习惯的多模块结构差异显著。

这条观察值得国内 AI Coding 团队抄进 prompt——多文件 / 模块化是大模型当前的弱项，不是它默认会做的事。

## 三、为什么 SWE-Bench 不够：从"修房子"到"盖房子"

### 3.1 SWE-Bench 已被刷到顶

把时间轴拉一下：2023 年 SWE-Bench Verified 上线，前沿模型解决率从十几个百分点一路爬到 2025 年中段的 65%+，2026 年初 SWE-Bench Pro 出来收紧难度，但前沿模型依旧能拿 50% 上下。**问题不是 benchmark 设计错了，而是模型真的越来越会修 bug**。可它修的依然是别人盖好的房子里的一颗螺丝。

ProgramBench 这篇论文里有一句关键话：**"building a functional application from scratch requires models to engage heavily with software design"**——从头盖一栋房子要的是建筑设计能力，不是螺丝刀技术。这是评测维度上的根本切换。

### 3.2 从"局部 patch"到"全局架构"

下面这张表把两类 benchmark 的差异摆清楚（数据来自论文 §1 与 §2）：

- **任务粒度**：SWE-Bench / SWE-Bench Pro 是"在 N 万行 repo 里改一处 bug 或加一个 feature"；ProgramBench 是"从空目录搭出一个 N 万行 repo"
- **输入信息**：SWE-Bench 给完整源码 + issue 描述；ProgramBench 只给二进制 + 文档，源码不可见
- **测试集**：SWE-Bench 用项目原有的 pytest / unittest；ProgramBench 用 agent 自己 fuzz 出来的端到端测试，纯黑盒
- **顶级模型分数**：SWE-Bench Pro 顶级模型 50%+；ProgramBench 顶级模型 % Resolved 一律 0.0%
- **评测重心**：SWE-Bench 测的是代码理解 + 局部修改；ProgramBench 测的是架构设计 + 长程实现 + 黑盒等价

ProgramBench 不是要替掉 SWE-Bench，而是在它头上加一层。SWE-Bench 测螺丝刀，ProgramBench 测建筑师。两道题缺一不可。

![SWE-Bench vs ProgramBench：两类编码评测的边界](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/programbench-meta-si-lab-rebuild/swe-bench-vs-programbench.png)

### 3.3 私榜不再是问题

这两年业界一个公开秘密：美国三家头部 AI 公司（OpenAI / Anthropic / Google）都在跑自己的内部"长程编码"私榜，但分数从来不公布。原因不是不能公布，而是**没有干净的公开标尺**——你说自己拿了 80 分，外面没办法 reproduce。ProgramBench 把这套评测做成开源 benchmark（Meta FAIR 已经把代码挂到 GitHub），200 道题、248K 测试函数、9 模型全跑一遍带 cost / API call 统计——业界等的就是这种公共标尺。

## 四、谁在领先：9 模型同台跑分

下面这份榜单数据全部来自 ProgramBench 论文 §4 leaderboard 表（按 % Almost 从高到低排）：

- **Claude Opus 4.7**——% Resolved 0.0%；**% Almost 3.0%**；平均 93 次 API call；单题成本 $3.81
- **Claude Opus 4.6**——% Resolved 0.0%；**% Almost 2.5%**；平均 260 次 API call；单题成本 $11.38
- **Claude Sonnet 4.6**——% Resolved 0.0%；**% Almost 1.6%**；平均 475 次 API call；单题成本 $27.09
- **Claude Haiku 4.5**——% Resolved 0.0%；% Almost 0.0%；平均 124 次 API call；单题成本 $0.80
- **Gemini 3.1 Pro**——% Resolved 0.0%；% Almost 0.0%；平均 94 次 API call；单题成本 $1.51
- **Gemini 3 Flash**——% Resolved 0.0%；% Almost 0.0%；平均 89 次 API call；单题成本 $0.33
- **GPT 5.4**——% Resolved 0.0%；% Almost 0.0%；平均 16 次 API call；单题成本 $0.33
- **GPT 5.4 mini**——% Resolved 0.0%；% Almost 0.0%；平均 18 次 API call；单题成本 $0.04
- **GPT 5 mini**——% Resolved 0.0%；% Almost 0.0%；平均 15 次 API call；单题成本 $0.03

![ProgramBench Leaderboard：% Almost 排名与单题成本](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/programbench-meta-si-lab-rebuild/programbench-leaderboard.png)

几条值得注意的判断：

**Claude 家族断层领先**。前三名全部是 Anthropic 模型，% Almost 这一栏从 3.0 一路占到 1.6，再往下就是 0.0%。GPT 5.4 / Gemini 3.1 Pro 两个旗舰在 % Almost 上一题都没拿下。

**长程能力 ≠ 单 token 智商**。GPT 5.4 平均只跑 16 次 API call 就放弃了，Claude Sonnet 4.6 跑了 475 次还在尝试。这道题考的是"愿不愿意一直试 + 能不能持续修正"，更像 agent 长跑能力，不是 zero-shot 推理。

**成本与分数不对齐**。Claude Sonnet 4.6 的单题成本 $27.09 是 Opus 4.7 的 7 倍，但 % Almost 只有 1.6%（Opus 4.7 是 3.0%）。意思是更便宜的中端模型在这种长程任务上不划算——便宜模型的"试错次数"补不上推理深度的差距。

**前沿模型的天花板就是 3%**。这是关键信号：哪怕最强的 Claude Opus 4.7 也只能在 200 道题里搞定接近完成度的 6 道。剩下 194 道题对所有模型来说都是黑盒。这个数字给国产模型留出了非常宽的追赶窗口——不是要追到饱和，而是从 0 起步，只要做到 1–2% 就已经能挤进第一梯队后排。

![200 道题分类与代码规模分布](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/programbench-meta-si-lab-rebuild/programbench-tasks-distribution.png)

## 五、200 道题里都有什么：从 lazygit 到 SQLite

ProgramBench 的 200 道题不是随机抽的，而是按"被工程师每天用、文档完善、行为可观察"三个标准筛出来的。论文 §3.2 给了任务分布，覆盖六大类：

- **语言解释器** —— PHP、Lua、TinyCC（C 编译器）
- **数据库** —— DuckDB、SQLite
- **媒体与压缩** —— FFmpeg、zstd、xz
- **开发者工具** —— ripgrep、fzf、jq、lazygit
- **CLI 工具** —— nnn、gron、以及其余 180 多个
- **系统工具混合** —— 涵盖 C/C++、Rust、Go、Shell、Java、Haskell 多种语言

这份清单的杀伤力在于：每一个程序都是开源世界的硬通货，它们的行为规范几十万开发者每天在用、在测、在依赖。让模型从零重建，不是"刁钻 corner case"，而是"工业现实里最普通的软件"。

最大的题—— FFmpeg、SQLite、PHP 解释器 —— 单题代码库都在百万行量级。Claude Opus 4.7 拿下的那 6 道接近完成的题，按论文图 4 的 cumulative 分布看，集中在 **300–10,000 行**这个区间。意思是：**目前所有前沿模型在小型工具类任务上才有"接近完成"的可能**，超过 5 万行的工业软件，没有任何模型能逼近 95% 行为等价。

## 六、Meta Superintelligence Labs：这道题为什么从这里出

ProgramBench 由 Meta FAIR、Meta TBD Lab、Stanford、Harvard 联合署名，TBD Lab 是 **Meta Superintelligence Labs**（MSL）旗下负责前沿大模型的小组。

MSL 这个 lab 的来历也是 2026 年 AI 圈的大事之一：2025 年 6 月，Meta 出 143 亿美元换 Scale AI 49% 的非投票权股份，把 28 岁的 Alexandr Wang 招进来，配上原 GitHub CEO Nat Friedman，组建一个全新的"超级智能"专项部门。前 OpenAI 首席科学家 Shengjia Zhao（GPT-4o 主要贡献者之一）出任 MSL 首席科学家。OpenAI 系的 Trapit Bansal（o-series 共同作者）、Shuchao Bi（GPT-4o voice 共同作者）、Huiwen Chang（GPT-4o 图像生成共同作者）、Lucas Beyer / Alexander Kolesnikov / Xiaohua Zhai（视觉三人组）等也都被打包带进 MSL。

MSL 内部分成四个组：**TBD Lab 管前沿大模型，Perception 管多模态视觉，FAIR 管长期研究（Rob Fergus 领头），还有一个产品/应用组**。ProgramBench 这篇论文的署名横跨 TBD Lab + FAIR，加上 Stanford 的 Diyi Yang、Princeton/Stanford 系的 Ofir Press，是典型的 MSL 大模型组与学术机构联合出题的格局。

把 ProgramBench 从这个 lab 出题这件事放在 2026 年节奏里看，很顺：**MSL 一边在造模型，一边要建立"自己人评测自己人"以外的公共标尺**。SWE-Bench 已经成熟，它需要新的硬题来证明"当前所有前沿模型都还远没到达 superintelligence"。这道题的政治意涵也明确：**给业界泼一盆冷水，提醒不要把 SWE-Bench 60% 当成 AGI 信号**。

![Meta Superintelligence Labs 组织与 ProgramBench 出处](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/programbench-meta-si-lab-rebuild/meta-si-lab-org-chart.png)

## 七、HN 与 r/ML 的反应：业界怎么看这道题

5 月 6 日 HN 首页这条 ProgramBench 链接，125 分 69 评论，讨论焦点集中在三件事。

### 7.1 "这道题是不是太狠了"

最高赞批评来自规则层面。一位资深工程师在评论里写："a model is supposed to reverse-engineer a blackbox using only limited number of tries. I'm not sure even ASI can do this under these constraints."——意思是这套规则下连"超级智能"都未必通关，何况现在的模型。FFmpeg 文档大约几十页，但真要从零实现一个等价 FFmpeg 需要几百页编解码器规范，光靠 README + man page 是不够的。

另一条高赞补刀更直白：**"the tester has vastly more knowledge of the program than the implementer"**——出题方（fuzzing agent）拿着参考二进制无限次调用 oracle，做题方（被测模型）只能在黑盒里盲打。这是评测公平性的真问题。

### 7.2 缺人类基线

第二个争议是**没有人类对照分**。一群人问：让一个工程师 8 小时给一份 README 重建 ripgrep，% Almost 能拿多少？论文没回答，因为这种基线测起来贵到离谱（200 道题 × 多人多日）。但缺了这条基线，"模型 0% 通关"这个数字就少了一个尺度——是模型菜，还是题真的解不动？

### 7.3 MirrorCode 的反例

最有意思的回应来自 Ofir Press 团队自己：**"On MirrorCode, Opus 4.6 successfully reimplements almost every program up to gotree's size."** MirrorCode 是 Press 团队另一个未公开的内部 benchmark，让模型重建中等规模 Go 项目（gotree 量级，约几千行），Opus 4.6 在那道题上几乎全过。把 MirrorCode 和 ProgramBench 放一起看，结论可以重新校准——**前沿模型在数千行规模的重建任务上已经能打，但跨过 1 万行边界就开始崩**。

ProgramBench 把题目难度刻意调高到行业天花板以上，Press 自己也承认这可能是"under-eliciting models or setting unfair constraints"。这个自我承认很重要，它说明这道题不是用来证明"模型多废"，而是用来圈出"前沿模型当前还摸不到的能力上限"。

### 7.4 文件结构争议

第三个被讨论很多的话题是论文那句"Models favor monolithic, single-file implementations"。HN 评论分两派：一派说这是模型缺陷（人类写多文件是为了维护性，模型不会）；另一派说这是 agent 工具链的缺陷——LLM 喜欢单文件是因为多文件需要更复杂的 file system 操作 + 跨文件上下文管理，agent harness 没把这条做顺。

两派都有道理。这条观察直接给国内 AI Coding 工具厂商提了个清单——**Trae / 通义灵码 / CodeBuddy / 智谱 CodeGeeX 这一波，谁先把"多文件项目结构生成"做成 first-class capability，谁就能在长程编码上拉开身位**。

![HN 与 r/ML 讨论焦点拼图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-08/programbench-meta-si-lab-rebuild/hn-discussion-screenshot.png)

## 八、对国产 LLM 的启发：标尺有了，接下来比什么

ProgramBench 这次跑分名单里没有出现 DeepSeek、千问（Qwen3）、Kimi（月之暗面 K2.6）、智谱 GLM-4.6、字节豆包大模型等任何一个国产前沿模型——不是国产模型不行，是论文是 2026 年 5 月 5 日提交的，9 个模型名单截止时间更早，而且 Meta 自己的 Llama 4 也没进榜。这个空缺反而是国产团队的机会。

几个判断：

**这是一道"零起点"的公共标尺**。前沿模型 % Resolved 全部 0.0%，% Almost 最高 3.0%。国产模型如果现在补跑这套 benchmark，从 0 起步，只要做到 1.0% 就能进 leaderboard 第二档；做到 2.0% 就能挤进 Claude Opus 4.6 同档；做到 3.0% 就能和 Claude Opus 4.7 平起平坐。这种"地板分"机会在 SWE-Bench 上早就没有了——SWE-Bench 现在的入场分是 50%。

**国内有没有人在做类似 benchmark**。2025 年下半年清华 KEG、上海 AI 实验室、智源研究院都发过几篇 long-horizon coding evaluation 论文，但规模都在 50–100 道题，覆盖深度也不到 ProgramBench 的"百万行级 reference"。如果国产研究机构跟着 ProgramBench 这个范式做一份 ChinaProgramBench——加入鸿蒙、欧拉、华为开源镜像里的核心组件——既能给国产模型做公平评测，也能反过来给国产 AI Coding 工具喂训练信号。

**长程能力是国产模型可以打的方向**。看 leaderboard 那栏 API call 数字：Claude Sonnet 4.6 跑了 475 次还在试，GPT 5.4 16 次就退场。这个差距不是"智商"差距，而是**愿意持续试错的训练倾向**。国产模型用 agent RL（DeepSeek-R1、Qwen3-Thinking、智谱 GLM-Zero）这条路在长程任务上有原生优势——核心是 RL 出来的模型不容易"放弃"，正好对上 ProgramBench 这种要跑 200+ 步才出结果的题。

**ProgramBench 给了国产 AI Coding 工具一个明确的设计提示**：从"单文件代码生成"升级到"多文件项目架构"。Trae、通义灵码、CodeBuddy、CodeGeeX 这一代，谁先做出"看到一份文档就生成完整项目骨架（src/ + tests/ + Makefile + Cargo.toml）"，谁就能在长程编码这条新赛道上抢到先发位次。

## 九、一句话收口

SWE-Bench 教会模型修房子，ProgramBench 让它们试着盖房子。**Claude Opus 4.7 在 200 道题里盖了 6 道半成品，剩下 194 道全军覆没**——这个数字不是悲观信号，是正在打开的评测真空。国产模型从 0 起步，每涨 1% 都能挤进 leaderboard 一档；国产 AI Coding 工具谁先把"多文件项目架构"做成 first-class，谁就能在 ProgramBench 这个新标尺上抢到第一批分。

**评测体系越硬，行业越往前走。这是 2026 年最值得关注的一道新题。**

---

*论文链接：arXiv:2605.03546，作者 John Yang / Kilian Lieret / Jeffrey Ma / Parth Thakkar / Dmitrii Pedchenko / Sten Sootla / Emily McMilin / Pengcheng Yin / Rui Hou / Gabriel Synnaeve / Diyi Yang / Ofir Press（Meta FAIR + Meta TBD Lab + Stanford + Harvard）。HN 讨论 125 pts / 69 cmt。*
