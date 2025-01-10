# 降低 CloudWatch 成本

## GetMetricData

通常 `GetMetricData` 调用来自第三方可观测性工具和/或在其平台中使用 CloudWatch 指标的云财务工具。

- 考虑降低第三方工具发出请求的频率。例如，将频率从 1 分钟降低到 5 分钟应该可以将成本降低到原来的 1/5（20%）。
- 要识别趋势，考虑暂时关闭来自第三方工具的所有数据收集。

## CloudWatch Logs

- 使用此[知识中心文档][log-article]查找主要贡献者。
- 除非必要，否则降低主要贡献者的日志记录级别。
- 查看是否除了 CloudWatch 之外还在使用第三方日志记录工具。
- 如果您在每个 VPC 上都启用了 VPC Flow Log，并且有大量流量，成本会快速增加。如果您仍然需要它，请考虑将其传送到 Amazon S3。
- 检查是否需要对所有 AWS Lambda 函数进行日志记录。如果不需要，在 Lambda 角色中拒绝 "logs:PutLogEvents" 权限。
- CloudTrail 日志通常是主要贡献者。将它们发送到 Amazon S3 并使用 Amazon Athena 进行查询，使用 Amazon EventBridge 进行告警/通知会更便宜。

更多详细信息请参考此[知识中心文章][article]。


[article]: https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-understand-and-reduce-charges/
[log-article]: https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-logs-bill-increase/