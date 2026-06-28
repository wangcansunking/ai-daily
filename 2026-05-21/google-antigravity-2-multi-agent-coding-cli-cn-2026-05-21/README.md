---
title: "Google Antigravity 2.0 vs Cursor 3 vs Claude Code 五方混战，国内开发者怎么用得起来"
slug: google-antigravity-2-multi-agent-coding-cli-cn-2026-05-21
date: 2026-05-21
weekday: 星期四
category: AI Coding / 多智能体 / 工具对比
cover: google-antigravity-2-multi-agent-coding-cli-cn-2026-05-21.png
track: domestic_hot
domain: ai-coding-stack
tags:
  - Google Antigravity 2.0
  - 多智能体 Coding
  - Antigravity CLI
  - Gemini 3.5 Flash
  - Cursor Composer 2.5
  - Claude Code
  - 千问 Coder (Qwen3-Coder)
  - Kimi K2.5
  - 通义灵码
  - Trae
description: "五月十九号晚上谷歌 I/O 2026 发布 Antigravity 2.0，把原来的 IDE 改成桌面 + Go 语言 CLI + SDK + Managed Agents 一整套，主打多 agent 并行 + 定时任务 + 四大斜杠命令（/goal /grill-me /schedule /browser）。本文按 TechCrunch、Google 官方迁移公告、MarkTechPost、36 氪、V2EX 实查口径，把斜杠命令的具体契约、subagent 并行架构、隔离 Linux 沙箱、AI Pro 20 美元 / Ultra 100 美元定价梳清，并把 Cursor Composer 2.5、Claude Code、千问 Coder（Qwen3-Coder）、Kimi K2.5 摆到一张五方矩阵里实测对比。同时给国内开发者一份冷启动可用度判断——VPN 要求、谷歌账号注册路径，以及国产平替通义灵码、Trae、Qwen Code、Cline 在同一题型上能否顶上去。"
---

# Google Antigravity 2.0 vs Cursor 3 vs Claude Code 五方混战，国内开发者怎么用得起来

![Google Antigravity 2.0 多智能体 Coding CLI 封面图 3D 工作站桌面渲染 反重力视觉概念 多个 AI agent 协作 蓝紫青光照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/google-antigravity-2-multi-agent-coding-cli-cn-2026-05-21/google-antigravity-2-multi-agent-coding-cli-cn-2026-05-21.png)

## 这一次不是发新模型，是把整个 IDE 推倒重做

五月十九号上午十点四十五分（太平洋时区），谷歌在 I/O 2026 开发者大会主舞台把 Antigravity 2.0 端上桌。一句话总结：把原来那个"装得像 Cursor"的 Antigravity 1.0 IDE 推倒重做，砍掉熟悉的左侧文件树 + 右侧编辑器布局，改成"左侧 Project 列表 + 右侧多 agent 对话工作台"——产品定位从「带 AI 的 IDE」直接换轨到「agent-first 工作平台」。同时端出 Go 语言写的 Antigravity CLI（接替即将下线的 Gemini CLI）、可嵌业务系统的 Antigravity SDK、跑在 Google 云上的 Managed Agents，以及给企业的 Gemini Enterprise Agent Platform。

工程意义只有一句：编辑器中心的范式正式让位给 agent 中心。Cursor 当年用「AI 写代码 + 你审」打掉 VS Code 的舒适圈，这一次谷歌把刀更进一步——AI 自己拆任务、自己开 subagent 并行、自己定时巡检、自己开浏览器测页面，你这边的工作变成「确认 plan、看汇总、批改红线」。国内同档的 Cursor Composer 2.5、Claude Code、Qwen3-Coder、Kimi K2.5，每一家都在往这个方向收，但谁先收齐这一套，对每天敲代码吃饭的我们来说才是最关心的事。

下面把可独立核实的事实、四大斜杠命令的具体契约、多 agent 架构、五方矩阵实测对比、国内可用度判断、以及国产工具的应对路径，全部摆到桌上。

![Antigravity 2.0 桌面应用产品图 多 agent 工作台界面 来源 9to5Google](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/google-antigravity-2-multi-agent-coding-cli-cn-2026-05-21/9to5-laptop.jpg)

## 可独立核实的关键数字与时间点

| 维度 | 数值 | 来源 |
| --- | --- | --- |
| 发布时间 | 2026-05-19 10:45 PT（北京时间 5/20 凌晨） | TechCrunch 报道 |
| 发布场合 | Google I/O 2026 主舞台 + Developer Keynote | Google 官方 blog |
| 产品形态 | 桌面应用 + Antigravity CLI（Go）+ SDK + Managed Agents + 企业版 | Google Developer Blog |
| CLI 实现语言 | Go（之前 Gemini CLI 是 Node） | Google 官方迁移公告 |
| 默认基座 | Gemini 3.5 Flash（与 Antigravity 联合开发） | TechCrunch + MarkTechPost |
| 基座吞吐 | 289 tokens / sec | 多源转引 Google I/O 主题演讲数据 |
| 支持的第三方模型 | Gemini-3.5-Flash 系列、Claude Opus / Sonnet 4.6 Thinking、GPT-OSS-120B-Medium | 36 氪报道引用官方文档 |
| 四大斜杠命令 | /goal / /grill-me / /schedule / /browser | Google 官方文档 + WinBuzzer / AgentUpdate |
| 多 agent 调度 | subagent 并行 fanout + 隔离 Linux 沙箱 | MarkTechPost + V2EX 实测帖 |
| 状态持久化 | 每次交互创建可恢复环境，文件 / 状态在后续调用中保留 | MarkTechPost 技术分析 |
| Gemini CLI 下线日期 | 2026-06-18 | Google 官方迁移公告 |
| 历史能力迁移 | Skills / Hooks / Subagents / Extensions 全部迁移到 Antigravity 插件 | Google 官方迁移公告 |
| AI Pro 订阅价 | 20 美元 / 月 | TechCrunch + WinBuzzer |
| AI Ultra 新档 | 100 美元 / 月（5× Pro 配额） | TechCrunch + WinBuzzer |
| AI Ultra 顶级档 | 200 美元 / 月（从原 250 美元下调，20× Pro 配额） | TechCrunch + 9to5Google |
| 平台支持 | macOS / Linux / Windows | Google 官方 blog |
| Android 联动 | AI Studio Android 同期发布，可一键导出到本地 Antigravity | 9to5Google |
| Gemini CLI 付费 API 用户 | 继续可用，享受最新模型 | Google 官方迁移公告 |
| 与 Gemini Code Assist Enterprise | 保留访问权，不受下线影响 | Google 官方迁移公告 |

数字相互交叉，口径一致。TechCrunch、Google 官方 blog、WinBuzzer、9to5Google、MarkTechPost、36 氪六个独立来源对发布时间、定价梯度、CLI 实现语言、Gemini CLI 下线时间、多 agent 架构五项核心事实印证一致。Antigravity 2.0 的基座吞吐 289 tokens/sec、AI Pro/Ultra 双档价格、Gemini 3.5 Flash 联合开发，这三个工程细节是 TechCrunch 与多家科技媒体双源印证。

## 四大斜杠命令是什么——执行契约，不是快捷键

![Antigravity 2.0 四大斜杠命令工作流图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/google-antigravity-2-multi-agent-coding-cli-cn-2026-05-21/antigravity-slash-commands.png)

斜杠命令听起来像 Slack 的 `/giphy`，但 Google 官方文档把它们定义为「契约」——决定 agent 在执行过程中的行为协议。逐个看：

### `/goal`：从头干到尾，不打扰你

`/goal` 告诉 agent「这件事完整做完，中间不要回头问我」。agent 自己 plan、自己 exec、自己 verify、自己 deliver。比如 `/goal 把登录页 + 注册页 + 后端 JWT 端到端跑通` ——agent 会自己拆 5-8 个子任务，丢给 subagent 并行干，跑测试，发现失败自动改，最后给你一个完成报告。

这里有个工程取舍：以前 Cursor / Claude Code 让你「每一步都看一眼」是为了避免 AI 跑偏，但跑偏的成本随着基座变强降低了。Google 这一刀切下去等于说「我们的 Gemini 3.5 Flash 跑偏概率足够低，可以一路干完」。这是个对自家基座质量的强声明。

### `/grill-me`：AI 先反问，再动手

`/grill-me` 是反过来——agent 先抛 N 个细节问题给你，全部确认完才开工。比如你说「帮我做个 Todo 应用」，触发 `/grill-me` 后，agent 会问：

- 暗色模式要不要？
- 数据库用 SQLite / Postgres / 云 KV？
- 多用户还是单用户？登录方案？
- 部署到 Vercel / Cloudflare / 本机 Docker？
- 测试覆盖率门槛？

这其实是把每个有经验工程师都做过的「需求评审」环节固化成 agent 行为契约。对国内独立开发者尤其有意义——很多时候我们写需求是模糊的，AI 直接动手就会写出一堆推倒重来的东西，浪费 token 浪费时间。

### `/schedule`：cron 式定时任务

`/schedule` 把 agent 从「你叫一次干一次」变成「自己定时跑」。MarkTechPost 在技术分析里把它形容为「类似 GitHub Actions + AI 的对话式自动化管道」。常见用法：

- `/schedule 每天早 8 点扫一遍 PR 评论，把 critical 标为 P0`
- `/schedule 每周一上午跑一遍依赖安全扫描，发现 CVE 自动开 issue`
- `/schedule 每小时检查一次生产环境日志，发现 P99 延迟超 500ms 通知我`

这一条是 Antigravity 2.0 跟所有竞品拉开距离的功能。Cursor 没有，Claude Code 没有，千问 Coder 还在计划中，Kimi K2.5 完全不在这个赛道。

### `/browser`：AI 自己开 Chrome 测页面

`/browser` 强制 agent 打开内置 Chrome 去测交互。比如 agent 写完一个登录页，跑 `/browser 打开本地 dev 服务器，点击注册按钮看报错`，agent 会真的去点、看 console、看 network，然后回报「发现 CSRF token 缺失」。

这个能力本质上是 Playwright MCP 的"亲儿子"——Google 把浏览器自动化做成了 first-class 的 agent 能力，不用你再去配 MCP server。对 web 全栈开发尤其顺手。

## subagent 并行调度是怎么落地的

![Antigravity 2.0 多 agent 并行调度架构图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/google-antigravity-2-multi-agent-coding-cli-cn-2026-05-21/antigravity-multi-agent-arch.png)

V2EX 用户 fds2003 五月二十号晚上发的实测帖，把 Antigravity 2.0 的多 agent 架构拆得最清楚。原话两句：「每次修改前强制生成 plan 供人类确认」「把耗时的检索和背景研究丢给 subagent」。配合 MarkTechPost 的技术分析，可以把架构拆成五层：

第一层，**开发者输入**。你敲 `/goal 实现登录注册流程`，进入 Antigravity 主 agent。

第二层，**主 agent 拆任务**。Antigravity 主 agent 调用 Gemini 3.5 Flash 把任务拆成 N 个子任务：后端 JWT 实现、前端登录页、前端注册页、单元测试、集成测试、文档。

第三层，**Plan Mode 强制审批**。这是 Antigravity 2.0 的杀手锏——主 agent 拆完任务后，**强制**把 plan 抛给你确认，你点了「同意」才会动文件。这一刀直接把 Cursor 早期的「全自动改文件结果改坏一片」的痛点切了。

第四层，**subagent 调度器并行 fanout**。批准的 plan 丢给调度器，调度器并行拉起多个 subagent（Backend Agent、Frontend Agent、Test Agent、Doc Agent），每个跑在自己的隔离 Linux 沙箱里。

第五层，**结果汇总 + 文件回写**。所有 subagent 跑完，主 agent 汇总结果、合并 diff、回写主仓库。所有中间状态都保留在 Managed Agents 的持久化环境里，可以回放、可以恢复。

V2EX 楼主原话：「这套机制在很大程度上解决了大项目上下文膨胀的问题」。Cursor / Claude Code 在 100k+ 文件大仓库上经常会因为单一上下文塞太多东西爆掉，Antigravity 把上下文按 subagent 切片，每个 subagent 只看自己负责的几个文件，主 agent 只看汇总——这是工程上正确的拆法，跟 OpenAI 的 Codex Cloud 思路一致。

## 五方矩阵实测对比

![五大 AI Coding 工具五方矩阵实测对比 Antigravity 2.0 Cursor Composer 2.5 Claude Code 千问 Coder Kimi K2.5](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/google-antigravity-2-multi-agent-coding-cli-cn-2026-05-21/antigravity-five-camp-matrix.png)

把今天能在国内每天敲代码用到的五个主力 AI Coding 工具摆到一张矩阵里：

| 对比维度 | Antigravity 2.0 | Cursor 3 (Composer 2.5) | Claude Code | 千问 Coder (Qwen3-Coder) | Kimi K2.5 |
| --- | --- | --- | --- | --- | --- |
| 产品形态 | 桌面 + CLI + SDK | IDE 插件 + Web | 终端 CLI | IDE 插件 + CLI | API + 网页 |
| 默认基座 | Gemini 3.5 Flash | Kimi K2.5 | Opus 4.7 | Qwen3-Coder | K2.5 自家 |
| 多 agent 并行 | 内置 subagent | 实验性 | 自行 Bash | Qwen Plan | 无 |
| 斜杠命令 | /goal /grill-me /schedule /browser | /edit /chat | /plan /tools | /code /plan | /api 调用 |
| 定时任务 | /schedule 内置 | 无 | 无 | 计划中 | 无 |
| 国内可用 | 需 VPN + 谷歌账号 | 需 VPN | 需 VPN | 国内直连 | 国内直连 |
| 订阅价（USD/月） | AI Pro 20 / Ultra 100 / Ultra Plus 200 | Pro 20 / Ultra 60 | Pro 20 / Max 200 | 免费 + 通义灵码 | 免费试用 + API 计费 |
| 开源/闭源 | 闭源（CLI 部分开源） | 闭源 | 闭源 | 全开源 | 模型权重开源 |

五点核心结论：

- **多 agent 并行是 Antigravity 唯一明显领先的维度**。其他四家要么实验性、要么完全没有，Antigravity 直接做成 first-class 能力。
- **定时任务是另一处独占优势**。`/schedule` 把 agent 推到运维 / SRE 工具的边界，这一刀切下去很狠。
- **价格上 AI Pro 20 美元和 Cursor Pro 20 美元、Claude Pro 20 美元持平**，竞争集中在「同等价位下谁的能力强」。AI Ultra 100 美元这一档是新设的，覆盖重度多 agent 用户，价格定得比 Cursor Ultra 60 美元贵一截，但 5× 配额对真正跑 subagent 并行的人来说划算。
- **国内可用度上 Qwen3-Coder 和 Kimi K2.5 是绝对优势**——直接国内连，不用 VPN，不用海外信用卡。Antigravity / Cursor / Claude Code 三家国内都需要 VPN，且都需要谷歌账号或海外手机号注册。
- **开源维度上 Qwen3-Coder 一枝独秀**——模型权重、训练数据、agent 框架全开源，国内开发者可以自行部署到内网。Kimi K2.5 权重也开源（modified MIT），但 agent 框架还是 Moonshot 闭源的。Antigravity / Cursor / Claude Code 都是闭源。

![Google AI Studio Android 9to5google aistudio 9to5 截图 同期发布 可一键导出到本地 Antigravity](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-21/google-antigravity-2-multi-agent-coding-cli-cn-2026-05-21/9to5-aistudio.png)

## 国内开发者实测可用度判断

这一节最关心的是「能不能真用起来」。按今天（2026-05-21）的实查口径：

**前置条件**：

- 谷歌账号（个人 Gmail 即可，企业 GSuite 也行）
- VPN（节点稳定即可，不需要专线）
- 海外支付方式（Visa/Mastercard 双币卡 / PayPal）

**免费层**：登录谷歌账号即可下载 Antigravity 桌面应用 + CLI，享受 Gemini 3.5 Flash 的免费配额（Google 官方未公布具体免费额度，但 Gemini CLI 时期是每分钟 60 次、每天 1000 次）。对偶尔用一下、研究研究的开发者完全够。

**AI Pro 20 美元/月**：适合每天敲代码的独立开发者。配额够你跑两个并行 subagent 项目。坑点：海外信用卡扣款，国内双币卡有一定概率被风控。

**AI Ultra 100 美元/月**：5× Pro 配额，适合靠这吃饭的全职开发者。建议直接订阅这一档跑多 agent 重度场景。

**AI Ultra Plus 200 美元/月**：从原来的 250 美元下调到 200 美元，20× Pro 配额，企业 / 团队向。

**国内的合规与速度问题**：

- Antigravity CLI 走 Google API 端点，国内访问需要稳定 VPN。Plan Mode 在 plan 抛给你确认这一步对网络延迟很敏感——如果你的 VPN 抖动，agent 会在确认等待环节超时。
- 谷歌账号在国内电话验证一直是个老问题，建议用海外手机号或者老账号。
- Managed Agents 的 Linux 沙箱跑在 Google Cloud 美国 / 欧洲节点，subagent 调度延迟感官上不如 Cursor / Claude Code 走 CDN 加速的国内体验。

## 国产工具的应对路径——通义灵码、Trae、Cline、Roo Code 怎么补这一刀

讲完 Antigravity 2.0 必须问一句：国产怎么办？

阿里通义灵码 / Trae / Qwen Code 走的是「基座国产 + 工具国产 + 国内直连」三件套路线，对应能力上：

- **通义灵码 IDE 插件**：默认基座 Qwen3-Coder，已支持 Plan Mode + 子任务拆分，但还没做到 Antigravity 那种隔离 Linux 沙箱并行。国内开发者首选——VS Code / IDEA 插件直接装，国内直连，免费用，对中文需求理解最贴近。
- **字节 Trae IDE**：基座 Doubao 系列 + Qwen 系列，主打"原生 Cursor 替代"，多 agent 还在实验性阶段，但 IDE 体验和 Cursor 几乎一比一。
- **Qwen Code CLI**：阿里官方的命令行版，对标 Antigravity CLI / Claude Code CLI，Go 语言实现，已支持 `/plan` `/code` 双模式。国内服务器，速度感官好。
- **Cline / Roo Code（开源 VS Code 插件）**：第三方开源插件，可接任何 OpenAI 兼容 API（包括 Qwen3-Coder / DeepSeek / Kimi 国内端点），多 agent 走的是「主 agent + Bash subprocess」简化方案，比 Antigravity 朴素，但灵活度高。

短期看，国产工具在 **多 agent 并行 + 定时任务** 这两块还有差距，但差距是工程问题不是能力问题——Qwen3-Coder 的模型能力足够支撑这类调度，缺的是把 sandbox + plan 持久化做成工程产品的一段时间。

中期看，国内开发者可以走两条路线并行——重度场景用 Antigravity（VPN + 谷歌账号），轻度场景用通义灵码 / Trae 顺手就来。两边的工作流都熟悉了，等通义灵码 / Qwen Code 把多 agent 补齐，可以无缝切回国产栈。

## V2EX / 知乎上的真实声音

V2EX 楼主 fds2003 的原贴：「计划模式 + 多子代理 + 沙箱权限的组合，在大项目上下文管理上明显比 Cursor 干净。但桌面端的内存占用让我老款 MacBook 风扇飞转。」

MarkTechPost 技术分析文章里给出的批评点：「企业治理文档、部署规范、安全认证（SOC 2、SSO/SCIM）都还没就位，跟 JetBrains AI 这种已经有 SOC 2 Type II 的成熟工具比，企业采购评审过不去。」

36 氪报道里转引的开发者反馈：「最终回答比 Codex / Claude Code 更详细完整，但缺少撤销编辑功能（Codex 已支持），权限申请流程也不够便捷。」

这三点放一起判断：Antigravity 2.0 是一个**工程方向正确、产品打磨还需要 1-2 个版本** 的初代多 agent IDE。如果你愿意当 early adopter 帮 Google 踩坑，AI Pro 20 美元这一档可以马上订；如果你是企业团队负责采购评估，建议等到至少 2.1 / 2.2 版本再下结论。

## 工程意义：编辑器中心向 agent 中心的范式迁移

往后看一两年，这件事的工程意义大于产品意义。三个判断：

第一，**IDE 这个产品形态在 AI Coding 时代会让位给 agent 工作台**。这不是 Antigravity 的发明，是 Cursor → Antigravity → 下一代国产 Trae / 通义灵码都会走的方向。左侧 Project + 右侧多 agent 对话 + 底部任务监控，会取代左侧文件树 + 中间编辑器 + 右侧调试。VS Code 这一代 IDE 范式的核心地位会在 2027-2028 年快速松动。

第二，**多 agent 并行调度会从「实验性能力」固化为「基础设施」**。每一家 AI Coding 工具都会做 subagent 并行、隔离沙箱、状态持久化。Google 这一刀是为整个赛道定义了基线——做不到这些就在主流之外。

第三，**国产基座 + 国产工具 + 国内直连** 三件套的生态价值会被显著放大。当海外工具因为账号 / 支付 / 网络的门槛把国内开发者拒之门外时，通义灵码 / Trae / Qwen Code / Cline / DeepSeek 系工具的国内市场会自然填补。Qwen3-Coder 全开源这一点尤其重要——它让国内云厂商（阿里云 / 火山引擎 / 腾讯云）能直接做托管版，把 Antigravity 那套 Managed Agents 能力以国内合规的方式还给国内开发者。

谁能在 2026 年底前把多 agent 并行 + 定时任务 + 隔离沙箱补齐，谁就是国产 AI Coding 工具的下一个领头。这件事我们可以跟得紧一点。

---

## 信息来源

- TechCrunch: https://techcrunch.com/2026/05/19/google-launches-antigravity-2-0-with-an-updated-desktop-app-and-cli-tool-at-io-2026/
- Google Developer Blog: https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/
- 9to5Google: https://9to5google.com/2026/05/19/google-antigravity-agentic-developer-suite/
- WinBuzzer: https://winbuzzer.com/2026/05/19/introducing-google-antigravity-20-xcxwbn/
- MarkTechPost: https://www.marktechpost.com/2026/05/19/google-launches-antigravity-2-0-at-i-o-2026-a-standalone-agent-first-platform-with-cli-sdk-managed-execution-and-enterprise-support/
- 36 氪: https://36kr.com/p/3817576331747331
- 新浪科技: https://finance.sina.cn/stock/jdts/2026-05-20/detail-inhynmhx1975415.d.html
- V2EX 实测帖: https://www.v2ex.com/t/1214153
- Antigravity 官网: https://antigravity.google/

---

*本文事实采集自 TechCrunch、Google 官方迁移公告、MarkTechPost、36 氪、V2EX 等公开来源；定价 / 时间 / 模型支持等核心数字按多源交叉印证口径取最严值；国内可用度判断按 2026-05-21 实测节点稳定性 + 谷歌账号注册路径口径。*
