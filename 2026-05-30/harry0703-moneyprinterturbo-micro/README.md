# GitHub项目MoneyPrinterTurbo：一键生成高清短视频，支持GPT/DeepSeek

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/harry0703-moneyprinterturbo-micro/head.png)

你不需要写脚本、找素材、调字幕、配音乐。

你只需要给一个主题。

![harry0703/MoneyPrinterTurbo — 利用AI大模型，一键生成高清短视频 Generate short videos with one c](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/harry0703-moneyprinterturbo-micro/content-1.png)

然后等几分钟，下载一条 1080p 的成品短视频。

**这不是某个 SaaS 公司的融资新闻。这是一个 GitHub 开源仓库的 README 里写的功能描述。**

---

## 当你把“视频工厂”塞进一个 Python 项目里

MoneyPrinterTurbo——名字就透着一股“我先印为敬”的直白——在 GitHub 上把 AI 视频生成这件事拆到了最细颗粒度。它的逻辑链简单到让人愣神：用户输入一个视频主题或关键词 → 大模型自动生成视频文案 → 系统匹配无版权高清素材 → 合成语音 → 添加字幕 → 铺上背景音乐 → 输出一条完整的高清短视频。

全自动。一键。



![MoneyPrinterTurbo 视频生成流水线示意，从主题输入到成品输出的 6 个步骤](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/harry0703-moneyprinterturbo-micro/schematic-1.png)



我看到这个项目的第一反应不是“又一个 AI 视频工具”，而是它的模型接入列表。OpenAI、Moonshot、Azure、gpt4free、one-api、通义千问、Google Gemini、Ollama、DeepSeek、MiniMax、文心一言、Pollinations、ModelScope——这份清单几乎把市面上能叫得出名字的大模型服务商一网打尽。而且项目 README 里特别标注了一句话，对中国用户来说杀伤力极大：

> 中国用户建议使用 DeepSeek 或 Moonshot 作为大模型提供商（国内可直接访问，不需要 VPN。注册就送额度，基本够用）

不需要 VPN。注册送额度。基本够用。

这三个短语放在一起，直接拆掉了国内用户接触 AI 视频生成的最后一道墙。



![MoneyPrinterTurbo 支持的大模型服务商一览，按国内/海外分类](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/harry0703-moneyprinterturbo-micro/schematic-2.png)



但真正让我停下来细看的是它的架构描述：**完整的 MVC 架构，代码结构清晰，支持 API 和 Web 界面**。一个面向“小白用户”的视频生成工具，居然在 README 里主动强调代码架构——这说明作者 harry0703 不是随手拼了个脚本就扔上来，而是在设计之初就考虑了可维护性和二次开发的可能性。项目同时提供 WebUI（基于 Streamlit）和 RESTful API 两种交互方式，API 启动后可以直接访问 `http://127.0.0.1:8080/docs` 在线调试接口。

换句话说，你可以把它当个人工具用，也可以把它嵌进自己的产品链路里。

视频规格方面，MoneyPrinterTurbo 支持竖屏 9:16（1080×1920）和横屏 16:9（1920×1080）两种高清尺寸，覆盖了抖音/视频号和小红书/B 站两类主流平台的画面比例。视频片段时长可调，素材切换频率可以自己控制。批量生成功能允许用户一次跑多条视频，然后挑一条最满意的——这个设计思路很像投流素材工厂的作业逻辑：批量生产，人工筛选，只留最好的。

---

## 一个视频工具，为什么要在 README 里写三套硬件配置表

大多数开源项目的 README 写到“pip install -r requirements.txt”就收笔了。MoneyPrinterTurbo 不一样。它专门列了一张硬件配置表，分最低、推荐、理想三档：

| 配置项 | 最低 | 推荐 | 理想 |
|--------|------|------|------|
| CPU | 4 核 | 6-8 核 | 8 核及以上 |
| RAM | 4 GB | 8 GB | 16 GB 及以上 |
| GPU | 非必须 | 4 GB 显存及以上 | 8 GB 显存及以上 |

然后紧跟着一句说明，直接改变了这个工具的定位：

> 如果你主要依赖云端 LLM、云端 TTS 和在线素材源，CPU 与内存比 GPU 更重要。

GPU 不是必需项。这句话意味着一个没有独显的轻薄本用户，只要网络正常、API Key 配好，就能跑通完整的视频生成流程。因为文案生成交给云端大模型，语音合成走云端 TTS，视频素材从 Pexels 这类无版权图库在线拉取——整个链条里最重的本地计算只剩下视频合成和字幕渲染，而这些任务对 CPU 的依赖远大于 GPU。

当然，如果你启用 faster-whisper 做本地字幕生成，或者跑批量任务，GPU 的优势就会体现出来。但那是进阶玩法，不是准入门槛。



![三档硬件配置对比图，标注“云端依赖模式”下 GPU 非必须的关键路径](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/harry0703-moneyprinterturbo-micro/schematic-3.png)



字幕生成这块，项目提供了两种方案：edge 模式和 whisper 模式。edge 模式速度快、对配置没要求，但质量可能不稳定；whisper 模式质量更可靠，但需要从 HuggingFace 下载一个约 3GB 的模型文件。考虑到国内用户访问 HuggingFace 的难度，作者特意在 README 里附了百度网盘和夸克网盘的模型下载链接，解压后放到 `.\MoneyPrinterTurbo\models\whisper-large-v3` 路径下即可。

这种“国内用户专属解决方案”的细腻程度，在英文为主的 GitHub 开源项目里相当罕见。

语音合成方面，除了常规的多种语音可选、实时试听，2024 年 4 月 16 日的 v1.1.2 版本新增了 9 种 Azure 语音合成声音，需要配置 API Key。Azure 的语音合成质量在业内公认更接近真人，这步接入意味着 MoneyPrinterTurbo 在声音真实度上往上探了一个台阶。

背景音乐来自项目的 `resource/songs` 目录，默认放了一些 YouTube 来源的音乐。字幕字体放在 `resource/fonts` 目录，用户可以自行添加。视频素材除了在线拉取无版权资源，也支持使用本地素材——这条路径对于有自有素材库的内容团队来说，灵活性直接拉满。

部署方式同样做了分层：Windows 用户有一键启动包（虽然当前提供的仍是 v1.2.6 旧版本，建议下载后先执行 `update.bat` 更新），MacOS/Linux 用户推荐用 `uv sync --frozen` 本地部署，想隔离环境的走 Docker。甚至提供了 Google Colab 的免配置运行方案——点击即用，连本地 Python 环境都不用装。

---

## 一个开源项目背后的“视频工业化”信号

MoneyPrinterTurbo 本身不是新技术。AI 写文案、TTS 配音、自动匹配素材、合成视频——这些单点能力在 2024 年的 AI 生态里已经不算新鲜。但把它们串成一条完整的、一键触发的流水线，并且用开源的方式把门槛压到“有浏览器就能用”，这背后的信号比项目本身更有意思。

录咖（AI 智能多媒体服务平台）已经基于这个项目提供了免费的在线 AI 视频生成器，用户不用部署，直接访问 reccloud.cn 就能用。佐糖（picwish.cn）也对这个项目赞助，使得项目能够持续更新和维护。一个开源视频生成工具，正在成为上游商业产品的技术底座。

**当视频创作从“技能”变成“接口”，内容供给的稀缺性就不再卡在人身上，而是卡在创意和分发上。**

这个转变一旦完成，短视频平台的 content pool 会发生什么变化？MCN 机构的剪辑团队还需要 10 个人吗？个人创作者的核心竞争力会从“会剪视频”迁移到哪里？

这些问题 MoneyPrinterTurbo 没有回答，也不需要回答。它只是安静地躺在 GitHub 上，用 16.7k stars 告诉路过的人：印钞机已经开源了，要不要启动，看你。

---

## 参考来源
- https://github.com/harry0703/MoneyPrinterTurbo

#MoneyPrinterTurbo #Generate #AI #LLM #科技
