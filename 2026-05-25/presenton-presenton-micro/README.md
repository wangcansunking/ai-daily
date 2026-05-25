# 开源项目Presenton挑战Gamma：支持OpenAI、Claude等10+模型

> ai-daily · 2026 年 5 月 24 日 21:43 · 来源：GitHub Trending any

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/presenton-presenton-micro/head.png)

2026 年的某个周三下午，你打开邮箱，第三封来自市场部的邮件静静躺在收件箱里——「老板说 PPT 风格要再调一版，明早 9 点前发」。你看了眼时间，16:47。Gamma 的免费额度上周就用完了，Beautiful AI 的订阅费上个月刚涨到 30 刀一个月，Decktopus 的模板库翻了半小时也没找到老板要的那个「既专业又有科技感」的调调。

你关掉浏览器，打开终端。

![presenton/presenton — Open-Source AI Presentation Generator and API (Gamma, Beau](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/presenton-presenton-micro/content-1.png)

`docker run -it --name presenton -p 5000:80 -e LLM="openai" -e OPENAI_API_KEY="sk-..." -e IMAGE_PROVIDER="dall-e-3" -v "./app_data:/app_data" ghcr.io/presenton/presenton:latest`

![Presenton Features](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/presenton-presenton-micro/content-2.png)

一行命令。一个开源项目。一套完全跑在本地的 AI 演示文稿生成器。这就是 Presenton——今天 GitHub Trending 榜首的那个项目，把自己定位为 Gamma、Beautiful AI 和 Decktopus 的「开源替代品」。

**SaaS 订阅的尽头，是 Docker 镜像。**



![Presenton 与 Gamma/Beautiful AI/Decktopus 的对比矩阵，维度包括开源/自托管/API/定价模式/模板自定义](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/presenton-presenton-micro/schematic-1.png)



## 把 AI 幻灯片拉下云端

Presenton 做的事情其实很直白：把 AI 生成 PPT 这件事从「按月付费的 SaaS 平台」手里拽出来，塞进一个 Apache 2.0 开源许可的 Docker 容器里。你不需要把文档上传到某个云端服务器，不需要忍受免费版右下角的水印，也不需要担心下个月公司财务部会不会把某个订阅砍掉。

从技术栈看，这个项目野心不小——Electron 桌面端（macOS Apple Silicon/Intel、Windows x64、Linux x64 三个平台都有 .dmg/.exe/.deb 安装包）、Docker 一键部署、Next.js 前端配 FastAPI 后端，外加一个完整 REST API。它支持的模型供应商名单读起来像一份 AI 基础设施黄页：OpenAI、Google Gemini、Vertex AI、Azure OpenAI、Amazon Bedrock、Fireworks、Together AI、Anthropic Claude、LM Studio、Ollama，甚至任何 OpenAI 兼容的自定义端点（`LLM="custom"` 配合 `CUSTOM_LLM_URL`）。文本模型和图像生成模型可以混搭——比如用 Claude 3.5 Sonnet 写幻灯片文案，同时用 DALL-E 3 生成配图，或者用 Gemini Flash 做图，甚至接上 Pexels/Pixabay 的免费素材库。



![Presenton 多模型供应商架构图，展示 LLM 层（OpenAI/Anthropic/Ollama 等）与图像层（DALL-E/Gemini Flash/ComfyUI/Open WebUI 等）的解耦关系](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/presenton-presenton-micro/schematic-2.png)



让我愣神的是图像生成那一栏的环境变量配置。除了常规的 `DALL_E_3_QUALITY`（standard/hd）和 `GPT_IMAGE_1_5_QUALITY`（low/medium/high），它甚至支持接入 ComfyUI——那个被 AI 绘画圈奉为神器的自托管工作流引擎（`COMFYUI_URL` + `COMFYUI_WORKFLOW`）。这意味着你可以把 Stable Diffusion 生态里最复杂的自定义工作流接到幻灯片配图生成里，完全绕开云端图像 API 的审核和速率限制。这已经不是「替代 SaaS」了，这是在给企业内网的 AI 能力拼乐高。

更值得玩味的是 MCP Server 集成。Presenton 内置了一个 Model Context Protocol 服务端，这意味着它可以直接被 Claude Desktop 或其他支持 MCP 的客户端调用——你可以在聊天界面里说「帮我生成一份关于 Q3 财报的 10 页 PPT」，Claude 用 MCP 协议把请求转发给本地运行的 Presenton 实例，然后返回一个 PPTX 文件。这种「AI 调用 AI」的工具链编排方式，正在把演示文稿生成从一个独立应用变成 AI Agent 工作流里的一个标准节点。

## 开源不是护城河，生态才是

但 Presenton 真正聪明的地方，不是技术栈的广度，而是它对「锁入效应」的精准反击。项目 README 第一屏就写明了三件事：No SaaS lock-in、No forced subscriptions、Full control over models and data。这种话术在开源圈子里并不新鲜，但在 AI 演示文稿这个细分赛道里，它戳中的痛点非常具体。

（值得一提的是，Gamma 去年刚拿到一轮融资后就把免费版的功能砍了一大截，Beautiful AI 的企业版定价至今不公开——需要联系销售团队「获取报价」。Decktopus 的 AI 生成功能在免费版里只给 3 次试用，用完就得掏钱。三家的共同点是：你的数据一旦上传，就进了他们的服务器。）

Presenton 的方案是「Bring Your Own Key」——你自己去 OpenAI 或 Google 申请 API 密钥，按 token 付费，Presenton 不抽成、不中转、不留存。对于已经有企业级 AI 网关的公司（比如用 Azure OpenAI 或 Bedrock 统一管理 API 配额），这个模式几乎零摩擦。Docker 启动时设置 `CAN_CHANGE_KEYS="false"`，管理员可以把密钥锁定，终端用户连改都改不了——IT 部门的最爱。



![Presenton 部署架构示意，左侧 Docker 容器（FastAPI + Next.js + Mem0 记忆层 + LiteParse 文档解析），右侧对接多种 LLM/图像 API，底部展示 Electron 桌面端和浏览器访问两条路径](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-25/presenton-presenton-micro/schematic-3.png)



另一个值得细看的设计是 Mem0 集成。Presenton 在 Docker 镜像里内置了一个基于 Qdrant 向量数据库 + SQLite 的记忆系统，默认用 Ollama 本地模型跑 embedding（`BAAI/bge-small-en-v1.5`），不需要 OpenAI 密钥就能初始化。这个记忆层按演示文稿维度做隔离，意味着 AI 在生成多页幻灯片时可以记住前面页面的上下文——比如第 3 页提到的「同比增长 23%」会在第 7 页的图表说明里被自动引用，而不是像某些竞品那样每页都像一个独立的新对话。

文档解析那一块用了 LiteParse，支持 OCR（`LITEPARSE_DPI` 默认 120），理论上你可以上传一份 PDF 财报或 Word 文档，让 Presenton 自动解析内容后生成幻灯片。这个功能在 Gamma 的付费版里叫「AI 文档转演示」，属于核心卖点之一。Presenton 把它也开源了。

不过，开源不代表没有门槛。Presenton 目前的认证系统只有一个「单管理员账号」——`AUTH_USERNAME` 和 `AUTH_PASSWORD` 写在环境变量里，哈希存进 `userConfig.json`。这对个人开发者或小团队够用，但如果想在企业内网推给 50 个同事用，就得自己搭一层 OAuth 或 SSO。另外，API 的认证方式是 HTTP Basic Auth（`-u username:password`），这在 2026 年的生产环境里多少有点朴素。但换个角度想，它把 API 设计得足够简单——一个 `/api/v1/ppt/presentation/generate` 端点，接收 `content`、`n_slides`、`language`、`template`、`export_as` 这几个参数，返回 `presentation_id` 和 PPTX 文件路径。五分钟上手，不用读 50 页 API 文档。

项目公开的 GitHub Projects 路线图里还挂着一些待办项，但光是目前这版——桌面端 + Docker + API + MCP Server + 多供应商混搭 + 本地模型支持——已经足够让那些每个月为 AI 幻灯片付 30 刀订阅费的团队认真考虑一次迁移了。毕竟，一行 `docker run` 的成本，比「联系销售获取报价」低了不止一个数量级。

## 参考来源
- https://github.com/presenton/presenton

#OpenSource #AI #Presentation #Generator #API
