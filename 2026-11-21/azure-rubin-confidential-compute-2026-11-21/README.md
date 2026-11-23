---
title: Azure 的机密 AI 演示，为什么先验证再推理
date: '2026-11-21'
weekday: 星期六
slug: azure-rubin-confidential-compute-2026-11-21
category: industry
description: 拆解单节点四卡演示的证明链与密钥授权。
cover: https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/azure-rubin-confidential-compute-2026-11-21.png
batch: noon
track: deep-dive
cover_style: conceptual 3d render
---

# Azure 的机密 AI 演示，为什么先验证再推理

![Azure 的机密 AI 演示，为什么先验证再推理的概念封面](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/azure-rubin-confidential-compute-2026-11-21.png)

微软在 Ignite 2026 展示了 Azure 的 Vera Rubin NVL72 机密裸金属技术。演示展示如何在一个节点、四块 GPU 上运行 3970 亿参数模型，先验证硬件和软件状态，再运行推理。对企业来说，这次最值得拆开的，是谁来证明推理环境可信，以及谁能决定什么时候交出密钥。

## 要点速览

- NVL72 是机架级平台，微软会场演示的范围是单节点四块 Rubin GPU。
- 裸金属设计由租户控制主机系统、运行时和应用，租户主机内核也属于可信计算基。
- 演示使用本地验证器。机密虚拟机、容器及 Foundry 集成仍在开发。

![Azure 机密计算 · 微软 Azure 机密裸金属架构原图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/azure-architecture.jpg)

*图片来源：微软 Azure 机密裸金属架构原图，2026-11-17。*

## 机架的能力，和演示的范围

微软引用的 NVL72 平台架构包括 72 块 Rubin GPU、36 颗 Vera CPU 及其互连。一个模型如果跨多块卡切分，设备之间传输的中间数据也要进入保护范围。微软的设计将 CPU、GPU 的内存保护与物理链路加密放在一起，涉及 C2C、PCIe IDE 和 NVLink。

会场演示选了较小的路径。四块 GPU 运行 vLLM，验证器检查组件签名、令牌绑定及 GPU 测量值，然后展示推理。这给工程团队一个具体参照：先把证据验证跑通，再讨论更大的节点数、跨节点通信和生产可用性。

| 观察对象 | 微软披露的范围 |
| --- | --- |
| NVL72 平台 | 72 块 Rubin GPU、36 颗 Vera CPU |
| Ignite 演示 | 单节点四块 GPU，vLLM 运行 3970 亿参数模型 |
| 证据核验 | 演示采用本地验证器 |
| 后续形态 | 机密虚拟机、机密容器和 Foundry 集成仍在开发 |

![Azure 机密计算 · NVIDIA Vera Rubin NVL72 产品页机架原图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/azure-rack.jpg)

*图片来源：NVIDIA Vera Rubin NVL72 产品页机架原图（取阅），2026-11-21。*

## 从证据到密钥，有一道授权判断

证明材料分几份。平台令牌记录固件测量值，租户令牌记录主机内核测量值，每块 GPU 另提供自己的固件与配置测量。验证器可以检查令牌是否来自预期组件、是否属于同一次环境核验，以及当前测量值是否符合基准。

这些检查能回答环境处于什么状态。真正决定数据何时可用的是密钥策略。比如，机构可以约定只向通过指定验证条件的实例交付解密密钥。主机内核升级后，旧基准可能无法匹配，团队就得先评估新版、更新允许的测量值，再走发布流程。

这里的租户责任比买一台托管推理服务更重。租户掌握主机和应用的运维，也掌握可能读取内容的权限。要限制内部操作员访问业务数据，仍需在应用、身份和运维层明确授权。微软将租户系统划入可信计算基，正是为了标明这条责任边界。

![Azure 机密计算 · 微软 Ignite 机密计算博客演示段落原文截图](https://raw.githubusercontent.com/wangcansunking/daily-report-images/master/content/2026/11/21/azure-demo.png)

*图片来源：微软 Ignite 机密计算博客演示段落原文截图，2026-11-17。*

## 采购和试点要问三个问题

- 谁管理主机镜像？把内核、运行时和推理服务纳入变更管理，记录哪些版本可以获得密钥。
- 信任根和验证器怎么部署？区分会场的本地核验与业务所需的远程策略执行，并明确失败时的告警和恢复责任。
- 目标规模需要哪些证据？先确认演示、技术预览和正式服务分别覆盖哪些实例形态，再衡量延迟、吞吐和实际可用容量。

可以从一项脱敏推理任务开始。先记录启动、验证和推理的耗时，再换成一份未通过核验的镜像，确认密钥释放是否被拒绝。最后执行一次升级和回滚，看看可信基准、模型部署和运维记录能否对齐。这样的步骤可以把验证条件写进验收要求。

试点记录还可以保存一组验证失败的原因，例如签名无效、组件缺失或策略不匹配。这样排障人员能分辨平台证据和应用部署分别由谁负责。

微软在 Ignite 展示的是一条可验证的机密推理路径，企业能否用起来，取决于密钥策略、运维责任和部署规模能否同时落地。

## 来源

- [微软 Ignite 计算基础设施综述](https://techcommunity.microsoft.com/blog/azurecompute/ignite-2026-the-next-era-of-enterprise-ai-starts-with-the-infrastructure-beneath/4574943)
- [Azure 裸金属技术预览](https://techcommunity.microsoft.com/blog/azureconfidentialcomputingblog/confidential-ai-at-rack-scale-introducing-azure%E2%80%99s-vera-rubin-nvl72-technology-pr/4570354)
- [Azure 机密计算演示细节](https://techcommunity.microsoft.com/blog/azureconfidentialcomputingblog/what%E2%80%99s-new-in-azure-confidential-computing-%E2%80%94-ignite-2026/4573909)
- [NVIDIA NVL72 产品页](https://www.nvidia.com/en-us/data-center/vera-rubin-nvl72/)
