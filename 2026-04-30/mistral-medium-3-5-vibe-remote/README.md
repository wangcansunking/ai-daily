---
title: "Claude Code 的开源对手来了：Mistral"
date: 2026-04-30
weekday: 星期四
tags: [大模型, AI Coding, agentic, 开源, Mistral]
slug: mistral-medium-3-5-vibe-remote
description: "Mistral 4 月 29 日同日发布 Medium 3.5 旗舰融合模型 + Vibe Remote Agents 异步云端编程代理 + Le Chat Work mode 三件事。Medium 3.5 是 128B Dense（不是 MoE）、256K 上下文、Vision encoder 从头训练、Modified MIT 许可（大企业有例外）。SWE-Bench Verified 77.6%、τ³-Telecom 91.4——官方只公布这两个 agentic 数字。API $1.5/$7.5 per M token，4 GPU 自托管即可。Vibe CLI 是 Python uv/pip 而非 npm 生态，subagent 用 TOML、skill 用 Markdown + YAML。文章覆盖三件事的硬数据、HN 顶赞讨论（YetAnotherNick / ksubedi / liuliu / UncleOxidant / simjnd / freakynit 等真实账号 verbatim 引语）、国内 OpenRouter + 支付宝实操路径与国产同档对位（DeepSeek V4 / 千问 Qwen 3.6 / MiniMax M2.5）。"
cover: mistral-medium-3-5-vibe-remote.png
---

# Claude Code 的开源对手来了：Mistral

![Mistral Medium 3.5 + Vibe Remote Agents 主视觉](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-30/mistral-medium-3-5-vibe-remote/mistral-medium-3-5-vibe-remote.png)

> 4 月 29 日，法国公司 Mistral AI 一口气发了三件事：旗舰融合模型 **Medium 3.5**（128B Dense、256K 上下文、Modified MIT 开源权重），云端异步编程代理 **Vibe Remote Agents**，以及 Le Chat 里专攻多工具复杂任务的 **Work mode**。SWE-Bench Verified 77.6%、τ³-Telecom 91.4——这是 Mistral 这次唯一公布的两个 agentic benchmark 数字。API 计费 $1.5/$7.5 per million tokens，自托管最少 4 GPU。

这三件事其实分别打三个不同战场。模型一档 Mistral 选了一条少有人走的路——**dense 而非 MoE**；coding agent 整体搬上云，和 Codex、Claude Code 不一样的是**长任务并行**默认开；Le Chat 加了 Work mode，一次串起邮件 / 日历 / Slack / Jira。

**国内开发者最快路径**：OpenRouter 注册账号 + 支付宝充值 + Vibe CLI 配 OpenRouter endpoint，半小时跑通。具体配置参数见 §七。

## 一、Medium 3.5 模型本身：把 instruct / reasoning / coding 合并成一套权重

先看 [Mistral 官方公告](https://mistral.ai/news/vibe-remote-agents-mistral-medium-3-5) 上的硬数据：

- **架构**：Dense 128B 参数（不是 MoE）
- **上下文长度**：256K tokens
- **多模态输入**：text + image → text 输出，vision encoder 从头训练以适配可变图像尺寸
- **推理力度可配**：`reasoning_effort` 字段支持 `none` / `high` 两档，按请求切换
- **多语言**：英 / 法 / 西 / 德 / 意 / 葡 / 荷 / 中 / 日 / 韩 / 阿
- **License**：Modified MIT，**对大营收企业有例外条款**

⚠️ 这里有一处和第三方报道的冲突。`thecodersblog` 写 Medium 3.5 的上下文是 128K，[官方 HuggingFace 模型卡](https://huggingface.co/mistralai/Mistral-Medium-3.5-128B) 写 256K——以官方为准。

"flagship merged model" 是 Mistral 自己的定位词。意思是这一条权重把指令遵循、推理、编程三种能力合并训出来，不再像 Codestral / Devstral / Magistral 系列拆成独立家族。这是这次发布的一个明显信号——Mistral 觉得把能力分家训练的 ROI 没有合并训练高。

EAGLE 推测解码版本 [`mistralai/Mistral-Medium-3.5-128B-EAGLE`](https://huggingface.co/mistralai/Mistral-Medium-3.5-128B-EAGLE) 同步发了。EAGLE 是用 draft 模型做 speculative decoding 的工程套路，能把推理吞吐提 2-3 倍，对自托管推理服务尤其值钱。

## 二、Benchmark：只公布两个数字这件事本身

Mistral 这次只公布了两个 agentic benchmark：

| Benchmark | Medium 3.5 | 含义 |
|---|---|---|
| SWE-Bench Verified | **77.6%** | 真实 GitHub PR 修复率，代理任务标杆 |
| τ³-Telecom | **91.4** | tau-bench 系列电信领域多轮工具调用基准 |

77.6 这条，把它和近期同档同代模型对比一下：

- DeepSeek V4 系列：80%+ 段位（[buildfastwithai 评测](https://www.buildfastwithai.com/blogs/deepseek-v4-pro-review-2026)）
- 千问 Qwen 3.6-27B：HN 用户 YetAnotherNick 直接对位 —— "It has similar SWE bench score to qwen 3.6 27b"（也就是 77 分附近）
- 千问 Qwen 3.6 35B-A3B MoE：HN 用户 ksubedi 评估"在所有指标上都更好且占用是这一档的零头"
- MiniMax M2.5：商业 API 路径，社区估算 80% 段位（具体数字以官方公告为准）
- Mistral Medium 3.5：77.6%（官方公布）

Medium 3.5 卡在和 Qwen 3.6-27B 同位，比 DeepSeek V4 系列略低。Mistral 自己在公告里只敢说"ahead of Devstral 2 and models like Qwen3.5 397B A17B"——选了一个上一代的对照基线。

只发两个数字这件事在 [HN 47949642](https://news.ycombinator.com/item?id=47949642) 直接被点出来了。HN 用户 **YetAnotherNick** 原话：

> "It has similar SWE bench score to qwen 3.6 27b[1]. No one is comparing it to frontier.
> [1]: There is no other common benchmark in the blog."

另一位用户 **ksubedi** 给得更狠：

> "Let's not forget Qwen 35B A3B MoE. It gets better performance than this in all the metrics for a fraction of the memory / compute footprint.
> Sad to see all the non Chinese open source models being at least one generation behind."

这条评论指向一个事实——2026 年这一轮开源模型迭代里，**中国团队（DeepSeek / Qwen / MiniMax）已经把性能/成本比拉到了西方同行不容易追上的水平**。MMLU / HumanEval / GPQA / 中文能力等常见数字 Mistral 这次都没发。

但反过来，**Medium 3.5 仍然有它的价值锚点**——HN 用户 **simjnd** 的客观评估：

> "I'm not sure what people are on in the comments. It doesn't beat the other models, but it sure competes despite its size."

> "GLM 5.1 is an excellent model, but even at Q4 you're looking at ~400GB. Kimi K2.5 is really good too, and at Q4 quantization you're looking at almost ~600GB. This model? You can run it at Q4 with 70GB of VRAM."

128B Dense 是一个比 DeepSeek V4 671B、Kimi K2 1T、千问 3.6 Max 都小的尺寸，加上 Modified MIT 开源权重，**自托管和私有化部署的门槛低出一档**。这是它的竞争生态位。

## 三、价格：$1.5 进、$7.5 出，4 GPU 起跑

| 维度 | 数字 |
|---|---|
| API input | **$1.5 / M tokens** |
| API output | **$7.5 / M tokens** |
| Le Chat Pro 月费 | 以 [mistral.ai/pricing](https://mistral.ai/pricing) 实时定价为准 |
| Self-host 最低配置 | 4 GPU（官方未指定型号；Docker 镜像 tags 包含 H100 / H200 / B200 / B300） |
| HuggingFace 权重 | fp8 原生约 128GB（128B × 1 byte），社区 Q4 量化版约 70GB（HN simjnd 实测）；[Ollama mistral-medium-3.5](https://ollama.com/library/mistral-medium-3.5) 默认拉取 80GB |
| vLLM 推荐 | `--tensor-parallel-size 8`（HF 模型卡） |

$1.5 / $7.5 这一档放进 2026-04 的开源旗舰价格带：

- DeepSeek V4 输出：人民币 24 元 / M（折合约 $3.3 / M）
- Qwen3.5 Max：阿里云 list price 输出约 $1.6 / M
- Mistral Medium 3.5：$7.5 / M

**Mistral 的输出端不便宜**。但若把"4 GPU 即可自托管 + 权重开源"作为加分项算，对很多既想用大模型又怕数据外传的企业，它能落地的形态比 DeepSeek V4 671B 多一档。

实际本地推理速度，[HN 47949642](https://news.ycombinator.com/item?id=47949642) 上几位用户给了具体数字：

- HN 用户 **simjnd** 实测："This model? You can run it at Q4 with 70GB of VRAM."（70GB Q4 是 Mistral Medium 3.5 自身的量化版门槛）
- HN 用户 **UncleOxidant** 在 AMD Strix Halo 平台实测约 3 tokens/s——他指出 unified memory 机器有 VRAM 但带宽对 dense 模型不够
- HN 用户 **freakynit** 推算苹果 M3 Ultra 的理论上限："M3 ultra has 819GB/s unified memory bandwidth. That means theoretical max tokem rate is 819/128 =~ 6.39 t/s."（128B fp8 时的天花板）
- 8×H100 vLLM TP：吞吐百倍以上，但需要服务器级硬件

128B Dense 的 ceiling 摆在那里，它**不是设计给 MacBook 跑的**。MoE 那一档（DeepSeek V4 / Qwen 3.6 35B-A3B）在 Mac 上的性价比依然更高，这点要承认。

## 四、Vibe Remote Agents：把 coding agent 真正搬上云

![Vibe Remote Agents 工作流（来自 Mistral 官方公告）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-30/mistral-medium-3-5-vibe-remote/mistral-medium-3-5-vibe-cli.png)

Vibe 这条产品线的时间线：

- **2025 年末**：[mistralai/mistral-vibe](https://github.com/mistralai/mistral-vibe) 仓库首发 Vibe 1.0
- **2026 年早期**：[Vibe 2.0](https://mistral.ai/news/mistral-vibe-2-0)，基于 Devstral 2 的 terminal-native coding agent
- **2026-04-29**：Vibe 2.9.2 + Remote Agents（GitHub Releases），默认模型切到 Medium 3.5

Remote 在这次的具体含义，[官方公告](https://mistral.ai/news/vibe-remote-agents-mistral-medium-3-5) 写得很直接：

> "coding sessions can work through long tasks while you're away. Many can run in parallel. Each coding session runs in an isolated sandbox, including broad edits and installs."

把它翻译成国内开发者熟悉的话——**每个会话跑在云上的隔离 sandbox 里，并行运行，长任务自动跑完后开 GitHub PR + 通知用户**。这跟 Codex Cloud / Cursor Background Agents / Claude Code Cloud 的产品形态对齐，但 Mistral 的差异点在两处：

1. **本地 → 云的"teleport"**：Vibe CLI 的本地会话可以一键托管到云。官方原文：

   > "Ongoing local CLI sessions can be teleported up to the cloud when you want to leave them running, with session history, task state, and approvals carrying across."

2. **集成清单走的是企业向**：GitHub（PR / code）、Linear / Jira（issues）、Sentry（incidents）、Slack / Teams（通知）。比单纯接 GitHub PR 多了 incident / issue 这一层。

启动一个普通 Vibe 会话的命令本身很朴素：

```bash
vibe "Refactor the main function in cli/main.py to be more modular."
```

非交互模式：

```bash
vibe --prompt "Add unit tests for billing module" \
     --max-turns 30 \
     --max-price 2.0
```

续接最近会话用 `vibe --continue`，按 ID 续接用 `vibe --resume <session-id>`。

⚠️ "把本地会话 teleport 到云" 的具体 CLI flag（是 `--remote` 还是 `vibe spawn`？）官方公告页没有明文，需要读 [docs.mistral.ai/mistral-vibe/](https://docs.mistral.ai/mistral-vibe/introduction) 落实。第一周用的人可以自己测一下，文档可能也会在接下来几天补全。

## 五、subagent / skill 不是 YAML，也不是 npm

跟 Claude Code 一对比就能看出 Vibe 的工程口味：

| 维度 | Vibe | Claude Code |
|---|---|---|
| 安装方式 | `pip install mistral-vibe` 或 `uv tool install mistral-vibe` 或 `curl install.sh` | `npm install -g @anthropic-ai/claude-code` |
| Subagent 配置 | **TOML**，存于 `~/.vibe/agents/` | YAML frontmatter |
| Skill 定义 | **Markdown + YAML frontmatter**（文件 `SKILL.md`） | Markdown + YAML frontmatter（同名同结构） |
| Hooks（Pre/Post tool use） | ⚠️ 官方文档没明确提，用 `safety` + `enabled_tools` 替代 | 完整 lifecycle hooks |
| 中断后继续 | `--continue` / `--resume <id>` | `claude --resume` |

Vibe 是 Python 生态，这个细节很重要——**国内 npm 镜像不好用的开发者，pip 走清华 / 阿里源比 npm 走淘宝镜像更稳定**，相当于一种意外的国内友好。

一个最简 subagent TOML：

```toml
display_name = "Research"
description = "Read-only subagent for research tasks"
safety = "safe"
agent_type = "subagent"
enabled_tools = ["grep", "read_file"]
```

放进 `~/.vibe/agents/research.toml`，主 agent 就能 `delegate to research`。Skill 那一侧用熟悉的 Markdown：

```yaml
---
name: code-review
description: Perform automated code reviews
allowed-tools:
  - read_file
  - grep
  - ask_user_question
---

# Code Review Skill

When invoked, scan the diff in the current branch...
```

格式取舍上，Mistral 的判断是：**配置文件用 TOML（受 Rust / Cargo 影响），知识/流程用 Markdown**。比起 Claude Code 全部 YAML，Vibe 的工程口味更"程序员日常"一点。Hooks 缺位是它最明显的功能差距——如果你的工作流里依赖 PreToolUse 拦危险命令、PostToolUse 校验输出，Vibe 当前还做不了。

## 六、Le Chat Work mode：把多工具串成一次请求

![Le Chat Work mode 跨工具示意](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-30/mistral-medium-3-5-vibe-remote/mistral-medium-3-5-le-chat-work.png)

Le Chat 的 Work mode 是这次发布的第三件事。Mistral 官方原文：

> "Work mode is a powerful new agentic mode for complex tasks in Le Chat, powered by a new harness and Mistral Medium 3.5."

具体能做的事，按它的 demo 描述：

- 跨工具一次跑完——邮件 + 日历 + Slack 在一个 prompt 里完成
- 收件箱 triage + 草稿
- 在 Jira / Linear 自动开 issue
- 每个 tool call + 推理过程对用户可见
- 敏感动作（发消息 / 写文档 / 改数据）需要用户显式批准
- connectors 默认全开，不像普通 Chat 要手动选

入口仅限 Pro / Team / Enterprise 计划。免费用户暂时拿不到这个能力。

把 Vibe Remote Agents 和 Le Chat Work mode 放在一起看，Mistral 的 thesis 是：**模型权重一个家族（Medium 3.5），但形态分两条路出去——Vibe 服务程序员的 long-running 编码任务，Work mode 服务办公场景的跨工具工作流**。这两条路上的 connectors / tool list / 安全审批策略不重叠。

## 七、国内开发者的最快上手路径

**场景 A · 试用 / 评估期**

最便宜的方式是 OpenRouter 走 API：

1. 注册 [OpenRouter](https://openrouter.ai/)
2. 按 [知乎 OpenRouter 充值教程](https://zhuanlan.zhihu.com/p/1898753591528908109) 用支付宝充 10 美元
3. 在 OpenRouter 的 model 列表选 `mistralai/mistral-medium-3.5-128b`
4. 拿到 API key 后，在任何支持 OpenAI 协议的客户端（包括 Cursor / Cline / Aider）配上去
5. 不动 Mistral 官网账号，绕开海外信用卡 / 银联问题

**场景 B · 跑 Vibe CLI 完整体验**

```bash
# 国内 pip 走清华镜像
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple mistral-vibe

# 配置走 OpenRouter
mkdir -p ~/.vibe
cat > ~/.vibe/config.toml <<'EOF'
[provider]
api_base = "https://openrouter.ai/api/v1"
api_key = "your-openrouter-key"
model = "mistralai/mistral-medium-3.5-128b"
EOF

# 启动
vibe "Add unit tests for the billing module"
```

⚠️ Vibe CLI 的 OpenRouter 兼容性**官方 docs 没明确背书**，第一次跑不通就直接走 Mistral 官方 API + 海外信用卡，或换 Aider / Continue 这类成熟客户端。

**场景 C · 本地权重自托管**

- 单卡：80GB fp8 权重，最低需要 RTX 6000 Ada / H100 80GB / Mac Studio M3 Ultra 128GB
- 服务器：8×H100 80GB 用 vLLM `--tensor-parallel-size 8`
- HF 权重国内拉取走 [hf-mirror.com](https://hf-mirror.com/) 或 [ModelScope](https://www.modelscope.cn/) 镜像
- Ollama 一行：`ollama pull mistral-medium-3.5:128b`，80GB 拉不动就先试 Q4 社区量化版

**场景 D · 产品里集成**

Mistral La Plateforme + NVIDIA NIM 是企业向部署路径，但前者付款、合规、出海数据落地都要走海外流程。如果是国内业务，**优先方向应当是用 Medium 3.5 的开源权重在国内云（阿里云 GN8 / 腾讯云 HCC / 火山引擎 GA1）上自建推理服务**——这条路上数据不出境、计费走人民币、增值税发票合规。

## 八、与国产开源旗舰的位置

把 Medium 3.5 放回 2026-04 这一波模型迭代的位置图：

| 模型 | 参数 | 架构 | 开源 | SWE-Bench | API 输出价 | 自托管门槛 |
|---|---|---|---|---|---|---|
| Mistral Medium 3.5 | 128B | Dense | Modified MIT | 77.6% | $7.5 / M | 4 GPU |
| DeepSeek V4-Pro | 671B | MoE | Modified MIT | 80.6% | ~$3.3 / M | 16 GPU+ |
| DeepSeek V4-Flash | ~80B | MoE | Modified MIT | 79.0% | ~$1 / M | 单卡可跑 |
| Qwen 3.6-27B | 27B | Dense | Apache-2.0 | 77.2% | 阿里云 list ~$1.5 / M | 单卡 |
| Qwen 3.6 35B-A3B | 35B (3B 激活) | MoE | Apache-2.0 | 80%+ | 类似 | 单卡 |
| MiniMax M2.5 | 未公开 | 未公开 | 部分开源 | 80.2% | 商业 API | — |

**Medium 3.5 在这张表里的位置**：

- 性能跟 Qwen 3.6-27B 平分秋色，比 DeepSeek V4 / Qwen 35B-A3B 落后一档
- 部署门槛比 DeepSeek V4-Pro 低，比 Qwen 3.6-27B 高
- API 价格在国际市场属正常，但在国内开发者视角下输出价偏高
- 真正差异化的是 **Vibe Remote Agents 这条工程产品线**——国产模型目前还没有同等成熟的"模型 + CLI + Le Chat Work mode 三位一体"产品组合

ksubedi 那条 HN 评论的另一面，是中国模型团队的工程红利现在已经传导到全球开源排行榜的头部。Mistral 这次的 Medium 3.5 没有正面追上中国对手的性能曲线，但它把能跑的事情往应用层（Vibe Remote / Work mode）推得更远。这条产品逻辑，国产模型团队接下来很可能会跟。

## 九、值得做的几件事

如果你是国内 AI Coding 开发者，建议把今天的注意力分到这几个点：

1. **测一次 Medium 3.5 的中文能力**——Mistral 这次没公布中文 benchmark，社区的真实手感更可信。OpenRouter 上跑几道文档摘要 / 代码注释，对比 Qwen 3.6-27B 的输出
2. **把 Vibe 加进你的工具试用清单**——不是替换，是看看 TOML subagent + Markdown skill 的组合在实战里手感如何，这套形态比 Claude Code 的 YAML 全家桶更轻
3. **跟踪 OpenRouter / 国产云对 Medium 3.5 的接入进度**——通常海外旗舰开源后 1-3 周内国产云会跟进
4. **保持对 ACP / Vibe / Codex CLI 三种 agent 工程范式的横向比较**——同一个任务（修一个 PR）跑三个 agent，看谁的工具调用次数少、改动质量高、回滚方便，这种实测比官方 benchmark 更有说服力

中国 AI 工程师这一年的工作面其实很完整：自家模型已经能在 SWE-Bench 80% 段稳定输出，海外旗舰开源后能用 OpenRouter / 自托管两条路并行用上。Mistral Medium 3.5 不是来抢饭碗的，它是又一块能拼进开发者工具链的拼图。

看见拼图慢慢满，比看到一两个新闻头条更让人踏实。下一块拼图，可能就是国产团队下周要发的东西。
