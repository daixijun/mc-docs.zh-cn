---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何使用 Apache Spark 执行组 K-折交叉验证 - Azure Databricks
description: 了解如何在 Azure Databricks 上使用 Apache Spark 执行组 K-折交叉验证。
ms.openlocfilehash: 7a9c51125aceef05ef642fe7f9af8b31d1a78825
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589724"
---
# <a name="how-to-perform-group-k-fold-cross-validation-with-apache-spark"></a>如何使用 Apache Spark 执行组 K-折交叉验证

交叉验证会将训练数据随机拆分为指定数量的折叠。 为了防止相同数据在多个折叠中出现时发生数据泄漏，可以使用组。 `scikit-learn` 支持组 [K-折交叉验证](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GroupKFold.html)，以确保折叠不同且不重叠。

在 Spark 上，你可以使用 `spark-sklearn` 库（分发 [scikit-learn 模型](https://databricks.com/blog/2016/02/08/auto-scaling-scikit-learn-with-apache-spark.html)的优化功能）来利用此方法。 此示例通过 `grp` 变量在 Spark 上使用组 K-折方法优化 scikit-learn 随机林模型：

```python
from sklearn.ensemble import RandomForestClassifier
from spark_sklearn import GridSearchCV
from sklearn.model_selection import GroupKFold
param_grid = {"max_depth": [8, 12, None],
              "max_features": [1, 3, 10],
              "min_samples_split": [1, 3, 10],
              "min_samples_leaf": [1, 3, 10],
              "bootstrap": [True, False],
              "criterion": ["gini", "entropy"],
              "n_estimators": [20, 40, 80]}
group_kfold = GroupKFold(n_splits=3)
gs = GridSearchCV(sc, estimator = RandomForestClassifier(random_state=42), param_grid=param_grid, cv = group_kfold)
gs.fit(X1, y1 ,grp)
```

> [!NOTE]
>
> * 用于运行网格搜索的库称为 `spark-sklearn`，因此必须首先传入 Spark 上下文（`sc` 参数）。
> * `X1` 和 `y1` 参数必须是 pandas 数据帧。 此网格搜索选项只对适合驱动程序的数据有效。