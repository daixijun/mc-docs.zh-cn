---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 从元存储中删除包含已损坏的元数据的表 - Azure Databricks
description: 了解如何从元存储中删除包含已损坏的元数据的表。
ms.openlocfilehash: 762d9d30ce86fa04ee609ee7949b08106239749b
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589873"
---
# <a name="drop-tables-with-corrupted-metadata-from-the-metastore"></a>从元存储中删除包含已损坏的元数据的表

本文介绍如何删除包含已损坏的元数据的表。

## <a name="problem"></a>问题

有时无法从 Azure Databricks UI 中删除表。 无法使用 `%sql` 或 `spark.sql` 来删除表。

## <a name="cause"></a>原因

元存储中存储的元数据（表架构）已损坏。 运行 `Drop table` 命令时，Spark 会检查表是否存在，然后再删除表。 由于表的元数据已损坏，因此 Spark 无法删除表，操作将失败并出现以下异常。

```scala
com.databricks.backend.common.rpc.DatabricksExceptions$SQLExecutionException: org.apache.spark.sql.AnalysisException: The metadata is corrupted
```

## <a name="solution"></a>解决方案

使用 Hive 客户端来删除表，因为 Hive 客户端不会像 Spark 那样检查表是否存在。 若要删除表：

1. 在 Hive 包中创建一个函数。

```scala
package org.apache.spark.sql.hive {
import org.apache.spark.sql.hive.HiveUtils
import org.apache.spark.SparkContext

object utils {
    def dropTable(sc: SparkContext, dbName: String, tableName: String, ignoreIfNotExists: Boolean, purge: Boolean): Unit = {
      HiveUtils
          .newClientForMetadata(sc.getConf, sc.hadoopConfiguration)
          .dropTable(dbName, tableName, ignoreIfNotExists, false)
    }
  }
}
```

2. 删除已损坏的表。

```scala
import org.apache.spark.sql.hive.utils
utils.dropTable(sc, "default", "my_table", true, true)
```