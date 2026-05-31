# Office 2019“买断”变“只读”：微软证书过期策略遭科技媒体曝光

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/microsoft-office-2019-and-2021-for-mac-view-only-conversion-micro/head.png)

2018 年 9 月 24 日，微软的 Jared Spataro 在官方博客上宣布 Office 2019 正式上市，定位是“一次性发布，不会有后续功能更新”。同期，Microsoft Store 的购买页面标价 149.99 美元，文案特意与订阅制划清界限——“一次性购买没有升级选项，想用下个大版本就得全价再买”。这不是什么模糊的表述，这是微软自己划下的契约边界：你付一笔钱，拿走一套永久可用的生产力工具。当时全球有数千万用户基于这个承诺掏了腰包，仅 Office 2019 一个版本就在发布后 18 个月内卖出了超过 3000 万份永久授权。

八年后的 2026 年 7 月 13 日，这份契约将被微软单方面作废。不是通过合同修订，不是通过弹窗告知，而是靠一个在服务器端预设的数字证书——它在那天准时过期，你的 Word、Excel、PowerPoint 图标依然亮着，文档依然能打开，文字图表清晰可见，但编辑、保存按钮全部灰掉。微软把这种状态称为“reduced functionality mode”，科技媒体 AppleInsider 在 5 月 28 日的报道中说得更直白：“微软将在那天彻底砖化 Office 2019。”砖化——bricking——这个词通常只用来描述硬件变废铁，现在微软把它带到了你“买断”的软件上。

![The same Microsoft URL captured on May 30, 2026, re-dated Published: May 15th, 2026.[4]](content-1.png)

**证书可以续签，微软选择不续。这不是技术故障，这是一次经过精心计算的产品报废决策。**

![Internet Archive snapshot of the page from June 3, 2023; originally published April 12, 2023.[2]](content-2.png)



![一条清晰的时间轴——2018 年“一次性购买”广告 → 2023 年“继续运行”承诺 → 2026 年 5 月承诺页面被悄悄修改 → 2026 年 7 月 13 日证书过期进入只读模式](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/microsoft-office-2019-and-2021-for-mac-view-only-conversion-micro/schematic-1.png)



## “继续运行”四个字，保质期只有三年

翻时间线会让问题更清晰。Office 2019 for Mac 在 2023 年 10 月 10 日到达官方支持终止日期。微软当时在支持页面上写了一段安抚用户的话，互联网档案馆在 2023 年 6 月 3 日存下了那版快照，白纸黑字：

> Support for Office 2019 for Mac will end on October 10, 2023. Rest assured that all your Office 2019 apps will continue to function—they won't disappear from your Mac, nor will you lose any data.

“continue to function”——继续运行。这四个单词是买断制软件的灵魂，是用户支付全款所购买的核心预期：我不再获得新功能和安全更新，但我已经拥有的工具，至少能一直启动、一直干活。微软在 2023 年说出这句话的时候，显然知道用户最恐惧的是什么——不是没有新功能，而是手里的软件变成废品。

然后到了 2026 年 5 月。同一个 URL，微软重新发布了页面，发布日期标着“May 15th, 2026”。那段话被改成了：

> Support for Office 2019 for Mac ended on October 10, 2023. Rest assured that all your Office 2019 apps won't lose any data. Your data can be accessed on any supported Microsoft 365 or Office product.

“continue to function”消失了，没有任何解释，没有任何过渡声明。取而代之的是“你的数据可以迁移到 Microsoft 365”——一个付费订阅产品，个人版每月 6.99 美元，家庭版每月 9.99 美元。旧金山 IT 咨询公司 JimmyTech 在 2026 年 5 月 21 日的博客里直接定性：“微软正在打破那个承诺。”

再看证书机制的技术细节。微软管理员文档里写得很清楚：Office 应用使用数字证书验证授权，当前证书在 2026 年 7 月 13 日过期。更新到最低要求版本——macOS 上 16.83，iOS 上 2.93——就能拿到续签证书，继续正常工作。但 Office 2019 有一个硬性的版本号上限，永远达不到 16.83。微软自己的支持文档也承认：“无法通过更新或重新安装 Office 2019 for Mac 解决此问题。”这意味着 2019 用户被锁死在一个技术上无解的困局里，而这个困局是微软主动选择不去解的。



![2023 年 6 月 3 日快照 vs 2026 年 5 月 30 日快照的页面文字对比，高亮被删除的“continue to function”和被新增的“switch to Microsoft 365”引导语](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/microsoft-office-2019-and-2021-for-mac-view-only-conversion-micro/schematic-2.png)



## 一纸证书背后的商业算盘

JimmyTech 的评论戳中了要害：“证书本可以续签。微软利用这次过期作为退役旧版 Office 的截止日期，而不是悄悄续签证书，这是一个选择。”

技术上讲，续签一张数字证书的成本微乎其微——本质上就是生成一个新的证书文件，通过更新通道推送下去。这不是安全漏洞迫使微软关闭旧版本，不是操作系统底层架构变更导致的不兼容——Windows 和 Android 版本的 Office 完全不受此次证书过期影响。受影响的是 macOS 和 iOS 上的 Office 2019，以及那些没有及时更新的 Office 2021。而 2021 用户只要还在支持周期内、系统版本够新，升级到 16.83 就能解决。唯独 2019 用户被精准排除在修复路径之外。

再看微软给受影响用户的三条出路：第一，继续用只读模式——你的软件还在，但已经退化成了一个文件查看器；第二，转用免费的 Microsoft 365 网页版——功能阉割、需要联网、数据留在微软的云上；第三，付费订阅 Microsoft 365，或者购买新的 Office Home 2024 永久授权。2026 年 5 月中旬开始的客户通知邮件里，微软还夹了一个“免费 Microsoft 365 Personal 试用”，需要绑定支付方式，到期自动转付费，取消流程藏在三层菜单下面。PiunikaWeb 在 5 月 16 日最早报道此事时，用“largely negative”

#Microsoft #AI #科技

![Microsoft degrades functionality of perpetually-licensed offline products](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/microsoft-office-2019-and-2021-for-mac-view-only-conversion-micro/content-3.png)
