---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 01/23/2020
title: Azure ML 上的 scikit-learn 模型部署 - Azure Databricks
description: 了解如何在 Azure ML 上部署 scikit-learn 模型部署。
ms.openlocfilehash: 47d39244ff21590f03f0fec36f83fca90b78adc7
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589794"
---
# <a name="scikit-learn-model-deployment-on-azure-ml"></a><a id="deployment-quickstart"></a><a id="scikit-learn-model-deployment-on-azure-ml"></a>Azure ML 上的 scikit learn 模型部署

本笔记本使用[训练 scikit-learn 模型并以 scikit-learn 格式保存](tracking-ex-scikit.md#training-quickstart)中所述的根据糖尿病数据集训练的 ElasticNet 模型。 本笔记本介绍如何完成下列操作：

* 使用 MLflow 试验 UI 选择要部署的模型
* 使用 MLflow API 将模型部署到 Azure ML
* 查询已部署的模型
* 对另一个模型重复部署和查询过程
* 使用 MLflow API 删除部署

## <a name="mlflow-scikit-learn-model-deployment-on-azure-notebook"></a>Azure 笔记本上的 MLflow scikit-learn 模型部署

[获取笔记本](../../_static/notebooks/mlflow/mlflow-quick-start-deployment-azure.html)