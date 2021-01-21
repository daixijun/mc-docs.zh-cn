---
title: Azure 数据资源管理器和 Azure Monitor 中的查询示例
description: 本文介绍了对 Azure 数据资源管理器和 Azure Monitor 使用 Kusto 查询语言的常见查询和示例。
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
ms.openlocfilehash: 051648ea780be9c2d5388d573803460ebd3d177b
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611437"
---
# <a name="samples-for-queries-for-azure-data-explorer-and-azure-monitor"></a>Azure 数据资源管理器和 Azure Monitor 的查询示例

::: zone pivot="azuredataexplorer"

本文介绍 Azure 数据资源管理器中的常见查询需求，以及如何使用 Kusto 查询语言来满足这些需求。

## <a name="display-a-column-chart"></a>显示柱形图

若要投影两列或多列，然后将这些列用作图表的 x 轴和 y 轴，请执行以下步骤：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* 第一列形成 x 轴。 它可以是数字、日期时间或字符串。 
* 使用 `where`、`summarize` 和 `top` 以限制显示的数据量。
* 对结果进行排序以定义 x 轴的顺序。

:::image type="content" source="images/samples/color-bar-chart.png" alt-text="柱形图的屏幕截图，其中包含十个着色列，分别说明 10 个位置的值。":::

## <a name="get-sessions-from-start-and-stop-events"></a>从启动和停止事件获取会话

在事件日志中，某些事件标记了扩展活动或会话的开始时间或结束时间。 

|名称|城市|SessionId|Timestamp|
|---|---|---|---|
|开始|London|2817330|2015-12-09T10:12:02.32|
|游戏|London|2817330|2015-12-09T10:12:52.45|
|开始|曼彻斯特|4267667|2015-12-09T10:14:02.23|
|停止|London|2817330|2015-12-09T10:23:43.18|
|取消|曼彻斯特|4267667|2015-12-09T10:27:26.29|
|停止|曼彻斯特|4267667|2015-12-09T10:28:31.72|

每个事件都有一个会话 ID (`SessionId`)。 质询是将开始事件和停止事件与会话 ID 匹配。

示例：

```kusto
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on SessionId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

若要将开始事件和停止事件与会话 ID 匹配，请执行以下操作：

1. 使用 [let](./letstatement.md) 命名表的投影，在开始联接之前尽可能缩减该表。
1. 使用 [project](./projectoperator.md) 更改时间戳的名称，使开始时间和停止时间都出现在结果中。 `project` 还可选择要在结果中查看的其他列。 
1. 使用 [join](./joinoperator.md) 匹配同一活动的开始和停止项。 为每个活动创建一行。 
1. 再次使用 `project` 添加列以显示活动的持续时间。

输出如下：

|城市|SessionId|StartTime|StopTime|持续时间|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|曼彻斯特|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

## <a name="get-sessions-without-using-a-session-id"></a>在不使用会话 ID 的情况下获取会话

假设不是那么方便，启动和停止事件没有可以用来进行匹配的会话 ID。 但是，我们确实具有发生会话的客户端 IP 地址。 假设每个客户端地址一次只执行一个会话，我们可以将每个开始事件与同一 IP 地址的下一个停止事件匹配：

示例：

```kusto
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize arg_min(duration, *) by bin(StartTime,1s), ClientIp
```

`join` 将每个开始时间与来自同一客户端 IP 地址的所有停止时间相匹配。 示例代码：

- 删除具有较早停止时间的匹配项。
- 按开始时间和 IP 地址分组，使每个会话都有一个组。 
- 为 `StartTime` 参数提供 `bin` 函数。 如果不执行此步骤，Kusto 会自动使用一小时箱将一些开始时间与错误的停止时间相匹配。

`arg_min` 查找每个组中持续时间最短的行，并且 `*` 参数将传递所有其他列。 

该参数在每个列名前加 `min_` 前缀。 

:::image type="content" source="images/samples/start-stop-ip-address-table.png" alt-text="列出结果的表的屏幕截图，其中显示的列有开始时间、客户端 IP、持续时间、城市以及每个客户端/开始时间组合的最早停止时间。"::: 

添加代码以计算大小适宜的箱中的持续时间。在此示例中，由于首选条形图，请除以 `1s` 以将时间跨度转换为数字：

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/number-of-sessions-bar-chart.png" alt-text="柱形图的屏幕截图，其中描述了会话持续时间在指定范围内的会话数。":::

### <a name="full-example"></a>完整示例

```kusto
Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"      
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

## <a name="chart-concurrent-sessions-over-time"></a>为一段时间内的并发会话绘制图表

假设你有一个活动表，其中包含活动的开始时间和结束时间。 你可显示一个图表，用于显示一段时间内同时运行的活动数。

下面是一个示例输入，称为 `X`：

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

对于使用一分钟箱的图表，建议按 1 分钟间隔对每个正在运行的活动进行计数。

下面是一个中间结果：

```kusto
X | extend samples = range(bin(StartTime, 1m), StopTime, 1m)
```

`range` 按指定间隔生成一组值：

|SessionId | StartTime | StopTime  | 示例|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

使用 [mv-expand](./mvexpandoperator.md) 展开这些数组，而不是保留它们：

```kusto
X | mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
```

|SessionId | StartTime | StopTime  | 示例|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | 10:01:00|
| a | 10:01:33 | 10:06:31 | 10:02:00|
| a | 10:01:33 | 10:06:31 | 10:03:00|
| a | 10:01:33 | 10:06:31 | 10:04:00|
| a | 10:01:33 | 10:06:31 | 10:05:00|
| a | 10:01:33 | 10:06:31 | 10:06:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|

现在，按采样时间对结果进行分组，并计算每个活动的发生次数：

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* 使用 `todatetime()` 是因为 [mv-expand](./mvexpandoperator.md) 会生成动态类型的列。
* 使用 `bin()` 是因为对于数值和日期，如果未提供时间间隔，`summarize` 始终使用默认时间间隔应用 `bin()` 函数。 

输出如下：

| count_SessionId | 示例|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

可使用条形图或时间表来呈现结果。

## <a name="introduce-null-bins-into-summarize"></a>将空箱引入 summarize

当对包含日期时间列的组键应用 `summarize` 运算符时，请将这些值放入固定宽度的箱中：

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

此示例生成一个表，其中 `T` 中的每组行都有一行，这些行用的是五分钟箱。

该段代码不会添加“空箱”，即在`StartTime` 和 `StopTime` 之间不会为时间箱值添加空箱行，因为 `T` 中没有对应行。 建议使用这些箱来“填充”表。以下是这样操作的一种方法：

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| summarize Count=count() by bin(Timestamp, 5m)
| where ...
| union ( // 1
  range x from 1 to 1 step 1 // 2
  | mv-expand Timestamp=range(StartTime, StopTime, 5m) to typeof(datetime) // 3
  | extend Count=0 // 4
  )
| summarize Count=sum(Count) by bin(Timestamp, 5m) // 5 
```

下面是上述查询的分步说明： 

1. 使用 `union` 运算符向表添加更多行。 这些行由 `union` 表达式生成。
1. `range` 运算符将生成只有一行一列的表。 除用于 `mv-expand` 外，该表不用于任何其他用途。
1. `range` 上的 `mv-expand` 运算符创建的行数与 `StartTime` 和 `EndTime` 之间的五分钟箱所具有的行数相等。
1. 使用一个值为 `0` 的 `Count`。
1. `summarize` 运算符将 `union` 的原始（左侧或外部）参数中的箱分组到一起。 此运算符还会根据它的内部参数进行分箱（空箱行）。 此过程可确保输出中每个箱都有一行，其值为零或为原始计数。

## <a name="get-more-from-your-data-by-using-kusto-with-machine-learning"></a>结合使用机器学习与 Kusto，从数据中获取更多信息 

许多有趣的用例使用机器学习算法，并从遥测数据中获得有趣的见解。 通常，这些算法需要一个结构严谨的数据集作为输入。 原始日志数据通常与所需的结构和大小不匹配。 

首先查找特定必应推理服务的错误率中的异常。 日志表有 650 亿条记录。 以下基本查询筛选了 250,000 个错误，然后对使用异常情况检测函数 [series_decompose_anomalies](series-decompose-anomaliesfunction.md) 的错误计数创建了时序。 这些异常是由 Kusto 服务检测到的，并在时序图上以红点突出显示。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

该服务识别了几个具有可疑错误率的时间存储桶。 使用 Kusto 放大此时间范围。 然后运行在 `Message` 列上聚合的查询。 尝试找出最常见的错误。 

消息的整个堆栈跟踪的相关部分将被截断，因此结果在页面上的显示效果更好。 

你可以看到成功识别出了前八个错误。 但是，后面是一长串错误，因为错误消息是使用包含更改数据的格式字符串创建的：

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| summarize count() by Message 
| top 10 by count_ 
| project count_, Message 
```

|count_|Message
|---|---
|7125|'RunCycleFromInterimData' 方法的 ExecuteAlgorithmMethod 已失败...
|7125|InferenceHostService 调用失败...System.NullReferenceException:对象引用未设置为某个对象的实例...
|7124|推理系统意外错误...System.NullReferenceException:对象引用未设置为某个对象的实例... 
|5112|推理系统意外错误...System.NullReferenceException:对象引用未设置为某个对象的实例...
|174|InferenceHostService 调用失败...System.ServiceModel.CommunicationException:写入到管道时出错:...
|10|'RunCycleFromInterimData' 方法的 ExecuteAlgorithmMethod 已失败...
|10|推理系统错误...Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|3|InferenceHostService 调用失败...System.ServiceModel.CommunicationObjectFaultedException:...
|1|推理系统错误...SocialGraph.BOSS.OperationResponse...AIS TraceId:8292FC561AC64BED8FA243808FE74EFD...
|1|推理系统错误...SocialGraph.BOSS.OperationResponse...AIS TraceId:5F79F7587FF943EC9B641E02E701AFBF...

此时，使用 `reduce` 运算符会有帮助。 该运算符在代码中识别出了 63 个不同错误，这些错误均源自同一跟踪检测点。 `reduce` 有助于将重点放在该时间范围内的其他有意义的错误跟踪。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|计数|模式
|---|---
|7125|'RunCycleFromInterimData' 方法的 ExecuteAlgorithmMethod 已失败...
|  7125|InferenceHostService 调用失败...System.NullReferenceException:对象引用未设置为某个对象的实例...
|  7124|推理系统意外错误...System.NullReferenceException:对象引用未设置为某个对象的实例...
|  5112|推理系统意外错误...System.NullReferenceException:对象引用未设置为某个对象的实例...
|  174|InferenceHostService 调用失败...System.ServiceModel.CommunicationException:写入到管道时出错:...
|  63|推理系统错误...Microsoft.Bing.Platform.Inferences.\*:写入 \* 是为了将数据写入对象 BOSS.\*:SocialGraph.BOSS.Reques...
|  10|'RunCycleFromInterimData' 方法的 ExecuteAlgorithmMethod 已失败...
|  10|推理系统错误...Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|  3|InferenceHostService 调用失败..System.ServiceModel.\*:\*\* 的对象 System.ServiceModel.Channels.\*+\* 是位于 Syst... 的 \*...

现在，你清楚地了解了导致检测到的异常的前几个错误。

若要了解这些错误对示例系统的影响，请考虑以下事项： 
* `Logs` 表包含额外的维度数据，如 `Component` 和 `Cluster`。
* 新的 autocluster 插件可使用简单的查询来帮助获得组件和群集见解。 

在下面的示例中，你可清楚地看到前四个错误的每一个都特定于某个组件。 此外，尽管前三个错误特定于 DB4 群集，但第四个错误会在所有群集中发生。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|计数 |百分比 (%)|组件|群集|Message
|---|---|---|---|---
|7125|26.64|InferenceHostService|DB4|方法的 ExecuteAlgorithmMethod...
|7125|26.64|未知组件|DB4|InferenceHostService 调用失败…
|7124|26.64|InferenceAlgorithmExecutor|DB4|推理系统意外错误...
|5112|19.11|InferenceAlgorithmExecutor|*|推理系统意外错误...

## <a name="map-values-from-one-set-to-another"></a>将值从一个集映射到另一个集

常见的查询用例是值的静态映射。 静态映射可使结果更加直观。

例如在下表中，`DeviceModel` 指定设备型号。 使用设备型号不是引用设备名称的一种简便方式。  

|DeviceModel |计数 
|---|---
|iPhone5,1 |32 
|iPhone3,2 |432 
|iPhone7,2 |55 
|iPhone5,2 |66 

 使用易记名称更方便：  

|FriendlyName |计数 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

接下来的两个示例演示如何将标识设备的方法从使用设备型号更改为使用易记名称。  

### <a name="map-by-using-a-dynamic-dictionary"></a>使用动态字典进行映射

可使用动态字典和动态访问器来实现映射。 例如：

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
// Dataset definition
let Source = datatable(DeviceModel:string, Count:long)
[
  'iPhone5,1', 32,
  'iPhone3,2', 432,
  'iPhone7,2', 55,
  'iPhone5,2', 66,
];
// Query start here
let phone_mapping = dynamic(
  {
    "iPhone5,1" : "iPhone 5",
    "iPhone3,2" : "iPhone 4",
    "iPhone7,2" : "iPhone 6",
    "iPhone5,2" : "iPhone5"
  });
Source 
| project FriendlyName = phone_mapping[DeviceModel], Count
```

|FriendlyName|计数|
|---|---|
|iPhone 5|32|
|iPhone 4|432|
|iPhone 6|55|
|iPhone5|66|

### <a name="map-by-using-a-static-table"></a>使用静态表进行映射

还可使用永久性表和 `join` 运算符来实现映射。
 
1. 仅创建一次映射表：

    ```kusto
    .create table Devices (DeviceModel: string, FriendlyName: string) 

    .ingest inline into table Devices 
        ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
    ```

1. 创建设备内容表：

    |DeviceModel |FriendlyName 
    |---|---
    |iPhone5,1 |iPhone 5 
    |iPhone3,2 |iPhone 4 
    |iPhone7,2 |iPhone 6 
    |iPhone5,2 |iPhone5 

1. 创建测试表源：

    ```kusto
    .create table Source (DeviceModel: string, Count: int)

    .ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
    ```

1. 联接表并运行 project：

   ```kusto
   Devices  
   | join (Source) on DeviceModel  
   | project FriendlyName, Count
   ```

输出如下：

|FriendlyName |计数 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>创建和使用查询时间维度表

通常，建议将查询结果与未存储在数据库中的即席维度表联接起来。 你可定义一个表达式，将其结果定义为范围限定为单个查询的表。 

例如：

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
// Create a query-time dimension table using datatable
let DimTable = datatable(EventType:string, Code:string)
  [
    "Heavy Rain", "HR",
    "Tornado",    "T"
  ]
;
DimTable
| join StormEvents on EventType
| summarize count() by Code
```

下面是一个稍微复杂一点的示例：

```kusto
// Create a query-time dimension table using datatable
let TeamFoundationJobResult = datatable(Result:int, ResultString:string)
  [
    -1, 'None', 0, 'Succeeded', 1, 'PartiallySucceeded', 2, 'Failed',
    3, 'Stopped', 4, 'Killed', 5, 'Blocked', 6, 'ExtensionNotFound',
    7, 'Inactive', 8, 'Disabled', 9, 'JobInitializationError'
  ]
;
JobHistory
  | where PreciseTimeStamp > ago(1h)
  | where Service  != "AX"
  | where Plugin has "Analytics"
  | sort by PreciseTimeStamp desc
  | join kind=leftouter TeamFoundationJobResult on Result
  | extend ExecutionTimeSpan = totimespan(ExecutionTime)
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage
```

## <a name="retrieve-the-latest-records-by-timestamp-per-identity"></a>检索每个标识的最新记录（按时间戳）

假设你有一个表，其中包含：
* 一个 `ID` 列，标识与每行关联的实体，如用户 ID 或节点 ID
* 一个 `timestamp` 列，提供该行的时间参考
* 其他列

可使用 [top-nested 运算符](topnestedoperator.md)进行查询，为 `ID` 列的每个值返回最新的两条记录，其中“最新”定义为“具有最高的 `timestamp` 值” ：

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // #1
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // #2
  top-nested 2 of timestamp by dummy1=max(timestamp),          // #3
  top-nested   of bla       by dummy2=max(1)                   // #4
| project-away dummy0, dummy1, dummy2                          // #5
```


下面是上述查询的分步说明（编号指的是代码注释中的数字）：

1. `datatable` 是生成用于演示的一些测试数据的方法。 通常，在这里将使用实际数据。
1. 此行实质上意味着返回 `id` 的所有非重复值。 
1. 然后，对于通过 max 函数选取的前两个记录，此行会返回以下项：
     * `timestamp` 列
     * 上一层的列（此处为 `id`）
     * 在此层指定的列（此处为 `timestamp`）
1. 此行为上一层返回的每条记录添加 `bla` 列的值。 如果表中有你感兴趣的其他列，可对每一列重复此行。
1. 最后一行使用 [project-away 运算符](projectawayoperator.md)删除 `top-nested` 引入的多余列。

## <a name="extend-a-table-by-a-percentage-of-the-total-calculation"></a>按总计算的百分比来扩展表

当表格表达式附有其值占总和的百分比时，包含数值列的表格表达式对用户来说更有用。

例如，假定查询生成下表：

|SomeSeries|SomeInt|
|----------|-------|
|Apple       |    100|
|香蕉       |    200|

建议按如下所示显示表：

|SomeSeries|SomeInt|Pct |
|----------|-------|----|
|Apple       |    100|33.3|
|香蕉       |    200|66.6|

若要更改表的显示方式，请计算 `SomeInt` 列的总和，然后将此列的每个值除以总和。 对于任意结果，请使用 [as 运算符](asoperator.md)。

例如：

```kusto
// The following table literally represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Apple",
  200, "Banana",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="perform-aggregations-over-a-sliding-window"></a>对滑动窗口执行聚合

下面的示例演示如何使用滑动窗口对列进行汇总。 若要查询，请使用下表，其中包含按时间戳列出的水果价格。

使用范围为七天的滑动窗口计算每种水果每日的最低成本、最高成本和总成本。 结果集中的每条记录都聚合前七天的数据，并且结果包含分析期内每一天的记录。

水果表：

|Timestamp|水果|价格|
|---|---|---|
|2018-09-24 21:00:00.0000000|Bananas|3|
|2018-09-25 20:00:00.0000000|苹果|9|
|2018-09-26 03:00:00.0000000|Bananas|4|
|2018-09-27 10:00:00.0000000|Plums|8|
|2018-09-28 07:00:00.0000000|Bananas|6|
|2018-09-29 21:00:00.0000000|Bananas|8|
|2018-09-30 01:00:00.0000000|Plums|2|
|2018-10-01 05:00:00.0000000|Bananas|0|
|2018-10-02 02:00:00.0000000|Bananas|0|
|2018-10-03 13:00:00.0000000|Plums|4|
|2018-10-04 14:00:00.0000000|苹果|8|
|2018-10-05 05:00:00.0000000|Bananas|2|
|2018-10-06 08:00:00.0000000|Plums|8|
|2018-10-07 12:00:00.0000000|Bananas|0|

下面是滑动窗口聚合查询。 请参阅查询结果后面的说明。

```kusto
let _start = datetime(2018-09-24);
let _end = _start + 13d; 
Fruits 
| extend _bin = bin_at(Timestamp, 1d, _start) // #1 
| extend _endRange = iif(_bin + 7d > _end, _end, 
                            iff( _bin + 7d - 1d < _start, _start,
                                iff( _bin + 7d - 1d < _bin, _bin,  _bin + 7d - 1d)))  // #2
| extend _range = range(_bin, _endRange, 1d) // #3
| mv-expand _range to typeof(datetime) limit 1000000 // #4
| summarize min(Price), max(Price), sum(Price) by Timestamp=bin_at(_range, 1d, _start) ,  Fruit // #5
| where Timestamp >= _start + 7d; // #6

```

输出如下：

|Timestamp|水果|min_Price|max_Price|sum_Price|
|---|---|---|---|---|
|2018-10-01 00:00:00.0000000|苹果|9|9|9|
|2018-10-01 00:00:00.0000000|Bananas|0|8|18|
|2018-10-01 00:00:00.0000000|Plums|2|8|10|
|2018-10-02 00:00:00.0000000|Bananas|0|8|18|
|2018-10-02 00:00:00.0000000|Plums|2|8|10|
|2018-10-03 00:00:00.0000000|Plums|2|8|14|
|2018-10-03 00:00:00.0000000|Bananas|0|8|14|
|2018-10-04 00:00:00.0000000|Bananas|0|8|14|
|2018-10-04 00:00:00.0000000|Plums|2|4|6|
|2018-10-04 00:00:00.0000000|苹果|8|8|8|
|2018-10-05 00:00:00.0000000|Bananas|0|8|10|
|2018-10-05 00:00:00.0000000|Plums|2|4|6|
|2018-10-05 00:00:00.0000000|苹果|8|8|8|
|2018-10-06 00:00:00.0000000|Plums|2|8|14|
|2018-10-06 00:00:00.0000000|Bananas|0|2|2|
|2018-10-06 00:00:00.0000000|苹果|8|8|8|
|2018-10-07 00:00:00.0000000|Bananas|0|2|2|
|2018-10-07 00:00:00.0000000|Plums|4|8|12|
|2018-10-07 00:00:00.0000000|苹果|8|8|8|

查询会在输入表中的每条记录实际出现后的七天内对其进行“延伸”（复制）。 每条记录实际出现 7 次。 因此，每日聚合包含前七天的所有记录。


下面是上述查询的分步说明： 

1. 将每个记录放入一天箱中（相对于 `_start`）。 
1. 确定每条记录的范围结束时间：`_bin + 7d`，除非该值超出 `_start` 和 `_end` 范围（如果出现这种情况，会进行调整）。 
1. 对于每条记录，创建一个 7 天（时间戳）的数组，从当前记录的日期开始。 
1. `mv-expand` 数组，因此将每条记录复制为七条记录，每条记录彼此之间相隔一天。 
1. 针对每天执行聚合函数。 由于 #4，此步骤实际上汇总了过去七天的数据。 
1. 前七天的数据不完整，因为前七天没有七天回溯期。 第一个七天会从最终结果中排除。 在此示例中，它们仅参与了 2018-10-01 的聚合。

## <a name="find-the-preceding-event"></a>查找前一个事件

下一示例演示如何在两个数据集之间查找前一个事件。  

你有两个数据集：A 和 B。对于数据集 B 中的每条记录，在数据集 A 中查找其前一个事件（即 A 中仍早于 B 的 `arg_max` 记录）。

下面是示例数据集： 

```kusto
let A = datatable(Timestamp:datetime, ID:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, ID:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|Timestamp|ID|EventB|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|y|Ay1|
|2019-01-01 00:00:05.0000000|y|Ay2|

</br>

|Timestamp|ID|EventA|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|y|B|
|2019-01-01 00:02:00.0000000|z|B|

预期输出： 

|ID|Timestamp|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|y|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

对于此问题，我们推荐两种不同的方法。 你可在特定数据集上测试这两种方法，以找到最适合你场景的方法。

> [!NOTE] 
> 每种方法在不同数据集上的运行方式可能不同。

### <a name="approach-1"></a>方法 1

此方法按 ID 和时间戳序列化这两个数据集。 然后，它将数据集 B 中的所有事件与数据集 A 中的所有先前事件分为一组。最后，它会在该组中选出数据集 A 中所有事件的 `arg_max`。

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by ID, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != ID), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, ID
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="approach-2"></a>方法 2

这种解决问题的方法需要最长的回溯期。 此方法将查看数据集 A 中的记录可能比数据集 B 早了多长时间。然后，该方法将根据 ID 和此回溯期联接两个数据集。

`join` 会生成所有可能的候选项，数据集 A 的所有记录都早于数据集 B 中的记录，并且在回溯期内。 然后，通过 `arg_min (TimestampB - TimestampA)` 筛选最接近数据集 B 的一条记录。 回溯期越短，查询结果就会越好。

在以下示例中，回溯期设置为 `1m`。 ID 为 `z` 的记录没有对应的 `A` 事件，因为其 `A` 事件早了两分钟。

```kusto 
let _maxLookbackPeriod = 1m;  
let _internalWindowBin = _maxLookbackPeriod / 2;
let B_events = B 
    | extend ID = new_guid()
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend B_Timestamp = Timestamp, _range;
let A_events = A 
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend A_Timestamp = Timestamp, _range;
B_events
    | join kind=leftouter (
        A_events
) on ID, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project ID, B_Timestamp, A_Timestamp, EventB, EventA
```

|ID|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|y|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||


## <a name="next-steps"></a>后续步骤

- 演练 [Kusto 查询语言教程](tutorial.md?pivots=azuredataexplorer)。

::: zone-end

::: zone pivot="azuremonitor"

本文介绍 Azure Monitor 中的常见查询需求，以及如何使用 Kusto 查询语言来满足这些需求。

## <a name="string-operations"></a>字符串操作

以下各节将举例说明在使用 Kusto 查询语言时如何使用字符串。

### <a name="strings-and-how-to-escape-them"></a>字符串及其转义方式

字符串值包装在单引号或双引号中。 在字符左侧添加反斜杠 (\\) 以转义字符：`\t` 表示制表符，`\n` 表示换行符，`\"` 表示单引号字符。

```kusto
print "this is a 'string' literal in double \" quotes"
```

```kusto
print 'this is a "string" literal in single \' quotes'
```

为了防止“\\”用作转义字符，请添加“\@”作为字符串的前缀：

```kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


### <a name="string-comparisons"></a>字符串比较

运算符       |说明                         |区分大小写|示例（生成 `true`）
---------------|------------------------------------|--------------|-----------------------
`==`           |等于                              |是           |`"aBc" == "aBc"`
`!=`           |不等于                          |是           |`"abc" != "ABC"`
`=~`           |等于                              |否            |`"abc" =~ "ABC"`
`!~`           |不等于                          |否            |`"aBc" !~ "xyz"`
`has`          |右侧值是左侧值的完整术语 |否|`"North America" has "america"`
`!has`         |右侧值不是左侧值的完整术语       |否            |`"North America" !has "amer"` 
`has_cs`       |右侧值是左侧值的完整术语 |是|`"North America" has_cs "America"`
`!has_cs`      |右侧值不是左侧值的完整术语       |是            |`"North America" !has_cs "amer"` 
`hasprefix`    |右侧值是左侧值的术语前缀         |否            |`"North America" hasprefix "ame"`
`!hasprefix`   |右侧值不是左侧值的术语前缀     |否            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |右侧值是左侧值的术语前缀         |是            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |右侧值不是左侧值的术语前缀     |是            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |右侧值是左侧值的术语后缀         |否            |`"North America" hassuffix "ica"`
`!hassuffix`   |右侧值不是左侧值的术语后缀     |否            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |右侧值是左侧值的术语后缀         |是            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |右侧值不是左侧值的术语后缀     |是            |`"North America" !hassuffix_cs "icA"`
`contains`     |右侧值作为左侧值的子序列出现  |否            |`"FabriKam" contains "BRik"`
`!contains`    |右侧值不会出现在左侧值中           |否            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |右侧值作为左侧值的子序列出现  |是           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |右侧值不会出现在左侧值中           |是           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |右侧值是左侧值的初始子序列|否            |`"Fabrikam" startswith "fab"`
`!startswith`  |右侧值不是左侧值的初始子序列|否        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |右侧值是左侧值的初始子序列|是            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |右侧值不是左侧值的初始子序列|是        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |右侧值是左侧值的结束子序列|否             |`"Fabrikam" endswith "Kam"`
`!endswith`    |右侧值不是左侧值的结束子序列|否         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |右侧值是左侧值的结束子序列|是             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |右侧值不是左侧值的结束子序列|是         |`"Fabrikam" !endswith "brik"`
`matches regex`|左侧值包含右侧值的匹配项|是           |`"Fabrikam" matches regex "b.*k"`
`in`           |等于某个元素       |是           |`"abc" in ("123", "345", "abc")`
`!in`          |不等于任何元素   |是           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>countof

计算字符串中子字符串的出现次数。 可以匹配纯字符串，也可使用正则表达式 (regex)。 纯字符串匹配项可能重叠，但 regex 匹配项不会重叠。

```
countof(text, search [, kind])
```

- `text`：输入字符串 
- `search`：用于在 text 内部进行匹配的纯字符串或 regex
- `kind`：_normal_ | _regex_（默认值：normal）。

返回搜索字符串可在容器中匹配的次数。 纯字符串匹配项可能重叠，但 regex 匹配项不会重叠。

#### <a name="plain-string-matches"></a>纯字符串匹配项

```kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>正则表达式匹配项

```kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


### <a name="extract"></a>*extract*

从特定字符串中获取正则表达式的匹配项。 还可将提取的子字符串转换为指定的类型。

```kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex`：正则表达式。
- `captureGroup`：指示待提取的捕获组的正整数常量。 使用 0 代表整个匹配项，1 代表正则表达式中第一个 \(\) 匹配的值，并用 2 及以上数字代表后续括号。
- `text` - 要搜索的字符串。
- `typeLiteral` - 可选的类型文本（例如 `typeof(long)`）。 （如果支持）提取的子字符串将转换成此类型。

返回与指定捕获组 `captureGroup` 匹配的子字符串，并且该字符串可转换为 `typeLiteral`。 如果没有匹配项或类型转换失败，则返回 null。

下面的示例从检测信号记录中提取 `ComputerIP` 的最后一个八位字节：

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

下面的示例提取最后一个八位字节，将其强制转换为 real 类型（数字），然后计算下一个 IP 值：

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

在下一示例中，字符串 `Trace` 用于搜索 `Duration` 的定义。 匹配项强制转换为 `real` 并与时间常量 (1 s) 相乘，该常量将 `Duration` 强制转换为 `timespan` 类型。

```kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>isempty、isnotempty、notempty  

- 如果参数是空字符串或 null，则 `isempty` 返回 `true`（请参阅 `isnull`）。
- 如果参数不是空字符串也不是 null，则 `isnotempty` 返回 `true`（请参阅 `isnotnull`）。 别名：`notempty`。


```kusto
isempty(value)
isnotempty(value)
```

#### <a name="example"></a>示例

```kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


### <a name="parseurl"></a>parseurl

将 URL 拆分为不同的组成部分（如 protocol、host 和 port），然后返回包含字符串形式的组成部分的字典对象。

```
parseurl(urlstring)
```

#### <a name="example"></a>示例

```kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

输出如下：

```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```

### <a name="replace"></a>*replace*

将所有正则表达式匹配项替换为另一字符串。 

```
replace(regex, rewrite, input_text)
```

- `regex`：作为匹配依据的正则表达式。 可在 \(\) 中包含捕获组。
- `rewrite`：通过匹配 regex 实现的任何匹配项的 regex 替换项。 使用 \0 引用整个匹配项，使用 \1 引用第一个捕获组，使用 \2 等引用后续捕获组。
- `input_text`：要在其中搜索的输入字符串。

在使用 rewrite 计算结果替换 regex 的所有匹配项后返回 text。 匹配项不会重叠。

#### <a name="example"></a>示例

```kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

输出如下：

活动                                        |已替换
------------------------------------------------|----------------------------------------------------------
4663 - 尝试访问某个对象  |活动 ID 4663：尝试访问某个对象。


### <a name="split"></a>*split*

根据指定的分隔符拆分特定的字符串，然后返回生成的子字符串的数组。

```
split(source, delimiter [, requestedIndex])
```

- `source`：要根据指定的分隔符拆分的字符串。
- `delimiter`：用于拆分源字符串的分隔符。
- `requestedIndex`：可选的从零开始的索引。 如果已提供，则返回的字符串数组只包含该项（如果存在）。


#### <a name="example"></a>示例

```kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

### <a name="strcat"></a>strcat

连接字符串参数（支持 1-16 个参数）。

```
strcat("string1", "string2", "string3")
```

#### <a name="example"></a>示例

```kusto
print strcat("hello", " ", "world") // result: "hello world"
```


### <a name="strlen"></a>strlen

返回字符串的长度。

```
strlen("text_to_evaluate")
```

#### <a name="example"></a>示例

```kusto
print strlen("hello")   // result: 5
```


### <a name="substring"></a>*substring*

从特定的字符串提取某个子字符串（从指定的索引开始）。 （可选）可以指定请求子字符串的长度。

```
substring(source, startingIndex [, length])
```

- `source`：从中提取子字符串的源字符串。
- `startingIndex`：请求子字符串的从零开始的起始字符位置。
- `length`：可选的参数，可用于指定返回的子字符串的请求长度。

#### <a name="example"></a>示例

```kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>tolower、toupper 

将特定的字符串转换为全部小写或全部大写。

```
tolower("value")
toupper("value")
```

#### <a name="example"></a>示例

```kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```

## <a name="date-and-time-operations"></a>日期和时间操作

以下各节将举例说明在使用 Kusto 查询语言时如何使用日期和时间值。

### <a name="date-time-basics"></a>日期时间基本信息

Kusto 查询语言主要具有两种与日期和时间相关的数据类型：`datetime` 和 `timespan`。 所有日期均以 UTC 表示。 尽管支持多种日期时间格式，但首选 ISO-8601 格式。 

时间跨度的表示形式是十进制后面接时间单位：

|简写   | 时间单位    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | 毫秒  |
|微秒 | 微秒  |
|时钟周期        | 纳秒   |

可通过使用 `todatetime` 运算符强制转换字符串来创建日期时间值。 例如，要查看在特定时间范围内发送的 VM 检测信号，可使用指定时间范围的 `between` 运算符：

```kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

还有一个常见的情况是将日期时间值与当前时间进行比较。 例如，要查看过去两分钟内的所有检测信号，可结合使用 `now` 运算符和表示两分钟的时间跨度：

```kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

还可通过快捷方式使用此函数：

```kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

最简短且可读性最强的方式是使用 `ago` 运算符：

```kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

假设你不知道开始和结束时间，但知道开始时间和持续时间。 可重写查询：

```kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="convert-time-units"></a>转换时间单位

建议以非默认时间单位表示日期/时间或时间跨度值。 例如，如果你要检查过去 30 分钟内的错误事件，且需要一个显示事件发生时间的计算列，可使用以下查询：

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo` 列包含的值（如 `00:09:31.5118992`）采用的是 hh:mm:ss.fffffff 格式。 如果要将这些值的格式设置为自开始时间以来的 `number` 分钟数，则用该值除以 `1m`：

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```

### <a name="aggregations-and-bucketing-by-time-intervals"></a>按时间间隔进行聚合和分桶

还有一种常见的情况是，需要按特定时间单位获取特点时间段内的统计数据。 对于这种情况，可使用 `bin` 运算符作为 `summarize` 子句的一部分。

使用以下查询获取过去半小时内每 5 分钟发生的事件数：

```kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

此查询会生成以下表：  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

还可使用函数（如 `startofday`）来创建结果存储桶：

```kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

输出如下：

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


### <a name="time-zones"></a>时区

由于所有日期/时间值都以 UTC 表示，因此将这些值转换为本地时区通常很有用。 例如，使用此计算将 UTC 转换为 PST 时间：

```kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>聚合

以下各节将举例说明在使用 Kusto 查询语言时如何聚合查询结果。

### <a name="count"></a>*计数*

在应用任一筛选器后，计算结果集中的行数。 下面的示例返回过去 30 分钟内 `Perf` 表中的总行数。 结果将在名为 `count_` 的列中返回，除非为该列分配了特定名称：


```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

时间表可视化效果可用于查看随时间变化的趋势：

```kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

本例中输出以 5 分钟为间隔显示了 `Perf` 记录计数趋势线：


:::image type="content" source="images/samples/perf-count-line-chart.png" alt-text="以 5 分钟为间隔显示 Perf 记录计数趋势线的折线图的屏幕截图。":::

### <a name="dcount-dcountif"></a>dcount、dcountif 

使用 `dcount` 和 `dcountif` 计算特定列中非重复值的数量。 以下查询计算过去一小时内发送检测信号的非重复计算机的数量：

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

要想只计算发送检测信号的 Linux 计算机，请使用 `dcountif`：

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluate-subgroups"></a>计算子组

要在数据中的子组上执行计数或其他聚合，请使用 `by` 关键字。 例如，要计算每个国家/地区发送检测信号的非重复 Linux 计算机的数量，请使用以下查询：

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|美国    | 19                  |
|加拿大           | 3                   |
|爱尔兰          | 0                   |
|英国   | 0                   |
|荷兰      | 2                   |


要分析更小的数据子组，请在 `by` 部分中添加列名称。 例如，想要根据每个操作系统类型 (`OSType`) 来计算来自每个国家或地区的非重复计算机的数量：

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```


### <a name="percentile"></a>百分位数

要查找中值，请使用带值的 `percentile` 函数指定百分位数：

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

还可指定不同的百分位数，以获取每一个的聚合结果：

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

结果可能显示某些计算机 CPU 具有相似的中值。 不过，尽管某些计算机稳定在中值附近，但其他计算机报告的 CPU 值会更低/更高。 高值和低值意味着计算机出现高峰期。

### <a name="variance"></a>Variance

要直接计算值的方差，请使用标准偏差和方差方法：

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

要分析 CPU 使用量的稳定性，一种很好的方式是将 `stdev` 与中值计算相结合：

```kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generate-lists-and-sets"></a>生成列表和集

可使用 `makelist` 根据特定列中的值顺序创建数据透视图。 例如，你可能想要浏览计算机上发生的最常见有序事件。 实际上，可以根据每台计算机上 `EventID` 值的顺序创建数据透视图： 

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

输出如下：

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` 根据数据的传入顺序生成列表。 若要以最旧到最新的顺序排序事件，请在 `order` 语句中使用 `asc`，而不要使用 `desc`。 

你可能会发现创建仅有非重复值的列表很有用。 此列表称为 set，你可使用 `makeset` 命令生成它：

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

输出如下：

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

与 `makelist` 一样，`makeset` 也使用已排序的数据。 `makeset` 命令根据传入的行的顺序生成数组。

### <a name="expand-lists"></a>展开列表

`makelist` 或 `makeset` 的反运算是 `mv-expand`。 `mv-expand` 命令将值列表展开为多个单独行。 它可以展开任意数目的动态列（包括 JSON 和数组列）。 例如，可以在 `Heartbeat` 表中检查在过去一小时发送了检测信号的计算机中发送数据的解决方案：

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

输出如下：

| Computer | 解决方案 | 
|--------------|----------------------|
| computer1 | "security", "updates", "changeTracking" |
| computer2 | "security", "updates" |
| computer3 | "antiMalware", "changeTracking" |
| ... | ... |

使用 `mv-expand` 可以显示单独行（而不是逗号分隔列表）中的每个值：

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
```

输出如下：

| Computer | 解决方案 | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... |


可使用 `makelist` 将各个项组合在一起。 在输出中，可以查看每个解决方案的计算机列表：

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

输出如下：

|解决方案 | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

### <a name="missing-bins"></a>缺少箱

为缺失的箱填写默认值时，`mv-expand` 应用程序很有用。例如，假设你要通过浏览特定计算机的检测信号来查看该计算机的正常运行时间。 此外，你还希望查看 `Category` 列中检测信号的源。 通常，我们将使用基本的 `summarize` 语句：

```kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

输出如下：

| 类别 | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接代理 | 2017-06-06T17:00:00Z | 15 |
| 直接代理 | 2017-06-06T18:00:00Z | 60 |
| 直接代理 | 2017-06-06T20:00:00Z | 55 |
| 直接代理 | 2017-06-06T21:00:00Z | 57 |
| 直接代理 | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

在输出中，与“2017-06-06T19:00:00Z”关联的存储桶缺失，因为在这一小时内没有任何检测信号数据。 使用 `make-series` 函数将默认值赋给空存储桶。 为每个类别生成一行。 输出包含两个额外数组列，其中一个列包含值，另一个列包含匹配时间存储桶：

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

输出如下：

| 类别 | count_ | TimeGenerated |
|---|---|---|
| 直接代理 | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

count_ 数组的第三个元素为 0，与预期相同。 TimeGenerated 数组匹配的时间戳为“2017-06-06T19:00:00.0000000Z”。 但此数组的格式难以阅读。 使用 `mv-expand` 展开数组，并生成 `summarize` 所生成的相同格式输出：

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mv-expand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

输出如下：

| 类别 | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接代理 | 2017-06-06T17:00:00Z | 15 |
| 直接代理 | 2017-06-06T18:00:00Z | 60 |
| 直接代理 | 2017-06-06T19:00:00Z | 0 |
| 直接代理 | 2017-06-06T20:00:00Z | 55 |
| 直接代理 | 2017-06-06T21:00:00Z | 57 |
| 直接代理 | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



### <a name="narrow-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>将结果缩小为一组元素：let、makeset、toscalar、in   

一种常见的方案是基于一组条件选择特定实体的名称，然后将不同的数据集筛选为该实体集。 例如，可以查找已知缺少更新的计算机，并识别这些计算机调用的 IP 地址。

下面是一个示例：

```kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="joins"></a>联接

可使用联接在同一查询中分析来自多个表的数据。 联接通过匹配指定列的值来合并两个数据集的行。

下面是一个示例：

```kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

在本例中，第一个数据集筛选所有登录事件。 该数据集与筛选所有注销事件的第二个数据集进行联接。 投影的列有 `Computer`、`Account`、`TargetLogonId` 和 `TimeGenerated`。 数据集由共享列 `TargetLogonId` 进行关联。 输出实为具有登录时间和注销时间的记录，每个关联对应一条记录。

如果两个数据集都具有相同名称的列，则右侧数据集的列将会获得一个索引号。 在本例中，结果将显示来自左侧表的 `TargetLogonId` 及其值，以及来自右侧表的 `TargetLogonId1` 及其值。 在此情况下，已使用 `project-away` 运算符删除第二个 `TargetLogonId1`。

> [!NOTE]
> 为提高性能，请使用 `project` 运算符仅保留已联接的数据集的相关列。


请使用以下语法来联接两个数据集，其中所联接的键在两个表之间具有不同的名称：

```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>查找表

联接的一个常见用法是将 `datatable` 用于静态值映射。 使用 `datatable` 可使结果更加直观。 例如，可使用每个事件 ID 的事件名称来丰富安全事件数据：

```kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

输出如下：

| eventName | count_ |
|:---|:---|
| 关闭某个对象的句柄 | 290,995 |
| 请求某个对象的句柄 | 154,157 |
| 尝试复制某个对象的句柄 | 144,305 |
| 尝试访问某个对象 | 123,669 |
| 加密操作 | 153,495 |
| 重要文件操作 | 153,496 |

## <a name="json-and-data-structures"></a>JSON 和数据结构

嵌套的对象是指在键值对的数组或映射中包含其他对象的对象。 这些对象表示为 JSON 字符串。 本节介绍如何使用 JSON 来检索数据和分析嵌套的对象。

### <a name="work-with-json-strings"></a>使用 JSON 字符串

使用 `extractjson` 访问已知路径中的特定 JSON 元素。 此函数需要使用以下约定的路径表达式：

- 使用 $ 引用根文件夹。
- 使用括号/圆点表示法来表示索引和元素，如下例所示。


对索引使用括号，并使用圆点来分隔元素：

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

此示例有点相似，但它只使用括号表示法：

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

如果只有一个元素，则只能使用圆点表示法：

```kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>parsejson

要访问 JSON 结构中的多个元素，将其作为动态对象进行访问更为简单。 使用 `parsejson` 将文本数据强制转换为动态对象。 将 JSON 转换为动态类型后，可使用额外的函数来分析数据。

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```

### <a name="arraylength"></a>*arraylength*

使用 `arraylength` 计算数组中元素的数量：

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mv-expand"></a>*mv-expand*

使用 `mv-expand` 将对象的属性拆分到单独的行中：

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mv-expand hosts_object.hosts[0]
```

:::image type="content" source="images/samples/mvexpand-rows.png" alt-text="屏幕截图显示了带有位置值、状态值和速率值的 host_0。":::

### <a name="buildschema"></a>*buildschema*

使用 `buildschema` 获取允许对象的所有值的架构：

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

结果是 JSON 格式的架构：

```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```

此架构显示了对象字段的名称及其匹配的数据类型。 

嵌套的对象可能具有不同架构，如下例所示：

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>图表

以下各节将举例说明在使用 Kusto 查询语言时如何使用图表。

### <a name="chart-the-results"></a>将结果制成图表

首先查看在过去 1 小时内，每个操作系统占用的计算机数量：

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

默认情况下，以表形式显示结果：

:::image type="content" source="images/samples/query-results-table.png" alt-text="以表形式显示查询结果的屏幕截图。":::

要优化视图效果，请选择“图表”，然后选择“饼图”选项以直观显示结果 ：

:::image type="content" source="images/samples/query-results-pie-chart.png" alt-text="以饼图形式显示查询结果的屏幕截图。":::

### <a name="timecharts"></a>时间图表

显示处理器时间的平均值、第 50 位百分值和第 95 位百分位值（按 1 小时的箱数计）。 

下面的查询将生成多个序列。 在结果中，可选择要在时间表中显示哪些序列。

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

选择“折线图”显示选项：

:::image type="content" source="images/samples/multiple-series-line-chart.png" alt-text="显示多序列折线图的屏幕截图。":::

#### <a name="reference-line"></a>参考线

参考线可帮助你轻松识别指标是否超出特定阈值。 要向图表添加一行，请添加一个常数列来扩展数据集：

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

:::image type="content" source="images/samples/multiple-series-threshold-line-chart.png" alt-text="显示具有阈值参考线的多序列折线图的屏幕截图。":::


### <a name="multiple-dimensions"></a>多个维度

`summarize` 的 `by` 字句中的多个表达式在结果中创建多个行。 会为每个值组合创建一行。

```kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

当你以图表形式查看结果时，图表使用 `by` 子句的第一列。 以下示例演示使用 `EventID` 值创建的堆积柱形图。 维度必须为 `string` 类型。 在此示例中，`EventID` 值强制转换为 `string`：

:::image type="content" source="images/samples/select-column-chart-type-eventid.png" alt-text="显示基于 EventID 列的条形图的屏幕截图。":::

可通过选择列名的下拉箭头在列之间进行切换：

:::image type="content" source="images/samples/select-column-chart-type-accounttype.png" alt-text="显示基于 AccountType 列的条形图的屏幕截图，其中显示了列选择器。":::

## <a name="smart-analytics"></a>智能分析

本节包含使用 Azure Log Analytics 中的智能分析函数分析用户活动的示例。 可以使用这些示例来分析你自己的由 Azure Application Insights 监视的应用程序，或者使用这些查询中的概念来对其他数据进行类似的分析。 

### <a name="cohorts-analytics"></a>队列分析

队列分析跟踪特定用户组（称为队列）的活动。 队列分析通过测量回头用户的比率，尝试衡量服务的吸引力。 按照用户首次使用该服务的时间对用户进行分组。 分析队列时，预期会发现活动量相比前几个跟踪周期有所下降。 按照首次观测队列成员的周次来设置每个队列的标题。

以下示例分析用户在首次使用该服务后的 5 周内完成的活动数：

```kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user, we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity.
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time.
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after.
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample, we calculate distinct users but you can change
// this to any other metric that would measure the engagement of the cohort members.
| extend 
    r0 = DistinctUsers(startDate, startDate+7d),
    r1 = DistinctUsers(startDate, startDate+14d),
    r2 = DistinctUsers(startDate, startDate+21d),
    r3 = DistinctUsers(startDate, startDate+28d),
    r4 = DistinctUsers(startDate, startDate+35d)
| union (week | where Cohort == startDate + 7d 
| extend 
    r0 = DistinctUsers(startDate+7d, startDate+14d),
    r1 = DistinctUsers(startDate+7d, startDate+21d),
    r2 = DistinctUsers(startDate+7d, startDate+28d),
    r3 = DistinctUsers(startDate+7d, startDate+35d) )
| union (week | where Cohort == startDate + 14d 
| extend 
    r0 = DistinctUsers(startDate+14d, startDate+21d),
    r1 = DistinctUsers(startDate+14d, startDate+28d),
    r2 = DistinctUsers(startDate+14d, startDate+35d) )
| union (week | where Cohort == startDate + 21d 
| extend 
    r0 = DistinctUsers(startDate+21d, startDate+28d),
    r1 = DistinctUsers(startDate+21d, startDate+35d) ) 
| union (week | where Cohort == startDate + 28d 
| extend 
    r0 = DistinctUsers (startDate+28d, startDate+35d) )
// Calculate the retention percentage for each cohort by weeks
| project Cohort, r0, r1, r2, r3, r4,
          p0 = r0/r0*100,
          p1 = todouble(r1)/todouble (r0)*100,
          p2 = todouble(r2)/todouble(r0)*100,
          p3 = todouble(r3)/todouble(r0)*100,
          p4 = todouble(r4)/todouble(r0)*100 
| sort by Cohort asc
```

输出如下：

:::image type="content" source="images/samples/cohorts-table.png" alt-text="显示基于活动的队列表的屏幕截图。":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>滚动每月活动用户数和用户粘性

下面的示例将时间序列分析与 [series_fir](/azure/kusto/query/series-firfunction) 函数结合使用。 可使用 `series_fir` 函数进行滑动窗口计算。 受监视的示例应用程序是一个通过自定义事件跟踪用户活动的网上商店。 查询将跟踪两种类型的用户活动：`AddToCart` 和 `Checkout`。 如果某用户在特定日内至少完成一次结帐，该查询会将该用户定义为活动用户。

```kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they completed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked out in our website.
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user.
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column that contains a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin.
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day).
| mv-expand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity.
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// Render as timechart.
| render timechart
```

输出如下：

:::image type="content" source="images/samples/rolling-monthly-active-users-chart.png" alt-text="显示某月内按日期排序的滚动活动用户的图表的屏幕截图。":::

下面的示例将前面的查询转换为可重用的函数。 然后，该示例使用查询来计算滚动用户粘性。 此查询中的活动用户定义为该用户在特定日内至少完成一次结帐。

```kusto
let rollingDcount = (sliding_window_size: int, event_name:string)
{
    let endtime = endofday(datetime(2017-03-01T00:00:00Z));
    let window = 90d;
    let starttime = endtime-window;
    let interval = 1d;
    let moving_sum_filter = toscalar(range x from 1 to sliding_window_size step 1 | extend v=1| summarize makelist(v));    
    let min_activity = 1;
    customEvents
    | where timestamp > starttime
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    | where (name == event_name)
    | where user_AuthenticatedId <> ""
    | make-series UserClicks=count() default=0 on timestamp 
        in range(starttime, endtime-1s, interval) by user_AuthenticatedId
    | extend RollingUserClicks=fir(UserClicks, moving_sum_filter, false)
    | project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
    | mv-expand RollingUserClicksByDay
    | extend Timestamp=todatetime(RollingUserClicksByDay[0])
    | extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
    | summarize sum(RollingActiveUsersByDay) by Timestamp
    | where Timestamp > starttime + 28d
};
// Use the moving_sum_filter with bin size of 28 to count MAU.
rollingDcount(28, "Checkout")
| join
(
    // Use the moving_sum_filter with bin size of 1 to count DAU.
    rollingDcount(1, "Checkout")
)
on Timestamp
| project sum_RollingActiveUsersByDay1 *1.0 / sum_RollingActiveUsersByDay, Timestamp
| render timechart
```

输出如下：

:::image type="content" source="images/samples/user-stickiness-chart.png" alt-text="显示一段时间内用户粘性的图表的屏幕截图。":::

### <a name="regression-analysis"></a>回归分析

此示例演示如何专门根据应用程序的跟踪日志，为服务中断创建自动检测器。 该检测器会在应用程序的相对错误和警告跟踪数量中查找异常的突发性增长。

使用两种方法来根据跟踪日志数据评估服务状态：

- 使用 [make-series](/azure/kusto/query/make-seriesoperator) 将半结构化文本跟踪日志转换为指标，用于表示正跟踪线与负跟踪线之间的比率。
- 结合时序分析和双线线性回归，使用 [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) 和 [series_fit_line](/azure/kusto/query/series-fit-linefunction) 执行高级跳步检测。

```kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day.
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total.
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series.
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series.
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down.
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease).
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```

## <a name="next-steps"></a>后续步骤

- 演练 [Kusto 查询语言教程](tutorial.md?pivots=azuremonitor)。


::: zone-end

