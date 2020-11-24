---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/17/2020
title: 在 Azure Databricks 上构建机器学习模型的端到端示例 - Azure Databricks
description: 在 Azure Databricks 上训练机器学习模型的端到端示例。
ms.openlocfilehash: eb65fe2b6b135da5891b24fc805cfbed9b1472b4
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589490"
---
# <a name="end-to-end-example-of-building-machine-learning-models-on-azure-databricks"></a><a id="end-to-end-example-of-building-machine-learning-models-on-azure-databricks"> </a><a id="mlflow-end-to-end-example"> </a>在 Azure Databricks 上构建机器学习模型的端到端示例

现实世界中的机器学习非常混乱。 数据源包含缺少的值、包含冗余行或可能无法放入内存中。 特征工程通常需要特定领域的专业知识并且可能很乏味。 建模也常常混合了数据科学和系统工程，不仅需要了解算法，还需要了解计算机体系结构和分布式系统。

Azure Databricks 简化了此过程。 下面的 10 分钟教程笔记本演示了针对表格数据训练机器学习模型的端到端示例。
你可以导入此笔记本并自己运行，也可以复制代码片段和想法供自己使用。

## <a name="requirements"></a>要求

此笔记本需要 Databricks Runtime 6.5 ML 或更高版本。

## <a name="notebook"></a>笔记本

### <a name="mlflow-end-to-end-example-notebook"></a>MLflow 端到端示例笔记本

[获取笔记本](../../_static/notebooks/mlflow/mlflow-end-to-end-example-azure.html)