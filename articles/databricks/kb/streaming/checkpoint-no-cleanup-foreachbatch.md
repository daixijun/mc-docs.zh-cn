---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 06/23/2020
title: 使用 foreachBatch() 时，检查点文件不会被删除 - Azure Databricks
description: 了解如何防止 foreachBatch() 检查点文件使用大量存储空间。
category: Spark Streaming
author: pradeepravi87
db-author: pradeep.ravi@databricks.com
ms.openlocfilehash: c588be8aaa12f829c829943f868f2b8f62761a53
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589471"
---
# <a name="checkpoint-files-not-being-deleted-when-using-foreachbatch"></a>使用 `foreachBatch()` 时，检查点文件不会被删除

## <a name="problem"></a>问题

你让一个流式处理作业使用 `foreachBatch()` 来处理数据帧。

```scala
streamingDF.writeStream.outputMode("append").foreachBatch { (batchDF: DataFrame, batchId: Long) =>
  batchDF.write.format("parquet").mode("overwrite").save(output_directory)
}.start()
```

将创建检查点文件，但不会删除这些文件。

可以通过导航到根目录并查找 `/local_disk0/tmp/` 文件夹来验证此问题。 检查点文件保留在文件夹中。

## <a name="cause"></a>原因

命令 `foreachBatch()` 用于支持流式处理数据帧上通常不支持的数据帧操作。 通过使用 `foreachBatch()`，可以将这些操作应用于每个微批处理。 这需要一个用于跟踪流式处理更新的检查点目录。

如果未指定自定义检查点位置，则将在 `/local_disk0/tmp/` 处创建默认的检查点目录。

Azure Databricks 使用检查点目录来确保进度信息的正确性和一致性。 当故意或意外关闭流时，检查点目录将允许 Azure Databricks 重启并从上次离开的确切位置继续操作。

如果因从笔记本取消流而导致该流被关闭，Azure Databricks 作业会尝试最大程度地清理检查点目录。 如果流以任何其他方式终止，或者如果作业被终止，则不会清理检查点目录。

这是设计使然。

## <a name="solution"></a>解决方案

应使用 `checkpointLocation` 选项手动指定检查点目录。

```scala
streamingDF.writeStream.option("checkpointLocation","<checkpoint-path>").outputMode("append").foreachBatch { (batchDF: DataFrame, batchId: Long) =>
batchDF.write.format("parquet").mode("overwrite").save(output_directory)
}.start()
```