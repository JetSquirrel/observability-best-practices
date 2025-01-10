# AWS 可观测性服务和成本

在投资可观测性技术栈时，定期监控可观测性产品的**成本**非常重要。这可以确保您只产生必要的成本，并且不会在不需要的资源上过度支出。

## AWS 成本优化工具

大多数组织的核心重点都在于扩展其云上的 IT 基础设施，通常是不受控制、未做准备且不了解其实际或即将发生的云支出。为了帮助您跟踪、报告和分析成本随时间的变化，AWS 提供了几个成本优化工具：

[AWS Cost Explorer][cost-explorer] – 查看 AWS 支出随时间的变化模式，预测未来成本，识别需要进一步调查的领域，观察预留实例使用情况，观察预留实例覆盖范围，并接收预留实例推荐。

[AWS 成本和使用报告(CUR)][CUR] – 详细记录跨账户每小时 AWS 使用情况的原始数据文件，用于自主分析。AWS 成本和使用报告具有动态列，这些列会根据您使用的服务进行填充。

## 架构概述：可视化 AWS 成本和使用报告

您可以在 Amazon Managed Grafana 或 Amazon QuickSight 中构建 AWS 成本和使用情况仪表板。以下架构图说明了这两种解决方案。

![架构图](../../../images/cur-architecture.png)
*架构图*

## Cloud Intelligence 仪表板

[Cloud Intelligence 仪表板][cid]是建立在 AWS 成本和使用报告(CUR)之上的一系列 [Amazon QuickSight][quicksight] 仪表板。这些仪表板可作为您自己的成本管理和优化(FinOps)工具。您可以获得深入、细致且基于推荐的仪表板，帮助您详细了解 AWS 的使用情况和成本。

### 实施

1. 创建启用了 [Amazon Athena][amazon-athnea] 集成的 [CUR 报告][cur-report]。  
*在初始配置期间，AWS 最多需要 24 小时才能开始向您的 Amazon S3 存储桶传送报告。报告每天传送一次。为了简化和自动化成本和使用报告与 Athena 的集成，AWS 提供了一个 AWS CloudFormation 模板，其中包含几个关键资源以及为 Athena 集成设置的报告。*

2. 部署 [AWS CloudFormation 模板][cloudformation]。  
*此模板包括 AWS Glue 爬虫、AWS Glue 数据库和 AWS Lambda 事件。此时，CUR 数据通过 Amazon Athena 中的表供您查询。*

    - 直接在 CUR 数据上运行 [Amazon Athena][athena-query] 查询。  
*要对数据运行 Athena 查询，首先使用 Athena 控制台检查 AWS 是否正在刷新您的数据，然后在 Athena 控制台上运行查询。*

3. 部署 Cloud Intelligence 仪表板。
    - 对于手动部署，请参考 AWS Well-Architected **[成本优化实验室][cost-optimization-lab]**。
    - 对于自动部署，请参考 [GitHub 仓库][GitHub-repo]。

Cloud Intelligence 仪表板非常适合财务团队、高管和 IT 经理。然而，我们经常收到客户的一个问题是如何深入了解各个 AWS 可观测性产品（如 Amazon CloudWatch、AWS X-Ray、Amazon Managed Service for Prometheus 和 Amazon Managed Grafana）在整个组织范围内的成本。

在下一节中，您将深入探讨这些产品各自的成本和使用情况。任何规模的公司都可以采用这种主动的云成本优化策略，通过云成本分析和数据驱动的决策提高业务效率，而不会影响性能或增加运营开销。



[cost-explorer]: https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/ce-what-is.html
[CUR]: https://docs.aws.amazon.com/cur/latest/userguide/what-is-cur.html
[cid]: https://wellarchitectedlabs.com/cost/200_labs/200_cloud_intelligence/
[quicksight]: https://aws.amazon.com/quicksight/
[cur-report]: https://docs.aws.amazon.com/cur/latest/userguide/cur-create.html
[amazon-athnea]: https://aws.amazon.com/athena/
[cloudformation]: https://docs.aws.amazon.com/cur/latest/userguide/use-athena-cf.html
[athena-query]: https://docs.aws.amazon.com/cur/latest/userguide/cur-ate-run.html
[cost-optimization-lab]: https://www.wellarchitectedlabs.com/cost/200_labs/200_cloud_intelligence/
[GitHub-repo]: https://github.com/aws-samples/aws-cudos-framework-deployment






