---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 04/29/2020
title: 2019 年 5 月 - Azure Databricks
description: Azure Databricks 新功能和改进的 2019 年 5 月发行说明。
ms.openlocfilehash: 2cc351d38b40a606e7e4e2b1e09a11fd47c149de
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329208"
---
# <a name="may-2019"></a>2019 年 5 月

这些功能和 Azure Databricks 平台改进已于 2019 年 5 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="cluster-event-log-filtering"></a>群集事件日志筛选

**2019 年 5 月 9 日至 14 日：版本 2.97**

现在，在筛选[群集事件日志](../../../clusters/clusters-manage.md#event-log)时可以“全选”，这样便可以通过排除特定事件类型更轻松地进行筛选。

> [!div class="mx-imgBorder"]
> ![选择所有事件筛选器](../../../_static/images/clusters/cluster-event-log-filter.gif)

## <a name="azure-databricks-diagnostic-logging"></a>Azure Databricks 诊断日志记录

**2019 年 5 月 6 日**

你现在可以捕获用户在 Azure Databricks 中执行的活动的详细审核线索。 诊断日志（审核日志）捕获以下类别的事件：

* DBFS
* 群集
* 帐户
* 作业
* 笔记本
* SSH
* 工作区
* 机密
* SQL 权限

可以将 Azure Databricks 诊断日志发送到各种目标：

* 将诊断日志存档到存储帐户。
* 将这些日志流式传输到事件中心。
* 将其发送到 Log Analytics 工作区，并使用 Azure Monitor 查看这些日志，以获取与其他第一方 Azure 服务类似的体验。

可以使用门户、Azure CLI、PowerShell、REST API 和 Azure Policy 在 Azure Databricks 工作区级别启用诊断。

请参阅 [Azure Databricks 中的诊断日志记录](../../../administration-guide/account-settings/azure-diagnostic-logs.md)。

## <a name="jdbcodbc-connectivity-available-without-azure-databricks-premium-plan"></a>在不提供 Azure Databricks Premium 计划的情况下可用的 JDBC/ODBC 连接

**2019 年 5 月 2 日至 7 日：版本 2.96**

对于没有 [Azure Databricks 高级计划](https://databricks.com/product/azure-pricing)的客户，Azure Databricks 现在支持[通过 JDBC/ODBC 连接到 Spark 群集](../../../integrations/bi/jdbc-odbc-bi.md)。