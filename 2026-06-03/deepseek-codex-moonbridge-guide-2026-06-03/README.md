---
title: "DeepSeek 接进 Codex 保姆级教程：国内直连不看额度脸色"
slug: deepseek-codex-moonbridge-guide-2026-06-03
date: 2026-06-03
weekday: 星期三
category: Codex / DeepSeek / 教程
cover: deepseek-codex-moonbridge-guide-2026-06-03.png
description: "Codex 好用但要 OpenAI 账号、额度紧；DeepSeek API 国内直连、按量付费、单价便宜。问题是 Codex 现在只走 Responses API，DeepSeek 是 Chat / Anthropic 兼容，直接接不通。这篇按 DeepSeek 官方文档，一步步用一层本地转发（Moon Bridge）把 DeepSeek 接进 Codex CLI——从装环境、配 config、生成 Codex 配置到 curl 验证，全程国内可直连，不用代理。"
---
# DeepSeek 接进 Codex 保姆级教程：国内直连不看额度脸色

![用一层本地转发把 DeepSeek 接进 OpenAI Codex CLI，国内直连、按量付费](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-03/deepseek-codex-moonbridge-guide-2026-06-03/deepseek-codex-moonbridge-guide-2026-06-03.png)

Codex 是 OpenAI 的编程智能体，好用，但有两道坎：要 OpenAI 账号、额度还总是不够花。而 DeepSeek 的 API 在国内可以直连、按量付费、单价便宜得多。把后者接到前者上，是很多国内开发者想干的事。

但有一个绕不过去的现实，先说在前面：

**Codex 现在只通过 OpenAI 的 Responses API 和模型通信，而 DeepSeek 提供的是 Chat Completions / Anthropic 兼容接口——两者对不上，没法直接把 DeepSeek 的地址填进 Codex 就用。** 官方给的解法不是改 Codex，而是在中间加一层本地转发：让 Codex 把 Responses 请求发给本地的转发程序，转发程序翻译成 DeepSeek 能听懂的格式，再把结果翻回来。DeepSeek 官方文档用的转发层是 Moon Bridge。这篇就按官方这条路，一步步把它走通。

先讲清楚为什么不能直接接——理解了这一层，后面每一步你都知道自己在干什么。

## 为什么不能直接填地址：协议对不上

![Codex 走 Responses API，DeepSeek 走 Chat / Anthropic 兼容接口，中间需要一层转发](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-03/deepseek-codex-moonbridge-guide-2026-06-03/source-deepseek-codex-doc.png)

*来源：DeepSeek 官方 awesome-deepseek-agent 仓库 接入 Codex 文档*

很多老教程教你直接在 Codex 配置里写一个 `model_providers`，把 `base_url` 填成 DeepSeek 的地址、`wire_api` 设成 `chat`。这套在早期能用，**但现在失效了**：Codex 已经把对话补全（Chat Completions）那条老路弃用，改走 OpenAI 自己的 Responses API；而 DeepSeek 只提供 Chat Completions 和 Anthropic 兼容两种接口。请求格式对不上，直接接就会报错。

所以正确的做法是加一层「翻译官」：

- **Codex** 这头：只会说 Responses API；
- **Moon Bridge**（本地转发层）：在中间收下 Responses 请求，翻译成 DeepSeek 能听懂的格式，发给 DeepSeek，再把回复翻回 Responses 格式；
- **DeepSeek** 那头：正常用它的 Anthropic 兼容接口收发。

整条路是：`Codex → 本地 Moon Bridge → DeepSeek API`。下面照着配就行。

## 第一步：装好环境

需要三样东西：Node.js、Go、Codex CLI。

- Node.js 18 及以上（从官网下载安装）；
- Go 1.25 及以上（Moon Bridge 用 Go 写，要它来跑）；
- 用 npm 全局装 Codex CLI：

```shell
npm install -g @openai/codex
```

装完验证一下两个版本号都能出来：

```shell
codex --version
go version
```

## 第二步：拿 DeepSeek API Key

去 DeepSeek 开放平台（platform.deepseek.com）的 API Keys 页面，新建一个 Key 并复制好。这个 Key 后面要填进转发层的配置里。

这一步顺带说一句**国内直连**的关键：DeepSeek 的 API 地址在国内可以直接访问，不需要任何代理。这正是这套方案对国内开发者最实在的地方——不用为了喂一个编程智能体去折腾网络。

## 第三步：配置 Moon Bridge（本地转发层）

![Moon Bridge（moonbridge）本地转发层仓库，把 Codex 的 Responses 请求翻译给 DeepSeek](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-03/deepseek-codex-moonbridge-guide-2026-06-03/source-moonbridge-repo.png)

*来源：Moon Bridge 开源仓库 github.com/ZhiYi-R/moon-bridge*

把 Moon Bridge 仓库克隆下来：

```shell
git clone https://github.com/ZhiYi-R/moon-bridge.git
cd moon-bridge
```

在目录里新建一个 `config.yml`，填入下面这份最小配置，把里面的 `sk-your-deepseek-api-key` 换成你刚拿到的 Key：

```yaml
mode: "Transform"

server:
  addr: "127.0.0.1:38440"

models:
  deepseek-v4-pro:
    context_window: 1000000
    max_output_tokens: 384000
    default_reasoning_level: "high"
    extensions:
      deepseek_v4:
        enabled: true
  deepseek-v4-flash:
    context_window: 1000000
    max_output_tokens: 384000
    default_reasoning_level: "high"
    extensions:
      deepseek_v4:
        enabled: true

providers:
  deepseek:
    base_url: "https://api.deepseek.com/anthropic"
    api_key: "sk-your-deepseek-api-key"
    offers:
      - model: deepseek-v4-pro
      - model: deepseek-v4-flash

routes:
  moonbridge:
    model: deepseek-v4-pro
    provider: deepseek

defaults:
  model: moonbridge
  max_tokens: 65536
```

这份配置做了几件事：把转发层监听地址设在本地 `127.0.0.1:38440`，挂上 DeepSeek V4 Pro 和 Flash 两个模型，默认路由走 Pro。要图片输入、联网搜索或多家模型路由，可以再参考 Moon Bridge 自带的 `config.example.yml` 扩展，这里先用最小可用版。

## 第四步：启动转发层

```shell
go run ./cmd/moonbridge --config config.yml
```

**这个终端要一直开着别关。** 启动后，Moon Bridge 在本地 `127.0.0.1:38440` 提供一个 Responses 兼容接口，地址是 `http://127.0.0.1:38440/v1/responses`——这正是 Codex 要去说话的地方。

## 第五步：生成 Codex 配置

另开一个终端，还在 moon-bridge 目录下。如果你之前已经配过 Codex，下面的命令会覆盖配置，**先备份一份**再生成。以 macOS / Linux 为例：

```shell
CODEX_HOME_DIR="${CODEX_HOME:-$HOME/.codex}"
mkdir -p "$CODEX_HOME_DIR"

# 先备份现有 config.toml
cp "$CODEX_HOME_DIR/config.toml" "$CODEX_HOME_DIR/config.toml.bak" 2>/dev/null || true

# 生成指向本地转发层的 Codex 配置
MODEL="$(go run ./cmd/moonbridge --config config.yml --print-codex-model)"
go run ./cmd/moonbridge \
  --config config.yml \
  --print-codex-config "$MODEL" \
  --codex-base-url "http://127.0.0.1:38440/v1" \
  --codex-home "$CODEX_HOME_DIR" \
  > "$CODEX_HOME_DIR/config.toml"
```

Windows PowerShell 用户用对应的写法：

```powershell
$CODEX_HOME_DIR = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { "$HOME\.codex" }
New-Item -ItemType Directory -Force -Path $CODEX_HOME_DIR | Out-Null
if (Test-Path "$CODEX_HOME_DIR\config.toml") {
  Copy-Item "$CODEX_HOME_DIR\config.toml" "$CODEX_HOME_DIR\config.toml.bak" -Force
}
$MODEL = go run ./cmd/moonbridge --config config.yml --print-codex-model
go run ./cmd/moonbridge `
  --config config.yml `
  --print-codex-config "$MODEL" `
  --codex-base-url "http://127.0.0.1:38440/v1" `
  --codex-home "$CODEX_HOME_DIR" `
  | Set-Content -Path "$CODEX_HOME_DIR\config.toml"
```

这一步会写出两个文件：`config.toml`（Codex 的服务商配置，关键是 `wire_api = "responses"`，让 Codex 走 Responses 这条路），以及 `models_catalog.json`（告诉 Codex 这个模型的上下文窗口、推理档位、工具支持等能力信息）。

## 第六步：启动 Codex，干活

![Codex 是 OpenAI 的编程智能体，配好转发层后即可由 DeepSeek 驱动](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-03/deepseek-codex-moonbridge-guide-2026-06-03/source-codex-repo.png)

*来源：OpenAI Codex 官方仓库 github.com/openai/codex*

进到你要写的项目目录，直接起 Codex：

```shell
cd /path/to/my-project
codex
```

此时 Codex 把请求发给本地 Moon Bridge，Moon Bridge 再路由到 DeepSeek V4。Codex 桌面 App 也能复用这同一份生成好的配置。

Moon Bridge 还提供了一键脚本，帮你一次把转发层、配置、Codex 全起好：

```shell
./scripts/start_codex_with_moonbridge.sh --project-directory /path/to/my-project
```

## 验证：两条命令确认通了

想确认链路真的通了，先看可用模型列表：

```shell
curl http://127.0.0.1:38440/v1/models
```

再发一条 Responses 测试请求，能正常回话就说明 DeepSeek 已经接上：

```shell
curl http://127.0.0.1:38440/v1/responses \
  -H "Content-Type: application/json" \
  -d '{
    "model": "moonbridge",
    "input": "请用一句话打个招呼。",
    "max_output_tokens": 1024
  }'
```

## 这套到底划不划算：国内直连 + 按量付费两笔账

走到这里，回到最开始的两个动机，把账算清楚：

- **国内直连**：DeepSeek 的 API 在国内可以直接访问，整套链路（Codex → 本地转发 → DeepSeek）全程不需要代理。转发层也只是跑在你自己机器上的本地程序，不绕外网。
- **额度自由**：DeepSeek API 是按量付费，用多少算多少，单价比官方自带额度便宜得多；不再受 Codex 自带额度的节奏限制，跑重活心里更有底。
- **代价是多一层维护**：你得让本地转发层一直开着，遇到 Codex 或 DeepSeek 接口升级，配置可能要跟着调。这是「用一层转发换协议兼容」必然的代价，不是这套方案的 bug。

一句话总结这条路：**它不是把地址一填就通的捷径，而是用一层本地转发，换来 Codex 这台好用的编程智能体在国内直连 DeepSeek、按量付费地跑起来。** 配置只是一次性的活，配好之后，你手里就多了一套「不看额度脸色」的编程智能体。开源工具把这些接法一条条做出来，我们这一代开发者，能选的组合只会越来越多——配好今晚这套，明天就能上手。
