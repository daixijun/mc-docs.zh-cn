---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/18/2020
title: 加载数据 - Azure Databricks
description: 在 Azure Databricks 中加载数据供机器学习使用。
ms.openlocfilehash: f2035750b9dc16030893827878858fc4bf0eea64
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589942"
---
# <a name="load-data"></a>加载数据

本部分介绍了如何加载专用于 ML 和 DL 应用程序的数据。 有关加载数据的常规信息，请参阅[数据](../../../data/index.md)。

## <a name="store-files-for-data-loading-and-model-checkpointing"></a>存储文件来执行数据加载和模型检查点操作

机器学习应用程序可能需要使用共享存储来执行数据加载和模型检查点操作。 对于分布式深度学习来说，这尤其重要。 Databricks 提供了 [Databricks 文件系统 (DBFS)](../../../data/databricks-file-system.md)，你可通过它使用 Spark 和本地文件 API 访问群集上的数据。

* **[Databricks Runtime 6.3 ML（不受支持）](../../../release-notes/runtime/6.3ml.md)及更高版本**：Azure Databricks 提供了 [高性能的 FUSE 装载](../../../data/databricks-file-system.md#fuse)。
* **[Databricks Runtime 5.5 LTS ML](../../../release-notes/runtime/5.5ml.md)** ：Azure Databricks 提供了 `dbfs:/ml`，它是一种特殊的文件夹，可为深度学习工作负载提供高性能 I/O - 该文件夹映射到驱动程序和工作器节点上的 `file:/dbfs/ml`。 Azure Databricks 建议将数据保存在 `/dbfs/ml` 下。 这种 FUSE 装载还会缓解 Databricks Runtime 中的[本地文件 I/O API 限制](../../../data/databricks-file-system.md#local-limitations)（即仅支持小于 2 GB 的文件）。

## <a name="load-tabular-data"></a>加载表格数据

可从[表格](../../../data/tables.md)或文件（例如，请参阅 [CSV 文件](../../../data/data-sources/read-csv.md)）加载表格机器学习数据。 可使用 PySpark [toPandas 方法](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#pyspark.sql.DataFrame.toPandas)将 Apache Spark 数据帧转换为 Pandas 数据帧，然后根据需要使用 Pandas [to_numpy 方法](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_numpy.html)转换为 NumPy 格式。

## <a name="prepare-data-for-distributed-training"></a>准备数据以进行分布式训练

本部分介绍了两种用于准备数据进行分布式训练的方法：Petastorm 和 TFRecords。

* [准备数据以进行分布式训练](ddl-data.md)
  * [Petastorm（推荐）](ddl-data.md#petastorm-recommended)
  * [TFRecord](ddl-data.md#tfrecord)