# GPT-5.5 与 Gemini 3.1 陷过度调查陷阱：企业级 IT 任务成 AI 新短板？

> ai-daily · 2026 年 5 月 28 日 08:42 · 来源：Hugging Face Blog

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/itbench-aa-micro/head.png)

凌晨三点，旧金山某数据中心的监控屏上，一个 Kubernetes 集群突然开始抽搐。前端服务返回 503，用户投诉量像心电图一样蹿升。值班的 SRE 工程师被 PagerDuty 叫醒，揉着眼睛开始翻日志、查追踪链、看拓扑图——这是一场典型的线上事故，故障源可能藏在几十个微服务、网络策略和资源配额的迷宫里。

六个月前，IBM 的研究人员把这类场景打包成了一套基准测试，交给 Artificial Analysis 团队来评测前沿 AI 模型。他们想知道：当 AI Agent 被扔进真实的 Kubernetes 事故现场，面对告警、事件、追踪、指标、日志和应用拓扑这六座数据大山，能不能像人类 SRE 一样，精准揪出根因实体？

![ITBench-AA: Frontier Models Score Below 50% on the First Benchmark for Agentic E](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/itbench-aa-micro/content-1.png)

2026 年 5 月 27 日，答案揭晓了。

![image](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/itbench-aa-micro/content-2.png)

**所有前沿模型，没有一个及格。**

## 47% 的冠军，和一场 59 道题的修罗场

这个叫 ITBench-AA 的基准测试，首批发布了 59 个 SRE 任务（其中 40 个公开，19 个全新隐藏题）。每个任务给模型一个 Kubernetes 事故快照——包含告警、事件、追踪链、指标、日志和拓扑图，要求模型在 100 轮对话内，用 shell 命令检查沙盒文件系统，最终提交一份 JSON 格式的根因诊断：列出导致事故的最简 Kubernetes 实体集合。

评分规则极其严苛——用的是「召回门控精度」。翻译成人话就是：如果模型漏掉了任何一个真实根因，这一轮直接零分。如果全找到了，得分等于精度——提交的实体里有多少个是真的根因（真阳性 / (真阳性 + 假阳性)）。最终成绩取 59 个任务 × 3 次重复的平均值。

排行榜是这样的：Claude Opus 4.7（自适应推理，最大努力模式）以 47% 登顶，GPT-5.5（xhigh 推理档）46% 紧随其后，Qwen3.7 Max 42% 排第三。Gemini 3.1 Pro Preview 只有 30%，被开源的 Gemma 4 31B（推理模式，37%）踩在脚下。GLM-5.1（推理模式）以 40% 领跑开源阵营，DeepSeek V4 Pro（推理，最大努力）38%。



![ITBench-AA SRE 排行榜前十名柱状图，Claude Opus 4.7 47% 居首，GPT-5.5 46%，Qwen3.7 Max 42%，GLM-5.1 40%，DeepSeek V4 Pro 38%，Gemma 4 31B 37%，Gemini 3.5 Flash 40%，Gemini 3.1 Pro Preview 30%，横轴标注得分百分比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/itbench-aa-micro/schematic-1.png)



47% 什么概念？Artificial Analysis 在博客里专门做了对比：同样的前沿模型在 Terminal-Bench（另一个 Agent 基准）上得分远高于此。ITBench-AA 是他们评测套件里饱和度最低的 Agent 基准之一。换句话说，这不是那种「模型已经快刷到天花板」的榜单——这是一块硬骨头，咬得最好的也才啃下一半。

最有意思的是，我看到数据时愣了一下：回合数多不代表分高。GPT-5.5（xhigh）平均每个任务跑 31 回合，拿了 46%；Gemini 3.1 Pro Preview 平均跑了 83 回合——将近三倍——得分却只有 30%。模型们陷入了过度调查的陷阱：它们顺着日志和追踪链往上游挖，挖出了故障注入机制本身（比如 chaos-mesh 控制器），或者把伴随症状当成了根因，提交了一堆「看似相关实则多余」的实体。评分规则里的精度惩罚机制，让这种画蛇添足的行为付出了代价。

## 开源模型的成本奇袭，和一场 5.38 美元的豪赌



![模型得分 vs 每次任务成本散点图，横轴成本（美元），纵轴得分百分比，Gemma 4 31B 在左下角 0.14 美元/37%，Claude Opus 4.7 在右上角 5.38 美元/47%，Gemini 3.1 Pro Preview 在 2.23 美元/30% 被红圈标出](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/itbench-aa-micro/schematic-2.png)



成本维度上，这张排行榜揭示了另一个故事。Gemma 4 31B（推理模式）每次任务只需 0.14 美元，得分 37%——比 Gemini 3.1 Pro Preview 的 2.23 美元/30% 又便宜又好。GLM-5.1（推理模式）1.23 美元一次，40% 的得分追平了 Gemini 3.5 Flash（high 档，1.70 美元）。而榜首 Claude Opus 4.7，每次任务烧掉 5.38 美元。

**47% 的准确率，乘以每次 5.38 美元的成本，等于生产环境里没人敢用的 Agent。**

（值得一提的是，IBM 和 Artificial Analysis 用了统一的开源工具 Stirrup 作为 Agent 框架，所有模型在同一套沙盒环境里跑，shell 访问权限一致，100 轮上限一致，3 次重复一致。这保证了模型之间的对比是苹果对苹果，而不是某个模型因为工具链更顺手而占了便宜。）

让我细想的一个任务是公开题里的典型案例：Agent 看到前端路径出现用户侧故障，用 shell 命令检查离线快照——先看告警锁定事故时间窗口，再看追踪和日志把故障缩小到前端流量，拓扑图定位受影响的服务，最后 Kubernetes 清单文件揭示真相：一个名为 `otel-demo/NetworkPolicy/frontend-block-all-ports` 的网络策略把前端的所有端口都封死了。成功的诊断只需要提交这一个实体。但很多模型在找到网络策略之后，继续往上挖，挖出了 chaos-mesh 故障注入控制器，或者把同时段出现的资源配额告警也当作根因一并提交——然后被精度惩罚一刀砍掉。

IBM 的研究人员从企业 IT 运维的深水区提取了这些任务，故障模式覆盖了基础设施故障、服务故障、应用故障和混沌注入事故，包括资源配额耗尽、滚动更新失败、连接池耗尽和网络分区。这不是实验室里的玩具题——这是每天凌晨三点把 SRE 从床上薅起来的真实灾难场景的离线快照版。

Artificial Analysis 透露，ITBench-AA 只是第一波。接下来还会扩展到 FinOps（财务运营）和 CISO（首席信息安全官）任务。如果 SRE 场景已经让前沿模型集体挂科，那面对云成本异常检测和安全事件响应时，这些 Agent 的表现恐怕会更难看。

毕竟，在真实的企业 IT 世界里，找错根因比找不到根因更危险——你修了半天以为是网络策略的问题，其实底层是资源配额耗尽，网络策略只是被连带触发的症状。模型们现在的问题不是不努力，而是太努力，挖得太深，把整个故障链条上的每一环都当成了罪犯。凌晨三点被叫醒的 SRE 凭经验知道该停在哪里，AI Agent 还不知道。5.38 美元一次的推理成本，换来的是一份让你修错东西的诊断书——这笔账，哪个运维团队敢批？

## 参考来源
- ITBench-AA 官方博客：https://huggingface.co/blog/ibm-research/itbench-aa
- ITBench 论文（arXiv）：https://arxiv.org/abs/2502.05352
- ITBench GitHub：https://github.com/itbench-hub/ITBench
- ITBench-AA 排行榜：https://artificialanalysis.ai/evaluations/itbench-aa
- ITBench-AA HuggingFace 数据集：https://huggingface.co/datasets/ArtificialAnalysis/ITBench-AA/tree/main/sre

#ITBenchAA #Frontier #Models #Score #Below

![image](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/itbench-aa-micro/content-3.png)
