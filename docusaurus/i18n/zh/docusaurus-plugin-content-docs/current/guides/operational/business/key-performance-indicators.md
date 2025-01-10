# 关键绩效指标 (KPIs) 指南

## 1.0 理解 KPIs（“黄金信号”）

组织利用关键绩效指标（KPIs），也称为“黄金信号”，来洞察业务和运营的健康状况或风险。组织的不同部分会有独特的 KPIs，用于衡量其各自的成果。例如，电子商务应用程序的产品团队会跟踪成功处理购物车订单的能力作为其 KPI。值班运营团队会将其 KPI 衡量为平均检测时间（MTTD）。对于财务团队，预算内资源成本的 KPI 非常重要。

服务级别指标（SLIs）、服务级别目标（SLOs）和服务级别协议（SLAs）是服务可靠性管理的重要组成部分。本指南概述了使用 Amazon CloudWatch 及其功能来计算和监控 SLIs、SLOs 和 SLAs 的最佳实践，并提供清晰简洁的示例。

- **SLI（服务级别指标）**：服务性能的定量衡量标准。
- **SLO（服务级别目标）**：SLI 的目标值，表示期望的性能水平。
- **SLA（服务级别协议）**：服务提供商与其用户之间的合同，规定了预期的服务水平。

常见的 SLI 示例：

- **可用性**：服务正常运行的时间百分比
- **延迟**：完成请求所需的时间
- **错误率**：失败请求的百分比

## 2.0 发现客户和利益相关者的需求（使用以下建议的模板）

1. 从顶层问题开始：“给定工作负载的业务价值或业务问题是什么？”（例如支付门户、电子商务订单处理、用户注册、数据报告、支持门户等）。
2. 将业务价值分解为以下类别：用户体验（UX）、业务体验（BX）、运营体验（OpsX）、安全体验（SecX）、开发者体验（DevX）。
3. 为每个类别导出核心信号，即“黄金信号”；围绕 UX 和 BX 的顶级信号通常构成业务指标。

| ID  | 缩写  | 客户  | 业务需求  | 衡量标准  | 信息来源  | 什么是好的表现？  | 告警  | 仪表板  | 报告  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| M1  | 示例  | 外部终端用户  | 用户体验  | 响应时间（页面延迟）  | 日志/跟踪  | < 5 秒（99.9%）  | 否  | 是  | 否  |
| M2  | 示例  | 业务  | 可用性  | 成功的 RPS（每秒请求数）  | 健康检查  | >85%（5 分钟窗口）  | 是  | 是  | 是  |
| M3  | 示例  | 安全  | 合规性  | 关键不合规资源  | 配置数据  | <10（15 天内）  | 否  | 是  | 是  |
| M4  | 示例  | 开发者  | 敏捷性  | 部署时间  | 部署日志  | 始终 < 10 分钟  | 是  | 否  | 是  |
| M5  | 示例  | 操作员  | 容量  | 队列深度  | 应用程序日志/指标  | 始终 < 10  | 是  | 是  | 是  |

### 2.1 黄金信号

| 类别  | 信号  | 备注  | 参考  |
| --- | --- | --- | --- |
| UX  | 性能（延迟）  | 参见模板中的 M1  | 白皮书：[可用性及超越（测量延迟）](https://docs.aws.amazon.com/whitepapers/latest/availability-and-beyond-improving-resilience/measuring-availability.html#latency)  |
| BX  | 可用性  | 参见模板中的 M2  | 白皮书：[可用性及超越（测量可用性）](https://docs.aws.amazon.com/whitepapers/latest/availability-and-beyond-improving-resilience/measuring-availability.html)  |
| BX  | 业务连续性计划（BCP）  | Amazon Resilience Hub (ARH) 弹性分数与定义的 RTO/RPO  | 文档：[ARH 用户指南（理解弹性分数）](https://docs.aws.amazon.com/resilience-hub/latest/userguide/resil-score.html)  |
| SecX  | （非）合规性  | 参见模板中的 M3  | 文档：[AWS Control Tower 用户指南（控制台中的合规状态）](https://docs.aws.amazon.com/controltower/latest/userguide/compliance-statuses.html)  |
| DevX  | 敏捷性  | 参见模板中的 M4  | 文档：[AWS 上的 DevOps 监控仪表板（DevOps 指标列表）](https://docs.aws.amazon.com/solutions/latest/devops-monitoring-dashboard-on-aws/devops-metrics-list.html)  |
| OpsX  | 容量（配额）  | 参见模板中的 M5  | 文档：[Amazon CloudWatch 用户指南（可视化服务配额并设置告警）](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Quotas-Visualize-Alarms.html)  |
| OpsX  | 预算异常  |  | 文档：<br/> 1. [AWS 计费和成本管理（AWS 成本异常检测）](https://docs.aws.amazon.com/cost-management/latest/userguide/getting-started-ad.html) <br/> 2. [AWS 预算](https://aws.amazon.com/aws-cost-management/aws-budgets/)  |

## 3.0 顶层指导（TLG）

### 3.1 通用 TLG

1. 与业务、架构和安全团队合作，帮助完善业务、合规性和治理需求，并确保它们准确反映业务需求。这包括[建立恢复时间和恢复点目标](https://aws.amazon.com/blogs/mt/establishing-rpo-and-rto-targets-for-cloud-applications/)（RTOs、RPOs）。制定衡量需求的方法，例如[测量可用性](https://docs.aws.amazon.com/whitepapers/latest/availability-and-beyond-improving-resilience/measuring-availability.html)和延迟（例如，正常运行时间可以允许在 5 分钟窗口内出现少量故障）。
2. 构建有效的[标记策略](https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/defining-and-publishing-a-tagging-schema.html)，使用与各种业务功能成果相一致的模式。这应特别涵盖[操作可观测性](https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/operational-observability.html)和[事件管理](https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/incident-management.html)。
3. 尽可能利用动态阈值进行告警（特别是对于没有基线 KPI 的指标），使用 [CloudWatch 异常检测](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Anomaly_Detection.html)，它提供了机器学习算法来建立基线。当使用发布 CloudWatch 指标的 AWS 服务（或其他来源，如 Prometheus 指标）配置告警时，考虑创建[复合告警](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Create_Composite_Alarm.html)以减少告警噪音。例如，一个包含业务指标（由成功请求跟踪的可用性）和延迟的复合告警，当在部署期间两者都低于关键阈值时触发告警，可能是部署错误的确定性指标。
4. （注意：需要 AWS 商业支持或更高级别）AWS 使用 AWS Health 服务发布与您的资源相关的事件。利用 [AWS Health Aware (AHA)](https://aws.amazon.com/blogs/mt/aws-health-aware-customize-aws-health-alerts-for-organizational-and-personal-aws-accounts/) 框架（使用 AWS Health）从中央账户（如管理账户）中摄取跨 AWS 组织的主动和实时告警。这些告警可以发送到首选的通信平台，如 Slack，并与 ITSM 工具（如 ServiceNow 和 Jira）集成。
5. 利用 Amazon CloudWatch [Application Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-application-insights.html) 为资源设置最佳监控器，并持续分析数据以发现应用程序问题的迹象。它还提供自动化的仪表板，显示受监控应用程序的潜在问题，以便快速隔离/排除应用程序/基础设施问题。利用 [Container Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContainerInsights.html) 聚合来自容器的指标和日志，并可以与 CloudWatch Application Insights 无缝集成。
6. 利用 [AWS Resilience Hub](https://aws.amazon.com/resilience-hub/) 分析应用程序是否符合定义的 RTO 和 RPO。通过使用 [AWS Fault Injection Simulator](https://aws.amazon.com/fis/) 等工具进行受控实验，验证可用性、延迟和业务连续性需求是否得到满足。进行额外的 Well-Architected 审查和服务特定的深入分析，以确保工作负载设计符合业务需求并遵循 AWS 最佳实践。
7. 有关更多详细信息，请参阅 [AWS 可观测性最佳实践](https://aws-observability.github.io/observability-best-practices/) 指南的其他部分、AWS 云采用框架：[操作视角](https://docs.aws.amazon.com/whitepapers/latest/aws-caf-operations-perspective/observability.html) 白皮书以及 AWS Well-Architected 框架操作卓越支柱白皮书中的“[理解工作负载健康](https://docs.aws.amazon.com/wellarchitected/latest/operational-excellence-pillar/understanding-workload-health.html)”内容。

### 3.2 按领域的 TLG（强调业务指标，即 UX、BX）

以下是使用 CloudWatch (CW) 等服务的合适示例（参考：发布 [CloudWatch 指标文档](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aws-services-cloudwatch-metrics.html) 的 AWS 服务）

#### 3.2.1 Canaries（即合成事务）和真实用户监控（RUM）

* TLG：了解客户/用户体验的最简单和最有效的方法之一是使用 Canaries（合成事务）模拟客户流量，定期探测您的服务并记录指标。

| AWS 服务  | 功能  | 衡量标准  | 指标  | 示例  | 备注  |
| --- | --- | --- | --- | --- | --- |
| CW  | 合成事务  | 可用性  | **SuccessPercent**  | (例如 SuccessPercent > 90 或 CW 异常检测 1 分钟周期)<br/>**[如果 Canaries 在每个工作日 7a-8a 运行（CloudWatchSynthetics）：** <br/>`IF(((DAY(m1)<6) AND (HOUR(m1)>7 AND HOUR(m1)<8)),m1)]`  |  |
| CW  | 合成事务  | 可用性  | VisualMonitoringSuccessPercent  | (例如 VisualMonitoringSuccessPercent > 90 用于 5 分钟周期的 UI 截图比较)<br/>**[如果 Canaries 在每个工作日 7a-8a 运行（CloudWatchSynthetics）：** <br/>`IF(((DAY(m1)<6) AND (HOUR(m1)>7 AND HOUR(m1)<8)),m1)`  | 如果客户期望 Canary 匹配预定的 UI 截图  |
| CW  | RUM  | 响应时间  | Apdex 分数  | (例如 Apdex 分数：<br/> NavigationFrustratedCount < ‘N’ 预期值)  |  |

#### 3.2.2 API 前端

| AWS 服务  | 功能  | 衡量标准  | 指标  | 示例  | 备注  |
| --- | --- | --- | --- | --- | --- |
| CloudFront  |  | 可用性  | 总错误率  | (例如 [总错误率] < 10 或 CW 异常检测 1 分钟周期)  | 可用性作为错误率的衡量标准  |
| CloudFront  | （需要开启额外指标）  | 性能  | 缓存命中率  | (例如 缓存命中率 < 10 CW 异常检测 1 分钟周期)  |  |
| Route53  | 健康检查  | （跨区域）可用性  | HealthCheckPercentageHealthy  | (例如 [HealthCheckPercentageHealthy 的最小值] > 90 或 CW 异常检测 1 分钟周期)  |  |
| Route53  | 健康检查  | 延迟  | TimeToFirstByte  | (例如 [p99 TimeToFirstByte] < 100 毫秒或 CW 异常检测 1 分钟周期)  |  |
| API Gateway  |  | 可用性  | Count  | (例如 [(4XXError + 5XXError) / Count) * 100] < 10 或 CW 异常检测 1 分钟周期)  | 可用性作为“放弃”请求的衡量标准  |
| API Gateway  |  | 延迟  | 延迟（或 IntegrationLatency，即后端延迟）  | (例如 p99 延迟 < 1 秒或 CW 异常检测 1 分钟周期)  | p99 比 p90 等较低百分位具有更大的容忍度（p50 与平均值相同）  |
| API Gateway  |  | 性能  | CacheHitCount（和未命中）  | (例如 [CacheMissCount / (CacheHitCount + CacheMissCount)  * 100] < 10 或 CW 异常检测 1 分钟周期)  | 性能作为缓存（未命中）的衡量标准  |
| Application Load Balancer (ALB)  |  | 可用性  | RejectedConnectionCount  | (例如 [RejectedConnectionCount/(RejectedConnectionCount + RequestCount) * 100] < 10 CW 异常检测 1 分钟周期)  | 可用性作为由于最大连接数达到限制而被拒绝的请求的衡量标准  |
| Application Load Balancer (ALB)  |  | 延迟  | TargetResponseTime  | (例如 p99 TargetResponseTime < 1 秒或 CW 异常检测 1 分钟周期)  | p99 比 p90 等较低百分位具有更大的容忍度（p50 与平均值相同）  |

#### 3.2.3 无服务器

| AWS 服务  | 功能  | 衡量标准  | 指标  | 示例  | 备注  |
| --- | --- | --- | --- | --- | --- |
| S3  | 请求指标  | 可用性  | AllRequests  | (例如 [(4XXErrors + 5XXErrors) / AllRequests) * 100] < 10 或 CW 异常检测 1 分钟周期)  | 可用性作为“放弃”请求的衡量标准  |
| S3  | 请求指标  | （总体）延迟  | TotalRequestLatency  | (例如 [p99 TotalRequestLatency] < 100 毫秒或 CW 异常检测 1 分钟周期)  |  |
| DynamoDB (DDB)  |  | 可用性  | ThrottledRequests  | (例如 [ThrottledRequests] < 100 或 CW 异常检测 1 分钟周期)  | 可用性作为“限制”请求的衡量标准  |
| DynamoDB (DDB)  |  | 延迟  | SuccessfulRequestLatency  | (例如 [p99 SuccessfulRequestLatency] < 100 毫秒或 CW 异常检测 1 分钟周期)  |  |
| Step Functions  |  | 可用性  | ExecutionsFailed  | (例如 ExecutionsFailed = 0)<br/>**[例如 如果 Step Function 执行在 UTC 时间每个工作日 9p-7a 运行：** <br/>`IF(((DAY(m1)<6 OR ** ** DAY(m1)==7) AND (HOUR(m1)>21 AND HOUR(m1)<7)),m1)]`  | 假设业务流要求在工作日 9p-7a（每日业务操作开始）完成 Step Functions  |

#### 3.2.4 计算和容器

| AWS 服务  | 功能  | 衡量标准  | 指标  | 示例  | 备注  |
| --- | --- | --- | --- | --- | --- |
| EKS  | Prometheus 指标  | 可用性  | APIServer 请求成功率  | (例如 Prometheus 指标如 [APIServer 请求成功率](https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/service/cwagent-prometheus/sample_cloudwatch_dashboards/kubernetes_api_server/cw_dashboard_kubernetes_api_server.json))  | 参见 [监控 EKS 控制平面指标的最佳实践](https://aws.github.io/aws-eks-best-practices/reliability/docs/controlplane/#monitor-control-plane-metrics) 和 [EKS 可观测性](https://docs.aws.amazon.com/eks/latest/userguide/eks-observe.html) 以获取详细信息。  |
| EKS  | Prometheus 指标  | 性能  | apiserver_request_duration_seconds, etcd_request_duration_seconds  | apiserver_request_duration_seconds, etcd_request_duration_seconds  |  |
| ECS  |  | 可用性  | 服务 RUNNING 任务计数  | 服务 RUNNING 任务计数  | 参见 ECS CloudWatch 指标 [文档](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cloudwatch-metrics.html#cw_running_task_count)  |
| ECS  |  | 性能  | TargetResponseTime  | (例如 [p99 TargetResponseTime] < 100 毫秒或 CW 异常检测 1 分钟周期)  | 参见 ECS CloudWatch 指标 [文档](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cloudwatch-metrics.html#cw_running_task_count)  |
| EC2 (.NET Core)  | CloudWatch Agent 性能计数器  | 可用性  | (例如 [ASP.NET 应用程序错误总数/秒](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/appinsights-metrics-ec2.html#appinsights-metrics-ec2-built-in) < 'N')  | (例如 [ASP.NET 应用程序错误总数/秒](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/appinsights-metrics-ec2.html#appinsights-metrics-ec2-built-in) < 'N')  | 参见 EC2 CloudWatch Application Insights [文档](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/appinsights-metrics-ec2.html#appinsights-metrics-ec2-built-in)  |

#### 3.2.5 数据库（RDS）

| AWS 服务  | 功能  | 衡量标准  | 指标  | 示例  | 备注  |
| --- | --- | --- | --- | --- | --- |
| RDS Aurora  | 性能洞察（PI）  | 可用性  | 平均活动会话数  | (例如 平均活动会话数与 CW 异常检测 1 分钟周期)  | 参见 RDS Aurora CloudWatch PI [文档](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_PerfInsights.Overview.ActiveSessions.html#USER_PerfInsights.Overview.ActiveSessions.AAS)  |
| RDS Aurora  |  | 灾难恢复（DR）  | AuroraGlobalDBRPOLag  | (例如 AuroraGlobalDBRPOLag < 30000 毫秒 1 分钟周期)  | 参见 RDS Aurora CloudWatch [文档](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.AuroraMonitoring.Metrics.html)  |
| RDS Aurora  |  | 性能  | 提交延迟、缓冲区缓存命中率、DDL 延迟、DML 延迟  | (例如 提交延迟与 CW 异常检测 1 分钟周期)  | 参见 RDS Aurora CloudWatch PI [文档](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_PerfInsights.Overview.ActiveSessions.html#USER_PerfInsights.Overview.ActiveSessions.AAS)  |
| RDS (MSSQL)  | PI  | 性能  | SQL 编译  | (例如 <br/>SQL 编译 > 'M' 5 分钟周期)  | 参见 RDS CloudWatch PI [文档](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights_Counters.html#USER_PerfInsights_Counters.SQLServer)  |

## 4.0 使用 Amazon CloudWatch 和 Metric Math 计算 SLIs、SLOs 和 SLAs

### 4.1 Amazon CloudWatch 和 Metric Math

Amazon CloudWatch 提供对 AWS 资源的监控和可观测性服务。Metric Math 允许您使用 CloudWatch 指标数据执行计算，使其成为计算 SLIs、SLOs 和 SLAs 的理想工具。

#### 4.1.1 启用详细监控

为您的 AWS 资源启用详细监控，以获得 1 分钟的数据粒度，从而实现更准确的 SLI 计算。

#### 4.1.2 使用命名空间和维度组织指标

使用命名空间和维度对指标进行分类和过滤，以便更轻松地进行分析。例如，使用命名空间将与特定服务相关的指标分组，并使用维度区分该服务的各个实例。

### 4.2 使用 Metric Math 计算 SLIs

#### 4.2.1 可用性

要计算可用性，将成功请求的数量除以总请求数：

```
可用性 = 100 * (成功请求数 / 总请求数)
```

**示例：**

假设您有一个 API Gateway，具有以下指标：
- `4XXError`：4xx 客户端错误的数量
- `5XXError`：5xx 服务器错误的数量
- `Count`：总请求数

使用 Metric Math 计算可用性：

```
可用性 = 100 * ((Count - 4XXError - 5XXError) / Count)
```

#### 4.2.2 延迟

要计算平均延迟，使用 CloudWatch 提供的 `SampleCount` 和 `Sum` 统计信息：

```
平均延迟 = Sum / SampleCount
```

**示例：**

假设您有一个 Lambda 函数，具有以下指标：
- `Duration`：执行函数所需的时间

使用 Metric Math 计算平均延迟：

```
平均延迟 = Duration.Sum / Duration.SampleCount
```

#### 4.2.3 错误率

要计算错误率，将失败请求的数量除以总请求数：

```
错误率 = 100 * (失败请求数 / 总请求数)
```

**示例：**

使用之前的 API Gateway 示例：

```
错误率 = 100 * ((4XXError + 5XXError) / Count)
```

### 4.4 定义和监控 SLOs

#### 4.4.1 设置现实的目标

根据用户期望和历史性能数据定义 SLO 目标。设置可实现的目标，以确保服务可靠性和资源利用率之间的平衡。

#### 4.4.2 使用 CloudWatch 监控 SLOs

创建 CloudWatch 告警以监控您的 SLIs，并在它们接近或违反 SLO 目标时通知您。这使您能够主动解决问题并保持服务可靠性。

#### 4.4.3 审查和调整 SLOs

定期审查您的 SLOs，以确保它们随着服务的发展保持相关性。必要时调整目标，并向利益相关者传达任何变更。

### 4.5 定义和衡量 SLAs

#### 4.5.1 设置现实的目标

根据历史性能数据和用户期望定义 SLA 目标。设置可实现的目标，以确保服务可靠性和资源利用率之间的平衡。

#### 4.5.2 监控和告警

设置 CloudWatch 告警以监控 SLIs，并在它们接近或违反 SLA 目标时通知您。这使您能够主动解决问题并保持服务可靠性。

#### 4.5.3 定期审查 SLAs

定期审查 SLAs，以确保它们随着服务的发展保持相关性。必要时调整目标，并向利益相关者传达任何变更。

### 4.6 衡量 SLA 或 SLO 在一段时间内的表现

要衡量 SLA 或 SLO 在一段时间内（例如一个日历月）的表现，请使用具有自定义时间范围的 CloudWatch 指标数据。

**示例：**

假设您有一个 API Gateway，其 SLO 目标为 99.9% 的可用性。要衡量 4 月份的可用性，请使用以下 Metric Math 表达式：

```
可用性 = 100 * ((Count - 4XXError - 5XXError) / Count)
```

然后，配置 CloudWatch 指标数据查询，使用自定义时间范围：
- **开始时间**：`2023-04-01T00:00:00Z`
- **结束时间**：`2023-04-30T23:59:59Z`
- **周期**：`2592000`（30 天的秒数）

最后，使用 `AVG` 统计信息计算该月的平均可用性。如果平均可用性等于或大于 SLO 目标，则您已达到目标。

## 5.0 总结

关键绩效指标（KPIs），也称为“黄金信号”，必须与业务和利益相关者的需求保持一致。使用 Amazon CloudWatch 和 Metric Math 计算 SLIs、SLOs 和 SLAs 对于管理服务可靠性至关重要。遵循本指南中的最佳实践，以有效监控和维护您的 AWS 资源的性能。请记住启用详细监控，使用命名空间和维度组织指标，使用 Metric Math 进行 SLI 计算，设置现实的 SLO 和 SLA 目标，并使用 CloudWatch 告警建立监控和告警系统。通过应用这些最佳实践，您可以确保最佳的服务可靠性、更好的资源利用率和更高的客户满意度。