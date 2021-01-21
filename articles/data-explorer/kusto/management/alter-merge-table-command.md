---
title: .alter-merge table - Azure 数据资源管理器
description: 本文介绍 .alter-merge table 命令。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 06/08/2020
ms.date: 01/22/2021
ms.openlocfilehash: c73d5ef0b7b2f627b56059344fc60e55438e8936
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611343"
---
# <a name="alter-merge-table"></a>.alter-merge table
 
`.alter-merge table` 命令：

* 保护现有列中的数据
* 将新列、`docstring` 和文件夹添加到现有表中
* 必须在指定表名范围的特定数据库的上下文中运行
* 需要[表管理员权限](../management/access-control/role-based-authorization.md)

> [!WARNING]
> `.alter-merge` 命令使用不当可能会导致数据丢失。

> [!TIP]
> `.alter-merge` 有一个对应的 `.alter` 表命令，它具有类似的功能。 有关详细信息，请参阅 [`.alter table`](../management/alter-table-command.md)

**语法**

`.alter-merge` `table` TableName (columnName:columnType, ...)  [`with` `(`[`docstring` `=` Documentation] [`,` `folder` `=` FolderName] `)`]    

指定表列：
 * 你指定的但不存在的列会添加到现有架构的末尾。
 * 如果传递的架构不包含某些表列，这些列不会被删除。
 * 如果使用其他类型指定现有列，该命令会失败。

> [!TIP]
> 更改之前，请使用 `.show table [TableName] cslschema` 获取现有列架构。

此命令将如何影响数据？
* 该命令实际上不修改现有数据。 已删除的列中的数据会被忽略。 新列中的数据被假定为 null。
* 根据群集的配置方式，即使没有用户交互，数据引入也可能会修改表的列架构。 更改表的列架构时，请确保引入操作不会添加命令随后要删除的必需列。

**示例**

```kusto
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
 
