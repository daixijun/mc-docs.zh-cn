---
title: .create-merge table - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 .create-merge table。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 07/05/2020
ms.date: 01/22/2021
ms.openlocfilehash: 21df77c9251e9029c13709e5013a6fc080312eda
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611651"
---
# <a name="create-merge-table"></a>.create-merge table

创建新表或扩展现有表。 

该命令必须在特定数据库的上下文中运行。 

需要[数据库用户权限](../management/access-control/role-based-authorization.md)。

## <a name="syntax"></a>语法

`.create-merge` `table` *TableName* ([columnName:columnType], ...)  [`with` `(`[`docstring` `=` *Documentation*] [`,` `folder` `=` *FolderName*] `)`]

如果该表不存在，则其功能与 `.create table` 命令完全相同。

如果表 T 存在并且你发送了 `.create-merge table T (<columns specification>)` 命令，则：

* <columns specification> 中先前在 T 中不存在的任何列都将添加到 T 的架构的末尾。
* T 中不在 <columns specification> 中的任何列都不会从 T 中删除。
* <columns specification> 中存在于 T 中但数据类型不同的任何列都会导致命令失败。

## <a name="see-also"></a>另请参阅

* [`.create-merge tables`](create-merge-tables-command.md)
* [`.create table`](create-table-command.md)
