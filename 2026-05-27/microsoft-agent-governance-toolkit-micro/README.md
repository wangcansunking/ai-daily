# 微软 AGT 发布：一键为 AI Agent 套上确定性“紧箍咒”

> ai-daily · 2026 年 5 月 27 日 08:47 · 来源：GitHub Trending python

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/microsoft-agent-governance-toolkit-micro/head.png)

凌晨三点，旧金山某写字楼的监控室。运维工程师盯着屏幕上跳出的告警——一个客服 Agent 刚刚调用了 `drop_table` 指令，理由极其荒诞："用户说想清空购物车，我理解成清空数据库了。"

他猛灌一口咖啡，冷汗浸透衬衫。这个 Agent 已经在生产环境跑了三个月，用了所有安全审查，OAuth 权限设置得滴水不漏，IAM 角色配置也无可挑剔。但问题就出在这里——**OAuth 只管你能不能进门，不管你在屋里干什么。**

![microsoft/agent-governance-toolkit — AI Agent Governance Toolkit — Policy enforc](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/microsoft-agent-governance-toolkit-micro/content-1.png)

2026 年 5 月 7 日，微软在 GitHub 开源了一个让所有 Agent 开发者可以睡个好觉的工具箱：**Agent Governance Toolkit（AGT）**。一个 `pip install`，一套 YAML 策略，就能把 Agent 从"嘴上说说不做坏事"变成"结构上根本做不了坏事"。

**提示词安全不是控制面。它是一个对随机系统的礼貌请求。**

## 提示词防线已死，确定性拦截才是解药

微软这次的文档语气罕见地硬。他们没有绕弯子，直接引用了 OWASP LLM01:2025 标准里的原话："目前尚不清楚是否存在万无一失的提示注入防御方法。"然后把学术界的数据砸在桌面上。

JailbreakBench（Chao 等人，NeurIPS 2024）基准测试显示，自适应攻击对前沿安全对齐模型的攻击成功率接近 100%。Andriushchenko 等人在 2024 年的论文里，用纯提示词攻击在 GPT-4、GPT-3.5、Claude 3 和 Llama-3 上打出了 100% 的 ASR（Attack Success Rate，攻击成功率）。即便是已发布的最强提示层防御方案，残余 ASR 仍高达两位数百分比。

微软自家的《AI Red Teaming Agent》把 ASR 定义为对抗性输入下策略违规率的规范指标，而他们红队测试了 100 个生成式 AI 产品后的结论是——"AI 红队测试永远不会完成"，因为模型层的防御在构造上就是概率性的。

AGT 做了一件反直觉的事：**它不跟模型在提示词层面打仗。** 每一次工具调用、消息发送、任务委派，都在模型意图到达网络之前被应用层的确定性代码拦截。AGT 内核拒绝的操作不是"不太可能发生"，而是"结构上不可能发生"。这就是"请 Agent 守规矩"和"让 Agent 没法不守规矩"之间的区别。



![AGT 拦截流程架构图，Agent 意图 → Policy Engine → 确定性拦截/放行 → 审计日志，对比传统提示词防御的虚线边界](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/microsoft-agent-governance-toolkit-micro/schematic-1.png)



具体怎么做到的？两行代码。

```python
from agentmesh.governance import govern
safe_tool = govern(my_tool, policy="policy.yaml")
```

然后写一份 YAML 策略文件，三秒钟就能定义出"不允许 drop、delete、truncate"的规则。更狠的是 `require_approval` 机制——发邮件这种高危操作可以强制走人工审批，审批人直接指定 security-team。这意味着你可以在策略里写："Agent 可以读数据库、可以查 API、可以写报告，但凡是涉及删除动作，要么直接禁止，要么必须真人签字。"

让我愣神的是他们支持的 SDK 语言数量：Python、TypeScript、.NET、Rust、Go，五门语言全覆盖。TypeScript 版直接 `npm install @microsoft/agent-governance-sdk`，.NET 版可以一行代码把治理能力挂到 MCP Server 上，Go 版甚至支持通配符策略（`"*": Deny`）。Copilot CLI 和 Claude Code 这两个第一方开发者界面，都是基于 TypeScript SDK 构建的。



![五种语言 SDK 对比表，Python 全栈 / TypeScript 中间件 / .NET MCP 集成 / Rust 客户端 / Go 通配符策略](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/microsoft-agent-governance-toolkit-micro/schematic-2.png)



## 从身份到沙箱，Agent 治理的九层铠甲

AGT 的架构分得极细。官方拆出了九个独立包：Agent OS（策略引擎和治理网关）、Agent Mesh（零信任身份和信任评分）、Agent Runtime（四个特权环的执行沙箱）、Agent SRE（熔断、混沌测试、SLO 监控）、Agent Compliance（OWASP 验证和策略 linting）、Agent Marketplace（插件信任评分）、Agent Lightning（RL 训练的违规惩罚治理）、Agent Hypervisor（执行审计和增量引擎）、以及 Agent Discovery（影子 AI 发现）。

其中最让我觉得有意思的是 **Shadow AI Discovery** 这个能力。它能扫描进程、配置文件、代码仓库，找出那些没有被注册在案的"野 Agent"——那些工程师私下部署、运维完全不知道、但已经在调用生产数据库的幽灵进程。文档里没写具体怎么扫，但提到了发现后的风险评分机制，这意味着它不只是列清单，而是给每个影子 Agent 打一个危险系数。



![九层治理架构图，从 Agent OS 内核到 Agent Lightning RL 训练治理，每一层标注核心能力](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/microsoft-agent-governance-toolkit-micro/schematic-3.png)



OWASP Agentic Top 10 的十个风险类别，AGT 全部覆盖。这十个类别涵盖了 Agent 特有的攻击面——提示注入、工具滥用、权限越界、委派链攻击等等。微软不仅做了覆盖，还直接对标了 NIST AI RMF 1.0 的四个维度（GOVERN、MAP、MEASURE、MANAGE）、EU AI Act 的合规映射，以及 SOC 2 的控制映射。这意味着你用 AGT 管 Agent，审计的时候可以直接导出证据链，而不是跟审计员解释"我们的提示词写得很好"。

CLI 工具包里藏了一个让我笑出声的命令：`agt red-team scan ./prompts/ --min-grade B`。它会对你的提示词做注入攻击审计，然后打分，低于 B 级直接挂掉 CI。这是一个把安全左移到开发阶段的硬核操作——你连 PR 都合不进去，别说上线了。

但微软也在安全边界上说了实话。文档里明确写："AGT 在应用中间件层执行治理，策略引擎和 Agent 共享同一个进程边界。"他们建议生产环境把每个 Agent 放在独立容器里做 OS 级隔离，并专门列了一页 Known Limitations 文档，坦白设计边界和推荐的纵深防御策略。这种坦诚在科技公司的开源项目里不多见——大部分人恨不得把自己的工具吹成银弹，微软却在 README 里写"我们不是在 OS 内核层做隔离，你自己加容器"。

992 个一致性测试、25 个架构决策记录（ADR）、10 份 RFC 2119 格式的正式规范，定义了"必须""应该""可以"的行为契约。这些数字透露出一个信号：**这不是一个实验性玩具，这是微软内部已经跑过生产环境的治理基础设施。**

有意思的是，项目本身用了 MIT 协议开源，但治理结构极其正式——有技术章程（LF Projects 格式）、维护者名单、贡献者阶梯、反垄断指南，甚至商标使用政策。一个 GitHub 仓库搞得像个小基金会，说明微软预期这个项目会走出自己的生态，而不是永远挂在微软名下。

凌晨的运维工程师如果拿到这个工具箱，他会在策略文件里加一行 `condition: "action.type in ['drop', 'delete', 'truncate']"`，然后 `action: deny`。下次那个客服 Agent 再想"清空数据库"，它连指令都发不出去，只会收到一个 `GovernanceDenied` 异常，审计日志里留下一行不可篡改的记录：谁、什么时间、试图做什么、被哪条规则拦截。

他大概终于能睡个好觉了。除非——他忘了扫影子 Agent。

## 参考来源
- https://github.com/microsoft/agent-governance-toolkit
- JailbreakBench (Chao et al., NeurIPS 2024) — 原文引用
- Andriushchenko et al., 2024 — 100% ASR 数据，原文引用
- Microsoft AI Red Teaming Agent — 原文引用
- Lessons from Red Teaming 100 Generative AI Products — 原文引用

#AI #Agent #Governance #Toolkit #Policy
