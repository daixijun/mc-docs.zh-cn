---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/26/2020
title: 如何发现谁在 Azure 门户中删除了工作区 - Azure Databricks
description: 了解如何发现谁删除了 Azure Databricks 工作区。
category: Workspace
ms.openlocfilehash: 9f639735e38aa4c86f374d948d936376191e0c68
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589814"
---
# <a name="how-to-discover-who-deleted-a-workspace-in-azure-portal"></a>如何发现谁在 Azure 门户中删除了工作区

如果工作区已消失或已删除，则可以通过检查 Azure 门户中的“活动日志”来确定删除该工作区的用户。

1. 在 Azure 门户中，转到“活动日志”。
2. 展开时间线，关注删除工作区的时间。
3. 筛选日志，获取特定事件的记录。
4. 单击事件以显示有关事件的信息，包括发起事件的用户。

屏幕截图显示如何单击“操作名称”列中的“删除 Databricks 工作区”事件，然后查看有关事件的详细信息 。

> [!div class="mx-imgBorder"]
> ![删除 Databricks 工作区](../_static/images/administration/activity-log.png)

如果仍找不到删除工作区的用户，请通过 Microsoft 支持部门创建支持案例。 提供[工作区 ID](/databricks/workspace/workspace-details#workspace-instance-and-id) 和事件的时间范围（包括时区）等详细信息。 Microsoft 支持部门将查看相应的后端活动日志。