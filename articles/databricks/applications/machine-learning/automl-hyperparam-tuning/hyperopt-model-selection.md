---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 使用分布式 Hyperopt 和自动化 MLflow 跟踪进行模型搜索 - Azure Databricks
description: 了解如何在使用 Hyperopt 选择最佳机器学习模型时使用自动化 MLflow 跟踪。
ms.openlocfilehash: 480386fc05d2f377db67af1f2778719a3021c770
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589682"
---
# <a name="model-search-using-distributed-hyperopt-and-automated-mlflow-tracking"></a><a id="hyperopt-model-selection"> </a><a id="model-search-using-distributed-hyperopt-and-automated-mlflow-tracking"> </a>使用分布式 Hyperopt 和自动化 MLflow 跟踪进行模型搜索

此笔记本演示如何优化多个模型的超参数并得到一个总体最佳的模型。 它结合使用 Hyperopt 和 `SparkTrials` 在两种模型类型之间进行选择：朴素贝叶斯和支持向量机 (SVM)。 对于每种模型类型，Hyperopt 可以搜索不同的超参数集。

## <a name="model-search-using-distributed-hyperopt--automated-mlflow-tracking-notebook"></a>使用分布式 Hyperopt + 自动化 MLflow 跟踪笔记本进行模型搜索

[获取笔记本](../../../_static/notebooks/hyperopt-sklearn-model-selection.html)