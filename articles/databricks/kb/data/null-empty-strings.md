---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 分区列中的 NULL 和空字符串在保存后变为 NULL - Azure Databricks
description: 了解为什么分区列中的 NULL 和空字符串在 Azure Databricks 中保存后变为 NULL。
ms.openlocfilehash: 1043800f47f010f42b81e575699648dd13339b39
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589603"
---
# <a name="nulls-and-empty-strings-in-a-partitioned-column-save-as-nulls"></a>分区列中的 Null 值和空字符串在保存后均变为 Null 值

## <a name="problem"></a>问题

如果将同时包含空字符串和 NULL 值的数据保存在对表分区所依据的列中，则在写入和读取该表后，两个值都将变为 NULL。

为了说明这一点，请创建一个简单的 `DataFrame`：

```scala
import org.apache.spark.sql.types._
import org.apache.spark.sql.catalyst.encoders.RowEncoder
val data = Seq(Row(1, ""), Row(2, ""), Row(3, ""), Row(4, "hello"), Row(5, null))
val schema = new StructType().add("a", IntegerType).add("b", StringType)
val df = spark.createDataFrame(spark.sparkContext.parallelize(data), schema)
```

此时如果显示 `df` 的内容，你会发现它看起来不变：

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/data/null-empty-strings-1.png)

写入 `df`，再次读取并显示它。 空字符串将替换为 NULL 值：

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/data/null-empty-strings-2.png)

## <a name="cause"></a>原因

这是预期的行为。 它继承自 Apache Hive。

## <a name="solution"></a>解决方案

通常，不应同时将 NULL 和空字符串用作分区列中的值。