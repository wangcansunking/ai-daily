---
title: "Matt Pocock 把全套 21 个 .claude/skills 私房菜放出来：1.96 万⭐、1641 fork"
date: 2026-04-26
tags: [Claude Code, Skills, AI Coding, Matt Pocock, TypeScript]
cover: 2026-04-26-mattpocock.png
description: "Matt Pocock 把自己 .claude/skills 目录原样开源，21 个独立 skill。GitHub Trending Daily #2 / 19,605 ⭐ / 1,641 fork / 单日 +857 ⭐。截至 2026-04-26 国内 36氪/量子位/机器之心/虎嗅/CSDN/掘金 未检索到中文报道，auto-research arbitrage 选题。"
---
# Matt Pocock 把全套 21 个 .claude/skills 私房菜放出来：1.96 万⭐、1641 fork

![cover](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-26/mattpocock-skills-cookbook/2026-04-26-mattpocock.png)

`mattpocock/skills` 在 4 月 25 日 20:03 UTC 时 GitHub 上 **19,605 ⭐ / 1,641 fork / 15 个 open issue**，当晚 GitHub Trending Daily 排名 #2、单日新增约 857 ⭐。仓库描述写得直白：

> My personal directory of skills, straight from my .claude directory.

翻译：作者 Matt Pocock 把自己的 `.claude/` skills 目录原样开源——21 个独立的 skill，每个都是一份 `SKILL.md` + 可选的 sub-doc 和 script。读完它们，等于看了一个产业级 TypeScript 教师怎么编排 Claude Code 的工作流。

## Matt Pocock 是谁

GitHub `users/mattpocock` API 当晚返回的字段：

| 字段 | 值（2026-04-25 20:03 UTC） |
|---|---|
| `name` | Matt Pocock |
| `bio` | TypeScript wizard. Building Total TypeScript - a comprehensive, production-grade TypeScript course.\r\n\r\nEx-Vercel, Stately. |
| `blog` | https://totaltypescript.com |
| `twitter_username` | mattpocockuk |
| `followers`（GitHub） | 9,239 |
| `public_repos` | 218 |
| `created_at` | 2017-05-02T09:05:18Z |

简单概括：英国 TypeScript 教学界的"业内最熟悉的脸"之一，做 *Total TypeScript* 这门付费课程多年，曾在 Vercel 和 Stately 任职，X 上的 ID 是 `@mattpocockuk`。他的内容主战场不是 Claude Code，而是 TypeScript 类型体操；这次开源 skills 是把"我每天在 .claude 里怎么干活"的私房菜公开。

## 这次为什么火

仓库 `created_at` 是 2026-02-03——也就是说 mattpocock/skills 不是一个一夜爆红的项目。它已经维护两个半月，慢慢累积。但 4 月 25 日单日 +857 ⭐ 这个曲线有两个直接推手：

1. **Anthropic 推出 *Skills* 概念的官方 spec 之后**（把 prompt + 资源打包成 `SKILL.md`、用 description 字段做触发词）——这套规范公开后，社区开始批量分享自己的 .claude 目录。
2. **Matt 自己有 X 流量基本盘**——9.2k GitHub followers + X 上 `@mattpocockuk`。一旦他公开 push 一条新 skill，就会在英文 TypeScript 圈里直接出圈到 AI Coding 社区。

仓库根目录除了 21 个 skill 子目录，只有 README.md（3,509 bytes）和 LICENSE（1,068 bytes）——总仓库大小只有 45 KB。这说明它不是"工程师在搭某个工具"，而是"教师在分享自己的 cheat sheet"——所有内容都是 markdown 文档。

## 21 个 skill 全表

`gh api repos/mattpocock/skills/contents/` 返回的目录列表，共 21 个：

```
caveman                          improve-codebase-architecture   tdd
design-an-interface              migrate-to-shoehorn             to-issues
domain-model                     obsidian-vault                  to-prd
edit-article                     qa                              triage-issue
git-guardrails-claude-code       request-refactor-plan           ubiquitous-language
github-triage                    scaffold-exercises              write-a-skill
grill-me                         setup-pre-commit                zoom-out
```

README 把其中 16 个分类入册，剩下 5 个（`caveman`、`domain-model`、`github-triage`、`qa`、`zoom-out`）是 Matt 还在打磨、暂未写进首页清单的。

入册的 16 个分四类：

![16 个分类](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-26/mattpocock-skills-cookbook/mattpocock-skills-table.png)

每个 skill 在 README 里都给了一行 npx CLI 安装命令：

```bash
npx skills@latest add mattpocock/skills/<name>
```

例如：

```bash
npx skills@latest add mattpocock/skills/tdd
npx skills@latest add mattpocock/skills/git-guardrails-claude-code
```

`skills` 这条 CLI 是 Anthropic 生态的第三方工具，用来把社区 skill 一键拷到自己的 `.claude/` 下。这条命令的存在是 Matt 之所以能"全开源"的一个重要前提——没有 CLI，社区就只能 `git clone` + `cp -r`，门槛会高很多。

## TDD skill 深拆：vertical slices via tracer bullets

`tdd/SKILL.md` 是这个仓库里最长的一份（4,211 bytes），值得拆开看。它的 frontmatter 是这样写的：

```
---
name: tdd
description: Test-driven development with red-green-refactor loop. Use when user wants to build features or fix bugs using TDD, mentions "red-green-refactor", wants integration tests, or asks for test-first development.
---
```

description 字段是 *触发词*——用户在对话里提到 "TDD" / "red-green-refactor" / "integration tests" / "test-first development" 中任意一个，Claude Code 就会加载这个 skill。这是 Anthropic Skills 设计的核心：**触发词具体到能反向回答 *"什么场合该用这个 skill"***。

skill 的核心论点反 *horizontal slicing*：

> DO NOT write all tests first, then all implementation. This is "horizontal slicing" — treating RED as "write all tests" and GREEN as "write all code."

Matt 给出三条具体反对理由：

> - Tests written in bulk test *imagined* behavior, not *actual* behavior
> - You end up testing the *shape* of things (data structures, function signatures) rather than user-facing behavior
> - Tests become insensitive to real changes — they pass when behavior breaks, fail when behavior is fine

正确做法是 *vertical slices via tracer bullets*：每写一个 test 就写一段实现，每段实现都让上一个 test 变绿，循环。

```
WRONG (horizontal):
  RED:   test1, test2, test3, test4, test5
  GREEN: impl1, impl2, impl3, impl4, impl5

RIGHT (vertical):
  RED→GREEN: test1→impl1
  RED→GREEN: test2→impl2
  RED→GREEN: test3→impl3
  ...
```

Workflow 三步：

1. **Planning**：跟用户确认 interface 怎么变、哪些 behavior 该测、识别 *deep modules* 机会、设计 testable interface、列要测的 behavior（不是 implementation step）、**让用户批准计划**。
2. **Tracer Bullet**：写一个 test 验证一件事，让它从红变绿——证明端到端通路。
3. **Incremental Loop**：每次再加一个 behavior、写 test → 红 → 写最少代码 → 绿 → 重复。

skill 还引用了 5 个 sub-doc：`deep-modules.md`、`interface-design.md`、`mocking.md`、`tests.md`、`refactoring.md`——这是 Anthropic Skills "progressive disclosure" 的标准模式：主 SKILL.md 写约束，遇到具体子问题再加载 sub-doc。

这个 skill 对国内开发者最大的启发在于：**让 Claude Code 不要一次写一坨**。很多人用 Cursor / Claude Code 的痛点都是"Claude 一上来给我生成 200 行代码、其中 100 行是想象中的"——TDD skill 用 description 触发词强行把这个流程拗成 *一个 test 一段代码循环*。

## git-guardrails 深拆：PreToolUse hook 拦危险 git 命令

`git-guardrails-claude-code/SKILL.md` 是 2,312 bytes，目标更窄——给 Claude Code 装一道护栏，拦截危险 git 命令。屏蔽列表：

- `git push`（含 `--force` 等所有变种）
- `git reset --hard`
- `git clean -f` / `git clean -fd`
- `git branch -D`
- `git checkout .` / `git restore .`

被拦截时 Claude Code 会看到一条消息说"你没有权限运行这些命令"——这是对 LLM agent 的一种 *prompt injection 防御*：直接告诉它这条不通。

安装分两步：

1. **选 scope**：项目级（`.claude/settings.json`）还是全局（`~/.claude/settings.json`）。
2. **拷脚本 + 改 settings**：把 bundled 的 `scripts/block-dangerous-git.sh` 拷到 `.claude/hooks/` 或 `~/.claude/hooks/`，`chmod +x`，再在 `settings.json` 加 PreToolUse hook：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

`PreToolUse` 是 Claude Code 的 hook 事件——每次 agent 准备调 *Bash* 工具时，先跑这个脚本；脚本退出码非 0 就拦下来。`matcher: "Bash"` 是工具过滤；只对 Bash 工具生效，不影响 Read / Write / Edit。

skill 的真正价值不是脚本本身（10 分钟自己也能写一份）——而是把"我用了三个月才意识到 Claude 会偷偷 `git push --force`"这个知识 *沉淀成可分发的 skill*。

## 我推荐你最先抄的 5 个

![Top 5](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-04-26/mattpocock-skills-cookbook/mattpocock-top5.png)

如果时间紧，先把这 5 个 add 到自己的 `.claude/`：

1. **`tdd`**（4,211 字节）——强制 vertical-slice 红绿重构。让 Claude 不再一次给你生成 200 行代码。
2. **`git-guardrails-claude-code`**（2,312 字节 + scripts 目录）——PreToolUse hook 拦截危险 git 命令。
3. **`grill-me`**——对你的方案做穷尽式追问。每次新 feature 你自嗨时让 Claude 把决策树都问一遍。
4. **`to-prd`**——把当前对话上下文转成 PRD 提 GitHub issue，**没有问卷环节**——只综合你已经讨论过的内容。这是 Matt 的设计哲学：不让 Claude 反过来再问你 10 个问题。
5. **`write-a-skill`**（3,056 字节）——按渐进披露 + bundled 资源结构出新 skill。学完它，你就能自己写 skill 而不是抄。

## 怎么搬到自己的 .claude/

### 路径 1：`npx skills@latest add` 单条加

最轻：

```bash
npx skills@latest add mattpocock/skills/tdd
npx skills@latest add mattpocock/skills/git-guardrails-claude-code
npx skills@latest add mattpocock/skills/to-prd
```

适合"挑选几个"。

### 路径 2：git clone 整套

最快：

```bash
cd ~/.claude/skills/
git clone https://github.com/mattpocock/skills.git mattpocock
```

每个 skill 仍可被 Claude Code 触发（按 description 字段），只是路径变成 `mattpocock/<name>`。

### 路径 3：git submodule 跟着 Matt 更新

如果你想在他更新 SKILL.md 时拉一下：

```bash
cd ~/.claude/skills/
git submodule add https://github.com/mattpocock/skills.git mattpocock
```

之后 `git submodule update --remote` 拿最新版。

注意：Matt 的 README 里详写了 16 个 skill；剩下的 5 个（`caveman` / `domain-model` / `github-triage` / `qa` / `zoom-out`）暂未文档化。如果你 clone 整套，这 5 个目录里有 SKILL.md 的会被 Claude Code 加载——是否触发要看每个的 description 字段写得多好。

## Matt Pocock 的 skill 设计哲学

读完这 16 份 SKILL.md，能反向归纳出 4 条贯穿的设计原则：

### 1. description 字段是触发词，越具体越好

`tdd` 的 description 列了 4 个触发词："TDD" / "red-green-refactor" / "integration tests" / "test-first development"。这些词是用户*真的会说出口*的 phrase。反例就是写"useful for testing things"——那种写法 Claude 永远拿不准什么时候该 load。

### 2. SKILL.md 主文档写约束、子文档写细节

`tdd/SKILL.md` 4,211 字节、列了 anti-pattern 和 workflow，剩下的 deep modules、interface design、mocking、refactoring 各自一个 sub-doc。Claude 在主 doc 看到 "see [tests.md](tests.md) for examples" 时再去加载——这是 Anthropic 文档里反复强调的 *progressive disclosure*：不预加载所有细节，需要时再读。

### 3. 输出形态尽量 *归档*

`to-prd` 不是给你写一份 PRD 完事——它把 PRD 提交成 *GitHub issue*。`to-issues` 也是把 plan 拆成可独立认领的 *GitHub issues*。`triage-issue` 同理。这些 skill 的产出都是 *持久 artifact*，不是聊天记录里的一段。这跟昨天那篇 Kevin Lynagh 的"scope creep 守恒定律"是对仗的——不归档的 LLM 工作量等于零。

### 4. 配可执行 script 而不是 prompt 里说"please run"

`git-guardrails-claude-code` 把 `block-dangerous-git.sh` 当成 bundled resource。skill 让 Claude *安装* 这个脚本到 `.claude/hooks/`，而不是每次让 Claude 现写一段 bash。这是把"重复的工程动作"沉淀成可分发的代码资产。

## 国内开发者怎么用

1. **国内 git 镜像加速**：`git clone https://github.com/mattpocock/skills.git` 在国内常常会 hang。换 `git clone https://gitclone.com/github.com/mattpocock/skills.git` 或 `https://hub.fastgit.xyz/mattpocock/skills.git` 这类镜像更稳。
2. **`npx skills@latest`**：这条命令拉的是 npm 上的 `skills` 包，国内 npm 用淘宝 / cnpm 镜像后体感差不多。
3. **SKILL.md 中文化**：description 字段的触发词最好留英文（用户对话里习惯混用），但 SKILL.md 的 workflow / examples 部分翻译成中文对国内开发者更友好。Matt 的 license 是 MIT，自由翻译再二次发布是合规的。
4. **跟 OpenClaw / Claude Code 一起用**：如果你在用 OpenClaw 跑 DeepSeek / Qwen，这些 skill 默认是给 Claude Code 写的，但 description 字段的触发词机制大多数 LLM 都能识别——能不能完美 follow workflow 取决于具体 LLM 的 *指令遵循度*。建议先在 Claude Code 上跑一遍验证流程，再迁移到国产 LLM。

## 不要跟昨天那篇混淆

我们昨天写过一篇 *Claude Code 生态四件套*，里面拆了 Anthropic postmortem + `context-mode` + `claude-context` + `free-claude-code`。那是一篇"Anthropic 自己事故 + 社区魔改工具"的故事——讲的是社区如何反向修补 Claude Code 的不足。

`mattpocock/skills` 是另一回事——它是 *个人最佳实践仓*。Matt 没有修补 Claude Code 的 bug，他在演示"一个产业级开发者怎么把 Claude Code 用对"。这两篇是互补的：昨天那篇讲生态架构，今天这篇讲个人工作流。

## 看点：未来一周

1. **simonw / swyx / karpathy 会不会跟进开自己的 `.claude` 目录**？以英文圈对"大V 公开工作流"的喜好程度，这条信息差不会持续超过一周——预计这周末就会有第二位大V 公开他自己的 skills。
2. **国内有没有人会做 mattpocock/skills 的中文 fork**？翻译 + 添加国产 LLM 适配（OpenClaw、Codex CLI 接 DeepSeek）会是一个高 arbitrage 的项目。
3. **Anthropic 自己会不会出 *官方 skill marketplace***？Matt 这种"个人 skill 仓"火起来后，Anthropic 把它做成 marketplace 的可能性会上升——4 月 25 日 GitHub Trending 已经有 `ComposioHQ/awesome-codex-skills`（Codex 生态）和 `davila7/claude-code-templates`（25,292 ⭐）这两个邻居项目。

## 一句收尾

Matt Pocock 没有写新工具——他只是把"我每天怎么用 Claude Code"的肌肉记忆 commit 进了 GitHub。1.96 万人觉得这值得 star。今天读完，明天就能给你的 Claude 装上一套被产业级开发者验证过的工作流——这是 *AI Coding 时代* 最便宜的一次知识转移。

---

*所有数字来自 2026-04-25 20:03 UTC 的 GitHub API 实查；mattpocock/skills 的 stars/forks 字段会随时间变化。Matt Pocock 当晚 GitHub followers 数为 9,239（不是 X 粉丝；X 粉丝口径 GitHub 不返回）。*
