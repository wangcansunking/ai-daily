# 开源项目实测：CodeGraph让Claude Code工具调用减少71%，费用降35%

> ai-daily · 2026 年 5 月 25 日 02:44 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/colbymchenry-codegraph-micro/head.png)

2026 年 5 月 24 日深夜，一个开发者把 CodeGraph 的 v0.9.4 跑分数据贴到了 GitHub 上。

7 个真实开源项目，7 种语言，从 VS Code 这种 1 万文件的庞然大物到 Gin 这种只有 110 个文件的轻量框架。每个项目跑 4 轮，取中位数。带 CodeGraph 的 Claude Code 直接回答架构问题，不带的那组疯狂 spawn Explore 子代理，grep、glob、Read 轮番上阵，token 烧得像不要钱一样。

![colbymchenry/codegraph — Pre-indexed code knowledge graph for Claude Code, Codex](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/colbymchenry-codegraph-micro/content-1.png)

结果让所有用 AI 写代码的人都会沉默几秒：**平均省 35% 费用，少 57% token，快 46%，工具调用砍掉 71%。**

**AI 编程工具终于学会"先看地图再走路"了。**

## 一个 SQLite 数据库，干掉了 grep 的命

CodeGraph 的核心想法简单到让人拍大腿：为什么每次问 AI "这个请求怎么到达数据库的"，它都要现用 grep 扫一遍代码库？

这个项目做的事情，是在 AI 动手之前，先用 tree-sitter 把整个代码库的 AST 解析完，把函数定义、调用关系、类继承、import 链路全塞进一个本地的 SQLite 数据库里——FTS5 全文索引、符号关系图、调用链追踪，全部预建好。AI 代理不需要再 spawn 子进程满世界 find / grep / Read，直接调 `codegraph_context` 看一眼地图，再 `codegraph_explore` 拉相关源码，完事。



![CodeGraph 架构对比图，左侧是传统 AI 代理 grep/find/Read 的 55 次工具调用链路，右侧是 CodeGraph 2-3 次 MCP 调用直接返回结果](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/colbymchenry-codegraph-micro/schematic-1.png)



跑分数据里最夸张的是 Tokio 这个 Rust 异步运行时项目。790 个文件，不带 CodeGraph 的那组 Claude Code 直接烧了 2.41 美元，3 分 2 秒才答完，调了 53 次工具。带上 CodeGraph 之后呢？0.42 美元，53 秒，4 次工具调用。费用砍了 82%，token 少 86%，工具调用砍 92%。这不是优化，这是直接换了个物种。

Excalidraw 的对比更离谱——96% 的工具调用消失，从 79 次降到 3 次。Claude Code 面对 "Excalidraw 怎么渲染和更新 canvas 元素" 这个问题时，不带 CodeGraph 的那组 spawn 了一堆子代理在 640 个文件里瞎撞，带着 CodeGraph 的那组直接查图、读源码、回答，零文件读取。

不过也有反例。OkHttp 这个 Java HTTP 客户端，645 个文件，只省了 2% 费用，13% token。作者在文档里坦诚得可爱：小项目原生搜索本来就便宜，边际效应递减。Gin 框架 110 个文件，省 21% 费用，40% 工具调用——有改善，但远不如大项目那么戏剧性。

**这揭示了一个残酷真相：AI 编程的成本瓶颈不在模型，在"找代码"这个动作本身。**



![7 个项目 CodeGraph 节省比例柱状图，横轴为项目文件数，纵轴为费用/token/工具调用节省百分比，大项目柱状明显更高](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/colbymchenry-codegraph-micro/schematic-2.png)



## 100% 本地、零配置、一个命令装完——这才是开发者想要的 DX

CodeGraph 的安装体验让我想起早年 Homebrew 的 "one-liner 搞定一切" 哲学。

macOS 和 Linux 上一条 curl 管道 sh，Windows 上一条 PowerShell irm 管道 iex。没有 Node.js 依赖——它自己打包了运行时。没有编译步骤，没有原生构建，跨平台行为完全一致。装完之后交互式安装器自动检测你机器上的 AI 编程工具——Claude Code、Cursor、Codex CLI、opencode、Hermes Agent——问你要配哪个，然后自动写入 MCP 配置和指令文件。

`codegraph init -i` 在项目根目录跑一次，建完索引之后，AI 代理只要发现 `.codegraph/` 目录存在就自动切换策略：优先用 CodeGraph 工具直接回答，不 spawn 文件读取子代理。文档里的指令写得很直白：

> CodeGraph is the pre-built search index; re-deriving its answers with grep + Read repeats work it already did and costs more for the same result.

这句话应该印在所有 AI 编程工具的文档首页。

更让我觉得有意思的是它尊重 `.gitignore` 的方式——不是自己搞一套配置规则，而是直接读 git 的忽略规则。`node_modules` 不会进索引，构建产物不会进索引，`.env` 不会进索引。超过 1MB 的文件自动跳过。零配置文件，零手动排除，零心智负担。

文件监听用的是原生 OS 事件——macOS 的 FSEvents、Linux 的 inotify、Windows 的 ReadDirectoryChangesW——2 秒防抖窗口，增量同步，改完代码等两秒图就更新了。这个细节说明作者真的在用这个工具写代码，不是闭门造车的产品经理。



![CodeGraph 安装流程三步图，命令行 → 自动检测代理 → MCP 配置写入 → 项目初始化索引](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/colbymchenry-codegraph-micro/schematic-3.png)



19 种语言支持，从 TypeScript 到 Pascal/Delphi 都有。14 种 Web 框架的路由识别——Django 的 `urls.py`、Express 的 `app.get()`、Spring 的 `@GetMapping`、Rails 的 `get '/x', to: 'users#index'`——全部解析成路由节点，链接到 handler 函数。你问 "这个 API 端点怎么走到数据库的"，CodeGraph 能直接从 URL pattern 一路追踪到 ORM 调用。

还有一个 `codegraph affected` 命令让我眼前一亮。传两个改动的源文件，它递归追踪 import 依赖，找出受影响的测试文件。管道接 `git diff --name-only`，CI 里只跑相关的测试，不用全量回归。这个功能单独拿出来都够一个小创业项目了。

截止 2026 年 5 月 24 日的 v0.9.4 版本，项目还在快速迭代。GitHub 上 MIT 协议开源，issue 区有人报 WSL2 的 `/mnt` 路径下 WAL 模式启用失败的问题，作者直接回答"把项目移到本地磁盘上"——没有试图修一个操作系统层面的限制，这个务实态度在开源项目里不多见。

Colby McHenry，这个项目的作者，做了一件看起来很"无聊"但极其正确的事：把 AI 编程工具最浪费资源的一环——代码探索——做成了预计算索引。不是新模型，不是新架构，不是融资新闻，就是一个 SQLite 数据库加一个 MCP server，把 grep 从 AI 的工作流里踢了出去。

下次你看到 Claude Code 的账单上那行 "Explore agent spawned"，想想它是不是又在替你 grep 一个你本可以提前索引好的代码库。然后跑一条 `npx @colbymchenry/codegraph`，省下的 token 够你多问好几个问题了。

## 参考来源
- https://github.com/colbymchenry/codegraph

#Preindexed #Claude #Code #Codex #Cursor
