---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/04/2020
title: 模型推理示例 - Azure Databricks
description: 了解如何使用机器学习模型进行推理。
ms.openlocfilehash: 38f966b4320ffc7abec6bb93a2f1cf39f71115da
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589475"
---
# <a name="model-inference-example"></a>模型推理示例

## <a name="model-inference"></a><a id="inference-quickstart"> </a><a id="model-inference"> </a>模型推理

本笔记本使用[训练 scikit-learn 模型并以 scikit-learn 格式保存](tracking-ex-scikit.md#training-quickstart)中所述的根据糖尿病数据集训练的 ElasticNet 模型。 本笔记本介绍如何完成下列操作：

* 使用 MLflow 试验 UI 选择要部署的模型
* 将训练后的模型加载为 scikit-learn 模型
* 将模型导出为 PySpark UDF
* 应用 UDF 以将预测列添加到数据帧

### <a name="mlflow-inference-notebook"></a>MLflow 推理笔记本

[获取笔记本](../../_static/notebooks/mlflow/mlflow-quick-start-inference.html)

## <a name="scikit-learn-model-deployment-on-azure-ml"></a><a id="deployment-quickstart"> </a><a id="scikit-learn-model-deployment-on-azure-ml"> </a>Azure ML 上的 scikit learn 模型部署

本笔记本使用[训练 scikit-learn 模型并以 scikit-learn 格式保存](tracking-ex-scikit.md#training-quickstart)中所述的根据糖尿病数据集训练的 ElasticNet 模型。 本笔记本介绍如何完成下列操作：

* 使用 MLflow 试验 UI 选择要部署的模型
* 使用 MLflow API 将模型部署到 Azure ML
* 查询已部署的模型
* 对另一个模型重复部署和查询过程
* 使用 MLflow API 删除部署

### <a name="mlflow-scikit-learn-model-deployment-on-azure-notebook"></a>Azure 笔记本上的 MLflow scikit-learn 模型部署

[获取笔记本](../../_static/notebooks/mlflow/mlflow-quick-start-deployment-azure.html)