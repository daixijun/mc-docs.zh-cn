---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/18/2020
title: 在 Azure Databricks 上使用 Apache Spark MLlib - Azure Databricks
description: 了解 Azure Databricks 中的 Apache Spark MLlib。
ms.openlocfilehash: 8f1f8f03a2aac0d7d80576b1172090bf105e61c2
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589588"
---
# <a name="use-apache-spark-mllib-on-azure-databricks"></a>在 Azure Databricks 上使用 Apache Spark MLlib

Apache Spark MLlib 是 Apache Spark 机器学习库，由常见学习算法和实用程序（包括分类、回归、聚集、协作筛选、维数约简以及底层优化基元）组成。 Azure Databricks 建议使用以下 Apache Spark MLLib 指南：

* [MLlib 编程指南](https://spark.apache.org/docs/latest/ml-guide.html)
* [Python API 参考](https://spark.apache.org/docs/latest/api/python/pyspark.ml.html)
* [Scala API 参考](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.ml.package)

## <a name="example-notebooks"></a>示例笔记本

以下笔记本演示如何通过 Azure Databricks 使用各种 Apache Spark MLlib 功能。

### <a name="in-this-section"></a>本部分内容：

* [二元分类示例](#binary-classification-example)
* [决策树示例](#decision-trees-examples)
* [Apache Spark MLib 管道和结构化流式处理示例](#apache-spark-mllib-pipelines-and-structured-streaming-example)
* [高级 Apache Spark MLlib 示例](#advanced-apache-spark-mllib-example)

### <a name="binary-classification-example"></a>二元分类示例

此笔记本演示如何使用 Apache Spark MLlib 管道 API 生成二进制分类应用程序。

#### <a name="binary-classification-notebook"></a>二进制分类笔记本

[获取笔记本](../../../../_static/notebooks/binary-classification.html)

### <a name="decision-trees-examples"></a>决策树示例

这些示例演示了使用 Apache Spark 管道 API 的决策树的各种应用程序。

#### <a name="decision-trees"></a>决策树

这些笔记本演示如何在决策树中执行分类。

##### <a name="decision-trees-for-digit-recognition-notebook"></a>数字识别笔记本的决策树

[获取笔记本](../../../../_static/notebooks/decision-trees.html)

##### <a name="decision-trees-for-sfo-survey-notebook"></a>SFO 调查笔记本的决策树

[获取笔记本](../../../../_static/notebooks/decision-trees-sfo-airport-survey-example.html)

#### <a name="gbt-regression-using-mllib-pipelines"></a>使用 MLlib 管道的 GBT 回归

此笔记本演示如何使用 MLlib 管道通过渐变提升树来执行回归，从而根据一周中的某一天、天气、季节等信息预测自行车租金计费（每小时）。

##### <a name="bike-sharing-regression-notebook"></a>自行车共享回归笔记本

[获取笔记本](../../../../_static/notebooks/gbt-regression.html)

### <a name="apache-spark-mllib-pipelines-and-structured-streaming-example"></a><a id="apache-spark-mllib-pipelines-and-structured-streaming-example"> </a><a id="mllib-pipelines-and-structured-streaming-example"> </a>Apache Spark MLib 管道和结构化流式处理示例

此笔记本演示如何针对历史数据训练 Apache Spark MLlib 管道并将其应用于流式处理数据。

#### <a name="mllib-pipeline-structured-streaming-notebook"></a>MLlib 管道结构化流式处理笔记本

[获取笔记本](../../../../_static/notebooks/using-mllib-with-structured-streaming.html)

### <a name="advanced-apache-spark-mllib-example"></a>高级 Apache Spark MLlib 示例

此笔记本说明如何创建自定义转换器。

#### <a name="custom-transformer-notebook"></a>自定义转换器笔记本

[获取笔记本](../../../../_static/notebooks/flat-map-transformer-example.html)

对于 MLlib 功能的参考信息，Azure Databricks 建议使用以下 Apache Spark API 参考：

* [Python API](https://spark.apache.org/docs/latest/api/python/pyspark.ml.html)
* [Scala API](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.ml.package)
* [Java API](https://spark.apache.org/docs/latest/api/java/org/apache/spark/ml/package-summary.html)

若要使用 R 中的 Apache Spark MLlib，请参阅 [R 机器学习](../../../../spark/latest/sparkr/overview.md#r-ml)文档。

若要了解 Azure Databricks 对机器学习算法可视化的支持，请参阅[机器学习可视化效果](../../../../notebooks/visualizations/index.md#display-ml-models)。