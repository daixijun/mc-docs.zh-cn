---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/18/2020
title: 深度学习 - Azure Databricks
description: 了解如何在 Azure Databricks 中训练深度学习模型。
ms.openlocfilehash: 7a057995c47aec61df75f25e12b31882ef608862
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590001"
---
# <a name="deep-learning"></a>深度学习

本部分包括使用两个最常用深度学习库 TensorFlow 和 PyTorch 的示例笔记本。

由于深度学习模型需要大量数据和计算，因此分布式训练非常重要。 本部分还包括使用 Horovod 和 spark-tensorflow-distributor 进行分布式深度学习的信息和示例。

* [TensorFlow](tensorflow.md)
  * [Databricks Runtime ML 中包含的 TensorFlow 版本](tensorflow.md#tensorflow-versions-included-in-databricks-runtime-ml)
  * [安装 TensorFlow](tensorflow.md#install-tensorflow)
  * [TensorBoard](tensorflow.md#tensorboard)
  * [在单节点上使用 TensorFlow](tensorflow.md#use-tensorflow-on-a-single-node)
* [PyTorch](pytorch.md)
  * [安装 PyTorch](pytorch.md#install-pytorch)
  * [在单节点上使用 PyTorch](pytorch.md#use-pytorch-on-a-single-node)
* [分布式训练](distributed-training/index.md)
  * [Horovod](distributed-training/index.md#horovod)
  * [spark-tensorflow-distributor](distributed-training/index.md#spark-tensorflow-distributor)
* [深度学习管道](deep-learning-pipelines.md)
  * [迁移到 Databricks Runtime 7.0 ML 及更高版本的指南](deep-learning-pipelines.md#migration-guide-to-databricks-runtime-70-ml-and-above)