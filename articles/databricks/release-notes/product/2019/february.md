---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 04/29/2020
title: 2019 年 2 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2019 年 2 月发行说明。
ms.openlocfilehash: 3307a7c19e243cadf27ecb694c4869df3ac0ee3c
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329437"
---
# <a name="february-2019"></a>2019 年 2 月

这些功能和 Azure Databricks 平台的改进已于 2019 年 2 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="databricks-light-generally-available"></a>Databricks Light 已推出正式版

**2019 年 2 月 26 日 - 3 月 5 日：版本 2.92**

Databricks Light（又称为轻量数据工程）现已正式推出。 Databricks Light 是开放源代码 Apache Spark 运行时的 Databricks 包。 它为不需要 Databricks Runtime 所提供的高级性能、可靠性或自动缩放优势的作业提供运行时选项。 仅当创建运行 [JAR、Python 或 spark-submit](../../../dev-tools/api/latest/jobs.md#jobscreatejob) 作业的群集时，才可以选择 Databricks Light；对于要在其上运行交互式或笔记本作业工作负荷的群集，不能选择此运行时。 请参阅 [Databricks Light](../../../runtime/light.md)。

## <a name="managed-mlflow-on-azure-databricks-public-preview"></a>Azure Databricks 上的托管 MLflow 公共预览版

**2019 年 2 月 26 日 - 3 月 5 日：版本 2.92**

[MLflow](https://www.mlflow.org/) 是用于管理端到端机器学习生命周期的开源平台。 它处理三个主要函数：

* 跟踪试验，以记录和比较参数与结果。
* 通过各种 ML 库管理模型，并将其部署到各种模型服务和推理平台。
* 将 ML 代码打包成可重用、可再现的格式，以便与其他数据科学家共享或转移到生产环境。

Azure Databricks 现提供与企业安全性功能、高可用性和其他 Azure Databricks 工作区功能（例如试验和运行管理，以及笔记本修订版捕获）集成的完全托管式 MLflow 版本。 Azure Databricks 上的 MLflow 提供集成体验用于跟踪和保护机器学习模型训练运行，以及运行机器学习项目。 通过在 Azure Databricks 上使用托管 MLflow，可以获得两个平台的优势，包括：

* **工作区：** 协同使用托管 MLflow 跟踪服务器和集成的试验 UI 跟踪和组织Azure Databricks 工作区中的试验和结果。 在笔记本中使用 MLflow 时，Azure Databricks 会自动捕获笔记本修订，以便以后可重现相同的代码和运行。
* **安全性：** 通过 ACL 在整个机器学习生命周期中集中使用一种通用的安全模型。
* **作业：** 远程以及直接在 Azure Databricks 笔记本中将 MLflow 项目作为 Azure Databricks 作业运行。

以下演示 Azure Databricks 工作区中的跟踪工作流：

> [!div class="mx-imgBorder"]
> ![跟踪运行并组织试验工作流](../../../_static/images/mlflow/mlflow-exp-azure.gif)

有关详细信息，请参阅[试验](../../../applications/mlflow/tracking.md#mlflow-experiments)和[在 Azure Databricks 上运行 MLflow 项目](../../../applications/mlflow/projects.md#mlflow-projects)。

## <a name="azure-data-lake-storage-gen2-connector-is-generally-available"></a>Azure Data Lake Storage Gen2 连接器已推出正式版

**2019 年 2 月 15 日**

[Azure Data Lake Storage Gen2](../../../data/data-sources/azure/azure-datalake-gen2.md) (ADLS Gen2) 是用于大数据分析的下一代数据湖解决方案，与适用于 Azure Databricks 的 ADLS Gen2 连接器一样，现已正式推出。 此外，很高兴地宣布，在 Databricks Runtime 5.2 及更高版本上运行群集时，ADLS Gen2 支持 Databricks Delta。

## <a name="python-3-now-the-default-when-you-create-clusters"></a>Python 3 现已成为创建群集时的默认版本

**2019 年 2 月 12-19：版本 2.91**

使用 UI 创建的群集的默认 Python 版本已从 Python 2 改为 Python 3。 使用 REST API 创建的群集的默认版本仍是 Python 2。

现有群集不会更改其 Python 版本。 但如果你习惯在新建群集时将 Python 2 作为默认版本，则需开始注意 Python 版本的选择。

> [!div class="mx-imgBorder"]
> ![默认 Python 版本](../../../_static/images/clusters/python-version-3.png)

请参阅 [Python 版本](../../../clusters/configure.md#python-3)。

## <a name="delta-lake-generally-available"></a>Delta Lake 现已推出正式版

**2019 年 2 月 1 日**

现在，每个人都可以从 Databricks Delta 强大的事务性存储层和超快速读取中受益：从 2 月 1 日开始，Delta Lake 正式发布，并且适用于所有支持的 Databricks Runtime 版本。  有关 Delta 的详细信息，请参阅 [Delta Lake](../../../delta/index.md)。