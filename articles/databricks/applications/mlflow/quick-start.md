---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 04/28/2020
title: 快速入门 - Azure Databricks
description: 了解使用 MLflow 跟踪机器学习训练运行的基础知识。
ms.openlocfilehash: 0c68cb60a7a02e1cb5610ef7741068f789b42caa
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589583"
---
# <a name="quick-start"></a><a id="mlflow-quick-start"> </a><a id="quick-start"> </a>快速入门

[MLflow](https://www.mlflow.org/) 是用于管理端到端机器学习生命周期的开源平台。 它包括三个主要组件：跟踪、模型和项目。 通过 MLflow 的“跟踪”组件可使用 [Java](https://www.mlflow.org/docs/latest/java_api/index.html)、[Python](https://www.mlflow.org/docs/latest/python_api/index.html)、[R](https://www.mlflow.org/docs/latest/R-api.html) 和 [REST](https://www.mlflow.org/docs/latest/rest-api.html) API 来记录和查询机器模型训练会话（也称为运行）。 MLflow 运行是与机器学习模型训练过程相关的参数、指标、标记和项目的集合。

试验是 MLflow 中组织的主要单位；所有 MLflow 运行都属于试验。 每个试验都允许可视化、搜索和比较运行，以及下载运行项目或元数据以便在其他工具中进行分析。 试验在 Azure Databricks 托管的 MLflow 跟踪服务器中进行维护。

试验位于[工作区](../../workspace/index.md)文件树中。 可使用与管理其他工作区对象（如文件夹、笔记本和库）相同的工具来管理试验。

## <a name="notebooks"></a>笔记本

下面的快速入门笔记本演示了如何使用 MLflow 跟踪 API 创建并记录 MLflow 运行，以及如何使用试验 UI 来查看运行。 这些笔记本可在 Python、Scala 和 R 中使用。

Python 和 R 笔记本使用[笔记本试验](tracking.md#mlflow-notebook-experiments)。 Scala 笔记本在 `Shared` 文件夹中创建试验。

* [Python 快速入门](quick-start-python.md)
* [Java 和 Scala 快速入门](quick-start-java-scala.md)
* [R 快速入门](quick-start-r.md)