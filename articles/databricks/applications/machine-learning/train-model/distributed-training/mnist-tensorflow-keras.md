---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 结合使用 TensorFlow 和面向 MNIST 的 HorovodRunner 的分布式深度学习训练 - Azure Databricks
description: 了解如何使用 Keras 对机器学习模型执行分布式训练。
ms.openlocfilehash: 8491dd7278afc0b99bbe733a5586e31803468785
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589898"
---
# <a name="distributed-deep-learning-training-using-tensorflow-with-horovodrunner-for-mnist"></a><a id="distributed-deep-learning-training-using-tensorflow-with-horovodrunner-for-mnist"> </a><a id="mnist-tensorflow-keras"> </a>结合使用 TensorFlow 和面向 MNIST 的 HorovodRunner 的分布式深度学习训练

以下笔记本演示了推荐的[开发工作流](horovod-runner.md#development-workflow)。 运行笔记本前，请准备好数据以进行分布式训练。

## <a name="horovodrunner-tensorflow-and-keras-mnist-example-notebook"></a>HorovodRunner TensorFlow 和 Keras MNIST 示例笔记本

[获取笔记本](../../../../_static/notebooks/deep-learning/mnist-tensorflow-keras.html)