---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 深度学习模型推理性能优化指南 - Azure Databricks
description: 了解如何优化模型推理性能以实现深度学习。
ms.openlocfilehash: 2302278d669cb7dbc9a27c45c05851814f4a5ee3
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589986"
---
# <a name="deep-learning-model-inference-performance-tuning-guide"></a>深度学习模型推理性能优化指南

本部分提供有关 Azure Databricks 上模型推理的调试和性能优化的一些提示。 有关概述，请参阅[深度学习推理工作流](dl-model-inference.md)。

模型推理通常有两个主要部分：数据输入管道和模型推理。 数据输入管道的数据 I/O 输入量较大，模型推理的计算量较大。 确定工作流的瓶颈非常简单。 以下是一些方法：

* 将模型缩小为普通模型，并测量每秒的示例数。 如果完整模型与普通模型之间的端到端时间之间的差异很小，则数据输入管道可能是瓶颈，否则模型推理就是瓶颈。
* 如果使用 GPU 运行模型推理，请检查 GPU 利用率[指标](../../../clusters/clusters-manage.md#cluster-performance)。 如果 GPU 利用率不是持续偏高，则数据输入管道可能会成为瓶颈。

## <a name="optimize-data-input-pipeline"></a>优化数据输入管道

使用 GPU 可以有效地优化模型推理的运行速度。  随着 GPU 和其他加速器的速度越来越快，数据输入管道必须跟上需求。 数据输入管道将数据读取到 Spark 数据帧中，对其进行转换，并将其加载为模型推理的输入。 如果数据输入是瓶颈，以下是增加 I/O 吞吐量的一些提示：

* 设置每批的最大记录。 如果记录可以装入内存，则大量最大记录可以减少调用 UDF 函数所用的 I/O 开销。 若要设置批大小，请设置以下配置：

  ```python
  spark.conf.set("spark.sql.execution.arrow.maxRecordsPerBatch", "5000")
  ```

* 在预处理 pandas UDF 中的输入数据时，分批加载数据并对其进行预提取。

  对于 TensorFlow，Azure Databricks 建议使用 [tf.data API](https://www.tensorflow.org/guide/data)。 可以通过在 `map` 函数中设置 `num_parallel_calls` 并为预提取和批处理调用 `prefetch` 和 `batch` 来并行分析映射。

  ```python
  dataset.map(parse_example, num_parallel_calls=num_process).prefetch(prefetch_size).batch(batch_size)
  ```

  对于 PyTorch，Azure Databricks 建议使用 [DataLoader 类](https://pytorch.org/tutorials/beginner/data_loading_tutorial.html)。 可以为批处理和 `num_workers` 设置 `batch_size`，以便进行并行数据加载。

  ```python
  torch.utils.data.DataLoader(images, batch_size=batch_size, num_workers=num_process)
  ```