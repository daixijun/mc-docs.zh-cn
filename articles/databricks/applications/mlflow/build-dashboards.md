---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/04/2020
title: 使用 MLflow 搜索 API 构建仪表板 - Azure Databricks
description: 了解如何使用 MLflow 搜索 API 构建聚合指标的仪表板
ms.openlocfilehash: 6dac819ad043742282a9119da8b9d5fc270d8c4c
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589481"
---
# <a name="build-dashboards-with-the-mlflow-search-api"></a><a id="build-dashboards"> </a><a id="build-dashboards-with-the-mlflow-search-api"> </a>使用 MLflow 搜索 API 构建仪表板

可以使用 [mlflow.search_runs](https://www.mlflow.org/docs/latest/search-syntax.html#programmatically-searching-runs) API 在 MLflow 运行时拉取聚合指标，并将这些指标显示在仪表板中。 以这种方式定期审阅指标有助于深入了解进度和工作效率。 例如，你可以在多次运行和/或试验中跟踪目标指标（如收入或准确性）在一段时间内的改进。

此笔记本演示如何使用 [mlflow.search_runs](https://www.mlflow.org/docs/latest/search-syntax.html#programmatically-searching-runs) API 构建以下自定义仪表板：

> [!div class="mx-imgBorder"]
> ![搜索 API 仪表板](../../_static/images/mlflow/search_api_dashboard.png)

可以在你自己的试验中运行笔记本，也可以根据自动生成的模拟试验数据运行笔记本。

## <a name="dashboard-comparing-mlflow-runs-notebook"></a>比较 MLflow 运行笔记本的仪表板

[获取笔记本](../../_static/notebooks/mlflow/custom-dashboards-mlflow-search-api.html)