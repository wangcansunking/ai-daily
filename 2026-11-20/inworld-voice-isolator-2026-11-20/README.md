---
title: Inworld 开源语音降噪模型，噪声去掉多少才好
date: '2026-11-20'
weekday: 星期五
category: model
slug: inworld-voice-isolator-2026-11-20
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/20/inworld-voice-isolator-2026-11-20.png
description: 比较离线与流式语音降噪的代价，以及呼吸、笑声和背景说话声如何影响评估。
tags:
- 模型
track: deep-dive
top_five_rank: 5
batch: noon
---

# Inworld 开源语音降噪模型，噪声去掉多少才好

![穿过声学空间的橙色波形](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/20/inworld-voice-isolator-2026-11-20.png)

Inworld 在 11 月 18 日公开 Voice Isolator，提供离线和实时两种 **48 kHz** 语音降噪模型，代码与权重采用 Apache 2.0 许可。模型卡给出的参数规模分别为 **64.0M** 和 **58.2M**。项目尝试在清除背景乐、杂音和部分背景说话声时保留前景人声的音色以及呼吸、笑声等细节。

## 同一段声音有两种处理方式

离线版可以利用前后的声音来判断当前片段是什么。模型卡说明，长输入按重叠的八秒窗口处理，窗口之间再做拼接。这种方式适合已经录好的采访和播客。实时版则要把一段声音尽快交回给通话下游，训练支持 0、20、40 毫秒几种前瞻设置。设置为 20 毫秒时，模型卡列出的算法延迟是 63 毫秒。采集、重采样、推理耗时和网络等环节还会影响实际通话感受到的延迟。

设想主持人刚把话筒递给嘉宾。录音剪辑可以用上下文判断这段手部摩擦声应压到什么程度，连线主持则更在意嘉宾第一声有没有被吞掉、下一次接话是不是仍然及时。前者可以离线一遍遍听，后者最好在实际设备上从收音到播放测整条链路。两种模式的指标不必相同。

![Inworld Voice Isolator 双检查点网络结构](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/20/source-inworld-architecture.png)

*图片来源：Inworld Voice Isolator 仓库 README 的官方架构图，2026-11-20 查阅。*

## 一段已知原始素材最适合评估

可以用自己有权处理的一段干净语音，混入可辨别的键盘声、音乐、与目标人声相近的另一条声音，分别保留混音前的干净参考。安装官方 Python 包后，使用 `voice-isolate noisy.wav clean.wav` 生成离线输出。需要比较实时路径时可加 `--mode streaming --lookahead 2`。参数值 2 指两帧，对应 20 毫秒前瞻。权重会在首次使用时下载。把输入和输出对齐并调到相近的回放音量，再核对词尾、换气声和来自背景的串音。

对采访还可以另挑一段多人同时说话的片段。模型卡指出，两条人声电平相近时往往都会保留。这对希望只听到固定发言人的用户很重要。测试时要把目标说话人的保留率与背景人声残留单独记分。如果原始录音已经削波或缺失一段频带，第一版的抑噪能力也无法把缺失内容还原出来。

## 把厂商分数带回自己的噪声里

Inworld 公布的内部 VIBE 降噪评估中，离线版得分 0.768，实时版得分 0.718。发布方明确说评估集暂未公开。两个数字适合用来理解公司报告的质量与实时性取舍。部署前的比较仍应使用自己的设备、语料和噪声条件，并记录信号中哪些细节是一错就无法接受的。

![厂商 VIBE 降噪评估对比图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/20/source-inworld-vibe-denoising.png)

*图片来源：Inworld Voice Isolator 仓库 README 的官方 VIBE 降噪图，2026-11-20 查阅。*

实时通话试点可以分别观察三种声音：长空调噪声、突然响起的碰撞声、另一位说话人的连续语句。每类都放入轻声或吞音明显的短句，交给实际使用者听。回放结束后问他们具体哪几个字听错了、笑声是否还在、下一轮对话有无明显拖延。这些结果直接影响选用离线版还是实时版。

## 🔗 引用链接

- [Inworld Voice Isolator 发布说明](https://inworld.ai/blog/introducing-voice-isolator)
- [Hugging Face 模型卡](https://huggingface.co/inworld/voice-isolator)
- [Voice Isolator 官方仓库](https://github.com/inworld-ai/voice-isolator)
