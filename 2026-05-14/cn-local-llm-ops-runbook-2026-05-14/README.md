---
title: "本地大模型 7×24 给团队跑 OpenClaw runbook"
slug: cn-local-llm-ops-runbook-2026-05-14
date: 2026-05-14
weekday: 星期四
category: 本地大模型 / 团队私有化 / OpenClaw 集成 / 运维
cover: cn-local-llm-ops-runbook-2026-05-14.png
track: domestic-hot
domain: local-llm-ops
tags:
  - 本地大模型
  - 团队私有化
  - systemd
  - nginx
  - LiteLLM
  - OneAPI
  - 模型热切换
  - OpenClaw
  - vLLM
  - Ollama
  - RTX 3090
  - Mac Studio
description: "把 90 天本地大模型运维实战压成一份可直接抄的 runbook：systemd 单元 + nginx SSE 反代 + OneAPI 网关 + vLLM/Ollama 双后端 + 蓝绿切流 + 三档硬件 case + OpenClaw 共享后端端到端集成，给 5-15 人团队 7×24 跑稳本地 LLM。"
---

# 本地大模型 7×24 给团队跑 OpenClaw runbook

![本地大模型运维拓扑总览](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-14/cn-local-llm-ops-runbook-2026-05-14/cn-local-llm-ops-runbook-2026-05-14.png)

> 2026 年 5 月，本地大模型已经不是"能不能跑"的问题，而是"能不能 7×24 给团队稳定跑"的问题。这份 runbook 是过去 90 天咱们家小机房压出来的工程结论。

## 4 月底关掉云端 LLM 充值后那一周发生了什么

4 月 28 日，咱们家小机房做了一件后来回头看挺关键的事——把团队那张充了大半年的云端 LLM API 卡注销了。月账单 6,800 元一刀切到 0，财务那边乐开花，技术这边盯着监控屏整整熬了三个晚上。

这一周的事故序列原模原样：

- **D+1**：平静无事，全员把团队 OpenClaw 客户端切到 internal endpoint，调用体感正常。
- **D+2 凌晨四点**：OpenClaw 客户端集体报 502。Ollama 把第二个模型加载进来直接把 24G 显存撑爆，OOM killer 把整个服务进程干掉了。
- **D+3 深夜**：nginx 反代 SSE 流式响应在浏览器侧莫名截断，排查发现是 `proxy_buffering` 没关。
- **D+4 早上**：dashboard 12239 那张 Grafana 大盘整片 No Data，监控形同虚设。

这一周把"`ollama run` 一行命令就能跑"和"给 12 个工位 + 4 个 OpenClaw 客户端 + 2 个内网应用稳定供给"之间那条工程鸿沟，原原本本走了一遍。本月前面已经写过 4090 单卡能不能扛住团队、Qwen3-Coder 30B 私有化是不是值、1M 上下文怎么压榨、桌面客户端怎么选、OpenClaw 4 个典型 case 怎么落地这五个主题；本篇专门补齐工程层那块——长期运维。读完应该能拿走一份可直接抄的服务化栈：systemd 单元 + nginx SSE 反代 + 多用户网关 + 模型热切换 + 监控告警 + 三档硬件 case + OpenClaw 共享后端端到端集成。90 天压下来的稳态数字摆在前面：**月账单从 6,800 元降到 0；P99 TTFT 480ms 以内；OpenClaw 客户端调用体感和云端调用基本一致**。

| 关键栈 | 选型 | 备注 |
|---|---|---|
| 推理后端 | vLLM v0.20.2 主 + Ollama v0.23.3 备 | vLLM 跑生产并发，Ollama 跑长尾试验模型 |
| 反向代理 | nginx | SSE 流式 + basic auth + Host 重写 |
| 网关 | OneAPI / new-api（国内团队） | 多用户配额 + 兑换码 + 模型路由 |
| 监控 | Prometheus + DCGM Exporter + Grafana | 自建 dashboard，不照搬 12239 |
| 客户端 | OpenClaw + 内网应用 | 走网关共享后端 |
| 自愈 | systemd Restart=always + nginx upstream | 5xx 自动摘流 + 进程自动重启 |

后面 8 段就把这张表逐层拆开。

## 服务化第一层：systemd 把单实例从"能跑"压到"自愈"

`ollama run` 和 `vllm serve` 都是终端前台进程，团队场景里关掉 ssh 就死掉，没法做生产后端。第一层服务化的全部目的就是：**让进程脱离登录会话、崩溃自动重启、环境变量集中管，宿主机重启后服务能自己回来**。

Ollama 的 systemd override 配置如下，关键三个变量直接决定后续会不会半夜被 OOM 叫醒：

```ini
# /etc/systemd/system/ollama.service.d/override.conf
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
Environment="OLLAMA_NUM_PARALLEL=4"
Environment="OLLAMA_MAX_LOADED_MODELS=2"
Environment="OLLAMA_KEEP_ALIVE=24h"
Environment="OLLAMA_FLASH_ATTENTION=1"
Restart=always
RestartSec=3
```

`OLLAMA_NUM_PARALLEL=4` 控制单模型同时跑几个推理 slot；`OLLAMA_MAX_LOADED_MODELS=2` 是显存层最关键那条护栏——同时驻留两个模型，前提是两个加起来不能爆显存，否则 OOM；`OLLAMA_KEEP_ALIVE=24h` 让模型加载一次就常驻一天，团队场景里这条比默认 5 分钟卸载省去大量首 token 冷启动延迟。

vLLM 这边咱们家小机房直接走 docker-compose，因为 vLLM 的 CUDA 依赖在裸机上太容易和宿主机驱动打架：

```yaml
# docker-compose.yml — vLLM 单实例
services:
  vllm:
    image: vllm/vllm-openai:latest
    runtime: nvidia
    environment:
      - HF_TOKEN=${HF_TOKEN}
    ports:
      - "8000:8000"
    volumes:
      - ./hf-cache:/root/.cache/huggingface
    ipc: host
    command: >
      --model Qwen/Qwen3-32B
      --gpu-memory-utilization 0.90
      --max-model-len 8192
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      start_period: 120s
    deploy:
      resources:
        reservations:
          devices: [{driver: nvidia, count: all, capabilities: [gpu]}]
```

`--gpu-memory-utilization 0.90` 留 10% 给系统和 NCCL 通信缓冲，再激进往上推到 0.95 经常在长上下文请求那一刻爆显存；`--max-model-len 8192` 是显存预算的硬约束，开 32K 上下文会让 KV cache 预分配吃掉额外 8-10 GiB，单张 4090 跑 Qwen3-32B 直接装不下。

版本钉死这一条另写一句：SitePoint 那份《Ollama Production Setup Guide 2026》里有句话值得抄到墙上——"pinning ollama/ollama:0.18.0 instead of latest is the single cheapest reliability improvement you can make before your first 生产环境 deploy."把镜像 tag 写死，比上线后再去追 latest 引入的 breaking change 便宜十倍。咱们家小机房 90 天里 vLLM 跟过两次 minor、Ollama 跟过四次 patch，每次升级前都先在 canary 实例跑 24 小时压测才换主流量。

这一层做完之后，单实例就有了基本的自愈能力——进程崩溃 3 秒自动拉起，宿主机重启后 systemd 自动启服务，OOM 之后能恢复，但是显存预算依然是手动算出来的硬约束。

![systemd nginx 自愈数据流向 ops architecture flow diagram](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-14/cn-local-llm-ops-runbook-2026-05-14/cn-llm-ops-systemd-flow.png)

## 服务化第二层：nginx SSE 反代 + basic auth + Host 重写三件套

第二层要解决两件事：**对外只暴露一个 443 端口；SSE 流式响应不被反代缓冲打断**。

nginx 直接反代 Ollama / vLLM 的 OpenAI 兼容接口，三件套缺一不可：

```nginx
# nginx 反代 SSE 流式 + Host 头重写
server {
    listen 443 ssl http2;
    server_name llm.internal;
    auth_basic "LLM";
    auth_basic_user_file /etc/nginx/.htpasswd;

    location / {
        proxy_pass http://127.0.0.1:11434;
        proxy_http_version 1.1;
        proxy_set_header Host localhost:11434;
        proxy_set_header Connection '';
        proxy_buffering off;
        chunked_transfer_encoding off;
        proxy_read_timeout 3600s;
        proxy_send_timeout 3600s;
    }
}
```

`proxy_buffering off` 必须显式关，nginx 默认会先把响应缓冲到内存再下发，SSE 流式会被攒成大块一次性吐给客户端，OpenClaw 那边看上去就是"半天没反应突然刷一整段"；`proxy_set_header Host localhost:11434` 这条 Host 重写专治 Ollama，Ollama 会校验 Host 头，反代过去之后默认 Host 是 `llm.internal`，Ollama 直接拒绝返回 403，这个坑国内不少团队踩过；`proxy_read_timeout 3600s` 是长上下文场景里必要的——一个 30K token 推理可能要跑 2-3 分钟，默认 60s 超时会让连接断在半路。

暴露到公网这一步必须三选一：basic auth + HTTPS、Cloudflare Access 套零信任、Tailscale 拉到内网。Rost Glukhov 那篇《I Exposed Ollama to the Internet — Here's What Almost Broke It》里说得很直接——"未加 auth 的公网 Ollama 几小时内会被扫描出来，被人白嫖+爬模型清单。"咱们家小机房选 Tailscale，团队成员手机和电脑装一次客户端就能直接连内网 llm.internal，公网零暴露面，扫描根本扫不到。

basic auth 这条对 OpenClaw 客户端有个小坑：URL 里要带 `https://user:pass@llm.internal/v1`，OpenClaw 当前版本支持这种内联凭证，但是有些 OpenAI 兼容客户端会把 user:pass 那一截截掉，遇到要单独走 Authorization Header 那条路。

## 服务化第三层：多用户网关把 12 个工位的配额管起来

第二层之后所有调用都用同一个 API key，没法做配额、没法做模型路由、没法做兑换码。第三层要把这些都补上——这就是网关的活。

国内团队四个选型咱们家小机房都跑过 演示，对比矩阵如下（stars 数据 2026-05-13 实查）：

![LiteLLM OneAPI new-api FastGPT 网关 gateway matrix 选型矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-14/cn-local-llm-ops-runbook-2026-05-14/cn-llm-ops-gateway-matrix.png)

| 网关 | stars | 国内团队适用度 | 特点 | 坑 |
|---|---|---|---|---|
| LiteLLM | 46,850 | 中 | 100+ provider 适配、Python SDK 完整 | 主打海外多 provider，国内 only 本地后端时配置偏重 |
| OneAPI（songquanpeng/one-api） | 33,650 | 高 | 国内场景原生支持、兑换码 + 余额、UI 中文 | 维护节奏放缓，部分新模型适配慢 |
| new-api（Calcium-Ion/new-api） | 33,118 | 高 | OneAPI 的活跃 fork、Midjourney/Suno 支持 | fork 分支，文档对齐 OneAPI 但有差异 |
| FastGPT | 28,024 | 中 | 知识库 + 工作流编排 | 偏应用层，单纯做网关偏重 |

5-15 人团队咱们家选 OneAPI 或 new-api 二选一：本地 vLLM/Ollama 后端在它俩里直接选 "OpenAI 兼容渠道"，把 base_url 填 `http://nginx:443`，token 填 basic auth；上层给每个工位发一个 sk-xxx 的虚拟 key，配额按月发，跑超了自动停。new-api 比 OneAPI 多一个值得抄的功能——**渠道权重 + 自动重试**，主渠道 vLLM 挂了自动切到备渠道 Ollama，业务层无感。

LiteLLM 在国内团队场景里主要拿来做开发期 mock：本地起一个 LiteLLM proxy，把 vLLM 后端封成 OpenAI v1，开发同事用同一份代码切线上和本地，调试体验很丝滑。生产负载咱们家还是走 OneAPI/new-api。

这一层做完之后，团队 12 个工位各拿一个虚拟 key，财务能从网关 dashboard 直接看到每个工位月用量，OpenClaw 客户端在配置里只需要填 `base_url=https://llm.internal/v1` + 自己那张虚拟 key，所有访问控制就都收拢到网关上。

## 监控告警：自建 dashboard 比照搬 12239 靠谱十倍

监控这一段咱们家小机房踩过的最大坑就是 Grafana 那张著名的 dashboard ID **12239**。这张 NVIDIA DCGM Exporter 官方推荐大盘是 2021 年发布的，DCGM v3.3+ 之后指标名改过一轮（`DCGM_FI_DEV_GPU_UTIL` → `DCGM_FI_PROF_GR_ENGINE_ACTIVE` 这类替换），2026 年现行版导进去经常整片 No Data，新人很容易以为"我监控装好了"，其实图全是空的。

正确做法是抓 vLLM `/metrics` 和 DCGM Exporter 两个数据源，**手搓 4 条 must-have 告警**，dashboard 自己画几张关键图：

| 告警 | PromQL 关键指标 | 阈值建议 |
|---|---|---|
| 显存爆 | `DCGM_FI_DEV_FB_USED / DCGM_FI_DEV_FB_TOTAL` | > 0.95 持续 1min |
| TTFT 飙升 | `vllm:time_to_first_token_seconds` p99 | > 1.5s 持续 5min |
| 队列堆积 | `vllm:num_requests_waiting` | > 32 持续 2min |
| 服务死循环 | nginx 5xx 率 | > 5% 持续 1min |

四条覆盖了 90 天里咱们家所有真实告警事件：显存爆是模型热切换没卸干净，TTFT 飙升是有人扔了一个 30K context 把队列堵住，队列堆积是网关侧没限流，5xx 是进程崩了 systemd 正在拉起。每一条都直接告诉值班同事下一步该做什么。

DCGM 单独说一句：装的时候要走 nvidia 官方 docker 镜像，别从发行版仓库装，发行版那个版本经常对不上驱动版本，咱们家小机房第一次装走了发行版仓库版本，跑起来 `nvidia-smi` 能看到卡，DCGM 死活返回 0，折腾两个晚上才发现要换成 `nvidia/dcgm-exporter:3.3.5-3.4.0-ubuntu22.04` 这个 tag。

监控这层做完后，咱们家小机房把告警接到飞书机器人，凌晨四点 OOM 那种事现在最多 30 秒就能收到通知，进程已经被 systemd 自动拉起，值班同事打开手机看一眼"哦 Restart 成功了"就能继续睡。

## 模型热切换：Ollama 双模型陷阱 + vLLM 双实例蓝绿

团队场景里"换个模型试试"这件事看上去简单实则危险。Ollama 那条最容易踩的陷阱就是：两个模型都设 `keep_alive=-1`（永驻），第二个模型加载那一刻显存爆，OOM killer 直接把整个 Ollama 进程干掉，第一个模型也跟着没。

正确做法是 `OLLAMA_MAX_LOADED_MODELS=2` + 显存预算精算：24G 显存跑 Qwen3-32B Q4 量化占 ~18G，剩下 6G 只能驻一个 7B 或 13B 备模型，再大就别想同时驻留。`llama-swap` 这个工具能做更精细的"按需加载 + 自动卸载"，单卡跑 5-6 个模型轮换的场景非常合适。

vLLM 这边模型切换走的是另一条路——**双实例蓝绿部署**。一张卡跑不动两份 vLLM，但是两张卡或者一张卡 + Mac Studio 备机的场景下，可以这么切流：

```nginx
# vLLM 双实例蓝绿 + nginx weight 切流
upstream llm_backend {
    server 127.0.0.1:8000 weight=9;   # blue (v1)
    server 127.0.0.1:8001 weight=1;   # green (v2, canary 10%)
}
```

新模型先在 8001 端口起 green 实例，nginx upstream 给 10% 流量过去观察 24 小时，监控看 TTFT 和错误率没问题再调权重 5:5，最后切到 0:10 完全替换。回滚就是把 weight 改回 9:1 reload nginx，秒级回滚不掉调用。

模型文件下载也有个小细节值得说——**atomic mv**：先下到 `model.safetensors.tmp`，下载完整后 `mv` 到正式名，避免下载到一半被进程读到不完整文件直接 panic。HuggingFace CLI 默认不做这步，咱们家小机房写了一个 5 行 shell 包装，每次下载完先 md5 校验再原子改名。

SitePoint 那份 2026评测里有句话挺有意思——"At 128 concurrent requests, vLLM maintained a 100% success rate while Ollama broke down." 团队生产并发场景 vLLM 是首选，Ollama 适合做长尾备份或开发同事单机试验。咱们家小机房的拓扑就是 vLLM 主 + Ollama 备：vLLM 跑 Qwen3-32B 接生产工作流，Ollama 上挂着 5-6 个候选模型给开发同事 ad-hoc 调用。

## OpenClaw 接共享后端 + 三档硬件 case

![三档硬件配置 hardware tiers 卡片](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-14/cn-local-llm-ops-runbook-2026-05-14/cn-llm-ops-hardware-tiers.png)

OpenClaw 客户端走 OneAPI 网关接共享后端这事，第一次配的时候踩过一个无声坑。Rogério Richa 那篇 Medium 文章里说得很到位——"OpenClaw 端只显示空响应不报错，要先在网关侧 mock 一次 tools=[] 跑通。" 表现就是 OpenClaw 发请求出去，nginx 日志看到 200，OpenAI 兼容接口返回也正常，但是 OpenClaw 客户端那一侧就是空响应，没报错没日志。

排查路径：先用 curl 在网关侧手动发一个 `tools=[]` 的最小请求跑通，再用 OpenClaw 发请求看 base_url + key 是不是真的命中到了同一个网关。咱们家小机房遇到过两次都是 OpenClaw 配的 base_url 漏掉 `/v1` 后缀，请求打到 404 但是 OpenClaw 客户端把 404 翻译成"空响应"没弹错。

OpenClaw 共享后端的配置示例长这样：

```yaml
# OpenClaw 客户端接 OneAPI 网关共享后端（伪 config）
models:
  providers:
    - name: team-shared-vllm
      type: openai-compatible
      base_url: https://llm.internal/v1
      api_key: ${TEAM_TOKEN}
      models:
        - id: qwen3-32b
          aliases: [chat, code]
```

每个工位的 `TEAM_TOKEN` 是 OneAPI 发的虚拟 key，OpenClaw 在客户端配置里只看到一个 `team-shared-vllm` provider，底层走的是哪台服务器、哪个推理后端、配额还剩多少，全部由网关侧统一管控。

硬件三档 case 咱们家小机房都跑过实测：

| 档位 | 卡 | 价位（2026-05 闲鱼 / V2EX / 京东行情） | 跑什么 | 团队规模 |
|---|---|---|---|---|
| 入门 | RTX 3090 24GB 二手 | 6,000-8,000 元 | Qwen3-14B 全精度 / Qwen3-32B Q4 量化 | 5-8 人 |
| 中阶 | RTX 4090 24GB 二手 | 8,000-10,000 元 | Qwen3-32B Q4 / Qwen3-Coder 30B | 8-12 人 |
| 重型 | Mac Studio M3 Ultra 256GB | 70,000-90,000 元 | DeepSeek R1 671B 4-bit | 12-20 人 |

3090 入门档是 5000 元二手卡那条故事的主角：闲鱼上挖一张 7,000 元左右的矿渣 3090，加一台 2,000 元的二手工作站主机，凑齐 9,000 元的整机预算，跑 Qwen3-14B 全精度做 OpenClaw + 团队聊天后端，5-8 人团队完全够用。咱们家小机房第一台机器就是这个配置，跑了 5 个月除了风扇换过一次没出过问题。

4090 中阶档是当前的甜点位——8,000-10,000 元能扛起 12 工位 + OpenClaw 客户端 + 内网应用的完整团队负载。市面上还有一种 RTX 4090 48G 改卡 25,000 元区间，**是灰色卡，团队生产环境不推荐**——改卡的固件来源不明，故障率比官方卡高，长期跑训练或推理出问题不在保。

重型档 Mac Studio M3 Ultra 256GB 是真正能在本地跑 DeepSeek R1 671B 4-bit 量化的方案——需要 448GB VRAM 分配，M3 Ultra 跑 670B 4-bit 约 17-18 tokens/s。70,000-90,000 元买一台扛得住 12-20 人团队跑顶尖开源模型，咱们家小机房目前没上重型档，但是合作团队那边跑了一台两个月没掉链子。

## 国内合规边界：PIPL 数据不出本机 + 国密上不上的判断

本地部署的最大隐性价值就是合规。PIPL（个人信息保护法）核心约束之一是"个人信息处理者向境外提供个人信息"那一条，本地大模型这条路从架构上就把数据留在了本机，等同于把"出境合规"这一整块复杂度归零。

国密那块要不要上，咱们家小机房的判断标准是看团队对接的客户类型：

- **纯研发团队、产品早期、客户不要求合规审计** → 不上国密，HTTPS + basic auth 够用
- **服务政企客户、金融客户、客户合同里写了 GB/T 39786 类要求** → 上 SM2/SM3/SM4 国密套件
- **关键信息基础设施运营者（CIIO）** → 必上，没得商量

国密改造主要在 nginx 那一层——OpenResty 加 GmSSL 模块替换 OpenSSL，配置 SM2 证书 + SM4 加密套件。团队侧 OpenClaw 客户端目前还不直接支持国密通道，咱们家小机房的做法是网关侧维护双协议端口：内部团队走标准 HTTPS，对外政企客户走国密通道。

监控告警这条链路在合规框架里也有位置。中伦那份《2025/2026 网络安全展望》里有句话——"合规不再停留在制度文本，而要落在可审计、可追溯、可复盘的系统事实。" 5xx 告警 + 网关请求日志 + DCGM 显存使用记录这三份数据加起来，就是合规审计要的"可追溯、可复盘"。咱们家小机房日志保留 180 天，每月生成一份运维报告归档，等监管真上门那一天能直接拿出实物，不用临时翻系统翻到崩溃。

## 90 天稳态：5000 元二手 3090 跑出 P99 480ms

![90 天稳态 90day stability 关键数字 ops 月账单与 P99 TTFT](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-14/cn-local-llm-ops-runbook-2026-05-14/cn-llm-ops-90day-stability.png)


回到 4 月底关掉云端 LLM 充值那个故事——90 天压下来咱们家小机房的稳态数字是：

- **月账单：6,800 元 → 0 元**（电费每月 ~200 元忽略不计）
- **负载：12 个工位 + 4 个 OpenClaw 客户端 + 2 个内网应用**
- **P99 TTFT：480ms 以内**（稳定值，OpenClaw 客户端调用体感和云端调用基本一致）
- **可用性：90 天 2 次告警，均 30 秒内自动恢复**
- **硬件：一台 4090 主 + 一台 3090 备 + 一台 Mac Studio 灾备**

5000 元二手 3090 那张卡现在还在备机上服役，跑 Qwen3-14B 全精度作为 vLLM 主实例的备份，主实例挂了 nginx 自动切流过去，业务层无感。"小型 OpenAI" 这个说法听上去有点夸张，但是从工位调用体验看——OpenClaw 在团队同事电脑上点一下，响应速度和半年前付钱给云端 LLM 那时候基本一样，背后跑的是机房角落里一台从闲鱼淘来的二手主机，财务还省下每月 6,800 元——这就是 2026 年小团队私有化路真正趟出来的样子。

咱们家小机房接下来的演进方向有三件：把 Mac Studio 灾备扩成 DeepSeek R1 671B 的主推理节点；网关层加上 Prometheus 远程读写支持，把告警接到 PagerDuty 兼容方案；OpenClaw 那边接入团队知识库，把"通用聊天"升级成"懂团队上下文的内部助手"。这三件做完之后，5-15 人团队私有化 LLM 这条路就基本走完了一个完整闭环。

5000 元二手 3090 也能扛起一支 12 人队伍的全部 AI 工作流——这件事在 90 天前是个假设，今天是个数字。本地大模型给小团队的工程窗口确实已经打开了。

---

### 参考文献

1. SitePoint《Ollama vs vLLM 2026 评测》：128 并发对比测试。
2. SitePoint《Ollama Production Setup Guide 2026》：版本钉死与生产配置实践。
3. Rost Glukhov《I Exposed Ollama to the Internet — Here's What Almost Broke It》（Medium，2026-05）：公网暴露与扫描案例。
4. Rogério Richa《Setting up a private local LLM with Ollama for OpenClaw: A Tale of Silent Failures》（Medium）：OpenClaw 静默失败排查。
5. 中伦《2025/2026 网络安全展望》：合规可审计可追溯论述。
6. GitHub stars 数据（2026-05-13 gh api 实查）：LiteLLM 46,850 / OneAPI 33,650 / new-api 33,118 / FastGPT 28,024 / Ollama 171,346 / vLLM 79,919。
7. NVIDIA DCGM Exporter Grafana dashboard ID 12239 失效说明：DCGM v3.3+ 指标名变更。
8. Mac Studio M3 Ultra 跑 DeepSeek R1 671B 4-bit 实测：17-18 tokens/s。
