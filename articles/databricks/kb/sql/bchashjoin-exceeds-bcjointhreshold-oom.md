---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 10/02/2020
title: 广播连接超出阈值，返回内存不足错误 - Azure Databricks
description: 解决在使用 BroadcastHashJoin 的表超出 BroadcastJoinThreshold 时出现的 Apache Spark OutOfMemorySparkException 错误。
category: SQL with Spark
author: dbsan
db-author: sandeep.chandran@databricks.com
ms.openlocfilehash: 334f5b33e419322e580cf7a9c598292921279440
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589479"
---
# <a name="broadcast-join-exceeds-threshold-returns-out-of-memory-error"></a>广播连接超出阈值，因内存错误而返回

## <a name="problem"></a>问题

你尝试联接两个大型表，想要呈现第一个表中的所选列和第二个表中的所有列。

尽管总大小超过了 `spark.sql.autoBroadcastJoinThreshold` 设置的限制，但仍使用 `BroadcastHashJoin`，并且 Apache Spark 返回 `OutOfMemorySparkException` 错误。

```console
org.apache.spark.sql.execution.OutOfMemorySparkException: Size of broadcasted table far exceeds estimates and exceeds limit of spark.driver.maxResultSize=1073741824. You can disable broadcasts for this query using set spark.sql.autoBroadcastJoinThreshold=-1
```

## <a name="cause"></a>原因

这是由 Spark 的大小估算器的限制所致。

如果其中一个数据帧的估计大小小于 `autoBroadcastJoinThreshold`，Spark 可能会使用 `BroadcastHashJoin` 来执行联接。 如果可用节点的资源不足，无法容纳广播数据帧，则作业会因内存不足错误而失败。

## <a name="solution"></a>解决方案

可以通过三种不同的方法来缓解此问题。

* 在尝试联接之前，使用 [ANALYZE TABLE](/databricks/spark/latest/spark-sql/language-manual/analyze-table) 收集有关数据帧的详细信息和计算统计信息。
* [缓存要广播的表](/databricks/spark/latest/spark-sql/language-manual/cache-table)。
  1. 根据 join 命令运行 `explain` 以返回物理计划。

     ```sql
     explain(<join command>)
     ```

  2. 查看物理计划。 如果广播联接返回 BuildLeft，则缓存左侧表。 如果广播联接返回 BuildRight，则缓存右侧表。
* 在 Databricks Runtime 7.0 及更高版本中，将联接类型设置为 `SortMergeJoin` 并启用联接提示。