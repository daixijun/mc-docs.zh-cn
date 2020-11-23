---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何使用 Apache Spark 指标 - Azure Databricks
description: 了解如何将 Apache Spark 指标与 Azure Databricks 配合使用。
ms.openlocfilehash: cf94c6c068aec4c1172e18a6c9bf10583b259ae6
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589849"
---
# <a name="how-to-use-apache-spark-metrics"></a>如何使用 Apache Spark 指标

本文提供了一个示例，说明如何使用 [Spark 可配置指标系统](https://spark.apache.org/docs/latest/monitoring.html#metrics)监视 Apache Spark 组件。 具体而言，它演示了如何设置新源和启用接收器。

有关可用于指标收集的 Spark 组件的详细信息（包括直接受支持的接收器），请访问上面的文档链接。

> [!NOTE]
>
> 还有其他几种收集指标的方法，可以深入了解 Spark 作业的执行方式，本文也没有介绍这些方法：
>
> * SparkStatusTracker（[源](https://github.com/apache/spark/blob/master/core/src/main/scala/org/apache/spark/api/java/JavaSparkStatusTracker.scala)、[API](https://spark.apache.org/docs/latest/api/java/org/apache/spark/api/java/JavaSparkStatusTracker.html)）：监视作业、阶段或任务进度
> * StreamingQueryListener（[源](https://github.com/apache/spark/blob/master/sql/core/src/main/scala/org/apache/spark/sql/streaming/StreamingQueryListener.scala)[API](https://spark.apache.org/docs/latest/api/java/org/apache/spark/sql/streaming/StreamingQueryListener.html)）：截获流式处理事件
> * [SparkListener](https://github.com/apache/spark/blob/master/core/src/main/scala/org/apache/spark/scheduler/SparkListener.scala)：截获 Spark 计划程序中的事件
>
> 有关使用其他第三方工具监视 Azure Databricks 中的 Spark 作业的信息，请参阅[指标](/databricks/clusters/clusters-manage#monitor-performance)。

此指标收集系统的工作原理是什么？ 实例化后，每个执行程序会创建一个到驱动程序的连接以传递指标。

第一步是编写一个扩展 `Source` 特征的类：

```scala
class MySource extends Source {
  override val sourceName: String = "MySource"

  override val metricRegistry: MetricRegistry = new MetricRegistry

  val FOO: Histogram = metricRegistry.histogram(MetricRegistry.name("fooHistory"))
  val FOO_COUNTER: Counter = metricRegistry.counter(MetricRegistry.name("fooCounter"))
}
```

下一步是启用接收器。 在此示例中，指标输出到控制台：

```scala
val spark: SparkSession = SparkSession
    .builder
    .master("local[*]")
    .appName("MySourceDemo")
    .config("spark.driver.host", "localhost")
    .config("spark.metrics.conf.*.sink.console.class", "org.apache.spark.metrics.sink.ConsoleSink")
.getOrCreate()
```

> [!NOTE]
>
> 要将指标汇聚到 Prometheus，可以使用此第三方库：[https://github.com/banzaicloud/spark-metrics](https://github.com/banzaicloud/spark-metrics)。

最后一步是实例化源并将其注册到 SparkEnv：

```scala
val source: MySource = new MySource
SparkEnv.get.metricsSystem.registerSource(source)
```

可以在 [https://github.com/newroyker/meter](https://github.com/newroyker/meter) 上查看完整的、可生成的示例。