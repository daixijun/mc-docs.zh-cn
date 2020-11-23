---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/21/2020
title: HorovodEstimator - 使用 Horovod 和 Apache Spark MLlib 进行分布式深度学习 - Azure Databricks
description: 了解如何使用 HorovodEstimator 对机器学习模型执行分布式训练。
ms.openlocfilehash: c2335baf132be963833485a1ea979e56b0480e5f
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590000"
---
# <a name="horovodestimator-distributed-deep-learning-with-horovod-and-apache-spark-mllib"></a><a id="horovodestimator"> </a><a id="horovodestimator-distributed-deep-learning-with-horovod-and-apache-spark-mllib"> </a>HorovodEstimator：使用 Horovod 和 Apache Spark MLlib 进行分布式深度学习

> [!IMPORTANT]
>
> [Databricks Runtime 7.0 ML](../../../../release-notes/runtime/7.0ml.md) 中已删除 HorovodEstimator。  请改为将 [HorovodRunner](horovod-runner.md#horovodrunner) 用于分布式深度学习训练。

HorovodEstimator 是一种 Apache Spark MLlib 样式的估算器 API，它利用 Uber 开发的 [Horovod](https://github.com/horovod/horovod) 框架。 HorovodEstimator 有助于在 Spark 数据帧上进行深度神经网络的分布式、多 GPU 训练，简化了 Spark 中的 ETL 与 TensorFlow 中的模型训练的集成。 具体而言，HorovodEstimator 通过以下方式简化了使用 Horovod 启动分布式训练：

* 将训练代码和数据分发到群集上的每台计算机
* 在驱动程序和辅助角色之间启用无密码 SSH，并通过 MPI 启动训练
* 编写自定义数据引入和模型导出逻辑
* 同时运行模型训练和评估

## <a name="requirements"></a>要求

Databricks Runtime ML。

可以在包含两个或更多 CPU 的群集或启用了 GPU 的计算机上运行 HorovodEstimator；建议在 GPU 实例上运行（如果可能）。

HorovodEstimator 需要当前群集上的所有 GPU 都可用；因此，我们不建议在共享群集上使用该 API。

如果使用 GPU，我们建议不要在与 HorovodEstimator 配合使用的群集上打开任何其他 TensorFlow 会话。 如果打开 TensorFlow 会话，则运行笔记本的 Python REPL 将使用 GPU，从而阻止 HorovodEstimator 运行。 在这种情况下，你可能需要分离/重新连接笔记本，并在事先未运行任何 TensorFlow 代码的情况下重新运行 HorovodEstimator 代码。

## <a name="distributed-training"></a>分布式训练

HorovodEstimator 是一种 Spark MLlib 估算器，可与 Spark MLlib 管道 API 配合使用，不过，估算器持久化尚不受支持。

拟合 HorovodEstimator 会返回 MLlib 转换器（一个 [TFTransformer](https://github.com/databricks/spark-deep-learning#for-tensorflow-users-1)），该转换器可用于数据帧上的分布式推理。 它还将模型检查点（可用于恢复训练）、事件文件（包含在训练期间记录的指标）和 `tf.SavedModel`（可用于将模型应用到 Spark 之外的推理）存储到指定的模型目录中。

HorovodEstimator 没有容错保证。 如果在训练期间发生错误，HorovodEstimator 不会尝试恢复，不过，你可以重新运行 `fit()` 以从最新的检查点恢复训练。

## <a name="example"></a>示例

以下示例笔记本演示了如何使用 HorovodEstimator 来针对 [MNIST](https://en.wikipedia.org/wiki/MNIST_database) 数据集（一个大型的手写数字数据库，如下图所示）训练深度神经网络。

> [!div class="mx-imgBorder"]
> ![MNIST 数据集](../../../../_static/images/distributed-deep-learning/mnist.png)

通常会被用作机器学习的“Hello World”是，训练一个模型来预测数字。

### <a name="horovodestimator-notebook"></a>HorovodEstimator 笔记本

[获取笔记本](../../../../_static/notebooks/deep-learning/horovod-estimator.html)