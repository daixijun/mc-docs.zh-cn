---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 多个 Apache Spark JAR 作业在并发运行时失败 - Azure Databricks
ms.openlocfilehash: ef328a7b3694fae8ffc1ca02d970577a018a23d8
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589502"
---
# <a name="multiple-apache-spark-jar-jobs-fail-when-run-concurrently"></a>多个 Apache Spark JAR 作业在并发运行时会失败

## <a name="problem"></a>问题

如果同时运行多个 Apache Spark JAR 作业，其中某些运行可能会失败，并显示以下错误：

```console
org.apache.spark.sql.AnalysisException: Table or view not found: xxxxxxx; line 1 pos 48
```

## <a name="cause"></a>原因

此错误是 Scala 中的 bug 导致的。 当对象扩展 `App` 时，其 `val` 字段将不再是不可变的，可能会在调用 `main` 方法时变化。 如果多次运行 JAR 作业，则包含数据帧的 `val` 字段可能会被意外更改。

因此，当任一并发运行完成时，它会清除其他运行的临时视图。 [Scala 问题 11576](https://github.com/scala/bug/issues/11576) 提供了更多详细信息。

## <a name="solution"></a>解决方案

若要解决此 bug，请显式调用 `main()` 方法。 例如，如果你有类似于下面的代码：

```scala

  object MainTest extends App {
    ...
  }
```

可以将其替换为不扩展 `App` 的代码：

```scala

  object MainTest {
    def main(args: Array[String]) {
    ......
    }
  }
```