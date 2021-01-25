---
title: 从事件中心引入 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的“从事件中心引入”功能。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
origin.date: 08/13/2020
ms.date: 01/19/2021
ms.openlocfilehash: 32ad1d3364744cf3454fc86fb8ea6c002ebf017b
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611540"
---
# <a name="event-hub-data-connection"></a>事件中心数据连接

[Azure 事件中心](/event-hubs/event-hubs-about)是大数据流式处理平台和事件引入服务。 Azure 数据资源管理器通过客户管理的事件中心提供持续引入。

事件中心引入管道使用几个步骤将事件传输到 Azure 数据资源管理器。 首先，在 Azure 门户中创建事件中心。 然后，创建 Azure 数据资源管理器目标表，使用给定的[引入属性](#ingestion-properties)将[特定格式的数据](#data-format)引入到该表中。 事件中心连接需要知道[事件路由](#events-routing)。 根据[事件系统属性映射](#event-system-properties-mapping)，使用选定的属性嵌入数据。 [创建到事件中心的连接](#event-hub-connection)，以[创建事件中心](#create-an-event-hub)并[发送事件](#send-events)。 可以通过 [Azure 门户](ingest-data-event-hub.md)使用 [C#](data-connection-event-hub-csharp.md) 或 [Python](data-connection-event-hub-python.md) 以编程方式管理此过程，也可以使用 [Azure 资源管理器模板](data-connection-event-hub-resource-manager.md)来这样做。

有关 Azure 数据资源管理器中数据引入的常规信息，请参阅 [Azure 数据资源管理器数据引入概述](ingest-data-overview.md)。

## <a name="data-format"></a>数据格式

* 将以 [EventData](https://docs.microsoft.com/dotnetapi/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 对象的形式从事件中心读取数据。
* 请参阅[支持的格式](ingestion-supported-formats.md)。
    > [!NOTE]
    > 事件中心不支持 .raw 格式。

* 可使用 `GZip` 压缩算法来压缩数据。 指定[引入属性](#ingestion-properties)中的 `Compression`。
   * 压缩格式（Avro、Parquet、ORC）不支持数据压缩。
   * 压缩数据不支持自定义编码和嵌入式[系统属性](#event-system-properties-mapping)。
  
## <a name="ingestion-properties"></a>引入属性

引入属性会指示引入过程、数据路由到的位置以及数据处理方式。 可以使用 [EventData.Properties](https://docs.microsoft.com/dotnetapi/microsoft.servicebus.messaging.eventdata.properties#Microsoft_ServiceBus_Messaging_EventData_Properties) 指定事件引入的[引入属性](ingestion-properties.md)。 可以设置以下属性：

|属性 |说明|
|---|---|
| 表 | 现有目标表的名称（区分大小写）。 替代“`Data Connection`”窗格上设置的“`Table`”。 |
| 格式 | 数据格式。 替代“`Data Connection`”窗格上设置的“`Data format`”。 |
| IngestionMappingReference | 要使用的现有[引入映射](kusto/management/create-ingestion-mapping-command.md)的名称。 替代“`Data Connection`”窗格上设置的“`Column mapping`”。|
| 压缩 | 数据压缩。`None`（默认值）或 `GZip` 压缩。|
| 编码 | 数据编码，默认值为 UTF8。 可以是 [.NET 支持的任何编码](https://docs.microsoft.com/dotnet/api/system.text.encoding#remarks)。 |
| Tags | 将要与引入的数据（格式设置为 JSON 数组字符串）关联的[标记](kusto/management/extents-overview.md#extent-tagging)的列表。 使用标记时存在[性能影响](kusto/management/extents-overview.md#performance-notes-1)。 |

> [!NOTE]
> 只有创建数据连接后进入队列的事件才会被引入。

## <a name="events-routing"></a>事件路由

设置到 Azure 数据资源管理器群集的事件中心连接时，请指定目标表属性（表名、数据格式、压缩和映射）。 数据的默认路由也称为 `static routing`。
还可以使用事件属性指定每个事件的目标表属性。 连接将按照 [EventData.Properties](https://docs.microsoft.com/dotnetapi/microsoft.servicebus.messaging.eventdata.properties#Microsoft_ServiceBus_Messaging_EventData_Properties) 中指定的要求动态路由数据，替代此事件的静态属性。

在以下示例中设置事件中心详细信息，并将天气指标数据发送到 `WeatherMetrics` 表。
数据采用 `json` 格式。 `mapping1` 在 `WeatherMetrics` 表中预定义。

```csharp
var eventHubNamespaceConnectionString=<connection_string>;
var eventHubName=<event_hub>;

// Create the data
var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = 32 }; 
var data = JsonConvert.SerializeObject(metric);

// Create the event and add optional "dynamic routing" properties
var eventData = new EventData(Encoding.UTF8.GetBytes(data));
eventData.Properties.Add("Table", "WeatherMetrics");
eventData.Properties.Add("Format", "json");
eventData.Properties.Add("IngestionMappingReference", "mapping1");
eventData.Properties.Add("Tags", "['mydatatag']");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>事件系统属性映射

系统属性在事件排队时存储由事件中心服务设置的属性。 Azure 数据资源管理器事件中心连接会将所选属性嵌入置于表中的数据中。

[!INCLUDE [event-hub-system-mapping](includes/event-hub-system-mapping.md)]

### <a name="system-properties"></a>系统属性

事件中心公开以下系统属性：

|属性 |数据类型 |说明|
|---|---|---|
| x-opt-enqueued-time |datetime | 将事件排队时的 UTC 时间 |
| x-opt-sequence-number |long | 事件中心分区流中的事件的逻辑序列号
| x-opt-offset |string | 事件与事件中心分区流之间的偏移量。 偏移量标识符在事件中心流的分区中独一无二 |
| x-opt-publisher |string | 发布服务器名称（如果消息已发送到发布服务器终结点） |
| x-opt-partition-key |string |存储了事件的相应分区的分区键 |

如果在表的“数据源”部分中选择了“事件系统属性”，则必须在表架构和映射中包含这些属性。

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="event-hub-connection"></a>事件中心连接

> [!Note]
> 为了获得最佳性能，请在 Azure 数据资源管理器群集所在的区域中创建所有资源。

### <a name="create-an-event-hub"></a>创建事件中心

[创建事件中心](/event-hubs/event-hubs-create)（如果还没有事件中心）。 可以通过 [Azure 门户](ingest-data-event-hub.md)使用 [C#](data-connection-event-hub-csharp.md) 或 [Python](data-connection-event-hub-python.md) 以编程方式管理到事件中心的连接，也可以使用 [Azure 资源管理器模板](data-connection-event-hub-resource-manager.md)来这样做。


> [!Note]
> * 分区计数不可更改，因此在设置分区计数时应考虑长期规模。
> * 使用者组对于每个使用者来说必须独一无二。 创建专用于 Azure 数据资源管理器连接的使用者组。

## <a name="send-events"></a>发送事件

请参阅可生成数据并将其发送到事件中心的[示例应用](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)。

有关如何生成示例数据的示例，请参阅[将数据从事件中心引入到 Azure 数据资源管理器](ingest-data-event-hub.md#generate-sample-data)

## <a name="set-up-geo-disaster-recovery-solution"></a>设置异地灾难恢复解决方案

事件中心提供[异地灾难恢复](/event-hubs/event-hubs-geo-dr)解决方案。 Azure 数据资源管理器不支持 `Alias` 事件中心命名空间。 若要在解决方案中实现异地灾难恢复，请创建两个事件中心数据连接：一个用于主命名空间，另一个用于辅助命名空间。 Azure 数据资源管理器将侦听这两个事件中心连接。

> [!NOTE]
> 用户负责实现从主命名空间到辅助命名空间的故障转移。

## <a name="next-steps"></a>后续步骤

* [将数据从事件中心引入到 Azure 数据资源管理器](ingest-data-event-hub.md)
* [使用 C# 为 Azure 数据资源管理器创建事件中心数据连接](data-connection-event-hub-csharp.md)
* [使用 Python 为 Azure 数据资源管理器创建事件中心数据连接](data-connection-event-hub-python.md)
* [使用 Azure 资源管理器模板为 Azure 数据资源管理器创建事件中心数据连接](data-connection-event-hub-resource-manager.md)
