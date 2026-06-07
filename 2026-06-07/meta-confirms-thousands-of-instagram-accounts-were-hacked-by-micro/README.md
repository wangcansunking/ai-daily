# 黑客只说"给我"：Meta AI送出2万Ins账户

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-07/meta-confirms-thousands-of-instagram-accounts-were-hacked-by-micro/head.png)

2026 年 6 月 6 日，一个普通的星期五。Meta 悄悄向缅因州总检察长办公室提交了一份数据泄露通知，编号不详，措辞冷静，格式规范——和过去几年里任何一家科技公司递交的合规文件没有两样。但这份文件里藏着一个数字：20,225。两万零两百二十五个 Instagram 账户被黑，持续时间从 4 月 17 日一直拉到本周才被堵上。而完成这场攻击的工具，不是零日漏洞，不是钓鱼邮件，不是社工库——是 Meta 自己的 AI 聊天机器人。

黑客只做了一件事：**开口要**。

![Meta confirms 1000s of Instagram accounts were hacked by abusing its AI chatbot](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-07/meta-confirms-thousands-of-instagram-accounts-were-hacked-by-micro/content-1.jpg)

**一个不设防的密码重置接口，伪装成了“智能客服”的模样。**

![two screenshots side-by-side, showingn the Meta AI support assistant showing a prompt that says, "I've been hacked," fol](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-07/meta-confirms-thousands-of-instagram-accounts-were-hacked-by-micro/content-2.jpg)

## 你问它要验证码，它就给了

整件事的技术内核简单到让人不适。Meta 在 Instagram 的账户恢复流程里接入了一个 AI 辅助系统，本来是为了让用户更“便捷”地找回密码。但问题是，这个系统里藏着一条没有做校验的代码路径：当有人请求密码重置时，聊天机器人会发一个验证链接到用户提供的邮箱——但它**不检查这个邮箱到底是不是账户绑定的那个**。

据 404 Media 和 TechCrunch 此前的联合报道，黑客发现只要直接对聊天机器人说“把验证码发到这个邮箱”，机器人就照做。没有二次确认，没有密保问题，没有任何人工介入。攻击者拿到重置链接，改密码，登进去，整个账户连同关联的所有账号、私信历史、联系人信息、生日、个人资料，全部易手。

Meta 在数据泄露通知里是这样写的：

> 由于一个独立代码路径中的缺陷，系统未能正确验证请求密码重置的人所提供邮箱，是否与该用户 Instagram 账户的关联邮箱一致。因此，当某人提供了一个此前未关联该账户的邮箱时，系统错误地将密码重置链接发给了这个未关联的邮箱，而非拒绝请求。

读三遍这段话。它说的是：**系统设计上根本没把“验证邮箱归属”这件事当作必要步骤**。不是被绕过了，不是被攻破了，是压根没写那行校验。



![攻击流程简化示意，用户→Meta AI 聊天机器人→密码重置链接→黑客邮箱→账户被接管](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-07/meta-confirms-thousands-of-instagram-accounts-were-hacked-by-micro/schematic-1.png)



而受害者的第一反应是什么？很多人直到收到 Meta 的“您的密码已被重置”通知，才知道自己中招了。更讽刺的是，据部分用户在 X 上的反馈，Meta 用来通知受害者的方式——也是发一封密码重置邮件。这让不少人最初以为是钓鱼，直接忽略。

## 20,225 个账户背后的时间线，和那个被裁掉的团队

缅因州那份文件里还有一个关键信息：黑客攻击**最早可以追溯到 4 月 17 日**，持续到 6 月初才被完全封堵。将近两个月的时间，一个全球顶级社交平台的核心账户恢复系统，被一个“问一句就给你”的逻辑漏洞敞着口子，无人察觉。

我们来看时间线的另一面。据 The Verge 和 Business Insider 此前报道，Meta 在 2026 年 Q1 新一轮大规模裁员，数千名员工离开，其中安全工程和信任与安全团队是重灾区。与此同时，Meta 董事会批准了对高管的股票激励计划，理由是“需要在 AI 竞赛中保留顶尖领导力”。

也就是说，在 AI 功能被加速推向产品线的同一时期，负责审计这些功能安全性的工程师正在打包个人物品。



![时间线对照表——4 月 17 日攻击开始 / Q1 安全团队裁员 / 5 月攻击持续未被发现 / 6 月初漏洞修复 / 6 月 6 日数据泄露通知提交](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-07/meta-confirms-thousands-of-instagram-accounts-were-hacked-by-micro/schematic-2.png)



Meta 在声明中说，已经暂时关闭了这个 AI 聊天机器人，移除了允许它处理密码重置的代码路径，并且正在检查其他平台上的类似机器人。但官方同时承认，他们“不清楚”黑客在接管账户后到底访问了哪些个人信息。发给 Meta 公关团队的邮件截至周六早上未获回复。

这句话值得划重点：一家市值万亿美元、以“连接世界”为使命的公司，在两万多个账户被陌生人完整接管两个月后，**仍然无法告诉受害者到底泄露了什么**。

## 这不是 AI 安全问题，这是工程纪律问题

让我愣神的是，整个事件里没有任何“高级攻击技术”。没有对抗样本，没有提示注入的复杂链条，没有模型幻觉被利用——就是一段代码没写邮箱校验。如果这是一个传统 Web 表单的密码找回接口，任何一个初级安全审计都会在第一天把它标红。但当同样的逻辑缺陷被裹在一个叫“AI 助手”的聊天界面里时，它神奇地隐身了 50 多天。

这暴露的不是 AI 有多危险，而是**AI 产品化过程中工程纪律的全面退步**。

当“聊天机器人”成为一切交互的入口，安全团队的审计清单似乎忘了更新——他们可能还在检查 API 端点有没有限流、数据库查询有没有参数化，但没人去问一个更基础的问题：这个能跟用户自由对话、且具备执行敏感操作能力的模型，它的每一条代码路径都校验了权限吗？

Meta 在通知里还提到，受影响的主要是没有开启双因素认证（2FA）的账户。这当然是一个技术事实，但把它放在叙述里，很难不读出一种微妙的 blame-shifting——“你要是开了 2FA 就没事了”。可问题在于，密码重置功能本身的目的是什么？恰恰是当用户**无法使用常规认证方式时**（比如丢了手机、忘了密码）的最后一道救生索。如果这条救生索可以被任意路人伸手拽住，那指责用户“没系另一根绳子”多少有点荒诞。



![Meta AI 聊天机器人被滥用流程 vs 正常密码重置流程对比，标注校验缺失环节](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-07/meta-confirms-thousands-of-instagram-accounts-were-hacked-by-micro/schematic-3.png)



截止目前，Meta 没有披露是否有更多平台（Facebook、WhatsApp）的聊天机器人存在同类漏洞，也没有说明是否会对受影响用户提供身份盗窃保护或其他补偿。唯一确定的是：AI 聊天机器人被关了，那个有问题的代码路径被删了，两万多封通知邮件正在陆续发出。

而整个行业该问的问题是：**当所有公司都在把“用 AI 重塑一切交互”写进 OKR，有多少人记得先检查一下，这个 AI 到底有没有权限做它被要求做的事？**

黑客们用两个月时间证明了一件事——有时候攻破一个系统不需要绕过防线，只需要礼貌地问一句。而那个回答“好的”的，是 Meta 花了几十亿美金押注的 AI。

这大概是 2026 年迄今为止，关于“AI 安全”最朴素也最刺眼的一个注脚。

#Meta #Instagram #AI #科技

![an Oura ring stood up on its side, against a red lit backdrop.](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-06-07/meta-confirms-thousands-of-instagram-accounts-were-hacked-by-micro/content-3.jpg)
