---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 某些文件中的架构不兼容 - Azure Databricks
description: 了解如何通过 Azure Databricks 解析 Parquet 文件中不兼容的架构。
ms.openlocfilehash: ab6891fcdf72bbcabe08162551dff51cc19873c3
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589751"
---
# <a name="incompatible-schema-in-some-files"></a>某些文件中的架构不兼容

## <a name="problem"></a>问题

在读取 Parquet 文件时，Spark 作业会失败并显示如下异常：

```console
Error in SQL statement: SparkException: Job aborted due to stage failure:
Task 20 in stage 11227.0 failed 4 times, most recent failure: Lost task 20.3 in stage 11227.0
(TID 868031, 10.111.245.219, executor 31):
java.lang.UnsupportedOperationException: org.apache.parquet.column.values.dictionary.PlainValuesDictionary$PlainDoubleDictionary
    at org.apache.parquet.column.Dictionary.decodeToLong(Dictionary.java:52)
```

## <a name="cause"></a>原因

此实例中的 `java.lang.UnsupportedOperationException` 是由写入到 Parquet 文件夹中的一个或多个具有不兼容架构的 Parquet 文件导致的。

## <a name="solution"></a>解决方案

找到这些 Parquet 文件，然后使用正确的架构重新编写这些文件。 尝试在启用架构合并的情况下读取 Parquet 数据集：

```scala
spark.read.option("mergeSchema", "true").parquet(path)
```

或

```scala
spark.conf.set("spark.sql.parquet.mergeSchema", "true")
spark.read.parquet(path)
```

如果 Parquet 文件确实具有不兼容的架构，那么上面的代码片段会输出一个错误，其中包含具有错误架构的文件的名称。

还可以使用 `merge` 方法检查两个架构是否兼容。 例如，假设你有以下两个架构：

```scala
import org.apache.spark.sql.types._

val struct1 = (new StructType)
  .add("a", "int", true)
  .add("b", "long", false)

val struct2 = (new StructType)
  .add("a", "int", true)
  .add("b", "long", false)
  .add("c", "timestamp", true)
```

然后，可以测试它们是否兼容：

```scala
struct1.merge(struct2).treeString
```

这会出现以下结果：

```scala
res0: String =
"root
|-- a: integer (nullable = true)
|-- b: long (nullable = false)
|-- c: timestamp (nullable = true)
"
```

但是，如果 `struct2` 具有以下不兼容的架构：

```scala
val struct2 = (new StructType)
  .add("a", "int", true)
  .add("b", "string", false)
```

则此测试将为你提供以下 `SparkException`：

```console
org.apache.spark.SparkException: Failed to merge fields 'b' and 'b'. Failed to merge incompatible data types LongType and StringType
```