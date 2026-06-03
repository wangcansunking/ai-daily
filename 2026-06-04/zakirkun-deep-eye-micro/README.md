# GitHub趋势项目Deep Eye：一支由OpenAI、Claude等组成的AI红队

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/zakirkun-deep-eye-micro/head.png)

一周前，一个名为 Deep Eye 的项目还只是 GitHub 上一个安静的 Python 仓库。到 2025 年 5 月 23 日，它已经爬上了 Trending 榜单，README 里列出的功能清单长得像一份安全厂商的产品路线图——45 种以上漏洞扫描、10 家 AI 提供商动态切换、合规框架映射、Nuclei 式 YAML 模板、Cloudflare 挑战绕过、Playwright 浏览器自动化，甚至还有团队协作扫描和扫描结果 diff。

一个开源渗透测试工具，把这些东西全部塞进了一个 `pip install` 里。

![zakirkun/deep-eye — Deep Eye orchestrates multiple AI providers (OpenAI, Claude,](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/zakirkun-deep-eye-micro/content-1.png)

**这不是工具，这是一支自动化红队外包公司，只不过它的雇员是 OpenAI、Claude、Gemini、Grok 和你的本机 OLLAMA。**

![原文配图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/zakirkun-deep-eye-micro/content-2.png)



![Deep Eye 的 10 家 AI 提供商架构示意，从云端 API 到本地模型的动态切换拓扑](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-04/zakirkun-deep-eye-micro/schematic-1.png)



## 10 个 AI 大脑同时给你写 payload，出错了就换下一个

Deep Eye 最让我愣神的设计，不是它扫了多少种漏洞，而是它对 AI 提供商的态度：不是选一家最好的，而是全都要，同时做好 failover。

在 `config/config.yaml` 里，你可以同时启用 OpenAI（`gpt-4o`）、Claude（`claude-3-5-sonnet-20241022`）、Grok、Gemini、OLLAMA 本地模型（`llama2`）、Groq、Mistral、OpenRouter、LM Studio 和 LiteLLM——整整 10 家。项目文档明确写了「Dynamic switching and failover across 10 AI providers」，这意味着当一家 API 挂了、限流了、或者返回了明显不靠谱的 payload，引擎会自动切到下一家继续干活。

这个设计的实际含义是：一个渗透测试人员现在拥有 10 个同时待命的 AI 攻击助手，每个都读过不同的训练语料、有不同的推理风格。Claude 擅长逻辑推理，可能更适合构造复杂的 SSRF 链；Gemini 对 Web 安全的理解可能偏 Google 系；本地 OLLAMA 跑 `llama2` 则完全离线，适合不想把目标 URL 发给任何第三方的场景。

而 Deep Eye 的 AI 能力不止于生成 payload。它的 `ai_payload_generator.py` 模块会做三件事：检测目标是否部署了 WAF、识别技术栈指纹（PHP？Node？Django？）、然后根据数据库类型（MySQL？PostgreSQL？MongoDB？）生成对性的注入载荷。这不是模板替换，是上下文感知的智能生成——「Context-Aware Payloads」被明写在 Feature 列表第二行。

更激进的是 AI Triage 模块。它能自动过滤误报，`drop_threshold` 默认 0.8，`min_severity` 可以设成 low，也就是说只有 AI 认为置信度低于 80% 且严重度足够高的漏洞才会被丢掉。过滤完之后，它还能自动生成 Bug Bounty 格式的报告，输出 Markdown 文件到 `reports/bounty` 目录。一个渗透测试员从发现漏洞到提交赏金报告的整个流程，被压缩成了几行 YAML 配置。



## 从 CVE 情报到合规报告，一条龙自动化

Deep Eye 的另一个让我觉得「这不像个人项目」的模块，是它的 CVE Intelligence 系统。

它内置了一个 RAG 检索增强生成索引，数据源来自 NVD（美国国家漏洞数据库）、MITRE 和 Exploit-DB。仓库里有两个脚本专门干这个：`scripts/update_cve_database.py` 负责从 NVD 拉取最新的 CVE 数据，`scripts/build_cve_rag_index.py` 负责构建向量索引。这意味着当你扫描一个目标时，Deep Eye 不是盲目地扔 payload，而是先检索这个目标的技术栈历史上出现过哪些 CVE，然后让 AI 根据真实的漏洞利用模式（real exploit patterns）生成攻击载荷。

这和传统扫描器的签名匹配有本质区别。传统扫描器靠规则库，规则没写的漏洞扫不出来；Deep Eye 靠的是「知道这个框架历史上出过什么事」+「让 AI 根据历史模式推断现在还能怎么打」。虽然官方没给出漏洞检出率的 benchmark 对比，但这个思路本身已经非常接近人类渗透测试专家的思维方式了。

扫描完成后，报告生成模块支持 6 种导出格式：HTML、PDF、JSON、JUnit XML、CSV、XLSX。JUnit XML 这个选项尤其有意思——这意味着你可以直接把安全扫描结果塞进 CI/CD 流水线，让 Jenkins 或 GitLab CI 像对待单元测试一样对待漏洞扫描。如果某个严重漏洞没修，构建直接标红。

合规映射模块则覆盖了 PCI-DSS v4、SOC2 CC 和 ISO 27001:2022 三套框架。每个检出的漏洞会自动映射到对应的合规条款，生成的报告可以直接拿去应付审计。一个渗透测试工具居然考虑到了合规审计的需求，这让我怀疑作者要么自己就是被审计折磨过的安全工程师，要么已经想好了这个工具的商业化路径。

还有一个容易被忽略但很实用的功能：Scan Diffing。你可以把两次扫描的 JSON 结果做对比，`--diff baseline.json current.json --diff-format html --diff-output diff_report.html`，输出一份 HTML 报告告诉你哪些漏洞是新的、哪些已经被修复了。对于需要持续监控安全态势的团队来说，这个功能比单次扫描有价值得多。



## 一个 MIT 协议的开源项目，踩在了所有人的边界上

Deep Eye 的 README 底部有一段 Legal Disclaimer，措辞标准但语气严肃：只用于授权测试，未经授权访问计算机系统违法，使用者自行承担法律责任，开发者不承担任何误用责任。项目用 MIT 许可证，这意味着任何人都可以 fork、修改、商用，几乎没有限制。

但问题在于，这个工具的能力集合已经远远超出了「帮助白帽子做授权测试」的范畴。Cloudflare 和 Akamai 的挑战绕过模块（`challenge_solver`）自带 cookie 持久化，Playwright + Browser Use AI 模块能做客户端自动化测试，mitmproxy 中间人代理模块能拦截和篡改流量——这些功能拼在一起，几乎就是一个完整的攻击链自动化平台。

更微妙的是 AI 提供商的使用方式。如果你在 `config.yaml` 里启用 OpenAI 的 API Key，Deep Eye 会把目标 URL 和扫描上下文发送给 OpenAI 的服务器。OpenAI 的使用政策是否允许用它的模型生成渗透测试 payload？Claude 的 Acceptable Use Policy 会不会把 SQL 注入载荷判定为「恶意代码生成」？Grok 和 Gemini 的态度又是什么？目前没有一家 AI 提供商明确表态过对这类工具的立场，而 Deep Eye 一次调用了 10 家。

这个项目让我想起 2024 年爆火的「黑客 GPT」类工具的争议，但 Deep Eye 比那些玩具严肃得多。它有完整的测试套件（`pytest` 可运行）、有端到端测试（`tests/e2e_litellm.py`）、有 25 个以上的功能模块、有 YAML 模板引擎兼容 Nuclei 社区生态——这是一个工程化程度极高的项目，不是 demo。

它会不会被滥用？一定会。它会不会逼迫 AI 提供商重新审视自己的 API 使用条款？很可能。它会不会让安全行业重新讨论「自动化攻击工具的伦理边界」这个老问题？已经在发生了。

有意思的是，Deep Eye 的 CLl 设计刻意做得极简，所有复杂逻辑都藏在 `config/config.yaml` 里，命令行只有 `-u`、`-c`、`-v`、`--formats` 这几个参数。作者显然希望降低使用门槛——一个初级安全研究员花 5 分钟配好 API Key，就能跑出以前需要高级渗透测试工程师花一天才能完成的工作量。

AI 没有让安全行业消失，它只是把攻击能力的基线往上抬了一大截。Deep Eye 就是这截新基线的刻度尺。

## 参考来源
- https://github.com/zakirkun/deep-eye

#Deep #Eye #AI #OpenAI #Claude
