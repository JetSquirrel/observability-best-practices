# 选择追踪代理

## 选择合适的代理

AWS 直接支持两种用于[追踪](../signals/traces/)采集的工具（以及我们丰富的 [可观测性合作伙伴](https://aws.amazon.com/products/management-and-governance/partners/)）：

* [AWS Distro for OpenTelemetry](https://aws-otel.github.io/)，通常称为 ADOT
* X-Ray 的 [SDK](https://docs.aws.amazon.com/xray/latest/devguide/xray-instrumenting-your-app.html) 和 [守护进程](https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon.html)

选择使用哪种工具或组合使用是你在完善可观测性方案时需要做的主要决策。这些工具并不互斥，你可以根据需要混合使用。而且，还存在一种最佳实践可帮助做出选择。但首先，你需要了解 [OpenTelemetry（OTEL）](https://opentelemetry.io/) 的现状。

OTEL 是当前业界标准的可观测性信号规范，包含对三种核心信号类型（[指标](../signals/metrics/)、[追踪](../signals/traces/)和[日志](../signals/logs/)）的定义。然而，OTEL 并非一直存在，而是从 [OpenMetrics](https://openmetrics.io) 和 [OpenTracing](https://opentracing.io) 等早期规范演进而来。近年来，各大可观测性供应商开始公开支持 OpenTelemetry Line Protocol (OTLP)。

AWS X-Ray 和 CloudWatch 早于 OTEL 规范出现，与其他领先的可观测性解决方案一样。然而，AWS X-Ray 服务能够通过 ADOT 直接接收 OTEL 追踪。ADOT 内置的各种集成已可将遥测数据发送到 X-Ray，也可以将其发送到其他 ISV 解决方案。

任何事务追踪方案都需要一个代理和对底层应用程序的集成来采集信号。这会带来[技术债务](../faq/#what-is-technical-debt)，包括需要对相关库进行测试、维护和升级，如果未来你想更换解决方案，可能还要重新调整工具。

X-Ray 提供的 SDK 是 AWS 提供的紧密集成的自动化方案；ADOT 则是更广泛行业解决方案的一部分，其中 X-Ray 只是众多追踪方案之一。你可以使用任何一种方式在 X-Ray 中实现端到端追踪，但了解二者的区别对于选择最适合你的方法非常重要。

:::info
    如果你需要以下功能，我们建议使用 AWS Distro for OpenTelemetry 为应用程序植入监控：

    * 在无需重新对代码进行检测的情况下，将追踪数据发送到多个不同的追踪后端。例如，如果你想从 X-Ray 控制台切换到 [Zipkin](https://zipkin.io)，只需配置 collector，不需要修改应用程序代码。

    * 由 OpenTelemetry 社区维护的针对各语言的大量库检测支持。
:::

:::info
    如果你需要以下功能，我们建议使用 X-Ray SDK 为应用程序植入监控：

    * 单一供应商的紧密集成解决方案。

    * 与 X-Ray 集中的采样规则集成，包含在使用 Node.js、Python、Ruby 或 .NET 时可从 X-Ray 控制台配置采样规则并自动在多个主机上应用它们的能力。
:::