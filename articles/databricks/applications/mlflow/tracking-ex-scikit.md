---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 02/11/2020
title: 训练 scikit-learn 模型并以 scikit-learn 格式保存 - Azure Databricks
description: 了解如何跟踪 scikit-learn 模型的机器学习训练。
ms.openlocfilehash: 52f497b0c641f90ba946cb1d4c4baec5e08e1627
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589909"
---
# <a name="train-a-scikit-learn-model-and-save-in-scikit-learn-format"></a><a id="train-a-scikit-learn-model-and-save-in-scikit-learn-format"> </a><a id="training-quickstart"> </a>训练 scikit-learn 模型并以 scikit-learn 格式保存

本笔记本基于 [MLflow 教程](https://www.mlflow.org/docs/latest/tutorials-and-examples/tutorial.html)。 本笔记本介绍如何完成下列操作：

* 在 Azure Databricks 群集上安装 MLflow
* 基于糖尿病数据集训练 scikit-learn ElasticNet 模型，并将训练指标、参数和模型项目记录到 Azure Databricks 托管的跟踪服务器
* 在 MLflow 试验 UI 中查看训练结果

若要了解如何在 Azure ML 上部署训练后的模型，请参阅 [Azure 上的 scikit-learn 模型部署](scikit-learn-model-deployment-on-azure-ml.md)。

## <a name="mlflow-scikit-learn-model-training-notebook"></a>MLflow scikit-learn 模型训练笔记本

[获取笔记本](../../_static/notebooks/mlflow/mlflow-quick-start-training.html)