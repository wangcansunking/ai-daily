# 84%学生用AI仅18%专业？这份473节课课表填平断层

> ai-daily · 2026 年 5 月 27 日 21:48 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/rohitg00-ai-engineering-from-scratch-micro/head.png)

凌晨两点十七分，布鲁克林一间合租公寓的次卧里，屏幕的蓝光打在一张略显疲惫的脸上。Rohit Ghosh 刚刚把第 473 个 lesson 的代码推上 GitHub。这不是一篇论文，也不是一个 demo，而是一份完整到令人窒息的 AI 工程课程表——20 个阶段，473 节课，横跨 Python、TypeScript、Rust、Julia 四种语言，预计耗时约 320 小时。

他把仓库设成了 MIT 开源协议，然后在 README 里写下了一句简单到狂妄的话：**Learn it. Build it. Ship it for others.**

![rohitg00/ai-engineering-from-scratch — Learn it. Build it. Ship it for others.](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/rohitg00-ai-engineering-from-scratch-micro/content-1.png)

我看到这个仓库在 GitHub Trending 上冒出来的时候，第一反应不是“又来了一个 AI 教程”，而是“这玩意儿怎么长得像一份军事作战地图”。从线性代数到多智能体集群（Multi-Agent Swarms），从反向传播的手写做到到 MCP 协议的生产部署，它试图用一根脊椎骨把整个 AI 工程的知识体系串起来——而这一切，要求你在自己的笔记本上跑通，不依赖任何云端黑箱。

**这可能是 2026 年最硬核的一份“免费大学”——但它的目标用户，恰恰是那些已经被 AI 工具“养懒了”的开发者。**



![20 个阶段从 Phase 0 到 Phase 19 的流程阶梯图，数学打底，Agent 封顶](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/rohitg00-ai-engineering-from-scratch-micro/schematic-1.png)



## 84% 的学生在用 AI，但只有 18% 觉得自己能专业地用——这份课表想填上这个窟窿

课程作者在 README 里扔出了一个刺眼的数字：84% of students already use AI tools. Only 18% feel prepared to use them professionally. 翻译过来就是：十个人里有八个在用 AI 帮忙写作业、生成代码、总结文档，但真正敢说自己能“专业地驾驭 AI”的人，不到两个。

这不是能力焦虑贩卖，这是事实陈述。过去两年，AI 教程市场被两种内容占领了天花板：一种是五分钟视频教你“用 LangChain 三行代码搭一个 Chatbot”，另一种是 ArXiv 论文精读专栏，从 Attention 机制讲到 Mixture of Experts，但从不告诉你这些东西怎么在真实工程里拼起来。前者让你以为自己会了，后者让你以为自己懂了。等真正要上手做一个能上线、能监控、能处理边缘案例的 AI 系统时，两头的人都傻眼。

这份课表的设计逻辑，就是冲着这个断层来的。它强制每一节课走完六个节拍：Motto（一句话核心思想）→ Problem（具体痛点）→ Concept（直觉和图示）→ Build It（裸写，不用任何框架）→ Use It（再用 PyTorch / sklearn 跑一遍）→ Ship It（产出一个可复用的 prompt、skill、agent 或 MCP server）。注意那个 **Build It / Use It** 的分割线——这是整份课表的脊椎骨。你先手撕一遍反向传播的数学，然后再让 PyTorch 替你自动求导。到那个时候，你不再是黑箱的调用者，你是知道箱子里面齿轮怎么转的人。



![六步教学节拍流程图，Motto → Problem → Concept → Build It → Use It → Ship It，突出 Build It 和 Use It 的分割](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/rohitg00-ai-engineering-from-scratch-micro/schematic-2.png)



更有意思的是，它甚至给你配了一套内置的 Agent Skills。在 Claude、Cursor、Codex 或者任何装了课程 skill 的 agent 里输入 `/find-your-level`，十个问题测完，直接告诉你从哪个 Phase 切入最合适，顺带估计一下你要花多少小时。每学完一个 Phase 还可以用 `/check-understanding 3` 自测，八道题，错了就给你指回到具体的 lesson。这套机制本身就是一个 Agent 工程的教学样本——你在学 AI，而你用的工具本身也是 AI 教你学 AI，闭环了。

## 473 个可交付物：不是“恭喜你学会了”，而是“拿着这个，去干活”

大多数课程结束的时候，给你的是一句“恭喜你学会了 X”。这份课表结束的时候，给你的是一个文件夹。每个 lesson 的 `outputs/` 目录下躺着一个可直接安装或粘贴到日常工作流里的东西：一段 prompt 模板、一个 skill 定义文件、一个 agent 循环的源码、或者一个 MCP server 的完整做到。

Phase 14 的第一课——Agent Loop，大概 120 行纯 Python，零依赖。你写出来的不是一个 toy demo，而是一个 ReAct 风格的 agent 循环骨架，可以接任何工具列表。同一节课的 `outputs/` 里还附带了一个 `skill-agent-loop.md`，描述这个 agent 的行为边界；外加一个 `prompt-debug-agent.md`，让另一个 agent 拿着 trace 日志帮你定位出错步骤。这不是作业，这是工具箱。

整个课程走完，你会攒下 473 个这样的可交付物。用作者的话说，“Real tools, not homework.” 你可以把它们装进任何一个支持 MCP 的客户端，或者直接用 `python3 scripts/install_skills.py` 一键安装到 Claude、Cursor、Codex、OpenClaw、Hermes 这些环境里。换句话说，你在学习的过程中，同时也在构建自己的 AI 工程基础设施。



![一个 outputs 文件夹展开视图，包含 prompt 文件、skill 文件、agent 文件和 MCP server 文件四种类型](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-28/rohitg00-ai-engineering-from-scratch-micro/schematic-3.png)



这种“学完即交付”的设计哲学，让我想起软件工程里那句老话：“Talk is cheap, show me the code.” 但这里更进一步——show me the code, and then give me the tool.

当然，这份课表也不是没有门槛。作者在 Prerequisites 里写得很直白：“你会写代码（任何语言都行，Python 加分），你想真正理解 AI 是怎么工作的，而不是只会调 API。” 没有五分钟视频，没有 copy-paste deploy，没有手把手教学。二十个阶段从数学打底到自主系统封顶，你可以跳过已经掌握的阶段，但“别跳了之后又纳闷为什么顶层的东西跑崩了”。这种语气，像是健身教练在跟你说“别跳过腿部训练日”。

从 Phase 0 的开发环境搭建，到 Phase 19 的 Capstone 项目，中间塞进了计算机视觉 28 课、NLP 29 课、语音音频 17 课、Transformer 深潜 14 课、LLM 从零构建 22 课、Agent 工程 42 课、多智能体集群 25 课、基础设施与生产部署 28 课、伦理与对齐 30 课。甚至 Phase 18 里还专门有一课讲 Sleeper Agents（潜伏特工）——那种在被部署后才会触发恶意行为的隐藏后门模型。这个选题本身就说明，作者不是在做一份“AI 速成宝典”，而是在试图构建一个 AI 工程师的完整心智模型。

说实话，我不知道有多少人能从头到尾把这 320 个小时啃完。但我知道的是，当 84% 的人都在用 AI 而只有 18% 的人觉得自己能用好的时候，这份课表至少给出了一个诚实的答案：想真正驾驭这头猛兽？那就从手撕反向传播开始，一行代码一行代码地，把黑箱拆成透明零件。

## 参考来源
- GitHub: rohitg00/ai-engineering-from-scratch — https://github.com/rohitg00/ai-engineering-from-scratch
- 课表官网: aiengineeringfromscratch.com

#Learn #Build #Ship #AI #科技
