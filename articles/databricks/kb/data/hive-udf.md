---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: Hive UDF - Azure Databricks
description: 了解如何为 Azure Databricks 创建和使用 Hive UDF。
ms.openlocfilehash: 5d32704003413ff19d89c03bfbf95f8a4c140d8b
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589760"
---
# <a name="hive-udfs"></a>Hive UDF

本文介绍如何创建 Hive UDF、在 Spark 中注册它以及在 Spark SQL 查询中使用它。

下面是一个 Hive UDF，它采用 `long` 作为参数并返回其十六进制表示形式。

```scala
import org.apache.hadoop.hive.ql.exec.UDF
import org.apache.hadoop.io.LongWritable

// This UDF takes a long integer and converts it to a hexadecimal string.

class ToHex extends UDF {
  def evaluate(n: LongWritable): String = {
    Option(n)
    .map { num =>
        // Use Scala string interpolation. It's the easiest way, and it's
        // type-safe, unlike String.format().
        f"0x${num.get}%x"
    }
    .getOrElse("")
  }
}
```

注册函数：

```scala
spark.sql("CREATE TEMPORARY FUNCTION to_hex AS 'com.ardentex.spark.hiveudf.ToHex'")
```

将函数用作任何其他已注册的函数：

```scala
spark.sql("SELECT first_name, to_hex(code) as hex_code FROM people")
```

可以在[示例 Hive UDF 项目](https://github.com/bmc/spark-hive-udf)中找到更多示例和可编译代码。