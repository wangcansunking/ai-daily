# 怎么用 Claude Code 每天自动写一份 AI 日报 · 工程手册

> 这不是"用 AI 写作"的鸡汤文。这是一份**工程手册**——讲清楚我怎么用 Claude Code + 飞书 + GitHub，每天 8 点自动生成一份 5000-15000 字的 AI 日报，包括信息采集、图片本地化、Writer Agent 润色、5 路质量审核、飞书发布、IM 通知、以及随附一份 5 分钟阅读的微信公众号版。
>
> 最近更新：2026-04-22 · 双版本输出 + 表格转图 + 封面像素词边界修复

![每天自动写一份 AI 日报 · 工程手册](how-to-write-cover.png)

---

## 一、为什么要做

AI 领域每天的信息量是指数级的：arXiv 每天几十篇论文，GitHub 每天多个项目爆款，X/Twitter 大佬随时甩观点。手动追 = 累死；RSS = 浅；别人写的日报 = 没你想要的角度。

我要的是：**每天早上 8 点，一份结构清晰、有深度、有图、有明确判断、有行动建议的 AI 日报，自动出现在飞书群里。同一份内容，顺带一份 5 分钟阅读的微信公众号版，让非工程师朋友也能看。**

这套方案已稳定跑了两周，日均生成 8-12KB markdown + 6-9 张图 + 飞书富文本 + IM 通知。下面把架构、每个关键部件、坑、复现步骤一次讲透。

---

## 二、技术栈总览

```
┌──────────────────────────────────────────────────────────────┐
│  systemd timer （每日 08:03）                                │
│       │                                                      │
│       ▼                                                      │
│  scripts/generate-daily-report.sh                            │
│       │                                                      │
│       ▼                                                      │
│  Claude Code CLI  →  SKILL.md 加载                           │
│       │                                                      │
│       ├─► WebSearch / WebFetch / gh CLI （信息源采集）       │
│       ├─► 图片下载 → daily-report-images repo （git push）   │
│       ├─► scripts/generate-cover.py （Azure gpt-image-1.5）  │
│       ├─► Writer Agent （subagent · 单次润色 pass）          │
│       ├─► 5 审核 Agent 并行                                  │
│       │     真实性 · 图片 · 可读性 · 有效性 · 去重          │
│       ├─► reports/YYYY-MM/YYYY-MM-DD.md （git commit）       │
│       ├─► docx_builtin_import（飞书 MCP）→ 云文档            │
│       └─► im_v1_message_create（飞书 MCP）→ IM 通知          │
│                                                              │
│  【双版本扩展】                                              │
│       └─► wechat-article skill 触发                          │
│             ├─► 压缩到 2500-3500 字                          │
│             ├─► scripts/generate-table-image.py （表格转图） │
│             ├─► 4 精简审核 Agent                             │
│             └─► 飞书 + IM（公众号版入口）                    │
└──────────────────────────────────────────────────────────────┘
```

**三个 GitHub 仓库：**

1. `daily-report` — 主仓，脚本 + 报告 + skill 镜像
2. `daily-report-images` — 图床，所有封面/配图/表格图都落到这里
3. `.claude/skills/` — 全局 skill（不进 git，但 daily-report/docs/ 有镜像）

**外部依赖：**
- Azure OpenAI（gpt-image-1.5 生成封面 · gpt-image-2 未来可升级）
- 飞书开放 API（docx / im 接口，通过 lark-mcp）
- GitHub（raw.githubusercontent.com 做图床）

---

## 三、关键部件逐个讲

### 3.1 SKILL 三件套 · 分层解耦的写作体系

这套系统用了 **3 个 skill 分层组合**，而不是把所有规则塞进一个巨型 SKILL.md：

```
┌──────────────────────────────────────────────────────────┐
│  writing （底座 · 通用文风）                             │
│   - 钩子前置 / 数据优先 / 禁用词 / 短段节奏              │
│   - Writer Agent 8 条规则 + 硬约束                       │
│   - 数据真实性 / 链接验证 / 图片本地化 / 中英双语        │
└─────────┬───────────────────────────┬────────────────────┘
          │ 继承                      │ 继承
          ▼                           ▼
┌─────────────────────┐      ┌────────────────────────────┐
│ ai-daily-report     │      │ wechat-article             │
│  AI 日报专项        │      │  公众号文章专项            │
│ - 信息源 / KOL      │      │ - 2500-3500 字 / 5 分钟读  │
│ - 9 区块结构        │      │ - 表格转图（禁 md table）  │
│ - 7 天去重          │      │ - 图片密度 300-500 字一张  │
│ - 5 审核 Agent      │      │ - 发布会全场覆盖规则       │
│ - 13 步执行流       │      │ - 精简 4 审核 Agent        │
│ - 双版本输出联动    │      │ - mdnice 手动搬运流程      │
└─────────────────────┘      └────────────────────────────┘
```

**为什么这么分？**

- 通用规则（比如"别写'重磅''颠覆'"）只在一处维护，避免 3 个 skill 同步改
- 添加第 4 种场景（如"年终总结""招股书解读"）只要新写一个 domain skill 继承 writing 即可
- Claude 加载时可以精准匹配——看到"生成日报"加载 writing + ai-daily-report；看到"公众号文章"加载 writing + wechat-article；看到"写一篇产品评测"直接走 writing 单独用

**三个 skill 都是带 frontmatter 的 markdown**：

```markdown
---
name: writing
description: Foundation skill for Chinese prose quality...
---

# 正文内容（Claude 会当成系统提示读取）
```

文件位置：
- `~/.claude/skills/writing/SKILL.md`（底座）
- `~/.claude/skills/ai-daily-report/SKILL.md`（日报）
- `~/.claude/skills/wechat-article/SKILL.md`（公众号）

仓内镜像（版本控制用）：
- `docs/writing-skill.md`
- `docs/ai-daily-report-skill.md`
- `docs/wechat-article-skill.md`

**关键设计原则**（踩了很多坑总结出来的）：

1. **触发词写足**：`description` 里穷举所有可能的触发短语，Claude 才能在模糊指令下主动 invoke
2. **硬性约束用粗体 + 明确数字**：不要"建议"和"尽量"，要"**必须**"和"**字数 5000-15000**"
3. **执行流程步骤化**：用 numbered list 给明确步骤，Claude 会按序执行
4. **审核 Agent 用代码块写 prompt**：方便 copy-paste，也避免 Claude 临场自己编 prompt
5. **底座 + domain 分开**：通用规则（writing）和领域特有规则（daily-report / wechat-article）不要混在一起写

### 3.2 信息源采集 · WebSearch + WebFetch + gh CLI

SKILL.md 里定义了三梯队信息源，第一梯队是必查的：

- GitHub Trending（Python/TypeScript/Rust/Go 各看一遍）
- arXiv cs.AI/cs.CL/cs.CV
- HuggingFace 模型发布 / Spaces
- Twitter/X（AI KOL 清单）

**关键实战技巧：搜索结果的 URL ≠ 可访问的 URL**。搜索引擎会返回已删除或移动的页面。所以 SKILL.md 里强制要求：

```
- 所有 GitHub 链接：`gh api repos/{owner}/{repo}` 验证 200
- 其他 URL：WebFetch 验证
- 无法验证不收录
```

宁可不放链接，也不放 404 链接。

### 3.3 图片本地化 · 硬规则

**项目硬规则**：*所有图片 MUST 下载到 daily-report-images 仓 · 禁止外链*。

为什么：外部 CDN（比如 `images.fonearena.com`、`img1.mydrivers.com`）6 个月内挂掉概率 >70%。读者 6 个月后打开一个全是裂图的"历史日报"比不给看还糟。

实现方式：

```bash
# 每采集一条新闻，同步执行：
curl -sL -A "Mozilla/5.0" --max-time 30 \
  -o ~/repos/daily-report-images/content/YYYY-MM/descriptive-name.png \
  "$og_image_url"

# 全部采集完 commit + push
cd ~/repos/daily-report-images && git add content/ && git commit -m "content: ..." && git push

# Markdown 中用 raw URL 引用
![描述](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/YYYY-MM/name.png)
```

### 3.4 封面生成 · Azure gpt-image-1.5 + PIL 词边界

封面要求：**与头条主题呼应 + 带日期日期 weekday + 标题不截断英文词**。

脚本：`scripts/generate-cover.py`。核心逻辑：

1. Azure OpenAI 生成 1536×1024 背景（prompt 限定 no text）
2. PIL 叠加：品牌"◆ AI 日报"、日期、标题
3. **字体大小 64→56→48→40 自动缩小**，直到标题能在 ≤2 行内装下
4. **英文词 / 版本号 / 连字符 token 整体保留**，不能从中间切（比如 "Cursor" 不能切成 "Cur" + "sor"）

调用：

```bash
python3 scripts/generate-cover.py \
  --headline "马斯克 600 亿美金锁死 Cursor" \
  --date "2026-04-22" --weekday "星期三" --tag "投融资" \
  --concept "A rocket launching with code streaming, tech merger concept"
```

四种风格按日期轮换：flat / isometric / doodle / pixel。style 每 4 天循环一次，避免视觉疲劳。

### 3.5 Writer Agent · 一次润色 pass（关键魔法）

这一步是日报从"信息堆砌"变"可读作品"的转折点。Writer Agent 的职责（写在 SKILL.md）：

- **钩子前置**：头条前 3 句就点出"为什么最重要"
- **数据优先**：新闻开头第一句出数字，再解释
- **长段拆短**：超过 3 句必拆
- **去 AI 腔 + 禁用"一句话总结："模板**：替换"值得关注""备受瞩目"等套话；任何"**一句话总结：XXX**"前缀一律删——核心判断要么写进小标题，要么用引用块、金句收尾
- **编辑说落到行动**：不做空泛总结，给"今天/本周做什么"
- **标题重写**：头条/要闻 ≤20 字，带数字或冲突钩子

**硬约束（违反即作废）**：所有数字、引语、链接、图片 URL、日期、公司名严禁改动。Writer 只能改表达方式。

启动方式：

```
Agent({
  description: "Writer 润色",
  prompt: "读取 reports/YYYY-MM/YYYY-MM-DD.md。按以下原则整篇重写：(1) 头条前 3 句有钩子 (2) 数字先于解释 (3) 去 AI 腔/套话，严禁「一句话总结：XXX」模板 (4) 单段 ≤3 句、单句 ≤40 字 (5) 编辑说落行动 (6) 标题 ≤20 字含钩子。硬约束：事实/数字/引语/链接严禁改动。直接用 Edit 工具原地修改。"
})
```

### 3.6 五路审核 Agent · 并行跑

Writer Agent 完事后，5 个审核 Agent 同时发动：

| Agent | 查什么 |
|---|---|
| 真实性 | GitHub 链接 `gh api` 验证、星数差异 >20% 标记、融资数据交叉验证 |
| 图片 | 所有 URL 200、来源标注完整、头条必须有图 |
| 可读性 | 字数 5000-15000、单条快讯 ≤60 字、禁用词 |
| 有效性 | 新闻在 24-48 小时内、来源可信 |
| 去重 | 区块间 ≤2 次、跨 7 天去重、Trending 项目连续上榜去重 |

**全部 ✅/⚠️ 才能发布**，❌ 必须修复后重审。

### 3.7 飞书发布 · 两条路径

**路径 A（默认）· `docx_builtin_import`**

直接把 markdown 扔给 `mcp__lark-mcp__docx_builtin_import`，拿回 token + URL。简单快。

**路径 B（fallback）· Block API in-place**

问题：`docx_builtin_import` 对大 markdown（>15KB）偶发"Document import failed, please try again later"。已知坑。

解决：*先 import 一个两行空壳拿 doc_id，再用 Block API 往里灌块*。

```python
from scripts.md_to_feishu_blocks import convert
blocks = convert(md)

# 1. list + 2. batchDelete 全部 existing children
# 3. 按 50 个/批 create
for i in range(0, len(blocks), 50):
    batch = blocks[i:i+50]
    post(f"{API}/docx/v1/documents/{DOC_ID}/blocks/{DOC_ID}/children?document_revision_id=-1",
         {"children": batch, "index": i}, token)
```

这套 flow 实现在 `scripts/sync-skill-to-feishu.py`，可复用到任意 markdown → 飞书原地更新场景。SKILL.md 本身也是这么同步到飞书 spec 文档的。

### 3.8 IM 通知 · 交互卡片 + 富文本兜底

`im_v1_message_create` + `msg_type: "interactive"` 可以发带按钮的卡片。坑：某些 emoji 组合会触发 Feishu 校验失败（`invalid message content`），这时换成 `msg_type: "post"` 富文本格式能绕过。

```json
{
  "receive_id": "oc_xxx",
  "msg_type": "post",
  "content": "{\"zh_cn\":{\"title\":\"...\",\"content\":[[{\"tag\":\"text\",\"text\":\"...\"}],[{\"tag\":\"a\",\"href\":\"https://...\",\"text\":\"阅读完整文档\"}]]}}"
}
```

---

## 四、双版本输出（日报 + 公众号）

最新加的玩法。日常的 ai-daily-report skill 跑完后，自动触发 wechat-article skill 做一份**压缩版**给微信公众号用。

| 维度 | ai-daily-report | wechat-article |
|---|---|---|
| 主题 | 全领域综合日报 | 单一主题专题文 |
| 字数 | 5000-15000 | 2500-3500 |
| 阅读时长 | 10-15 分钟 | 5 分钟 |
| 表格 | markdown 表格 OK | 必须转图（`generate-table-image.py`） |
| 语气 | 中立 + 明确判断 | 有态度 + 推荐必须明确 |
| 图片 | 5+ 张 | 6-10 张（含表格图） |
| 审核 Agent | 完整 5 个 | 精简 4 个 |

**为什么表格要转图**：微信公众号对 markdown table 渲染极差（字挤成一团、列对不齐），转成图就不会有这些问题。而且公众号读者习惯在手机上读，图比表格更友好。

`scripts/generate-table-image.py` 用 PIL 渲染干净表格：标题 + 列头 + 斑马纹 + 获胜列高亮 + 圆角边框 + WenQuanYi 中文字体。产出 1536×N 的 PNG，直接粘进公众号编辑器。

示例：

```bash
python3 scripts/generate-table-image.py \
  --title "OPPO vs REDMI 核心规格" \
  --subtitle "2026 国产旗舰两极" \
  --columns "核心规格,Find X9 Ultra,K90 Max" \
  --rows "起售价|7499 起|3499 起" \
         "SoC|骁龙 8 至尊版 Gen5|天玑 9500 + D2" \
         ... \
  --output content/2026-04/oppo-vs-redmi-specs.png
```

---

## 五、定时调度 · systemd timer

因为 Linux 没 crontab 或用户嫌 cron 语法难读，我用 systemd user timer：

`~/.config/systemd/user/ai-daily-report.service`：

```ini
[Unit]
Description=AI Daily Report Generator

[Service]
Type=oneshot
WorkingDirectory=/home/can-u-claw/repos/daily-report
ExecStart=/bin/bash scripts/generate-daily-report.sh
StandardOutput=append:%h/repos/daily-report/logs/cron.log
StandardError=append:%h/repos/daily-report/logs/cron.log
```

`~/.config/systemd/user/ai-daily-report.timer`：

```ini
[Unit]
Description=Trigger AI Daily Report at 08:03

[Timer]
OnCalendar=*-*-* 08:03:00
Persistent=true

[Install]
WantedBy=timers.target
```

启用：

```bash
systemctl --user enable --now ai-daily-report.timer
systemctl --user status ai-daily-report.timer
```

日志在 `logs/cron.log`，每天 08:03 自动跑，失败自动重试 3 次（`generate-daily-report.sh` 内部循环）。

---

## 六、复现步骤（给想自己搭一份的人）

**Step 1 · 准备账号 + token**

- GitHub：两个仓 `daily-report` + `daily-report-images`（公开，用 raw.githubusercontent.com 做图床）
- 飞书：注册开发者应用，拿 `app_id` + `app_secret`，开通 docx / im / drive 权限
- Azure OpenAI：拿一个 gpt-image-1.5 部署的 endpoint + key
- Claude Code：安装 + Max 订阅（能跑长任务）
- lark-mcp：配置到 `~/.claude.json` 的 MCP servers

**Step 2 · 克隆仓库骨架**

```bash
git clone git@github.com:wangcansunking/daily-report.git
git clone git@github.com:wangcansunking/daily-report-images.git
```

**Step 3 · 创建 `.env`**

```
AZURE_OPENAI_IMAGE_ENDPOINT=https://xxx.cognitiveservices.azure.com/openai/deployments/gpt-image-1.5/images/generations?api-version=2024-02-01
AZURE_OPENAI_IMAGE_API_KEY=...
IMAGE_REPO_PATH=/home/you/repos/daily-report-images
IMAGE_REPO_BASE_URL=https://raw.githubusercontent.com/yourname/daily-report-images/master
```

**Step 4 · 复制 SKILL.md 到全局 skill**

```bash
mkdir -p ~/.claude/skills/ai-daily-report
cp docs/ai-daily-report-skill.md ~/.claude/skills/ai-daily-report/SKILL.md
```

可选：同样复制 `wechat-article` skill。

**Step 5 · 首次手动跑一次**

```bash
claude
# 在 Claude Code 里说：
> 生成今天的 AI 日报
```

Claude 会自动 invoke skill → 执行全流程。首次通常会遇到几个小问题（比如没权限、token 过期等），修复后以后就稳定了。

**Step 6 · 开定时**

```bash
cp scripts/ai-daily-report.service ~/.config/systemd/user/
cp scripts/ai-daily-report.timer ~/.config/systemd/user/
systemctl --user enable --now ai-daily-report.timer
```

---

## 七、常见坑

- **SKILL.md 改完忘了同步飞书 spec**：用 `scripts/sync-skill-to-feishu.py`（内部走 Block API list → batchDelete → create in batches of 50）。
- **封面字被截断**：已修复。`generate-cover.py` 用像素宽度测量 + 词边界保护 + 字号自动缩小。任何英文词不会从中间切。
- **Feishu `docx_builtin_import` 对大 markdown 失败**：用"空壳 + Block API 填充"的 fallback。
- **Ollama 默认拉低量化**：与本文无关但相关——`ollama pull qwen3.6:35b-a3b` 默认给你 Q2/Q3，显式指定 `q4_K_M` 或 `q5_K_M`。
- **图片外链会挂**：硬性规则，所有图片 MUST 走 daily-report-images。发现外链 → 审核 Agent 的"图片"那条会 ❌。
- **Writer Agent 改事实**：硬约束"数字 / 引语 / 链接严禁改动"。抽查几次，Writer 偶尔会违反；发现就在 prompt 里加更严格限制。

---

## 八、关于内容质量的几条死规矩

1. **宁可少放链接，不放 404 链接**——读者点开一个 404 比不看更差
2. **不要"值得关注""重磅"等空套话**——具体判断或不说
3. **编辑说必须落到行动**——"今天 / 本周你应该做什么"三选一明确
4. **不骑墙**——对每件事表明立场，哪怕可能错
5. **数字先于解释**——先出 600 亿 / 58.6 分 / 4 小时破纪录，再说为什么

---

## 九、下一步

- [ ] 公众号版自动上传到 mdnice → 复制到微信后台（目前需手动搬）
- [ ] 封面升级到 gpt-image-2（2K 原生 + 文字渲染大幅改善）
- [ ] 增加"数据真实性"第二层校验：关键数字让另一个 LLM 独立 fact-check 一次
- [ ] 支持多语言（英文 / 日文版本）
- [ ] 周度/月度自动归档索引

---

## 十、参考资料

- **Claude Code 文档**：[code.claude.com](https://code.claude.com/docs/en)
- **飞书开放平台**：[open.feishu.cn](https://open.feishu.cn)
- **mdnice 公众号编辑器**：[mdnice.com](https://mdnice.com)
- **Anthropic Skills 介绍**：[anthropic.com/engineering/skills](https://www.anthropic.com/engineering/skills)

---

*Last updated: 2026-04-22 · 本文本身就是用这套系统里的 Writer Agent 润色过的。*
