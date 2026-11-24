---
title: 用 Codex、Obsidian 和 GitHub 搭一个第二大脑
date: '2026-11-24'
weekday: 星期二
slug: codex-obsidian-github-second-brain-2026-11-24
category: workflow
description: 把原始笔记和生成稿分开，用 Obsidian 记录、Codex 整理、GitHub 同步与审阅。
batch: special-codex-obsidian-github-brain
track: deep-dive
cover_style: editorial-still-life
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/codex-obsidian-github-second-brain-2026-11-24.png
---

# 用 Codex、Obsidian 和 GitHub 搭一个第二大脑

把 Obsidian 笔记交给 Codex 整理，最让人犹豫的是它会不会顺手改掉原话。把笔记库放进 Git，再为机器生成的内容预留目录，就能先看修改、再决定留什么。下面从两台电脑和一个私有仓库开始。

## 要点速览

- Obsidian 负责记录、链接和检索，Codex 提交整理建议，GitHub 保留已上传的版本。
- 将原始材料与生成稿分开，先让 Codex 复述规则，再授权它写入指定目录。
- 换设备先拉取，离开前提交并推送。遇到分叉，保留双方版本再逐段核对。
- 私有仓库控制访问范围，敏感原件和凭据放在笔记库之外。

## 先让三个工具各做一件事

| 工具 | 日常职责 | 你要检查什么 |
| --- | --- | --- |
| Obsidian | 在本地 Markdown 库里记录、建链接和查笔记 | 原文、附件与链接是否齐全 |
| OpenAI Codex | 在选定的工作目录里归纳材料、提议链接和检查笔记 | 摘要是否忠于原文，改了哪些文件 |
| GitHub | 托管私有 Git 仓库、查看历史和审阅拉取请求（PR） | 哪些改动进入主分支 |

Obsidian 的笔记库（vault）是一个本地文件夹。先安装 Git 并完成认证，在 GitHub 建私有仓库，用 README 初始化后克隆到电脑。把克隆目录作为 Obsidian 的现有笔记库打开，第二台电脑也从同一个远端克隆。

早期只需要六个目录。原文和附件分开放，同一份材料始终保留一个可定位的入口；读书笔记、项目记录等人写的内容统一放在 `notes/`。

- `inbox/` 存放临时摘记。
- `sources/` 存放经授权且适合入库的原始材料和逐字记录。
- `notes/` 存放人工维护的笔记。
- `generated/` 存放Codex 写出的待审稿。
- `templates/` 存放笔记模板。
- `attachments/` 存放图片和附件。

启用核心插件 Templates 并指向 `templates/`。模板顶部用两行 `---` 包住三项 YAML 属性，每项单独一行：`status: draft`、`created: {{date}}` 和 `source: ""`。插入后补来源，Codex 直接写文件时填实际日期。

在「文件与链接」设置附件位置为 `attachments/`。Git 只同步已提交的文件，其余空目录可在第二台电脑上按需建立。

启用核心插件 Bases，新建一个表格视图，筛选 `generated/` 里状态为 `draft` 的笔记，显示日期和来源。每行对应一个文件。审完再更新状态，下一次打开就知道哪些稿件还有待确认。

![Obsidian Bases 表格视图示例](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/codex-brain-source-bases.png)

*图片来源：Obsidian 帮助文档《Bases 简介》书单表格示例，2026-11-24 查阅。*

## 让 Codex 先复述写入范围

安装并登录 Codex CLI 后，在库根目录运行 `codex`。也可在 ChatGPT 桌面应用选择本地项目，再用 Codex 新建会话。可用性取决于支持地区、账号和组织设置。

首次任务先请 Codex 复述规则，确认工作目录和允许读写的范围，再给一份脱敏样稿。

把长期约定写入库根目录的 `AGENTS.md`。Codex 启动时从项目根目录到当前工作目录查找相关指令。修改约定后开个新会话，再确认它加载了最新版本。规则可以直接写成下面这样。

> 只读取任务点名的材料和必要的索引，只写 `generated/`。保留原文件、附件和 YAML 属性。为每条重要结论记录来源文件和小节，推测标成「待核」。

> 链接前确认目标存在。链接原始记录时使用 `[[sources/访谈记录]]` 这类库内路径。同名笔记写完整路径，缺失目标列到待办。

> 交付时列出新增文件、已核实的链接和疑点。移动、重命名、批量改写、提交和推送留给人来决定。

以虚构访谈为例，把记录整理为三条发现。原文只说「找入口花了时间」，摘要就保留操作和语境，别擅自升级成「大多数用户找不到入口」。

把新增的链接点开核对，再检查生成稿有没有把受访者的意见写成团队结论。

跑通几次后，再把「读来源、提炼结论、核对链接、报告变更」做成技能（skill），放在 `.agents/skills/vault-review/SKILL.md`。用 `name` 和 `description` 写清用途，规则文件继续承担读写约定。

规则属于给模型的工作指令。需要限制写入时，先用只读沙箱出方案，再将脱敏样稿和必要索引复制到独立目录生成草稿，最后人工检查差异。把网页摘录和转录中的指令当作材料阅读。

## 两台电脑轮换，先同步再编辑

切到另一台电脑时，先确认当前分支为 `main`、上游为 `origin/main`。用 `git status` 检查工作区干净，再运行 `git pull --ff-only`。

离开前用 `git diff` 看未暂存改动，新文件另行打开检查。按文件暂存，核对后提交并推送。等推送成功再换设备。

习惯在 Obsidian 里操作时，可装社区插件 Git（项目名 Obsidian Git）。桌面端使用本机 Git 和已配置的认证。

插件提供提交、拉取、推送和查看差异的界面，也能定时提交并同步。建议先手动跑通一轮，确认当前分支和待提交文件，再决定是否开启定时同步。

维护者将移动端支持标为实验性质，先把双向工作流跑在桌面。

![Obsidian Git 冲突编辑与来源控制面板](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/codex-brain-source-git-conflict.png)

*图片来源：Obsidian Git 维护者仓库的冲突处理界面，2026-11-24 查阅。*

若两台电脑都已提交，`--ff-only` 可能因历史分叉而拒绝拉取。暂停定时同步，确认工作区干净，执行 `git fetch origin`，用 `git log --left-right --oneline HEAD...origin/main` 看两边独有提交，再用 `git -c merge.ff=true merge origin/main` 合并。

遇到冲突先看 `git status`。逐段核对本机与远端内容，保留双方有效信息，清掉冲突标记；同一属性只保留一个键，删改冲突则逐文件决定保留还是删除。

用 `git add` 暂存保留的文件，确认删除的用 `git rm`。检查没有未合并路径后提交，再推送。需要暂缓时，在合并未完成前运行 `git merge --abort`。

最常见的噪音是两台机器不断改写窗口布局。首次提交前可忽略 `.obsidian/workspace.json` 和 `.obsidian/workspaces.json`。其余设置挑选后再共享。让同一个 vault 只由一套同步机制负责，避免云盘和 Git 同时搬动文件。

## 把人工审阅和自动检查放在提交之后

小改动按日提交，大批整理单开分支。先确认工作区干净，再从已同步的主分支用 `git switch -c review/2026-11-24`，让 Codex 生成本周草稿。用 `git status --short` 确认新增文件，逐个检查，提交后用 `git push -u origin review/2026-11-24` 推送，在 GitHub 开 PR。

在 PR 的 Files changed 页面，先看是否碰了原文，再到检出该分支的 Obsidian 中核对摘要出处、点开链接。个人库也可以把 PR 当自己的改动清单；多人共享时请同事审阅。合并后，让两台电脑都回到主分支并拉取。

![GitHub PR 的 Files changed 与 Review changes 入口](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/codex-brain-source-pr-review.png)

*图片来源：GitHub Docs《审阅拉取请求中的拟议更改》界面，2026-11-24 查阅。*

规则稳定后，再在 `.github/workflows/` 增加由 `pull_request` 触发的 Actions 检查。用自编脚本查待审笔记的必填属性和原文目录的改动，再考虑查失效链接。链接检查要识别库内路径、别名和标题锚点，遇到同名目标单独报歧义。

工作流只需读取仓库时，给 `GITHUB_TOKEN` 配置 `contents: read`。检查失败时只报脱敏路径和错误类型，别打印笔记内容。

![GitHub Actions 工作流运行步骤和日志](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/codex-brain-source-actions.png)

*图片来源：GitHub Docs《GitHub Actions 快速入门》的 push 示例运行界面，2026-11-24 查阅。*

## 哪些内容留在这套系统之外

私有仓库仍会把已推送内容存到 GitHub。给 Codex 处理前另想一遍模型的数据边界：用已脱敏的副本，按账户和组织的数据控制配置使用。合同原件、私人日记、API 密钥、访问令牌和凭据都放在库外。

提交前用 `git diff --cached` 检查已经暂存的内容，新附件也要打开看。连同旧提交一起考虑：把文件加进 `.gitignore` 不会抹去历史。若凭据已提交，先撤销或轮换，再按 GitHub 指引处理历史和已有副本。

一套能长期用的第二大脑，是人能找到原文、能审阅 Codex 的每次整理，也能从 Git 找回已经提交的版本。

## 来源

- [OpenAI｜Codex CLI](https://learn.chatgpt.com/docs/codex/cli)
- [OpenAI｜ChatGPT 桌面应用](https://learn.chatgpt.com/docs/app)
- [OpenAI｜AGENTS.md 项目指令](https://learn.chatgpt.com/docs/agent-configuration/agents-md)
- [OpenAI｜技能的创建与加载](https://learn.chatgpt.com/docs/build-skills)
- [OpenAI｜Codex 沙箱与权限](https://learn.chatgpt.com/docs/agent-approvals-security)
- [Obsidian｜数据存储](https://obsidian.md/help/data-storage)
- [Obsidian｜内部链接](https://obsidian.md/help/links)
- [Obsidian｜笔记属性](https://obsidian.md/help/properties)
- [Obsidian｜Templates](https://obsidian.md/help/plugins/templates)
- [Obsidian｜Bases](https://obsidian.md/help/bases)
- [Obsidian Git｜维护者文档与界面](https://github.com/Vinzent03/obsidian-git)
- [Git｜git pull](https://git-scm.com/docs/git-pull)
- [Git｜git merge](https://git-scm.com/docs/git-merge)
- [GitHub Docs｜仓库与可见性](https://docs.github.com/en/repositories/creating-and-managing-repositories/about-repositories)
- [GitHub Docs｜审阅 PR](https://docs.github.com/en/pull-requests/how-tos/review-pull-requests/reviewing-proposed-changes-in-a-pull-request)
- [GitHub Docs｜命令行处理冲突](https://docs.github.com/en/pull-requests/how-tos/merge-and-close-pull-requests/resolving-a-merge-conflict-using-the-command-line)
- [GitHub Docs｜Actions 快速入门](https://docs.github.com/en/actions/get-started/quickstart)
- [GitHub Docs｜工作流语法和权限](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#permissions)
- [GitHub Docs｜删除仓库中的敏感数据](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
- [GitHub Docs｜Actions 的 pull_request 事件](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows#pull_request)
- [GitHub Docs 图片出处与 CC BY 4.0 许可](https://raw.githubusercontent.com/github/docs/main/LICENSE)
- [Obsidian Git｜社区插件清单](https://github.com/Vinzent03/obsidian-git/blob/master/manifest.json)
- [OpenAI｜数据使用与控制](https://help.openai.com/en/articles/5722486-how-your-data-is-used-to-improve-model-performance)
