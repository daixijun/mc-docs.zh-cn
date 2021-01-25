---
title: .alter table - Azure 数据资源管理器
description: 本文介绍 .alter table 命令。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
origin.date: 06/08/2020
ms.date: 01/22/2021
ms.openlocfilehash: 18ce3d1f93e30a84d2f4683076fdfd29c6156865
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611560"
---
# <a name="alter-table"></a>.alter table
 
`.alter table` 命令：
* 保护“已保留”列中的数据
* 对表列重新排序
* 对现有表设置新列架构 `docstring` 和文件夹，并覆盖现有列架构 `docstring` 和文件夹
* 必须在指定表名范围的特定数据库的上下文中运行
* 需要[表管理员权限](../management/access-control/role-based-authorization.md)

> [!WARNING]
> `.alter` 命令使用不当可能会导致数据丢失。

> [!TIP]
> `.alter` 有一个对应的 `.alter-merge` 表命令，它具有类似的功能。 有关详细信息，请参阅 [`.alter-merge table`](../management/alter-merge-table-command.md)

**语法**

`.alter` `table` *TableName* (*columnName*:*columnType*, ...)  [`with` `(`[`docstring` `=` *Documentation*] [`,` `folder` `=` *FolderName*] `)`]


 * 表将具有采用指定的顺序的完全相同的列。
 指定表列：
 * 如果未在命令中指定现有列，则将删除这些列，且列中的数据将丢失，如使用 `.drop column` 命令时一样。
 * 更改表时，不支持更改列类型。 请改为使用 [`.alter column`](alter-column.md) 命令。

> [!TIP]
> 更改之前，请使用 `.show table [TableName] cslschema` 获取现有列架构。


此命令将如何影响数据？
* 该命令实际上不修改现有数据。 已删除的列中的数据会被忽略。 新列中的数据被假定为 null。
* 根据群集的配置方式，即使没有用户交互，数据引入也可能会修改表的列架构。 更改表的列架构时，请确保引入操作不会添加命令随后要删除的必需列。

> [!WARNING]
> 如果将数据引入表的过程会修改表的列架构，且与 `.alter table` 命令并行发生，那么执行这些过程时可能不知道表列的顺序。 这也有可能会将数据引入到错误的列中。 请通过在命令期间停止引入操作，或者确保此类引入操作始终使用映射对象来避免这些问题。

**示例**

```kusto
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
