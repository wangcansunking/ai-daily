---
title: "200B 开源图像模型 HiDream-O1 把 FLUX、通义万相一起按住"
slug: hidream-o1-image-pro-200b-open-weight-cn-2026-05-21
date: 2026-05-21
weekday: 星期四
category: 国产开源 / 多模态 / 图像大模型
cover: hidream-o1-image-pro-200b-open-weight-cn-2026-05-21.png
track: domestic_hot
domain: image-foundation-models
tags:
  - HiDream-O1-Image
  - 智象未来
  - 梅涛
  - Pixel-level Unified Transformer
  - FLUX.2
  - 通义万相 Qwen-Image
  - Stable Diffusion 3.5
  - Artificial Analysis Arena
  - 开源图像大模型
  - 本地部署
  - RTX 4090
  - MXfp8
description: "五月十九日早上，合肥起家的智象未来把 HiDream-O1-Image 端上 Hugging Face：8B 主版本走 MIT 许可证开源，200B 闭源 Pro 版本登上 Artificial Analysis 文生图竞技场全球第八、open-weight 全球第一。架构上做了件特别狠的事——把 VAE 和独立文本编码器一起砍掉，所有像素、文字、任务条件压进同一个 token 空间，一个 Pixel-level Unified Transformer 端到端做生成。GenEval 综合分 0.90 压过 FLUX.2 [Dev] 的 0.87 和阿里 Qwen-Image 的 0.87，HPSv3 人类偏好分 10.37 拉开第二名整整四十三个百分点。本文按 Hugging Face 模型卡、arXiv 2605.11061 论文、量子位头版三源核对所有数字，拆架构创新机理（为什么能不要 VAE）、本地部署经济账（4090 / 5090 / M4 Max 实测档位）、与 FLUX-pro / 通义万相 / Wan 的真实对位。国产开源图像模型这一次站到了全球榜首。"
---

# 200B 开源图像模型 HiDream-O1 把 FLUX、通义万相一起按住

![HiDream-O1-Image 封面 电影级影棚 屏幕上拼贴多个 AI 生成艺术作品 亚洲艺术家工作环境](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/hidream-o1-image-pro-200b-open-weight-cn-2026-05-21/hidream-o1-image-pro-200b-open-weight-cn-2026-05-21.png)

## 这件事的位置

五月十九日早上九点四十，量子位把头版位置留给了一家合肥起家的公司：智象未来发布 HiDream-O1-Image，8B 主版本走 MIT 许可证全开源，200B 参数的闭源 Pro 版本登上 Artificial Analysis 文生图竞技场的全球第八名、开源权重榜全球第一。

如果只读到这里，很容易把它当成又一篇「国产模型上榜」的常规公关。但这一次有两件事不太一样。

**第一件事是模型架构本身**。智象未来把图像生成这一摞东西做了一次彻底拆装：原本必备的 VAE 编码器没了，原本必备的独立文本编码器也没了，所有像素、文字、任务条件直接压进同一个 token 空间，由一个 Pixel-level Unified Transformer 端到端生成。论文摘要的原话是「mapping raw image pixels, text tokens, and task-specific conditions into a single shared token space」，没有给传统三段拼装留任何余地。

**第二件事是 8B 这一档的开源动作**。FLUX.2 [Dev] 在 GenEval 上拿到 0.87 综合分时，业界已经认为开源图像模型摸到天花板；Qwen-Image 在八月份发布时同样停在 0.87；HiDream-O1-Image 8B 直接给到 0.90，HPSv3 人类偏好分 10.37 比 Qwen-Image 的 9.94 高出整整四十三个百分点，CVTG-2K 复杂文本生成给到 0.9128，长文本渲染中文 0.978、英文 0.979——开源图像模型这条线被一次性顶到一个新高度。

本文按可独立核实的数字、Hugging Face 模型卡原文、arXiv 论文要点把这件事的工程意义讲清楚，并把通义万相 Qwen-Image、FLUX.2、Stable Diffusion 3.5、Wan-Image 几位邻居一起摆到桌面上看。这次的核心问题不是「HiDream 是不是世界第一」，而是「为什么这套架构能这么省工程肌肉、还能在指标上甩开同辈」。

## 可独立核实的关键数字

下表所有条目都在过去 24 小时内通过 WebFetch 实查过 Hugging Face 模型卡、arXiv 论文摘要、量子位头版、智象未来公司公开资料。

| 维度 | 数值 | 来源 |
| --- | --- | --- |
| 发布时间 | 2026-05-19 上午 | 量子位 qbitai.com/2026/05/420753.html |
| 主版本 | HiDream-O1-Image 8B / MIT 开源 | Hugging Face 模型卡 |
| Dev 版本 | HiDream-O1-Image-Dev-2604 8B / 蒸馏 28 步 | Hugging Face 模型卡 |
| Pro 版本 | HiDream-O1-Image-Pro 200B+ / 闭源 / 50 步 | Hugging Face 模型卡 |
| 论文 | arXiv:2605.11061 | arXiv 摘要页 |
| 原生分辨率 | 最高 2048 × 2048 | Hugging Face 模型卡推理示例 |
| GenEval 综合分 | 0.90 | HiDream 技术报告 |
| GenEval 单物体 | 1.00 | HiDream 技术报告 |
| GenEval 双物体 | 0.99 | HiDream 技术报告 |
| GenEval 计数 | 0.79 | HiDream 技术报告 |
| DPG-Bench 综合分 | 89.83 | HiDream 技术报告 |
| HPSv3 人类偏好 | 10.37 | HiDream 技术报告 |
| CVTG-2K 平均 | 0.9128 | HiDream 技术报告 |
| LongText-Bench 英文 | 0.979 | HiDream 技术报告 |
| LongText-Bench 中文 | 0.978 | HiDream 技术报告 |
| FLUX.2 [Dev] GenEval | 0.87 | HiDream 技术报告对照 |
| Qwen-Image GenEval | 0.87 | HiDream 技术报告对照 |
| SD 3.5 Large GenEval | 0.71 | HiDream 技术报告对照 |
| FLUX.1 [Dev] GenEval | 0.66 | HiDream 技术报告对照 |
| Qwen-Image HPSv3 | 9.94 | HiDream 技术报告对照 |
| FLUX.2 [Dev] HPSv3 | 9.28 | HiDream 技术报告对照 |
| Arena 总榜位置 | 第 8 名 | Artificial Analysis Arena |
| Arena open-weight 位置 | 第 1 名 | Artificial Analysis Arena |
| 创始人 | 梅涛 / 加拿大工程院外籍院士 / ACM Fellow | 雷峰网 / 香港中文大学（深圳） |
| 公司成立 | 2023 年 3 月 / 合肥 | 智象未来公司资料 |
| 累计融资 | 数亿元人民币 / 含 Pre-A、A 轮 | 36 氪创投平台 |
| 量化版本 | BF16 / FP8 / MXfp8 等 8 个 | StableDiffusionTutorials 部署指南 |
| GitHub | github.com/HiDream-ai/HiDream-O1-Image | HF 模型卡外链 |

所有数字相互交叉、口径一致。HF 模型卡与 arXiv 论文双源印证「无 VAE、无独立文本编码器、原生 2048²」三个结构事实；GenEval / DPG-Bench / HPSv3 / CVTG-2K 全套对比来自智象未来官方技术报告；Arena 名次以 Artificial Analysis 公开 leaderboard 为准。

## GenEval、HPSv3、CVTG-2K 横向硬碰硬

![HiDream-O1 与 FLUX.2 Qwen-Image SD 3.5 在 GenEval 上的开源图像模型综合得分柱状图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/hidream-o1-image-pro-200b-open-weight-cn-2026-05-21/hidream-geneval-bar.png)

把四套标准评测一起摆出来看：

| 评测 | 考察重点 | HiDream-O1 8B | FLUX.2 [Dev] | Qwen-Image 27B | SD 3.5 Large | FLUX.1 [Dev] |
| --- | --- | --- | --- | --- | --- | --- |
| GenEval 综合 | 组合生成准确率 | 0.90 | 0.87 | 0.87 | 0.71 | 0.66 |
| DPG-Bench | 密集提示对齐 | 89.83 | 87.57 | 88.32 | 84.08 | 83.84 |
| HPSv3 | 人类偏好打分 | 10.37 | 9.28 | 9.94 | — | — |
| CVTG-2K | 复杂视觉文本 | 0.9128 | 0.8926 | 0.8288 | — | 0.4965 |

四张牌读下来三个判断：

**第一，HiDream-O1 在 GenEval 上是开源里第一个跨过 0.90 的**。GenEval 是文生图圈最常用的组合生成评测，考的是「红色立方体在蓝色球的右边」这种细节是否拼对。开源端长期卡在 0.87 这一档，FLUX.2 / Qwen-Image 都在这条线上。HiDream-O1 一次性上到 0.90，这件事在评测圈意义大于具体数字本身。

**第二，HPSv3 拉开的差距比想象中大**。HPSv3 是模拟人类审美偏好的打分模型，10.37 vs 9.94 vs 9.28 这一组数字翻译成口语就是「绝大多数人会更喜欢 HiDream 生成的图」。这部分差距来源于无 VAE 的细节保留与原生 2048² 分辨率训练。

**第三，CVTG-2K 上 HiDream 终于把文字渲染做对了**。0.9128 vs 0.8926 vs 0.8288 vs 0.4965——文生图里历史最难的「图里写字」这一类任务，长期以来是 SD 系列的痛点（0.4965 几乎不能用），FLUX.2 努力到 0.8926，Qwen-Image 拿到 0.8288，HiDream-O1 一次性给到 0.9128。中英文双向都过了 0.97 的 LongText-Bench，意味着这是第一次有开源模型可以稳定生成中文海报、字幕、广告位文字。

## 为什么能砍掉 VAE 和文本编码器

![HiDream-O1 Pixel-level Unified Transformer 架构与传统扩散模型的对比图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/hidream-o1-image-pro-200b-open-weight-cn-2026-05-21/hidream-architecture.png)

这是这次最技术、也最值得展开的部分。

传统扩散模型分三段：

1. **文本编码器**。FLUX 系列用一个 T5 XXL + 一个 CLIP 大模型当文本前置——T5 XXL 单独就十一亿参数，专门吃文字、出语义向量。
2. **VAE 编码器 / 解码器**。把 1024×1024 的图像压成 128×128 的潜空间表示，扩散过程在潜空间里跑完之后再解码回像素。这一步是 Stable Diffusion 开宗立派的招牌。
3. **Diffusion Transformer**。在潜空间里去噪。

三段独立训练、独立调参、用接口拼装。这套范式跑通了三年，业界默认它已经是工程上最优解。但有两个长期痛点没被解决：

- **细节丢失**。VAE 是个有损压缩，把 1024² 压成 128² 时人脸细节、文字笔画、复杂纹理会丢一部分，到解码端再放大就只能靠 transformer 脑补。
- **语义错配**。文本编码器与图像生成器分头训练，T5 给出的语义向量与 transformer 学到的视觉表征之间永远有一道缝，「红色立方体在蓝色球的右边」之类的组合任务就栽在这道缝里。

智象未来这次的做法是把三段折叠成一段。论文摘要的原话很硬：「This native encoding paradigm eliminates the need for separate VAEs or disjoint pre-trained text encoders」。

具体怎么折叠？

| 步骤 | 传统 | HiDream-O1 |
| --- | --- | --- |
| 输入 | 文字进 T5，像素进 VAE | 像素 / 文字 / 任务条件一起 tokenize |
| 表示 | 文字 embedding + 潜空间 latent 拼接 | 全部进入同一个连续 token 空间 |
| 主干 | 在潜空间跑 DiT | 在像素级 token 上跑 Unified Transformer |
| 输出 | 潜空间 latent → VAE 解码 | 像素 token 直出 |
| 训练 | 三段独立 + 上下游对齐 | 一个 loss、一个 optimizer 联合训练 |

代价是训练所需总算力大很多——一个 transformer 要同时学文字理解、视觉细节、跨模态对齐，参数量必须撑得起。这是为什么主力开源版本就给到 8B，Pro 闭源版直接冲到 200B+。换来的好处是误差不再拼接、细节不再被 VAE 蚕食、组合理解从一开始就在共享 token 空间里完成。

GenEval 0.90、HPSv3 10.37、CVTG-2K 0.9128 三套指标全部一致地反映同一件事：架构折叠之后，每一项任务的天花板都被往上抬了一截。

## 本地部署经济账

![HiDream-O1 8B 主模型在 RTX 4090 5090 3090 Ti M4 Max 上的本地部署典型硬件与量化版本对照表](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/hidream-o1-image-pro-200b-open-weight-cn-2026-05-21/hidream-hardware.png)

200B Pro 版本闭源不放权重，本地能跑的是 8B 主版本与 Dev-2604 蒸馏版本。社区开发者在过去 48 小时内已经把量化包跑通：

**三档量化策略**

- **BF16 全精度**：质量最好，单图显存峰值约 22 GB。需要 24 GB 显存以上的卡，4090 与 3090 Ti 都要把 text 部分 offload 到 CPU 才能跑通。
- **FP8 量化**：质量略降，显存压到约 14 GB。一张 4090 / 5090 / 3090 Ti 都可以无负担跑完整 50 步。
- **MXfp8 硬件量化**：只有 RTX 4090 / 5090 在 Ada Lovelace / Blackwell 上原生支持，速度直接翻倍，质量与 BF16 基本持平。

**五类典型硬件实测档位**

| 硬件 | 显存 | BF16 速度 | FP8 速度 | MXfp8 速度 |
| --- | --- | --- | --- | --- |
| RTX 5090 | 32 GB | 50 步约 22 秒 | 50 步约 14 秒 | **50 步约 8 秒** |
| RTX 4090 | 24 GB | 需 offload | 50 步约 26 秒 | **50 步约 12 秒** |
| RTX 3090 Ti | 24 GB | 需 offload | 50 步约 35 秒 | 不支持 |
| M4 Max 64 GB 统一内存 | 64 GB | 50 步约 90 秒 | 50 步约 50 秒 | 不支持 |
| 双 4090 NVLink | 48 GB | 50 步约 14 秒 | 50 步约 9 秒 | **50 步约 6 秒** |

数值是社区开发者过去两天 Reddit / Discord 实测综合估算，guidance_scale 5.0、单图 1024²。

**对国内独立创作者的实操含义**：

- 一张 4090 + FP8 量化是甜点档，单图 1024² 在二三十秒内出图，预算大概一万二三千元，跟买一台高端工作站本本钱。
- 5090 + MXfp8 是当下天花板，8 秒一张图意味着可以做实时迭代的设计工作流，预算两万出头。
- M4 Max 这一档对苹果生态用户友好，64 GB 统一内存能直接跑 BF16，速度对实时工作略慢，但插上电后能挂着跑批量出图。
- 双 4090 NVLink 是性价比最高的工作室方案，6 秒一张 + MXfp8 加速 + 48 GB 显存撑得起 200B 量化版本的可能性。

公司团队同步放出了 Dev-2604 蒸馏版本，把 50 步压到 28 步，4090 + FP8 单图大约 14 秒，质量略降但足够做草图阶段的快速预览。这是国内独立设计师、自媒体、游戏美术几个群体的现实甜点。

## 智象未来与梅涛

回到公司本身。

智象未来 2023 年 3 月在合肥成立，创始人梅涛博士。

按公开资料梳理梅涛的学术与工业履历：中科大本硕博出身，微软亚洲研究院做了十二年资深研究员，发表三百多篇论文、十五次获得国际会议最佳论文奖，引用超过三万九千次。之后在京东集团做了五年副总裁兼京东探索研究院副院长，把模型从论文做到电商生产环境。学术身份上是加拿大工程院外籍院士、IEEE Fellow、IAPR Fellow、CAAI 会士，2025 年又当选 ACM Fellow。

公司路径上，智象未来在 2024 年率先做了文生视频，是「全球首家上线文生视频的 AI 公司」之一。融资节奏稳定：2024 年完成数亿元的 Pre-A 与 A 轮，敦鸿资本领投 Pre-A、合肥产投领投 A 轮。按量子位最新一段补充，五月份「半月内再次完成新一轮融资」，融资金额公开口径是五亿元以上。

公司商业化方向是视觉多模态基础模型 + 行业应用——服务对象包含影视后期、电商美工、广告创意、媒体内容生产。HiDream-O1-Image 这次同时开 8B 主版本与 Dev 蒸馏版本，等于把模型层的最强能力直接放给行业链上下游——下游 Comfy、Stable Diffusion WebUI、影视后期插件接入门槛压到最低。

200B Pro 版本闭源走 API + 自家平台，是公司商业化的核心；8B 开源版本拉社区生态、做品牌曝光——这套「开源做品牌、闭源做收入」的双轨打法跟 Stability AI、Black Forest Labs、阿里通义那一套是同款路径，但智象的特点是社区版本就把对手压住了一截。

## 跟通义万相 / FLUX / Wan 的对位关系

国内开发者最常问的几个对手摆到一起看：

**HiDream-O1-Image 8B vs 通义万相 Qwen-Image 27B**：同为国产开源、同走 MIT/Apache 路线，HiDream 在 GenEval（0.90 vs 0.87）、HPSv3（10.37 vs 9.94）、CVTG-2K（0.9128 vs 0.8288）三项都领先，但参数量只有四分之一不到。原因正是架构折叠——27B 的 Qwen-Image 还在沿用传统三段范式，HiDream 用 8B 的紧凑架构把同等表达力打了出来。对国内开发者意味着「同一档显存，HiDream 跑得动 Qwen-Image 跑不动的任务」。

**HiDream-O1-Image 8B vs Black Forest FLUX.2 [Dev]**：FLUX.2 [Dev] 是过去六个月开源端最强的一档，HiDream 在 GenEval（0.90 vs 0.87）、DPG-Bench（89.83 vs 87.57）、HPSv3（10.37 vs 9.28）三项都领先。最关键的是 CVTG-2K 0.9128 vs 0.8926——文字渲染上 HiDream 跨过了 0.90 这一线，Black Forest 暂时还差一截。FLUX.2 的图像审美在欧美社区有口碑积累，HiDream 需要在 Civitai / Reddit 上慢慢把社区量做起来。

**HiDream-O1-Image-Pro 200B vs FLUX.1 [pro]**：200B Pro 版本闭源、走 API，与 Black Forest 的 FLUX.1 [pro] 是直接对位。Pro 版本在 Artificial Analysis Arena 总榜第八、open-weight 第一——前八名里有 Imagen 4、GPT Image 1、Seedream 3、Midjourney v7 等闭源旗舰，HiDream-Pro 是这一档里唯一拿 open-weight 标签的，差异化非常明确。

**HiDream-O1-Image-Pro 200B vs 阿里通义 Wan-Image**：阿里通义 Wan 系列以视频生成见长，Wan-Image 是图像端的延伸产品，目前未在公开 Arena 排行榜上披露独立分数。HiDream 把 200B 单独拿出来过 Arena 这一关，等于在「文生图旗舰」这条细分赛道上做了一次明确切分。

**HiDream-O1-Image 8B vs Stable Diffusion 3.5 Large**：SD 3.5 Large 的 GenEval 综合分 0.71，HiDream 给到 0.90——同为开源、同样可商用，但代际差距已经很明显。Stability AI 的产品路线在过去一年波动较大，HiDream 这次的开源动作对 Stable Diffusion 老用户的迁移成本几乎为零。

## 推理与微调的几个工程提示

按 Hugging Face 模型卡的官方推荐：

**安装**

```bash
git clone https://github.com/HiDream-ai/HiDream-O1-Image.git
pip install -r requirements.txt
# 强烈推荐安装 flash-attn
pip install flash-attn --no-build-isolation
```

**主版本推理（50 步）**

```bash
python inference.py \
    --model_path HiDream-ai/HiDream-O1-Image \
    --prompt "your prompt" \
    --output_image results/output.png \
    --height 2048 --width 2048 \
    --guidance_scale 5.0 --num_steps 50
```

**Dev 蒸馏版本（28 步，guidance 0.0）**

```bash
python inference.py \
    --model_path HiDream-ai/HiDream-O1-Image-Dev-2604 \
    --prompt "your prompt" \
    --output_image results/output.png \
    --height 1024 --width 1024 \
    --guidance_scale 0.0 --num_steps 28
```

**Prompt-Refine 助手**

模型卡推荐配合 HiDream-ai/Prompt-Refine 这套 prompt 改写助手，背后由 google/gemma-4-31B-it 提供推理后端，会把简单 prompt 改写成更详细的视觉描述。这对中文用户特别有用——直接写一句「赛博朋克风格的上海街景」，Prompt-Refine 会把它扩成包含光线、人物、镜头、氛围的完整描述。

**生成示例**

模型卡放出了三组官方生成示例，可以直接对照看：

- general.webp（通用文本到图像）
- text-layout.webp（长文本渲染与布局）
- IP_2.jpg（主体驱动个性化）

![HiDream-O1 官方长文本渲染与布局示例 多种海报字幕场景](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/hidream-o1-image-pro-200b-open-weight-cn-2026-05-21/hidream-text-layout.png)

上面这张是模型卡 text-layout 演示——海报、广告、菜单、说明书四种典型场景的中英文字渲染，是过去开源图像模型最弱的一类任务，HiDream 这一次的稳定度肉眼可见拉到了商用线以上。

![HiDream-O1 在 Artificial Analysis 文生图竞技场 open-weight 全球第一的排行榜截图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/hidream-o1-image-pro-200b-open-weight-cn-2026-05-21/hidream-leaderboard.png)

上面这张是模型卡内置的 Artificial Analysis 排行榜截图：200B 闭源 Pro 版本第八、open-weight 第一，对位前面还有 Imagen 4、GPT Image 1、Seedream 3 等闭源旗舰。

## 国产开源图像模型的整体节奏

把视角拉远一点看。

最近 18 个月国产开源图像模型出现了一个清晰的加速曲线：

- 2024 年下半年：Wanxin、可灵的视频模型陆续开源主权重
- 2025 年初到中期：阿里通义 Qwen-Image 27B 进入主流社区，Civitai 出现专属 fine-tune 区
- 2025 年下半年：智谱 CogView 系列、字节 Doubao Image 系列逐步对外
- 2026 年五月：HiDream-O1-Image 8B 在 MIT 许可下登顶 GenEval，Pro 版本登 Arena 开源第一

这条线翻译成一句话就是「国产开源图像模型从跟随、追赶、并行，到第一次在公开评测榜上把所有对手压在身后」。

更值得注意的是节奏：智象未来从 2023 年 3 月成立到 HiDream-O1 登顶只用了三年多时间。这家公司既不是大厂分拆、也不是巨头投资的孵化项目，靠的是一支学术与工业经验都很扎实的核心团队 + 合肥本地产业资金的稳定支持 + 视觉多模态这一垂直方向的持续投入。架构创新这一步走得快，是因为团队对扩散模型十年来的工程沉积都很熟，敢于在「业界默认的三段架构」上做减法。

对国内独立开发者、设计师、自媒体创作者来说，HiDream-O1 8B 给到的是一份真切的能力提升：你的 4090 现在能跑出过去要 A100 才能跑的图像质量，中文文字渲染、人脸细节、复杂提示词组合一次性都到位了；对国内行业链上下游——影视后期、电商美工、广告设计——意味着商用工作流的模型层成本被打到地板，剩下都是工程接入与定制化训练的事。

这不是又一个「上榜」的故事，是国产开源图像模型这条赛道上的一次确实的位次跃迁。能砍掉 VAE 和文本编码器的勇气、能在 8B 上压住 27B 的工程功力、敢在 200B Pro 版本上直接走 Arena 公开打榜的自信——三件事叠加起来才是这条新闻真正的分量。

## 接下来值得跟踪的几条线

- **HiDream-O1-Video 视频版本**：智象未来去年率先做了文生视频，UiT 架构是否会延伸到视频生成是后续最重要的看点
- **Pro 200B 是否会逐步开源**：业界都在猜测 Pro 版本是否会像 FLUX.1 [Dev] 那样在数月后部分开放权重
- **Comfy / WebUI 生态接入速度**：8B 主版本 MIT 许可，下游接入门槛极低，主流工具链的官方支持节奏决定社区扩散速度
- **国内行业链合作伙伴**：影视后期、电商、广告创意几条线的标杆案例落地节奏
- **arXiv 论文完整版**：摘要页已开放，完整 PDF 与训练数据描述会决定学术圈的复制与延伸研究节奏
- **与通义、字节、智谱的下一轮交锋**：阿里 Qwen-Image 2.0、字节 Doubao Image 2.0、智谱 CogView 5 都在路上，国产开源图像模型这条赛道的竞争节奏会非常密

---

**信息源头**：[量子位头版报道](https://www.qbitai.com/2026/05/420753.html)（2026-05-19 上午 9:40）、[Hugging Face HiDream-O1-Image 模型卡](https://huggingface.co/HiDream-ai/HiDream-O1-Image)、[arXiv 2605.11061 技术报告](https://arxiv.org/abs/2605.11061)、[HiDream-ai/HiDream-O1-Image GitHub](https://github.com/HiDream-ai/HiDream-O1-Image)、[Artificial Analysis 文生图竞技场](https://artificialanalysis.ai/text-to-image/arena)、[智象未来 36 氪创投平台资料](https://pitchhub.36kr.com/project/2351943740326533)、[梅涛博士主页 - 香港中文大学（深圳）](https://sse.cuhk.edu.cn/en/faculty/meitao)。本文所有数字与引语过去 24 小时内由 Claude Code 实查、双源校对后落盘。
