---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何从上次写入的偏移位置重启结构化流式处理查询 - Azure Databricks
description: 了解如何从上次写入的偏移位置重启结构化流式处理查询。
ms.openlocfilehash: 35e3fe886892f972f65df2aa7a54b2135934e6a2
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589494"
---
# <a name="how-to-restart-a-structured-streaming-query-from-last-written-offset"></a>如何从上次写入的偏移位置重启结构化流式处理查询

## <a name="scenario"></a>方案

你有一个流运行开窗聚合查询，该查询从 Apache Kafka 进行读取，并以 `Append` 模式写入文件。 你希望升级应用程序并重新启动查询（偏移位置等于上次写入的偏移位置）。 你希望放弃所有尚未写入到接收器的状态信息，从导致该已放弃状态的最早偏移位置开始处理，并相应地修改检查点目录。

但是，如果在升级应用程序代码后使用现有检查点，则会重新使用以前的应用程序版本中的旧状态和对象，这会导致意外的输出，例如从旧的源中进行读取或使用旧的应用程序代码进行处理。

## <a name="solution"></a>解决方案

Apache Spark 在检查点上维护执行和二进制对象的状态。 因此，不能修改检查点目录。 作为替代方法，请随输入记录一起复制和更新偏移位置，并将其存储在文件或数据库中。 在下一次重新启动的初始化期间读取此项，并在 `readStream` 中使用相同的值。 请确保删除检查点目录。

可以使用异步 API 获取当前偏移位置：

```scala
spark.streams.addListener(new StreamingQueryListener() {
    override def onQueryStarted(queryStarted: QueryStartedEvent): Unit = {
        println("Query started:" + queryStarted.id)
    }
    override def onQueryTerminated(queryTerminated: QueryTerminatedEvent): Unit = {
        println("Query terminated" + queryTerminated.id)
    }
    override def onQueryProgress(queryProgress: QueryProgressEvent): Unit = {
     println("Query made progress")
        println("Starting offset:" + queryProgress.progress.sources(0).startOffset)
        println("Ending offset:" + queryProgress.progress.sources(0).endOffset)
        //Logic to save these offsets
    }
})
```

可以将 `readStream` 与上面所示的进程所写入的最新偏移位置一起使用：

```scala
option("startingOffsets",  """ {"articleA":{"0":23,"1":-1},"articleB":{"0":-2}} """)
```

流式处理记录的输入架构是：

```
root
|-- key: binary (nullable = true)
|-- value: binary (nullable = true)
|-- article: string (nullable = true)
|-- partition: integer (nullable = true)
|-- offset: long (nullable = true)
|-- timestamp: timestamp (nullable = true)
|-- timestampType: integer (nullable = true)
```

此外，还可以实现逻辑，以便在数据库中保存和更新该偏移位置，并在下次重新启动时读取它。