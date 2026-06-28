---
title: "CloakBrowser：49 个 C++ 补丁打 Cloudflare"
slug: cloakbrowser-stealth-chromium-2026-05-11
date: 2026-05-11
weekday: 周一
category: AI 爬虫 / 反爬浏览器 / Browserbase 替代
track: arbitrage
domain: stealth-browser-cn-replacement
cover: cloakbrowser-stealth-chromium-2026-05-11.png
tags:
  - CloakBrowser
  - 反爬
  - stealth-browser
  - Cloudflare
  - reCAPTCHA
  - Playwright
  - Browserbase
  - AI 爬虫
  - grounded-search
  - DrissionPage
description: "做 grounded-search agent 的同行最近都被同一道墙挡着：Cloudflare Turnstile 一升级，自家 Playwright 脚本一夜全废。5 月 9 日 GitHub Trending 上有个叫 CloakBrowser 的项目单日涨 567 颗星，累计 4,529 颗，做的就是 Chromium 源码层的反检测——不打 JS 补丁、不改启动参数，而是在 C++ 层一次改出 49 个补丁，编译进二进制。这是海外热但国内主流 AI 媒体（36氪 / 量子位 / 机器之心 / 虎嗅）0 篇深度的工具，对国内做 AI 爬虫、做 Browserbase 同档服务的工程师价值很大。本文把它和 undetected-chromedriver / DrissionPage / camoufox / playwright-stealth 摆一起做硬指标对比，把官方 README 的 30/30 测试数字翻译成可复跑的命令，再讲讲国内开发者今晚就能装机的姿势。"
---

# CloakBrowser：49 个 C++ 补丁打 Cloudflare

![CloakBrowser 49 个 C++ 补丁打造的 stealth Chromium](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/cloakbrowser-stealth-chromium-2026-05-11/cloakbrowser-stealth-chromium-2026-05-11.png)

## 一、Playwright 抓不动 Cloudflare 的那一夜

写 grounded-search agent 的同行最近都遇到过同一道墙：上周还能跑通的 Playwright 脚本，今天打开就被 Cloudflare Turnstile 拦在登录前面；上个月 reCAPTCHA v3 还能拿到 0.7 分，本周直接掉到 0.1——服务器把这条流量当机器人，整个 pipeline 全废。

5 月 9 日北京时间晚上，GitHub Trending 首页飘出一个新名字：CloakHQ/CloakBrowser，单日涨 567 颗星，累计 4,529 颗。仓库 2026-02-22 创建到现在不到三个月、73 天里发了 27 个版本（平均 2.7 天一发），主语言是 Python（包装层），底子是一份编译好的自定义 Chromium 二进制，MIT 协议，36 个 Watcher、356 个 fork。

> **本文要回答的事**：CloakBrowser 把哪 49 个 C++ 补丁打到了 Chromium 上、它和 undetected-chromedriver / DrissionPage / camoufox 的硬数据差多少、国内 AI 爬虫工程师今晚怎么装机替掉自己的 Playwright、Browserbase 这种海外托管服务还要不要订。

同一周国内开发者群里被刷屏的还有四件事：DeepSeek V4-Flash 把 1M 上下文做到 13B 激活、千问 3.6-27B 在 SWE-Bench 拿到 77.2%、智谱 GLM-5.1 在 SWE-bench Pro 拿到 58.4%、月之暗面 Kimi K2.6 用 INT4 native 跑通了 256K 上下文。模型这一头国产开源已经追平海外，但下游的 grounded-search、agent 抓数据、自动化测试这一层卡住的不是模型，是浏览器——Cloudflare、Akamai、reCAPTCHA、FingerprintJS 这一圈反爬服务把闸门拧得越来越紧，传统的 `playwright-stealth` 这种 JS 注入打法越来越力不从心。

CloakBrowser 走的是另一条路：**把指纹改在 Chromium 的 C++ 源码里、再编译成二进制**。reCAPTCHA v3 拿到 0.9（人类区间）、Cloudflare Turnstile 通过、FingerprintJS 通过、BrowserScan 4/4——README 顶端把这些数字写得清清楚楚，并且在仓库 `tests/` 目录里附了 30 个独立检测站点的实测脚本。这些数字让国内做爬虫的同行第一时间想起一件事：**Browserbase 这家美国公司每千次浏览器会话收 5 美元的服务，本质上卖的就是这个能力**——而 CloakBrowser 是 MIT 协议、本机跑、零费用。

![CloakBrowser 49 个 C++ 补丁覆盖矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/cloakbrowser-stealth-chromium-2026-05-11/cloak-patch-matrix.png)

## 二、49 个 C++ 补丁到底改了什么

打开 README 看到「49 个源码级 C++ 补丁」时，第一反应通常是怀疑——补丁数字常常是营销话术，重要的是补在什么位置、覆盖哪些指纹维度。

CHANGELOG 把 49 这个数字的演进过程写得相当透明。2026-03-13 v0.3.15 是 33 个补丁、3-30 v0.3.19 升到 42 个（新增 9 个）、4-06 v0.3.20 升到 48 个（新增 6 个，覆盖 WebRTC IP 假名 / 代理信号去除 / 网络时序归零）、4-09 v0.3.22 加到 49 个，4-16 v0.3.25 在 Linux x64 + arm64 上升到 57 个（增加 WebAuthn / AAC / window position）、4-28 v0.3.26 把 Windows x64 也升到同样 57 个。这不是一次性堆上去的数字，而是 73 天里跟随 Chromium 145→146 版本号一次次重新 rebase 的累积。

49 个补丁不是平均铺开的——它们集中在 7 个传统反爬系统最重视的指纹维度上：

| 类别 | 补丁覆盖 | 解决的检测点 |
|---|---|---|
| Canvas / 字体 | canvas hash 噪声、字体枚举去 headless、AAC 音频解码器、emoji fallback 补全 | FingerprintJS 字体差异检测、Kasada / Akamai 字体缺失阻断 |
| WebGL / GPU | GPU vendor / renderer 池化、不同 seed 不同 GPU 画像、WebGPU 报告归一化 | BrowserScan GPU 一致性、headless 共用同一 GPU 画像识别 |
| Audio / WebRTC | AudioContext 采样指纹、WebRTC ICE IP 假名、MediaDevices 列表正常化 | reCAPTCHA WebRTC IP 泄露、Turnstile 音频指纹 |
| 自动化信号 | navigator.webdriver=false、window.chrome 对象齐全、plugins 列表非空、CDP isAutomatedWithCDP=false | Playwright / Puppeteer 老牌自动化标记 |
| 网络 / TLS | ja3n / ja4 / akamai 一致、DNS / connect / SSL 时序归零、QUIC over SOCKS5 UDP ASSOCIATE | Akamai TLS 指纹、代理特征剥离 |
| 存储 / 上下文 | StorageBuckets 配额归一、outerHeight 修正、incognito 检测 bypass | FingerprintJS 隐身模式识别 |
| 行为层 humanize | 贝塞尔曲线鼠标轨迹、按字逐键打字、isolated world keyboard | Cloudflare 行为分析、deviceandbrowserinfo.com 行为信号 |

`navigator.webdriver` 这一项可以单拎出来看。常规 Playwright 启动后这个属性是 `true`，是反爬系统识别自动化最古早的信号之一。`playwright-stealth` 通过注入一段 `Object.defineProperty` 的 JS 把它改回 `false`——但反爬系统回头就检测「这个 getter 是不是被 override 过」，一查就破。CloakBrowser 在 Chromium 源码 `WebDriverImpl::WebDriver()` 那一行直接把返回值改成 `false`，编译进 binary。检测站点拿到的就是「这是个原生没装 webdriver 的浏览器」的真实回答。

这 7 类补丁的设计逻辑可以总结成一句话：**所有指纹检测都问浏览器一个问题，所有反 stealth 都问"这个回答是不是被人改过"——CloakBrowser 让真实的浏览器从源头就给出"看起来正常"的回答，而不是事后再去说谎。**

## 三、和 Playwright / undetected / Camoufox 同台测试

光说技术原理没意思，反爬工具的硬通货是「拿到的分」。CloakBrowser README 给了一张五家同台测试表，把 reCAPTCHA v3 分数、Cloudflare Turnstile、补丁层级、Chrome 升级抗性、维护活跃度、内核、API 兼容性 7 个维度全摆出来。

![5 家工具反爬测试同台对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/cloakbrowser-stealth-chromium-2026-05-11/cloak-test-compare.png)

把这张表读完会发现一个之前被忽略的格局：**反爬这条赛道上有两个流派**。

**JS / 配置流派**（`playwright-stealth`、`undetected-chromedriver`）：在浏览器启动后通过 JS 注入或者命令行参数改写指纹。优点是上手成本低、几行 import 就能加。缺点是 Chrome 每升级一次都可能失效，反爬系统现在已经会检测「指纹被 JS override 过」这件事本身，所以越来越钝。CHANGELOG 上 `playwright-stealth` 最后一次提交是 2024 年，`undetected-chromedriver` 最后一次有意义提交是 2025 年中——两个项目都到了维护停滞期。

**C++ 源码流派**（Camoufox、CloakBrowser）：把指纹改在浏览器源码里、编译进 binary。优点是反爬系统拿到的是真实回答（因为 binary 里那个回答确实就是真的）。缺点是要维护一份自己的浏览器分支、跟着 Chromium 升级 rebase。Camoufox 走的是 Firefox 路线（daijro/camoufox 当下 8,147 颗星），CloakBrowser 是这条路上第一个 Chromium 实现。

具体的数字对比里有几个关键点值得说：

- **reCAPTCHA v3**：Stock Playwright 是 0.1（机器）、`undetected-chromedriver` 是 0.3-0.7、Camoufox 是 0.7-0.9、CloakBrowser 是 0.9（人类）。一个 grounded-search agent 如果在 reCAPTCHA v3 拿不到 0.5 以上，下游的搜索抓取链路基本走不通；0.9 这个数字意味着抓取行为和真实用户已经无法用单点检测区分。
- **Cloudflare Turnstile**：Stock Playwright 是失败、`playwright-stealth` 和 `undetected-chromedriver` 是「时灵时不灵」、Camoufox 是通过、CloakBrowser 是通过。「时灵时不灵」这个状态对生产系统意味着不可用——抓 1000 次有 200 次失败的 pipeline 没法上线。
- **Playwright API**：CloakBrowser 是原生支持、`undetected-chromedriver` 是 Selenium、Camoufox 不兼容。这一项决定迁移成本——Playwright 项目改成 CloakBrowser 是一行 import，改成 `undetected-chromedriver` 要重写整个测试代码。

需要把话说在前面：这张表里的所有数字都是 CloakBrowser 自己跑、自己写在 README 里的——和 agentmemory 在 LongMemEval 上的 95.2% 一样，没有第三方 leaderboard 复核。但仓库 `tests/` 目录里挂了完整的检测脚本（包括 `bot.incolumitas.com`、`deviceandbrowserinfo.com`、`browserscan.net` 这些公开站点的真实检测代码），开发者可以在自己机器上跑一遍核对。这种「数字 + 设置 + 可复跑脚本」的写法在反爬这条赛道上不多见。

## 四、国产对位：DrissionPage 和 camoufox 现在是什么档

写国内 AI 爬虫的工程师在 2026 年 5 月这个时点，桌上能拿出的开源工具不止 CloakBrowser 一个。把范围拉到「同档 stealth / 浏览器自动化」会发现这一格已经站满了：

![国产 / 海外 stealth 工具横评（2026-05-09 实测）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/cloakbrowser-stealth-chromium-2026-05-11/cloak-cn-compare.png)

挑几个国内同行最熟悉的项目说下定位：

- **g1879/DrissionPage**（11,923 颗星）：国内开发者最熟的反爬工具之一，Selenium + 真实浏览器接管思路，对 IP 类反爬、需要登录的场景做得很顺手；面对 Cloudflare Turnstile 时优势不明显，因为它本质上还是配置层补丁。
- **ultrafunkamsterdam/undetected-chromedriver**（12,615 颗星）：Selenium 时代的反爬主力，国内开发者很多存量项目还在用；2025 年起维护节奏放缓，对 2026 年这一代的 Cloudflare / reCAPTCHA 升级跟不上。
- **daijro/camoufox**（8,147 颗星）：Firefox 路线的源码级 stealth，技术质量很高，缺点是生态——国内大部分爬虫脚本和 AI agent 框架（browser-use / Crawl4AI / Stagehand）都是 Chromium 优先，Firefox 兼容性要额外适配。
- **lwthiker/curl-impersonate**（5,994 颗星）：另一个流派——不渲染 JS、只在 TLS 层做 ja3 / ja4 仿真。适合静态接口抓取，但 grounded-search agent 大多需要拿到 JS 渲染后的页面，curl-impersonate 这一档帮不上忙。
- **AtuboDad/playwright_stealth**（943 颗星）：JS 注入流派的 Playwright 版本，存量项目快速续命可以用，新项目不建议从这里起步。

需要补一个事实：CloakBrowser 母组织 CloakHQ 还有一个 `CloakBrowser-Manager`（177 颗星，2026 年新仓库），定位是「自托管版 Multilogin / GoLogin / AdsPower」——浏览器多账号 profile 管理 + 唯一指纹 + 代理隔离 + 持久 session，整套通过 Docker + noVNC 网页操作。这一层对国内做矩阵号、做电商运营、做对账自动化的同行有用，但和 grounded-search agent 这条主线关系较远。

把这张表合起来看，**反爬这一格的国产同档没有空缺、但 C++ 源码级 Chromium 流派只有 CloakBrowser 一家**。DrissionPage 这种国内项目的最大价值是「易上手 + 国内 IP 反爬场景成熟」，camoufox 是 Firefox 单点强化，curl-impersonate 是 TLS 单点。CloakBrowser 占的位置是「Chromium 内核 + Playwright API + C++ 源码补丁 + 主动维护」，4 个属性的交集在国内目前没有同档替代项目。

## 五、30 秒上手：Linux 一台机器装机

这一节直接讲「今晚怎么用上」。环境要求：Python 3.10+、Linux x64 / arm64 / macOS / Windows x64 都支持，Docker 镜像 `cloakhq/cloakbrowser` 也已经发布。首次启动会自动下载 Chromium 二进制（约 200MB，缓存到本地，之后启动 0 网络）。

![CloakBrowser 30 秒上手命令](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-11/cloakbrowser-stealth-chromium-2026-05-11/cloak-quickstart.png)

最小可运行例子是 5 行 Python——和 Playwright 完全一样的 API：

```python
from cloakbrowser import launch

browser = launch()
page = browser.new_page()
page.goto("https://protected-site.com")  # 之前会被 Turnstile 拦的页面
print(page.title())
browser.close()
```

要从 Playwright 项目迁过来，README 给了一段官方 diff，改动量是 3 行：

```diff
- from playwright.sync_api import sync_playwright
- pw = sync_playwright().start()
- browser = pw.chromium.launch()
+ from cloakbrowser import launch
+ browser = launch()

  page = browser.new_page()
  page.goto("https://example.com")
  # 后续代码不动
```

进阶用法值得展开几个：

- **humanize=True**：一行参数把鼠标、键盘、滚动行为切换成贝塞尔曲线 + 按字逐键 + 真实滚动节奏，覆盖 deviceandbrowserinfo.com 那种行为分析检测站点。`launch(humanize=True, human_preset="careful")` 还能切到更慢、更接近真实用户的节奏。
- **proxy + geoip 联动**：`launch(proxy="socks5://user:pass@host:port", geoip=True)` 一行命令把代理、时区、locale、WebRTC ICE 候选 IP 全部对齐到代理出口 IP——这是国内开发者用海外代理时最容易翻车的点（时区还是 UTC+8 但 IP 在美国，反爬系统一眼识别）。
- **launch_persistent_context**：`launch_persistent_context("./my-profile")` 把整个用户配置目录持久化，cookies / localStorage / 缓存 / IndexedDB 都跨会话保留。这一项绕过 incognito 检测同时让 profile 看起来是真实用户长期使用积累出来的。
- **Docker 一行试**：`docker run --rm cloakhq/cloakbrowser cloaktest` 不装 pip 直接验证 30 个检测站点，5 分钟跑完。

`humanize` 这个开关在国内 grounded-search 场景特别值。Cloudflare 在 2026 年 1 月之后引入了行为指纹（mouse curve entropy、scroll velocity 分布），常规 Playwright 因为是直线移动 + 瞬时点击全部命中。`humanize=True` 把这一层补齐之后，BrowserScan 行为检测从「DETECTED」变成「NORMAL（4/4 通过）」，deviceandbrowserinfo.com 24 个行为信号全部通过。

## 六、Browserbase / Bright Data 这种海外托管还要不要订

国内做 grounded-search agent 的同行相当一部分是订 Browserbase 或者 Bright Data 解决反爬这一层的——前者收 5 美元/千会话，后者按流量。CloakBrowser 出现之后这个订阅决策可以重新算一遍。

| 维度 | Browserbase 托管 | CloakBrowser 自部署 |
|---|---|---|
| 月成本（10 万次会话） | 约 500 美元 | 服务器电费 + 代理费 |
| 反爬强度 | 闭源 stealth Chromium | 49 个 C++ 补丁开源 + reCAPTCHA 0.9 |
| Chromium 版本 | 平台决定 | 自己控（v0.3.26 是 146.0.7680.177.4）|
| Session API | Browserbase SDK | 原生 Playwright |
| 数据驻留 | 海外（出海合规友好）| 自己机器（数据合规友好）|
| 二次开发 | 黑盒 | MIT 源码 |
| 故障 / 升级 | 平台兜底 | 自己跟 |

这张表读完会发现两条结论：

第一，**CloakBrowser 不是 Browserbase 的简单替代品**——Browserbase 卖的不仅是反爬，还有 session 录制、并发管理、跨地域出口 IP 池这一套基础设施。如果团队需要的是「立刻有 1000 个并发会话从全球 50 个 IP 段出去」，CloakBrowser 自己跑要叠加代理服务、session manager（CloakBrowser-Manager 或者自研）、监控这一套，工作量不止替换一行 import。

第二，**对国内大量「中等规模 + 数据本地化」的 grounded-search agent 团队，CloakBrowser 的成本比是压倒性的**。日均 1 万次抓取、需求是「能稳定绕过 Cloudflare + reCAPTCHA + FingerprintJS」、数据要落国内服务器——这一档过去一年是 Browserbase 的主场，CloakBrowser 出来之后整个账可以重算。一台 4U 服务器 + 几个商业代理池 + CloakBrowser 自部署，月成本能压到 2,000 元人民币以内。

海外大量做 grounded-search 的团队当下都是 Browserbase 重度用户，CloakBrowser 出来之后给团队多了一个「不出海购买、可审计、可二次开发、数据落本地」的选项——这一格的开源版本第一次成立。

## 七、诚实的几条疑问

写完几张表和命令例子之后，需要把几条诚实的疑问留在这里——这是一个 73 天的新项目，没必要捧杀。

**疑问 1：49 / 57 这个数字会不会通货膨胀？** Chromium 每个 milestone 升级（145→146）都会让一部分补丁需要 rebase，反爬系统也在每个月引入新的检测维度。CHANGELOG 显示 0.3.15 到 0.3.26 这 2 个月里补丁数从 33 涨到 57，速度不慢。但补丁数本身不是 KPI，重要的是「覆盖到反爬系统当下使用的所有维度」。如果哪天反爬系统加了一个 CloakBrowser 没覆盖的指纹，单个补丁能不能跟上，要看维护者的反应速度。当下每 2.7 天一个版本是好信号，但 73 天的项目不足以判断长期。

**疑问 2：reCAPTCHA 0.9 是不是被精挑测试出来的？** README 说在 30+ 检测站点上跑过，并把测试代码开源在 `tests/`。这一点比绝大多数同行更透明。但 reCAPTCHA 真实生产环境会综合行为信号、历史 IP 信誉、Google 账号关联多个维度——单跑 demo 站点拿 0.9 不等于真实业务里也是 0.9。生产部署前自己用真实业务流跑一遍是必须的。

**疑问 3：Chromium binary 200MB + 自动更新，安全吗？** README 说所有 binary 下载用 SHA-256 校验、binary 走自家 CDN 分发。这是对的姿势，但「自家 CDN 是否被劫持」「自动更新机制是否可关」这两件事在生产环境要明确。仓库提供了 `python -m cloakbrowser update` 的手动控制，企业内网部署建议关掉自动更新、走内网镜像。

**疑问 4：Browser Profile Manager（CloakHQ/CloakBrowser-Manager）177 颗星是不是泡沫？** 这是 CloakHQ 同生态的另一个仓库，定位是 Multilogin 自托管版。177 颗星反映两件事——一是发布时间短（2026 年 4 月才公开），二是用户群和主仓库不完全重合（用 stealth 浏览器写代码的工程师 vs 做矩阵号运营的非技术用户）。这个仓库本身和 grounded-search agent 主线关系不大，不必为它花时间。

**疑问 5：MIT 协议、binary 也开源？** README 写了主仓库 MIT，binary 单独有一个 `BINARY-LICENSE.md`。读了文件之后是「binary 自由分发但禁止用于反编译规避检测系统的恶意目的」——典型的 dual license 安排，对国内大部分场景（写自己业务的爬虫 / agent）没限制，但需要把这一行写进自己 repo 的 license 备注。

## 八、所以国内开发者今天能做什么

把这一篇收住到一句话：**反爬这件事在 2026 年 5 月有了一个开源 + 源码级 + Chromium 内核 + Playwright API 的解决方案，而国内做 grounded-search agent / AI 爬虫 / 浏览器自动化的同行恰好需要这一格。**

CloakBrowser 不是另起炉灶——之前 Browserbase / Bright Data 这种托管服务、DrissionPage / camoufox 这种开源工具已经把这条路趟开。CloakBrowser 的价值是把「反爬强度（reCAPTCHA 0.9）+ Chromium 内核 + Playwright API + MIT 开源 + 主动维护」这五个属性第一次合到一个项目里——这五个属性的交集过去不存在。

国内 AI 开发者这一代赶上的是好时点：底层模型有国产开源（千问 / DeepSeek / GLM / Kimi），推理引擎有自研（vLLM / SGLang），AI Coding 工具有自家（通义灵码 / Trae / Qoder / OpenClaw / Cline），持久记忆栈有 agentmemory 这种横评第一名，今天浏览器底座这一格也凑齐开源版本。整套私有化 AI 工具链第一次能从「下个月再说」变成今晚就能装机——前辈把路趟出来了，下一程值得期待。

---

**重要链接**

- 项目主页：[cloakbrowser.dev](https://cloakbrowser.dev/)
- 仓库：[github.com/CloakHQ/CloakBrowser](https://github.com/CloakHQ/CloakBrowser)（4,529 颗星，本文写作时实测）
- Profile Manager（同生态）：[github.com/CloakHQ/CloakBrowser-Manager](https://github.com/CloakHQ/CloakBrowser-Manager)
- 国内同档对位：DrissionPage、undetected-chromedriver、camoufox、curl-impersonate、playwright_stealth（主语见正文 §四）
