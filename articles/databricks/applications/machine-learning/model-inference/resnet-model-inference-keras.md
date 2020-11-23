---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 使用 TensorFlow Keras API 进行模型推理 - Azure Databricks
description: 了解如何使用 Keras 进行模型推理。
ms.openlocfilehash: 8c0f1979eea7f5e21603d7653161c3c1662c0bfc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589580"
---
# <a name="model-inference-using-tensorflow-keras-api"></a>使用 TensorFlow Keras API 进行模型推理

以下笔记本演示了 Azure Databricks 建议的[深度学习推理工作流](dl-model-inference.md)。 本示例演示了使用 ResNet-50 模型进行的模型推理，该模型将 TensorFlow Keras API 和 Parquet 文件作为输入数据进行训练。 要理解该示例，应熟悉 [Spark数据源](../../../data/data-sources/index.md#data-sources)。

## <a name="model-inference-tensorflow-keras-api-notebook"></a>模型推理 TensorFlow Keras API 笔记本

[获取笔记本](../../../_static/notebooks/deep-learning/keras-metadata.html)