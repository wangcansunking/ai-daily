# 开源神器MoneyPrinterTurbo：接入DeepSeek等12种模型，5步生成高清视频

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/harry0703-moneyprinterturbo-micro/head.png)

看到一个开源项目把视频生成流程拆成“文案-素材-字幕-音乐-合成”五个环节，然后接入 12 种大模型和 2 套语音引擎，最后打包成一键启动的 exe 扔进百度网盘——我第一反应不是“这东西好用”，而是“这作者太懂国内用户的痛点了”。

MoneyPrinterTurbo，GitHub 上 2024 年 4 月上线的 Python 项目，至今仍在持续更新。它的核心功能用一句话就能说清楚：**输入一个视频主题或关键词，自动生成文案、匹配无版权高清素材、合成语音、叠加字幕、混入背景音乐，最后输出一个 1080P 的短视频。** 竖屏 9:16 或横屏 16:9 任选，支持批量生成，一次跑多个版本挑最好的。



![MoneyPrinterTurbo Web 界面截图，左侧输入框填写主题，右侧预览竖屏视频成品，字幕在底部居中显示](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/harry0703-moneyprinterturbo-micro/schematic-1.png)



但真正让它从一堆“AI 视频生成工具”里跳出来的，不是功能列表——是它对中国开发者和内容创作者的适配程度，精确到令人发指。

**一个开源项目的“本土化”，可以细致到连网盘提取码都帮你准备好了。**

---

## 12 个模型接入 + 百度网盘分发：这不是开源，这是“保姆级”交付

翻 MoneyPrinterTurbo 的 README，你会发现一个很少在英文开源项目里出现的细节：它专门给中国用户写了推荐。

> 中国用户建议使用 DeepSeek 或 Moonshot 作为大模型提供商（国内可直接访问，不需要 VPN。注册就送额度，基本够用）

这句话背后是一个现实判断——国内大量想做短视频的人，既没有稳定的 VPN 访问 OpenAI，也没有信用卡绑 API。DeepSeek 和 Moonshot（月之暗面）恰好都支持国内直接注册、赠送试用额度。作者没有站在“技术中立”的立场列一堆模型让用户自己试，而是直接替用户做了选择。

更让我愣神的是模型支持列表的长度：OpenAI、Moonshot、Azure、gpt4free、one-api、通义千问、Google Gemini、Ollama、DeepSeek、MiniMax、文心一言、Pollinations、ModelScope——**整整 13 种 LLM 服务商被塞进了一个视频生成工具里。**（是的，gpt4free 这种社区代理方案也在列，懂的都懂。）



![13 个 LLM 服务商 Logo 排列，中间箭头指向“文案生成模块”，右侧输出视频脚本示例](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/harry0703-moneyprinterturbo-micro/schematic-2.png)



这还没完。语音合成支持“实时试听”，字幕生成给了两套方案：edge TTS（快，免费，对配置没要求）和 whisper（慢，需要 3GB 模型下载，但质量更稳）。作者甚至贴了 whisper-large-v3 模型的**百度网盘和夸克网盘**下载链接——因为 HuggingFace 在国内经常连不上。

我看到这里的时候在想：这哪是写 README，这简直是给国内用户写了一本《从零到一避开所有坑》的操作手册。

再看看分发方式。除了标准的 `git clone` + 命令行部署，项目提供了：
- Windows 一键启动包（百度网盘 + Google Drive 双链接，提取码 `sbqx`）
- Docker 部署方案
- **Google Colab 直接运行**（免本地配置）

Windows 一键包是 v1.2.6 版本，下载后先跑 `update.bat` 更新到最新代码，再双击 `start.bat` 启动。启动后自动打开浏览器进 WebUI。如果浏览器空白，README 还贴心地提醒“换成 Chrome 或 Edge”——连这种小坑都替你踩过了。

---

## 竖屏 1080×1920，字幕描边，背景音乐音量可调——这些“小需求”才是流量密码

技术圈经常犯一个错误：用“技术难度”来衡量产品价值。但 MoneyPrinterTurbo 里最有价值的功能，恰恰是那些技术上没什么难度、却直接决定视频能不能用的细节。

竖屏 9:16，分辨率 1080×1920——这是抖音、快手、视频号的**原生尺寸**。横屏 16:9 1920×1080 对应 B 站和 YouTube。两个尺寸一键切换，意味着同一个文案可以同时覆盖短视频平台和中长视频平台。

字幕支持字体、位置、颜色、大小调整，**还支持字幕描边**。做过短视频的人知道，字幕描边不是审美偏好，是**刚需**——浅色背景上白色字幕没有描边根本看不清，而 AI 生成的素材你控制不了背景颜色。这个功能在大多数视频编辑软件里要点三四层菜单，MoneyPrinterTurbo 直接做进了配置项。

背景音乐可以随机从 `resource/songs` 目录选，也可以指定文件，**音量独立可调**。视频片段时长可以调节，控制素材切换频率——太快显得碎，太慢显拖沓，这个参数直接决定视频的“节奏感”。



![配置面板展示，字幕位置/颜色/描边、背景音乐音量滑块、视频片段时长输入框等参数可视化](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/harry0703-moneyprinterturbo-micro/schematic-3.png)



这些功能单个拿出来都不起眼。但把它们和“批量生成”组合在一起，就变成了一个可怕的内容工厂：你写 10 个主题，每个主题自动生成 3 版文案，配上不同的素材组合和音乐，一晚上跑出 30 个视频，第二天挑最好的发。**人力成本从“剪辑师干一天”变成“电脑跑一宿”。**

项目配置要求也很有意思。最低配置只要 4 核 CPU + 4GB 内存，GPU 不是必需项。README 里专门加了一句说明：

> 如果你主要依赖云端 LLM、云端 TTS 和在线素材源，CPU 与内存比 GPU 更重要

这意味着一个 3000 块的轻薄本也能跑。真正吃配置的是本地 whisper 转录和批量生成，那时候才需要 8GB 以上显存。这种“低配也能用，高配更流畅”的分层设计，让项目的潜在用户池从“有显卡的开发者”直接扩展到“任何有电脑的人”。

---

项目特别感谢里提到了两个商业产品：录咖（reccloud.cn / reccloud.com）和佐糖（picwish.cn）。录咖基于 MoneyPrinterTurbo 做了在线 AI 视频生成器，免部署直接用；佐糖赞助了项目，是做图像处理的 SaaS。这种“开源项目 → 商业产品反哺”的路径，在国内 AI 工具链里越来越常见——开源负责获客和教育市场，商业版负责变现和持续维护。

截至 2026 年 5 月，MoneyPrinterTurbo 的 GitHub Star 数还在涨。它没有论文，没有 benchmark，没有复杂架构图。它只有一个 `MVC 架构`、一个 `config.toml` 配置文件、一个 `webui.bat` 启动脚本，和一份用中文写的、细致到告诉你“不要用中文路径”“路径不要有空格”的 README。

**在 AI 视频生成的军备竞赛里，最稀缺的不是模型能力，是“让普通人真正用起来”的最后一公里。** MoneyPrinterTurbo 把这最后一公里铺成了高速公路——连路障都帮你清掉了。

---

## 参考来源
- GitHub 项目主页：https://github.com/harry0703/MoneyPrinterTurbo
- 项目 README 全部功能说明、配置要求、部署指南、演示视频链接均来源于上述仓库的公开文档

#MoneyPrinterTurbo #Generate #AI #LLM #科技
