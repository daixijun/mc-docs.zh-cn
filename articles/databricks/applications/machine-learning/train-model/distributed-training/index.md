---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/21/2020
title: 分布式训练 - Azure Databricks
description: 了解如何对机器学习模型执行分布式训练。
ms.openlocfilehash: 54a71d0a0d95895c2d3f4400eac6dd9220d8c7e4
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589903"
---
# <a name="distributed-training"></a>分布式训练

Azure Databricks 建议尽量在一台计算机上训练神经网络；由于通信开销，用于训练和推理的分布式代码比单计算机代码更复杂，且速度更慢。 但是，如果模型或数据太大，以致无法装入一台计算机的内存中，则应该考虑使用分布式训练和推理。

> [!NOTE]
>
> Azure Databricks 支持的 GPU VM 上不提供加速网络。 因此，我们不建议在多节点 GPU 群集上运行分布式 DL 训练。 可以使用单个多 GPU 节点或一个多节点 CPU 群集来运行分布式 DL 训练。

## <a name="horovod"></a>Horovod

[Horovod](https://github.com/horovod/horovod) 是适用于 TensorFlow、Keras 和 PyTorch 的分布式训练框架。 Azure Databricks 支持使用 HorovodRunner 进行分布式 DL 训练。

### <a name="requirements"></a>要求

Databricks Runtime ML。

### <a name="use-horovod"></a>使用 Horovod

以下页面提供了有关使用 Horovod 进行分布式深度学习的一般信息，以及演示如何使用 HorovodRunner 的示例笔记本。

* [HorovodRunner：使用 Horovod 进行分布式深度学习](horovod-runner.md)
* [HorovodRunner 示例](horovod-runner-examples.md)

### <a name="troubleshoot-horovod-installation"></a>排查 Horovod 安装问题

**问题**：导入 `horovod.{torch|tensorflow}` 引发 `ImportError: Extension horovod.{torch|tensorflow} has not been built`

**解决方案**；Horovod 预安装在 Databricks Runtime ML 上，因此，如果未正确更新环境，通常会发生此错误。 此错误表示 Horovod 是在所需库（PyTorch 或 TensorFlow）之前安装的。 由于 Horovod 是在安装过程中编译的，因此如果在 Horovod 安装过程中没有这些包，则系统不会编译 `horovod.{torch|tensorflow}`。
要解决该问题，请执行以下步骤：

1. 验证是否位于 Databricks Runtime ML 群集上。
2. 确保已安装 PyTorch 或 TensorFlow 包。
3. 卸载 Horovod (`%pip uninstall -y horovod`)。
4. 安装 `cmake` (`%pip install cmake`)。
5. 重新安装 `horovod`。

### <a name="horovodestimator"></a>HorovodEstimator

Databricks Runtime ML 6.6 及更低版本支持 HorovodEstimator，后者与 HorovodRunner 类似，但你只能通过它使用 TensorFlow 估算器和 Apache Spark ML 管道 API。

* [HorovodEstimator：使用 Horovod 和 Apache Spark MLlib 进行分布式深度学习](horovod-estimator.md)

## <a name="spark-tensorflow-distributor"></a>spark-tensorflow-distributor

[spark-tensorflow-distributor](https://github.com/tensorflow/ecosystem/tree/master/spark/spark-tensorflow-distributor) 是 TensorFlow 中的开源原生包，用于在 Spark 群集上通过 TensorFlow 进行分布式训练。

* [使用 TensorFlow 2 进行分布式训练](spark-tf-distributor.md)