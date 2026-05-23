# dotnet/skills — Repository for skills to assist AI coding agents with .NET and C#

> ai-daily · 2026 年 5 月 23 日 19:17 · 来源：GitHub Trending any

![](head.png)

刚刚，微软 .NET 团队在 GitHub 上开源了一个叫 **dotnet/skills** 的仓库，这玩意儿不是给开发者用的，是给 AI 编程助手“喂招”用的。

简单说，它把 .NET 开发里那些零碎又磨人的活儿——比如排查构建失败、升级框架版本、调优 EF Core 数据访问、写 MAUI 界面——打包成了 **12 个标准技能插件**。你装进 Copilot CLI、Claude Code 或者 Cursor，AI 就能直接调用这些“专家技能”去干活，而不是靠猜。（我看到那个 dotnet-diag 插件专门负责性能诊断和事故分析，第一反应是：以后线上翻车是不是可以先甩给 AI 顶一阵？）

![dotnet/skills — Repository for skills to assist AI coding agents with .NET and C](content-1.png)

更狠的是，这仓库本身就是一个 **Cursor 插件市场**，而且所有技能都遵循 agentskills.io 开放标准，OpenAI Codex 也能用。.NET 团队还搞了个仪表盘，实时追踪每个插件的准确率和效率——这相当于把 AI 写 C# 代码的水平，第一次摆到明面上打分。微软这是要把自家生态的 AI 编程体验，直接焊死在标准答案上。

#Repository #AI #NET #科技
