# 323个赞，OpenBSD如何用安全哲学重构30年老工具rsync？

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/kristapsdz-openrsync-micro/head.png)

有一组数据我想先摆在这里：

323 个 upvote。141 条评论。一个只有大约 10,000 行 C 代码的项目，在 Hacker News 上炸出了当天最大的水花。

![Openrsync: An implementation of rsync, by the OpenBSD team](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/kristapsdz-openrsync-micro/content-1.png)

这不是什么新出的多模态大模型，也不是哪家刚融了十亿美金的独角兽。这是 OpenBSD 团队写的 rsync——一个 1996 年就诞生的文件同步工具的重新做到。

名字叫 openrsync。

**当整个科技圈都在追逐 AGI 的时候，一群做操作系统的人，选择去重写一个 30 年前的工具。**

## 这不是“又一个 rsync”，这是 OpenBSD 的安全哲学在代码层面的暴力执行

如果你点进 openrsync 的 GitHub 仓库，最先看到的不是 feature list，不是 benchmark，而是一段关于安全的声明——而且语气相当不客气。

它告诉你，这个项目使用了 OpenBSD 原生的 `pledge(2)` 和 `unveil(2)` 系统调用。`pledge` 用来限制进程在整个生命周期中可以使用的系统资源类别（比如 receiver 模式只有在非 dry-run 时才被允许写磁盘，daemon 客户端只在需要时有 DNS 和网络访问权限）。`unveil` 则把文件系统的访问范围锁死在目标目录内——一旦目标目录创建完毕，任何试图“越狱”到其他路径的操作都会被内核直接拒绝。



![pledge 和 unveil 的权限收缩示意，进程从启动到运行逐步缩减可用系统调用和文件路径范围](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/kristapsdz-openrsync-micro/schematic-1.png)



这意味着什么？意味着就算 openrsync 的代码里有漏洞，攻击者能做的事情也极其有限。你连文件系统都逃不出去，谈什么提权？

更有意思的是 MD4 哈希的种子生成。原版 rsync 用 `time(3)`，openrsync 用 `arc4random(3)`——一个密码学强度的随机数生成器。这只有在 server 模式下才相关，因为种子由 server 端生成。但 openrsync 选择在所有模式下都这么干。

**在安全这件事上，OpenBSD 团队从来不相信“够用就好”。**

然后他们还很直白地告诉所有想在 Linux 上运行 openrsync 的人：Linux 的安全设施是一团乱麻（“Linux's security facilities are a mess”），而且需要一个专家级的人才能正确加固。FreeBSD 的 Capsicum 也许能做到类似的事情，但他们不确定。至于 macOS、NetBSD、OmniOS——能编译，能跑，但没有 pledge 和 unveil 的保护，你就是在“接受来自公共网络的任意数据写到你的文件系统上”。

最后那句反问几乎是挑衅：“openrsync 大约有 10,000 行 C 代码：你相信我不会犯错吗？”

这句话的潜台词是：你不该相信我。你应该依赖操作系统级别的强制隔离。

## 架构上的一个“反传统”决定：干掉独立进程，用事件循环

原版 rsync 的架构里有一个独立的“generator”进程。它由 receiver fork 出来，负责计算文件的块哈希，然后跟 sender 和 receiver 三方通信。这是 rsync 协议里最核心的流水线设计。

openrsync 的做法是：把 generator 和 receiver 合并成一个进程。



![原版 rsync 三进程架构 vs openrsync 单进程事件循环架构对比](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/kristapsdz-openrsync-micro/schematic-2.png)



怎么做到的？用了一个有限状态机驱动的事件循环。receiver 同时承担两个角色：uploader（负责扫描文件列表、异步打开文件、计算块哈希、上传给 sender）和 downloader（等待 sender 发来的数据，异步打开已有文件块拷贝，然后继续读取新数据）。

关键在这里：uploader 在等待文件打开时不会阻塞。它把控制权交还给事件循环。downloader 同理。当文件就绪时，事件循环再把控制权交回来。

这不仅仅是代码行数变少了。这是在用单进程的异步模型，去替代多进程的同步模型。

原版 rsync 依赖 `fork(2)` 来并行处理。openrsync 依赖的是非阻塞 I/O 和状态机。在 2025 年的语境下看这个选择，你会意识到它跟整个服务端架构的演进方向完全一致——从多进程到多线程到事件驱动，rsync 这个 30 岁的工具也在经历同样的范式迁移。

而这件事的触发点，甚至不是 rsync 本身。

openrsync 最初是作为 rpki-client(1) 项目的一部分被写出来的——这是一个 RPKI（Resource Public Key Infrastructure）验证器，用于验证互联网路由来源的合法性。RPKI 需要从多个远端仓库同步大量证书和签名文件。rsync 是 RPKI 依赖的核心传输协议之一。

所以这根本不是一个“重写经典工具”的业余项目。这是互联网基础设施安全链条上的一环。出资方是 NetNod、IIS.SE、SUNET 和 6connect——全是互联网基础设施运营商。



![RPKI 验证流程中 rsync 的角色，从远端仓库拉取 ROA 证书到本地验证器](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/kristapsdz-openrsync-micro/schematic-3.png)



现在再看 openrsync 的那些安全设计，就完全说得通了：它处理的不只是你的照片备份，而是决定全球 BGP 路由是否合法的证书数据。如果 rsync 被攻破，攻击者可以伪造路由来源，劫持整个 IP 段。

**这就是为什么一个 10,000 行的 C 项目值得 323 个人在深夜点赞。**

block 大小算法的细节也很值得玩味。openrsync 沿用了 rsync 协议的规则：block 大小取文件总大小的平方根，然后向上取整到最近的 8 的倍数。最小 block 是 700 字节。

为什么是 8 的倍数？文档里写的是“原因未知”（“For reasons unknown”）。这是一个在 1996 年由 Andrew Tridgell 写进 rsync 的设计选择，然后被 openrsync 原样继承。30 年后，没人记得原因，但所有人都遵守。

这大概是开源世界里最浪漫的一种考古学。

openrsync 目前已经被合并进 OpenBSD 的 base 系统。GitHub 上的那个仓库只是 OpenBSD 版本加上一些可移植性的胶水代码。官方支持的操作系统只有 OpenBSD。Linux、FreeBSD、macOS 能编译运行，但那叫“portable”，不叫“supported”。

文档里还有一句很 OpenBSD 风格的话：“如果你想写自己的 rsync 做到，协议手册应该包含所有需要的信息。”

没有“欢迎贡献”。没有“社区友好”。就是干巴巴地告诉你，协议文档在 `rsync(5)` 和 `rsyncd(5)` 里，代码在 `main.c`、`sender.c`、`receiver.c`、`blocks.c`、`hash.c` 里，剩下的你自己看。

**这就是一群把操作系统当信仰的人，在用写内核的态度写一个文件同步工具。**

---

## 参考来源
- https://github.com/kristapsdz/openrsync
- https://news.ycombinator.com/item?id=48334854

#Openrsync #An #OpenBSD #AI #科技
