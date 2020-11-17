---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/26/2020
title: 模型推理 - Azure Databricks
description: 了解如何使用训练的机器学习模型进行推理。
ms.openlocfilehash: d53cdd87b0402041ca08d2cdf67560d4937edaab
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589586"
---
# <a name="model-inference"></a>模型推理

对于使用 MLLib 和 XGBoost4J 模型进行的可缩放模型推理，请使用原生 `transform` 方法直接在 Spark 数据帧上执行推理。 请参阅 [MLlib 示例笔记本](../train-model/mllib/index.md#example-notebooks)，查看包含推理步骤的几个示例笔记本。

对于其他库和模型类型，请创建一个 Spark UDF 来横向扩展针对大型数据集的推理。 对于较小的数据集，请使用库提供的原生模型推理例程。

如果在 MLfLow 中注册了模型，可以[创建 Spark UDF](https://www.mlflow.org/docs/latest/python_api/mlflow.pyfunc.html#mlflow.pyfunc.spark_udf)。
否则，请使用 [Pandas 迭代器 UDF](../../../spark/latest/spark-sql/udf-python-pandas.md) 来包装机器学习模型。

对于流式处理应用程序，请使用 Apache Spark [结构化流式处理](../../../spark/latest/structured-streaming/index.md) API。 请参阅 Apache Spark [MLib 管道和结构化流式处理示例](../train-model/mllib/index.md#mllib-pipelines-and-structured-streaming-example)。

以下文章介绍如何在 Azure Databricks 上进行深度学习模型推理。

* [深度学习模型推理工作流](dl-model-inference.md)
* [深度学习模型推理示例](dl-model-inference-examples.md)
* [深度学习模型推理性能优化指南](model-inference-performance.md)
* [分布式图像模型推理的参考解决方案](../reference-solutions/images-etl-inference.md)