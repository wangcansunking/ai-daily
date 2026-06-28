# OpenClaw 国内用户完全指南 · 99 元/年养一只 35 万 star 的龙虾

>
> OpenClaw（🦞 龙虾）是 2025 年 11 月一个奥地利独立开发者 Peter Steinberger 开源的个人 AI 助手，到 2026 年 4 月 GitHub 突破 **35 万 star**，GitHub 历史上增长最快的项目。2026 年 1 月底阿里云 / 腾讯云 / 火山引擎**同步推出官方一键部署**，国内用户的门槛降到了一行命令。本文完整讲清楚：国内怎么装、喂哪家模型、花多少钱、哪种人该选哪种方案。

![OpenClaw 国内完全指南 · 99 元养一只龙虾](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/openclaw-guide-long/openclaw-long-cover.png)

---

## 📋 国内用户先看这 5 件事


- 🦞 **OpenClaw 是谁**：开源个人 AI 助手，跑在你自己机器 / 云上
- 💰 **要花多少钱**：云托管 99-200 元/年，AI 模型按量付费 0-80 元/月（国内方案）
- 🛠 **多难装**：阿里云 / 腾讯云 / 火山引擎都有**官方一键部署**，无需手搓服务器
- 💬 **支持哪些国内软件**：微信、**企业微信**、**飞书**、**钉钉**、**QQ**（海外 Slack/Telegram 也行）
- ⚠️ **海外方案说明**：Claude / GPT / Gemini 全部需科学上网 + 国际信用卡，国内方案更直接

---

## 一、🦞 什么是"龙虾"？


350K stars、1,600 个贡献者、月活 320 万——它不新开 App，而是在你**已经在用的聊天软件**里多出一个好友，发消息就能让它干活。

**名字两次被投诉才定下来。** 作者 Peter Steinberger 最早叫它 **Clawdbot**（因为它基于 Anthropic 的 Claude 封装）。Anthropic 发律师函投诉商标，1 月 27 日改 **Moltbot**，3 天后作者又嫌"说不顺口"改成 **OpenClaw**。🦞 emoji 从此成官方吉祥物。

**规模**：
- GitHub **35 万+ stars**（2026 年 4 月）
- 0 → 10 万 star 只用 3 个月
- 4 月初**单日 +10K star，打破 GitHub 历史纪录**
- 月活 320 万，月访问 3800 万
- 支持 20+ 聊天软件

**和 ChatGPT / 豆包的本质差别**：
- ChatGPT：开网页 → 一对一对话，和你的聊天软件隔离
- 豆包：App 内用，和你的聊天软件隔离
- **OpenClaw**：**植入你现有的聊天软件**。朋友 / 同事 / 龙虾 同一个界面，切换零成本

---

## 二、8 个真实场景：它每天替你干什么


1. **半夜老板消息自动回**——"老板 @我 → 自动回'收到，早上处理'"，你睡你的
2. **一句话订高铁**——"下周五晚上海虹桥到北京南，二等座靠窗"，自动查 12306 比价返 3 个选项
3. **30 秒读完论文**——PDF / 合同丢给它，30 秒出摘要 + 核心论点 + 生词解释
4. **Excel 一句话分组画图**——"按部门分组算平均工资"，自动搞定带图表
5. **智能家居**——连米家 / Home Assistant，下班前 20 分钟开空调到 26 度
6. **日程规划**——接飞书日历 / 钉钉日历："下周空出 3 个下午做深度工作"
7. **发票拍照自动报税**——国内版 skill-tax 识别 + 分类 + 汇总
8. **随时知识问答**——"黑洞是什么颜色？用小学生能听懂的话"

---

## 三、💰 国内云托管 · 99-200 元/年


![国内云一键部署 OpenClaw](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/openclaw-guide-long/openclaw-cloud-pricing.png)

![三家云官方部署示意图](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/openclaw-guide-long/openclaw-cloud-deploy.png)

### 腾讯云 · 99 元/年（性价比首选）

- 预装 OpenClaw **2026.2.3-1**
- **QQ / 微信 / 企业微信 / 钉钉 / 飞书全支持**
- 配套腾讯云混元 **100 万通用额度**

### 阿里云 · 首单 <200 元/年

- 官方部署脚本（见阿里云帮助中心"OpenClaw"）
- **阿里云百炼平台**免费额度叠加：通义千问、DeepSeek、Kimi、MiniMax、GLM 都能直接调用
- 适合要吃国内 AI 生态的人

### 火山引擎 · 豆包免费额度加成

- 方舟平台：**日均 200 万 tokens 内永久免费**
- 适合要吃字节系 AI 的人

### 三种非云方案

- **自建 VPS**（Ubuntu / Debian）：1 元入门一个月，完全自主，适合技术老手
- **OneClaw 托管 SaaS**：199 元/月，零运维，但走海外线路
- **本地 Mac / Win**：0 元电费，跑本地 Qwen3.6 / Kimi K2.6，适合隐私洁癖党

---

## 四、🍤 喂龙虾的粮食 · 国内 AI API 价格


![国内 AI API 价格](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/openclaw-guide-long/openclaw-api-pricing.png)

![国内主流模型 logo 矩阵](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/openclaw-guide-long/openclaw-domestic-models.png)

### ✅ 国内直连 · 无需科学上网

- **智谱 GLM-4-Flash**：**永久免费**（浅尝党无脑选它）
- **DeepSeek V3.2**：按量付费 **0.5 元 / 百万 tokens**，月花 10-30 元
- **Kimi K2.6 API**：按量 10 元 / 百万 tokens，月花 30-50 元
- **豆包 lite**：按量 3 元 / 百万 tokens
- **通义千问 Plus**：阿里云百炼平台送免费额度
- **硅基流动 / 七牛云 AI 聚合**：一个 Key 用多家，50-200ms 延迟

### ⚠️ 海外方案（需科学上网 + 国际信用卡 / 虚拟卡）

- **Claude Pro**：$20 ≈ **¥145/月**
- **Claude Max 5x**：$100 ≈ **¥725/月**
- **Claude Max 20x**：$200 ≈ **¥1,450/月**
- **ChatGPT Plus**：$20 = **¥145/月**

**国内 API 的底价比海外便宜 5-30 倍**，但智能上限略低——Claude Opus、GPT-5 在复杂推理上仍顶尖。普通场景国内完全够用。

---

## 五、🛠 10 分钟上手 · 腾讯云方案（推荐）


### 第一步：买腾讯云轻量服务器

- 打开 [cloud.tencent.com](https://cloud.tencent.com) → 搜 "OpenClaw" 应用镜像
- 选**新用户 99 元/年**套餐（2 核 4G 内存，够用）
- 付款后等 5 分钟实例创建

### 第二步：拿控制台粘 AI Key

- 进 OpenClaw 控制台（镜像内置 Web 界面）
- **配 AI 模型**：粘贴你的国产 API Key（推荐新手选智谱 GLM-4-Flash 免费 Key）
- 智谱注册地址：[open.bigmodel.cn](https://open.bigmodel.cn)

### 第三步：绑定聊天软件

**飞书方案（最平滑）**：
1. 飞书开放平台建应用
2. 开 Messaging / Bot 权限
3. 把 App ID + App Secret 贴进 OpenClaw 控制台
4. 完成

**微信个人号方案**（小心风控）：
- 走官方 iPad 协议版接入
- 别用个人企业号搞商业推广

**钉钉 / 企业微信方案**：
- 管理员后台建机器人
- Webhook + Token 贴进控制台

### 第四步：发第一条消息

"你好" → 龙虾自我介绍，**整个流程不超过 10 分钟**。

---

## 六、💎 进阶三件套 · 2000+ skill 任你挑


**Skill（技能包）· 像 iPhone 装 App**
- `skill-travel`（出行规划 · 12306 + 天气 + 美食）
- `skill-tax`（国内报税 · 发票识别 + 分类）
- `skill-health`（健康追踪 · 体检报告解读）
- `skill-finance`（投资组合 · 连券商 API 看持仓）

**Hooks（自动触发）· 它主动报告**
- 每天早 8 点：天气 + 新闻早报 + 今日日程
- 每晚 11 点：当天花销 + 明天待办
- 股票跌幅 >3% 自动告警

**MCP（外部工具接入）· 给龙虾装触手**
- **飞书 MCP**：读文档、发消息、建日程
- **钉钉 MCP**：OA 审批、考勤打卡
- **Notion MCP**：读写文档
- **12306 MCP**：订票查询
- **SiliconFlow / 七牛云 MCP**：聚合 API Key 切模型

---

## 七、⚠️ 7 个血亏坑 · 一晚上能烧 1000 元


- **AI 费烧爆**：真人案例——有人忘关自动 loop，一晚烧掉 $200（¥1450）。**必设月度预算上限**，OpenClaw 自带
- **用错模型档位**：日常对话用 Kimi / DeepSeek 够了，别用 Claude Max 20x 大炮打蚊子
- **权限给太多**：别给根目录读写 / 删除文件权限，除非 100% 信任该 skill
- **隐私泄漏**：别装第三方野路子 skill，它可能把聊天记录发到作者服务器。**只装官方市场的**
- **聊天号绑错**：公司号和个人号**严格分开**。工作内容进个人号会很尴尬
- **Gateway 挂了不知道**：设"心跳检查"——龙虾每天早 8 点跟你说"在线"，不在线立刻告警
- **自动升级踩雷**：OpenClaw 每周出新版。**固定周末手动升，关自动**，免得生产环境夜里崩

---

## 八、💡 6 种人对号入座


![6 种人推荐方案](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-22/openclaw-guide-long/openclaw-buyer-guide-cn.png)

- 💡 **学生党 / 零预算**：本地 Mac/Win + **智谱 GLM-4-Flash 永久免费**，**月花 0 元**先玩 2 周
- 💡 **白领轻度**：**腾讯云 99 元/年** + **DeepSeek V3.2**（~15 元/月 token 费）——最划算组合
- 💡 **白领日常**：**阿里云 <200 元/年** + **通义千问 Plus + 百炼免费额度**（~40 元/月）
- 💡 **自由职业 / 创作者**：**阿里云 / 腾讯云** + **Kimi K2.6 + 豆包组合**（~80 元/月），skill 市场配 travel + health + finance
- 💡 **程序员 / AI 重度**：**自建 VPS** + **硅基流动聚合 Key**（~150 元/月），可一个 Key 切 DeepSeek / Kimi / Qwen / GLM
- 💡 **企业 / 老板**：**OneClaw 托管 199 元/月起**，企业账号直采，零运维

### ⚠️ 海外方案（仅适合）

- 已有科学上网稳定线路 + 国际信用卡 / 虚拟卡
- 重度用 Claude（复杂推理、长代码）的程序员
- 推荐 **Claude Max 5x ¥725/月** + 自建 VPS 走海外线路

### 🚫 3 种人别碰 OpenClaw

- 只想写 1 篇文章 → 直接用豆包 App / 通义千问 App
- 完全拒绝命令行 → 直接用豆包 App
- 公司禁用境外 API → 那就别碰 Claude 系，用国内方案完全够

---

## 九、🔮 实测 3 个月：每月回本 1500 元


**原来每天（75 分钟）：**
- 回微信消息 30 分钟
- 订机票 / 查路线 15 分钟
- 整理收据 / 记账 10 分钟
- 读收藏文章 20 分钟

**养了龙虾后（13 分钟）：**
- 消息它先筛，重要才叫你 → 5 分钟
- 订票一句话 → 2 分钟
- 发票拍照自动分类 → 1 分钟
- 文章先出摘要挑感兴趣深读 → 5 分钟

**每天省 62 分钟 = 月省 30 小时 = 按时薪 50 元 ¥1,500/月**。腾讯云 99 元 + DeepSeek 月均 15 元 = 年度 ¥280，当场回本剩下全赚。