---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/18/2020
title: 使用 MLlib 进行特征工程处理 - Azure Databricks
description: 在 Databricks 上使用 MLlib 进行特征工程处理的示例。
ms.openlocfilehash: f8702406f3ab6e628f5a28b53faef5d20af2be5c
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589985"
---
# <a name="feature-engineering-with-mllib"></a>使用 MLlib 进行特征工程处理

Apache Spark MLlib 包含许多用于大规模执行特征工程处理的实用函数，包括对特征编码和转换特征的方法。 这些方法还可用于处理其他机器学习库的特征。

Azure Databricks 建议使用以下 Apache Spark MLLib 指南：

* [通过 MLlib 提取、转换和选择特征](https://spark.apache.org/docs/latest/ml-features)
* [MLlib 编程指南](https://spark.apache.org/docs/latest/ml-guide.html)
* [Python API 参考](https://spark.apache.org/docs/latest/api/python/pyspark.ml.html)
* [Scala API 参考](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.ml.package)

这个基于 PySpark 的笔记本包含预处理步骤，这些步骤使用类别索引和独热编码将分类数据转换为数值变量。

## <a name="binary-classification-example"></a>二元分类示例

[获取笔记本](../../../_static/notebooks/binary-classification.html)