---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/13/2020
title: 2019 年 3 月 - Azure Databricks
description: Azure Databricks 新增功能和改进的 2019 年 3 月发行说明。
ms.openlocfilehash: 6ae7263643a65d793f6984a2d66f67bfbcdd1b1b
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329331"
---
# <a name="march-2019"></a>2019 年 3 月

这些功能和 Azure Databricks 平台改进已于 2019 年 3 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="purge-deleted-mlflow-experiments-and-runs"></a>清除已删除的 MLflow 试验和运行

**2019 年 3 月 26 日 - 4 月 2 日：版本 2.94**

现在可以永久清除已删除的 MLflow 试验和运行。 请参阅[管理工作区存储](../../../administration-guide/workspace/storage.md)。

## <a name="manage-groups-from-the-admin-console"></a>在管理控制台中管理组

**2019 年 3 月 12 日 - 3 月 19 日：版本 2.93**

管理员现在可以使用管理控制台中的“新建组”选项卡来创建组。 使用“组”选项卡可以添加用户、添加子组、授予成员创建群集的权限以及管理父-子组关系。

> [!div class="mx-imgBorder"]
> ![“组”选项卡](../../../_static/images/admin-settings/groups-tab-azure.png)

请参阅[管理组](../../../administration-guide/users-groups/groups.md)。

## <a name="notebooks-automatically-have-associated-mlflow-experiment"></a>笔记本自动关联 MLflow 试验

**2019 年 3 月 12 日 - 3 月 19 日：版本 2.93**

Azure Databricks 工作区中的每个笔记本现在都关联一个 MLflow 试验。 可以使用 MLflow 跟踪 API 通过引用试验 ID 或试验名称来记录笔记本试验中的运行。 如果你在 MLflow 0.9.0 或更高版本中使用 [Python API](https://mlflow.org/docs/latest/python_api/mlflow.html#module-mlflow) 来创建运行，MLflow 会自动检测笔记本试验。

若要查看与笔记本关联的 MLflow 试验，请单击笔记本上下文栏中的 ![“MLflow 试验链接”图标](../../../_static/images/notebooks/experiment.png) 图标。

> [!div class="mx-imgBorder"]
> ![MLflow 笔记本试验](../../../_static/images/mlflow/mlflow-notebook-experiments-python.gif)

请参阅[笔记本试验](../../../applications/mlflow/tracking.md#mlflow-notebook-experiments)。

## <a name="lsv2-series-instance-types-beta"></a>Lsv2 系列实例类型 (Beta)

**2019 年 3 月 12 日 - 3 月 19 日：版本 2.93**

Azure Databricks 现在为 [Lsv2 VM 系列](https://azure.microsoft.com/blog/announcing-the-general-availability-of-lsv2-series-azure-virtual-machines/)提供 Beta 版支持，以实现高吞吐量和高 IOPS 工作负荷。

## <a name="databricks-delta-public-community"></a>Databricks Delta 公共社区

**2019 年 3 月 8 日**

由于 Delta Lake 现已正式发布，因此我们创建了一个公共社区，供开发人员入门、讨论文章和获取帮助。

* [公共 Delta Slack 工作区](https://join.slack.com/t/delta-users/shared_invite/enQtNTY1NDg0ODcxOTI1LWE3YjMxOTM4MmM0YWNhNjE2YmI2OGI4N2Y3MTRhOWQ1YzE3MTMyYTM5YzRiZWZlYzMwYzk0M2JiZmJhY2Q4NWI)
* [公共 Delta Google 组](https://groups.google.com/forum/#!forum/delta-users)

> [!NOTE]
>
> 这些论坛是公开的。 请勿使用这些论坛与 Azure Databricks 代表或其他用户共享机密信息。