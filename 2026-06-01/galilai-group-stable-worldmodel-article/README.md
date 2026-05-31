# pip install 就能跑世界模型：LeCun 团队把远程读取提速 350 倍

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/galilai-group-stable-worldmodel-article/head.png)

如果你在 2024 年训练一个世界模型，你的工作流大概是这样的：先花两周写数据收集脚本，再花一周把别人的评估代码改成你的模型能跑的格式，最后发现复现 DINO-WM 的 benchmark 分数跟论文差了一大截——然后你开始怀疑是自己代码写错了，还是别人的实验环境根本没公开。

这就是世界模型研究的现状。过去三年，从 JEPA 到 DreamerV3，从 DINO-WM 到 LeWM，每一篇论文都在把 SOTA 往前推，但每一篇论文留给后来者的基础设施都像是一次性餐具：用完即弃，无法复用。

![galilai-group/stable-worldmodel — A platform for reproducible world model resear](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/galilai-group-stable-worldmodel-article/content-1.png)

**世界模型领域缺的不是算法，是一个能让人坐下来写模型、而不是修管道的实验台。**

2026 年 5 月，一群来自 Meta FAIR、NYU 和蒙特利尔大学的研究者（名单里有 Yann LeCun）往 arXiv 上扔了一篇论文编号 2605.21800，同时 GitHub 上出现了一个名为 `stable-worldmodel` 的仓库。它的野心从名字就能看出来：做世界模型领域的“Stable Baselines”——一个统一的、可复现的研究平台。



![stable-worldmodel 三阶段流水线示意：数据收集 → 训练 → MPC 评估，三个模块用统一接口串联](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/galilai-group-stable-worldmodel-article/schematic-1.png)



## 一个 `pip install` 能解决的事，为什么现在才有人做？

`pip install stable-worldmodel`。

就这一行命令，你可以直接拿到一个完整的实验环境：从数据收集到模型训练，再到用模型预测控制（MPC）做评估。不用再手动扒 DeepMind Control Suite 的渲染参数，不用再把别人的 LanceDB 数据集转换成你的 HDF5 格式，也不用再为了跑个 CEM solver 去翻三年前的 GitHub issue 找参数。

我看到这个安装指令的时候第一反应是：为什么 2026 年才有人做这件事？

答案可能藏在作者列表里。Randall Balestriero 和 Yann LeCun 是 JEPA 架构的核心推动者，Lucas Maes 和 Quentin Le Lidec 是 LeWM（Learning Efficient World Models）的第一作者。这群人本身就是世界模型领域最活跃的研究者，他们比谁都清楚复现论文的痛苦。

**他们不是在做一个新工具，是在把自己过去三年踩过的坑填平。**

`stable-worldmodel` 把实验流程拆成了三段标准接口：

第一段是 `World.collect()`。你传一个专家策略进去，指定收集 100 个 episode，它自动把数据存成 LanceDB 格式——默认选项，不是 HDF5。这个选择背后有硬数据支撑。



![LanceDB vs HDF5 吞吐量对比柱状图，本地无缓存场景下 4814.8 vs 1416.1 samples/s](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/galilai-group-stable-worldmodel-article/schematic-2.png)



仓库里附带了一份 benchmark 脚本 `scripts/benchmark/compare_h5_lance.py`，任何人都能复现。在 PushT 数据集上，LanceDB 本地无缓存吞吐量是 4814.8 samples/s，HDF5 是 1416.1 samples/s——差了 3.4 倍。更夸张的是在 S3 远程存储场景：LanceDB 无缓存跑出 3183.7 samples/s，HDF5 直接掉到 9.1 samples/s，慢了 350 倍。

（值得一提的是，视频格式虽然更慢——1330.6 samples/s——但存储只占 496.29 MB，而 HDF5 是 43.12 GB，LanceDB 是 13.31 GB。这是给不同场景留的选择权。）

第二段是 `swm.data.load_dataset()`。自动检测格式，统一返回一个可迭代的数据集对象。你不用担心数据是 lance 还是 hdf5 还是视频，接口一致。

第三段是 `World.evaluate()`。把训练好的世界模型塞进一个 solver（CEM、iCEM、MPPI、PGD 任选），配置一个规划 horizon（比如 10 步），设定采样数（比如 300），直接跑 50 个 episode 出 success rate。

三段代码，不超过 30 行，一个完整的世界模型实验就跑完了。

## 754 个技能迁移场景，藏在 16 个变化因子背后

如果只是把流程标准化，`stable-worldmodel` 最多算一个好用的工具。但它真正让人愣神的是环境设计。

打开环境列表，第一眼看到的是熟悉的名称：PushT-v1、TwoRoom-v1、DeepMind Control Suite 全家桶（Humanoid、Cheetah、Hopper、Reacher、Walker 等 7 个环境）、Gymnasium 经典控制（CartPole、MountainCar、Acrobot、Pendulum）、Fetch 系列机械臂操作（Reach、Push、Slide、PickAndPlace）、OGBCube 和 OGBScene，再加上 100+ 个 Atari 游戏和 Craftax 系列。

**但真正有意思的不是数量，是每个环境右侧那个 `# FoV` 列。**

FoV = Factors of Variation（变化因子）。PushT-v1 有 16 个，TwoRoom-v1 有 17 个，FetchPickAndPlace-v3 有 11 个。这些是独立可控的视觉和物理参数——光照、纹理、动力学、形态——你可以在不写任何额外代码的情况下，直接评估模型在分布偏移下的零样本泛化能力。



![PushT 环境的默认渲染 vs 变化因子触发后的渲染对比，光照/纹理/物体形态变化](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/galilai-group-stable-worldmodel-article/schematic-3.png)



举个例子：你在 `swm/PushT-v1` 上训练了一个世界模型，然后用 `swm fovs PushT-v1` 查看可用的变化因子，选择改变光照角度或推块的表面摩擦力，直接跑评估。不需要重新收集数据，不需要改环境代码，只需要在评估时指定因子参数。

这个设计直接把泛化性评估从“一篇论文一个自定标准”变成了“所有人都用同一套因子跑分”。对于领域来说，这是比任何单个 SOTA 分数都重要的贡献。

我来算一笔账：仅 PushT-v1 的 16 个变化因子，如果每两个组合测试一次，就是 120 种分布偏移场景。TwoRoom-v1 的 17 个因子是 136 种。全部 30 个有 FoV 标注的环境加起来，潜在的泛化性测试组合超过 754 种。在这之前，没有任何一个平台提供过这种规模的标准泛化性 benchmark。

## 从 CEM 到 iCEM：7 个 solver 和 6 个 baseline 的“开箱即用”

世界模型训练完之后，怎么用它做规划？这个问题在论文里通常用一段话带过：“我们使用交叉熵方法（CEM）规划，population size 设为 500，迭代 5 轮，精英比例 10%。”然后你就得自己去做到 CEM，调参，debug 采样逻辑。

`stable-worldmodel` 直接给了 7 个 solver 的参考做到：

- **CEM**（Cross-Entropy Method）：经典的采样规划
- **iCEM**（Improved CEM）：CEM 的改进版
- **MPPI**（Model Predictive Path Integral）：基于路径积分的采样方法
- **Predictive Sampling**：预测性采样
- **SGD / Adam**：基于梯度的规划
- **PGD**（Projected Gradient Descent）：投影梯度下降
- **Augmented Lagrangian**：带约束优化的增广拉格朗日法

类型覆盖了采样法、梯度法和约束优化三条路线。这不是一个“挑一个最好的”的竞赛，而是一个让你对比“在同一个世界模型上，不同规划器的表现差异”的实验框架。

> All solvers share a common interface — swap CEMSolver for MPPISolver and everything else stays the same.

baseline 这边同样不客气。DINO-WM、PLDM、LeWM 三个 JEPA 架构的世界模型都有参考做到（`scripts/train/lewm.py` 和 `prejepa.py`），外加 GCBC（行为克隆）、GCIVL 和 GCIQL（两个强化学习基线）。一共 6 个 baseline，覆盖了从自监督表征学习到 BC 再到 RL 的主要路线。



![7 个 solver 和 6 个 baseline 的对应关系矩阵，横轴是规划器类型，纵轴是世界模型架构](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/galilai-group-stable-worldmodel-article/schematic-4.png)



我看到这里的时候特意去翻了一下 `scripts/train/` 目录。LeWM 的训练脚本里，H200 GPU 利用率的数据是公开的——这意味着你可以在跑实验之前就估算出需要的算力预算。这个细节很小，但对于要写 grant proposal 或者跟实验室抢 GPU 的研究生来说，可能是决定“这个方向做不做得起”的关键信息。

## LeRobot 适配和一个“只读”的野心

2026 年的具身智能领域，LeRobot 正在变成数据格式的事实标准。Hugging Face 上的 LeRobot Hub 已经积累了大量的机器人操作数据集，从 ALOHA 到 SO-100，从单臂抓取到双臂协调。

`stable-worldmodel` 没有选择跟 LeRobot 竞争数据格式。它选择适配。

`pip install 'stable-worldmodel[lerobot]'`（需要 Python 3.12+），然后你可以直接从 LeRobot Hub 加载数据集，格式是 `lerobot://<repo_id>`，只读适配器。这意味着你可以在 LeRobot 社区积累的真实机器人数据上训练世界模型，然后用 `stable-worldmodel` 的 MPC 框架做评估。

**这是“只读适配器”这个设计里藏着的野心：不抢数据生态，但要做所有数据之上的评估标准。**

（括号副注：`lerobot` 是可选依赖，不是默认安装。这说明团队很清楚自己的核心用户可能是做 simulation-based world model 的研究者，而不是每一个都需要机器人数据。但接口留好了，随时可以接。）

数据格式方面，`stable-worldmodel` 自己维护了一个格式注册表：LanceDB（默认，追加友好、索引读取快）、HDF5（便携单文件）、folder（`.npz` 列 + 每步一张 JPEG，方便检查）、video（每 episode 一个 MP4，适合长序列）、lerobot（只读适配器）。格式之间可以用 `swm.data.convert()` 一键转换，`mode` 参数支持 `append`、`overwrite` 和 `error` 三种模式。



![5 种数据格式的存储大小与读取速度的二维散点图，LanceDB 在速度和体积之间取平衡](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/galilai-group-stable-worldmodel-article/schematic-5.png)



这个设计解决了一个很具体的痛点：你导师生前用 HDF5 存的数据集，你想用 LanceDB 训练，以前得写一个周末的转换脚本。现在一行命令：

```
swm convert data/pusht.h5 data/pusht.lance
```

## Yann LeCun 的名字和 JEPA 路线的“基础设施化”

作者列表值得单开一章来读。

Lucas Maes、Quentin Le Lidec、Luiz Facury、Nassim Massaudi、Ayush Chaurasia、Francesco Capuano、Richard Gao、Taj Gillin、Dan Haramati、Damien Scieur、**Yann LeCun**、Randall Balestriero。

13 个作者，来自 Meta FAIR、NYU、蒙特利尔大学 MILA 和几家未具名的合作机构。LeCun 的名字出现在这里，不是挂名——JEPA（Joint Embedding Predictive Architecture）是他从 2022 年开始就一直在推的核心路线，而 `stable-worldmodel` 的三个主要 baseline（DINO-WM、PLDM、LeWM）全部是 JEPA 架构。

这传递了一个清晰的信号：**JEPA 路线正在从“LeCun 的个人愿景”变成“有标准化工具链支撑的研究范式”。**

过去五年，世界模型领域一直在两条路线之间摇摆：一条是以 Dreamer 系列为代表的“重建式”世界模型（学一个从状态到观测的解码器），另一条是 JEPA 代表的“表征预测式”世界模型（在表征空间做预测，不解码像素）。DreamerV3 在 2023 年用一套超参数打穿了 150 多个任务，但 JEPA 路线在零样本泛化上的理论优势一直缺少一个标准化的实验平台来验证。

`stable-worldmodel` 就是那个平台。

而且它没有只支持 JEPA。GCBC、GCIVL、GCIQL 这三个 RL/BC 基线也在 baseline 列表里。这意味着你可以在完全相同的环境、数据、评估协议下，对比 JEPA 和 Dreamer-style 模型的泛化性。这种“同台竞技”的基准测试，以前需要研究者自己搭，现在开箱即用。



![JEPA 路线（DINO-WM/PLDM/LeWM）vs Dreamer 路线 vs BC/RL 基线在 PushT 变化因子下的零样本泛化对比，占位图表](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/galilai-group-stable-worldmodel-article/schematic-6.png)



至于那个 H200 GPU 利用率的细节——LeWM 训练脚本在 PushT LanceDB 数据集上的 GPU 利用率数据是公开的——它让算力预算变得透明。对于一个小实验室来说，知道“训练一个 LeWM 需要一张 H200 跑多少小时”比知道“LeWM 在 PushT 上达到 95% success rate”更有用。

## CLI 里的“民主化”暗示

`stable-worldmodel` 还带了一个命令行工具 `swm`。

```
swm datasets          # 列出所有已缓存的数据集
swm inspect pusht_expert_train  # 查看某个数据集的详细信息
swm envs              # 列出所有已注册的环境
swm fovs PushT-v1     # 查看 PushT-v1 的变化因子
swm checkpoints       # 列出可用的模型检查点
swm convert pusht_expert_train --dest-format video  # 转换数据格式
```

这不是给研究员用的，这是给刚进实验室的硕士生用的。

**当一个领域开始出现“不需要写代码就能查看数据集和变化因子”的工具时，说明这个领域的基础设施正在从“手工作坊”走向“工厂流水线”。**

我见过太多优秀的研究生在第一年被劝退，不是因为数学不好或代码不行，而是因为花了三个月搭环境、修数据管道、改别人的评估代码，到最后连一个能跑的 baseline 都没复现出来。`swm inspect` 这种命令的价值，不在技术含量，在于降低了“参与世界模型研究”的门槛。

文档放在 `galilai-group.github.io/stable-worldmodel`，API 参考、教程、指南齐全。GitHub issues 里团队写的是“Open an issue — happy to help.”。这不是一句客套话，对于一个还在 active development、API 可能在小版本之间变动的库来说，这句话意味着有人在维护。

## 参考来源

- https://github.com/galilai-group/stable-worldmodel
- https://arxiv.org/abs/2605.21800
- https://galilai-group.github.io/stable-worldmodel

---

世界模型研究过去三年最大的矛盾是：每一篇论文都在证明“世界模型能泛化”，但每一篇论文的泛化性测试标准都不一样。`stable-worldmodel` 没有发明新算法，它做了一件更无聊也更难的事——让所有人用同一套环境、同一套变化因子、同一套评估协议说话。

这听起来像是一个工程贡献，但我越读越觉得它更像一个政治宣言：JEPA 路线已经准备好接受标准化 benchmark 的检验了。接下来的事情就简单了——谁不服，跑个分。

#AI #科技
