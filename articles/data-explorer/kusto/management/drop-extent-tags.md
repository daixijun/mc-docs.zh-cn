---
title: .drop extent tags - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 drop extent tags 命令。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 07/02/2020
ms.date: 01/22/2021
ms.openlocfilehash: 16896e62ce22dd5d8592901d93bb138cc2c524b7
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611326"
---
# <a name="drop-extent-tags"></a>.drop 盘区标记

此命令在特定数据库的上下文中运行。 它从数据库和表中所有的或特定的区删除特定的[区标记](extents-overview.md#extent-tagging)。  

> [!NOTE]
> 在 Kusto 中，数据分片被称为“盘区”，所有命令都将“extent”或“extents”作为同义词使用。
> 若要详细了解区，请参阅[区（数据分片）概述](extents-overview.md)。

可以通过两种方式来指定应从哪些盘区中删除哪些标记：

* 明确指定应从指定的表中所有区中删除的标记。
* 提供一个查询，其结果指定表中的区 ID，并指定每个区应删除的标记。

## <a name="syntax"></a>语法

`.drop` [`async`] `extent` `tags` `from` `table` *TableName* `(`'*Tag1*'[`,`'*Tag2*'`,`...`,`'*TagN*']`)`

`.drop` [`async`] `extent` `tags` <| *query*

`async`（可选）：以异步方式执行该命令。
   * 返回操作 ID (Guid)。
   * 可以监视操作的状态。 使用 [`.show operations`](operations.md#show-operations) 命令。
   * 使用 [`.show operation details`](operations.md#show-operation-details) 命令检索成功执行的结果。

## <a name="restrictions"></a>限制

所有盘区都必须在上下文数据库中，并且必须属于同一个表。

## <a name="specify-extents-with-a-query"></a>使用查询指定区

使用 Kusto 查询指定区和要删除的标记。 它返回一个记录集，其中包含名为“ExtentId”的列和名为“Tags”的列。

> [!NOTE]
> 使用 [Kusto .NET 客户端库](../api/netfx/about-kusto-data.md)时，以下方法将会生成所需的命令：
> * `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
> * `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

需要所有涉及的源表和目标表的[表管理员权限](../management/access-control/role-based-authorization.md)。

### <a name="syntax-for-drop-extent-tags-in-query"></a>查询中的 .drop extent tags 的语法

```kusto 
.drop extent tags <| ...query...
```

### <a name="return-output"></a>返回输出

输出参数 |类型 |说明 
---|---|---
OriginalExtentId |string |其标记已被修改的原始区的唯一标识符 (GUID)。 该区在操作过程中被删除。
ResultExtentId |string |已经修改了标记的结果区的唯一标识符 (GUID)。 该区在操作过程中被创建和添加。 失败时 -“已失败”。
ResultExtentTags |string |用于对结果区进行标记的一组标记（如果仍然存在），或者为“null”（如果操作失败）。
详细信息 |string |包括失败详细信息（如果操作失败）。

## <a name="examples"></a>示例

### <a name="drop-one-tag"></a>删除一个标记

从表中标记了 `drop-by:Partition000` 标记的的任何区删除该标记：

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

### <a name="drop-several-tags"></a>删除多个标记

从表中带有 `drop-by:20160810104500`、`a random tag` 或 `drop-by:20160810` 标记的任何区删除这些标记：

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

### <a name="drop-all-drop-by-tags"></a>删除所有 `drop-by` 标记 

从表 `MyTable` 的区删除所有 `drop-by` 标记：

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

### <a name="drop-all-tags-matching-specific-regex"></a>删除所有符合特定正则表达式的标记 

从表 `MyTable` 的区删除所有符合正则表达式 `drop-by:StreamCreationTime_20160915(\d{6})` 的标记：

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

## <a name="sample-output"></a>示例输出

|OriginalExtentId |ResultExtentId | ResultExtentTags | 详细信息
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |
