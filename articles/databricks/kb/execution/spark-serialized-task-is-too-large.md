---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 序列化任务太大 - Azure Databricks
description: 了解 Azure Databricks 中序列化任务太大时要执行的操作。
ms.openlocfilehash: c5fd3f50fefb05fcf565f8f3db0d10d2599ca35b
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589460"
---
# <a name="serialized-task-is-too-large"></a>序列化任务太大

如果你看到以下错误消息，则可以通过在启动群集时更改 [Spark 配置](/databricks/clusters/configure#spark-config)来修复此错误。

若要更改 Spark 配置，请设置属性：

虽然优化配置是一种选择，但通常此错误消息意味着你将驱动程序中一些大型对象发送到了执行器，例如，使用大型列表调用 `parallelize`，或将大型 R 数据帧转换为 Spark 数据帧。

如果是这样，我们建议首先审核代码以删除使用的大型对象，或改用广播变量。 如果这样做不能解决此错误，则可以增大分区号以将大型列表拆分为多个小型列表，从而减少 Spark RPC 消息大小。 下面是 Scala 和 Python 的示例：

```scala
val largeList = Seq(...) // This is a large list
val partitionNum = 100 // Increase this number if necessary
val rdd = sc.parallelize(largeList, partitionNum)
val ds = rdd.toDS()
```

```python
largeList = [...] # This is a large list
partitionNum = 100 # Increase this number if necessary
rdd = sc.parallelize(largeList, partitionNum)
ds = rdd.toDS()
```

R 用户需要增加 Spark 配置 `spark.default.parallelism` 以在群集初始化时增大分区号。 创建群集后，无法设置此配置。