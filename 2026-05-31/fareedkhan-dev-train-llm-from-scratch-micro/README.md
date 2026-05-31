# 13M参数小模型也能跑通？一张表格揭露“穷人AI”训练真相

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/fareedkhan-dev-train-llm-from-scratch-micro/head.png)

你是一名 AI 研究员，刚在 GitHub 上看到一个 13M 参数的小模型，从零训练，跑在单张 T4 上，输出结果居然是一段关于“电子围栏”和“中国古代村庄”的胡言乱语。

你愣了一下，然后笑了——不是嘲笑，而是一种“这家伙居然跑通了”的欣慰。

![FareedKhan-dev/train-llm-from-scratch — A straightforward method for training yo](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/fareedkhan-dev-train-llm-from-scratch-micro/content-1.png)

这个项目叫 `train-llm-from-scratch`，作者 FareedKhan-dev 把它扔上 GitHub 时，README 第一句话写的是：“I am Looking for a PhD position in AI.” 没有大厂光环，没有 A100 集群，只有一个人、一台差点“死掉”的 GPU、一份从 Pile 数据集里抠出来的 5%-10% 的训练数据，以及一个从《Attention Is All You Need》论文里一行行抠出来的 PyTorch 做到。

![Transformer Architecture by Fareed Khan](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/fareedkhan-dev-train-llm-from-scratch-micro/content-2.png)

然后他告诉你：13M 参数就够了，一天出结果，输出至少“能拼对单词”。

**在追逐万亿参数大模型的军备竞赛里，一个 13M 的小模型反而让人停下来想了三秒。**

## 一张“贫民 GPU 生存指南”表格，暴露了 AI 民主化的真正底线

Fareed 做了一件很实在的事——他在 README 里列了一张 GPU 兼容性表格，从 NVIDIA A100（40GB）一路排到 AMD RX 7600（8GB），总共 27 款显卡，每款都标明了内存、是否能跑 2B 模型训练、以及“最大实际可训练模型尺寸”。



![27 款 GPU 对比表格截图，A100 到 GTX 1660 Ti 的训练上限一览](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/fareedkhan-dev-train-llm-from-scratch-micro/schematic-1.png)



这张表格本身就是一份“穷人 AI 入门指南”。Tesla T4——Google Colab 免费版送的那种——16GB 显存，能跑 13M 训练，最大推到 1.5B-2B。RTX 3090 这种消费级旗舰，24GB 显存，能撑到 3.5B-4B。而一张 GTX 1660 Ti，6GB 显存，最多 0.75B 参数，但依然能跑 13M 的小模型训练。

这意味着什么？意味着任何一个有台游戏本的学生，都能在一天内从零训练出一个能生成“勉强通顺英文”的语言模型。不需要申请算力资源，不需要等审批，不需要跟实验室抢 GPU 排期。

Fareed 自己用的配置更激进：`VOCAB_SIZE = 50304`、`CONTEXT_LENGTH = 128`、`N_EMBED = 128`、`N_HEAD = 8`、`N_BLOCKS = 1`。你没看错，N_BLOCKS = 1——就一个 transformer block。这跟 GPT-3 的 96 层比起来，简直是“单细胞生物 vs 蓝鲸”。但他就是靠这一个 block、8 个注意力头、128 维嵌入，把模型跑出了“能拼对单词”的效果。



![13M 模型架构简化图，1 个 Transformer Block + 8 头注意力 + MLP 展开/压缩](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/fareedkhan-dev-train-llm-from-scratch-micro/schematic-2.png)



当然，输出还是一坨。他贴出的 13M 模型生成样本是这样的：

> In \*\*\*1978, The park was returned to the factory-plate that the public share to the lower of the electronic fence that follow from the Station's cities. The Canal of ancient Western nations were confined to the city spot. The villages were directly linked to cities in China that revolt that the US budget and in Odambinais is uncertain and fortune established in rural areas.

语法是烂的，逻辑是碎的，“电子围栏”和“中国古代村庄”被强行塞进同一个段落。但注意——每个单词拼写都是对的，标点符号基本到位，甚至还有“1978 年”这种具体年份。对比他之前用 Tiny Shakespeare 数据集训练的 2.3M 参数模型（输出是“ZELBETH: Sey solmenter! tis tonguerered…”这种完全不可读的乱码），13M 是一次质的飞跃。

**13M 参数不是天花板，而是一道“语义及格线”。**

## 从 Pile 到 tiktoken：一个极简技术栈的“刚好够用”哲学

这个项目的技术选型有一种“抠门但聪明”的气质。训练数据用的是 Pile 数据集——825GB 的巨型开源语料库，包含 PubMed 摘要、GitHub 代码、书籍、文章等 22 个子集。但 Fareed 只下了 5%-10%，最多 30 个文件（每个约 11GB），用 `--train_max` 参数控制。数据处理管道是 `.jsonl.zst → JSON 解析 → tiktoken 编码 → HDF5 存储`，每一步都选的是最省显存、最易复现的方案。



![数据处理流程图，从 .jsonl.zst 压缩包到 HDF5 token 数组的转换过程](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/fareedkhan-dev-train-llm-from-scratch-micro/schematic-3.png)



tokenizer 用的是 OpenAI 的 `tiktoken`，编码器选 `r50k_base`——GPT-3 同款。他没自己训练 tokenizer，因为“目标是让模型至少输出正确单词”，而一个成熟的 tokenizer 能省掉大量预处理工作和词汇表对齐的麻烦。HDF5 格式存储 token 数组，支持动态扩展（`maxshape=(None,)`），这让他在训练时能高效随机读取，不用把整个数据集塞进内存。

训练脚本 `train_transformer.py` 把所有配置集中在一个 `config/config.py` 文件里，修改参数不用动代码。这种工程习惯在工业界是基操，但在“为了发论文而写的一次性代码”里很少见到。Fareed 甚至给每个模块写了独立的 `.py` 文件——`mlp.py`、`attention.py`、`transformer_block.py`、`transformer.py`——结构清晰到可以直接当教材用。

让我愣神的是 README 末尾的“What's Next”部分。他没画大饼，没说要 scale up 到 7B，而是建议用户“先用 13M 跑通，一天出结果”，然后基于这个 pretrained model 做特定领域的 fine-tune——“可能最终得到一个不到 1B 甚至 500M 的模型，在你的私有数据上安全运行”。

这句话背后是一个被大模型叙事掩盖的真相：绝大多数商业场景不需要 AGI，只需要一个懂你公司内部术语、能跑在本地服务器上、不怕数据泄露的专用小模型。而 Fareed 的项目，恰好给了你一条从零开始的 build path。

最后说一句题外话。Fareed 在 GitHub 个人页上写“I am Looking for a PhD position in AI”，然后扔出这个项目当 portfolio。这比发一百封套磁邮件都有用——因为导师们看到的不只是一个申请者，而是一个能把 transformer 从 attention 公式写到 batch processing、从数据下载写到 text generation、顺便附赠一份 GPU 选型指南的实干派。建议他下次更新 README 时把 loss curve 也贴出来，那就更完美了。

## 参考来源
- https://github.com/FareedKhan-dev/train-llm-from-scratch

#FareedKhandev #LLM #AI #科技

![Transformer Block by Fareed Khan](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/fareedkhan-dev-train-llm-from-scratch-micro/content-3.png)
