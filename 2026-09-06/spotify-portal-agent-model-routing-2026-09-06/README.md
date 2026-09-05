---
title: "Spotify 给 Claude Code 加了分流阀"
date: 2026-09-06
weekday: 星期日
category: AI 工程实践
slug: spotify-portal-agent-model-routing-2026-09-06
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/spotify-portal-agent-model-routing-2026-09-06.png
description: "Spotify 用 Portal、AiKA Modes 与 Claude Code hooks，把大文件批量读取和样板代码生成交给低价模型；作者在一组 Java monorepo 基准中报告批量读取平均少用约 90% Claude token。"
tags: [Spotify Portal, AiKA Modes, Claude Code, 模型路由, token成本]
---

# Spotify 给 Claude Code 加了分流阀

2026 年 9 月 3 日，Spotify 产品经理负责人 Dimitri Mazmanov 公布了一组很具体的实验：在一个大型 Java monorepo 里，他没有让 Claude Code 亲自吞下每个大文件，而是把批量读取交给便宜的小模型，只把压缩后的答案送回来。三个读取场景的 Claude token 平均减少约 **90%**。

这个数字不是 Portal 的通用承诺，更不是所有仓库都能复现的固定比例。它来自作者选择的文件、问题、阈值和模型配置。不过，实验背后的工程判断很容易复用：**前沿模型负责意图、取舍与复核；低价模型负责边界清楚、结果易验的读写工作。模型路由的依据应是任务风险，而不是“能不能调用另一个 agent”。**

![Spotify Portal 模型分流专题封面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/spotify-portal-agent-model-routing-2026-09-06.png)

## 90% 只属于这组读取实验

![Spotify Engineering 为 shunt 模型分流文章制作的主题图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/spotifyportal-source-feature.png)

*图：Spotify Engineering 文章配图，2026-09-03。来源：[Spotify Engineering](https://engineering.atspotify.com/2026/9/portal-by-spotify-cut-my-claude-code-token-usage-by-90)*

作者测了四类任务。前三类是 `bulk-reader`：小模型读取完整文件，再按问题返回简洁摘要；第四类是 `code-writer`：小模型参照现有文件生成新代码，并直接写入目标路径。

作者分别测试了单个大文件、源码与测试组合、跨服务文件，以及样板代码生成。工程博客给出的总体结论是：前三类批量读取场景平均少用了约 90% Claude token。

这里的分母是“Claude 直接读取原文件所消耗的 token”，不是完整账单，也没有把小模型调用、Portal 服务、网络等待和复核成本折成同一种货币。

`code-writer` 更不能与前三项直接相除。原流程是 Claude 读参考文件并生成内容；分流后则由 worker 直接写盘。作者展示的是上下文转移，不是同口径的成本降幅。

所以，更准确的说法是：**在作者这组 Java 仓库实验里，批量读取场景平均少用了约 90% Claude token。** 换成小文件、复杂调试或不同摘要要求，比例会变化。

## hooks 守住上下文入口

![AiKA 聊天界面中的 Mode 选择器](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/spotifyportal-source-mode-selector.png)

*图：AiKA 可按任务选择专用 Mode。来源：[Spotify Portal 官方文档](https://backstage.spotify.com/docs/portal/core-features-and-plugins/aika/modes)*

最初，作者只在 `CLAUDE.md` 里写一条建议：遇到大文件就用低价模型。问题是建议可能被忽略，而且每个项目都要复制。后来做成的 shunt 插件改用两个 Claude Code `PreToolUse` hooks，在工具调用发生前检查输入。

它的默认规则很克制：

- `Read` 要完整读取超过 **350 行**的文件时，阻止调用并提示改用 `/bulk-reader`；
- 带 `offset` 或 `limit` 的定点读取继续放行，Claude 仍能查看需要编辑的局部；
- Bash 中用 `cat`、`head`、`tail`、`less`、`more` 读取大文件时，同样会被检查；
- `cat file | grep pattern` 这类有明确筛选条件的读取允许通过；
- 阈值可用 `SHUNT_MIN_LINES` 调整，团队不必接受统一的 350 行。

这套规则没有把 Claude 与源码隔绝。它只拦截“整份搬进上下文”的高消耗动作，把局部检查、精确编辑和疑难推理保留给 Claude。拦截点选在工具入口，也比依靠提示词自觉更稳定。

强制规则主要覆盖读取。样板生成仍要由 Claude 判断何时调用 `code-writer`，并非所有写代码请求都会自动改道。这种不对称是合理的：读文件通常可按行数判断，写代码是否适合委托却取决于规范完整度和验证条件。

## Mode 是一份窄任务契约

![AiKA 创建 Mode 的官方界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/spotifyportal-source-create-mode.png)

*图：AiKA Mode 可设置名称、说明、模型与公开范围。来源：[Spotify Portal 官方文档](https://backstage.spotify.com/docs/portal/core-features-and-plugins/aika/modes)*

AiKA Mode 不是“再放一个万能助手”。官方把它定义为面向特定目的的 agent，可分别限定 instructions、model、tools 和 processors。文章中的两个公开 Mode 各自只有一个窄目标：

1. `bulk-reader` 读取一组文件，围绕提问返回简洁、结构化的事实；
2. `code-writer` 接收规格与必需的参考文件，只生成符合既有模式的代码。

作者示例给两个 Mode 配置了 **Gemini 2.5 Flash**，温度为 **0.2**。这只是实例选择，不是 Portal 的固定模型。Mode 的 `model` 字段可以指向 Portal 实例已经配置的任意模型，官方采用 `provider|model` 形式覆盖默认值。

```yaml
name: bulk-reader
model: google|gemini-2.5-flash
modelSettings:
  temperature: 0.2
instructions: |
  Read the supplied files and answer only the stated question.
  Return concise, structured findings.
```

真正可复用的是契约，而非模型名：输入范围清楚；输出格式足够窄；工具权限最小；结果可以被上层模型或自动检查快速验证。小模型一旦需要猜测隐含需求、长期维护对话状态或自行决定架构，节省出来的 token 很可能会以返工形式回来。

## 路由先问四个问题

![AiKA Mode 的 YAML 与 MCP 工具配置界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/spotifyportal-source-mode-yaml.png)

*图：Mode 可精确配置模型、说明与 MCP tools。来源：[Spotify Portal 官方文档](https://backstage.spotify.com/docs/portal/core-features-and-plugins/aika/modes)*

把这套做法搬到别的 Coding Agent，先不要按“读”与“写”粗分。更可靠的路由依据有四项：

- **可恢复性**：worker 出错后，能否删除结果并低成本重来；
- **可验证性**：是否有测试、格式检查、编译器或确定性规则判断对错；
- **上下文保真度**：摘要是否会丢掉行号、调用关系、并发语义等关键信息；
- **等待容忍度**：省下的 token 是否足以抵消一次额外网络往返。

| 任务 | 建议执行者 | 原因 | 必要保护 |
|---|---|---|---|
| 大文件事实提取、跨文件名单汇总 | 低价 worker | 输入大，推理窄，输出短 | 明确问题、保留文件名、抽查原文 |
| 按现有样例生成测试或配置 | 低价 worker | 模式重复，结果可编译或测试 | 必需参考文件、限定目标路径、自动校验 |
| 精确编辑与 bug 定位 | 前沿模型 | 依赖原文细节与多步推理 | 用 offset/limit 读取局部 |
| 架构、安全、并发判断 | 前沿模型加人工复核 | 漏掉隐含约束的代价高 | 禁止仅凭摘要决策 |

一个实用的准入条件是：只有当任务同时满足“边界清楚、失败可恢复、结果可机器验证”时，才进入低价通道。缺一项，就保留给能力更强的模型。

此外，worker 的输出最好返回“回答问题所需的最小证据”，而不是无差别缩写整份文件。摘要越通用，越容易重新膨胀；问题越具体，越可能把上下文压到真正有用的部分。

## token 省了，延迟与调用仍在

![AiKA Mode 的 processors 配置界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/spotifyportal-source-processors.png)

*图：Mode 可增加分类、规划、验证与置信度等 processors。来源：[Spotify Portal 官方文档](https://backstage.spotify.com/docs/portal/core-features-and-plugins/aika/modes)*

作者称其调用通常需要 **10—30 秒**，且其 Portal 环境将单次调用限制为 **30 秒**。若文件只有几百行，委托、上传、等待和取回答案可能比 Claude 直接读取更慢。这也是默认阈值存在的原因：路由需要一个“值得分流”的最低输入规模。

总账至少包含四部分：前沿模型输入成本、小模型输入输出成本、额外调用延迟、错误带来的复核与返工。只看第一项，会把“Claude token 下降”误写成“系统成本同幅下降”。

AiKA processors 也要单独计价。分类、规划、答案格式化、验证、置信度与上下文管理，每增加一个 processor，都会多一次前置或后置 LLM 调用。官方文档提醒，verification 若允许两轮 retry，验证成本最高可能变成原来的 **3 倍**。processors 可以另配便宜模型，但仍不是免费的。

更稳妥的预算方式是按任务记录：原始输入行数；worker 的输入与输出 token；Claude 最终接收量；端到端耗时；失败重试次数；抽查发现的遗漏数。连续观察一段时间后，再把 350 行改成适合自己仓库的阈值。

## 质量边界比价格边界更重要

![AiKA 官方搜索结果界面，答案附有可回看的来源](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/spotifyportal-source-search-results.png)

*图：AiKA 搜索结果保留来源入口，便于复核。来源：[Spotify Portal 官方文档](https://backstage.spotify.com/docs/portal/core-features-and-plugins/aika/getting-started)*

作者在实验里遇到过一个明确反例：小模型能找出表层模式，却漏掉了细微的线程安全 bug；Claude 获得正确上下文后很快识别出来。这说明“能总结文件”不等于“能承担代码判断”。

有三类任务不适合只看 worker 摘要：

- **编辑**：摘要里的行号和局部结构可能不可靠，修改前应让 Claude 精确读取目标片段；
- **调试**：根因常藏在跨文件状态、时序和异常路径里，压缩会删除看似无关的证据；
- **架构与安全**：遗漏一个并发、权限或数据边界，代价远高于节省的 token。

样板生成也必须带可执行验收。测试文件要真正运行，配置要通过 schema 检查，代码要编译或类型检查。若生成内容直接写盘而上层模型完全不看，自动验证就不是附加项，而是使用低价 worker 的前提。

AiKA 每次调用还是临时的，worker 不会自然记住上一次传过的文件。后续请求若依赖旧上下文，需要重新发送。对持续调试会话，这会抵消一部分节省；对一次性的批量提取，影响则较小。

## 数据边界必须跟着请求走

![AiKA 官方数据流图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/spotifyportal-source-data-flow.png)

*图：查询与上下文会流向所选 LLM provider、工具及可选 tracing collector。来源：[AiKA Data Flow & Security](https://backstage.spotify.com/docs/portal/core-features-and-plugins/aika/data-flow)*

分流并不意味着数据留在 Claude Code 本机。Portal 官方数据流说明，查询与上下文会进入客户选择的 LLM provider，也可能进入配置的 MCP server 和可选 tracing collector。Spotify 表示不会用客户数据训练模型，但团队仍需审查自己所选 provider 与工具的数据政策。

权限配置也有一个容易忽略的细节：Mode 可以按 MCP server 和具体 tool 做白名单；如果只添加 server 而不列 tools，该 Mode 会获得该 server 的全部工具。对只做批量读取的 worker，写文件、发消息或改资源的权限都没有必要。

因此，低价通道至少应做到：

- 只发送完成任务所需的文件，不把整个仓库默认打包；
- 对 secrets、客户数据和安全代码设置排除规则；
- 显式列出允许的 MCP tools，并限制最大执行步数；
- 记录 provider、保留策略与 tracing 去向；
- 让高风险目录绕过分流，直接进入受控审查。

成本优化只有在数据边界清楚时才成立。否则，少掉的是 token，增加的却是一个新的外部处理面。

## 模型可以换，任务分层要留下

![Spotify Portal AI Gateway 的模型配置界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/09/06/spotifyportal-source-ai-gateway.png)

*图：Portal AI Gateway 统一配置不同 provider 与模型。来源：[Spotify Portal AI Gateway](https://backstage.spotify.com/docs/portal/core-features-and-plugins/ai-gateway)*

Portal AI Gateway 官方列出的 provider 包括 Anthropic、AWS Bedrock、Azure OpenAI、OpenAI、Cohere、Google Generative AI 与 Ollama。AiKA 也允许用 `aika.models.language` 覆盖默认语言模型。这意味着同一份任务契约可以更换 worker，不必把路由写死在 Gemini 2.5 Flash 上。

模型替换后，应重新测三项：摘要遗漏率、可执行产物通过率、端到端成本。价格更低但遗漏更多的模型，未必便宜；能力更强却输出冗长的模型，也可能让 Claude 的上下文再次变大。选择 worker 的目标不是最低单价，而是最低的“合格结果成本”。

## 从一个读取任务开始校准

模型分流最容易失败的地方，是一开始就想覆盖整套开发流程。更稳的起点，是选择一种高频且可复查的读取任务，例如“从十个服务文件里列出所有外部依赖，并附文件名”。它有明确问题，也能由工程师抽取部分文件核对。

第一周可以只记录，不强制拦截。每次任务保存原始文件行数、Claude 直接读取量、worker 摘要长度和耗时，再对摘要做人工抽查。这样能得到自己的基准，而不是照搬 90%。

第二周再加入软提示。超过候选阈值时，允许 Claude 在直接读取与 worker 之间选择，并记录它为什么绕过分流。常见理由包括需要精确行号、正在追踪异常路径、文件虽长但目标区域很窄。这些理由会帮助团队把例外规则写得更准。

第三周才适合启用强制 hook，并保留三条退路：

- 用户或维护者可针对单次任务明确绕过；
- Claude 可用定点读取复核摘要中的关键片段；
- worker 失败、超时或输出缺少来源时，自动退回直接读取。

验收指标也不应只有 token。至少同时看摘要事实遗漏率、错误引用率、平均等待时间、绕过比例和返工次数。若 token 降得很多，返工却持续增加，说明任务契约过宽，或 worker 模型能力不够。

样板生成可以晚一步启用。先从测试夹具、重复配置和已有格式非常稳定的文件开始，并把“生成后必须通过什么命令”写进 Mode。没有现成校验命令的产物，暂时不应直接写盘。

这套渐进过程还有一个好处：阈值会成为经验数据，而不是管理者拍下的数字。某些仓库 200 行就有大量生成代码，适合早分流；另一些仓库单文件虽长，却充满宏、并发或安全逻辑，1,000 行也不该只看摘要。真正有效的阈值总要结合目录、文件类型和任务意图。

回到那组 90%：它最有价值的部分不是一个漂亮比例，而是一条可验证的分工线。大模型上下文应留给真正需要判断的内容；批量搬运和样板生成可以交给便宜模型，但必须同时带上阈值、权限和验收。只要这三件事还在，即使明天换掉 Portal、Claude 或 Gemini，这套模型路由仍然成立。
