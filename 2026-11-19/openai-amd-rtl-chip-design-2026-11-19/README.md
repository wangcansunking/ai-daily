---
title: "OpenAI 的模型改写芯片电路，部分改动进入 AMD 送制造设计"
date: 2026-11-19
weekday: 星期四
category: 产业
slug: openai-amd-rtl-chip-design-2026-11-19
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/openai-amd-rtl-chip-design-2026-11-19.png
description: "OpenAI 披露模型改写 SystemVerilog RTL，并在 Synopsys 工具中核对行为与 PPA；部分修改已进入 AMD 送制造设计，另有自研 Jalapeño 芯片的硅验证案例。"
tags: ["OpenAI","AMD","Jalapeño","芯片设计","Synopsys"]
track: overseas-hot
batch: evening
---

# OpenAI 的模型改写芯片电路，部分改动进入 AMD 送制造设计
![芯片基板上方的半透明电路结构与旁侧的检视镜头概念图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/openai-amd-rtl-chip-design-2026-11-19.png)
*图片来源：Sunky 中文 AI 日报封面概念图，2026-11-19。*

**OpenAI 披露，AMD 已将部分由模型改写的 RTL 纳入近期送制造的设计。OpenAI 自研芯片 Jalapeño 的相关优化则经过硅片初始调试验证。** 这两项披露分别对应设计采纳和实物验证。[1]

OpenAI 技术文章标注日期为 2026 年 11 月 18 日，页面未标明具体时间和时区。官方账号发帖，时间为北京时间 11 月 19 日 01:05:52。据 The Next Web 报道，部分改写进入了 AMD 的设计流程。该报道刊于北京时间同日 02:55。[1][2][3]

## AMD 采纳的是经过工具评估的 RTL 改动

![Ibex 处理器在 RTL 优化实验中的基准对照图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-openai-ibex-rtl-optimization.jpg)
*图片来源：OpenAI 官方账号发布的 Ibex RTL 优化评测图，北京时间 2026 年 11 月 19 日。*

AMD 案例里，工程团队采纳的是可验证的模块改动。模型写的是 SystemVerilog RTL，即寄存器传输级描述。它约定硬件在时钟推进时如何更新状态、计算和传递数据。工程师在同一个模块内可以用不同方式实现约定的行为，修改的价值就体现在这些实现之间的取舍。[1]

- **模型提出候选改写。** SystemVerilog RTL 是团队能直接查看和比较的设计描述。对于已有模块，可以把候选改动放回原设计的上下文，明确这次动了哪一部分电路。
- **Synopsys 工具核对行为和 PPA。** 行为检查回答改写后模块能否继续按原约定工作。功耗、性能、面积统称 PPA，分析回答这种实现需要多少硬件资源、带来怎样的功耗与性能权衡，两类检查回答不同的问题。
- **工程团队决定采纳范围。** 据 OpenAI 披露，AMD 把部分修改纳入近期送制造的设计，另一些进入量产设计文件。写入具体设计的事实，表明这些建议已从可供比较的候选方案变成团队实际使用的设计改动。[1]

![PicoRV32 在 RTL 优化实验中的基准对照图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-openai-picorv32-rtl-optimization.jpg)
*图片来源：OpenAI 官方账号发布的 PicoRV32 RTL 优化评测图，北京时间 2026 年 11 月 19 日。*

Ibex 和 PicoRV32 的评测图比较了模型在已有设计上改写 RTL 的表现。AMD 的数据则来自实际设计项目中的模块评估。[1][2]

| 证据 | 比较对象与结果 | 进展或验证阶段 |
| --- | --- | --- |
| AMD 旗舰 GPU IP | 相对于该 GPU IP 的高度优化基线，PPA 改善最高 14% | 部分修改进入近期送制造的设计，另一些进入量产设计文件 |
| AMD 下一代旗舰 CPU | 部分模块的 PPA 改善为个位数百分比 | 据 OpenAI 披露，AMD 已验证这些模块的改善 |
| OpenAI 自研 Jalapeño | 相对于改写前的对应单元，向量、矩阵单元的标准单元面积分别估计减少约 13% 和 10% | OpenAI 称相关优化经过 Jalapeño 的硅片验证 |

最高 14% 是 OpenAI 报告的旗舰 GPU IP 相对于高度优化基线的 PPA 改善，适用范围限于该 IP。整卡性能、功耗和面积各自的变化需要产品级数据才能判断。[1]

## Jalapeño 在硅片初始调试中验证优化

![OpenAI 自研推理芯片 Jalapeño 的晶圆资料照片](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-openai-jalapeno-archive.jpg)
*图片来源：Tom’s Hardware 专访所刊 Jalapeño 晶圆资料照，署名 OpenAI，2026 年 9 月 30 日。*

OpenAI 在说明 Jalapeño 的相关优化时写道，“These optimizations were silicon proven during Jalapeño’s silicon bring-up”。向量和矩阵单元的标准单元面积降幅仍是估计值。[1]

Jalapeño 的单个乘法器给出了更小尺度的例子。这里的面积来自综合阶段，也就是把硬件描述转换成电路实现并估算所需单元的过程。把面积和延迟放在一起，才能看到这次重设计改变了哪两项约束。[1]

| 同一乘法器的指标 | 原实现 | 重设计 |
| --- | --- | --- |
| 综合阶段的单元面积 | 以原实现的单元面积为基线 | 比原实现少用约 15% |
| 运算延迟 | 两个时钟周期 | 一个时钟周期 |

对这个乘法器，改动意味着综合时需要的单元面积更少，运算结果也能少等一个周期。时钟周期衡量的是单次运算经过的时间步数，读者可以据此理解局部调度的变化。至于这种变化怎样反映到整颗芯片上，还取决于其他单元何时能提供输入、下游何时能使用输出。[1]

![安装在电路板上的 Jalapeño ASIC 特写](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-openai-jalapeno-asic.jpg)
*图片来源：Tom’s Hardware 专访所刊 Jalapeño ASIC 板上特写，署名 OpenAI，2026 年 9 月 30 日。*

从工程交接看，Jalapeño 同时提供了几种性质不同的信息。

- **单元面积估计**描述向量和矩阵单元改写前后的规模差异，比较范围是各自对应的功能单元。
- **综合结果**显示乘法器在转换成具体电路实现时节约的单元面积，可与改写前的同一个乘法器比较。
- **硅片初始调试**提供实物阶段的证据。OpenAI 对这一步的表述指向 Jalapeño，AMD 的公开进展则分别对应设计采纳和模块级评估。[1]

## GPT-Synopsys 计划接入现有设计工具

![Ibex 从规格到 RTL 的实验基准对照图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-openai-ibex-specification-to-rtl.jpg)
*图片来源：OpenAI 官方账号发布的 Ibex 从规格生成 RTL 的评测图，北京时间 2026 年 11 月 19 日。*

Synopsys 于 2026 年 9 月 30 日公告与 OpenAI 联合开发 GPT-Synopsys，OpenAI 在 11 月的技术文章里把这项合作放在接入现有电子设计自动化工具的后续计划中。本次披露的工程案例已使用 Synopsys 工具评估候选改写，GPT-Synopsys 的工具集成仍在后续计划中。[1][4]

![PicoRV32 从规格到 RTL 的实验基准对照图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-openai-picorv32-specification-to-rtl.jpg)
*图片来源：OpenAI 官方账号发布的 PicoRV32 从规格生成 RTL 的评测图，北京时间 2026 年 11 月 19 日。*

Ibex 和 PicoRV32 的另外两张图对应从规格到 RTL 的评测。这个任务从规格出发，要求模型给出可以检查的硬件描述。已有 RTL 的优化任务则有一份可直接对照的实现，团队可以围绕原有模块比较改动及其代价。这两种起点会改变工程团队需要交给模型的材料，也会改变它要对照什么来判断输出是否合格。[1][2]

AMD 案例表明，经过工具评估的部分改写已进入送制造设计或量产设计文件。Jalapeño 案例则表明，局部改写可以在硅片初始调试中获得进一步验证。两组案例为模型接入现有芯片设计工具提供了工程经验。[1][4]

## 参考来源

- [1] OpenAI 技术文章《Advancing chip design with our models》
- [2] OpenAI 官方账号发布帖及评测配图（可读镜像） · https://www.nitter.gq/OpenAI/status/2123100386638877054
- [3] The Next Web 关于 OpenAI 模型参与 AMD 芯片设计的报道，2026 年 11 月 19 日（北京时间）
- [4] Synopsys 关于联合开发 GPT-Synopsys 的公告 · https://investor.synopsys.com/news/news-details/2026/OpenAI-and-Synopsys-Announce-GPT-Synopsys-Frontier-Intelligence-to-Revolutionize-Chip-Design/
- [5] Tom’s Hardware 对 OpenAI Jalapeño 硬件设计的专访及配图 · https://www.tomshardware.com/tech-industry/asics/this-is-how-ai-should-be-used-openai-head-of-hardware-breaks-down-the-ai-assisted-design-of-its-jalapeno-asic
