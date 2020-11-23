---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: Apache Spark MLlib 和自动化 MLflow 跟踪 - Azure Databricks
description: 了解如何在执行 Apache Spark MLlib 机器学习模型优化时使用自动化 MLflow 跟踪。
ms.openlocfilehash: 632197358969126245bada26d719165e095377f3
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589943"
---
# <a name="apache-spark-mllib-and-automated-mlflow-tracking"></a>Apache Spark MLlib 和自动化 MLflow 跟踪

[MLflow](../../mlflow/index.md#mlflow-guide) 是用于管理端到端机器学习生命周期的开源平台。 MLflow 支持对 Python、R 和 Scala 中的机器学习模型优化进行跟踪。 仅适用于 Python 笔记本，[Databricks Runtime](../../../runtime/dbr.md) 和[用于机器学习的 Databricks Runtime](../../../runtime/mlruntime.md) 支持自动化 [MLflow 跟踪](../../mlflow/tracking.md#mlflow-tracking)以进行 Apache Spark MLlib 模型优化。

启用 MLlib 中的自动化 MLflow 跟踪并运行使用 `CrossValidator` 或 `TrainValidationSplit` 的优化代码时，系统会自动在 MLflow 中记录超参数和评估指标。 如果没有自动化 MLflow 跟踪，需要进行显式 API 调用来记录 MLflow。

## <a name="manage-mlflow-runs"></a>管理 MLflow 运行

`CrossValidator` 或 `TrainValidationSplit` 将优化结果记录为嵌套的 MLflow 运行：

* 主运行或父运行：`CrossValidator` 或 `TrainValidationSplit` 的信息被记录为“主”运行。 如果已有一个活动的运行，它将在这个活动运行下记录，并且不结束运行。 如果没有活动的运行，则会创建一个新的运行，在其下进行记录，并在返回之前结束该运行。
* 子运行：经过测试的每个超参数设置及其评估指标都记录为主运行下的子运行。

当调用 `fit()` 时，建议使用活动 MLflow 运行管理；也就是说，将对 `fit()` 的调用包装在“`with mlflow.start_run():`”语句中。
这样可以确保信息都记录在其自己的 MLflow“主”运行下，并且可以更轻松地将额外的标记、参数或指标记录到该运行中。

> [!NOTE]
>
> 当在同一个活动 MLflow 运行中多次调用 `fit()` 时，它会将这些多个运行记录到同一个“主”运行中。  若要解决 MLflow 参数和标记的名称冲突，可以通过附加一个 UUID 来处理有冲突的名称。

以下 Python 笔记本演示了自动化 MLflow 跟踪的实际操作。

### <a name="automated-mlflow-tracking-notebook"></a>自动化 MLflow 跟踪笔记本

[获取笔记本](../../../_static/notebooks/mllib-mlflow-integration.html)

在笔记本的最后一个单元格中执行操作后，MLflow UI 应显示：

> [!div class="mx-imgBorder"]
> ![MLlib-MLflow 演示](../../../_static/images/mlflow/mllib-mlflow-demo.png)