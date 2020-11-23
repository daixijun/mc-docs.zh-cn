---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 使用 Petastorm 加载数据 - Azure Databricks
description: 了解如何使用 Petastorm 加载数据，以进行机器学习模型的分布式训练和评估。
ms.openlocfilehash: 07eb01a6a831a4d9842a9b9673d1b87bac42ff10
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589941"
---
# <a name="load-data-using-petastorm"></a>使用 Petastorm 加载数据

[Petastorm](https://github.com/uber/petastorm) 是一种开放源代码数据访问库。
此库可直接从 Apache Parquet 格式的数据集和已加载为 Apache Spark 数据帧的数据集对深度学习模型进行单节点或分布式训练和评估。
Petastorm 支持基于 Python 的热门机器学习 (ML) 框架，如 Tensorflow、PyTorch 和 PySpark。
有关 Petastorm 的详细信息，请参阅 [Petastorm GitHub 页](https://github.com/uber/petastorm)和 [Petastorm API 文档](https://petastorm.readthedocs.io/en/latest)。

## <a name="load-data-from-spark-dataframes-using-petastorm"></a>使用 Petastorm 从 Spark 数据帧加载数据

Petastorm Spark 转换器 API 简化了从 Spark 到 TensorFlow 或 PyTorch 的数据转换。 输入 Spark 数据帧首先以 Parquet 格式具体化，然后作为 `tf.data.Dataset` 或 `torch.utils.data.DataLoader` 进行加载。
请参阅 Petastorm API 文档中的 [Spark 数据集转换器 API 部分](https://petastorm.readthedocs.io/en/latest/api.html#module-petastorm.spark.spark_dataset_converter)。

建议的工作流为：

1. 使用 Apache Spark 来加载数据，还可以选择对数据进行预处理。
2. 使用 Petastorm `spark_dataset_converter` 方法可将 Spark 数据帧中的数据转换为 TensorFlow 数据集或 PyTorch DataLoader。
3. 将数据传入 DL 框架进行训练或推理。

### <a name="configure-cache-directory"></a>配置缓存目录

Petastorm Spark 转换器以 Parquet 格式将输入 Spark 数据帧缓存到用户指定的缓存目录位置。 缓存目录必须是以 `file:///dbfs/` 开头的 DBFS FUSE 路径，例如 `file:///dbfs/tmp/foo/` 表示 `dbfs:/tmp/foo/` 的相同位置。 可以通过两种方式配置缓存目录：

* 在群集 [Spark 配置](../../../clusters/configure.md#spark-config)中添加行：`petastorm.spark.converter.parentCacheDirUrl file:///dbfs/...`
* 在笔记本中，调用 `spark.conf.set()`：

  ```python
  from petastorm.spark import SparkDatasetConverter, make_spark_converter

  spark.conf.set(SparkDatasetConverter.PARENT_CACHE_DIR_URL_CONF, 'file:///dbfs/...')
  ```

在使用缓存后，可以通过调用 `converter.delete()` 来显式删除缓存，或通过在对象存储中配置生命周期规则隐式管理缓存。

Databricks 支持三种方案中的 DL 训练：

* 单节点训练
* 分布式超参数优化
* 分布式训练

有关端到端示例，请参阅以下笔记本：

* [简化从 Spark 到 TensorFlow 的数据转换](#petastorm-tensorflow)
* [简化从 Spark 到 PyTorch 的数据转换](#petastorm-pytorch)

## <a name="load-parquet-files-directly-using-petastorm"></a>使用 Petastorm 直接加载 Parquet 文件

此方法优先级低于 Petastorm Spark 转换器 API。

建议的工作流为：

1. 使用 Apache Spark 来加载数据，还可以选择对数据进行预处理。
2. 将 Parquet 格式的数据保存到具有随附 FUSE 装载的 DBFS 路径。
3. 通过 FUSE 装入点以 Petastorm 格式加载数据。
4. 在 DL 框架中使用数据进行训练或推理。

有关端到端示例，请参阅[示例笔记本](#petastorm-example)。

## <a name="examples"></a>示例

### <a name="simplify-data-conversion-from-spark-to-tensorflow-notebook"></a><a id="petastorm-tensorflow"> </a><a id="simplify-data-conversion-from-spark-to-tensorflow-notebook"> </a>简化从 Spark 到 TensorFlow 笔记本的数据转换

[获取笔记本](../../../_static/notebooks/deep-learning/petastorm-spark-converter-tensorflow.html)

### <a name="simplify-data-conversion-from-spark-to-pytorch-notebook"></a><a id="petastorm-pytorch"> </a><a id="simplify-data-conversion-from-spark-to-pytorch-notebook"> </a>简化从 Spark 到 PyTorch 笔记本的数据转换

[获取笔记本](../../../_static/notebooks/deep-learning/petastorm-spark-converter-pytorch.html)

### <a name="use-spark-and-petastorm-to-prepare-data-for-deep-learning-notebook"></a><a id="petastorm-example"> </a><a id="use-spark-and-petastorm-to-prepare-data-for-deep-learning-notebook"> </a>使用 Spark 和 Petastorm 为深度学习笔记本准备数据

[获取笔记本](../../../_static/notebooks/deep-learning/petastorm.html)