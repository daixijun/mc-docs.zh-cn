---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/04/2020
title: 使用 Delta Lake 跟踪 ML 模型训练数据 - Azure Databricks
description: 了解如何在 Delta Lake 中存储机器学习训练数据。
ms.openlocfilehash: 7a457b667965f319647d3966b9e2cf94c680f94d
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589979"
---
# <a name="track-ml-model-training-data-with-delta-lake"></a><a id="track-ml-model-training-data-with-delta-lake"> </a><a id="training-mlflow-delta"> </a>使用 Delta Lake 跟踪 ML 模型训练数据

以下笔记本演示了如何在 [Delta Lake](https://delta.io) 中使用 MLflow 来跟踪和重现 ML 模型训练所用的训练数据，以及识别 ML 模型和从特定数据集派生的运行。

## <a name="mlflow-training-data-in-delta-lake-notebook"></a>Delta Lake 笔记本中的 MLflow 训练数据

[获取笔记本](../../_static/notebooks/mlflow/mlflow-delta-training.html)