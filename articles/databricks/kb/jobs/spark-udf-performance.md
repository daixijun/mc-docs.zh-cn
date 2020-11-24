---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: Apache Spark 作业因非确定性自定义 UDF 而挂起 - Azure Databricks
description: 了解 Apache Spark 作业因非确定性自定义 UDF 而挂起时要执行的操作。
ms.openlocfilehash: 3aab70ca2f9b962d2e248a5083b053d27cd8e89f
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589964"
---
# <a name="apache-spark-jobs-hang-due-to-non-deterministic-custom-udf"></a>Apache Spark 作业因非确定性自定义 UDF 而挂起

## <a name="problem"></a>问题

有时，由于 Spark 用户定义函数 (UDF) 的不确定性行为，Apache Spark 作业会无限期挂起。 以下是此类函数的示例：

```scala
val convertorUDF = (commentCol: String) =>
    {
              #UDF definition
    }
val translateColumn = udf(convertorUDF)
```

如果使用 `withColumn()` API 调用此 UDF，然后对生成的 `DataFrame` 应用一些筛选器转换，则此 UDF 可能会对每个记录执行多次，从而影响应用程序性能。

```scala
val translatedDF = df.withColumn("translatedColumn", translateColumn( df("columnToTranslate")))
val filteredDF = translatedDF.filter(!translatedDF("translatedColumn").contains("Invalid URL Provided")) && !translatedDF("translatedColumn").contains("Unable to connect to Microsoft API"))
```

## <a name="cause"></a>原因

有时，确定性 UDF 的行为可能不确定，根据 UDF 的定义执行重复调用。 当你对数据帧使用 UDF 以使用 `withColumn()` API 添加其他列，然后对生成的 `DataFrame` 应用转换（筛选器）时，通常会看到此行为。

## <a name="solution"></a>解决方案

UDF 必须是确定性的。 由于优化，可能会消除重复调用，或者调用函数的次数可能会超过函数在查询中出现的次数。

更好的选择是在使用 UDF 的地方缓存 `DataFrame`。 如果 `DataFrame` 包含大量数据，则最好将其写入 Parquet 格式文件。

可以使用以下代码来缓存结果：

```scala
val translatedDF = df.withColumn("translatedColumn", translateColumn( df("columnToTranslate"))).cache()
```