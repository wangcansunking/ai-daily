---
title: 混元 Hy4 flash 开放三套权重，先测哪一项
date: '2026-11-23'
weekday: 星期一
slug: hunyuan-hy4-flash-weights-2026-11-23
category: domestic
description: 区分指令权重、FP8 和预训练底座，梳理长上下文与部署成本。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/hunyuan-hy4-flash-weights-2026-11-23.png
batch: morning
track: deep-dive
cover_style: abstract data visualization
top_five_rank: 3
---

# 混元 Hy4 flash 开放三套权重，先测哪一项

![稀疏路由与专家节点抽象图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/hunyuan-hy4-flash-weights-2026-11-23.png)

腾讯混元在官方模型卡公布 Hy4 flash 的指令权重、FP8 指令权重及预训练底座。对准备部署的团队，先要分清三套权重的用途，再把存储、长上下文和实际任务成本放到同一张账上。

## 要点速览

- 模型卡列出约 2200 亿总参数，每词元约 120 亿活跃参数。
- 模型接受文本和图像，输出文本，上下文上限为 1,048,576 词元。
- 官方公开三套权重，可在 Hugging Face、ModelScope 和 GitCode 查阅。
- 腾讯内部 WorkIndex 上，Flash 得分 42.1，高于 Hy4 preview 的 39.7，低于 Hy4 的 51.4。

## 三套权重分别用在哪

| 模型卡版本 | 用途 | 验证重点 |
| --- | --- | --- |
| Hy4 flash | 指令模型 | 对话模板、图像输入和任务质量 |
| Hy4 flash-FP8 | FP8 量化指令模型 | 推理框架、算子与硬件支持 |
| Hy4 flash-base | 预训练底座 | 后训练与任务适配 |

底座面向继续训练与适配，指令版则带有面向对话和指令任务的使用方式。量化版可以作为部署评估对象，质量、吞吐量和显存用量需要在目标硬件上一起测。官方模型卡标明适用 Tencent Hy License。

![预训练阶段每字节比特数（BPB）与活跃参数的对照](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/hy-pretrained-model-bpb.jpg)

*图片来源：腾讯混元 Hy4 flash 模型卡 · 预训练阶段每字节比特数（BPB）与活跃参数的对照（取阅），2026-11-23。*

混合专家模型每个词元只调用路由选中的部分专家。模型卡写明，路由从 512 个专家中选择 16 个，另有一个共享专家。活跃参数描述单步计算，部署时仍要为专家权重安排存储和访问。

## 百万上下文怎样影响部署

模型卡将稀疏注意力和滑窗注意力交错组织，并用 Engram 提供短词元序列的检索表示。对长文输入，先选出相关位置再做部分计算，可以减少某些位置反复访问带来的开销。

![DFly2 草稿模块复用键值缓存的架构图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/hy-dfly2.jpg)

*图片来源：腾讯混元 Hy4 flash 模型卡 · DFly2 草稿模块复用键值缓存的架构图（取阅），2026-11-23。*

内置的 DFly2 是用于推测解码的草稿模块，模型卡描述它复用目标模型的键值缓存和稀疏索引。草稿生成候选，目标模型再验证。实际收益取决于候选接受情况、输入长度以及引擎能否支持相应路径。

在标称上限内，短文本问答、长文档跨段检索和多页含图材料的负载仍然不同。建议对同一批任务逐档增加输入长度，分开记录首词元时间、生成速率、峰值显存及并发时的失败率。

模型卡给出文本和视觉活跃参数的细分。图片输入还涉及视觉编码和预处理，实际压测可以分开计算纯文本和含图的吞吐，这样更容易定位是否需要为图像链路单独预留资源。

## 成本数字跟任务一起读

![WorkIndex 内部评测得分与成本](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/hy-performance-cost-workindex.jpg)

*图片来源：腾讯混元 Hy4 flash 模型卡 · WorkIndex 内部评测得分与成本（取阅），2026-11-23。*

WorkIndex 是混元团队基于 WorkBuddy 办公场景设计的内部评测，涵盖幻灯片制作与编辑、文档协作和表格修复等任务。Flash 得分 42.1，同表 Hy4 preview 为 39.7，Hy4 为 51.4。

![三款混元模型在 WorkIndex 的平均任务成本](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/hy-workindex-cost.jpg)

*图片来源：腾讯混元 Hy4 flash 模型卡 · 三款混元模型在 WorkIndex 的平均任务成本，官方正文与图示倍数有差异（取阅），2026-11-23。*

模型卡的文字和图都显示 Flash 的平均任务成本较低，给出的具体倍数有差异。内部任务构成、价格和完成标准影响这个结论。自部署评估还应带入服务器使用率和人工复核时间。

![混元盲评（Blind Evaluation）的满意度、时间和成本](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/hy-blind-evaluation-en.jpg)

*图片来源：腾讯混元 Hy4 flash 模型卡 · 混元盲评（Blind Evaluation）的满意度、时间和成本，85 名内部用户完成 27 类共 259 项任务（取阅），2026-11-23。*

腾讯还以七项公开基准汇总性能与成本。其任务类型和计价口径应与 WorkIndex 分开阅读。

另一组团队盲评把用户满意度、耗时和成本放在同一张图上。它提供了比单看生成速度更完整的读法。办公交付常涉及多轮修订，验收时可同时记录输出是否可直接使用、需要改几处以及改完总共耗时多久。

![公开任务口径的性能和成本比较](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/23/hy-performance-cost-open-benchmarks.jpg)

*图片来源：腾讯混元 Hy4 flash 模型卡 · 公开任务口径的性能和成本比较（取阅），2026-11-23。*

部署前可先固定三类样本：短对话、长文档、含图内容。对照指令版和 FP8 的输出，再增加并发；遇到长上下文退化时，结合日志区分输入处理、生成和排队所占的时间。

Hy4 flash 的开放权重增加了部署选择，能否降低单位交付成本，最终要用自己的任务、硬件和验收标准核算。

## 🔗 引用链接

- [腾讯混元 Hy4 flash 模型卡](https://huggingface.co/tencent/Hy4-flash)
- [腾讯混元 Hy4 flash-FP8 模型卡](https://huggingface.co/tencent/Hy4-flash-FP8)
- [腾讯混元 Hy4 flash-base 模型卡](https://huggingface.co/tencent/Hy4-flash-base)
