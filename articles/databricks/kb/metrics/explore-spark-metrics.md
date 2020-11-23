---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何利用 Spark 侦听器探索 Apache Spark 指标 - Azure Databricks
description: 了解如何在 Azure Databricks 中使用 Spark 侦听器探索 Apache Spark 指标。
ms.openlocfilehash: f43af9eecd7fc5a3f72a801ec7680bdfc76e521d
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589851"
---
# <a name="how-to-explore-apache-spark-metrics-with-spark-listeners"></a>如何利用 Spark 侦听器探索 Apache Spark 指标

Apache Spark 提供了几个有用的内部侦听器，用于跟踪有关任务和作业的指标。 例如，在开发周期中，这些指标可以帮助你了解何时以及为什么任务需要很长时间才能完成。 当然，你可以利用 Spark UI 或历史记录 UI 来查看每个任务和阶段的信息，但这有一些缺点。 例如，你不能并行比较两个 Spark 作业的统计信息，并且对于大型 Spark 作业，加载 Spark 历史记录 UI 可能需要很长的时间。

可提取 Spark 内部类生成的指标，并将其作为表或 `DataFrame` 保留到磁盘中。 然后，可像查询任何其他数据科学表一样查询 `DataFrame`。

可使用此 [SparkTaskMetrics 包](https://github.com/zheyuan28/SparkTaskMetrics)，了解如何使用 Spark 侦听器从任务和作业中提取指标。

## <a name="build-the-spark-metrics-package"></a>生成 Spark Metrics 包

使用以下命令以生成该包。

```bash
sbt package
```

## <a name="gather-metrics"></a>收集指标

导入 `TaskMetricsExplorer`。 创建查询 `sql("""SELECT * FROM nested_data""").show(false)` 并将其传递到 `runAndMeasure` 中。 查询应至少包含一个 Spark 操作，以触发 Spark 作业。 在执行 Spark 作业之前，Spark 不会生成任何指标。

例如：

```scala
import com.databricks.TaskMetricsExplorer

val t = new TaskMetricsExplorer(spark)
sql("""CREATE OR REPLACE TEMPORARY VIEW nested_data AS
       SELECT id AS key,
       ARRAY(CAST(RAND(1) * 100 AS INT), CAST(RAND(2) * 100 AS INT), CAST(RAND(3) * 100 AS INT), CAST(RAND(4) * 100 AS INT), CAST(RAND(5) * 100 AS INT)) AS values,
       ARRAY(ARRAY(CAST(RAND(1) * 100 AS INT), CAST(RAND(2) * 100 AS INT)), ARRAY(CAST(RAND(3) * 100 AS INT), CAST(RAND(4) * 100 AS INT), CAST(RAND(5) * 100 AS INT))) AS nested_values
       FROM range(5)""")
val query = sql("""SELECT * FROM nested_data""").show(false)
val res = t.runAndMeasure(query)
```

`runAndMeasure` 方法运行命令，并使用 Spark 侦听器获取任务的内部指标。 然后，它运行查询并返回结果：

```
+---+-------------------+-----------------------+
|key|values             |nested_values          |
+---+-------------------+-----------------------+
|0  |[26, 11, 66, 8, 47]|[[26, 11], [66, 8, 47]]|
|1  |[66, 8, 47, 91, 6] |[[66, 8], [47, 91, 6]] |
|2  |[8, 47, 91, 6, 70] |[[8, 47], [91, 6, 70]] |
|3  |[91, 6, 70, 41, 19]|[[91, 6], [70, 41, 19]]|
|4  |[6, 70, 41, 19, 12]|[[6, 70], [41, 19, 12]]|
+---+-------------------+-----------------------+
```

任务指标信息保存在 `DataFrame`中。 可通过以下命令来显示它：

```scala
res.select($"stageId", $"taskType", $"taskLocality", $"executorRunTime", $"duration", $"executorId", $"host", $"jvmGCTime").show(false)
```

随即会显示：

```
+-------+----------+-------------+---------------+--------+----------+---------+---------+
|stageId|taskType  |taskLocality |executorRunTime|duration|executorId| host    |jvmGCTime|
+-------+----------+-------------+---------------+--------+----------+---------+---------+
|3      |ResultTask|PROCESS_LOCAL|2              |9       |driver    |localhost|0        |
|4      |ResultTask|PROCESS_LOCAL|3              |11      |driver    |localhost|0        |
|4      |ResultTask|PROCESS_LOCAL|3              |16      |driver    |localhost|0        |
|4      |ResultTask|PROCESS_LOCAL|2              |20      |driver    |localhost|0        |
|4      |ResultTask|PROCESS_LOCAL|4              |22      |driver    |localhost|0        |
|5      |ResultTask|PROCESS_LOCAL|2              |12      |driver    |localhost|0        |
|5      |ResultTask|PROCESS_LOCAL|3              |17      |driver    |localhost|0        |
|5      |ResultTask|PROCESS_LOCAL|7              |21      |driver    |localhost|0        |
+-------+----------+-------------+---------------+--------+----------+---------+---------+
```

若要查看所有可用的指标名称和数据类型，则显示 `res` `DataFrame` 的架构：

```scala
res.schema.treeString
```

```
root
 |-- stageId: integer (nullable = false)
 |-- stageAttemptId: integer (nullable = false)
 |-- taskType: string (nullable = true)
 |-- index: long (nullable = false)
 |-- taskId: long (nullable = false)
 |-- attemptNumber: integer (nullable = false)
 |-- launchTime: long (nullable = false)
 |-- finishTime: long (nullable = false)
 |-- duration: long (nullable = false)
 |-- schedulerDelay: long (nullable = false)
 |-- executorId: string (nullable = true)
 |-- host: string (nullable = true)
 |-- taskLocality: string (nullable = true)
 |-- speculative: boolean (nullable = false)
 |-- gettingResultTime: long (nullable = false)
 |-- successful: boolean (nullable = false)
 |-- executorRunTime: long (nullable = false)
 |-- executorCpuTime: long (nullable = false)
 |-- executorDeserializeTime: long (nullable = false)
 |-- executorDeserializeCpuTime: long (nullable = false)
 |-- resultSerializationTime: long (nullable = false)
 |-- jvmGCTime: long (nullable = false)
 |-- resultSize: long (nullable = false)
 |-- numUpdatedBlockStatuses: integer (nullable = false)
 |-- diskBytesSpilled: long (nullable = false)
 |-- memoryBytesSpilled: long (nullable = false)
 |-- peakExecutionMemory: long (nullable = false)
 |-- recordsRead: long (nullable = false)
 |-- bytesRead: long (nullable = false)
 |-- recordsWritten: long (nullable = false)
 |-- bytesWritten: long (nullable = false)
 |-- shuffleFetchWaitTime: long (nullable = false)
 |-- shuffleTotalBytesRead: long (nullable = false)
 |-- shuffleTotalBlocksFetched: long (nullable = false)
 |-- shuffleLocalBlocksFetched: long (nullable = false)
 |-- shuffleRemoteBlocksFetched: long (nullable = false)
 |-- shuffleWriteTime: long (nullable = false)
 |-- shuffleBytesWritten: long (nullable = false)
 |-- shuffleRecordsWritten: long (nullable = false)
 |-- errorMessage: string (nullable = true)
```