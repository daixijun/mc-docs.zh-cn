---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/10/2020
title: MLflow - Azure Databricks
description: 了解如何管理端到端的机器学习生命周期。
ms.openlocfilehash: 27d2a1832f949ee73a0b4037b784c2cc875cb208
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589487"
---
# <a name="mlflow"></a><a id="mlflow"> </a><a id="mlflow-guide"> </a>MLflow

[MLflow](https://www.mlflow.org/) 是用于管理端到端机器学习生命周期的开源平台。 它具有以下主要组件：

* 跟踪：用于跟踪试验，以记录和比较参数与结果。
* 模型：用于通过各种 ML 库管理模型，并将其部署到各种模型服务和推理平台。
* 项目：用于将 ML 代码打包成可重用、可再现的格式，以便与其他数据科学家共享或转移到生产环境。
* 模型注册表：使你可以将模型存储集中化，以便使用版本控制和批注的功能来管理模型的完整生命周期阶段转换：从过渡到生产。
* 模型服务：可用于将 MLflow 模型以 REST 终结点的形式托管。

MLflow 支持 [Java](https://www.mlflow.org/docs/latest/java_api/index.html)、[Python](https://www.mlflow.org/docs/latest/python_api/index.html)、[R](https://www.mlflow.org/docs/latest/R-api.html) 和 [REST](../../dev-tools/api/latest/mlflow.md) API。

Azure Databricks 提供与企业安全性功能、高可用性和其他 Azure Databricks 工作区功能（例如试验和运行管理，以及笔记本修订版捕获）集成的完全托管式 MLflow 版本。 Azure Databricks 上的 MLflow 提供集成体验用于跟踪和保护机器学习模型训练运行，以及运行机器学习项目。

首次使用的用户应从该[快速入门](quick-start.md)开始，该快速入门演示了基本 MLflow 跟踪 API。 后续文章通过示例笔记本介绍每个 MLflow 组件，并描述这些组件在 Azure Databricks 中的托管方式。

* [快速入门](quick-start.md)
* [跟踪机器学习训练运行](tracking.md)
* [记录、加载和部署 MLflow 模型](models.md)
* [在 Azure Databricks 上运行 MLflow 项目](projects.md)
* [在 MLflow 模型注册表中管理 MLflow 模型的生命周期](model-registry.md)
* [Azure Databricks 上的 MLflow 模型服务](model-serving.md)
* [关于在 Azure Databricks 上构建机器学习模型的端到端示例](end-to-end-example.md)