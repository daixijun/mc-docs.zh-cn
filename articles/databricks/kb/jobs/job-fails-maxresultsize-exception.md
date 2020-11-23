---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: Apache Spark 作业由于出现 maxResultSize 异常而失败 - Azure Databricks
description: 了解当 Apache Spark 作业由于出现 maxResultSize 异常而失败时该怎么办
ms.openlocfilehash: 66cbab65b3bbac9acb154a0252d15687db98bcd9
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589795"
---
# <a name="apache-spark-job-fails-with-maxresultsize-exception"></a>Apache Spark 作业失败，出现 `maxResultSize` 异常

## <a name="problem"></a>问题

Apache Spark 作业由于出现 `maxResultSize` 异常而失败：

```console
org.apache.spark.SparkException: Job aborted due to stage failure: Total size of serialized
results of XXXX tasks (X.0 GB) is bigger than spark.driver.maxResultSize (X.0 GB)
```

## <a name="cause"></a>原因

发生此错误是因为超出了配置的大小限制。 大小限制适用于跨所有分区的 Spark 操作的总序列化结果。 Spark 操作包括多项操作，例如对驱动程序节点执行 `collect()` 操作、`toPandas()` 或将大型文件保存到驱动程序本地文件系统。

## <a name="solution"></a>解决方案

在某些情况下，可能必须重构代码，防止驱动程序节点收集大量数据。 可以更改代码，使得驱动程序节点收集有限数量的数据或增加驱动程序实例的内存大小。 例如，可以调用启用了 Arrow 的 `toPandas` 或写入文件，然后读取这些文件，而不是将大量数据收集回驱动程序。

如果十分有必要，则可以将属性 `spark.driver.maxResultSize` 设置为值 `<X>g`，该值高于群集 [Spark 配置](/databricks/clusters/configure#spark-config)中的异常消息报告的值：

```ini
spark.driver.maxResultSize <X>g
```

默认值为 4g。 有关详细信息，请参阅[应用程序属性](http://spark.apache.org/docs/latest/configuration.html#application-properties)

如果设置上限，则驱动程序中可能会发生内存不足错误（取决于 `spark.driver.memory` 和 JVM 中对象的内存使用情况）。 设置适当的限制，防止出现内存不足错误。