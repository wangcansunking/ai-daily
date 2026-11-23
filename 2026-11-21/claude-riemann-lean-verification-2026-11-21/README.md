---
title: Claude 参与的黎曼猜想证明，Lean 核验到了哪一步
date: '2026-11-21'
weekday: 星期六
slug: claude-riemann-lean-verification-2026-11-21
category: top-news
description: 按核验记录区分模块重放、独立内核、命题比对与同行评议。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/claude-riemann-lean-verification-2026-11-21.png
batch: evening
track: deep-dive
cover_style: minimalist editorial still-life photography
---

# Claude 参与的黎曼猜想证明，Lean 核验到了哪一步

![Claude 参与的黎曼猜想证明，Lean 核验到了哪一步](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/claude-riemann-lean-verification-2026-11-21.png)

11 月 20 日，数学家 Levent Alpöge 公布了一份四页说明，称自己与 Claude 找到了黎曼猜想的证明，并公开了分成 14 卷的 Lean 形式化文件。随后，独立核验者报告了可复查的进展。要读懂这些结果，得先分清源代码能否通过检查、代码证明了哪个命题，以及数学界怎样评议整套论证。

## 要点速览

- Alpöge 声称证明了黎曼猜想，以及狄利克雷 L 函数对应的广义版本。
- MIT 数学家 Andrew Sutherland 的核验记录显示，175,187 个模块编译并通过逐模块内核重放，两套独立实现的内核接受了完整导出。
- 同一份核验快照中，端到端 Comparator 仍在运行。克雷数学研究所的千禧年奖另有发表、时间和学界认可条件。

![Levent Alpöge 的《Remark》第一页](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/math-remark.png)

*图片来源：Levent Alpöge 的《Remark》第一页，2026-11-20。*

## 从四页说明到 14 卷文件

黎曼猜想断言，黎曼 ζ 函数的所有非平凡零点都位于实部等于二分之一的直线上。克雷数学研究所用素数分布解释它的意义：素数定理给出平均分布，而黎曼猜想约束实际分布偏离平均趋势的幅度。

![黎曼 1859 年手稿扫描（Riemann manuscript）](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/math-manuscript.jpg)

*图片来源：克雷数学研究所馆藏的黎曼 1859 年手稿扫描，2026-11-21。*

Alpöge 在说明里回顾了分工。他说自己把两个想法带进一个可尝试的框架，再推动它们进入更一般的情形，Claude 则在接近结论时提出了关键技术步骤。这是作者对协作过程的自述，核验日志检验的是公开交付的形式化文件。

他选择先放出 Lean，之后再写出更易读的论证。对读者来说，四页说明交代的是思路和时间线，核心证据在归档源码里。形式化把论证拆成机器可以检查的定义、命题与推导。

## 核验走到了哪一步

Sutherland 公布的记录截至 11 月 20 日 22:05 UTC，即北京时间 21 日 06:05。记录区分了源码编译、逐模块重放和完整导出检查，也写明了各项检查使用的工具。

![MIT 数学家 Andrew Sutherland 的核验结果表](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/math-mit-excerpt.png)

*图片来源：Andrew Sutherland 核验记录原文与表格摘录（UTC），2026-11-20。*

| 检查 | 这份快照的结果 | 它回答的问题 |
| --- | --- | --- |
| 编译与逐模块重放 | 175,187 个模块通过 | 已发布模块能否被指定环境接受 |
| 公理审计 | 五条主要定理只依赖 Lean 的三条标准公理 | 推导是否额外依赖自定义公理 |
| 独立内核和命题比对 | nanoda、con-leche 接受完整导出，con-leche 另确认五个目标命题 | 不同实现如何核对证明对象与目标 |

记录同时列出尚未完成的端到端 Comparator 和导出文件的 Lean 内核重放。这里有两种重放对象：已经通过的是按源码模块重放，仍需时间的是把导出的完整证明作为输入走另一条核验路径。

![Lean 官方核验文档原文摘录（取阅）](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/math-lean-excerpt.png)

*图片来源：Lean 官方核验文档原文摘录（取阅），2026-11-21。*

Lean 官方文档解释了为什么还要比对命题。内核检查某个形式命题能否从给定定义、公理与推导得出。命题比对接着检查提交中的定义和结论有没有准确表达原题，例如目标是否真的使用 Mathlib 对黎曼猜想的定义。

## 机器检查之后，数学家还要读什么

独立内核接受结果，可以让同行将精力转到数学结构。同行可以逐步追问，哪些想法真正不可替代，哪些引理可以缩短，哪些技巧还能用到相邻问题。Alpöge 预计论证能够简化，并表示会整理一份可读的写法。陶哲轩也公开讨论了证明的大致结构，指出消化这些数学内容需要时间。

![克雷数学研究所千禧年奖规则（Prize Rules）](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/math-rules-excerpt.png)

*图片来源：克雷数学研究所千禧年奖规则原文摘录（取阅），2026-11-21。*

克雷数学研究所的规则要求拟议解答发表于合格渠道、发表满两年，并获全球数学界普遍认可，才会进入奖金审议。这套流程和仓库里的机器核验记录回答的是不同问题。

这次发布让人看到，AI 协助提出证明之后，机器核验和同行解读怎样接力。

## 🔗 引用链接

- [Alpöge 的公告与 14 卷文件入口](https://alpo.ge/remark.pdf)
- [克雷数学研究所：黎曼猜想](https://www.claymath.org/millennium/Riemann-Hypothesis/)
- [克雷数学研究所：黎曼 1859 年手稿](https://www.claymath.org/collections/riemanns-1859-manuscript/)
- [Andrew Sutherland 核验快照](https://math.mit.edu/~drew/grh/)
- [Lean 官方证明核验文档](https://lean-lang.org/doc/reference/latest/ValidatingProofs/)
- [陶哲轩对形式化与数学结构的初步说明](https://terrytao.wordpress.com/2026/11/20/unforced-euler-singularity-unforced-navier-stokes-singularity-and-the-generalized-riemann-hypothesis/)
- [克雷数学研究所千禧年奖规则](https://www.claymath.org/millennium-problems/rules/)
