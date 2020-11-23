---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何使用 gapply 将 R 代码并行化 - Azure Databricks
description: 了解如何使用 gapply 将 R 代码并行化。
ms.openlocfilehash: a63a5fb35bf4b3c44cd0f16f1e862f4eed68c9d7
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589509"
---
# <a name="how-to-parallelize-r-code-with-gapply"></a>如何使用 `gapply` 将 R 代码并行化

将 R 代码并行化比较困难，因为 R 代码在驱动程序上运行，并且 R data.frame 不是分布式的。 通常有在本地运行的现有 R 代码和转换为在 Apache Spark 上运行的现有 R 代码。 在其他情况下，一些用于高级统计分析和机器学习技术的 SparkR 函数可能不支持分布式计算。 在这些情况下，可以使用 SparkR UDF API 在群集中分布所需的工作负载。

示例用例：你希望针对数据集的子集（按某个键分组）训练机器学习模型。 如果数据的子集适合于辅助角色，则使用 SparkR UDF API 一次训练多个模型可能会更有效。

`gapply` 和 `gapplyCollect` 函数将函数应用到 Spark 数据帧中的每个组。 对于 Spark 数据帧中的每个组：

1. 收集每个组作为 R data.frame。
2. 将函数发送到辅助角色并执行。
3. 将结果返回到由架构指定的驱动程序。

> [!NOTE]
>
> 调用 `gapply` 时，必须指定输出架构。 借助 `gapplyCollect`，将会为输出使用 R data.frame，从而将结果收集到驱动程序。

在下面的示例中，单独的支持向量机模型已与每个月的 `airquality` 数据拟合。 输出是一个 data.frame，其中包含为每个月生成的 MSE（在指定和未指定架构的情况下显示）。

```r
df <- createDataFrame(na.omit(airquality))

schema <- structType(
  structField("Month", "MSE"),
  structField("integer", "Number"))

result <- gapply(df, c("Month"), function(key, x) {
  library(e1071)
  data.frame(month = key, mse = svm(Ozone ~ ., x, cross = 3)$tot.MSE)
}, schema)
```

```r
df <- createDataFrame(na.omit(airquality))

gapplyCollect(df, c("Month"), function(key, x) {
  library(e1071)
  y <- data.frame(month = key, mse = svm(Ozone ~ ., x, cross = 3)$tot.MSE)
 names(y) <- c("Month", "MSE")
  y
})
```

> [!NOTE]
>
> 从一个 Spark 数据帧开始，在所有辅助角色上安装包。