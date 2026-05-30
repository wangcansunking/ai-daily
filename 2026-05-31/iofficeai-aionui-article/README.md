# 开源项目 AionUi 免费聚合 Claude Code、Gemini CLI 等 20+ AI 助手

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/iofficeai-aionui-article/head.png)

# 一个开源项目把 Claude Code、Gemini CLI 和 18 个 AI Agent 塞进了同一个界面——而且免费

Claude Code 的用户最近有个共同的烦恼：$100/月的订阅费交着，但 Claude 只能在 macOS 上跑，想用 Gemini 还得另开一个终端，想切 DeepSeek 又得换 API key。多 Agent 协作？想都别想——你只能手动在两个窗口之间复制粘贴。

![iOfficeAI/AionUi — Free, local, open-source 24/7 Cowork app for OpenClaw, Hermes](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/iofficeai-aionui-article/content-1.png)

Anthropic 的产品经理大概没料到，这个痛点会被一个 GitHub 仓库用 Electron 一把梭哈解决。

![Built-in Agents](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/iofficeai-aionui-article/content-2.png)

iOfficeAI/AionUi——一个 2025 年在 GitHub 上迅速蹿红的 TypeScript 项目，做的事情简单到粗暴：把 20+ 个 CLI AI Agent（Claude Code、Codex、Gemini CLI、Hermes Agent、OpenClaw、Cursor Agent……）全部塞进一个桌面应用，自动检测你装了哪些，统一界面管理，然后——免费开源。

**一个 Electron 壳，捅破了 Anthropic $100/月订阅费筑起的护城河。**



![AionUi 多 Agent 统一界面截图，左侧 Agent 列表，右侧并行会话窗口](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/iofficeai-aionui-article/schematic-1.png)



## 不是聊天客户端，是"同事"——AionUi 的产品定义为什么值得拆解

我第一眼看到这个项目的 README 时，注意到一个刻意重复的词：**Cowork**。不是 Chat，不是 Assistant，不是 Copilot——是 Cowork（同事）。

项目官方是这么定义的：

> AionUi is more than a chat client. It's a Cowork platform where AI agents work alongside you on your computer — reading files, writing code, browsing the web, and automating tasks. You see everything the agent does, and you're always in control.

这个措辞不是随便写的。传统的 AI Chat 客户端（包括 ChatGPT Desktop、Claude Desktop）本质上是"对话窗口 + 模型 API"——AI 只能在你主动发消息时响应，无法主动操作文件系统、无法执行多步骤任务、无法定时自动运行。

AionUi 的做法是把 Agent 的执行能力内置进桌面应用：文件读写、网页搜索、图片生成、MCP（Model Context Protocol）工具调用——所有这些不需要额外安装 CLI 工具，装好 AionUi 就能用。它内置了一个完整的 Agent 引擎，用 Google 账号登录或粘贴任意 API key 就能启动。

这让我想起 2024 年 Rabbit R1 和 Humane AI Pin 试图做但没做成的事——让 AI 真正"操作"你的设备。区别在于，AionUi 没造硬件，直接在操作系统层面用 Electron 的权限做到了。

（值得一提的是，这个项目支持 30+ AI 平台，从 Gemini、OpenAI、Anthropic 到国内的 Dashscope 通义千问、智谱、月之暗面 Kimi、百度千帆、腾讯混元，甚至包括本地部署的 Ollama 和 LM Studio——你能想到的模型接入方式它几乎都覆盖了。）

**免费，本地存储，任何 API key——这三个词组合在一起，对闭源收费的 AI 桌面客户端市场是一次精准的"降维打击"。**

## 21 个内置助手 + OfficeCLI：PPT、Word、Excel 的自动化暗线

AionUi 最让我愣神的设计，不是它支持了多少个 Agent，而是它内置了一套完整的 Office 文档生成流水线。

项目仓库里有一个 `assistant/` 目录，里面定义了 21 个专业助手：Cowork（通用任务执行）、PPT Creator、Morph PPT、Morph PPT 3D、Pitch Deck Creator、Dashboard Creator、Word Creator、Word Form Creator、Excel Creator、Academic Paper Writer、Financial Model Creator、3D Game、UI/UX Pro Max（57 种风格、95 种配色方案）……

这些助手不是简单的 prompt 模板。它们背后是 AionUi 自研的 **OfficeCLI**——一个专门处理 `.pptx`、`.docx`、`.xlsx` 文件生成的技能引擎。仓库里的 `skills/` 目录包含了 `pptx`、`docx`、`pdf`、`xlsx`、`mermaid` 等内置技能，用户还可以在 `skills/` 目录下创建自定义技能，甚至用 Extension SDK 加载第三方扩展技能。



![AionUi 三层技能架构示意——Builtin / Custom / Extension，每个技能对应文件格式能力](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/iofficeai-aionui-article/schematic-2.png)



具体的输出能力是这样的：

- **PPT 助手**：生成可编辑的 Morph PPT（`.pptx`），带 Morph 动画的幻灯片过渡效果
- **Word 助手**：输出可编辑的 Word 文档（`.docx`），支持论文写作和生产级文档编辑
- **Excel 助手**：生成可用的 Excel 表格（`.xlsx/.xlsm/.csv`），支持数据分析、自动格式化和图表

这套系统让我想到的是——Notion AI 和 Gamma 在文档生成上收了用户不少钱，而 AionUi 用开源的方式把同样的能力免费放了出来。而且因为它是本地运行 + 任意 API key，你完全可以用 Gemini 的免费配额来驱动这些 Office 助手。

一个具体的场景：用户说"帮我生成一份 Q3 销售数据报告的 PPT"，内置的 Cowork Agent 会调用 PPT Creator 助手，用 OfficeCLI 的 `pptx` 技能生成带 Morph 动画的演示文稿，然后内置的 Preview Panel 支持直接预览 `.pptx` 文件——整个过程不需要打开 PowerPoint。

**Anthropic 收了 $100/月只给你一个 Claude，AionUi 免费给了你 21 个专业助手加一个完整的 Office 自动化引擎。**

## 多 Agent 协作：Team Mode 的技术野心

如果说内置 Office 助手是"广度"，那 Team Mode 就是 AionUi 在"深度"上最激进的设计。

这个模式允许用户把多个 AI Agent 组织成一个团队：一个 **Leader** Agent 接收用户指令，拆解成子任务，用内置的 Team MCP Server 分发给多个 **Teammate** Agent 并行执行。Teammate 之间用异步邮箱共享结果，写入共享任务面板。



![Team Mode 架构——Leader 拆解任务 → Team MCP Server 分发 → Teammate 并行执行 → 异步邮箱汇总](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/iofficeai-aionui-article/schematic-3.png)



具体的技术细节，从 README 里能挖出这些：

- **并行多 Agent 执行**：Leader 将任务拆解为子任务并委托给 Teammate Agent 并行运行；每个 Teammate 使用自己的模型，用 ACP（Agent Communication Protocol，AionUi 的多 Agent 协调层）、Gemini 或 Aionrs 通信
- **Leader 编排**：Leader 负责分配、跟踪和汇总结果；支持的后端包括 Claude Code、Codex、Hermes Agent、Gemini、Snow CLI 和 Aion CLI
- **团队隔离工作区**：所有 Agent 共享同一个文件夹，但每个 Agent 有独立的权限确认对话框，侧边栏有徽章显示待审批操作
- **动态扩缩**：团队运行时可以添加或移除 Teammate，静默的 Agent 会自动升级为失败状态并支持一键移除
- **文件共享**：Leader 可以将文件附件传递给 Teammate

这个设计的野心在于——它试图解决当前 AI Agent 领域最头疼的问题之一：**单个 Agent 处理复杂任务时上下文窗口不够用、执行路径容易跑偏**。用把任务拆解给多个专业化 Agent 并行处理，理论上可以大幅提升复杂任务的完成率。

（不过说实话，多 Agent 协作在 2025 年仍然是一个"听起来很美、跑起来很痛"的方向。Agent 之间的通信协议、任务分配的准确性、并行执行的冲突处理——这些问题在学术界都还没解决好。AionUi 的 Team Mode 能跑通多少场景，可能要看社区的实际反馈。）

**多 Agent 协作不是新概念，但把它做成免费开源的一键启动功能——AionUi 是第一个。**

## 24/7 自动化 + 全平台远程访问：把 AI Agent 变成"永不停机的同事"

AionUi 另一个让我觉得"这项目想得很清楚"的设计，是它的定时任务系统和远程访问能力。

先说定时任务。用户可以用自然语言告诉 Agent 要做什么，然后选择三种调度模式：

- **标准 Cron 表达式**（带时区支持，如 `0 9 * * 1` + `Asia/Shanghai`）
- **固定间隔**（每 N 分钟/小时执行一次）
- **一次性触发**（指定时间执行一次后自动禁用）

执行模式也分两种：**继续现有会话**（追加到绑定的对话中，AI 保留完整上下文历史）或**每次创建新会话**（每次触发开启全新会话，适合独立的周期性报告）。

这意味着什么？你可以设置一个定时任务，让 Gemini 每天早上 9 点自动抓取天气数据生成日报，或者让 Claude Code 每周一自动聚合销售数据生成 Excel 报表——完全无人值守。

项目 README 里还提到一个细节：AionUi 会在任务活跃时自动阻止系统休眠，并在唤醒后检测错过的触发器。这个"Keep-awake"机制说明开发团队真的在考虑 24/7 运行的边缘场景。



![定时任务配置界面——Cron 表达式输入、时区选择、执行模式切换](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/iofficeai-aionui-article/schematic-4.png)



再说远程访问。AionUi 支持五种远程控制方式：

- **WebUI 模式**：用浏览器从手机、平板或任何电脑访问；支持局域网、跨网络和服务器部署，用二维码或密码登录
- **Telegram 集成**：直接在 Telegram 中与 AI Agent 协作
- **Lark（飞书）集成**：用飞书机器人企业协作
- **DingTalk（钉钉）集成**：AI Card 流式响应，带自动降级
- **WeChat（微信）集成**：个人微信账号集成

这个设计直接把 AionUi 从"桌面工具"升级成了"可部署的 AI Agent 服务器"。你可以在办公室的电脑上跑着 AionUi，然后在地铁上用手机用 WebUI 查看 Agent 的执行进度，或者用 Telegram Bot 给它发新任务。

**Claude Code 只能在你盯着屏幕时工作。AionUi 在你睡觉时还在跑——而且你可以用微信指挥它。**

## 开源社区的"野蛮生长"：5500 Stars 背后的信号

根据项目 README 引用的社区文章，AionUi 在发布后迅速积累了 **5500+ GitHub Stars**，被社区称为"用开源打破 Anthropic AI 工具护城河"的项目。

YouTube 上，拥有 38.4 万订阅的 SEO 专家 Julian Goldie 发布了两条关于 AionUi 的视频——"Hermes + Aion UI is Insane (FREE!)"（2.7 万次观看）和"OpenClaw + Aion UI is Insane (FREE!)"（1.1 万次观看）。20 万订阅的 WorldofAI 频道也做了相关评测。

社区文章的数量也在增长。一篇来自"AI 硅基时刻"的文章标题直接叫《5500 Stars: How Open Source Breaks Anthropic's AI Tool Moat》，另一篇来自"懒猫爱钓鱼"的文章标题是《让普通人像使用 APP 一样使用 Claude Code》。

这些声音指向同一个趋势：**用户对 AI 工具的"平台锁定"越来越不耐烦了。**

Anthropic 的 Claude Code 只支持 macOS + Claude 模型。OpenAI 的 ChatGPT Desktop 虽然跨平台但 Agent 能力有限。Google 的 Gemini 有免费配额但桌面体验不如网页版。Cursor 和 Copilot 专注代码但不管 Office 文档。

AionUi 的做法是"全都要"——macOS / Windows / Linux 全平台、30+ AI 平台、20+ CLI Agent 自动检测、21 个内置助手、Office 文档生成、定时任务、远程访问、多 Agent 协作。而且所有数据存储在本地 SQLite 数据库，不上传任何服务器。

（这个"本地 SQLite"的设计也意味着——你的 API key、对话历史、文件操作记录全部留在你自己的机器上。对于企业用户来说，这是一个重要的合规优势。）



![AionUi vs Claude Cowork 功能对比表——OS 支持、模型支持、交互方式、自动化、成本](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/iofficeai-aionui-article/schematic-5.png)



## 隐忧与天花板：开源项目的"甜蜜负担"

看到这里你可能会问：这东西这么好，为什么我之前没听说过？

答案可能藏在项目的发展阶段里。AionUi 目前是一个典型的"社区驱动型开源项目"——功能丰富但文档可能不够完善，多 Agent 协作的稳定性有待验证，OfficeCLI 生成复杂文档的质量可能比不上专门的商业工具。

还有几个现实问题：

**第一，API 费用。** 虽然 AionUi 本身免费，但它驱动的 AI Agent 需要消耗 API 调用。用 Gemini 的免费配额跑简单任务没问题，但如果你用 Claude Code 用 Anthropic API 跑复杂任务，费用可能比直接订阅 Claude Pro 还高。

**第二，多 Agent 协作的可靠性。** Team Mode 的 Leader-Teammate 架构在理论上很优雅，但在实际运行中——任务拆解的准确性、Agent 之间的通信延迟、并行执行的资源竞争——这些问题在单机 Electron 应用里能解决到什么程度，需要大量用户反馈来验证。

**第三，Electron 的性能开销。** 虽然 Electron 让跨平台开发变得容易，但它的内存占用和启动速度一直是被诟病的点。对于一个需要 24/7 运行的 Agent 平台来说，4GB+ 的推荐内存配置可能在长时间运行后变得紧张。

**第四，社区维护的可持续性。** 开源项目最大的敌人不是竞争对手，而是维护者 burnout。当 Issues 堆积、PR 需要 review、文档需要更新时，一个靠热情驱动的团队能撑多久——这是所有开源项目都要面对的"甜蜜负担"。

**免费的东西最贵——贵在你要花时间踩坑，贵在你要忍受不完美的体验，贵在你要祈祷维护者不要跑路。**

## 参考来源

- https://github.com/iOfficeAI/AionUi （项目主页，所有功能描述、技术细节、数字数据均来自 README）
- https://github.com/iOfficeAI/AionUi#cowork--ai-agents-that-work-alongside-you （Cowork 产品定义章节）
- https://github.com/iOfficeAI/AionUi#built-in-agent--install--go-zero-configuration （内置 Agent 引擎章节）
- https://github.com/iOfficeAI/AionUi#office-assistants--ppt-word--excel （Office 助手章节）
- https://github.com/iOfficeAI/AionUi#multi-agent-mode--already-have-cli-agents-bring-them-in （多 Agent 模式章节）
- https://github.com/iOfficeAI/AionUi#team-mode--coordinated-multi-agent-collaboration （Team Mode 章节）
- https://github.com/iOfficeAI/AionUi#scheduled-tasks--cowork-on-autopilot （定时任务章节）
- https://github.com/iOfficeAI/AionUi#cowork-from-anywhere （远程访问章节）
- https://github.com/iOfficeAI/AionUi#why-choose-aionui-over-claude-cowork （与 Claude Cowork 对比表）
- https://github.com/iOfficeAI/AionUi#community-articles （社区文章引用，含 5500 Stars 数据）

#AionUi #Free #Cowork #OpenClaw #Hermes

![AI Assistants & Skills Ecosystem](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/iofficeai-aionui-article/content-3.png)
