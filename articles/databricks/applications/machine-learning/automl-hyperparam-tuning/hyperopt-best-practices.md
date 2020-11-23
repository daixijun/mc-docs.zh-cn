---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 分布式 Hyperopt 最佳做法和故障排除 - Azure Databricks
description: 了解最佳做法以及在 Azure Databricks 上使用 Hyperopt 时如何解析不需要的结果
ms.openlocfilehash: aad9aafa9febb65af748220b44ae2d0683fd1aa4
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589680"
---
# <a name="distributed-hyperopt-best-practices-and-troubleshooting"></a><a id="distributed-hyperopt-best-practices-and-troubleshooting"> </a><a id="hyperopt-best-practices"> </a>分布式 Hyperopt 最佳做法和故障排除

## <a name="best-practices"></a>最佳实践

以下是一些可帮助你充分利用 Hyperopt 的方法：

* 贝叶斯方法比网格搜索和随机搜索要有效得多。 因此，使用 Hyperopt Tree of Parzen Estimators (TPE) 算法，通常可以探索更多的超参数和更大的范围。 但是，如果可以使用域知识来限制搜索领域，这将有助于加快优化速度并生成更佳的结果。
* 对于训练时间较长的模型，请首先试验小型数据集和尽可能多的超参数。 使用 MLflow 来自检性能最佳的模型，就如何修复尽可能多的超参数做出明智的决定，并在准备进行大规模优化时智能地缩小参数范围。
* 利用 Hyperopt 对条件维度和超参数的支持。  例如，评估梯度下降的多种形式时，可以让 Hyperopt 包含仅适用于部分形式的条件超参数，而不是将超参数空间限制为常见的超参数。

以下是一些可帮助你充分利用 Hyperopt 与 `SparkTrials` 的方法：

* 将 `parallelism` 相应地配置为仅限 CPU 与启用了 GPU 的群集。 在 Azure Databricks 中，CPU 和 GPU 群集在每个工作器节点上使用不同数量的执行程序线程。 CPU 群集为每个节点使用多个执行程序线程。 GPU 群集仅为每个节点使用一个执行程序线程，避免尝试使用同一 GPU 的多个 Spark 任务之间发生冲突。 虽然通常这最适用于为 GPU 编写的库，但这意味着 GPU 群集上的最大 `parallelism` 数减少了，因此请注意在选择 GPU 实例类型时每个试验可使用的 GPU 数量。 有关详细信息，请参阅[启用了 GPU 的群集](../../../clusters/gpu.md#create-a-gpu-cluster)。
* `SparkTrials` 不应与自动缩放群集一起使用，因为 Hyperopt 在执行开始时选择了 `parallelism`。  如果群集稍后自动缩放，则 Hyperopt 将无法利用新的群集大小。

## <a name="troubleshooting"></a><a id="hyperopt-troubleshooting"> </a><a id="troubleshooting"> </a>故障排除

本部分介绍使用 Hyperopt 时如何解析与预期不符的结果。

* 如果丢失的是 `NaN`（并非数字），通常是因为传递给 `fmin()` 的目标函数返回了 `NaN`。 `NaN` 的丢失不影响其他运行，可以放心地忽略它。  如果要避免 `NaN` 丢失，则可以调整超参数空间或修改目标函数。
* 使用 Hyperopt 搜索方法，丢失通常不会随每次运行而单调地减少。 但是，通常可以比使用其他方法更快地找到最佳的超参数。
* Hyperopt 和 Spark 均会产生某些开销。 对于短时间内的试运行（数十秒），这些开销占主导，而加速可能非常小，甚至为零。
* 使用 `hp.choice` 时，Hyperopt 仅返回选择列表的索引。 因此，登录到 MLflow 中的参数也是索引。 可以使用 `hyperopt.space_eval` 检索参数值。

## <a name="handle-datasets-of-different-orders-of-magnitude-when-using-sparktrials"></a>使用 `SparkTrials` 时处理不同数量级的数据集

`SparkTrials` 在 Spark 工作器节点上运行试用。 本笔记本提供了一些指导原则，说明在使用 `SparkTrials` 时应如何将不同数量级的数据集移动到工作器节点。

### <a name="handle-datasets-of-different-orders-of-magnitude-notebook"></a>处理不同数量级笔记本的数据集

[获取笔记本](../../../_static/notebooks/hyperopt-spark-data.html)