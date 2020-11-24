---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/17/2020
title: 记录、加载和部署 MLflow 模型 - Azure Databricks
description: 了解如何记录、加载和部署 MLflow 模型。
ms.openlocfilehash: 52cc1e0d73bb2ad2f3f9c842bf45016d3d77d0cc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589666"
---
# <a name="log-load-and-deploy-mlflow-models"></a><a id="log-load-and-deploy-mlflow-models"> </a><a id="mlflow-models"> </a>记录、加载和部署 MLflow 模型

MLflow [模型](https://mlflow.org/docs/latest/models.html)是一种用于将机器学习模型打包的标准格式，可在多种不同下游工具（例如 Apache Spark 上的批量推理或通过 REST API 提供实时服务）中使用。 该格式定义了一种约定，利用这种约定，能够以不同的模型[服务和推理平台](https://www.mlflow.org/docs/latest/models.html#built-in-deployment-tools)可以理解的不同[风格](https://www.mlflow.org/docs/latest/models.html#built-in-model-flavors)（python-function、pytorch、sklearn，等等）保存模型。

* 若要将模型记录到 MLflow [跟踪服务器](tracking.md)，请使用 `mlflow.<model-type>.log_model(model, ...)`。
* 若要加载模型，请使用 `mlflow.<model-type>.load_model(modelpath)`。
* 若要部署模型，请使用 `mlflow.<model-type>.deploy()`。

可以在 [MLflow 模型注册表](model-registry.md)中注册模型，该注册表是一个集中的模型存储，它提供了 UI 和一组 API 来管理 MLflow 模型的完整生命周期。

有关记录模型的示例，请参阅[跟踪机器学习训练运行示例](tracking.md#tracking-examples)；有关加载和部署模型的示例，请参阅本文中的笔记本。

模型也可以保存在本地。

* 若要在本地保存模型，请使用 `mlflow.<model-type>.save_model(model, modelpath)`。 `modelpath` 必须是 [DBFS](../../data/databricks-file-system.md#fuse) 路径。 例如，如果使用 DBFS 位置 `dbfs:/my_project_models` 来存储项目工作，则必须使用模型路径 `/dbfs/my_project_models`：

  ```python
  modelpath = "/dbfs/my_project_models/model-%f-%f" % (alpha, l1_ratio)
  mlflow.sklearn.save_model(lr, modelpath)
  ```

## <a name="examples"></a>示例

* [模型推理示例](model-example.md)
* [Azure ML 上的 scikit-learn 模型部署](scikit-learn-model-deployment-on-azure-ml.md)