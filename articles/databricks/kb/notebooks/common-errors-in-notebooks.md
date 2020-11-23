---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 笔记本中的常见错误 - Azure Databricks
description: 了解 Azure Databricks 笔记本中的常见错误。
ms.openlocfilehash: e4237ea7ea49ca46c4fba98cd51f9a633a6e3e4b
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589847"
---
# <a name="common-errors-in-notebooks"></a>笔记本中的常见错误

使用笔记本时会发生一些常见问题。
本部分概述了一些常见问题和应遵循的最佳做法。

## <a name="spark-job-fails-with-javalangnoclassdeffounderror"></a><a id="no-class-def-error"> </a><a id="spark-job-fails-with-javalangnoclassdeffounderror"> </a>Spark 作业失败，并出现 java.lang.NoClassDefFoundError

有时，可能会遇到如下错误：

```scala
java.lang.NoClassDefFoundError: Could not initialize class line.....$read$
```

如果在同一笔记本单元中搭配使用 case 类定义和 Dataset/DataFrame 操作，之后在另一个单元的 Spark 作业中使用 case 类，则在 Spark Scala 2.11 群集和 Scala 笔记本中可能会发生这种情况。
例如，在第一个单元中，假设定义了 case 类 `MyClass`，并且还创建了数据集。

```scala
case class MyClass(value: Int)

val dataset = spark.createDataset(Seq(1))
```

然后，在后面的单元中，在 Spark 作业中创建 `MyClass` 的实例。

```scala
dataset.map { i => MyClass(i) }.count()
```

### <a name="solution"></a>解决方案

将 case 类定义移至其自己的单元。

```scala
case class MyClass(value: Int)   // no other code in this cell
```

```scala
val dataset = spark.createDataset(Seq(1))
dataset.map { i => MyClass(i) }.count()
```

## <a name="spark-job-fails-with-javalangunsupportedoperationexception"></a>Spark 作业由于出现 java.lang.UnsupportedOperationException 而失败

有时，可能会遇到如下错误：

```console
java.lang.UnsupportedOperationException: Accumulator must be registered before send to executor
```

Spark Scala 2.10 群集和 Scala 笔记本可能会发生这种情况。
此错误的原因和解决方案与 [Spark 作业失败并出现 java.lang.NoClassDefFoundError](#no-class-def-error) 的情况相同。