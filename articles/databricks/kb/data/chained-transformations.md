---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 简化链式转换 - Azure Databricks
description: 了解如何在 Azure Databricks 中简化数据帧上的链式转换。
ms.openlocfilehash: a47e4ca3c060483a6efd53a84fd1d7ee7075b0fc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589761"
---
# <a name="simplify-chained-transformations"></a>简化链式转换

有时可能需要在数据帧上执行多个转换：

```scala
import org.apache.spark.sql.functions._
import org.apache.spark.sql.DataFrame

val testDf = (1 to 10).toDF("col")

def func0(x: Int => Int, y: Int)(in: DataFrame): DataFrame = {
  in.filter('col > x(y))
}
def func1(x: Int)(in: DataFrame): DataFrame = {
  in.selectExpr("col", s"col + $x as col1")
}
def func2(add: Int)(in: DataFrame): DataFrame = {
  in.withColumn("col2", expr(s"col1 + $add"))
}
```

应用这些转换时，可能最终生成如下所示的面条式代码：

```scala
def inc(i: Int) = i + 1

val tmp0 = func0(inc, 3)(testDf)
val tmp1 = func1(1)(tmp0)
val tmp2 = func2(2)(tmp1)
val res = tmp2.withColumn("col3", expr("col2 + 3"))
```

本文介绍了几种简化链式转换的方法。

## <a name="dataframe-transform-api"></a>数据帧 `transform` API

若要利用 Spark 中的函数编程样式，可以使用数据帧 `transform` API，例如：

```scala
val res = testDf.transform(func0(inc, 4))
                .transform(func1(1))
                .transform(func2(2))
                .withColumn("col3", expr("col2 + 3"))
```

## <a name="functionchain-api"></a>`Function.chain` API

若要进一步了解，可以利用 Scala [函数](https://www.scala-lang.org/api/current/scala/Function$.html)库进行 `chain` 转换，例如：

```scala
val chained = Function.chain(List(func0(inc, 4)(_), func1(1)(_), func2(2)(_)))
val res = testDf.transform(chained)
                .withColumn("col3", expr("col2 + 3"))
```

## <a name="implicit-class"></a>`implicit` 类

另一种替代方案是定义 Scala `implicit` 类，使用该类可消除数据帧 `transform` API：

```scala
implicit class MyTransforms(df: DataFrame) {
    def func0(x: Int => Int, y: Int): DataFrame = {
        df.filter('col > x(y))
    }
    def func1(x: Int): DataFrame = {
        df.selectExpr("col", s"col + $x as col1")
    }
    def func2(add: Int): DataFrame = {
        df.withColumn("col2", expr(s"col1 + $add"))
    }
}
```

然后，可以直接调用函数：

```scala
val res = testDf.func0(inc, 1)
            .func1(2)
            .func2(3)
            .withColumn("col3", expr("col2 + 3"))
```