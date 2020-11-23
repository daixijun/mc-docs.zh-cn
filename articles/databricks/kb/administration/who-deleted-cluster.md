---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/26/2020
title: 如何发现谁在 Azure 门户中删除了群集 - Azure Databricks
description: 了解如何发现谁删除了 Azure Databricks 群集。
category: Cluster Creation, Termination, or Sizing
ms.openlocfilehash: 8bc8d05ece48619199e51ebd597f9735d82510b5
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589779"
---
# <a name="how-to-discover-who-deleted-a-cluster-in-azure-portal"></a>如何发现谁在 Azure 门户中删除了群集

如果工作区中的某个群集消失或被删除，可通过在 Azure 门户中的 Log Analytics 工作区服务中运行查询来确定哪个用户删除了该群集。

> [!NOTE]
>
> 如果尚未设置分析工作区，则必须配置 [Azure Databricks 中的诊断日志记录](/databricks/administration-guide/account-settings/azure-diagnostic-logs)才能继续操作。

1. 在 Azure 门户中加载 Log Analytics 工作区服务。
2. 单击工作区的名称。
3. 单击“日志”。
4. 查找以下文本：在此处键入查询或单击示例查询之一即可开始。

   > [!div class="mx-imgBorder"]
   > ![Azure Log Analytics 工作区](../_static/images/administration/azure-log-analytics.png)

5. 输入以下查询：

   ```
   DatabricksClusters
   | where ActionName == "permanentDelete"
        and Response contains "\"statusCode\":200"
        and RequestParams contains "\"cluster_id\":\"0210-024915-bore731\""  // Add cluster_id filter if cluster id is known
        and TimeGenerated between(datetime("2020-01-25 00:00:00") .. datetime("2020-01-28 00:00:00"))  // Add timestamp (in UTC) filter to narrow down the result.
   | extend id = parse_json(Identity)
   | extend requestParams = parse_json(RequestParams)
   | project UserEmail=id.email,clusterId = requestParams.cluster_id, SourceIPAddress, EventTime=TimeGenerated
   ```

6. 根据需要编辑 cluster_id。
7. 编辑 datetime 值以在特定时间范围内进行筛选。
8. 单击“运行”以执行查询。

结果（如果有）将显示在查询框下方。

  ![查询结果](../_static/images/administration/azure-log-results.png)

如果仍找不到删除群集的用户，请通过 Microsoft 支持部门创建一个支持案例。 提供[工作区 ID](/databricks/workspace/workspace-details#workspace-instance-and-id) 和事件的时间范围（包括时区）等详细信息。 Microsoft 支持部门将查看相应的后端活动日志。