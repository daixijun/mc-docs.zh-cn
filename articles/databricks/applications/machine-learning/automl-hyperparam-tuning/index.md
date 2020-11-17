---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/31/2020
title: 超参数优化和 AutoML - Azure Databricks
description: 了解 Azure Databricks 中的 AutoML 和超参数优化。
ms.openlocfilehash: 63eff590ae4d8eda87fd5f1e7574ddc57d386b19
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589679"
---
# <a name="hyperparameter-tuning-and-automl"></a>超参数优化和 AutoML

AutoML（自动化机器学习）是指自动完成开发机器学习模型的过程。
Databricks Runtime 机器学习整合了 [MLflow](../../mlflow/index.md#mlflow-guide) 和 Hyperopt，这两款开源工具可自动完成模型选择和超参数优化过程。

## <a name="automated-mlflow-tracking"></a>自动化 MLflow 跟踪

MLflow 是用于管理端到端机器学习生命周期的开源平台。
对于 AutoML，MLflow 通过 Apache Spark MLlib 为模型优化提供自动跟踪。 借助自动化 MLflow 跟踪时，可在使用 `CrossValidator` 或 `TrainValidationSplit` 运行优化代码时自动记录指定的超参数和评估指标，从而简化识别最佳模型的过程。
自动化 MLflow 跟踪仅可用于 Python 笔记本。

* [Apache Spark MLlib 和自动化 MLflow 跟踪](mllib-mlflow-integration.md)

## <a name="hyperparameter-tuning-with-hyperopt"></a><a id="hyperopt-overview"> </a><a id="hyperparameter-tuning-with-hyperopt"> </a>使用 Hyperopt 进行超参数优化

[Hyperopt](https://github.com/hyperopt/hyperopt) 是一个开源库，可简化分布式超参数优化。 你还可使用条件参数跨不同的模型体系结构自动搜索。

Hyperopt 可基于标量值目标函数的一组输入参数优化该函数。 使用 Hyperopt 对机器学习模型进行超参数优化时，需将目标函数定义为使用超参数作为输入，并输出训练或验证损失。 在目标函数中加载训练数据，使用从输入中收到的超参数训练机器学习模型，并像平时一样每隔几次迭代保存模型检查点。 Hyperopt 提供两种优化算法 - 随机搜索和贝叶斯方法 Tree of Parzen Estimator (TPE)；与网格搜索等暴力算法相比，这两种算法的计算效率更高。

可通过两种方式在分布式设置中使用 Hyperopt：

* 将分布式 Hyperopt 与单计算机训练算法配合使用。 具体而言，在调用 `hyperopt.fmin()` 时使用 `SparkTrials` 类，并在目标函数中运行单计算机训练算法。
* 将单计算机 Hyperopt 与分布式训练算法配合使用。 具体而言，在调用 `hyperopt.fmin()` 时使用默认的 `base.Trials` 类，并在目标函数中运行分布式训练算法。

有关这两种用例的详细演示，请参阅以下文章：

* [分布式 Hyperopt 和自动化 MLflow 跟踪](hyperopt-spark-mlflow-integration.md)
* [使用分布式 Hyperopt 和自动化 MLflow 跟踪进行模型搜索](hyperopt-model-selection.md)
* [分布式 Hyperopt 最佳做法和故障排除](hyperopt-best-practices.md)
* [将 Hyperopt 与 HorovodRunner 和 Apache Spark MLlib 配合使用](hyperopt-distributed-ml.md)

此[端到端示例笔记本](../../mlflow/end-to-end-example.md)使用 Hyperopt 和 SparkTrials 来运行并行超参数扫描，从而并行训练多个模型。 此示例还使用 MLflow 跟踪每个参数配置的性能。