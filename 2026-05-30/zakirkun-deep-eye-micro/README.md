# Deep Eye 一周冲上 GitHub Top 3：如何用 10 个 AI 模型自动化渗透测试？

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/zakirkun-deep-eye-micro/head.png)

一周前我在翻 GitHub Trending 的时候，看到一个项目的星星数还在三位数。七天后再刷新，它已经冲上 Python 榜 Top 3，底下 issue 区挤满了红队工程师和 Bug Bounty Hunter 在吵「能不能加个 Nuclei 模板兼容层」。

项目名叫 Deep Eye。作者 zakirkun 在 README 里写了一句话：

![zakirkun/deep-eye — Deep Eye orchestrates multiple AI providers (OpenAI, Claude,](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/zakirkun-deep-eye-micro/content-1.png)

> Deep Eye orchestrates multiple AI providers for intelligent payload generation, scans targets for 45+ vulnerability types, and produces professional reports with compliance mapping.

![原文配图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/zakirkun-deep-eye-micro/content-2.png)

翻译成人话：一个命令行工具，接上 OpenAI、Claude、Grok、Gemini、OLLAMA、Groq、Mistral、OpenRouter、LiteLLM、LM Studio 里任意一个模型，就能自动生成渗透测试 Payload、扫 45 种以上漏洞类型、然后输出带 PCI-DSS v4 / SOC2 / ISO 27001 合规映射的专业报告。

**一个渗透测试工具把 AI Provider 做成了可插拔的算力后端——这思路比大多数 SaaS 创业公司都激进。**



![Deep Eye CLI 启动界面，ASCII banner + 交互式配置向导，10 个 AI provider 选择列表](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/zakirkun-deep-eye-micro/schematic-1.png)



---

## 10 个 AI 后端轮换、45 种漏洞覆盖——这工具把「自动化渗透」的边界推到了哪

先看一组数字。Deep Eye 支持的漏洞类型覆盖了 SQL 注入（基于错误 / 盲注 / 时间盲注三种变体）、XSS（反射 / 存储 / DOM）、命令注入、SSRF、XXE、路径穿越、CSRF、CORS 错误配置、安全头缺失等基础项。但这只是 v1.0 的起点。到 v1.4，它的扫描器已经扩展到 NoSQL 注入、HTTP 请求走私、竞态条件、Log4Shell、批量赋值、原型污染、OAuth 攻击、缓存投毒、子域名接管、SAML 攻击、端口扫描、目录爆破和密钥泄露检测。

45 种不是虚数。项目 `modules/` 目录下有 25 个以上的独立模块文件，每个对应一类攻击面的自动化探测逻辑。

但真正让我愣神的是 AI Provider 的设计。Deep Eye 没有绑定任何一家模型厂商。它的 `ai_providers/` 目录里集成了 10 个后端，配置文件里用 `enabled: true/false` 控制开关，支持动态切换和故障转移。这意味着你可以把 OpenAI 当主力、Claude 当备胎、OLLAMA 跑本地模型做敏感目标测试、Groq 加速推理——全在一个 `config.yaml` 里搞定。

**「不绑定模型」这件事，在渗透测试工具里第一次被做成了系统级特性，而不是一个 feature request。**

Payload 生成逻辑也不是简单的「把漏洞描述扔给 GPT 让它编一个」。Deep Eye 做了三层上下文感知：WAF 检测（识别目标前面有没有 Cloudflare 或 Akamai）、技术栈指纹识别（判断后端是 PHP / Java / Node / Python）、数据库特定注入（MySQL vs PostgreSQL vs MongoDB 的 Payload 语法完全不同）。AI 在生成 Payload 之前会先拿到这些指纹数据，然后才输出——这相当于给模型装了个「渗透测试专用的 system prompt」。



![Deep Eye 扫描流程架构图，从目标 URL → 指纹识别 → AI Payload 生成 → 漏洞验证 → 合规映射 → 报告输出的完整 pipeline](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/zakirkun-deep-eye-micro/schematic-2.png)



---

## CVE 情报用 RAG 索引、报告直接对标合规框架——这已经不是脚本小子的玩具了

如果只是跑 45 种扫描器 + AI Payload 生成，Deep Eye 大概就是一个「高级版的 sqlmap + Burp Suite 插件缝合怪」。但 zakirkun 做了两个让安全工程师无法忽视的决定。

第一，CVE 情报模块。项目里有一个 `cve_intelligence/` 目录，里面是 CVE 爬虫 + RAG 向量索引构建器。它从 NVD、MITRE、Exploit-DB 三个源拉取漏洞数据，本地建索引，扫描时实时匹配目标技术栈对应的已知 CVE，把真实 Exploit 模式注入到 Payload 生成流程里。README 里直接给了脚本：

```bash
python scripts/update_cve_database.py    # 从 NVD 拉最新 CVE
python scripts/build_cve_rag_index.py    # 建向量索引
```

这意味着 Deep Eye 不是一个「用固定规则匹配漏洞」的扫描器。它在扫描时会检索历史上对同类技术栈的真实攻击模式，然后让 AI 基于这些模式生成新的 Payload。这跟传统 DAST 工具的差异，大概相当于 ChatGPT 跟 `grep` 的差异。

第二，合规映射。这是我在渗透测试工具里见过的最「企业级」的功能。Deep Eye 的报告不只是列漏洞，它会把每个发现的漏洞映射到三个合规框架的具体控制项：PCI-DSS v4、SOC2 CC、ISO 27001:2022。举个例子，扫到一个 SQL 注入，报告里会自动标注这对应 PCI-DSS 要求 6.5.1（防止注入漏洞）和 ISO 27001 A.8.26（应用安全要求）。这对需要用合规审计的团队来说，等于把渗透测试报告直接翻译成了审计证据。

**安全工程师最头疼的两件事——「这个漏洞跟合规有什么关系」和「怎么给老板解释修它的优先级」——被一个 YAML 配置解决了。**



![合规映射示意图，左侧漏洞列表（SQLi/XSS/SSRF），右侧三列合规框架对照表，箭头标注映射关系](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/zakirkun-deep-eye-micro/schematic-3.png)



AI Triage 模块也值得单独拎出来说。它做了两件事：自动过滤误报（`drop_threshold: 0.8`，置信度低于 80% 的结果直接丢掉），以及自动生成 Bug Bounty 报告（Markdown 格式，按 `medium` 以上严重度输出）。这意味着一个白帽子可以用 Deep Eye 扫完目标，直接拿到一份可以直接提交给 HackerOne 的报告草稿，不需要手动写「Steps to Reproduce」。

项目还支持扫描对比（Scan Diffing）——把两次扫描的 JSON 结果扔进去，输出新增 / 修复漏洞的差异报告。对于需要持续监控攻击面的团队，这个功能让 Deep Eye 从「一次性扫描工具」变成了「持续安全监测平台」的轻量替代品。

---

当然，Deep Eye 远不是完美的。它的 Playwright 浏览器自动化依赖 `pip install playwright && playwright install chromium` 这种「装个 Chromium 自己跑」的粗暴方案，Challenge Solver 虽然声称能绕过 Cloudflare 和 Akamai，但 Cookie 有效期只有 1800 秒（30 分钟），真要在生产环境里用，大概率还是会被 JS Challenge 卡住。而且整个项目目前只有一个作者在维护，issue 区的 feature request 已经堆到 40 多个，从「支持自定义 HTTP Header 注入」到「加个 Web GUI」什么都有。

但话说回来，一个 MIT 协议开源、支持 10 个 AI Provider 热切换、覆盖 45 种漏洞、能出合规映射报告、还能自动写 Bug Bounty 报告的工具，让一个小团队的安全工程师单兵作战能力直接翻倍——这大概就是为什么它在 GitHub Trending 上一周内从三位数星星冲到 Python 榜前列。

**当渗透测试的门槛从「十年红队经验」降到「一个 config.yaml + 一个 API key」，防守方该睡不着了。**

---

## 参考来源

- GitHub - zakirkun/deep-eye: https://github.com/zakirkun/deep-eye

#Deep #Eye #AI #OpenAI #Claude
