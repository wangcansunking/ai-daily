# 微软 .NET 团队发布 13 个 AI 技能插件，通吃 Copilot/Claude/Cursor

> ai-daily · 2026 年 5 月 25 日 08:31 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/dotnet-skills-micro/head.png)

凌晨三点，一个 .NET 开发者对着屏幕发呆。他在 Claude Code 里敲下 `/plugin install dotnet@dotnet-agent-skills`，然后重启终端。30 秒后，AI 编码助手突然“理解”了 Entity Framework 的迁移命令、MSBuild 的构建优化参数、甚至能诊断一个诡异的死锁问题——这些不是靠 prompt engineering，而是靠一套名为 Agent Skills 的标准化插件体系。

与此同时，在微软雷德蒙德园区，.NET 团队悄悄把 13 个插件、几十项技能推上了 GitHub。仓库名叫 `dotnet/skills`，48 小时内冲上 GitHub Trending。

![dotnet/skills — Repository for skills to assist AI coding agents with .NET and C](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/dotnet-skills-micro/content-1.png)

**这不是又一个“AI 写代码”的 demo，这是一次编码助手生态的标准化突袭。**



![dotnet/skills 仓库首页截图，13 个插件卡片排列，标注“Copilot CLI / Claude Code / Cursor / Codex CLI 全兼容”](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/dotnet-skills-micro/schematic-1.png)



## 一套技能标准，通吃四大 AI 编码平台

`dotnet/skills` 最让我愣神的地方，不是它提供了多少技能，而是它同时兼容 Copilot CLI、Claude Code、Cursor 和 OpenAI Codex 四个平台。这在 AI 编码工具圈里极其罕见——通常每个平台都有自己的插件格式、自己的 API、自己的“护城河”。

而 .NET 团队的做法是：**走开放标准**。

仓库里的技能全部遵循 `agentskills.io` 规范——这是一个专门为 AI 编码代理设计的技能定义标准。它的核心思路是：把“技能”抽象成一组可验证的指令、约束和工具调用，不绑定任何特定平台的 API。你在 Claude Code 里安装的 `dotnet-msbuild` 插件，和你在 Cursor 里安装的版本，底层是同一套技能定义文件。

这带来的直接后果是：开发者不需要为不同工具重复配置。更关键的是，技能的准确性和效率可以被统一追踪——.NET 团队已经上线了一个公开 Dashboard（`https://dotnet.github.io/skills/`），实时展示每个插件的准确率和效率评分趋势。

（值得一提的是，这个 Dashboard 目前只覆盖“contained plugins”，也就是那些不依赖外部服务的技能。像 `dotnet-ai` 这种需要调用 LLM 或 RAG 管线的技能，评分体系还在建设中。）

13 个插件覆盖了 .NET 生态几乎所有核心领域。`dotnet` 是基础技能集，处理日常编码任务；`dotnet-data` 专攻 Entity Framework 和数据访问；`dotnet-diag` 负责性能诊断和故障分析；`dotnet-msbuild` 涵盖构建失败诊断、性能优化和代码质量；`dotnet-nuget` 管理依赖和包现代化；`dotnet-upgrade` 处理跨版本迁移；`dotnet-maui` 解决移动端开发的环境配置和故障排除；`dotnet-ai` 覆盖 LLM 集成、agentic 工作流、RAG 管线、MCP 协议，甚至经典 ML.NET 机器学习；`dotnet-template-engine` 做模板发现和项目脚手架；`dotnet-test` 聚焦测试执行、诊断和 MSTest 工作流；`dotnet-aspnet` 涵盖中间件、端点、实时通信和 API 模式；还有一个 `dotnet11`，专门对即将到来的 .NET 11 新 API 和语言特性。



![13 个插件的功能对照表，按“基础设施 / 数据 / 诊断 / 构建 / 包管理 / 升级 / 移动端 / AI / 模板 / 测试 / Web / 新版本”分类](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/dotnet-skills-micro/schematic-2.png)



## 安装体验：30 秒从零到“理解 .NET”

安装流程简单到让我觉得“这是不是太草率了”。以 Claude Code 为例：

```
/plugin marketplace add dotnet/skills
/plugin install dotnet-msbuild@dotnet-agent-skills
```

然后重启，敲 `/skills` 就能看到可用技能列表。整个过程不超过 30 秒。

VS Code 和 VS Code Insiders 用户需要在 `settings.json` 里加两行配置（`chat.plugins.enabled: true` 和 `chat.plugins.marketplaces: ["dotnet/skills"]`），然后在 Copilot Chat 里输入 `/plugins` 浏览安装。

Cursor 用户更简单：打开 marketplace 面板搜索 `.NET`，或者直接访问 `cursor.com/marketplace`。有意思的是，这个仓库本身就是 Cursor 的插件 marketplace——GitHub 仓库即市场，不需要额外的发布流程。对于本地开发或未发布的插件，可以软链接到 `~/.cursor/plugins/local/dotnet-agent-skills`。

Codex CLI 用户则用 `skill-installer` 工具，直接指向 GitHub URL 安装单个技能。

**这种“零摩擦安装”背后是一个更深的信号：微软在押注 AI 编码助手的技能标准化，而不是平台绑定。**

`.NET` 团队没有把技能锁在 GitHub Copilot 生态里，反而主动适配了竞争对手 Claude Code 和 Cursor。这看起来“反常”，但逻辑很清晰：如果 Agent Skills 标准（`agentskills.io`）成为事实标准，.NET 开发者无论选择哪个 AI 工具，都能获得一致的 .NET 编码体验。而微软作为标准的早期推动者，自然占据生态位优势。



![四种平台安装流程对比图，Copilot CLI / Claude Code / Cursor / Codex CLI 的安装命令并排展示](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/dotnet-skills-micro/schematic-3.png)



## 戏谑收尾：技能标准化，谁会睡不着？

`dotnet/skills` 的出现，让我想起 2016 年的 Language Server Protocol（LSP）。当年微软推出 LSP 时，很多人觉得“微软怎么会推开放协议”，但结果是 LSP 成了编辑器语言支持的通用标准，VS Code 因此受益巨大。

现在同样的剧本在 AI 编码助手领域上演。`agentskills.io` 如果被广泛采纳，技能市场就会像 VS Code 扩展市场一样繁荣——而 .NET 团队抢先卡位，把 13 个核心技能变成了“标准答案”。

那些还在构建封闭插件生态的 AI 编码工具厂商，看到这个仓库时，大概会有一丝不安。毕竟，开发者永远选择“开箱即用”的那条路。

## 参考来源
- https://github.com/dotnet/skills
- https://dotnet.github.io/skills/ （Dashboard）
- https://agentskills.io （Agent Skills 标准）

#Repository #AI #NET #科技
