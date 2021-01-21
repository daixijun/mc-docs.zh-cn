---
title: Kusto 查询引入（set、append、replace）- Azure 数据资源管理器
description: 本文介绍了 Azure 数据资源管理器中的从查询引入（.set、.append、.set-or-append、.set-or-replace）。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 03/30/2020
ms.date: 01/22/2021
ms.localizationpriority: high
ms.openlocfilehash: d914d6a1e3b47dc61ac26a0530162c4fc0c018ce
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611340"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>从查询引入（.set、.append、.set-or-append、.set-or-replace）

这些命令执行查询或控制命令，并将查询结果引入表中。 这些命令之间的区别在于它们如何处理现有的或不存在的表和数据。

|命令          |如果表存在                     |如果表不存在                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |命令将失败                  |将创建表并引入数据|
|`.append`        |数据将追加到表      |命令将失败                        |
|`.set-or-append` |数据将追加到表      |将创建表并引入数据|
|`.set-or-replace`|数据将替换表中的数据|将创建表并引入数据|

**语法**

`.set` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

`.append` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...`])`] `<|` *QueryOrCommand*

`.set-or-append` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

`.set-or-replace` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

**参数**

* `async`：如果指定了此项，命令会立即返回，并继续在后台执行引入操作。 命令的结果将包含一个 `OperationId` 值，该值随后可以与 `.show operations` 命令一起使用，以检索引入完成状态和结果。
* TableName：要将数据引入到其中的表的名称。
  表名称始终与上下文中的数据库相关。
* PropertyName、PropertyValue：影响引入过程的任意数量的引入属性。

 受支持的引入属性。

|属性        |说明|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | 日期时间值，格式为 ISO8601 字符串，在创建引入的数据盘区时使用。 如果未指定，将使用当前值 (`now()`)|
|`extend_schema`  | 一个布尔值，如果指定了此项，将指示命令扩展表的架构。 默认值为“false”。 此选项仅适用于 `.append`、`.set-or-append` 和 `set-or-replace` 命令。 进行架构扩展时，只允许在表的末尾添加其他列|
|`recreate_schema`  | 一个布尔值， 如果指定了此项，它将指示此命令是否可以重新创建表的架构。 默认值为“false”。 此选项仅适用于 set-or-replace 命令。 在同时设置的情况下，此选项优先于 extend_schema 属性|
|`folder`         | 要分配给表的文件夹。 如果表已存在，则此属性会替代表的文件夹。|
|`ingestIfNotExists`   | 一个字符串值， 如果指定了此项，则当表的数据已通过一个具有相同值的 `ingest-by:` 标记进行了标记时，引入操作会失败|
|`policy_ingestiontime`   | 一个布尔值。 当指定了此项时，它将指示是否在通过此命令创建的表上启用[引入时间策略](../../management/ingestiontime-policy.md)。 默认值为“true”|
|`tags`   | 一个 JSON 字符串，指示在引入期间要运行哪些验证|
|`docstring`   | 一个用来记录表的字符串|

 用于控制命令行为的属性。

|属性        |类型    |说明|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |指示此命令以并行方式从执行查询的所有节点引入。 默认值为“false”。  请参阅下面的备注。|

* *QueryOrCommand*：其结果将用作要引入的数据的查询或控制命令的文本。

> [!NOTE]
> 仅支持 `.show` 控制命令。

**备注**

* `.set-or-replace` 将替换表的数据（如果存在）。 它会删除现有的数据分片或创建目标表（如果不存在）。
  除非将 `extend_schema` 或 `recreate_schema` 引入属性之一设置为“true”，否则将保留表架构。 如果对架构进行了修改，则这是在实际引入数据之前在其自己的事务中进行的。 因此，引入数据失败并不意味着未修改架构。
* `.set-or-append` 和 `.append` 命令会保留架构，除非将 `extend_schema` 引入属性设置为“true”。 如果对架构进行了修改，则这是在实际引入数据之前在其自己的事务中进行的。 因此，引入数据失败并不意味着未修改架构。
* 建议将数据引入量限制为每个引入操作少于 1 GB。 可以根据需要使用多个引入命令。
* 数据引入是一项占用大量资源的操作，可能会影响群集上的并发活动（包括正在运行的查询）。 请避免同时运行过多的此类命令。
* 将结果集架构与目标表架构匹配时，比较是基于列类型进行的。 不对列名进行匹配。 请确保查询结果架构列与表中的顺序相同。 否则，数据将引入到错误的列中。
* 如果查询生成的数据量很大，超过 1 GB，并且查询不需要序列化，则可将 `distributed` 标志设置为“true”，使多个节点可以并行产生输出。
  如果查询结果较小，请不要使用此标志，因为它可能会不必要地生成很多小数据分片。

**示例** 

在数据库中创建名为 :::no-loc text="RecentErrors"::: 的新表，该表与 :::no-loc text="LogsTable"::: 具有相同的架构，并保存最近一小时的所有错误记录。

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

在数据库中创建一个名为“OldExtents”的新表，使该表包含单个列（即“ExtentId”），并保存数据库中已创建 30 天以上的所有盘区的盘区 ID。 数据库有一个名为“MyExtents”的现有表。

```kusto
.set async OldExtents <|
   MyExtents 
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

在当前数据库中将数据追加到名为“OldExtents”的现有表，该表包含单个列（即“ExtentId”），并保存数据库中已创建 30 天以上的所有盘区的盘区 ID。
基于名为“MyExtents”的现有表，用标记 `tagA` 和 `tagB` 来标记新盘区。

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

在当前数据库中将数据追加到“OldExtents”表，或者创建该表（如果不存在）。 使用 `ingest-by:myTag` 标记新盘区。 只有在表尚未包含使用 `ingest-by:myTag` 标记的盘区的情况下，才应当基于名为“MyExtents”的现有表来进行标记。

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <|
   MyExtents
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

在当前数据库中替换“OldExtents”表中的数据，或者创建该表（如果不存在）。 使用 `ingest-by:myTag` 标记新盘区。

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

在当前数据库中将数据追加到“OldExtents”表，并将已创建盘区的创建时间设置为过去的某个具体日期/时间。

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**返回输出**
 
返回通过 `.set` 或 `.append` 命令创建的盘区的信息。

**示例输出**

|ExtentId |OriginalSize |ExtentSize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 个 |
