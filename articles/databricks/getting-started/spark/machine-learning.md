---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/10/2020
title: 开始使用 Apache Spark MLlib 进行机器学习 - Azure Databricks
description: 了解如何在 Azure Databricks 中使用 Apache Spark 机器学习库 (MLlib)。
ms.openlocfilehash: c4894489a9879998ff5490c01760da29ee9f86fc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589645"
---
# <a name="get-started-with-apache-spark-mllib-for-machine-learning"></a>开始使用 Apache Spark MLlib 进行机器学习

> [!NOTE]
>
> Databricks Runtime ML 是使用 Azure Databricks 开发和部署机器学习模型的综合性工具。 它包括最常用的机器学习和深度学习库，以及 [MLflow](../../applications/mlflow/index.md)（一种用于跟踪和管理端到端机器学习生命周期的机器学习平台 API）。 有关详细信息，请参阅[机器学习和深度学习](../../applications/machine-learning/index.md)。

Apache Spark 机器学习库 (MLlib) 使数据科学家能够专注于其数据问题和模型，而不是专注于解决围绕分布式数据的复杂性问题（例如基础结构、配置等）。
教程笔记本会引导你完成以下步骤：加载数据、直观显示数据和准备用于 ML 算法的数据、运行和评估简单的线性回归模型以及直观显示结果。

## <a name="notebook"></a>笔记本

若要访问所有这些代码示例，请导入以下笔记本。 如需更多机器学习示例，请参阅[机器学习和深度学习](../../applications/machine-learning/index.md)。

### <a name="apache-spark-machine-learning-notebook"></a>Apache Spark 机器学习笔记本

[获取笔记本](../../_static/notebooks/getting-started/popvspricelr.html)