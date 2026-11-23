---
title: "Modern Web Guidance：让编程智能体少写过时的前端代码"
date: 2026-11-19
weekday: 星期四
category: 开源
slug: github-modern-web-guidance-coding-agents-2026-11-19
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/github-modern-web-guidance-coding-agents-2026-11-19.png
description: "GoogleChrome/modern-web-guidance-src 11 月 19 日上午位列 GitHub Trending 周榜第一，本周新增 7,257 星。Chrome、Edge、Firefox、MDN 团队与 Web 开发社区共同维护这套技能。官方评测中，最佳实践检查通过率从 52% 升至 92%。"
tags: ["Modern Web Guidance","Chrome","Agent Skill","GitHub Trending"]
track: github-trending
batch: noon
repos: ["GoogleChrome/modern-web-guidance-src"]
---

# Modern Web Guidance：让编程智能体少写过时的前端代码
![小机器人与戴安全帽的工人手持 Modern Web 手册，身后是正在组装的网页与回收的旧零件](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/github-modern-web-guidance-coding-agents-2026-11-19.png)
*图片来源：Sunky AI 日报《Modern Web Guidance：让编程智能体少写过时的前端代码》配图，2026-11-19。*

**训练数据里的旧代码容易让编程智能体沿用几年前的前端写法。沿用同一智能体，按任务把现代实现和兼容条件送进上下文，就能用浏览器测试检验它是否写得更合适。** 11 月 19 日上午，Modern Web Guidance 的源码仓库位列 GitHub Trending 周榜首位。项目把浏览器团队维护的知识做成可检索的技能包，再用有无指南的对照评测衡量它对生成代码的影响。[1][3][4]

## 周榜热度落在一个编写和评测指南的源码仓库

![GitHub Trending 本周榜单，GoogleChrome/modern-web-guidance-src 位列第一，显示本周新增 7,257 星](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-github-trending-weekly-modern-web-guidance.png)
*图片来源：GitHub Trending（This week）页面截图，2026-11-19。*

登上周榜的是指南的源码仓库。它包含编写、校准和评测指南的代码与文档，供开发者使用的技能内容对应发行仓库 modern-web-guidance。对试用者来说，源码仓库提供了检查指南如何制作、如何判分的入口，安装则走命令行工具。[1][2]

| 项目 | 记录 |
| --- | --- |
| GitHub Trending 周榜 | 2026 年 11 月 19 日约 09:55（北京时间）位列第一，本周新增 7,257 星 [4] |
| 累计星标 | 同日约 09:55（北京时间）周榜页面记录为 8,851 星，10:04 的仓库统计为 8,860 星 [1][4] |
| 日榜 | 当日未进全站及 TypeScript 日榜前列，无官方今日增量数字 [4] |
| Fork 数 | 11 月 19 日上午为 739 [1] |
| 许可证 | 仓库软件代码采用 Apache-2.0 [1] |
| 创建时间 | 2026 年 1 月 27 日 [1] |
| 维护方 | Google Chrome、Microsoft Edge、Mozilla Firefox、MDN 团队与 Web 开发社区 [1] |

周榜页面记录与随后仓库统计的累计星标相差 9 星。配图也标出了本周新增 7,257 星。日榜没有给出该仓库的当日增量。[1][3][4]

- 对编写或维护指南的人，仓库里同时有写法和评测材料，可以追踪一条指南对应什么行为。
- 对使用指南的人，Chrome、Edge、Firefox 与 MDN 共同参与维护，使浏览器支持情况成为实现建议的一部分。具体项目仍要依据自身支持的浏览器决定如何采用。[1][2]

## 把原生写法和兼容条件一并交给智能体

![GoogleChrome/modern-web-guidance-src 的 GitHub 社交预览卡，副标题为让编程智能体跟上最新 Web 最佳实践](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-github-modern-web-guidance-social-preview.png)
*图片来源：GitHub 社交预览图，2026-11-19。*

指南在智能体选择实现方案时提供信息。按项目的解释，旧代码在训练数据中大量存在，模型即便知道新 API 的名字，也可能缺少足够多的现代实现示例。前端改动因而可能继续沿用旧模式，用一段臃肿的 JavaScript 重做浏览器已有的行为，或为覆盖旧环境引入体积较大的 polyfill。代码能完成需求，维护者仍要承担额外的兼容逻辑。[1]

项目的覆盖范围延伸到页面结构和行为。平台特性的统计按 CSS 与布局、HTML 与 DOM、JavaScript 与 API 分组。[1]

- **CSS 与布局覆盖 140 项特性。** 指南涉及组件布局和界面状态变化，可让智能体在增加脚本前先考虑浏览器的布局与样式能力。
- **HTML 与 DOM 覆盖 37 项特性。** 弹窗等界面涉及元素语义和交互，指南能把使用原生元素时需要考虑的行为带进实现。
- **JavaScript 与 API 覆盖 85 项特性。** 对确实需要脚本的需求，指南可以把 API 的用法和浏览器支持条件一起交给智能体。[1]

![Chrome for Developers 的 Modern Web Guidance 文档首页，展示安装命令、Vercel Agent Skills 和 Claude Code 的安装选项](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-chrome-docs-modern-web-guidance.png)
*图片来源：Chrome for Developers《Modern Web Guidance》文档页截图，2026-11-19。*

官方列出的两个技能包里，`modern-web-guidance` 约为 205 tokens，覆盖浏览器 API、布局和性能写法。`chrome-extensions` 约为 175 tokens，面向 Chrome 扩展开发。命令行工具按当前任务返回相关指南，项目也会用评测删减智能体已经掌握的内容。包的标称长度说明了起始提示的规模，实际任务还会读入相关指南。[1][2]

兼容处理取决于功能在产品里的作用。拿弹窗举例，遮罩动画可以视情况退化，打开和关闭却属于必须保留的交互。指南对渐进增强和必要的回退分别处理，优先采用轻量的替代写法，或按条件加载 polyfill。这样，同一个现代 API 能否进入实现，就可以结合目标浏览器和用户需要完成的操作来判断。[1]

## 评测先用黄金解和基线校准，再比较生成结果

![评测看板顶部，最佳实践通过率提升 40 个百分点，从 52% 到 92%，无障碍检查提升 38 个百分点](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-modern-web-guidance-eval-dashboard-top.png)
*图片来源：Modern Web Guidance Evals 看板截图（最近一次评测运行日期为 2026-11-15），2026-11-19。*

官方用浏览器中的行为检验指南效果。项目从专家指南和演示出发，在完整的 Web 应用 daily-grind 和 devtools-times 上设计任务，准备参考实现，也就是黄金解，再用 Playwright 写自动判分器。判分器包含静态检查和浏览器测试。任务提示词只描述要实现的效果，不点名 API，因此智能体要自己决定查询什么指南、选择哪种实现。[1][3]

- 判分器以黄金解通过全部检查为校准目标，目标通过率为 100%。
- 判分器同时以未修改的基线通过率为 0% 为校准目标，以排除应用原本就满足检查条件的情况。
- 判分器校准后，官方对比提供指南和不提供指南的端到端结果。同一智能体在两种条件下完成相同任务，差值反映指南在这套评测中的效果。[1]

看板汇总各模型最近一次评测运行的结果，最近的运行日期为 11 月 15 日。最佳实践指标涵盖 1,114 个任务、约 9,400 项检查，涉及 3 种智能体和 5 次评测运行。表中的每个通过率都以相应组别的检查总数为分母，任务数用于说明评测的覆盖规模。[3]

| 评测项目 | 未提供指南 | 提供指南 | 变化 |
| --- | ---: | ---: | ---: |
| 最佳实践检查，全部智能体 | 52% | 92% | 增加 40 个百分点 |
| 最佳实践检查，Claude Code | 50% | 91% | 增加 41 个百分点 |
| 最佳实践检查，Codex CLI | 50% | 87% | 增加 37 个百分点 |
| 最佳实践检查，Antigravity CLI | 54% | 95% | 增加 41 个百分点 |
| 自动化无障碍检查 | 55% | 93% | 增加 38 个百分点 |

![按智能体分组的评测表，显示 Claude Code、Codex CLI 和 Antigravity CLI 在有无指南时的最佳实践与无障碍检查通过率](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-modern-web-guidance-eval-by-agent.png)
*图片来源：Modern Web Guidance Evals 看板按智能体分组截图，2026-11-19。*

各智能体在最佳实践检查上都取得了更高的通过率，说明改善并未集中在看板上的某一个工具。无障碍一栏只反映这套自动化检查的结果，完整的《Web 内容无障碍指南》（WCAG）审计超出看板的覆盖范围。产品验收时，实际页面上的交互和使用体验仍要由团队验证。[3]

同一项任务可以包含多项检查。评测表衡量的是实现要求逐项通过的比例，验收整项改动还需要项目自己的测试。[1][3]

## 从一项浏览器行为试起，再用自己的代码库复测

![GitHub 上 GoogleChrome/modern-web-guidance-src 仓库主页的目录与简介](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/19/source-github-modern-web-guidance-repo.png)
*图片来源：GitHub 仓库页面截图，2026-11-19。*

命令行工具允许先看指南，再决定是否装进智能体。安装命令会打开交互式向导，需要选择技能包时可以加 `--choose`。其余命令可在未安装技能的情况下试用。[1][2]

```bash
npx modern-web-guidance@latest install
npx modern-web-guidance@latest search "animate a dialog modal backdrop"
npx modern-web-guidance@latest retrieve "animate-to-from-top-layer"
npx modern-web-guidance@latest legacy-scan .
```

- `search` 接收需求描述。示例从“给模态弹窗的遮罩加动画”入手，可以看到工具会找到什么指南。
- `retrieve` 按指南 ID 取回具体内容，便于在接受改动之前核对建议的写法和兼容条件。
- `legacy-scan .` 扫描当前目录下代码中的旧模式。扫描结果可以作为代码审查的线索，保留还是迁移要看原有行为和兼容要求。[1]

项目可用根目录下的 `.mwgrc` 保存配置。指南里涉及试验性或仍不稳定的特性时，搜索可以发现这些指南，检索则受显式选择限制。对同一代码库里的成员，共用配置可以让试验特性的采用条件保持一致。[1]

遥测方面，谷歌在项目 README 中说明会收集匿名使用信息，包括安装量、调用工具的智能体名称、检索的指南 ID，以及智能体生成的命令行搜索词。原始用户提示词不在收集范围内。搜索词和原始提示词是不同的输入，试用涉及内部术语或产品细节时，团队可以按自己的数据规则判断哪些内容适合放进查询。[1]

项目的评测设计也适用于已有组件。以弹窗为例，在同一需求下比较有无指南的实现，再用目标浏览器和现有测试检查打开、关闭及键盘操作，可以判断简化代码后是否仍保留了所需行为。[1][3]

## 🔗 引用链接

- [1] GoogleChrome/modern-web-guidance-src 源码仓库与 README · https://github.com/GoogleChrome/modern-web-guidance-src
- [2] Chrome for Developers《Modern Web Guidance》文档 · https://developer.chrome.com/docs/modern-web-guidance
- [3] Modern Web Guidance Evals 官方评测看板 · https://googlechrome.github.io/modern-web-guidance-src/
- [4] GitHub Trending（This week）榜单页面，2026-11-19 北京时间约 09:55
