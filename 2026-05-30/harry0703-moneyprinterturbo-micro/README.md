# 如何用 MoneyPrinterTurbo 本地“指挥” OpenAI 与 DeepSeek 生成视频？

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/harry0703-moneyprinterturbo-micro/head.png)

看到 MoneyPrinterTurbo 这个名字，我第一反应是去数它接了多少个模型 API。OpenAI、Moonshot、Azure、gpt4free、one-api、通义千问、Google Gemini、Ollama、DeepSeek、MiniMax、文心一言、Pollinations、ModelScope —— 一共 14 个。这哪叫“接入”，这简直是 AI 视频生成赛道的“模型联合国”。

更有意思的是 README 里那句加粗提示：**“中国用户建议使用 DeepSeek 或 Moonshot 作为大模型提供商（国内可直接访问，不需要 VPN。注册就送额度，基本够用）”**。翻译一下：开发者已经把“怎么让国内用户白嫖到爽”这件事想透了。

![harry0703/MoneyPrinterTurbo — 利用AI大模型，一键生成高清短视频 Generate short videos with one c](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/harry0703-moneyprinterturbo-micro/content-1.png)

**这不是一个技术演示，这是一份极其务实的生产力工具说明书。**

## 14 个模型一字排开，本质是在打“算力成本战”

MoneyPrinterTurbo 的架构并不复杂——MVC 结构，支持 WebUI 和 API 双界面，文案可以 AI 生成也可以自定义。但真正让它登上 GitHub Trending 的，是它对“生成成本”的极致压榨。



![MoneyPrinterTurbo 模型接入矩阵，14 个 LLM 提供商按“是否需 VPN”和“免费额度”排列](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/harry0703-moneyprinterturbo-micro/schematic-1.png)



项目支持 9:16 竖屏（1080×1920）和 16:9 横屏（1920×1080）两种尺寸，视频素材“高清且无版权”，也可以使用本地素材。字幕生成提供了 edge 和 whisper 两种模式，前者“速度快、对配置无要求但质量不稳定”，后者“速度慢但质量更可靠”——这种把 trade-off 摆明面上让用户自己选的写法，在开源项目 README 里算一股清流。

但真正让我愣神的是配置要求表。最低配置：4 核 CPU、4GB RAM、GPU 非必须。推荐配置：6-8 核、8GB RAM、4GB 显存。理想配置才到 16GB RAM 和 8GB 显存。如果你主要依赖云端 LLM 和云端 TTS，“CPU 与内存比 GPU 更重要”。这意味着一个没有独显的轻薄本，理论上也能跑通整个流程。

**这就是 14 个模型接入的真正意图——把重活全部甩给云端，让本地只做拼接和渲染。**

## 一键启动包 + Colab + Docker，三种姿势把门槛踩到地板以下

MoneyPrinterTurbo 的部署方式有三种：Windows 一键启动包（百度网盘和 Google Drive 都有，v1.2.6 版本，解压后先跑 update.bat 更新）、Google Colab（免本地配置，点开即用）、以及 Docker（docker-compose up 一把梭）。手动部署也提供了 uv 和传统 venv+pip 两条路径，Python 版本锁定 3.11。



![三种部署路径对比，一键启动包 vs Colab vs Docker，标注各自适用人群](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/harry0703-moneyprinterturbo-micro/schematic-2.png)



这里面有个细节值得玩味：Windows 一键启动包的百度网盘链接和提取码直接写在 README 里，update.bat 和 start.bat 的流程也写得明明白白。开发者甚至专门提醒“路径不要有中文、特殊字符、空格”——这种保姆级程度，说明他们很清楚目标用户是谁：不是那些能自己配 CUDA 环境的开发者，而是想“输入关键词、拿到成品视频”的内容创作者。

字幕字体放在 resource/fonts 目录下，背景音乐放在 resource/songs 目录下，用户可以随意替换。声音列表里，2024-04-16 v1.1.2 版本新增了 9 种 Azure 语音合成声音，标注“该声音合成的更加真实”。这又是一处 trade-off 的诚实表达：Azure 的声音更好，但需要自己配 API Key；免费的声音也能用，但效果你自己听。



![视频生成流程，从“输入主题”到“文案生成→素材匹配→字幕合成→BGM 叠加→输出 MP4”全链路](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-30/harry0703-moneyprinterturbo-micro/schematic-3.png)



**“特别感谢”部分更把这层窗户纸捅破了。** 项目 README 里直接感谢录咖（reccloud.cn / reccloud.com）基于该项目提供的免费 AI 视频生成器服务，“可以不用部署，直接在线使用”。也感谢佐糖（picwish.cn）的赞助，“使得该项目能够持续的更新和维护”。一个开源项目，商业变现路径已经清晰到这种程度——部署有门槛？没关系，有第三方帮你托管。想白嫖？没关系，赞助商养着项目。

这种“开源引流 + 商业托管 + 赞助养项目”的三层结构，比大多数只靠 GitHub Star 撑场面的项目务实得多。issue 和 pull request 渠道开着，ffmpeg 找不到、ImageMagick 安全策略报错、Whisper 模型下载失败这些常见问题都给了解决方案。甚至连“Whisper 模型文件约 3GB，国内无法访问 HuggingFace”这种痛点都提前准备了百度网盘和夸克网盘的下载链接，解压后放到指定目录就行。

**一个项目是否真的想让用户用起来，看它怎么处理“国内网络问题”就知道了。MoneyPrinterTurbo 在这件事上做到了极致。**

---

## 参考来源

- MoneyPrinterTurbo GitHub 仓库：https://github.com/harry0703/MoneyPrinterTurbo

#MoneyPrinterTurbo #Generate #AI #LLM #科技
