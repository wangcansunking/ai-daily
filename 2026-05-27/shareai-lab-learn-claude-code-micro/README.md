# 从DQN到Claude Code：OpenAI与DeepMind的Agent演进史揭秘

> ai-daily · 2026 年 5 月 27 日 11:39 · 来源：GitHub Trending python

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/shareai-lab-learn-claude-code-micro/head.png)

2025 年某个周三的凌晨，GitHub Trending 榜单上突然炸出一个仓库——没有花哨的 Demo 视频，没有大厂背书，甚至 README 第一页就甩出一句近乎挑衅的判断：**"Agency comes from the model, not from external code orchestration."** 中文翻译得更狠——"Bash is all you need"。

这个叫 `learn-claude-code` 的项目，来自一个叫 shareAI-lab 的组织。他们在 20 个循序渐进的章节里，把 Claude Code 拆成了一副骨架——然后告诉你，这副骨架你也能搭。而且他们真的给了你从 `s01_agent_loop` 到 `s20_comprehensive` 的完整可运行代码。每一章一个 `code.py`，每一章一个机制，每一章一句"motto"。

![shareAI-lab/learn-claude-code — Bash is all you need - A nano claude code–like 「](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/shareai-lab-learn-claude-code-micro/content-1.png)

我读完 s01 的 README 之后第一反应是：**这帮人不是在教你怎么用 Claude Code，是在教你认清一个整个 AI 行业都在集体误读的真相。**

**"Agent"这个词已经被 prompt 管道工们绑架了。**

## 从 Atari 到 Dota 2：智能是练出来的，不是写出来的

shareAI-lab 在仓库里铺了一条清晰得近乎暴力的历史线索——他们管它叫"the historical record is unambiguous"。从 2013 年 DeepMind 的 DQN 打 Atari 开始讲起。

那年一个神经网络，只接收原始像素和游戏分数，学会了 7 款 Atari 2600 游戏，其中 3 款超越人类专家。到 2015 年扩展到 49 款游戏，全部达到专业测试员水平，发在了《Nature》上。没有游戏特定规则，一个模型，从经验中学习。

然后跳到 2019 年——OpenAI Five 打 Dota 2。五个神经网络自我对战了相当于 45,000 年的 Dota 2 对局，花了 10 个月，最后在直播中 2-0 干掉了 TI8 世界冠军 OG。在公开竞技场里，AI 赢了 42,729 场对局中的 99.4%。没有脚本策略，模型用自我博弈学会了团队协作。

同一年，DeepMind 的 AlphaStar 在《星际争霸 II》里 10-1 击败职业选手，打到欧服宗师级——90,000 名玩家中的前 0.15%。一个不完全信息、实时决策、动作空间远超围棋和象棋的游戏。

还是 2019 年，腾讯 AI Lab 的"绝悟"系统在世界冠军杯半决赛上击败了 KPL 职业选手，5v5 全阵容。1v1 模式下，职业选手 15 局只赢了 1 局，撑不过 8 分钟。训练强度：一天等于人类 440 年。



![四条时间线并排对比——DQN Atari / OpenAI Five Dota 2 / AlphaStar 星际争霸 / 绝悟王者荣耀，标注各自训练规模和关键战绩](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/shareai-lab-learn-claude-code-micro/schematic-1.png)



shareAI-lab 把这条线索一路拉到 2024-2025 年：Claude、GPT、Gemini 这些大语言模型，被部署成编程 Agent，读代码库、写做到、调试失败、协调团队。架构跟前面所有 Agent 一模一样——一个训练好的模型，放进一个环境里，给它感知和行动的工具。

**每一次里程碑都指向同一个事实：智能是训练出来的，不是代码堆出来的。**

但问题来了——每个 Agent 都需要一个运行环境：Atari 模拟器、Dota 2 客户端、星际争霸 II 引擎、一个 IDE 和一个终端。模型提供智能，环境提供行动空间。两者合在一起才是完整的 Agent。

而这个环境，就是他们所谓的"harness"——缰绳、挽具、载具。模型是司机，harness 是车。

## 别再造鲁布·戈德堡机械了——你需要的只是一个循环加一个 Bash

仓库里最让我愣神的一句话藏在 s01 的标题下面："One loop & Bash is all you need"——一个循环加一个 Bash，就是一个 Agent。

他们给的核心代码短得令人不适：

```python
def agent_loop(messages):
    while True:
        response = client.messages.create(
            model=MODEL, system=SYSTEM, messages=messages, tools=TOOLS
        )
        messages.append({"role": "assistant", "content": response.content})
        if response.stop_reason != "tool_use":
            return
        results = []
        for block in response.content:
            if block.type == "tool_use":
                output = TOOL_HANDLERS[block.name](**block.input)
                results.append({
                    "type": "tool_result",
                    "tool_use_id": block.id,
                    "content": output,
                })
        messages.append({"role": "user", "content": results})
```

就这。模型决定什么时候调用工具、什么时候停。代码只执行模型要求的事。整个仓库的 20 节课，都是在这个循环外面一层一层加 harness 机制——循环本身从来不改变。

shareAI-lab 把市面上那些拖拽式工作流、无代码"AI Agent"平台、prompt 链编排库骂得毫不留情。他们管这些叫"鲁布·戈德堡机械"——过度工程化、脆弱、用 if-else 分支和节点图堆出来的程序化规则管道，把一个 LLM 塞进去当美化过的文本补全节点。"那不是 Agent，那是一个有妄想症的 shell 脚本。"

**你不能用堆砌程序化逻辑的方式暴力制造智能——铺开规则树、节点图、链式 prompt 瀑布，然后祈祷足够的胶水代码会自发产生自主行为。不会的。你无法把智能工程化出来。智能是学来的，不是写出来的。**



![左边是"prompt 管道"式工作流——拖拽节点、if-else 分支、硬编码路由；右边是 agent loop——一个 while True 循环 + 工具分发映射表，简洁对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/shareai-lab-learn-claude-code-micro/schematic-2.png)



那 harness 工程师到底在干什么？仓库里列了五件事：做到工具（给 Agent 手——文件读写、shell 执行、API 调用、浏览器控制）、编排知识（按需加载产品文档和领域参考，不是一股脑塞进上下文）、管理上下文（子 Agent 隔离防噪音泄露，上下文压缩防历史淹没当下）、控制权限（沙盒文件访问、破坏性操作需审批）、收集轨迹数据（Agent 在 harness 里执行的每一条动作序列都是训练信号，真实部署轨迹是微调下一代 Agent 模型的原材料）。

20 节课从 s01 的纯循环开始，一路加到 s20 的综合 Agent——中间塞进了权限系统、Hook 扩展点、TodoWrite 规划机制、子 Agent 生成、技能按需加载、多层上下文压缩、记忆系统（选择-提取-整合三子系统）、系统提示词运行时拼装、错误恢复与 token 升级、文件持久化的任务图、后台线程执行与通知注入、Cron 定时触发、多 Agent 团队与异步邮箱、自组织任务认领、Worktree 目录隔离、MCP 外部能力路由。



![20 节课的 harness 机制叠加示意图——从中心的 agent loop 向外层层包裹：tool use → permission → hooks → planning → subagent → skill loading → context compact → memory → system prompt → error recovery → task system → background tasks → cron → teams → protocols → autonomous → worktree → MCP → comprehensive](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-27/shareai-lab-learn-claude-code-micro/schematic-3.png)



而这一切的起点，不过是一个 `while True` 循环加一个 Bash 工具。

shareAI-lab 在仓库末尾扔了两个延伸项目：Kode Agent CLI（一个开源的编程 Agent 命令行工具，支持 GLM / MiniMax / DeepSeek 等开放模型）和 Kode Agent SDK（一个可嵌入后端、浏览器插件、嵌入式设备的独立库）。还有一个姐妹教程 `claw0`，讲怎么把 Agent 从"戳一下动一下"变成"每 30 秒自己醒来找活干"的常驻助手——加了心跳机制、Cron 定时、IM 多渠道路由、持久化记忆和 Soul 人格系统。

**Bash is all you need. Real agents are all the universe needs.**

这个仓库不是在教你怎么抄 Claude Code。它在教你认清一件事：最好的 Agent 产品，来自那些明白自己造的是车、不是司机的工程师。把 harness 造好，模型会完成剩下的。

## 参考来源
- https://github.com/shareAI-lab/learn-claude-code
- 文中所有历史数据（DQN 49 款游戏 / OpenAI Five 45,000 年训练 / AlphaStar 宗师前 0.15% / 绝悟 440 年日训练量）均来自该仓库 README 原文

#Bash #AI #科技
