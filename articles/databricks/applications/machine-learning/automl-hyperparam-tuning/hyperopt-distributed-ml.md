---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 将 Hyperopt 与 HorovodRunner 和 Apache Spark MLlib 配合使用 - Azure Databricks
description: 了解如何在使用 Hyperopt 与 HorovodRunner 进行分布式训练时使用自动化 MLflow 跟踪。
ms.openlocfilehash: 9f6dfb4be68fff0808cb27458a7cfaddee98ebe3
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589683"
---
# <a name="hyperopt-with-horovodrunner-and-apache-spark-mllib"></a><a id="hyperopt-distributed-ml"></a><a id="hyperopt-with-horovodrunner-and-apache-spark-mllib"></a>将 Hyperopt 与 HorovodRunner 和 Apache Spark MLlib 配合使用

Hyperopt 通常用于优化可在单台计算机上计算的客观函数。 不过，还可以使用 Hyperopt 优化需要通过分布式训练进行评估的客观函数。 在这种情况下，Hyperopt 会生成表示驱动程序节点上不同超参数设置的试验，并且每个试验均通过可使用整个群集的分布式训练算法进行评估。 此设置适用于任何分布式机器学习算法或库，包括：

* [Apache Spark MLlib](../train-model/mllib/index.md)：Apache Spark 自适应机器学习库，由常见的学习算法和实用工具组成。
* [HorovodRunner](../train-model/distributed-training/horovod-runner.md)一种通用 API，用于使用 [Horovod](https://github.com/horovod/horovod) 框架在 |Databricks| 上运行分布式深度学习工作负载。 通过将 Horovod 与 Spark 的[屏障模式](https://jira.apache.org/jira/browse/SPARK-24374)集成，Azure Databricks 可为 Spark 上长期运行的深度学习训练作业提供更高的稳定性。

下一节演示了使用 Hyperopt 和 HorovodRunner 进行分布式训练的超参数优化示例。

## <a name="how-to-use-hyperopt-with-horovodrunner"></a>如何将 Hyperopt 与 HorovodRunner 配合使用

粗略看来，你只需在传递给 Hyperopt 的客观函数中以分布式模式（即 `HorovodRunner(np>0)`）启动 HorovodRunner 即可。

具体技术详细信息的描述如下。 在 Hyperopt 中，所有试验都在 Spark 驱动程序节点上按顺序进行评估。 相比之下，HorovodRunner 在 Spark 驱动程序节点上启动，它将训练作业分配给 Spark 工作器节点。 HorovodRunner 将返回值收集到驱动程序节点，然后将其传递给 Hyperopt。

> [!NOTE]
>
> Azure Databricks 不支持将常规试验自动记录到 MLflow，因此你必须手动调用 MLflow 来记录 Hyperopt 的试验。

### <a name="hyperopt-and-horovodrunner-distributed-training-notebook"></a>Hyperopt 和 HorovodRunner 分布式训练笔记本

[获取笔记本](../../../_static/notebooks/hyperopt-distributed-ml-training.html)