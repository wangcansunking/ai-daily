# 告别混乱终端：cmux 如何用原生 Swift 解决 Claude 多会话管理痛点

> ai-daily · 2026 年 5 月 25 日 08:26 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/manaflow-ai-cmux-micro/head.png)

2025 年夏天的某个深夜，旧金山某间公寓的屏幕还亮着。一个开发者面前开着 12 个 Ghostty 终端窗口，每个窗口里都跑着一个 Claude Code 会话。macOS 的通知中心弹了无数次「Claude is waiting for your input」—— 但完全看不出是哪个 agent 在等、在等什么、该不该立刻切过去。他切到通知中心，标题已经被截断，上下文全丢。

这不是虚构场景。这是 cmux 作者在 README 里写下的原话。

![manaflow-ai/cmux — Ghostty-based macOS terminal with vertical tabs and notificat](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/manaflow-ai-cmux-micro/content-1.png)

三个小时后，他关掉了所有 Ghostty 窗口，开始写 Swift 代码。

![cmux screenshot](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/manaflow-ai-cmux-micro/content-2.png)

**最好的开发者从不等待别人来解决自己的痛点。**

## 一个终端工具的「反叛」：不要 Electron，不要 GUI 锁死，只要原生

cmux 做的事情说出来很简单：给 macOS 做了一个基于 Ghostty 渲染引擎的终端 App，加了垂直标签页、通知系统和内置浏览器。但它的「反叛」之处藏在技术选型里 —— Swift + AppKit，不是 Electron。

这个选择在 2025 年的开发者工具圈里几乎是种宣言。过去三年，基于 Electron 的终端类工具（Warp、Hyper、Tabby）和 Tauri 系的轻量替代品层出不穷，但性能一直是被诟病的软肋。cmux 的作者在 Why cmux 段落里明确提到：「我试过几个 coding orchestrators，但大多数是 Electron/Tauri 应用，性能让我受不了。」他用了一个词：bugged me。

于是 cmux 直接调用 libghostty 做 GPU 加速渲染，读取用户已有的 `~/.config/ghostty/config` 配置文件来继承主题、字体和配色。这意味着 Ghostty 用户迁移过来时，所有视觉配置零成本复用。



![cmux 垂直标签页侧边栏特写，展示 git 分支、PR 状态、工作目录、监听端口和最新通知文本](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/manaflow-ai-cmux-micro/schematic-1.png)



但真正让我愣神的设计，是那个侧边栏。

每个 workspace 在侧边栏里占据一个垂直标签。标签上显示的不是简单的终端标题，而是 git branch、linked PR status/number、working directory、listening ports、以及 latest notification text。这些元数据直接嵌在标签里，一眼扫过去就能知道「哦，那个在 `feature/auth-refactor` 分支上的 agent 刚刚弹了通知，PR #342 还挂着」。

这本质上是在把终端窗口从「被动容器」变成「主动信息面板」。

## 蓝色光环：当通知系统学会了上下文

cmux 的通知机制值得单独拆开讲。

传统 macOS 终端通知（比如 Claude Code 弹出的）只有一个模糊的标题体 ——「Claude is waiting for your input」。没有任何上下文，连是哪个项目、哪个任务都不知道。cmux 的做法是拦截终端序列（OSC 9/99/777），同时暴露一个 `cmux notify` CLI 命令，让开发者可以把它接入 Claude Code、OpenCode 等 agent 的 hook 系统。

当一个 coding agent 等待用户输入时，对应的 pane 边缘会出现一个蓝色光环，同时侧边栏的标签会亮起。Cmd+Shift+U 直接跳到最新未读通知。



![cmux 通知面板界面，展示所有待处理通知的列表视图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/manaflow-ai-cmux-micro/schematic-2.png)



这里有三个细节很值得琢磨。

第一，通知面板（Cmd+I 呼出）不是简单的消息列表。它聚合了所有 workspace 的待处理通知，并且区分了已读/未读状态，可以用 Ctrl+Cmd+U 标记当前项为最旧未读并跳到下一个最新未读。这几乎是把邮件客户端的 triage 逻辑搬进了终端。

第二，蓝色光环的视觉设计不是装饰。在多 pane 分屏场景下（比如左边跑 dev server，右边跑 agent），开发者扫一眼就能定位到「谁在等我」，不需要读任何文字。

第三，也是最容易被忽略的一点：`cmux notify` 是一个 CLI 命令，不是 GUI 配置项。这意味着任何 agent、任何脚本、任何自动化流程都可以触发通知。作者在 README 里特意提到「you can wire into agent hooks for Claude Code, OpenCode, etc.」—— 这个 etc. 才是重点。他给了一套可编程的通知原语，而不是锁死在某个特定 agent 的集成方案里。

**工具作者最聪明的决定，就是知道自己该在哪条线上停手。**

## 内置浏览器：把 localhost 变成 agent 的游乐场

cmux 的内置浏览器值得单独说几句。

它不是一个简单的 WebView。浏览器 API 是从 `agent-browser` 项目移植过来的，支持 accessibility tree 快照、元素引用、点击、表单填充和 JS 执行。关键是可以直接在终端旁边 split 一个浏览器 pane，让 Claude Code 跟 dev server 交互。



![cmux split view 布局，左侧终端跑 Claude Code，右侧内置浏览器显示 localhost:3000](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/manaflow-ai-cmux-micro/schematic-3.png)



这解决了一个很具体的场景：agent 在终端里跑，但需要「看到」浏览器里渲染的结果才能继续工作。传统做法是开发者手动切到浏览器、截图、粘贴回终端，或者依赖 Playwright 之类的 headless 方案。cmux 把浏览器变成了终端的一个「surface」，和 split pane 同级。

更妙的是 SSH 场景下的处理。`cmux ssh user@remote` 会创建一个远程 workspace，浏览器 pane 的流量自动走远程网络，`localhost` 直接指向远程机器的本地端口。拖拽一张图片到远程会话里，它会用 scp 上传。这种「本地工具的远程感知」设计，在同类工具里几乎没见过。

## Claude Code Teams 模式：不需要 tmux 的多 agent 协作

有个功能在 README 里只占了一小段，但我觉得是整篇最值得关注的信号。

`cmux claude-teams` 一条命令启动 Claude Code 的 teammate 模式。每个 teammate 作为原生 split 打开，侧边栏自动带元数据和通知。作者特意加了一句：「No tmux required。」

这句话的潜台词很明确：多 agent 协作的终端编排，不应该依赖 tmux 那种基于 session 的手动管理。cmux 把 agent teammate 当成一等公民 —— 每个 teammate 有自己的 split、自己的标签元数据、自己的通知管道。

支持的 agent 列表相当长：Claude Code、Codex、Grok、OpenCode、Pi、Amp、Cursor CLI、Gemini、Rovo Dev、Copilot、CodeBuddy、Factory、Qoder。这不是一个「Claude Code 专用工具」，而是一个 agent-agnostic 的终端框架。

会话恢复机制也值得提一句。cmux 不 checkpoint 任意进程状态（明确说了不支持 tmux/vim/shell 的进程恢复），但支持的 agent 可以用 hook 保存原生 session ID，重启后自动恢复。高级用户可以用 `cmux surface resume set` 手动绑定自定义恢复命令，比如 `tmux attach -t work`。敏感环境变量（tokens、passwords、secrets、API keys）在存储恢复绑定前会被自动清除。



![cmux session restore 流程示意图，从 snapshot 到 layout 重建到 agent resume 命令执行](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/manaflow-ai-cmux-micro/schematic-4.png)



## 开源、GPL-3.0、和 Founder's Edition 的商业信号

cmux 的许可证是 GPL-3.0-or-later。如果组织不能遵守 GPL，可以联系 founders@manaflow.com 获取商业授权。

Founder's Edition 目前是付费支持通道，承诺的内容包括优先 feature 请求、早期访问 cmux AI（提供 workspace/tab/panel 上下文感知）、iOS app（桌面和手机终端同步）、Cloud VMs、语音模式、以及「作者的私人 iMessage/WhatsApp」。

这个组合很有意思：核心工具完全开源，靠 GPL 的传染性条款倒逼企业付费，同时用 Founder's Edition 验证付费意愿。iOS 同步和 Cloud VMs 是真正的付费墙功能，但优先级排在 AI 上下文感知之后 —— 说明团队认为「终端里跑 agent 时自动理解你在做什么」比多端同步更值钱。

至于这个判断对不对，开发者社区会用 Star 和付费转化来投票。

GitHub 上的 Star History 图表还空着，毕竟这是新项目。但考虑到 Ghostty 本身在 macOS 开发者群体里的口碑，以及「不要 Electron」这个立场切中的情绪，cmux 很可能在接下来几周成为 Hacker News 和终端爱好者圈子的热门话题。

**给开发者原语而非方案，让他们自己找到最高效的工作流 —— 这个哲学比任何具体功能都更有杀伤力。**

---

## 参考来源
- https://github.com/manaflow-ai/cmux (GitHub 仓库 README，包含所有功能描述、安装方式、快捷键、会话恢复机制、许可证信息)
- https://cmux.com/docs/session-restore (会话恢复完整文档链接，README 中引用)

#Ghosttybased #AI #科技

![Vertical tabs and split panes](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/manaflow-ai-cmux-micro/content-3.png)
