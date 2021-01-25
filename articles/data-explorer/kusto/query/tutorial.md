---
title: 教程：Azure 数据资源管理器和 Azure Monitor 中的 Kusto 查询
description: 本教程介绍如何使用以 Kusto 查询语言编写的查询来满足 Azure 数据资源管理器和 Azure Monitor 中的常见查询需求。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 10/08/2020
ms.date: 01/22/2021
ms.localizationpriority: high
zone_pivot_group_filename: zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 7d5b9c8342d1c5f6b3ac39a40e5903c184c42a5f
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611520"
---
# <a name="tutorial-use-kusto-queries-in-azure-data-explorer-and-azure-monitor"></a>教程：在 Azure 数据资源管理器和 Azure Monitor 中使用 Kusto 查询

::: zone pivot="azuredataexplorer"

了解 Kusto 查询语言的最佳方式是查看一些基本查询，从而“感受”这种语言。 建议使用[包含一些示例数据的数据库](https://help.kusto.chinacloudapi.cn/Samples)。 本教程中演示的查询应在该数据库上运行。 示例数据库中的 `StormEvents` 表提供了一些有关在美国发生的风暴的信息。

## <a name="count-rows"></a>统计行数

我们的示例数据库有一个名为 `StormEvents` 的表。 若要了解该表的大小，请将其内容传递到一个运算符，该运算符只计算表中的行数。 

语法说明：查询是数据源（通常是表名称），可以选择后跟一个或多个管道字符和一些表格运算符。

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents | count
```

输出如下：

|计数|
|-----|
|59066|
    
有关详细信息，请参阅 [count 运算符](./countoperator.md)。

## <a name="select-a-subset-of-columns-project"></a>选择列的子集：project

使用 [project](./projectoperator.md) 来只选择所需的列。 请参阅下面的示例，该示例同时使用 [project](./projectoperator.md) 和 [take](./takeoperator.md) 运算符。

## <a name="filter-by-boolean-expression-where"></a>按布尔表达式筛选：where

仅查看 2007 年 2 月在 `California` 发生的 `flood` 事件：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

输出如下：

|StartTime|EndTime|状态|EventType|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|CALIFORNIA|洪水|19 日凌晨，一股锋面系统穿过南部的 San Joaquin 山谷，给西部的 Kern 县带来了短时强降雨。 据报道，Taft 附近的 166 号州高速公路发生不严重的洪水。|

## <a name="show-n-rows-take"></a>显示 n 行：take 

我们来看一些数据。 5 行随机示例是怎样的？

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

输出如下：

|StartTime|EndTime|EventType|状态|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|暴雨|佛罗里达州|在 24 小时的时间里，Volusia 县沿海部分地区降雨量高达 9 英寸。|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|龙卷风|佛罗里达州|龙卷风袭击了位于 West Crooked 湖北端的 Eustis 镇。 该龙卷风迅速增强为 EF1 级别，并向西北偏北移动，穿过 Eustis。 其移动轨迹将近 2 英里长，最大宽度为 300 码。  龙卷风摧毁了 7 座房屋。 27 所房屋受到严重破坏，81 所房屋报告有轻微损坏。 没有发生严重的人员伤亡，财产损失估计为 620 万美元。|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|海龙卷|大西洋南部|Melbourne 海滩东南的大西洋上形成了一个海龙卷，并短暂地向海岸移动。|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|雷雨大风|密西西比州|许多大树被吹倒，有些倒在电线上。 东部 Adams 县遭到破坏。|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|雷雨大风|佐治亚州|县政府报告说，在 206 国道附近的 Quincey Batten 环线上，有几棵树被吹倒。 预估了树木移除费用。|

但是 [take](./takeoperator.md) 不按特定顺序显示表中的行，所以现在对它们进行排序。 （[limit](./takeoperator.md) 是 [take](./takeoperator.md) 的别名，具有相同的效果。）

## <a name="order-results-sort-top"></a>对结果排序：sort 和 top 

* 语法说明：某些运算符具有由关键字（如 `by`）引入的参数。
* 在下面的示例中，`desc` 按降序对结果排序，`asc` 按升序对结果排序。

显示前 n 行，按指定列排序：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

输出如下：

|StartTime|EndTime|EventType|状态|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|冬季风暴|密歇根州|这场大雪持续到元旦凌晨。|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|冬季风暴|密歇根州|这场大雪持续到元旦凌晨。|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|冬季风暴|密歇根州|这场大雪持续到元旦凌晨。|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|疾风|CALIFORNIA|据报道，Ventura 县的山区有北到东北阵风，风速约为每小时 58 英里。|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|疾风|CALIFORNIA|温泉 RAWS 传感器报告说，北风达到每小时 58 英里。|

你可以先使用 [sort](./sortoperator.md)，然后使用 [take](./takeoperator.md) 来获得相同的结果：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="compute-derived-columns-extend"></a>计算派生列：extend

通过计算每行中的值来创建新列：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

输出如下：

|StartTime|EndTime|持续时间|EventType|状态|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|暴雨|佛罗里达州|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|龙卷风|佛罗里达州|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|海龙卷|大西洋南部|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|雷雨大风|密西西比州|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|雷雨大风|佐治亚州|

可重复使用列名称并将计算结果分配给同一列。

示例：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

输出如下：

|x|y|
|---|---|
|3|1|

[标量表达式](./scalar-data-types/index.md)可包含所有常用运算符（`+`、`-`、`*`、`/` 和 `%`），且可使用一系列有用的函数。

## <a name="aggregate-groups-of-rows-summarize"></a>聚合行组：summarize

统计每个州发生的事件数：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[summarize](./summarizeoperator.md) 将在 `by` 子句中具有相同值的行组合在一起，然后使用聚合函数（如 `count`）将每个组合并为单个行。 在这种情况下，每个州都有相应的行，还有一个列，用来表示该州的行计数。

有一系列[聚合函数](./summarizeoperator.md#list-of-aggregation-functions)可供使用。 可以在一个 `summarize` 运算符中使用多个聚合函数，以生成多个计算列。 例如，我们可以获得每个州的风暴数，以及每个州独特风暴类型的总和。 然后，可以使用 [top](./topoperator.md) 来获取受风暴影响最大的州：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

输出如下：

|状态|StormCount|TypeOfStorms|
|---|---|---|
|德克萨斯|4701|27|
|KANSAS|3166|21|
|衣阿华州|2337|19|
|ILLINOIS|2022|23|
|MISSOURI|2016|20|

在 `summarize` 运算符的结果中：

* 每个列都命名为 `by`。
* 每个计算表达式都有一个列。
* `by` 值的每个组合都有一个行。

## <a name="summarize-by-scalar-values"></a>按标量值汇总

可在 `by` 子句中使用标量（数字、时间或间隔）值，但建议使用 [bin()](./binfunction.md) 函数将值放入箱中：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

查询将所有时间戳缩短为一天的间隔：

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

[bin()](./binfunction.md) 在许多语言中与 [floor()](./floorfunction.md) 函数等效。 它简单地将每个值缩减为最接近提供的系数的倍数，这样 [summarize](./summarizeoperator.md) 便可以将行分配给组。

<a name="displaychartortable"></a>
## <a name="display-a-chart-or-table-render"></a>显示图表或表：render

可投射两个列，并将它们用作图表的 x 轴和 y 轴：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="显示按州列出风暴事件计数的柱形图的屏幕截图。":::

尽管我们在 `project` 操作中删除了 `mid`，但如果我们希望图表按该顺序显示州，则仍需要它。

严格来说，`render` 是客户端的一项功能，而不是查询语言的功能。 不过，查询语言还是集成了该功能，该功能对于预想结果非常有用。


## <a name="timecharts"></a>时间图表

回到数字箱，显示一个时序：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="按时间量化的事件折线图的屏幕截图。":::

## <a name="multiple-series"></a>多个序列

在 `summarize by` 子句中使用多个值可为值的每个组合创建单独的行：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="显示按源计数的表的屏幕截图。":::

只需将 `render` 术语添加到前面的示例中：`| render timechart`。

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="显示按源计数的折线图的屏幕截图。":::

注意，`render timechart` 使用第一列作为 x 轴，然后将其他列显示为单独的行。

## <a name="daily-average-cycle"></a>每日平均周期

平均一天的活动如何变化？

按一天的时间取模对事件计数，将其量化到小时。 此处，我们使用 `floor` 而不是 `bin`：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="显示按小时计数的时间表的屏幕截图。":::

目前，`render` 无法正确标记持续时间，但我们可以使用 `| render columnchart` 代替：

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="显示按小时计数的柱形图的屏幕截图。":::

## <a name="compare-multiple-daily-series"></a>比较多个每日系列

不同州一天中不同时间的活动如何变化？

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="按小时和州计数的时间表的屏幕截图。":::

除以 `1h` 以将 x 轴转换为小时数，而不是持续时间：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="显示按小时和州计数的柱形图的屏幕截图。":::

## <a name="join-data-types"></a>联接数据类型

如何查找两个特定的事件类型以及它们分别发生在哪个州？

可以使用第一个 `EventType` 和第二个 `EventType` 拉取风暴事件，然后在 `State` 上联接这两个集：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tutorial/join-events-lightning-avalanche.png" alt-text="显示联接闪电和雪崩事件的屏幕截图。":::

## <a name="user-session-example-of-join"></a>联接的用户会话示例

此部分不使用 `StormEvents` 表。

假设你有一些描述事件的数据，这些事件使用每个会话的唯一 ID 标记每个用户会话的开始和结束时间。 

如何确定每个用户会话的持续时间？

可以使用 `extend` 为这两个时间戳提供别名，然后计算会话持续时间：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
Events
| where eventName == "session_started"
| project start_time = timestamp, stop_time, country, session_id
| join ( Events
    | where eventName == "session_ended"
    | project stop_time = timestamp, session_id
    ) on session_id
| extend duration = stop_time - start_time
| project start_time, stop_time, country, duration
| take 10
```

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="用户会话扩展的结果表的屏幕截图。":::

执行联接前，最好使用 `project` 以只选择所需的列。 在相同子句中，重命名 `timestamp` 列。

## <a name="plot-a-distribution"></a>绘制分布图

返回到 `StormEvents` 表，其中有多少不同时长的风暴？

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m)
| sort by duration asc
| render timechart
```

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="按持续时间计数的事件结果时间表的屏幕截图。":::

或者，你可以使用 `| render columnchart`：

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="按持续时间计数的事件的柱状图的屏幕截图。":::

## <a name="percentiles"></a>百分位数

在不同百分比的风暴中，我们发现了哪些持续时间范围？

若要获取此信息，请使用上述查询，但将 `render` 替换为以下内容：

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

在此示例中，我们没有使用 `by` 子句，因此输出是单个行：

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" lightbox="images/tutorial/summarize-percentiles-duration.png" alt-text="按持续时间计数的汇总百分位数的结果表的屏幕截图。":::

从输出中可以看到：

* 有 5% 的风暴的持续时间不超过 5 分钟。
* 有 50% 的风暴的持续时间不超过 1小时 25 分钟。
* 有 95% 的风暴至少持续了 2 小时 50 分钟。

若要获取每个州的单独明细，请分别将 `state` 列与两个 `summarize` 运算符一起使用：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m), State
| sort by duration asc
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="按州计数的汇总百分位数持续时间表。":::

## <a name="assign-a-result-to-a-variable-let"></a>将结果分配给变量：let

使用 [let](./letstatement.md) 分离出上一个 `join` 示例中查询表达式的各部分。 结果不变：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
let LightningStorms = 
    StormEvents
    | where EventType == "Lightning";
let AvalancheStorms = 
    StormEvents
    | where EventType == "Avalanche";
LightningStorms 
| join (AvalancheStorms) on State
| distinct State
```
> [!TIP]
> 在 Kusto Explorer 中，若要执行整个查询，请不要在查询的各部分之间添加空白行。

## <a name="combine-data-from-several-databases-in-a-query"></a>合并查询中多个数据库中的数据

在下面的查询中，`Logs` 表必须位于默认数据库中：

```kusto
Logs | where ...
```

若要访问其他数据库中的表，请使用以下语法：

```kusto
database("db").Table
```

例如，如果你有名为 `Diagnostics` 和 `Telemetry` 的数据库，并且想要关联这两个表中的某些数据，则可使用以下查询（假设 `Diagnostics` 为默认数据库）：

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

如果默认数据库为 `Telemetry`，请使用以下查询：

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
前面的两个查询假定两个数据库都位于当前连接到的群集中。 如果 `Telemetry` 数据库位于名为 TelemetryCluster.kusto.chinacloudapi.cn 的群集中，请使用以下查询：

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> 指定群集后，数据库是必需的。

有关合并查询中多个数据库中的数据的详细信息，请参阅[跨数据库查询](cross-cluster-or-database-queries.md)。

## <a name="next-steps"></a>后续步骤

- 查看 [Kusto 查询语言](samples.md?pivots=azuredataexplorer)的代码示例。

::: zone-end

::: zone pivot="azuremonitor"

了解 Kusto 查询语言的最佳方式是查看一些基本查询，从而“感受”这种语言。 这些查询类似于 Azure 数据资源管理器教程中所用的查询，只是使用的是 Azure Log Analytics 工作区中通用表中的数据。 

使用 Azure 门户中的 Log Analytics 运行这些查询。 Log Analytics 是一种可以用来编写日志查询的工具。 使用 Azure Monitor 中的日志数据，然后评估日志查询结果。 如果你不熟悉 Log Analytics，请完成 [Log Analytics 教程](/azure-monitor/log-query/log-analytics-tutorial)。

本教程中的所有查询都使用 [Log Analytics 演示环境](https://portal.azure.cn/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)。 你可以使用自己的环境，但可能不具有在此处使用的某些表。 由于演示环境中的数据不是静态的，因此查询的结果可能与此处显示的结果略有不同。


## <a name="count-rows"></a>统计行数

[InsightsMetrics](/azure-monitor/reference/tables/insightsmetrics) 表包含由见解（如用于 VM 的 Azure Monitor 和用于容器的 Azure Monitor）收集的性能数据。 若要了解表的大小，请将其内容传递到只计算行数的运算符。

查询是数据源（通常是表名称），可以选择后跟一个或多个管道字符和一些表格运算符。 在这种情况下，将返回 `InsightsMetrics` 表中的所有记录，然后将其发送到 [count 运算符](./countoperator.md)。 `count` 运算符显示结果，因为该运算符是查询中的最后一个命令。

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
InsightsMetrics | count
```

输出如下：

|计数|
|-----|
|1,263,191|
    

## <a name="filter-by-boolean-expression-where"></a>按布尔表达式筛选：where

[AzureActivity](/azure-monitor/reference/tables/azureactivity) 表包含 Azure 活动日志中的条目，让你能够深入了解 Azure 中发生的任何订阅级别或管理组级事件。 让我们仅查看特定周内的 `Critical` 条目。

[where](/data-explorer/kusto/query/whereoperator) 运算符在 Kusto 查询语言中是通用的。 `where` 将筛选表中符合特定条件的行。 以下示例命令行使用多个命令。 首先，查询检索表中的所有记录。 然后，它仅针对时间范围内的记录筛选数据。 最后，它仅针对具有 `Critical` 级别的记录筛选这些结果。

> [!NOTE]
> 除了使用 `TimeGenerated` 列在查询中指定筛选器外，还可以在 Log Analytics 中指定时间范围。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](/azure-monitor/log-query/scope)。

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

:::image type="content" source="images/tutorial/azure-monitor-where-results.png" lightbox="images/tutorial/azure-monitor-where-results.png" alt-text="显示 where 运算符示例结果的屏幕截图。":::

## <a name="select-a-subset-of-columns-project"></a>选择列的子集：project

使用 [project](./projectoperator.md) 以只包含所需的列。 基于前面的示例，将输出限制为特定列：

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

:::image type="content" source="images/tutorial/azure-monitor-project-results.png" lightbox="images/tutorial/azure-monitor-project-results.png" alt-text="显示 project 运算符示例结果的屏幕截图。":::

## <a name="show-n-rows-take"></a>显示 n 行：take 

[NetworkMonitoring](/azure-monitor/reference/tables/networkmonitoring) 包含 Azure 虚拟网络的监视数据。 我们来使用 [take](./takeoperator.md) 运算符查看该表中的十个随机示例行。 [take](./takeoperator.md) 不以任何特定顺序显示表中一定数量的行：

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-take-results.png" lightbox="images/tutorial/azure-monitor-take-results.png" alt-text="显示 take 运算符示例结果的屏幕截图。":::

## <a name="order-results-sort-top"></a>对结果排序：sort 和 top 

可先按时间进行排序，返回最新的五条记录，而不是随机记录：

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

可改为使用 [top](./topoperator.md) 运算符来获取此确切结果： 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-top-results.png" lightbox="images/tutorial/azure-monitor-top-results.png" alt-text="显示 top 运算符示例结果的屏幕截图。":::

## <a name="compute-derived-columns-extend"></a>计算派生列：extend

[extend](./projectoperator.md) 运算符与 [project](./projectoperator.md) 运算符相似，只不过它会添加到一组列中，而不是替换它们。 你可使用这两个运算符基于每行上的计算创建一个新列。

[Perf](/azure-monitor/reference/tables/perf) 表包含的性能数据是从运行 Log Analytics 代理的虚拟机中收集的。 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

:::image type="content" source="images/tutorial/azure-monitor-extend-results.png" lightbox="images/tutorial/azure-monitor-extend-results.png" alt-text="显示 extend 运算符示例结果的屏幕截图。":::

## <a name="aggregate-groups-of-rows-summarize"></a>聚合行组：summarize

[summarize](./summarizeoperator.md) 运算符将在 `by` 子句中具有相同值的行组合在一起。 然后，它使用聚合函数（如 `count`）将每个组合并为单个行。 有一系列[聚合函数](./summarizeoperator.md#list-of-aggregation-functions)可供使用。 可以在一个 `summarize` 运算符中使用多个聚合函数，以生成多个计算列。 

[SecurityEvent](/azure-monitor/reference/tables/securityevent) 表包含安全事件，如在受监视的计算机上启动的登录和进程。 可计算每台计算机上发生的每个级别的事件数。 在此示例中，将为每个计算机和级别组合生成一个行。 包含事件计数的列。

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-count-results.png" lightbox="images/tutorial/azure-monitor-summarize-count-results.png" alt-text="显示 summarize count 运算符示例结果的屏幕截图。":::

## <a name="summarize-by-scalar-values"></a>按标量值汇总

可按标量值（如数字和时间值）进行聚合，但应使用 [bin()](./binfunction.md) 函数将行分组为不同的数据集。 例如，如果按 `TimeGenerated` 进行聚合，则几乎每个时间值都有一行。 使用 `bin()` 将这些值合并为小时或天。

[InsightsMetrics](/azure-monitor/reference/tables/insightsmetrics) 表包含由见解（如用于 VM 的 Azure Monitor 和用于容器的 Azure Monitor）收集的性能数据。 以下查询显示多台计算机处理器每小时的平均使用率：

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-avg-results.png" lightbox="images/tutorial/azure-monitor-summarize-avg-results.png" alt-text="显示 avg 运算符示例结果的屏幕截图。":::

## <a name="display-a-chart-or-table-render"></a>显示图表或表：render

[render](./renderoperator.md?pivots=azuremonitor) 运算符指定如何呈现查询的输出。 默认情况下，Log Analytics 将输出呈现为一个表。 可以在运行查询后选择不同的图表类型。 `render` 运算符可用于通常首选特定图表类型的查询。

以下示例显示单台计算机处理器每小时的平均使用率。 它将输出呈现为时间表。

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-results.png" lightbox="images/tutorial/azure-monitor-render-results.png" alt-text="显示 render 运算符示例结果的屏幕截图。":::

## <a name="work-with-multiple-series"></a>使用多个序列

如果在 `summarize by` 子句中使用多个值，则图表将为每组值显示一个单独的序列：

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-multiple-results.png" lightbox="images/tutorial/azure-monitor-render-multiple-results.png" alt-text="显示具有多个序列示例的 render 运算符结果的屏幕截图。":::

## <a name="join-data-from-two-tables"></a>联接两个表中的数据

如果需要在单个查询中检索两个表中的数据该怎么办？ 可使用 [join](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) 运算符将多个表中的行合并到一个结果集中。 每个表都必须有一个具有匹配值的列，以便 join 识别要匹配的行。

[VMComputer](/azure-monitor/reference/tables/vmcomputer) 是 Azure Monitor 用于 VM 的表，用于存储它所监视的虚拟机的详细信息。 [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 包含从这些虚拟机收集的性能数据。 InsightsMetrics 中收集的一个值是可用内存，但不是可用的内存百分比。 若要计算百分比，需要每个虚拟机的物理内存。 该值位于 `VMComputer`。

下面的示例查询使用联接来执行此计算。 [distinct](/azure/data-explorer/kusto/query/distinctoperator) 运算符与 `VMComputer` 一起使用，因为会定期从每台计算机中收集详细信息。 因此，会为该表中的每台计算机创建多个行。 使用 `Computer` 列联接两个表。 在结果集中创建一个行，其中对于 `InsightsMetrics` 中的每一行，包括两个表中的列，并且 `Computer` 中的值与 `VMComputer` 中 `Computer` 列中的相同值匹配。

```kusto
VMComputer
| distinct Computer, PhysicalMemoryMB
| join kind=inner (
    InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val
) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

:::image type="content" source="images/tutorial/azure-monitor-join-results.png" lightbox="images/tutorial/azure-monitor-join-results.png" alt-text="显示 join 运算符示例结果的屏幕截图。":::

## <a name="assign-a-result-to-a-variable-let"></a>将结果分配给变量：let

使用 [let](./letstatement.md) 使查询更易于读取和管理。 可使用此运算符将查询结果分配给以后可以使用的变量。 使用 `let` 语句，可以将前面示例中的查询重新编写为：

 
```kusto
let PhysicalComputer = VMComputer
    | distinct Computer, PhysicalMemoryMB;
    let AvailableMemory = 
InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val;
PhysicalComputer
| join kind=inner (AvailableMemory) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

:::image type="content" source="images/tutorial/azure-monitor-let-results.png" lightbox="images/tutorial/azure-monitor-let-results.png" alt-text="显示 let 运算符示例结果的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

- 查看 [Kusto 查询语言](samples.md?pivots=azuremonitor)的代码示例。


::: zone-end
