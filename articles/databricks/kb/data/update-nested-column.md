---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 如何更新嵌套列 - Azure Databricks
description: 了解如何在 Azure Databricks 中更新嵌套列。
ms.openlocfilehash: 74250b775b52a8b87a0c15732701abf8cabeede5
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589561"
---
# <a name="how-to-update-nested-columns"></a>如何更新嵌套列

Spark 不支持在嵌套结构中添加新列或删除现有列。 具体而言，`Dataset` 类的 `withColumn` 和 `drop` 方法不允许你指定与任何顶级列均不同的列名。 例如，假设你的数据集具有以下架构：

```scala
val schema = (new StructType)
      .add("metadata",(new StructType)
             .add("eventid", "string", true)
             .add("hostname", "string", true)
             .add("timestamp", "string", true)
           , true)
      .add("items", (new StructType)
             .add("books", (new StructType).add("fees", "double", true), true)
             .add("paper", (new StructType).add("pages", "int", true), true)
           ,true)
schema.treeString
```

架构如下所示：

```
root
 |-- metadata: struct (nullable = true)
 |    |-- eventid: string (nullable = true)
 |    |-- hostname: string (nullable = true)
 |    |-- timestamp: string (nullable = true)
 |-- items: struct (nullable = true)
 |    |-- books: struct (nullable = true)
 |    |    |-- fees: double (nullable = true)
 |    |-- paper: struct (nullable = true)
 |    |    |-- pages: integer (nullable = true)
```

假设你有数据帧：

```scala
val rdd: RDD[Row] = sc.parallelize(Seq(Row(
  Row("eventid1", "hostname1", "timestamp1"),
  Row(Row(100.0), Row(10)))))
val df = spark.createDataFrame(rdd, schema)
display(df)
```

你想要将 `books` 下嵌套的 `fees` 列增加 1%。 若要更新 `fees` 列，可以从现有列和已更新列重新构造数据集，如下所示：

```scala
val updated = df.selectExpr("""
    named_struct(
        'metadata', metadata,
        'items', named_struct(
          'books', named_struct('fees', items.books.fees * 1.01),
          'paper', items.paper
        )
    ) as named_struct
""").select($"named_struct.metadata", $"named_struct.items")
updated.show(false)
```

然后会得到以下结果：

```
+-----------------------------------+-----------------+
| metadata                          | items           |
+===================================+=================+
| [eventid1, hostname1, timestamp1] | [[101.0], [10]] |
+-----------------------------------+-----------------+
```