---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/26/2020
title: 机器学习和深度学习 - Azure Databricks
description: 了解 Azure Databricks 中的机器学习和深度学习功能。
ms.openlocfilehash: d67f4147bcc2a28222c2d0f7e997cafcdee0bf2c
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589958"
---
# <a name="machine-learning-and-deep-learning"></a>机器学习和深度学习

通过 Azure Databricks 这一环境，可以轻松地大规模生成、训练、管理和部署机器学习与深度学习模型。 Azure Databricks 与多个热门开源库和 [MLflow](../mlflow/index.md) 机器学习平台 API 紧密集成，支持从数据准备到部署的端到端机器学习生命周期。

  [用于机器学习的 Databricks Runtime](../../runtime/mlruntime.md#mlruntime) (Databricks Runtime ML) 是一个针对机器学习和数据科学优化的现成环境。 Databricks Runtime ML 基于每个 Databricks Runtime 版本并随这些版本进行更新。

 Databricks Runtime ML 包含很多外部库，例如 TensorFlow、PyTorch、Horovod、scikit-learn 和 [XGBoost](train-model/xgboost.md)，它还提供了多个扩展来提升性能，这些扩展包括 XGBoost 中的 GPU 加速、使用 [HorovodRunner](train-model/distributed-training/horovod-runner.md) 的分布式深度学习，以及使用 [Databricks 文件系统 (DBFS) FUSE 装载](load-data/index.md#store-files-for-data-loading-and-model-checkpointing)的模型检查点。

若要使用 Databricks Runtime ML，请在创建群集时选择运行时的 ML 版本。

* 可将安装额外的[库](../../libraries/index.md)，也可在创建时使用 [init 脚本](../../clusters/init-scripts.md#cluster-scoped-init-script)在群集上安装库。
* 可创建启用 GPU 的群集来提升深度学习任务的执行速度。 有关创建支持 GPU 的 Azure Databricks 群集的信息，请参阅[支持 GPU 的群集](../../clusters/gpu.md#gpu-clusters)。 Databricks Runtime ML 包括 GPU 硬件驱动程序和 NVIDIA 库（例如 CUDA）。

本部分中有完整机器学习/深度学习工作流的相关信息和示例，包括数据加载、特征工程、模型训练、超参数优化、模型推理和模型部署与导出。 其中许多示例还阐释了使用 [MLflow](../mlflow/index.md) 跟踪、管理和部署机器学习工作流的优势。

* [10 分钟教程：Azure Databricks 上的机器学习](tutorial/index.md)
* [加载数据](load-data/index.md)
* [预处理数据](preprocess-data/index.md)
* [训练模型](train-model/index.md)
* [超参数优化和 AutoML](automl-hyperparam-tuning/index.md)
* [模型推理](model-inference/index.md)
* [部署和提供模型](model-deploy/index.md)
* [导出和导入模型](model-export/index.md)
* [参考解决方案](reference-solutions/index.md)