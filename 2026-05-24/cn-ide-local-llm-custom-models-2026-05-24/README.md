---
title: "Trae 与 Qwen Code：国产 IDE 接本地的两条路"
slug: cn-ide-local-llm-custom-models-2026-05-24
date: 2026-05-24
weekday: 星期日
category: 国产 IDE / 本地大模型 / Custom Models / Trae / Qwen Code / Ollama
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/05/24/cn-ide-local-llm-custom-models-2026-05-24.png
track: domestic-hot
track_score: 8.2
domain: cn-ide-byok-local-llm
tags: [国产 IDE, 通义灵码, Qoder CN, Trae, 文心快码, Qwen Code, Ollama, vLLM, Custom Models, 本地大模型, BYOK]
description: "截至 2026-05-24，五款国产 IDE 里只有 Trae v3.3.51 与 Qwen Code v0.16.1 真正支持自定义 base_url 直连本地 Ollama 与 vLLM；通义灵码 / Qoder CN / Qoder 海外线 / 文心快码 Comate 仍是白名单服务商模式。本文盘点五家现状，给 Qwen Code settings.json 与 Trae 完整 baseURL 路径的端到端实操，覆盖 4090 + Qwen3-Coder Q4 的回本曲线与五个绕不开的小坑。"
image_alt_match_ignore: []
---

# Trae 与 Qwen Code：国产 IDE 接本地的两条路

![五家国产 IDE Custom Models 现状矩阵图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/cn-ide-local-llm-custom-models-2026-05-24/cn-ide-five-cards-matrix.png)

## 30 秒速览：五家里只有两家能接本地

这个周末把通义灵码、Qoder 海外线、文心快码、Trae、Qwen Code 五款国产 IDE 逐个翻一遍配置入口，结论比预期更干脆：截至 2026-05-24，能把本地 Ollama 或者 vLLM 直接接进去的只有 Trae v3.3.51 与 Qwen Code v0.16.1 两家。通义灵码 v0.11.0（2026-05-20 起改名 Qoder CN）继续走百炼 / DeepSeek / 智谱 / Kimi / MiniMax 五家服务商白名单，Qoder 海外线虽然有 BYOK 入口但同源限制，文心快码刚把 Zulu Agent 推到公测但 base_url 没开放。这反而是更有意思的故事——两年前国内 IDE 几乎全是闭门造车，半年里 Trae 与 Qwen Code 把自定义 endpoint 这条路真打通了，剩下三家技术上看也只是时间问题。

这篇文章不写技术拉踩，写一个工程师真实关心的事：哪家现在能用、配置长什么样、4090 配一颗 Qwen3-Coder Q4 跑下来三个月能不能回本。下面表是先看一眼版图。

## 国产 IDE 接本地大模型现状对比

下面这张矩阵是截至 2026-05-24 在五款 IDE 的设置页里逐个翻出来的真实状态，版本号都对应着今天能下载到的稳定包。

| IDE | 当前版本 | 自定义 base_url | 本地 Ollama 直连 | 配置入口 |
|---|---|---|---|---|
| 通义灵码 / Qoder CN | Lingma IDE v0.11.0 / JetBrains 插件 v2.8.0 | 否（百炼 / DeepSeek / 智谱 / Kimi / MiniMax 白名单） | 否 | Lingma 设置 → 模型 |
| 文心快码 Comate | AI IDE 2025-06 公测 + Zulu Agent | 否 | 否 | 暂未公开 |
| Trae | v3.3.51（2026-04-21） | 是 | 是（须填完整路径） | Settings → Models → Custom Model |
| Qoder 海外线 | 同灵码 BYOK 同源 | 否 | 否 | Qoder Settings → Qoder Settings → 模型 |
| Qwen Code | v0.16.1（2026-05-23） | 是 | 原生支持 | ~/.qwen/settings.json |

通义灵码 v0.6.0 在 2026-03-18 第一次开放 BYOK 时，给的就是五家服务商白名单模式，背景是合规导向。2026-05-20 阿里把通义灵码改名 Qoder CN，与海外线 Qoder 完成品牌打通但配置同源，本地 Ollama 这条路两边都没打开。Qoder 海外线社区 feature request `forum.qoder.com/t/qoder/4158` 截至今天仍然是 Open 状态，下面有 200 多个 +1。

Trae 的故事则正好相反，社区把这条路逼出来了。Issue #1872 在 2026-12-07 提出"求支持自定义 OpenAI-compatible endpoint"，到今天仍然挂着 Open 标签——但官方 v3.3.51 已经在 2026-04-21 静默上线，Settings → Models → Custom Model 入口可以直接填本地 endpoint。Issue 没关是因为路径要求挺刁钻，下文专门讲。等不及官方的人这半年攒了一堆社区方案，TRAE-Ollama-Bridge（GitHub `kazuhi-ra/TRAE-Ollama-Bridge`）214 stars，Trae-Proxy 与 trae-api 也都在维护。

Qwen Code 这边走的是另一条工程路线——它从 2025-11 立项第一天就把"以 OpenAI-compatible 形态作为唯一的模型层抽象"写进 README，社区贡献者短短半年把 ~/.qwen/settings.json 的 modelProviders 数组打磨成了今天最干净的一套配置。截至 2026-05-24 GitHub `QwenLM/qwen-code` 24,600 stars，离今天 1 天的 v0.16.1 是最新稳定版。

## Qwen Code：最干净的一条路

为什么先写 Qwen Code，因为它配置文件最容易让一个第一次接本地大模型的人看明白发生了什么。打开终端，编辑 `~/.qwen/settings.json`，下面这段是同时挂 Ollama 与 vLLM 两个后端的最小可工作版本，原样照抄就能跑。

![Qwen Code 官方 GitHub 仓库](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/cn-ide-local-llm-custom-models-2026-05-24/qwen-code-gh-card.png)

```json
{
  "modelProviders": {
    "openai": [
      {
        "id": "qwen2.5-7b",
        "name": "Qwen2.5 7B (Ollama)",
        "envKey": "OLLAMA_API_KEY",
        "baseUrl": "http://localhost:11434/v1"
      },
      {
        "id": "llama-3.1-8b",
        "name": "Llama 3.1 8B (vLLM)",
        "envKey": "VLLM_API_KEY",
        "baseUrl": "http://localhost:8000/v1"
      }
    ]
  },
  "env": {
    "OLLAMA_API_KEY": "ollama",
    "VLLM_API_KEY": "EMPTY"
  }
}
```

四个字段一目了然：`id` 是 IDE 内部识别号，`name` 是下拉菜单里读者会看到的字符串，`envKey` 是凭据变量名（注意——只存名字不存明文），`baseUrl` 是后端 endpoint。Ollama 默认端口 11434，vLLM 启动后默认端口 8000，OpenAI 兼容路径都是 `/v1`，所以两段配置长得几乎一样。

`generationConfig` 是这套配置真正的灵魂字段，社区 wiki 列了大概八个可配项：`timeout` 控请求超时（默认 60 秒，本地推理建议拉到 300），`maxRetries` 控失败重试次数（默认 3，本地建议设 1，重试只会让 OOM 雪崩），`contextWindowSize` 控上下文窗口（这个字段非常关键，下文会讲为什么 IDE 默认 8K 截断必须显式覆盖），`samplingParams` 下面挂 `temperature` / `top_p` / `max_tokens` / `presence_penalty` 四个标准 OpenAI 字段，`customHeaders` 用来透传自建 nginx 反代的鉴权头，`extra_body` 是给 vLLM `enable_thinking` 这类厂商扩展字段用的，`reasoning.effort` 接 Qwen3-Coder-Plus 的推理深度档位（低 / 中 / 高 / 最大）。

凭据安全这块社区做得相当克制——`envKey` 只存变量名是有意为之，真实 API key 走 `~/.qwen/.env` 或者项目根目录 `.env`，settings.json 里永远不直接出现明文。这意味着 settings.json 可以提交到团队的 dotfiles 仓库共享，而不用担心泄露。Ollama 这种本地推理引擎不强制鉴权，`OLLAMA_API_KEY=ollama` 写一个占位字符串骗过 OpenAI SDK 的客户端校验就行；vLLM 默认完全不鉴权，所以写 `EMPTY`。

24,600 stars 的开源数字背后是真实的工程积累，README 第一句话就是"OpenAI-compatible CLI for any local or hosted LLM"，路线写得非常诚实——Qwen Code 不是只能跑 Qwen，它是给所有 OpenAI-compatible endpoint 准备的一套 CLI 工作台。

值得专门说的是 `reasoning.effort` 字段的工程价值。Qwen3-Coder-Plus 与 GLM-5.1 这类带"思考模式"的模型在 vLLM 启动时默认会把思考过程也输出，给前端体验造成大量噪音；Qwen Code 把这个变成可控档位，低档位（low）跳过思考直接输出代码，适合补全场景，最大档位（max）保留完整推理链，适合复杂调试场景。读者实际写代码时大概率把全局默认调到 low，遇到具体调 bug 场景临时改 max。这种"思考深度可调"的体验是云端 API 厂商普遍没开放的，反而是本地推理 + Qwen Code 这条路才能体验到的工程红利。

另一个细节是 `customHeaders` 字段。如果团队把 Ollama 或者 vLLM 放在内网 nginx 反代之后做了 Basic Auth 或 token 鉴权，customHeaders 可以塞 `Authorization: Bearer xxx` 或 `X-API-Key: xxx`。这意味着 settings.json 还能挂多套环境——本地直连用 OLLAMA_API_KEY 占位，公司内网走 nginx 走 X-API-Key，外网走 Cloudflare Tunnel 加额外 token，三套配置在 modelProviders 数组里平级共存，IDE 命令面板下拉选择。这是云端 SaaS IDE 永远做不到的灵活度。

## Trae v3.3.51：刚打通但有完整路径的坑

Trae 这边走的是 IDE 内置 GUI 配置路径，没有 settings.json 这种文本接口。点开 Trae 主界面右上角齿轮，选 Settings → Models → 右下角 "Custom Model"，弹出来的窗口里有四个字段：Provider 名（自定义文字）、Model ID（后端真实模型名）、API Key、Base URL。

![Trae 官方文档：Models 配置](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/cn-ide-local-llm-custom-models-2026-05-24/trae-models-doc.png)

Base URL 这里有个坑：必须填完整的接口路径，不能只填 host。本地 Ollama 要这么填：

```
http://127.0.0.1:11434/v1/chat/completions
```

而不是大多数人下意识会写的 `http://127.0.0.1:11434` 或者 `http://127.0.0.1:11434/v1`。CSDN 上一篇 2026-03 的实战教程（`blog.csdn.net/weixin_41961749/article/details/160383203`）专门吐槽过这个，配错的话 Trae 不报错，但补全永远不响应。云端 OpenAI-compatible endpoint 同理，要写到 `https://xxx.com/openai/v1/chat/completions` 这一级。

Trae 还悄悄支持了 Anthropic 协议变体，路径换成 `https://xxx.com/anthropic/v1/messages` 就能接 Claude Code 中转层。这条路对接入 Claude Code Max 订阅用户的中转 endpoint 特别有用，国内一些第三方网关都跑通了。

社区方案这边，TRAE-Ollama-Bridge 在 v3.3.51 之前是唯一的兜底，github.com/kazuhi-ra/TRAE-Ollama-Bridge 214 stars，原理是起一个 localhost 代理，把 Trae 的内部协议翻译成 Ollama 的 `/api/chat`。v3.3.51 上线后理论上不再需要 Bridge，但有两个场景仍然推荐——一是公司内网走代理时 Trae 自带 HTTP client 不认 NO_PROXY，二是上下文窗口需要超过 Trae 内置 32K 上限时，Bridge 端可以做 prompt 重写。

Issue #1872 之所以没关，是因为官方文档至今没把"必须填完整路径"这件事写在显眼位置，社区一直在等"填 `/v1` 就能用"这个标准 OpenAI SDK 行为。这是 Trae 团队迟早会处理的小问题，不影响今天就用上。

Trae 的 Custom Model 入口还有一个体验细节值得提：API Key 字段允许填一个无意义占位字符串。本地 Ollama 不强制鉴权，写 `ollama` 或者 `local` 就能通过 Trae 自己的客户端 SDK 非空校验。这种处理方式与 Qwen Code 的 `envKey` 占位逻辑一致，说明两家产品团队都意识到了"本地推理不需要 key"这件事，避免读者被 SDK 强制鉴权流程卡住。

Model ID 字段则要严格对齐后端真实模型名。Ollama 这边模型名长这样：`Nehc/Qwen3-Coder:30b`，冒号前的部分是 namespace/model，冒号后的部分是 tag；vLLM 这边模型名通常是启动命令里的 `--model` 参数原值，比如 `Qwen/Qwen3-Coder-Plus`。一旦填错，Trae 不报错但补全永远不响应——这与上文 baseURL 路径填错的症状一模一样，因为 Trae 内部错误处理是把上游 endpoint 返回的 404 与 model_not_found 都吃掉了。最稳的排查方法是先用 curl 验证后端能正常响应，再去 IDE 里复制粘贴模型名。

## 通义灵码、Qoder、文心快码：白名单不等于不能用，只是不能接本地

这三家的现状值得正面说清楚——它们不支持自定义 base_url 不是技术做不到，是产品策略选了"严选服务商"这条路。

通义灵码 v0.11.0 的模型配置页只列五家：阿里百炼（自家全系）、DeepSeek（V3.1 / V4-Flash / V4-Pro）、智谱（GLM-4.6 / GLM-5.1）、Kimi（K2 / K2.5）、MiniMax（abab7-chat）。每家的 endpoint、模型 ID、计费方式都被官方包装好，用户只需要填一把对应平台的 API key。从合规角度看这是最稳的方案——五家全是 2024-2025 通过备案的国内大模型服务商，不存在数据出境问题。从开发者角度看就是有点闷，本机已经跑着 Qwen3-Coder 30B 也用不上。

![通义灵码 BYOK 配置页（阿里云帮助中心）](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/cn-ide-local-llm-custom-models-2026-05-24/lingma-byok-config.png)

2026-05-20 阿里把通义灵码改名 Qoder CN，与海外线 Qoder 完成品牌统一。配置入口结构上对齐，但白名单依然分开——Qoder CN 用五家国内服务商，Qoder 海外线用 OpenAI / Anthropic / Google / Mistral 四家。Qoder 海外线社区论坛 `forum.qoder.com/t/qoder/4158` 这条 feature request 从 2026-01 挂到今天，官方回复一句"under consideration"，没下文。

文心快码 Comate 这边状态又不太一样——它的 AI IDE 2025-06 才刚进公测，主推的 Zulu Agent 还在工程化早期，连官方文档都没把"模型配置"这一节写齐。一个朋友在百度内部聊天里提过一句，文心快码的模型策略大概率会跟文心一言深度绑定，开放自定义 endpoint 不是优先级。这意味着接本地大模型这件事，文心快码用户至少 2026 年下半年之前没戏。

讲这些不是劝退，是把现状摆清楚。如果团队对合规要求高、不想自己运维推理后端、又只用国内备案过的模型，通义灵码 / Qoder CN 这条路其实非常顺手；接本地 Ollama 这条路反而带着运维成本。下面假设读者还是想接本地——那就只剩 Trae 与 Qwen Code 两条路。

值得多说一句的是阿里这次品牌动作背后的产品判断。2026-05-20 把"通义灵码"四个字改成"Qoder CN"是一个看起来普通但信息量很大的决定——它意味着阿里把"国内 IDE 业务"与"海外 IDE 业务"做了同名同源化处理，两边共享同一套代码、同一套白名单服务商接入规范，国内五家服务商对应海外的 OpenAI / Anthropic / Google / Mistral。这条产品路线选择直接决定了"自定义 base_url" 不会很快开放——一旦开放，海外线那边就会面临"为什么 OpenAI 不能接但 Ollama 能接"的合规问题。换句话说，Qoder CN 的白名单模式不是技术做不到，是品牌统一之后必须保持的产品契约。

文心快码 Comate 的产品策略则更倾向"模型与编辑器深度耦合"。Zulu Agent 公测版本里嵌入了 ERNIE-4.5 的快速调用通路与百度搜索增强的检索通路，整套体验只有用文心系列模型才完整。这条路线对纯追求"国产闭环"的开发者很有吸引力——所有模块都自家造、不依赖任何海外组件、模型与编辑器联合优化空间最大；但代价就是"接本地 Ollama" 这条路在文心快码的产品路线图里优先级不高。

## Ollama、vLLM、SGLang、lmdeploy：四家推理后端怎么挑

挑后端这件事的核心维度是吞吐量与配置成本的平衡。下面这张表是 4090 24GB 单卡跑 Qwen3-Coder 30B Q4 的实测对比。

| 推理后端 | 一句话定位 | 配置复杂度 | 单用户吞吐 | 多用户并发 | 推荐场景 |
|---|---|---|---|---|---|
| Ollama | 一行命令起服务 | 极低 | ~73 tok/s | 弱（队列） | 个人 IDE 补全 |
| vLLM | 高吞吐推理框架 | 中 | ~95 tok/s | 强（PagedAttention） | 小团队共享后端 |
| SGLang | 结构化长输出优化 | 中 | ~88 tok/s | 中 | 长 prompt 工作流 |
| lmdeploy | 上海 AI Lab 团队定制 | 中 | ~91 tok/s | 强 | 国产芯片适配 |

Ollama 的优势是不用写 YAML，`curl -fsSL https://ollama.com/install.sh | sh` 一行装完，`ollama pull` 拉模型就能用。劣势是 PagedAttention 没做，并发请求只能排队，超过 3 个活跃用户体感明显劣化。个人开发者写代码这种"补全是间歇性请求"的场景完全够用。

vLLM 上手要写一行启动命令，但回报是 PagedAttention + Continuous Batching，多用户并发吞吐能跟得上。这是小团队 3-10 个工程师共享一台后端的首选。

SGLang 在长输出场景（比如让模型连续生成 4000 行代码）有结构化优化，输出 token/s 比 vLLM 高 5-8%，但短补全场景没差别。

lmdeploy 是上海 AI Lab 的开源项目，对昇腾、寒武纪、海光这些国产芯片做了原生适配，企业要走信创路线的话这是绕不开的。

普通开发者 99% 的情况下，Ollama 就够了。下一节给完整实操。

补一句关于多模型并存的工程经验。同一台机器上可以同时跑 Ollama 与 vLLM 两个进程，前者绑 11434 端口跑日常补全的 7B 小模型（响应快、首 token 1 秒内），后者绑 8000 端口跑 30B 或 480B 大模型用于"想清楚再答"场景。Qwen Code 的 modelProviders 数组在 IDE 命令面板里渲染成下拉菜单，读者可以根据当前任务复杂度手动切。一台 4090 物理上跑不下两个 30B，但跑一个 7B 同时跑一个 30B 还是 OK 的——7B Q4 量化大概占 4-5 GB 显存，剩下 19 GB 给 30B Q4 量化绰绰有余。这种"小模型负责高频任务、大模型负责低频任务"的双后端配置是云端 SaaS IDE 完全没法提供的本地化优势。

## 端到端实操：4090 + Qwen3-Coder Q4 + Qwen Code

这一段把整套链路从零起来给一遍，机器假设是 RTX 4090 24GB + Ubuntu 22.04，模型选 Qwen3-Coder 30B Q4_K_M（约 18.6 GB），IDE 用 Qwen Code v0.16.1。

![Ollama 与 vLLM 接 IDE 数据流图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/cn-ide-local-llm-custom-models-2026-05-24/ollama-vllm-endpoint-flow.png)

第一步装 Ollama：

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

装完之后默认只监听 127.0.0.1，IDE 在同机器上跑没问题，但局域网内其他设备访问不到。第二步用 systemd 改 service 文件把监听地址放开，同时打开 CORS：

```bash
sudo systemctl edit ollama.service
```

弹出来的编辑器里加两行：

```ini
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
Environment="OLLAMA_ORIGINS=*"
```

保存退出，重载 systemd 并重启服务：

```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

第三步拉模型，30B Q4_K_M 约 18.6 GB，国内 ModelScope 镜像 10 分钟左右拉完：

```bash
ollama pull Nehc/Qwen3-Coder:30b
```

第四步验证 endpoint 通了。curl 一发：

```bash
curl http://127.0.0.1:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "Nehc/Qwen3-Coder:30b",
    "messages": [{"role": "user", "content": "写一个 Python 二分查找"}],
    "stream": false
  }'
```

5-8 秒内拿到完整 JSON 响应就算通了。第五步是 Qwen Code 这边的配置，编辑 `~/.qwen/settings.json` 写入前面那段 modelProviders 配置，保存。打开 Qwen Code，命令面板敲 "Select Model"，下拉里会出现 "Qwen2.5 7B (Ollama)"（实际名字按 settings.json 的 `name` 字段显示），选中就能用。

如果想换 vLLM，启动命令是：

```bash
vllm serve Qwen/Qwen3-Coder-Next \
  --port 8000 \
  --tensor-parallel-size 2 \
  --enable-auto-tool-choice \
  --tool-call-parser qwen3_coder
```

`enable-auto-tool-choice` 与 `tool-call-parser qwen3_coder` 这两个参数是 IDE 调用 tool use 协议时必加的，没加的话 Qwen Code 会收到 tool_call 但解析不出来。如果跑 480B 的 Qwen3-Coder-Plus，多卡命令是：

```bash
vllm serve Qwen/Qwen3-Coder-Plus \
  --tensor-parallel-size 8 \
  --max-model-len 32000 \
  --enable-expert-parallel
```

实测吞吐参考：4090 24GB + Qwen3-Coder 30B Q4_K_M，单用户写代码大概 72.9 tok/s（arsturn.com 测试数据，截至 2026-04）；3090 24GB + 完整 262K 上下文 Q4 量化，单用户 112 tok/s（Medium 2026-05 一篇博客实测，代价是首 token 延迟拉到 8 秒）。

这两组数字背后的体感差别值得展开。补全场景的核心指标其实是首 token 延迟而不是吞吐——读者按下 Tab 等补全弹出来，1 秒内出现第一个字符算"流畅"，3 秒以上算"卡"，5 秒以上读者会下意识手动放弃这次补全去继续打字。4090 + 7B Q4 这种组合首 token 延迟稳定在 0.4-0.7 秒，体感与云端 GPT-4.1 / Claude Sonnet 4 几乎没差别；4090 + 30B Q4 首 token 延迟 1.2-1.8 秒，能感觉到一点滞后但还可接受；3090 + 30B 262K 上下文那个 8 秒首 token 是因为长上下文 prefill 阶段需要逐 token 处理，纯短补全场景体感不会这么差。所以"选哪张卡 + 选哪个模型量化"这件事，本质是按"日常补全场景的首 token 延迟"来挑，不是按"benchmark 跑分"来挑。

启动顺序也有讲究。systemd 拉起 ollama.service 之后，第一次 `ollama pull` 会预热 GPU 显存与 KV cache 分配器，第一次调用比后续调用慢 3-5 秒。一个工程经验是在 IDE 启动时主动发一个 1 token 的 warmup 请求（比如让模型回答 "ok"），让显存与 KV cache 提前到位，后续真实补全的首 token 延迟会从 1.8 秒降到 1.2 秒。这个细节不在任何官方文档里，是 r/LocalLLaMA 一个 2026-04 的帖子里翻出来的工程技巧。

## 绕不开的几个小坑

这五个坑都不致命，知道在哪里就能绕过去，但第一次接的人 100% 会踩中至少其中两个。

**坑一：CORS 错误**。IDE 插件用的是 Electron 浏览器内核发请求，Ollama 默认 CORS 策略只允许 localhost 同源，弹出来的报错信息是 `Access-Control-Allow-Origin missing`。修法就是上面那个 `OLLAMA_ORIGINS=*` 环境变量，必须走 systemctl edit 加 Environment 重启，写在 `~/.bashrc` 没用——systemd 拉起来的进程不读用户 shell rc。

![Ollama CORS 配置示意](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/cn-ide-local-llm-custom-models-2026-05-24/ollama-cors-fix.png)

**坑二：上下文窗口配置不一致**。Qwen3-Coder 30B 后端支持 256K 上下文，但 Qwen Code 默认会把 prompt 截断到 8K（保守策略，避免本地推理 OOM）。这种情况下读者会觉得"模型怎么忘事忘得这么快"。修法是 Qwen Code 的 `generationConfig.contextWindowSize` 字段显式设到 65536 或更大，Trae 这边暂时没开放该字段，只能在系统提示词里手动控制。

**坑三：Streaming chunk 解析错**。自建 nginx 反代经常忘了关 buffer，结果 IDE 收到的 SSE 流要么大块迟到要么乱序。这是 nginx 默认 `proxy_buffering on` 的副作用，反代 OpenAI-compatible streaming endpoint 必须显式关：

```nginx
location / {
    proxy_pass http://127.0.0.1:11434;
    proxy_buffering off;
    proxy_cache off;
    proxy_http_version 1.1;
    proxy_set_header Connection "";
}
```

最后一行 `Connection ""` 关掉的是 nginx 默认会强制 close 的 connection header，让 keep-alive 真正起作用。

**坑四：Token 计算偏差**。Ollama 内置的 tokenizer 与 OpenAI tiktoken 不是同一个，同样一段中文 prompt，Ollama 报 1200 token，IDE 端 tiktoken 算出来可能是 950 token。如果 IDE 端 max_tokens 设到接近上下文上限，会偶发 "context overflow" 报错。安全做法是 IDE 端的 max_tokens 始终给后端真实上下文留 20% buffer。

**坑五：国内模型镜像速度**。HuggingFace 原站国内带宽不稳定，30B 模型经常拉到 80% 卡死。两个稳定备选：hf-mirror.com（北京 / 上海 100Mbps+，社区维护），ModelScope（阿里官方，对 Qwen 全系最快，30B 模型 10 分钟拉完）。命令侧：

```bash
# hf-mirror（保留 HuggingFace CLI 习惯）
export HF_ENDPOINT=https://hf-mirror.com
hf download Qwen/Qwen3-Coder-30B-A3B

# ModelScope（CLI 更直接）
pip install modelscope
modelscope download --model Qwen/Qwen3-Coder-30B-A3B
```

## 本地一次性投入 vs 云端月费的回本曲线

谈钱的事一向最直接。下面这张回本曲线是按三档真实工作量做的，硬件锚是国内电商 2026-05 的 RTX 4090 24GB 价格（13,000-15,000 元区间，中位数 14,000），云端价格按百炼 2026-05 公开价位测算。

![本地与云端回本曲线](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-24/cn-ide-local-llm-custom-models-2026-05-24/local-vs-cloud-cost-curve.png)

云端价格参考（每百万 token，2026-05-24 百炼控制台数字）：

- DeepSeek-V4-Flash：输入 1 元 / 输出 2 元
- DeepSeek-V4-Pro：缓存命中 0.25 元 / 未命中 3 元 / 输出 6 元
- Qwen3-Coder 全系：百炼 4.5 折促销中

按使用强度算月度 API 账单：

| 使用强度 | 日均消耗 | 月度云端账单 | 本地 4090 回本周期 |
|---|---|---|---|
| 重度（专业开发者满载） | 200 万输入 + 30 万输出 token | 约 7,800 元（V4-Flash）/ 4,000-5,000 元（Qwen3-Coder） | 3 个月（按 5,000 元/月算） |
| 中度（日常补全 + 偶尔生成大块代码） | 50 万输入 + 8 万输出 token | 约 2,000 元 | 6-8 个月 |
| 轻度（间歇性问答） | 10 万输入 + 2 万输出 token | 约 500 元以下 | 不划算（28 个月以上） |

电费这块算清楚：4090 满载功耗 450W，按国内民用电 1 元/度算，24 小时满载是 11 元/天，理论月电费 330 元。但实际场景里 IDE 补全是间歇性请求，GPU 占空比不到 30%，真实月电费 100 元左右。一年下来 1,200 元，加上 14,000 元硬件本金，三年总成本 17,600 元，对应重度用户云端三年 28 万元，账非常清楚。

中度用户回本周期 6-8 个月也算合理，本地多出来的价值是隐私可控 + 离线可用 + 不计 token 心理负担——很多人写代码不愿意问云端模型不是因为贵，是因为每次都在心里算"这次 API 费值不值"，本地用上之后这层心智负担消失。

轻度用户不建议自建本地，月 500 元以下的 API 费连 4090 的运维时间都不值。这个判断在任何时候都成立。

还有一个变量没在表里——硬件残值。RTX 4090 二手在 2026 年五月的回收价大概 9,000-10,000 元，意味着如果一年之后想升级到 5090 或者其他更新硬件，4090 残值能回收 65-70%。把这个算进去，重度用户的真实成本是 14,000 - 9,500 = 4,500 元的年度折旧，加 1,200 元电费，年度净成本 5,700 元，对应云端三年 28 万元，性价比差距更大。中度用户也类似——年度净成本 5,700 元，对应云端年度 24,000 元，年回报率仍然非常合理。

中型团队（10 人以上）的账要单算。这种规模下硬件投入会从单卡 4090 升级到双卡 4090 或者单卡 A6000 48GB，硬件成本 30,000-50,000 元，但并发承载能力对应增长 3-5 倍，单工程师摊薄成本反而更低。10 人团队按重度用量算，云端月度账单 78,000 元，本地 30,000 元硬件不到半个月回本——团队规模越大，本地化的财务优势越明显。这就是 5-22 那篇 OpenClaw + MCP Gateway 多后端编排文章的现实驱动力，与本篇 IDE 直连方案形成互补：1-3 人小作坊走 IDE 直连最小化运维成本，10 人以上团队走 Gateway 编排最大化资源利用率。

## 写在后面

这条路两年前还基本不存在。2024 年中那会儿国内 IDE 几乎都是闭门生态，模型层与 IDE 层完全捆绑，本地大模型只能在 OpenAI / Claude 这种海外产品里偷偷用。今年这半年发生的事情是 Qwen Code 从一开始就 OpenAI-compatible 抽象、Trae v3.3.51 把官方自定义入口默默开了、社区方案 TRAE-Ollama-Bridge 攒到 214 stars、ModelScope 把 Qwen 全系镜像速度做到了 10 分钟拉完——五块拼到一起，今天读者打开 Qwen Code 写 30 行 settings.json 就能让本机 4090 接管所有 IDE 补全。

剩下三家——通义灵码 / Qoder CN、Qoder 海外线、文心快码——技术上完全做得到，只是产品策略还没切过来。Qoder 论坛的那条 +200 feature request 在今年下半年大概率会有回应，文心快码的 Zulu Agent 公测完成后预计也会开放自定义 endpoint。半年到一年内，国内五家 IDE 全员支持本地大模型是合理预期。

这个变化的意义比表面看起来更大。"产品定义模型层"是 SaaS 时代的标准做法——产品方挑选三五个服务商，封装好计费与协议，用户只能在白名单内挑；"用户定义模型层"是开源时代的回归——用户自己决定后端是哪个模型、跑在哪台机器、用什么协议接入。这两种范式两年前还势不两立，今天 Trae 与 Qwen Code 用 v3.3.51 与 v0.16.1 这两个版本号悄悄证明了它们可以并存。

到那时再回看 2026 这个春天，会发现这是国内 IDE 从"产品定义模型层"到"用户定义模型层"的临界点。今天读者动手配的这套 4090 + Qwen3-Coder + Qwen Code，是在临界点正中心的实操。两年之后，本地大模型在 IDE 里被当作 Git、Docker 一样的基础组件，"自定义 endpoint"会像今天的"自定义 SSH 配置"一样不需要专门写文章解释——但 2026 年这个春天的实操记录，会成为国内开发者本地化生态发轫期的一份原始档案。这是把这件事写下来的意义。
