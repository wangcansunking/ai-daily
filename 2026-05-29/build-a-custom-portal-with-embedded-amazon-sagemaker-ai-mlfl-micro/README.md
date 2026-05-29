# AWS 顾问 Manish Garg 等发布架构，用 CDK 栈自动化部署 MLflow 门户

<!-- head fallback: head.html -->

如果你管理一个 50 人的机器学习团队，每人每天要打开 AWS 控制台点三次才能找到 MLflow 实验面板，仅这一个动作全年就要浪费 3.6 万次点击。这还不算给每个人配 IAM 角色、发预签名 URL、处理“为什么我的链接过期了”的工单。

AWS 的解决方案工程师大概也受够了这种重复劳动。2026 年 5 月 28 日，Manish Garg、Ashish Bhatt 和 Ram Yennapusa 三位 AWS ProServe 顾问在官方 ML 博客上扔出了一套架构方案：用 React 前端 + Flask 反向代理，把 Amazon SageMaker AI 的 MLflow Apps 完整嵌入企业内部门户，用户只需一个书签就能访问全部实验追踪功能，背后所有 SigV4 签名认证全由代理层静默处理。

![Build a custom portal with embedded Amazon SageMaker AI MLflow Apps](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/build-a-custom-portal-with-embedded-amazon-sagemaker-ai-mlfl-micro/content-1.png)

**这不是一篇“如何用 MLflow”的教程，这是一封写给所有 MLOps 工程师的“别再手动管权限了”的劝降书。**

![Architecture diagram showing the React dashboard, Flask reverse proxy on Amazon EC2, and SageMaker AI MLflow Apps integr](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/build-a-custom-portal-with-embedded-amazon-sagemaker-ai-mlfl-micro/content-2.png)



![方案四层架构全景图，ALB → EC2 Flask 代理 → React 前端含 iframe → SageMaker MLflow App，箭头标注 SigV4 签名路径](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/build-a-custom-portal-with-embedded-amazon-sagemaker-ai-mlfl-micro/schematic-1.png)



## 预签名 URL 是 2023 年的临时方案，不是 2026 年的基础设施

原文开篇就直接点出了当前企业 ML 团队的痛点：预签名 URL 分发“不适用于拥有数十名数据科学家的团队”，而逐个授予 AWS 控制台访问权限“增加了管理员管理访问控制的操作开销”。这两个问题在 2023-2024 年 MLflow 刚集成进 SageMaker 时还勉强能忍——彼时团队规模小，实验数量少，发几个预签名链接就能应付。但到了 2026 年，当 ML 团队膨胀到几十甚至上百人，预签名 URL 的 7 天有效期、手动轮换、无法与 SSO 集成等短板就变成了每天消耗运维精力的出血点。

AWS 这次给出的方案本质上是一个**认证代理模式**：Flask 应用运行在 EC2 上，拦截所有发往 `/mlflow-ui/` 和 `/api/2.0/mlflow/` 的请求，用临时 IAM 角色凭证对每个请求做 SigV4 签名，然后转发给 SageMaker 的无服务器 MLflow 端点。MLflow 返回的 HTML 响应会经过两道加工——绝对 URL 被重写为相对路径（防止 iframe 内导航跳出代理），`X-Frame-Options` 头被剥离（允许浏览器在 iframe 中渲染 UI）——然后才送回用户浏览器。

用户全程只看到一个 ALB 域名下的统一界面，React 前端在 `/app` 路径提供品牌化入口，MLflow UI 嵌入在 iframe 中，REST API 调用走同一个代理端点。这意味着 CI/CD 流水线和自动化脚本也能用相同的 `/api/2.0/mlflow/` 路径与 MLflow 交互，无需在脚本里硬编码 AWS 凭证或手动拼接 SigV4 签名头。



## 四层 CDK 栈 + 一个 bash 脚本 = 全自动部署

整个方案被拆成了 4 个 CDK 栈，每一层职责分明：

- **Networking 栈**：VPC、公私子网、路由表、安全组，所有上层资源依赖的网络基础。
- **SageMaker AI Domain 栈**：创建 SageMaker 域作为组织容器，提供身份和访问上下文。
- **SageMaker MLflow 栈**：在域内部署无服务器 MLflow App，存储实验、运行、指标和模型注册表。
- **Flask Application 栈**：在 EC2 上部署 Flask 反向代理，前方挂 ALB 做流量入口和 HTTPS 终结。

部署流程只有一个 `deploy.sh` 脚本，它编排 CDK 部署和无服务器 MLflow App 的创建。部署完成后，管理员用 Session Manager 登录 EC2 实例，跑两个脚本——`install_python13

#Build #Amazon #SageMaker #AI #MLflow

![MLflow UI in the React dashboard showing the new experiment, run, logged parameters, and metrics created via the REST AP](https://raw.githubusercontent.com/wangcansunking/ai-daily/main/2026-05-29/build-a-custom-portal-with-embedded-amazon-sagemaker-ai-mlfl-micro/content-3.png)
