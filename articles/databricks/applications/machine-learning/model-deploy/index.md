---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 10/01/2020
title: 部署和提供模型 - Azure Databricks
description: 学习在 Azure Databricks 中部署和提供模型。
ms.openlocfilehash: b291e0fd8efa6c749421d8bb0c7c7f438fefe5d8
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589474"
---
# <a name="deploy-and-serve-models"></a>部署和提供模型

## <a name="deploy-models-to-production-with-mlflow"></a>通过 MLflow 将模型部署到生产环境

有关通过 MLflow 部署模型的信息，请参阅[记录、加载和部署 MLflow 模型](../../mlflow/models.md)。 以下笔记本演示了如何使用 MLflow 模型注册表来生成、管理和部署模型。

[MLflow 模型注册表示例](../../mlflow/model-registry-example.md)

## <a name="serve-models-with-mlflow"></a>使用 MLflow 提供模型

Azure Databricks 提供 [MLflow 模型提供](../../mlflow/model-serving.md)功能，使你能够将模型注册表中的机器学习模型作为 REST 终结点（它们基于模型版本的可用性和阶段自动更新）托管。 MLflow 模型提供功能可用于 Python MLflow 模型。

## <a name="run-a-azure-databricks-job"></a>运行 Azure Databricks 作业

你可以创建 Azure Databricks [作业](../../../jobs.md)，以立即或按计划运行笔记本或 JAR。