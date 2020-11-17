---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 准备数据 - Azure Databricks
description: 了解 Azure Databricks 中的特征工程和数据预处理。
ms.openlocfilehash: 904748bbcd09c4df4801ce4768b4788163eabb14
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589582"
---
# <a name="preprocess-data"></a>预处理数据

对于大型数据集，可使用 Spark SQL 和 MLlib 进行特征工程处理。 Databricks Runtime ML 中包含的第三方库（例如 scikit-learn）也提供了有用的帮助程序方法。 相关示例，请参阅下面关于 scikit-learn 和 MLlib 的机器学习笔记本：

* [使用 scikit-learn 进行特征工程处理](scikit-learn.md)
* [使用 MLlib 进行特征工程处理](mllib.md)

对于更复杂的深度学习特征处理，该示例笔记本演示了如何使用迁移学习进行特征化处理：

* [用于迁移学习的特征化](transfer-learning-tensorflow.md)