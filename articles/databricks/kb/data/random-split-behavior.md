---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: randomSplit 方法的行为 - Azure Databricks
description: 了解在 Azure Databricks 中使用 randomSplit 方法时的不一致行为。
ms.openlocfilehash: 030b5d5043dce7efd8a7d1cc7e660e30bb5255fb
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589754"
---
# <a name="behavior-of-the-randomsplit-method"></a>`randomSplit` 方法的行为

对数据帧使用 `randomSplit` 时，可能会观察到不一致的行为。 以下是示例：

```python
df = spark.read.format('inconsistent_data_source').load()
a,b = df.randomSplit([0.5, 0.5])
a.join(broadcast(b), on='id', how='inner').count()
```

通常，此查询会返回 `0`。 但是，根据基础数据源或输入数据帧，在某些情况下，查询可能会生成超过 0 条记录。

这种意外行为的原因是跨 RDD 分区的数据分布不是幂等的，可以在查询执行期间重新排列或更新，从而影响 `randomSplit` 方法的输出。

> [!NOTE]
>
> * Spark 数据帧和 RDD 会保留分区顺序；仅当查询输出依赖于跨分区的实际数据分布时，才会出现此问题，例如，来自文件 1、2 和 3 的值始终出现在分区 1 中。
> * 使用 [Delta 缓存](/databricks/delta/optimizations/delta-cache)时也可能会出现此问题。 在这种情况下，下面列出的所有解决方案仍然适用。

## <a name="solution"></a>解决方案

执行下列操作之一：

* 使用显式 Apache Spark RDD 缓存

  ```python
  df = inputDF.cache()
  a,b = df.randomSplit([0.5, 0.5])
  ```

* 按一列或一组列进行重新分区

  ```python
  df = inputDF.repartition(100, 'col1')
  a,b = df.randomSplit([0.5, 0.5])
  ```

* 应用聚合函数

  ```python
  df = inputDF.groupBy('col1').count()
  a,b = df.randomSplit([0.5, 0.5])
  ```

这些操作会持久保存或重新整理数据，从而在 Spark 作业中实现各分区的数据分布一致。