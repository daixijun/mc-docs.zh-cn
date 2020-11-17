---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 导出和导入模型 - Azure Databricks
description: 了解如何从 Azure Databricks 中的 Apache Spark 导出和导入模型以及完整的 ML 管道。
ms.openlocfilehash: 8979343d3548aaf3ec6d97088061556e670d804e
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589908"
---
# <a name="export-and-import-models"></a>导出和导入模型

若要保存模型，请使用 MLflow 函数 [log_model 和 save_model](https://www.mlflow.org/docs/latest/models.html#model-api)。 你还可使用模型的原生 API 将模型保存到 [Databricks 文件系统 (DBFS)](../../../data/databricks-file-system.md) 上。 对于 MLlib 模型，请使用 [ML 管道](https://spark.apache.org/docs/latest/ml-pipeline.html#ml-persistence-saving-and-loading-pipelines)。

若要导出模型来进行单个预测，可使用 [MLeap](https://mleap-docs.combust.ml/)，它是机器学习管道的一种常见序列化格式和执行引擎。 MLeap 支持将 Apache Spark、scikit-learn 和 TensorFlow 管道序列化处理成一个捆绑包，让你能够加载和部署已训练的模型，使用新数据作出预测。 可将导出的模型导入 Spark 和其他平台来进行评分和预测。

在 [Databricks Runtime 5.5 LTS](../../../release-notes/runtime/5.5.md) 上，你可使用 [Databricks ML 模型导出](model-export-import.md)。

* [MLeap ML 模型导出](mleap-model-export.md)
  * [在 Scala 中导出和导入模型](mleap-model-export.md#export-and-import-models-in-scala)
  * [在 Python 中导出和导入模型](mleap-model-export.md#export-and-import-models-in-python)