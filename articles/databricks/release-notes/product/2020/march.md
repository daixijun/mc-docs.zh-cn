---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 04/07/2020
title: 2020 年 3 月 - Azure Databricks
description: Azure Databricks 新增功能和改进功能的 2020 年 3 月发行说明。
ms.openlocfilehash: 45f50e99f74e5465118ed8750ce25e3d6a24db02
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329158"
---
# <a name="march-2020"></a>2020 年 3 月

这些功能和 Azure Databricks 平台的改进功能已于 2020 年 3 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="managed-mlflow-model-registry-collaborative-hub-available-public-preview"></a>托管 MLflow 模型注册表合作中心已可供使用（公共预览版）

**2020 年 3 月 26 日至 31 日：版本 3.16**

MLflow 模型注册表是一个协作中心，团队可以在其中共享机器学习模型，在从试验到过渡和生产的整个阶段进行协作，将模型与审批和治理工作流集成，并跟踪模型部署。 托管 MLflow 模型注册表现在可供所有 Azure Databricks 客户公开预览。 请参阅[在 MLflow 模型注册表中管理 MLflow 模型的生命周期](../../../applications/mlflow/model-registry.md)。

## <a name="workspace-pool-and-cluster-tags-propagate-to-dbu-usage-details-and-azure-vms-for-better-cost-management-reporting"></a>工作区、池和群集标记将传播到 DBU 使用情况详细信息和 Azure VM，以便改进成本管理报告

**2020 年 3 月 26 日**

从 3 月 26 日开始，我们会将标记传播推广到 Azure Databricks 使用详细信息和 Azure VM。 新的标记传播功能组合了 Azure Databricks 工作区标记（即资源组标记）、池标记和群集标记，并将它们作为资源标记传播到 Databricks DBU 使用情况详细信息和 Azure VM。 你将能够在 Azure 成本管理门户和使用情况详细信息导出内容中查看组合标记信息，从而更好地了解 Azure Databricks 使用情况以及业务部门和团队的准确属性。 请参阅[使用群集、池和工作区标记监视使用情况](../../../administration-guide/account-settings/usage-detail-tags-azure.md)。

## <a name="databricks-runtime-70-beta-previews-apache-spark-30"></a>Databricks Runtime 7.0（Beta 版本）提供 Apache Spark 3.0 预览版

**2020 年 3 月 22 日**

Databricks Runtime 7.0 (Beta) 提供了包含 Scala 2.12 的 Apache Spark 3.0 预览版。 请使用非生产工作负荷对它进行尝试，并向我们提供反馈。

有关详细信息，请参阅完整的 [Databricks Runtime 7.0](../../runtime/7.0.md) 发行说明。

## <a name="databricks-runtime-65-ml-beta"></a>Databricks Runtime 6.5 ML（Beta 版本）

**2020 年 3 月 20 日**

Databricks Runtime 6.5 ML (Beta) 引入了以下库升级：

* MLflow 已从 1.5.0 升级到 1.7.0

有关详细信息，请参阅完整的 [Databricks Runtime 6.5 ML](../../runtime/6.5ml.md) 发行说明。

## <a name="databricks-runtime-65-beta"></a>Databricks Runtime 6.5（Beta 版本）

**2020 年 3 月 20 日**

Databricks Runtime 6.5 (Beta) 引入了许多库升级和新功能，其中包括：

* 对 Delta 表的所有写入、更新和删除操作的操作指标现在会显示在表历史记录中
* 你可以对在 Delta Lake 流式传输微批中处理的数据进行速率限制
* Snowflake 连接器已更新为 2.5.9

有关详细信息，请参阅完整的 [Databricks Runtime 6.5](../../runtime/6.5.md) 发行说明。

## <a name="azure-databricks-feedback-now-goes-directly-to-azure-databricks-feedback-portal"></a>Azure Databricks 反馈现在直接进入 Azure Databricks 反馈门户

**2020 年 3 月 10 日至 17 日：版本 3.15**

“? > 反馈”菜单下的链接现在指向 [Azure Databricks 反馈门户](https://feedback.azure.com/forums/909463-azure-databricks)。

## <a name="develop-and-test-shiny-applications-inside-rstudio-server"></a>在 RStudio Server 中开发和测试 Shiny 应用程序

**2020 年 3 月 10 日至 17 日：版本 3.15**

现在可以在托管在 Azure Databricks 中的 RStudio Server 内开发和测试 [Shiny](https://shiny.rstudio.com/) 应用程序。 请参阅 [Azure Databricks 上的 Shiny](../../../spark/latest/sparkr/shiny.md)。

## <a name="change-the-default-language-of-a-notebook"></a>更改笔记本的默认语言

**2020 年 3 月 10 日至 17 日：版本 3.15**

现在可以更改笔记本的[默认语言](../../../notebooks/notebooks-use.md#default-language)。

## <a name="databricks-connect-now-supports-databricks-runtime-64"></a>Databricks Connect 现在支持 Databricks Runtime 6.4

**2020 年 3 月 6 日**

[Databricks Connect](../../../dev-tools/databricks-connect.md) 现在支持 Databricks Runtime 6.4。

## <a name="databricks-connect-now-supports-databricks-runtime-63"></a>Databricks Connect 现在支持 Databricks Runtime 6.3

**2020 年 3 月 3 日**

[Databricks Connect](../../../dev-tools/databricks-connect.md) 现在支持 Databricks Runtime 6.3。