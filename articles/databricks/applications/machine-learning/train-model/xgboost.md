---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/11/2020
title: 在 Azure Databricks 上使用 XGBoost - Azure Databricks
description: 了解如何在 Azure Databricks 中使用 XGBoost 训练机器学习模型。
ms.openlocfilehash: 237a3d0167d80b8385e89fcc835e80376696fba2
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589485"
---
# <a name="use-xgboost-on-azure-databricks"></a>在 Azure Databricks 上使用 XGBoost

> [!WARNING]
>
> 如果使用 XGBoost 0.90 进行训练但训练作业失败，则[共享的 Spark 上下文](../../../jobs.md#shared-spark-context)将会终止，而唯一的恢复方式是重启群集。 这是 XGBoost 中的一个 [bug](https://github.com/dmlc/xgboost/issues/4826)。

## <a name="single-node-training-in-python"></a>Python 中的单节点训练

Python 包允许只训练单节点工作负荷。

### <a name="xgboost-python-notebook"></a>XGBoost Python 笔记本

[获取笔记本](../../../_static/notebooks/xgboost-python.html)

## <a name="distributed-training-in-scala"></a>Scala 中的分布式训练

若要执行分布式训练，必须使用 XGBoost 的 Scala/Java 包。 本部分中的示例演示如何将 XGBoost 与 MLlib 结合使用。 第一个示例演示如何将 XGBoost 模型嵌入 MLlib ML 管道。
第二个示例演示如何使用 MLlib 交叉验证来优化 XGBoost 模型。

### <a name="xgboost-classification-with-ml-pipeline-notebook"></a>使用 ML 管道笔记本实现 XGBoost 分类

[获取笔记本](../../../_static/notebooks/xgboost-classification.html)

### <a name="xgboost-regression-with-cross-validation-notebook"></a>使用交叉验证笔记本实现 XGBoost 回归

[获取笔记本](../../../_static/notebooks/xgboost-regression.html)