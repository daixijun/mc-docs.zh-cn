---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 04/14/2020
title: 使用 display() 时，检查点文件不会被删除 - Azure Databricks
description: 了解如何防止 display(streamingDF) 检查点文件使用大量存储空间。
category: Spark Streaming
author: pradeepravi87
db-author: pradeep.ravi@databricks.com
ms.openlocfilehash: 20a58b009bd232943c050932ee8b62df4d40f24a
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589500"
---
# <a name="checkpoint-files-not-being-deleted-when-using-display"></a>使用 `display()` 时，检查点文件不会被删除

## <a name="problem"></a>问题

你有一个流式处理作业使用 `display()` 来显示数据帧。

```scala
val streamingDF = spark.readStream.schema(schema).parquet(<input_path>)
display(streamingDF)
```

检查点文件会被创建，但不会被删除。

可以通过导航到根目录并查看 `/local_disk0/tmp/` 文件夹来验证此问题。 检查点文件保留在文件夹中。

## <a name="cause"></a>原因

该命令 `display(streamingDF)` 是一种内存接收器实现，它可以为每个微批显示流式处理数据帧中的数据。 若要跟踪流式处理更新，需要一个检查点目录。

如果未指定自定义检查点位置，则将在 `/local_disk0/tmp/` 处创建默认的检查点目录。

Azure Databricks 使用检查点目录来确保进度信息的正确性和一致性。 当故意或意外关闭流时，检查点目录将允许 Azure Databricks 重启并从上次离开的确切位置继续操作。

如果因从笔记本取消流而导致该流被关闭，Azure Databricks 作业会尽最大努力清理检查点目录。 如果流以任何其他方式终止，或者如果作业被终止，则不会清理检查点目录。

这是设计使然。

## <a name="solution"></a>解决方案

可以通过以下准则避免不需要的检查点文件。

* 不应在生产作业中使用 `display(streamingDF)`。
* 如果 `display(streamingDF)` 对用例是必需的，则应使用 Apache Spark 配置选项 `spark.sql.streaming.checkpointLocation` 手动指定检查点目录。
* 如果手动指定检查点目录，则应定期删除此目录中的任何剩余文件。 这可以每周执行一次。