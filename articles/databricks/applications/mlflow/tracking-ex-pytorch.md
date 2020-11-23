---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 训练 PyTorch 模型 - Azure Databricks
description: 了解如何跟踪 PyTorch 模型的机器学习训练。
ms.openlocfilehash: 20fe97707f8fb6dd6b21a486fab6860cf5baa48f
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589910"
---
# <a name="train-a-pytorch-model"></a><a id="train-a-pytorch-model"> </a><a id="training-pytorch"> </a>训练 PyTorch 模型

[PyTorch](../machine-learning/train-model/pytorch.md) 是一个 Python 包，它为构建深度学习网络提供了 GPU 加速张量计算和高级功能。

MLflow PyTorch 笔记本适用于基于 MNIST 手写数字识别数据的神经网络。 运行结果会记录到 MLflow 服务器。 TensorFlow 事件格式的训练指标和权重会记录在本地，然后上传到 MLflow 运行的项目目录。 最后，TensorBoard 启动并读取记录在本地的事件。

## <a name="mlflow-pytorch-model-training-notebook"></a>MLflow PyTorch 模型训练笔记本

[获取笔记本](../../_static/notebooks/mlflow/mlflow-pytorch-training.html)