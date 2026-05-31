# 2026年Hacker News热帖：7年前开源项目如何重现1992年3D奇迹？

![](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/voxelspace-micro/head.png)

如果你在 1992 年打开一台 486 电脑，大概率看到的是《德军总部 3D》那种单色填充的平面墙。但如果你点开 NovaLogic 的《Comanche》，你会看到连绵起伏的山脉、带纹理的峡谷，甚至还有动态阴影——那一年，距离《毁灭战士》让游戏界学会“黑暗走廊恐怖学”还有整整一年。

**《Comanche》比它的时代早了三年，而它用的技术，是一套连 GPU 都不需要的“2.5D 戏法”。**

![C1W.png](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/voxelspace-micro/content-1.png)

## 1024×1024 张“假 3D”地图，骗过了整个 90 年代

![D1.png](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/voxelspace-micro/content-2.png)

《Comanche》背后的核心技术叫 Voxel Space，2017 年，开发者 Sebastian Macke 在 GitHub 上把它的算法扒了个精光，并附上了从游戏里逆向工程出来的原始高度图和颜色图。这两张图各 1024×1024 像素，每像素一个字节——也就是说，整个地形数据加色彩信息，总共只占 2MB。



![1024×1024 的高度图与颜色图对比，左边是灰度高度，右边是带阴影的卫星视角色彩](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/voxelspace-micro/schematic-1.png)



这个尺寸在今天看来连一张微信聊天截图都不如，但在 1992 年，它撑起了一个足以让所有飞行模拟玩家目瞪口呆的世界。CPU 比现在慢 1000 倍，没有 GPU 加速，3D 渲染全靠多边形填充单色——这是当年的基线。而《Comanche》直接在 CPU 上跑出了一套“射线投射”变种算法：从屏幕最底端开始，逐列往上画竖线，每画一条线就从高度图里取一个高度值，再从颜色图里取对应像素的颜色，碰到比前一条线更高的点就停笔。

这套逻辑的核心代码，用 Python 写出来只有寥寥几行。Macke 在项目页面上直接贴了出来，让人看完的第一反应是：“就这？”

**就这。1992 年最惊艳的 3D 画面，靠的是“画竖线”三个字。**

但它有个致命限制：一张高度图只能存一个高度值，意味着一个地图坐标上不可能同时存在地面和树冠。所以《Comanche》里没有建筑，没有树木，只有地形。那些看起来像山间植被的东西，其实是颜色图上预先“画”上去的阴影和纹理——Voxel Space 引擎完全不计算实时光照，它只是把美术提前烘焙好的颜色原样贴回去。换句话说，你看到的每一帧画面，本质上是一张被“竖着拉伸”的 1024×1024 位图。



![Voxel Space 渲染流程示意——从屏幕底部逐列向上画竖线，高度图决定截止点，颜色图直接取色](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/voxelspace-micro/schematic-2.png)



这个取巧方案带来一个反直觉的结果：在 1992 年的硬件上，它跑得比真正的 3D 引擎快得多。因为不需要做多边形变换、不需要光照计算、不需要纹理映射——只需要查表、画线、停止。NovaLogic 把省下来的算力全砸在了一件事上：分辨率。《Comanche》的地形精度在那个年代是碾压级的，远不是同期《Gunship 2000》那种大色块三角面能比的。

## 一个 7 年前的项目，2026 年冲上 Hacker News 榜首

Macke 这个项目是 2017 年发布的，代码 MIT 开源，但他在 README 里小心翼翼地加了一句：Voxel Space 技术在某些国家可能仍处于专利保护期，而高度图和颜色图是从《Comanche》原版游戏里逆向工程提取的，不在开源许可范围内。

8 年后，2026 年 5 月，这个页面突然被顶到 Hacker News 首页，252 个 point，56 条评论。评论区里一群头发花白的程序员集体陷入回忆杀，有人贴出自己 1994 年用 Turbo Pascal 复刻 Voxel Space 的代码片段，有人争论这和《Outcast》的体素渲染到底是不是一回事，还有人认真分析 NovaLogic 当年是否真的申请过专利——结论是，美国专利号 US5748867，1995 年提交，1998 年获批，标题赫然写着“用于实时渲染体素地形的系统和方法”。



![Hacker News 评论区截图，讨论从算法原理一路歪到 90 年代游戏公司兴衰史](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/voxelspace-micro/schematic-3.png)



让我愣神的是评论区里一个细节：有人说 NovaLogic 后来把 Voxel Space 一路用到 2001 年的《Comanche 4》，然后就在 DirectX 和 GPU 的浪潮里消失了。这家公司没有死于技术不行，而是死于技术太“够用”——当整个行业转向硬件加速的多边形渲染时，Voxel Space 那种“CPU 画竖线”的极致优化突然失去了意义。你优化得再狠，也拼不过一块 TNT2 显卡的多边形吞吐量。

这大概是所有“极致工程技巧”的宿命。它们在某一个硬件断层期大放异彩，然后在下一个断层期被连根拔起。Voxel Space 不是第一个，也不是最后一个——2017 年 Macke 把它开源的时候，评论区里就有人说：“这就像考古学家挖出一套秦弩机，精度惊人，但没人再用弩打仗了。”

**有些技术被淘汰，不是因为不够好，而是因为时代不需要它好了。**

但有意思的是，2026 年重新翻红的不只是怀旧。Hacker News 上有人认真讨论了一个问题：在今天的硬件上，Voxel Space 有没有可能被重新捡起来？讨论的焦点不在游戏，而在无人机地形导航和嵌入式实时渲染——那些功耗受限、没有 GPU、又需要快速生成地形视图的场景。一个 1024×1024 的高度图加颜色图，2MB 内存，CPU 跑十几行代码就能出画面——这种“穷鬼友好型”渲染方案，在某些边缘计算场景里反而比 Unreal Engine 更实际。

这让我想起 NovaLogic 当年给《Comanche》打的一句广告词：“看得见的地方，就有我们的直升机。” 2026 年回头看，这句话可以改成：“没有 GPU 的地方，就有人重新翻出 Voxel Space。”

## 参考来源

- Voxel Space 项目页面：https://s-macke.github.io/VoxelSpace/
- Hacker News 讨论：https://news.ycombinator.com/item?id=48336564

#Voxel #Space #AI #科技

![C2W.png](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-31/voxelspace-micro/content-3.png)
