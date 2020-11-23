---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: MLeap ML 模型导出 - Azure Databricks
description: 了解如何使用 MLeap 导入和导出 Azure Databricks 中的机器学习模型。
ms.openlocfilehash: 7ca08f1fd9ca8be86edf87cad2d05527cbbae942
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589930"
---
# <a name="mleap-ml-model-export"></a><a id="mleap-ml-model-export"> </a><a id="mleap-model-export"> </a>MLeap ML 模型导出

MLeap 采用序列化格式，是机器学习管道的执行引擎。 可在 [MLeap GitHub 存储库](https://github.com/combust/mleap)中找到该包。 它是一个开源包，使用 [Apache 2.0 许可证](https://www.apache.org/licenses/LICENSE-2.0)。 有关许可的详细信息，请参阅 MLeap [许可证](https://github.com/combust/mleap/blob/master/LICENSE)。

> [!NOTE]
>
> * MLeap 适用于 Spark 版本 2.0、2.1、2.2、2.3 和2.4。
> * 本指南并不是关于 MLeap 的全面指南。  请参阅 [MLeap 文档](https://mleap-docs.combust.ml/)。

> [!NOTE]
>
> MLeap 包含在[用于机器学习的 Databricks Runtime](../../../runtime/mlruntime.md) 中，后者是一种为机器学习和数据科学提供现成环境的 Databricks Runtime 运行时。 可直接使用 Databricks Runtime ML 来创建群集，而不必按照以下说明安装 MLeap。 请参阅[用于机器学习的 Databricks Runtime](../../../runtime/mlruntime.md)。

下面的笔记本提供了相关指导来讲解如何在 Azure Databricks 上安装 MLeap，还提供了模型导出工作流的示例。

## <a name="export-and-import-models-in-scala"></a>在 Scala 中导出和导入模型

### <a name="mleap-export-scala-notebook"></a>MLeap 导出 Scala 笔记本

[获取笔记本](../../../_static/notebooks/mleap-model-export-demo-scala.html)

## <a name="export-and-import-models-in-python"></a>在 Python 中导出和导入模型

### <a name="mleap-export-python-notebook"></a>MLeap 导出 Python 笔记本

[获取笔记本](../../../_static/notebooks/mleap-model-export-demo-python.html)