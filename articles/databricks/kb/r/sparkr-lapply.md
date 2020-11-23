---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何使用 spark.lapply 并行处理 R 代码 - Azure Databricks
description: 了解如何使用 spark.lapply 并行处理 R 代码。
ms.openlocfilehash: 701eaf6ea9855c378c724f78c7ccb94b065f831f
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589507"
---
# <a name="how-to-parallelize-r-code-with-sparklapply"></a>如何使用 `spark.lapply` 将 R 代码并行化

并行处理 R 代码比较困难，因为 R 代码在驱动程序上运行，并且 R data.frame 不是分布式的。 通常，存在在本地运行和转换为在 Apache Spark 上运行的现有 R 代码。 在其他情况下，一些用于高级统计分析和机器学习技术的 SparkR 函数可能不支持分布式计算。 在这些情况下，可以使用 SparkR UDF API 在群集中分布所需的工作负载。

示例用例：你希望根据相同的数据（例如用于超参数优化）训练多个机器学习模型。 如果数据集适合各个辅助角色，则使用 SparkR UDF API 一次性训练多个版本的模型可能会更高效。

`spark.lapply` 函数使你能够在多个辅助角色上执行相同的任务，方法就是对元素列表运行函数。 对于列表中的每个元素：

1. 将函数发送到辅助角色。
2. 执行函数。
3. 将所有辅助角色的结果作为列表返回给驱动程序。

在下面的示例中，支持向量机模型适合具有 3 折交叉验证的 `iris` 数据集，而成本从 0.5 到 1 不等（按 0.1 递增）。 输出是一个列表，其中包含各种成本参数的模型的摘要。

```r
library(SparkR)

spark.lapply(seq(0.5, 1, by = 0.1), function(x) {
  library(e1071)
  model <- svm(Species ~ ., iris, cost = x, cross = 3)
  summary(model)
})
```

> [!NOTE]
>
> 必须在所有辅助角色上安装包。