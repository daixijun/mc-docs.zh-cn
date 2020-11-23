---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/22/2020
title: Python 快速入门 - Azure Databricks
description: 了解使用 Python 版 MLflow 跟踪机器学习训练运行的基础知识。
ms.openlocfilehash: 215d79704a5109a71fc654e8b4252fd82c384bed
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589661"
---
# <a name="quick-start-python"></a>Python 快速入门

[MLflow](https://www.mlflow.org/) 是用于管理端到端机器学习生命周期的开源平台。 MLflow 提供简单的 API 来记录指标（例如模型丢失）、参数（例如学习速率）和拟合模型，以便简化随后的训练结果分析或模型部署。

## <a name="in-this-section"></a>本部分内容：

* [安装 MLflow](#install-mlflow)
* [自动将训练运行记录到 MLflow](#automatically-log-training-runs-to-mlflow)
* [查看结果](#view-results)
* [跟踪其他指标、参数和模型](#track-additional-metrics-parameters-and-models)
* [示例笔记本](#example-notebooks)
* [了解详细信息](#learn-more)

## <a name="install-mlflow"></a>安装 MLflow

如果使用的是[用于机器学习的 Databricks Runtime](../../runtime/mlruntime.md)，则已经安装 MLflow。
否则，请[从 PyPI 安装 MLflow 包](../../libraries/cluster-libraries.md)。

## <a name="automatically-log-training-runs-to-mlflow"></a>自动将训练运行记录到 MLflow

MLflow 提供 `mlflow.<framework>.autolog()` API 来自动记录采用多种 ML 框架编写的训练代码。
你可以先调用此 API，然后再运行训练代码以记录特定于模型的指标、参数和模型项目。

### <a name="tensorflow"></a>Tensorflow

```python
# Also autoinstruments tf.keras
import mlflow.tensorflow
mlflow.tensorflow.autolog()
```

### <a name="keras"></a>Keras

```python
# Use import mlflow.tensorflow and mlflow.tensorflow.autolog() if using tf.keras
import mlflow.keras
mlflow.keras.autolog()
```

### <a name="xgboost"></a>Xgboost

```python
import mlflow.xgboost
mlflow.xgboost.autolog()
```

### <a name="lightgbm"></a>Lightgbm

```python
import mlflow.lightgbm
mlflow.lightgbm.autolog()
```

### <a name="scikit-learn"></a>Scikit-learn

```python
import mlflow.sklearn
mlflow.sklearn.autolog()
```

### <a name="pyspark"></a>Pyspark

如果使用 `pyspark.ml` 执行优化，则系统会自动将指标和模型记录到 MLflow 中。
请参阅 [Apache Spark MLlib 和自动化 MLflow 跟踪](../machine-learning/automl-hyperparam-tuning/mllib-mlflow-integration.md)

## <a name="view-results"></a>查看结果

执行机器学习代码后，你可以在“试验运行”边栏中查看结果：

1. 单击笔记本上下文栏中的“试验”图标![试验](../../_static/images/access-control/experiment.png)。 随即显示“试验运行”边栏。 在边栏中，你可以查看运行的参数和指标：

   > [!div class="mx-imgBorder"]
   > ![本垒打次数](../../_static/images/mlflow/mlflow-notebook-experiments-python.gif)

2. 单击“外部链接”图标 ![外部链接](../../_static/images/external-link.png) （位于“试验运行”上下文栏中）以查看试验：

   > [!div class="mx-imgBorder"]
   > ![查看试验](../../_static/images/mlflow/quick-start-nb-experiment.png)

3. 在试验中，单击一个日期：

   > [!div class="mx-imgBorder"]
   > ![选择“运行”](../../_static/images/mlflow/quick-start-run-date.png)

   随即显示运行详细信息：

   > [!div class="mx-imgBorder"]
   > ![运行详细信息](../../_static/images/mlflow/quick-start-nb-run.png)

4. 在试验中，单击一个源：

   > [!div class="mx-imgBorder"]
   > ![试验源](../../_static/images/mlflow/quick-start-run-source.png)

   随即显示运行中使用的笔记本修订版：

   > [!div class="mx-imgBorder"]
   > ![笔记本修订版](../../_static/images/mlflow/quick-start-run-revision.png)

## <a name="track-additional-metrics-parameters-and-models"></a>跟踪其他指标、参数和模型

你可以通过直接调用 [MLflow 跟踪日志记录 API](https://www.mlflow.org/docs/latest/tracking.html#logging-functions) 来记录其他信息。

* 数值指标：

  ```python
  import mlflow
  mlflow.log_metric("accuracy", 0.9)
  ```

* 训练参数：

  ```python
  import mlflow
  mlflow.log_param("learning_rate", 0.001)
  ```

* 模型：

  ### <a name="scikit-learn"></a>Scikit-learn

  ```python
  import mlflow.sklearn
  mlflow.sklearn.log_model(model, "myModel")
  ```

  ### <a name="pyspark"></a>Pyspark

  ```python
  import mlflow.spark
  mlflow.spark.log_model(model, "myModel")
  ```

  ### <a name="xgboost"></a>Xgboost

  ```python
  import mlflow.xgboost
  mlflow.xgboost.log_model(model, "myModel")
  ```

  ### <a name="tensorflow"></a>Tensorflow

  ```python
  import mlflow.keras
  mlflow.keras.log_model(model, "myModel")
  ```

  ### <a name="keras"></a>Keras

  ```python
  import mlflow.keras
  mlflow.keras.log_model(model, "myModel")
  ```

  ### <a name="pytorch"></a>PyTorch

  ```python
  import mlflow.pytorch
  mlflow.pytorch.log_model(model, "myModel")
  ```

  ### <a name="spacy"></a>Spacy

  ```python
  import mlflow.spacy
  mlflow.spacy.log_model(model, "myModel")
  ```

* 其他项目（文件）：

  ```python
  import mlflow
  mlflow.log_artifact("/tmp/my-file", "myArtifactPath")
  ```

## <a name="example-notebooks"></a>示例笔记本

### <a name="requirements"></a>要求

Databricks Runtime 6.4 或更高版本或者 Databricks Runtime 6.4 ML 或更高版本。

### <a name="notebooks"></a>笔记本

要通过 Python 开始使用 MLflow 跟踪，推荐的方法是使用 MLflow `autolog()` API。 使用 MLflow 的 autologging 功能，一行代码会自动记录生成的模型、用于创建模型的参数和模型分数。 以下笔记本演示了如何使用 autologging 设置运行。

#### <a name="mlflow-autologging-quick-start-python-notebook"></a>MLflow Autologging 快速入门 Python 笔记本

[获取笔记本](../../_static/notebooks/mlflow/mlflow-quick-start-python.html)

如果需要更好地控制为每次训练运行记录的指标，或想要记录其他项目（如表或绘图），可以使用以下笔记本中演示的 MLflow 日志记录 API 函数。

#### <a name="mlflow-logging-api-quick-start-python-notebook"></a>MLflow 日志记录 API 快速入门 Python 笔记本

[获取笔记本](../../_static/notebooks/mlflow/mlflow-logging-api-quick-start-python.html)

## <a name="learn-more"></a>了解详细信息

* [MLflow 概述](index.md)
* [跟踪机器学习训练运行](tracking.md)
* [在 Azure Databricks 上运行 MLflow 项目](projects.md)
* [记录、加载和部署 MLflow 模型](models.md)