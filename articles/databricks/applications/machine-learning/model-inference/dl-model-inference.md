---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 深度学习模型推理工作流 - Azure Databricks
description: 了解推荐用于深度学习模型推理的工作流。
ms.openlocfilehash: 56112bc2601832b8e5dc8fed621d81114cdc6ee4
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589584"
---
# <a name="deep-learning-model-inference-workflow"></a>深度学习模型推理工作流

对于深度学习应用程序的模型推理，Azure Databricks 建议以下使用工作流。 如需详细了解如何对 Tensorflow 和 PyTorch 执行模型推理，请参阅[模型推理示例](dl-model-inference-examples.md)。

1. **将数据加载到 Spark 数据帧。**  根据数据类型，Azure Databricks 建议使用以下方法来加载数据：
   * 图像文件（JPG、PNG）：将图像路径加载到 Spark 数据帧。 图像加载和预处理输入数据发生在 pandas UDF 中。

   ```python
   files_df = spark.createDataFrame(map(lambda path: (path,), file_paths), ["path"])
   ```

   * TFRecords：使用 [spark-tensorflow-connector](https://github.com/tensorflow/ecosystem/tree/master/spark/spark-tensorflow-connector) 加载数据。

   ```python
   df = spark.read.format("tfrecords").load(image_path)
   ```

   * 数据源（如 Parquet、CSV、JSON、JDBC 和其他元数据）：使用 [Spark 数据源](../../../data/data-sources/index.md#data-sources)加载数据。
2. **使用 pandas UDF 执行模型推理。** [pandas UDF](https://spark.apache.org/docs/latest/sql-pyspark-pandas-with-arrow.html#pandas-udfs-aka-vectorized-udfs) 使用 Apache Arrow 传输数据，使用 pandas 来处理数据。 若要进行模型推理，请遵循 pandas UDF 工作流中的主要步骤。
   1. 加载训练后的模型：为提高效率，Azure Databricks 建议从驱动程序广播模型的权重并加载模型图，然后从 pandas UDF 的广播变量中获得权重。
   1. 加载和预处理输入数据：若要批量加载数据，Azure Databricks 建议使用 [tf.data API](https://www.tensorflow.org/guide/data)（针对 TensorFlow）和 [DataLoader 类](https://pytorch.org/tutorials/beginner/data_loading_tutorial.html)（针对 PyTorch）。 两者还支持预提取和多线程加载，以隐藏 IO 绑定延迟。
   1. 运行模型预测：对数据批次运行模型推理。
   1. 将预测结果发送回 Spark 数据帧：收集预测结果并作为 `pd.Series` 返回。