---
title: ingest inline 命令（推送）- Azure 数据资源管理器
description: 本文介绍 .ingest inline 命令（推送）。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 03/24/2020
ms.date: 01/22/2021
ms.openlocfilehash: 12c1f99370f215b7f6d79d22918ce9b60f8c74ab
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611334"
---
# <a name="ingest-inline-command-push"></a>.ingest inline 命令（推送）

此命令通过“推送”内嵌在命令文本本身中的数据将数据引入到表中。

> [!NOTE]
> 此命令用于手动临时测试。
> 对于生产使用，我们建议你使用其他更适合批量传送大量数据的引入方法，例如[从存储引入](./ingest-from-storage.md)。

**语法**

`.ingest` `inline` `into` `table` *TableName* [`with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [`,` ...] `)`] `<|` *Data*

**参数**

* TableName 是要将数据引入到其中的表的名称。
  该名称始终与上下文中的数据库相关。
  如果没有提供架构映射对象，则表架构将是为数据假定的架构。

* Data 是要引入的数据内容。 除非已通过引入属性进行其他修改，否则此内容将作为 CSV 进行分析。
 
 > [!NOTE]
 > 与大多数控制命令和查询不同，此命令的 Data 部分的文本不一定要遵循语言的语法约定。 例如，空格字符很重要，否则系统不会将 `//` 组合视为注释。

* IngestionPropertyName、IngestionPropertyValue：影响引入过程的任意数量的[引入属性](../../../ingestion-properties.md)。

**结果**

结果是一个表，其中包含的记录数目与生成的数据分片（“盘区”）的数目相同。
如果未生成任何数据分片，则返回一条带有空（零值）盘区 ID 的记录。

|名称       |类型      |说明                                                               |
|-----------|----------|--------------------------------------------------------------------------|
|ExtentId   |`guid`    |该命令生成的数据分片的唯一标识符|

**示例**

以下命令将数据引入到包含 `SKU`（类型为 `string`）和 `Quantity`（类型为 `long`）两列的表 (`Purchases`)。

```kusto
.ingest inline into table Purchases <|
Shoes,1000
Wide Shoes,50
"Coats, black",20
"Coats with ""quotes""",5
```

<!--
You can generate inline ingests commands using the Kusto.Data client library. 
Compression lets you embed new lines in quoted fields.
    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);
-->
