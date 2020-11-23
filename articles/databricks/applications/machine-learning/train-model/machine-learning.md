---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 机器学习 - Azure Databricks
description: 了解如何在 Azure Databricks 中训练机器学习模型。
ms.openlocfilehash: 9cd7bdddf0ad527875ed28c8f4676dbdc5fb4c23
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589891"
---
# <a name="machine-learning"></a>机器学习

本部分包含的示例笔记本介绍如何在 Azure Databricks 中使用最常用的包来训练模型。

## <a name="scikit-learn"></a>scikit-learn

[scikit-learn](https://scikit-learn.org/stable/index.html) 是单节点机器学习最常用的 Python 库之一。 它包含在 Databricks Runtime 和 Databricks Runtime ML 中。 请参阅 [Databricks 运行时发行说明](../../../release-notes/runtime/index.md)，了解群集运行时随附的 scikit-learn 库版本。

* [在 Azure Databricks 上使用 scikit-learn](scikit-learn.md)

## <a name="mllib"></a>MLlib

Apache Spark MLlib 是 Apache Spark 机器学习库，由常见学习算法和实用程序（包括分类、回归、聚集、协作筛选、维数约简以及底层优化基元）组成。

* [在 Azure Databricks 上使用 Apache Spark MLlib](mllib/index.md)

## <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.io/en/latest/) 是一个常用的机器学习库，专门用于训练决策树和随机林。 它包含在 Databricks Runtime ML 中。 有关在 Databricks Runtime 上安装 XGBoost 或在 Databricks Runtime ML 上安装一个自定义版本的信息，请参阅[这些说明](install-xgboost.md)。

可在单独的计算机上或以分布方式训练 XGBoost 模型。

* [在 Azure Databricks 上安装 XGBoost](install-xgboost.md)
* [在 Azure Databricks 上使用 XGBoost](xgboost.md)