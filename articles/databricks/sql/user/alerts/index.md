---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 11/17/2020
title: 警报 - Azure Databricks
description: 了解 Azure Databricks SQL Analytics 中的警报。
ms.openlocfilehash: 47a481a78280ddbe781853ab5081731bf205c24c
ms.sourcegitcommit: bb7497d5a11e8fb506907221ff65a18e6c523372
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98692169"
---
# <a name="alerts"></a>警报

> [!IMPORTANT]
>
> 此功能目前以[公共预览版](../../../release-notes/release-types.md)提供。 请联系 Azure Databricks 代表，以申请访问权限。

[计划查询](../queries/schedule-query.md)返回的字段达到阈值时，你会收到警报。 使用警报来监视业务或将其与工具集成，以启动用户加入或支持工单等工作流。 警报是对计划查询的补充，但每次执行后都会检查警报的条件。

> [!IMPORTANT]
>
> * 查询计划不是必需的，但强烈建议用于警报。 如果将警报添加到非计划查询，则只有在用户手动执行查询并且满足警报条件时，你才会收到警报。
> * 警报不适用于具有[参数](../queries/query-parameters.md)的查询。

## <a name="view-alerts"></a>查看警报

若要查看警报列表，请单击边栏中的![警报图标](../../../_static/images/icons/alerts-icon.png)图标。 默认情况下，警报按“创建者”列以时间倒序排序。 可以通过单击列标题重新对列表进行排序。

* “名称”显示每个警报的字符串名称。
* “创建者”显示创建警报的用户。
* “状态”显示警报状态是 ``TRIGGERED``、``OK`` 还是 ``UNKNOWN``。
  * ``TRIGGERED`` 表示执行最后一次查询时，目标查询中的“值”列满足配置的“条件”和“阈值”。 如果警报检查“cats”是否高于 1500，则“cats”高于 1500 时将触发警报。
  * ``OK`` 表示执行最后一次查询时，“值”列不满足配置的“条件”和“阈值”。 这并不表示之前未触发“警报”。 如果“cats”值现为 1470，则警报将显示为“OK”。
  * ``UNKNOWN`` 表示 SQL Analytics 缺少足够数据，无法评估警报条件。 创建警报后将立即显示此状态，直到查询执行完毕。 如果查询结果中没有数据，或者最新查询结果中不包含配置的“值列”，也会显示此状态。

> [!div class="mx-imgBorder"]
> ![警报列表](../../../_static/images/sql/alerts.png)

* [警报任务](alerts.md)
* [多个列上的警报](alert-multiple-columns.md)