---
title: Cursor Live Apps 3步上线工位图
date: '2026-11-27'
weekday: 星期五
slug: cursor-live-apps
category: ai-tools-practice
description: 从 Cloud Agent 的预览走到定向分享，用一张模拟工位图跑通多人协作。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/cursor-live-apps.png
batch: noon
cover_style: minimalist-still-life
---

# Cursor Live Apps 3步上线工位图

Cursor 里的应用预览跑起来了，你发给同事的却还是一张截图。这是很多 AI 写工具的项目卡住的最后一公里。Cursor 在 11 月 5 日推出 Live Apps，把 Cloud Agent 的应用预览、托管和分享接到一起。试手可以从一个小目标开始，让三个人打开同一张工位图。

## 要点速览

- 用 Cloud Agent 构建网页应用，需要保存数据时可由 Cursor 配置 Postgres。
- 先在 Agent 会话里看预览，再用 Publish 选择应用地址和访问范围。
- 访问范围有受邀用户、团队和公开三种。前两种通过 Cursor 登录。
- 首次上线可以用假数据、定向邀请和两次独立浏览器会话跑完整条路径。

## 第一步，让 Agent 搭个够小的工位图

从 Cloud Agent 发起新任务，明确说你要构建一个可发布的网页应用。这个入口能同时覆盖网页、托管和登录。官方演示用的是给新同事分配座位的工位图，拿来练手刚好有列表、筛选和状态变更三个要素。

![Cloud Agent 已生成应用预览](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/cursor-preview.png)

*图片来源：Cursor《Build Live Apps in Cursor》发布演示截帧，2026-11-05。*

可以直接给它这段需求。**做一个供三位同事试用的工位图。预置 12 个模拟座位，显示楼层、编号和空闲状态。允许应用内的「工位管理员」分配或释放座位，经刷新后保留。使用虚构姓名。先给我一个可运行预览，等我确认再发布。**

先把需求收窄有两个好处。第一，点三四下就能走完主要场景，改了什么也容易看见。第二，应用需要什么资源一开始就清楚。只展示座位时可以用种子数据。要让两位同事看到同一次分配，则要跨会话持久化。官方文档给出的托管数据库是 Postgres。

在预览里先分配一个座位，再尝试把同一个座位给第二个人。可让 Agent 把冲突处理做在服务端，并给出容易理解的提示。也可以让它把空状态做成一句「这一层暂时没有空位」，这样同事第一次点筛选时就知道系统在正常工作。

## 第二步，给链接选个合适的门

确认预览能跑，再点 Publish。官方流程会在这里选择应用地址并设置可访问范围。发给同事的是应用的访问链接，可以从自己的浏览器打开，摆脱作者电脑上那个临时预览窗口。

![Cursor 发布对话框中的 URL 和访问范围](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/cursor-publish.png)

*图片来源：Cursor《Build Live Apps in Cursor》发布演示截帧，2026-11-05。*

| 访问范围 | 谁能打开 | 适用场景 |
| --- | --- | --- |
| People you invite | 受邀成员或组，需登录 Cursor | 三人试用、收第一轮反馈 |
| Team | 登录 Cursor 的整个团队 | 已确定向团队开放的内用工具 |
| Public | 拿到链接的人 | 使用虚构数据的公开演示 |

对首次试点，定向邀请就够了。可以给同事 Viewer 角色，让他从另一浏览器会话进来；再用未受邀的账号打开同一链接观察登录和权限提示。想把座位分配这类业务动作限制给特定角色，要在应用内部落实这套授权，别把部署面板的角色名称直接当业务权限。

团队和企业的公开分享默认关闭，管理员可在 Apps Dashboard 中管理。产品公告把付费且启用 Cloud Agents 列为使用条件。发布入口和团队策略以自己账户所见为准。对于小型试用，先确定谁能打开、谁可以继续改应用，再把网址发出去。

## 第三步，从另一个窗口打开上线版本

![发布后的工位图网页](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/27/cursor-live.png)

*图片来源：Cursor《Build Live Apps in Cursor》发布演示截帧，2026-11-05。*

给两位试用者分配应用内的「工位管理员」权限，再让第一位同事分配 A03，第二位同事刷新后看到 A03 已占用。再让第二位同事改动其他座位，确认这两人的操作没有相互覆盖。两人拿着同一个网址，数据和状态也终于有了同一份可以讨论的版本。

试用顺畅以后，如需接外部服务，可在 Dashboard 的 Secrets 给已发布应用关联保存的密钥。Cursor 文档说明，添加密钥会启动新部署。密钥可以先限制给指定应用，使用最小权限的测试凭据，业务数据再按团队安排接入。

试点再往前走一步，可以请 Agent 补一份短说明。写清数据库保存什么、谁能改动、出现两个同事同时分配时网页怎么提示。下一次有人接手，读完说明和网页就能继续改。之后要加一列部门或一张楼层图，先保住现有座位编号和数据，再把改动给这三个人试一轮。

预览和线上还各有一个容易忽略的状态。前者可能用的是 Agent 环境里的默认数据，后者是同事实际打开的应用。试点时在页面上显示「模拟数据」和最后更新时间，再让同事回报他打开的是哪个地址。这样一句「我这里还是空的」就能定位到具体版本，讨论也能直接落在页面上。

如果这项需求最后只剩一个静态楼层图，数据库就可以从方案里拿掉。如果真正有用的是多人实时分配，下一轮就围绕换座、撤销和保存失败做改动。先把这类体验打磨好，再考虑把场景扩到整层楼。

最大的工作流变化是反馈从「能发张截图吗」变成「我打开链接，在 B 区点了一下」。如果一页工具的价值仍有争议，给一小组同事真实点击的机会，比再雕一个界面更容易知道该加哪一项功能。

Cursor Live Apps 可以让第一个内部工具从一次 Agent 会话走到一条可打开的链接，先从一张工位图开始就够了。

## 来源

- [Cursor 发布公告及官方演示](https://cursor.com/changelog/build-live-apps)
- [Cursor Apps 文档：发布、权限与密钥](https://cursor.com/docs/cloud-agent/apps)
- [Cursor Cloud Agent 环境配置](https://cursor.com/docs/cloud-agent/setup)
