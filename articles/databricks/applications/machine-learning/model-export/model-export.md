---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/11/2020
title: 导出 Apache Spark ML 模型和管道 - Azure Databricks
description: 了解如何使用 Databricks ML 模型导出来导出机器学习模型。
ms.openlocfilehash: 5f1a3a7d04c836a3119820933acd76f48557dbfb
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589927"
---
# <a name="export-apache-spark-ml-models-and-pipelines"></a><a id="export-apache-spark-ml-models-and-pipelines"> </a><a id="model-export"> </a>导出 Apache Spark ML 模型和管道

本文讨论 [Databricks ML 模型导出](model-export-import.md#model-export-import)工作流的导出部分；有关工作流的导入和评分部分，请参阅[将模型导入应用程序](model-import.md)。

使用 Databricks ML 模型导出可轻松导出训练后的 Apache Spark ML 模型和管道。

```scala
import com.databricks.ml.local.ModelExport
val lr = new LogisticRegression()
val model = lr.fit(trainingData)
// Export the model into provided directory
ModelExport.exportModel(lrModel, "<storage-location>")
```

有关详细的代码示例，请参阅[示例笔记本](#model-export-notebooks)。

## <a name="model-export-format"></a>模型导出格式

MLlib 模型以 JSON 文件形式导出，且格式与 [Spark ML 暂留格式](https://spark.apache.org/docs/latest/ml-pipeline.html#ml-persistence-saving-and-loading-pipelines)匹配。
MLlib 格式的主要变化如下：

* 使用 JSON 代替 Parquet
* 添加其他元数据

模型导出格式具有多个优点：

**JSON**

简单易读的格式，可签入版本控制系统

匹配的 MLlib 格式

模型导出与 MLlib 标准和 API 同步

**计分**

Azure Databricks 中的其他元数据允许在 Spark 之外进行评分

例如，导出逻辑回归模型将生成包含以下 JSON 文件的目录：

* `metadata`，其中包含模型的类型及其训练配置。
  该文件与 MLlib 的 `metadata` 文件匹配。

  ```json
  {
     "class":"org.apache.spark.ml.classification.LogisticRegressionModel",
     "paramMap":{
     "featuresCol":"features",
     "predictionCol":"prediction",
     "aggregationDepth":2,
     "elasticNetParam":0.0,
     "family":"auto",
     "fitIntercept":true,
     "maxIter":100,
     "regParam":0.0,
     "standardization":true,
     "threshold":0.5,
     "tol":1.0E-6
   },
   "sparkVersion":"2.1.0",
   "timestamp":1488858051051,
   "uid":"logreg_a99aee74cfef"}
  ```

* `data`，其中包含训练后的模型参数。
  该文件与 MLlib 的 `data` 文件匹配。 `type` 是 MLlib 向量格式：`0` 表示稀疏向量，`1` 表示密集向量。

  ```json
  {
    "numClasses":2,
    "numFeatures":13,
    "interceptVector": {
      "type":1,
      "values":[ -8.44645260967139 ]
   },
   "coefficientMatrix":{
      "type":1,
      "numRows":1,
      "numCols":4,
      "values":[ -0.01747691176982096, 1.542111173068903, 0.700895509427004, 0.025215711086829903 ],
      "isTransposed":true
   },
   "isMultinomial":false}
  ```

* `dbmlMetadata`，其中包含特定于 Databricks ML 模型导出的其他信息。

```json
{
  "dbmlExportLibraryVersion":"0.1.1"
}
```

## <a name="supported-models"></a>支持的模型

可以通过调用 `ModelExport.supportedModels` 以编程方式检索一系列支持的模型。 支持以下模型：

* 管道 - 可以导出完整的 ML 管道（包含支持的转换器和模型）。 这些管道必须是拟合后的（训练后的）`PipelineModels`，其中包含 Spark ML `Transformers` 和 `Models`，不包含任何 `Estimators`。
* **模型**
  * [决策树分类器](https://spark.apache.org/docs/latest/ml-classification-regression.html#decision-tree-classifier)
  * [决策树回归](https://spark.apache.org/docs/latest/ml-classification-regression.html#decision-tree-regression)
  * [逻辑回归](https://spark.apache.org/docs/latest/ml-classification-regression.html#logistic-regression)
  * [随机林分类器](https://spark.apache.org/docs/latest/ml-classification-regression.html#random-forest-classifier)
  * [随机林回归](https://spark.apache.org/docs/latest/ml-classification-regression.html#random-forest-regression)

> [!NOTE]
>
> 概率分类器（决策树分类器、逻辑回归、随机林分类器等）可以输出包含类概率向量的附加 `probability` 字段。

* **转换器**
  * [Bucketizer](https://spark.apache.org/docs/latest/ml-features.html#bucketizer)
  * [HashingTF](https://spark.apache.org/docs/latest/ml-features.html#tf-idf)
  * [OneHotEncoder](https://spark.apache.org/docs/latest/ml-features.html#onehotencoder-deprecated-since-230) - 无法单独导出。 必须将其作为包含 `Bucketizer` 或 `StringIndexer` 的管道的一部分导出。
  * [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer)
  * [分词器](https://spark.apache.org/docs/latest/ml-features.html#tokenizer)
  * [VectorAssembler](https://spark.apache.org/docs/latest/ml-features.html#vectorassembler)
  * [VectorSlicer](https://spark.apache.org/docs/latest/ml-features.html#vectorslicer)

## <a name="exporting-models-from-databricks"></a><a id="exporting-models-from-databricks"> </a><a id="model-export-notebooks"> </a>从 Databricks 导出模型

以下笔记本演示如何从 Azure Databricks 导出 ML 模型。

### <a name="model-export-scala-notebook"></a>模型导出 Scala 笔记本

[获取笔记本](../../../_static/notebooks/model-export-scala.html)

### <a name="model-export-python-notebook"></a>模型导出 Python 笔记本

[获取笔记本](../../../_static/notebooks/model-export-python.html)