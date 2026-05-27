# 挑战 Salesforce？开源 CRM 项目 twenty 获 GitHub 趋势榜前列

> ai-daily · 2026 年 5 月 27 日 11:35 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/twentyhq-twenty-micro/head.png)

旧金山时间 2025 年 1 月 21 日凌晨，一个 GitHub 仓库的 Star 数正在以肉眼可见的速度跳动。twentyhq/twenty——一个开源 CRM 项目——在 Trending 榜上挤进前列，README 第一行写着「The #1 Open-Source CRM」。

这个标题很狂。CRM 市场被 Salesforce 统治了二十多年，Oracle、SAP、微软 Dynamics 365 三家加起来才勉强能跟它掰手腕。一家开源初创公司敢自称 #1，要么是疯了，要么是赌对了一件事。

![twentyhq/twenty — The open alternative to Salesforce, designed for AI.](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/twentyhq-twenty-micro/content-1.png)

**AI 时代的 CRM，不该长成 Salesforce 那样。**

![All the tools you need to build anything](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/twentyhq-twenty-micro/content-2.webp)



![Twenty 与 Salesforce 的架构对比，左侧是 Salesforce 的单体 SaaS 封闭生态，右侧是 Twenty 的代码化可组装模块](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/twentyhq-twenty-micro/schematic-1.png)



---

## 代码即配置：CRM 终于学会用 Git 了

我打开 Twenty 的文档，第一反应是这不像一个 CRM 项目——更像一个前端框架的 Getting Started。CLI 脚手架、defineObject 定义数据模型、app:publish 发布到工作区，整套流程跟 Vercel 部署一个 Next.js 应用没什么区别。

```
npx create-twenty-app my-app
```

然后你就能用 TypeScript 定义业务对象。Deal（交易）、amount（金额）、closeDate（成交日期）——这些 Salesforce 里需要拖拽 20 分钟才能建好的自定义字段，在 Twenty 里是几行代码。defineObject 函数接收 nameSingular、fields 数组，字段类型从 TEXT 到 CURRENCY 到 DATE_TIME 一应俱全。写完 npx twenty app:publish --private，你的私人 CRM 模块就上线了。

这件事的本质是什么？**CRM 的配置终于从鼠标拖拽变成了版本控制。** Salesforce 的配置存储在它自己的元数据层里，你改一个字段、调一个流程，回滚得靠管理员手动操作。Twenty 把这一切放进 Git——你所有的对象定义、视图逻辑、工作流规则，都是 TypeScript 文件。出问题了 git revert，上线前走 PR review，CI/CD 直接跑测试。CRM 变成了你技术栈的一部分，而不是一个黑盒 SaaS 孤岛。

Twenty 的 Stack 列表暴露了它的基因：NestJS 做后端（BullMQ 处理队列、PostgreSQL 存数据、Redis 做缓存），React 做前端（Jotai 状态管理、Linaria CSS-in-JS、Lingui 国际化）。这是一个全 TypeScript 单体仓库（Nx monorepo），技术选型跟 2023-2024 年的主流全栈项目完全一致。如果你是一个全栈团队，接手 Twenty 的学习成本远低于去啃 Salesforce 的 Apex 和 Visualforce。



![Twenty 代码化 CRM 配置流程图，从 defineObject 到 app:publish 到 Git 版本控制](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/twentyhq-twenty-micro/schematic-2.png)



---

## AI Agent 的原生土壤：为什么 Salesforce 的 AI 很难用

Twenty 文档里有一个链接特别显眼：「Learn more about AI in doc」。虽然原文没有展开具体做到细节，但结合它的架构设计，Twenty 对 AI 的友好程度是 Salesforce 无法比拟的。

Salesforce 的 Einstein AI 套件很强大，但它有一个致命缺陷：数据模型是封闭的。Einstein 要读取你的客户数据、预测成交概率、推荐下一步动作，它得用 Salesforce 的 API 层层调用。每次模型迭代，你都得等 Salesforce 发布新版本。而且 Einstein 的定价是独立 SKU，每用户每月 50-150 美元不等——在已有的 CRM 订阅费之上再加一层。

Twenty 完全不一样。你定义的对象和字段是代码，数据存在你自己的 PostgreSQL 里。这意味着你可以直接把数据库暴露给任何 LLM Agent。一个 LangChain 脚本就能读取 deals 表、分析 closeDate 趋势、自动生成跟进邮件。甚至可以用 Twenty 的 workflow 引擎触发 Agent 动作——当 deal 状态从 negotiation 变成 closed-won，自动调用 OpenAI API 生成复盘报告。

**AI 时代，CRM 的核心壁垒不是功能多，而是数据可编程。**

这个判断我在看到 Twenty 的技术栈时更加确信。BullMQ 做任务队列——这正是 AI Agent 异步执行的最佳基础设施。Agent 调用需要时间，不能阻塞 HTTP 请求，BullMQ 的 Redis 队列天然适配这种场景。NestJS 的模块化架构让注入 AI 服务变得简单，一个 AI Module 就能封装所有 LLM 调用逻辑。Twenty 团队显然在设计之初就想到了 AI 不是附加功能，而是基础设施层。



![Twenty 的 AI Agent 架构示意，PostgreSQL 直连 LLM Agent，BullMQ 队列处理异步任务](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/twentyhq-twenty-micro/schematic-3.png)



---

## 开源 CRM 的二十年轮回

这不是开源 CRM 第一次挑战 Salesforce。2004 年，SugarCRM 以开源姿态杀入市场，一度拿下了 IBM、NASA 等大客户。但 SugarCRM 在 2014 年转向闭源，社区版停滞不前，开发者大量流失。之后的 EspoCRM、SuiteCRM 都是 PHP 栈，技术架构停留在 2010 年代，面对现代技术团队毫无吸引力。

Twenty 的关键差异在于时机和生态。2025 年的开发者工具链比 2004 年成熟太多——Docker Compose 一键部署、GitHub Actions 自动化 CI/CD、Vercel/Netlify 级别的云托管体验。Twenty 提供了 Cloud 版本（twenty.com 注册，一分钟内创建工作区），也支持 Docker Compose 自托管。这种「先云后自建」的路径，跟 GitLab、Supabase 的开源商业化策略如出一辙。

Twenty 的社区运营也值得关注。Discord、Feature requests、Figma 设计稿公开——这是 PLG（Product-Led Growth）开源项目的标准打法。README 里特别感谢了 Chromatic（UI 测试）、Greptile（代码审查）、Sentry（错误追踪）、Crowdin（翻译）四家服务——全是开发者工具生态里的明星公司。Twenty 团队在表明立场：我们不是传统软件公司，我们是开发者社区的一份子。

**Salesforce 卖的是软件，Twenty 卖的是可编程性。**

这个定位让 Twenty 避开了一个陷阱：它不需要在功能广度上跟 Salesforce 竞争。Salesforce 有 1000+ 功能模块，Twenty 只需要提供 primitives（原语）——objects、views、workflows、agents——然后让开发者自己组装。就像 Notion 不需要在功能上跟 SAP 竞争，它只提供 block 原语，用户自己搭出项目管理、知识库、CRM。

结尾留一个问题：Twenty 能走多远？SugarCRM 当年在 1000 万美元 ARR 时转向闭源，Twenty 目前还没有公布商业化数据。但它的 Cloud 版本已经在运营，app:publish --private 这个命令暗示未来会有 Public App Marketplace。如果 Twenty 在 2025 年内推出 AI Agent 的官方 SDK 并跑通商业化闭环，Salesforce 的产品经理们可能真的要打开这个 GitHub 仓库仔细看看了。毕竟，Marc Benioff 最怕的不是竞争对手的 feature，而是开发者社区的叛逃。

## 参考来源
- twentyhq/twenty GitHub Repository: https://github.com/twentyhq/twenty
- Twenty 官方文档（安装、应用开发、自托管、技术栈）：同 GitHub README
- Salesforce Einstein AI 定价参考：Salesforce 官方定价页（历史公开信息）
- SugarCRM 开源转闭源历史：SugarCRM 官方公告（2014 年）

#The #Salesforce #AI #科技

![Plus all the tools of a good CRM](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/twentyhq-twenty-micro/content-3.webp)
