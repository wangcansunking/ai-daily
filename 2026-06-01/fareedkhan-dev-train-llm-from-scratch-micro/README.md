# 开发者FareedKhan：如何用PyTorch与825GB数据从头构建Transformer

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/fareedkhan-dev-train-llm-from-scratch-micro/head.png)

几年前，一位名叫 Andrej Karpathy 的工程师在 YouTube 上直播用一台 MacBook 从头写了一个 GPT，观众看着终端里滚动的 loss 曲线，第一次意识到“造一个大模型”的门槛可能没有想象中那么高。但 Karpathy 用的是莎士比亚全集——一个只有 1MB 的文本文件。真正的难题从来不是“能不能跑通”，而是“能不能在真实世界的脏数据上跑出像样的结果”。

2025 年，一个名为 FareedKhan-dev 的开发者把一个更完整的答案扔到了 GitHub 上：一个从数据下载、预处理、Transformer 从头搭建、训练到文本生成的完整流水线。仓库名叫 `train-llm-from-scratch`，没有花哨的框架封装，没有分布式训练的复杂配置，只有纯 PyTorch 代码和一份写给人看的 README。作者甚至在文档里坦白：他正在找 AI 方向的 PhD 位置。

![FareedKhan-dev/train-llm-from-scratch — A straightforward method for training yo](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/fareedkhan-dev-train-llm-from-scratch-micro/content-1.png)

**一个求职 PhD 的开发者，把“训练大模型”这件事拆到了单 GPU 可复现的程度。**

![Transformer Architecture by Fareed Khan](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/fareedkhan-dev-train-llm-from-scratch-micro/content-2.png)

## 从 The Pile 啃起：825GB 里的 13M 参数

这个项目最让我愣神的地方，不是它把 Transformer 写得多优雅，而是它对训练数据的处理思路异常务实。

作者选择了 The Pile 数据集——一个由 22 个子集组成的 825GB 开源语料库，涵盖书籍、论文、GitHub 代码、网页文本等。但他没有试图吞下整个巨兽。README 里明确说：只取 5%–10% 的数据量用于训练。数据下载脚本 `data_download.py` 的 `--train_max` 参数默认值为 1（每个文件约 11GB），最大可到 30。



![The Pile 数据集 22 个子集构成饼图，标注 PubMed Abstracts / GitHub / Books3 等主要来源](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/fareedkhan-dev-train-llm-from-scratch-micro/schematic-1.png)



数据格式是 `.jsonl.zst`——JSON Lines 加 Zstandard 压缩。每一行是一个 JSON 对象，包含 `text` 字段和 `meta` 元数据（比如 `pile_set_name: "PubMed Abstracts"`）。预处理脚本 `data_preprocess.py` 用 OpenAI 的 tiktoken 库（`r50k_base` 编码，也就是 GPT-3 用的那套分词器）把文本转成 token 序列，存进 HDF5 文件。这个设计很聪明：HDF5 支持随机读取和动态扩展，训练时不用每次都重新解析原始 JSON，数据加载效率大幅提升。

然后是最核心的架构部分。作者在 `src/models/` 目录下逐层做到 Transformer 的全部组件：

- **MLP**（`mlp.py`）：标准的 4 倍扩展-激活-投影结构，`n_embed → 4*n_embed → n_embed`，激活函数用 ReLU。
- **单头注意力**（`attention.py` 中的 `Head` 类）：Key/Query/Value 三个线性投影层，因果掩码用 `torch.tril` 下三角矩阵做到，缩放因子 `1/sqrt(C)`。
- **多头注意力**（`MultiHeadAttention` 类）：`n_head` 个独立 `Head` 并行计算，每个头的维度是 `n_embed // n_head`，最后沿最后一维拼接。
- **Transformer Block**（`transformer_block.py`）：LayerNorm → MultiHeadAttention → 残差连接 → LayerNorm → MLP → 残差连接。这是和 GPT-2 完全一致的结构。
- **完整模型**（`transformer.py`）：Token Embedding + Position Embedding + `N_BLOCKS` 个 Block 堆叠 + 最终输出层映射到 `vocab_size`。



![简化 Transformer 架构图，输入 Token → Embedding + Position → N 个 Block（Attention + MLP）→ 输出预测](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/fareedkhan-dev-train-llm-from-scratch-micro/schematic-2.png)



默认配置跑的是 13M 参数版本：词表大小 `VOCAB_SIZE = 50304`（GPT-2 标准词表），上下文长度 `CONTEXT_LENGTH = 128`，嵌入维度 `N_EMBED = 128`，注意力头数 `N_HEAD = 8`，Transformer Block 层数 `N_BLOCKS = 1`。训练脚本 `train_transformer.py` 跑通后，模型会在 `models/` 目录下保存 `.pth` 权重，文本生成脚本 `generate_text.py` 可以直接加载推理。

作者还贴了一段 13M 模型的输出样本，语法确实不完美，但已经能生成有结构的句子：“In 1978, The park was returned to the factory-plate that the public share to the lower of the electronic fence...” 这种输出水平和 GPT-2 小模型（124M 参数）的早期 checkpoint 相当，而参数量只有后者的十分之一。

## 一张 GPU 能跑多大？作者给了张“算力对照表”

这个项目最实用的部分，可能不是代码本身，而是 README 里那张 GPU 兼容性表格。作者列出了从 NVIDIA A100 到 GTX 1660 Ti 共 23 款 GPU，标注了每张卡的显存、能否训练 2B 参数模型、能否训练 13M 参数模型，以及“最大可训练模型规模”。



![GPU 算力对照表，横轴为 GPU 型号，纵轴为最大可训练参数量，A100 40GB 标注 6B–8B / RTX 4090 标注 4B / Tesla T4 标注 1.5B–2B](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/fareedkhan-dev-train-llm-from-scratch-micro/schematic-3.png)



几个关键数据点：

- **NVIDIA A100（40GB）**：可训练约 6B–8B 参数模型。
- **NVIDIA RTX 4090（24GB）**：可训练约 4B 参数模型。
- **NVIDIA RTX 3090（24GB）**：可训练约 3.5B–4B 参数模型。
- **Tesla T4（16GB，Colab/Kaggle 免费版常见配置）**：可训练约 1.5B–2B 参数模型，13M 模型完全无压力。
- **NVIDIA GTX 1660 Ti（6GB）**：只能跑 0.75B 以下的模型，但 13M 依然能跑。

这张表的隐含信息是：如果你只是想做实验、写论文、验证一个想法，你不需要 AWS 账单上多出一行四位数。一张 Colab 免费 T4 足够训练 13M 模型并在一天内出结果；一张 RTX 3090 或 4090 就能摸到 2B–4B 的门槛。作者的建议是“先训练一个 13M 模型，理解整个流程，再考虑扩展”。

于是他反向推导：如果把架构做得更小更简单，但把数据做得更多样化，参数规模要多大才能让模型“至少写出像样的英文”？答案是 13M。

这个思路和当前“越大越好”的主流叙事形成了一种有趣的张力。当 OpenAI 和 Anthropic 在拼万亿参数、万卡集群的时候，一个独立开发者用单 GPU 证明了：在特定条件下，13M 参数就能产生有意义的文本输出。这对于那些想在私有数据上跑模型、又不想把数据送进第三方 API 的企业来说，是一个值得关注的信号。

## 参考来源
- https://github.com/FareedKhan-dev/train-llm-from-scratch
- https://huggingface.co/datasets/monology/pile-uncopyrighted

#FareedKhandev #LLM #AI #科技

![Transformer Block by Fareed Khan](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-01/fareedkhan-dev-train-llm-from-scratch-micro/content-3.png)
