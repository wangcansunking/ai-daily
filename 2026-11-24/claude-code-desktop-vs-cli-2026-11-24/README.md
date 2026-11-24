---
title: Claude Code Desktop 与 CLI，何时用哪一个
date: '2026-11-24'
weekday: 星期二
slug: claude-code-desktop-vs-cli-2026-11-24
category: tool
description: 比较桌面审查、终端自动化、并行隔离和远端执行，给出两端搭配的工作流。
batch: special-claude-code-desktop-vs-cli
track: deep-dive
cover_style: data-visual
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/claude-code-desktop-vs-cli-2026-11-24.png
---

# Claude Code Desktop 与 CLI，何时用哪一个

![青色与琥珀色粒子轨迹汇聚](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/claude-code-desktop-vs-cli-2026-11-24.png)

截至 2026 年 11 月 24 日，Claude Code Desktop 指 Claude 桌面应用中的 Code 标签页，Claude Code CLI 则是从终端启动的 `claude`。两者共用底层引擎，真正影响选型的是人在哪里审查改动、任务在哪里执行、输出要交给谁。

## 要点速览

- **要逐行看改动、边改边验界面**，优先用 Desktop 的 Changes 面板和本地预览。
- **要把任务串进脚本或 CI**，用 CLI 的 `-p` 和结构化输出，把结果交给下游程序。
- **要在同一仓库并行改文件**，两端都能用 Git worktree。分开会话后，还要分开工作目录。
- **要离开当前终端继续运行**，先区分 CLI 本机后台、Desktop 的 SSH 远程进程和云端会话。

## 同一个引擎，两种工作台

Anthropic 在 2025 年 11 月公布桌面端可并行运行 Claude Code 会话。2026 年 4 月的改版加入会话侧栏、可拖动面板、内置终端和文件编辑器，把原本散落在几个窗口的操作放到同一工作台。

Desktop 开始会话前可选执行环境、项目目录和权限模式。

本地会话在所选目录工作，SSH 会话在远端主机工作，Cloud 会话默认用 Anthropic 托管环境。本地执行描述的是工具和文件所在位置。

CLI 在当前目录启动交互会话，也可用 `claude -p` 执行一次请求后返回。

所谓终端快，主要指少切窗口、命令可复用、结果能直接进管道。两种界面本身不足以判断模型响应哪边更快。

![Claude Code 产品页的终端模式视觉](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/source-claude-code-terminal.jpg)

*图片来源：Anthropic Claude Code 产品页，2026-11-24 查阅。*

| 任务 | Desktop | CLI |
| --- | --- | --- |
| 审查改动 | Changes 逐文件看 diff，可逐行留言 | `/diff` 交互查看，可配合 `git diff` |
| 验证页面 | 本地 Browser 面板预览开发服务器 | 在 shell 跑项目已有的构建和测试 |
| 同仓并行 | 新会话开启 Worktree，侧栏切换 | `claude --worktree <名称>` |
| 跟踪后台 | Background tasks 查看当前会话内任务 | `--bg` 启动后台会话，`claude agents` 查看 |
| 固定流程 | 交互选择环境与权限 | `-p --output-format json` 接脚本或 CI |
| 自有远端 | Desktop 经 SSH 连接主机 | SSH 登录后在主机上运行 `claude` |

## 审查改动，还是把输出接入工具链

在 Desktop 打开 **Changes**，可以逐文件看代码差异。把鼠标移到行号旁点加号，写下反馈，再一次发给 Claude。比如在校验函数旁加一句“保留空值处理”，改完继续看新 diff。

![Desktop Changes 中按文件显示的代码差异](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/source-desktop-changes.png)

*图片来源：Anthropic 桌面端预览与审查公告，2026-02-20。*

做前端任务时，可以把 Changes 与本地 **Browser** 面板并排。让 Claude 启动开发服务器，再检查报错状态和空数据页面。页面验证、代码审查和测试分别回答不同问题，交付前可逐项验收。

Desktop 可用已安装并登录的 GitHub CLI（`gh`）跟踪拉取请求（PR）的持续集成（CI）状态。现行指南称自动修复和自动合并开关为 **Auto-fix**、**Merge when ready**。

下图展示 2026 年 2 月的旧界面。试用时可先观察 CI 状态，由负责人决定何时开启自动修复与合并。SSH 和 WSL 会话中的 **Merge when ready** 处于禁用状态。

![Desktop 的 CI 监控状态以及 Auto fix、Auto merge 开关](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/source-desktop-ci-monitoring.png)

*图片来源：Anthropic 桌面端预览与审查公告，2026-02-20。*

CLI 也有交互式 `/diff`。如果只需对照一个文件和一条测试结果，留在 shell 里就能完成。批量处理则可把日志或命令输出送入 `claude -p`，用 `--output-format json` 给后续脚本读取。

例如用 `claude -p "总结标准输入里的测试失败" --output-format json < test.log > diagnosis.json` 保存诊断结果。

是否允许合并仍可由 CI 的真实测试退出码决定，模型摘要负责帮助工程师定位问题。

## 并行时先隔离目录，再看任务在哪运行

同仓库同时修两处时，在 Desktop 发第一条消息前开启 **Worktree**，或在终端用 `claude --worktree fix-tests`。每项任务在独立的 Git 工作目录和分支上改动，共用仓库历史。

新建 worktree 默认以远端默认分支为基础。若要带上当前分支的已提交改动，可设 `worktree.baseRef` 为 `head`。主工作目录的未提交改动不会自动进入新目录。

Desktop 的 Worktree 默认关闭。团队可先试两项互不依赖的改动，确认会话所在目录和分支，再核对依赖、环境变量和测试命令。若两个任务需要启动服务，也应为它们分配不同端口。

Desktop 的 **Background tasks** 面板可查看当前会话内的子代理和后台命令。CLI 的 `claude --bg "梳理测试失败"` 则启动整个后台会话，关闭原终端后由本机后台服务继续管理。

用 `claude agents` 打开后台会话列表。更新日志记载，Agent view 在 2026 年 5 月随 v2.1.139 加入，截至核查日仍是研究预览。

![终端 Agent view 显示等待输入、进行中和已完成的会话](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/source-anthropic-agent-view.png)

*图片来源：Anthropic Claude Code Agent view 官方文档，2026-11-24 查阅。*

需要关机后仍能推进的任务，可另开 Cloud 会话。先检查云端是否能拉取仓库、安装依赖和运行测试，再交付长任务。

回到本地时，可用 CLI 的 `--teleport` 接续云端会话，需用同一 Claude 账号并准备匹配的本地仓库。

若要同时切换到云端任务分支，先确认本地工作区干净、云端分支已推送。

![Claude Code 网页端的仓库与分支选择界面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/24/source-claude-code-cloud.jpg)

*图片来源：Anthropic Claude Code 产品页，2026-11-24 查阅。*

## 扩展、权限和 SSH

两端共享项目说明 `CLAUDE.md` 和 Claude Code 配置。模型上下文协议（MCP）服务可写入项目的 `.mcp.json`，也可配置在用户侧的 `~/.claude.json`。

Desktop 为符合条件的本地和 SSH 会话提供 Connectors 界面。

独立 CLI 不直接读 `claude_desktop_config.json`。团队需要两端一致时，可先以项目级 MCP 配置试跑一个只读查询，再检查两端加载的工具和认证。桌面聊天里的连接配置也应纳入这次核对。

钩子（hooks）可按事件执行确定的动作。`PreToolUse` 可在工具执行前拦截调用，`PostToolUse` 可在调用成功后运行检查。可用后者在编辑完成后执行格式检查，再观察两端是否给出一致结果。

高影响改动可先选 **Plan** 审阅方案，再切 **Manual** 逐项放行。CLI 可用 `--permission-mode` 设定模式。

批处理前应明确允许哪些工具，并先在与正式任务相同的账户和目录下验证规则。

已有远端开发机时，Desktop 可经 SSH 登录并使用图形界面，代码与命令在远端运行。远端开发服务器可用端口转发查看。本地 Browser 面板的自动验证工具在 SSH 会话里不可用。

终端用户可直接 SSH 到主机、进入仓库再启动 CLI。需要断线后继续时，要确认主机允许后台进程存活。

Desktop 自 2.31226.0 起支持 SSH 断连后继续运行，也受远端进程策略和凭据有效期约束。

## 怎么选，怎么搭配

- **选 Desktop**。改动横跨多个文件，验收靠逐行反馈、页面检查或并排追踪多条任务时，从 Code 标签页开工。交付前看 Changes，再跑项目测试。
- **选 CLI**。修复范围小、日常工作已在 shell，或需要复用命令、保存 JSON、接入 CI 时，从仓库目录启动。先跑一份可人工复核的输入，再做批量。
- **两者搭配**。本地 CLI 先跑测试并定位文件，需要集中审查时运行 `/desktop`，或关闭终端会话后从 Desktop 本地会话用 `/resume` 接续。交付以分支、diff 和测试记录为准。

`/desktop` 有平台和登录条件。官方文档列出 macOS、x64 Windows 和 Claude 订阅登录。团队若跨机器或跨认证方式，可用 Git 分支和测试日志交接，再在目标环境启动会话。

## 一句话总结

> 用 Desktop 把人工审查和验收放在同一窗口，用 CLI 把可重复步骤交给脚本，并在并行执行前选好目录与运行环境。

## 来源

- [Anthropic Claude Code 产品页](https://claude.com/product/claude-code)
- [Anthropic Opus 4.5 发布公告及桌面端更新](https://www.anthropic.com/news/claude-opus-4-5)
- [Anthropic 桌面端预览与审查公告](https://claude.com/resources/articles/preview-review-and-merge-with-claude-code)
- [Anthropic Claude Code 桌面端改版公告](https://claude.com/resources/articles/claude-code-desktop-redesign)
- [Claude Code Desktop 官方指南](https://code.claude.com/docs/en/desktop)
- [Claude Code CLI 命令参考](https://code.claude.com/docs/en/cli-reference)
- [Claude Code 无交互运行指南](https://code.claude.com/docs/en/headless)
- [Claude Code 交互命令参考](https://code.claude.com/docs/en/commands)
- [Claude Code 官方更新日志](https://code.claude.com/docs/en/changelog)
- [Claude Code Git worktree 文档](https://code.claude.com/docs/en/worktrees)
- [Claude Code 后台会话与 Agent view](https://code.claude.com/docs/en/agent-view)
- [Claude Code 云端运行指南](https://code.claude.com/docs/en/claude-code-on-the-web)
- [Claude Code MCP 文档](https://code.claude.com/docs/en/mcp)
- [Claude Code hooks 参考](https://code.claude.com/docs/en/hooks)
- [Claude Code 权限文档](https://code.claude.com/docs/en/permissions)
