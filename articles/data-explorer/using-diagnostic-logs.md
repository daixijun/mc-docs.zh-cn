---
title: 使用诊断日志监视 Azure 数据资源管理器的引入、命令和查询
description: 了解如何设置诊断日志，使 Azure 数据资源管理器能够监视引入、命令和查询操作。
author: orspod
ms.author: v-tawe
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: how-to
origin.date: 09/18/2019
ms.date: 01/22/2021
ms.openlocfilehash: 809feb3f648febf7efaf10e80d268eb597a31f4c
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611279"
---
# <a name="monitor-azure-data-explorer-ingestion-commands-queries-and-tables-using-diagnostic-logs"></a>使用诊断日志监视 Azure 数据资源管理器的引入、命令、查询和表

Azure 数据资源管理器是一项快速、完全托管的数据分析服务，用于实时分析从应用程序、网站和 IoT 设备等资源流式传输的海量数据。 [Azure Monitor 诊断日志](/azure/azure-monitor/platform/diagnostic-logs-overview)提供有关 Azure 资源操作的数据。 Azure 数据资源管理器使用诊断日志获取有关引入、命令、查询和表的见解。 可将操作日志导出到 Azure 存储、事件中心或 Log Analytics 以监视引入、命令和查询状态。 可将 Azure 存储和 Azure 事件中心的日志路由到 Azure 数据资源管理器群集中的某个表，以进一步分析。

> [!IMPORTANT] 
> 诊断日志数据可能包含敏感数据。 请根据监视需求限制日志目标的权限。 

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请创建一个[试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。
* 登录到 [Azure 门户](https://portal.azure.cn/)。
* 创建[群集和数据库](create-cluster-database-portal.md)。

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>设置 Azure 数据资源管理器群集的诊断日志

诊断日志可用于配置以下日志数据的收集：

# <a name="ingestion"></a>[引流](#tab/ingestion)

> [!NOTE]
> 对于使用 SDK、数据连接和连接器将排队的引入内容引入到引入终结点，引入日志受支持。
>
> 对于流式引入、目标为引擎的直接引入、从查询进行的引入或者设置或追加命令，引入日志不受支持。

> [!NOTE]
> 只会针对引入操作的最终状态报告“失败引入”日志，这与[引入结果](using-metrics.md#ingestion-metrics)指标不同，后者是针对在内部重试的暂时性故障发出的。

* **成功的引入操作**：这些日志包含有关已成功完成的引入操作的信息。
* **失败的引入操作**：这些日志包含有关失败的引入操作的详细信息，包括错误详细信息。 
* 引入批处理操作：这些日志详细说明了可用于引入的批处理的统计信息（持续时间、批大小和 blob 计数）。

# <a name="commands-and-queries"></a>[命令和查询](#tab/commands-and-queries)

* **命令**：这些日志包含已达到最终状态的管理命令的相关信息。
* **查询**：这些日志包含已达到最终状态的查询的相关详细信息。 

    > [!NOTE]
    > 查询日志数据不包含查询文本。
    
# <a name="tables"></a>[表](#tab/tables)

* TableUsageStatistics：这些日志包含已达到最终状态的命令和查询的详细使用情况信息。

    > [!NOTE]
    > `TableUsageStatistics` 日志数据不包含命令或查询文本。

* TableDetails：这些日志包含有关群集的表的详细信息。

---

然后可根据规范将数据存档到存储帐户、流式传输到事件中心，或发送到 Log Analytics。

### <a name="enable-diagnostic-logs"></a>启用诊断日志

诊断日志默认已禁用。 若要启用诊断日志，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.cn)中，选择要监视的 Azure 数据资源管理器群集资源。
1. 在“监视”下，选择“诊断设置”   。
  
    ![添加诊断日志](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. 选择“添加诊断设置”。 
1. 在“诊断设置”窗口中，执行以下操作：

    :::image type="content" source="media/using-diagnostic-logs/configure-diagnostics-settings.png" alt-text="配置诊断设置":::

    1. 输入一个诊断设置名称。
    1. 选择一个或多个目标：Log Analytics 工作区、存储帐户或事件中心。
    1. 选择要收集的日志：`SucceededIngestion`、`FailedIngestion`、`IngestionBatching`、`Command`、`Query`、`TableUsageStatistics` 或 `TableDetails`。
    1. 选择要收集的[指标](using-metrics.md#supported-azure-data-explorer-metrics)（可选）。  
    1. 选择“保存”以保存新的诊断日志设置和指标。 

在几分钟内即会完成新的设置。 日志随后会显示在配置的存档目标（存储帐户、事件中心或 Log Analytics）中。 

> [!NOTE]
> 如果将日志发送到 Log Analytics，则 `SucceededIngestion`、`FailedIngestion`、`IngestionBatching`、`Command`、`Query`、`TableUsageStatistics` 和 `TableDetails` 日志会分别存储在名为 `SucceededIngestion`、`FailedIngestion`、`ADXIngestionBatching`、`ADXCommand`、`ADXQuery`、`ADXTableUsageStatistics` 和 `ADXTableDetails` 的 Log Analytics 表中。

## <a name="diagnostic-logs-schema"></a>诊断日志架构

所有 [Azure Monitor 诊断日志共享一个通用的顶级架构](/azure-monitor/platform/diagnostic-logs-schema)。 Azure 数据资源管理器对其自身的事件使用唯一属性。 所有日志均以 JSON 格式存储。

# <a name="ingestion"></a>[引流](#tab/ingestion)

### <a name="ingestion-logs-schema"></a>引入日志架构

日志 JSON 字符串包含下表中列出的元素：

|名称               |说明
|---                |---
|time               |报告时间
|ResourceId         |Azure Resource Manager 资源 ID
|operationName      |操作名称：'MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION'
|operationVersion   |架构版本：'1.0' 
|category           |操作类别。 `SucceededIngestion`、`FailedIngestion` 或 `IngestionBatching`。 [成功的操作](#successful-ingestion-operation-log)、[失败的操作](#failed-ingestion-operation-log)或[批处理操作](#ingestion-batching-operation-log)的属性不同。
|properties         |操作的详细信息。

#### <a name="successful-ingestion-operation-log"></a>成功引入操作日志

**示例：**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "SucceededOn": "2019-05-27 07:55:05.3693628",
        "OperationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "Database": "Samples",
        "Table": "StormEvents",
        "IngestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "IngestionSourcePath": "https://kustoingestionlogs.blob.core.chinacloudapi.cn/sampledata/events8347293.json",
        "RootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**成功操作诊断日志的属性**

|名称               |说明
|---                |---
|SucceededOn        |引入完成时间
|OperationId        |Azure 数据资源管理器引入操作 ID
|数据库           |目标数据库的名称
|表              |目标表的名称
|IngestionSourceId  |引入数据源的 ID
|IngestionSourcePath|引入数据源或 Blob URI 的路径
|RootActivityId     |活动 ID

#### <a name="failed-ingestion-operation-log"></a>失败引入操作日志

**示例：**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.chinacloudapi.cn/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**失败操作诊断日志的属性**

|名称               |说明
|---                |---
|FailedOn           |引入完成时间
|OperationId        |Azure 数据资源管理器引入操作 ID
|数据库           |目标数据库的名称
|表              |目标表的名称
|IngestionSourceId  |引入数据源的 ID
|IngestionSourcePath|引入数据源或 Blob URI 的路径
|RootActivityId     |活动 ID
|详细信息            |失败和错误消息的详细说明
|ErrorCode          |错误代码 
|FailureStatus      |`Permanent` 或 `Transient`。 重试暂时性故障可能会成功。
|OriginatesFromUpdatePolicy|如果故障源自更新策略，则为 True
|ShouldRetry        |如果重试可以成功，则为 True

#### <a name="ingestion-batching-operation-log"></a>引入批处理操作日志

**示例：**

```json
{
  "resourceId": "/SUBSCRIPTIONS/12534EB3-8109-4D84-83AD-576C0D5E1D06/RESOURCEGROUPS/KEREN/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KERENEUS",
  "time": "2020-05-27T07:55:05.3693628Z",
  "operationVersion": "1.0",
  "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGESTIONBATCHING/ACTION",
  "category": "IngestionBatching",
  "correlationId": "2bb51038-c7dc-4ebd-9d7f-b34ece4cb735",
  "properties": {
    "Database": "Samples",
    "Table": "StormEvents",
    "BatchingType": "Size",
    "SourceCreationTime": "2020-05-27 07:52:04.9623640",
    "BatchTimeSeconds": 215.5,
    "BatchSizeBytes": 2356425,
    "DataSourcesInBatch": 4,
    "RootActivityId": "2bb51038-c7dc-4ebd-9d7f-b34ece4cb735"
  }
}

```
**引入批处理操作诊断日志的属性**

|名称               |说明
|---                   |---
| TimeGenerated        | 生成此事件的时间 (UTC) |
| 数据库             | 保存目标表的数据库的名称 |
| 表                | 数据引入到的目标表的名称 |
| BatchingType         | 批处理类型：批处理是否达到批处理策略设置的批处理时间、数据大小或文件数限制 |
| SourceCreationTime   | 此批中 blob 的最早创建时间 (UTC) |
| BatchTimeSeconds     | 此批的总批处理时间（秒） |
| BatchSizeBytes       | 此批中数据的未压缩大小总计（字节） |
| DataSourcesInBatch   | 此批中的数据源数 |
| RootActivityId       | 操作的活动 ID |


# <a name="commands-and-queries"></a>[命令和查询](#tab/commands-and-queries)

### <a name="commands-and-queries-logs-schema"></a>命令和查询日志架构

日志 JSON 字符串包含下表中列出的元素：

|名称               |说明
|---                |---
|time               |报告时间
|ResourceId         |Azure Resource Manager 资源 ID
|operationName      |操作名称：“MICROSOFT.KUSTO/CLUSTERS/COMMAND/ACTION”或“MICROSOFT.KUSTO/CLUSTERS/QUERY/ACTION”。 命令和查询日志的属性不同。
|operationVersion   |架构版本：'1.0' 
|category           |操作类别：`Command` 或 `Query`。 命令和查询日志的属性不同。
|properties         |操作的详细信息。

#### <a name="command-log"></a>命令日志

**示例：**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/COMMAND/ACTION",
    "operationVersion": "1.0",
    "category": "Command",
    "properties":
    {
        "RootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d",
        "StartedOn": "2020-09-12T18:06:04.6898353Z",
        "LastUpdatedOn": "2020-09-12T18:06:04.6898353Z",
        "Database": "DatabaseSample",
        "State": "Completed",
        "FailureReason": "XXX",
        "TotalCpu": "00:01:30.1562500",
        "CommandType": "ExtentsDrop",
        "Application": "Kusto.WinSvc.DM.Svc",
        "ResourceUtilization": "{\"CacheStatistics\":{\"Memory\":{\"Hits\":0,\"Misses\":0},\"Disk\":{\"Hits\":0,\"Misses\":0},\"Shards\":{\"Hot\":{\"HitBytes\":0,\"MissBytes\":0,\"RetrieveBytes\":0},\"Cold\":{\"HitBytes\":0,\"MissBytes\":0,\"RetrieveBytes\":0},\"BypassBytes\":0}},\"TotalCpu\":\"00:00:00\",\"MemoryPeak\":0,\"ScannedExtentsStatistics\":{\"MinDataScannedTime\":null,\"MaxDataScannedTime\":null,\"TotalExtentsCount\":0,\"ScannedExtentsCount\":0,\"TotalRowsCount\":0,\"ScannedRowsCount\":0}}",
        "Duration": "00:03:30.1562500",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a3b4136b53;5c443533-c927-4410-a5d6-4d6a5443b64f"
    }
}
```
**命令诊断日志的属性**

|名称               |说明
|---                |---
|RootActivityId |根活动 ID
|StartedOn        |该命令开始的时间 (UTC)
|LastUpdatedOn        |该命令结束的时间 (UTC)
|数据库          |运行命令的数据库的名称
|状态              |命令结束的状态
|FailureReason  |失败原因
|TotalCpu |CPU 总持续时间
|CommandType     |命令类型
|应用程序     |调用了命令的应用程序的名称
|ResourceUtilization     |命令资源利用率
|Duration     |命令持续时间
|用户     |调用了查询的用户
|主体     |调用了查询的主体

#### <a name="query-log"></a>查询日志

**示例：**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/QUERY/ACTION",
    "operationVersion": "1.0",
    "category": "Query",
    "properties": {
        "RootActivityId": "3e6e8814-e64f-455a-926d-bf16229f6d2d",
        "StartedOn": "2020-09-04T13:45:45.331925Z",
        "LastUpdatedOn": "2020-09-04T13:45:45.3484372Z",
        "Database": "DatabaseSample",
        "State": "Completed",
        "FailureReason": "[none]",
        "TotalCpu": "00:00:00",
        "ApplicationName": "MyApp",
        "MemoryPeak": 0,
        "Duration": "00:00:00.0165122",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a8b4132b53;5c823e4d-c927-4010-a2d8-6dda2449b6cf",
        "ScannedExtentsStatistics": {
            "MinDataScannedTime": "2020-07-27T08:34:35.3299941",
            "MaxDataScannedTime": "2020-07-27T08:34:41.991661",
            "TotalExtentsCount": 64,
            "ScannedExtentsCount": 64,
            "TotalRowsCount": 320,
            "ScannedRowsCount": 320
        },
        "CacheStatistics": {
            "Memory": {
                "Hits": 192,
                "Misses": 0
          },
            "Disk": {
                "Hits": 0,
                "Misses": 0
      },
            "Shards": {
                "Hot": {
                    "HitBytes": 0,
                    "MissBytes": 0,
                    "RetrieveBytes": 0
        },
                "Cold": {
                    "HitBytes": 0,
                    "MissBytes": 0,
                    "RetrieveBytes": 0
        },
            "BypassBytes": 0
      }
    },
    "ResultSetStatistics": {
        "TableCount": 1,
        "TablesStatistics": [
        {
          "RowCount": 1,
          "TableSize": 9
        }
      ]
    }
  }
}
```

**查询诊断日志的属性**

|名称               |说明
|---                |---
|RootActivityId |根活动 ID
|StartedOn        |该命令开始的时间 (UTC)
|LastUpdatedOn           |该命令结束的时间 (UTC)
|数据库              |运行命令的数据库的名称
|状态  |命令结束的状态
|FailureReason|失败原因
|TotalCpu     |CPU 总持续时间
|ApplicationName            |调用了查询的应用程序的名称
|MemoryPeak          |内存峰值
|Duration      |命令持续时间
|用户|调用了查询的用户
|主体        |调用了查询的主体
|ScannedExtentsStatistics        | 包含扫描的盘区统计信息
|MinDataScannedTime        |最短数据扫描时间
|MaxDataScannedTime        |最长数据扫描时间
|TotalExtentsCount        |盘区总数
|ScannedExtentsCount        |扫描的盘区计数
|TotalRowsCount        |总行计数
|ScannedRowsCount        |扫描的行计数
|CacheStatistics        |包含缓存统计信息
|内存        |包含缓存内存统计信息
|命中数        |内存缓存命中数
|未命中数        |内存缓存未命中数
|磁盘        |包含缓存磁盘统计信息
|命中数        |磁盘缓存命中数
|未命中数        |磁盘缓存未命中数
|Shards        |包含热和冷分片缓存统计信息
|热        |包含热分片缓存统计信息
|HitBytes        |分片热缓存命中数
|MissBytes        |分片热缓存未命中数
|RetrieveBytes        | 分片热缓存检索的字节数
|冷        |包含冷分片缓存统计信息
|HitBytes        |分片冷缓存命中数
|MissBytes        |分片冷缓存未命中数
|RetrieveBytes        |分片冷缓存检索的字节数
|BypassBytes        |分片缓存绕过字节数
|ResultSetStatistics        |包含结果集统计信息
|TableCount        |结果集表计数
|TablesStatistics        |包含结果集表统计信息
|RowCount        | 结果集表行计数
|TableSize        |结果集表行计数


# <a name="tables"></a>[表](#tab/tables)

### <a name="tableusagestatistics-and-tabledetails-logs-schema"></a>TableUsageStatistics 和 TableDetails 日志架构

日志 JSON 字符串包含下表中列出的元素：

|名称               |说明
|---                |---
|time               |报告时间
|ResourceId         |Azure Resource Manager 资源 ID
|operationName      |操作名称：“MICROSOFT.KUSTO/CLUSTERS/DATABASE/SCHEMA/READ”。 TableUsageStatistics 和 TableDetails 的属性相同。
|operationVersion   |架构版本：'1.0' 
|properties         |操作的详细信息

#### <a name="tableusagestatistics-log"></a>TableUsageStatistics 日志

**示例：**

```json
{
    "resourceId": "/SUBSCRIPTIONS/0571b364-eeeb-4f28-ba74-90a8b4132b53/RESOURCEGROUPS/MYRG/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/MYKUSTOCLUSTER",
    "time": "08-04-2020 16:42:29",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/DATABASE/SCHEMA/READ",
    "correlationId": "MyApp.Kusto.DM.MYKUSTOCLUSTER.ShowTableUsageStatistics.e10fe80b-6f4d-4b7e-9756-b87720f88901",
    "properties": {
        "RootActivityId": "3e6e8814-e64f-455a-926d-bf16229f6d2d",
        "StartedOn": "2020-08-19T11:51:41.1258308Z",
        "Database": "MyDB",
        "Table": "MyTable",
        "MinCreatedOn": "2020-07-20T09:16:00.9906347Z",
        "MaxCreatedOn": "2020-08-19T11:50:37.1233374Z",
        "Application": "MyApp",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a8b4132b53;5c823e4d-c927-4010-a2d8-6dda2449b6cf"
    }
}
```

**TableUsageStatistics 诊断日志的属性**

|名称               |说明
|---                |---
|RootActivityId |根活动 ID
|StartedOn        |该命令开始的时间 (UTC)
|数据库          |数据库的名称
|TableName              |表的名称
|MinCreatedOn  |表的最早区时间
|MaxCreatedOn |表的最新区时间
|ApplicationName     |调用了该命令的应用程序的名称
|用户     |调用了查询的用户
|主体     |调用了查询的主体

#### <a name="tabledetails-log"></a>TableDetails 日志

**示例：**

```json
{
    "resourceId": "/SUBSCRIPTIONS/0571b364-eeeb-4f28-ba74-90a8b4132b53/RESOURCEGROUPS/MYRG/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/MYKUSTOCLUSTER",
    "time": "08-04-2020 16:42:29",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/DATABASE/SCHEMA/READ",
    "correlationId": "MyApp.Kusto.DM.MYKUSTOCLUSTER.ShowTableUsageStatistics.e10fe80b-6f4d-4b7e-9756-b87720f88901",
    "properties": {
        "RootActivityId": "3e6e8814-e64f-455a-926d-bf16229f6d2d",
        "TableName": "MyTable",
        "DatabaseName": "MyDB",
        "TotalExtentSize": 9632.0,
        "TotalOriginalSize": 4143.0,
        "HotExtentSize": 0.0,
        "RetentionPolicyOrigin": "table",
        "RetentionPolicy": "{\"SoftDeletePeriod\":\"90.00:00:00\",\"Recoverability\":\"Disabled\"}",
        "CachingPolicyOrigin": "database",
        "CachingPolicy": "{\"DataHotSpan\":\"7.00:00:00\",\"IndexHotSpan\":\"7.00:00:00\",\"ColumnOverrides\":[]}",
        "MaxExtentsCreationTime": "2020-08-30T02:44:43.9824696Z",
        "MinExtentsCreationTime": "2020-08-30T02:38:42.3031288Z",
        "TotalExtentCount": 1164,
        "TotalRowCount": 223325,
        "HotExtentCount": 29,
        "HotOriginalSize": 1388213,
        "HotRowCount": 5117
  }
}
```

**TableDetails 诊断日志的属性**

|名称               |说明
|---                |---
|RootActivityId |根活动 ID
|TableName        |表的名称
|DatabaseName           |数据库的名称
|TotalExtentSize              |表中数据的原始总大小（以字节为单位）
|HotExtentSize  |表中的区（存储在热缓存中）的总大小（压缩大小和索引大小），以字节为单位。
|RetentionPolicyOrigin |保留策略源实体（表/数据库/群集）
|RetentionPolicy     |表的有效实体保留策略，已序列化为 JSON
|CachingPolicyOrigin            |缓存策略源实体（表/数据库/群集）
|CachingPolicy          |表的有效实体缓存策略，已序列化为 JSON
|MaxExtentsCreationTime      |表中的区的最大创建时间（如果没有区，则为 NULL）
|MinExtentsCreationTime |表中的区的最小创建时间（如果没有区，则为 NULL）
|TotalExtentCount        |表中的总区数
|TotalRowCount        |表中的总行数
|MinDataScannedTime        |最短数据扫描时间
|MaxDataScannedTime        |最长数据扫描时间
|TotalExtentsCount        |盘区总数
|ScannedExtentsCount        |扫描的盘区计数
|TotalRowsCount        |总行计数
|HotExtentCount        |表中的区（存储在热缓存中）的总数
|HotOriginalSize        |表中的数据（存储在热缓存中）的原始总大小（以字节为单位）
|HotRowCount        |表中的行（存储在热缓存中）的总数

---

## <a name="next-steps"></a>后续步骤

* [使用指标来监视群集运行状况](using-metrics.md)
* [教程：在 Azure 数据资源管理器中引入和查询监视数据](ingest-data-no-code.md)，可帮助获取引入诊断日志
