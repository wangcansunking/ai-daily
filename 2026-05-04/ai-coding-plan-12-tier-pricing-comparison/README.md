---
title: "海外 5 家 vs 国产 5 家：AI Coding 选档与组合实操"
date: 2026-05-04
weekday: 星期一
category: AI Coding · 选档实操
tags: [AI Coding, Claude Code, Cursor, GitHub Copilot, Qoder, DeepSeek, Qwen, 通义灵码, Trae, CodeGeeX, 订阅定价, 实操组合]
slug: ai-coding-plan-12-tier-pricing-comparison
description: "AI Coding 订阅怎么选？10 家主流厂商各自擅长什么、对应哪类开发者、价格档多少。文章给 4 个能直接执行的组合方案——入门档（月费 ¥144）、普通工程师档（¥250-450）、重度 Coding Lead 档（¥1500-3000）、企业合规档——每档都注明用哪家 + 哪个 plan + setup 一行话。性价比拐点：Claude Opus 4.7 87.6% vs Kimi K2.6 80.2% 差 7 分，月费差 10 倍。"
cover: "ai-coding-plan-12-tier-cover.png"
---

# 海外 5 家 vs 国产 5 家：AI Coding 选档与组合实操

![海外 vs 国产 AI Coding 订阅对照](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-04/ai-coding-plan-12-tier-pricing-comparison/ai-coding-plan-12-tier-cover.png)

> **60 秒看完决定**：日常 coding 选 Cursor Pro $20 / Qoder 半价 $10；重度专业用户上 Claude Max 20× $200；预算紧 + 自己愿搭工具栈走 DeepSeek API + Aider。文末有 4 个能直接复制的组合方案。

AI Coding 订阅市场 2026-05 进入价格洗牌期——Cursor 把 Ultra 档定到 $200、Claude Max 20× 同价、GitHub Copilot Pro+ 稳在 $39。**已宣布的下一站**：GitHub Copilot 2026-06-01 全线转 usage-based billing。Google Gemini API 免费层 2025-12 已先行砍 50%-92%。订阅制全盛期正在悄悄结束，但各家擅长什么、适合谁，没有变。

下面按"各家擅长 → 适合哪类开发者 → 推荐档 → 4 个能执行的组合"的顺序展开。

## 一、各家擅长什么（10 家全景对照）

把读者每天接触的 10 家主流 AI Coding 厂商按能力擅长 + 适合人群 + 推荐档放在同一表里——

### 海外 5 家

| 厂商 | 擅长什么 | 适合什么开发者 | 推荐档（月费） |
|---|---|---|---|
| **Anthropic Claude（含 Claude Code）** | frontier 工程化能力（Plan Mode / Sub-agent / Skills / Hooks / Cloud Agent）；Opus 4.7 SWE-Bench Verified 87.6% 当前 ceiling | 重度 coding lead / 跨服务架构 / Agent 长任务 | **Pro $20**（入门）/ **Max 20× $200**（重度）|
| **Cursor** | IDE-first 体验 + 多 frontier 模型挂载（Claude / GPT-5 / Gemini）+ Cloud Agents | 普通工程师日常 coding / 多语言项目 | **Pro $20**（标准）/ **Pro+ $60**（中度）|
| **GitHub Copilot** | 最广泛分发 + GitHub 生态原生集成 + 5 档定价覆盖个人到企业；2026-06-01 转 usage-based 月费数字不变 | GitHub 重度用户 / 团队需要 IP 索赔 + 数据隔离 | **Pro $10**（入门）/ **Pro+ $39**（中度）|
| **OpenAI ChatGPT + Codex CLI** | OpenAI 生态深度用户（GPT-5.5 / Codex CLI 全档免费含）；Pro 有 $100 / $200 双档 | 跨境技术资料 + 英文文档场景 / OpenAI API 重度用户 | **Plus $20**（入门）/ **Pro $100**（中度）|
| **Google Gemini Code Assist** | Google Cloud 原生 + 长上下文（Gemini 2.5 Pro / Flash）+ 1000-2000 requests/day 配额 | GCP 重度用户 / Vertex AI 工程团队 | **Standard $22.80** / **Enterprise $54** 每用户 |

### 国产 5 家

| 厂商 | 擅长什么 | 适合什么开发者 | 推荐档（月费） |
|---|---|---|---|
| **Qoder（阿里）** | 多模型挂载（Qwen 3.6-Plus / DeepSeek V4-Pro / GLM-5）+ Quest Mode 持久身份 + 长期记忆 | 国内开发者最划算 / 想试多家国产模型 | **Pro $20（半价活动 $10）** |
| **通义灵码（阿里）** | VSCode + JetBrains 双 IDE 插件 + 阿里通义系列 + 阿里云数据驻留（企业版） | 阿里云生态 / 个人免费用户 | **个人 ¥0** / **企业按席位（联系销售）** |
| **Trae（字节 AI IDE）** | 字节 AI IDE，2026-02-24 转 token 计费 + 五档订阅 | 字节生态 / 国内云数据驻留 | **Free $0**（限额收紧）/ **Pro $10** |
| **CodeGeeX 4（智谱）** | VSCode + JetBrains 插件 + GLM-5 / GLM-Coder 模型 | 国产 IDE 集成里被忽视的成熟一档 / 智谱模型重度用户 | **个人免费** |
| **DeepSeek V4-Pro API + Aider / Cline** | 国产 frontier 性价比 ceiling；折扣期 $0.435 / $0.87 per M（至 2026-05-31）| 重度 + 自己愿意搭 Aider 工具栈 / 预算紧的小团队 | **0 月费 + 按 token**（折扣期内重度月度约 ¥814）|

**国产 API 备份组合**（不单列档但值得提）：Kimi K2.6（Verified 80.2%）/ 智谱 GLM-5 / Qwen 3.6-Plus 三家 API + Aider 客户端，按 token 计费，可与 DeepSeek V4-Pro 形成多模型 fallback，避免单家限速影响生产。

## 二、4 个能直接执行的组合方案

按月度预算给 4 个**能复制粘贴 setup 的组合**——不是抽象建议，是「装哪个 + 配哪个 + 月度多少钱」。

### 方案 A · 入门 / 学生 / 个人偶尔用（月度 ¥0-200）

**对位人群**：每天调用 5-15 次 / 主要写脚本 / 改 bug / 学习项目

**组合**：
- **通义灵码个人版**（¥0）— VSCode 主用 IDE
- **ChatGPT Plus**（$20，约 ¥144）— 跨境技术资料 + 英文文档查阅
- **Trae Free**（$0）作 IDE 备选 — 限额收紧但轻度仍够用

**Setup（3 步）**：
1. VSCode 装「通义灵码」插件，登录阿里云账号即可激活个人版
2. 访问 chatgpt.com 订阅 Plus（如需海外网络）
3. （可选）trae.ai 装 Trae 桌面客户端体验 byte 字节生态

**月度成本**：约 ¥144-216（Trae 升 Lite $3 时）

---

### 方案 B · 普通工程师 / 多语言日常 coding（月度 ¥250-500）

**对位人群**：每天 30-50 次调用 / 多语言项目 / 长上下文 / 跨文件重构

**组合**：
- **Qoder Pro 半价 $10**（约 ¥72）— IDE 主入口 + 多模型挂载
- **DeepSeek V4-Pro API**（按用量 ¥100-300/月，折扣期内）— 高质量 fallback
- **Aider 客户端**（开源 ¥0）— 处理 git diff / 跨文件重构兜底

**Setup（4 步）**：
1. 访问 qoder.com 注册 + 订阅 Pro 半价档（活动结束日期官方未公布，建议尽早锁定）
2. 在 Qoder 配置里挂载 Qwen 3.6-Plus / DeepSeek V4-Pro / GLM-5 三家国产模型
3. api-docs.deepseek.com 申请 DeepSeek API key，配置 75% off 折扣期套餐
4. `pip install aider-chat` 装 Aider，`aider --model openai/deepseek-chat` 启动

**月度成本**：约 ¥250-450

---

### 方案 C · 重度专业 / Coding Lead / Agent 长任务（月度 ¥1500-3000）

**对位人群**：每天 50-150 次调用 / 跨服务架构 / Agent 长任务 / 自动化 CI 修复

**组合**：
- **Claude Max 20×（$200，约 ¥1440）**— frontier 工程化能力（Plan Mode / Sub-agent / Cloud Agent）主力
- **DeepSeek V4-Pro API**（按用量 ¥100-300/月）— 备份切换防限速
- **（可选）Cursor Pro+ $60**（约 ¥432）— IDE 内多模型 fallback

**Setup（3 步）**：
1. claude.com/pricing 订阅 Max 20× 档
2. cursor.com 装 Cursor IDE，按需开 Pro+ 订阅
3. DeepSeek API key 作 fallback，写一个 `~/.config/aider.conf.yml` 配置切换路径

**月度成本**：约 ¥1500-3000（含 Cursor Pro+）

**需注意**：Uber 2025-12 全员推 Claude Code 到 2026-04 烧光全年 AI 预算的真实案例已被多次报道（[HN 47976415](https://news.ycombinator.com/item?id=47976415)）。**$200 月费乘上工程团队规模，单家公司年度账单可达百万美元级**。重度档要算清按 token 实际用量是否值。

---

### 方案 D · 企业 / 团队 / 数据合规（月度按席位算）

**对位人群**：多人协作 / 数据敏感（金融 / 医疗 / 政府 / 法律）/ 国家合规要求 / 代码不出境

**组合**：
- **通义灵码企业版**（按席位以阿里云为准）— 团队规则管理面板 + 阿里云数据驻留
- **自托管 Qwen3 Coder 480B / DeepSeek-V4 / GLM-5**（公司 IDC GPU + Aider/Cline 客户端）— 国产权重在企业内 IDC，敏感代码不出境
- **DeepSeek V4-Pro API**（备份）— 公网 API fallback 用于非敏感任务

**Setup（4 步）**：
1. 联系阿里云销售订通义灵码企业版（具体定价以阿里云官方为准）
2. 公司 IDC 部署：单卡 80GB（A100 / H100）跑 Qwen3 Coder 32B；4 GPU 起跑 480B / DeepSeek-V4-Flash
3. 装 Aider 或 Cline 客户端，配置内部 API endpoint 替换 OpenAI base_url
4. 写一份团队 plan：哪些项目走自托管（敏感）、哪些走云 API（一般）、合规审计 log 流向

**月度成本**：主要是 **GPU 折旧 + 运维工程师工资**，订阅费占比小

## 三、性价比拐点：能力差 7 分但价差 10 倍

把 10 家厂商背后的模型按 SWE-Bench Verified 排起来——

| 模型 | SWE-Bench 分数 | 出现在哪几档 |
|---|---|---|
| Claude Opus 4.7 | **87.6%** Verified（[llm-stats.com](https://llm-stats.com/benchmarks/swe-bench-verified)） | Claude Pro / Max 5× / Max 20× / Cursor Pro+ + Ultra / Copilot Pro+ |
| MiniMax M2.5 | **80.2%** Verified（官方） | MiniMax API 路径 |
| Kimi K2.6 | **80.2%** Verified · **58.6%** Pro（更难一档） | Moonshot API |
| Qwen3.6-35B-A3B | **73.4%** Verified（实测；3.6-Plus 厂商未单独公开 Verified 分） | 通义灵码 / Qoder 多模型挂 |
| DeepSeek V3.2 | **73.1%** Verified（V3.2 论文实测；V4-Pro 厂商尚未公开 Verified 分） | DeepSeek API + Aider 自配 |

**两个判断**：

**第一，能力差 7 分但月费差 10 倍**。Claude Opus 4.7 87.6% 是 ceiling，国产同档 Kimi K2.6 80.2% 紧跟（差约 7.4 分），DeepSeek V3.2 / Qwen 73% 量级。但月费差距超 10 倍——Claude Max 20× $200 vs DeepSeek API 折扣后 $0.435/$0.87 per M，按月度 100M token 用量算约 ¥190 量级。**性价比拐点不在能力差异，在订阅模式差异**。

**第二，$200 档 Claude vs DeepSeek API 自配的对比，本质是「模型 + 工具栈」与「裸模型 API」的对比**。Claude Max 20× $200 含的不仅是 Opus 4.7 调用配额，还有 Claude Code 的工具链（Plan Mode / Sub-agent / Skills / Hooks / Cloud Agent）。DeepSeek V4-Pro 折后 $0.87/M 输出只是模型调用本身，工具栈要自己用 Aider / Cline / Continue 搭——**省的是订阅费，付的是工程时间**。

## 四、过去 · 现在 · 未来时间轴

![AI Coding 订阅定价 · 过去-现在-未来时间轴](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-04/ai-coding-plan-12-tier-pricing-comparison/ai-coding-plan-pricing-timeline.png)

12 档定价格局不是一夜形成的，下一步也不是空气来的。

**过去 12 个月已落地**：
- **2025-04-09 · Anthropic 上线 Claude Max 5× $100 / 20× $200**：首次把"重度档 $200"钉在 AI Coding 行业（[anthropic.com/news/max-plan](https://www.anthropic.com/news/max-plan)）
- **2025-06-16 · Cursor Pro 改 usage-based + 推 Pro+ $60 / Ultra $200**：用户「几天就烧完」配额集体爆发，CEO 道歉退款（[cursor.com/blog/new-tier](https://cursor.com/blog/new-tier)）
- **2025-12-07 · Google Gemini API 免费层砍 50%-92%**：Code Assist 走免费 / Standard 路径的轻量集成被波及（[ai.google.dev/gemini-api/docs/rate-limits](https://ai.google.dev/gemini-api/docs/rate-limits)）
- **2026-04-04 · Anthropic Enterprise 弃用 legacy seat**：新合同改为 seat 起步费 + token 实际消耗（[axios.com 2026-04-06](https://www.axios.com/2026/04/06/anthropic-openclaw-subscription-openai)）
- **2026-04-09 · OpenAI 推 ChatGPT Pro $100 中间档**：Codex 用量是 Plus $20 档的 5 倍（[cnbc.com 2026-04-09](https://www.cnbc.com/2026/04/09/openai-chatgpt-pro-subscription-anthropic-claude-code.html)）

**未来已宣布**：
- **2026-06-01 · GitHub Copilot 全线转 usage-based billing**：月费数字不变（Pro $10 / Pro+ $39 / Business $19 / Enterprise $39 per user），但配额改按 token 实扣。开发者社区定性：「**You will get less, but pay the same price**」（[github.blog/usage-based-billing](https://github.blog/news-insights/company-news/github-copilot-is-moving-to-usage-based-billing/)）
- **Anthropic Enterprise 新合同强制按 seat + 用量收费**：legacy 固定 seat 月费档不再新签

**意味着什么**：未来 6-12 个月，AI Coding 订阅定价的方向不是哪家又涨价或降价，是 **flat-rate 订阅 → usage-based 计费**的整体迁移。Cursor 已经走完、Anthropic Enterprise 在走、Copilot 6-01 即将走完。**月费数字稳定是表象，真实成本结构正在转向 token 实扣**。

国内 Trae 2026-02-24 已先转 token 计费——**字节是国产第一家在个人档转 usage-based 的厂商**——这条暗线在国产里也已经开始跑。

## 五、收官：选档判断 3 个原则

2026-05 是 AI Coding 订阅市场的明确分水岭。给国内一线开发者 3 条判断原则——

**第一，按使用强度选档**。新手 / 学生免费档够用；普通工程师走 Cursor Pro $20 或 Qoder 半价 $10；重度 coding lead 上 Claude Max 20× $200。**不要为不会用到的高级功能付钱**。

**第二，留一条 fallback**。任何月费档都有限速 / 限额；任何 API 都可能限流。重度用户至少配两家——主力 + 备份（Claude Max + DeepSeek API / Cursor Pro+ + Qoder + Aider）。

**第三，关注 6-01 转 token 计费的影响**。GitHub Copilot 6-01 之后真实成本结构会变，年付用户合同到期前可观望；月付用户 6-1 自动迁移按 token 实扣，建议 6-1 起每天看自己的 preview bill 记录 30 天真实 token 消耗，决定是否续订。

下一波要看的不是哪家又降价 / 涨价，是**哪一档的工具栈做出了别人复刻不了的体验、哪一档把 token 实扣的成本透明度做到一眼可读**。月费数字的透明已经达成，**单位 token 成本的透明是下一关**。

---

**信息源**

- Cursor 定价：[cursor.com/pricing](https://cursor.com/pricing)（2026-05-04 实证）
- GitHub Copilot：[github.com/features/copilot/plans](https://github.com/features/copilot/plans) + [docs.github.com/copilot/billing](https://docs.github.com/en/copilot/concepts/billing/organizations-and-enterprises)（2026-05-04 实证）
- DeepSeek V4-Pro / V4-Flash 模型卡 + API 价：[api-docs.deepseek.com](https://api-docs.deepseek.com/quick_start/pricing)
- Anthropic Claude Pro / Max：[claude.com/pricing](https://claude.com/pricing)（含 Uber 案例 [HN 47976415](https://news.ycombinator.com/item?id=47976415) 间接实证 Max 20× $200）
- OpenAI ChatGPT Plus / Pro $100 / Pro $200：[help.openai.com 9793128](https://help.openai.com/en/articles/9793128-about-chatgpt-pro-plans)
- Google Gemini Code Assist：[developers.google.com/gemini-code-assist/resources/quotas](https://developers.google.com/gemini-code-assist/resources/quotas)
- Qoder：[docs.qoder.com/events/ultimatediscount](https://docs.qoder.com/events/ultimatediscount)（半价 $10 活动期，结束日期官方未公布）
- 通义灵码：[lingma.aliyun.com/pricing](https://lingma.aliyun.com/pricing)
- Trae：[trae.ai/pricing](https://www.trae.ai/pricing) + [ithome.com 2026-02-24 计费改革](https://www.ithome.com/0/923/234.htm)
- CodeGeeX：[codegeex.cn](https://codegeex.cn/)
- Kimi K2.6：[kimi.com/blog/kimi-k2-6](https://www.kimi.com/blog/kimi-k2-6)
- 智谱 GLM-5：[bigmodel.cn/pricing](https://bigmodel.cn/pricing)
- 时间轴信息源：[anthropic.com/news/max-plan](https://www.anthropic.com/news/max-plan) · [cursor.com/blog/new-tier](https://cursor.com/blog/new-tier) · [ai.google.dev/gemini-api/docs/rate-limits](https://ai.google.dev/gemini-api/docs/rate-limits) · [axios.com 2026-04-06](https://www.axios.com/2026/04/06/anthropic-openclaw-subscription-openai) · [cnbc.com 2026-04-09](https://www.cnbc.com/2026/04/09/openai-chatgpt-pro-subscription-anthropic-claude-code.html) · [github.blog/usage-based-billing](https://github.blog/news-insights/company-news/github-copilot-is-moving-to-usage-based-billing/)

注：所有月费数字以官方页面实时定价为准，本文为 2026-05-04 撰写时点对照。SWE-Bench 分数仅引用厂商官方公开数据 + 权威排行榜，未公开项明示"厂商未公开"。
