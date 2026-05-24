# 谷歌发布chrome-devtools-mcp：AI编码助手现在能自己用Chrome DevTools

> ai-daily · 2026 年 5 月 23 日 19:28 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/chromedevtools-chrome-devtools-mcp-micro/head.png)

刚刚，Chrome 团队亲自下场，把 DevTools 塞进了 AI 编码助手。

一个叫 chrome-devtools-mcp 的工具直接开源，它做的事很简单：让你的 Claude、Cursor、Copilot 这些编码智能体，能像人类开发者一样操控一个真实的 Chrome 浏览器。不是模拟，是真的打开、点击、看网络请求、抓控制台报错，甚至做性能分析。

![ChromeDevTools/chrome-devtools-mcp — Chrome DevTools for coding agents](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-23/chromedevtools-chrome-devtools-mcp-micro/content-1.png)

我看到这个的第一反应是——以前让 AI 调前端 bug，得靠截图和描述来回传话。现在它自己就能用 Puppeteer 自动化操作浏览器，拿到的还是带 source-map 的堆栈追踪（这意味着它能看到压缩代码背后的原始源码位置）。性能分析那块更狠，直接调用 Chrome 内置的 tracing 工具，把渲染瓶颈、长任务都揪出来，甚至还能拉 Google CrUX 上的真实用户数据做对比。

这相当于给 AI 配了一双肉眼和一只手，前端调试的玩法要变了。

#ChromeDevTools #Chrome #DevTools #AI #科技
