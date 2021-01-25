---
title: 将数据导出到存储 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍了如何在 Azure 数据资源管理器中将数据导出到存储。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 03/12/2020
ms.date: 01/22/2021
ms.openlocfilehash: 91e94feed10f88baa9f1660fe2d91f7bb36145e2
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611644"
---
# <a name="export-data-to-storage"></a>将数据导出到存储

执行查询，将第一个结果集写入到通过[存储连接字符串](../../api/connection-strings/storage.md)指定的外部存储。

**语法**

`.export` [`async`] [`compressed`] `to` *OutputDataFormat*
`(` *StorageConnectionString* [`,` ...] `)` [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`] `<|` *Query*

**参数**

* `async`：如果指定了此项，则指示此命令以异步模式运行。
  若要更详细地了解此模式下的行为，请参阅下文。

* `compressed`：如果指定了此项，则输出存储项目将压缩为 `.gz` 文件。 请参阅 `compressionType`，了解如何将 parquet 文件压缩为 snappy。 

* OutputDataFormat：指示由此命令写入的存储项目的数据格式。 支持的值为 `csv`、`tsv`、`json` 和 `parquet`。

* StorageConnectionString：指定一个或多个[存储连接字符串](../../api/connection-strings/storage.md)，用于指示要将数据写入到其中的存储。 （为了实现可缩放写入，可以指定多个存储连接字符串。）每个这样的连接字符串都必须指明将数据写入存储时要使用的凭据。
  例如，在写入到 Azure Blob 存储时，凭据可以是存储帐户密钥，也可以是具有读取、写入和列出 blob 权限的共享访问密钥 (SAS)。

> [!NOTE]
> 强烈建议将数据导出到与 Kusto 群集本身位于同一区域中的存储。 其中包括已导出的数据，这样，这些数据便可以传输到其他区域中的其他云服务。 写入应在本地执行，而读取则可以远程进行。

* PropertyName/PropertyValue：零个或零个以上的可选导出属性：

|属性        |类型    |说明                                                                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------|
|`sizeLimit`     |`long`  |在压缩之前要写入的单个存储项目的大小限制（字节）。 允许的范围为 100MB（默认值）到 1GB。|
|`includeHeaders`|`string`|对于 `csv`/`tsv` 输出，此属性控制列标题的生成。 可以是 `none`（默认值；不发出标题行）、`all`（将标题行发出到每个存储项目中）或 `firstFile`（仅将标题行发出到第一个存储项目中）之一。|
|`fileExtension` |`string`|表示存储项目的“扩展名”部分（例如 `.csv` 或 `.tsv`）。 如果使用了压缩，还将追加 `.gz`。|
|`namePrefix`    |`string`|表示要添加到每个生成的存储项目名称前面的前缀。 如果未指定此项，将使用随机前缀。       |
|`encoding`      |`string`|表示文本编码方式：`UTF8NoBOM`（默认值）或 `UTF8BOM`。 |
|`compressionType`|`string`|表示要使用的压缩类型。 可能的值为 `gzip` 或 `snappy`。 默认值为 `gzip`。 `snappy` 可以（选择性地）用于 `parquet` 格式。 |
|`distribution`   |`string`  |分布提示（`single`、`per_node`、`per_shard`）。 如果值等于 `single`，则由单个线程将数据写入存储。 否则，导出会以并行方式从执行查询的所有节点进行写入。 请参阅 [evaluate 插件运算符](../../query/evaluateoperator.md)。 默认为 `per_shard`。
|`distributed`   |`bool`  |禁用/启用分布式导出。 设置为 false 等效于 `single` 分布提示。 默认值为 true。
|`persistDetails`|`bool`  |指示此命令应保留其结果（请参阅 `async` 标志）。 在异步运行中默认为 `true`，但如果调用方不需要结果，则可将其关闭。 在同步执行中默认为 `false`，但在这类执行中也可以开启。 |
|`parquetRowGroupSize`|`int`  |只有数据格式为 parquet 时才相关。 控制已导出文件中的行组大小。 默认的行组大小为 100000 条记录。|

**结果**

这些命令会返回一个表，该表描述了生成的存储项目。
每条记录描述一个项目，并包括项目的存储路径和该项目包含的数据记录数。

|`Path`|NumRecords|
|---|---|
|http://storage1.blob.core.chinacloudapi.cn/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10 个|
|http://storage1.blob.core.chinacloudapi.cn/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**异步模式**

如果指定了 `async` 标志，则命令将以异步模式执行。
在此模式下，命令会立即返回一个操作 ID，而数据导出则在后台继续进行，直到完成。 可以使用此命令返回的操作 ID 通过以下命令跟踪其进度并最终跟踪其结果：

* `.show operations`[](../operations.md#show-operations)：跟踪进度。
* `.show operation details`[](../operations.md#show-operation-details)：获取完成结果。

例如，在成功完成后，可使用以下命令来检索结果：

```kusto
.show operation f008dc1e-2710-47d8-8d34-0d562f5f8615 details
```

**示例** 

在此示例中，Kusto 运行查询，然后将查询生成的第一个记录集导出到一个或多个压缩的 CSV blob。
列名标签添加为每个 blob 的第一行。

```kusto 
.export
  async compressed
  to csv (
    h@"https://storage1.blob.core.chinacloudapi.cn/containerName;secretKey",
    h@"https://storage1.blob.core.chinacloudapi.cn/containerName2;secretKey"
  ) with (
    sizeLimit=100000,
    namePrefix=export,
    includeHeaders=all,
    encoding =UTF8NoBOM
  )
  <| myLogs | where id == "moshe" | limit 10000
```

## <a name="failures-during-export-commands"></a>执行 export 命令期间发生故障

export 命令在执行期间可能会暂时失败。 [连续导出](continuous-data-export.md)会自动重试此命令。 常规 export 命令（[导出到存储](export-data-to-storage.md)、[导出到外部表](export-data-to-an-external-table.md)）不执行任何重试操作。

*  如果 export 命令失败，不会删除已写入到存储的项目。 这些项目将保留在存储中。 如果该命令失败，应假定导出不完整（即使已写入了一些项目）。 
* 若要跟踪命令完成情况和成功完成时导出的项目，最好使用 [`.show operations`](../operations.md#show-operations) 和 [`.show operation details`](../operations.md#show-operation-details) 命令。

### <a name="storage-failures"></a>存储故障

默认情况下会对 export 命令进行分配，从而可能会有许多针对存储的并发写入。 分配级别取决于 export 命令的类型：
* 常规 `.export` 命令的默认分配为 `per_shard`，这意味着所有包含要导出数据的[区](../extents-overview.md)都以并发方式写入到存储。 
* [导出到外部表](export-data-to-an-external-table.md)命令的默认分配为 `per_node`，这意味着并发度是指群集中的节点数。

当区/节点的数量很大时，可能会导致存储中的负载过高，从而导致发生存储限制或出现暂时性存储错误。 以下建议可能会解决这些错误（按优先级顺序）：

* 增加为 export 命令或[外部表定义](../external-tables-azurestorage-azuredatalake.md)提供的存储帐户数（负载会在帐户之间均匀分配）。
* 通过将分配提示设置为 `per_node` 来降低并发度（请参阅命令属性）。
* 通过将[客户端请求属性](../../api/netfx/request-properties.md) `query_fanout_nodes_percent` 设置为所需的并发度（节点百分比）来降低进行导出的节点的并发度。 可以在导出查询中设置此属性。 例如，以下命令会将以并发方式写入到存储的节点的数目限制为群集节点的 50%：

    ```kusto
    .export async  to csv
        ( h@"https://storage1.blob.core.chinacloudapi.cn/containerName;secretKey" ) 
        with
        (
            distribution="per_node"
        ) 
        <| 
        set query_fanout_nodes_percent = 50;
        ExportQuery
    ```

* 如果要导出到分区的外部表，则设置 `spread`/`concurrency` 属性可以降低并发度（请参阅[命令属性](export-data-to-an-external-table.md#syntax)中的详细信息）。
* 如果上述方式都不起作用，则也可通过将 `distributed` 属性设置为 false 来完全禁用分配，但我们不建议这样做，因为这可能会显著影响命令性能。
