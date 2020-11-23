---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/13/2020
title: R 快速入门 - Azure Databricks
description: 了解在 R 中使用 MLflow 跟踪机器学习训练运行的基础知识。
ms.openlocfilehash: 98922952b217cf09acf71e1b7cb84b27b51850b8
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589451"
---
# <a name="quick-start-r"></a><a id="mlflow-quick-start-r"> </a><a id="quick-start-r"> </a>R 快速入门

## <a name="mlflow-quick-start-r-notebook"></a>MLflow R 快速入门笔记本

[获取笔记本](../../_static/notebooks/mlflow/mlflow-quick-start-r.html)

查看快速入门中使用的试验、运行和笔记本修订版：

1. 单击笔记本上下文栏中的“试验”图标![试验](../../_static/images/access-control/experiment.png)。 随即显示“试验运行”边栏。 在边栏中，你可以查看运行的参数和指标：

   > [!div class="mx-imgBorder"]
   > ![本垒打次数](../../_static/images/mlflow/mlflow-notebook-experiments-r.gif)

2. 单击“外部链接”图标 ![外部链接](../../_static/images/external-link.png) （位于“试验运行”上下文栏中）以查看试验：

   > [!div class="mx-imgBorder"]
   > ![查看试验](../../_static/images/mlflow/quick-start-nb-experiment.png)

3. 在试验中，单击一个日期：

   > [!div class="mx-imgBorder"]
   > ![查看运行](../../_static/images/mlflow/quick-start-run-date.png)

   随即显示运行详细信息：

   > [!div class="mx-imgBorder"]
   > ![运行详细信息](../../_static/images/mlflow/quick-start-nb-run.png)

4. 在试验中，单击一个源：

   > [!div class="mx-imgBorder"]
   > ![试验源](../../_static/images/mlflow/quick-start-run-source.png)

   随即显示运行中使用的笔记本修订版：

   > [!div class="mx-imgBorder"]
   > ![笔记本修订版](../../_static/images/mlflow/quick-start-run-revision.png)