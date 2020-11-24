---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/24/2020
title: 结构化流式处理教程 - Azure Databricks
description: 了解如何使用 Azure Databricks 进行结构化流式处理，这是用于处理 Apache Spark 中的流式处理数据集的主要模型。
ms.openlocfilehash: 7b2c2c068982d3ef179a8ccad7791c9332a39c85
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589638"
---
# <a name="structured-streaming-tutorial"></a>结构化流教程

传感器、IoT 设备、社交网络和在线交易都生成需要不断监视并快速采取措施的数据。 因此，对大规模实时流处理的需求比以往任何时候都更为明显。 此教程模块介绍了结构化流式处理，这是用于处理 Apache Spark 中的流式处理数据集的主要模型。 在结构化流式处理中，数据流被视为持续追加的表。 这会形成一个与批处理模型非常相似的流处理模型。 你可以像在静态表上一样，将流式处理计算表示为类似于标准批处理的查询，但 Spark 将其作为对无界输入表的增量查询来运行。

> [!div class="mx-imgBorder"]
> ![结构化流式处理工作流](../../_static/images/getting-started/gsasg-spark-streaming-workflow.png)

请将输入数据流视为输入表。 流上到达的每个数据项都像是将追加到输入表的新行。

> [!div class="mx-imgBorder"]
> ![结构化流式处理模型](../../_static/images/getting-started/gsasg-spark-streaming-model.png)

对输入的查询会生成一个结果表。 每个触发器间隔（假设间隔为 1 秒）过后，新行会追加到输入表中，后者最终会更新结果表。 每当更新结果表时，更改的结果行会写入到外部接收器。 输出定义为向外部存储写入的内容。 可以采用不同的模式配置输出：

* **完整模式**：整个更新后的结果表都将写入到外部存储。 由存储连接器决定如何处理整个表的写入。
* **追加模式**：只有自上次触发后在结果表中追加的新行会被写入到外部存储中。 这仅适用于结果表中的现有行不应更改的查询。
* **更新模式**：只有自上次触发后在结果表中更新的行会被写入到外部存储中。 这不同于完整模式，因为更新模式仅输出自上次触发后发生更改的行。 如果查询不包含聚合，则它等效于追加模式。

本教程模块介绍如何执行以下操作：

* [加载示例数据](#load-streaming-sample-data)
* [初始化流](#init-stream)
* [启动流作业](#start-stream)
* [查询流](#query-stream)

我们还提供了一个[示例笔记本](#notebook-stream)，你可导入它来访问和运行模块中包含的所有代码示例。

## <a name="load-sample-data"></a><a id="load-sample-data"> </a><a id="load-streaming-sample-data"> </a>加载示例数据

若要开始使用结构化流式处理，最简单的方法是使用可在 Azure Databricks 工作区中访问的 `/databricks-datasets` 文件夹中提供的 Azure Databricks 数据集示例。 Azure Databricks 将示例事件数据作为 `/databricks-datasets/structured-streaming/events/` 中的文件，用于生成结构化流式处理应用程序。 让我们看看此目录的内容。

> [!div class="mx-imgBorder"]
> ![事件数据集](../../_static/images/getting-started/gsasg-event-dataset.png)

该文件中的每一行都包含一条 JSON 记录，其中包含两个字段：`time` 和 `action`。

```json
{"time":1469501675,"action":"Open"}
{"time":1469501678,"action":"Close"}
{"time":1469501680,"action":"Open"}
{"time":1469501685,"action":"Open"}
{"time":1469501686,"action":"Open"}
{"time":1469501689,"action":"Open"}
{"time":1469501691,"action":"Open"}
{"time":1469501694,"action":"Open"}
{"time":1469501696,"action":"Close"}
{"time":1469501702,"action":"Open"}
{"time":1469501703,"action":"Open"}
{"time":1469501704,"action":"Open"}
```

## <a name="initialize-the-stream"></a><a id="init-stream"> </a><a id="initialize-the-stream"> </a>初始化流

由于示例数据只是一组静态文件，因此你可以通过一次读取一个文件，按创建它们的时间顺序来模拟其中的流。

```python
from pyspark.sql.types import *
from pyspark.sql.functions import *

inputPath = "/databricks-datasets/structured-streaming/events/"

# Define the schema to speed up processing
jsonSchema = StructType([ StructField("time", TimestampType(), True), StructField("action", StringType(), True) ])

streamingInputDF = (
  spark
    .readStream
    .schema(jsonSchema)               # Set the schema of the JSON data
    .option("maxFilesPerTrigger", 1)  # Treat a sequence of files as a stream by picking one file at a time
    .json(inputPath)
)

streamingCountsDF = (
  streamingInputDF
    .groupBy(
      streamingInputDF.action,
      window(streamingInputDF.time, "1 hour"))
    .count()
)
```

## <a name="start-the-streaming-job"></a><a id="start-stream"> </a><a id="start-the-streaming-job"> </a>启动流式处理作业

你可以通过定义一个接收器并启动它来启动流式处理计算。 在我们的示例中，若要以交互方式查询计数，请将 1 小时计数的完整集设置到内存中表中。

```python
query = (
  streamingCountsDF
    .writeStream
    .format("memory")        # memory = store in-memory table (for testing only)
    .queryName("counts")     # counts = name of the in-memory table
    .outputMode("complete")  # complete = all the counts should be in the table
    .start()
)
```

`query` 是在后台运行的名为 `counts` 的流式处理查询的句柄。 此查询会持续选取文件并更新时间窗口内的计数。

命令窗口报告流的状态：

> [!div class="mx-imgBorder"]
> ![流状态](../../_static/images/getting-started/gsasg-stream-status.png)

展开 `counts` 时，你会获得一个仪表板，其中显示了已处理的记录数、批处理统计信息和聚合的状态：

> [!div class="mx-imgBorder"]
> ![流仪表板](../../_static/images/getting-started/gsasg-streaming-dashboard.png)

## <a name="interactively-query-the-stream"></a><a id="interactively-query-the-stream"> </a><a id="query-stream"> </a>以交互方式查询流

我们可以定期查询 `counts` 聚合：

```sql
%sql select action, date_format(window.end, "MMM-dd HH:mm") as time, count from counts order by time, action
```

从此屏幕截图系列中可以看到，每次执行查询时，查询都会变化，以反映基于输入数据流的操作计数。

> [!div class="mx-imgBorder"]
> ![流更新 1](../../_static/images/getting-started/gsasg-streaming-1.png)

> [!div class="mx-imgBorder"]
> ![流更新 2](../../_static/images/getting-started/gsasg-streaming-2.png)

> [!div class="mx-imgBorder"]
> ![流更新 3](../../_static/images/getting-started/gsasg-streaming-3.png)

## <a name="notebook"></a><a id="notebook"> </a><a id="notebook-stream"> </a>笔记本

若要访问这些代码示例和更多内容，请导入以下笔记本。 如需更多的结构化流式处理示例，请参阅[结构化流式处理](../../spark/latest/structured-streaming/index.md)。

### <a name="apache-spark-structured-streaming-python-notebook"></a>Apache Spark 结构化流式处理 Python 笔记本

[获取笔记本](../../_static/notebooks/structured-streaming-python.html)