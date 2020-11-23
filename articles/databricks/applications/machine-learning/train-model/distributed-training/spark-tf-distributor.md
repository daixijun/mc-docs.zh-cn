---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/26/2020
title: 使用 TensorFlow 2 进行分布式训练 - Azure Databricks
description: 了解如何使用 spark-tensorflow-distributor 对机器学习模型执行分布式训练。
ms.openlocfilehash: 9cb264d98103f35f0bad0fa10be7db6bd2888721
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589599"
---
# <a name="distributed-training-with-tensorflow-2"></a><a id="distributed-training-with-tensorflow-2"> </a><a id="spark-tf-distributor"> </a> 使用 TensorFlow 2 进行分布式训练

[spark-tensorflow-distributor](https://github.com/tensorflow/ecosystem/tree/master/spark/spark-tensorflow-distributor) 是 TensorFlow 中的开源本机包，可帮助用户在 Spark 群集上使用 TensorFlow 进行分布式训练。 它是在 `tensorflow.distribute.Strategy`（TensorFlow 2 中的主要功能之一）的基础上构建的。 有关详细的 API 文档，请参阅 [docstrings](https://github.com/tensorflow/ecosystem/blob/master/spark/spark-tensorflow-distributor/spark_tensorflow_distributor/mirrored_strategy_runner.py#L40)。 有关分布式 TensorFlow 的常规文档，请参阅[使用 TensorFlow 进行分布式训练](https://www.tensorflow.org/guide/distributed_training)。

## <a name="example-notebook"></a>示例笔记本

### <a name="distributed-training-with-tensorflow-2"></a>使用 TensorFlow 2 进行分布式训练

[获取笔记本](../../../../_static/notebooks/deep-learning/spark-tensorflow-distributor.html)