---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/10/2020
title: 深度学习管道 - Azure Databricks
description: 了解如何使用深度学习管道来训练机器学习模型。
ms.openlocfilehash: a7e4337cbeeda7fafb632377ecd060272e7429d7
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590002"
---
# <a name="deep-learning-pipelines"></a><a id="deep-learning-pipelines"> </a><a id="sparkdl"> </a>深度学习管道

> [!NOTE]
>
> 本页描述了 Databricks Runtime 6.6 ML 及更低版本中包含的开源[深度学习管道包](https://github.com/databricks/spark-deep-learning/tree/v1.6.0)。 本页不用作有关 Azure Databricks 上的深度学习管道的常规信息资源。

深度学习管道包是高级深度学习框架，可通过 Apache Spark MLlib 管道 API 促进常见的深度学习工作流，并使用 Spark 横向扩展大数据深度学习。 它是一个使用 [Apache 2.0 许可证](https://github.com/databricks/spark-deep-learning/blob/master/LICENSE)的开源项目。

深度学习管道包调用较低级别的深度学习库。 它支持 TensorFlow 和具有 TensorFlow 后端的 Keras。

## <a name="migration-guide-to-databricks-runtime-70-ml-and-above"></a>迁移到 Databricks Runtime 7.0 ML 及更高版本的指南

> [!IMPORTANT]
>
> [Databricks Runtime 7.0 ML](../../../release-notes/runtime/7.0ml.md) 中删除了深度学习管道库 `sparkdl` 的某些部分，具体来说，就是 Apache Spark ML 管道中使用的“转换器”和“估算器”。 请参阅以下部分，获取迁移提示和解决方法。

### <a name="reading-images"></a>读取图像

深度学习管道包包含图像读取器 `sparkdl.image.imageIO`，[Databricks Runtime 7.0 ML](../../../release-notes/runtime/7.0ml.md) 中已删除该读取器。

请改用 Apache Spark 中的[图像数据源](../../../data/data-sources/image.md)或[二进制文件数据源](../../../data/data-sources/binary-file.md)。  [加载数据](../load-data/index.md)中的许多示例笔记本显示了这两个数据源的用例。

### <a name="transfer-learning"></a>迁移学习

深度学习管道包包含 Spark ML 转换器 `sparkdl.DeepImageFeaturizer`，可促进深度学习模型中的迁移学习。  [Databricks Runtime 7.0 ML](../../../release-notes/runtime/7.0ml.md) 中已经删除了 `DeepImageFeaturizer`。

请改用 pandas UDF 在深度学习模型中执行特征化。  [pandas UDF](../../../spark/latest/spark-sql/udf-python-pandas.md#pandas-udf) 及其更新的变体 [Scalar Iterator pandas UDF](../../../spark/latest/spark-sql/udf-python-pandas.md#scalar-iterator-udfs) 可提供更灵活的 API，支持更多深度学习库并提供更高的性能。

有关使用 pandas UDF 进行迁移学习的示例，请参阅[用于迁移学习的特征化](../preprocess-data/transfer-learning-tensorflow.md)。

### <a name="distributed-hyperparameter-tuning"></a>分布式超参数优化

深度学习管道包包含 Spark ML 估算器 `sparkdl.KerasImageFileEstimator`，可用于通过 Spark ML 优化实用程序优化超参数。  [Databricks Runtime 7.0 ML](../../../release-notes/runtime/7.0ml.md) 中已经删除了 `KerasImageFileEstimator`。

请改用[使用 Hyperopt 进行超参数优化](../automl-hyperparam-tuning/index.md#hyperopt-overview)，为深度学习模型分发超参数优化。

### <a name="distributed-inference"></a>分布式推理

深度学习管道包包含多个用于分发推理的 Spark ML 转换器，[Databricks Runtime 7.0 ML](../../../release-notes/runtime/7.0ml.md) 中已删除了这些转换器：

* `DeepImagePredictor`
* `TFImageTransformer`
* `KerasImageFileTransformer`
* `TFTransformer`
* `KerasTransformer`

请按照[模型推理](../model-inference/index.md)中的示例，改用 [pandas UDF](../../../spark/latest/spark-sql/udf-python-pandas.md#pandas-udf) 在 Spark 数据帧上运行推理。

### <a name="deploy-models-as-sql-udfs"></a>将模型部署为 SQL UDF

深度学习管道包包含实用程序 `sparkdl.udf.keras_image_model.registerKerasImageUDF`，可用于将深度学习模型部署为可从 Spark SQL 调用的 UDF。 [Databricks Runtime 7.0 ML](../../../release-notes/runtime/7.0ml.md) 中已经删除了 `registerKerasImageUDF`。

请按照 [Azure ML 上的 scikit-learn 模型部署](../../mlflow/model-example.md#model-inference)中的示例，改用 [MLflow](../../mlflow/index.md) 将模型导出为 UDF。