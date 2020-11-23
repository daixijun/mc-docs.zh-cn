---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 用于迁移学习的特征化 - Azure Databricks
description: 使用 pandas UDF 的用于迁移学习的特征化示例。
ms.openlocfilehash: 0eb6bf0df89cad1c34b03eb1909a0217b9475c19
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589983"
---
# <a name="featurization-for-transfer-learning"></a><a id="dl-featurization"> </a><a id="featurization-for-transfer-learning"> </a>用于迁移学习的特征化

Azure Databricks 支持通过深度学习模型实现的特征化。 可以通过预先训练的深度学习模型计算将要在其他下游模型中使用的特征。  Azure Databricks 支持大规模的特征化，在整个群集中分配计算。 可以使用 [Databricks Runtime ML](../../../runtime/mlruntime.md) 中包含的深度学习库（包括 TensorFlow 和 PyTorch）执行特征化。

Azure Databricks 还支持[迁移学习](https://en.wikipedia.org/wiki/Transfer_learning)，一种与特征化密切相关的技术。 可以通过迁移学习重复使用相关域中某个问题域的知识。 特征化本身是一种简单但强大的适用于迁移学习的方法：使用预先训练的深度学习模型来计算特征可以将良好特征的相关知识从原始域迁移出来。

本文演示了如何使用以下工作流来计算使用预训练 TensorFlow 模型的迁移学习的特征：

1. 从预训练的深度学习模型（在本例中为 `tensorflow.keras.applications` 中的一个图像分类模型）开始。
2. 截断模型的最后一层。 修改后的模型生成一个特征张量作为输出，而不是一个预测。
3. 将该模型应用于其他问题域的新图像数据集，以计算图像的特征。
4. 使用这些特征来训练新模型。  以下笔记本省略了这最后一步。  有关训练简单模型（例如逻辑回归）的示例，请参阅[机器学习和深度学习](../index.md)。

以下笔记本使用 [pandas UDF](../../../spark/latest/spark-sql/udf-python-pandas.md#pandas-udf) 执行特征化步骤。  pandas UDF 及其更新的变体 [Scalar Iterator pandas UDF](../../../spark/latest/spark-sql/udf-python-pandas.md#scalar-iterator-udfs) 可提供灵活的 API，支持任何深度学习库并提供高性能。

## <a name="featurization-and-transfer-learning-with-tensorflow"></a>使用 TensorFlow 进行特征化和迁移学习

[获取笔记本](../../../_static/notebooks/deep-learning/deep-learning-transfer-learning-keras.html)