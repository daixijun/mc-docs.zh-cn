---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/03/2020
title: PyTorch - Azure Databricks
description: 了解如何使用 PyTorch 在单个节点上训练机器学习模型。
ms.openlocfilehash: f2237f98ab4b564b0caeb81915601e008368a554
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589981"
---
# <a name="pytorch"></a>PyTorch

[PyTorch 项目](https://github.com/pytorch)是一个 Python 包，可提供 GPU 加速的张量计算和用于构建深度学习网络的高级功能。
如需许可详细信息，请参阅 [GitHub 上的 PyTorch 许可文档](https://github.com/pytorch/pytorch/blob/a90c259edad1ea4fa1b8773e3cb37240df680d62/LICENSE)。

若要监视和调试 PyTorch 模型，请考虑使用 [TensorBoard](tensorflow.md#using-tensorboard)。

以下部分提供了有关在 Azure Databricks 上安装 PyTorch 的指导，并提供了运行 PyTorch 程序的示例。

> [!NOTE]
>
> 本文并不是 PyTorch 的全面指南。  请参阅 [PyTorch 网站](https://pytorch.org/)。

## <a name="install-pytorch"></a>安装 PyTorch

### <a name="databricks-runtime-for-ml"></a>用于 ML 的 Databricks Runtime

[用于机器学习的 Databricks Runtime](../../../runtime/mlruntime.md) 包括 PyTorch，因此你可以创建群集并开始使用 PyTorch。 下面是包括的 Pytorch 版本：

| Databricks Runtime ML 版                                                      | PyTorch 版本                                    |
|------------------------------------------------------------------------------------|----------------------------------------------------|
| [Databricks Runtime 7.3 ML](../../../release-notes/runtime/7.3ml.md)               | 1.6.0                                              |
| [Databricks Runtime 7.2 ML](../../../release-notes/runtime/7.2ml.md)               | 1.5.1                                              |
| [Databricks Runtime 7.1 ML](../../../release-notes/runtime/7.1ml.md)               | 1.5.1                                              |
| [Databricks Runtime 7.0 ML](../../../release-notes/runtime/7.0ml.md)               | 1.5.0                                              |
| [Databricks Runtime 6.6 ML](../../../release-notes/runtime/6.6ml.md)               | 1.4.0                                              |
| [Databricks Runtime 6.5 ML](../../../release-notes/runtime/6.5ml.md)               | 1.4.0                                              |
| [Databricks Runtime 6.4 ML](../../../release-notes/runtime/6.4ml.md)               | 1.4.0                                              |
| [Databricks Runtime 6.3 ML（不受支持）](../../../release-notes/runtime/6.3ml.md) | 1.3.1                                              |
| [Databricks Runtime 5.5 LTS ML](../../../release-notes/runtime/5.5ml.md)           | 1.1.0                                              |

### <a name="databricks-runtime"></a>Databricks Runtime

建议使用[用于机器学习的 Databricks Runtime](../../../runtime/mlruntime.md) 中包含的 PyTorch。  但是，如果必须使用 [Databricks Runtime](../../../runtime/dbr.md)，则可以以 [Databricks PyPI 库](../../../libraries/index.md)的形式安装 PyTorch。 下面的示例演示如何安装 PyTorch 1.5.0：

* 在 GPU 群集上，通过指定以下内容来安装 `pytorch` 和 `torchvision`：
  * `torch==1.5.0`
  * `torchvision==0.6.0`
* 在 CPU 群集上，使用以下 wheel 文件安装 `pytorch` 和 `torchvision`：
  * `https://download.pytorch.org/whl/cpu/torch-1.5.0%2Bcpu-cp37-cp37m-linux_x86_64.whl`
  * `https://download.pytorch.org/whl/cpu/torchvision-0.6.0%2Bcpu-cp37-cp37m-linux_x86_64.whl`

## <a name="use-pytorch-on-a-single-node"></a>在单节点上使用 PyTorch

若要测试和迁移单机 PyTorch 工作流，可以通过将工作器数设置为零，在 Azure Databricks 上从仅限驱动程序的群集着手。
尽管 Apache Spark 在此设置下不起作用，但这是运行单机 PyTorch 工作流的一种经济高效的方法。

### <a name="pytorch-notebook"></a>PyTorch 笔记本

[获取笔记本](../../../_static/notebooks/deep-learning/pytorch-single-node.html)