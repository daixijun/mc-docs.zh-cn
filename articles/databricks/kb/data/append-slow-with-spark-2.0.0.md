---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: Spark 2.0.0 群集花费很长时间来追加数据 - Azure Databricks
description: 了解如何排查 Azure Databricks 群集追加数据速度慢的问题。
ms.openlocfilehash: 14d73f6aaddc627b07c7dfb4b6c41d4560eba82b
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589757"
---
# <a name="spark-200-cluster-takes-a-long-time-to-append-data"></a>Spark 2.0.0 群集花费很长时间来追加数据

如果你发现使用 `Spark 2.0.0` 版本的群集需要更长的时间才能将数据追加到现有数据集，尤其是所有 Spark 作业都已完成而命令尚未完成，这是因为驱动程序节点正在将任务的输出文件从作业临时目录逐个移动到最终的目标（使用云存储时，此过程较慢）。 要解决此问题，请将 `mapreduce.fileoutputcommitter.algorithm.version` 设置为 2. 此问题不会影响覆盖数据集或将数据写入到新位置。

> [!NOTE]
>
> 从 `Spark 2.0.1-db1` 开始，`mapreduce.fileoutputcommitter.algorithm.version` 的默认值为 2。
> 如果使用的是 `Spark 2.0.0`，请在遇到此缓慢问题时手动设置此配置。

如何确认是否遇到此问题？

可以通过检查以下内容来确认是否遇到了这个问题：

1. 所有 Spark 作业都已完成，而单元格尚未完成。 进度栏应如下所示

   > [!div class="mx-imgBorder"]
   > ![no-alternative-text](../_static/images/data/progress-bar.png)

2. 驱动程序的线程转储（可以在 Spark UI 的执行程序页上找到）显示 `FileOutputCommitter` 类的 `commitJob` 方法中有一个线程花费了很长时间。

如何将 `spark.hadoop.mapreduce.fileoutputcommitter.algorithm.version` 设置为 2？

可以使用以下任一方法来设置此配置：

* 启动群集时，可以将 `spark.hadoop.mapreduce.fileoutputcommitter.algorithm.version 2` 放入 Spark 配置。
* 可以在笔记本中运行 `%sql set mapreduce.fileoutputcommitter.algorithm.version=2` 或 `spark.conf.set("mapreduce.fileoutputcommitter.algorithm.version", "2")`（`spark` 是 Databricks 笔记本随附的 `SparkSession` 对象）。
* 使用数据集 API 写入数据时，可以在选项中设置它，即 `dataset.write.option("mapreduce.fileoutputcommitter.algorithm.version", "2")`。

原因是什么？

当 Spark 将数据追加到现有数据集时，Spark 使用 `FileOutputCommitter` 来管理暂存输出文件和最终输出文件。 `FileOutputCommitter` 的行为直接影响写入数据的作业的性能。

`FileOutputCommitter` 有两种方法，`commitTask` 和 `commitJob`。
Apache Spark 2.0 和更高版本使用 Apache Hadoop 2，而 Apache Hadoop 2 使用 `mapreduce.fileoutputcommitter.algorithm.version` 的值控制 `commitTask` 和 `commitJob` 的工作方式。 在 Hadoop 2 中，`mapreduce.fileoutputcommitter.algorithm.version` 的默认值为 1。 对于此版本，`commitTask` 将任务生成的数据从任务临时目录移动到作业临时目录，当所有任务完成时，`commitJob` 将数据从作业临时目录移动到最终目标 <sup>[[1]]</sup>。 由于驱动程序正在执行 `commitJob` 的工作，因此对于云存储，此操作可能需要很长时间。 你可能常常认为你的单元格处于“挂起”状态。
但是，当 `mapreduce.fileoutputcommitter.algorithm.version` 的值为 2 时，`commitTask` 将任务生成的数据直接移动到最终目标，而 `commitJob` 基本上不执行任何操作。

[1]: https://github.com/apache/hadoop/blob/branch-2.7.3/hadoop-mapreduce-project/hadoop-mapreduce-client/hadoop-mapreduce-client-core/src/main/java/org/apache/hadoop/mapreduce/lib/output/FileOutputCommitter.java