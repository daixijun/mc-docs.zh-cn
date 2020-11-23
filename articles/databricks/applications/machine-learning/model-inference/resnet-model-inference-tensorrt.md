---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 使用 TensorFlow 和 TensorRT 进行模型推理 - Azure Databricks
description: 了解如何使用 TensorRT 进行模型推理。
ms.openlocfilehash: 5e64a2e64a46e513d248ee40501ec06cd2380168
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589585"
---
# <a name="model-inference-using-tensorflow-and-tensorrt"></a>使用 TensorFlow 和 TensorRT 进行模型推理

[NVIDIA TensorRT](https://developer.nvidia.com/tensorrt) 是一种高性能推理优化器和运行时，可为深度学习推理应用程序提供低延迟和高吞吐量。 TensorRT 安装在[启用了 GPU](../../../clusters/gpu.md) 的 [Databricks Runtime 7.0](../../../release-notes/runtime/7.0.md) 及更高版本中。

以下笔记本演示了 Azure Databricks 建议的[深度学习推理工作流](dl-model-inference.md)。 此示例演示如何使用 TensorRT 优化训练后的 ResNet-50 模型以进行模型推理。

## <a name="model-inference-tensorflow-tensorrt-notebook"></a>模型推理 Tensorflow-TensorRT 笔记本

[获取笔记本](../../../_static/notebooks/deep-learning/tensorflow-tensorrt.html)