---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 08/06/2020
title: Apache Spark 作业失败，出现“无法分析字节字符串”错误 - Azure Databricks
description: Apache Spark 作业失败，出现“无法分析字节字符串”错误。
category: Jobs
author: nnigam19
db-author: noopur.nigam@databricks.com
ms.openlocfilehash: b25d47ed81b14b921f710637511f83dc781e615e
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589965"
---
# <a name="apache-spark-job-fails-with-failed-to-parse-byte-string"></a>Apache Spark 作业失败，显示 `Failed to parse byte string`

## <a name="problem"></a>问题

Spark-submit 作业失败，出现“`Failed to parse byte string: -1`”错误消息。

```console
java.util.concurrent.ExecutionException: java.lang.NumberFormatException: Size must be specified as bytes (b), kibibytes (k), mebibytes (m), gibibytes (g), tebibytes (t), or pebibytes(p). E.g. 50b, 100k, or 250m.
Failed to parse byte string: -1
at java.util.concurrent.FutureTask.report(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
at org.apache.spark.sql.execution.exchange.BroadcastExchangeExec.doExecuteBroadcast(BroadcastExchangeExec.scala:182)
... 108 more
Caused by: java.lang.NumberFormatException: Size must be specified as bytes (b), kibibytes (k), mebibytes (m), gibibytes (g), tebibytes (t), or pebibytes(p). E.g. 50b, 100k, or 250m.
Failed to parse byte string: -1
```

## <a name="cause"></a>原因

`spark.driver.maxResultSize` 应用程序属性的值为负。

## <a name="solution"></a>解决方案

分配给 `spark.driver.maxResultSize` 的值定义了每个 Spark 操作的序列化结果的最大大小（以字节为单位）。 可以为 `spark.driver.maxResultSize` 属性指定一个正值，以定义特定大小。 也可以指定值 0 来定义无限制的最大大小。 不能为此属性指定负值。

如果作业的总大小高于 `spark.driver.maxResultSize` 值，则作业将中止。

为 `spark.driver.maxResultSize` 设置过高的（或无限制的）值时，应小心。 限制过高可能会导致驱动程序出现内存不足错误（在 `spark.driver.memory` 属性设置得不够高的情况下）。

有关更多详细信息，请参阅 [Spark 配置应用程序属性](http://spark.apache.org/docs/latest/configuration.html#application-properties)。