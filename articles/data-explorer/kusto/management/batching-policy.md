---
title: Kusto IngestionBatching 策略管理命令 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 IngestionBatching 策略命令。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/19/2020
ms.date: 01/22/2021
ms.openlocfilehash: e60e0b1c53ce10f3aae2bd3f9555932f1d014e53
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611425"
---
# <a name="ingestionbatching-policy-command"></a>IngestionBatching 策略命令

[ingestionBatching 策略](batchingpolicy.md)是一个策略对象，用于根据指定的设置确定在数据引入期间数据聚合应何时停止。

此策略可以设置为 `null`，这种情况下会使用默认值，将最大批处理时间跨度设置为5 分钟，将最大项数设置为 1000 项，将总批大小设置为 1G，也可使用由 Kusto 设置的默认群集值。

如果没有为某个实体设置策略，则该实体会查找更高层次结构级别的策略。如果所有策略都设置为 NULL，则会使用默认值。 

**IngestionBatching 限制：**

| 类型 | 默认 | 最小值 | 最大值
|---|---|---|---|
| 项数 | 1000 | 1 | 2000 |
| 数据大小 (MB) | 1000 | 100 | 1000 |
| 时间 | 5 分钟 | 10 秒 | 15 分钟 |

## <a name="displaying-the-ingestionbatching-policy"></a>显示 IngestionBatching 策略

可以在数据库或表上设置此策略，并可以使用以下命令之一来显示此策略：

* `.show` `database` *DatabaseName* `policy` `ingestionbatching`
* `.show` `table` *DatabaseName*`.`*TableName* `policy` `ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>更改 IngestionBatching 策略

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

更改多个表（在同一数据库上下文中）的 IngestionBatching 策略：

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

IngestionBatching 策略：

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type`：表、数据库
* `database_or_table`：如果实体为表或数据库，则应在命令中指定其名称，如下所示： 
  - `database_name` 或 
  - `database_name.table_name` 或 
  - `table_name`（在特定数据库的上下文中运行时）

## <a name="deleting-the-ingestionbatching-policy"></a>删除 IngestionBatching 策略

```kusto
.delete <entity_type> <database_or_table_name> policy ingestionbatching
```

**示例**

```kusto
// Show IngestionBatching policy for table `MyTable` in database `MyDatabase`
.show table MyDatabase.MyTable policy ingestionbatching 

// Set IngestionBatching policy on table `MyTable` (in database context) to batch ingress data by 30 seconds, 500 files, or 1GB (whatever comes first)
.alter table MyTable policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:30", "MaximumNumberOfItems": 500, "MaximumRawDataSizeMB": 1024}'

// Set IngestionBatching policy on multiple tables (in database context) to batch ingress data by 1 minute, 20 files, or 300MB (whatever comes first)
.alter tables (MyTable1, MyTable2, MyTable3) policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:01:00", "MaximumNumberOfItems": 20, "MaximumRawDataSizeMB": 300}'

// Delete IngestionBatching policy on table `MyTable`
.delete table MyTable policy ingestionbatching

// Delete IngestionBatching policy on database `MyDatabase`
.delete database MyDatabase policy ingestionbatching
```
