# 微软AGT工具包发布，直面OWASP十大AI Agent安全风险

> ai-daily · 2026 年 5 月 26 日 08:16 · 来源：GitHub Trending python

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/microsoft-agent-governance-toolkit-article/head.png)

2026 年 5 月 7 日凌晨两点，硅谷某 SaaS 公司的基础架构 Slack 频道里，一条告警弹了出来：

> `GovernanceDenied: Action denied by policy rule 'block-destructive': Destructive operations require human approval.`

![microsoft/agent-governance-toolkit — AI Agent Governance Toolkit — Policy enforc](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/microsoft-agent-governance-toolkit-article/content-1.png)

值班工程师盯着屏幕愣了三秒。他们的客服 Agent 刚才接到一个用户工单，用户说“帮我把所有历史订单删掉”，Agent 理解了这个请求，调用 `delete_orders` 工具——然后被一个 YAML 文件挡了下来。不是模型拒了，不是 prompt 里写了“请遵守规则”，是应用层的 governance kernel 在工具调用抵达数据库连接池之前的 17 毫秒内，判定 `action.type == 'delete'`，直接抛异常。

工程师在 war room 里打了一行字：“We didn't ask it to behave. We made it incapable of misbehaving.”

这句话不是他原创的。它来自微软三天前在 GitHub 上开源的一个项目——Agent Governance Toolkit（AGT）——的核心理念。这个项目在 GitHub Trending 上冲到了 Python 榜第一，star 数 24 小时内破了两千。而它的 README 第一段话，几乎是一封对全行业 AI Agent 部署现状的公开诊断书。

**Prompt-level safety is not a control surface. It is a polite request to a stochastic system.**

这句话值得刻在每个正在把 Agent 推上生产环境的技术负责人桌上。因为过去 18 个月，我们在做的事情本质上就是“礼貌地请求一个随机系统遵守规则”——然后祈祷它听话。

---

## 一、OWASP 说得很直白：prompt 防不住，数字也证明了

OWASP 在 2025 年发布了专门对 AI Agent 的 Top 10 风险清单——Agentic Top 10。其中 LLM01:2025（Prompt Injection）的开篇陈述，被 AGT 的 README 直接引用：

> "It is unclear if there are fool-proof methods of prevention for prompt injection."

这不是危言耸听。微软自己的 AI Red Teaming Agent 团队在过去两年里红队测试了 100 个生成式 AI 产品，结论写在公开报告里——“AI red teaming is never complete”。因为模型层的防御本质上就是概率性的，你永远不能证明一个 prompt 防御是完备的。

而学术界的数据更让人睡不着觉。JailbreakBench（Chao et al., NeurIPS 2024）——这是目前 LLM 越狱攻防领域最权威的开放基准——显示自适应攻击（adaptive attacks）对前沿安全对齐模型的攻击成功率接近 100%。Andriushchenko et al., 2024 的论文报告了一个更扎心的数字：仅使用简单的 prompt-only 攻击，GPT-4、GPT-3.5、Claude 3、Llama-3 全部达到 100% Attack Success Rate（ASR）。即便是目前公开发表的最强 prompt 层防御方案，残余 ASR 仍然停留在两位数百分比。

让我愣神的是这个对比：安全团队花了两年时间做 alignment、RLHF、red teaming、constitutional AI，结果攻击者用几行精心构造的 prompt 就能把 ASR 打到 100%。这就像你在金库门口装了虹膜识别、声纹验证、量子加密锁，但金库的墙是纸糊的。



![prompt 层防御 vs 应用层治理的对比，左侧是“礼貌请求”被绕过，右侧是 governance kernel 在工具调用前拦截](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/microsoft-agent-governance-toolkit-article/schematic-1.png)



微软显然意识到了这个问题。但他们的解法不是继续在 prompt 层加码——AGT 的 README 明确说：“AGT does not try to win that fight inside the prompt.” 他们选择了一条完全不同的路径：**在模型意图抵达执行层之前，用确定性代码拦截每一次工具调用、消息发送和 Agent 间委托**。

这就是 AGT 的核心架构决策——把安全边界从“模型输出”移到“应用中间件”。AGT kernel 拒绝的操作不是“不太可能发生”，而是“结构上不可能发生”。这不是语义上的细微差别，这是两种完全不同的安全范式。

---

## 二、两行代码给 Agent 戴上镣铐——这个 API 设计是故意的

AGT 的 Quick Start 只有两行 Python：

```python
from agentmesh.governance import govern
safe_tool = govern(my_tool, policy="policy.yaml")
```

就这两行。`safe_tool` 会在每次被调用时评估 YAML 策略、记录决策日志，如果被策略阻止就抛出 `GovernanceDenied` 异常。策略文件长这样：

```yaml
apiVersion: governance.toolkit/v1
name: production-policy
default_action: allow
rules:
  - name: block-destructive
    condition: "action.type in ['drop', 'delete', 'truncate']"
    action: deny
    description: "Destructive operations require human approval"
  - name: require-approval-for-send
    condition: "action.type == 'send_email'"
    action: require_approval
    approvers: ["security-team"]
```

我第一眼看到这个 YAML 设计的时候，觉得它太简单了——简单到让人觉得“这也能叫治理？”但仔细想想，这正是它最聪明的地方。Agent 治理的落地瓶颈从来不是“没有足够复杂的策略引擎”，而是“团队根本没用上任何策略引擎”。两行代码的门槛意味着一个工程师在喝第一口咖啡之前就能把治理层挂上自己的 Agent。

（值得一提的是，AGT 的策略引擎同时支持 YAML、OPA 的 Rego 语言和 AWS 开源的 Cedar 策略语言——Cedarling 集成已经作为 example 出现在仓库里。这意味着已经在用 AWS Verified Permissions 的团队可以直接迁移策略定义。）

但“简单”不等于“简陋”。AGT 的整体架构是分层可插拔的：

```
Agent ──► Policy Engine ──► Identity ──► Audit Log
            (YAML/OPA/Cedar)  (SPIFFE/DID/mTLS)  (Tamper-evident)
                 │                                      │
                 ├── Allowed ──► Tool executes           │
                 └── Denied  ──► GovernanceDenied        │
                                                        ▼
                                                 Decision Record
```

每一层都是可选的。README 说得很坦白：“Most teams run policy enforcement + audit logging and never need the full stack.” 这很诚实——大部分团队只需要策略引擎和审计日志就够了，但如果你需要零信任身份、防篡改审计链、执行沙箱、SLO 监控、混沌测试，AGT 的完整技术栈也在那儿等着你。



![AGT 分层架构图，从 Agent 到 Policy Engine 到 Identity 到 Audit Log，展示每层的可选性和决策记录流向](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/microsoft-agent-governance-toolkit-article/schematic-2.png)



---

## 三、十个 spec、992 个一致性测试——这不是一个“玩具级”治理框架

如果你觉得 AGT 只是又一个“微软开源了个 Python 库”的故事，那你可能低估了这个项目的工程野心。AGT 仓库里有 10 份正式的 RFC 2119 规范文档，每一份都定义了组件的行为契约——什么必须做（MUST）、什么应该做（SHOULD）、什么可以做（MAY）。这些 spec 覆盖了策略评估、身份信任、执行沙箱、SRE 治理、MCP 安全网关、RL 训练治理、框架适配器契约、审计合规等全部核心模块。

10 个 spec，992 个一致性测试，25 个架构决策记录（ADR）。这个数字组合传递的信号很明确：这不是一个实验性项目，这是一个面向生产环境的基础设施级工具。

让我觉得最有意思的是 `Agent Hypervisor` 这个模块。它的 spec 定义了一个四环特权模型（four privilege rings），类似操作系统的 ring 0-3 设计，但在 Agent 的语境下重新定义了每一环的权限边界。最内环是“执行计划验证”——Agent 在执行多步操作前，hypervisor 会先验证整个执行计划是否可逆（reversibility verification）。如果计划包含不可逆操作（比如 `DROP TABLE`），hypervisor 会要求额外的审批签名或者直接拒绝。

这个设计思路其实来自数据库领域的 saga 模式，但把它应用到了 Agent 的自治决策链上。Agent 不是执行一条 SQL，它可能执行一个包含 15 步工具调用的复杂计划——第 7 步调了 Stripe API 扣款，第 12 步调了 SendGrid 发邮件，第 14 步调了 Snowflake 跑一个 ETL 任务。Hypervisor 的职责是在计划执行前做一次完整的“可逆性扫描”，标记出哪些步骤一旦执行就无法回滚，然后根据风险等级决定是放行、要求审批还是直接阻断。



![Agent Hypervisor 四环特权模型，从执行计划验证到可逆性检查到审批门控到执行审计](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/microsoft-agent-governance-toolkit-article/schematic-3.png)



AGT 的安全模型还有一个值得注意的设计选择：**策略引擎和 Agent 共享同一个进程边界**。README 在 Security 章节明确写了：“AGT enforces governance at the application middleware layer, not at the OS kernel level.” 生产环境的推荐做法是每个 Agent 跑在独立容器里，用 OS 级隔离作为第二道防线。

这个坦诚的“已知局限”声明反而让我对这个项目更有信心——它没有假装自己是一颗银弹。配合 CodeQL SAST、Gitleaks 密钥扫描、ClusterFuzzLite 的 7 个 fuzz target（覆盖策略引擎、注入检测、MCP、沙箱、信任评分）、Dependabot 监控 13 个生态的依赖、以及每周跑一次的 OpenSSF Scorecard——这套安全工程实践至少是认真的。

---

## 四、5 种语言 SDK + 11 个框架适配器——生态覆盖的野心藏不住

AGT 的 SDK 支持矩阵是这样的：Python（全栈）、TypeScript、.NET、Rust、Go——五种语言都有核心治理能力（策略、身份、信任、审计），Python 拥有完整技术栈。这还没完：

- **GitHub Copilot CLI** 有专门的治理安装器（`npx @microsoft/agent-governance-copilot-cli install`）
- **Claude Code** 有第一方治理插件包（`claude --plugin-dir ./agent-governance-claude-code`）
- .NET 有专门的 MCP 扩展包（`Microsoft.AgentGovernance.Extensions.ModelContextProtocol`），可以直接在 `AddMcpServer()` 后面链式调用 `.WithGovernance()`

框架适配器列表覆盖了目前市面上几乎所有主流 Agent 框架：Microsoft Agent Framework（原生中间件）、Semantic Kernel（.NET + Python 原生）、AutoGen（适配器）、LangGraph/LangChain（适配器）、CrewAI（适配器）、OpenAI Agents SDK（中间件）、Google ADK（适配器）、LlamaIndex（中间件）、Haystack（Pipeline）、Mastra（适配器）、Dify（插件）、Azure AI Foundry（部署指南）。

11 个框架适配器，152 个一致性测试专门验证适配器的拦截链行为。这个覆盖度说明 AGT 的目标不是“让微软生态内的 Agent 变安全”，而是“让整个行业的 Agent 都能用上确定性治理”。



![AGT 语言 SDK 和框架适配器矩阵图，展示 Python 全栈 vs 其他语言核心功能，以及 11 个框架的集成方式](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/microsoft-agent-governance-toolkit-article/schematic-4.png)



CLI 工具链的设计也很有实战感：

- `agt doctor`：检查安装环境
- `agt verify`：OWASP 合规检查，支持 `--evidence` 和 `--strict` 参数，可以在 CI 里跑，证据不足直接挂构建
- `agt red-team scan ./prompts/ --min-grade B`：12 向量的 prompt 注入审计，给提示词打分，低于 B 级就告警
- `agt lint-policy policies/`：策略文件语法和逻辑校验

这套 CLI 工具的命名——`doctor`、`verify`、`lint`——明显借鉴了 Kubernetes 生态的 `kubectl` 风格。它假定用户是每天和 CI/CD 管道打交道的工程师，而不是安全合规部门的人。这种“把治理变成开发工具”的产品思路，可能是 AGT 能否被大规模用的关键。

---

## 五、OWASP Agentic Top 10 全覆盖 + 四重合规映射——企业买单的理由

AGT 的合规映射覆盖了四套标准：OWASP Agentic AI Top 10（全部 ASI 风险类别有确定性控制映射）、NIST AI RMF 1.0（GOVERN、MAP、MEASURE、MANAGE 四个功能对齐）、EU AI Act（合规映射 + 自动化证据生成）、SOC 2（控制映射 + 审计轨迹导出）。

这不是一个“我们觉得这样比较安全”的项目。这是一个“我们知道审计师会问什么，所以提前把证据链准备好了”的项目。

`agt verify --evidence ./agt-evidence.json` 这个命令输出的 JSON 文件，本质上就是一份机器可读的合规证据包。在 SOC 2 Type II 审计或者 EU AI Act 合规审查时，这份证据可以直接提交给审计师，证明“Agent 的每一次工具调用都经过了策略引擎评估，且评估结果被防篡改地记录了”。

防篡改审计日志的做到方式也值得一提。AGT 的 Audit and Compliance spec 定义了 Merkle 审计结构——每一笔决策记录都被哈希后链入一个 Merkle tree，根哈希可以被外部锚定（commitment anchoring）。这意味着你无法事后修改一条“这个操作被允许了”的记录而不破坏整棵树的哈希一致性。这在技术上和 Certificate Transparency 的日志系统是同源的思路。

---

## 六、Shadow AI 发现 + MCP 安全网关——两个被低估的杀手功能

AGT 的 Additional Capabilities 列表里有两个功能我觉得被严重低估了。

第一个是 **Shadow AI Discovery**。它的描述是：“Find unregistered agents across processes, configs, and repos.” 翻译成人话：扫描你的整个代码仓库、配置文件、运行进程，找出那些没有被注册到治理系统的 Agent。

这解决的是一个非常现实的运维问题——在一个有 200 个微服务的组织里，你怎么知道哪个团队偷偷部署了一个 LangChain Agent 调了生产数据库？Shadow AI Discovery 的 spec 定义了发现机制和风险评分，它会在 CI 管道里扫描所有 `agent.json`、`langchain.yaml`、`crewai.toml` 等 Agent 配置文件，标记出未注册的 Agent 并给风险打分。

第二个是 **MCP Security Gateway**。MCP（Model Context Protocol）是 Anthropic 提出的 Agent-工具通信协议，正在被越来越多工具厂商用。但 MCP 本身没有定义安全层——一个 Agent 连接到一个 MCP server 后，它可以调用服务器暴露的所有工具。AGT 的 MCP Security Gateway 在 MCP 协议层增加了工具投毒检测（tool poisoning detection）、工具定义漂移监控（drift monitoring）、拼写欺骗检测（typosquatting）、隐藏指令扫描（hidden instruction scanning）。127 个一致性测试专门验证这个网关的行为。

（拼写欺骗检测这个功能让我想起 npm 生态的 typosquatting 攻击——攻击者发布一个叫 `requsts` 的包，拼写和 `requests` 差一个字母，Agent 如果被诱导调用了这个恶意 MCP server，后果不堪设想。）



![MCP Security Gateway 在 Agent 和 MCP Server 之间的拦截层，展示四个检测维度：投毒、漂移、拼写欺骗、隐藏指令](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-26/microsoft-agent-governance-toolkit-article/schematic-5.png)



---

## 七、25 个 ADR 和 60+ 教程——开源治理的诚意

一个开源项目的真正诚意，往往不在功能列表里，而在那些“为什么这么做”的文档里。AGT 仓库里有 25 个 Architecture Decision Records，记录了每一个重大架构决策的上下文、备选方案和取舍理由。60 多个教程覆盖了从 Quick Start 到生产部署的完整路径。社区治理文件一应俱全：GOVERNANCE.md（决策机制和贡献者晋升阶梯）、CHARTER.md（LF Projects 格式的技术章程）、MAINTAINERS.md（维护者名单）、SECURITY.md（漏洞报告和响应 SLA）、CODE_OF_CONDUCT.md、甚至还有 ANTITRUST.md（竞争法指南）和 TRADEMARKS.md（商标使用政策）。

一个 ANTITRUST.md 文件出现在 AI 治理工具仓库里，这件事本身就有点黑色幽默——一群做 AI 治理的人，连社区协作中的反垄断合规都考虑到了。这要么是微软法务部门的肌肉记忆，要么是这个项目从一开始就打算走 Linux Foundation 的治理路线。

AGT 的 LICENSE 是 MIT，这给了商业公司最大的灵活性。但 README 里有一条重要提示：“If you use the Agent Governance Toolkit to build applications that operate with third-party agent frameworks or services, you do so at your own risk.” 这条免责声明意味着微软不会为 AGT 治理下的 Agent 行为承担任何责任——你可以用 AGT 给 Agent 戴镣铐，但如果镣铐断了，那是你的事。

---

## 八、真正的问题不是“有没有治理工具”，而是“谁会真的用”

AGT 解决了一个技术问题：如何用确定性代码给概率性系统加护栏。但它没有解决一个组织问题：谁会在 Agent 上线前花时间写 YAML 策略文件？

答案可能是：那些已经被 Agent 事故伤过的人。当你的客服 Agent 在凌晨三点给客户发了一封“您的订单已被删除”的邮件，而实际上它只是误解了一个模糊指令——你会开始认真对待 governance。当审计师问你“你能证明这个 Agent 的每一次数据库操作都是被授权的吗”，而你只能给出 prompt 日志——你会开始认真对待 audit trail。

**技术行业的一个残酷规律是：安全工具只有在事故发生后才会被认真对待。**

AGT 的价值不在于它能不能防止第一起事故——它可能不能，因为没人在事故发生前会装它。它的价值在于，当第一起 Agent 事故发生后，CTO 问“我们怎么防止这种事再次发生”，工程 VP 可以回答：“两行代码，明天上线。”

**远水解不了近渴。但至少有人开始打井了。**

AGT 是微软在 AI Agent 治理领域打下的第一口深井。10 个 formal spec、992 个 conformance test、5 种语言 SDK、11 个框架适配器、4 重合规映射——这些数字说明这不是一次“我们也开源个东西”的公关行为。这是对“Agent 如何在生产环境安全运行”这个问题的系统性工程回答。

而它的核心理念——不在 prompt 里和随机性搏斗，在应用层用确定性代码拦截——可能会成为未来两年 AI Agent 安全架构的主流范式。

至于那些还没装任何治理框架就把 Agent 推上生产环境的团队——我只能说，祝你们的 on-call 工程师睡眠充足。

---

## 参考来源

- https://github.com/microsoft/agent-governance-toolkit
- https://owasp.org/www-project-top-10-for-large-language-model-applications/ (OWASP LLM01:2025 Prompt Injection)
- https://jailbreakbench.github.io/ (Chao et al., NeurIPS 2024)
- https://arxiv.org/abs/2404.02151 (Andriushchenko et al., 2024)
- https://www.microsoft.com/en-us/security/blog/2024/06/26/lessons-from-red-teaming-100-generative-ai-products/ (Microsoft AI Red Teaming: Lessons from Red Teaming 100 Generative AI Products)

#AI #Agent #Governance #Toolkit #Policy
