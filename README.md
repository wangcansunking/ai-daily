# ai-daily

> AI / 大模型 / Agent / 头部科技公司 — 每日自动产出的中文深度文章。

每篇文章包含：

- **1000+ 字**原创中文叙述（基于真实信息源）
- **1 张头图**（gpt-image-2 生成，cute / playful 风格）
- **1-2 张内容图**（从原文 HTML 抓取的真实图）
- **0-2 张结构图**（gpt-image-2 schematic，用于对比 / 流程 / 架构可视化）

按日期归档：`{YYYY-MM-DD}/{stamp}-{article|micro}.md`。

## 怎么阅读

GitHub 直接渲染 markdown，点进任意日期目录看 `.md` 即可。所有图片都在 .md 同目录，相对引用 `![](xxx.png)` 在 GitHub 网页上能正常显示。

## 怎么生产的

这个仓库只是**作品集**，背后的 5-Agent 自动化流水线在另一个私有仓：

| Agent | 模型 | 干的事 |
|---|---|---|
| Scout | — | 扫 100+ 信息源（RSS / GitHub Trending / Reddit / HN / Web 搜索），关键词过滤聚焦 AI + 头部科技公司，按 `源质量^1.3 × 0.22 + 传播潜力 × 0.25 + 新鲜度 × 100 × 0.20` 评分排序 |
| Writer | DeepSeek-V4-Pro (写稿) / DeepSeek-V3.2 (critique 评委) / DeepSeek-V4-Flash (改稿) / Kimi-K2.6 (标题) | 7 道工序，每篇文章过 100+ 条 AI 腔正则审校 + 评委打分 ≥ 70 才放行 |
| Illustrator | gpt-image-2 | 头图 cute 风格 + schematic 结构图 |
| Publisher | — | 把文稿 + 图片同步到这个公开仓 |

每天 4 个时间窗口（07:00 / 12:00 / 18:00 / 21:00）共产 13 篇（4 长文 + 9 微头条）。

## License

文章内容采用 CC BY 4.0；图片中 gpt-image-2 生成的部分可自由使用，从原文抓取的真实图片版权归原作者。

## 灵感来源

[《我用 Hermes 通过 5 个 Agent 搭了一条内容自动生产线》](https://github.com/wangcansunking/daily-report/blob/main/example/%E6%88%91%E7%94%A8Hermes%E9%80%9A%E8%BF%875%E4%B8%AAAgent%E6%90%AD%E4%BA%86%E4%B8%80%E6%9D%A1%E5%86%85%E5%AE%B9%E8%87%AA%E5%8A%A8%E7%94%9F%E4%BA%A7%E7%BA%BF%2C%20%E7%8E%B0%E5%9C%A8%E6%AF%8F%E5%A4%A9%E8%83%BD%E5%8F%9113%E7%AF%87%2C%20%E6%9E%81%E5%A4%A7%E7%9A%84%E6%8F%90%E9%AB%98%E4%BA%86%E5%88%9B%E6%95%88%E7%8E%87.pdf) — 这个项目是对原作的 Claude Code + Azure AI Foundry 重新实现。
