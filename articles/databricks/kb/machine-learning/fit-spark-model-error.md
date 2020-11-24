---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 拟合 Apache SparkML 模型时引发错误 - Azure Databricks
description: 了解在拟合 SparkML 模型或管道时如何解决 Azure Databricks 引发的错误。
ms.openlocfilehash: 5efb8963a986c3767a9cd8fb23b63c821b8a2410
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589734"
---
# <a name="fitting-an-apache-sparkml-model-throws-error"></a>拟合 Apache SparkML 模型时引发错误

## <a name="problem"></a>问题

拟合 SparkML 模型或管道时，Azure Databricks 引发错误：

```console
org.apache.spark.SparkException: Job aborted due to stage failure: Task 0 in stage 162.0 failed 4 times, most recent failure: Lost task 0.3 in stage 162.0 (TID 168, 10.205.250.130, executor 1): org.apache.spark.SparkException: Failed to execute user defined function($anonfun$9: (string) =&gt; double)
```

## <a name="cause"></a>原因

通常，拟合 SparkML 模型或管道时出现错误是由于训练数据存在问题。

## <a name="solution"></a>解决方案

检查是否存在以下问题：

1. 识别并处理数据集中的 NULL 值。 Spark 需要知道如何处理数据集中缺失的值。
   * 使用 [dropna()](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#pyspark.sql.DataFrame.dropna) 丢弃缺少值的行。
   * 插补一些值，例如零或列的平均值。 此解决方案取决于什么对数据集有意义。
2. 确保所有训练数据都正确地转换为数字格式。 Spark 需要知道如何处理分类变量和字符串变量。 各种[特征转换器](https://spark.apache.org/docs/latest/ml-features.html)可用于处理数据特定情况。
3. 检查[共线性](https://en.wikipedia.org/wiki/Multicollinearity)。 高度相关甚至重复的特征可能会导致模型拟合出现问题。 这种情况很少发生，但你应确保将其排除。