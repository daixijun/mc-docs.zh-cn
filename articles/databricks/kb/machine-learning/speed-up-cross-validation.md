---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何加快交叉验证的速度 - Azure Databricks
description: 了解如何使用 Azure Databricks 提高 SparkML 中的交叉验证性能。
ms.openlocfilehash: 4d8ebb1d079a3986ac1933cccf5e74795b18c81c
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589828"
---
# <a name="how-to-speed-up-cross-validation"></a>如何加快交叉验证的速度

对 Apache SparkML 模型进行超参数优化会花费很长时间，具体取决于参数网格的大小。 可以提高 SparkML 中交叉验证步骤的性能，以加快处理速度：

* 在运行任何特征转换或建模步骤（包括交叉验证）之前缓存数据。 多次引用数据的进程将受益于缓存。 请记住对 `DataFrame` 调用一个操作以使缓存生效。
* 加大 `CrossValidator` 中的 parallelism 参数，该参数设置运行并行算法时要使用的线程数。 默认设置为 1。 有关详细信息，请参阅 [CrossValidator 文档](https://spark.apache.org/docs/latest/api/python/pyspark.ml.html#pyspark.ml.tuning.CrossValidator.parallelism)。
* 请勿将管道用作 `CrossValidator` 规范中的估算器。 在某些情况下，特征化器将与模型一起优化，此时在 `CrossValidator` 中运行整个管道是有意义的。 但是，这会对每个参数组合和折叠执行整个管道。 因此，如果只优化模型，请将模型规范设置为 `CrossValidator` 中的估算器。

> [!NOTE]
>
> 可以将 `CrossValidator` 设置为特征化器后的管道内部的最后阶段。 `CrossValidator` 确定的最佳模型是输出。